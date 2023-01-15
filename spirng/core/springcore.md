# Spring Core Technologies (v.6.0.4)

- [reference - docs.spring.io](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#spring-core)
- 스프링 프레임 워크의 꼭 필요한 모든 기술들을 가지고 있는 레퍼런스
- 가장 중요한 것은 스프링 프레임워크의 IoC(Inversion of Control) 컨테이너 이다.
- 스프링 IoC 컨테이너의 철저한 처리는 스프링 관점지향 프로그래밍(Aspect-Oriented Programming, AOP)의 포괄적인 범위에 이어진다.
- 스프링 프레임워크는 자신만의 AOP 프레임워크를 가지고있으며, JAVA 엔터프라이즈 프로그래밍 세계에서 개념적으로 이해하기 쉽고, AOP 요구사항의 80% 스윗 스팟을 보낸다.

## 1. The IoC Container

### 1.1 Introducing to the Spring IoC Container and Beans

- IoC는 DI(Dependencies Injection, 의존성 주입)으로 알려져있다.
- DI는 오브젝트들의 의존성을 정의하는 과정이다.
- 이 과정은 오직 (같이 사용되는 다른 오브젝트들) 생성자 인수, 팩토리 메서드 인수 또는 프로퍼티 설정 객체 인스턴스는 팩토리 메서드에 의해 생성되거나 반환된 후에 생성된다.
- 컨테이너는 이 의존성들을 빈(Bean)을 생성할때 주입한다.
- 이 프로세스는 기본적으로 서비스 로케이터 패턴과 같은 메커니즘 또는 클래스의 직접 구성을 사용하여 종속성의 인스턴스화 또는 위치를 제어하는 ​​빈 자체의 역전(따라서 이름, Inversion of Control)입니다.
- ` org.springframework.beans``org.springframework.context ` 스프링 프레임워크의 IoC 의 기본적인 패키지이다.
- `Bean Factory`인터페이스는 어떤 타입의 객체든 관리할 수 있는 유용한 구성 매커니즘을 제공한다.
- `Application Context`는 빈 팩토리의 서브 인터페이스이다.
  - Spring 의 AOP 기능과의 간편한 통합
  - 메시지 리소스 처리(국제화에 사용)
  - 이벤트 개시
  - 웹 어플리케이션에서 사용하기 위한 `WebApplicationContext`와 같은 애플리케이션 계층 특정 컨텍스트
- 요약하자면, `BeanFactory`는 프레임워크 설정과 기본적인 기능을 제공하고, `ApplicationContext`는 더 많은 엔터프라이즈 특정 기능을 추가한다.
- `ApplicationContext`는 `BeanFactory`의 완전한 상위 집합이고, Spring IoC 컨테이너 설명에서 이 장에서만 독점적으로 사용된다.
- Spring에서 애플리케이션의 백본을 형성하고 Spring IoC 컨테이너에 의해 관리되는 객체를 빈이라고 한다.
-
