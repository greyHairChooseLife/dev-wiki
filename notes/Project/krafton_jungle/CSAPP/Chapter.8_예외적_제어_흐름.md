# Chapter.8 예외적 제어 흐름


[notebookLM](https://notebooklm.google.com/notebook/f405095d-572f-4992-bf85-e8781645f33a)



- **챕터 요약:**


  - **정의 및 중요성**: 
    - 프로세서 상태 변화에 대응하여
    - 제어 흐름이 갑작스럽게 변경되는 현상이며,
    - OS가 아래를 구현하는 데 사용하는 기본 메커니즘
      - I/O
      - 프로세스
      - 가상 메모리(VM)
      - 동시성

  - **예외 클래스**:

      - **인터럽트 (Interrupt)**: I/O 장치와 같은 외부 요인에 의해 비동기적으로 발생하며,
        다음 명령어로 복귀함.

      - **트랩 (Trap)**: 명령어 실행 결과로 발생하는 의도적인 동기적 예외이며,
        시스템 호출(System Call)을 구현하는 데 사용되고 다음 명령어로 복귀함.

      - **결함 (Fault)**: 복구 가능한 오류(예: 페이지 폴트)로 인해 동기적으로 발생하며,
        핸들러가 복구에 성공하면 결함을 일으킨 명령어를 재실행함.

      - **중단 (Abort)**: 복구 불가능한 치명적인 하드웨어 오류로 인해 발생하며,
        인터럽트된 프로그램으로 제어가 돌아가지 않음.

  - **프로세스 및 시그널**: ECF는 OS가 프로세스(논리적 제어 흐름)를 추상화하는 기반이며, 시그널은
    애플리케이션 레벨에서 이벤트 발생을 알리는 소프트웨어 ECF 메시지임.

  - **비지역 점프 (Nonlocal Jumps)**: `setjmp`/`longjmp` 함수를 사용하여 일반적인 호출/반환 스택
    규율을 우회하여 제어를 이동시키는 애플리케이션 레벨의 ECF 형태임.

    - Because normal jumps (like goto) are local to a function, but setjmp/longjmp can jump
      across function boundaries, from one function to another, even unwinding the stack.


---

- **목차**

  1. ECF 개요
  2. 예외 (Exceptions)
  3. 시스템 호출 (System Calls)
  4. 프로세스 (Processes)
  5. 프로세스 제어
  6. 시그널 (Signals)
  7. 비지역 점프 (Nonlocal Jumps)




## Exceptional Control Flow (ECF) 개요



- **시스템 내외부 상태 변화에 반응**

  - PC가 순차적으로 명령을 수행지 않게 되는 경우
  - 예시
    > - 하드웨어 타이머가 주기적으로 작동함
    >   - ex) 일정 시간 뒤에 화면 보호기 작동
    >
    > - 네트워크 어댑터에 패킷이 도착함
    >   - ex) 카톡 메시지가 도착하면 사용중인 프로세스가 아니어도 알람이 보임
    >
    > - 프로그램이 디스크에서 데이터를 요청하고 알림을 받을 때까지 대기함
    >   - ex) 에디터로 파일을 열면 메모리에 로드되는동안 기다렸다가 로드 완료시 컨텐츠를 보여주도록
    >     프로그램 재개
    >
    > - 부모 프로세스가 자식 프로세스 종료 알림을 받아야 함
    >   - ex) 쉘에서 어떤 명령을 실행하면, 해당 명령(자식 프로세스)이 실행 및 종료 될 때까지
    >     쉘(부모프로세스)는 기다린다.




- ECF는 컴퓨터 시스템의 **모든 레벨에서 발생**

  - **하드웨어 레벨**: 하드웨어가 감지한 이벤트는 _예외 핸들러(Exception Handler)로_ 제어를 전환
    - Timer, I/O Device, Page Faults, Seg Fault, External(USB, network, etc)

  - **운영체제 레벨**: _컨텍스트 스위치(Context Switch)를_ 통해 A 프로세스에서 B 프로세스로 제어를 전환

  - **애플리케이션 레벨**:
    - _System Calls를_ 통해 _커널 모드로_ 실행가능한 작업을 요청하고, 완료시 제어권을 되돌려 받음
    - 프로세스는 다른 프로세스에게 **시그널(Signal)을** 보낼 수 있음
      - 그래서 htop, 작업관리자 같은 프로그램으로 다른 프로세스를 종료시킬 수 있다.
      - 단, process owner(loggin user)는 같아야한다.
    - 프로세스는 시그널 핸들러로 시그널을 제어할 수 있음
    - 오류 조건에 반응하여 임의의 위치로 비지역 점프(Nonlocal Jumps)를 수행

    > [!NOTE]  What is "시그널(Signal)"?
    >
    > ECF 맥락에서 OS와 어플리케이션을 연계하는 개념
    >
    > hardware/application level에서 signal을 다른 프로세스에게 보내려 하면, OS가 signal을 생성하고
    > 전달해준다. application에서는 받은 signal을 어떻게 처리할지 정의할 수도 있고, OS가 제공하는
    > 기본값을 사용할수도 있다.


