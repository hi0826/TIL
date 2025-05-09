# 예외를 활용해 코드에 제한을 걸어라

- 코틀린에서 코드에 제한을 거는 방법
- require : 아규먼트 제한
- check : 상태와 관련된 동작 제한
- assert : 어떤 것이 true인지 확인, 테스트 모드에서만 작동
- return /throw와 함꼐 사용하는 elvis 연산

## 코드에 제한을 걸었을 떄의 장점

- 문서를 읽지 않은 개발자도 문제 확인 가능
- 문제가 있을 경우 throw, 코드가 더 안정적으로 작동
- 자체적으로 검사되므로 단위 테스트를 줄일 수 있음
- 스마트 캐스트 기능을 활용할 수 있게되어, 캐스트를 적게 할 수 있음 (???)

## 아규먼트

타입 시스템을 활용하여 아규먼트에 제한을 거는 코드를 많이 사용한다.

- ex. 팩토리얼 계산엔 정수형만, 이메일 확인, 좌표 등등

아규먼트 제한시엔 require를 많이 사용한다

require은 해당 조건을 만족하지 못할 때 무조건적으로 IllegalArgumentException

## 상태

구체적인 조건을 만족할 때만 함수를 사용할 수 있게 해야하는 경우

- 미리 초기화되어 있어야만 처리를 할 때
- 사용자가 로그인했을 때만 처리 하게 하고 싶은 함수
- 객체를 사용할 수 있는 시점에 사용하고 싶은 함수

상태에 관련된 제한을 걸 때는 check를 상요한다

check는 해당 조건을 만족하지 못할 때 IllegalStateException 사용

## Assert 계열 함수 사용

구현 문제로 발생할 수 있는 문제를 예방하려면 단위테스트를 사용하는 것이 좋다

내부에서 assert를 사용해서 모든 함수가 호출 위치에서 제대로 동작하는지 확인 가능하다

코틀린/JVM에서만 활성화되며, -ea JVM을 활성화해 확인해야 한다

테스트 할 때만 활성화되므로, 오류가 발생해도 사용자가 알아차릴 수 없는데, 심각한 오류를 검증하려면 check를 사용할 것

### 장점

- Assert 계열의 함수는 코드를 자체 점검, 더 효율적 테스트가 가능
- 특정 사오항이 아닌 모든 상황에 대한 테스트
- 실행 시점에 정확하게 어떻게 되는지 확인 가능
- 실제 코드가 더 빠른 시점에 실패하게 만듦, 예상치 못한 동작이 언제 어디서 실행됐는지 확인 가능

assert는 코드를 안정적으로 만들고 싶을 때 양념처럼 사용할 수 있음

## nullability와 스마트 캐스팅

require, check블록으로 어떤 조건을 확인해 true가 나왔다면, 해당 조건은 이후로도 true일 거라고 가정한다

이를 이용해 타입 비교를 했다면, 스마트 캐스트가 작동한다.

requireNotNull, checkNotNull은 스마트 캐스트를 지원하므로, 변수를 언팩하는 용도로 활용하기 좋다

nullability를 목적으로, 오른쪽에 throw 나 return 을 두고 elvis 연산자를 활용하는 경우가 많음. 이런 코드는 읽기 쉽고 유연하게 사용하기 좋다

오류 발생없이 함수를 중단시키거나 할 수 있다

null일 때 여러 처리를 할 경우도 return/run을 조합해서 사용하면 된다. (로그 ㅜㄹ력과 같은 곳에 사용)

## 정리

- 제한을 훨씬 더 쉽게 확인 가능
- 애플리케이션을 더 안정적으로 지킬 수 있음
- 코드를 잘못 쓰는 상황을 막을 수 있음
- 스마트 캐스팅 활용 가능
- require {} : 아규먼트와 관련된 예측을 정의할 때 사용
- check {} : 상태와 관련된 예측을 정의할 때 사용
- assert {} : 테스트 모드에서 테스트를 할 때 사용
- return / throw 와 함께 elvis 사용하기
