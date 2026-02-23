# Section 18. 구조체와 공용체



> [!td] Centinel Node
>
> 센티넬 노드(Centinel Node, Sentinel Node)는 자료구조에서 특별한 역할을 하는 노드로, 주로 링크드
> 리스트의 시작이나 끝에 위치하여 경계 조건 처리를 단순하게 만들어줍니다.  
>
> - [ ] 활용해서 만들어보자.





## 119. 구조체 선언 및 정의


- 뭔가의 집합체
- 서로 다른 것들이 모임
  - 구성 요소를 멤버(Member)라 부름
  - index가 없다.
  - 대신 개별요소에 접근할 때 **멤버접근 연산자**를 사용한다.
- 초기값을 주고싶으면 멤버 선언 순서대로 줘야함
- `typedef` 선언을 동반하는것이 일반적
    

- 멤버접근 연산자
  - 기본적으로는 `.`
  - 만약 구조체에 대한 포인터라면 `->`


- 예시 코드
  ````c
  // 구조체 선언 및 typedef
  typedef struct {
      int id;
      char name[20];
      float score;
  } STUDENT;


  int main() {
      // 구조체 변수 선언 및 초기화 (멤버 순서대로)
      STUDENT s1 = {1, "", 95.5};

      // s1은 변수보다는 "인스턴스"라고 불림
      // 기본적인 접근
      s1.name = "Alice";

      // 구조체 멤버 접근 (.)
      printf("ID: %d\n", s1.id);
      printf("Name: %s\n", s1.name);
      printf("Score: %.1f\n", s1.score);
  }
  ````




## 120. 구조체와 메모리 동적 할당


- 구조체도 배열 선언 가능
- `malloc()` 함수로 **동적 선언해** 관리하는 경우도 일반적


- 예시 코드
  ```c
  // 구조체 선언 및 typedef
  typedef struct {
      int id;
      char name[20];
      float score;
  } STUDENT;


  int main() {
      STUDENT *s1 = NULL;

      // malloc으로 구조체 메모리 동적 할당
      s1 = (STUDENT *)malloc(sizeof(Student));

      // 멤버 값 할당
      s1->id = 1;
      strcpy(s1->name, "Alice"); // 문자열 복사
      s1->score = 95.5;

      // 구조체 멤버 접근 (->)
      printf("ID: %d\n", s1->id);
      printf("Name: %s\n", s1->name);
      printf("Score: %.1f\n", s1->score);

      // 메모리 해제
      free(s1);
  }
  ```



- 매개변수 및 반환형으로 구조체를 전달할 때: **포인터 활용** 

  - **왜?** 값을 다 갔다주면 **쓸데없이 복사만 많이하는거니까 비효율적이다.**

  - 예시 코드
    ```c
    typedef struct {
        int id;
        char name[20];
        float score;
    } STUDENT;


    // callee: 구조체 포인터로 값 저장
    void set_student(STUDENT *s, int id, const char *name, float score) {
        s->id = id;
        strcpy(s->name, name);
        s->score = score;
    }


    int main() {
        // caller: 구조체 포인터변수 선언
        STUDENT *s1 = NULL;

        // malloc으로 구조체 메모리 동적 할당
        s1 = (STUDENT *)malloc(sizeof(Student));

        // callee 호출 (주소 전달)
        set_student(s1, 1, "Alice", 95.5);

        // 값 출력
        printf("ID: %d\n", s1.id);
        printf("Name: %s\n", s1.name);
        printf("Score: %.1f\n", s1.score);
    }
    ```



## 121. 구조체와 함수(매개변수, 반환자료)

- 포인터로 넘겨라



## 122. 구조체를 멤버로 갖는 구조체


- 컴파일러에게 알려줘야하니 선언 순서가 중요하다.

