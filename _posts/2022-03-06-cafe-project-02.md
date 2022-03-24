---
title: 프로젝트) Cafe(웹 사이트) 만들기 2 - DB 테이블 만들기
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
* OpenJDK 8
* Eclipse 2021-12
* tomcat 8.5
* MySQL Workbench 8.0.19<br><br><br>

# 시작
* 2022.3.4 ~ <br><br><br>

# 주제
* 웹 백앤드 수업 중 중간 과제로 개인 프로젝트를 진행하게 되었다.
* 회원가입/로그인/탈퇴 등 기본적인 회원관리 시스템을 가진 웹 사이트를 만드는 것이다. 주어진 기한은 `한 달`
* 나는 `다음 카페`를 소규모로 만들어 보기로 했다. 평소 자주 이용하기도 했고 과제의 평가 기준에서 요구하는 기능들을 다 담고 있기도 했기 때문에 이번 기회에 구현해 보면 그동안 배운 것들을 활용하기에 좋을 거 같았다.
* 평가 기준에 사이트의 디자인 구현(HTML/CSS 등 프론트엔드)은 포함되지 않기 때문에 본인이 쓰고 싶은 HTML/CSS 템플릿을 구한 뒤 회원 관리 기능을 구현하면 된다.<br><br><br>

# 진행상황
<p align="center"><img src="../../assets/images/e-r-Diagram.png"></p><br>

* 오늘은 좀 더 진행하기 전에 `E-R 다이어그램`을 만들면서 `DB 테이블`을 정리하고 가는 것이 좋을 거 같아서 내 프로젝트에 필요한 `E-R 다이어그램`을 만들었다.
* 회원은 게시판에 글과 댓글을 쓰지 않을 수도 있지만 쓴다면 여러 개 쓸 수 있고 게시글과 댓글은 회원이 쓸 때에만 생성될 수 있다. 그리고 댓글 또한 게시글이 없으면 생성될 수 없고 한 게시글에 댓글이 달리지 않을 수도, 여러 개의 댓글이 달릴 수도 있다.
* 일단 이 정도로 틀을 잡아놓고 테이블들을 생성했다.

## 1. 회원 테이블(cafe_members)

```sql
create table cafe_members (
    member_num int primary key auto_increment,
    id varchar(10) unique not null,
    pass varchar(10) not null,
    name varchar(20) not null,
    birth date not null,
    age int not null,
    gender varchar(2) not null,
    address varchar(100),
    phone varchar(11) not null,
    email varchar(200),
    regdate timestamp not null
);
```

<p align="center"><img src="../../assets/images/memberTable.png"></p><br>

* member_num : 회원 번호. 1부터 차례대로 증가하며 회원의 고유 식별자로 사용
* id : 회원 아이디. 중복되지 않도록 `unique` 조건을 걸어주었다.
* pass : 회원 비밀번호
* name : 회원 이름
* birth : 회원의 생년월일
* age : 회원 나이
* gender : 회원 성별
* address : 회원의 거주지 주소
* phone : 회원 휴대폰 번호
* email : 회원 이메일 주소
* regdate : 회원이 가입한 날짜. `timestamp`로 가입하는 당시의 날짜와 시간으로 자동 저장

## 2. 게시글(post)

```sql
CREATE TABLE `cafe_board` (
  `num` int NOT NULL,
  `id` varchar(10) NOT NULL,
  `title` varchar(200) NOT NULL,
  `content` varchar(5000) NOT NULL,
  `readcount` int DEFAULT NULL,
  `re_ref` int DEFAULT NULL,
  `re_lev` int DEFAULT NULL,
  `re_seq` int DEFAULT NULL,
  `date` date DEFAULT NULL,
  `ip` varchar(200) DEFAULT NULL,
  `file` varchar(200) DEFAULT NULL,
  PRIMARY KEY (`num`)
)
```

<p align="center"><img src="../../assets/images/postTable.png"></p><br>

* num : 게시글 번호. 1부터 차례대로 증가하며 고유 식별자로 사용
* id : 작성자 아이디
* title : 게시글 제목
* content : 게시글 내용
* readcount : 조회수
* re_ref : 게시글 그룹. 답글을 달게 되면 몇 번 게시글의 답글인지 구분할 때 쓸 그룹이다.
* re_lev : 게시글의 들여쓰기 레벨. 어떤 게시글의 답글이면 1번 들여쓰고 답글의 답글이면 2번 들여쓰는 용도로 사용할 값이다.
* re_seq : 게시글 그룹에서 작성된 순서. 어떤 게시글에 답글이 달린 순서를 표시할 값이다.
* date : 최초로 작성된 날짜. YYYY-MM-DD 형식
* ip : 작성자의 `IP` 주소
* file : 파일 업로드 시 파일명

## 3. 댓글(comment)

```sql
create table comment (
	num int primary key auto_increment,
    post_num int not null,
	writer varchar(10) not null,
    content longtext not null,
    commented_date datetime not null,
    parent int default 0
);
```

<p align="center"><img src="../../assets/images/commentTable.png"></p><br>

* num : 댓글 번호. 1부터 차례대로 증가하며 고유 식별자로 사용
* post_num : 댓글이 등록된 글 번호
* writer : 댓글 작성자 아이디
* content : 댓글 내용
* commented_date : 댓글이 작성된 날짜
* parent : 만약 이 댓글이 대댓글이라면 대댓글을 단 원래 댓글의 댓글 번호. 모든 댓글이 대댓글이 되는 것은 아니기 때문에 디폴트값은 0으로 둔다. 0이라면 상위 댓글을 의미한다.<br><br><br>

# 마감까지 
* `D-29`
