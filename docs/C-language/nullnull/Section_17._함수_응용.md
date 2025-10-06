# 섹션 17. 함수 응용





## 108. 매개변수 전달 기법


- 함수가 받는 매개변수는

  - Register 영역 사용에 저장된다.

    - 32bit 시스템은 Stack 영역에 저장된다.

  - callee가 받을 때, **오른쪽 매개변수부터 받는다.**


### call by value

- 매개변수로 **값을 전달**

- 실습) File Path: 108_2.c
  ```c
  int Add(int a, int b) {
      int c = 0;
      c = a + b; // edx와 eax에 값을
      return c;  // eax에 저장되었다.
  }
  /*   0x555555555119 <Add>                           pushq  %rbp */
  /*   0x55555555511a <Add+1>                         movq   %rsp, %rbp            RBP => 0x7fffffffc4e0  0x7fffffffc510  0x7fffffffc5b0  0x7fffffffc610  ... */
  /*   0x55555555511d <Add+4>                         movl   %edi, -0x14(%rbp)     [0x7fffffffc4cc] <= 5 */
  /*   0x555555555120 <Add+7>                         movl   %esi, -0x18(%rbp)     [0x7fffffffc4c8] <= 0xa */
  /* ► 0x555555555123 <Add+10>                        movl   $0, -4(%rbp)          [0x7fffffffc4dc] <= 0 */
  /*   0x55555555512a <Add+17>                        movl   -0x14(%rbp), %edx     EDX, [0x7fffffffc4cc] => 5 */
  /*   0x55555555512d <Add+20>                        movl   -0x18(%rbp), %eax     EAX, [0x7fffffffc4c8] => 0xa */
  /*   0x555555555130 <Add+23>                        addl   %edx, %eax            EAX => 15 (0xa + 0x5) */
  /*   0x555555555132 <Add+25>                        movl   %eax, -4(%rbp)        [0x7fffffffc4dc] <= 0xf */
  /*   0x555555555135 <Add+28>                        movl   -4(%rbp), %eax        EAX, [0x7fffffffc4dc] => 0xf */
  
  int main(int argc, char *argv[]) {
      int data = 0;
      data = Add(5, 10);
  
      return 0;
  }
  /*   0x555555555150 <main+22>    movl   $0xa, %esi       ESI => 0xa */
  /*   0x555555555155 <main+27>    movl   $5, %edi         EDI => 5 */
  /* ► 0x55555555515a <main+32>    callq  Add                         <Add> */
  /*        rdi: 5 */
  /*        rsi: 0xa */
  ```

  - ```c
    data = Add(5, 10);
    ```
    - esi, edi를 통해 인자 전달

  - ```c
    int Add(int a, int b) {
    ```
    - 전달받은 인자를 자신의 스택 영역에 복사
    - 그것들을 edx, eax에 복사 및 계산하여 eax에 저장
    - 계산되어 eax에 저장된 값을 변수 `c`에 대입하기 위해 `c`가 위치한 스택 영역에 복사
    - 변수 `c`를 return하기 위해 eax에 복사


### call by reference

- 매개변수로 **주소를 전달**

- 언제?
  - 인자를 많이 전달해야할 때
  - 결과를 많이 받아야 할 때

- 왜?
  : call by value의 예제처럼 복사를 엄청 한다. 이게 다 비용이다.
  




## 109. [필수 실습 문제] MyStrcpy() 함수 작성하기

문제) 아래 코드에 MyStrcpy 함수를 구현해라

```c
int main(int argc, char* argv[]){
    char szBufferSrc[12] = {"TestString"};
    char szBufferDst[12] = {0};

    MyStrcpy(szBufferDst, sizeof(szBufferDst),szBufferSrc);
    puts(szBufferDst);
    return 0;
}
```


### 정답

