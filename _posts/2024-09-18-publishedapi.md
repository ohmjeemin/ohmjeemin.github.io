---
title: "코틀린의 @PublishedApi 어노테이션에 대해서 알아보자"
date: 2024-09-18 10:00:00 +/- TTTT
categories: [Dev, git]
tags: [git]
pin: true
---

Kotlin의 `inline`함수는 함수 호출을 성능 향상을 위해 컴파일 시점에 함수 호출을 실제로 함수의 바이트 코드로 교체합니다. 그러나 인라인 함수 내에서 가시성이 `private`이나 `internal`인 멤버를 사용할 때, 컴파일러가 접근 제어 문제를 일으킬 수 있습니다. 이 때 `@PublishedApi`어노테이션을 사용하여 해당 멤버의 가시성을 `public`으로 한정적으로 노출시킴으로써 문제를 해결합니다. `@PublishedApi`는 가시성 자체를 완전히 `public`으로 바꾸지 않습니다. 그러므로 다른 모듈에서는 여전히 접근할 수 없습니다.


## 사용 에시
```kotlin
class Calculator {
    inline fun sum(a: Int, b: Int): Int {
        return internalSum(a, b)
    }

    @PublishedApi
    internal fun internalSum(a: Int, b: Int): Int {
        return a + b
    }
}

```


`inline`함수 내에서 `private`나 `internal` 멤버를 사용할 때 `@PublishedApi` 어노테이션을 사용하지 않으면 다음과 같은 에러가 발생합니다. 

```shell
Public-API inline function cannot access non-public-API 'internal final fun internalSum(a: Int, b: Int): Int defined in Calculator'
```

## 참고 자료
- [코틀린 공식 문서](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-published-api/)