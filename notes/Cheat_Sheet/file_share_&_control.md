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

rsync를 사용하더라도 보안을 위해 protocol은 ssh를 사용한다.

```
rsync -az <path from> <user>@<host>:<path to>

ex)
rsync -az ./*.json sy@10.71.126.25:/home/sy/temp
```

- a: 압축
- z: 압축
