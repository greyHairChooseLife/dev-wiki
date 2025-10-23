# Chapter.9 가상 메모리



[notebookLM](https://notebooklm.google.com/notebook/f405095d-572f-4992-bf85-e8781645f33a)


- **챕터 요약:**

  - **VM의 추상화**: VM은 주 메모리에 대한 추상화이며, 애플리케이션에 사설(Private) 주소 공간을 제공함.
    - 개별 프로세스마다 독립적으로 가진다는 점에서 Private이라 표현

  - **VM의 주요 기능**: 캐싱(주 메모리를 DRAM 캐시로 사용), 메모리 관리 단순화, 메모리 보호 기능(접근 권한)을 제공함.

  - **주소 변환**: CPU가 생성한 가상 주소(VA)는 MMU(Memory Management Unit)에 의해 물리적 주소(PA)로 변환됨.

  - **페이지 폴트**: DRAM 캐시 미스를 페이지 폴트(Page Fault)라고 부르며, OS 커널의 핸들러가
    디스크에서 페이지를 가져와(Swapping/Paging) 메모리에 로드하고, 결함 명령어를 재시작함.

  - **메모리 맵핑**: 가상 메모리 영역을 디스크의 파일(Regular file) 또는 익명 파일(Anonymous file)과
    연결하여 초기 내용을 설정하는 방식이며, `fork` 및 `execve` 함수 구현에 핵심적인 역할을 함.

  - **동적 메모리 할당**: `malloc`, `free`와 같은 명시적 할당자(Explicit Allocators)를 통해 힙
    메모리를 관리하는 방식을 다루며, 암묵적 프리 리스트(Implicit Free List), 경계 태그(Boundary Tags)
    등을 사용하여 효율성을 높임.


---

- **목차**

  1. 주소 지정
  2. VM의 기능
  3. DRAM 캐시 조직
  4. 주소 변환
  5. Linux VM 시스템
  6. 메모리 매핑 (Memory Mapping)
  7. 동적 메모리 할당 (Dynamic Memory Allocation)
  8. C 프로그램의 메모리 버그


> [!rf]
> 
> [Demystifying Page Tables - Computer Systems Programming Course](https://www.youtube.com/watch?v=ccWRlNP1UsY)


---
---

> [!td]


- block은 의미 단위, VM 레이어에선 Page 크기에 대응됨.

- VM에 uncached의 존재가 궁금하다. 디스크에서 로드해오진 않아도 존재하는지 정도는 모두
확인한다는건가? 뭐지?

- 페이지 크기는 특정 상황에서 여러가지일 수 있다.
  - 기본 페이지 크기가 있다.
  - 근데 프로세스의 시작이라던지 등등에서는 더 크게 가져올 수 있다.
  - 즉, 하드웨어 및 OS가 지원하면 멀티 페이지 사이즈도 가능하다.


- 페이지 테이블 생성 과정(원리)

  프로세스가 시작될때 

  - 빈 페이지 테이블 초기화 
  - 실행파일 분석해서 필요한 페이지 수 계산 
  - 그에따라 PTE 생성

  - 미할당: 디스크에도 없음. 전체 가상주소공간을 관리는 해야하니까 미리 줄은 그어놓는것.
  - 캐시됨: 디스크에 있고 메모리에 로드
  - 미캐시: 디스크에 있고 메모리에 로드 안됨
    


- "malloc을 통해 페이지 테이블 엔트리의 미할당 페이지에 위치를 기록한다."는 설명이 헷갈려

  - malloc 한다해서 바로 물리메모리를 할당해두는건 아니다. 그냥 페이지 테이블 엔트리만 할당받는다.
  - 가상 Page Table Entry에 disk 공간이 맵핑만 된다. 왜냐? 혹시나 할당받아 사용될 물리메모리의
    페이지가 페이징에 의해 축출 될 때를 대비하는것


> [!qt] 페이지 단위로 가져오면 현재 실행파일 말고 다른 실행파일의 데이터 또는 코드도 포함될 수 있나?
>   󱞪 
>  No, pages for one process do not contain code/data from other executables unless intentionally shared.




- 다단계 page 테이블

  모든 VM을 다 page table로 만드니까 32bit 시스템에서만 봐도 PT하나당 4MB씩 사용해야한다. 이러면
  비효율이니까 계층구조를 만들어 사용하는것.


- VPO virtual page offset은 cache set index와 cache block index로 구성된다. cache tag는 VPN로부터
얻는다.
  - CPU는 VA에서 VPN은 mmu로, vpo는 캐시로 보내서 병렬 처리한다.


- vm의 영역 하나하나는 구조체로 존재한다.
  - 각 영역에 대한 정보가 구조체 안에 담겨있고, 이를 예외처리 따위에 사용한다.


- 메모리 맵핑:

  - RAM에서 쫓겨나는 page는 스왑디스크로 간다. 왜냐면 아직 디스크에 저장되지 않은 것들(그냥
  변수로 접근해서 사용하던 메모리 영역)은 어 디저장이 안돼있으니까 디스크의 스왑영역에 안넣어놓으면
  걍 사라지니까?



- 공유객체 vs 사적객체


- [ ] 이거 제대로 공부해. 왜 구분해서 사용하는지 
  - `malloc`이 메모리 할당에 `brk`와 `mmap`을 나눠서 사용하는 이유는 **효율성과 관리의 편의성** 때문입니다.
  



---
---

## 주소 지정


### '가상주소(VA) -> 물리주소(PA)' 변환 과정


- 물리적 주소는 메인 메모리 내의 **바이트 단위** 셀에 할당되는 고유한 주소



- 현대 시스템에서는 가상 메모리 시스템을 통해 간접적으로 접근
  - **MMU(Memory Management Unit)**라는 전용 하드웨어를 통해 가상 주소를 물리적 주소로 변환
  - 주소 변환 과정
    - 가상 주소 공간의 주소를 물리적 주소 공간의 주소로 매핑
    - OS가 관리하는 주 메모리의 lookup table(page table)을 사용하여 실시간으로 수행됨

      > [!nt] Page Table
      >
      > - There is **one page table per process** (sometimes shared for threads).
      > - The page table is **data structure** stored in physical memory (RAM), **not as a file.**
      > - It is managed directly by the OS and hardware.
      > - It maps **virtual page numbers** (from virtual addresses) to **physical page numbers** (in physical memory).




- The virtual address (VA) is translated into a physical address (PA), by the MMU

  - PA is composed of mainly 2 parts:

    - **VPN (Virtual Page Number)**
      - VPN은 VA의 상위 $(n-p)$ 비트를 구성함
      - 페이지 테이블(Page Table) 내의 적절한 PTE(Page Table Entry)를 선택하는 인덱스로 사용됨

    - **VPO (Virtual Page Offset)**
      - VPO는 $p$비트를 구성하며, 페이지 내의 바이트 위치를 나타냄
      - VPO는 주소 변환 후 생성되는 물리적 주소(PA)의 PPO(Physical Page Offset)와 값이 동일함


  - PA is composed of:

    - **PPN (Physical Page Number)**: Retrieved from the Page Table Entry (PTE) in the page table.
    - **PPO (Physical Page Offset)**: Directly copied from the VA's VPO (Virtual Page Offset),
      as it specifies the exact byte position within the page(no translation needed for the offset).


      > [!nt] "Frame Number" == "Physical Page Number"
      >
      > for example, if a frame is 4 kb,
      > frame number 0 corresponds to physical addresses 0x0000–0x0fff, and so on.


      > [!nt] PCB (Process Control Block) has pointer for Page Table
      >
      > - PCB is stored in the kernel's memory area
      > - PCB is a kernel data structure that stores information about a process,
      >   including:
      >   - Its state, registers, and scheduling details.
      >   - A pointer to the process's **page table**.
      >     - This is loaded on the **PTBR** (Page Table Base Register) by context switching


      > [!nt] Page Table vs Page Table Entry
      >
      > - **Page Table:**
      >   - 운영체제에서 가상 메모리 주소를 실제 물리 메모리 주소로 변환하기 위해 사용하는 자료구조
      >   - _각 프로세스마다 하나씩 존재하며, 여러 개의 Page Table Entry로 구성_
      >   - 쉽게 말해, Page Table은 여러 Page Table Entry를 모아놓은 배열
      > 
      > - **Page Table Entry:**
      >   - Page Table을 구성하는 하나의 "항목"
      >   - 각 PTE는 하나의 가상 페이지에 대한 정보를 담고 있음
      > 
      >     (usually) which includes:
      > 
      >       > 1. **Physical Frame Number (PFN) / Physical Address (aka. Physical Page Number (PPN))**
      >       >    - 해당 가상 페이지가 매핑되는 실제 물리 메모리 프레임의 번호 또는 주소.
      >       > 2. **Present/Valid Bit**
      >       >    - 페이지가 실제로 물리 메모리에 존재하는지 여부.  
      >       >    - 0이면 페이지 폴트가 발생할 수 있음.
      >       > 3. **Access Rights (Protection Bits)**
      >       >    - 읽기/쓰기/실행 권한 등 접근 제어 정보.
      >       > 4. **Dirty Bit**
      >       >    - 페이지가 수정(쓰기)되었는지 표시.  
      >       >    - 페이지 교체 시 디스크에 다시 기록해야 하는지 결정.
      >       > 5. **Referenced/Accessed Bit**
      >       >    - 최근에 접근(읽기/쓰기)된 적이 있는지 표시.  
      >       >    - 페이지 교체 알고리즘에서 사용.
      >       > 6. **Cache/Write-Through/Write-Back Control Bits**
      >       >    - 캐시 사용 여부, 쓰기 정책 등 메모리 동작 방식 제어.
      >       > 7. **User/Supervisor Bit**
      >       >    - 사용자 모드/커널 모드에서 접근 가능한지 여부.
      >       > 8. **Other OS or Architecture-specific Bits**
      >       >    - 예: 페이지 크기, 글로벌 페이지 여부, NX(Non-Executable) 비트 등.


      > [!nt] TLB (Translation Lookaside Buffer)
      >
      > - It is a **cache** inside the MMU.
      > - It stores recently accessed Page's data
      >   - as key-value pairs:
      >     the key information from PTEs (e.g., VPN → PPN + flags like valid/permissions),
      >     enabling fast translation.
      >
      > - It is visited during address translation and,
      >   - If hit (TLB has the cached translation), it retrieves the PPN directly from the TLB (fast).
      >   - If miss,
      >     1. it accesses the page table in RAM via the PTBR (hardware checks L1/L2/L3 caches first for PTEs)
      >     2. retrieves the PTE (from cache or RAM)
      >     3. extracts and caches the key translation (VPN → PPN + flags) in the TLB
      >     4. and proceeds
      



- 대부분의 시스템은 SRAM 캐시(L1, L2, L3)에 접근할 때도 **물리적 주소 지정을 선호**
  - 정의: 주소 변환이 먼저 수행된 다음, 물리적 주소가 SRAM 캐시로 전달되어 Cache Lookup
  - 이점: 
    - 여러 프로세스가 동시에 캐시에 블록을 가질 수 있고
    - 동일한 가상 페이지의 블록을 공유하기 쉬워지며
    - 접근 권한 확인 문제가 VA -> PA 주소 변환 단계에서 처리되어, 캐시가 복잡한 보호 문제를 처리할 필요가 없음



> [!nt]
>
> 리눅스 x86-64 시스템에서 프로세스의 VM은 일반적으로 아래와 같이 구성됨
>
>   - 사용자 스택
>   - 공유 라이브러리
>   - 힙
>   - 초기화되지 않은 데이터(.bss)
>   - 초기화된 데이터(.data)
>   - 코드(.text)



### 4. ECF 및 메모리 보호

_MMU는 단순한 주소 변환뿐만 아니라 예외적 제어 흐름(ECF) 및 메모리 보호와도 깊이 연관됨._


- **페이지 폴트 처리**:
  MMU가 PTE를 확인했을 때,
  유효 비트(Valid Bit)가 설정되어 있지 않다면 (DRAM에 페이지가 캐시되어 있지 않다면),
  MMU는 **페이지 폴트(Page Fault) 예외**를 트리거하고 OS 커널로 제어를 전환


- **메모리 보호**: PTE에 포함된 권한 비트(Permission Bits, 예: SUP, READ, WRITE)를 MMU가
  검사하여, 해당 페이지에 대한 접근 권한(읽기/쓰기/커널 모드 접근)을 제어함.

  권한 위반 시 MMU는 **일반 보호 오류(General Protection Fault)**를 트리거하여 프로세스를
  종료시킬 수 있음



## VM의 기능

### VM의 기본 기능 및 DRAM 캐시의 역할

_VM은 메인 메모리(DRAM)를 효율적으로 사용하는 메커니즘이며, 주 메모리를 디스크에 저장된 주소
공간에 대한 캐시로 취급함_


- **메인 메모리(DRAM)를 캐시로 사용**

  - VM 시스템은 디스크에 저장된 가상 주소 공간을 위한 캐시로서 메인 메모리(DRAM)를 사용함
  - 이를 DRAM 캐시라고 칭하며, CPU와 메인 메모리 사이의 SRAM 캐시(L1, L2, L3)와 구분함
  - VM은 디스크에 있는 주소 공간의 활성 영역(active areas)만 메인 메모리에 유지하고,
    필요에 따라 디스크와 메모리 간에 데이터를 전송함



- **가상 페이지(VP)와 물리 페이지(PP)의 개념**

  - VM 시스템은 "디스크의 가상 메모리"를 고정 크기 블록인 **가상 페이지(VP)**로 분할함 (크기 $P = 2^p$ 바이트)
  - 물리 메모리(DRAM) 역시 동일한 크기 $P$ 바이트의 **물리 페이지(PP, 페이지 프레임)**로 분할됨

    > [!nt]  "디스크의 가상 메모리"
    > It means executable file's code, data, and other regions

    > [!nt] Page Size
    >
    > The page size P is calculated as `P = 2^p` bytes, where **`p` is the number of bits used for the
    > virtual page offset (VPO)** in the virtual address.
    > 
    > For example:
    > 
    > - If p = 12, P = 2^12 = 4096 bytes (4 KB, common in many systems).
    > - If p = 13, P = 2^13 = 8192 bytes (8 KB).


  - **Virtual Page Set** composed of three kinds:

    - **Cached pages**: These are pages that are currently loaded in physical memory (DRAM). They are
      active and can be accessed directly by the CPU without a page fault.

    - **Uncached pages**: These are virtual pages that have been allocated (e.g., via `malloc`) and
      have disk backing, but are not currently in physical memory.
      Accessing them triggers **a page fault**, causing the OS to load them from disk into RAM.

      - something yet-no-read from the disk.
      - virtual page for the area allocated with `malloc()`, but without physical page.

    - **Unallocated pages**: These are pages in the virtual address space that have not been assigned
      any physical memory or disk backing. Accessing them typically causes a segmentation fault or similar
      error, as they don't exist yet.



## DRAM 캐시 조직


### Page: Block of DRAM cache


- VM 시스템은 디스크의 데이터를 주 메모리에 캐싱하기 위해, **페이지 개념**을 도입
  - Virtual Page for Virtual Memory
  - Physical Page for Physical Memory



- **Virtual Page Size (VM Block Size)**
  - **(Typical) Size:** The size of a virtual page is usually between **4KB and 2MB**.
  - **Reason for Large Size:** The tendency for larger page sizes is **due to the very high miss penalty.**



### Page Fault: DRAM Cache Miss


- Page Fault Occurrence and Handling Process

  1. **Fault Detection (Trigger):**
     * When the CPU references virtual memory address A, the MMU (Memory Management Unit) reads the
       Page Table Entry (PTE).
     * If the **Valid bit** of the PTE is set to 0, it means the virtual page (VP) being referenced
       is not cached in DRAM, and the **MMU raises a page fault exception.**

  2. **Execution Handler:**
     * The page fault exception calls the **page fault exception handler** within the kernel.
     * The handler selects a victim page from physical memory (DRAM).

  3. **Data Exchange (Swapping/Paging):**
     * If the selected victim page is modified (Dirty), the kernel copies it to disk for storage.
     * The PTE of the victim page is modified to reflect that the VP is no longer cached in memory.
     * The kernel copies the copy of the requested VP stored on disk into physical memory.
       * **This method** of fetching pages from disk only on a miss **is called Demand Paging**.
     * The kernel updates the PTE of the new page
       - setting the valid bit
       - recording which physical page address

  4. **Instruction Restart:**
     * When the handler returns, it **restarts** the instruction that caused the fault.
     * Now that the required page is cached in physical memory, the MMU performs address translation normally,
       resulting in a page hit, and data access is completed.





- **Characteristics of DRAM Cache**
  Unlike SRAM caches (L1, L2, L3),
  DRAM caches have very high miss costs, leading to the following organizational features:

  - **Maximized Associativity**
    - DRAM caches use a **fully associative** approach.
    - This means any virtual page can be placed in any physical page (PP) in physical memory.
      (no hashing to find exact cache set index.)

  - **Importance of Replacement Policy**
    - Replacing the wrong virtual page and accessing the disk incurs a very high penalty.
    - Therefore, the OS uses much more sophisticated replacement algorithms
      than those used by hardware SRAM caches to minimize the likelihood of misses.

  - **Choice of Write Policy**
    - Due to the long disk access times, DRAM caches always use the **write-back** policy.
    - This is delaying updates to the disk when a write occurs to improve performance.




## 주소 변환 상세


### VA == VPN + VPO

- An n-bit virtual address (VA) is split by the MMU into two key parts:

  ```
  Virtual Address (VA) - n bits:
  +-----------------------------+--------------------------+
  | VPN (Virtual Page Number) | VPO (Virtual Page Offset)  |
  | (n-p bits)                | (p bits)                   |
  +-----------------------------+--------------------------+
  ```

  1. **Virtual Page Number (VPN)**
     * **Role:** Acts as an index to locate the page table entry (PTE) for the virtual page (VP).
     * **Composition:** Made up of the upper (n-p) bits of VA.

  2. **Virtual Page Offset (VPO)**
     * **Role:** Specifies the exact byte position **within the page.**
     * **Composition:** Consists of p bits, based on page size P=2^p.
     * **Physical Match:** Matches the physical page offset (PPO) and stays unchanged during address translation.




- The division of VPN and VPO is essential for locating the PTE and building the physical address.

  - **PTE Lookup (Using VPN)**
    - When VA arrives, the MMU extracts the VPN to index the current process's page table.
    - The PTE includes:
      - the valid bit (indicating if the virtual page is cached in DRAM)
      - and the physical page number (PPN).
    - If the valid bit is unset, a page fault (DRAM cache miss) occurs.

  - **Physical Address (PA) Generation (Using VPO)**
    - If the PPN(Physical Page Number) is found in the PTE, the MMU creates the final PA.
    - PA is formed by concatenating the PPN from the PTE and the VPO from the VA.
    - VPO acts like PPO, pinpointing the exact byte within the physical page.




- Address Partitioning Complexity and Optimization

  1. **Multi-Level Page Tables**
     * To reduce the memory requirements of a single page table,
       - a multi-level hierarchical structure is used,
       - where the VA is divided into multiple VPNs (e.g., VPN 1, VPN 2, ...) and a single VPO.
         - Hence, it is composed of multiple virtual page numbers at different depth.
     * Each VPN sequentially indexes each level of the page table to find the final PPN.

  2. **Cache Access Optimization**
     * Some systems speed up the address translation process by leveraging the characteristics of VPO.
     * The VPO bits of the VA are identical to the PPO bits of the PA.
       - So while the MMU translates the VPN in the TLB,
         the L1 cache can simultaneously start finding the cache index using the VPO bits.

     * For example, in Core i7 processors using 4KB pages, the VPO is 12 bits,
       which exactly matches the index and offset bits of the L1 cache, enabling this parallel processing.





### Page Table: array of PTE(Page Table Entry) with index of VPN









### PTE: 유효 비트, 물리 페이지 번호(PPN) 또는 디스크 주소 포함










### TLB (Translation Lookaside Buffer): PTE를 캐싱하여 변환 속도 향상










### 다단계 페이지 테이블: 큰 주소 공간에서 메모리 낭비 줄임












## Linux VM 시스템




- **Definition of Area:** Linux organizes virtual memory as a collection of areas(or segments).
  - An area is a contiguous chunk of allocated virtual memory.
  - The existence of areas allows **gaps** in the virtual address space, and the kernel does not
    track pages corresponding to these gaps, saving memory resources.

    - **gaps?:** Gaps refer to unallocated regions within a process's virtual address space (VAS).
      The VAS is a vast, contiguous range, but only portions are actively used. Areas are
      allocated as contiguous chunks, leaving gaps—unmapped holes between them.

      For example, after the code segment ends, there might be a large gap before the heap
      starts. This enables sparse allocation, where not every byte of the VAS needs to be backed
      by physical memory or tracked, optimizing for large address spaces with limited physical
      RAM. The kernel avoids creating page table entries (PTEs) for gaps, reducing overhead, and
      handles them via page faults if accessed (e.g., segmentation fault). This supports dynamic
      growth and prevents fragmentation.

- **Examples of Main Areas:** Each area exists for a specific purpose, including code, data,
  heap, shared libraries, and user stack segments.

- **Private Space per Process:** VM provides each process with a private and uniform address
  space, and each process maintains its own page table.



### standard process area layout


- **Kernel Virtual Memory:**
  - Located above the user stack,
  - contains kernel code and data structures,
  - and **is shared identically across all processes.**


- **User Virtual Memory:**
  | Area                       | Permissions | Typical Address Range | Description                                                                             |
  |----------------------------|-------------|-----------------------|-----------------------------------------------------------------------------------------|
  | User Stack                 | `RW `       | `0x7FFFFFFFFFFF`      | Starts below the largest legal user address and grows downward (to lower addresses).    |
  | Shared Libraries           | `R X`       | `0x7Fxxxxxxxxxx`      | Memory-mapped areas for dynamically linked shared objects at runtime (e.g., `libc.so`). |
  | Heap                       | `RW `       | `0x5xxxxxxxxxxx`      | Starts right after the `.bss` area and grows upward (to higher addresses).              |
  | Data(.bss)                 | `RW `       | `0x4xxxxxxxxxxx`      | Contains uninitialized global/static variables.                                         |
  | Data(.data)                | `RW `       | `0x4xxxxxxxxxxx`      | Contains initialized global/static variables.                                           |
  | Executable Code (.text)    | `R X`       | `0x400000xxxxxx`      | Contains the machine instructions for execution.                                        |
  | String Constants (.rodata) | `R  `       | `0x400000xxxxxx`      | Contains read-only string literals and constants.                                       |



### Kernel Data Structures for Area Management

Linux kernel uses the following data structures to manage each process's VM areas:

- **Task Structure (`task_struct`):**
  - Exists for each process and points to an `mm_struct` that characterizes the process's VM state. 
    - `mm_struct` is a component within `task_struct` for modularity,
      allowing efficient sharing like in threads.

  > [!nt] `task_struct` is the comprehensive process descriptor, containing much more than VM details.
  >
  > It includes:
  >   - pid
  >   - pc(program counter)
  >   - user stack pointer
  >   - fields for scheduling
  >   - signals
  >     - fields like `signal` (a pointer to a `signal_struct` for shared signal handling)
  >     - and `sighand` (for signal handlers).
  >   - file descriptors
  >   - credentials and more.
  >
  > Using only `mm_struct` wouldn't suffice for full task management



- **Memory Management Structure (`mm_struct`):**
  - `pgd`(Page Global Directory): A pointer to the base address of the level 1 page table .
  - `mmap`: A pointer to **a list of** `vm_area_struct` **instances**, each characterizing an area.

    - There are typically multiple `vm_area_struct` per process, one for each VM area
      like code, heap, stack, etc., linked in a list for efficient management.



- **Main Fields (`vm_area_struct`):**
  - `vm_start`: 영역의 시작
  - `vm_end`: 영역의 끝
  - `vm_prot`: 영역에 포함된 모든 페이지에 대한 읽기/쓰기 권한
  - `vm_flags`: 해당 영역의 페이지가 다른 프로세스와 공유되는지 여부
  - `vm_next`: 목록에서 다음 영역 구조체




- **Process Switching and Memory Protection**

  When the kernel schedules a process to run, it loads the `pgd` pointer from the process's
  `mm_struct` into the CR3(PTBR, page table base register) register.

  This ensures the process runs in its own private address space.



- **Page Fault Handling**

  When the MMU triggers a page fault (DRAM cache miss) for a virtual address, the kernel's page fault
  handler uses the process's `mm_struct` and `vm_area_struct` to:

  1. **Check Address Validity:** Verify if the virtual address (A) is between `vm_start` and `vm_end`
     of an area. If not, trigger a segmentation fault.
  2. **Check Access Permissions:** Confirm if the process has read, write, or execute rights based on
     `vm_prot`. If not, trigger a protection exception.
  3. **Handle Normal Faults:** If valid, the kernel fetches the page from disk, caches it in physical
     memory, and updates the page table.





## 메모리 매핑 (Memory Mapping)


- **Mapping:** It associates (maps) a disk file's contents with a region in the process's virtual
  address space (VAS). The file appears as if it's part of the VAS.

- **Loading into RAM:** Data isn't loaded entirely upfront. Instead, it's loaded on-demand: when you
  access a part of the mapped region, the OS fetches the corresponding page from disk into RAM
  (physical memory) via paging. This is efficient for large files.

- **Result:** You can read/write the file by accessing memory addresses directly, without explicit
  I/O calls. Changes can be flushed back to disk.


### Area Initialization via Memory Mapping

Linux initializes VM area contents by associating them with disk objects through **memory mapping**.

- **Regular File Mapping:**
  - Maps an area to a contiguous section of a regular disk file, such as an executable file.
  - Code and data areas are mapped to the `.text` and `.data` sections of the executable file.
  - Data is copied from disk to physical memory only when the page is first referenced(**demand paging**).
  - If the area is broader than file section, the OS fills with zero.

- **Anonymous File Mapping (Demand-Zero):**
  - Maps an area to an anonymous file created by the kernel, consisting only of binary zeros.
    - When a page in this area is first accessed (demand-zero), the kernel provides a physical page
      initialized entirely with binary zeros (all bits set to 0), ensuring clean, uninitialized memory
      without disk I/O.
  - `.bss` area, stack, and heap areas are typically initialized as demand-zero areas.
  - "demand-zero area" means 
    when a page is first accessed,
    the kernel overwrites the corresponding physical page with zeros(no disk transfer).


- **스왑 공간 (Swap Space)**
  - 가상 페이지는 일단 초기화되면, 커널이 관리하는 특별한 스왑 파일(swap area)을 통해 디스크와
    메모리 사이에서 스와핑(paging)됩니다.


- **Copy-on-Write (COW):**
  - The `fork` function creates exact copies of the parent's `mm_struct`, area structures, and page tables.
  - All pages in the new process are flagged as **read-only**, and areas are flagged as **private copy-on-write**.
  - This ensures that new pages are created only when either parent or child attempts to write to a page,
    maintaining the abstraction of private address spaces for both processes.
  - This approach optimizes memory usage by deferring allocations and enabling efficient process creation.



### VM 시스템에서의 MM 활용

MM은 VM이 제공하는 프로세스 추상화 및 관리 기능을 구현하는 데 필수적입니다.

- **로딩 단순화:**
  - Linux 로더는 실행 파일의 `.text` 및 `.data` 섹션에 대해 가상 페이지를 할당하고, PTE를
    유효하지 않음으로 표시한 다음, 해당 PTE가 객체 파일의 위치를 가리키도록 합니다.
  - 로더는 디스크에서 메모리로 데이터를 복사하지 않습니다. 데이터는 각 페이지가 참조될 때 VM
    시스템에 의해 자동으로 페이징됩니다.
  - `execve` 함수는 기존 영역을 삭제하고, 코드, 데이터, BSS 및 스택 영역에 대한 새 영역
    구조체를 생성하며, 이 영역들을 파일 섹션(코드/데이터) 또는 익명 파일(BSS, 스택/힙)에 매핑하여 새
    프로그램을 로드하고 실행합니다.

- **프로세스 생성 (`fork`)**:
  - `fork`가 호출될 때, 커널은 새 프로세스를 위해 부모 프로세스의 `mm_struct`, 영역 구조체,
    및 페이지 테이블의 정확한 복사본을 생성합니다.
  - 두 프로세스 모두에서 모든 페이지를 읽기 전용으로, 모든 영역을 사적 COW로 플래그를
    지정합니다. 이로써 쓰기가 발생할 때까지는 페이지가 공유되고, 쓰기 시에만 새 페이지가 생성되어
    사적인 주소 공간 추상화를 유지합니다.


### 사용자 수준 메모리 매핑 (`mmap`)

`mmap` 함수를 사용하여 명시적으로 **새로운 VM 영역을 생성하고 디스크 객체에 매핑**할 수 있습니다.

  - Using `mmap` to create and map a new VM area effectively allocates previously unallocated
    virtual pages, setting up page table entries (PTEs) that reference disk-backed storage
    (e.g., a file or anonymous zero-filled pages).

    This transitions them to uncached pages(from unallocated pages), which can be loaded into
    physical memory on demand via page faults.
  


- **함수 시그니처:**

  - `void *mmap(void *start, size_t length, int prot, int flags, int fd, off_t offset);`



- **접근 권한 (`prot`):** 매핑된 영역에 대한 읽기/쓰기 권한을 설정합니다.

  - `PROT_READ`: 페이지를 읽을 수 있습니다.
  - `PROT_WRITE`: 페이지에 쓸 수 있습니다.
  - `PROT_EXEC`: 페이지의 명령을 실행할 수 있습니다.



- **플래그 (`flags`):** 객체의 유형과 공유 여부를 설정합니다.

  - `MAP_ANON`: 익명 객체(demand-zero)로 설정합니다.
  - `MAP_PRIVATE`: 사적 COW 객체를 생성합니다.
  - `MAP_SHARED`: 공유 객체를 생성합니다.



- **munmap 함수:**
  가상 주소 `start`에서 시작하여 `length`만큼의 VM 영역을 삭제합니다. 이 영역에 대한 후속
  참조는 세그멘테이션 오류를 발생시킵니다.



## 동적 메모리 할당 (Dynamic Memory Allocation)


















---

## C 프로그램의 메모리 버그
