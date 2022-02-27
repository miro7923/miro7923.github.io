---
title: JSP) 서블릿
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - JSP
tags:
    - JSP
    - Servlet
---
# 👀 서블릿(Servlet)이란?
* 자바를 이용해서 웹 개발을 하기 위한 기술로 동적인 데이터를 처리하는 페이지인 `JSP` 파일을 최종적으로 사용하려면 자바 클래스 파일로 만들어야 하는데 서블릿은 그 중간 과정이라 할 수 있다.
* 즉 `JSP(.jsp) -> Servlet(.java) -> 클래스파일(.class)` 이런 과정을 거치게 된다.

## 서블릿 작성 규칙
1) javax.servlet.Servlet 인터페이스 구현<br>
2) 1)의 구현이 어려운 경우 javax.servlet.http.HttpServlet 클래스 상속(일반적으로 사용)<br>
3) doGet()/ doPost() 생성(오버라이딩), HttpServletRequest/ HttpServletResponse 객체 구현<br>
4) IOException/ ServletException을 처리해야 함<br>
5) web.xml 파일을 통해서 주소 매핑 (어노테이션 처리)<br>

## 서블릿 실행 구조

<img src="../../assets/images/servletProcess.png"><br><br><br>

# 서블릿 클래스 만들기

