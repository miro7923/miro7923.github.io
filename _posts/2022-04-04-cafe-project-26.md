---
title: JAVA Servlet 프로젝트) Cafe(웹 사이트) 만들기 26 - 관리자 페이지 + 회원 관리 기능 추가
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

# 진행상황 1
* 관리자 페이지를 추가했다.
* 관리자 아이디인 `admin`으로 로그인 했을 때에만 접근할 수 있다.

## top.jsp

```jsp
<p class="MOD_HEADER1_Phone">
<%
    String id = (String)session.getAttribute("id");
		
    if (null == id)
    {
        %>
          <a href="./login.me">로그인</a>&nbsp;&nbsp;&nbsp;
          <a href="./join.me">회원가입</a>
        <%
    }
    else 
    {
        %>
          <a href="./logout.me">로그아웃</a>&nbsp;&nbsp;&nbsp;
        <%
        if (id.equals("admin"))
        {
            %>
              <a href="./admin.me">관리자 페이지</a>
            <%
        }
        else
        {
            %>
              <a href="./checkPass.me">마이페이지</a>
            <%
        }
    }
%>
</p>
```

* 세션에 저장된 아이디 정보가 `admin`일 때만 관리자 페이지 버튼이 활성화된다.

## admin.jsp

```jsp
<section class="MOD_SUBNAVIGATION1">
  <div data-layout="_r">
    <jsp:include page="../inc/adminLeftNav.jsp"></jsp:include>
    <div data-layout="al-o1 de-o2 de10" class="MOD_SUBNAVIGATION1_Page">
      <h3>관리자 페이지</h3><br>
      <%
      	String id = (String) session.getAttribute("id");
      	if (null == id)
            response.sendRedirect("./login.me");
      	else 
      	{
            if (!id.equals("admin"))
            {
                // 관리자 계정이 아니면 쫓아내기
                session.invalidate();
                response.sendRedirect("./login.me");
            }
      	}
      %>
      <div class="formRow">
          <label for="MOD_TEXTFORM_NameField">관리자님 환영합니다. </label>
        </div>
    </div>
  </div>
</section>
```

* 관리자로 로그인하면 관리자 전용 페이지에 접근할 수 있으며 만약 관리자가 아닌 사용자가 접근하면 세션 정보를 초기화하고 쫓아낸다.

## adminLeftNav.jsp

```jsp
<div data-layout="al16 al-o2 de-o1 de6 ec4">
  <nav class="MOD_SUBNAVIGATION1_Menu" data-theme="_bo2">
    <p class="MOD_SUBNAVIGATION1_Menutitle" data-theme="_bgs">Menu</p>
    <ul>
      <li><a href="./MemberListAction.me">회원관리</a></li>
      <li><a href="#">게시글 관리</a></li>
    </ul>
  </nav>
</div>
```

* 관리자 메뉴용 사이드 메뉴도 따로 만들었다.<br><br>

<p align="center"><img src="../../assets/images/adminPageMain.png" width="500"></p>

* 회원관리 메뉴를 누르면 먼저 회원 전체 정보를 불러오기 위해서 해당 동작을 수행할 서블릿으로 이동한다.

## MemberListAction.java

```java
package com.project.cafe.member.action;

import java.util.ArrayList;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;
import com.project.cafe.member.db.MemberDAO;
import com.project.cafe.member.db.MemberDTO;

public class MemberListAction implements Action 
{
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        System.out.println("M : MemberListAction - execute() 호출");
		
        // 한글처리
        request.setCharacterEncoding("utf-8");
		
        // DB에서 전체 회원 목록 불러오기
        MemberDAO dao = new MemberDAO();
        ArrayList<MemberDTO> memberList = dao.getMemberList();
		
        // request에 저장
        request.setAttribute("memberList", memberList);
		
        // 페이지 이동
        ActionForward forward = new ActionForward();
        forward.setPath("./admin/memberManagement.jsp");
        forward.setRedirect(false);
		
        return forward;
    }
}
```

