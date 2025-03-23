# 󰏢 file share



## magic-wormhole

_양쪽 시스템에 모두 설치해야하는 것 외엔, 간편하고 훨씬 좋은듯?_

[repo](https://github.com/magic-wormhole/magic-wormhole)

- 보내기

  ```bash
  wormhole send <file path>

  # 출력 예시
  # [me@my-computer]$ wormhole send ~/file.txt
  # Sending 365.4 kB file named 'file.txt'
  # Wormhole code is: 1-retraction-sentence
  # On the other computer, please run:
  #
  # wormhole receive 1-retraction-sentence
  ```

- 받기

  ```bash
  wormhole receive <code>
  ```


## deprecated


### SCP

- ```bash
  scp <object file path> <target system user>@<target system IP>:<target system path>

  ex)
  scp inno-data/test/test.sh sy@10.71.126.194:/home/sy/test/
  scp -r /home/sy/cbpromaker/미래창업연구소/inno-forest-web-scrapper sy@10.71.126.216:/home/sy/inno2
  scp -r /home/sy/cbpromaker/미래창업연구소/inno-forest-web-scrapper sy@10.71.126.201:/home/sy/inno2
  ```

> 디렉토리 자체를 옮기술도 있다.


### RSYNC


[!re] rsync를 사용하더라도 보안을 위해 protocol은 ssh를 사용한다.

#### Options

- a: 압축
- z: 압축
- r: 모든 하위 내용

#### 내보내기

- ```bash
  rsync -az <path from> <user>@<host>:<path to>

  ex)
  rsync -az ./*.json sy@10.71.126.25:/home/sy/temp
  ```

#### 가져오기

_(순서만 반대로)_

- ```bash
  rsync -az <user>@<host>:<path from> <path to>
  ```


보통은 password접근을 막아놓기 때문에 ssh key를 전달해야한다. rsync에는 직접적으로 사용할수는 없고 아래처럼 `-e` 옵션으로 rsync가 사용할 ssh명령 자체를 전달해야한다.

- ```bash
  rsync -az -e "ssh -i ~/.ssh/<SSH-키> -p <포트번호>" <user>@<host>:<path from> <path to>
  ```
