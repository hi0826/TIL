# finalizer와 cleaner 사용을 피하라

- finalizer 는 자바 9부터 deprecated
- cleaner가 있으나, 네이티브 자원 회수용으로만 사용할 것, 이 마저도 안전하지 않으니 안전장치를 해야함
  - 불확실성과 성능 저하에 주의할 것
- finalizer, cleaner를 사용하지 않는 상황에선 gc가 알아서 하도록 두자
