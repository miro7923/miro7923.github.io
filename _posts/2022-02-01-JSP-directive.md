---
title: JSP 지시어(Directive)
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
# 👀 JSP 지시어란?(Directive)

```jsp
<%@ ... %>
```

* `JSP` 파일 내에서 `JSP`를 실행할 컨테이너에서 해당 페이지를 어떻게 처리할 것인가에 대한 설정 정보들을 지정해주는 데 사용
* `page` 지시어, `include` 지시어, `taglib` 지시어 3가지로 나누어진다.

## 1. page 지시어

```jsp
<%@ page 속성1="값1" 속성2="값2" 속성3="값3"... %>
```

* `JSP` 페이지에 대한 속성을 지정하는 지시어
* 속성에는 스크립트 언어, import할 패키지/클래스, 세션 사용 여부, 에러 페이지 등 12개의 설정 정보를 지정해 사용할 수 있다.

|속성|사용법|기본값|설명|
|---|---|---|---|
|language|language="java"|java|스크립트 요소에서 사용할 언어 설정|
|extends|extends="클래스명"|없음|상속받을 클래스를 설정|
|import|import="패키지.클래스명"|없음|import할 패키지.클래스 설정|
|session|session="true"|true|HttpSession 사용 여부를 설정|
|buffer|buffer="16kb"|8kb|JSP 페이지의 출력 버퍼 크기를 설정|
|autoFlush|autoFlush="true"|true|출력 버퍼가 다 찼을 경우 처리 방법을 설정|
|isThreadSafe|isThreadSafe="true"|true|다중 스레드의 동시 실행 여부를 설정|
|info|info="페이지 설명"|없음|페이지 설명|
|errorPage|errorPage="에러 페이지.jsp"|없음|에러 페이지로 사용할 페이지를 지정|
|contentType|contentType="text/html"|text/html;charset=ISO-8859-1|JSP 페이지가 생성할 문서의 타입을 지정|
|isErrorPage|isErrorPage="false"|false|현재 페이지를 에러 페이지로 지정|
|pageEncoding|pageEncoding="euc-kr"|ISO-8859-1|현재 페이지의 문자 인코딩 타입 설정| <br>

* 각각의 속성을 하나의 `page 지시어`에 한 번에 지정할 수도 있으며 여러 개의 `page 지시어`에 나누어 지정할 수도 있다.
* 하지만 `import` 속성을 제외한 나머지 속성은 하나의 페이지에서 오직 한 번씩만 지정할 수 있다.<br><br>

## 2. include 지시어

```jsp
<%@ include file="header.jsp" %>
```

* 특정한 `JSP` 파일 또는 `HTML` 파일을 해당 `JSP` 페이지에 삽입할 수 있도록 하는 기능을 제공한다.
* `include`되는 `JSP` 코드 자체가 해당 `JSP` 페이지에 복사되어 더해지므로 서블릿 컴파일 과정은 `include` 되는 페이지의 수가 아무리 많아도 단 한 번만 이루어지게 된다.
* 사용되는 공통 변수값들을 추가할 때 주로 사용한다.
* `include` 지시어는 중첩 사용이 가능하기 때문에 `include`되는 파일 안에서 또 다른 파일을 `include`하여도 문제없이 동작한다.<br><br>

## 3. taglib 지시어

```jsp
<%@ taglib url="http://taglib.com/sampleURI" prefix="samplePrefix" %>
```

* `JSTL(JSP Standard Tag Library)이나 커스텀 태그 등 태그 라이브러리를 `JSP`에서 사용할 때 접두사를 지정하기 위해 사용된다.
* `uri` 속성과 `prefix` 속성으로 이루어진다.

### 🔸 uri 속성
* 태그 라이브러리에서 정의한 태그와 속성 저보를 저장한 `TLD(Tag Library Descriptor)` 파일이 존재하는 위치 지정

### 🔸 prefix 속성
* 사용할 커스텀 태그의 네임 스페이스(Name Space)를 지정
