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

- `free()` 자체는 주소만 필요로 한다. 별도로 크기가 없어도 해제 가능



## 112. 재귀호출


- 호출 시 마다 콜스택이 쭉쭉 쌓여간다.



## 113. 문자열 처리 - 주요 함수 소개 및 strcat() 함수 성능 개선


https://www.inflearn.com/courses/lecture?courseId=331984&type=LECTURE&unitId=183192&subtitleLanguage=ko&tab=curriculum



## 114. 가변 길이 입력에 의한 Stack frame 손상

ho


## 115. 유틸리티 함수 - atoi()

ho


## 116. 유틸리티 함수 - time(), localtime(), ctime()

ho


## 117. 유틸리티 함수 - srand(), rand()

ho


## 118. 유틸리티 함수 - system(), exit()

ho


## 

ho


