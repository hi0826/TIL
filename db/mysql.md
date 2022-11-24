## UNION, OR

- OR의 경우 서로다른 column을 조건절로 추가할 경우 index를 타지 않음.
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
