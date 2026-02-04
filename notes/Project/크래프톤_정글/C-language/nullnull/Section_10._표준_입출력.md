# 섹션 10. 표준 입/출력

## 33. 콘솔(Console)이란 무엇일까?

  - kernel 영역에 입출력 장치와 연결된 영역이 있다.
  - 이 영역에 접근할 수 있도록 user 영역에 추상화된 계층을 제공하는데, 그것이 File이다.
  - 결국 이 File에 Read / Write / eXecute 하는게 프로세스다.

  - 예시,

    하드웨어 입력장치에 입력(키보드 누르기)
    -> interrupt 발생
    -> 커널 영역의 '어딘가'에 입력
    -> 입출력 버퍼(메모리)
    -> (출력 등 관련된 놈들이 read해서 이용)


## 34. 문자 입/출력

```c
/* buffered I/O */
ch = getchar(); /* I/O 버퍼(que)에서 최근 값 하나 가져와서 'ch' 메모리에 저장 */
putchar(ch);    /* 'ch' 메모리에서 값을 가져다가 I/O 버퍼에 넣기 */
putchar('Z');   /* 원시값 하나를 I/O 버퍼에 넣기 */
```

```c
/* Non-buffered I/O */
/* 하드웨어 입력을 버퍼 거치지 않고 걍 가져와버림 */
_getch()
_getche()
```




## 35. 문자열 입/출력

- 문자열은 0x00로 끝난다.




## 36. gets() 함수와 보안 결함 (feat. 시큐어 코딩)

- ~시큐어코딩 시작부터 해라~
- `gets(<주소>)`는 경계를 벗어난 쓰기를 수행할 수 있다. => 버퍼 오버플로우
  - `gets_s(<주소>, <길이>)`로 개선되었다. `<길이>`만큼 이후의 입력값은 버리는 것
  - `fgets(<주소>, <길이>, stdin)` linux, mac에서 사용



## 37. 형식 문자와 이스케이프 시퀀스

- 메모리에 입력된 것은 단순한 2진수
- print할 때 그 값을 어떤 형식으로 출력할지 정해줘야함
- 일부 특수문자를 위한 escape가 있음



## 38. 실수 출력

```c
/* F는 float */
pritnf("%f", 123.456F);  // float

/* double을 실수형식으로 출력 */
pritnf("%f", 123.456);   // double
pritnf("%.2f", 123.456);   // double 소수점 아래 몇 2번째 자리까지 표현
pritnf("%lf", 123.456F); // long double
pritnf("%g", 123.456F);  // long double


pritnf("%자리수.자리수", <실수>);
```


- 정수부에 자리수를 맞추서 표현하는 법
```c
pritnf("%05d", 3);   // 00003 출력된다.
```




## 39. 문자, 정수 입력과 개행문자 제거


- 기본적인 문자 입력

  ```c
  char ch = 0;             // ch라는 이름(심볼)의 메모리를 사용할거고, 거기다 0을 할당해라
  scanf_s("%c", &ch, 1);   // ch라는 이름(심볼)에 맵핑된 주소 위치에 character를 입력할거고, 1개 입력한다
  ```


  > [!NOTE] scanf_s는 C99 표준이 아니다.
  >
  > ```md
  > **Diagnosis:**  
  > The error occurs because `scanf_s` is not a standard C function in ISO C99. It is a
  > Microsoft-specific extension. On Linux and with standard C, you should use `scanf` instead. Also,
  > when reading a character, you should pass the address of the variable.
  > 
  > **Pseudocode plan:**
  > - Replace `scanf_s` with `scanf`.
  > - Ensure you pass the address of the variable (e.g., `&ch`).
  > - If `ch` is an array, ensure the format string and arguments are correct.
  > ```



- 입력이 들어오면 '개행문자'까지 I/O 버퍼에 들어온다.

  ```c
      char ch[4] = {0};
      scanf("%4c", ch);   // ban 입력
  ```

  - ban세개만 입력하고 엔터키까지 쳐서 `\n`(`ASCII 0x0a`)이 입력된 모습이다.

  ```hexdump
  pwndbg-lldb> hexdump &ch 8
  +0000 0x7ffc9cb0ed24  62 61 6e 0a 00 a4 7e 03                           │ban...~.│        │
  ```


  - 그런데 입력을 만약 두번 받는다면, 첫번째 입력의 개행문자 때문에 두번째 입력은 곧바로 \n가
    stdin으로 들어온것으로 인식한다. 같은 I/O 버퍼를 쓰니까. 그래서 아래처럼 마지막 하나를
    없애줘야한다.

    `%*c`를 활용한다.

    ```c
        int age = 0;
        /* scanf("%d", age);      이렇게 하면 안되고 */   
        scanf("%d%*c", age);      // %*c로 메모리에서 char 하나 버려준다.

        char name[32] = {0};
        fgets(name, sizeof(name), stdin);
    ```






## 40. 형식 문자 기반 문자열 입력


(misc)


## 41. (필수 실습 문제) 나이와 이름 입/출력 하기


```
/home/sy/jg/dokk-C/실습/10-41.c
```


