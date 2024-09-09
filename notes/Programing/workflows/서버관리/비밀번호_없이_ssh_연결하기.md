# 󰏢 비밀번호 없이 ssh 연결하기


접속하려는 서버측에 ~/.ssh/authorized_keys 파일에 클라이언트 측의 공개키를 등록하면 비밀번호 없이 ssh 접속이 가능하다.

1. 클라이언트에서 `ssh-keygen -t rsa`로 ssh 키를 생성한다.
   기본 세팅 그대로 해도 좋다.

2. 생성된 `.pub` 퍼블릭 키를 서버의 auth key로 등록한다.
   직접 갖다 복붙해도 되지만, `ssh-copy-id` 명령어를 사용하면 편리하다.

   ```bash
   ssh-copy-id -i ~/.ssh/id_rsa.pub <user>@<host>
   ```


> [!re] 특이사항
>
> 클라이언트에서 기본키(`id_rsa 따위`) 외에 다른 키를 (ssh)서버측에 authorized_keys로 등록했다면, ssh 접속 시에 `-i` 옵션을 사용하여 해당 키를 지정해주어야 한다.
