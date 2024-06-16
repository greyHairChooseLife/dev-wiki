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
