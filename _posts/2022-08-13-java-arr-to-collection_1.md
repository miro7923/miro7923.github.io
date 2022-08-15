---
title: Java) int[] 배열을 List로 간편하게 변경하기
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Java
tags:
    - Java
    - Queue
---

* 알고리즘 문제를 풀다 보면 입력으로 주어지는 기본형 배열을 컬렉션 배열로 바꿔서 사용하는 것이 편할 때가 있다. 그럴 때 사용하기 좋은 방법들을 자꾸 까먹어서.. 두고두고 보려고 정리해 보았다.<br><br>

# 단순한 방법
* 코딩테스트에서는 외부 IDE가 사용 불가한 경우가 많은데 자바의 패키지를 모두 외우는 건 참 어렵다. 그래서 컬렉션을 써야한다 싶으면 기본적으로 util 패키지를 임포트 해 놓고 시작하면 편하다. 

* 가장 쉽게 접근할 수 있는 방법으로는 반복문을 돌려 값을 복사해주는 방법이 있다.

```java
import java.util.*;

int[] arr = {1, 2, 3, 4, 5};	// ArrayList로 바꾸고 싶은 배열
ArrayList<Integer> newArr = new ArrayList<>(); 

for (int i : arr) {
	newArr.add(i);
}
```

* 간단하지만 뭔가 코드도 길고 프로페셔널 해 보이지 않는다. 이제 막 반복문 배우는 초보가 쓴 것 같아서 좀 없어보이니까 더 좋은 방법을 찾아보자.<br><br>

# Arrays.asList()
* `Arrays.asList()` 메서드를 사용해 기본형 배열을 컬렉션 배열로 바꿀 수 있다. 

```java
import java.util.*;

String[] strArr = {"ab", "cd", "ef"};
List<String> newArr = Arrays.asList(strArr);
```

* `List` 인터페이스로 구현되어 있기 때문에 데이터타입 선언시 `List`를 적어줘야 한다.
* 하지만 이건 `String`같은 클래스에서만 쉽게 적용할 수 있고 기본형 배열에선 적용되지 않는다. 

```java
import java.util.*;

int[] arr = {1,2,3,4,5};	// ArrayList로 바꾸고 싶은 배열
List<Integer> newArr = Arrays.asList(arr); // 컴파일 에러!
```

<p align=“center”><img src=“../../assets/images/arrays-aslist.png” width=“500”></p>

* 코드를 작성해보면 위와 같은 메시지와 함께 컴파일이 되지 않는다.
* 그렇다… 여기서 `Arrays.asList()`는 컬렉션 리스트 객체 생성을 위한 오토박싱이 되지 않는다는 것을 알 수 있다. 그래서 요상한 모양으로 바뀌는 것이다. 이걸 해결하기 위해 스트림을 사용해 컬렉션 리스트로 바꿀 수 있다.<br><br>

# Stream을 사용해 컬렉션 리스트로 바꾸기
* 자바 8 이상부터는 스트림이라는 기능이 제공된다. 배열과 같은 자료구조에서 기존에는 for문을 사용해 반복작업을 하던 것을 메서드만 호출하면 for문 작성 없이 내부적으로 처리해 주는 기능이라고 보면 된다.

<p align=“center”><img src=“../../assets/images/arrays-stream-aslist.png” width=“900”></p>

* 스트림에 있는 `boxed()` 라는 메서드는 이름처럼 기본형 타입에 대한 박싱 기능을 제공한다. for문으로 배열을 순회하며 하나하나 Integer로 형변환 해 주는 코드를 작성하지 않아도 배열의 내부 요소들이 모두 형변환되는 것이다. 짱짱 

<p align=“center”><img src=“../../assets/images/arrays-stream-aslist-collect.png” width=“900”></p>

* 박싱된 데이터들을 `collect()`로 컬렉션 리스트로 바꿔주면 된다.  .만 누르면 자동완성 다 보여주는 인텔리제이 사랑해

```java
import java.util.*;
import java.util.stream.*; // 필수 임포트

int[] arr = {1, 2, 3, 4, 5};
List<Integer> newArr = Arrays.stream(arr) // 여기서 IntStream으로 바뀌고
			.boxed() // 여기서 Stream<Integer>로 바뀌고
			.collect(Collectors.toList()); // 여기서 List<Integer>가 됨
```

* 위와 같이 코드를 작성하면 `int[] => List<Integer>`로 바꿀 수 있다.
* 스트림의 변환 메서드 역시 `List` 인터페이스로 구현되어 있기 때문에 `ArrayList`가 아닌 `List`로 선언해야 한다는 것을 기억하자! (인텔리제이는 알아서 빨간줄 긋지만 웹에서는 그렇지 않으니까…)<br><br>

# 참고
* [[Java] int 배열을 List로 변환하기](https://hianna.tistory.com/552)
