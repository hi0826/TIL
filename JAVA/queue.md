# Queue

## add

- 해당 큐 맨 뒤에 값 삽입
  - 성공시 return true
  - 큐가 꽉 찬 경우 IllegalStateException 에러 발생

## offer

- 해당 큐 맨 뒤에 값 삽입
  - 성공시 return true
  - 실패시 return false

## remove

- 큐 맨 앞에 있는 값 반환 후 삭제
- 비어있을 경우 NoSuchElementException 에러 발생

## poll

- 맨 앞에 있는 값 반환 뒤 삭제
- 비어있을 경우 null

## clear

- 큐 비우기

## element

- 큐의 맨 앞에 있는 값 반환
- 비어있을 경우 NoSuchElementException 에러 발생

## peek

- 큐의 맨 앞에 있는 값 반환
- 비어있을 경우 null 반환
