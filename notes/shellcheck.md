---
title: 'shell script 에러 체크'
date: 2024-04-16
---

예전에 원티드에서 진행한 팀 프로젝트 때 나도 뭔가 도움이 될 수 있는게 없을까하고 열을 올렸던게 git
pre-commit script를 작성해본거였다.

여섯명 쯤 되는 우리 팀원들과의 온라인 미팅때 배포하며 얼마나 걱정을 했는지.. 여 지없이 edge case는 발생했다.
다행히 금방 해결은 했지만 진땀 빼는 순간이었다.

그런데 아래와 같은 도구가 있다! 나도 앞으로 간단한 스크립트를 짤 때 활용해봐야겠 다.

## ShellCheck

ShellCheck is a GPLv3 tool that gives warnings and suggestions for bash/sh shell scripts:

The goals of ShellCheck are

- To point out and clarify typical beginner's syntax issues that cause a shell to give cryptic error
  messages.
- To point out and clarify typical intermediate level semantic problems that cause a shell to behave
  strangely and counter-intuitively.
- To point out subtle caveats, corner cases and pitfalls that may cause an advanced user's otherwise working
  script to fail under future circumstances.
- See the gallery of bad code for examples of what ShellCheck can help you identify!

## reference

- [github](https://github.com/koalaman/shellcheck)
