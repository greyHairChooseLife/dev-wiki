# 섹션 16. 메모리와 포인터




## 96. 컴퓨터 메모리 종류

- CPU는 연산할 코드와 대상 정보를 모두 메모리로부터 가져옴
- 메모리는 변수를 통해 사용


- 가상메모리로 추상화해서 사용

  - Stack: 자동변수(생겼다 사라졌다 하는 놈들), 보통 1MB
  - Heap: 동적 할당 메모리
    - 프로그램 실행 중(runtime)에 필요에 따라 할당하거나 해제할 수 있는 메모리 영역
    - 관련 명령
      ```c
      malloc(), calloc()  // 메모리 내놔
      free()              // 다썼다 반납
      ```

    - 물리메모리의 어디를 줄지는 OS가 결정

  - 실행 코드
    - text(code) section: 기계어 실행 코드
    - data section:
      - Read only: 문자열 상수
        - `printf("%s", "hello")`같은 코드의 "hello"같은 문자열이 여기에 있다.
      - Read/Write: 정적 메모리


> [!nt] canonical address
> 
> - x86-64 아키텍쳐 중 intel계열 프로세서는 canonical addresss를 사용한다.
> - 아래의 두가지 형식의 주소를 쓴다.
>   - 0x0000.... 
>   - 0xFFFF....
> - 이때 5번째 Byte의 48번째 비트가 1인지 0인지에 따라 똑같이 따라간다.
>   - 이를 통해 먼저 보이는 5개의 바이트만 보고도 어떤 16진수 값이 주소값인지 아닌지 판단할수있다.
>   - 5번째 바이트가 8이상의 값이면 무조건 0x0000..., 미만이면 0xFFFF...
>
> - [ ] 알아보기!!
> > [!qt] 혹시 그럼 FF로 시작하면 스택이고 0000이면 힙인거 아닐까?
> >   󱞪 



- [ ] 아래 확실히 공부해 두자

> [!qt] 세그먼트가 뭐야? c언어 메모리 다룰 때 말야
>   󱞪 
> 
> C언어에서 "세그먼트(segment)"란 프로그램이 메모리에 적재될 때 나뉘는 여러 영역(구역)을 의미합니다.
> 각각의 세그먼트는 특정한 목적을 가지고 있습니다.
> 
> 주요 세그먼트는 다음과 같습니다:
> 
> - **코드 세그먼트 (Text Segment)**  실행할 프로그램 코드(함수 등)가 저장되는 영역입니다. 보통 읽기
>   전용입니다.
> 
> - **데이터 세그먼트 (Data Segment)**  초기값이 있는 전역 변수, static 변수 등이 저장됩니다.
> 
> - **BSS 세그먼트**  초기값이 없는 전역 변수, static 변수 등이 저장됩니다. 프로그램 실행 시 0으로
>   초기화됩니다.
> 
> - **힙(Heap) 세그먼트**  `malloc`, `calloc` 등 동적 메모리 할당에 사용되는 영역입니다. 크기가 실행
>   중에 변할 수 있습니다.
> 
> - **스택(Stack) 세그먼트**  함수 호출 시 지역 변수, 매개변수, 리턴 주소 등이 저장되는 영역입니다.
>   함수 호출/종료에 따라 크기가 변합니다.
> 
> **정리:**  세그먼트는 프로그램이 메모리에 올라갈 때 용도별로 나뉘는 구역입니다. 각각의 세그먼트는
> 변수나 코드의 종류에 따라 다르게 사용됩니다.




## 97. 포인터 변수 기본 문법


- 포인터는 주소값 저장 전용 변수


- 문법
  ```c
  char *pszData  = &ch;  // char ch = 'a';
  ```
  - pszData라는 심볼(식별자)에는 주소값이 들어있는데, 그 주소값이 가리키는 메모리의 값은 `char`로
    읽겠다.


