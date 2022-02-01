---
title: HTML 문법
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - HTML
tags:
    - HTML
    - Front-end
---
# 👀 시작 전에
* `HTML` 문서는 크게 `<head>` 부분과 `<body>` 부분으로 나누어져 있다.
* 크롬 탭에서 보이는 제목을 지정하고 싶으면 `<head>` 부분에 `<title>` 태그를 이용해 제목을 적어준다.
* `<body>` 부분에 본격적인 웹 페이지 내용을 작성한다.<br><br>
 
## 🔸 HTML 주석문

```html
<!-- 주석 -->
```

* `HTML`에서의 주석문은 다른 프로그래밍 언어와는 다르게 위와 같이 쓴다. 

## 🔸 제목 태그

```html
<h1>제목</h1>
<h2>제목2</h2>
<h3>제목3</h3>
<h4>제목4</h4>
<h5>제목5</h5>
<h6>제목6</h6>
```

* 제목 태그에는 6가지가 있다.
* 1번이 글씨 크기가 가장 크고 숫자가 커질수록 글씨 크기가 작아진다.

## 🔸 문단 태그

```html
<p>문단 내용</p>
```

* 아래위로 공백이 약간 생기면서 한 문단을 만드는 것과 같은 효과를 줄 수 있다. 

## 🔸 글씨 꾸미기 태그

```html
<b>진하게</b>
<strong>진하게</strong>
<i>기울이기(이탤릭체)</i>
<em>기울이기</em>
<small>작게</small>
<u>밑줄</u>
<sup>위 첨자</sup>
<sub>아래 첨자</sub>
<del>취소선 긋기</del>

<blockquote>인용문 들여쓰기 효과</blockquote>

<hr> <!-- 수평선 긋는 태그 -->

<pre>
    원하는 
		모양대로
				출력
			이 가능한 태그
</pre>
```

* `<b>` 태그를 쓰면 글씨를 진하게 표현할 수 있고 `<i>` 태그를 쓰면 글씨를 옆으로 기울일 수 있다.

## 🔸 줄바꿈 태그
```html
안녕하세요!<br>
Hello!
```

* `HTML`에서는 단순히 엔터를 치는 것으로는 줄을 바꿀 수 없다. 
* 줄을 바꾸고 싶은 곳에서 `<br>` 태그를 적어줘야 줄바꿈이 적용된다.

## 🔸 하이퍼링크 태그
```html
<a href="http://www.naver.com"> 네이버 하이퍼링크 </a><br>
<a href="test1.html"> test1.html 문서 하이퍼링크 </a><br>
<a href="4.jpeg" download=""> 이미지 다운로드 </a><br>
<a href="ex4.html" target="_blank">새 창에서 연결</a><br>
```

* 웹 페이지 링크를 걸 수도 있고 내가 만든 `HTML` 페이지를 링크로 걸 수도 있다.
* 웹 페이지 링크를 작성할 땐 `http://`도 꼭 써주어야 제대로 동작한다.

### ☑️ 문서 내에서 하이퍼링크
```html
<a href="#content1" id="menu">메뉴1</a>
<a href="#content2">메뉴2</a>
<a href="#content3">메뉴3</a>
<br><br><br><br><br><br>

<a id="content1">메뉴1 문서</a>
<a href="#menu">메뉴 위로 이동</a>
<br><br><br><br><br><br>

<a id="content2">메뉴2 문서</a>
<a href="#menu">메뉴 위로 이동</a>
<br><br><br><br><br><br>

<a id="content3">메뉴3 문서</a>
<a href="#menu">메뉴 위로 이동</a>
```

## 🔸 이미지 삽입 태그
```html
<img src="1.jpeg"><br>
<img src="2.gif"><br>
<img alt="3번이미지" src="./3.png" width="50%" height="50%" border="5">
```

* 이미지를 삽입할 때엔 닫는 태그가 없어도 된다.
* 이미지 파일은 삽입하고자 하는 `HTML` 파일과 동일한 위치에 있어야 경로지정 없이 삽입할 수 있다.
* 같은 위치에 없다면 경로지정을 해 주어야 하는데
    * `.` : 현재폴더(생략가능)
    * `..` : 상위폴더
    * `상대참조` : 현재 페이지를 기준으로 상대적 파일을 찾는 방법
    * `절대참조` : /(root) 기준으로 파일 경로를 찾아가는 방법

