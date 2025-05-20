# 󰏢 file share



## magic-wormhole

_양쪽 시스템에 모두 설치해야하는 것 외엔, 간편하고 훨씬 좋은듯?_

[repo](https://github.com/magic-wormhole/magic-wormhole)

- 보내기

  ```bash
  wormhole send <file path>

  # 출력 예시
  # [me@my-computer]$ wormhole send ~/file.txt
  # Sending 365.4 kB file named 'file.txt'
  # Wormhole code is: 1-retraction-sentence
  # On the other computer, please run:
  #
  # wormhole receive 1-retraction-sentence
  ```

- 받기

  ```bash
  wormhole receive <code>
  ```

## RSYNC


> [!re]
> rsync를 사용하더라도 보안을 위해 protocol은 ssh를 사용한다.


- options
  | Option       | Name         | Description                                                              | When to Use                                          |
  |--------------|--------------|--------------------------------------------------------------------------|------------------------------------------------------|
  | `-a`         | Archive      | Preserves permissions, timestamps, recursive, etc. (combines `-rlptgoD`) | Most backup/mirroring scenarios                      |
  | `-z`         | Compress     | Compresses data during transfer                                          | Slow networks, transferring text files               |
  | `-r`         | Recursive    | Copies directories recursively                                           | When copying directory structures (included in `-a`) |
  | `-e`         | Remote Shell | Specifies the remote shell to use                                        | When specifying SSH keys or custom SSH options       |
  | `-n`         | Dry Run      | Simulates the transfer without making changes                            | Testing before actual transfers                      |
  | `--delete`   | Delete       | Removes files at destination that aren't in source                       | Creating exact mirrors                               |
  | `--exclude`  | Exclude      | Excludes specified patterns from transfer                                | Skipping specific files/directories                  |
  | `--progress` | Progress     | Shows progress during transfer                                           | When copying large files                             |
  | `--partial`  | Partial      | Keeps partially transferred files                                        | Resuming interrupted large transfers                 |
  | `-v`         | Verbose      | Shows detailed output of transferred files                               | When you want to see what's being copied             |

  - > The `-a` (archive) option is actually a shorthand that enables several options including:
    > 
    >   - `-r` (recursive)
    >   - `-l` (copy symlinks as symlinks)
    >   - `-p` (preserve permissions)
    >   - `-t` (preserve times)
    >   - `-g` (preserve group)
    >   - `-o` (preserve owner)
    >   - `-D` (preserve device files and special files)


- Examples

  ```bash
  # local
  rsync -az <path/from> <path/to>

  # remote
  rsync -az <path/from> <user>@<host>:<path/to>


  # ssh-key
  rsync -az -e "ssh -i ~/.ssh/<SSH-키> -p <포트번호>" <user>@<host>:<path from> <path to>
  # 보통은 password 접근을 막아놓기 때문에 ssh-key를 전달해야한다. rsync에서 ssh 명령을 직접
  # 사용할 수는 없다. 따라서 ssh-key를 지정해야한다면 아래처럼 `-e` 옵션으로 rsync가 사용할 ssh명령
  # 자체를 전달해야한다.
  

  ex)
  rsync -az ./*.json sy@10.75.726.225:/home/sy/temp
  ```




## deprecated


### SCP

- ```bash
  scp <object file path> <target system user>@<target system IP>:<target system path>

  ex)
  scp inno-data/test/test.sh sy@10.71.126.194:/home/sy/test/
  scp -r /home/sy/cbpromaker/미래창업연구소/inno-forest-web-scrapper sy@10.71.126.216:/home/sy/inno2
  scp -r /home/sy/cbpromaker/미래창업연구소/inno-forest-web-scrapper sy@10.71.126.201:/home/sy/inno2
  ```

> 디렉토리 자체를 옮기술도 있다.


