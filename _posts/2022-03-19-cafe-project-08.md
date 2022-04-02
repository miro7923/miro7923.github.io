---
title: JAVA Servlet 프로젝트) Cafe(웹 사이트) 만들기 8 - 회원 정보 수정 기능 구현
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

# 시작
* 2022.3.4 ~ <br><br><br>

# 주제
* 웹 백엔드 수업 중 중간 과제로 개인 프로젝트를 진행하게 되었다.
* 회원가입/로그인/탈퇴 등 기본적인 회원관리 시스템을 가진 웹 사이트를 만드는 것이다. 주어진 기한은 `한 달`
* 나는 `다음 카페`를 소규모로 만들어 보기로 했다. 평소 자주 이용하기도 했고 과제의 평가 기준에서 요구하는 기능들을 다 담고 있기도 했기 때문에 이번 기회에 구현해 보면 그동안 배운 것들을 활용하기에 좋을 거 같았다.
* 평가 기준에 사이트의 디자인 구현(HTML/CSS 등 프론트엔드)은 포함되지 않기 때문에 본인이 쓰고 싶은 HTML/CSS 템플릿을 구한 뒤 회원 관리 기능을 구현하면 된다.<br><br><br>

# 진행상황
* 마이페이지로 이동해서 회원 정보를 수정할 수 있는 기능을 추가했다.
* 로그인 하면 마이페이지로 이동할 수 있는 버튼이 활성화 된다. 

## checkPass.jsp

```html
<!-- 회원정보 수정 페이지로 가기 전에 비밀번호 한 번 더 확인 후 이동 -->
<h3>마이페이지</h3>
<%
    String id = (String)session.getAttribute("id");
    if (null == id)
        response.sendRedirect("./login.me");
%>
<form name="checkPass" action="./CheckPassAction.me" method="post" onsubmit="return finalCheck();">
    <input type="hidden" value="<%=id%>" name="id">
    <div class="formRow">
        비밀번호를 한 번 더 입력해 주세요.
    </div><br>
    <div class="formRow">
    <label for="MOD_TEXTFORM_NameField">비밀번호 </label><input id="pass" type="password" name="pass">
    </div>
    <button type="submit" class="btn">확인</button>
</form>
```

* 마이페이지로 이동하기 전에 비밀번호를 한 번 더 확인 할 것이다.

## checkPass.js

```javascript
function finalCheck()
{
    var checkPass = document.checkPass;
	
    if (0 >= checkPass.pass.value.length)
    {
        // 비밀번호가 입력되지 않았으면 false
        alert("비밀번호를 입력해 주세요!");
        checkPass.pass.focus();
        return false;
    }
}
```

* 자바스크립트 코드를 사용해서 빈 데이터가 넘어가지 않게 처리했다.

## CheckPassAction.java

```java
import java.io.PrintWriter;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.member.db.MemberDAO;
import com.project.cafe.member.db.MemberDTO;

public class CheckPassAction implements Action 
{
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        System.out.println("M : CheckPassAction - execute() 호출");
		
        // 파라메터 저장
        String id = request.getParameter("id");
        String pass = request.getParameter("pass");
		
        // 회원정보 저장
        MemberDTO dto = new MemberDTO();
        dto.setId(id);
        dto.setPass(pass);
		
        response.setContentType("text/html; charset=utf-8");
        PrintWriter out = response.getWriter();
		
        // DB 연동해서 아이디와 비밀번호가 일치하는 지 확인
        MemberDAO dao = new MemberDAO();
        int result = dao.loginCheck(dto);
        if (1 == result)
        {
            // 비번 일치
            out.println("<script>");
            out.println("location.href='./myPage.me';");
            out.println("</script>");
        }
        else 
        {
            // 비번 불일치
            out.println("<script>");
            out.println("alert('비밀번호가 일치하지 않습니다!');");
            out.println("history.back();");
            out.println("</script>");
        }
		
        out.close();
		
        return null;
    }
}
```

