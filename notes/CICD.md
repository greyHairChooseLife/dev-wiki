배포 자동화는 고객의 요구사항을 빠르게 반영하는 핵심 요소이다. 요구사항을 파악하고 빠르게 반영하기도 바쁜데,
배포가 매끄럽지 못해서 턱턱 막힌다면 진짜 갑갑하다.

## 1. single container SPA

SPA(Single Page Application)로 간단한 정적 사이트를 배포하는 일도 많다.

**파이프라인 구성요소**

- 개발 서버
- runner 서버
- container (image)registry
- 배포 서버

> 순서도
> 1. 개발 진행
> 2. github push 또는 pr merge에 따라 runner 실행(적절한 방식을 runner의 트리거로 설정한다.)
> 3. runner server(github 또는 self-hosted)에서 runner 실행
>    - container (image)registry 로그인
>    - 컨테이너 이미지 빌드, 레지스트리에 push
>    - 배포서버에 ssh 접속하여 이미지 pull, 컨테이너 재시작

"self-hosted runner"의 설정은 github 저장소 설정에서 매우 쉽게 할 수 있다. github 웹사이트의 설정창에서 관련 안내가 모두 제공된다.

github: <https://github.com/study-and-trial/static-site>

