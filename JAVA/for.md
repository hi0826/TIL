# forEach, for loop

- forEach를 사용할 때에는 종료 조건이 있는 경우 잘 고려해야 한다.
- 종료 조건이 있는 경우 forEach는 비효율적으로 동작할 수 있다.
  - forEach는 결국 모든 요소들을 순회하기 때문에, return 을 사용해 종료시키더라도 바로 아래의 로직을 실행하지 않을 뿐, 모든 element에 접근하여 block의 내용을 수행한다.
- 따라서 중간 로직이 없는, 최종 연산에서만 사용할 것
- Effetive Java 46
  - forEach는 최종 연산중 기능이 가장 적고 가장 '덜' 스트림 답기 때문에 스트림 계산 결과를 보고할 때 (주로 print) 의 기능을 제외하고 계산할 때는 사용하지 말자
- 스트림 병렬화 공식 문서
  - forEach에 내부 로직이 추가되면, 동시성 보장이 어려워지고 가독성이 떨어질 위험이 있다.
  - 또한 Stream 의 의도를 벗어나게 된다.
  - 원래 forEach의 설계 의도는 스트림 종료를 위한 연산
    - 로직의 수행은 종료되기 전의 Stream을 반환하는 중간 연산이 해야할 일
- 조건과 로직이 추가된다면 forEach를 쓰고 내부에 추가할 것이 아니라, 중간 스트림 연산자를 추가하자.
- for를 사용해야된다면 for Range를 사용하자