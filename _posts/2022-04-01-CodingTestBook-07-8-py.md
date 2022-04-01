---
title: Python) 이것이 취업을 위한 코딩테스트다 07 실전문제 3. 떡볶이 떡 만들기
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - BOJ
tags:
    - Algorithm
    - BinarySearch
    - Python
---

# 문제 출처
<hr>
* 이것이 취업을 위한 코딩테스트다 with 파이썬
* Chapter 07 - 실전문제 3. 떡볶이 떡 만들기<br><br>
 
# 제한
<hr>
* 시간 제한 : 2 초
* 메모리 제한 : 128 MB<br><br>

# 문제
<hr>
* 오늘 동빈이는 여행 가신 부모님을 대신해서 떡집 일을 하기로 했다. 오늘은 떡볶이 떡을 만드는 날이다. 동빈이네 떡볶이 떡은 재밌게도 떡볶이 떡의 길이가 일정하지 않다. 대신에 한 봉지 안에 들어가는 떡의 총 길이는 절단기로 잘라서 맞춰준다.
* 절단기에 높이(H)를 지정하면 줄지어진 떡을 한 번에 절단한다. 높이가 H보다 긴 떡은 H 위의 부분이 잘릴 것이고, 낮은 떡은 잘리지 않는다.
* 예를 들어 높이가 19, 14, 10, 17cm인 떡이 나란히 있고 절단기 높이를 15cm로 지정하면 자른 뒤 떡의 높이는 15, 14, 10, 15cm가 될 것이다. 잘린 떡의 높이는 차례대로 4, 0, 0, 2cm이다. 손님은 6cm만큼의 길이를 가져간다.
* 손님이 왔을 때 요청한 총 길이가 M일 때 적어도 M만큼의 떡을 얻기 위해 절단기에 설정할 수 있는 높이의 최댓값을 구하는 프로그램을 작성하시오.<br><br>

# 입력
<hr>
* 첫째 줄에 떡의 개수 N과 요청한 떡의 길이 M이 주어진다. (1 <= N <= 1,000,000, 1 <= M <= 2,000,000,000)
* 둘째 줄에는 떡의 개별 높이가 주어진다. 떡 높이의 총합은 항상 M 이상이므로, 손님은 필요한 양만큼 떡을 사갈 수 있다. 높이는 10억보다 작거나 같은 양의 정수 또는 0이다.<br><br>

# 출력
<hr>
* 적어도 M만큼의 떡을 집에 가져가기 위해 절단기에 설정할 수 있는 높이의 최댓값을 출력한다.<br><br><br>

# 👀 풀이
<hr>
* 이진탐색 문제이자 파라메트릭 서치 문제라고 한다.
* 이런 유형의 문제는 풀어본 적이 없어서 책에 나와 있는 해설을 보고 풀었다.
* 자른 떡의 길이가 M과 같아질 때까지 절단기의 위치를 옮겨가면서 적절한 높이(H)를 찾는다.<br><br>

 
# 코드
<hr>

<script src="https://gist.github.com/miro7923/16e20d83fcb01090e7464e072607dbdb.js"></script>