---

  > [!NOTE] 이밖에도 ECF 관련 아래의 역할도 수행
  >
  > - **OS/kernel**은 signals를 생성 또는 릴레이로 타겟 프로세스까지 전달
  >   - 릴레이로 전달해주는 signals는 이놈들이 만든다: hardware events, **system calls** from processes, etc ..
  > - **OS**는 기본값인 signals handling 방법을 제공한다.
  >   - 개별 어플리케이션 코드에서 signal handler를 정의할 수 있다.
  > 
  > - For example, a segmentation fault (SIGSEGV) is detected by the OS and delivered to the
  >   process, which may handle it by terminating or running a custom handler.
  >
  >
  >   > [!NOTE] What is "system calls"
  >   >
  >   > user-level 프로그램이 kernel에 시스템 레벨의 작업을 요청하는 것. **traps 예외**로 구현된다.
  >   > 커널은 요청을 받아서 실행시켜주고 다시 프로세스에게 제어권을 돌려준다.
  >   >
  >   > Examples in C/Linux:
  >   > - `read()`: Reads data from a file descriptor.
  >   > - `fork()`: Creates a new process.
  >   > - `kill()`: Sends a signal to a process.




---


  - 프로세스는 시그널 핸들러로 시그널을 제어할 수 있음
    ````c
    void sigint_handler(int sig) {
        printf("Caught SIGINT, exiting gracefully\n");
        exit(0);
    }

    void main() {
        signal(SIGINT, sigint_handler);
        while (1) {
            printf("Running...\n");
            sleep(1);
        }
    }
    ````
    - 어플리케이션 레벨에서 제어할 수 없는 것도 있다.
      - **SIGKILL (9)**: Forces immediate termination; cannot be handled.
      - **SIGSTOP (19)**: Suspends the process; cannot be handled or ignored.


  - 오류 조건에 반응하여 임의의 위치로 비지역 점프(Nonlocal Jumps)를 수행
    - goto문법 같은것인데, **스택 범위라던지 함수 스코프도 무시하고 아무데나 즉시 갈 수 있음**
    - C++나 Java의 `try`, `catch`, `throw`와 같은 소프트웨어 예외 메커니즘이 이것으로 구현됨
    - ex)
      ````c
      jmp_buf env; // setjmp가 처음 실행되는거라는 등 상태 저장

      int main() {
          int ret = setjmp(env);  // Saves state, ret = 0 initially
          if (ret == 0) {
              printf("First call\n");
              longjmp(env, 42);  // Jumps back, setjmp returns 42
          } else {
              printf("Jumped back with value: %d\n", ret);
          }
          return 0;
      }
      ````




## 예외 핸들러(Exceptions Handler)


### Exception Handling Mechanism


_예외 발생_   _예외 테이블 인덱싱_   _예외 핸들러 호출(procedure 상태저장)_   _제어권 후처리(돌려주거나 종료)_



1. **Event Detection**:
   The processor (CPU) detects an exception event

   - **Exception Number**:
     Each exception type has a **unique identifier** (e.g., 0x80 for system calls on x86 Linux).
     The CPU uses this number **to index into a predefined table.**
     - Bound to 0 ~ 255. (Processor defines 0 ~ 31, OS defines 32 ~ 255)

   - **Exception Table (Jump Table)**:
     This is a kernel-maintained array in memory (often at a fixed address like 0x00000000 on x86).

     - OS는 시스템 부팅 시점에 예외 테이블이라는 점프 테이블을 할당하고 초기화
     - 이 테이블은 특수 CPU 레지스터인 Exception Table Base Register에 시작 주소가 저장되어 있음

     Each entry points to **the address of an exception handler routine.**
     For example:
       - Entry 0: Divide-by-zero handler.
       - Entry 14: Page fault handler.
       - Entry 128 (0x80): System call handler.



2. **Indirect Procedure Call**:
   The CPU performs an indirect jump using the (exception)table entry.
   This is like a function pointer call: the processor loads the handler address from the table
   and branches to it. This is why it is "indirect" call.

   - **Before jumping, the CPU saves state on to the kernel stack** to allow resumption later.
     - e.g., pushes the current instruction pointer, flags, and registers

   - 프로세서는 권한을 나타내는 **Mode Bit**를 설정하여 **Kernel Mode**로 전환



3. **Control Transfer and Handler Execution**: 
   - The handler runs **in kernel mode**, with full privileges **to access hardware and memory.**
   - It processes the exception (e.g., for a page fault, it might load data from disk into RAM).
   - **After handling,**
     - it either resumes the interrupted program (by popping saved state and returning)
     - or terminates/aborts it (e.g., for fatal errors).


### 예외 핸들러(Exceptions Handler) classes


- **Asynchronous Exceptions**:

  - 현재 instruction과 무관하게 외부 요인으로 발생
    - 이런 의미에서 asyncronous
  - 예측 불가능하며, 프로세스 중간에 언제든지 끼어들 수 있음
  - 현재 명령($I_{k}$)이 끝난 후 프로세서가 감지하여 핸들러를 호출

  - **복귀:**
    핸들러 실행이 완료되면 **다음 명령어($I_{k+1}$)로** 복귀
    (인터럽트가 발생하지 않았던 것처럼 프로그램이 계속 실행되게 함)

  - Example:

    - `Interrupts`: from I/O devices (e.g., a network packet arriving or a timer expiring),
      which don't depend on what instruction is running.



