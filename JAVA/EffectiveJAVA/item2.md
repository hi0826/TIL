# 생성자에 매개변수가 많다면 빌더를 고려하라

- 점층적 생성자 패턴(telescoping constructor pattern)
  - 매개변수를 받는 생성자들을 하나씩 추가해서 만드는 패턴
  - 매개변수 개수가 많아지면 클라이언트 코드를 작성하거나 읽기 어려워진다
- 자바빈즈 패턴(JavaBeans pattern)
  1. 매개변수가 없는 생성자로 객체를 만든다
  2. 세터 메서드들을 호출해 원하는 매개변수의 값을 설정
  - 코드가 길어졌지만 인스턴스를 만들기 쉽고, 읽기 쉬움
  - 객체 하나를 만들려면 메서드를 여러 개 호출해야 하고, 객체가 완전히 생성되기 전까지 일관성이 무너진 상태에 놓임
    - 점층적 생성자 패턴에서는 매개변수들이 유효한지를 생성자에서 확인하면 일관성 유지가 가능
  - 일관성이 무너지기 떄문에 클래스를 불변 아이템으로 만들 수 없음
  - 안정성을 얻기위해 `freeze`를 사용해야 한다. (다루기 어려움)

## 빌더패턴(Builder pattern)

1. 필요한 객체를 직접 만드는 대신, 필수 매개변수만으로 생성자를 호출해 빌더 객체를 얻음
2. 빌더객체가 제공하는 세터 메서드로 원하는 매개변수들 지정
3. `build`메서드를 호출해 필요한 객체를 얻음

- `Builder` 객체는 생성할 클래스 안에 정적 멤버 클래스로 만들어둔다
- 빌더의 세터 메서드는 `this`를 반환한다.
  - 메서드 체이닝(method chaining)을 사용해서 연쇄적 호출이 가능
- 빌더 패턴은 파이썬과 스칼라의 named optional parameters를 흉내낸 것
- 마지막 `build`메서드에서 매개변수에 대한 불변식을 검사
- 계층적을 설계된 클래스와 사용하기 좋다
  - 추상(abstract) 클래스는 추상 빌더를
  - 구현(concrete) 클래스는 구현 빌더를 갖게함

```java
public abstract class Pizza {
    public enum Topping {
        HAM, MUSHROOM, ONION, PEPPER, SAUSAGE
    }
    final Set<Topping> toppigs;

    abstract static class Builder<T extends Builder<T>> {
        EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
        public T addTopping(Topping topping) {
            topping.add(Objects.requireNonNull(topping));
            return self();
        }

        abstract Pizza build();

        protected abstract T self();
    }

    Pizza(Builder<?> builder) {
        toppings = builder.toppings.clone();
    }
}

public class NyPizza extends Pizza {
    public enum Size { SMALL, MEDIUM, LARGE }
    private final Size size;

    public static class Builder extends Pizza.Builder<Builder> {
        private final Size size;

        public Builder(Size size) {
            this.size = Objects.requireNonNull(size);

            @Override
            public NyPizza build() {
                return new NyPizza(this);
            }

            @Override
            protected Builder self() {
                return this;
            }
        }

        private NyPizza(Builder builder) {
            super(builder);
            size = builder.size;
        }
    }
}
```

- 빌더 패턴을 사용하면 가변인수 여러 개를 사용할 수 있음
- 빌더 생성 비용이 크진 않지만, 성능이 중요하면 문제가될 수 있다
- 코드가 장황해져 매개변수가 많아지는 경향이 있음
- 생성자나 정적 팩터리가 처리해야할 매개변수가 많다면 빌더 패턴을 선택하는게 낫다.
  - 특히 매개변수 중 다수가 필수가 아니거나 같은 타입이면 더 그렇다.
  - 점층적 생성자보다 클라이언트 코드를 읽고 쓰기가 훨씬 간결하고, 자바빈즈보다 훨씬 안전하다(일관성 면)
