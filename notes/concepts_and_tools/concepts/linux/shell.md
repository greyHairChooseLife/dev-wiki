---
hide:
title: 'shell script의 세계'
date: 2024-04-07
update:
---

> `shell`과 `shell-sript`는 매력적이다.

## `shell`이란 무엇인가

- `shell`은 사용자와 커널 사이의 인터페이스다. (사람이 커널에 직접 명령을 내리긴 어렵다.)

  - `shell`에도 종류가 있다. `bash, zsh, csh, ksh` 등이 있다.

- 인터페이스라면 명령을 받고 결과를 뱉어줄텐데, `shell`은 어떻게 듣고 말할까?

  옛날엔 전기 플러그를 이쪽 저쪽 적절한 위치에 꼽아서 말하고, 프린터가 출력해주는 결과를 받기도 했다. 이런
  장비를 터미널이라고 했다.

  지금은 그걸 실시간으로 할 수 있는 것이다. hardware였던 `터미널`은 software인 `터미널 에뮬레이터`로
  대체되었다. (`urxvt, xterm, iterm2` 등이 있다.)

## `shell script`란 무엇인가

shell이 이해할 수 있을 정도의 low-level 언어이다.