### ☑️ img 태그 속성
* `src` : 원본(그림) 파일
* `alt` : 원본이 안 보일 때 표시할 그림 설명
* `width` : 이미지 너비(픽셀) 지정
    * `px` : 이미지 크기를 픽셀 단위로 지정. 고정값
    * `%` : 이미지 크기가 브라우저 크기에 따라 변동됨

### ☑️ 픽셀(Pixel)
* 화면을 이루는 빛(하나의 점)

### ☑️ 해상도
* 화면을 이루는 점(빛)의 개수

### ☑️ 웹에서 사용하는 이미지 형식
* .jpg : 사진형태. 색상과 명암을 다양하게 표현
* .gif : 256색상 사용. 작은 아이콘, 작은 이미지 사용. 움직이는 이미지
* .png : 색상 다양하게 표현. 투명한 배경색 가능. 웹에서 많이 사용

## 🔸 목록 만드는 태그
### 순서 있는 목록 
```html
<ol>
  <li> 항목1 </li>
  <li> 항목2 </li>
</ol>
```

* 1부터 차례대로 순서가 매겨지면서 목록이 만들어진다.

```html
<ol type="i">
  <li>항목1</li>
  <li>항목2</li>
</ol>
```

* `type`에 `1`, `a`, `A`, `i`, `I`(이탤릭)들을 사용해 순서를 매길 포맷을 지정해 줄 수 있다.

### 순서 없는 목록
```html
<ul>
  <li>항목1</li>
  <li>항목2</li>
</ul>
```

* 순서가 없이 점만 찍히는 목록이 만들어진다.

```html
<ul type="circle">
  <li>항목1</li>
  <li>항목2</li>
</ul>
```

* 마찬가지로 `disc`, `square`, `circle` 점 모양 속성을 지정해 줄 수 있다.

## 🔸 표 만드는 태그
```html
<table border="1">
  <caption>표제목</caption>
  <tr><td>1행1열</td><td>1행2열</td></tr>
  <tr><td>2행1열</td><td>2행2열</td></tr>
</table>
```

* `<table>` 태그를 이용해 틀을 만든 다음 `<tr>`을 사용해 가로로 줄을 그어 칸을 만들고 `<td>` 태그로 세로로 줄을 그어 칸을 만들 수 있다.

```html
<thead> <!-- 코드 상에서 영역 나눠서 작업할 때 사용 -->
<tr><th>제목1</th><th>제목2</th></tr>
</thead>

<tbody>
<tr><td>내용1</td><td>내용2</td></tr>
</tbody>

<tfoot>
<tr><td>내용3</td><td>내용4</td></tr>
</tfoot>
```

### 표 합치기
```html
<table border="1" width="500" height="300">
  <tr><td>용도</td><td>중량</td>  <td colspan="2">개수가격</td></tr>
  <tr><td rowspan="2">선물용</td><td>3kg</td><td>11-16과</td><td>35000원</td></tr>
  <tr>                         <td>5kg</td><td>18-26과</td><td>52000원</td></tr>
  <tr><td rowspan="2">가정용</td><td>3kg</td><td>11-16과</td><td>30000원</td></tr>
  <tr>                         <td>5kg</td><td>18-26과</td><td>47000원</td></tr>
</table>
```

* `colspan`으로 가로로 나란히 위치한 칸들을 합치고 `rowspan`으로 세로로 나란히 위치한 칸들을 합칠 수 있다.

## 🔸 폼(양식) 태그
* 기능과 데이터들을 묶어주는 것
* `submit` 버튼을 클릭하면 `폼태그`에 있는 내용(데이터, 이름 => 값)을 `서버(백엔드)`로 전송하는 기능을 `폼태그`를 사용해 만들 수 있다.
* 전송 방식에는 `Get` 방식과 `Post` 방식이 있다.

