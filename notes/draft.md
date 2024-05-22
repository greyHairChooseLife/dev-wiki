
1. run  dev server with `pnpm run dev`
2. insult secrets into github repo secerts
   - secrets vs variables
   - env vs repo vs org




# nvim plugin diffview for git


두개의 명령이 주요하다.

1. DiffviewOpen <FROM>..<TO>
   : 두개의 커밋 사이의 변경사항을 보여준다. commit / branch / tag 모두 가능하다.

2. DiffviewFileHistory --range=<FROM>..<TO>
   : (이름이 왜 파일 히스토리인지는 모르겠지만) 두 지점 사이에 커밋들을 나열하고, 각 커밋마다 변경된 파일들을
   모두 보여준다.

(1)은 통째로 비교할 때, (2)는 비교 범위 내의 커밋들 하나 하나를 비교할 때 사용하면 되겠다.

## 업그레이드 할것들

<leader><leader>reg 으로 grep을 활용하는것도 좋겠다. 이 외에도 내 워크플로우상 어떤 것들이 유용할지
고려해보자. 플러그인의 깃헙 저장소를 가보는것도 좋을듯




