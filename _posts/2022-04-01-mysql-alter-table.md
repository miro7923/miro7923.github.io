---
title: MySQL) 다른 데이터베이스로 테이블 이동, 복사 및 변경하기
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - MySQL
tags:
    - MySQL
    - alterTable
---
# 다른 데이터베이스로 테이블을 이동하고 싶을 때
* 테이블 이름을 변경하는 명령어는 다음과 같다.

```sql
mysql> alter table 테이블명 rename 새 테이블명;
```
<br>

* 이걸 이용해서 테이블을 다른 데이터베이스로 옮길 수 있다.

```sql
mysql> alter table 데이터베이스A.테이블명 rename 데이터베이스B.테이블명;
```
<br><br><br>

# 테이블 복사

```sql
mysql> create table 새 테이블명 like 복사할 테이블명;
```
<br>

* 이걸 이용해서 다른 데이터베이스에 있는 테이블을 복사할 수 있다.

```sql
mysql> create table 데이터베이스A.테이블명 like 데이터베이스B.테이블명;
```
<br>

* `insert` 구문을 이용한 방법

```sql
mysql> insert into 새 테이블명 select * from 원본테이블명;
```
<br>

* `insert` 구문을 이용해서 다른 데이터베이스에 있는 테이블 복사

```sql
mysql> insert into 데이터베이스B.테이블명 select * from 데이터베이스A.테이블명;
```
