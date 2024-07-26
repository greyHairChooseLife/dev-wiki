# wiki for DEV

  _이 위키의 목적은 기록하여 (나중에)필요시 되찾아보기 위함이다._
  _따라서, 가급적 개조식으로 작성한다._


## TOC

  * [Concept](concepts/index)
  * [[Workflow](workflows/index)
  * [Problem Solving](problem-solving/index)
  * [Post](dev-posts/index)

## (temperal)from here to here

  _이 메뉴의 내용은 모두 바로 위에 TOC로 옮기도록 하자. (sub-directory를 구성하는 작업)_

  * [Concepts](Concepts)
  * [Tools](Tools)
  * [Projects](Projects)
  * [Architectures](Architectures)
  * [CICD](CICD)
  * [Bugs](Bugs)
  * [Devlog](Devlog)
  * [Life](Life)
  * [Tiny Posts](Tiny Posts)

## Scratch Pad

  * [(moving from...) draft](draft)
  * [(to here...) index](./draft/index)

## Cheet Sheet

  * [hardware manage](hardware manage)
  * [file share & control](file share & control)

    디렉토리 내 모든 json파일의 길이의 총 합
    `jq '. | length' *.json | awk '{s+=$1} END {print s}'`

    ```
    jq '. | length' dataset/*.json | awk '{s+=$1} END {print s}'
    jq '. | length' uniq*.json | awk '{s+=$1} END {print s}'
    jq '. | length' dataset/Bibliographic_cleaned.csv.*.json | awk '{s+=$1} END {print s}'
    jq '. | length' dataset/Abstract_cleaned.csv.*.json | awk '{s+=$1} END {print s}'
    ```
    도커 컨테이너 내부 파일 복사 호스트로 복사
    `[sy@sy-230620 ~]$ docker cp a7614153bd78:/dist/assets/index-C6b_ONX_.js prod.js`

    %% ref: https://github.com/sindrets/diffview.nvim
    DiffviewFileHistory
    : 지정한 범위의 commit histroy 리스트를 메뉴판에 넣어주고, 항목당 변경 내역을 보여준다.
    : 이때 다양한 범위를 지정할 수 있는데,
        - 특정 파일
        - 특정 범위(visual block)
        - 특정 commit(git rev)
        - 특정 범위와 범위의 범위의 차이점

    DiffviewOpen
    : 지정한 범위에서 변경된 file view를 메뉴판에 넣어준다. 무엇이 변경되었는지 파일마다 볼 수 있다.


## Reference

  * [useful links](useful-links)

TODO

  - [upgrade wiki](upgrade wiki)
  - 윈도우로 ssh 통신
  - dotfile 관리, youtube
  - arch linux 설치하기
  - 배포 방법 공부
  - command line redirection & pipe
  - linux's permission
  - _custom cli command: ffg_가 `.gitignore`까지 검색하도록 기능 추가
  - _custom cli command: ffd_의 preview가 `node_modules/`는 제외하도록 기능 업데이트
  - vimwiki에서는 tab size를 2 -> 4로 변경하기
  - read: [An Experienced (Neo)Vimmer's Workflow](https://seniormars.com/posts/neovim-workflow/)
  - nvim: ,R 커맨드(keymap)로 화면 얼어버리는 문제
      - xorg를 kill 및 -HUB하면 프로세스를 종료하지 않는다고 한다. 그런데 나는 그냥 다 종료된다.
      - 디스플레이매니저를 다시시작하는 방법도 있다고 한다. i3 wm을 말하는게 아니고, lightdm을 말한다.
        (system)service니까 systemctl restart lightdm해주자.


[bigdata with javascript](https://www.manning.com/books/data-wrangling-with-javascript?a_aid=datawranglingwithjavascript&a_bid=acc654f9)


css 참고용 사이트
https://animista.net/
https://www.youtube.com/watch?v=OWaHfpHxgb8



[learn how to project](https://github.com/boostcampwm-2022)
 and https://www.essential2189.dev/db-performance-fts
