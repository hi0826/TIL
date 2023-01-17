# @PostConstruct

- 의존성 주입이 이루어진 후 초기화를 수행하는 메서드
- @PostConstruct가 붙은 메서드는 클래스가 service를 수행하기 전에 발생
  - 다른 리소스에서 호출되지 않는다해도 수행된다

## 왜 사용하는가?(장점)

- 일반 생성자가 호출 되었을 때, 빈은 아직 초기화되지 않았다(주입된 의존성이 없음)
- @PostConstruct를 사용하면, 빈이 초기화 됨과 동시에 의존성을 확인할 수 있음
- Bean LifeCycle에서 오직 한 번만 수행된다는 것을 보장할 수 있음
  - WAS가 올라가면서 bean이 생성될 때 딱 한 번 초기화한다
  - 그래서 @PostConstruct를 사용하면 bean이 여러번 초기화되는 것을 방지할 수 있다.

## 오류

- javax.annotation.PostConstruct 를 import 할 수 없는 문제
  - JAVA 9부터 PostConstruct가 deprecated 됨
  - maven에서 javax.annotation-api 추가

```xml
<dependency>
    <groupId>javax.annotation</groupId>
    <artifactId>javax.annotation-api</artifactId>
    <version>1.3.2</version>
</dependency>
```
