---
title: 'about systemd'
date: 2024-04-16
update: 2024-04-23
---

처음 systemd를 접한건 mysql을 설치하고 실행하는 과정이었다. 그땐 도커도 몰랐는데, 그래서 이걸 실행하려면
systemctl로 켜줘야했다.

켜고 끄고 반복하고, 다른 어플리케이션들도 같은 방식인게 많았다. 명령이나 status 포맷같은것도 나름 패턴이
보이기 시작했다. 그래서 공부를 따로 안해도 막 불편한건 몰랐는데...

[sysz](/Tools/sysadmin/sysz)라는 관리도구를 사용하기 시작한 지금부터는 좀 알고 써야겠다는 생각이 들었다.

## 1. What is "systemd"

`init`은 부팅 후 가장 먼저 실행되는 PID 1번 프로스세스였는데, 이것을 대체한 것이 `systemd`다. 그리고 다른
기능들도 대체했다.

> system and service manager, usually not invoked directly by the user
>
> _ref. `man systemd`_

<div class="bs-callout bs-callout-info">
  <h4>init의 세대교체</h4>
`init`은 쉘 스크립트로 작성된 파일을 실행하는 방식이었는데, `systemd`는 이를 `unit`라 부르는 요소로 바꿔
속도와 가독성을 개선했다.
</div>

## 2. Types of "unit"

- systemd는 unit을 이용해 시스템 부팅 및 유지를 관리
- unit은 11개 타입
- 관리 대상들을 `<service name>.<unit type>`으로 지칭, 동일한 naming을 따르는 설정 파일을 사용

  |   unit    | 담당 기능                                                               |
  | :-------: | :---------------------------------------------------------------------- |
  |  service  | daemons                                                                 |
  |  socket   | local IPC, network sockets                                              |
  |  target   | to group units                                                          |
  |  device   | kernel devices & device-based activation                                |
  |   mount   | control mount points in the file system                                 |
  | automount | automount capabilities                                                  |
  |   timer   | triggering activation of other units based on timers                    |
  |   swap    | memory swap partitions                                                  |
  |   path    | activate other services when file system objects change or are modified |
  |   slice   | group units which manage system processes for resource management       |
  |   scope   | similar to service units, but manage foreign processes                  |

실사용시 주로 serives를 다루게 되겠지만, 이 밖에도 다양한 종류의 unit이 있다는것을 알아두자.

## 3. Where are unit files?

아래의 다양한 위치에 있다. 각각이 상이한 목적을 가진다.

**특히 1>2>3 번호순으로 높은 priority를 가진다.** unit file은 말하자면 설정파일이기 때문에 덮어 씌워진다는
뜻이다.

1. `/etc/systemd/system/` : 가장 자주 방문할 위치.

2. `/run/systemd/system/` : systemd가 전체 시스템을 관리하는 와중에 생기는 임시파일 따위를 저장하는 곳.
   사람이 관여할 일이 없다고 보면 된다.

3. `/lib/systemd/system/` : application이 설치될 때 필요하면 여기에 만들어진다. 낮은 우선순위를 갖는건
   당연하다. 내가 시스템의 주인이니까.

## 4. How is "unit file" look alike?

우리는 기계가 아니니까, 모든 종류의 unit을 다룰수는 없다. 다만 대략 이렇게 생겼다는것을 살펴보자.

예시로 apatch web server를 설치하면 httpd.service 유닛이 생성된 것을 볼 수 있을 것이다.

열어보면 3개의 section이 있다. 각각에는 필수/비필수 field가 있다. 이것들은 배포판마다 다르기도 하다.

```bash
# systemctl cat <unit name>.<type>

[Unit]
Description=Apache Web Server
After=network.target remote-fs.target nss-lookup.target # prerequisite before this unit

[Service]
Type=simple # unit의 타입. 모두 알 필요 없다.
ExecStart=/usr/bin/httpd -k start -DFOREGROUND # systemctl 따위로 start 명령시 수행할 것
ExecStop=/usr/bin/httpd -k graceful-stop
ExecReload=/usr/bin/httpd -k graceful
PrivateTmp=true
LimitNOFILE=infinity
KillMode=mixed

[Install]
WantedBy=multi-user.target # run level과 관련
```

### 4.1 run level => target

unit 파일의 하단에 "[Install].WantedBy" 속성에 multi-user.target은 무엇인가?

Unix 계열과 Linux는 부팅시에 시스템이 어떤 수준으로 기능을 제공할지 정하는 run level이라는 개념이 있었다.
근데 이게 systemd 도입 이후에 run level은 target으로 용어가 변경되었다.

<center>**compare "run level" & "target"**</center>

| run level |      target       | 설명                              |
| :-------: | :---------------: | :-------------------------------- |
|     0     |  poweroff.target  | 시스템 종료                       |
|     1     |   rescue.target   | 단일 사용자 모드                  |
|     2     | multi-user.target | 사용되지 않음(사용자가 지정 가능) |
|     3     | multi-user.target | 텍스트 기반의 다중 사용자 모드    |
|     4     | multi-user.target | 사용되지 않음(사용자가 지정 가능) |
|     5     | graphical.target  | 그래픽 모드                       |
|     6     |   reboot.target   | 시스템 재부팅                     |

