# 󰏢 SSH protocol




## 서버 측

_: 접속 대상, 목적지_

- sshd(SSH daemon)가 실행되고 있어야 한다.

  ```bash
  sudo systemctl status sshd
  ```

- configuraion 파일은 `/etc/ssh/sshd_config`에 있다.

  > [!ye] 설정 변경 후 서비스 재시작 필요

  - 비밀번호 인증 사용 여부
    : `PasswordAuthentication <yes/no>`
  - 기본포트번호 변경
    : `Port <번호>`


## 클라이언트 측

_: 접속 요청지, 출발지_

- ssh-client가 필요하다.

  ```bash
  # 설치 확인
  ssh -V

  # 필요시 설치
  pacman -S openssh
  ```


## ssh 접속

```bash
ssh -i <사용할_키> -p <포트번호> <user>@<host>
```



## tunneling

```bash
ssh -t -i ~/.ssh/my-key -p 22 -L 8080:localhost:80 me@123.123.123.123
```

보통 80번 포트는 기본적으로 막혀있다. 8080 등 다른걸로 뚫어서 사용하고, 필요시 로컬 시스템 내에서
포트포워딩 하자.


### local system port_forwarding & port_redirection

- Check current setup:
  ```bash
  sudo iptables -t nat -L -n -v
  ```



- Add a Rule to the `OUTPUT` Chain:
  ```bash
  sudo iptables -t nat -A OUTPUT -p tcp --dport 80 -j REDIRECT --to-port 8080
  ```
  - This will redirect traffic **originating from your local machine** to port 8080.



- Remove the rule which is added to the OUTPUT chain:
  ```bash
  sudo iptables -t nat -D OUTPUT -p tcp --dport 80 -j REDIRECT --to-port 8080
  ```



## 비밀번호 없이 ssh 연결하기

> The process:
> 
> 1. **Client**: Generate SSH key pair (`ssh-keygen`)
> 2. **Client → Server**: Transfer the public key to the server
> 3. **Server**: Add the client's public key to `~/.ssh/authorized_keys`

접속하려는 서버측에 ~/.ssh/authorized_keys 파일에 클라이언트 측의 공개키를 등록하면 비밀번호 없이 ssh 접속이 가능하다.

1. 클라이언트에서 `ssh-keygen -t rsa`로 ssh 키를 생성한다.
   기본 세팅 그대로 해도 좋다.

2. 생성된 `.pub` 퍼블릭 키를 서버의  `~/.ssh/authorized_keys`로 등록한다.

   또는 `ssh-copy-id` 명령을 이용해 client측에서 서버의 `~/.ssh/authorized_keys`에 등록 해준다.

   ```bash
   ssh-copy-id -i ~/.ssh/cbpm_desktop.pub <user>@<host>
   ```

> [!re] 특이사항
>
> 클라이언트에서 기본키(`id_rsa 따위`) 외에 다른 키를 (ssh)서버측에 authorized_keys로 등록했다면, ssh 접속 시에 `-i` 옵션을 사용하여 해당 키를 지정해주어야 한다.


## ISP, 공유기 보안 등 설정

1. 공유기 관리자 페이지 접속
   : 이것은 기본 게이트웨이에 접속하면 된다. `ip route | grep default` 명령어로 확인할 수 있다.
2. 고정 ip 설정
3. 포트포워딩 설정
   : 외부 내부 포트를 모두 22 등 적당히 설정해주고, 내부 ip주소를 입력해준다.
   이때 내부 ip주소는 접속 타겟이 되는 ssh서버의 내부 ip주소를 말한다. 이것은 당연히 해당 공유기에서 내부 네트워크로 뿌려주는것이다.

> [!rf]
>
> [참고](https://happymkh.tistory.com/2)
> - 위 블로그 내용의 일부만 수행해도 된다.

