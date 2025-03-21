# 󰏢 tmux




## 플러그인

_vim 생태계와 상당히 비슷하다. 오픈소스 플러그인이 공유되고, 직접 만들고 배포 할 수도 있는 것이 매우 닮았다.
TPM이란 플러그인 매니저가 별도로 있는것도 마찬가지다._


- tmux 서버 자체를 저장하고 불러오는 기능

  서버를 저장하지 않아도 기본적으로 서버의 모든 세션이 유지된다. 서버(프로세스)를 종료하지만 않는다면 말이다.
  근데 컴퓨터를 껏다 켤 때도 있으니까 프로세스의 상태를 저장할 필요가 있다.

  ```tmux
  set -g @plugin 'tmux-plugins/tmux-resurrect'
  set -g @plugin 'tmux-plugins/tmux-continuum'  # tmux-resurrect를 자동화
  ```

  > [!ye] 기본적으로는 last saved envrionment를 불러오는데symlink를 수정해서 바꿔줄수도 있다.
  >
  > [ref](https://github.com/tmux-plugins/tmux-resurrect/blob/master/docs/restoring_previously_saved_environment.md)
  >
  > - make sure you start this with a "fresh" tmux instance
  >   `$ cd ~/.tmux/resurrect/`
  > - locate the save file you'd like to use for restore (file names have a timestamp)
  > - symlink the last file to the desired save file: $ ln -sf <file_name> last
  >   _`-f` 옵션을 사용하  기존 last에 해당하는 심볼릭 링크 파일을 삭제한다. 아니면 에러 발생_
  > - do a restore with tmux-resurrect key: prefix + Ctrl-r
