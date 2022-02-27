---
title: JSP) 커넥션 풀
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - JSP
tags:
    - JSP
    - ConnectionPool
---
# 👀 커넥션 풀 (Connection Pool)이란?
* `JDBC`를 연동하기 위해서는 드라이버를 로드하고 `JDBC URL`로 접속하여 `Connection` 객체를 얻어오는 단계를 거쳐야 한다. 
* 커넥션 풀은 데이터베이스와 연결된 `Connection` 객체를 미리 생성하여 풀(Pool)에 저장해 두고 필요할 때마다 풀에 접근하여 `Connection` 객체를 사용하고 사용이 끝나면 다시 반환하는 것을 말한다.
* 사용자가 웹 사이트에 `Connection` 객체를 생성하게 되면 메모리 소모가 많고 시간도 오래 걸린다.
* 하지만 풀에 저장해서 사용한다면 미리 생성된 객체를 쓰기 때문에 생성에 시간이 걸리지도 않고 또 쓰지 않는 객체는 다시 풀 속에 넣어뒀다가 필요할 때 꺼내 쓰면 되기 때문에 불필요한 메모리 낭비가 없어 프로그램 효율과 성능이 전체적으로 증가하게 된다.

## 커넥션풀의 연결전략
1) DB연결이 필요한 JSP 페이지 (service()) 실행시 DB연결 요청당 1개씩 할당<br>
2) 커넥션의 개수를 제한<br>
3) 커넥션의 사용을 다 한 경우 (객체관리자가 자원을 모두 다 쓰면) 자원 회수<br>

## 실제 동작 구현
1. 웹브라우저 요청<br>
2. 할당될 커넥션 객체가 있는지 없는지 체크<br><br>

2-1. 있을 때<br>
    3. 커넥션 객체 할당 (pool에 저장된 정보 전달)<br>
    4. 객체 사용<br>
    5. 객체 사용 반환 (자원해제 X, pool에 저장)<br>
    
2-2. 없을 때<br>
    1. 커넥션 객체를 기다림 (커넥션이 반환될 때 까지)<br>
    2. 임시 커넥션 객체 생성 -> 사용 -> 반환 (사라짐)<br><br><br>

# JNDI (Java Namming and Directory Interface)
* 명명 서비스 및 디렉토리 서비스에 접근하기 위한 API. 즉 특정 자원에 접근하기 위한 이름으로 사용된다.
* [아파치 홈페이지](https://commons.apache.org/)에 가서 `collections`, `DBCP`, `Pool` 다운받기 
	* 압축 풀어서 `commons-collections4-4.4.jar` 처럼 옆에 잡다한 이름 안 붙은걸로 복사해서 `WEB-INF/lib`에 붙여넣기
* `API`를 설치했으면 `xml` 파일을 만들어야 한다.

## XML
* 태그 형태로 데이터를 저장하는 페이지로 `HTML` 태그 형태는 아니지만 지정된 태그를 통해서 데이터를 저장하고 사용한다.
* `src/main/webapp/META-INF`에 `content.xml` 파일을 생성하고 서버에 공유할 리소스를 정의한다.

```xml
<?xml version="1.0" encoding="UTF-8"?>

<Context>
  <Resource 
    name="jdbc/mysql" 
    auth="Container" 
    type="javax.sql.DataSource" 
    username="root" 
    password="1234" 
    driverClassName="com.mysql.cj.jdbc.Driver" 
    url="jdbc:mysql://localhost:3306/jspdb" 
    maxActive="500"
  />
</Context>
```

* 이렇게 작성하면 되는데 각 코드 라인의 의미는 다음과 같다.

```xml
<Context>
  <!-- Context : 프로젝트 -->
  <Resource 
    name="디비에 접근하기 위한 이름" 
    auth="컨테이너 자원 관리자 설정 - Application or Container" 
    type="리소스를 사용할 때 실제로 사용되는 클래스 타입" 
    username="디비 아이디" 
    password="디비 비밀번호" 
    driverClassName="드라이버 주소" 
    url="디비 연결 주소" 
    maxActive="커넥션 회수 대기시간"
  />
</Context>
```

* `xml` 파일에서도 주석문 사용이 가능하지만 사용하지 않는 것이 좋다. 
* 왜냐면 주석 처리한 코드가 실행되는 경우가 있는데 이 때 에러가 나도 컴파일 에러가 표시되지 않고 단순 서버 에러라고만 나오기 때문에 원인 찾기가 매우매우 힘들다.
* `xml` 파일까지 만들었다면 `MemberDAO` 클래스를 커넥션 풀을 사용하도록 수정해야 한다.<br><br><br>

# MemberDAO 클래스를 커넥션 풀을 쓰도록 수정하기
* `getConnect()` 함수를 아래와 같이 수정한다.

```java
private Connection getConnect()
{
    try 
    {
        // 커넥션풀을 사용한 디비연결
        
        // 업캐스팅이라 인터페이스인데 객체 생성 가능
        // 프로젝트(CTX)정보 초기화
        Context initCTX = new InitialContext();
        
//	Context envCTX = (Context) initCTX.lookup("java:comp/env");
//				envCTX.lookup("jdbc/mysql"); // 이거랑 아래 한 문장이랑 같은 의미
        // java:comp/env/ 여기까지는 항상 고정 & 뒤에 xml 파일의 name에 쓴 내용 넣기
        // type="javax.sql.DataSource" 에서 정해준 데이터타입으로 다운캐스팅 해줘야 함
        DataSource ds = (DataSource) initCTX.lookup("java:comp/env/jdbc/mysql");
        
        // ds에 연결정보가 다 들어있으니까 getConnection만 실행
        con = ds.getConnection();
        System.out.println("DAO : 커넥션풀을 사용한 디비연결 성공");
        System.out.println("DAO : " + con);
    } 
    catch (NamingException e) 
    {
        e.printStackTrace();
    } 
    catch (SQLException e) 
    {
        e.printStackTrace();
    }
    
    return con;
}
```

* 이후 사용하는 것은 기존과 같이 사용하면 된다.
