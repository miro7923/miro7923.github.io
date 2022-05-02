---
title: JAVA Spring) 1인가구를 위한 쇼핑몰 Uno más 개발일지 9 - 카테고리별로 해당되는 상품 목록 출력하기
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Uno mas
tags:
    - Project
    - UnoMas
    - Log
---

* 작성일 : 2022.05.03
* 작성자 : 황유진

* 팀원 : 김진영, 박승지, 반현빈, 오성은, 오은현, 윤정환, 황유진
* 팀장 : 황유진
* 부팀장 : 오성은
* GitHub Repository : [https://github.com/miro7923/Uno-Mas](https://github.com/miro7923/Uno-Mas)<br><br><br>

# 개발환경
* MacBook Air (M1, 2020)
* OpenJDK 8
* Spring Tool Suite 4.14.0
* Spring framework 4.3.1.RELEASE
* Tomcat 8.5
* MySQL Workbench 8.0.19<br><br><br>

# 기간
* 2022.4.13 ~ 2022.5.20<br><br><br>

# 주제
* 웹 백엔드 수업 중 마지막 과제로 팀 프로젝트를 진행하게 되었다.
* 조건은 `Spring` 기반으로 웹 사이트를 제작하는 것이다.
* 총 팀원은 7명이며, 우리 팀은 `1인 가구를 위한 쇼핑몰`을 주제로 정했다.
* 팀 이름으로 정해진 `Uno más`는 스페인어로 `하나 더`라는 뜻이다. <br><br><br>

# 진행상황 
* 상품 목록페이지를 카테고리별로 상품을 분류해 보여줄 수 있도록 만들었다.

## ProductDAO.java

```java
package com.april.unomas.persistence;

import java.util.List;

import com.april.unomas.domain.ProductVO;

public interface ProductDAO {

    // 상품 상위 카테고리 이름 가져오기
    public String getTopCateName(int topcate_num) throws Exception;
}
```

* 먼저 `DAO` 객체를 만드는데 결합도를 낮추기 위해 인터페이스를 만든 다음에 이를 구현하는 클래스를 만들었다.

## ProductDAOImpl.java

```java
package com.april.unomas.persistence;

import java.util.List;

import javax.inject.Inject;

import org.apache.ibatis.session.SqlSession;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Repository;

import com.april.unomas.domain.ProductVO;

@Repository
public class ProductDAOImpl implements ProductDAO {

    @Inject
    private SqlSession sqlSession;
    private static String NAMESPACE = "com.unomas.mapper.ProductMapper";
    private static final Logger log = LoggerFactory.getLogger(ProductDAOImpl.class);
	
    @Override
    public String getTopCateName(int topcate_num) throws Exception {
        return sqlSession.selectOne(NAMESPACE + ".getTopCateName", topcate_num);
	}
}
```

* 아까 만든 인터페이스를 구현하는 클래스에서 세부 동작을 구현한다.
* `SqlSession` 생성 테스트는 저번 포스트에서 진행한 결과 성공적이었기 때문에 이를 이용해 DB에 접근하는 동작을 구현한다.
* 먼저 상품 목록 페이지의 상단에 출력할 대분류 이름을 테이블에서 가져온다.

## ProductMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  
<mapper namespace="com.unomas.mapper.ProductMapper">
    <!-- 상품 상위 카테고리 이름 가져오기 -->
    <select id="getTopCateName" resultType="String">
        SELECT topcate_name 
        FROM top_category  
        WHERE topcate_num = #{topcate_num}
    </select>
</mapper>
```

* 매퍼로 이동해서 `SQL` 쿼리문을 작성한다.

## ProductDAOTest.java - 실제 구현 전에 DAO가 제대로 동작하는 지 테스트!

```java
package com.april.unomas;

import javax.inject.Inject;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import com.april.unomas.persistence.ProductDAO;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(
        locations = {"file:src/main/webapp/WEB-INF/spring/root-context.xml"}
        )
public class ProductDAOTest {

    @Inject
    private ProductDAO dao;
    private static final Logger log = LoggerFactory.getLogger(ProductDAOTest.class);
	
    @Test
    public void DAO생성테스트() {
        log.info("dao : " + dao.toString());
    }
    
    @Test
    public void 상위카테고리출력테스트() throws Exception {
        log.info(dao.getTopCateName(1));
    }
}
```

* 테스트 클래스를 생성해서 아까 만든 `DAO`의 동작여부를 테스트한다.
* 결과는 성공적!

## ProductService.java

```java
package com.april.unomas.service;

import java.util.List;

import com.april.unomas.domain.ProductVO;

public interface ProductService {

    // 상위 카테고리 이름 가져오는 메서드
    public String getTopCateName(int topcate_num) throws Exception;
}
```

* 컨트롤러와 DBMS의 결합도를 낮춰줄 서비스 계층을 만든다. 이것 또한 인터페이스를 만든 다음 구현하는 클래스를 만든다.

## ProductServiceImpl.java

```java
package com.april.unomas.service;

import java.util.List;

import javax.inject.Inject;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Service;

import com.april.unomas.domain.ProductVO;
import com.april.unomas.persistence.ProductDAO;

@Service
public class ProductServiceImpl implements ProductService {

    @Inject
    private ProductDAO dao;
	
    private static final Logger log = LoggerFactory.getLogger(ProductServiceImpl.class);

    @Override
    public String getTopCateName(int topcate_num) throws Exception {
        return dao.getTopCateName(topcate_num);
    }
}
```

* 아까 `DAO`에서 만들었던 메서드를 호출해 결과값을 리턴한다.

## ProductServiceTest.java

```java
package com.april.unomas;

import javax.inject.Inject;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import com.april.unomas.service.ProductService;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(
        locations = {"file:src/main/webapp/WEB-INF/spring/root-context.xml"}
        )
public class ProductServiceTest {

    @Inject
    private ProductService service;
	
    private static final Logger log = LoggerFactory.getLogger(ProductServiceTest.class);
	
    @Test
    public void 상품상위카테고리이름() throws Exception {
        log.info(service.getTopCateName(2));
    }
}
```

* 서비스 클래스 또한 테스트를 진행했다. 동작 잘 됨!

## ProductController.java

* DB에 접근하는 동작의 테스트가 끝났으니까 이제 컨트롤러에서 뷰 페이지로 연결시켜 준다.

```java
package com.april.unomas;

import javax.inject.Inject;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;

import com.april.unomas.service.ProductService;


@Controller
public class ProductController {

    @Inject
    private ProductService service;
	
    private static final Logger log = LoggerFactory.getLogger(ProductController.class);
	
    @RequestMapping(value = "/product_list", method = RequestMethod.GET) // /shop -> /product_list
    public String shopGET(@RequestParam("topcate_num") int topcate_num, Model model) throws Exception {
        // 해당 카테고리의 상품 전체 목록 
        model.addAttribute("productList", service.getProductList());
        
        // 대분류 이름
        model.addAttribute("topcate", service.getTopCateName(topcate_num));
        
        // 소분류 이름 리스트
        model.addAttribute("dcateList", service.getDcateNames(topcate_num));
		
        return "product/productList";
    }
}
```

* DB에서 가져온 정보를 뷰 페이지에 출력하기 위해 컨트롤러에서 이동 전에 `Model` 객체에 저장한다.
* 해당 카테고리의 전체 상품을 가져오는 메서드와 소분류 이름 리스트를 가져오는 메서드도 만들었는데 위에서 작성한 것과 같은 과정을 거쳐 만들었기 때문에 생략했다. (글이 너무 길어져서...)
* `topcate_num` 파라미터값으로 대분류를 불러온 다음 그에 해당하는 소분류와 상품들을 출력할 것이다.

## ProductList.jsp

```jsp
<div class="categoryBox">
    <h3 class="title">${topcate }</h3>
    <ul class="categoryList">
        <li><a class="category" id="category0" 
            onclick="changeSort(0, ${fn:length(dcateList) });"> 전체보기</a>
        <c:forEach var="cate" items="${dcateList }" varStatus="it">
            <li><a class="category" id="category${it.index + 1 }"
                onclick="changeSort(${it.index + 1 }, ${fn:length(dcateList) });"> ${cate }</a></li>
        </c:forEach>
    </ul>
</div>
```

* 뷰 페이지에서는 `EL` 표현식을 사용해 출력한다.

<p align="center"><img src="../../assets/images/unomas_prodListAddDb.png"></p>

* 그러면 이제 손으로 일일이 타이핑하지 않아도 DB 정보에 맞춰 출력된다! 뿌-듯 😄
* 이제 다음에 해야할 것은 헤더에 있는 메뉴에서 상품 목록 페이지를 호출했을 때 각 대분류별로 보여지게 하는 것과 상품 하나를 클릭하면 상세 페이지로 연결되는 것을 구현하는 것이다.<br><br><br>

# 마감까지
* `D-17`