- File Path: 109.c
  ```c
  #include <stdio.h>
  #include <string.h>
  
  void MyStrcpy(char *, unsigned int, char *);
  
  int main(int argc, char *argv[]) {
      char szBufferSrc[12] = {"TestString"};
      char szBufferDst[12] = {0};
  
      MyStrcpy(szBufferDst, sizeof(szBufferDst), szBufferSrc);
      puts(szBufferDst);
      return 0;
  }
  
  void MyStrcpy(char *dst, unsigned int dstSize, char *src) {
      size_t lenSafeCopy =
          strlen(src) < (dstSize - 1) ? strlen(src) : (dstSize - 1);
  
      for (int i = 0; i < lenSafeCopy; ++i)
          dst[i] = src[i];
  
      dst[lenSafeCopy] = '\0';
  }
  ```


## 110. Stack frame과 지역변수 주소 반환 문제


- callee의 지역변수는 제어권 반환(`return, ret`)과 함께 모두 소멸

  - **이때 (소멸 예정인) 지역변수의 주소값을 caller에 반환하는 것은 심각한 오류**

    ```c
    int* TestFunc(void) {
      int nData = 10;
      return &nData;            // 이렇게 지역변수(자동변수)의 주소값을 리턴하면 안된다.
    }

    int main(void){
      int* pnResult = NULL;
      pnResult = TestFunc();    // 실제로는 신뢰할 수 없는 쓰레기 값이 들어있다.
    }
    ```


  - heap 영역에 있다면 괜찮다. `free()`하지 않으면 그대로 있을테니까.



## 111. Call by reference와 메모리 동적 할당 이슈


1. callee가 동적 할당 받은 메모리를 반환한다면, caller가 언젠가 `free()` 해줘야 한다.
2. 이때 callee가 주소값만 반환한다면, caller는 해당 메모리 영역의 크기를 모른다.
3. 그니까 **크기를 몰라도 되는 경우가 아니라면, 할당받은 메모리의 크기까지 같이 반환해서 잘 관리하자.**

   - 배열의 크기를 모르면 순회를 얼만큼 해야하는지 모르니까 써먹을수가 없지
   - `free()` 자체는 주소만 필요로 한다. 별도로 크기가 없어도 해제 가능




## 112. 재귀호출


- 호출 시 마다 콜스택이 쭉쭉 쌓여간다.



## 113. 문자열 처리 - 주요 함수 소개 및 strcat() 함수 성능 개선


Parser
자료구조(선형, 비선형)



- `strpbrk()`

  - 문자열 쪼개고 포인터로 받기

  - 예제) File Path: 113_2.c
    ```c
    #include <stdio.h>
    #include <string.h>
    
    int main(void) {
        char str[] = "hello, world!";
        const char *delims = ",! ";
        char *p = str;
    
        while ((p = strpbrk(p, delims)) != NULL) {
            printf("발견된 구분자: '%c' (인덱스: %ld)\n", *p, p - str);
            p++; // 다음 문자부터 다시 검색
        }
    
        return 0;
    }
    ```

    - 이놈은 원본 메모리에 '쓰기'하지 않는다.
    - 그냥 delimiter로 나뉘어지는 부분의 주소값을 리턴해준다.


---

- `strcat()`

  - 문자열 합치기

  - 예제)
    ````c
    #include <stdio.h>
    #include <string.h>

    int main(void) {
        char result[100] = ""; // 결과를 저장할 충분히 큰 배열
        const char *words[] = {"hello", ", ", "world", "!", NULL};
        int i = 0;

        while (words[i] != NULL) {
            strcat(result, words[i]);
            i++;
        }

        printf("합쳐진 문자열: %s\n", result);

        return 0;
    }
    ````





---


