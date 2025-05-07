# 사용자 정의 오류보다는 표준 오류를 활용하라

가능하다면 직접 오류를 정의하는 것보다는 최대한 표준 라이브러리 오류를 사용하는 것이 많은 개발자가 알고 있어 좋다.

사용자가 API를 더 쉽게 배우고 이해할 수 있다

- IllegalArgumentException : require, check를 통해 throw 할 수 있는 예외
- IndexOutOfBoundsException : 인덱스 파라미터의 범위를 벗어남
- ConCurrentModificationException : 동시 수정을 금지했으나, 발생
- UnsupportedOperationException : 사용자가 사용하려고 했던 메서드가 현재 객체에서는 사용할 수 없음
- NoSuchElementException : 사용자가 사용하려고 했던 요소가 존재하지 않음, 빈 Iterable에 대해 next 호출시 발생
