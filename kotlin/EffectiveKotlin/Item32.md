## Effective Kotlin - Item 32: 추상화 계약을 존중하라

### 핵심 요지

- **공개 API, 슈퍼클래스, 인터페이스**는 모두 ‘계약(contract)’이다.
- 리플렉션·다운캐스트·무분별한 override 등으로 **계약을 깨면 안 된다**.
- Kotlin의 `final` 기본, 널 안정성, `sealed` 클래스 등은 계약 존중을 장려한다.

---

### 왜 중요한가?

1. **변경 안전성** – 구현 세부가 바뀌어도 외부가 영향받지 않는다.
2. **LSP 준수** – 하위 타입은 상위 타입의 전·후조건을 지켜야 한다.
3. **유지보수성** – 계약을 지키면 테스트·교체·리팩터링이 수월해진다.

---

### 코드 예시

```kotlin
class Employee {
    private fun secret() {}
}

fun hack(e: Employee) {
    e::class.declaredMemberFunctions
        .first { it.name == "secret" }
        .also { it.isAccessible = true } // 계약 파괴
        .call(e)
}
```

```kotlin
class Id(val id: Int) {
    override fun equals(other: Any?) =
        other is Id && other.id == id
    // hashCode 미구현 → HashSet 동작 오류
}
```

---

### 잘못 적용 시 문제점

| 계약 위반 사례              | 발생 가능한 문제                   |
| --------------------------- | ---------------------------------- |
| 리플렉션으로 `private` 접근 | 구현 변경 시 런타임 오류·보안 취약 |
| `equals`/`hashCode` 불일치  | `Set`·`Map` 중복, 캐시 실패        |
| LSP 위반 `override`         | 예측 불가 버그, 교체 불가 설계     |

---

### Java와 Kotlin 비교

| 측면          | Java(기본)  | Kotlin(기본)           |
| ------------- | ----------- | ---------------------- |
| 기본 상속     | 열림(open)  | 닫힘(final)            |
| 내부 API 은닉 | `private`   | `private` + `internal` |
| 널 안전성     | 개발자 책임 | 타입 시스템 강제       |
| `sealed` 계층 | 수동 구현   | 언어 차원 지원         |

---

### 결론

> **가능하다고 해서 해서는 안 된다.** 추상화 계약을 지키면 코드가 견고해지고 변화에 강해진다.
