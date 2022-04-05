---
title: JAVA Servlet 프로젝트) Cafe(웹 사이트) 만들기 27 - 관리자 페이지에서 회원 상세정보 보기 기능 추가
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
* 관리자 페이지에서 회원 한 명의 상세 정보를 확인하고 거기에서 삭제할 수 있는 기능을 추가했다.

## memberManagement.jsp

```jsp
<%if (!memberList.isEmpty())
  {
    for (int i = 0; i < memberList.size(); i++)
    { %>
  <tr>
    <td><input type="checkbox" name="member" value="<%=memberList.get(i).getId() %>"></td>
    <td><a href="./MemberInfoAction.me?id=<%=memberList.get(i).getId()%>"><%=memberList.get(i).getId() %></a></td>
    <td><%=memberList.get(i).getName() %></td>
    <td><%=memberList.get(i).getEmail() %></td>
    <td><%=memberList.get(i).getRegdate() %></td>
  </tr>
<% }} %>
```

* 회원 목록을 보는 페이지에서 아이디를 클릭하면 해당 회원의 정보를 가져오는 서블릿으로 이동한다.

## MemberInfoAction.java

```java
package com.project.cafe.member.action;

import java.io.PrintWriter;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;
import com.project.cafe.member.db.MemberDAO;
import com.project.cafe.member.db.MemberDTO;

public class MemberInfoAction implements Action 
{
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        System.out.println("M : MemberInfoAction - execute() 호출");
		
        // 파라메터 저장
        String memberId = request.getParameter("id");
		
        // DB 접속해서 회원 정보 가져오기
        MemberDAO dao = new MemberDAO();
        MemberDTO dto = dao.getMember(memberId);
		
        // 회원정보 저장
        request.setAttribute("dto", dto);

        // 회원 상세 정보 페이지로 이동
        ActionForward forward = new ActionForward();
        forward.setPath("./admin/memberInfo.jsp");
        forward.setRedirect(false);
		
        return forward;
    }
}
```

* 회원 한 명의 정보를 가져온 뒤 `request` 영역에 저장한다.
* 그 다음 회원 상세 정보 페이지로 이동한다.

## memberInfo.jsp

```jsp
<section class="MOD_SUBNAVIGATION1">
  <div data-layout="_r">
    <jsp:include page="../inc/leftNav.jsp"></jsp:include>
    <div data-layout="al-o1 de-o2 de10" class="MOD_SUBNAVIGATION1_Page">
      <h3>회원 상세 정보</h3><br>
      <%
      	String id = (String) session.getAttribute("id");
      	if (null == id)
      		response.sendRedirect("./login.me");
      	
      	MemberDTO dto = (MemberDTO)request.getAttribute("dto");
      	
      	String[] regdate = dto.getRegdate().toString().split(" ");
      %>
        <div class="formRow">
          <label for="MOD_TEXTFORM_NameField">회원번호 </label><input type="text" name="memberNum" id="memberNum" value="<%=dto.getMemberNum() %>" readonly>
        </div>
        <div class="formRow">
          <label for="MOD_TEXTFORM_NameField">아이디 </label><input type="text" name="id" id="id" value="<%=dto.getId() %>" readonly>
        </div>
        <div class="formRow">
          <label for="MOD_TEXTFORM_NameField">이름 </label><input id="MOD_TEXTFORM_NameField" type="text" name="name" class="name" value="<%=dto.getName()%>" readonly>
        </div>
        <div class="formRow">
          <label for="MOD_TEXTFORM_NameField">생년월일 </label><input id="MOD_TEXTFORM_NameField" type="date" name="birth" class="birth" value="<%=dto.getBirth()%>" readonly>
        </div>
        <div class="formRow">
          <label for="MOD_TEXTFORM_NameField">나이 </label><input id="MOD_TEXTFORM_NameField" type="text" name="age" class="age" value="<%=dto.getAge()%>" disabled>
        </div>
        <div class="formRow">
          <label for="MOD_TEXTFORM_NameField">성별 </label><input class="radio" type="radio" name="gender" id="gender" value="남"
          <%if (dto.getGender().equals("남")) { %> 
          checked
          <%} %> readonly><label class="radioText">남</label> 
          <input class="radio" id="gender" type="radio" name="gender" value="여"
          <%if (dto.getGender().equals("여")) { %>
          checked
          <%} %> readonly><label class="radioText">여</label>
        </div>
        <div class="formRow">
          <label for="MOD_TEXTFORM_NameField">우편번호 </label>
          <label class="phone">
            <input type="text" name="postalcode" id="postalcode" value="<%=dto.getPostalcode() %>" readonly>
          </label>
        </div>
        <span id="guide" style="color:#999;display:none"></span>
        <div class="formRow">
          <label for="MOD_TEXTFORM_NameField">도로명 주소 </label>
          <input type="text" id="roadAddress" name="roadAddress" value="<%=dto.getRoad_address() %>" readonly>
        </div>
        <div class="formRow">
          <label for="MOD_TEXTFORM_NameField">상세 주소 </label>
		  <input type="text" id="detailAddress" name="detailAddress" value="<%=dto.getDetail_address()%>" readonly>
        </div>
        <div class="formRow" id="phoneArea">
          <label for="MOD_TEXTFORM_TelField">휴대폰 번호 </label>
          <label class="phone">
          <input id="phone1" type="tel" name="phone1" size="1" maxlength="3" value="<%=dto.getPhone().substring(0, 3) %>" oninput="tabCursor(1)" readonly> - 
          <input id="phone2" type="tel" name="phone2" size="3" maxlength="4" value="<%=dto.getPhone().substring(3, 7) %>" oninput="tabCursor(2)" readonly> - 
          <input id="phone3" type="tel" name="phone3" size="3" maxlength="4" value="<%=dto.getPhone().substring(7, 11) %>" readonly>
          </label>
        </div>
        <div class="formRow">
          <label for="MOD_TEXTFORM_EmailField">이메일 </label><input type="email" name="email" id="email" value="<%=dto.getEmail()%>" readonly>
        </div>
        <div id="emailMsg"></div>
        <div class="formRow">
          <label for="MOD_TEXTFORM_NameField">가입일 </label><input type="text" name="regdate" id="regdate" value="<%=regdate[0] %>" readonly>
        </div>
      <form action="./AdminDeleteAction.me" method="post">
        <input type="hidden" name="member" value="<%=dto.getId()%>">
        <button type="submit" class="btn">회원삭제</button>
      </form><br>
      <button class="btn" onclick="history.back();">회원 목록 보기</button>
      </div>
  </div>
</section>
```

* 회원 상세 정보 페이지는 마이페이지와 동일한 레이아웃이되, 각 정보를 관리자가 수정할 필요는 없기 때문에 모두 `readonly`를 걸었다.
* 하단에 있는 `회원삭제` 버튼을 누르면 해당 회원을 삭제할 수 있다. 삭제 로직은 이전 일지에서 만들었던 것과 동일하다.
* 참고 : [JAVA Servlet 프로젝트) Cafe(웹 사이트) 만들기 26 - 관리자 페이지 + 회원 관리 기능 추가](https://miro7923.github.io/project%20log/cafe-project-26/)
* `회원 목록 보기` 버튼을 누르면 이전 페이지로 되돌아 간다.<br><br><br>

# 마감까지
* 마감 기한이 늘어났다. 
* `D-2`
