# 3 카프카 기본 개념과 구조

## 3.1 카프카 기초 다지기

### 카프카 주요 구성 요소

- 주키퍼(ZooKeeper)
  - 아파치 프로젝트 애플리케이션 이름
  - 카프카 메타데이터 관리, 브로커 health check 담당
- 카프카, 카프카 클러스터(Kafka, Kafka cluster)
  - 여러 대의 브로커를 구성한 크러스터 이름
- 브로커(Broker)
  - 카프카 서버, 노드
- 프로듀서(Producer)
  - 메세지를 보내는 클라이언트
- 컨슈머(Consumer)
  - 메세지를 꺼내가는 클라이언트
- 토픽(Topic)
  - 카프카는 메세지 피드를 토픽으로 구분
  - 카프카 내에서 고유하다
- 파티션(Partition)
  - 하나의 토픽을 여러개로 나눈 것
  - 병렬 처리 및 고성능을 얻기 위함
- 세그먼트(Segment)
  - 프로듀서가 전송한 실제 메세지가 저장되는 파일 이름
  - 브로커의 로컬 디스크에 저장
- 메시지, 레코드(Message, Record)
  - 프로듀서가 보내는 데이터
  - 컨슈머가 읽어가는 데이터

### 3.1.1 리플리케이션(replication)

- 메세지들을 여러 개로 복제, 브로커들에 분산 시키는 동작
- 하나의 브로커가 종료되어도 안정성을 유지할 수 있다.
- `--replication-factor` 옵션으로 개수 조절
- 리플리케이션 팩터가 증가할 수록 안정성은 높아지지만, 리소스 사용이 늘어남 (복제 오버헤드 발생)
- 토픽 생성시 리플리케이션 팩터에 대한 기준을 세우자
  - 예시) test, dev : factor 1
  - real : factor 2
  - real(strict) : factor 3

### 3.1.2 파티션(partition)

- 토픽 하나를 여러 개로 나눠 병렬 처리, 분산 처리가 가능하도록 만든 것
- 파티션 수만큼 컨슈머 연결이 가능
- 파티션 번호는 0부터 시작
- 초기 생성후 +는 가능하지만, -는 불가하므로 작게 생성 후, 늘려나갈 것
  - 컨슈머 LAG을 모니터링하면서 늘려나가자
  - LAG = produce message - consume message
  - 컨슈머에 지연이 없는지 확인
  - 자세한 내용은 7장

### 3.1.3 세그먼트(segment)

- 로그 파일의 형태, 브로커의 로컬 디스크에 저장
- 프로듀서가 토픽으로 메세지 전송
- 파티션이 하나일 경우 파티션0의 세그먼트 로그 파일에 저장
- 컨슈머가 읽어감

## 3.2 카프카의 핵심 개념

- 카프카의 높은 처리량, 안정성을 지니게 된 특성

### 3.2.1 분산 시스템

- 분산 시스템은 높은 성능, 장애 대응에 유리
- 브로커를 추가하는 방식으로 간단하게 확장이 가능하다
- 확장에 용이함

### 3.2.2 페이지 캐시

- OS의 페이지 캐시 사용, 잔여 메모리를 활용
- 디스크 I/O 접근이 줄어 성능을 높일 수 있다

### 3.2.3 배치 전송 처리

- 수많은 메세지 통신을 묶어서 처리

### 3.2.4 압축 전송

- 압축 전송 권장
- 높은 압축률이 필요한 경우 `gzip, snappy`
- 빠른 응답 속도가 필요한 경우 `lz4, zstd`
- 타입별 테스트 후 결정할 것

### 3.2.5 토픽, 파티션, 오프셋(offset)

- 토픽 > 파티션 > 오프셋
- 오프셋은 파티션의 메세지가 저장되는 위치
- 각 파티션에서의 오프셋은 고유한 숫자
- 오프셋을 통해 메세지의 순서를 보장, 컨슈머에서 마지막 읽은 위치를 알 수 있다

### 3.2.6 고가용성 보장 (4장)

- 리플리케이션 제공
- 토픽의 파티션을 복제 (토픽 복제 X)
- 리더(leader), 팔로워(folloser)
- 리더는 1, 나머지 팔로워
  - 프로듀서, 컨슈머의 모든 읽기, 쓰기 요청을 처리
  - 오직 리더로부터 리플리케이션
- 팔로워가 무작정 많다고 다 좋은 것은 아니다
- 카프카에서는 리플리케이션 팩터 3을 권장

### 3.2.7 주키퍼의 의존성 (카프카 분리 예정)

- 하둡(Hadoop)의 서브 프로젝트에서 출발
- 분산 애플리케이션의 코디네이터 역할
- 여러대의 서버를 앙상블(클러스터)로 구성, 살아있는 노드가 과반수 이상일 경우 지속적인 서비스가 간으한 구조
  - 반드시 서버는 홀수로 구성
- 지노드(znode)를 이용해 메타 정보가 주키퍼에 기록
  - 지노드를 통해 브로커의 노드, 토픽, 컨트롤러 관리
- 성능의 한계가 점점 보여 카프카에서 의존성을 제거할 에정 (11장)

## 3.3 프로듀서의 기본 동작과 예제

- 3-1 메세지 보내고 확인하지 않기 (ProducerFireForgot.java)
- 동기 전송 (ProducerSync.java)
- 콜백 예제 (PeterProducerCallback.java)
- 비동기 전송 (ProducerAsync.java)

### 3.3.1 프로듀서 디자인

- `Producer Record` : 데이터
  - `topic, value` : 필수 값
  - `partition, key` : 선택 값
- `send()`
  - 시리얼라이저(serializer)
  - 파티셔너(partitioner)
    - `partition` 지정시 해당 파티션으로 레코드 전달
    - 지정하지 않은 경우 `key`값을 가지고 파티션을 선택하여 레코드 전달, 기본적으로 RR방식
