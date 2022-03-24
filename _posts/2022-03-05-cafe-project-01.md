---
title: 프로젝트) Cafe(웹 사이트) 만들기 1 - 페이지 템플릿 세팅 및 서블릿 매핑
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Project Log
tags:
    - Project
    - Cafe
    - Log
---
# 개발환경
* OpenJDK 8
* Eclipse 2021-12
* tomcat 8.5
* MySQL Workbench 8.0.19<br><br><br>

# 시작
* 2022.3.4 ~ <br><br><br>

# 주제
* 웹 백앤드 수업 중 중간 과제로 개인 프로젝트를 진행하게 되었다.
* 회원가입/로그인/탈퇴 등 기본적인 회원관리 시스템을 가진 웹 사이트를 만드는 것이다. 주어진 기한은 `한 달`
* 나는 `다음 카페`를 소규모로 만들어 보기로 했다. 평소 자주 이용하기도 했고 과제의 평가 기준에서 요구하는 기능들을 다 담고 있기도 했기 때문에 이번 기회에 구현해 보면 그동안 배운 것들을 활용하기에 좋을 거 같았다.
* 평가 기준에 사이트의 디자인 구현(HTML/CSS 등 프론트엔드)은 포함되지 않기 때문에 본인이 쓰고 싶은 HTML/CSS 템플릿을 구한 뒤 회원 관리 기능을 구현하면 된다.<br><br><br>

# 진행상황
<p align="center"><img src="../../assets/images/cafeProj01.png"></p><br>

* 오늘 진행한 것은 무료 템플릿 사이트에서 다운받은 템플릿을 이용해 기본적인 사이트 틀을 만들었다.
* 메인 페이지를 만들고 로그인 페이지와 회원가입 페이지 폼을 만들어서 연결시키는 것 까지 했다.<br><br>

```xml
<servlet>
    <servlet-name>CafeFrontController</servlet-name>
    <servlet-class>com.project.cafe.CafeFrontController</servlet-class>
  </servlet>
  
  <servlet-mapping>
    <servlet-name>CafeFrontController</servlet-name>
    <url-pattern>*.me</url-pattern>
  </servlet-mapping>
```

* 물론 `HTML`을 이용해 그냥 연결시키지 않았고 서블릿을 이용해 `Model2 MVC` 패턴을 적용시킬 계획이기 때문에 `.xml` 페이지에서 서블릿 클래스 매핑을 통해 페이지 경로를 주소창에 노출시키면서 연결하는 것이 아닌, 주소창에는 가상 주소를 보여주도록 하기 위한 틀을 만들었다.
* 매핑될 가상주소는 하나로 고정시키는 것이 아닌, 페이지별 용도에 따라 다른 주소를 출력할 수 있도록 `*`을 사용해 맨 뒤에 `.me`만 붙으면 그 주소를 올바른 것으로 인식하고 대응시킬 수 있도록 했다.<br><br>

```java
import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class CafeFrontController extends HttpServlet
{
	protected void doProcess(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException 
	{
		// 1. 전달되는 가상주소 계산
		// 매핑된(.me로 끝나는) 주소를 받아옴
		String requestURI = req.getRequestURI();
		System.out.println("requestURI : " + requestURI);
		
		// 매핑된 해당 프로젝트 주소를 구함
		String ctxPath = req.getContextPath();
		System.out.println("ctxPath : " + ctxPath);
		
		// 매핑된 주소 - 프로젝트 주소 = 계속 바뀔 뒷자리 주소 구함
		String command = requestURI.substring(ctxPath.length());
		System.out.println("command : " + command);
		
		System.out.println("C : 가상주소 계산 완료\n");
		// 1. 전달되는 가상주소 계산
		
		
		// 2. 가상주소 매핑
		ActionForward forward = null;
		
		if (command.equals("/main.me"))
		{
			System.out.println("C : 메인페이지 호출");
			
			forward = new ActionForward();
			forward.setPath("./main/main.jsp");
			forward.setRedirect(false);
		}
		
		System.out.println("C : 가상주소 매핑 완료\n");
		// 2. 가상주소 매핑
		
		
		// 3. 페이지 이동
		if (null != forward) // 페이지 이동정보가 있을 때
		{
			if (forward.isRedirect())
			{
				resp.sendRedirect(forward.getPath());
			}
			else 
			{
				RequestDispatcher dis = req.getRequestDispatcher(forward.getPath());
				dis.forward(req, resp);
			}
			
			System.out.println("C : 페이지 이동 완료");
		}
		// 3. 페이지 이동
	}
	
	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException 
	{
		doProcess(req, resp);
	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException 
	{
		doProcess(req, resp);
	}
}
```

* 이런 식으로 서블릿 클래스를 만든 다음<br><br>

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>

<%
  response.sendRedirect("./main.me");
%>
```

* 실제 실행시킬 페이지인 `index.jsp`에서는 매핑된 서블릿으로 연결시켜서 실행되도록 했다.<br><br>

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html lang="en">
<!-- Start Head -->
  <jsp:include page="../inc/top.jsp"></jsp:include>
<!-- End Head -->

<body class="modern">

<!--
START MODULE AREA 2: Menu 1
-->
  <jsp:include page="../inc/subTop.jsp"></jsp:include>
<!--
END MODULE AREA 2: Menu 1
-->
```

* 그리고 이런 식으로 헤더와 푸터 부분은 다른 `jsp` 페이지로 분리해서 새로운 페이지가 추가되어도 헤더와 푸터 페이지를 액션 태그를 써서 인클루드만 해 주고 헤더와 푸터 부분에서 수정 사항이 생기면 `top.jsp`, `bottom.jsp` 페이지만 수정하면 되도록 만들었다.
* 이렇게 만들어놓고 나니까 헤더 부분에서 수정할 점이 생겨도 페이지 하나만 수정하면 되니까 정말 편하다.<br><br><br>

# 마감까지 
* `D-30`
