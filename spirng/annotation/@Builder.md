# @Builder

- builder 패턴을 쉽게 적용할 수 있는 lombok annotation
- 자동으로 빌더 패턴을 만들어준다.
- boolean 타입의 경우 is 가 builder에 붙으면서 이상하게 변질될 수 있음
  - ex. isisHas
- generic에서 @Builder를 사용할 경우
  - `Class.<T>builder().build()` 의 형식으로 사용할 것
