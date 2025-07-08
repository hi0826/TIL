## Effective Kotlin - Item 33: 세컨더리 생성자 대신 **팩토리 함수**를 고려하라

### 핵심 주제

- **복잡한 객체 생성 로직**은 세컨더리 생성자보다 **이름 있는 팩터리 함수(factory function)** 로 분리하는 것이 가독성과 유연성을 높인다
- 팩터리 함수는 **이름이 있어 의도를 드러내고**, **서브타입·캐싱·nullable 반환** 등 생성 제어가 가능하다

---

### 왜 중요한가?

1. **이름을 통한 명확성** – `ArrayList(3)` 보다 `ArrayList.withCapacity(3)` 가 의미를 설명한다
2. **유연한 반환** – 인터페이스 타입이나 조건에 따른 서브타입을 자유롭게 반환할 수 있다(예: `listOf` 가 플랫폼마다 다른 구현 반환)
3. **객체 재사용·싱글턴·캐싱** – 동일 인자를 받을 때 동일 객체를 돌려주는 등의 최적화가 가능하다
4. **가시성 제어** – top‑level/companion 함수는 `private`·`internal` 범위로 숨길 수 있어 모듈 경계를 깔끔히 유지한다
5. **네이밍 컨벤션 활용** – `from`, `of`, `getInstance`, `copy`, `withX` 등으로 생성 방식·의도를 표현한다

---

### 코드 예시

```kotlin
// ❌ 세컨더리 생성자 버전
class User private constructor(
    val id: UUID,
    val created: Instant,
    val name: String,
) {
    constructor(name: String) : this(UUID.randomUUID(), Instant.now(), name)
}

// ✅ 팩터리 함수 (companion)
class User private constructor(
    val id: UUID,
    val created: Instant,
    val name: String,
) {
    companion object {
        fun of(name: String): User = User(UUID.randomUUID(), Instant.now(), name)
    }
}

// ✅ top‑level 팩터리 함수 & 캐싱
private val colorCache = mutableMapOf<String, Color>()
fun colorOf(hex: String): Color =
    colorCache.getOrPut(hex) { Color.parse(hex) }
```

---

### 잘못 적용·무시 시 문제점

| 문제 상황                   | 부작용                                      |
| --------------------------- | ------------------------------------------- |
| 세컨더리 생성자 남발        | 의미 불명확·오버로드 충돌·테스트 어려움     |
| 복잡한 로직을 생성자에 배치 | 상속 시점 제한으로 코드 중복·에러 처리 곤란 |
| 캐싱 필요 객체를 매번 `new` | 불필요한 GC·성능 저하                       |

---

### Java 등과의 비교

| 관점           | Java (Effective Java Item 1) | Kotlin                                          |
| -------------- | ---------------------------- | ----------------------------------------------- |
| 기본 권장      | **정적 팩터리 메서드** 우선  | **factory function** (top‑level·companion) 우선 |
| 네임스페이스   | 클래스 정적 메서드           | 독립 top‑level + companion 선택폭 넓음          |
| reified 제네릭 | 불가                         | `inline` + reified 로 런타임 타입 확보 가능     |

---

### 팁 요약

- **1차 선택은 primary constructor**. 추가 생성 경로가 필요할 때 **팩터리 함수** 고려.
- 네이밍 컨벤션(`from/of/valueOf/getInstance`)을 지켜 IDE 자동완성 혼란 최소화.
- 로깅·검증·캐싱 등 **비즈니스 로직**이 필요한 경우 세컨더리 생성자 대신 팩터리 함수에 배치.

---

### 결론

> **세컨더리 생성자보다 의도와 유연성을 가진 팩터리 함수가 더 나은 ‘객체 생성 API’가 된다.**
