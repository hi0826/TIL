# Array

## arrayOf(elements: T)

- 값을 직접 입력하여 배열 생성
- Array의 크기가 입력된 값에 따라 정해짐
- `val array: Array<Int> = arrayOf(1, 2, 3)`

## arrayOfNulls(size: Int)

- array의 크기만 지정, 초기 null 세팅
- `val arrOfNulls: Array<Int?> = arrayOfNulls<Int>(3)`

## emptyArray()

- 크기가 0인 Array생성, 아무런 값도 들어있지 않음
- `val emptyArray = Array<Int> = emptyArray()`

## Array(size: Int, init: (Int) -> T)

- 람다식을 사용해 생성 가능하다 `IntArray`와 같은 Primitive Type Array도 생성 가능 Array의 생성자를 이용해 생성
- `val arrayLambda: Array<Int> = Array(5) { index -> index }`
- `val intArrayLambda: IntArray = IntArray(5) { index -> index }`

### IntRange를 통한 Array 생성

- IntRange는 바로 Array로 변환하지 못해 List 로 변환후 변환이 가능
- `val intArrayWithRange: Array<Int> = (0..10).toList().toTypedArray()`
- `val intArrayWithRange: IntArray = (0..10).toList().toIntArray()`
