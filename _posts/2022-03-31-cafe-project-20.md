---
title: 프로젝트) Cafe(웹 사이트) 만들기 20 - 파일업로드 기능 추가
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
* 파일업로드 기능을 추가했다.
* `COS` 라이브러리를 사용했다.

## COS 라이브러리 다운로드 및 설치
* [http://www.servlets.com/](http://www.servlets.com/cos/)
* 위 사이트에 접속해서 하단에 `Download` 탭에 있는 압축파일을 다운로드 한다.
* 다운받은 파일 압축을 푼 후 `cos.jar` 파일을 `WEB-INF/lib` 폴더에 넣는다.

## boardWrite.jsp

```jsp
<section class="MOD_SUBNAVIGATION1">
  <div data-layout="_r">
    <jsp:include page="../inc/leftNav.jsp"></jsp:include>
    <div data-layout="al-o1 de-o2 de10" class="MOD_SUBNAVIGATION1_Page">
    	<h2>게시글 작성</h2>
    	<form name="write" action="./BoardWriteAction.bo" method="post" enctype="multipart/form-data" onsubmit="return finalCheck();">
    	<input type="hidden" name="id" value="<%=id%>">
        <div class="formRow">
          <label for="MOD_TEXTFORM_NameField">제목 </label><input type="text" name="title" id="title">
        </div>
        <div class="formRow">
          <label for="MOD_TEXTFORM_MsgField">내용 </label>
          <textarea id="MOD_TEXTFORM_MsgField" name="content"></textarea>
        </div>
        <div class="formRow">
          <label for="MOD_TEXTFORM_NameField">파일 등록 </label><input type="file" name="file" id="file">
        </div>
        <button type="submit" class="btn">글 등록</button>
      </form>
      </div>
  </div>
</section>
```

* 기존에 글을 작성하던 페이지에 업로드 할 파일을 등록하는 태그를 추가했다.
* 사용자가 선택한 파일 객체가 전송될 수 있도록 인코딩 타입을 `multipart/form-data`로 지정해 주었다.

## BoardWriteAction.java

```java
package com.project.cafe.board.action;

import javax.servlet.ServletContext;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.oreilly.servlet.MultipartRequest;
import com.oreilly.servlet.multipart.DefaultFileRenamePolicy;
import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;
import com.project.cafe.board.db.BoardDAO;
import com.project.cafe.board.db.BoardDTO;

public class BoardWriteAction implements Action 
{
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        System.out.println("M : BoardWriteAction - execute() 호출");
		
        // 한글처리
        request.setCharacterEncoding("UTF-8");
		
        // 파일 업로드 먼저 하기
        // 1. 저장경로 생성
        ServletContext ctx = request.getServletContext();
        String realPath = ctx.getRealPath("/upload");
		
        // 2. 파일의 저장크기
        int maxSize = 10 * 1024 * 1024; // 10MB
		
        // 3. 파일업로드(객체 생성)
        MultipartRequest multi = new MultipartRequest(
                request, 
                realPath,
                maxSize,
                "utf-8",
                new DefaultFileRenamePolicy()
                );
		
        // 파라메터를 DTO에 저장
        // MultipartRequest 객체 안에 들은 파라미터 정보에 접근해야 한다.
        BoardDTO dto = new BoardDTO();
        dto.setContent(multi.getParameter("content"));
        dto.setId(multi.getParameter("id"));
        dto.setTitle(multi.getParameter("title"));
        
        // 파일이름에 접근할 때엔 getFilesystemName() 메소드 사용
        dto.setFile(multi.getFilesystemName("file"));
		
        // 사용자 ip주소 저장
        dto.setIp(request.getRemoteAddr());
        System.out.println("M : "+dto);
		
        // DB에 DTO 보내서 저장
        BoardDAO dao = new BoardDAO();
        dao.insertPost(dto);
		
        // 완료되면 글 목록 페이지로 이동
        ActionForward forward = new ActionForward();
        forward.setPath("./BoardList.bo");
        forward.setRedirect(true);
		
        return forward;
    }
}
```

## BoardDAO - insertPost(dto)

```java
public void insertPost(BoardDTO dto)
{
    int postNum = 0; 
		
    try {
        // 1.2. DB 연결
        con = getCon();
			
        // 3. sql 작성 & pstmt 객체
        // 이번 차례에 DB에 저장될 글번호 계산
        sql = "select max(num) from cafe_board";
        pstmt = con.prepareStatement(sql);
			
        rs = pstmt.executeQuery();
			
        if (rs.next())
            postNum = rs.getInt(1) + 1;
			
        // 3. 데이터 삽입용 sql 작성 & pstmt 설정
        sql = "insert into cafe_board(num, id, title, content, readcount, re_ref, re_lev, re_seq, date, ip, file) "
                + "values(?,?,?,?,?,?,?,?,now(),?,?)";
        pstmt = con.prepareStatement(sql);
			
        // ? 채우기
        pstmt.setInt(1, postNum);
        pstmt.setString(2, dto.getId());
        pstmt.setString(3, dto.getTitle());
        pstmt.setString(4, dto.getContent());
        pstmt.setInt(5, 0); // 처음에 조회수 0
        pstmt.setInt(6, postNum); // 답글의 그룹. 일반글의 글번호와 동일하게 만듦
        pstmt.setInt(7, 0); // 답글의 레벨. 처음엔 들여쓰기 없음
        pstmt.setInt(8, 0); // 답글의 순서. 처음엔 가장 최상단
        pstmt.setString(9, dto.getIp());
			
        if (dto.getFile() == null)
            pstmt.setString(10, "없음");
        else 
            pstmt.setString(10, dto.getFile());
			
        // 4. sql 실행
        pstmt.executeUpdate();
			
        System.out.println("DAO : 게시글 DB 삽입 완료");
    }
    catch (Exception e) {
        e.printStackTrace();
    }
    finally {
        closeDB();
    }
}
```

* 기존에 새 게시글을 `DB` 삽입하는 동작을 수행하던 `BoardWriteAction`과 `insertPost()` 메소드를 약간 수정했다.

## boardContent.jsp

```jsp
<tbody>
  <tr>
    <td colspan="5" style="white-space:pre-wrap; word-wrap:break-word; word-break: break-all;"><%=dto.getContent() %><br><br><br></td>
  </tr>
  <tr>
    <td colspan="2">첨부파일</td>
    <td colspan="3">
    <!-- 첨부파일이 있을 때에만 하이퍼링크 연결 -->
      <% if (dto.getFile() == null || dto.getFile().equals("없음")) { %>
        <%=dto.getFile() %>
      <% }
        else { %>
        <a href="./BoardFileDownloadAction.bo?file_name=<%=dto.getFile() %>"><%=dto.getFile() %></a>
      <% } %>
    </td>
  </tr>
</tbody>
```

* 게시글 내용을 보여주는 페이지에서는 첨부파일이 있을 때에만 하이퍼링크를 연결하도록 했다.

<p align="center"><img src="../../assets/images/fileDownload.png" width="500"></p>
<p align="center"><img src="../../assets/images/fileDownloadNull.png" width="500"></p>

* 여기까지 하면 파일 유무에 따라서 하이퍼링크 연결까지 된다.

## BoardFileDownloadAction.java

```java
package com.project.cafe.board.action;

import java.io.FileInputStream;
import java.net.URLEncoder;

import javax.servlet.ServletContext;
import javax.servlet.ServletOutputStream;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;

public class BoardFileDownloadAction implements Action 
{
    @Override
    public ActionForward execute(HttpServletRequest request, HttpServletResponse response) throws Exception 
    {
        System.out.println("M : BoardFileDownloadAction - execute() 호출");
		
        // 전달된 파일 이름 저장
        String fileName = request.getParameter("file_name");
		
        // 서버에 업로드 된 폴더 찾기
        String savePath = "upload";
		
        // 서버에 업로드 된 파일의 위치 계산(물리적 경로)
        ServletContext ctx = request.getServletContext();
        String downloadPath = ctx.getRealPath(savePath);
		
        System.out.println("downloadPath : "+downloadPath);
		
        // 다운로드 할 파일의 전체 경로 계산
        // 사용자 OS에 따라 연결문자 구분
        String filePath = null;
        String agent = request.getHeader("User-Agent");
        System.out.println("agent: "+agent);
		
        if (agent.indexOf("Windows") != -1)
            filePath = downloadPath + "\\" + fileName;
        else
            filePath = downloadPath + "/" + fileName;
		
        System.out.println("filePath : "+filePath);
		
        ///////////////////////////////////////////
		
        // 파일을 저장하는 배열(버퍼)
        byte[] b = new byte[4096]; // 4kb
		
        // 데이터를 주고받을 수 있는 통로 만들기
        FileInputStream fis = new FileInputStream(filePath);
		
        // MIME 타입 얻어오기
        String mimeType = ctx.getMimeType(filePath);
        System.out.println("mimeType: "+mimeType);
		
        // MIME 타입 정보가 없는 경우
        if (mimeType == null)
        {
            // 실제로 잘 알려지지 않은 이진파일을 의미하며, 브라우저가 자동 실행하지 않고 실행여부를 질문한다.
            mimeType = "application/octet-stream";
        }
		
        // 브라우저가 전달받은 MIME 타입으로 응답정보 설정
        response.setContentType(mimeType);
		
        // IE / 그 외 나머지 브라우저 구분
        boolean ieBrowser = (agent.indexOf("MISE") > -1 || agent.indexOf("Trident") > -1);
        if (ieBrowser)
        {
            // 인터넷 익스플로러일 때
            // 인터넷 익스플로러에서는 한글 다운로드시 한글 인코딩이 깨져서 utf-8로 바꿔준다.
            // 인터넷 익스플로러에서는 공백문자 표시가 [+]라서 [%20]으로 바꿔준다.
            fileName = URLEncoder.encode(fileName, "utf-8").replaceAll("\\+", "%20");
        }
        else
        {
            // 그 외 브라우저
            // 브라우저 다운로드시 한글 깨짐 처리
            // 8859-1 < EUC-KR < MS949 < UTF-8
            // -> 방향으로 갈수록 인코딩 범위가 커짐 (한글처리할 수 있는 범위가 늘어남)
            fileName = new String(fileName.getBytes("utf-8"), "iso-8859-1");
        }
		
        // 브라우저에서 해석되는(미리보기 형태로 바로 볼 수 있는) 파일도 다운로드 형태로 처리가능하게 변경
        response.setHeader("Content-Disposition", "attachment; filename=" + fileName);
		
        // 다운로드할 파일 출력
        ServletOutputStream out = response.getOutputStream();
		
        int data = 0;
        // 데이터를 배열을 사용해 배열의 길이만큼 잘라서 data에 저장한다. EOF가 아닐 때까지
        while ((data = fis.read(b, 0, b.length)) != -1)
        {
            // 가져온 data 길이만큼 출력
            out.write(b, 0, data);
        }
		
        // ServletOutputStream은 버퍼의 길이가 다 찼을 때에만 데이터를 전달하기 때문에 
        // flush로 버퍼의 빈 공간을 채워서 전송함
        out.flush();
		
        out.close();
        fis.close();
		
        return null;
    }
}
```

* 업로드 된 파일을 클릭하면 파일 다운로드를 처리하는 서블릿으로 이동한다.
* 다운로드 할 파일의 경로를 설정할 때 `Mac OS`에서는 파일 경로 구분자를 `"/"`를 사용하는데 `Windows OS`에서는 `"\"`를 사용하기 때문에 `filePath`를 설정하는 부분에서 `"\\"`만 쓰면 맥에서 경로를 제대로 못 찾는다.(난 맥북을 쓰는데 수업은 윈도우 기반이라서 처음에 윈도우 버전으로 따라하면서 `"\\"` 쓰니까 안 되서 헤멨었음)
* 그래서 사용자의 OS에 따라 다르게 처리하는 부분을 추가했다.

* 여기까지 하면 다운로드도 잘 된다.<br><br><br>

# 마감까지 
* `D-4`
