# Section 21. 전처리기




## 143. #include

- 전처리의 전은 **컴파일 전**


- `#` 기로호 시작
  - 산더미처럼 많다.
    - ex) `#pragma`


- 뭘하냐?
  - 헤더 포함
  - 조건부 컴파일
  - 심볼릭 상수 정의
  - 매크로 정의


- `#include <무엇.h>`

  - `< >` 이건 컴파일러가 정의하고 있는 `시스템 헤더 파일(특정 디렉토리에 존재)`에서 검색한다는 것
    - `<무엇>` 부분에 꺽쇠 없이 절대경로, 상대경로를 쓸 수도 있다.
    - `" "` 이렇게 하면 현재경로에서 찾는다.


- `#pragma once`
  - `#include` 중복을 막기 위한 전처리기



## 144. #define과 매크로 정의


- `#define`

  - 심볼릭 상수 만드는 전처리기
  - 컴파일 전에 적용되어 소스코드를 치환(replace)한다.

  - **매크로**를 정의할때도 쓴다.
    - 매크로는 함수처럼 보여도 함수가 아님
    - **(함수처럼 호출해도) 호출에 의한 오버헤드가 없음**
      - 컴파일러 최적화가 너무 좋아져서 **사용 빈도가 현저히 줄어듦**(어지간하면 필요 없다.)

    - 이미 만들어져 있는 것만 잘 갖다 써라.

### Common Pitfalls

- case 1
  ```c
  #define MAX_MACRO(a, b) ((a) > (b) ? (a) : (b))       // Macro: simple but risky

  void main() {
      int x = 5, y = 10;
      printf("Max: %d\n", MAX_MACRO(x, y));             // Works: 10

      // Pitfall 1: Side effects - increments x twice!
      int result = MAX_MACRO(x++, y);
      printf("Result: %d, x: %d\n", result, x);         // Output: 10, x=7 (unexpected)
  }
  ```


- case 2
  ```c
  #define  BAD_MAX(a, b) a > b ? a : b               // No outer parens!
  #define GOOD_MAX(a, b) ((a) > (b) ? (a) : (b))

  void main() {
      int x = 5, y = 10, z = 3;

      printf("Bad Max: %d\n", BAD_MAX(x, y));  // 10, This works as expected here


      // Pitfall: Precedence issue in larger expression
      // Expands to: 2 * x > y ? x : y  (due to * having higher precedence than >)
      // So: 2 * 5 > 10 ? 5 : 10 → 10 > 10 ? 5 : 10 → 10 (wrong! Should be 2 * 10 = 20)
      int result = 2 * BAD_MAX(x, y);
      printf("Result: %d (expected 20)\n", result);  // Output: 10


      // Good macro with full parentheses avoids this
      int good_result = 2 * GOOD_MAX(x, y);  // Expands to 2 * ((x) > (y) ? (x) : (y))
      printf("Good Result: %d\n", good_result);  // 20
  }
  ```



### 요새 최적화는 `inline`으로

- How it works
  Unlike macros (preprocessor text replacement), inline is handled **during compilation**.
  The compiler embeds the code, making it faster for small, frequently called functions.

- Benefits
  **Eliminates call overhead;** improves performance in tight loops.

- Limitations
  **Not guaranteed;** can increase binary size if overused.
  More over **Modern compilers inline automatically** with optimizations enabled.

- Example:
  ```c
  inline int square(int x) {            // Hint to inline
      return x * x;
  }

  int main() {
      int result = square(4);           // May be inlined as: int result = 4 * 4;
      printf("Square: %d\n", result);   // Output: 16
      return 0;
  }
  ```



## 145. 매크로 특수화 연산자

_매크로에서 사용하는 특수 연산자들_
_이제는 딱히 안쓰고 레거시 코드를 이해할 수 있는 정도로만 하자._


- `#` (문자열화 연산자): 매크로 인자를 문자열로 변환.
  - 예: `#define STRINGIFY(x) #x` → `STRINGIFY(hello)`는 `"hello"`로 치환.


- `##` (토큰 연결 연산자): 두 토큰을 연결.
  - 예: `#define CONCAT(a, b) a##b` → `CONCAT(var, 1)`은 `var1`로 치환.




## 146. 조건부 컴파일


- 컴파일 시 특정 조건에 따라 코드를 포함하거나 제외

  - `#ifdef SYMBOL`: SYMBOL이 정의되어 있으면 코드 포함
  - `#ifndef SYMBOL`: SYMBOL이 정의되지 않았으면 코드 포함
  - `#if CONDITION`: CONDITION이 참이면 코드 포함 (숫자 표현식 가능)
  - `#else`: 위 조건이 거짓일 때 코드 포함
  - `#elif CONDITION`: else if처럼 사용
  - `#endif`: 조건부 블록 끝


- 활용

  - 디버그 모드에서만 코드 실행
    `#ifdef DEBUG printf("Debug mode\n"); #endif`

  - 플랫폼별 컴파일이나 헤더 중복 방지에 유용
    ```c
    #ifdef _WIN32
        // Windows-specific code
        #include <windows.h>
        printf("Running on Windows\n");
    #elif defined(__linux__)
        // Linux-specific code
        #include <unistd.h>
        printf("Running on Linux\n");
    #else
        // Other platforms
        printf("Unknown platform\n");
    #endif
    ```


