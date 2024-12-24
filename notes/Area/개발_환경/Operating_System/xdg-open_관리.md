# 󰏢 xdg-open 관리



> [!cn] 개념정리
>
> `xdg-open`은 목표 대상에 따라 적절한 프로그램으로 실행해주는 편리한 도구다.

## MIME 타입별로 맵핑하기

```bash
vi $HOME/.config/mimeapps.list
```


## 어플리케이션 동작 상세 설정


```bash
# 시스템 전체
/usr/share/applications/<application>.desktop

# 사용자별
~/.local/share/applications/<application>.desktop

# 특히 Exec 키에서 option, flag 등 구체적 명령을 지정할 수 있다.
```
