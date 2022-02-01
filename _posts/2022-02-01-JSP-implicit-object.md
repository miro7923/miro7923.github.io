---
title: JSP 내장 객체
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - JSP
tags:
    - JSP
    - WEB
---
# 👀 내장 객체란?
* `JSP` 페이지가 웹 컨테이너에 의해서 서블릿으로 변환할 때 웹 컨테이너가 자동으로 생성해 주는 객체(`클래스`, `import` 구분없이 사용 가능)<br><br>

javax.servlet 패키지 - 8개<br>

|내장 객체 변수명|설명|
|---|---|
|request|클라이언트의 `HTTP` 요청 정보를 저장한 객체(`HTTP`헤더 정보, 파라미터 등)|
|response|클라이언트 요청에 대한(`HTTP`) 응답 정보를 저장한 객체|
|session|클라이언트의 세션 정보를 저장한 객체|
|pageContext|페이지 실행에 필요한 컨텍스트 정보를 저장한 객체|
|out|응답 페이지 정보를 전송하기 위한 출력 스트림 객체|
|application|동일한 애플리케이션의 컨텍스트 정보를 저장한 객체|
|config|해당 페이지의 서블릿 설정 정보(초기화 정보)를 저장한 객체|
|page|해당 페이지 서블릿 객체(인스턴스)|<br><br>

java.lang 패키지 - 1개<br>
* exception : 예외 처리를 위한 객체

## 1. request 객체
* 사용자의 요청에 관한 정보를 얻기 위한 객체<br><br>

* 서버 도메인명 : ```request.getServerName();```
* 서버 포트번호 : ```request.getServerPort();```
* URL : ```request.getRequestURL();```
* URI : ```request.getRequestURI();```
* 클라이언트 호스트명 : ```request.getRemoteHost();```
* 클라이언트 IP주소 : ```request.getRemoteAddr();```
* 프로토콜 : ```request.getProtocol();```
* 페이지 요청(전송)방식 : ```request.getMethod();```
* 프로젝트 경로 : ```request.getContextPath();```
* 물리적 경로 : ```request.getRealPath("/");```
* http헤더 (user-agent): ```request.getHeader("user-agent");```
* http헤더 (accept-language) : ```request.getHeader("accept-language");```
* http헤더 (host) : ```request.getHeader("host");```
* http헤더 (connection) : ```request.getHeader("connection");```

* 전송을 통해 다른 페이지에서 전달받은 (이름 등의)정보를 얻을 때

```jsp
String name = request.getParameter("name");
```
<br>

* 정보들을 배열로 얻을 때

```jsp
String hobbies[] = request.getParameterValues("hobby");
```

### 🔸 URI
* URI는 특정 리소스를 식별하는 `통합 자원 식별자(Uniform Resource Identifier)`를 의미한다. 
* 웹 기술에서 사용하는 논리적 또는 물리적 리소스를 식별하는 고유한 문자열 시퀀스다.

### 🔸 URL
* URL은 흔히 웹 주소라고도 하며, 컴퓨터 네트워크 상에서 리소스가 어디 있는지 알려주기 위한 규약이다. URI의 서브셋이다.
* 한마디로 URI가 자원 자체에 대한 고유 식별자라면 URL은 자원이 실제로 존재하는 위치를 가리킨다. <br><br>
![uri-url](../../assets/images/uri-url.png)<br><br>

## 2. response 객체
* 클라이언트의 요청에 대한 `HTTP` 응답(HTTP Response)을 나타내는 객체<br><br>

* response.setHeader("헤더이름", 값);

```jsp
response.addHeader("Refresh", "3"); // 3초에 한번씩 새로고침
response.addHeader("Refresh", "3;url=http://www.naver.co.kr"); // 3초 후에 다음 페이지로 이동
```

* response.sendRedirect("주소"); // "주소"로 요청 재전송

```jsp
response.sendRedirect("http://www.naver.co.kr"); // 해당 페이지로 바로 이동
```

* ```response.setContentType("속성값"); 컨텐츠 타입 지정```
* ```response.addCookie("쿠키값"); 쿠키 추가```

## 3. session 객체
* 클라이언트의 정보가 유지되어야 할 필요가 있는 경우를 위해 가상 연결을 구현해주는 세션<br><br>

* 세션ID값 : ```session.getId();```
* 세션생성시간 정보(ms) : ```session.getCreationTime();```
* 최종 접속 시간(ms) : ```session.getLastAccessedTime();```
* 세션 유지시간(기본)(1800s,30m) : ```session.getMaxInactiveInterval();```<br>

## 4. application 객체
* 해당 웹 애플리케이션의 실행 환경을 제공하는 서버의 정보와 서버측 자원에 대한 정보를 얻어내거나 해당 애플리케이션의 이벤트 로그를 다루는 메소드들을 제공<br><br>

* 서버정보 : ```application.getServerInfo();```
* 서버의 물리적 경로 : ```application.getRealPath("/");```

## 5. out 객체
* 서블릿/JSP 컨테이너가 응답 페이지를 만들기 위해 사용하는 출력 스트림 객체
* 하지만 표현식을 사용해서 자바 코드의 변수 값들과 메소드의 리턴 값들을 출력할 수 있기 때문에 잘 사용되지 않는다.<br><br>

* 출력 : ```out.print("Hello");```
* 버퍼 사이즈 : ```<%=out.getBufferSize() %>byte<br>```
* 버퍼 사용후 : ```<%=out.getRemaining() %>byte<br>```