- 예시
  ```c
  typedef struct {
      char city[20];
      char street[50];
  } Address;


  typedef struct {
      int id;
      char name[20];
      Address addr; // 구조체 멤버
  } Student;


  int main() {
      // 구조체를 멤버로 갖는 구조체 사용
      Student s1;
      s1.id = 1;
      strcpy(s1.name, "Alice");
      strcpy(s1.addr.city, "Seoul");
      strcpy(s1.addr.street, "123 Main St");

      printf("ID: %d\n", s1.id);
      printf("Name: %s\n", s1.name);
      printf("City: %s\n", s1.addr.city);
      printf("Street: %s\n", s1.addr.street);
  }
  ```




- 자기참조 구조체: 자기 자신에 대한 포인터를 멤버로 갖는 구조체

  - 대표적 예시: **Linked List**

    ```c
    // 자기참조 구조체 예시 (연결 리스트 노드)
    typedef struct Node {
        int data;
        struct Node *next; // 자기 자신을 가리키는 포인터
    } Node;


    int main() {
        // 자기참조 구조체 사용 예시
        Node n1, n2;

        n1.data = 10;
        n1.next = &n2;
        n2.data = 20;
        n2.next = NULL;

        printf("Node 1 data: %d\n", n1.data);
        printf("Node 2 data: %d\n", n1.next->data);
    }
    ```



## 123. [필수 실습 문제] 자기 참조 구조체와 재귀호출


- File Path: 123.c
  ```c
  #include <stdio.h>
  
  typedef struct USERDATA {
          char szName[32];
          char szPhone[32];
  
          struct USERDATA *pNext;
  } USERDATA;
  
  void nameAllUser(USERDATA *);
  
  int main(void) {
      USERDATA userList[4] = {
          {"김두식", "1234", NULL},
          {"이미현", "2345", NULL},
          {"김봉석", "8231", NULL},
          {"장희수", "7703", NULL},
      };
  
      USERDATA *pUser = NULL;
  
      userList[0].pNext = &userList[1];
      userList[1].pNext = &userList[2];
      userList[2].pNext = &userList[3];
      userList[3].pNext = NULL;
  
      nameAllUser(userList);
  };
  
  void nameAllUser(USERDATA *pUser) {
      printf("this: %p, next: %p\n", pUser, pUser->pNext);
      printf("    이름: %s, 번호: %s\n", pUser->szName, pUser->szPhone);
  
      if (pUser->pNext != NULL)
          nameAllUser(pUser->pNext);
  };
  
  ```

  - 실행 결과
    ```bash
    this: 0x7ffdff8dc950, next: 0x7ffdff8dc998
        이름: 김두식, 번호: 1234
    this: 0x7ffdff8dc998, next: 0x7ffdff8dc9e0
        이름: 이미현, 번호: 2345
    this: 0x7ffdff8dc9e0, next: 0x7ffdff8dca28
        이름: 김봉석, 번호: 8231
    this: 0x7ffdff8dca28, next: (nil)
        이름: 장희수, 번호: 7703
    ```


- 주소 차이를 계산해보면 딱 구조체의 크기만큼인 것을 알 수 있다.
  - 이것은 단지 `userList`를 그냥 배열에 담았기 때문이다.
  - 연결 리스트와는 아무 관련 없다.

  - 주소 차이 계산:
    ```
    0x7ffd385400c8 - 0x7ffd38540080 = 0x48 (16진수, 10진수로는 72)
    ```



## 124. 비트 필드


- 1 Byte 정보를 **쉽게 잘라 쓰기위한 문법**
  - 주로 임베디드 쪽에서 사용함

- 멤버는 **비트 단위** 데이터
- 멤버 선언 시 먼저 기술한 멤버는 8 bit 중 **오른쪽부터 적용**