- 직접지정 vs 간접지정

  - **직접지정(direct access/assignment)**  
    변수명이나 배열명 등 **메모리의 식별자**를 직접 사용해서 값을 읽거나 씁니다.  
    예시:
    ```c
    int a = 10;
    a = 20; // 직접지정

    int arr[3] = {0};
    arr[1] = 5; // 직접지정
    ```

  - **간접지정(indirect access/assignment)**  
    **포인터(주소값을 가진 변수)**를 통해 해당 주소의 메모리에 접근해서 값을 읽거나 씁니다.  
    예시:
    ```c
    int a = 10;
    int* p = &a;
    *p = 20; // 간접지정

    int arr[3] = {0};
    int* p_arr = arr;
    *(p_arr + 1) = 5; // 간접지정
    ```


- 메모리 들여다보기

  ```c
  #include <stdio.h>
  
  int main(void) {
      int nData = 10;
      printf("%s\n", "nData");
  
      printf("%d\n", nData);
      printf("%p\n", &nData);
      return 0;
  }
  ```

  - 리틀 앤디안
    ```asm
    pwndbg> p &nData
    $1 = (int *) 0x7fffffffc514
    pwndbg> hexdump &nData
    +0000 0x7fffffffc514  0a 00 00 00 00 80 96 6b  86 02 21 68 c0 c5 ff ff  │.......k│..!h....│
    ```

- "실행 할 때마다 주소값이 달라질거에요."
  - ~아닌데?~
  - gdb에서 ASLR 켜기/끄기
    - ASLR 끄기(기본값):
      ```
      (gdb) set disable-randomization on
      ```
    - ASLR 켜기:
      ```
      (gdb) set disable-randomization off
      ```


- 주소값이나 정수값은 리틀-앤디안 방식으로 저장된다.
  ```asm
  In file: /home/sy/jg/dokk-C/실습/my.c:6
      1 #include <stdio.h>
      2
      3 int main(void) {
      4     int nData = 10;
      5     int *nDataPnt = &nData;
   ►  6     printf("%s\n", "nData");
     11 }
  ─────────────────────────────────────────────
  pwndbg> p &nData
  $10 = (int *) 0x7fffffffc50c
  pwndbg> hexdump &nDataPnt
  +0000 0x7fffffffc510  0c c5 ff ff ff 7f 00 00
  ```




## 98. 포인터와 1차원 배열


- 포인터는 1차원 배열과 같이 많이 쓰인다.
- 포인터 변수나 배열 이름(포인터 변수처럼 쓰인다.)에 덧셈 뺄셈을 할 수 있다.
  - 근데 이건 산술연산이라기보단 위치를 특정하기 위해 사용하는거다.


> [!nt]
>
> - 배열의 이름은 포인터처럼 쓰인다.
>   ```c
>   int nbr[3] = {5, 10, 3};
>   int *nbrPtn = nbr;
>   char chaar[3] = "hel";
>   ```
>   - ~근데 또 nbr을 print해보면 배열 전체가 나오지만 nbrPtn을 역참조해보면 `nbr[0]` 값이 나온다.~
>     - debugger가 센스있게 그냥 보여주는것 뿐, `&nbr == &nbr[0]`이 맞다.
>   - 이때 주소값에 더하기 빼기 등 연산을 하면 해당 배열에 담긴 데이터 타입에 따라 위치값을 찾는다.
>     - (&chaar + 1)
>       ```asm
>       pwndbg> p *(chaar +1)
>       $11 = 108 'e'
>       ```
>
> - 타입 정의할때가 아니면 `*`는 간접지정 연산자가 된다.
>   ```c
>   char* pszData = <어떤 주소값>;
>   *pszData = 'A';
>   ```
>   - pszData는 어떤 메모리를 가리키는 주소값만 가지고 있는 놈인데, 이렇게 한다리 건너서 대입 연산을
>     하겠다는 것


- 배열에 원소가 얼마 나들었는지 확인
  - int형의 경우 배열의 주소값으로 연산을 하면 자료형의 크기만큼씩 증가한다.
  - File Path: 98.c, 4:14
    ```c
    char szBuffer[16] = {"hello"};
    char *pszData = szBuffer;
    /* int szBuffer[16] = {3, 5, 5}; */
    /* int *pszData = szBuffer; */

    while (*pszData != '\0')
        ++pszData; // char 배열은 1B씩, int 배열은 4B씩 증가한다.

    printf("&pszData: %p\n&szBuffer: %p\n\n", pszData, szBuffer);
    printf("Len: %ld\n", pszData - szBuffer);
    ```
    - 사실은 이게 strlen 함수다.