비교표를 보면 알겠지만 맨날만날 붙잡고 있는 컴퓨터는 5번, graphical.target에 해당하겠다. 텍스트 기반이라면
그보다 낮은 숫자인데, 예를들어 서버라면 보통 multi-user.target이겠다.

### 4.2 usful commands

- `systemctl get-default`

  명령어로 기본 타겟을 확인할 수 있다.

- `systemctl list-dependencies <unit name>.<type>`

  unit의 의존성을 확인할 수 있다. 보통은 type=target을 보게 된다.

- `systemctl list-units [--type=<type of unit>]`

  unit type별로 메모리에 로드된 것을 확인할 수 있다. active, inactive, failed 등으로 나뉜다.

- `systemctl list-unit-files [--type=<type of unit>]`

  메모리에 load되지 않은것도 확인 할 수 있다. unit의 구성을 관리할 때 유용

참고로 수 많은 유닛은 유기적으로 의존하고 있다. 그래서 `cat` 명령으로 unit 파일을 열어보는 것과는 다르게,
`show` 명령으로 현황을 살펴보면 확인할 수 있는 정보가 훨씬 많다.

이런걸 다 꿰고 있어야 하진 않지만, 어떤 서비스에 문제가 생겼다면 무엇에 의존하고 있는지 파악할 줄
알아야겠다.

## 5. How to edit unit file?

### 5.1 edit

`systemctl edit <service>` path는 굳이 줄 필요 없다.

unit 파일의 문법에 맞게 override 할 부분만 작성해주면, `/etc/systemd/system/<service>.d/override.conf`처럼
최상위 파일이 생성된다.

> `--full` flag를 추가하면 완전히 대체할 파일을 작성할 수 있다.(원본 복사해서 시작)
>
> 패키지 업데이트 등에도 변함없이 나만의 설정을 유지해야한다면 이것이 좋은 대안이 될 것이다.

### 5.2 delete

변경 내용을 되돌리는것도 간단하다. 이 파일을 삭제하면 된다.

### 5.3 reload

`systemctl daemon-reload [ <service> ]`

물론 실행중인 서비스가 종료되지는 않는다. 단지 unit 파일에 변경사항이 있는지 확인하고 load하는것 뿐이다.

> 그러나 돌다리도 두들겨 보고 건너자.

## \*QnA

- **Loaded, Active**가 나뉘어진 이유는 뭐야?

> **Loaded**: systemd가 해당 서비스의 unit 파일을 읽고 파싱해서 메모리에 로드했다는 뜻.

- **Loaded**의 괄호 내부에 표현되는 "상태"들의 종류

아래의 요소들 외에도 어쩌구 저쩌구 많다만, 일단 이거라도 한번 읽어보자.

> **enabled**: 자동시작 O **disabled**: 자동시작 X

> **static**: 기타 유닛에 의해서 관리되는 서비스. 그러니 (이름처럼)자동시작 걸고 자시고 필요가 없다. 때가
> 되면 분연히 떨치고 일어난다.

> **transient**: 기타 유닛이 실행될 때(runtime) 필요해서 생겨나고 또 사라지는 놈. 위에서 공부한
> `/run/systemd/system/`에 unit 파일이 생기고 삭제 된다.
>
> 내 시스템을 조회해보니 docker container들과 kubernetes pod들이 이런 상태를 가진다. 헌데
> `/run/systemd/system/`에는 없다. 모든 서비스의 unit 파일이 해당 위치에서 보이는건 아니란 뜻이다.

> **generated**: systemd가 직접 관리하는 요소는 아니지만(_unit 파일이 없다._), 기타 시스템 구성요소로부터
> service로 실행될 놈들이다.
>
> 역시나 `/run/systemd/system/`에 unit 파일이 임시로 생성되고 삭제된다.
>
> 내 시스템을 살펴보면 몇가지 mount point가 이 상태를 가지고, 이외에는 autostart 되는 실행파일들이다.

> **preset**: package manager가 app을 설치할 때 설정된 내용을 기록한 것. 역시 리눅스!!

- init을 대체한다는데, 그럼 최초에 실행될 service 또는 service list는?

> `systemctl get-default` 로 확인할 수 있다.

- `<unit name>.service`가 아니라 `<unit name>@.service`의 정체는 뭐야?

  > 템플릿 유닛이다. 비슷한 설정의 서비스를 여러개 만들어야 할 때 사용한다. 생성된 instance는
  > <service name>@<instance name>.<type>와 같은 모습니다.

## \*reference

- youtube: [[따라學 IT] 04. systemd - 이론](https://www.youtube.com/watch?v=vg8bcRlNXB8&t=678s)
- youtube:
  [Systemd Deep-Dive: A Complete, Easy to Understand Guide for Everyone](https://www.youtube.com/watch?v=Kzpm-rGAXos)
- `man systemd`