* 입력된 비밀번호가 전달되면 DB와 접속해서 비밀번호가 일치하는 지 확인한다.
* 비밀번호가 틀렸을 때에 알림창을 출력하기 위해서 자바스크립트 코드를 사용한다. 그래서 여기에서의 페이지 이동방식도 자바스크립트로 처리해 주었다.(페이지 이동은 자바 코드로 하면서 알림창 출력만 자바스크립트로 하면 충돌 생겨서 정상 동작이 안 됨)
* 관련글 : [에러해결 Log) java.lang.IllegalStateException 응답이 이미 커밋된 후에는 sendRedirect()를 호출할 수 없습니다.](https://miro7923.github.io/errorlog/warning-log-03/)

## myPage.jsp

```jsp
...
생략

<h3>마이페이지</h3><br>
<%
    String id = (String) session.getAttribute("id");
    if (null == id)
        response.sendRedirect("./login.me");
      	
        MemberDAO dao = new MemberDAO();
        MemberDTO dto = dao.getMember(id);
      	
        String[] regdate = dto.getRegdate().toString().split(" ");
%>
<form name="myPage" action="./MemberUpdateAction.me" method="post" onsubmit="return finalCheck();">
    <div class="formRow">
        <label for="MOD_TEXTFORM_NameField">회원번호 </label><input type="text" name="memberNum" id="memberNum" value="<%=dto.getMemberNum() %>" readonly>
    </div>
    <div class="formRow">
        <label for="MOD_TEXTFORM_NameField">아이디 </label><input type="text" name="id" id="id" value="<%=dto.getId() %>" readonly>
    </div>
    <div id="idMsg"></div>
    <div class="formRow">
        <label for="MOD_TEXTFORM_NameField">비밀번호 </label><input type="password" name="pass" id="pass">
    </div>
    <div id="passMsg"></div>
    <div class="formRow">
        <label for="MOD_TEXTFORM_NameField">비밀번호 확인 </label><input type="password" name="confirm" id="confirm">
    </div>
    <div id="confirmMsg"></div>
    <div class="formRow">
        <label for="MOD_TEXTFORM_NameField">이름 </label><input id="MOD_TEXTFORM_NameField" type="text" name="name" class="name" value="<%=dto.getName()%>">
    </div>
    <div id="nameMsg"></div>
    <div class="formRow">
        <label for="MOD_TEXTFORM_NameField">생년월일 </label><input id="MOD_TEXTFORM_NameField" type="date" name="birth" class="birth" value="<%=dto.getBirth()%>">
    </div>
    <div id="birthMsg"></div>
    <div class="formRow">
        <label for="MOD_TEXTFORM_NameField">나이 </label><input id="MOD_TEXTFORM_NameField" type="text" name="age" class="age" value="<%=dto.getAge()%>" disabled>
    </div>
    <div id="ageMsg"></div>
    <div class="formRow">
        <label for="MOD_TEXTFORM_NameField">성별 </label><input class="radio" type="radio" name="gender" id="gender" value="남"
            <%if (dto.getGender().equals("남")) { %> 
                checked
            <%} %>><label class="radioText">남</label> 
        <input class="radio" id="gender" type="radio" name="gender" value="여"
            <%if (dto.getGender().equals("여")) { %>
                checked
            <%} %>><label class="radioText">여</label>
    </div>
    <div id="genderMsg"></div>
    <div class="formRow">
        <label for="MOD_TEXTFORM_NameField">도시 </label>
        <select name="city">
            <option selected disabled>도시를 선택하세요.</option>
            <option 
            <%if (dto.getAddress().equals("서울")) { %>
                selected
            <%} %>>서울</option>
            <option
            <%if (dto.getAddress().equals("부산")) { %>
                selected
            <%} %>>부산</option>
            <option
            <%if (dto.getAddress().equals("대전")) { %>
                selected
            <%} %>>대전</option>
        </select>
    </div>
    <div id="cityMsg"></div>
    <div class="formRow" id="phoneArea">
        <label for="MOD_TEXTFORM_TelField">휴대폰 번호 </label>
        <label class="phone">
            <input id="phone1" type="tel" name="phone1" size="1" maxlength="3" value="<%=dto.getPhone().substring(0, 3) %>" oninput="tabCursor(1)"> - 
            <input id="phone2" type="tel" name="phone2" size="3" maxlength="4" value="<%=dto.getPhone().substring(3, 7) %>" oninput="tabCursor(2)"> - 
            <input id="phone3" type="tel" name="phone3" size="3" maxlength="4" value="<%=dto.getPhone().substring(7, 11) %>">
            <button id="requestBtn" class="btn" type="submit" name="requestBtn" onclick="changePhoneBtnStatus();"></button>
        </label>
    </div>
    <div id="phoneMsg"></div>
    <div class="formRow" id="validateArea">
        <label for="MOD_TEXTFORM_EmailField">인증번호 </label>
        <label class="phone">
            <input type="text" name="validateNum" id="validateNum">
            <button id="validateBtn" class="btn" type="submit" name="validateBtn">인증번호 확인</button>
        </label>
    </div>
        <div id="validateMsg"></div>
        <div class="formRow">
            <label for="MOD_TEXTFORM_EmailField">이메일 </label><input type="email" name="email" id="email" value="<%=dto.getEmail()%>">
        </div>
        <div id="emailMsg"></div>
        <div class="formRow">
            <label for="MOD_TEXTFORM_NameField">가입일 </label><input type="text" name="regdate" id="regdate" value="<%=regdate[0] %>" readonly>
        </div>
    <button type="submit" class="btn">회원 정보 수정</button>
</form>

생략
...
```

* 회원번호와 아이디, 가입일과 같은 정보들은 변경할 수 없게 했다.
* 비밀번호는 빈 칸으로 두고 비밀번호를 입력해야만 최종 정보 수정을 할 수 있도록 했다.(기존과 같은 비밀번호 입력해도 ok)
* 휴대폰 번호는 기본적으로 변경할 필요가 없되, 변경을 원하면 `휴대폰변경` 버튼을 눌러서 변경할 수 있도록 했다. 이에 대한 처리는 자바스크립트로 해 주었다.

## myPage.js

```javascript
...
생략(join.js 코드와 유사)

function changePhoneBtnStatus()
{
    // 휴대폰 번호 변경 버튼을 클릭하면 부분만 새로고침
    $changePh = true;
    $("#phoneArea").load(location.href + " #phoneArea");
    $("#validateArea").load(location.href + " #validateArea");
}

function changePhone()
{
    if ($changePh)
    {
        // 휴대폰 번호 변경 
        // 휴대폰변경 버튼을 누르면 휴대폰 번호 입력란과 인증번호 입력란을 활성화 시킴
        $("#phone1").removeAttr("readonly");
        $("#phone2").removeAttr("readonly");
        $("#phone3").removeAttr("readonly");
        $("#validateNum").removeAttr("disabled");
        $("#validateBtn").removeAttr("disabled");
        $("#requestBtn").html("휴대폰인증");
    }
    else 
    {
        // 초기값은 변경 불가능
        $("#phone1").attr("readonly", true);
        $("#phone2").attr("readonly", true);
        $("#phone3").attr("readonly", true);
        $("#validateNum").attr("disabled", true);
        $("#validateBtn").attr("disabled", true);
        $("#requestBtn").html("휴대폰변경");
    }
}

생략
...
```

* 입력한 정보의 유효성을 검사하는 부분은 `join.js`에 썼던 코드와 같은 것을 사용해서 생략했다.
* `휴대폰변경` 버튼을 클릭하면 휴대폰 번호와 인증번호 입력 필드 부분만 새로고침한다. 이후 인증 로직은 회원 가입 때 썼던 것과 동일

## MemberFrontController.java

```java
package com.project.cafe.member.action;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.api.sms.SmsService;

public class MemberFrontController extends HttpServlet
{
    protected void doProcess(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException 
    {
        // 주소 계산 부분 생략
        ...
        
        // 2. 가상주소 매핑
        Action action = null;
        ActionForward forward = null;

        if (command.equals("/MemberJoinAction.me"))
        {
            System.out.println("C : /MemberJoinAction.me 호출");
            System.out.println("C : 이전 페이지 정보를 가져와서 DB 테이블에 저장 후 페이지 이동");
			
            action = new MemberJoinAction(); // 인터페이스를 통해 객체를 생성함으로써 약한결합이 되도록 한다.
			
            try {
                forward = action.execute(req, resp);
            } 
            catch (Exception e) {
                e.printStackTrace();
            }
        }
        else if (command.equals("/MemberUpdateAction.me"))
        {
            System.out.println("C : /MemberUpdateAction.me 호출");
			
            action = new MemberUpdateAction();
			
            try {
                forward = action.execute(req, resp);
            }
            catch (Exception e) {
                e.printStackTrace();
            }
        }
        else if (command.equals("/CheckPassAction.me"))
        {
            System.out.println("C : /CheckPassAction.me 호출");
			
            action = new CheckPassAction();
			
            try {
                forward = action.execute(req, resp);
            }
            catch (Exception e) {
                e.printStackTrace();
            }
        }
        else
        {
            forward = new ActionForward();

            if (command.equals("/main.me"))
            {
                System.out.println("C : 메인페이지 호출");
                forward.setPath("./main/main.jsp");				
            }
            else if (command.equals("/join.me"))
            {
                System.out.println("C : 회원가입 페이지 호출");
                forward.setPath("./member/join.jsp");				
            }
            else if (command.equals("/login.me"))
            {
                System.out.println("C : 로그인 페이지 호출");
                forward.setPath("./member/login.jsp");				
            }
            else if (command.equals("/logout.me"))
            {
                System.out.println("C : 로그아웃 페이지 호출");
                forward.setPath("/logout.me");
            }
            else if (command.equals("/checkPass.me"))
            {
                System.out.println("C : 비번 한 번 더 입력하는 페이지 호출");
                forward.setPath("./member/checkPass.jsp");
            }
            else if (command.equals("/myPage.me"))
            {
                System.out.println("C : 마이페이지 호출");
                forward.setPath("./member/myPage.jsp");
            }
			
            forward.setRedirect(false);
        }
		
        System.out.println("C : 가상주소 매핑 완료\n");
        // 2. 가상주소 매핑
		
        // 3. 페이지 이동
        if (null != forward) // 페이지 이동정보가 있을 때
        {
            if (forward.isRedirect())
            {
                System.out.println("C : sendRedirect 방식 - " + forward.getPath() + " 이동");
                resp.sendRedirect(forward.getPath());
            }
            else 
            {
                System.out.println("C : forward 방식 - " + forward.getPath() + " 이동");
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

* 수정하고 싶은 회원 정보를 입력하고 제출 버튼을 누르면 `Front`로 이동해서 매핑된 페이지로 이동한다.
* 회원 정보를 DB에서 업데이트 할 것이니까 `MemberUpdateAction()` 객체를 만들어서 이동한다.

## MemberUpdateAction.java

```java
package com.project.cafe.member.action;

import java.io.PrintWriter;
import java.sql.Date;
import java.util.Calendar;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.member.db.MemberDAO;
import com.project.cafe.member.db.MemberDTO;

// 회원 정보 수정
public class MemberUpdateAction implements Action
{
    private int getAge(String birth)
    {
        int year = Integer.parseInt(birth.split("-")[0]);
        int curYear = Calendar.getInstance().get(Calendar.YEAR);
		
        return curYear - year;
    }
	
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        System.out.println("M : MemberUpdateAction - execute() 호출");
		
        // 한글처리
        request.setCharacterEncoding("UTF-8");
		
        // 전달받은 파라미터 저장
        MemberDTO dto = new MemberDTO();
        dto.setAddress(request.getParameter("city"));
		
        // 나이 계산
        int age = getAge(request.getParameter("birth"));
        dto.setAge(age);
		
        dto.setBirth(Date.valueOf(request.getParameter("birth")));
        dto.setEmail(request.getParameter("email"));
        dto.setGender(request.getParameter("gender"));
        dto.setId(request.getParameter("id"));
        dto.setName(request.getParameter("name"));
        dto.setName(request.getParameter("name"));
        dto.setPass(request.getParameter("pass"));
		
        // 폰번호 3개 필드 합치기
        String phone = request.getParameter("phone1") + request.getParameter("phone2") + request.getParameter("phone3");
        dto.setPhone(phone);
		
        System.out.println("M : 수정된 회원 정보 저장");
        System.out.println("M : " + dto);
		
        // DAO 객체 생성
        MemberDAO dao = new MemberDAO();
		
        // update 함수 호출
        int result = dao.updateMember(dto);
		
        System.out.println("M : result - " + result);
        System.out.println("M : 회원 정보 수정 완료");
		
        response.setContentType("text/html; charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.print("<script>");
        out.print("alert('정보 수정이 완료되었습니다.');");
        out.print("location.href='./myPage.me';");
        out.print("</script>");
		
        out.close();
		
        return null;
    }
}
```

* 전달된 정보들을 `MemberDTO` 객체에 저장한 뒤 DB와 연결해서 `update` 동작을 수행한다.
* 수정이 완료되면 알림창을 띄우기 위해서 자바스크립트 코드를 사용했다.

## MemberDAO - updateMember(dto)

```java
public int updateMember(MemberDTO dto)
{
    int result = -1;
		
    try {
        // 1.2 DB 연결
        // 회원 정보가 존재하는 지 먼저 확인한 후에 수정 동작 수행
        con = getCon();
        sql = "select * from cafe_members where id=?";
        pstmt = con.prepareStatement(sql);
        pstmt.setString(1, dto.getId());
        rs = pstmt.executeQuery();
			
        if (rs.next())
        {
            if (rs.getString("id").equals(dto.getId()))
            {
                // 회원 정보가 있으면
                // 3. sql 작성 & pstmt 객체 생성
                sql = "update cafe_members set pass=?, name=?, birth=?, age=?, gender=?, address=?, phone=?, email=? where id=?";
                pstmt = con.prepareStatement(sql);
					
                // ? 채우기
                pstmt.setString(1, dto.getPass());
                pstmt.setString(2, dto.getName());
                pstmt.setDate(3, dto.getBirth());
                pstmt.setInt(4, dto.getAge());
                pstmt.setString(5, dto.getGender());
                pstmt.setString(6, dto.getAddress());
                pstmt.setString(7, dto.getPhone());
                pstmt.setString(8, dto.getEmail());
                pstmt.setString(9, dto.getId());
					
                // 4. sql 실행
                pstmt.executeUpdate();
					
                // 5. 데이터 처리
                // 수정 성공
                result = 1;
                System.out.println("DAO : 회원 정보 수정 완료");
            }
            else 
            {
                result = 0;
            }
        }
        else 
        {
            // 회원 정보 없음 - 비회원
            result = -1;
        }
    }
    catch (Exception e) {
        e.printStackTrace();
    }
    finally {
        CloseDB();
    }
		
    return result;
}
```

* `select` 조회 결과 일치하는 회원 정보가 있을 때에만 `update` 동작을 수행한다.
* 마이페이지에 들어가기 전에 비밀번호를 한 번 더 확인하고 들어오기 때문에 항상 정보가 있는 회원만 들어오게 되지만 그래도 업데이트 동작은 안전하게 하는 것이 정석이라 배워서 이렇게 구현했다.<br>

* 이제 회원 탈퇴 기능을 만들어야지...!<br><br><br>

# 마감까지 
* `D-16`