## 99. 메모리 동적 할당 및 해제


- ```
  ┌─ Dev Time
  │   └── Build Time
  │       ├── Compile Time
  │       └── Link Time
  │
  └─ Run Time                      // Heap!!
  ```


- 동적 할당: `Run Time`에 할당하는 메모리

  - `Heap` 영역(자유 메모리 영역)을 사용
  - **프로세스에 필요한 메모리를 `OS`에게 요청(할당)해 사용하고 반환(해제)**
    - `malloc() / free()`

  - 할당 받은 메모리는 **쓰레기 값**이 들어 있음
    - clear는 필수가 아니다. 많이 지우려면 비용도 높다.
    - 쓰레기 값이 들어있다는 것을 인지하고, 그럼에도 괜찮은 코드를 작성해라.

  - 할당 단위와 Page
    - `OS`는 메모리를 Page단위로 관리함
    - 기본단위: 4KB
    - PageFault 할 때 그 Page


  - 예제
    ```c
    #include <stdlib.h>      // malloc은 여기서 갖다 쓴다.

    int* pList = Null;       // 포인터 변수는 배열처럼 사용할 수 있다. 배열을 쓰겠다는 것!!!
    pList = (int*)malloc(sizeof(int) * 3); // 12B 할당 요청

    pList[0] = 10;   // 포인터 변수는 배열처럼 사용 할 수 있다.
    pList[1] = 20;
    pList[2] = 30;
    pList[3] = 100;  // 이런게 대표적인 실수다. int 3개만큼 쓴대놓고선 4개째 쓰고있다.

    free(pList);     // 다 썼으면 반납
    ```
    - 어떤 주소를 받을지는 run time에 결정
    - **`free()` 하면 내가 할당 요청한 것 이상을 free한다. 아마도 Chunk 사이즈만큼?**
    - 대표적인 실수: `pList[3] = 100;`
      - 읽는 것만도 문제인데 쓰기는 더 큰 문제
      - 근데 컴파일러가 안알려줌
      - 심지어 작동은 그대로 된다.(`pList[2]`뒤에 바로 저장된다.)
      - **다행이도 `free()` 시점에는 알 수 있다. canary bit를 오염시킨것으로 판단한다.**

        > [!re] debug 모드 한정 동작하는듯
        >
        > - 나는 canary로 채워지지도 않고, 상관없이 free()이후에도 에러 없다.


        > [!nt] Tip: canary bits
        >
        > _debug 모드일 때,_
        > _메모리 동적 할당하면, 주소 위치에_
        >
        > - cdcdcd라고 값이 채워져 있다
        > - 그리고 그 앞뒤로 fdfdfd 같은 값들이 있다.
        >
        >   - 근데 시스템마다 다른듯
        >     ```asm
        >     pwndbg> hexdump &pList
        >     +0000 0x7fffffffc508  00 00 00 00 00 00 00 00  b0 c5 ff ff ff 7f 00 00  │........│........│
        >     pwndbg> n
        >     8           pList[0] = 10; // 포인터 변수는 배열처럼 사용 할 수 있다.
        >     pwndbg> hexdump &pList
        >     +0000 0x7fffffffc508  10 93 55 55 55 55 00 00  b0 c5 ff ff ff 7f 00 00  │..UUUU..│........│
        >     ```







## 100. 메모리 초기화, 복사, 비교


- **초기화**

  - 꼭해야하냐? 
    => 아니. 필요하면 하는거다.
       - "문자열을 저장할거라면 초기화 해주는게 좋다 - 널널"

  - 할땐 어떻게 해야하냐?

    ```c
    int* pList = NULL, * pNewList = NULL;


    // 방법 1.
    pList = (int*)malloc(sizeof(int) * 3);
    memset(pList, 0, sizeof(int) * 3);

    // 방법 2.
    pNewList = (int*)calloc(3, sizeof(int));
    ```


    > [!nt] `free()` 하더라도 (malloc 이전에) 포인터가 가리키던 메모리에는 값이 여전히 남아있다.
    >
    > ```c
    > char szBuffer[] = { "Hell" };    // 크기를 비워둬도 컴파일러가 알아서 해준다.
    > 
    > char* pMy = "Hello";
    > pMy = (char*)malloc(sizeof(char) * 6);
    > pMy[0] = 'a';
    > pMy[1] = 'b';
    > pMy[2] = 'c';
    > pMy[3] = 'd';
    > pMy[4] = 'e';
    > 
    > free(pMy);
    > ```