- **Synchronous Exceptions**:

  - 직전 실행한 instruction에 의해 발생
  - 예측 가능하며, 프로세스의 flow에 따라 발생한다.

  - Examples:

    - `Traps`: _Intentional (aka. software interrupts)_, like system calls (e.g., `read()` or `write()` in code).
      - **복귀:** 트랩을 일으킨 시스템 호출 명령어의 **다음 명령어**

    - `Faults`: _Recoverable errors_, like page faults (e.g., accessing unmapped memory).
      - **복귀:** **결함을 일으킨 명령어**로 복귀시켜 명령어를 재실행

    - `Aborts`: _Unrecoverable fatal errors_, like hardware failures (e.g., invalid instruction).
      - **복귀:** 애플리케이션을 종료



---


> [!qt] 256가지 예외 번호가 있다면서 왜 handler는 4가지 뿐인가?
>   󱞪 
>
> Interrupt, Trap, Fault, Abort 4가지는 핸들러가 아니라 핸들러 카테고리다. 해당 카테고리에
> 속한 다양한 예외를 처리한다.
> 
> 실제로는 수 십 가지의 핸들러가 있으며, 또한 이것은 확장 가능하다.
>
> 확장 가능하다는 말의 의미는 커널 코드 수정을 통해 32~255영역이 추가/수정이 가능하단 소리지
> 어플리케이션 레벨에선 아니다.






## 시스템 호출 (System Calls)


- **정의:** application-level에서 kernel-level 동작을 요청하는 의도적 Trap Exception


### 시스템 호출 처리 과정

1. **서비스 요청:**
   - 파일 읽기(`read`), 프로세스 생성(`fork`), 프로그램 로드(`execve`) 등 커널 서비스 필요 시 사용
   * C 프로그램은 C 라이브러리의 래퍼 함수로 간접 호출하는 방식


2. **인자 전달:**

   * x86-64에서 인자는 레지스터로 전달

   - 시스템 호출 번호
     - `%rax`에 System Call 고유번호 저장
     - 이는 커널 내 점프 테이블의 오프셋에 해당하며, 예외 테이블과는 다름
     - e.g., `read` (0), `write` (1), `open` (2), `fork` (57), `execve` (59).

   - 인자 레지스터 순서
     - 시스템 호출 인자는 최대 6개까지 다음 레지스터에 순서대로 저장됨
       - `%rdi`, `%rsi`, `%rdx`, `%r10`, `%r8`, `%r9`


3. **트랩 실행:**
   - `syscall`로 트랩 발생, Exception Handler로 제어 전환
   - 함수 호출과 비슷하지만, **커널 모드에서 특권 명령어 실행 및 커널 스택 접근**


4. **핸들러 실행 및 복귀:**

   - 핸들러가 시스템 호출을 디코딩해 커널 루틴 호출, 완료 후 실행중이던 프로세스의 다음 명령어로 복귀.

   - 반환 값 및 상태
     - 시스템 호출이 복귀할 때, `%rax` 레지스터에는 반환 값이 포함됨.
       - non-negative values: indicate success (e.g., the number of bytes read)
       - negative values    : 반환 값은 $-4,095$에서 $-1$ 사이의 음수 값으로, `errno`에 해당
     - `%rcx`와 `%r11` 레지스터는 파괴(destroyed)됨.



### 어셈블리 코드 예시


| C 함수 호출               | 어셈블리 명령어      | 레지스터 사용   | 설명                                          |
|---------------------------|----------------------|-----------------|-----------------------------------------------|
| `write(1, "hello\n", 13)` | `movq $1, %rax`      | `%rax = 1`      | `write` 시스템 호출 번호 1 저장.              |
|                           | `movq $1, %rdi`      | `%rdi = 1`      | 첫 번째 인자 (stdout 파일 디스크립터 1) 저장. |
|                           | `movq $string, %rsi` | `%rsi = string` | 두 번째 인자 (문자열 주소) 저장.              |
|                           | `movq $len, %rdx`    | `%rdx = 13`     | 세 번째 인자 (문자열 길이) 저장.              |
|                           | `syscall`            |                 | 시스템 호출 실행.                             |
| C 함수 호출               | 어셈블리 명령어      | 레지스터 사용   | 설명                                          |
|---------------------------|----------------------|-----------------|-----------------------------------------------|
| `_exit(0)`                | `movq $60, %rax`     | `%rax = 60`     | `_exit` 시스템 호출 번호 60 저장.             |
|                           | `movq $0, %rdi`      | `%rdi = 0`      | 첫 번째 인자 (종료 상태 0) 저장.              |
|                           | `syscall`            |                 | 시스템 호출 실행.                             |


- 일반적으로 C 프로그램은 `syscall`을 직접 사용하기보다는 C 표준 라이브러리가 제공하는
  **래퍼 함수(wrapper functions)**를 통해 시스템 호출을 간접적으로 호출함.



## 프로세스 (Processes)


_애플리케이션 프로그램의 실행 및 context(실행 맥락)의 추상화_


- **Processor 및 Memory system을 독점적으로 사용하는 것처럼 추상화를 제공**

  - 실제로는 단일 하드웨어 시스템이 다수의 프로세스를 실행

  - Processor 독점 환상의 원리
    - Concurrency(동시성): 동시에 다수 프로세스를 실행하는 성질
      - Multitasking(멀티태스킹)은 여러 프로세스가 프로세서를 동시에 사용하는 방식으로,
        동시성 구현의 사례

      - 코어 두개라서 진짜 두 개 동시에 실행하는건 병렬성

  - Memory 독점 환상의 원리
    - 가상 주소공간을 적절히 맵핑하여 다른 프로세스와 독립된 사설(Private) 공간을 이용함
    - 프로그램 영역(코드, 데이터, 힙, 스택)과 커널 영역으로 나뉨
      - **커널 영역은** inter-processes차원에서 **유일함**



