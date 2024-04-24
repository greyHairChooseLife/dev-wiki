---
uid: 
title:
created: 
updated: 
---

# 공통

- snippet 등 노트 시작 시 최대한 자동화 하자.

# uid
 
- 숫자와 알파벳을 번갈아 쓴다.
- depth가 하나 내려갈 때마다 서로다른 단위가 사용되고, '1' 혹은 'a'부터 다시 시작된다.
 
> ex)  
> 1~0a~z...

# title

- (실제 파일의 제목은 "uid.md"가 될테니, title이 필요하다.)
- wiki에 link를 생성할 때 description이 파일 이름이 되면 BEST
  - 이것으로 충분한 경우에만
- 반드시 그럴 필요는 없다. 더 좋은 설명을 붙여주면 더 좋다.

# created

created에 값이 없을 경우 또는 metadata가 없을 경우 생성한다.

# updated

created에 값이 있을 경우 실행한다. 파일을 저장 할 때마다 계속 업데이트 된다.
