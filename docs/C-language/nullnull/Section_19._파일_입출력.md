# Section 19. 파일 입/출력





## 127. 파일 시스템 기본 이론 - 첫 번째

- 보조 기억장치(2차 메모리)를 관리하는 방법으로 파일시스템(추상화) 사용
- 경로에는 절대경로, 상대경로, 현재경로가 있다.

- `inode`
  : 파일시스템으로 관리되는 모든 파일의 메타정보



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



> [!nt] 모든것은 파일이다.
>
> "**장치/데이터 모두** 파일로 추상화하여 다룬다. 프로세스 입장에서 일관된(추상화된) 접근을 제공한다."


## 129. 파일 스트림이란 무엇인가?

- 스트림:
  - 1차원 선형구조
  - 빈틈없이 연속된 형태


- `File`의 특성
  - 파일을 Write 할 때, 크기를 사전 정의하지 않는다. (쓰면 쓰는대로 늘어남)
    - 최대 얼마나 크게 만들수 있는지는 파일시스템의 스펙에 규정되어있다.

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

    > [!nt] 아스키(ASCII) 기준으로 텍스트 파일에서 사용되는 문자 범위
    >
    > - **0~127**: 표준 아스키 코드 전체 범위  
    >   - **0~31**: 제어 문자(줄바꿈, 탭 등. 사람이 읽을 수 없음)
    >   - **32~126**: 사람이 읽을 수 있는 인쇄 가능한 문자(공백, 숫자, 영문자, 특수문자 등)
    >   - **127**: DEL(삭제) 제어 문자
    > 
    > 실제로 **텍스트 파일**에서 주로 사용되는 값은:
    > - **9**: 탭 (`\t`)
    > - **10**: 줄바꿈 (`\n`)
    > - **13**: 캐리지 리턴 (`\r`)
    > - **32~126**: 인쇄 가능한 문자(공백 포함)



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

- `fopen()` 함수가 반환하는 것: 파일 구조체에 대한 포인터

  - `FILE* fp = NULL;` 로 파일 구조체 포인터 변수를 선언한다.

  - malloc이 힙 영역의 메모리 주소에 대한 포인터를 반환하는것과 비슷


- 실습) File Path: 132.c
  ```c
  #include <stdio.h>
  
  int main() {
      FILE* fp = NULL;
      char ch;
  
      fp = fopen("test.txt", "w");
      if (!fp) {
          puts("ERROR: Failed to open file!");
          return -1;
      }
      fputs("abc\n", fp);
      fclose(fp);
  
      fp = fopen("test.txt", "r");
      if (!fp)
          return -1;
  
      while ((ch = fgetc(fp)) != EOF)
          putchar(ch);
      fclose(fp);
  
      return 0;
  }
  ```


### 관련 기본 API 알아보기

**1. fscanf 기본 사용법**

  - 파일에서 정형화된 데이터를 읽을 때 사용
  - 포맷 문자열로 데이터 형식 지정

  - 예제:
    ````c
    FILE *fp = fopen("data.txt", "r");
    int num;
    char str[100];
    float f;
    while (fscanf(fp, "%d %s %f", &num, str, &f) == 3) {
        // 한 줄, 한 줄 읽은 데이터 처리
    }
    fclose(fp);
    ````



**2. scanf와 stdin**

  - scanf는 표준 입력(stdin)에서 값을 읽음
  - stdin도 파일 포인터다. stdout, stderr와 함께 자동으로 open되는 파일이라 열지 않아도 바로 쓸 수 있는것
  - 내부적으로 fscanf(stdin, ...)과 동일하게 동작

  - 예제:
    ````c
    int x;
    scanf("%d", &x); // == fscanf(stdin, "%d", &x)
    ````



**3. 파일 내용이 정형화되어 있지 않은 경우**

  - 한 줄씩 읽어서 직접 파싱해야 함
  - fgets로 줄 전체를 읽고, 필요하면 sscanf 등으로 추출

  - 예제:
    ````c
    FILE *fp = fopen("data.txt", "r");
    char buf[256];
    while (fgets(buf, sizeof(buf), fp)) {
        // buf에 한 줄이 들어옴
        // 필요에 따라 파싱
    }
    fclose(fp);
    ````




**4. 일기처럼 자유로운 파일 읽기**

  - 구조가 없으므로 한 줄씩 읽어서 그대로 출력하거나, 필요한 정보만 추출

  - 예제:
    ````c
    FILE *fp = fopen("diary.txt", "r");
    char buf[512];
    while (fgets(buf, sizeof(buf), fp)) {
        // 한 줄씩 전부 출력
        printf("%s", buf); 

        // 아니면 필요한것만 찾기
        if (sscanf(buf, "%d원짜리", &price) == 1) {
            printf("발견된 가격: %d\n", price);
        }
    }
    fclose(fp);
    ````




## 133. Buffered I/O와 파일 입출력 버퍼 플러싱


- 입출력이 일어난다는 것은 RAM의 어딘가에 있는 I/O Buffer에 데이터(입력값)가 복사 된다는 것

- 성능상의 이유로 해당 Buffer가 가득차면 (장치 또는 데이터)파일로 입출력 할 수 있음

- Flushign(변기 물 내리기): 버퍼가 가득 차기 전에 버퍼를 비우고 파일로 보내라! 기다리기(버퍼링) 답답하니까
  - `fflush(<파일 포인터>);`
  - 근데 이제는 "출력"에 대해서만 플러싱 한다.




