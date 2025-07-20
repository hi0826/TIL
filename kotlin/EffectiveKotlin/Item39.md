# 태그 클래스보다는 클래스 계층을 사용하라

## 핵심 정리 한눈에 보기

**태그(Tag) 값으로 모드를 구분하는 “태그 클래스(tagged class)”**는 서로 다른 책임을 하나의 자료형에 억지로 밀어넣기 때문에  
보일러플레이트가 늘고, 잘못된 상태를 표현하기 쉽고, `when`/`switch` 분기로 인해 다형성을 제대로 활용할 수 없습니다.  
Kotlin에서는 `sealed` 클래스·인터페이스로 이루어진 **제한된 클래스 계층(restricted hierarchy)** 을 만들어  
각 변형을 **개별 타입**으로 분리하면 이러한 문제를 깔끔하게 해결할 수 있습니다.  
이 방식은 컴파일러가 `when` 식의 누락 분기를 잡아 주고, 각 서브타입이 자신에게 필요한 데이터만 보유하므로  
가독성·안전성·테스트 용이성이 모두 향상됩니다.

---

## 1. 태그 클래스란?

### 1‑1 정의

- **태그 값**(enum·상수 등)을 가진 필드가 객체의 “모드”를 나타내고,  
  각 메서드에서 `when`/`if`로 태그를 검사하여 동작을 달리하는 구조를 말합니다.

```kotlin
class ValueMatcher<T> private constructor(
    private val value: T? = null,
    private val matcher: Matcher
) {
    fun match(target: T?) = when (matcher) {
        Matcher.EQUAL         -> target == value
        Matcher.NOT_EQUAL     -> target != value
        Matcher.LIST_EMPTY    -> target is List<*> && target.isEmpty()
        Matcher.LIST_NOT_EMPTY-> target is List<*> && target.isNotEmpty()
    }

    enum class Matcher { EQUAL, NOT_EQUAL, LIST_EMPTY, LIST_NOT_EMPTY }
    companion object { /* 팩토리 메서드 생략 */ }
}
```

### 1‑2 대표적인 문제점

| 문제점                  | 설명                                                                                          |
| ----------------------- | --------------------------------------------------------------------------------------------- |
| **보일러플레이트 증가** | 한 클래스가 모든 모드를 지원하려면 불필요한 필드·검사 코드가 많아집니다.                      |
| **일관성 훼손**         | 어떤 태그에서는 쓰이지 않는 필드가 반드시 존재해 “잘못된 상태”를 표현할 수 있습니다.          |
| **상태 검증 어려움**    | 생성 시 유효성 검사를 제대로 못 하면 런타임 오류가 납니다.                                    |
| **다형성 부재**         | 호출자는 항상 태그를 중심으로 `when` 분기를 작성해야 하므로 OCP(개방‑폐쇄 원칙)에 어긋납니다. |

---

## 2. 클래스 계층(특히 `sealed`)으로 개선하기

### 2‑1 기본 형태

```kotlin
sealed class ValueMatcher<T> {
    abstract fun match(target: T?): Boolean

    class Equal<T>(private val value: T) : ValueMatcher<T>() {
        override fun match(target: T?) = target == value
    }
    class NotEqual<T>(private val value: T) : ValueMatcher<T>() {
        override fun match(target: T?) = target != value
    }
    class EmptyList<T> : ValueMatcher<T>() {
        override fun match(target: T?) =
            target is List<*> && target.isEmpty()
    }
    class NotEmptyList<T> : ValueMatcher<T>() {
        override fun match(target: T?) =
            target is List<*> && target.isNotEmpty()
    }
}
```

`sealed`는 **동일 모듈/패키지 내에서만** 하위 타입을 허용해 “닫힌 집합”을 보장합니다.  
덕분에 `when (vm)` 구문에서 `else`가 필요 없고, 새 하위 클래스가 생기면 컴파일러가 모든 분기를 재검토하도록 강제합니다.

### 2‑2 장점 요약

| 장점                | 이유                                                         |
| ------------------- | ------------------------------------------------------------ |
| **읽기·유지보수 ↑** | 각 타입이 자기 필드만 보유—불필요한 null 체크·주석 감소      |
| **타입 안전성 ↑**   | 컴파일 단계에서 누락된 케이스를 바로 발견                    |
| **테스트 용이**     | 변형별 독립 인스턴스 생성 가능—단위 테스트가 직관적          |
| **확장성**          | 새 기능을 **서브타입 추가**로 구현 가능; 기존 코드 최소 변경 |

