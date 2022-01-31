---
title: JavaScript 기본 문법
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - JavaScript
tags:
    - JSP
    - JavaScript
---
# 자바스크립트란?
* 웹 페이지에 방문했을 때 사용자의 동작(이벤트)에 따른 처리를 하는 언어
* `인터프리터 언어`로 작성된 코드를 위에서 아래로 순차적으로 실행한다.
* `컴파일 언어`와 다른 점은 일단 실행을 하고 에러가 발생하기 전 까지의 코드만 실행한다.
* 객체 기반 언어이다.
* 오픈소스 언어
* 다양한 라이브러리(API) 사용 가능
    * `Jquery` 주로 사용 - `Ajax`, `Json`
* `HTML5`(웹표준) API 기반의 언어<br><br><br>

# 변수 선언
* `var` 키워드를 이용해 선언한다. 

```javascript
var num = 1;
```

* 일반적인 프로그래밍 언어와 다르게 자료형별로 구분해서 선언하지 않는다. 변수 선언과 동시에 초기화하는 내용물에 따라서 자바스트립트가 알아서 자료형을 지정한다.
* 편한 듯 하면서도 원래 하던 언어가 있어서 그런지 무의식적으로 int num ... 과 같이 타이핑하게 될 때가 많다.

## 🔸 var
* `var` 키워드는 같은 이름으로 중복해서 선언하고 값을 넣는 것이 가능하다.

```javascript
var name = "james";
console.log(name); // james

var name = "lily";
console.log(name); // lily
```

* 컴파일 언어를 하다 온 입장에서는 다소 이해가 되지 않지만 `자바스크립트`에서의 `var` 키워드는 이런 흐름이 가능하다. 
* 변수가 많아지면 중간에 꼬일 가능성이 아주 많아 보이는 속성이다. 그래서 이것을 보완하기 위해 나온 것이 `let` 키워드이다.

## 🔸 let
* `let` 키워드도 `var`처럼 변수를 선언하는 키워드지만 약간 속성이 다르다.

```javascript
let name = "james";
console.log(name); // james

let name = "lily";
console.log(name); 
// Uncaught SyntaxError: Identifier 'name' has already been declared
```

* `let` 키워드를 사용하니까 컴파일 언어와 같은 흐름을 보인다. 

## 🔸 const
* 컴파일 언어에서와 마찬가지로 상수 선언 키워드이다.
* `const` 다음에 자료형은 쓸 필요 없이 바로 변수 이름을 선언하고 초기화하면 된다.

```javascript
const name = "james";
```
<br><br>

# 연산자
* 연산자의 사용은 동일하지만 자바스크립트에는 `===`과 `!==`이라는 연산자가 있다.
* `데이터값`과 `자료형`이 **모두 동일**한지 확인하는 연산자이다.
* `==`, `!=` 연산자들은 단순히 값만 확인하고 자료형이 같은지는 확인하지 않는다.
    * 그래서 문자 100과 숫자 100을 `==`을 이용해서 값이 같은지 비교하면 `true`를 리턴한다.
    * `===` 연산자 사용시 `false` 리턴
    
## 🔸 연산자 우선순위<br>
1. ()<br>
2. 단항연산자(++, --, !)<br>
3. 산술연산자(+, -, *, /, %)<br>
4. 비교연산자(>, <, >=, <=, ==, !=, ===, !==)<br>
5. 논리연산자(&&, ||)<br>
6. 복합대입연산자(=, +=, -=, *=, /=, %=)<br><br> 

# 함수
* 함수 또한 자료형을 구분하지 않고 `function` 키워드 하나만 쓴다.

```javascript
function 함수명()
{
    실행문;
}
호출법 : 함수명();
```
<br><br>

* 익명함수도 사용 가능하다.
```javascript
변수 = function ()
{
    실행문;
}
호출법 : 참조 변수명();
```
<br><br>

# 객체
* 내 눈앞에 보이는 모든 대상

### 🔸 객체 생성은 new 키워드를 이용해서 생성한다.
```javascript
var 참조변수(레퍼런스) = new Object();
```
<br><br>

## 1) 내장객체
* 자바스크립트 안에 포함된 객체
    * `문자(String)`, `날짜(Date)`, `수학(Math)`, `숫자(Number)`, `배열(Array)`, ...
    
