# Section 20. 변수와 상수 고급 이론





## 137. 형한정어와 컴파일러 최적화 소개

_Type Qualifier_

- 변수에 적용하는 문법으로 컴파일러 **최적화에 관련**


- `const`
  : 상수에 이름(심볼)을 부여해서 사용할 때

  - 변수를 상수화
  - 개념상 읽기 전용으로 만듦
  - 변수의 개수가 줄어들기 때문에 최적화에 도움이 됨
    - 많이 쓸수록 좋다. 실력에 척도로 볼 정도
    - 당연히 더 까다로워진다.

  - ex)
    ```c
    // 이 코드는 불필요하게 변수를 많이 쓰는 것일 수 있다.
    int nData, a, b;
    a = 3;
    b = 3;
    nData = a + b;
    printf("%d\n", nData);


    // 컴파일러 최적화를 적용하면 아래처럼 될 수 있다.
    printf("%d\n", 3 + 4);
    ```
    


- `volatile`
  : 최적화 대상에서 제외해라



## 138. 심볼릭 상수


- "Symbolic 하다"

- 프로그램 내부에서 특별한 의미를 갖는 기준 **값에 대해 이름을 부여**
  - 읽기 좋은 코드를 만드는 방법의 하나
  - Python으로치면 `enum`, Typescript로 치면 `as const`


- 방법
  - `const`
  - `#define`
  - 비교
    | 특징        | `const`                                              | `#define`                                                         |
    |-------------|------------------------------------------------------|-------------------------------------------------------------------|
    | 처리 시점   | 컴파일 완료된 코드에 존재 (런타임에 메모리에 할당됨) | 컴파일 과정에서 전처리기로 처리됨 (텍스트 치환)                   |
    | 타입 체크   | 수행됨 (컴파일러가 타입을 확인)                      | 수행되지 않음 (단순 텍스트 치환으로 타입 오류를 잡지 못함)        |
    | 메모리 사용 | 메모리를 차지하며, 디버깅 시 값 확인 가능            | 메모리를 차지하지 않음 (치환 후 사라짐), 디버깅 어려움            |
    | 스코프      | 변수처럼 스코프를 가짐 (블록, 함수 등)               | 전역적이며, 스코프 개념 없음 (언디파인드할 때까지 유지)           |
    | 사용 예시   | `const int x = 10;` (상수 변수)                      | `#define PI 3.14` (매크로 상수)                                   |
    | 장점        | 안전성 높음, 타입 안전                               | 간단한 치환에 유용, 조건부 컴파일 가능                            |
    | 단점        | 약간의 메모리 오버헤드                               | 디버깅 어려움, 예상치 못한 부작용 가능 (예: 연산자 우선순위 문제) |



## 139. 상수형 포인터

_C언어의 참조형(포인터)는 참조 대상이 달라질 수 있다는 한계가 있다.
이것을 문법적으로 보완하는 방법._

### 포인터 변수 자체를 상수화

_포인터 변수는 재할당할 수 없지만, 가리키는 데이터는 수정 가능._

- 예제)
  ```c
  char str[] = "Hello";           // 수정 가능한 배열
  char* const ptr = str;          // 포인터 불변: ptr 자체는 재할당 불가

  // 가능: 가리키는 데이터 수정
  ptr[0] = 'h';                   // OK: "hello"로 변경
  printf("%s\n", ptr);            // 출력: hello

  // 불가능: 포인터 재할당 (컴파일 에러)
  // ptr = "World";               // 에러: Cannot assign to variable 'ptr' with const-qualified type
  ```


### 포인터가 가리키는 대상 상수화

_포인터는 재할당할 수 있지만, 가리키는 데이터는 수정 불가._

- 예제)
  ```c
  char str[] = "Hello";         // 수정 가능한 배열
  const char* ptr = str;        // 값 불변: 가리키는 데이터 수정 불가

  // 불가능: 가리키는 데이터 수정 (컴파일 에러)
  // ptr[0] = 'w';              // 에러: Read-only variable is not assignable

  // 가능: 포인터 재할당
  ptr = "World";                // OK: 이제 "World"를 가리킴
  printf("%s\n", ptr);          // 출력: World
  ```



### 여전히 한계는 있다. 주의 할 것

- A _wrong_ use of the `const` qualifier
  ````c
  const int n = 10;           // n is read-only
  int *ptr = (int*)&n;        // Cast away constness (dangerous)
  *ptr = 20;                  // Modifying const variable via pointer is UB
  printf("%d %d\n", *ptr, n); // Output unpredictable (e.g., 20 10 or crash)
  ````

**Key Points**:
- `const` makes variables immutable; modifying them **violates C standards.**
- Casting to non-const bypasses protection, leading to UB (compilers can't always detect it).
- Avoid this; **use `const` only for truly constant values.** If changes are needed, declare without `const`.



## 140. 열거형 상수


- 심볼릭 상수를 여러개 한번에 정의
- 정수 값으로 계산 될 수 있음

- `switch-case` 문법과 결합해 읽기 좋은 코드 만들기 좋음
  - 예시)
    ```c
    enum ACTION { MOVE, JUMP, ATTACK };                    // 내부적으로는 0, 1, 2로 평가됨 
    typedef enum COLOR { RED = 100, GREEN, BLUE } COLOR;   // 100, 101, 102로 평가됨

    void DoAction(enum ACTION act) {
        switch (act) {
            case MOVE:
                // do something
                break;

            default:
                // do something
                break;
        }
    }
    ```




## 141. 형 재선언


- `typedef`

- **새로운 자료형**을 선언하는 문법

  - 기존 타입을 묶어 하나의 새로운 타입으로 만들고, 타입의 이름을 부여한다.
    - 그러니까 구조체, 공용체, 열거형 상수에 많이 쓰지

  - C언어가 제공하는 기본 형식의 이름을 바꾸는 용도로도 쓴다.

  - **남발시 읽기 어려운 코드가** 될 우려가 발생
    - 예시)
      ```C
      typedef char* LPSTR;

      LPSTR myVar = NULL;   // 이런식으로 하면 *아이콘이 없으니 이게 포인터인지 뭔지 바로 알기 어렵다.
      ```
  



## 142. extern


- `extern`


- 외부변수선언시 사용되는 문법
- 한 프로젝트 내부에 여러 C파일이 잇을 경우 다른 `.c`파일에 정의된 전역변수에 접근하기 위해 선언
  - 외부변수로 선언하면 알아서 적용된다.

  - 예시)
    ````c
    // file1.c
    int global_var = 10; // 전역 변수 정의

    // file2.c
    extern int global_var; // 외부변수 선언 (file1.c에 정의됨)
                           // 여기서 extern을 혹여 생략해도 알아서 적용된다는 뜻
    ````



- `static`은 참조 범위를 파일 내로 좁힌다.
  - `static`에는 `extern`을 붙일 수 없다.

  - 캡슐화(은닉화): `extern`이 외부파일에서 변수를 찾을 수 있는 이유는 심볼을 보기때문인데, static을 붙여주면
    심볼이 없다.