- **복사**

  - 원래 변수에 뭔가 할당하면 그것은 우항(r-value)를 copy하고, 좌항(l-value)에 overwrite

  - 근데 배열은 달라:

    - **Error**
      ```c
      int* prev_A[10] = { ... };
      int* next_A[10] = { 0 };

      // next_A = prev_A;     // 상수인 주소값에 주소값을 할당하려니 당연히 안된다.
      ```

    - **Good**
      ```c
      memcpy(next_A, prev_A, 4)   // 4개(int * 4) 저장된다.

      // 이놈은 알고보면 아래와 같다.
      // malloc + 초기화
      next_A = (int*)malloc(sizeof(int) * 4);
      for (int i = 0; i < 4; ++i)
        next_A[i] = prev_A[i];
      ```

  

- **비교**

  ```c
  memcmp(next_A, prev_A, 4)   // 무엇과, 무엇을, 얼만큼 비교할거냐
  ```
  - 각 자리에 해당하는 요소를 뺄셈하여 비교한다.
  - `strcmp()`는 NULL문자(`\0`) 만나면 그냥 비교 끝이다. 뒷쪽에 서로 다른 것이 있어도 같다고 해버림



## 101. [필수 실습 문제] 잘못된 메모리 복사



- 아래 코드의 결함을 찾으시오.

  ```c
  int main(void){
    char bf[12] = {"HelloWorld"};
    char* pbf = NULL;

    bf = (char*)malloc( sizeof(char) * 12);
    pbf = bf;
  }
  ```


- 정답:
  - 문법적으로는 하자가 없고, 실행하면 오류도 안난다. 근데 바보같은것이다.

    - `free()`를 안했다.
    - **`malloc`으로 `Heap` 사용한대놓고 그냥 주소를 날려버렸다. 이러면 `Stack`으로 가는것.**
      - 그래서 `free()`도 안했지만 오류가 안나는 것

  - shallow-copy vs deep-copy
    - shallow-copy는 포인터만 하나 추가하는것
    - deep-copy는 실제 값을 복제하는것


- 내 대답:
  ~"szBuffer가 아니라 &szBuffer를 대입(할당)해야한다."~



- [ ] 알아보자.
> [!qt] 근데 free()하고 난 포인터 변수에는 무슨 값이 들어있을까? 원래의 주소값?
>   󱞪 






## 102. 배열 연산자 풀어 쓰기


- 배열 연산자

  - 배열 이름(주소값, 상수)에 덧셈/뺄셈을 할 수 있댔는데, 이게 배열 연산자
  - `*(기준주소 + index)`로 인덱싱 할 수 있음
  - 포인터 변수를 배열 선언으로 사용하는것이 보통

  ```c
  char szBuffer[32] = { "You are a girl." };

  // 아래 모두 똑같다.
  szBuffer[0];
  *szBuffer;
  *(szBuffer + 1);

  // WRONG!
  *szBuffer + 1;          // szBuffer의 위치에는 주소가 있는데, 그 주소가 가리키는 값에 1을 더한 값


  // 잘 봐봐. 아래도 모두 같다.
  &szBuffer[4];
  &*(szBuffer + 4);       // (szBuffer + 4)의 위치에는 주소가 있는데, 그 주소가 가리키는 값의 주소를 내놔라
  szBuffer + 4;           // 당연히 주소가 나온다.
  ```





## 103. 문자열 복사, 비교, 검색



