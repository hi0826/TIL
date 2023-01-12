# ln(심볼릭 링크) 명령어

## 파일 링크

- 윈도우의 바로가기와 비슷한 역할
- "하드링크"와 "소프트링크"로 나눌 수 있다.
- 리눅스에서는 파일/디렉터리를 생성할 경우 `i-node` 번호를 부여한다.
  - `ls -i`로 확인 가능
  - 파일 명이 다르더라도 번호가 같다면 내부적으로 같은 파일로 인식한다.

### 하드 링크

- 원본과 동일한 "다른 파일"
- 같은 `i-node`번호를 가지고 있다
- 원본 파일/하드 링크 파일 둘 중 하나를 수정하면 다른 쪽에도 영향이 간다.
- 원본 파일이 삭제되어도 데이터 손실이 일어나지 않는다. (복사의 개념)

### 소프트 링크(심볼릭 링크)

- 원본 파일을 가르키는 포인터를 가지고 있다.
- 원본 파일과는 `i-node`번호가 다르기에 다른 파일로 인식 됨
- 일반적으로 디렉터리의 경로 단축이나 변경에 사용
- 원본 파일이 삭제되면 아무런 구실을 할 수 없음

### ln 명령어 사용

- `ln [option] [origin] [target]`

```text
-s(--symbolic): 심볼릭 링크를 생성시에 사용
-S(--suffix): 백업 파일 생성시 원하는 접미사 지정
-v(--verbose): 링크 만드는 정보를 자세히 출력
-b(--backup): 대상 파일이 이미 존재할 경우 백업 파일을 만든 후 링크파일 생성
-f(--force): 링크 파일 존재 시 삭제하고 생성
-i(--interactive): 대상 파일이 존재할 경우 대상 파일을 지울것인지 물어봄
-d(-F, --directory):allow the superuser to attempt to hard link
                                directories (note: will probably fail due to
                                system restrictions, even for the superuser)
-L(--logical): dereference TARGETs that are symbolic links
-n(--no-deference): treat LINK_NAME as a normal file if
                                it is a symbolic link to a directory
-P(--physical): make hard links directly to symbolic links
-r(--relative): create symbolic links relative to link location
-t(--target-directory): specify the DIRECTORY in which to create
                                the links
-T(--no-target-directory=DIRECTORY): treat LINK_NAME as a normal file always
```
