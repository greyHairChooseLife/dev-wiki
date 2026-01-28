# Section 22. 함수에 대한 고급 이론




## 147. 함수 포인터

- 함수의 주소를 저장하는 포인터.
- 선언: `반환형 (*포인터이름)(매개변수 타입들);`
  - 예: `int (*func_ptr)(int, int);`
- 사용: 함수를 가리키고 호출 가능.
  - 예:
    ```c
    int add(int a, int b) { return a + b; }
    int (*fp)(int, int) = add;
    int result = fp(3, 4);  // result = 7
    ```

- 유용: 동적 함수 호출, 콜백 등.

  - 사례 1: qsort 같은 라이브러리 함수에서 비교 함수 콜백.
    - `qsort(array, size, sizeof(int), compare_func);` (compare_func은 `int (*)(const void*, const void*)`)

  - 사례 2: 메뉴 시스템에서 명령 선택.
    - 함수 포인터 배열로 메뉴 항목에 함수 할당, 사용자 입력으로 호출.

  - 사례 3: 플러그인 아키텍처.
    - 동적으로 함수 로드하여 실행.



## 148. 역호출 구조

- 콜백(callback): 함수가 다른 함수를 인자로 받아 호출하는 구조.
- 함수 포인터를 매개변수로 사용.
- 예: 이벤트 처리나 라이브러리에서 사용자 정의 함수 호출.
  - 예:
    ```c
    void process(int x, void (*callback)(int)) {
        // some processing
        callback(x);
    }
    void my_callback(int val) {
        printf("Callback called with %d\n", val);
    }
    // 사용: process(10, my_callback);
    ```
- 장점: 유연성, 모듈화.



## 149. Lookup 테이블과 역호출 결합

- 룩업 테이블: 함수 포인터 배열로, 인덱스로 함수 선택.
- 콜백과 결합: 동적 행동 선택.
- 예: 명령어 처리나 상태 머신.
  - 예:
    ```c
    typedef void (*CommandFunc)(void);
    CommandFunc commands[] = {cmd1, cmd2, cmd3};
    void execute_command(int index) {
        if (index < sizeof(commands)/sizeof(commands[0])) {
            commands[index]();
        }
    }
    ```

- 효율적: switch 대신 빠른 접근.
