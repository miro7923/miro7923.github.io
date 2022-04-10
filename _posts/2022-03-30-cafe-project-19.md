---
title: JAVA Servlet 프로젝트) Cafe(웹 사이트) 만들기 19 - 메일 보내기 기능 추가
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
* 사이트 관리자에게 메일을 보낼 수 있는 기능을 만들었다.
* 자바 메일 `API`를 사용했다.
* `JSP 2.3 & Servlet 3.1` 책을 참고했다.

## 자바 메일 API 추가
* [javax.mail.jar](https://javaee.github.io/javamail/) - `javax.mail.jar`만 다운로드 후 `WEB-INF/lib`에 넣으면 된다.
* [actication.jar](https://www.oracle.com/java/technologies/java-archive-downloads-java-plat-downloads.html#jaf-1.1.1-fcs-oth-JPR) - 여기서 `jaf-1_1_1.zip` 다운로드 후 압축 풀어서 `activation.jar` 파일만 `WEB-INF/lib`에 넣으면 된다.

## contactUs.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html lang="en">

<!-- Start Head -->
  <jsp:include page="../inc/top.jsp"></jsp:include>
  <script src="${pageContext.request.contextPath }/js/contactUs.js"></script>
<!-- End Head -->

<body class="modern">

<!--
START MODULE AREA 2: Menu 1
-->
  <jsp:include page="../inc/subTop.jsp"></jsp:include>
<!--
END MODULE AREA 2: Menu 1
-->

<!--
START MODULE AREA 3: Sub Navigation 1
-->
<section>
  <div data-layout="_r">
    <!-- <div data-layout="de6 ec5 ec-n3">
      <p></p>
    </div> -->
    <div data-layout="de10 ec-half">
      <h3>Contact us</h3>
      <form name="mail" action="./SendMailAction.bo" method="post" onsubmit="return finalCheck();">
        <div class="formRow">
          <label for="MOD_TEXTFORM_NameField">이름 </label><input id="MOD_TEXTFORM_NameField" type="text" name="name">
        </div>
        <div id="nameMsg"></div>
        <div class="formRow">
          <label for="MOD_TEXTFORM_EmailField">답변 받을 이메일 </label><input id="MOD_TEXTFORM_EmailField" type="email" name="email">
        </div>
        <div id="emailMsg"></div>
        <div class="formRow">
          <label for="MOD_TEXTFORM_TelField">제목 </label><input id="MOD_TEXTFORM_TelField" type="tel" name="title">
        </div>
        <div id="titleMsg"></div>
        <div class="formRow">
          <label for="MOD_TEXTFORM_MsgField">내용 </label><textarea id="MOD_TEXTFORM_MsgField" placeholder="내용을 입력하세요..." name="content"></textarea>
        </div>
        <div id></div>
        <button type="submit" class="btn">보내기</button>
      </form>
    </div>
  </div>
</section>
<!--
END MODULE AREA 3: Sub Navigation 1
-->

<!--
START MODULE AREA 5: Footer 2
-->
  <jsp:include page="../inc/bottom.jsp"></jsp:include>
<!--
END MODULE AREA 5: Footer 2
-->

<script src="${pageContext.request.contextPath }/js/index.js"></script>
</body>

</html>
```

* 폼태그를 이용해 입력한 정보들을 서블릿으로 전송한다.

## contactUs.js

```javascript
function finalCheck()
{
    var mail = document.mail;
	
    if (mail.name.value.length <= 0)
    {
        alert('이름을 입력하세요!');
        mail.name.focus();
        return false;
    }
	
    if (mail.email.value.length <= 0)
    {
        alert('연락 받을 이메일을 입력하세요!');
        mail.email.focus();
        return false;
    }
	
    if (mail.title.value.length <= 0)
    {
        alert('제목을 입력하세요!');
        mail.title.focus();
        return false;
    }
	
    if (mail.content.value.length <= 0)
    {
        alert('내용을 입력하세요!');
        mail.content.focus();
        return false;
    }
}
```

* 메일을 보내기 전 모든 필드가 입력되었는지 확인 후 모든 분기가 `true`일 때에만 서블릿으로 이동한다.

## BoardFrontController.java

```java
package com.project.cafe.board.action;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;

public class BoardFrontController extends HttpServlet
{
    protected void doProcess(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException 
    {
        // 1. 전달되는 가상주소 계산
        // .. 생략
		
        // 2. 가상주소 매핑
        Action action = null;
        ActionForward forward = null;
		
        // .. 생략
        else if (command.equals("/SendMailAction.bo"))
        {
            System.out.println("C : /SendMailAction.bo 호출");
			
            action = new SendMailAction();
			
            try {
                forward = action.execute(request, response);
            }
            catch (Exception e) {
                e.printStackTrace();
            }
        }
        // .. 생략
		
        // 3. 페이지 이동
        // .. 생략
    }
 
    // .. 생략
}
```

* `컨트롤러`에서 메일 전송 동작을 수행할 서블릿으로 연결한다.

## SendMailAction.java

```java
package com.project.cafe.board.action;

import java.io.PrintWriter;
import java.util.Date;
import java.util.Properties;

import javax.mail.Address;
import javax.mail.Authenticator;
import javax.mail.Message;
import javax.mail.Session;
import javax.mail.Transport;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeMessage;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;

public class SendMailAction implements Action 
{
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        System.out.println("M : SendMailAction - execute() 호출");
		
        // 한글처리
        request.setCharacterEncoding("utf-8");
		
        // 파라미터 저장
        String sender = request.getParameter("email");
        String receiver = "메일을 받는데 사용할 관리자 메일주소";
        String title = request.getParameter("title");
        String content = request.getParameter("content");
        String name = request.getParameter("name");
		
        StringBuilder sb = new StringBuilder();
        sb.append("이름 : ");
        sb.append(name);
        sb.append("<br>");
        sb.append("메일주소 : ");
        sb.append(sender);
        sb.append("<br>");
        sb.append("내용 : ");
        sb.append(content);
		
        // 메일 보내는 동작 수행
        response.setContentType("text/html; charset=utf-8");
        PrintWriter out = response.getWriter();
		
        try {
            // 서버 정보를 Properies 객체에 저장
            Properties properties = System.getProperties();
			
            // Starttls Command를 사용할 수 있게 설정
            properties.put("mail.smtp.starttls.enable", "true");
			
            // SMTP 서버를 지정
            properties.put("mail.smtp.host", "smtp.gmail.com");
			
            // AUTH command를 사용하여 사용자 인증을 할 수 있게 하는 설정 부분
            properties.put("mail.smtp.auth", "true");
			
            // 서버 포트를 지정하는 부분
            properties.put("mail.smtp.port", "587");
			
            // 인증 정보 생성
            Authenticator auth = new GoogleAuthentication();
			
            // 메일을 전송하는 역할을 하는 단위인 Session 객체 생성
            Session s = Session.getDefaultInstance(properties, auth);
			
            // 생성한 Session 객체를 사용하여 전송할 Message 객체 생성
            Message message = new MimeMessage(s);
			
            // 메일을 송신할 송신 주소 생성
            Address senderAddr = new InternetAddress(sender);
			
            // 메일을 수신할 수신 주소 생성
            Address receiverAddr = new InternetAddress(receiver);
			
            // 메일 전송에 필요한 값들 설정
            message.setHeader("content-type", "text/html; charset=utf-8");
            message.setFrom(senderAddr);
            message.addRecipient(Message.RecipientType.TO, receiverAddr);
            message.setSubject(title);
            message.setContent(sb.toString(), "text/html; charset=utf-8");
            message.setSentDate(new Date());
			
            // 메시지를 메일로 전송
            Transport.send(message);
			
            out.print("<script>");
            out.print("alert('메일이 정상적으로 전송되었습니다.');");
            out.print("location.href='./Contact.bo';");
            out.print("</script>");
        }
        catch (Exception e) {
            out.print("SMTP 서버가 잘못 설정되었거나 서비스에 문제가 있습니다.");
            e.printStackTrace();
        }
		
        return null;
    }
}
```

* 받은 메일을 열어볼 때 줄을 바꿔서 보여주기 위해서 `<br>` 태그를 추가했다.
* 처음엔 `\n`과 `System.lineSeparator()` 등 자바에서 줄을 바꾸는 코드를 썼는데 메일을 받으면 줄이 바뀌지 않아서 한참 찾아 헤멨다.
* 알고보니 메일이 출력되는 필드도 `HTML`이라 `HTML`태그로 줄바꿈을 작성해야 했다. `message.setContent(sb.toString(), "text/html; charset=utf-8");`이 구문의 `"text/html; charset=utf-8"`이 힌트였다...
* [참고글](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=deeperain&logNo=221387794478)

## GoogleAuthentication.java

```java
package com.project.cafe.board.action;

import javax.mail.Authenticator;
import javax.mail.PasswordAuthentication;

public class GoogleAuthentication extends Authenticator 
{
    PasswordAuthentication passAuth;
	
    public GoogleAuthentication()
    {
        // 첫번째 인자는 구글 아이디, 두번째는 비밀번호
        passAuth = new PasswordAuthentication("구글아이디", "앱 비밀번호");
    }
	
    public PasswordAuthentication getPasswordAuthentication() {return passAuth;}
}
```

* `구글 SMTP`를 사용할 것이기 때문에 구글 계정 인증 정보를 받아오는 클래스를 만들었다.
* 앱 비밀번호는 [앱 비밀번호 설정](https://myaccount.google.com/apppasswords?rapt=AEjHL4NLJqxIzXCjpOREZBAtKSdJowWSk6Hl9qx59piLBQz_fNsRxBDDi02cYrIZw8GPr-zDU_3xzV4uoyYm_F6zLxRmucszuQ) 페이지에서 설정할 수 있다.

<p align="center"><img src="../../assets/images/mailTest.png" width="400"></p>

* 이제 메일을 보내보면 작성한 대로 잘 간다! 👏<br><br><br>

# 마감까지 
* `D-5`
