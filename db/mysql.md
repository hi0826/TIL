## UNION, OR

- OR의 경우 서로다른 column을 조건절로 추가할 경우 index를 타지 않는다
- 따라서 다른 조건을 OR로 묶는 경우에는 UNION을 사용하자

## CONCAT, CONCAT_WS

- `CONCAT(str1, str2, ...)`
- `CONCAT_WS([seperator], str1, str2, ...)`
- `concat`은 전달된 모든 문자열들을 하나로 합쳐준다. `concat_ws` 첫번째로 들어온 문자를 구분자로 사용하여 각각 문자열들을 합칠때 사용

## EXIST

- `WHERE`절에서 사용, 서브쿼리를 사용하여 조건을 정한다.
- 잘 사용하면 `IN` 절보다 빠른 조건검색이 가능하다
- `IN` 절은 메인 쿼리의 값을 토대로 조건 처리

## ENUM

-`ENUM` 으로 `ORDER BY` 시에 `ENUM`에 등록된 순대로 오름 차순

## IN, NOT IN

- `WHERE 'column' IN ('value', 'value')`
- `WHERE 'column' NOT IN ('value', 'value')`
  - 'value' 에는 서브쿼리를 사용할 수 있음
- 특정 값이 들어있거나/들어있지 않거나에 대한 조건으로 검색할 때 사용

## JOIN

- 두 개의 테이블을 하나로 합칠때 사용하는 키워드

### INNER

- 일반적으로 생각하는 조인
- 특정 조건을 기준으로 테이블을 합친다 (교집합)
- `FROM A JOIN B ON A.id = B.id`의 형식으로 ON을 사용하여 조건을 줄 수 있다
- `FROM A JOIN B WHERE A.id = B.id`의 형식으로 WHERE을 사용하여 조건을 줄 수 있다

### OUTER (LEFT OUTER, RIGHT OUTER)

- 하나의 테이블을 기준으로 합치는 조인
- 일치하지 않는 값이 있더라도 합칠 수 있다 (합집합)
  - 일치하지 않는 값은 `null`로 표기
- OUTER 키워드를 생략해도 된다.
- `FROM A LEFT (OUTER) JOIN B ON A.id = B.id`
- `FROM A RIGHT (OUTER) JOIN B ON A.id = B.id`

### CROSS(Catesian Product)

- 두 개의 테이블을 전체 조인 (곱집합)
- 너무 많은 레코드를 생성할 위험이 있어 잘 사용하지 않음
- `FROM A, B`
- `FROM A CROSS JOIN B`

### SELF

- 자기 자신을 조인
- 자기 자신 테이블을 column으로 참조하고 있을 때 사용
  - ex) 회원 테이블에서 id 를 상사 column으로 사용하고 있는 경우
