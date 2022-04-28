---
title: 깃허브로 Spring Legacy Project를 사용한 팀 프로젝트 진행시 내 컴퓨터에서 다른 사람 작업물 확인하기
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

* 깃허브를 이용해 팀 프로젝트를 진행하는 중 다른 팀원의 작업물 확인 방법을 자주 잊어버리는 팀원들을 위해서 작성했다.

# Pull request 가이드
1. 본인 작업물 먼저 본인이 작업중인 브랜치에 커밋 & 푸시하기 (master 브랜치에 하면 안됨!!!)
2. sts에서 프로젝트 이름 위에서 마우스 우클릭
3. Team - Switch to - master 브랜치 선택
4. master 브랜치로 바꿨으면 Team - pull 선택

<p align="center"><img src="../../assets/images/pullResultInSts.png" width="600"></p>￼

* 이렇게 뜨면 pull 완료된 것임 근데 브랜치 목록에 바로 보이지는 않아서 세팅 하나 더 필요함

5. Team - Switch to - Other
￼
<p align="center"><img src="../../assets/images/chooseBranch.png" width="600"></p>

* check out... 클릭하면 선택창이 하나 뜨는데

<p align="center"><img src="../../assets/images/checkoutBranchInSts.png" width="600"></p>
￼
* 일단 빨간 박스를 선택함
* 빨간 박스 안에 있는 check out commit을 누르면 내 로컬에는 이 브랜치를 생성하지 않고 보기만 하는 것이고(읽기 전용 모드 느낌)
* 파란 버튼인 check out as new local branch를 선택하면 내 로컬에도 이 브랜치를 생성하는 것임. 이렇게 하면 이 브랜치 내용을 수정해서 커밋/푸시하는 것이 가능함(근데 merge 완료되면 삭제할 브랜치라 로컬에 굳이 생성할 필요가 없어보여서 그냥 빨간 박스 선택 추천)

6. 브랜치 변경이 완료되면 똑같이 서버 실행한 뒤 HomeController의 매핑 주소를 참고해서 주소창에 입력하면 작업한 페이지를 볼 수 있다.
7. 다 봤으면 다시 본인 브랜치로 바꿔서 계속 작업하면 됨
8. 수정했으면 하는 사항이 있으면 여기서 밑으로 쭉 내려보면 댓글 작성란이 있음. 작성하면 된다.

