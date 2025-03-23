# 󰏢 bash



## bash를 실행하면 어떤일이 일어나는가?

> [!rf]
>
> [양주종의 코딩스쿨1](https://www.youtube.com/watch?v=_DWiDKFEfjo)
> [양주종의 코딩스쿨2](https://www.youtube.com/watch?v=xJLJ9q_Z2eU)


1. `/etc/profile`스크립트가 실행된다.

   여기서 `/etc/profile.d/*.sh` 스크립트를 실행하도록 되어있다.

2. 유저 레벨에서도 마스터파일(`$HOME/.bash_profile`)을 실행한다.

   여기서 `$HOME/.bashrc`를 실행한다.