- **프로세스는 Trap Exception 및 컨텍스트 스위칭을 통해 실행 및 상호작용함**

  - **Trap Exception:** 복수의 프로세스가 동시성을 갖는 수단중 하나
    - Application-level에서 system call 호출을 통해 Trap Exception을 유발하고, 커널 특권 명령을 실행

  - **컨텍스트 스위치**

    - **컨텍스트**: 커널이 선점된(preempted) 프로세스를 다시 시작하기 위해 필요한 **상태**
      - 일반 목적 레지스터, PC, 사용자 스택, 커널 스택, 페이지 테이블 등

    - **스위치 과정**:
      1. 현재 프로세스의 컨텍스트를 저장(커널영역에)
      2. 이전에 선점된 다른 프로세스의 저장된 컨텍스트를 복원
      3. 새로 복원된 프로세스로 제어를 전달

    - **발생 시점**:
      컨텍스트 스위치는 시스템 호출 실행 중(예: I/O 대기 시) 또는 주기적인 타이머
      인터럽트와 같은 예외 발생 시 일어날 수 있음



## 프로세스 제어


- Unix 시스템은 프로세스를 생성, 관리, 종료하기 위한 다양한 시스템 호출을 제공


### 프로세스 식별 및 상태

- **프로세스 ID (PID)**: 각 프로세스는 고유한 양의 정수 프로세스 ID(PID)를 가짐
    - `getpid` : 현재 프로세스의 ID
    - `getppid`: 현재 프로세스의 Parent 프로세스 ID


- **프로세스 상태**: 아래 중 하나의 상태

  - **실행 (Running)**: CPU에서 실행/실행 대기 중이며, 결국 커널에 의해 스케줄링될 상태

- [ ] 나눠 정리더해
```
### 실행(Running)
- **정의**: 프로세스가 실제로 CPU에서 명령어를 실행 중인 상태입니다.
- **특징**:  
  - 시스템에서 한 번에 하나(혹은 멀티코어라면 여러 개)의 프로세스만 "실행" 상태가 될 수 있습니다.
  - CPU를 점유하고 있으므로, 명령어가 계속 진행됩니다.

- **정의**: 프로세스가 실행될 준비가 되어 있지만, 아직 CPU를 할당받지 못한 상태입니다.
- **특징**:  
  - 메모리 등 필요한 자원은 모두 확보된 상태입니다.
  - CPU가 할당되기를 기다리고 있습니다.
  - 스케줄러가 CPU를 할당하면 "실행(Running)" 상태로 전환됩니다.
```



  - **정지 (Stopped)**: 실행이 일시적으로 중단된 상태로, `SIGSTOP`, `SIGTSTP` 등의 시그널을
    받아 정지되며, `SIGCONT` 시그널을 받을 때까지 정지됨.

  - **종료 (Terminated)**: 영구적으로 중지된 상태임. 이는 시그널을 받거나, `main` 루틴에서
    복귀하거나(main 함수 실행 완료), `exit` 함수를 호출하여 발생함.

    - [ ]  정리 정리

```
이런 상태는 왜 필요한거야? 종료됐으면 어차피 끝난거 아냐?


A "종료(Terminated)" 상태는 프로세스가 실행을 마치고 더 이상 동작하지 않음을 나타냅니다. 하지만, 종료된 프로세스의 정보(예: 종료 코드, 자원 등)는 즉시 시스템에서 완전히 사라지지 않습니다.

**이유:**
- 운영체제는 종료된 프로세스의 정보를 부모 프로세스가 확인하고 정리(reap)할 수 있도록 잠시 보관합니다.  
- 부모 프로세스가 `wait()` 또는 `waitpid()`로 자식의 종료 상태를 수집하지 않으면, 종료된 프로세스는 "좀비(zombie)" 상태로 남아 시스템 자원을 계속 점유합니다.
- 이런 관리가 없으면, 자식 프로세스가 많이 생성되고 종료될 때마다 시스템 자원이 소진되어 문제가 발생할 수 있습니다.

**요약:**  
종료 상태는 부모가 자식의 종료 정보를 확인하고 자원을 회수할 수 있도록 운영체제가 관리하는 중간 단계입니다.
```




### 프로세스 Life Cycle

_부모 프로세스가 자식 프로세스를 생성-관리-종료-정리 한다고 생각하면서 이해_


1. **Creation**
   `fork()` duplicates the parent process
   - creating a child that inherits resources (e.g., memory, file descriptors)
     - memory의 경우 읽기만 할 땐 부모 프로세스의 메모리를 읽다가, 'write'작업이 발생하면
       복사해간다.
     - file descriptors는 stdi/o/e 등 open상태의 장치파일들을 의미한다.
   - starts executing the same code as the parent
   - 호출은 한번되지만 반환은 부모/자식 프로세스에서 모두 일어남(2번)
     - 부모: 새로 생성된 자식의 PID(0이 아닌 값)를 반환
     - 자식: 0을 반환


2. **Execution**
   The child can continue running the inherited code or call `execve()` to replace its
   process with a new program (e.g., launching an executable).

   - `execve()`는,
     - 새 프로세스를 생성하지 않으며,
     - 현재 프로세스의 주소 공간을 새 프로그램으로 덮어쓴다.
     - **PID는 유지**되고 열려 있던 파일 디스크립터는 상속된다.
       - 즉, 기존 프로세스는 유실된다.
     - 따라서 `fork()`로 sub-process를 만들어 사용하는게 일반적임


