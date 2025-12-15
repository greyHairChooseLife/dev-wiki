# Pintos 회고


**TOC**

- OS에 대한 이해
    - booting
        - 물리메모리
        - 가상주소공간(Page Table)
    - 쓰레드 생성
    - 동시성
        - mutex, semaphore, conditional variable
    - schedule
        - timer interrupt
        - context switch
    - interrupt setup
    - system call
    - paging

    - (곁)malloc


- 디버깅 경험
    
    - register, memory 레벨까지 내려가는 디버깅


- 시스템 프로그래밍


- 컴파일, 실행파일




- 개발환경

    - tmux 및 tmuxp를 활용한 개발환경 관리는 매우 효과적이다.
        - tmuxp 명세는 yaml파일이며, 그 자체로 문서의 기능을 한다.
        - 다만, 개인적으로만 사용하는 alias 사용을 지양하자. 시스템이 바뀌었을 때 재현이 어렵다.
        - 주석을 적극적으로 달자.


- 협업
    - history 관리
        - PR의 volume을 적절히 계획하는 것이 무엇보다 중요하다.
        - PR을 위한 브랜치는 별도로 만들 수도 있다.
        - 개발 과정, 사고의 흐름을 그대로 표현하는 history도 좋지만, 팀원이 모든 것을 알아야하는 것은 아니다.
