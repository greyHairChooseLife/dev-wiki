# week-9: Pintos - Threads & scheduler



> [!NOTE]
>
> 팀원: 김용환 + 유선목



> [!NOTE] how to 코어타임
>
>
> - 책은 개인적으로 보면서 같이 보고싶은게 있으면 자유롭게 제안하기
>
> - 원하는것:
>
>     - 용환: 이 과정이 잘 드러나는 결과물이 만들어지면 좋겠다. 일단 생각하는것은 블로그?
>     - 선목: 추상적으로 배워온 OS의 실제 모습을 보고싶다.
>     - 상연: - 70%: OS가 소프트웨어라는데 어떻게 생겼나 보고 알고싶다.
>             - 30%: 내 코드를 남이 이해하기 쉽게하고, 남의 코드를 잘 이해해보는 연습을 하는것
>
>
> 


> [!NOTE] PR 단위는 아래처럼
>
> - Project.1
> - Project.2-3
> - Project.4



> [!rf]
> 
> [team 레포](https://github.com/tjsahr9191/KJ-Pintos-7team)
> [나](https://github.com/greyHairChooseLife/KJ-Pintos-7team)



> [!rf]
> 
> [master gitbook](https://casys-kaist.github.io/pintos-kaist/)
> [totalOS.pdf _ Youngjin Kwon](file:./static/20240928-01_os_review.pdf)


> [!rf]
>
> [pitos P1](file:./static/Pintos_1.pdf)




## GitHub 저장소 전체 아카이빙 & 복원 레퍼런스


> [!lg] Log 2025-11-07
>
> 선목님이 레포지토리 주인을 자처하셔서 나중에 이것을 통째로 옮겨올 필요가 있겠다.
> - 아카이빙 하는 좋은 경험이 될듯.
> - 잘 만들어서 공유해봐도 좋겠다.



- 코드, 브랜치, 태그 백업 및 복원

  1. 백업:  
     git clone --mirror https://github.com/원본/저장소.git
  2. 복원:  
     cd 저장소.git  
     git remote add myorigin https://github.com/내계정/저장소.git  
     git push --mirror myorigin

- Wiki 백업 및 복원

  1. Wiki는 별도 저장소임  
     git clone https://github.com/원본/저장소.wiki.git
  2. 복원:  
     cd 저장소.wiki  
     git remote add myorigin https://github.com/내계정/저장소.wiki.git  
     git push --all myorigin

- 이슈, PR, Releases, Projects 등 메타데이터 백업

  1. github-backup 설치  
     pip install github-backup
  2. 백업 실행  
     github-backup 원본계정 --repository 저장소 --token <GITHUB_TOKEN> --all
     - --all : 모든 데이터 백업 (이슈, PR, Releases, Wiki, Projects 등)
     - --output-directory <폴더> : 백업 파일 저장 위치 지정

- 이슈, PR 등 복원

  1. 공식적으로 완전 자동화된 복원 도구는 없음
  2. 이슈/PR: github-issues-import, GitHub API 등으로 수동 업로드 필요
     - 예시: https://github.com/IQAndreas/github-issues-import

- Releases 수동 복원

  1. github-backup으로 다운로드한 릴리즈 파일을 새 저장소에 수동 업로드

- 참고

  - GitHub API 문서: https://docs.github.com/en/rest
  - github-backup: https://github.com/josegonzalez/python-github-backup
  - github-issues-import: https://github.com/IQAndreas/github-issues-import


- 주의사항
  - 이슈/PR 복원 시 작성자, 날짜 등은 완벽히 동일하게 복원되지 않을 수 있음
  - Wiki, Releases 등은 별도 수동 작업 필요
  - 대규모 저장소는 API Rate Limit에 주의


## (팀)코어타임 기록


### 0차 2025-11-06 (목) 17:00


팀 레포지토리를 만들고 branch 전략 등 기본적인 내용을 논의했다. github의 wiki와 issue탭을 적극
활용하기로 했다.

또, 일단은 좀 둘러보고 익일 16시에 둘러본 결과를 공유해보기로 했다.

PR 리뷰 전략에 대해 추가 논의가 필요하다는 의견을 냈다. 누군가 PR을 올렸을 때 이것을 이해하지
못한 팀원이 있다면 반드시 함께 충분히 이해하도록 하고 진행하는것이 좋을까? 또는 싫어도 그렇게
해야하는것일까?

나는 그렇다고 생각한다. 무조건이라고 할 수는 없지만 특히나 이렇게 학습과정이라면 더욱 그렇다.
다만 최선을 다 해보려는 마음이 모두에게 있어야지만 말이다.


### 1차 2025-11-07 (금) 17:00

일단 Project.1은 3개 단위로 나누어 PR하기로 했다.

그리고 github PR과 merge에 개념에 관하여 약간의 혼동이 있었던 것 같다. 내가 막 설명을 해드려봤다.

겪어보면서 더 이해해 보기로 하였다.



## 과제 수행 기록

### Project.1


> [!lg] Log 2025-11-11 (화) 05:36
>
> 화요일 새벽이라면 월요일 밤을 새서 했다는 소리다. 비로소 나도 Project.1의 4단계중 1단계인
> alarm clock을 해결했다!
>
> 주말이 지나면 (월, 화, 수) 3일이 남는다. 마음이 좀 더 다급해진다.
> 사람들은 왜이렇게 빨리 빨리 잘 하는가~ 반면에 나는 왜 이렇게 느릿느릿 진행되는건가ㅠㅠ
> 
> 언젠가는 내가 컴퓨터를 썩 잘 한다는 생각도 했다. 꼭 컴퓨터가 아니어도 논리적인 사고랄까.
> 그러니까 대단한 정도는 아니어도, 배워나가기에 충분하고도 남는 정도의 능력은 있다고 말이다. 
> 근데 그렇지도 않은것 같다.
>
> 그래도 별 수 없다. 그렇다고 이 재밌는걸 안하기도 아쉽고... 아마도 나의 시야가 좁고 식견이
> 부족해서 그렇지, 알고보면 지금의 격차는 사실 도토리 키재기 일 것이다.
> 그래 나는 도토리다.