3. **Termination**
   When the child finishes (via `exit()`, return, or signal),
   it enters a "zombie" state; _it's dead but still holds a process table entry and some resources._


4. **Cleanup (Reaping)**
   - The parent must call `waitpid()` (or similar)
     to **retrieve the child's exit status** from the kernel **and then reap it.**
     - **`wait`**: `waitpid(-1, &status, 0)`과 동일한 간단한 버전
     - $(pid)-1$은 아무 child process든 받아버린다. 하나만 있을때 유용
   - With the exit code, the parent must release the resources back to the OS.
   - Without this, zombies accumulate, potentially exhausting system resources.


---

- **기타**
             
  - 프로세스 일시중지/수면
    - **`sleep`**: 지정된 시간(초) 동안 프로세스를 일시 중지(suspend)시킴. 요청된 시간이 경과하면
      0을 반환하지만, 시그널을 받아 일찍 중단되면 남은 시간을 반환할 수 있음.
    - **`pause`**: 프로세스에 시그널이 수신될 때까지 호출 프로세스를 수면 상태로 만듦.

  - 시스템 호출 오류 처리

    Unix 시스템 레벨 함수는 오류 발생 시 일반적으로 $-1$을 반환하고 전역 변수 `errno`를
    설정하여 오류 유형을 알림.
    - ex)
      ```c
      if ((pid = fork()) < 0) {
          fprintf(stderr, "fork error: %s\n", strerror(errno));
          exit(1);
      }
      ```
      - 또는, 이런 기능이 내장된 버전을 사용
        - `pid = Fork();`



### 예시코드


```c
int main() {
    pid_t pid = fork();  // Create child process

    if (pid < 0) {
        perror("fork failed");
        exit(1);
    } else if (pid == 0) {
        // Child process: replace with a new program (e.g., /bin/ls)
        char *args[] = {"/bin/ls", "-l", NULL};
        execve("/bin/ls", args, NULL);
        // If execve fails, exit. Or it will never be executed.
        perror("execve failed");
        exit(1);
    } else {
        // Parent process: wait for child to finish
        int status;
        waitpid(pid, &status, 0);
        printf("Child process %d exited with status %d\n", pid, WEXITSTATUS(status));
    }

    return 0;
}
```


> [!NOTE] Analysis of the `waitpid()`
>
> - `pid`: The process ID of the child to wait for (returned by `fork()`). Use `-1` to
>   wait for any child, or specific values for groups.
>
> - `status`: A pointer to an `int` where the child's exit information is stored (e.g.,
>   exit code or signal). Access it with macros like `WEXITSTATUS(status)` for normal exit codes.
>
> - `options`: An integer for flags (0 means block until the child exits; use `WNOHANG`
>   for non-blocking).
>
>   - non-blocking `WNOHANG` 활용하기
>     ```c
>     int main() {
>         pid_t pid = fork();
>         if (pid == 0) {
>             // Child process
>             sleep(2);  // Simulate work
>             return 0;
>         } else {
>             // Parent process: non-blocking wait
>             int status;
>             while (1) {
>                 pid_t result = waitpid(pid, &status, WNOHANG);
>                 if (result == 0) {
>                     printf("Child not ready yet, doing other work...\n");
>                     sleep(1);  // Simulate other tasks
>                 } else if (result == pid) {
>                     printf("Child exited with status %d\n", WEXITSTATUS(status));
>                     break;
>                 } else {
>                     perror("waitpid error");
>                     break;
>                 }
>             }
>         }
>         return 0;
>     }
>     ```





## 시그널 (Signals)

개념: 프로세스에 메시지를 보내는 소프트웨어 메커니즘
상태: 전송됨, 보류 중, 블록됨, 수신됨
동작: 종료(Terminate), 중지(Stop), 무시(Ignore), 핸들러 실행(Catch)
안전한 시그널 핸들링: 단순하게 유지, async-signal-safe 함수만 호출, errno 저장/복원, 공유 전역 데이터 보호, volatile/sig_atomic_t 사용
sigsuspend: 시그널을 명시적으로 기다리는 원자적 메커니즘


### 개념


- **시스템에서 특정 유형의 이벤트가 발생했음을 프로세스에 알리는 메시지**


- hardware-level, application-level에서 trigger될 수 있다.

  - **하드웨어 관련 이벤트**
    - 프로세스가 0으로 나누기를 시도(SIGFPE)
    - 유효하지 않은 명령어를 실행(SIGILL)
    - 잘못된 메모리 참조(SIGSEGV, Segmentation Fault)

  - **고수준 소프트웨어 이벤트**
    - 사용자가 Ctrl+C를 입력(SIGINT)
    - 한 프로세스가 다른 프로세스를 강제로 종료(SIGKILL)
    - 자식 프로세스가 종료되거나 정지(SIGCHLD)


### 상태: 전송, 차단, 보류, 수신


#### 시그널 전송 (Sending/Delivering)