- 이후 파티션 별로 레코드들을 모아 배치 전송
- 실패시 재시도, 최종 실패 전달
- 성공시 메타데이터 리턴

### 3.3.2 프로듀서의 주요 옵션

- bootstrap.servers
  - 클라이언트가 카프카 클러스터에 처음 연결하기 위한 호스트와 포트 정보를 나타냄
- client.dns.lookup
  - 클라이언트가 하나의 IP와 연결하지 못할 경우 다른 IP로 시도하는 설정
  - default use_all_dns_ips : dns에 연결된 모든 ip를 쿼리하고 저장
  - resolve_canonical_bootstrap_servers_only : 커버로스 환경에서 FQDN을 얻기 위한 용도
- acks
  - 프로듀서가 카프카 토픽의 리더에 메세지 전송 요청 후 완료하기를 결정
  - 1 리더가 메세지를 받았는지만 확인 (default)
  - 0 빠른 전송, 메세지 손실 가능
  - -1(all) 팔로워까지 메세지를 받았는지 확인, 느림, 손실X
    - enable.idempotence true시 필수
- buffer.memory
  - 프로듀서가 데이터를 보내기 전 잠시 대기할 수 있는 전체 메모리(byte)
- compression.type
  - 압축 타입
  - `none, gzip, snappy, lz4, zstd`
- enable.idempotence
  - true시 중복없는 전송 가능
- max.in.flight.requests.per.connection
  - 하나의 커넥션에서 프로듀서가 최대한 ack 없이 전송할 수 있는 요청 수
  - 메세지 순서가 중요할 경우 1
  - enable.idempotence true시 5이하
- retries
  - 재시도 횟수
  - enable.idempotence true시 0이상
- batch.size
- linger.ms
  - 배치 전송 전 추가적인 메세지를 기다리는 시간
  - lindger.ms에 도달하면 배치 크기에 상관없이 전송
- transactional.id
  - 정확히 한 번 전송을 위해 사용하는 옵션
  - 동일 transactionalId에 한 해 정확히 한 번 보장
  - enable.idempotence true 시에 사용

### 3.3.3 프로듀서 예제

## 3.4 컨슈머의 기본 동작과 예제

### 3.4.1 컨슈머의 기본 동작

- 컨슈머 그룹이 파티션 리더에게 메시지 수신 요청을 보냄
- 파티션 수와 컨슈머 수가 1:1로 매핑되는 것이 이상적이다
- 파티션 수 < 컨슈머 수 는 바람직하지 않음
  - 남는 컨슈머 들이 대기상태로 존재하기 때문
- 컨슈머그룹의 리밸런싱 동작으로 active, standby 나눌 필요도 없다
  - 장애가 발생한 컨슈머를 동일 그룹의 다른 컨슈머가 대체

### 3.4.2 컨슈머의 주요 옵션

- bootstrap.servers
  - 브로커의 정보 입력
- fetch.min.bytes
  - 한 번에 가져올 수 있는 데이터의 최소 크기
  - 지정한 크기 보다 작을 경우 누적될 때까지 대기
- group.id
  - 컨슈머가 속한 컨슈머 그룹 식별용
- heartbeat.interval.ms
  - 컨슈머의 상태가 active인지 판단(heartbeat)
  - session.timeout.ms 보다 낮게 설정, 일반적으로 1/3
- max.partition.fetch.bytes
  - 파티션당 가져올 수 있는 최대 크기
- session.timeout.ms
  - 컨슈머가 종료된 것인지 판단
  - 컨슈머는 주기적으로 heartbeat를 보내야한다
  - 이 시간을 넘을경우 해당 컨슈머는 종료된 것으로 판단, 그룹에서 제외 후 리밸런싱
- enable.auto.commit
  - 백그라운드로 오프셋을 커밋
- auto.offset.reset
  - 카프카에서 초기 오프셋이 없거나 현재 오프셋이 존재하지 않을 경우 다음 옵션으로 reset
  - earliest : 가장 초기 오프셋
  - latest : 가장 마지막 오프셋
  - none : 이전 오프셋 값을 찾지 못하면 에러
- fetch.max.bytes
  - 한 번에 가져올 수 있는 데이터의 최대 크기
- group.instance.id
  - 컨슈머의 고유한 식별자
  - 설정할 경우 static 멤버로 간주, 불필요한 리밸런싱을 하지 않는다
- isolation.level
  - 트랜잭션 컨슈머에서 사용
  - read_uncommitted : 기본값으로 모든 메세지를 읽음
  - read_committed : 트랜잭션이 완료된 메세지만 읽음
- max.poll.records
  - 한 번의 poll 요청으로 가져오는 최대 메세지 수
- partition.assignment.strategy
  - 파티션 할당 전략
  - default range
- fetch.max.wait.ms
  - fetch.min.bytes 보다 작을 경우 요청에 대한 응답을 기다리는 최대 시간

### 3.4.3 컨슈머 예제

- 3-5 오토 커밋(ConsumerAuto.java)
- 3-6 동기 가져오기(ConsumerSync.java)
- 3-7 비동기 가져오기(ConsumerAsync.java)

### 3.4.4 컨슈머 그룹의 이해

- 토픽 파티션과 일대일로 매핑되어 메세지를 가져온다
- 그룹 내 컨슈머 들은 서로의 정보를 가지고 있어 하나에 문제가 생기면 다른 컨슈머가 하던일을 대신한다
- 6장(컨슈머의 내부 동작 원리와 구현)에서 자세히

## 3.5 정리

- 페이지 캐시
- 배치 전송
- 압축 사용
- 프로듀서
- 컨슈머