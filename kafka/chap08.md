# 카프카 버전 업그레이드와 확장

## 카프카 버전 업그레이드

- 버전 확인하기
  - 현재 버전과 패치할 버전의 호환성 확인
- 다운 타임을 가질 수 없는 경우 롤링 업그레이드
- 순서대로 카프카 버전 맞추기
  1. 브로커 프로토콜과 메시지 포맷 버전은 기존 버전과 동일하게 실행
  2. 모든 브로커에서 프로토콜 버전과 메시지 포맷 버전 제거
  3. 한 대씩 재시작

## 카프카 확장

- 카프카의 과부화로 파티션이 늘어날 경우
- 서버 부하가 적은 시간에 reassign topic 을 할 것
