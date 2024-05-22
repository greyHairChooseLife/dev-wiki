# 나만의 git flow

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
   
   ex) (3의 예시에 이어서...)
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
