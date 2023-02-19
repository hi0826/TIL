# 자원을 직접 명시하지말고 의존 객체 주입을 사용하라

- 맞춤법 검사기 예시
- 맞춤법 검사기는 사전에 의존하고 있음
- 정적 유틸리티 클래스, 싱글턴으로 만들기에는 부적절하다
- 내부에서 final로 선언한 사전 객체는 종류를 바꿀 수 없고, 이 영향으로 테스트나 활용이 매우 불편해짐
- 사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않음
- 인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주는 방식을 사용해야함
  - 의존 객체 주입
    - spring

```java
public class SpellChecker {
    private static final Lexicon dictionary;

    // 객체 생성 방지
    private SpellChecker() {}

    public static boolean isValid(String word) {}
    public static List<String> suggestions(String typo) {}
}

public class SpellChecker2 {
    private final Lexicon dictionary;

    private SpellChecker2() {}
    public static SpellChecker2 INSTANCE = new SpellChecker2();

    public boolean isValid(String word) {}
    public List<String> suggestions(String typo) {}
}

public class NewSpellChecker {
    private final Lexicon dictionary;

    public NewSpellChecker(Lexicon dictionary) {
        this.dictionary = Objects.requireNonNull(dictionary);
    }

    public boolean isValid(String word) {}
    public List<String> suggestions(String typo) {}
}
```