* 회원 전체 목록을 세션에 저장한 후 회원관리 페이지로 이동한다.

## MemberDAO.java - getMemberList()

```java
public ArrayList<MemberDTO> getMemberList()
{
    ArrayList<MemberDTO> memberList = new ArrayList<MemberDTO>();
		
    try {
        con = getCon();
			
        sql = "select * from cafe_members where id != 'admin'";
        pstmt = con.prepareStatement(sql);
			
        rs = pstmt.executeQuery();
			
        while (rs.next())
        {
            MemberDTO dto = new MemberDTO();
				
            dto.setAddress(rs.getString("address"));
            dto.setAge(rs.getInt("age"));
            dto.setBirth(rs.getDate("birth"));
            dto.setEmail(rs.getString("email"));
            dto.setGender(rs.getString("gender"));
            dto.setId(rs.getString("id"));
            dto.setMemberNum(rs.getInt("member_num"));
            dto.setName(rs.getString("name"));
            dto.setPhone(rs.getString("phone"));
            dto.setPostalcode(rs.getInt("postalcode"));
            dto.setRegdate(rs.getTimestamp("regdate"));
				
            memberList.add(dto);
        }
			
        System.out.println("DAO : 회원 전체 목록 저장 완료");
    }
    catch (Exception e) {
        e.printStackTrace();
    }
    finally {
        CloseDB();
    }
		
    return memberList;
}
```

* 전체 회원 정보를 불러온다.
* 하지만 관리자 정보는 보여줄 필요가 없기 때문에 제외하고 불러온다.

## memberManagement.jsp

```jsp
<section class="MOD_SUBNAVIGATION1">
  <div data-layout="_r">
    <jsp:include page="../inc/adminLeftNav.jsp"></jsp:include>
    <div data-layout="al-o1 de-o2 de10" class="MOD_SUBNAVIGATION1_Page">
      <h3>회원관리 페이지</h3><br>
      <%
      	String id = (String) session.getAttribute("id");
      	if (null == id)
            response.sendRedirect("./login.me");
      	else 
      	{
            if (!id.equals("admin"))
            {
                // 관리자 계정이 아니면 쫓아내기
                session.invalidate();
                response.sendRedirect("./login.me");
            }
      	}
      	
      	ArrayList<MemberDTO> memberList = (ArrayList<MemberDTO>)request.getAttribute("memberList");
      %>
      <form action="./AdminDeleteAction.me" method="post" onsubmit="return finalCheck();">
        <table class="type09">
          <tbody>
            <colgroup>
              <col width="5%">
              <col width="10%">
              <col width="10%">
              <col width="35%">
              <col width="">
            </colgroup>
            <tr>
              <th><input type="checkbox" id="selectAll"></th>
              <th>아이디</th>
              <th>이름</th>
              <th>이메일</th>
              <th>가입일</th>
            </tr>
            <%if (!memberList.isEmpty())
              {
                for (int i = 0; i < memberList.size(); i++)
                  { %>
            <tr>
              <td><input type="checkbox" name="member" value="<%=memberList.get(i).getId() %>"></td>
              <td><%=memberList.get(i).getId() %></td>
              <td><%=memberList.get(i).getName() %></td>
              <td><%=memberList.get(i).getEmail() %></td>
              <td><%=memberList.get(i).getRegdate() %></td>
            </tr>
            <% }} %>
          </tbody>
        </table><br>
        <button type="submit" class="btn">회원 삭제</button>
      </form>
    </div>
  </div>
</section>
```

* 아까 세션에 저장했던 회원목록을 토대로 회원들을 출력해서 보여준다.

<p align="center"><img src="../../assets/images/memberManagementPage.png"></p>

* 회원 삭제 동작을 테스트 한다고 좀 삭제했더니 회원이 많이 줄었다. 😅

## memberManagement.js