- **복사**

  - 문자열은 문자배열이다.
  - 그니까 배열을 복사하는것과 마찬가지 요소들을 주의해야한다.
    - 예를들어,
      ```c
      char szBuffer[] = "Hello";
      char* pszBuffer = szBuffer;

      char* pszHeap = malloc(16);
      strcpy_s(pszHeap, 16, pszBuffer);

      // 아래처럼 하면 안된다. 문법적으로는 문제가 없지만...(아래 설명)
      //pszHeap = pszBuffer
      ```

        - 의도와 다르게 `Heap`을 못쓰고 `Stack`을 쓰게된다.
        - malloc으로 할당받은 `Heap` 영역 메모리가 유실된다.




- **비교**

  - 일단 상식 채우기

    > ```c
    > char szBuffer[12] = { "TestString" };
    > char* pszData = "TestString";
    > ```
    >
    > - `szBuffer` 이 배열 식별자는 주소값을 가지고 있다. 이 주소값은 Stack에 저장된다.
    > - `pszData` 포인터 변수도 주소값을 가지고 있다. 이 주소값은 Stack에 저장된다.
    >
    > - "TestString"은 문자열 리터럴이다. 그니까 **정적 메모리 영역(코드 및 데이터 영역)** 에 있다.
    > - `{ "TestString" }`라는 것은 문자열리터럴("TestString")을 복사해서 배열에 넣은 것 뿐이다.
    >   즉, 정적 메모리 영역에 있는 문자열 리터럴을 복사해다가 `Stack`에 저장한 것
    >
    >
    > - 가상 메모리 구조
    >   ```
    >   [ 높은 주소 ]
    >   --------------------------------------
    >   | 스택(Stack)                        | 자동변수, 보통 1MB
    >   --------------------------------------
    >   | ...                                | guard page: 충돌 방지
    >   --------------------------------------
    >   | 힙(Heap)                           | 동적 할당 메모리, ex) malloc()
    >   --------------------------------------
    >   | 데이터 영역                        |
    >   | 초기화/비초기화 데이터             |
    >   --------------------------------------
    >   | 실행코드: Text section             | 실행코드 기계어, ex) main(), foo()
    >   |     //  : Data section, read only  | 문자열 상수, ex) "Hello World"
    >   |     //  :     //      , read write | 정적 메모리, ex) 전역변수
    >   --------------------------------------
    >   [ 낮은 주소 ]
    >   ```
    >
    >   - [ ] 가상메모리 세분화해서 공부하고 정리해두자!
    >
    >   - **코드(텍스트) 영역**
    >
    >     - 내용: 프로그램의 실행 코드(기계어 명령어), 즉 함수 본문 등.
    >     - 특징:  
    >       - **읽기 전용(보통 실행만 가능, 수정 불가)**
    >       - 함수, 라이브러리 코드 등이 저장됨
    >     - 예시:  
    >       - `main()`, `printf()` 등 함수의 실제 명령어 코드
    >
    >
    >   - **데이터 영역**
    >
    >     - 내용:  
    >       - **초기화된 전역/정적 변수**  
    >       - **초기화되지 않은 전역/정적 변수**  
    >       - **문자열 리터럴** 등
    >     - 특징:  
    >       - 읽기/쓰기 모두 가능
    >       - 프로그램 실행 중 값이 바뀔 수 있음
    >     - 세부 구분:  
    >       - Data Segment: 초기값이 있는 전역/정적 변수 (`int a = 3;`)
    >       - BSS Segment: 초기값이 없는 전역/정적 변수 (`int b;`)
    >       - Read-only Data (rodata): 문자열 리터럴 등 읽기 전용 데이터



  - 진짜 비교하는 함수

    ```c
    char szBuffer[12] = { "TestString" };
    char szBuffer_2[12] = "TestString";      // C99 이래로 위와 동일하다.

    char* pszData = "TestString";

    strcmp(szBuffer, pszData)                // 0
    strcmp("TestString", pszData)            // 0
    strcmp("Test", "TestString")             // -1  : 다르다! : 이놈은 길이까지 따져 비교한다.


    // 비교를 하려면
    if (strcmp(szBuffer, pszData) == 0) {
      // 이런식으로
    }
    ```
    



- **검색**

  ```c
  char szBuffer[32] = { "I am a boy." };

  strstr(szBuffer, "no_search");        // NULL 반환
  strstr(szBuffer, "am");               // 해당 요소가 있다면 그 첫번째 값의 주소값
  strstr(szBuffer, "boy");


  strstr(szBuffer, "boy") - szBuffer;   // 이러면 이제 인덱스를 반환하겠지
  ```





