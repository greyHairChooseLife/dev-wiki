# Section 19. 파일 입/출력





## 127. 파일 시스템 기본 이론 - 첫 번째

- 보조 기억장치(2차 메모리)를 관리하는 방법으로 파일시스템(추상화) 사용
- 경로에는 절대경로, 상대경로, 현재경로가 있다.


## 128. 파일 시스템 기본 이론 - 두 번째

- `File`은 프로세스의 접근 대상

  - 복수의 프로세스가 하나의 파일에 접근할 경우 "데이터가 엉키는 경우"가 있음
    - 데이터가 엉키는 경우란 두 프로세스가 동시에 한 파일에 '쓰기'하는 경우라던가

  - 그래서 OS가 파일에 대한 프로세스의 접근을 통제함
    - 예를들면 이미 어떤 프로세스가 어떤 파일에 접근한 경우 다른 프로세스는 해당 파일에 접근 금지

  - 그러니까 파일에 접근하고싶으면 `Open`을 해야하고, 다 썼으면 `Close`를 해줘야한다.
    - Close 하지 않고 프로세스가 종료되어도 OS가 처리해주긴 한다.


---


- 파일 입/출력에 사용되는 **내부 버퍼**가 있을 수 있음
  - 이것을 Buffered I/O라고 구분함


---


- **장치는 `File`로 추상화됨**

  - 즉, 장치를 사용하고싶으면 해당 장치를 추상화시켜둔 `File`을 사용하면 되는것
  - 다시말해 장치를 추상화한 `File`은 장치와 유저 사이의 insterface가 된다.

  - `File`이라 하면 흔히 아는 `데이터 File`과 `장치를 추상화한 File`로 이분된다.


  - ex A) SSD 장치

    > - User Level
    >   - File: _장치를 추상화한 파일. 이 파일을 통해 Kernel Level과 소통한다._ 
    >
    > - Kernel Level
    >   - File System
    >   - Filter(실시간 감시 엔진)
    >     - 접근하려는 파일이 안전한지 검사
    >   - (SoftWare) Disk Driver
    >     - 운영체제에 설치되는 소프트웨어  
    >   - (HardWare) SSD 장치


  - ex B) 터미널 에뮬레이터

    - 터미널은 입출력 장치를 emulating하는데, 이것이 바로 터미널을 추상화해둔 인터페이스이고,
      이것은 파일 형태로 존재한다.



## 129. 파일 스트림이란 무엇인가?

- 스트림:
  - 1차원 선형구조
  - 빈틈없이 연속된 형태


- `File`의 특성
  - 파일을 Write 할 때, 크기를 사전 정의하지 않는다. (쓰면 쓰는대로 늘어남)
    - 최대 얼마나 크게 만들수 있는지는 파일시스템의 스펙에 규정되어있다.
    - 쓰는 과정 같은 경우
      1. 

> [!nt] 파일 쓰기 과정
>
> - 유저가 실행한 프로세스가 write
>   1. malloc으로 Heap 등 가상메모리에 버퍼 영역을 할당받음
>   2. 해당 버퍼에 값을 채워넣음
>   3. 버퍼가 가득 차거나 파일을 닫을 때 RAM에서 디스크로 복사됨
>
> - 커널이 write
>   1. 커널이 write 할 땐 RAM의 커널영역에서 버퍼를 마련
>   2. 나머진 같음



## 130. 텍스트와 바이너리 직접 들여다 보기



- 바이너리 파일의 부분집합이 텍스트 파일이다.
  - 어차피 모든 데이터(파일 포함)는 바이너리 데이터이고,
  - 그중에 "텍스트"로 해석되는 특정 영역의 바이너리 데이터로만 구성된 파일을 텍스트 파일이라 부름


- 예시)

  - File Path: my_file
    ```
    banana
    soccer
    hello world!
    ```

  - hexdump
    ```bash
    example$ hexdump -X my_file
    0000000  62  61  6e  61  6e  61  0a  73  6f  63  63  65  72  0a  68  65
    0000010  6c  6c  6f  20  77  6f  72  6c  64  21  0a
    000001b

    example$ hexdump -C my_file
    00000000  62 61 6e 61 6e 61 0a 73  6f 63 63 65 72 0a 68 65  |banana.soccer.he|
    00000010  6c 6c 6f 20 77 6f 72 6c  64 21 0a                 |llo world!.|
    ```




