# Inferred 타입으로 리턴하지 말라

- 타입 추론의 위험한 부분을 피하려면 할당 시 inferred 타입은 피연산자에게 맞게 설정된다는 것을 인지
- 라이브러리 조작이 불가능한 경우 inferred 타입을 노출 시 위험한 일 발생 가능
- 리턴타입을 외부에서 확인할 수 있게 명시적으로 지정해서 주는 것이 좋다

## 정리

- 타입을 확실하게 지정해야할 경우 명시적으로 지정하면 됨
- 타입은 중요한 정보이므로 숨기지 말기(아이템 14 참고)
- 안전을 위해 외부 API 를 만들 경우 타입을 반드시 지정, 특별한 이유가 없다면 제거하지 말 것
- inferred 타입은 예측하지 못한 결과를 낼 수 있음
