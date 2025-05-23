# 가독성을 목표로 설계하라

- 개발자가 코드를 작성하는 데는 1분이 걸리지만 읽는 데는 10분이 걸린다
- 프로그래밍은 쓰기보다 읽기가 중요하다
- 항상 가독성을 생각하면서 코드를 작성하자

## 인식 부하 감소

- 사용 빈도가 적은 관용구는 코드를 복잡하게 만든다
- 창의적인 구조는 유연하지 않고, 지원도 제대로 받지 못함
- 기본적으로 인지 부하를 줄이는 방향으로 코드를 작성하자
  - 뇌가 프로그램의 작동 방식을 이해한느 과정을 더 짧게 만들기

## 극단적이 되지 않기

- let을 자주 사용하는 경우
  - 연산을 아규먼트 처리로 이동시킬 때
  - 데코레이터를 사용해서 객체를 감쌀 때
- let을 사용하는 것은 디버그하기 어렵고 경험이 적을 때 이해하기 어렵지만 용도에 따라 그 정도의 비용을 지불할 만한 가치가 있을 수 있다
- 구조들이 복잡성을 얼마나 가져오냐를 파악하자

## 컨벤션

- 연산자는 의미에 맞게 사용할 것
- 람다를 마지막 아규먼트로 사용한다
- 함수 이름과 실제 내부 동작이 같을 것
- 이미 있는 기능을 다시 만들지 말 것
