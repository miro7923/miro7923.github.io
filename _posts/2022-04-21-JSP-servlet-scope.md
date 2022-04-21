---
title: JSP & Servlet) 영역객체(Scope) - Page, Request, Session, Application 차이
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - JSP
tags:
    - JSP
    - Servlet
    - Scope
---
<br>
# 👀 영역객체(Scope)란?
* `JSP` 내장객체로 특정 공간(Scope)에 정보를 저장하고, 저장된 정보(Attribute)를 공유할 수 있는 객체

<p align="center"><img src="https://t1.daumcdn.net/cfile/tistory/99E89A3C5C6D04451B" width="600"></p><br><br><br>

# 4가지 영역별 차이
* `Page` : 페이지 내에서만 지역 변수처럼 사용할 수 있다.
* `Request` : 한 페이지에서 다른 페이지로 전달해서 사용할 수 있다.
* `Session` : 웹 브라우저의 상태가 유지되는 동안 사용 가능하다.
* `Application` : 웹 어플리케이션에 있는 모든 대상이 사용 가능하다.

## Page
* `Page` 영역 안에서만 사용 가능. 지역 변수 개념
* 영역객체 : `pageContext`
* 사용범위 : 해당 `JSP` 페이지 (다른 페이지로 넘겨줄 수 없다)
* `JSP`에서 `pageScope`에 값을 저장한 후 해당 값을 `EL표기법`, `JSTL`에서 사용할 때 사용된다.

## Request
* 어떤 페이지에 있는 값을 서블릿이나 다른 페이지로 넘겨주고 싶을 때 사용할 수 있다.
* 영역객체 : JSP 페이지) `request` / 서블릿) `HttpServletRequest`
* 사용범위 : 클라이언트 요청이 처리되는 페이지
* 값을 저장할 때엔 `request.setAttribute("속성명", 값)` 메서드를 사용한다.
* 저장된 값을 꺼내올 때엔 `request.getAttribute("속성명")` 메서드를 사용한다.
* `GET/POST` 방식으로 날아온 데이터를 가져올 때엔 `getParameter("파라미터 이름")` 메서드를 사용한다.
* `forward`시 값을 유지하고자 사용한다.

## Session
* 로그인 한 회원의 상태유지와 같이 웹 브라우저에서 상태가 지속적으로 유지되어야 할 필요가 있을 때 사용한다.
* 영역객체 : JSP 페이지) `session` / 서블릿) `HttpServletRequest`의 `getSession()` 메서드를 사용해 얻음
* 사용범위 : 세션이 유지되는 동안
* 값을 저장할 때엔 `session.setAttribute("속성명", 값)` 메서드를 사용한다.
* 저장된 값을 꺼내올 때엔 `session.getAttribute("속성명")` 메서드를 사용한다.
* `session` 지속시간이 만료되거나 웹 브라우저가 종료되면 사라진다.
* 웹 브라우저의 탭 간에는 공유되기 때문에 각각의 탭에서 같은 세션 정보를 사용할 수 있다.

## Application
* 웹 어플리케이션이 시작되고 종료되는 동안 사용할 수 있다.
* 영역객체 : JSP 페이지) `application` / 서블릿) `getServletContext()` 메서드를 사용해 얻음
* 사용범위 : 웹 애플리케이션(서버)이 실행되는 동안
* 값을 저장할 때엔 `application.setAttribute("속성명", 값)` 메서드를 사용한다.
* 저장된 값을 꺼내올 때엔 `application.getAttribute("속성명")` 메서드를 사용한다.
* 모든 클라이언트가 공통으로 사용해야 할 값이 있을 때 사용한다. (클라이언트가 바뀌어도 누적된다)<br><br><br>

# 참고
* [Scope - Page, Request, Session, Application](https://zester7.tistory.com/46)
