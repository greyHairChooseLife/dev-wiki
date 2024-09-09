# 󰏢 HUB: Workflow


## 1. 큰 것들

[ Big Data Engineering](/Programing/workflows/big_data_engineering/index.md)
[ Hardware Management](/Programing/workflows/hardware-management/index)
[ Web Dev](/Programing/workflows/web_dev/index)
[ Init Setting](/Programing/workflows/project_initiator/index.md)

## 2. 작은 것들

### 2.1. web scrapping

> [!lg]웹 접근성 기반 선택자를 활용하자
>
> - 접근성 기반 선택자 사용의 실제 예시
>
>   예를 들어, 다음과 같은 HTML 코드가 있을 때:
>
>   html
>   코드 복사
>   <button aria-label="Download">Download</button>
>   이 버튼을 선택하려면 다음과 같은 선택자를 사용할 수 있습니다:
>
>   javascript
>   코드 복사
>   document.querySelector('button[aria-label="Download"]');
>   이 선택자는 aria-label 속성에 "Download"가 설정된 모든 버튼을 선택합니다. 버튼의 클래스나 ID가 변경되더라도 이 선택자는 유지될 가능성이 큽니다.
>
>   접근성 기반 선택자는 웹 스크래핑, 자동화 테스트, UI 테스트 등에서 안정적으로 사용할 수 있는 강력한 도구입니다.


### 2.2. 서버 관리

[비밀번호 없이 ssh 연결하기](/Programing/workflows/서버관리/비밀번호_없이_ssh_연결하기)
[rsync로 파일 주고받기](/Programing/workflows/서버관리/rsync로_파일_주고받기)
[nfs로 디렉토리 공유하기](/Programing/workflows/서버관리/nfs로_디렉토리_공유하기)

### 2.3. cloud storage

[rclone으로 클라우드 스토리지 서비스 동기화](/Programing/workflows/cloud_storage/rclone으로_클라우드_스토리지_서비스_동기화)


### 2.4. CI/CD

> [!cn] 개념정리
>
> 배포 자동화는 고객의 요구사항을 빠르게 반영하는 핵심 요소이다. 요구사항을 파악하고 빠르게 반영하기도 바쁜데, 배포가 매끄럽지 못해서 턱턱 막힌다면 진짜 갑갑하다.

> [!ye] single container SPA
>
> [실습 github](https://github.com/study-and-trial/static-site)
>
> - 파이프라인 구성요소
>   - dev 서버
>   - github-action runner 서버
>   - container (image)registry
>   - 배포 서버
>
> - 작업 순서
>   1. 개발 진행
>   2. github push 또는 pr merge에 따라 runner 실행(적절한 방식을 runner의 트리거로 설정한다.)
>   3. runner server(github 또는 self-hosted)에서 runner 실행:
>      1. container (image)registry 로그인
>      2. 컨테이너 이미지 빌드, 레지스트리에 push
>      3. 배포서버에 ssh 접속하여 이미지 pull, 컨테이너 재시작


## tiny

- server
  [bore](/Programing/concepts_and_tools/tools/bore.md):
    simple tcp tunnel CLI. make localhost to public server

