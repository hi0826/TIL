# Item 35. 복잡한 객체를 **DSL**로 선언하라

> “Kotlin의 함수 리터럴 + 수신 객체(receiver) 기능을 조합하면, 타입‑세이프하고 IDE‑친화적인 _도메인 전용 언어_ (DSL)를 만들 수 있습니다.”

---

## 1. 왜 DSL이 필요한가?

- **보일러플레이트 제거** : 복잡하거나 중첩 구조인 객체(HTML·UI·빌드 스크립트 등)를 코드 한줄마다 `new …` / `add(…)`로 조립하면 가독성이 곤두박질합니다. DSL은 “선언적” 문법으로 의도를 드러내면서 반복 코드를 숨겨 줍니다.
- **타입 안전** : Kotlin DSL은 컴파일 타임에 타입을 확인하므로 Groovy · XML 같은 문자열 기반 DSL보다 안전합니다.
- **계층적 데이터 표현** : HTML 문서, 설정 파일, UI 트리처럼 “트리형” 데이터를 코드 구조 그대로 서술할 수 있습니다.

---

## 2. Kotlin이 제공하는 DSL 재료

| 기능                                                    | 한 줄 설명                                                                    |
| ------------------------------------------------------- | ----------------------------------------------------------------------------- |
| **함수 타입 with receiver** (`HTML.() -> Unit`)         | 람다 안에서 `this`가 _HTML_ 인스턴스가 되도록 만듭니다.                       |
| **`apply`/`run`/`also`**                                | 빌더 초기화 패턴을 한 줄로 축약합니다.                                        |
| **연산자 오버로딩** (`operator fun String.unaryPlus()`) | `+”text”` 같은 미니 문법을 추가할 수 있습니다.                                |
| **infix 함수**                                          | `width eq 100.px` 처럼 읽기 쉬운 구문을 만듭니다.                             |
| **`@DslMarker`**                                        | 중첩된 빌더에서 “바깥” 수신객체를 실수로 호출하지 못하게 스코프를 제한합니다. |

---

## 3. 대표 예제 — HTML 테이블 DSL (요약)

```kotlin
table {
    tr {
        td { +"R1C1" }
        td { +"R1C2" }
    }
    tr {
        td { +"R2C1" }
        td { +"R2C2" }
    }
}
```

- `table`, `tr`, `td`는 각각 `TableBuilder`, `TrBuilder`, `TdBuilder` 내부에서만 보입니다.
- 각 빌더는 `apply(init)` 패턴으로 자식을 채우고 자신을 반환해 계층 구조를 완성합니다.

---

## 4. 직접 만들어 본 **SQL Query DSL** 예제

> _목표 : 간단한 SELECT 문을 선언적으로 기술_

```kotlin
@DslMarker
annotation class SqlDsl

/* --- 모델 정의 --- */
class SelectBuilder internal constructor() {
    private val columns = mutableListOf<String>()
    private lateinit var fromTable: String
    private val predicates = mutableListOf<String>()
    private var limit: Int? = null
    private var order: String? = null

    fun columns(vararg names: String) { columns += names }
    fun from(table: String) { fromTable = table }

    fun where(block: PredicateBuilder.() -> Unit) {
        predicates += PredicateBuilder().apply(block).build()
    }

    fun orderBy(column: String) { order = column }
    fun limit(n: Int) { limit = n }

    /* 최종 SQL 문자열 */
    fun build(): String = buildString {
        append("SELECT ${columns.joinToString()} FROM $fromTable")
        if (predicates.isNotEmpty()) append(" WHERE ${predicates.joinToString(" AND ")}")
        order?.let { append(" ORDER BY $it") }
        limit?.let { append(" LIMIT $it") }
    }
}

@SqlDsl
class PredicateBuilder {
    private val parts = mutableListOf<String>()
    infix fun String.eq(value: Any?) = parts.add("$this = '$value'")
    infix fun String.gt(value: Any) = parts.add("$this > $value")
    fun and(block: PredicateBuilder.() -> Unit) {
        val nested = PredicateBuilder().apply(block).build()
        parts.add("($nested)")
    }
    fun build() = parts.joinToString(" AND ")
}

/* --- DSL 진입 함수 --- */
fun select(block: SelectBuilder.() -> Unit): String =
    SelectBuilder().apply(block).build()

/* --- 사용 예시 --- */
val sql = select {
    columns("id", "name", "age")
    from("users")
    where {
        "age" gt 18
        and { "country" eq "KR" }
    }
    orderBy("name")
    limit(10)
}
println(sql) // SELECT id,name,age FROM users WHERE age > 18 AND (country = 'KR') ORDER BY name LIMIT 10
```

### 구현 포인트

1. **수신 객체 체이닝** : `SelectBuilder` → `PredicateBuilder`로 단계별 책임을 분리했습니다.
2. **중복 방지** : `@SqlDsl`로 바깥 `PredicateBuilder`를 실수로 섞어 쓰는 오류를 방지합니다.
3. **빌더 vs 결과 분리** : `build()`에서 최종 SQL 문자열을 생성해 UI·DB 호출 등과 분리했습니다.

---

## 5. DSL 설계 체크리스트

| 질문                          | 해설                                                                              |
| ----------------------------- | --------------------------------------------------------------------------------- |
| **정말 DSL이 필요한가?**      | 단순 객체라면 _named args_·_data class copy_·`apply` 만으로 충분할 수도 있습니다. |
| **스코프가 명확한가?**        | `@DslMarker`로 수신객체 충돌을 예방했는지 확인합니다.                             |
| **런타임 비용은 acceptable?** | 빌더 객체 다량 생성이 과도하면 `inline` + `build()` 최적화를 고려합니다.          |
| **IDE 지원이 충분한가?**      | 탑‑레벨 함수 이름이 모호하지 않고, 자동 완성이 잘 작동하는지 테스트합니다.        |

---

## 6. 실무 적용 사례

- **Gradle Kotlin DSL** : 빌드 스크립트를 Kotlin 코드로 안전하게 작성 — 변수를 빼내거나 조건 분기 넣기 용이.
- **Jetpack Compose / Anko / TornadoFX** : UI 트리를 코틀린 코드 블록으로 작성해 XML 레이아웃을 대체.
- **Ktor Routing DSL** : REST 엔드포인트를 `route("users") { get { … } }` 형태로 선언.
- **인프라 코드화** : Terraform‑like DSL, Kubernetes manifest 빌더 등으로 구성 오류를 줄이고 재사용성을 높임.

---

## 7. 정리

1. **DSL은 “코드 속 작은 언어”** 로, 복잡·계층적 구조를 간결하게 서술할 때 진가를 발휘합니다.
2. Kotlin은 _lambda with receiver_·_extension/operator_·`@DslMarker` 등 DSL 제작을 위한 1급 기능을 제공합니다.
3. 설계 전 “복잡도를 줄이는 비용 > DSL 구현·학습 비용”인지 면밀히 판단해야 합니다.
4. 잘 만든 DSL은 **보일러플레이트를 없애며** 동시에 **타입 안전성**과 **IDE 지원**까지 챙기는 훌륭한 설계 도구가 됩니다.

> **한 줄 요약** : _“객체 구조가 복잡해질수록, 선언적 DSL은 코드를 **읽기처럼 쓰게** 만들어 준다.”_
