# API 외부를 wrap해서 사용하라

API를 신뢰할 수 없는 경우를 불안정하다고 한다.

잠재적으로 불안정하다고 판단되는 외부 라이브러리를 wrap해서 사용한다.

## 장점

- 문제가 있다면 래퍼만 변경하면 되므로, API 변경에 쉽게 대응할 수 있다
- 프로젝트의 스타일에 맞춰서 API의 형태를 조정할 수 있다
- 특정 라이브러리에서 문제가 발생하면, 래퍼를 수정해서 다른 라이브러리를 사용하도록 코드를 쉽게 변경할 수 있다
- 필요한 경우 동작 추가/수정이 용이하다

## 단점

- 래퍼를 따로 정의해야 한다
- 다른 개발자가 프로젝트를 다룰 때, 어떤 래퍼들이 있는지 따로 확인
- 래퍼들은 프로젝트 내부에서만 유효하므로, 문제가 생겨도 질문이 불가능

장/단점을 모두 이해하고 랩할 API를 선정하기

라이브러리의 안정성을 확인할 수 있는 기본적인 휴리스틱은 버전 번호, 사용자 수

새로 생기고, 인기가 적을수록 위험할 수 있다