## 104. 동적 할당된 메모리 구조와 realloc()


- **메모리는 Page 단위로 관리된다. 그래서 자투리가 남을 수 있다.**
- 동적 할당을 요구하면 OS는 **이런 자투리를 고려해서** Page 단위로 계산된 `chunk(또는 block)`을 준다.
  - 예를들어 `malloc()`으로 12B 메모리를 요청하면 OS는 때로 16B를 줄 수도 있다.

- 꽤 느리다.
  - 왜냐? 마땅한 chunk를 찾아봐야 하니까
  - 고성능이 필요하면 웬만큼 받아놓고 알아서 관리해 사용하는게 좋을 정도.

- 그래서 **메모리 공간에 좀 낭비가 있더라도, `OS`가 볼 때 적당한 사이즈의 Chunk면 그냥 다시
  할당 해주는게 성능상 좋다.**


> [!nt]
>
> (윈도우같은 경우는)
> - `OS` 수준에서는 메모리를 4KB Page 단위로 관리
> - `CPU` 수준에서는 64KB Allocation 사이즈 단위로 관리
>   - 이것을 4KB 단위로 다시 잘라서 가상메모리 체계로 관리



- **종합해보면, 메모리를 할당 받아도 여유공간이 조금은 있을 수 있다.**

  => **`realloc()`을 통해 할당 받은 메모리의 크기를 조정**해 다시 할당 받을 수 있다.
  => (이렇게 증가 요청시)만약 인근의 자투리로 요구한 Chunk 크기를 채울 수 없으면 전혀 새로운 위치로
     다시 할당 받는 수밖에 없다.
     => **당연히 성능이 안좋겠다.**
     _즉,_

     - 주변에 여유공간이   충분 => 성공 => 같은 자리에서 공간만 늘림 => 주소값이 기존과 같다.
     - 주변에 여유공간이 불충분 => 실패 => 다른 자리로 이동          => 주소값이 달라진다.

  > [!nt]
  >
  > - "웬만하면 쓰지마라."
  > - "통제하기 난해한 위험을 감수하지마."
  > - "그냥 충분히 할당 받아놓고 잘 관리를 해라."





## 105. 대충 살피는 다중 포인터


> [!nt]
>
> - "현업에 별로 안쓰더라, 너무 힘빼지마."


- 그러나, 이정도는 좀 있다.

  ```c
  // 3개의 (문자)배열의 주소값을 갖는 포인터 변수의 배열이다.
  // 즉, 포인터가 3개 들어있는 배열!
  char* astrList[3] = { "Hello", "World", "String" };

  // %s는 문자열로 출력하는 표현
  %s, astrList[0];         // Hello,  "Hello"의 주소값
  %s, astrList[1];         // World,  "World"의 주소값
  %s, astrList[2];         // String, "String"의 주소값

  %s, astrList[0] +1;      // ello,    "Hello"의 주소값 + 1  => 주소연산
  %s, astrList[1] +2;      // rld,     "World"의 주소값 + 2  => 주소연산
  %s, astrList[2] +3;      // ing,    "String"의 주소값 + 3  => 주소연산

  %c, astrList[0][0];      // l,        "Hello" 배열의 0번째 요소 => 인덱싱 연산자
  %c, astrList[1][1];      // l,        "World" 배열의 1번째 요소 => 인덱싱 연산자
  %c, astrList[2][2];      // i,       "String" 배열의 2번째 요소 => 인덱싱 연산자
  ```




## 106. 다차원 배열에 대한 포인터


_다중 포인터와는 별개!_
_다중 포인터와는 별개!_



- 2차원 배열은 1차원 배열을 요소로 갖는 1차원 배열: 당연하다!

- 예시 **(중요)**

  ```c
  // char[12]를 요소로 갖는 길이 2개짜리 배열
  char aStrList[2][12]

  // 똑같은 것이다. 요소가 char[12]인 배열에 대한 포인터
  char (*pStrList)[12] = ...
  ```

  - `괄호`를 꼭 쳐줘야한다.


