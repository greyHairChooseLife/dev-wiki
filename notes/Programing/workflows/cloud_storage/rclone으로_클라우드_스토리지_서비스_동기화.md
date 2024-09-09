# 󰏢 rclone으로 클라우드 스토리지 서비스 동기화

[best reference](https://www.carc.usc.edu/user-information/user-guides/data-management/transferring-files-rclone)

## 자주 사용하는 명령어

- 등록된 remotes 리스트

  `rclone listremotes`

- <remote>의 <path> 파일리스트

  `rclone ls <remote>:<path>`

- 걍 tree랑 똑같다.

  `rclone tree`

- copy는 이름이 중복이면 건너뛴다.

  `rclone copy <source path> <remote>:<dest path>`

- 중복이면 덮어쓴다. remote만 modifeid 되는것.

  `rclone sync <src path> <remote>:<dest path>`


## 유용한 flag

- 진행률 표시

  `--progress`