- 매직 넘버(Magic Number):

  파일의 맨 앞부분에 위치한 고유한 바이트 시퀀스로, OS 또는 프로그램이 파일의 종류를 식별하는 데 사용

  - 예시:
    - ELF (Linux 실행 파일): `0x7F 45 4C 46` (ASCII로 `.ELF`)
    - PE (Windows 실행 파일): `0x4D 5A` (ASCII로 `MZ`)
    - PNG 이미지: `0x89 50 4E 47 0D 0A 1A 0A`

    - ZIP 파일: `0x50 4B 03 04`
      ```bash
      ~$ hexdump -X Downloads/awk.zip | head
      0000000  50  4b  03  04  14  00  00  00  08  00  07  95  d6  56  ff  a7
      ```








## 131. 파일 생성, 개방, 폐쇄


- 어떻게?
  : `fopen(<경로>, <접근모드>)`

  - 접근모드
    | 모드  | 파일 종류 | 의미                    | 파일이 없을 때 |
    |-------|-----------|-------------------------|----------------|
    | "r"   | 텍스트    | 읽기 전용               | 실패           |
    | "w"   |           | 쓰기 전용 (내용 덮어씀) | 새로 생성      |
    | "a"   |           | 추가(append) 전용       | 새로 생성      |
    | "r+"  |           | 읽기/쓰기               | 실패           |
    | "w+"  |           | 읽기/쓰기 (내용 덮어씀) | 새로 생성      |
    | "a+"  |           | 읽기/쓰기 (끝에 추가)   | 새로 생성      |
    | "rb"  | 바이너리  |                         |                |
    | "wb"  |           |                         |                |
    | "ab"  |           |                         |                |
    | "rb+" |           |                         |                |
    | "wb+" |           |                         |                |
    | "ab+" |           |                         |                |

    - 근데 r+ 해도 그냥 기존내용 덮어쓰는 경우가 있다. 이건 cursor역할을 하는 입출력 포인터가 0번을
      가리키고 있어서 그렇다.




- 예제)
  - 그냥 `fopen()`으로는 해당 File에 대한 타 프로세스의 접근을 막지 않는다.

    - File Path: 131.c "close 전에 멈춰놔도 문제 없이 잘 열림"
      ```c
      #include <stdio.h>
      
      int main() {
          FILE* fp = NULL;
      
          fp = fopen("test.txt", "w");
          if (!fp) {
              puts("ERROR: Failed to open file!");
              return -1;
          }
      
          fclose(fp);
      
          return 0;
      }
      ```


    - 별도로 해줘야한다.

      - 근데 `flock()`같은건 "권고" 정도 의미라 다른 프로세스가 따를수도 있고 아닐수도 있다.
      - 다른 프로세스도 마찬가지로 flock을 통해 접근하면 막히는 방식
      - `fcntl()` with `F_SETLK` 등 여타 프로세스를 모두 막는것도 가능은 하지만 성능상 이유로
        현대적 리눅스에선 허용되지 않는 경우가 많다.

      ````c
      #include <stdio.h>
      #include <sys/file.h> // for flock()
      #include <unistd.h>   // for close()

      int main() {
          FILE* fp = fopen("test.txt", "w");
          if (!fp) {
              puts("ERROR: Failed to open file!");
              return -1;
          }

          // Get the file descriptor from FILE*
          int fd = fileno(fp);

          // Try to acquire an exclusive lock (blocks other writers/readers)
          if (flock(fd, LOCK_EX) != 0) {
              puts("ERROR: Failed to lock file!");
              fclose(fp);
              return -1;
          }

          puts("File locked. Press Enter to unlock and exit...");
          getchar(); // Wait for user input

          // Release the lock
          flock(fd, LOCK_UN);

          fclose(fp);
          return 0;
      }
      ````





## 132. 텍스트 파일 입/출력



## 133. Buffered I/O와 파일 입출력 버퍼 플러싱



## 134. 콘솔 I/O 버퍼 메모리 추적하기



## 135. 바이너리 파일 입/출력



## 136. 파일 입/출력 포인터 위치 제어