```javascript
$(document).ready(function()
{
    selectAll();
});

function selectAll()
{
    // 전체 선택 / 해제
    $('#selectAll').change(function()
    {		
        if ($('#selectAll').is(':checked'))
            $('input:checkbox[name=member]').prop('checked', true);
        else 
            $('input:checkbox[name=member]').prop('checked', false);
    });
}

function finalCheck()
{
    if ($('input:checkbox[name=member]:checked').length <= 0)
    {
        // 선택된 엘리먼트가 없으면 삭제할 회원이 없음
        alert('선택된 회원이 없습니다!');
        return false;
    }
    else 
    {
        // 선택된 회원이 있을 때
        if (confirm('선택한 회원들을 탈퇴 시키겠습니까?'))
            return true;
        else 
            return false;
    }
}
```

* 테이블의 맨 위에 있는 체크박스를 누르면 전체 선택과 헤재가 가능하다.
* 삭제 버튼을 눌렀을 때엔 `finalCheck()` 메서드를 통해 동작 분기를 나누었다.<br><br>

# 진행상황 2
* 관리자가 회원을 일괄 삭제할 수 있는 기능을 추가했다.

## AdminDeleteAction.java

```java
package com.project.cafe.member.action;

import java.io.PrintWriter;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;
import com.project.cafe.member.db.MemberDAO;

public class AdminDeleteAction implements Action 
{
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        System.out.println("M : AdminDeleteAction - execute() 호출");
		
        // 파라미터 저장
        String[] members = request.getParameterValues("member");
        System.out.println("members size: "+members.length);
		
        response.setContentType("text/html; charset=utf-8");
        PrintWriter out = response.getWriter();
		
        // DB 접속해서 삭제 연산
        MemberDAO dao = new MemberDAO();
        for (String id : members) 
        {
            System.out.println("id: "+id);
            int result = dao.deleteMember(id);
            if (result != 1)
            {
                out.print("<script>");
                out.print("alert('잘못된 접근!');");
                out.print("history.back();");
                out.print("</script>");
				
                out.close();
				
                return null;
            }
        }
		
        // 페이지 이동
        out.print("<script>");
        out.print("alert('선택한 회원 탈퇴가 완료 되었습니다.');");
        out.print("location.href='./MemberListAction.me';");
        out.print("</script>");
		
        out.close();
		
        return null;
    }
}
```

* 체크박스로 선택한 회원들의 아이디를 받아서 그 갯수만큼 삭제 연산을 반복한다.
* 기존에 만들었던 회원 한 명을 삭제하는 메소드가 있긴 했는데 회원의 아이디와 비밀번호를 함께 확인한 후 삭제 동작을 수행하는 메서드라 목적에 좀 맞지 않아서 오버로딩해서 하나 더 만들었다.

## MemberDAO.java - deleteMember(id)

```java
public int deleteMember(String id)
{
    int ret = -1;
		
    try {
        con = getCon();
			
        sql = "select * from cafe_members where id=?";
        pstmt = con.prepareStatement(sql);
			
        pstmt.setString(1, id);
			
        rs = pstmt.executeQuery();
			
        if (rs.next())
        {
            sql = "delete from cafe_members where id=?";
            pstmt = con.prepareStatement(sql);
				
            pstmt.setString(1, id);
				
            ret = pstmt.executeUpdate();
				
            System.out.println("DAO : 운영자가 회원정보 삭제 완료");
        }
    }
    catch (Exception e) {
        e.printStackTrace();
    }
    finally {
        CloseDB();
    }
		
    return ret;
}
```

* 아이디를 매개변수로 받아 일치하는 회원 한 명의 정보를 삭제한다.
* 여기까지 수행하고 아까전에 있던 회원관리 페이지로 돌아가면 업데이트 된 회원 목록을 볼 수 있다.<br><br><br>

# 마감까지
* 마감 기한이 늘어났다. 
* `D-3`