## 134. 콘솔 I/O 버퍼 메모리 추적하기


### 표준 입출력 스펙 (NOT same, based on OS)

- Linux:  

  - 표준 출력 사용  
    ```c
    fp = stdout;
    ```

  - 현재 터미널 직접 접근  
    ```c
    fp = fopen("/dev/tty", "w");
    ```

  - 특정 터미널(예: `/dev/pts/2`) 직접 접근  
    ```c
    fp = fopen("/dev/pts/2", "w");
    ```


- Windows:  

  ```c
  fp = fopen("CON", "w");
  ```


- 현재 터미널 경로 확인  

  ```sh
  $ tty
  /dev/pts/2
  ```



### 실습

- 영 안된다...

- 아래처럼 debugger를 활용해 실제 `FILE sturcture`가 어떻게 생겨먹었는지 확인

  ```bash
  pwndbg> ptype FILE
  type = struct _IO_FILE {
      int _flags;
      char *_IO_read_ptr;
      char *_IO_read_end;
      char *_IO_read_base;
      char *_IO_write_base;
      char *_IO_write_ptr;
      char *_IO_write_end;
      char *_IO_buf_base;
      char *_IO_buf_end;
      char *_IO_save_base;
      char *_IO_backup_base;
      char *_IO_save_end;
      struct _IO_marker *_markers;
      struct _IO_FILE *_chain;
      int _fileno;
      int _flags2 : 24;
      char _short_backupbuf[1];
      __off_t _old_offset;
      unsigned short _cur_column;
      signed char _vtable_offset;
      char _shortbuf[1];
      _IO_lock_t *_lock;
      __off64_t _offset;
      struct _IO_codecvt *_codecvt;
      struct _IO_wide_data *_wide_data;
      struct _IO_FILE *_freeres_list;
      void *_freeres_buf;
      struct _IO_FILE **_prevchain;
      int _mode;
      int _unused3;
      __uint64_t _total_written;
      char _unused2[8];
  }
  ```



## 135. 바이너리 파일 입/출력


- 텍스트 파일이 아닌 바이너리 파일에서는 십진수 -1 처럼 표준 ascii 형식으로 표현되지 않는 값을
  파일에 저장할 필요가 있다.
  - 이때 파일 접근 모드에 b를 붙여주지 않으면 이런 값의 입력이 안될 수 있다.
  - 접근모드
    | 모드  | 파일 종류 |
    |-------|-----------|
    | "r"   | 텍스트    |
    | "rb"  | 바이너리  |


- 만약 유저정보 같은 구조체를 입출력할 때 사용한다면, 고정된 크기로 용량을 차지한다.
  - 그래서 효율성은 좀 떨어지지만, 
  - 대신 읽어들일 때 편리하다.


- 이거는 바이너리 파일을 입출력 할때만 쓴다.

  - 쓰기 / 읽기
    - `fread(버퍼주소, 크기, 회수, 파일포인터);`
    - `fwrite(버퍼주소, 크기, 회수, 파일포인터);`

    - I/O 버퍼의 타입은 상관 안한다. 그냥 갖다가 복사한다.


## 136. 파일 입/출력 포인터 위치 제어


- 파일포인터: 

  - 파일 I/O 버퍼에서 cursor의 위치를 의미한다.
    - `FILE* my_fp` 이거 말하는게 **아니다.**

  - write 하면 이 파일포인터가 자동으로 옮겨진다.




- 때로는 파일포인터를 **임의로 옮겨**야 할 때가 있다.

  - `fseek(포인터변수, offset, 기준점)`
  - 기준점은 아래 종류가 있다.
    ```c
    #define SEEK_CUR 1  // 입출력 현위치
    #define SEEK_END 2  // 파일의 끝
    #define SEEK_SET 0  // 파일의 시작
    ```




- 파일포인터 실습


  - 파일포인터 활용해서 파일 크기 읽기
    ```c
    FILE* fp = NULL;
    fp = fopen("myfile.dat", "rb");

    fseek(fp, 0, SEEK_END);

    printf("size of myfile.dat: %u\n", ftell(fp));   // 제일 끝 부분으로, offset 0 주고 옮겼으니 파일 크기를 알 수 있다.
    fclose(fp);
    ```


  - fseek 활용한 입/출력 실습) File Path: 136.c
    ```c
    #include <stdio.h>
    
    int main() {
        FILE* fp = NULL;
        char* data = "1234567890123456789012345678901";
    
        fp = fopen("test", "wb");
        if (!fp) {
            printf("something wrong!");
            return -1;
        }
    
        fwrite(data, 32, 1, fp);
    
        fseek(fp, 5, SEEK_SET);
        fwrite("hello", 5, 1, fp);
    
        return 0;
    }
    ```

    - 기존
      ```sh
      실습$ hexdump -C test
      00000000  31 32 33 34 35 36 37 38  39 30 31 32 33 34 35 36  |1234567890123456|
      00000010  37 38 39 30 31 32 33 34  35 36 37 38 39 30 31 00  |789012345678901.|
      00000020
      ```

    - fseek 활용해서 hello 실행
      ```sh
      실습$ hexdump -C test
      00000000  31 32 33 34 35 68 65 6c  6c 6f 31 32 33 34 35 36  |12345hello123456|
      00000010  37 38 39 30 31 32 33 34  35 36 37 38 39 30 31 00  |789012345678901.|
      00000020
      ```

