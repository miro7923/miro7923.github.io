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

```java
import java.io.IOException;
import java.io.PrintWriter;
import java.util.ArrayList;
import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.itwillbs.test.Student;

// 서블릿 - jsp코드 + java코드를 함께 수행할 수 있는 파일
// 서블릿 클래스 상속받으면 서블릿이 됨

// http://localhost:8090/JSP6/ex1
@WebServlet("/ex1")
public class ExServlet extends HttpServlet 
{
    // 폼태그에서 get method를 썼을 때 호출되는 함수
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException 
    {
        System.out.println("doGet() 호출");
		
        // 응답정보의 내용의 형태는 html문서로 표현하겠다.
        response.setContentType("text/html; charset=UTF-8");

        // response를 통해서 출력가능한 통로를 생성하겠다.
//	    PrintWriter out = response.getWriter();
//
//	    // 서블릿을 이용한 JSP 페이지 표현 - 사용하진 않을 것임
//	    out.print("<html>");
//	    out.print("<head>");
//	    out.print("</head>");
//	    out.print("<body>");
//	    out.print("<h1> 서블릿을 활용한 JSP페이지 만들기</h1>");
//	    out.print("</body>");
//	    out.print("</html>");
//	    out.close();

        // 포워딩 전 request 영역에 정보를 저장
        request.setAttribute("itwill", "busan");

        // 객체정보를 전달
        Student kim = new Student();
        kim.setName("김학생");
        kim.setKor(100);
        kim.setEng(90);
        kim.setMath(76);
        request.setAttribute("Student", kim);

        Student user = new Student();
        user.setName("사용자");
        user.setKor(45);
        user.setEng(70);
        user.setMath(98);

        ArrayList<Student> memberList = new ArrayList<Student>();
        memberList.add(kim);
        memberList.add(user);

        request.setAttribute("memberList", memberList);

        // 서블릿 코드를 사용한 화면 출력 X
        // => 포워딩을 사용한 화면 출력 O

        // 자바 코드를 이용한 포워딩 방식 - 외우기
//	    <jsp:forward/> 사용불가 - JSP 페이지가 아닌 자바파일이라서 못 씀
        // 이동할 경로 설정
        RequestDispatcher dis = request.getRequestDispatcher("/jstl/coreSet2.jsp");

        dis.forward(request, response);
	} // doGet()
}
```

* 이렇게 만들면 되는데 사용할 함수들을 다 오버라이딩 하기 때문에 일일이 작성하지 않아도 된다.
* `HttpServlet`을 상속받고 나면 이클립스가 구현해야 하는 함수들을 다 만들어 준다.
* 우리는 형태가 만들어진 함수의 내부만 채우면 된다.<br><br><br>

# JSP 페이지에서 서블릿을 활용한 정보 전달

```jsp
<!-- 서블릿에서 저장된 정보(request.setAttribute("itwill", "busan");) 출력 -->
  itwill : <%=request.getAttribute("itwill") %><br>
  itwill : ${itwill }<br>
  itwill : ${requestScope.itwill }<br>
  
<!-- 앞으로는 보안을 위해 주소에 정보가 처리되는 jsp 페이지를 나타내지 않을 것임
  		자바 서블릿 코드를 이용해 jsp 페이지 작성(가상주소 생성) -->
```

* 자바 서블릿 코드를 이용해 `jsp` 페이지를 작성할 것이기 때문에 서블릿 클래스를 실행하면 `jsp` 페이지로 연결되어 액션을 수행하지만 주소창에는 `jsp` 페이지가 나타나지 않고 어노테이션 `@WebServlet("/ex1")`에 쓴 `/ex1`가 맨 뒤에 붙어 주소창에는 `http://localhost:8090/JSP6/ex1` 와 같은 형태로 나타나게 된다.<br><br>

```jsp
${Student}<br>
학생 이름 : ${Student.getName() }<br>
국어 : ${Student.getKor() }<br>
영어 : ${Student.getEng() }<br>
수학 : ${Student.getMath() }<br>
  
<%
  Student kim = (Student) request.getAttribute("Student");
%>
<%=kim.getName() %><br>
${Student.name }<br><!-- el 표현식에서는 내부적으로 get/set 메서드를 자동으로 구현해 사용함. 그래서 변수명으로도 접근가능 -->
  
<!-- 좀 더 간단하게 쓰기 -->
<c:set var="kim" value="${Student }"/>
  
${kim.name }
  
<hr>
  
${requestScope.memberList }<br>
${requestScope.memberList[0].name }
```

* 그리고 아까 서블릿에서 함께 저장했던 클래스 객체도 위와 같이 `el 표현식`을 사용해서 출력할 수 있다.