```html
<fieldset> <!-- 테두리를 그려서 그룹으로 묶어주는 태그 -->
	<legend>그룹이름1</legend>
	<label>아이디 :</label>
	<input type="text" name="id" value="아이디" size="30" maxlength="5" readonly="readonly"><br>
	<!-- size="30" 30자를 적을 수 있는 크기 maxlength="5" 입력학 글자개수 readonly="readonly" 읽기전용 -->
	<label>비밀번호 : </label>
	<input type="password" name="pass" required autofocus><br>
	<!-- 비밀번호 필수요소로 입력제어
	autofocus : 입력상자에 커서 깜박이게 하기  -->
	<label>자기소개 : </label>
	<textarea rows="5" cols="10"></textarea><br>
	<label>이메일 :</label>
	<input type="email" name="email" placeholder="이메일 형식"><br>
	<!-- placeholer : 배경이미지 형태로 설명글 -->
	<label>날짜 : </label>
	<input type="date, name="date" min="2022-01-01" max="22022-01-14">
	<label>날짜(월) : </label>
	<input type="week" name="week">
	<label>시간 : </label>
	<input type="time" name="time"><br>
	<label>날짜 시간 : </label>
	<input type="datetime-local" name="datetime"><br>
</fieldset>```

* `type`의 `text`는 입력하는 글자를 보여주는 것이고 `password`는 입력하는 글자를 보여주지 않는 것이다.

```html
<textarea rows="5" cols="30" placeholder="본사 지원 동기를 간략히 적어주세요."></textarea><br>
```

* `<textarea>`를 사용하면 긴 텍스트를 입력할 수 있는 `텍스트박스`를 만들 수 있다.
* `placeholder=""` 속성은 회색 글씨로 안내 사항을 입력해 놓는 것. 입력을 시작하면 사라진다.

```html
<fieldset>
	<legend>그룹이름2</legend>
	<label>검색 : </label>
	<input type="search" name="search"><br>
	<label>웹주소 : </label>
	<input type="url" name="url"><br>
	<label>연락처(모바일) : </label>
	<input type="tel" name="tel"><br> <!-- 모바일 환경에서는 숫자 키패드 나옴 -->
	<label>숫자 : </label>
	<input type="number" name="number"><br>
	<label>슬라이드 밸류값 설정 : </label>
	<input type="range" name="range"><br> 
</fieldset>
```

## 🔸 라디오 버튼(radio)과 체크박스(checkbox)

```html
성별 : <input type="radio" name="gender" value="남">남성 
      <input type="radio" name="gender" value="여">여성<br>
취미 : <input type="checkbox" name="hobby" value="여행">여행
	  <input type="checkbox" name="hobby" value="게임">게임
      <input type="checkbox" name="hobby" value="독서">독서<br>
목록상자 : <select name="sel" size="5" multiple="multiple">
		<option value="목1">목록1</option>
		<option value="목2">목록2</option>
		<option value="목3">목록3</option>
        </select>
```      

* `radio`는 하나만 선택 가능할 때 사용하고 `checkbox`는 여러개를 선택할 수 있을 때 사용한다.
* 이 때 `name`을 동일하게 해야 같은 그룹으로 취합되고 그 안에서 선택하는 것이 가능하다.

```html
<form action="test1.html" method="get"> 
파일업로드 : <input type="file" name="file"><br>
히든값 : <input type="hidden" name="hi" value="값"> <!-- 사용자에게는 보이지 않는 숨겨진 입력 필드 정의 -->
<input type="button" value="버튼"><br>
<input type="submit" value="전송"><br> <!-- action="test1.html"으로 작성 내용들을 전달함. 폼태그 밖에서 쓰면 작동 안됨  --> 
<input type="image" scr="5.jpeg" width="100" height="100">
<input type="reset" value="초기화"><br>
</form>
```

## 🔸 영역 지정 태그
```html
<div>
  큰 영역을 지정할 때 사용(웹 블록 지정)
</div>

<span> 인라인(작은 영역) 블록 지정 </span>
```

## 🔸 특수 문자 입력 태그
```html
&nbsp;  공백 표시
&lt;    꺽쇄(< >) 표시
&copy;  © 
&amp;   & 
&quot;  " 
&clubs; ♣
```
