# equals의 규약을 지켜라

## 핵심 정리 한눈에 보기

`equals` 는 **반사성(reflexive)**, **대칭성(symmetric)**, **추이성(transitive)**, **일관성(consistent)**, 그리고 **null‑과의 불일치**라는 다섯 가지 계약을 반드시 지켜야 합니다. Kotlin에서 `==` 연산자는 `equals`를 호출해 **구조적 동등성(structural equality)** 을 확인하고, `===` 는 두 참조가 같은 객체를 가리키는지 확인해 **레퍼런스 동등성(referential equality)** 을 판단합니다.citeturn0search1turn0search6  
데이터 클래스는 계약을 만족하는 `equals`/`hashCode` 를 자동 생성하지만, 그 외 클래스는 두 메서드를 **함께** 재정의하고, 변경 가능(mutable) 프로퍼티를 포함하지 말아야 합니다. 올바른 구현은 `HashSet`, `HashMap`, 정렬 컬렉션, 캐시 키 등에서 예측 가능한 동작을 보장하며, `compareTo` 를 구현할 때는 “`compareTo(x)==0` ⇒ `x.equals(y)`” 관계도 유지해야 합니다.citeturn0search0turn0search8

---

## 1. 구조적 vs. 레퍼런스 동등성

| 종류                  | 연산자/메서드                       | 의미                                        |
| --------------------- | ----------------------------------- | ------------------------------------------- |
| **구조적(값) 동등성** | `a == b`, `a != b` → `a?.equals(b)` | 두 객체가 *같은 값*을 가지면 `true`         |
| **레퍼런스 동등성**   | `a === b`, `a !== b`                | 두 변수가 *같은 인스턴스*를 가리키면 `true` |

구조적 비교는 **nullable 안전**을 위해 `a?.equals(b) ?: (b === null)` 로 컴파일됩니다.citeturn0search1

---

## 2. equals 계약: 5가지 원칙

1. **Reflexive** — `x.equals(x)` 는 항상 `true`.
2. **Symmetric** — `x.equals(y)` 와 `y.equals(x)` 결과가 같아야 함.
3. **Transitive** — `x.equals(y)` & `y.equals(z)` 가 `true` 면 `x.equals(z)` 도 `true`.
4. **Consistent** — 비교 대상의 변경이 없다면 결과가 바뀌지 않아야 함.
5. **null‑inequality** — `x.equals(null)` 은 항상 `false`.  
   위 원칙은 Java API의 `Object.equals` 문서를 그대로 상속받으며 Kotlin도 동일하게 적용됩니다.citeturn0search3turn0search8

---

## 3. 안전한 equals 구현 패턴

```kotlin
class Point(val x: Int, val y: Int) {

    override fun equals(other: Any?): Boolean {
        if (this === other) return true           // 1. 동일 참조면 true
        if (other !is Point) return false         // 2. 타입 검사 & null 처리
        return x == other.x && y == other.y       // 3. 의미 있는 필드 비교
    }

    override fun hashCode(): Int =
        31 * x + y                                // 4. 같은 필드를 사용
}
```

- 매개변수 타입은 **Any?** 여야 하며 가드 절로 타입을 좁혀야 합니다.citeturn0search9
- 비교에 들어가는 프로퍼티는 **불변(immutable)** 이어야 일관성을 지킬 수 있습니다.citeturn0search0
- 상속이 가능한 클래스를 열어둘 경우, `canEqual` 패턴(Java Effective Item 10)을 적용해 대칭성 문제를 방지합니다.citeturn0search8

---

## 4. 데이터 클래스와 value class

- **data class** 는 `equals`/`hashCode`/`toString`/`copy` 를 자동 생성합니다. 필드가 mutable 하면 일관성 계약이 깨질 수 있으므로 되도록 불변으로 선언합니다.citeturn0search4turn0search2
- **value class**(JVM inline class)는 한 개의 `val` 만 가지므로 참조·값 동등성이 대체로 일치하지만, 객체 캐싱(박싱) 시점에 따라 `===` 결과가 달라질 수 있어 주의가 필요합니다.citeturn0search7

---

## 5. equals ↔ compareTo 일치성

`Comparable` 을 구현하는 클래스라면 `compareTo(a) == 0` 일 때 `equals(a)` 도 `true` 가 되어야 합니다. 불일치하면 `TreeSet`, `TreeMap`, `sortedBy` 와 같은 정렬 기반 컬렉션에서 중복 원소가 허용되거나 누락되는 버그가 생깁니다.citeturn0search3turn0search8

---

## 6. 흔한 실수 & 대처법

| 실수                          | 결과                           | 해결책                                 |
| ----------------------------- | ------------------------------ | -------------------------------------- |
| **`hashCode` 미재정의**       | `HashSet`, `HashMap` 탐색 실패 | `equals`와 같은 필드로 `hashCode` 작성 |
| **mutable 필드 비교**         | 일관성(consistency) 깨짐       | 불변 필드만 사용                       |
| **타입 오버로드 대칭성 위반** | `a.equals(b)`≠`b.equals(a)`    | `canEqual` 또는 sealed 계층으로 제한   |
| **`super.equals` 호출 누락**  | 상위 타입 계약 무시            | 상속 구조가 복잡하면 `final` 로 막기   |

---

## 7. 실전 적용 사례

- **도메인 값 객체**: `Money`, `EmailAddress` 등은 data class 나 value class 로 구현해 컬렉션 키·캐시 키에 안전하게 사용.citeturn0search4
- **Android RecyclerView DiffUtil**: `areItemsTheSame` vs `areContentsTheSame` 에서 `equals` 계약을 따를 때 UI 갱신이 올바르게 동작.citeturn0search6
- **멀티 플랫폼 캐싱**: 서버와 클라이언트가 동일 equals 로직을 공유하면 캐시 키 충돌을 예방.citeturn0search0

---

## 8. 요약

1. **equals=계약**: 5대 원칙을 지키지 않으면 컬렉션·알고리즘이 오작동합니다.
2. `==`/`===` 차이를 명확히 하고, 필요하다면 직접 `equals`/`hashCode`를 작성합니다.
3. 데이터 클래스는 편리하지만 **mutable 속성**에 주의하세요.
4. `Comparable`을 구현할 땐 `compareTo == 0 ⇔ equals == true` 를 보장해야 합니다.
5. 상속 구조가 복잡하다면 차라리 클래스를 `final` 또는 `sealed`로 제한해 버그 가능성을 줄이세요.
