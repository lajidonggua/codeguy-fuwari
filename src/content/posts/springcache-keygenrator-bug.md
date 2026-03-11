---
title: Spring Cache SimpleKeyGenerator 缓存永不命中问题
published: 2026-03-11T00:00:00.000Z
tags: [Spring Cache, Kotlin, Bug, 缓存, 后端]
category: 故障排查
draft: false
---

# Spring Cache SimpleKeyGenerator 缓存永不命中问题

## Situation（背景）

项目中使用 Spring Cache 对部分接口进行缓存加速，缓存 key 由 Spring 默认的 `SimpleKeyGenerator` 根据方法入参自动生成。

`SimpleKeyGenerator` 的 key 生成逻辑是基于参数对象的**引用**，而非在方法调用时对参数做快照（deep copy）。具体来说，它在方法执行前生成 key 时持有的是对象引用，若该对象在方法体内被修改，最终存入缓存时使用的 key 也会随之变化。

---

## Task（问题）

定位一个缓存命中率为 0 的 Bug：

- 缓存注解配置正确，逻辑上应该命中缓存
- 实际每次请求都穿透到数据库，缓存形同虚设
- 日志显示每次都在写入缓存，但从未读取到缓存

---

## Action（分析与排查）

### 根因分析

Spring `SimpleKeyGenerator` 生成 key 的时机是**方法执行前**，但它保存的是入参对象的**引用地址**，而不是对象当时的值快照。

```kotlin
// 伪代码示意：SimpleKeyGenerator 内部行为
val key = SimpleKeyGenerator.generateKey(*params)  // 此时 key 持有 requestObj 的引用

// 方法体内修改了 requestObj
requestObj.someField = "modified value"

// 方法执行完毕，Spring 尝试将结果存入缓存
// 此时用于存储的 key 是基于已被修改的 requestObj 生成的
// → 与方法执行前生成的 lookup key 不一致 → 永远无法命中
```

### 复现场景

```kotlin
@Cacheable(cacheNames = ["myCache"])
fun getData(requestObj: RequestDto): ResponseDto {
    // ⚠️ 在方法内修改了入参对象
    requestObj.pageSize = 100
    requestObj.sortField = "createTime"

    return repository.query(requestObj)
}
```

执行流程：

| 阶段 | key 基于的对象状态 | key 值（示意） |
|------|-------------------|---------------|
| 方法调用前（cache lookup） | `pageSize=10, sortField=null` | `hash(10, null)` |
| 方法执行后（cache put） | `pageSize=100, sortField=createTime` | `hash(100, createTime)` |

两次 key 不一致 → 缓存永远无法命中。

### 为什么难以发现

- 缓存写入是正常的，监控上看缓存有数据
- 没有任何报错或异常
- 只有在对比 lookup key 和 put key 时才能发现差异
- `SimpleKeyGenerator` 是 Spring 默认行为，容易被忽视

---

## Result（解决方案）

自定义 `KeyGenerator`，在 `generate` 方法中直接将参数调用 `toString()` 拼接成字符串返回。

由于字符串是值类型，key 在 `generate` 执行的那一刻就已固定，后续方法体内对入参对象的任何修改都不会影响这个 key。

```kotlin
@Component("toStringKeyGenerator")
class ToStringKeyGenerator : KeyGenerator {
    override fun generate(target: Any, method: Method, vararg params: Any?): Any {
        // 直接 toString，生成时即固定为字符串，与后续对象状态无关
        return params.joinToString(":") { it.toString() }
    }
}
```

```kotlin
@Cacheable(cacheNames = ["myCache"], keyGenerator = "toStringKeyGenerator")
fun getData(requestObj: RequestDto): ResponseDto {
    // 方法内修改入参不再影响 cache key
    requestObj.pageSize = 100
    return repository.query(requestObj)
}
```

> 前提：入参对象需要正确重写 `toString()`（Kotlin data class 默认已包含所有字段）。

---

## 经验总结

- `SimpleKeyGenerator` 基于对象引用，不是值快照，入参在方法内被修改会导致 cache lookup key 与 cache put key 不一致
- 自定义 KeyGenerator 在 `generate` 时将参数转为字符串，key 即时固化，彻底规避引用问题
- 缓存命中率监控是发现此类问题的有效手段，命中率长期为 0 应立即排查



---

## 面试口述版（约 2 分钟）

**S（背景）**
项目里用了 Spring Cache 做接口缓存，key 由默认的 `SimpleKeyGenerator` 自动生成。

**T（问题）**
发现缓存命中率一直是 0，每次请求都打到数据库，但缓存里明明有数据，也没有任何报错。

**A（分析）**
排查后发现根因在于 `SimpleKeyGenerator` 是基于对象**引用**生成 key 的，不是值快照。我们在方法体内修改了入参对象的字段（比如补填默认分页参数），导致 Spring 在方法执行前做 cache lookup 时用的 key，和方法执行后做 cache put 时用的 key 不一致——两个 key 永远对不上，所以永远不会命中。

**R（解决）**
自定义了一个 `KeyGenerator`，在 `generate` 方法里直接把入参 `toString()` 拼成字符串返回。字符串是值类型，key 在 `generate` 执行那一刻就固定了，后续方法体内怎么改对象都不会影响这个 key，cache lookup 和 cache put 用的始终是同一个 key，问题解决。前提是入参对象要正确重写 `toString()`，Kotlin 的 data class 默认就满足。

> 追问补充：这类问题没有报错、缓存也有写入，只有通过命中率监控才能发现，所以缓存监控很重要。