### 🔸 날짜(Date)객체

```javascript
var day = new Date(2022,0,1); // 2022년 1월 1일
```

* `자바스크립트`에서의 `날짜객체`에서 좀 희안한 점이라면 월(Month)이 실제 숫자보다 **1 적은 형태**로 표현된다는 것이다. `new Date(2022,1,1);` 이라 쓰면 2022년 2월 1일이다.

### 🔸 배열(Array)객체

```javascript
var arr1 = new Array();
var arr2 = new Array('a', 'b', 'c'); // 다 가능
let arr3 = [1, 2, 3]; 
```

* `자바스크립트`에서 `배열`은 `배열객체`를 통해 생성 할 수도 있고 컴파일 언어와 비슷하게 `[]를 사용해서 초기화` 하는 형태로 선언할 수도 있다. 
* 또 특이한 점이라면 **하나의 배열에 서로 다른 자료형을 가진 값을 담을 수 있다**는 것이다.

## 2) 브라우저 객체 모델(BOM)
* 웹 브라우저에 포함되어 브라우저를 표현하는 객체
* 계층형 구조로 되어 있다.

```javascript
         / document, screen, ...
window -   location, history
         \ navigator
```

* 알림창을 출력할 때 
```javascript
window.alert("메시지 출력 메서드");
```
* 를 많이 쓴다.

```html
<script type="text/javascript">
function myOpen()
{
    window.open("test1.html", "test1", "width=300, height=200, top=150, left=500");
}
</script>

<input type="button" value="팝업창 열기" onclick="myOpen();">
```

* 위 처럼 `HTML` 태그와 함께 쓸 수 있다.

### 🔸 location 객체

```javascript
location.href = 'test1.html'; // test1.html로 이동
location.reload(); // 페이지 새로고침
location.replace('test2.html'); // 현재 페이지를 test2.html로 변경
```

* 주소창과 관련된 기능을 가지고 있다.
* 특정 페이지로 이동하거나 현재 페이지를 다시 로드하는 등의 동작을 수행할 수 있다.

### 🔸 history 객체

```javascript
history.back(); // 뒤로 가기
history.forward(); // 앞으로 가기
history.go(-숫자); // 숫자만큼 뒤로 가기
history.go(숫자); // 숫자만큼 앞으로 가기
```

* 페이지의 방문기록에 따른 동작을 수행할 수 있다.

### 🔸 navigator 객체
* 방문자(클라이언트)의 브라우저 정보 및 운영체제정보 확인 가능

```javascript
<script type="text/javascript">
    alert(navigator.userAgent);
</script>
```

* 위와 같이 쓰면 팝업창에서 사용자의 브라우저 정보와 운영체제정보를 볼 수 있다.
    
## 3) 문서 객체 모델(DOM)
* HTML문서 구조(객체)

```javascript
document.images[0].src; // 현재 페이지에 있는 이미지들 중 첫번째 소스에 접근
```

```javascript
document.폼태그명.속성
```

* `HTML`의 `폼태그`와 결합해 사용할 수 있다.

### 🔸 폼태그 form
* 사용자의 정보를 입력받아서 특정 페이지(action)로 정보를 전달하는(submit) 태그
* 전달 방식에서는 `Get`방식과 `Post`방식이 있다.

```html
<fieldset>
    <legend>회원정보 가입하기</legend>
    <form action="itwill.jsp" name="fr2" method="get" onsubmit="return fun8();">
        <label>아이디 : </label><input type="text" name="id" value=""> <br>
        비밀번호 : <input type="password" name="pw"> <br> 
        주민번호 : <input type="text" name="ju1" onkeyup="check1();" maxlength="6"> - <input type="text" name="ju2" maxlength="7" onkeyup="check2();">
        <br> 메세지 :
        <textarea rows="5" cols="10" name="msg"></textarea>
        <br> <input type="submit" value="회원 가입하기" onsubmit="return fun8();">

        <hr>
        <input type="button" value="속성 확인 버튼" onclick="fun5()"> 
        <input type="button" value="속성 변경 버튼" onclick="fun6()"> 
        <input type="button" value="데이터 확인 버튼" onclick="fun7()">
    </form>
</fieldset>
```

* 위와 같이 사용 가능하다.
