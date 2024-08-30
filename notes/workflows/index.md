# 󰏢 HUB: Workflow


## TOC

[HUB: Big Data Engineering](/workflows/big_data_engineering/index.md)
[HUB: Hardware Management](/workflows/hardware-management/index)
[HUB: Web Dev](/workflows/web_dev/index)

[HUB: project_initiator](/workflows/project_initiator/index.md)

## web scrapping

> [!lg] 웹 접근성 기반 선택자를 활용하자
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







## 서버 관리

[비밀번호 없이 ssh 연결하기](/workflows/서버관리/비밀번호_없이_ssh_연결하기)
[rsync로 파일 주고받기](/workflows/서버관리/rsync로_파일_주고받기)
[nfs로 디렉토리 공유하기](/workflows/서버관리/nfs로_디렉토리_공유하기)

## cloud storage

[rclone으로 클라우드 스토리지 서비스 동기화](/workflows/cloud_storage/rclone으로_클라우드_스토리지_서비스_동기화)


## shell scripting

[shellcheck](/workflows/shell_scripting/shellcheck.md): 쉘 스크립트 에러 체크
  [-] 근데 shell script는 LSP가 없나? 있을것같은데...한번 대조해보자.


## tiny

/workflows/tiny/bor
- server
  [bore](/concepts_and_tools/tools/bore.md):
    simple tcp tunnel CLI. make localhost to public server

