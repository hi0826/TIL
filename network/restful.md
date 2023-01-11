# REST api

- Representational State Transfer API

## URL

- URL 에는 정보의 자원을 표현해야 한다
- 자원에 대한 행위는 HTTP method 로 표현

### 소문자 사용

- 주소에서는 대소문자를 구분하므로, 카멜 케이스(camelCase) 대신 소문자를 이용하여 작성
- `http://localhost:8080/users/postComments`
- `http://localhost:8080/users/post-comments`

### 언더바(\_) 대신 하이픈(-)

- 하이픈의 사용도 가급적이면 지양할 것, 정확한 의미나 표현을 위해 단어의 결합이 불가피한 경우 사용
- `http://localhost:8080/users/post_comments`
- `http://localhost:8080/users/post-comments`

### 마지막에 슬래시(/) 표현 금지

- 슬래시는 계층 구분을 위한 용도, 마지막에는 사용하지 않는다
- `http://localhost:8080/users/`
- `http://localhost:8080/users`

### 행위는 포함하지 않는다

- 행위는 최대한 HTTP METHOD(GET, POST, PUT, DELETE ...)을 사용하여 나타낸다.
- `POST http://localhost:8080/users/1/delete-post/1`
- `DELETE http://localhost:8080/users/1/posts/1`

### 파일 확장자는 URL에 포함하지 않는다

- 메세지 바디 내용의 포멧을 나타내기위한 파일 확장자를 URI안에 포함시키지 않도록 주의, Accept 헤더에 포함 시킬 것
- `http://localhost:8080/users/photo.jpg`
- `GET http://localhost:8080/users/photo`
- `HTTP/1.1 Host: localhost Accept:image/jpg`

### 가급적 명사를 사용하되, 컨트롤 자원의 의미는 예외적으로 동사를 사용한다

- `http://localhost:8080/posts/duplicating`
- `http://localhost:8080/posts/duplicate`