- `strtok()`

  - 문자열 쪼개는데 원본 값 훼손

  - 예제) File Path: 113.c
    ```c
    #include <stdio.h>
    #include <string.h>
    
    int main(void) {
        char szBuffer[256] = {"nData = x + y;\nnResult = a * b"};
        const char *pszSep = " *+=;\n";
        char *pszToken;
        char *pszNextToken;
    
        pszToken = strtok_r(szBuffer, pszSep, &pszNextToken);
        while (pszToken != NULL) {
            puts(pszToken);
            pszToken = strtok_r(NULL, pszSep, &pszNextToken);
        }
    
        printf("\nszBuffer: %s\n", szBuffer);   // szBuffer: nData
    
        return 0;
    }
    ```

    - `pszToken = strtok_r(NULL, pszSep, &pszNextToken);`
      - NULL을 사용해도 strtok은 내부적으로 static 변수를 사용해서 괜찮다. 알아서 남은 부분을 이어서
        작업한다.
    - `pszNextToken`에 남은 부분의 주소가 복사된다.
    - 원본이었던 szBuffer에 값을 **쓰기(Write)**한다. pszSep에 해당하는 것 마다 공백문자를 넣어서.
      ```asm
      3: hexdump 0x00007fffffffc3f0
      +0000 0x7fffffffc3f0  6e 44 61 74 61 00 3d 20  78 00 2b 20 79 00 0a 6e  │nData.=.│x.+.y..n│
      +0010 0x7fffffffc400  52 65 73 75 6c 74 00 3d  20 61 00 2a 20 62 00 00  │Result.=│.a.*.b..│
      ```




## 114. 가변 길이 입력에 의한 Stack frame 손상

교육적 목적으로 보안 결함이 있는 gets같은 함수를 사용한다고 가정해보자.
아래의 경우, 사용자가 7글자가 아닌 8글자를 입력해버리면 `printf`의 결과는 `<입력값>11223300`이 나올 수 있다.
왜 44대신 00으로 바뀌어 나올까? 

그것은 지역변수가 Stack영역을 함께 쓰기 때문이다. 이런 경우를 **Buffer Overflow**라 한다.


- 예제)
  ```c
  #include <stdio.h>
  
  void getString(void) {
      char szBuffer[8] = {0};
      int nData = 0x11223344;
      gets(szBuffer);         // 사용자 입력 받는 함수. 보안 결함이 있다. 
      /* fgets(szBuffer, sizeof(szBuffer), stdin); */
  
      printf("%s, %08X\n", szBuffer, nData);
      return;
  }
  
  int main(void) {
      getString();
      return 0;
  }
  ```


## 115. 유틸리티 함수 - atoi()


- `atoi()`: 문자열을 숫자로 변경
  - 이렇게 형변환이 일어나는것을 사용할 땐 항상 **타입 크기를 유의**해라

- `itoa()`: 숫자를 문자열로 변환  
  - 사용 예: `itoa(123, str, 10);` → "123"

- `strlen()`: 문자열의 길이 반환  
  - 사용 예: `strlen("hello")` → 5

- `strcpy()`, `strncpy()`: 문자열 복사  
  - 사용 예: `strcpy(dest, src);`

- `strcat()`, `strncat()`: 문자열 이어붙이기  
  - 사용 예: `strcat(dest, src);`

- `strcmp()`, `strncmp()`: 문자열 비교  
  - 사용 예: `strcmp(str1, str2);`

- `memset()`: 메모리 영역을 특정 값으로 초기화  
  - 사용 예: `memset(arr, 0, sizeof(arr));`

- `memcpy()`: 메모리 복사  
  - 사용 예: `memcpy(dest, src, n);`

- `abs()`: 정수의 절댓값 반환  
  - 사용 예: `abs(-10)` → 10

- `toupper()`, `tolower()`: 문자 대소문자 변환  
  - 사용 예: `toupper('a')` → 'A'


## 116. 유틸리티 함수 - time(), localtime(), ctime()

2038년...!


## 117. 유틸리티 함수 - srand(), rand()

필요할 때 잘 찾아보고 쓰자...


## 118. 유틸리티 함수 - system(), exit()

- `system()`: 시스템에 명령을 전달하고 수행
- `exit()`: 프로세스 종료