- 예제) File Path: 124.c

  - **주석을  잘 읽고 이해하자.**
  ```c
  #include <stdio.h>
  
  typedef struct DATAFLAG {
          // 맨 오른쪽 용법이 bit field
          unsigned char main : 1; // 1 bit 사용한다는 뜻
          unsigned char left : 2; // 2 bit
          unsigned char right : 3;
          unsigned char top : 2;
  } DATAFLAG;
  
  int main() {
      // top 멤버는 2비트를 사용하는데 4를 넣으면  오버된다.
      DATAFLAG flagSwitch = {0, 3, 7, 4};
      /* 1: x/1tb &flagSwitch */
      /* 0x7fffffffc517: 00111110 */

      printf("%d\n", flagSwitch.main);
      printf("%d\n", flagSwitch.left);
      printf("%d\n", flagSwitch.right);
      printf("%d\n\n", flagSwitch.top);
  
      // 구조체에 어떤 값이 저장되어있는가?
      printf("%x\n", *((unsigned char *)&flagSwitch)); // 0x3e
      printf("%b\n",
             *((unsigned char *)&flagSwitch)); // 0b111110, 오른쪽 부터 저장
  
      // 구조체의 크기는? 1 bit
      printf("%zd\n", sizeof(flagSwitch));
      return 0;
  }
  ```
  
  - 비트 필드를 사용하지 않고 같은것을 구현하려면 아래처럼 비트 연산도 하고 자리수에 따라
    shift연산도 맞춰서 하고 하여튼 귀찮다!
    ```c
    printf("bit mask: %d\n", *((unsigned char *)&flagSwitch) & 0x01);
    printf("bit mask: %d\n", (*((unsigned char *)&flagSwitch) & 0x06) >> 1);
    ```



## 125. 공용체


- 고정 크기

- 하나의 대상에 대해 **여러 해석방법(자료형)을 부여하는 문법**

- 고수준 언어 입장에서 직관적으로 데이터를 해석하기에 더 좋은 방법
  - 이렇게 안하면 마찬가지로 비트 연산 하면서 지지고 복고 해야한다.

  

> - IPv4 주소가 하나의 예시다.
>   - IPv4의 주소 범위는 `<0~255>.<0~255>.<0~255>.<0~255>`
>   - 저렇게 범위가 지정되는 이유는 저것이 4 Byte 데이터이고, 각 자리마다 1 Byte 즉 총 32 bit 라는거다.
>   - 실제로는 32비트의 2진수 값으로 유효하지만 우리 사람 눈에는 10진수로 해석해서 읽는다.




## 126. 구조체 멤버 맞춤(정렬)


- 구조체의 멤버 사이엔 공백이 있을 수 있다. 관리 편의상 그렇다.

- 정 원한다면 전처리기를 이용해 바꿀수는 있다.
  - `#pragma pack()`

- 특수한 경우에는 이렇게 바꿔줄 수 있다.
  - 하드웨어를 건든다던가..
  - 네트워크 패킷을 다룬다던가..



- 예제) File Path: 126.c
  ```c
  #pragma pack(push, 1)
  typedef struct USERDATA {
          char ch;
          int nAge;
          char szName[5];
  } USERDATA;
  #pragma pack(pop)
  
  typedef struct USERDATA2 {
          char ch;
          int nAge;
          char szName[5];
  } USERDATA2;
  
  int main() {
      USERDATA user = {'A', 10, "Hoon"};
      USERDATA2 user2 = {'A', 10, "Hoon"};
  
      int data = 0x11223344;
  
      printf("%lu\n", sizeof(USERDATA));      // 10
      printf("%lu\n", sizeof(USERDATA2));     // 16
  }
  ```

  - debugger hexdump
    ```asm
    pwndbg> hx &user 10
    +0000 0x7fffffffc4f6  41 0a 00 00 00 48 6f 6f  6e 00                    │A....Hoo│n.      │

    pwndbg> hx &user2 16
    +0000 0x7fffffffc500  41 00 00 00 0a 00 00 00  48 6f 6f 6e 00 00 00 00  │A.......│Hoon....│
    ```