```c
a = malloc(sizeof(char[2][12]));
char b[2][12];

a = b;
free(a);
```




## 107. 정적 메모리와 기억부류 지정자


- 변수를 어디서 어떻게 선언하냐에 따라 값이 이용하는 메모리의 추상적 위치가 바뀐다.
- 기억부류 지정자(Storage-class specifier): **"메모리의 종류"**
  - `Stack`: 함수의 지역변수(자동변수)
  - `Heap`: `malloc()`
  - 실행코드(data section): 전역변수
  - 키워드
    | Specifier  | Scope         | Lifetime | Storage Location    | Default Initialization | Usage Example   |
    |:----------:|---------------|----------|---------------------|------------------------|-----------------|
    |  `extern`  | Global        | **Program**  | Data section        | Zero (if not set)      | `extern int x;`   |
    |  ~`auto`~   | Block (local) | Block    | Stack               | Garbage value          | `~auto~ int x;`     |
    |  `static`  | Block/Global  | **Program**  | Data section        | Zero (if not set)      | `static int x;`   |
    | `register` | Block (local) | **Block**    | CPU Register (hint) | Garbage value          | `register int x;` |

    - auto는 ~자동변수를 선언할 때 사용하는데~, 아무도 안쓰고 나도 쓰지말자.

    - register는 임베디드 레벨로 가야 쓴다.
      ```c
      register int i = 0;
      printf("%d\n", i);
      // printf("%p\n", &i);   // 레지스터는 주소가 없다. 이름 그 자체로 식별되는 저장장치
      ```


    - static은 **정적 메모리 영역**을 사용하겠다는 것. 

      > - **동시성 이슈** 주의: 전역변수와 마찬가지로 멀티쓰레딩 구조에서 주의해서 사용하자.
      > - static 지역변수 vs static 전역변수
      >   ````c
      >   // filepath: example.c
      >   #include <stdio.h>
      >   
      >   // static 전역 변수 선언 (파일 내 모든 함수에서 접근 가능, 다른 파일에서는 접근 불가)
      >   static int global_static_var = 10;
      >   
      >   void foo() {
      >       // static 지역 변수 선언 (이 함수 내에서만 접근 가능, 함수가 끝나도 값 유지)
      >       static int local_static_var = 0;
      >       local_static_var++;
      >       printf("local_static_var: %d\n", local_static_var);
      >   }
      >   
      >   int main() {
      >       printf("global_static_var: %d\n", global_static_var); // 접근 가능
      >       foo(); // local_static_var: 1
      >       foo(); // local_static_var: 2
      >       foo(); // local_static_var: 3
      >       return 0;
      >   }
      >   ````
      >     - static **지역**변수: **선언한 함수 내에서만 접근 가능, 함수가 끝나도 값 유지**
      >     - static **전역**변수: **파일 내 모든 함수에서 접근 가능, 다른 파일에서는 접근 불가**




