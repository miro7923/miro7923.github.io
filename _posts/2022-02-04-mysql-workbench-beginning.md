---
title: MySQL Workbench 시작하기
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - MySQL
tags:
    - MySQL
    - Workbench
    - Database
    - JSP
---
# 👀 MySQL Workbench란?
* 터미널을 통한 데이터베이스 관리를 좀 더 편하게 할 수 있게 해주는 도구
* `IDE`를 이용해서 프로그래밍 하는 것처럼 자동완성도 되고 테이블도 시각적으로 좀 더 편하게 볼 수 있고 터미널보다 훨씬 편하다.
* 최신버전에는 버그가 있어서 `8.0.19` 버전으로 수업을 진행할 예정
* 윈도우라면 `MySQL` 인스톨러에서 `Remove` 버튼을 눌러 개별 프로그램만 간편히 제거한 후 `Add` 버튼을 눌러 원하는 버전으로 선택해서 설치할 수 있다.
* 하지만 난 맥을 써서 `homebrew`로 `MySQL`만 따로 깔았기 때문에 `Workbench`는 홈페이지에서 dmg 파일을 따로 받아서 설치했다.
<br><br><br>

# MySQL 주석문
```sql
# 주석문
-- 주석문
/* 주석문 */
```

* 셋 다 사용가능하다.
<br><br><br>

# DB 조회
```sql
select * from jspdb.tbl_member;
```

* `Workbench`에서 테이블을 조회하려면 테이블 앞에 `DB명`을 꼭 붙여줘야 한다. 
* 그런데 매번 이렇게 쓰려면 좀 귀찮다...

```sql
use jspdb;

select * from tbl_member;
```

* `C++`에서 `using`을 쓰듯 `use`를 써서 `DB명`을 선언해주면 그 다음부터는 `DB명` 없이 테이블을 부를 수 있다.

## 쿼리문 실행
```
Windows) ctrl + enter
Mac) command + enter
```

* 일정 쿼리 구문들만 실행하고 싶으면 해당 쿼리문들을 블록으로 선택한 후 위 명령어를 입력하면 해당 쿼리문들만 실행된다.

```
Windows) ctrl + shift + enter
Mac) command + shift + enter
```

* 전체 쿼리 실행은 위 명령어를 입력한다.
