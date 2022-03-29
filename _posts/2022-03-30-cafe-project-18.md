---
title: 프로젝트) Cafe(웹 사이트) 만들기 18 - 우편번호 검색 기능 추가
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
* 다음 우편번호 서비스 API를 이용해 회원가입 시 우편번호와 주소를 검색하는 기능을 추가했다.
* [다음 우편번호 서비스](https://postcode.map.daum.net/guide)

## join.jsp

```jsp
<div class="formRow">
  <label for="MOD_TEXTFORM_NameField">우편번호 </label>
  <label class="phone">
    <input type="text" name="postalcode" id="postalcode" placeholder="우편번호" readonly>
    <button id="postalBtn" class="btn" name="postalBtn" onclick="execDaumPostcode()">우편번호 찾기</button><br>
  </label>
</div>
<div id="postalCodeMsg"></div>
<span id="guide" style="color:#999;display:none"></span>
<div class="formRow">
  <label for="MOD_TEXTFORM_NameField">도로명 주소 </label>
  <input type="text" id="roadAddress" name="roadAddress" placeholder="도로명주소" readonly>
</div>
<div class="formRow">
  <label for="MOD_TEXTFORM_NameField">상세 주소 </label>
  <input type="text" id="detailAddress" name="detailAddress" placeholder="상세주소">
</div>

<script src="//t1.daumcdn.net/mapjsapi/bundle/postcode/prod/postcode.v2.js"></script>
<script>
function execDaumPostcode() {
    new daum.Postcode({
        oncomplete: function(data) {
            // 팝업에서 검색결과 항목을 클릭했을때 실행할 코드를 작성하는 부분.

            // 도로명 주소의 노출 규칙에 따라 주소를 표시한다.
            // 내려오는 변수가 값이 없는 경우엔 공백('')값을 가지므로, 이를 참고하여 분기 한다.
            var roadAddr = data.roadAddress; // 도로명 주소 변수
            var extraRoadAddr = ''; // 참고 항목 변수

            // 법정동명이 있을 경우 추가한다. (법정리는 제외)
            // 법정동의 경우 마지막 문자가 "동/로/가"로 끝난다.
            if(data.bname !== '' && /[동|로|가]$/g.test(data.bname)){
                extraRoadAddr += data.bname;
            }
            // 건물명이 있고, 공동주택일 경우 추가한다.
            if(data.buildingName !== '' && data.apartment === 'Y'){
               extraRoadAddr += (extraRoadAddr !== '' ? ', ' + data.buildingName : data.buildingName);
            }
            // 표시할 참고항목이 있을 경우, 괄호까지 추가한 최종 문자열을 만든다.
            if(extraRoadAddr !== ''){
                extraRoadAddr = ' (' + extraRoadAddr + ')';
            }

            // 우편번호와 주소 정보를 해당 필드에 넣는다.
            document.getElementById('postalcode').value = data.zonecode;
            document.getElementById("roadAddress").value = roadAddr;
            // document.getElementById("sample4_jibunAddress").value = data.jibunAddress;
                
            // 참고항목 문자열이 있을 경우 해당 필드에 넣는다.
            /*if(roadAddr !== ''){
                document.getElementById("sample4_extraAddress").value = extraRoadAddr;
            } else {
                document.getElementById("sample4_extraAddress").value = '';
            }*/

            var guideTextBox = document.getElementById("guide");
            // 사용자가 '선택 안함'을 클릭한 경우, 예상 주소라는 표시를 해준다.
            if(data.autoRoadAddress) {
                var expRoadAddr = data.autoRoadAddress + extraRoadAddr;
                guideTextBox.innerHTML = '(예상 도로명 주소 : ' + expRoadAddr + ')';
                guideTextBox.style.display = 'block';

            } else if(data.autoJibunAddress) {
                var expJibunAddr = data.autoJibunAddress;
                guideTextBox.innerHTML = '(예상 지번 주소 : ' + expJibunAddr + ')';
                guideTextBox.style.display = 'block';
            } else {
                guideTextBox.innerHTML = '';
                guideTextBox.style.display = 'none';
            }
        }
    }).open();
}
</script> 
```

* `API` 안내 페이지에 나와 있는대로 추가하고 나에게 맞게 조금만 수정하니까 아주 쉽게 추가가 되었다.

## MemberJoinAction.java

```java
package com.project.cafe.member.action;

import java.io.PrintWriter;
import java.sql.Date;
import java.sql.Timestamp;
import java.util.Calendar;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.project.cafe.action.Action;
import com.project.cafe.action.ActionForward;
import com.project.cafe.member.db.MemberDAO;
import com.project.cafe.member.db.MemberDTO;

// 회원가입 처리동작 수행
// model 객체로 pro 페이지 역할을 한다.
public class MemberJoinAction implements Action
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
        System.out.println("M : MemberJoinAction - execute() 호출");
		
        // 한글처리
        request.setCharacterEncoding("UTF-8");
		
        // 전달받은 파라미터 저장 (JSP 페이지가 아니므로 액션태그는 쓸 수 없고 setter를 이용해 저장한다)
        MemberDTO dto = new MemberDTO();
        dto.setId(request.getParameter("id"));
        dto.setPass(request.getParameter("pass"));
        dto.setName(request.getParameter("name"));
        dto.setBirth(Date.valueOf(request.getParameter("birth")));
        dto.setEmail(request.getParameter("email"));
        dto.setGender(request.getParameter("gender"));
		
        // 입력받은 생년월일로 나이 계산
        int age = getAge(request.getParameter("birth"));
        dto.setAge(age);
		
        // 우편번호 저장
        dto.setPostalcode(Integer.parseInt(request.getParameter("postalcode")));
		
        // 주소 필드 합친 후 저장
        String roadAddress = request.getParameter("roadAddress");
        String detailAddress = request.getParameter("detailAddress");
        dto.setAddress(roadAddress + " " + detailAddress);
		
        // 폰번호 3개 필드 합친 후 저장
        StringBuilder sb = new StringBuilder();
        sb.append(request.getParameter("phone1"));
        sb.append(request.getParameter("phone2"));
        sb.append(request.getParameter("phone3"));
        dto.setPhone(sb.toString());
		
        // 날짜 정보 추가 저장
        dto.setRegdate(new Timestamp(System.currentTimeMillis()));
		
        System.out.println("M : 전달된 회원 정보 저장");
        System.out.println("M : " + dto);
		
        // DAO 객체 생성
        MemberDAO dao = new MemberDAO();
		
        // 회원가입 메서드 호출
        dao.insertMember(dto);
		
        System.out.println("M : 회원가입 완료");
		
        // 페이지 이동 (로그인 페이지로 - ./login.me)		
        response.setContentType("text/html; charset=utf-8");
        PrintWriter out = response.getWriter();
        out.print("<script>");
        out.print("alert('회원가입이 완료되었습니다!');");
        out.print("location.href='./login.me';");
        out.print("</script>");
		
        out.close();
		
        return null;
    }
}
```

* 우편번호 필드가 추가되었기 때문에 회원가입처리를 하는 부분도 수정해 주었다.
* `DB` 테이블에 우편번호를 저장할 컬럼도 추가해 주었다.<br><br><br>

# 마감까지 
* `D-5`
