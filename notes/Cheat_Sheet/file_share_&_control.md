# file transfer


## SCP

  ```bash
  scp <object file path> <target system user>@<target system IP>:<target system path>

  ex)
  scp inno-data/test/test.sh sy@10.71.126.194:/home/sy/test/
  scp -r /home/sy/cbpromaker/미래창업연구소/inno-forest-web-scrapper sy@10.71.126.216:/home/sy/inno2
  scp -r /home/sy/cbpromaker/미래창업연구소/inno-forest-web-scrapper sy@10.71.126.201:/home/sy/inno2
  ```

> 디렉토리 자체를 옮기술도 있다.


## RSYNC


[!re] rsync를 사용하더라도 보안을 위해 protocol은 ssh를 사용한다.

#### Options

- a: 압축
- z: 압축
- r: 모든 하위 내용

#### 내보내기

  ```bash
  rsync -az <path from> <user>@<host>:<path to>

  ex)
  rsync -az ./*.json sy@10.71.126.25:/home/sy/temp
  ```

#### 가져오기

_(순서만 반대로)_

  ```bash
  rsync -az <user>@<host>:<path from> <path to>
  ```


보통은 password접근을 막아놓기 때문에 ssh key를 전달해야한다. rsync에는 직접적으로 사용할수는 없고 아래처럼 `-e` 옵션으로 rsync가 사용할 ssh명령 자체를 전달해야한다.

  ```bash
  rsync -az -e "ssh -i ~/.ssh/<SSH-키> -p <포트번호>" <user>@<host>:<path from> <path to>
  ```



## history


rsync -az ./KR*.json sy@10.71.126.200:/home/sy/Working/patent/detail-docker/inputs
rsync -az index.js sy@10.71.126.200:/home/sy/Working/patent/detail-docker
rsync -az /home/sy/cbpromaker/미래창업연구소/patent/scrappers/query_detail/src/grepData.js sy@10.71.126.200:/home/sy/Working/patent/detail-docker/src/
rsync -az /home/sy/cbpromaker/미래창업연구소/patent/scrappers/query_detail/index.js sy@10.71.126.200:/home/sy/Working/patent/detail-docker
