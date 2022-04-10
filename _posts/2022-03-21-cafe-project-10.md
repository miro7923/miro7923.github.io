---
title: JAVA Servlet 프로젝트) Cafe(웹 사이트) 만들기 10 - 게시판 만들기 시작
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
* MacBook Air (M1, 2020)
* OpenJDK 8
* Eclipse 2021-12
* tomcat 8.5
* MySQL Workbench 8.0.19<br><br><br>

# 기간
* 2022.3.4 ~ 2022.4.6<br><br><br>

# 주제
* 웹 백엔드 수업 중 중간 과제로 개인 프로젝트를 진행하게 되었다.
* 회원가입/로그인/탈퇴 등 기본적인 회원관리 시스템을 가진 웹 사이트를 만드는 것이다. 주어진 기한은 `한 달`
* 나는 `다음 카페`를 소규모로 만들어 보기로 했다. 평소 자주 이용하기도 했고 과제의 평가 기준에서 요구하는 기능들을 다 담고 있기도 했기 때문에 이번 기회에 구현해 보면 그동안 배운 것들을 활용하기에 좋을 거 같았다.
* 평가 기준에 사이트의 디자인 구현(HTML/CSS 등 프론트엔드)은 포함되지 않기 때문에 본인이 쓰고 싶은 HTML/CSS 템플릿을 구한 뒤 회원 관리 기능을 구현하면 된다.<br><br><br>

# 진행상황
* 게시판을 만들기 위한 기본 세팅만 했다.
* 새로운 게시판 전용 컨트롤러를 만들고 `xml` 페이지에서 매핑시켜 주었다.

## web.xml

```xml
...
생략

<!-- 회원(Member) 처리 컨트롤러 -->
<servlet>
    <servlet-name>MemberFrontController</servlet-name>
    <servlet-class>com.project.cafe.member.action.MemberFrontController</servlet-class>
</servlet>
  
<servlet-mapping>
    <servlet-name>MemberFrontController</servlet-name>
    <url-pattern>*.me</url-pattern>
</servlet-mapping>
<!-- 회원(Member) 처리 컨트롤러 -->
  
<!-- 게시판(Board) 처리 컨트롤러 -->
<servlet>
    <servlet-name>BoardFrontController</servlet-name>
    <servlet-class>com.project.cafe.board.action.BoardFrontController</servlet-class>
</servlet>
  
<servlet-mapping>
    <servlet-name>BoardFrontController</servlet-name>
    <url-pattern>*.bo</url-pattern>
</servlet-mapping>

...
생략
```

* 게시판용 컨트롤러를 추가하기 위해서 서블릿 매핑을 추가해 주었다.
* 두 번째 하니까 좀 더 쉽게 느껴진다. ☺️

## ActionForward.java

```java
package com.project.cafe.action; // MemberController에서도 같이 사용하는 클래스라서 다른 패키지 사용

public class ActionForward 
{
    // 페이지를 이동할 때 필요한 정보를 저장하는 클래스
	
    private String path; // 이동경로
    private boolean isRedirect; // 이동방식
	
    // isRedirect = true  => 주소가 바뀌고 화면도 바뀜
    // isRedirect = false  => 주소는 바뀌지 않고 화면만 바뀜
	
    public String getPath() {return path;}
    public void setPath(String path) {this.path = path;}
    public boolean isRedirect() {return isRedirect;}
    public void setRedirect(boolean isRedirect) {this.isRedirect = isRedirect;}
}
```

## BoardFrontController.java

```java
package com.project.cafe.board.action;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.ActionForward;

public class BoardFrontController extends HttpServlet
{
    protected void doProcess(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException 
    {
        // 1. 전달되는 가상주소 계산
        // 매핑된(.bo로 끝나는) 주소를 받아옴
        String requestURI = request.getRequestURI();
        System.out.println("requestURI : " + requestURI);
		
        // 매핑된 해당 프로젝트 주소 구함
        String ctxPath = request.getContextPath();
        System.out.println("ctxPath : " + ctxPath);
		
        // 매핑된 주소(requestURI)에서 프로젝트 주소(ctxPath)를 빼서 계속 바뀌는 맨 뒤 주소를 구함
        String command = requestURI.substring(ctxPath.length());
        System.out.println("command : " + command);
		
        System.out.println("1. 가상주소 계산 완료\n");
		
		
        // 2. 가상주소 매핑
        ActionForward forward = null;
		
        if (command.equals("/board.bo"))
        {
            System.out.println("C : /board.bo 호출");
			
            forward = new ActionForward();
            forward.setPath("./contents/boardList.jsp");
            forward.setRedirect(false);
        }
        else if (command.equals("/boardWrite.bo"))
        {
            System.out.println("C : /boardWrite.bo 호출");
			
            forward = new ActionForward();
            forward.setPath("./contents/boardWrite.jsp");
            forward.setRedirect(false);
        }
		
        System.out.println("2. 가상주소 매핑 완료");
		
		
        // 3. 페이지 이동
        if (null != forward)
        {
            // 페이지 정보가 있을 때
            if (forward.isRedirect())
            {
                System.out.println("C : sendRedirect 방식으로 페이지 이동 : " + forward.getPath());
                response.sendRedirect(forward.getPath());
            }
            else 
            {
                System.out.println("C : forward 방식으로 페이지 이동 : " + forward.getPath());
                RequestDispatcher dis = request.getRequestDispatcher(forward.getPath());
                dis.forward(request, response);
            }
			
            System.out.println("3. 페이지 이동 완료");
        }
    }
	
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException 
    {
        doProcess(request, response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException 
    {
        doProcess(request, response);
    }
}
```

* 게시판용 `컨트롤러`를 만들고 게시글 리스트를 보는 페이지와 글쓰기 페이지를 연결시켜 주었다.
* 그런데 글쓰기 페이지로 연결하는 기능을 만들고 나서 생각해 보니까 로그인 한 사용자인지 확인을 하지 않고 글쓰기 버튼을 누르면 무조건 연결시켜 주고 있었다... 😅 이제부터 로그인 한 회원만 글을 쓸 수 있도록 유효성 검사하는 부분을 추가할 것이다!<br><br><br>


# 마감까지 
* `D-14`