- 커널은 프로세스를 즉시 중지시키며 Signal을 전달하는것이 아님
- 프로세스의 컨텍스트 내 일부 데이터(pending signal bit vector)를 수정하는 방식으로 표시(전달)함
  - "blocked signal bit vector"는 차단 여부만 표시하고,
  - "pending signal bit vector"는 일단 들어온걸 다 표시한다.
  - ex)
  ```
  pending_signals:  0000 0000 0000 0000 0000 0000 0000 0100
                     ↑
                   SIGINT(2번) 도착 → 2번째 비트가 1
  ```

- **커널 감지**: 커널이 시스템 이벤트(예: 자식 프로세스 종료, 0 나누기)를 감지하여 시그널을 보냄
- **명시적 호출**: 한 프로세스가 `kill` 함수를 호출하여 명시적으로 시그널을 보낼 수도 있음


#### Blocked Signal

- 프로세스는 필요에 따라 일부 signal의 수신을 차단할 수 있다.
  - 일부 Signal(e.g., SIGKILL, SIGSTOP)은 차단 못함.

- 목적: 프로세스가 중요한 작업을 수행하는 동안 시그널에 의해 방해받지 않도록 하기 위해

  - 임계 구역 보호
    - 예를 들어, 파일을 쓰거나 메모리를 수정하는 등 중요한 작업(임계 구역)을 수행할 때 시그널
      핸들러가 실행되면 데이터가 손상될 수 있습니다.
    - 이런 구역에서는 시그널을 차단하여 작업이 끝날 때까지 방해받지 않게 합니다.
  
  - 일관성 유지
    - 여러 작업이 동시에 진행될 때, 시그널 핸들러가 중간에 실행되면 프로그램의 상태가
      불일치하게 될 수 있습니다.
    - 차단을 통해 일관성을 유지합니다.
  
  - 중첩 시그널 방지
    - 이미 시그널 핸들러가 실행 중일 때, 같은 시그널이 또 오면 중첩 실행이 발생할 수 있습니다.
    - 핸들러 실행 중에는 해당 시그널을 차단하여 중첩 실행을 막습니다.


- **암묵적 차단**
  시그널 핸들러가 실행 중일 때, 커널은 기본적으로 현재 처리 중인 유형의 시그널을 자동으로 차단

- **명시적 차단**
  `sigprocmask` 함수를 사용하여 프로세스가 특정 시그널의 수신을 일시적으로 차단


#### Pending Signals

- 스케쥴이 도래하지 않아 아직 signal pending flag에 존재하는 경우를 말함

- 1 ~ 31번 시그널(standard signals)의 경우 한개의 시그널만 pending될 수 있음

  - 추가로 더 받게되면 그냥 버림
  - Ctrl-C 연타해도 의미없다.
    ```c
    #include <signal.h>
    #include <stdio.h>
    #include <unistd.h>  // For pause()

    void handler(int sig) {
        printf("Caught SIGINT\n");
        // Optionally exit or continue
    }

    int main() {
        struct sigaction sa;
        sa.sa_handler = handler;
        sigaction(SIGINT, &sa, NULL);
        
        while (1) {   // Keep running to catch signals
            pause();  // Wait for signals
        }
        return 0;
    }
    ```


 - 32 ~ 64번 시그널(realtime signals, introduced later in POSIX.1b)의 경우 queue에 쌓임
   - 멀티미디어 또는 robotics 관련



#### 시그널 수신 (Received)

- **정의:** 커널에 의해 프로세스가 시그널에 반응하도록 강제된 상태

- **처리 시점**
  커널이 프로세스를 kernel-mode에서 user-mode로 전환할 때 **차단되지 않은 보류 중인
  시그널(`pending & ~blocked`)** 을 확인하여 수신을 강제
  - 시스템 호출(syscall)에서 복귀할 때
  - 프로세스가 스케줄러에 의해 다시 실행될 때(컨텍스트 스위치)
  - 인터럽트 처리 후 사용자 모드로 돌아갈 때

- **처리 동작**
  - 수신된 시그널에 대해 프로세스는 다음 중 하나의 동작을 수행
  - **처리 가능한 모든 시그널을 순서대로(보통 시그널 번호가 작은 것부터)** 처리


> [!td]
>
> - [ ] global note에 보고 정리해라
>
> - [ ] https://velog.io/@junttang/SP-1.6-%EC%8B%9C%EA%B7%B8%EB%84%90-%ED%95%B8%EB%93%A4%EB%A7%81Signal-Handling
> - [ ] https://stackoverflow.com/questions/68509464/when-are-signals-handled
> - [ ] https://stackoverflow.com/questions/9493529/do-signal-handlers-run-in-kernel-or-user-space

  - ex)
    - 시그널 핸들러(`signal handler`)를 실행하여 시그널 포착 (Catching)
    - 시그널 무시 (Ignore)
    - 프로세스 종료 (Terminate) 또는 코어 덤프 후 종료



- **결과**: 시그널 수신 후 커널은 해당 시그널 유형의 `pending` 비트를 해제함



### 동작: 종료, 중지, 무시, 핸들러 실행


- 시그널 유형별로 미리 정의된 **기본 동작(Default Action)** 존재
- 프로세스는 `signal` 함수를 사용하여 이 기본 동작을 변경할 수 있음
- 다만 `SIGSTOP`과 `SIGKILL`은 예외적으로 기본 동작을 변경할 수 없음


  `sigprocmask` 함수를 사용하여 프로세스가 특정 시그널의 수신을 일시적으로 차단
