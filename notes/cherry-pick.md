cherry-pick은 필요한 커밋만 뽑아다 merge하는 것이다.

## 가져오기

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
  
  > 근데 브랜치를 통째로 가져올거면 그냥 branch를 merge하자.

## 활용하기

커밋 히스토리를 하나하나 살려두는것도 필요할 수 있지만 보통은 squash하게 될 것같다. 그 방법으로는,

방법A. 가져온 개수만큼 soft reset하고 다시 커밋한다.
방법B. interactive rebase로 squash한다. 

브랜치를 통째로 merge할 경우 곧바로 `git merge --squash --allow-unrelated-histories <branch-name>` 해주자.
바로 커밋이 되지는 변경내역이 staged 되어있을 것이다. 커밋하면 된다.
