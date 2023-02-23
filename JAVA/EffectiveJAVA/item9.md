# try-finally 보다는 try-with-resources를 사용하라

- mybatis나 JPA 같은 lib를 사용하지 않는 경우
- JDBC에서 connection 을 사용할 때
- try - finally 에서 close를 넣어줘야하는데 빼트린 경우가 많음
- try, finally 모두 예외가 발생할 수 있음
- AutoClosable 인터페이스가 구현된 클래스를 try 문에 사용하여 try-with-resources를 사용하자
- 꼭 회수해야 하는 자원을 다룰 떄는 try-with-resources를 사용하자
