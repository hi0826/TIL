# private 생성자나 열거 타입으로 싱글턴임을 보증하라

## 싱글턴

- 인스턴스를 오직 하나만 생성할 수 있는 클래스
- 무상태 객체나 설계상 유일해야하는 시스템 컴포넌트
- 클라이언트를 테스트하기가 어려워질 수 있음

### 생성방식

- 생성자를 private으로 감추고, 유일한 인스턴스에 접근할 수 있는 수단으로 public static 멤버를 하나 마련

```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() {}

    public void leaveTheBuilding() {}
}
```

- private 생성자는 `Elvis.INSTANCE`를 초기화 할 때 딱 한번만 호출
- 리플렉션 API인 `AccessibleObject.setAccesible`을 사용해 private생성자에 접근 가능함
- 생성자를 수정하여 두번째 객체가 생성될 때 예외를 사용해 막을 수 있다
- 싱글턴임을 명백히 드러냄
- 간결함

```java
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() {}
    public static Elvis getInstance() { return INSTANCE; }

    // 싱글턴임을 보장하는 readResolve()
    public Object readResolve() {
        // 진짜 Elvis를 반환하고 가짜 Elvis는 가비지 컬렉터에 맡긴다
        return INSTANCE;
    }
    public void leaveTheBuilding() {}
}
```

- 정적 팩터리 메서드를 `public static` 멤버로 제공하는 방법
- `Elvis.getInstance()` 메서드는 항상 같은 인스턴스만을 반환
- 리플렉션 API 예외의 경우 똑같이 적용
- API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다는 점
  - 메서드를 수정하면 됨
- 정적 팩터리 메서드를 제네릭 싱글턴 팩터리로 만들 수 있다(item 30)
- 정적 팩터리 메서드 참조를 supplier로 사용할 수 있다
  - 굳이 필요없다면 public필드 방식이 좋음

```java
public enum Elvis {
    INSTANCE;

    public void leaveTheBuilding() {}
}
```

- Enum을 사용해서 만드는 방법
  - public 필드 방식과 유사하지만 더 간결하고, 직렬화할 수 있고, 리플렉션 공격등에 새로운 인스턴스가 생기는 일을 막아준다
- 대부분 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법
- Enum외의 클래스를 상속해야한다면 사용 불가능한 방법
  - 열거 타입이 다른 인터페이스를 구현하도록 설계는 가능

### 직렬화

- 위 방식대로 만든 싱글턴 클래스를 직렬화 하려면 Serializable을 구현한다고 선언하는 것만으로는 부족
- 모든 인스턴스 필드를 일시적(transient)라고 선언, readResolve 메서드를 제공해야 한다
  - 직렬화된 인스턴스를 역직렬화 할 때마다 새로운 인스턴스가 만들어지는 것을 방지
- readResolve 메서드 추가도 방법
