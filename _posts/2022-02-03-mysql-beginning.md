---
title: MySQL 시작하기
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - MySQL
tags:
    - MySQL
    - Database
    - JSP
---
# 👀 MySql 시작하기
* 맥 기준으로 `MySql`을 설치하는 방법에는 2가지가 있는데 
* `homebrew`를 통해 설치하는 방법과
* MySql 홈페이지에서 `dmg` 파일을 다운받아 설치하는 방법이 있다. <br><br>

* 나는 여기서 `homebrew`를 통해 설치하는 방법을 택했다.
* `homebrew`는 맥에서 쓸 수 있는 터미널 버전 앱스토어 같은 느낌으로 개발에 필요한 각종 소프트웨어들을 명령어 한 줄로 간편하게 설치할 수 있다. 

## MySql 설치
* `homebrew`가 설치되어 있다는 전제하에 `homebrew` 터미널을 열어 명령어 입력을 통한 설치를 진행할 것인데, 먼저 홈 브루를 최신 버전으로 업데이트 시켜주고 시작하자.
```
brew update
```

* 업데이트가 완료되면 아래 명령어를 입력하면 MySql이 설치된다. 
```
brew install mysql
```

* 설치가 완료된 후 
```
brew list
```
* 를 입력하면 홈 브루를 통해 설치된 프로그램들이 나열되는데 그 중에 mysql이 있으면 설치가 잘 된 것임

## MySql 실행
* 아까 설치할 때 썼던 터미널창에서 명령어 입력을 통해 `MySql`을 실행할 수 있는데 역시 2가지 방법이 있다.
```
brew services start mysql 혹은
mysql.server start
```
* 둘 중 하나를 입력해서 `MySql`서버를 시작할 수 있다.
* 네트워크 연결 허용하겠냐는 창이 뜨면 허용하겠다고 하면 된다.

## MySql 초기 설정
* 서버가 시작되었다고 바로 쓸 수 있는 것은 아니고 간단한 설정을 해 주어야 한다.
``` 
mysql_secure_installation
```
* 위 명령어를 입력하면 `MySql` 초기 세팅을 시작할 수 있다.<br><br>

* "Would you like to setup VALIDATE PASSWORD component? (Press y|Y for Yes, any other key for No)"
* 비밀번호를 복잡하게 설정하겠느냐는 것인데 아직 초보 단계니까 `No`를 입력해서 '1234'와 같은 쉬운 비밀번호를 설정한다. (`Yes`를 입력하면 복잡한 비밀번호 설정)
* 새 비밀번호를 입력하라는 메시지가 나오면 입력하고 엔터 누르면 된다.<br><br>

* "Remove anonymous users? (Press y|Y for Yes. any other key for No)"
* 사용자 설정을 묻는 것인데 `Yes`를 선택했다. 
* Yes - 접속하는 경우 "mysql -uroot"처럼 -u 옵션 필요
* No - 접속하는 경우 "mysql"처럼 -u 옵션 불필요<br><br>

* "Disallow root login remotely? (Press y|Y for Yes, any other key for No)"
* 다른 IP에서 root 아이디로 원격 접속이 가능한지 묻는 것인데 `Yes`는 불가능, `No`는 가능
* 나는 `Yes`로 선택했다.<br><br>

* "Remove test database and access to it? (Press y|Y for Yes, any other key for No)"
* 테스트 데이터베이스를 삭제할건지 말건지 묻는 것인데 나는 `Yes`로 선택했다.<br><br>

* "Reload privilege tables now? (Press y|Y for Yes, any other key for No)"
* 변경된 권한을 테이블에 적용하는 설정에 대한 질문인데 이 질문은 무조건 `Yes`로 선택하는 것이 좋다고 한다.<br><br>

* 여기까지 하면 모든 초기 설정이 완료된다.

## MySql 접속
```
mysql -u root -p
```
* 위 명령어를 입력하면 root 아이디로 로그인할 수 있다.
* 비밀번호 입력창이 나오면 비밀번호를 입력 후(보이지 않지만 그냥 치면 됨) 엔터를 누르면 로그인이 완료된다.
* 정상적으로 로그인 되면 쉘이 `mysql>`로 바뀐다. 

```
mysql> status;
```
* 위 명령어를 입력하면 현재 설정을 확인할 수 있다. 

## MySql 종료
```
mysql> exit
```
* 위 명령어를 입력하면 `Bye`라는 메시지와 함께 `mysql`에서 빠져나올 수 있다.

```
brew services stop mysql 혹은
mysql.server stop
```
* 둘 중 하나를 입력하면 `MySql` 서버가 종료된다.
