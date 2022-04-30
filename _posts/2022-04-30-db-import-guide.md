---
title: MySQL Workbench에서 EER Diagram model & SQL Script import Guide
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Uno mas
tags:
    - Project
    - UnoMas
    - MySQLWorkbench
    - Log
---

* `MySQL Workbench`에 내장되어 있는 `EER` 다이어그램 제작툴의 사용법과 `SQL` 스크립트를 불러와서 테이블 생성하는 법 가이드<br><br>

# EER 다이어그램 모델 불러오기
1. `master` 브랜치를 `pull`한다.
2. `MySQL Workbench` 실행

<p align="center"><img src="../../assets/images/workbenchEer1.png" width="600"></p>￼

* 빨간 박스 선택

<p align="center"><img src="../../assets/images/workbenchEer2.png" width="600"></p>￼

* `+` 눌러서 모델 추가함

<p align="center"><img src="../../assets/images/workbenchEer3.png" width="800"></p>￼

* 이런 창이 나올 것임. 여기에서 `File` 메뉴 클릭

<p align="center"><img src="../../assets/images/workbenchEer4.png" width="600"></p>￼

* `Open Model` 클릭

<p align="center"><img src="../../assets/images/workbenchEer5.png" width="600"></p>￼

* `/Uno-Mas/unomasdb/unomasTable.mwb` 경로에 있는 파일 선택하고 `Open`

<p align="center"><img src="../../assets/images/workbenchEer6.png" width="800"></p>￼

* 그럼 만들어진 다이어그램 모델 확인 가능!<br><br>

# 다이어그램 세부사항 확인

<p align="center"><img src="../../assets/images/workbenchEer7.png" width="600"></p>￼

* 테이블 사이 관계선 위에 마우스오버하면 외래키로 연결된 컬럼을 표시해 준다.

<p align="center"><img src="../../assets/images/workbenchEer8.png" width="600"></p>￼

* 테이블 이름이 있는 영역에 마우스오버하면 이 테이블과 관계를 맺고 있는 테이블들을 표시해준다.

<p align="center"><img src="../../assets/images/workbenchEer9.png" width="800"></p>￼

* 테이블명을 더블클릭하면 세부사항을 확인할 수 있다.

<p align="center"><img src="../../assets/images/workbenchEer10.png" width="800"></p>￼

* 각 테이블별로 설명도 써 놨으니까 참고하거나 본인이 더 필요하다고 생각되는 메모가 있으면 추가할 수 있다.

<p align="center"><img src="../../assets/images/workbenchEer11.png" width="800"></p>￼

* 컬럼을 클릭하면 주석문 확인이 가능하다. 존재가 이해 안 되는 컬럼은 여기를 추가하고 본인이 필요하면 아래에 주석문 추가 가능 <br><br>

# 공유받은 SQL Script로 테이블 생성하기

<p align="center"><img src="../../assets/images/workbenchEer12.png" width="600"></p>￼

* 스키마 생성한 후 사진상 메뉴로 들어감

<p align="center"><img src="../../assets/images/workbenchEer13.png" width="800"></p>￼

* `/Uno-Mas/unomasdb/unomasDbTable.sql` 경로에 있는 파일 선택

<p align="center"><img src="../../assets/images/workbenchEer14.png" width="800"></p>￼

* 전체실행 버튼 눌러서 실행하면 테이블 생성됨. 

<p align="center"><img src="../../assets/images/workbenchEer15.png" width="800"></p>￼

* 하단 로그창에 성공적으로 생성됐다는 메시지 뜨면 다 생성된 것임. 안 보이면 테이블 목록 새로고침하면 보일 것임

<p align="center"><img src="../../assets/images/workbenchEer16.png" width="200"></p>￼

<br><br><br>

# 참고
* [MySQL Workbench 로 DB 설계하기](https://litiblue.com/post/mysql-workbench-db/)
