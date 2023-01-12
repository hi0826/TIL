# tomcat

## tomcat error page setting

- tomcat의 기본 error page (white-label-page)에 버전이 나옴.
- 버전이 나오게 되면 해당 버전의 취약점을 알 수 있기 때문에, 주의해야 한다.
- conf/web.xml 에서 error page 에 대한 설정을 할 수 있다.

```xml
<error-page>
    <error-code>404</error-code>
    <location>/error-404.html</location>
</error-page>
```

- `/error-404.html`에서 `/`인 루트 디렉터리는 `webapps/ROOT` 를 의미
- `webapps/ROOT`에 `error-404.html` 파일을 추가해주자
- 후에 tomcat을 재실행