- 각종 메모리들이 어디에 저장되는지 살펴보기

  - 가상 메모리 구조
  ```
  [ 높은 주소 ]
  --------------------------------------
  | 스택(Stack)                        | 자동변수, 보통 1MB
  --------------------------------------
  | ...                                | guard page: 충돌 방지
  --------------------------------------
  | 힙(Heap)                           | 동적 할당 메모리, ex) malloc()
  --------------------------------------
  | 데이터 영역                        |
  | 초기화/비초기화 데이터             |
  --------------------------------------
  | 실행코드: Text section             | 실행코드 기계어, ex) main(), foo()
  |     //  : Data section, read only  | 문자열 상수, ex) "Hello World"
  |     //  :     //      , read write | 정적 메모리, ex) 전역변수
  --------------------------------------
  [ 낮은 주소 ]
  ```

  - File Path: 107.c
    ```c
    #include <stdio.h>
    
    int TestFunc(void) {
        int x = 5;
        static int nData = 10;                  // 학습을 위해 작성했지, 좋은 코드가 아니다. 함수 내부에 static 변수 선언은 지양하자.
        ++nData;
        return nData;
    }
    
    int main(int argc, char *argv[]) {
        char *hoho = "hell";                   // (아래참고) 'hoho에 식별자에 대입된 주소' VS 'hoho가 저장된 메모리 주소'의 위치 찾기
        printf("%d\n", TestFunc());
        printf("%d\n", TestFunc());
        printf("%d\n", TestFunc());
        return 0;
    }
    ```

    - debugging
      ```asm
      pwndbg> x/xg &hoho                       // hoho의 주소를 출력해보면
      0x7fffffffc518: 0x0000555555556004       // <주소값>: <담겨있는것> 형태로 나온다.

      pwndbg> x/s 0x0000555555556004           // <담겨있는것>에 해당하는 주소가 가리키는 곳을 확인해보면 "hell" 문자열이 잘 출력된다.
      0x555555556004: "hell"

      pwndbg> p &nData                         // static 변수는 전역변수와 취급이 비슷하댔는데 어디 저장되어있나 보자.
      $26 = (int *) 0x555555558018 <nData.0>

      // 이때, hoho 식별자의 주소와 식별자의 메모리에 할당 된 값(주소값)을 비교해보면 아래와 같다.
      // 1. hoho 식별자의 주소                     : 0x7fffffffc518
      // 2. hoho 식별자의 메모리에 할당 된 값(주소): 0x0000555555556004
      // 3. static 지정자로 선언한 변수            : 0x555555558018
      //
      // 이들을 아래 가상메모리 맵에서 확인해보자.

      pwndbg> vmmap
                   Start                End Perm     Size Offset File (set vmmap-prefer-relpaths on)
          0x555555554000     0x555555555000 r--p     1000      0 107
          0x555555555000     0x555555556000 r-xp     1000   1000 107
          0x555555556000     0x555555557000 r--p     1000   2000 107                                      // <2.>은 read only 영역에 쓰였다.
          0x555555557000     0x555555558000 r--p     1000   2000 107
          0x555555558000     0x555555559000 rw-p     1000   3000 107                                      // <3.>은 read/write 영역에 쓰였다.
          0x7ffff7c00000     0x7ffff7c24000 r--p    24000      0 /usr/lib/libc.so.6
          0x7ffff7c24000     0x7ffff7d96000 r-xp   172000  24000 /usr/lib/libc.so.6
          0x7ffff7d96000     0x7ffff7e05000 r--p    6f000 196000 /usr/lib/libc.so.6
          0x7ffff7e05000     0x7ffff7e09000 r--p     4000 204000 /usr/lib/libc.so.6
          0x7ffff7e09000     0x7ffff7e0b000 rw-p     2000 208000 /usr/lib/libc.so.6
          0x7ffff7e0b000     0x7ffff7e13000 rw-p     8000      0 [anon_7ffff7e0b]
          0x7ffff7f9a000     0x7ffff7f9f000 rw-p     5000      0 [anon_7ffff7f9a]
          0x7ffff7fba000     0x7ffff7fbe000 r--p     4000      0 [vvar]
          0x7ffff7fbe000     0x7ffff7fc0000 r--p     2000      0 [vvar_vclock]
          0x7ffff7fc0000     0x7ffff7fc2000 r-xp     2000      0 [vdso]
          0x7ffff7fc2000     0x7ffff7fc3000 r--p     1000      0 /usr/lib/ld-linux-x86-64.so.2
          0x7ffff7fc3000     0x7ffff7fed000 r-xp    2a000   1000 /usr/lib/ld-linux-x86-64.so.2
          0x7ffff7fed000     0x7ffff7ffb000 r--p     e000  2b000 /usr/lib/ld-linux-x86-64.so.2
          0x7ffff7ffb000     0x7ffff7ffd000 r--p     2000  39000 /usr/lib/ld-linux-x86-64.so.2
          0x7ffff7ffd000     0x7ffff7ffe000 rw-p     1000  3b000 /usr/lib/ld-linux-x86-64.so.2
          0x7ffff7ffe000     0x7ffff7fff000 rw-p     1000      0 [anon_7ffff7ffe]
          0x7ffffffdc000     0x7ffffffff000 rw-p    23000      0 [stack]                                 // <1.>는 스택 영역에 위치한다.(r/w)
      0xffffffffff600000 0xffffffffff601000 --xp     1000      0 [vsyscall]
      ```