1. **종료 (Terminate)**
   - **정의**: 프로세스가 즉시 종료됨.
   - **예시**: `SIGINT` (Ctrl+C 입력 시), `SIGTERM` (소프트웨어 종료), `SIGILL` (불법
     명령어), `SIGSEGV` (잘못된 메모리 참조) 등의 기본 동작
   - **코어 덤프 후 종료**: 일부 시그널은 프로세스를 종료시키고 메모리 이미지(코어 덤프)를
     디스크에 기록


2. **중지 (Stop)**
   - **정의**: 프로세스의 실행이 일시적으로 중단(Suspend)되고 스케줄링되지 않음. 프로세스는 정지
     상태로 남아 있다가 **`SIGCONT` 시그널을 수신해야** 다시 실행 가능한 상태(Running)로 전환
   - **예시**: `SIGSTOP` (터미널 외), `SIGTSTP` (터미널 정지 신호)의 기본 동작임.


3. **무시 (Ignore)**
   - **정의**: 프로세스가 해당 시그널을 무시하며, 시그널이 도착했어도 아무런 조치도 취하지 않음
   - **예시**: `SIGCHLD` (자식 프로세스 종료 또는 정지), `SIGCONT` (정지된 프로세스 재개)의 기본 동작
   - **변경 방법**: `signal` 함수에 `SIG_IGN`을 인자로 전달하여 기본 동작을 무시로 변경할 수 있음


4. **핸들러 실행/포착 (Catch/Execute a Handler)**
   - **정의**: 프로세스가 시그널을 **포착(Catch)하고,** 미리 설치된 **사용자 정의** 함수인 **시그널
     핸들러(Signal Handler)**를 실행하여 이벤트에 반응
   - **메커니즘**: `signal` 함수에 핸들러 함수의 주소를 전달하여 기본 동작을 변경하며,
     핸들러는 시그널 번호를 단일 정수 인자로 받아 호출
   - **제어 복귀**: 핸들러가 실행을 마친 후 `return`을 수행하면, 제어는 (일반적으로) 시그널
     수신으로 인터럽트되었던 프로그램 흐름의 다음 명령어($I_{next}$)로 복귀



### 안전한 시그널 핸들링

안전한 시그널 핸들러를 작성하기 위한 보수적인 Guidelines


1. **핸들러를 최대한 단순하게 유지 (G0: Keep handlers as simple as possible)**

  - **목표**: 문제를 피하기 위한 가장 좋은 방법은 핸들러를 가능한 작고 단순하게 유지하는 것임.
  - **권장 사항**: 핸들러는 단순히 전역 플래그를 설정하고 즉시 반환하며, 시그널 수신과 관련된
    모든 처리는 주기적으로 플래그를 확인하고 재설정하는 주 프로그램에서 수행하도록 함.



2. **Async-Signal-Safe 함수만 호출 (G1: Call only async-signal-safe functions)**

  - **정의**: 핸들러 내에서는 **Async-Signal-Safe** 함수(또는 간단히 안전한 함수)만 호출해야 함.
  - **안전 기준**: 안전한 함수는 재진입 가능(reentrant, 지역 변수만 접근)하거나 시그널 핸들러에
    의해 인터럽트될 수 없는 속성을 가짐.
  - **비안전 함수 예시**: `printf`, `sprintf`, `malloc`, `exit`와 같은 많은 대중적인
    함수들은 안전하지 않음.
  - **안전한 출력**: 시그널 핸들러에서 출력을 생성하는 유일하게 안전한 방법은 `write` 함수를
    사용하는 것임. 소스는 `sio_putl` 및 `sio_puts`와 같은 안전한 I/O 패키지(Sio)를 제공하며,
    이는 `_exit` (안전한 종료 함수)를 포함함.



3. **`errno` 저장 및 복원 (G2: Save and restore errno)**

  - **문제**: 많은 Linux Async-Signal-Safe 함수는 오류 발생 시 전역 변수 `errno`를 설정함. 핸들러
    내에서 이러한 함수를 호출하면, 주 프로그램이 `errno`에 의존하고 있을 경우 이를 방해할 수 있음.
  - **해결책**: 핸들러 진입 시 `errno`를 지역 변수에 저장하고, 핸들러가 반환하기 전에 이를
    복원해야 함. (단, 핸들러가 `_exit`를 호출하여 프로세스를 종료시키는 경우에는 필요하지 않음).

    예시)
    1. **동일 스레드 내 인터럽트**:
       - 시그널 핸들러는 **현재 실행 중인 코드를 인터럽트**하여 실행됩니다
       - 메인 코드가 시스템 호출 후 `errno` 확인 직전에 시그널이 발생하면
       - 핸들러 내에서 호출한 시스템 함수가 `errno`를 변경할 수 있습니다



4. **공유 전역 데이터 구조 보호 (G3: Protect accesses to shared global data structures)**

  - **문제**: 핸들러가 주 프로그램이나 다른 핸들러와 전역 데이터 구조를 공유하는 경우, 데이터 접근
    시퀀스가 인터럽트될 경우 데이터 구조가 일관성 없는 상태(inconsistent state)에 놓여 예측 불가능한
    결과를 초래할 수 있음.
  - **해결책**: 핸들러와 주 프로그램은 해당 데이터 구조를 읽거나 쓸 때 **일시적으로 모든 시그널을
    차단(block all signals)**해야 함. 이는 핸들러가 명령어 시퀀스를 인터럽트하는 것을 방지하여
    원자성(atomicity)을 보장함.



