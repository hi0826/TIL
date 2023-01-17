# @Controller

- @Controller는 주로 view를 반환하기 위해 사용
  - 요청
  - DispatcherServlet이 HandlerMapping을 찾음
  - HandlerMapping을 통해 요청을 Controller로 위임
  - Controller는 요청을 처리한 후 viewName 반환
  - DispatcherServlet은 ViewResolver를 통해 ViewName에 해당하는 View를 찾아 사용자에게 반환
- Controller가 반환한 view Name으로부터 view를 렌더링하기 위해서는 viewResolver가 사용되고, 설정에 맞게 view를 찾아서 렌더링
- Controller 로 Data 반환하기
  - @ResponseBody 어노테이션을 사용해서 controller에서 json type 으로 데이터를 반환할 수 있다.
- 컨트롤러에서 일반적으로 객체를 반환할 때에는 일반적으로 ResponseEntity로 감싸서 반환, viewResolver가 아닌 HttpMessageConverter가 동작한다.
  - HttpMessageConver에는 여러 Converter들이 등록되어 있고, 반환해야하는 데이터에 따라 사용되는 converter가 달라진다.
    - 문자열 StringHttpMessageConverter
    - 객체 MappingJackson2HttpMessageConverter
  - Spring은 Client Http Accept Header와 서버의 컨트롤러 반환 타입 정보를 조합 - 적합한 HttpMessageConverter를 선택하여 이를 처리
  - 작동하는 시점은 HandelrAdapter와 Controller가 요청을 주고받는 시점

## @RestController

- @Controller에 @ResponseBody가 추가된 것
- Json으로 객체 반환
- Client가 예상하는 HttpStatus를 설정해줄 수 없으므로, ResponseEntity로 감싸서 반환하자.(RestFul 입장에서)
