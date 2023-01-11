`@DateTimeFormat`

- 어노테이션을 지정하면 pattern을 사용해서 dateTimeFormat을 지정해줄 수 있다.
- `DateTime`의 기본 패턴은 `yyyy-MM-ddTHH:mm:ss`이기 때문에 request param이나 body로 받을 때 format 변경이 필요하다.

```java
public class controllerRequest {
    @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private LocalDateTime localDateTime;
}
```
