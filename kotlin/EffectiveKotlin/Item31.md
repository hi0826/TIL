# Effective Kotlin — Item 31: 문서로 규약을 정의하라

## 1. ‘문서로 규약을 정의하라’란?

- **계약(Contract)**: 함수/클래스가 보장해야 하는 **전제조건·불변식·사후조건**을 자연어로 서술한 약속
- 규약은 이름·타입·주석·가시성에 녹아 있으며, 특히 외부 API에서 중요

---

## 2. 왜 중요한가?

| 효과              | 설명                                                        |
| ----------------- | ----------------------------------------------------------- |
| **독립 개발**     | 구현자와 사용자가 동일한 규약을 참고하므로 병렬 작업이 가능 |
| **리팩터링 자유** | 내부 구현을 변경해도 계약만 유지하면 외부 영향 없음 (OCP)   |
| **신뢰·안전**     | 계약이 명시되면 IDE·리뷰·테스트 단계에서 위반을 빠르게 탐지 |

---

## 3. 규약을 표현하는 네 가지 수단

### 3.1 이름

- `sum`, `map` 같이 **보편적 의미**를 가진 이름이면 추가 설명 없이도 동작 예측이 가능

### 3.2 타입 & 가시성

- 파라미터·리턴 타입, 널 가변성, mutable/immutable 여부가 규약의 일부
- `private`/`internal`/`public` 가시성을 좁히면 오·남용을 방지

### 3.3 KDoc 주석

- `/** 요약 … 상세 … @param … */` — Markdown + 블록 태그
- 주요 태그: `@param`, `@return`, `@throws`, `@sample`, `@see`
- IntelliJ IDEA는 KDoc을 툴팁으로 렌더링

### 3.4 예시 — 표준 라이브러리 `listOf`

```kotlin
/**
 * Returns a new read‑only list of given elements.
 * The returned list is serializable on the JVM.
 *
 * @sample samples.collections.Collections.Lists.readOnlyList
 */
fun <T> listOf(vararg elements: T): List<T> =
    if (elements.isNotEmpty()) elements.asList() else emptyList()
```

- 요약·행위·제약(읽기 전용·Serializable)·샘플을 한눈에 전달

---

## 4. KDoc 작성 가이드

1. **요약**: 한 문장으로 기능 설명
2. **상세 설명**: 필요 시 동작·제약·예시 추가
3. **블록 태그**: `@param`, `@return`, `@throws`, `@sample` 등

### 좋은 KDoc의 특징

- **행동 중심**: *무엇*과 *전제/결과*를 명확히 기술
- **실행 가능한 예시**: `@sample` 코드
- `[Type]` 링크로 타입‑안전한 참조

---

## 5. Dokka — 문서 자동 생성

- Gradle 플러그인 설정
  ```kotlin
  plugins {
      id("org.jetbrains.dokka") version "1.x.x"
  }
  ```
- 명령: `./gradlew dokkaHtml`
- 다중 모듈·템플릿 커스터마이즈 지원

---

## 6. 실무 적용 팁 & 함정

1. **LSP 위반 방지**: 상위 타입의 계약을 하위 타입이 깨지지 않도록 문서와 테스트 작성
2. **계약 vs 런타임 체크**: `kotlin.contracts`로 일부 사후조건을 컴파일러 힌트로 줄 수 있지만, 문서화가 우선
3. **버전 관리**: 계약 변경 시 SemVer 업·`@Deprecated`·CHANGELOG 동시 업데이트
4. **과도한 주석 경계**: 공개 API에는 반드시 계약을 서술하되, 로컬/사소한 함수에는 과도한 주석을 피함

---

## 7. 체크리스트

- [ ] 공개 함수·클래스마다 KDoc 요약과 태그 작성
- [ ] 입력·출력·예외·스레드 안전성·성능 기대치 명시
- [ ] Dokka CI 작업으로 HTML 문서 배포
- [ ] 변경 시 SemVer 상승 및 `@Deprecated` 가드
- [ ] 테스트로 계약 자동 검증
