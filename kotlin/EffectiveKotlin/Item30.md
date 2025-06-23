# 요소의 가시성을 최소화하라

## 핵심 요지

* 클래스, 함수, 프로퍼티 등의 **가시성은 가능한 한 좁게 유지**해야 한다.
* 내부 구현을 숨기고, 꼭 필요한 API만 외부에 노출해야 한다.
* 이는 **변경에 유연한 구조**와 **상태의 안정성**을 확보하는 데 도움이 된다.

---

## 가시성 수준 정리

| 대상     | 가시성 수단                                          | 설명                                          |
| ------ | ----------------------------------------------- | ------------------------------------------- |
| 클래스 멤버 | `private` / `protected` / `internal` / `public` | 가장 좁은 범위부터 시작해 점진적으로 확장                     |
| 최상위 요소 | `private` / `internal` / `public`               | 파일 또는 모듈 범위에서만 사용하면 `private` 또는 `internal` |

---

## 실전 예시

```kotlin
class CounterSet<T>(
    private val innerSet: MutableSet<T> = mutableSetOf()
) : MutableSet<T> by innerSet {
    var elementsAdded: Int = 0
        private set

    override fun add(element: T): Boolean {
        elementsAdded++
        return innerSet.add(element)
    }

    override fun addAll(elements: Collection<T>): Boolean {
        elementsAdded += elements.size
        return innerSet.addAll(elements)
    }
}
```

* 내부 `innerSet`은 `private`로 은닉
* 외부에서 `elementsAdded` 값은 읽을 수 있지만 직접 수정할 수 없음 (`private set`)

---

## 가시성 최소화의 장점

1. **이해하기 쉬운 API** 제공
2. **변경에 유연** (외부에 영향 없음)
3. **상태 보호**로 인해 데이터 일관성 유지
4. **병렬 환경에서도 안전성 강화**

---

## 팁

* 항상 "이 요소를 외부에 공개할 필요가 있는가?"를 고민할 것
* 기본적으로 `private`로 시작하고, 필요에 따라 `internal`, `public` 등으로 확장
* 상속보다 컴포지션을 우선 고려하고, 꼭 상속이 필요한 경우에만 `protected` 사용

---

## 결론

> **요소의 가시성을 최소화하면 안정성, 유지보수성, 확장성이 뛰어난 구조를 설계할 수 있다.**

이 원칙은 Kotlin뿐 아니라 모든 객체지향 언어 설계의 핵심 원칙 중 하나입니다.
