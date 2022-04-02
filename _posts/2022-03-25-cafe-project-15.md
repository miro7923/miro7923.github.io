---
title: JAVA Servlet 프로젝트) Cafe(웹 사이트) 만들기 15 - 게시글 삭제 기능 구현
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
* 게시글 삭제 기능을 만들었다.

## boardContent.jsp

```jsp
<div id="boardPage">
  <%if (null != id && id.equals(dto.getId())) { %>
    <button type="button" class="btn" 
      onclick="location.href='./BoardModify.bo?num=<%=dto.getNum()%>&pageNum=<%=pageNum%>';">수정하기</button>
    <button type="button" class="btn" 
      onclick="location.href='deleteCheck(<%=dto.getNum()%>, <%=pageNum%>);">삭제하기</button>
  <%} %>
```

* 로그인 한 사용자 중 작성 글의 `ID`와 일치하는 사용자 본인일 때에만 `삭제하기` 버튼이 활성화 된다.
* 버튼을 누르면 정말 삭제할 것인지 확인하는 함수를 호출해 알림창을 띄운다.

## boardContent.js - deleteCheck(num, pageNum)

```javascript
function deleteCheck(num, pageNum)
{
    if (confirm('정말 삭제 하시겠습니까?'))
        location.href='./BoardDelete.bo?num='+num+'&pageNum='+pageNum;
}
```

* 취소 버튼을 누르면 게시글 본문 페이지에 머무르고 확인 버튼을 누르면 삭제 동작을 수행하는 서블릿으로 연결하기 위해 컨트롤러로 이동한다.

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
		else if (command.equals("/BoardDelete.bo"))
		{
			System.out.println("C : /BoardDelete.bo 호출");
			
			action = new BoardDeleteAction();
			
			try {
				forward = action.execute(request, response);
			}
			catch (Exception e) {
				e.printStackTrace();
			}
		}
		
		
        // 3. 페이지 이동
        // .. 생략
	}
}
```

* 컨트롤러로 오면 게시글 삭제 동작을 수행할 액션 클래스로 연결한다.

## BoardDeleteAction.java

```java
package com.project.cafe.board.action;

import java.io.PrintWriter;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;
import com.project.cafe.board.db.BoardDAO;

public class BoardDeleteAction implements Action 
{
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        System.out.println("M : BoardDeleteAction - execute() 호출");
		
        // 파라메터 저장
        int num = Integer.parseInt(request.getParameter("num"));
        String pageNum = request.getParameter("pageNum");
		
        // DB 연결해서 해당 글 삭제
        BoardDAO dao = new BoardDAO();
        int result = dao.deletePost(num);
		
        response.setContentType("text/html; charset=UTF-8");
        PrintWriter out = response.getWriter();
        // 글 리스트 페이지로 이동
        if (1 != result)
        {
            // 해당 게시글 정보 없음
            out.println("<script>");
            out.println("alert('해당 게시글 정보가 없습니다!');");
            out.println("location.href='./BoardList.bo';");
            out.println("</script>");
			
            out.close();
			
            return null;
        }

        out.println("<script>");
        out.println("alert('게시글 삭제가 완료되었습니다.');");
        out.println("location.href='./BoardList.bo?pageNum="+pageNum+"';");
        out.println("</script>");
		
        out.close();
		
        return null;
    }
}
```

* `DB`와 연결해서 글 정보를 확인한 후 삭제 동작을 수행한다.
* 로그인 한 본인 글일 때에만 삭제 동작에 접근할 수 있어서 글 삭제에 실패하는 경우는 생기지 않을 것이라 생각하지만 혹시 모르니까 예외처리를 해 주었다.

## BoardDAO.java - deletePost(num)

```java
public int deletePost(int num)
{
    int ret = -1;
		
    try {
        con = getCon();
			
        // 삭제 대상 글 찾기
        sql = "select * from cafe_board where num=?";
        pstmt = con.prepareStatement(sql);
			
        pstmt.setInt(1, num);
			
        rs = pstmt.executeQuery();
			
        if (rs.next())
        {
            sql = "delete from cafe_board where num=?";
            pstmt = con.prepareStatement(sql);
				
            pstmt.setInt(1, num);
				
            ret = pstmt.executeUpdate();
				
            System.out.println("DAO : 글 삭제 완료");
        }
    }
    catch (Exception e) {
        e.printStackTrace();
    }
    finally {
        closeDB();
    }
		
    return ret;
}
```

* 글 삭제가 완료되면 전체 게시글 목록으로 이동한다.
* 그러면 선택한 글이 삭제된 리스트를 볼 수 있다. `DB`에서도 삭제 완료 됨!

* 다음엔 답글 기능을 만들 것이다.<br><br><br>

# 마감까지 
* `D-10`