5. **`volatile` 및 `sig_atomic_t` 사용 (G4, G5)**

  - **`volatile` 선언 (G4)**:
      - **문제**: 핸들러와 주 루틴이 전역 변수 $g$를 공유하고 핸들러가 $g$를 업데이트하는 경우,
      최적화 컴파일러는 $g$의 값을 레지스터에 캐싱할 수 있으며, 이 경우 주 함수는 핸들러에 의해
      업데이트된 최신 값을 보지 못할 수 있음.
      - **해결책**: $g$를 `volatile` 한정자(qualifier)로 선언하여, 컴파일러가 레지스터에 값을
      캐싱하는 것을 막고, $g$가 참조될 때마다 항상 메모리에서 값을 읽도록 강제함.
      - **추가 권장 사항**: 일반적으로 모든 공유 전역 변수는 시그널을 일시적으로 차단하여 접근하는
      방식으로 보호되어야 함.

  - **`sig_atomic_t` 선언 (G5)**:
    - **정의**: 핸들러가 전역 플래그를 작성하여 시그널 수신을 기록하는 일반적인 디자인 패턴에서,
    플래그는 **`sig_atomic_t`** 자료형으로 선언해야 함.
    - **원자성 보장**: 이 자료형은 읽기 및 쓰기 작업이 단일 명령어로 구현될 수 있으므로
    **원자적(atomic, 인터럽트 불가능)**임이 보장됨. 따라서 `sig_atomic_t` 변수에 대한 개별
    읽기/쓰기 작업은 시그널을 일시적으로 차단하지 않고도 안전하게 수행될 수 있음. (단, `flag++`와
    같은 복합 업데이트 작업에는 원자성이 보장되지 않음).


- [ ] 다시 정리해~~

```
2. **원자적 접근 보장**
   - 이 타입에 대한 읽기/쓰기 연산은 **단일 기계어 명령**으로 실행됨이 보장됩니다
   - 따라서 이 연산은 중간에 인터럽트될 수 없습니다
   - 이로 인해 시그널 핸들러와 메인 코드 사이에서 안전하게 데이터를 공유할 수 있습니다

- 중요한 제한 사항

1. **단일 연산에만 원자성 보장**
   - `sig_atomic_t var = 1;` (원자적)
   - `sig_atomic_t var = other_var;` (원자적, other_var가 변경되지 않는 경우)
   
2. **복합 연산은 원자적이지 않음**
   - `sig_atomic_t var++;` (비원자적: 읽기, 증가, 쓰기의 3단계 연산)
   - `sig_atomic_t var = compute_something();` (비원자적: 함수 호출이 포함됨)

- 일반 용도

`sig_atomic_t`는 주로 시그널이 발생했음을 간단히 표시하거나, 시그널 핸들러에서 처리해야 할 상태를 안전하게 변경할 때 사용합니다. 복잡한 작업은 핸들러에서 직접 하지 않고, 메인 프로그램이 플래그를 확인한 후 수행하는 것이 안전합니다.

이 타입은 간단하지만 시그널 처리 시 중요한 안전성을 제공합니다.
```


### sigsuspend: 시그널을 명시적으로 기다리는 원자적 메커니즘


`sigsuspend`는 프로그램이 특정 시그널 핸들러의 실행을 명확하게 기다려야 할 때 사용되는 동기화 도구

- **원자적 대기 메커니즘 제공**:
  - `sigsuspend` 함수는 현재 프로세스의 **`blocked set`를 일시적으로 `mask`로 교체하고,**
    원하는 시그널이 수신될 때까지 프로세스를 정지(suspend)시킴

    > [!NOTE] 원자적이 뭐임?
    >
    > "중간에 시그널이 오거나, 다른 코드가 끼어들 수 없는, 완전히 안전한 한 덩어리 작업"이라는 뜻

    > [!NOTE] `sigsuspend`의 동작 요약
    >
    > `sigsuspend(mask)` 호출은 다음 세 단계를 **인터럽트 불가능하게** 수행하는 것과 동일함:
    > 
    > 1. `sigprocmask(SIG_BLOCK, &mask, &prev)`: 현재 차단 세트를 저장하고 임시로 `mask`로 설정
    > 2. `pause()`: 시그널이 도착할 때까지 프로세스를 정지시킴.
    > 3. `sigprocmask(SIG_SETMASK, &prev, NULL)`: 시그널 수신 후 원래의 차단 세트를 복원
    >
    > - ex)
    >     ```c
    >     sigset_t mask;
    >     sigemptyset(&mask);
    >     sigaddset(&mask, SIGINT);   // SIGINT만 block
    >     sigaddset(&mask, SIGTERM);  // SIGTERM도 block
    >     // SIGCHLD는 block하지 않음 (기다릴 시그널)
    >   
    >     sigsuspend(&mask); // SIGCHLD만 unblock된 상태로 대기
    >     ```



- **적용 예시**:
  - Linux 셸과 같은 프로그램에서 포그라운드 작업이 `SIGCHLD` 시그널을 통해 종료되고 핸들러가
    해당 자식을 회수할 때까지 **프로세스를 효율적으로 대기**시키는 데 사용
  - 이는 프로세스 자원을 낭비하는 스핀 루프(`while (!pid)`)나 느린 `sleep(1)` 함수를 사용하는
    비효율적인 방식 대비 우아하고 안전한 대안



## 비지역 점프 (Nonlocal Jumps)


















---



