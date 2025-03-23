# 󰏢 single container SPA



[실습 repo](https://github.com/study-and-trial/static-site)


- 파이프라인 구성요소
  - dev 서버
  - github-action runner 서버
  - container (image)registry
  - 배포 서버


- 작업 순서
  1. 개발 진행
  2. github push 또는 pr merge에 따라 runner 실행(적절한 방식을 runner의 트리거로 설정한다.)
  3. runner server(github 또는 self-hosted)에서 runner 실행:
     1. container (image)registry 로그인
     2. 컨테이너 이미지 빌드, 레지스트리에 push
     3. 배포서버에 ssh 접속하여 이미지 pull, 컨테이너 재시작
