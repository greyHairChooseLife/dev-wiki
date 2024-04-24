---
title: 'sysz: interactive ui for systemctl'
date: 2024-04-16
update: 2024-04-21
---

정확히는 TUI는 아니겠다. 단지 fzf를 활용해서 systemctl를 더욱 interactive하게사 용하는 스크립트 정도라고 할
수 있다. 제작자도 오직 shell script로만 제작했다고강 조한다.

매번 `systemctl status`를 치면서 번거로웠고, 마땅한 도구가 안보여서 fzf preview 를 활용해서 직접 만들까
했는데, 오늘 보니 멀쩡한게 있다.

## sysz

A fzf terminal UI for systemctl

### Features

_VERSION: 1.4.3_

- See and filter both system and user units simultaneously.
- Supports all unit types.
- Units ordered by service, timer, socket, and the rest.
- Runs sudo automatically and only if necessary.
- Filter units by state using ctrl-s or the --state option.
- Run daemon-reload with ctrl-r.
- Has short versions of systemctl commands to reduce typing.
- Runs status after other commands (start, stop, restart, etc).
- Select multiple units, states, and commands using TAB.
- Only prompts commands based on current state (e.g. show "start" only if the unit is inactive).

## \*QnA

- `Unit <unit name> could not be found.`라고 나오는 것은 뭐야? 어떻게 대처해?

  > 말 그대로 unit 파일을 찾을 수 없다는건데, 이상한건 없는파일을 도대체 어떻게 찾을 시도를 했냐는거다.
  > systemctl에 어떤 명령을 붙여봐도 sysz의 기본 명령만큼 리스트를 보여주지 않는다.

  - sysz를 뜯어보자.

    <details>
    <summary>source code of "sysz"</summary>

    ```bash
    _sysz_list() {
      local args
      declare -a args
      args=(
        --all
        --no-legend
        --full
        --plain
        --no-pager
        "${STATES[@]}"
        "$@"
      )
      (
        systemctl list-units "${args[@]}"
        systemctl list-unit-files "${args[@]}"
      ) | sort -u -t ' ' -k1,1 |
        while read -r line; do
          unit=${line%% *}
          if [[ $line = *" active "* ]]; then
            printf '\033[0;32m%s\033[0m\n' "$unit" # green
          elif [[ $line = *" failed "* ]]; then
            printf '\033[0;31m%s\033[0m\n' "$unit" # red
          elif [[ $line = *" not-found "* ]]; then
            printf '\033[1;33m%s\033[0m\n' "$unit" # red
          else
            echo "$unit"
          fi
        done
    }
    ```

    </details><br />

    뭐지? 내가 저렇게 똑같이 command 입력하고 살펴봐도 다 모아봐도 숫자가 다르다... 이상하다...  
    이것만 너무 매달릴수 없으니 일단은 넘어가자...

## \*reference

- [github](https://github.com/joehillen/sysz)
