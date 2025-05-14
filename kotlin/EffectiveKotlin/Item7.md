# 결과 부족이 발생할 경우 null과 Failure를 사용하라

- 함수가 원하는 결과를 만들어 낼 수 없는 경우
- null 혹은 sealed 클래스 리턴
- 예외 throw > 정보를 전달하는 목적으로 사용하면 안됨
- 예외는 잘못된 특별한 상황을 나타내고 처리되어야 한다 > 예외적인 상황이 생겼을 때 사용하는 것이 좋음
  - 예외 전파의 과정을 제대로 추적하지 못함
  - 코틀린의 모든 예외는 uncheked 예외이므로 사용자가 예외를 처리하지 않을 수도 있음
  - 예외는 예외적인 상황을 처리하기 위해 만들어져 명시적인 테스트만큼 빠르게 동작하지 않음
  - try catch 블록은 최적화가 제한됨

## null Failure

- 예상되는 오류를 표현함에 있어 굉장히 좋음
- 명시적이고, 효율적, 간단한 방법으로 처리 가능
- 예측 가능한 오류는 Null과 Failure를 사용하자
- null 처리시에는 safe call이나 Elvis 연산자와 같은 null safety 기능 활용하기
- Union type 리턴시 when 사용하기
- try-catch 보다 효율적이며, 사용하기 쉽고 더 명확
- 추가적인 정보를 전달할 경우 sealed result 클래스 사용하기
  - 처리할 때 필요한 정보를 가질 수 있다
- 예상할 수 있을때 / 없을 때
  - get : 특정 위치에 있는 요소 추출 시 사용, 없을 시 Exception 발생
  - getOrNull : 발생시 null 리턴
  - 외에 getOrDefault등, 일반적으로는 getOrNull / ?: 연산 사용
- nullable을 리턴하면 안됨. null에 대한 경고를 주려면 getOrNull등을 사용해서 리턴예측을 도와주자
