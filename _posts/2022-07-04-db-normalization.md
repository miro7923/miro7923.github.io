---
title: 정보처리기사) 정규화와 반정규화
toc: true
toc_sticky: true
toc_label: 목차
published: true
categories:
    - Certificate
tags:
    - Certificate
    - normalization
    - denormalization
    - study
---

* 최근 미뤄뒀던 정보처리기사 실기를 준비하며... 꼭 외워야 하는 내용 정리 🥲
 
# 정규화(Normalization)
* 중복을 최소화하게 데이터를 구조화하는 작업
* 데이터베이스 이상현상의 원인이 되는 데이터 중복성을 제거하여 데이터의 무결성을 보존하는 기법 
* 1NF, 2NF, 3NF, BCNF, 4NF, 5NF로 분류한다. <br><br>

|유형|설명|
|---|---|
|제1정규화(1NF)|릴레이션 R의 모든 속성값이 원자값을 가지는 릴레이션|
|제2정규화(2NF)|릴레이션 R이 제1정규형이고 기본키가 아닌 속성이 기본키에 완전 함수 종속일 때|
|제3정규화(3NF)|릴레이션 R이 제2정규형이고 기본키가 아닌 속성이 기본키에 비이행적 non-transitive으로 종속할 때(직접 종속)|
|보이스/코드 정규화(BCNF)|릴레이션 R에서 함수 종속성 X -> Y가 성립할 때 모든 결정자 X가 후보키 일 때|
|제4정규화(4NF)|릴레이션 R에서 MVD A ->> B가 존재할 때 R의 모든 속성들이 A에 함수종속(FD)이면 R은 4NF(즉 R의 모든 속성 X에 대해 A -> X이고 A가 후보키)|
|제5정규화(5NF)|릴레이션 R에 존재하는 모든 조인 종속(Join Dependency)이 R의 후보키를 통해 성립되면, R은 5NF|
<br><br>

# 비정규화(Denormalization)
* 정규화된 데이터 모델을 통합, 중복, 분리하는 과정으로, 성능개선을 위하여 의도적으로 정규화 원칙을 위배하는 행위
