# web.xml

- 설정을 위한 설정 파일
- WAS가 처음 구동될 때 web.xml을 읽어 웹 애플리케이션 설정을 구성
- DispatcherServlet을 등록해주면서 스프링 설정 파일을 지정
  - 초기화 과정에서 지정된 설정 파일을 이용해 스프링 컨테이너를 초기화

```xml
<!-- The definition of the Root Spring Container shared by all Servlets and Filters -->
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/spring/root-context.xml</param-value>
</context-param>
<!-- Processes application requests -->
<servlet>
    <servlet-name>appServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/spring/appServlet/servlet-context.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>
```

- 모든 요청을 DispatcherServlet이 처리하도록 서블릿 매핑 설정
- `url-pattern`은 `*.do, /*, /`을 많이 사용
  - `*.do`는 무의미한 확장자
  - `/*`는 톰캣(conf)에 있는 기본 web.xml의 .jsp를 처리하는 설정 파일을 무시해버리게 되므로 `/`를 사용
- `servlet-mapping`을 `/`로 사용하게 되면서 `/*`을 사용하는 것과 마찬가지로 톰캣에 있는 `/`설정을 무시해버리게 된다.
  - `.jsp`, `.css`, `.js`든 파일에 직접 접근하는 경우는 없다
    - 요청은 톰캣이 받게되고 서블릿에 패턴을 요청해서 처리하는데, 이 설정을 뭉개버린 것
    - servlet-context.xml 에 코드를 추가해서 해결할 수 있음
      - `<mvc:default-servlet-handler />`
      - default-servlet-handler가 빈으로 추가됨
      - 매핑되는 핸들러가 없다면, default-servlet-handler가 요청을 처리하게 된다.
      - `<mvc:resources mapping="/resources/**" location="/resources/" />`

```xml
<servlet-mapping>
    <servlet-name>appServlet</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

# servlet-context.xml

- 이 컨텍스트에 등록되는 빈들은 서블릿 컨테이너에만 사용되어진다.
  - 컨트롤러는 서블릿 컨텍스트에 등록
- web.xml에서 DispatcherServlet 등록시 설정한 파일
- 해당 설정 파일을 이용해서 스프링 컨테이너 초기화

```xml
<!-- Enable the Spring MVC @Controller programming model -->
<annotation-driven />
<context:component-scan base-package="com.board.controller">
<!-- Resolves views selected for rendering by @Controllers to .jsp resources inthe /WEB-INF/views directory -->
<beans:bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <beans:property name="prefix" value="/WEB-INF/views/" />
    <beans:property name="suffix" value=".jsp" />
</beans:bean>
```

# root-context.xml

- 이 컨텍스트에 등록되는 빈들은 모든 컨텍스트에서 사용되어 진다.(공유 가능)
  - 서비스와 레포지터리등의 등록
  - 모든 빈들을 등록해도되지만? 용도에 따라 나눈 것 같은 느낌, 다른 이유 찾아보기
- 스프링 프로젝트 생성 시 root-context.xml에는 특별한 설정이 없음
- 공통적으로 사용하려는 Bean을 그때그때 사용
