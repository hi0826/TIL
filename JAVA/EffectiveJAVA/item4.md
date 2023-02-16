# 인스턴스화를 막으려거든 private 생성자를 사용하라

- 정적 메서드와 정적 필드만을 담은 클래스를 만들고 싶은 경우
  - java.lang.Math
  - java.util.Arrays
  - java.util.Collections
- 추상 클래스로 만드는 것도 인스턴스화를 막을 수 없음
- 컴파일러의 기본 생성자를 막기 위해 private 으로 기본 생성자를 만든다

```java
public class UtilityClass {
    private UtilityClass() {
        throw new AssertionError();
    }
}
```
