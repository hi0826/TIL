# Ansible

## 개요

- 여러 서버를 효율적으로 관리할 수 있게 도와주는 환경 구성 자동화 도구
- `플레이북` 이라는 곳에 실행할 구성을 선언하면, 필요할 때마다 자동으로 실행 시킬 수 있음
- 웹 서버의 구성, DB 서버의 구성을 선언하면 관리자들은 필요할 때마다 그 구성대로 서버의 설정을 배포할 수 있게 해준다.

## 역할

- 한 명의 관리자가 관리해야할 서버가 늘어나고 있음
  - 일일히 bash 쉘 스크립트로 관리하기엔 한계점 존재
  - 이를 위해 고안된 Infrastructure as a code 개념
    - 환경의 배포와 구성을 규격화된 코드로 정의해 사용하는 것을 의미
    - Infrastructure as a code가 가능한 자동화도구를 이용하여 인프라의 상태를 코드로 작성하고 이를 모든 서버에 배포함으로써 특정 환경을 동일하게 유지할 수 있도록 도와준다.

## 요소

### 인벤토리

- 어디서 수행할 것인가?
- 앤서블에 의해 제어될 대상을 정의 `hosts.ini`에 정의해 사용
- 다른 서버들의 ssh 접근, ip, port, linux user와 같은 접속 정보를 정의

```ini
[webserver]

web1 ansible_host = aaa.app.host
web2 ansible_host = bbb.app.host

[db]
db1 ansible_host = aaa.db1.host
db2 ansible_host = bbb.db2.host
```

### 플레이북

- 무엇을 수행할 것인가?
- 인벤토리 파일에서 정의한 대상들이 무엇을 수행할 것인지 정의하는 역할 `yaml`포맷으로 설정
- 단독으로 사용되는 것이 아닌 인벤터리와 플레이북의 조합으로 같이 사용

```yaml
- name: ngins install
  host: all
  become: true
  tasks:
    - name: ngix package install
    yum:
        name: nginx
        state: installed
```

### 모듈

- 어떻게 수행할 것인가?
- task가 어떻게 수행될지를 정의하는 요소
- 타겟 호스트로 실제 작업을 처리하는 단위로 Module이라는 개념 사용
- 앤서블은 Python Code를 호출하여 실행
  - Python이 필요, 다양한 모듈이 같이설치됨

## 앤서블의 멱등성

- 멱등성(Idempotency)라는 특징을 가짐
- 여러 번 적용해도 동일한 결과, 수정된 부분이 있다면 그 부분만 새롭게 반영
