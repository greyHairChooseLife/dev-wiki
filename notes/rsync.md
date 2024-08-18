```bash
rsync -az <path from> <user>@<host ip>:<path to>

ex)
rsync -az ./*.json sy@10.71.126.25:/home/sy/temp
```

여기서 `<user>@<host ip>` 이건 상대방 쪽 시스템의 user와 ip를 말한다.

- a: 압축
- z: 압축
- r: 모든 하위 내용
rsync -az ./*.json sy@10.71.126.200:/home/sy/Working/patent/detail-docker/output/KR/* /home/sy/cbpromaker/미래창업연구소/patent-data-processing/scrappers/detail/output/output_from_cblabtop
