# 불필요한 객체 생성을 피하라

- 생성비용이 비싼 객체가 있다면 매번 생성하지말고 생성해놓고 사용하자
- string.matches는 성능이 중요한 상황에 반복해 사용하기에 적합하지 않음

```java
static boolean isRomanNumeral(String s) {
    return s.matches("^(?=.)M*(C[MD]|d?C{0,3})(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
}
// 개선
public class RomanNumerals {
    private static final Pattern ROMAN = Pattern.compile("^(?=.)M*(C[MD]|d?C{0,3})(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$")

    static boolean isRomanNumeral(String s) {
        return ROMAN.matchers(s).matches();
    }
}
```

- 오토 박싱을 경계하기
  - 기본 타입과 그에 대응하는 박싱된 기본 타입의 구분을 흐려주지만 완전히 없애주지 않음
- long 계산에 Long을 사용해서 성능을 떨어트리지 않기
- 박싱된 기본 타입보다는 기본 타입을 사용하고, 오토 박싱을 경계할 것
- 기존 객체를 재사용해야 한다면 새로운 객체를 만들지 말 것
  - 새로운 객체를 만들어야 할 때 재사용은 하지마라!!!