> **주의:** 외부 라이브러리에 공개하는 API가 `sealed`라면 **새 서브타입 추가 자체가 breaking change**가 될 수 있습니다.  
> 외부 확장이 필요하다면 `abstract` + `open` 계층으로 설계해 확장 가능성을 열어두세요.

---

## 3. 새 코드 예제 (책 예시를 넘어선 추가)

### 3‑1 잘못된 태그 클래스 – 결제 수단

```kotlin
class Payment(
    val amount: Int,
    val tag: Method,
    val cardNumber: String? = null,
    val email: String? = null,
    val bankSwift: String? = null
) {
    enum class Method { CARD, PAYPAL, BANK }

    fun pay() = when (tag) {
        Method.CARD   -> println("Pay \$${amount} with card $cardNumber")
        Method.PAYPAL -> println("Pay \$${amount} via PayPal $email")
        Method.BANK   -> println("Wire \$${amount} to $bankSwift")
    }
}
```

- `cardNumber` 등이 null일 수 있어 **런타임 NPE 위험**이 있습니다.

### 3‑2 `sealed` 계층으로 리팩터링

```kotlin
sealed interface Payment {
    val amount: Int
    fun pay()

    data class Card(val amount: Int, val cardNumber: String) : Payment {
        override fun pay() = println("Pay \$${amount} with card $cardNumber")
    }
    data class PayPal(val amount: Int, val email: String) : Payment {
        override fun pay() = println("Pay \$${amount} via PayPal $email")
    }
    data class Bank(val amount: Int, val swift: String) : Payment {
        override fun pay() = println("Wire \$${amount} to $swift")
    }
}
```

각 서브타입이 **필수 필드**를 강제하므로 잘못된 상태를 표현할 수 없습니다.  
또한 `when (payment)` 분기도 컴파일러가 exhaustiveness(완전성)를 강제합니다.

---

## 4. 실전 적용 팁 & FAQ

| 질문                                        | 가이드                                                                                                                                          |
| ------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| **상태 패턴과 헷갈린다?**                   | 상태 패턴은 _하나의_ 객체가 런타임에 여러 상태 객체(대개 `sealed`)를 가리키며 **mutable**하게 전환합니다. 태그 클래스 교체와 모순되지 않습니다. |
| **서브타입을 외부가 확장해야 한다면?**      | `abstract class` 또는 `interface`를 사용해 열려 있는 계층을 설계하고, 공통 계약을 문서화하세요.                                                 |
| **값 객체에 `data class`를 써도 괜찮을까?** | 변형이 고유한 데이터를 보유하고 동등성(`==`) 비교가 의미 있다면 `data class`로 만드는 것이 좋습니다.                                            |

---

## 5. 마무리 & 실적용 사례

> **타입을 나누면 로직이 단순해집니다.**—클래스 계층을 활용하면 조건 분기가 사라지고, 각 타입이 자신의 책임에 집중합니다.

- **Android UI 상태**

  ```kotlin
  sealed interface UiState {
      object Loading : UiState
      data class Success(val data: List<Item>) : UiState
      data class Error(val cause: Throwable) : UiState
  }
  ```

  ViewModel ↔ View 통신에서 널·불완전 상태를 방지합니다.

- **네트워크 응답 래퍼**  
  `sealed class ApiResult<out T>` 로 성공·실패·캐싱 상태를 표현—Rx/Flow 변환 시 가독성↑.

- **도메인 모델**  
  `sealed class Employee` 하위에 `FullTime`, `PartTime`, `Contractor` 를 두어 급여 계산 전략을 캡슐화.

---

### 요약

1. 태그 클래스 → `sealed` 계층으로 변형을 **타입**으로 승격.
2. 불필요한 필드·분기 제거로 **가독성**·**안정성** 업그레이드.
3. 실전 코드(결제, UI 상태, 네트워크, 도메인)에도 동일 원칙 적용 가능.
4. 외부 확장이 필요하면 `open` 계층을 설계하여 신중히 공개 API를 잡을 것.
