---
title: Mac) Java OpenJDK 버전 여러개 설치해 놓고 돌려가며 쓰기
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Java
tags:
    - Java
    - OpenJDK
    - Homebrew
---
👀 터미널에서 단어 하나만 입력하면 `OpenJDK 8` 버전과 `OpenJDK 11` 버전을 그 때 그 때 기본으로 세팅할 수 있다.

# Homebrew로 OpenJDK 설치
```java
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
* `Homebrew`가 설치되어 있지 않다면 위 명령어를 터미널에 입력해서 설치한다. 
* 처음엔 그림도 없고 `CLI` 환경이라 겁 먹었는데 적응되니까 세상에서 젤 편한 브루~ 😄
<br><br>

```java
brew install --cask adoptopenjdk
```
* 브루용 터미널을 켠 다음에 위 명령어를 입력하면 현재 `OpenJDK`의 최신버전을 설치할 수 있다. 
* 현재 17, 18 버전까지 나와있는데 많이 사용하는 버전은 8과 11 버전인 거 같다. 
* 그래서 나도 8과 11 버전만 설치할 것이다. 

## 버전별 명령어 리스트
![openjdkVersions](../../assets/images/openjdkVersions.png)

```java
brew tap AdoptOpenJDK/openjdk
brew install --cask adoptopenjdk11
```
* 현재 8버전은 깔려 있어서 11버전만 추가로 설치했다.
* 위 목록에서 설치하고 싶은 버전을 `--cask` 뒤에 입력하면 된다.
* `Homebrew`를 통해 설치하는 것은 정식 루트를 통하는 것은 아니고 커뮤니티에 배포되어 있는 것을 사용하는 것이라고 하는데 오라클 JDK가 유료화 된 이후로 다 이렇게 써서 문제 되는 것은 없다고 한다. 
<br><br>

```java
🍺  adoptopenjdk11 was successfully installed!
```
* 정상적으로 설치가 되면 마지막에 성공적으로 설치되었다는 메세지를 볼 수 있다. 
<br><br>

```java
% java -version
openjdk version "1.8.0_302"
OpenJDK Runtime Environment (Temurin)(build 1.8.0_302-b08)
OpenJDK 64-Bit Server VM (Temurin)(build 25.302-b08, mixed mode)
```
* 현재 사용중인 자바의 버전을 확인해보면 기존에 사용하던 것이 있어서 8버전이 나온다.
<br><br>

```java
% /usr/libexec/java_home -V
Matching Java Virtual Machines (3):
    11.0.11 (x86_64) "AdoptOpenJDK" - "AdoptOpenJDK 11" /Library/Java/JavaVirtualMachines/adoptopenjdk-11.jdk/Contents/Home
    1.8.0_312 (arm64) "Azul Systems, Inc." - "Zulu 8.58.0.13" /Library/Java/JavaVirtualMachines/zulu-8.jdk/Contents/Home
    1.8.0_302 (x86_64) "Eclipse Temurin" - "Eclipse Temurin 8" /Library/Java/JavaVirtualMachines/temurin-8.jdk/Contents/Home
```
* 설치된 모든 자바 버전을 보고 싶으면 `/usr/libexec/java_home -V` 명령어를 입력하면 된다.
* 사용중이던 8버전 외에 11버전도 추가로 잘 설치된 것을 볼 수 있다. <br><br>

# 기본으로 사용할 OpenJDK 버전 바꾸기
* 컴퓨터에 설치된 자바가 하나뿐이라면 그게 자동으로 기본값으로 설정이 되지만 나는 사정상 11로 완전 갈아타면 안 되고 8버전도 써야 하기 때문에 두 개를 바꿔가며 써야 하는데 매번 환경설정을 해주려면 참 귀찮을거 같아서 좀 미뤄왔었다.
* 그런데 구글링 해 보니까 완전 편한 방법이 있었다.

```java
export JAVA_HOME=$(/usr/libexec/java_home -v 1.8)
export PATH="/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/sbin:$JAVA_HOME"
alias setJava8='export JAVA_HOME=$(/usr/libexec/java_home -v 1.8)'
alias setJava11='export JAVA_HOME=$(/usr/libexec/java_home -v 11)'
```
* 위 명령어를 한 줄씩 복붙해서 실행하면 자바 버전별로 `앨리어스`를 지정해 사용할 수 있다.
* 이제 터미널에서 `setJava11`만 입력하면 11버전으로 바꿀 수 있다! 8로 돌아가는 것도 마찬가지

```java
% setJava11
% java -version
openjdk version "11.0.11" 2021-04-20
OpenJDK Runtime Environment AdoptOpenJDK-11.0.11+9 (build 11.0.11+9)
OpenJDK 64-Bit Server VM AdoptOpenJDK-11.0.11+9 (build 11.0.11+9, mixed mode)

% setJava8
% java -version
openjdk version "1.8.0_312"
OpenJDK Runtime Environment (Zulu 8.58.0.13-CA-macos-aarch64) (build 1.8.0_312-b07)
OpenJDK 64-Bit Server VM (Zulu 8.58.0.13-CA-macos-aarch64) (build 25.312-b07, mixed mode)
```
<br><br>

## 출처
* [[Mac] OpenJDK 버전별로 여러개 설치하기 ( using Homebrew )](https://yonguri.tistory.com/119)
* [MacOS에서 오라클 JDK를 삭제하고 OpenJDK 11 설치](https://velog.io/@jsoh/%EC%98%A4%EB%9D%BC%ED%81%B4-JDK%EB%A5%BC-%EC%82%AD%EC%A0%9C%ED%95%98%EA%B3%A0-OpenJDK-11-%EC%84%A4%EC%B9%98)
