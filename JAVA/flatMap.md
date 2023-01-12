# flatMap()

- 스트림 평면화
- 중복 구조로 되어있는 리스트를 평면화 할 수 있음

```java
List<String> animals = ["cat", "dog"];
// ["c", "a", "t", "d", "o", "g"] 의 결과를 얻고 싶음

animals.stream()
    .map(animal -> animal.split(""))
    .collect(Collectors.toList());
// [["c", "a", "t"], ["d", "o", "g"]]

animals.stream()
    .map(animal -> animal.split(""))
    .flatMap(Arrays::stream)
    .collect(Collectors.toList())
// ["c", "a", "t", "d", "o", "g"]
```
