# JVM GC 구조와 종류

- Heap 메모리 영역을 위한 기능
- 더 이상 참조되지 않는 메모리 영역을 찾아내 제거
  - stack으로부터 더이상 탐색되지 않거나 참조되지 않는 메모리를 제거
- 세대를 나눠서 참조함
  - 오래된 객체와 젊은 객체 탐색

## Minor GC

- 사용되지 않는 객체들을 마킹함
- 마킹된 객체를 제거한다
- 메모리 압축 memory compacting

## 접근 불가능 상태 GC

- 세대별 메모리 구분
  - Young, Old, Permanent generation
  - minor gc는 young generation에서 일어남
- scavenge 알고리즘 이용
