# 타입 파라미터의 새도잉을 피하라

섀도잉은 프로퍼티와 파라미터가 같은 이름을 가지는 경우, 지역 파라미터가 외부 스코프에있는 프로퍼티를 가르키는 경우를 말한다

섀도잉은 많이 사용되나, 제네릭을 사용할 때 문제가 될 수 있다

클래스 타입 파라미터와 함수 타입 파라미터를 사용할 때, 각 제네릭의 타입 파라미터가 독립적으로 동작하기 때문에, 의도하지 않은 동작이 발생할 가능성이 높다

만약 독립적인 타입 파라미터를 의도했다면, 이름을 바꾸는 것이 맞다(T, ST)
