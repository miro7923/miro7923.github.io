---
title: DB 구동 및 접속
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Database
tags:
    - DB
---

# 🎬 DB 구동 및 접속<br>

## 1. Linux 부팅 및 터미널 실행
* Windows에서 가상 PC를 실행한 뒤 Linux 부팅 후 로그인한다.
* Linux 부팅이 완료되면 **마우스 우클릭 - 터미널 열기**

## 2. Listener 시작
```
$ lsnrctl start
```
* Listener를 시작한다. 
* Listener는 네트워크를 이용하여 클라이언트에서 오라클 서버로 연결하기 위한 오라클 네트워크 관리자이다.
* 리스너를 시작하지 않아도 DB 구동엔 문제가 없지만 네트워크를 통한 연결은 모두 리스너가 담당하기 때문에 리스너를 시작하지 않으면 DB에 접속하고자 하는 클라이언트들이 접속할 수 없다. 
* 따라서 터미널이 실행되면 가장 먼저 리스너를 시작한 후 다음 프로세스를 진행한다.<br><br>

## 3. SqlPlus 실행
```
$ sqlplus /nolog
```

## 4. Database 접속
```
$ conn 아이디/비밀번호 as sysdba(권한)
```
### 🔸 DB User
* sysdba : dba + DB 생성 + DB 시작/종료 권한
* system : dba 권한
* hr : Object(테이블, 뷰, 시퀀스 등) 생성 및 운영 권한<br><br>

## 5. Database 시작
```
SQL> startup
```

## 6. HR 사용자로 DB Login
```
SQL> conn 아이디/비밀번호
```

## 7. 사용자 확인
```
SQL> show user
```
* HR 사용자로 최종 로그인 되었는지 확인한다.

### 🔸 User 비밀번호 변경 방법
```
SQL> alter user 아이디
     identified by 새 비밀번호;
```
