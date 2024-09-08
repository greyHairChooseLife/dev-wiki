# 󰏢 git & github


## 1. cherry-pick

> [!cn] 개념정리
>
> cherry-pick은 필요한 커밋만 뽑아다 merge하는 것이다.

- 1.1. 가져오기

  당연히 전혀 다른 저장소의 커밋을 가져올수도 있다.(remote add로 추가하고 fetch받아서 필요한걸 갖다 쓰면
  된다.)

  - 여러개의 커밋 가져오기

    ```bash
    git cherry-pick <commit1> <commit2> <commit3>
    ```

  - 범위를 지정하여 가져오기

    ```bash
    git cherry-pick <start-commit>^..<end-commit>
    # <start-commit>도 포함해서 가져온다.
    ```

  - 특정 브랜치 통째로 가져오기

    ```bash
    git cherry-pick <branch-name>^..HEAD
    ```

    _*근데 브랜치를 통째로 가져올거면 그냥 branch를 merge하자._

- 1.2. 활용하기

  커밋 히스토리를 하나하나 살려두는것도 필요할 수 있지만 보통은 squash하게 될 것같다. 그 방법으로는,

  방법A. 가져온 개수만큼 soft reset하고 다시 커밋한다.
  방법B. interactive rebase로 squash한다.

  브랜치를 통째로 merge할 경우 곧바로 `git merge --squash --allow-unrelated-histories <branch-name>` 해주자.
  바로 커밋이 되지는 변경내역이 staged 되어있을 것이다. 커밋하면 된다.


## 2. github actions

> [!cn] 개념정리
>
> 자동화 툴. github과 연관된 어떤 작업에 트리거를 설정해서 원하는 명령을 실행할 수 있다.
>
> 명령을 실행한다면 역시 시스템이 필요하고, 이것을 기본적으로는 github에서 대여해준다. 신기한게 3대 운영체제 모두 대여해준다.
>
> 다만 private repository에는 다소 제한이 있어서, 본격적인 상업용으로는 사용이 어렵다. 물론 특별한 일이
> 아니라면 private의 제한된 허용시간으로도 충분할 것이다.
>
> 하지만 나는 내 멋대로 하는게 좋으니까 self-hosted runner를 사용하자. 여기서 runner는 위에서 표현한 system이다.
> github actions의 명세를 수행할 시스템을 뜻한다.


- self-hosted runner 사용법

  원하는 github repository의 setting에서 설정할 수 있다. self-hosted runner라고 큰 버튼이 있는데, 눌러보면
  복붙만으로 모든 설정을 끝내는 명령어들을 안내하고있다.

- 주의할 점

  복수의 repository를 하나의 runner에서 다룰 수 없다. 도대체 왜 못하게 해놨는지 모르겠다. 다만 organization
  단위로 runner를 공유할 수는 있다.

  최근들어 org로 repository를 구분하기 시작했는데, 그래서 썩 나쁘지는 않다. org마다 하나의 runner서버를 둔다고 생각하면 되겠다.

## 99. deprecated

### 99.1. 나만의 git flow

- 아무리 급하고 간단한 개발 작업도 main에서는 하지 않는다. 반드시 아래의 workflow를 따른다.
- 다만 `README` 또는 `.gitignore` 같은 개발 외적인 요소는 main에서 직접 하자

  >이때 비록 간단한 수정사항이라도 훗날, (개발중이던 여타 브랜치와) conflict가 발생할 수 있다. 그러니
  변경사항이 발생하면 빠르게 rebase 하자. 즉, feature branch로 checkout 한 뒤에 git rebase main 따위를
  실행하는것.

1. issue 생성
   <bug, feature, new-version, etc, deploy>

2. branch 생성
   브랜치 이름은 <issue type>/issue-<issue number>으로 한다.
   예를들어 버그가 발견되어 issue 5번이 생성됐다면, 이를 해결하는 브랜치는 bug/issue-5로 한다.

3. 개발 완료되면 remote에 push하고  Pull Request 생성
   이때 PR 메시지는 해당 브랜치에서 작업한 커밋 내역만 모아준다. 적절히 포맷팅 해주면 좋은데, 나는 아래와
   같이 만들었다.
   `git log --pretty=format:"COMMIT : %h%nTITLE  : %s%nMESSAGE: %b%n%cd==================================== %ae%n%n" --date=short`
   이때 끝에 `<start branch>..<destination branch>`를 해 주면 원하는 만큼의 커밋 log를 가져올 수 있다.
   `alias glMM`

   ex) (2의 예시에 이어서...)
   ```message for PR
   title: (기본값으로 하자. 기본값인 branch name으로 issue type과 number를 알 수 있으니까.)

   message:
   COMMIT : 866d6ea
   TITLE  : feature-5
   MESSAGE: this is content!! 이놈아!!

   2024-05-16==================================== greyhairchooselife@gmail.com


   COMMIT : c433057
   TITLE  : feature-4
   MESSAGE:
   2024-05-16==================================== greyhairchooselife@gmail.com


   COMMIT : dcacb77
   TITLE  : feature-3
   MESSAGE:
   2024-05-16==================================== greyhairchooselife@gmail.com


   COMMIT : 8ab59b9
   TITLE  : feature-2
   MESSAGE:
   2024-05-16==================================== greyhairchooselife@gmail.com


   COMMIT : dca81d8
   TITLE  : b1
   MESSAGE:
   2024-05-16==================================== greyhairchooselife@gmail.com
   ```

4. PR을 squash merge하고, issue를 close한다.
   이때 PR의 메시지를 merge commit에도 그대로 활용한다. 단, title과 message는 조금 수정/추가한다.

   ex)
   ```(squash)merge commit message
   title: SQUASH MERGE from PR #6, branch bug/issue-5
   message:
   This commit Resolved #5

   (...paste copy of message from PR)
   ```

5. main 브랜치에서 (merge된 origin/main을)pull 받아온다.
   이때 기존 PR 후 merge 완료된 브랜치 원격에서만 기록용으로 살려둔다. 로컬에서는 지우는데, squash
   merge특성상 `-D` 플래그로 지워야 한다.

6. 1~5를 반복한다.