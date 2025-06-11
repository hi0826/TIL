# 제네릭 타입과 variance 한정자를 활용하라

제네릭 클래스에서 variance 한정자를 활용해서 클래스 간의 연관성을 만들어주자

기본적으로 제네릭은 invariant 상태

out 을 붙여주면 covariant

in 을 붙여주면 contravariant

```kotlin
// invariant
class Cup<T>

// covariant 상하 관계
class Cup<out T>
open class Dog
class Puppy: Dog()
Cup<Dog> = Cup<Puppy>
Cup<Puppy> != Cup<Dog>

// contravariant 역 상하 관계
Cup<Any> != Cup<Int>
Cup<Int> = Cup<Any>
```

## 함수타입 (Item35)

함수 타입은 파라미터 유형과 리턴 타입에 따라서 서로 어떤 관계를 갖는다

코틀린 함수 타입의 모든 파라미터 타입은 contravariant(in)이고, 모든 리턴 타입은 covariant(out) 이다

함수 타입을 사용할 때는 자동으로 variance 한정자가 사용된다

코틀린에서 자주 사용되는 것으로는 covariant를 가진 List

## variance 한정자의 안정성

자바의 배열은 covariant, 배열을 기반으로 제네릭 연산자는 정렬 함수등을 만들기 위함 > 큰 문제가 발생할 수 있다

```java
Integer[] numbers = {1, 4, 2, 3};
Object[] objects = numbers;
objects[2] = "B"; // error
```

컴파일중엔 아무 이상없으나, 런타임에서 오류 발생

이런 결함을 막기 위해 코틀린은 invariant 로 만들었다

public in 한정자 위치에 covariant 타입 파라미터가 오는 것을 금지하여 업캐스팅을 막는다

private 으로 제한하면 covariant 타입을 막을 수 있음 > 가시성 제한

## variance 한정자의 위치

1. 선언 부분 (일반적인 사용 위치)
   - 클래스와 인터페이스 선언에 한정자 적용
2. 클래스와 인터페이스를 활용하는 위치
   - 특정 변수에만 variance 한정자 적용
   - 특정 인스턴스에만 적용할 때 사용

mutableList 는 in 한정자 사용을 금지하는데, 단일 파라미터 타입에 in은 허용 > 여러가지 타입을 받을 수 있음

## 정리

코틀린은 타입 아규먼트의 관계에 제약을 걸 수 있는 강력한 제네릭 기능을 제공한다.

제네릭 객체를 연산할 때 다양한 지원을 받을 수 있음

- 타입 파라미터의 기본적인 variance 동작은 invariant, A가 B의 서브 타입이라고 할 때 `Cup<A> Cup<B>`는 아무 관계를 가지지 않음
- out 한정자는 타입 파라미터를 covariant 하게 만드는데, A가 B의 서브 타입이라고 할 때 `Cup<A> Cup<B>`는 cup a가 cup b 의 서브 타입이 된다, out 위치에 사용 가능
- in 한정자는 타입 파라미터를 contravariant 하게 만드는데, A가 B의 서브 타입이라고 할 때 cup b는 cup a의 슈퍼타입이 된다, in 위치에 사용 가능
- List, Set의 타입 파라미터는 covariant, Array, MutableList, MutableSet, MutableMap의 타입 파라미터는 invariant
- 함수 타입 파라미터는 contravariant, 리턴 타입은 contravaraiant
- 리턴만 되는 타입에는 covariant를 사용한다
- 허용만 되는 타입에는 contravariant를 사용한다
