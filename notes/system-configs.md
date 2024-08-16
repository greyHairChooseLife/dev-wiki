# 󰏢 System Configs

## xdg-open

아래 파일에 **어떤 경우**에 **어떤 명세**를 따라 프로그램을 실행할지 정의되어있다.

```bash
$ vi .config/mimeapps.list
```

  > [!re] 주의사항
  >
  > - 현재 **각 경우**에 대하여 어덯게 적용되어있는지는 `xdg-settings get`으로 확인해야한다.
  > - 각 경우를 일컫는 사전 정의된 이름들이 다소 난해하다. 그러니 그냥 검색하는게 좋을것.
  > - 파일을 수정한 뒤에도 적용이 바로는 안된다. 껏다켜야할듯? 그냥 `xdg-settings set`을 활용하는게 속편하다.


이때, 어떤 명세라는 것은 아래에 정의되어 있다.

```bash
~/.local/share/applications/{spec-name}.desktop
```

이름이야 맘대로 짓고, 다른 비슷한 application들은 어덯게 정의하고 있는지 참고해서 만들자.

또한, `/usr/share/applications`에 system-wide로 적용되는 기본값이 정의되어있는 듯 하다.

  > [!qt] system-wide로 적용된 스펙을 변경하면 어플리케이션을 더 자유롭게 사용할 수 있을 것 같다. 근데 패키지 매니저를 통해 어플리케이션이 업데이트 될 때 또 새롭게 덮어씌워지는건 아닌지 확인이 필요하다.
  >   󱞪

###

> [!rf]
> [stack exchange](https://unix.stackexchange.com/questions/36380/how-to-properly-and-easily-configure-xdg-open-without-any-environment)
> [stack exchange](https://askubuntu.com/questions/540939/xdg-open-only-opens-a-new-tab-in-a-new-chromium-window-despite-passing-it-a-url?newreg=5d330212224449878da2c5eb50a3d43e)
> [github issue](https://github.com/vimwiki/vimwiki/issues/137)
