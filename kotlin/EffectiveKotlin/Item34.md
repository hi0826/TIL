# Effective Kotlin Item 34 – 기본 생성자에 **이름 있는 옵션 아규먼트**를 사용하라

## 한눈에 보는 핵심 요약

**Item 34**는 “필요 이상 `Builder`나 ‘텔레스코핑(telescoping) 생성자’ 패턴에 의존하지 말고, *기본(primary) 생성자에 기본값*을 두고 **이름 있는(named) 옵션 인수**로 호출하라”는 조언을 담고 있다.  
이렇게 하면

- 매개변수의 의미가 코드에 드러나 **가독성**이 높아지고
- 불필요한 오버로드·Builder 클래스를 줄여 **구현/유지 비용**이 감소하며
- 생성자 파라미터가 변하지 않는 값으로 전달돼 **스레드 안전성**도 자연히 확보된다.

---

## 1. 배경 – 왜 이 패턴이 필요할까?

### 1‑1. Telescoping Constructor Anti‑pattern

자바에서는 옵션이 늘어날 때마다  
`Required(arg1)` → `Required(arg1, opt1)` → `Required(arg1, opt1, opt2)` … 처럼  
다단(多段) 생성자를 추가하게 된다.

> **문제점**: 인수 순서를 기억하기 어렵고, 새 옵션이 생길 때마다 생성자가 “폭발”한다.

### 1‑2. Builder Pattern의 단점

Builder는 **이름**을 붙여 줄 수 있고 **순서**를 바꿀 수 있다는 장점이 있지만

- _보일러플레이트_ 코드가 많아 작성·리팩터 시간이 길다.
- Builder 내부 프로퍼티가 `var`이므로 동시성 문제에 취약하다.
- 코틀린–자바 상호 운용 같은 특수 상황이 아니라면 과한 선택일 수 있다.

---

## 2. Kotlin이 주는 대안 – 기본값 + 이름 있는 인수

| 특징                               | 효과                                 |
| ---------------------------------- | ------------------------------------ |
| **기본값(default value)**          | 인수를 생략해도 동작, 오버로드 감소  |
| **이름 있는 인수(named argument)** | 순서 무관, 의미 명확                 |
| **불변 파라미터**                  | Builder보다 자연스러운 스레드 안전성 |
| **IDE 지원**                       | 자동 완성, 리팩터 편의성 ↑           |

> **한 줄 정리**  
> ‘_주 생성자 + 기본값_’은 Builder가 주던 세 가지 이점(이름·순서 자유·기본값)을 *언어 차원*에서 제공한다.

---

## 3. 모범 가이드라인

1. **필수·선택 인수를 선명히 구분**
   - 필수값만 먼저 배치하고, 나머지는 기본값으로 처리한다.
2. **불리언·널러블 인수는 꼭 이름을 쓰자**
   - `sendEmail(true, false)` → `sendEmail(markAsHtml = true, highPriority = false)`
3. **Java 호출을 염두에 두면 `@JvmOverloads` 고려**
   - JVM 오버로드 수가 늘어나고 바이너리 호환성에 영향을 줄 수 있음을 주의.
4. **인수 개수가 5–6개를 넘으면 DSL·데이터 클래스 `copy`도 검토**
   - 복잡한 설정 객체라면 DSL(Item 35)이나 `data class + copy`가 더 명확하다.

---

## 4. 코드 예시

### 4‑1. 전통적 Builder vs. Named Arguments

```kotlin
// ✅ 권장: 기본 생성자 + 이름 있는 옵션
class Mail(
    val to: String,
    val subject: String = "",
    val html: Boolean = false,
    val highPriority: Boolean = false,
)

val welcome = Mail(
    to           = "user@example.com",
    subject      = "Welcome!",
    html         = true,
    highPriority = true,
)

// ❌ 구 Java 스타일 Builder
class MailBuilder(private val to: String) {
    var subject = ""
    var html = false
    var highPriority = false

    fun subject(value: String)       = apply { subject = value }
    fun html(value: Boolean = true)  = apply { html = value }
    fun highPriority(value: Boolean) = apply { highPriority = value }
    fun build() = Mail(to, subject, html, highPriority)
}
```

### 4‑2. 실무 예 – HTTP 요청 옵션 객체

```kotlin
data class RequestOptions(
    val timeoutMillis: Long          = 5_000,
    val followRedirect: Boolean      = true,
    val retryCount: Int              = 3,
    val headers: Map<String, String> = emptyMap(),
)

// 사용
httpClient.get(
    url = "/users",
    options = RequestOptions(
        timeoutMillis = 10_000,
        headers       = mapOf("X-Trace" to traceId),
    )
)
```

### 4‑3. `copy`로 부분 수정하기

```kotlin
val base = RequestOptions()
val noRetry = base.copy(retryCount = 0)   // 나머지 값은 기본값 유지
```

---

## 5. 자주 묻는 질문 & 주의할 Pitfall

| Q                                               | A                                                                                     |
| ----------------------------------------------- | ------------------------------------------------------------------------------------- |
| **파라미터 기본값을 바꾸면 바이너리 호환성은?** | 동일 시그니처로 컴파일되기 때문에 _사용 측_ 재컴파일이 필요하다. 공개 API라면 주의!   |
| **Java에서 호출해야 한다면?**                   | Java는 기본값·이름 인수를 지원하지 않는다. `@JvmOverloads`나 Builder를 병행 제공하자. |
| **선택 인수가 10개쯤 되는데 계속 추가될 예정**  | DSL(Item 35)이나 설정용 `data class`를 고려하라.                                      |

---

## 6. 실전 적용 사례

1. **Spring Boot 설정 객체** – `@ConfigurationProperties`에서 기본값을 두고 테스트에서 원하는 필드만 오버라이드.
2. **Android Jetpack Compose** – `Modifier.padding(all = 8.dp)`처럼 옵션 인수를 가진 함수가 대다수.
3. **코틀린 DSL (Ktor, Gradle KTS 등)** – DSL 내부 역시 이름 있는 인수를 갖춘 함수·생성자 체인이 핵심.

---

## 결론 & 정리

- **주 생성자 + 기본값 + 이름 있는 인수**는 _가독성, 구현 난이도, 스레드 안전성_ 세 마리 토끼를 잡는 Kotlin 고유의 강점.
- Builder가 반드시 필요한 **자바 호환**·**부분적 설정 저장** 등의 엔드 케이스가 아니라면, **Item 34 패턴을 우선 적용**하자.
- 테스트 코드·DSL·설정 객체에서 특히 큰 효율을 발휘한다.

> **실무 팁**: 기존 자바 Builder를 마이그레이션할 때, 내부 구현은 `data class` + `copy`로 바꾸고, 외부 API는 `@JvmOverloads`로 유지해 Kotlin의 이점을 안전하게 누릴 수 있다.
