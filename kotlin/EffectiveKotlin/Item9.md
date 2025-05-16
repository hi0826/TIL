# Use를 사용하여 리소스를 닫아라

use 메서드를 사용해서 Closable 객체를 사용하기

- 코드가 간단하면서도 try-finally를 사용하면서 생기기 쉬운 블록내 오류 처리등에 이점이 있음
- Closeable / AutoCloseable을 구현한 객체를 쉽고 안전하게 처리 가능
- 파일을 읽는다면 useLines 사용
