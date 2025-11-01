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


### Introduction

- **Purpose**
  - Dynamic memory allocation provides C programmers with the capability to acquire additional
    virtual memory during program execution.
  - It is used when data structure sizes are unknown until run time.

- **The Heap Area**
  - The dynamic memory allocator manages **a specific area** of a process’s virtual memory known as the **heap**.
  - The heap is generally assumed to be an area of demand-zero memory that starts after the
    uninitialized data area and grows upward (toward higher addresses).
    >
    > 1. **Does "demand-zero" mean it initializes all values with 0?**  
    >    Yes, in virtual memory systems, demand-zero pages are initialized to zero when first accessed
    >    (e.g., via page faults). This ensures security by preventing exposure of old data. However, this is
    >    at the OS level for page allocation, not directly tied to user-level functions like `malloc`.
    >
    > 2. **Why does `malloc` return garbage values despite demand-zero memory?**  
    >    `malloc` allocates memory from the heap but does not initialize it; it simply marks the space
    >    as available and returns a pointer. The contents are whatever residual data was in those bytes
    >    (often called "garbage" or uninitialized). Demand-zero applies to new pages mapped by the OS, but if
    >    the heap reuses already-allocated pages, they retain old values. For zero-initialized allocation,
    >    use `calloc` instead, which explicitly sets bytes to 0.

  - The kernel maintains the variable `brk` (break), which points to the top of the heap.
  


- **Heap Organization**
  - The heap is organized as a collection of various-size **blocks**.
  - Blocks are contiguous chunks of virtual memory and are categorized into two states:
    - **Allocated**: Reserved explicitly for application use.
    - **Free**: Available to be reserved by the allocator.
      >
      > - Free blocks in the heap are part of the process's virtual address space, which has been
      >   allocated and "mapped" by the OS. This means the virtual pages containing these blocks are valid and
      >   accessible in the virtual memory space.
      > - However, these virtual pages are not necessarily backed by physical RAM until the process
      >   actually accesses them (e.g., via a read or write). At that point, a page fault occurs, and the OS
      >   allocates physical memory (or swaps in from disk if needed). Until then, the pages are "allocated
      >   virtually" but not physically mapped.


- **Allocator Styles**
  - Allocators differ based on who is responsible for returning allocated memory:
    - **Explicit Allocators:** Require the application to explicitly free blocks (e.g., the C standard library `malloc`/`free`).
    - **Implicit Allocators (Garbage Collectors):** Automatically detect blocks no longer in use and free them (e.g., in languages like Java or Lisp).



### 9.9.1 The `malloc` and `free` Functions


- **The `malloc` Function**
  - **Provider:** It is the primary function provided by the C standard library's explicit allocator, often referred to as the `malloc` package.
  - **Interface:** `void *malloc(size_t size)`.
  - **Return Value:** Returns a pointer to a block of memory on the heap of at least `size` bytes.
  - **Initialization:** `malloc` does not initialize the memory it returns. (A related function, `calloc`, initializes memory to zero).
  - **Error Handling:** If `malloc` encounters a problem (e.g., requesting more memory than available), it returns `NULL` and sets `errno`.
  - **Alignment Requirement:** The returned block address must be suitably aligned for any type of data object.
    - In 32-bit mode (`gcc -m32`), the address is always a multiple of 8.
    - In 64-bit mode (the default), the address is always a multiple of 16.

    > [!nt]
    >
    > `errno` is a global integer variable defined in the C standard library (typically in `<errno.h>`).
    > It stores error codes set by system calls or library functions (like `malloc`) when they fail,
    > indicating the specific type of error (e.g., `ENOMEM` for out-of-memory). You can check
    > its value after a function call to diagnose issues, often using `perror()` or `strerror()`
    > for human-readable messages.


- **The `free` Function**
  - **Purpose:** Programs free previously allocated heap blocks by calling the `free` function.
  - **Interface:** `void free(void* ptr)`
  - **Argument Requirement:** The `ptr` argument must point to the beginning of an allocated block obtained from `malloc`, `calloc`, or `realloc`.
  - **Undefined Behavior:** If `ptr` does not point to **the beginning of a valid allocated block,** the behavior of `free` is undefined.
  - **Return Value:** `free` returns **nothing** and gives **no indication** to the user if an error occurs.


- **Related Allocation Functions**
  - `calloc`: A wrapper around `malloc` that initializes the allocated memory to zero.
  - `realloc`: Used to change the size of a previously allocated block.



- **Heap Management Mechanisms**
  - Dynamic memory allocators can acquire or deallocate memory using two primary methods:
    - Explicitly using `mmap` and `munmap` functions.
    - Using the `sbrk` function to grow or shrink the heap.
  - The `sbrk` function grows or shrinks the heap by adding `incr` to the kernel's `brk` pointer. If successful, it returns the old value of `brk`.

  > [!qt] If I get the memory space by the mmap, will not that grow up my heap space size?
  >   󱞪 
  >
  > No, using `mmap` to allocate memory does not necessarily grow the heap space size.
  >
  > Here's why:
  > 
  > - The heap is a contiguous region in the virtual address space, typically grown by `sbrk` (as
  >   described in the Vimwiki excerpt), which adjusts the `brk` pointer to extend or shrink the
  >   heap boundary.
  > - `mmap` can allocate memory anywhere in the virtual address space, often anonymously or from
  >   files, without modifying the heap. For example, dynamic memory allocators (like `malloc` in
  >   glibc) may use `mmap` for large allocations to avoid heap fragmentation, but this creates
  >   separate mapped regions outside the heap, not expanding it.
  > - The heap size remains unchanged unless `sbrk` is used to explicitly adjust it.




### 9.9.2 Why Dynamic Memory Allocation?


The primary reason programs utilize dynamic memory allocation is related to **runtime data size uncertainty**.

- **Handling Unknown Sizes**
  - Programs often do not know the required sizes of certain data structures until the program actually executes.
  - Dynamic allocation resolves this by allocating memory at runtime, after the size becomes known.
  - When memory is allocated dynamically, the maximum size is limited only by the amount of available virtual memory.

- **Issues with Static (Hard-Coded) Allocation**
  - Defining array sizes statically with hard-coded maximums is often inefficient or flawed.
  - Hard-coded limits are arbitrary and bear no relation to the actual available virtual memory on the machine.
  - If user input exceeds a hard-coded maximum, the only solution is typically to recompile the program with a larger limit.
  - Hard-coded array bounds can create a maintenance nightmare in large software products.

- **Conclusion**
  - Dynamic memory allocation is an important programming technique necessary for using allocators correctly and efficiently.


### 9.9.3 Allocator Requirements and Goals


Allocators must operate under strict constraints and aim for specific performance goals:

#### Allocator Requirements (Constraints)

- **Arbitrary Request Sequences:**
  The allocator must handle any sequence of `malloc` and `free` requests. It cannot
  assume requests are nested or ordered.

- **Immediate Response:**
  Allocation requests must be satisfied immediately. The allocator cannot buffer or reorder
  requests for performance.

- **Heap-Only Data Structures:**
  Any nonscalar structures (like free lists) used by the allocator must be stored entirely
  within the heap for scalability.

  > [!qt] If so, can a scalar data be stored outside of the heap?
  >
  > Yes, scalar data (such as simple variables like integers or pointers) can be stored outside the
  > heap, as the constraint only specifies that nonscalar structures (e.g., free lists) must reside
  > entirely within the heap for scalability. Scalars are typically managed on the stack or in
  > global/static memory by the allocator's runtime code.
  >
  > for example, 
  >
  > - **Global/static variables**: 
  >   - Pointers to the heap's start or end (e.g., `heap_start` or `heap_end`).
  >   - Counters for allocation statistics (e.g., total bytes allocated).
  >   - Configuration flags (e.g., alignment requirements).
  > 
  > - **Stack variables**:
  >   - Function parameters in allocation routines (e.g., `size` in `malloc(size_t size)`).
  >   - Local pointers or indices used temporarily during allocation/free operations (e.g., a loop
  >     counter for searching free blocks).


- **Block Alignment:**
  Allocated blocks must be aligned to hold any type of data object.

- **No Modification of Allocated Blocks:**
  Allocators may only change free blocks. Allocated blocks cannot be moved or modified (no compaction).



#### Allocator Performance Goals

- Allocators balance two **conflicting** goals: 
  maximizing throughput and maximizing memory utilization.

- **Maximizing Throughput:**
  Complete as many allocation/free requests per unit time as possible. Minimize average
  time for each request.
  _Note: Some allocators have allocation time linear in free blocks, but freeing can be constant time._

- **Maximizing Memory Utilization:**
  Virtual memory is limited (by swap space), so it must be used efficiently.

  > [!qt] lots of swap space means lots of virtual memory, right? although it could be so slow.
  >
  > Yes, more swap space increases the total available virtual memory by providing additional
  > backing store beyond physical RAM, allowing processes to use more memory than physically installed.
  > However, this comes at a cost: accessing data in swap causes slower performance due to disk I/O,
  > potentially leading to thrashing if overused.


    - **Peak Utilization ($U_{n-1}$):** Defined as the maximum ratio of total allocated payload ($P_k$) 
      to current heap size ($H_k$) over all requests. The goal is to maximize peak utilization
      throughout the program's life.




### 9.9.4 Fragmentation

Fragmentation is the primary reason for poor heap utilization
and occurs when unused memory cannot be made available
to satisfy allocation requests.


- **Internal Fragmentation**

    - **Description:**
      Internal fragmentation happens when the allocated memory block is larger than the
      actual data (payload) requested by the application.

    - **Causes:**
        - The allocator enforces a minimum block size that exceeds the requested payload.
        - Extra padding is added to meet alignment requirements.

    - **Quantification:**
      Internal fragmentation is easy to measure: it is the total difference between the sizes
      of allocated blocks and their payloads. This value depends on previous allocation
      patterns and the allocator's design.



- **External Fragmentation**

    - **Description:**
      External fragmentation occurs when enough free memory exists in total, but there is no
      single contiguous free block large enough to fulfill a request.

    - **Quantification Difficulty:**
      Measuring external fragmentation is challenging. It depends on the history of previous
      allocations, the allocator's strategy, and the unpredictable nature of future requests.

    - **Mitigation:**
      Allocators often use heuristics to keep a few large free blocks available, rather than
      many small ones, to reduce external fragmentation.



### 9.9.5 Implementation Issues


#### 9.9.5 Implementation Issues

Before exploring specific free list organizations, it is important to understand the core challenges
that all practical memory allocators must solve:

- **Key Challenges for Practical Allocators**

    - A simple allocator that only returns pointers and never reuses memory blocks can achieve
      high speed, but results in very poor memory utilization.

    - Practical allocators must balance speed (throughput) and memory usage (utilization) by
      addressing these main issues:

        - **Free Block Organization:**
          How the allocator tracks which memory blocks are available for allocation.

        - **Placement:**
          How the allocator selects a suitable free block to fulfill a new allocation request.

        - **Splitting:**
          How the allocator handles leftover space when a large free block is partially used for
          a smaller allocation.

        - **Coalescing:**
          How the allocator merges a newly freed block with neighboring free blocks to reduce
          fragmentation.


#### 9.9.9 Getting Additional Heap Memory

This section explains what happens when the allocator cannot fulfill a memory request with the
current free space.

- **When More Heap Memory Is Needed**

    - If the allocator cannot find a free block large enough for a request, even after merging
      adjacent free blocks, it must obtain more memory **from the operating system.**

    - The allocator requests extra heap space by calling the `sbrk` function.

    - **Processing New Memory:**

        - The allocator converts the newly acquired memory into a single large free block.
        - This new block is added to the free list.
        - The requested memory is then allocated from this new block.



### 9.9.6 Implicit Free Lists



- **Organization and Structure**

    - The heap is a continuous sequence of allocated **and** free blocks.

    - Essential allocator data (block boundaries, allocation status) is stored inside each block.

    - **Implicit Linkage:**
      Free blocks are linked by the size fields in block headers. The allocator must **scan
      every block** in order to find free space.

    - **End Marker:**
      The heap ends with a special block (**epilogue** header) marked as allocated and size zero.
      This helps manage edge cases during traversal and coalescing.




- **Block Format**

    - Each block contains a header, the payload (requested memory), and optional padding.

    - **Header Encoding:**
      The header is one word and stores:
        - Block Size: Covers header, payload, and padding.
        - Allocated Bit: Shows if the block is allocated (1) or free (0).

    - **Alignment Constraint:**
      With double-word alignment (block size is a multiple of 8 bytes), **the lowest bits** of
      the header can **store the allocation status.**




- **Minimum Block Size**

    - Block format and alignment rules set a minimum size for all blocks.
    - Example: With double-word alignment, the minimum block size is two words (8 bytes),
      enough for the header and alignment, even if only 1 byte is requested.

      > [!qt] why not 1 word?
      >
      > One word (4 bytes) wouldn't suffice due to the header and alignment constraints. This design
      > prioritizes simplicity and performance over minimal space.
      > 
      > - **Header space**: The header (typically 4 bytes) stores size and allocation info.
      > 
      > - **Alignment requirement**:
      >   Blocks must align to 8-byte boundaries for efficient memory access (e.g., on 64-bit systems).
      >   Even for a 1-byte request, you need the 4-byte header + 1 byte payload + 3 bytes padding to reach
      >   the next 8-byte boundary, totaling 8 bytes.
      > 
      > - **Avoid fragmentation**: Smaller blocks (e.g., 4 bytes) would violate alignment, leading to
      >   inefficiencies or crashes.
      > 
      > - **Standard "Block Size" design:**
      >   (29 bits for size, 3 bits for other uses): Max size = 536,870,911 bytes (0x1FFFFFFF)




- **Performance Implications**

    - **Advantage:** Simple design.
    - **Disadvantage:**
      Searching for free space takes time proportional to the total number of blocks(`O(n)`) in the heap (linear time).



### 9.9.7 Placing Allocated Blocks


When an application requests memory, the allocator **searches the free list** for a block large enough
**to fulfill the request.** The method used for this search is called the **placement policy**.


- **Placement Policies**

    - **First Fit**
      >
      > - **Mechanism:** Starts searching from the beginning of the free list and selects the first
      >   block that is large enough for the request.
      >
      > - **Trade-offs:**
      >     - Large free blocks tend to remain at the end of the list.(cause front entries get splitted)
      >     - Small fragments ("splinters") may accumulate at the start, **making future searches for
      >       large blocks slower.**


    - **Next Fit**
      >
      > - **Mechanism:** Similar to First Fit, but begins searching from where the previous search
      >   ended, not from the start of the list.
      >
      > - **Trade-offs:**
      >     - May be faster than First Fit.
      >     - Research indicates it can lead to poorer memory utilization compared to First Fit.


    - **Best Fit**
      >
      > - **Mechanism:** **Checks every free block** and selects the smallest block that is still large
      >   enough for the request.
      >
      > - **Trade-offs:**
      >     - Often provides better memory utilization than First Fit and Next Fit.
      >     - Requires a full search of the heap with simple free list structures, which can slow
      >       down allocation.



### 9.9.8 Splitting Free Blocks


Once an allocator locates a free block large enough to satisfy an allocation request,
it must **decide** how much of that block to allocate, a choice that determines the **splitting policy**.


- **Policy Options**

    - **Use the Entire Free Block:**
      This method is straightforward and fast. However, it can cause internal fragmentation,
      especially when the free block is much larger than the requested size.

    - **Split the Free Block (Typical Approach):**
      If the free block is much larger than needed, the allocator usually splits it into two parts.
      The first part is allocated to the request, and the remainder becomes a new, smaller free block.




- **Conditions for Splitting**

    - Splitting happens only if the leftover space is large enough to form a valid new free block.
      (The remainder must be at least the allocator's minimum block size.)

        - *Example:* In a simple allocator, the minimum block size is 16 bytes (`2 * DSIZE`). If the
          leftover space is less than this, the allocator uses the entire block to prevent creating
          unusable fragments.

          > [!qt] why double word size?
          > 
          > - **Overhead Requirement**
          >     - The simple allocator uses an implicit free list with boundary tags. This means every block,
          >       whether allocated or free, contains both a header and a footer.
          >     - Each header and footer is 4 bytes (`WSIZE`), resulting in a total overhead of 8 bytes
          >       (`DSIZE`) per block.
          > 
          > - **Alignment Requirement**
          >     - The allocator enforces **double-word alignment**, so all blocks must have sizes that are
          >       multiples of 8 bytes (`DSIZE`).
          >     - When allocating memory, the implementation adjusts the requested size to account for both
          >       overhead and alignment.
          >     - The code ensures a minimum block size of 16 bytes (`2 × DSIZE`): 8 bytes for the header and
          >       footer overhead, plus 8 bytes to meet the double-word alignment requirement.
          >     - Even if the application requests only 1 byte, the allocator will create a block of at least
          >       16 bytes.




> [!nt]
>
> Internal fragmentation occurs when allocated memory blocks are **larger than needed,** wasting space.
> Splitting helps reduce this, but only if the leftover block is usable according to the allocator's
> minimum size requirement.



### 9.9.9 Getting Additional Heap Memory


How allocators acquire additional space when existing free blocks are insufficient.


- **Triggering the Request**

    - If the allocator cannot find a free block large enough for an allocation, it requests more
      heap memory **from the kernel.**

    - This happens even **after the allocator tries to coalesce** (merge) adjacent free blocks.




- **Mechanism**

    - The allocator obtains additional memory by calling the `sbrk` function.





- **Processing the New Memory**

    - The newly acquired memory is converted into a single large free block.
    - This block is added to the free list.
    - The allocator then allocates the requested block from this new free block.
      >
      > This could be the pit fall losing efficiency? Like, no need to search again or so.




### 9.9.10 Coalescing Free Blocks


Coalescing is the process of merging adjacent free memory blocks to combat fragmentation.


- **False Fragmentation**

    - When a block is freed, it may be next to other free blocks.
    - If these adjacent free blocks are not merged, this causes **false fragmentation**.
    - False fragmentation means free memory is split into small blocks that cannot be used for
      larger allocations, even if the total free memory is enough.




- **Coalescing Requirement**

    - Practical allocators must **merge adjacent free blocks** (coalescing) to prevent false
      fragmentation.





- **Coalescing Timing Policies**

    - Allocators must choose when to merge free blocks:

        - **Immediate Coalescing:** Adjacent free blocks are merged right away when a block is freed.

            - This is simple to implement.
            - However, certain patterns can cause "thrashing," where blocks are merged and then
              split again repeatedly.

              > For example,
              >
              > 1. Allocate block A (size 100 bytes).
              > 2. Allocate block B (size 100 bytes, adjacent to A).
              > 3. Free block A → A becomes free, but B is still allocated, so no merge yet.
              > 4. Free block B → Now both A and B are free and adjacent, so immediate coalescing merges them into one free block (200 bytes).
              > 5. Allocate a new block C (size 150 bytes) → Splits the merged block into a used 150-byte block and a free 50-byte remnant.
              > 6. Free block C → Merges the 150-byte block with the 50-byte remnant (back to 200 bytes free).
              > 7. Repeat steps 5-6 in a loop.
              > 
              > This pattern causes thrashing because the allocator repeatedly merges (step 4 and 6) and splits
              > (step 5) the same blocks, wasting CPU on unnecessary operations.



        - **Deferred Coalescing:** Merging is delayed until later.

            - For example, coalescing may occur only after an allocation fails, triggering a scan
              of the heap to merge all free blocks.





### 9.9.11 Coalescing with Boundary Tags


A technique to achieve constant-time merging of adjacent free blocks.



- **An allocator must merge adjacent free blocks (coalescing) to avoid false fragmentation:**

    - **Merging Next Block:**
      Merging with the next adjacent block is simple because its header is easily accessible.

    - **Merging Previous Block:**
      Merging with the previous block is difficult when using basic structures like an implicit
      free list. It requires a linear search through the heap to find the previous block's
      status and location.



- **Boundary Tags: The Solution**

    - Knuth introduced **boundary tags**, a technique that enables constant-time coalescing
      with the previous block.

    - This method adds a **footer** (boundary tag) at the end of each block, duplicating the
      header's information (size and allocation status).

    - By checking the footer, which is always one word away from the start of the current block,
      the allocator can quickly determine the previous block's location and status.





- **Immediate Coalescing Cases**

    - Boundary tags allow constant-time coalescing, regardless of the allocation status of
      neighboring blocks.

    - When freeing a block, the allocator considers four possible cases for the previous and
      next blocks:

        - Case 1: Both previous and next blocks are allocated.
            - Action: Only the current block's status is changed to free (no merging).

        - Case 2: Previous block is allocated, next block is free.
            - Action: Merge the current block with the next block.

        - Case 3: Previous block is free, next block is allocated.
            - Action: Merge the previous block with the current block.

        - Case 4: Both previous and next blocks are free.
            - Action: Merge all three blocks into a single free block.





- **Trade-offs and Optimization**

    - **Memory Overhead:**
      Using both a header and a footer increases memory usage, especially for small blocks
      (the header and footer may consume a large portion of the block).

    - **Optimization:**
      Allocated blocks do not need a footer if the previous block's allocation status is stored
      in a spare bit of the current block's header. This saves space, but free blocks still
      require footers.





### 9.9.12 Putting It Together: Implementing a Simple Allocator


This section describes the implementation of a simple, explicit dynamic memory allocator (`mm.c`)
designed for demonstration purposes, utilizing the previously discussed concepts,
especially,
  - the implicit free list
  - and immediate boundary-tag coalescing.



- **General Allocator Design**

    - **Base:** Uses an implicit free list with immediate boundary-tag coalescing.


    - **Interface:**
        Provides three main functions:
        - `mm_init`: Initializes the allocator.
        - `mm_malloc`: Allocates memory.
        - `mm_free`: Frees memory.


    - **Memory Model:**

        Interacts with a simulated memory system (`memlib.c`) to avoid conflicts with the system's
        `malloc`.
        - `mem_init`: Sets up the heap as a large, double-word aligned array.
        - `mem_sbrk`: Simulates the system `sbrk` to extend the heap, but does not allow shrinking.




- **Heap Structure and _Invariant Form_**

    - Maintains a strict heap structure, anchored by `heap_listp`, which always points to the
      prologue block.

    - The heap structure includes:
        - **Alignment Padding:** An unused word at the start for double-word alignment.
        - **Prologue Block:** An 8-byte allocated block (header + footer), created at initialization
          and never freed.
        - **Regular Blocks:** Allocated or free blocks managed by `malloc` or `free`.
        - **Epilogue Header:** _A zero-size header_ at the end, always marked as allocated.

    - Prologue and epilogue blocks help eliminate edge cases during coalescing.


    > [!nt]
    >
    > **Invariant Form in Heap Structure**
    >
    > In this context, "invariant form" refers to a specific, unchanging organization of the heap
    > maintained by the allocator at all times. This structure ensures that certain conditions and
    > layouts—such as the presence of alignment padding, prologue block, regular blocks, and epilogue
    > header—are always present and correctly ordered.
    >
    > By keeping this invariant, the allocator can reliably perform operations like allocation,
    > freeing, and coalescing without encountering unexpected edge cases or errors.
    >
    > Maintaining the invariant form simplifies the logic for managing memory and helps prevent bugs
    > related to boundary conditions.


    > [!nt]
    >
    > **Zero-Size Header (Epilogue Header) Explanation**
    >
    > The "zero-size header" refers to a special header block placed at the very end of the heap. Its
    > size field is set to zero, and it is always marked as allocated. This header does not correspond
    > to any actual memory block for use—it simply acts as a sentinel or marker to indicate the end of
    > the heap.
    >
    > The purpose of the epilogue header is to simplify memory management operations, such as traversing
    > the heap or coalescing free blocks, by providing a consistent boundary at the end. Since it is
    > always allocated and has zero size, it prevents accidental merging or access beyond the heap's
    > limits.





- **Block Minimum Size and Alignment**

    - Minimum block size is **16 bytes** (`2 * DSIZE`).

    - This covers:
        - 8 bytes overhead (header + footer).
        - Ensures double-word alignment (size is a multiple of 8 bytes).





- **Core Utility Macros**

    - Macros are used for pointer arithmetic and memory manipulation:

        - `PACK`: Combines size and allocation bit into one word.
        - `GET` / `PUT`: Reads or writes a word at address `p`.
        - `HDRP` / `FTRP`: Returns the header or footer address for a block pointer (`bp`).
        - `NEXT_BLKP` / `PREV_BLKP`: Returns the next or previous block pointer in memory.






- **Heap Initialization (`mm_init` and `extend_heap`)**

    - `mm_init` uses `mem_sbrk` to allocate four words for padding, prologue header, prologue
      footer, and epilogue header.

    - Then, it calls `extend_heap` to grow the heap by `CHUNKSIZE` bytes and create the initial
      large free block.

    - `extend_heap` rounds the requested size for alignment, initializes the new free block's
      header/footer, creates the new epilogue header, and immediately coalesces if the previous
      block was free.






### 9.9.13 Explicit Free Lists



- **Structure and Organization**

    - **Data Structure:**
      Free blocks are managed using an explicit data structure, typically a doubly linked list.
      This list is stored within the free blocks themselves.

    - **Pointers:**
      Each free block contains two pointers: `pred` (predecessor) and `succ` (successor).

    - **Overhead:**
      Free blocks must be large enough to hold the pointers, a header, and possibly a footer.
      This **increases the minimum block size** and can lead to more internal fragmentation.





- **Performance Benefits**

    - Using a doubly linked list for free blocks reduces allocation search time for first-fit
      policies. **The search becomes linear in the number of free blocks, not the total number of
      blocks.**

    - The time to free a block depends on how the list is ordered. Some policies allow freeing
      in constant time.







- **Free List Ordering Policies**


    - **LIFO (Last-In, First-Out) Ordering**

        - **Mechanism:**
          Newly freed blocks are added to the **start** of the list.

        - **Speed:**
          Freeing a block is a constant-time(`O(n)`) operation. If boundary tags are used, coalescing
          is also constant time.

        - **Allocation Search:**
          First-fit searches check the most recently freed blocks first.


        - **Drawback:**
          However, constant-time freeing policies like LIFO or unordered insertion can reduce utility.
          Since blocks are not organized by address or size, it may be harder to find a good fit for future
          allocations. This can lead to increased fragmentation and less efficient memory usage.



    - **Address-Ordered Ordering**

        - **Mechanism:**
          The list is kept in increasing order of memory addresses.

        - **Speed:**
          Freeing a block requires a linear-time search to find its predecessor.

        - **Utilization:**
          Address-ordered first-fit usually results in **better memory usage,** similar to best-fit
          policies.

          > [!nt] Why Address-Ordered First-Fit Improves Utilization
          >
          > - **Locality of Free Blocks:**
          >   Keeping the free list sorted by address means adjacent free blocks are next to each other
          >   in the list. This makes it easier to merge (coalesce) neighboring blocks when they are freed.
          >
          > - **Reduced Fragmentation:**
          >   When searching for a block to allocate, address-ordered first-fit tends to select blocks
          >   that are close together in memory. This helps minimize leftover small fragments and
          >   improves overall memory utilization.
          >
          > - **Similarity to Best-Fit:**
          >   Since the list is ordered by address, the first-fit search often finds the smallest
          >   available block that fits the request, which is similar to the best-fit strategy.
          >   This leads to efficient use of memory and less wasted space.
          >
          >   - In an address-ordered free list, blocks are arranged by their memory addresses, not by size.
          >   - When you use first-fit allocation, you scan from the start of the list and pick the first block
          >     that is big enough for your request.
          >   - Because adjacent free blocks are merged quickly (since they're next to each other in the list),
          >     the list tends to have fewer, larger blocks and fewer tiny fragments.
          >   - As a result, the first block that fits your request is often close in size to what you need,
          >     just like best-fit (which always picks the smallest possible block).
          >   - This means address-ordered first-fit can use memory efficiently, almost as well as best-fit,
          >     but with less searching and overhead(compared to best-fit).







### 9.9.14 Segregated Free Lists



- **Core Concept: Segregated Storage**

    - Segregated storage uses multiple free lists instead of just one.
    - Each free list, called a **size class**, contains blocks of roughly the same size.
    - All possible block sizes are divided into these size classes (often by powers of 2).
    - The allocator keeps an array of free lists, sorted by increasing block size.

    > [!nt] Storage of Segregated Free Lists
    >
    > - In most implementations, the array of free lists for segregated storage is stored as a
    >   **global or static variable.**
    > - This allows the allocator to access and manage the free lists efficiently throughout the
    >   program's execution.
    > - Using the `static` qualifier (in C/C++) ensures the free lists persist for the lifetime of
    >   the program and are only accessible within the allocator's source file (if declared with
    >   file scope).




- **Allocation Process with Segregated Storage**

    - To allocate a block of size $n$, the allocator checks the free list for $n$'s size class first.
    - _If no suitable block is found,_ it searches the next larger size class, continuing as needed.

    > [!qt] When Is "No Suitable Block Found"?
    >
    > "No suitable block is found" means either:
    >
    > - The free list for the requested size class is empty (no blocks available).
    > - All blocks in that size class are too small to satisfy the allocation request (if the
    >   size class contains a range of sizes).




#### Variants of Segregated Storage


##### Simple Segregated Storage (SSS)

- **Structure:** Every block in a free list is exactly the same size (the largest for that class).
- **Splitting/Coalescing:** Blocks are never split or merged when freed.
- **Performance:** Both allocation and freeing are constant-time operations.
- **Overhead:** Very low memory overhead; only a single pointer may be needed in free blocks.
- **Fragmentation:** Prone to internal fragmentation (no splitting) and external fragmentation (no merging).


##### Segregated Fits (SF)

- **Structure:** Each list may contain blocks of different sizes within the same size class.
- **Policies:**
    - Allocation uses a first-fit search in the relevant list.
    - Blocks are split if a suitable fit is found.
    - Freed blocks are merged (coalesced) and returned to the correct list.
- **Efficiency:** Widely used in production allocators (e.g., GNU `malloc`) for speed and memory efficiency.

> [!nt]
>
> Immediate coalescing in segregated fits helps reduce fragmentation, but it does not fully prevent
> thrashing if allocations and frees happen rapidly for small blocks. Thrashing can still occur if
> the allocator repeatedly splits and merges blocks for tiny requests, leading to overhead and
> possible performance issues.
>
> However, immediate coalescing does help maintain larger free blocks, which can mitigate some
> fragmentation effects.


> [!nt] Tuneing
>
> - "Tune" refers to adjusting the allocator's parameters or policies to optimize performance
>   and memory usage for specific workloads or environments.
>
> - Examples of tuning include:
>     - Setting thresholds for when to split or coalesce blocks.
>     - Choosing the size and number of segregated free lists.
>     - Deciding between immediate or deferred coalescing.
>     - Adjusting alignment requirements for allocated blocks.
>     - Modifying search strategies (e.g., first-fit, best-fit).
>
> - Tuning helps the allocator perform efficiently for the application's typical allocation and
>   free patterns.


###### QnA: "segregated allocator"-wise

> [!qt] How to order those multiple free lists then? by address? Or, won't it needed?
> 
> - **Ordering Multiple Free Lists in Segregated Storage**
> 
>     - In segregated storage, each free list represents a different size class.
> 
>     - **Ordering Within Each Free List:**
>         - The blocks in a free list can be ordered in several ways:
>             - **Unordered:** Blocks are simply linked together with no specific order.
>             - **Address-Ordered:** Blocks are sorted by their memory addresses, which helps with
>               coalescing adjacent blocks.
>             - **Size-Ordered:** Less common, since all blocks in a size class are usually similar
>               in size.
> 
>     - **Is Ordering Needed?**
>         - Ordering by address is helpful if you want to coalesce adjacent free blocks efficiently.
>         - If coalescing is not a priority, unordered lists are simpler and allow constant-time
>             insertion and removal.
>         - The choice depends on the allocator's goals: simplicity and speed vs. fragmentation
>             control.


> [!qt] I wonder what is the checker of the merging when a block is freed. Check prev or next blocks? Or, place in the classified free-list first?
>
> - When a block is freed, the allocator typically checks both the previous and next blocks in
>   memory to see if they are also free.
>
> - If either neighbor is free, the allocator merges (coalesces) the blocks into a single,
>   larger free block.
>
> - This merging is usually done before placing the resulting block into the appropriate
>   size-class free list.
>
> - **Process:**
>     1. Free the block.
>     2. Check adjacent blocks (previous and next) for free status.
>     3. Merge with any free neighbors.
>     4. Insert the merged block into the correct free list for its new size.
>
> - This approach helps reduce fragmentation and keeps the free lists organized by block size.


> [!qt] Maybe recursively?
>
> Yes, recursive coalescing is normal and necessary in memory allocators. Sequential free blocks can
> still occur if multiple adjacent blocks are freed before coalescing happens. For example, if three
> blocks are freed one after another, recursive coalescing ensures they are merged into a single
> large block. Even with proper merging, allocation and free patterns can create sequences of free
> blocks, so recursive coalescing helps maintain large contiguous free regions.



> [!qt] Does free() Run Recursively or Iteratively?
>
>   - In most allocators,
>     the `free()` function may use an iterative or recursive approach to coalescing:
>
>       - **Iterative Coalescing:** After freeing a block, the allocator checks adjacent blocks
>         and merges with any that are free. If merging creates a new block that is adjacent to
>         another free block, the process repeats until no more merges are possible. This is
>         usually done with a loop.
>
>       - **Recursive Coalescing:** Some allocators may implement this as a recursive function,
>         but iterative loops are more common for simplicity and stack safety.
>
>   - The merging happens within a single `free()` call, not across multiple calls.
>   - When the next `free()` is called, the process repeats for the newly freed block.
>   - So, coalescing is completed during each `free()` operation, not deferred to future frees.




##### Buddy Systems Explained

- **Structure:**
    - Buddy systems are a type of memory allocator that organizes free memory into blocks
      whose sizes are all powers of 2 (e.g., 16, 32, 64 bytes).
    - Each size class contains blocks of exactly that size.

- **Allocation:**
    - When a memory request is made, the allocator rounds the requested size up to the nearest
      power of 2.
    - The allocator then finds a free block of that size to satisfy the request.

- **Coalescing:**
    - When a block is freed, the allocator checks if its "buddy" (the adjacent block created
      from the same split) is also free.
    - If both buddies are free, they are merged into a larger block of the next power-of-2 size.
    - Finding and merging buddies is very fast because the buddy's address can be computed
      using simple bitwise operations.

- **Fragmentation:**
    - Buddy systems suffer from high internal fragmentation because every allocation is rounded
      up to a power of 2.
    - If you request 33 bytes, you get a 64-byte block, wasting the extra space.
    - This strict sizing can lead to inefficient use of memory, especially for many small or
      oddly-sized allocations.



## Garbage Collection

Garbage collection is a dynamic memory management technique that automatically frees blocks
no longer used by a program.

- **Mechanism Comparison:**
  Applications using garbage collection allocate memory (e.g., with `malloc`) but do not
  explicitly free it (do not call `free`). The garbage collector **periodically** finds unused
  blocks ("garbage") and returns them to the free list.

- **Conceptual Model:**
  Garbage collectors view memory as a directed reachability graph.

  > [!nt]
  >
  > 메모리를 "방향성 있는 도달 가능성 그래프(directed reachability graph)"로 본다는 것은, 가비지
  > 컬렉터가 프로그램의 메모리 구조를 그래프로 모델링한다는 뜻입니다. 여기서 각 노드는 객체(메모리
  > 블록)를 나타내고, 엣지(화살표)는 한 객체가 다른 객체를 참조하고 있음을 의미합니다. 
  > 
  > 즉, 프로그램에서 어떤 객체가 다른 객체를 가리키면, 그래프에서 한 노드에서 다른 노드로 향하는 방향성
  > 있는 엣지가 생깁니다. 가비지 컬렉터는 이 그래프를 따라가면서 "도달 가능한(reachable)" 객체와 "도달
  > 불가능(unreachable)" 객체를 구분합니다. 도달 불가능한 객체는 더 이상 프로그램에서 사용되지 않으므로,
  > 메모리에서 해제(수거)할 수 있습니다.
  > 
  > 간단히 말해, 가비지 컬렉터는 메모리 내 객체들 사이의 참조 관계를 그래프로 보고, 이 그래프에서
  > 루트(예: 전역 변수, 스택 변수)로부터 도달할 수 없는 객체를 찾아내어 메모리를 정리합니다.




### 9.10.1 Basics of Garbage Collection

Garbage collection uses a graph-based model to determine which memory blocks are still in use.

- **Reachability Graph Components:**
    - **Root Nodes:** Pointers outside the heap, such as registers, stack variables, or global
      variables, that reference heap memory.
    - **Heap Nodes:** Allocated blocks within the heap.
    - **Edges:** A directed edge $p \rightarrow q$ means a location in block $p$ points to a
      location in block $q$.

- **Garbage Identification:**
    A node $p$ is reachable if there is a directed path from any root node to $p$. Unreachable
    nodes are considered garbage.

- **Collector Types:**
    - **Exact Collectors (e.g., ML, Java):**
        These languages tightly control pointer usage, allowing the collector to precisely
        identify all garbage.
    - **Conservative Collectors (e.g., C/C++):**
        These languages cannot track pointers exactly. Conservative collectors correctly
        identify all reachable blocks but may mistakenly keep some garbage.

- **Operational Integration:**
    If `malloc` cannot find a free block, it can invoke the garbage collector. The collector
    frees garbage blocks, then `malloc` retries the allocation.

### 9.10.2 Mark & Sweep Garbage Collection

Mark & Sweep is a classic garbage collection algorithm with two main phases.

- **Algorithm Steps:**
    - **Mark Phase:**
        The collector recursively marks all reachable blocks starting from the root nodes.
        Helper functions check if a pointer references an allocated block, whether it is
        already marked, and set its mark status.
    - **Sweep Phase:**
        The collector scans all heap blocks and frees any that remain unmarked (i.e., garbage).

### 9.10.3 Conservative Mark & Sweep in C

Implementing Mark & Sweep in C requires a conservative approach due to language limitations.

- **Challenges in C:**
    - **No Type Information:** C does not tag memory locations, making it hard to distinguish
      pointers from integers or scalars.
    - **Locating Blocks:** When a value is identified as a pointer, the collector must check
      if it points inside an allocated block's payload.

- **Addressing Block Location:**
    To quickly determine if a pointer points inside an allocated block, the set of allocated
    blocks is stored in a balanced binary tree, requiring extra pointer fields (`left`, `right`)
    in block headers.

- **Necessity of Conservatism:**
    Since C lacks type tags, scalar values that match valid addresses must be treated as pointers.
    The collector conservatively marks these blocks as reachable, ensuring correctness but
    possibly failing to free all garbage.



> 번역
>
> - **C 언어의 한계:**  C는 변수나 메모리 블록에 타입 정보를 저장하지 않습니다. 즉, 어떤 값이
>   포인터(주소)인지, 그냥 정수나 다른 데이터인지 구분할 수 없습니다.
> 
> - **블록 위치 찾기:**  만약 어떤 값이 포인터처럼 보인다면, 그 값이 실제로 할당된 메모리 블록
>   안을 가리키는지 확인해야 합니다. 이를 빠르게 확인하기 위해, 할당된 모든 블록 정보를 균형 이진
>   트리(예: AVL 트리)에 저장합니다. 그래서 각 블록의 헤더에 트리 구조를 위한 포인터(`left`,
>   `right`)가 추가로 필요합니다.
> 
> - **보수적(Conservative) 접근:**  타입 정보를 알 수 없으므로, 어떤 값이 실제로는 포인터가
>   아니더라도, 그 값이 유효한 메모리 블록의 주소와 일치하면 "포인터일 수 있다"고 간주해야 합니다.
>   그래서 가비지 컬렉터는 이런 값이 가리키는 블록도 "사용 중"으로 표시(mark)합니다.  이 방식은
>   안전하지만, 실제로는 더 이상 사용하지 않는(가비지인) 메모리도 해제하지 못할 수 있습니다.
> 
> **요약:**  C에서는 타입 정보가 없기 때문에, 가비지 컬렉터가 모든 가능성을 고려해 "보수적으로"
> 동작해야 하며, 이로 인해 완벽하게 모든 가비지를 수거하지 못할 수 있습니다.




> [!nt]
>
> Conservative garbage collection in C may leave some garbage uncollected, but it guarantees
> that no necessary memory is freed prematurely.




## Common Memory-Related Bugs in C Programs


Here are common memory-related bugs in C programming:

- **Dereferencing Bad Pointers**
    - **Concept:** Accessing memory addresses that do not contain valid data or are read-only.
    - **Result:** Causes segmentation faults or protection faults, usually terminating the program.
    - **Example:** Passing a variable's value instead of its address to `scanf` (e.g.,
      `scanf("%d", val)` instead of `scanf("%d", &val)`). If the value happens to be a valid
      address, it may overwrite memory, leading to serious bugs.

- **Reading Uninitialized Memory**
    - **Concept:** Assuming memory from `malloc` is zeroed. Only uninitialized global variables
      (`bss` segment) are zeroed by the loader.
    - **Example:** Using a block from `malloc` (like a vector in a matrix-vector multiplication)
      without initializing it leads to undefined results. Use `calloc` or manually set memory to
      zero.

- **Allowing Stack Buffer Overflows**
    - **Concept:** Writing to a stack buffer without checking input size, which can overwrite
      adjacent memory.
    - **Example:** Using `gets` to read input into a stack buffer allows overflow. Use `fgets`
      instead to limit input size.

- **Assuming Pointers and Pointed Objects Are the Same Size**
    - **Concept:** Mistakenly thinking a pointer (e.g., `int *`) is the same size as the object it
      points to (`int`). On many systems, pointers are larger.
    - **Result:** Allocating an array of pointers using `sizeof(int)` instead of `sizeof(int *)`
      can cause out-of-bounds writes and memory corruption.

- **Making Off-by-One Errors**
    - **Concept:** Incorrect loop bounds can cause memory overwrites.
    - **Example:** Allocating an array of `n` elements but initializing `n + 1` elements, which
      writes past the array's end.

- **Referencing a Pointer Instead of Its Value**
    - **Concept:** Misunderstanding C operator precedence, leading to modifying the pointer instead
      of the value.
    - **Example:** Writing `*size--` decrements the pointer, not the value. Use parentheses:
      `(*size)--`.

- **Misunderstanding Pointer Arithmetic**
    - **Concept:** Pointer arithmetic is scaled by the size of the data type, not by bytes.
    - **Example:** Doing `p += sizeof(int)` for an `int *p` advances the pointer by
      `sizeof(int) * sizeof(int)` bytes. Use `p++` to move to the next integer.

- **Referencing Nonexistent Variables**
    - **Concept:** Accessing a local variable after its function has returned.
    - **Example:** Returning the address of a local variable (`return &val;`) is unsafe, as the
      memory may be reused.

- **Referencing Data in Freed Heap Blocks**
    - **Concept:** Using a pointer to a heap block after it has been freed.
    - **Result:** If the memory is reallocated, using the old pointer can corrupt new data.

- **Introducing Memory Leaks**
    - **Concept:** Failing to free allocated memory, creating unreachable "garbage" in the heap.
    - **Result:** Over time, the heap fills with unused memory, which is especially problematic for
      long-running programs.




## Understanding of the simple allocator


### 


> [!re] Question
> in GET and PUT macro, there are castings. I see that this is allocater which means it handles
> void type pointer.  But, I don't understand why it casting to unsigned int?


- **Allocator Context (Low-Level Memory Manipulation)**

    - Dynamic memory allocators work with raw memory addresses, usually using `char *` or `void *`
      pointers. This allows them to manage the heap as a sequence of bytes.

    - Important metadata for each block, such as its size and allocation status, is stored in
      headers and footers. Each header or footer is exactly one word (4 bytes), defined as `WSIZE`.

- **Role of `GET` and `PUT`**

    - The `GET` macro reads a word (4 bytes) from a specified address `p`.

    - The `PUT` macro writes a value (`val`) as a word (4 bytes) to a specified address `p`.

- **Necessity of Casting to `(unsigned int *)`**

    1. **Handling Untyped Pointers:**
       The macros often receive a `(void *)` pointer, which cannot be directly dereferenced in C.
       Casting is needed to allow access to the memory location.

    2. **Defining Data Size:**
       Casting to `(unsigned int *)` tells the compiler to treat the memory as a pointer to a
       4-byte integer. This matches the word size (`WSIZE`) used for allocator metadata.

    3. **Preventing Sign Extension:**
       Using `unsigned int` avoids problems with sign extension that could occur if a signed type
       were used. This is important for manipulating raw data like size fields and status bits.

> [!nt]
>
> Casting to `(unsigned int *)` ensures that the allocator performs correct 4-byte read and write
> operations on generic memory pointers, maintaining consistency and preventing errors.



### 

> [!re] Question
> Where is the block pointer(`bp`) pointing?


This distinction is important because the block's actual start is where its metadata is stored:

- **Block Start vs. Pointer Location**

    - Each heap block begins with metadata: a one-word **header**. The header's size is
      defined by `WSIZE` (4 bytes).

    - To find the header's address, subtract one word size from the block pointer:
      `HDRP(bp)` calculates the header address as `bp - WSIZE`.

    - As a result, the block pointer (`bp`) is always **4 bytes (one word) after the block's
      true start**.

- **Alignment and the Block Pointer**

    - The `malloc` function must return an address aligned for any data type. In a simple
      allocator, this means **double-word alignment** (a multiple of 8 bytes, or `DSIZE`)
      for the returned address.

    - The **alignment requirement applies to the pointer given to the application** (the
      payload start, or `bp`), not the header's start. Internally, the allocator ensures
      that if the header is at address $A$, then the payload at $A+4$ is a multiple of 8.



> [!nt] Size of block
>
> It is including 'header' & 'footer'



### 


> [!re] Question
> Why is the `HDRP` or `NEXT_BLKP` macro casting with `(char*)`?


Casting to `char *` is essential for precise pointer arithmetic in heap management:

- **Byte-Level Pointer Arithmetic**
    - The `char` type in C always occupies one byte.
    - Arithmetic on a `(char *)` pointer (e.g., `p + N`) moves the pointer exactly N bytes.
    - This is crucial for allocators, which must jump specific byte offsets to access metadata
      (such as headers and footers) or traverse between blocks.

- **Accurate Metadata Offset Calculation**
    - Allocators use constants like `WSIZE` (4 bytes) and `DSIZE` (8 bytes) for metadata sizes.
    - The block pointer (`bp`) points to the payload. To locate the header, the allocator subtracts
      `WSIZE` bytes.
    - *Example:* In the `HDRP` macro, `((char *)(bp) - WSIZE)`, casting to `(char *)` ensures
      subtraction moves back exactly 4 bytes, regardless of the payload's data type.

- **Why Not Other Pointer Types?**
    - Pointer arithmetic on types like `int *` or `double *` moves by the size of those types
      (typically 4 or 8 bytes).
    - This scaling would cause incorrect calculations when moving by specific byte counts,
      breaking boundary tag logic or block traversal.


> [!nt]
>
> Using `char *` for pointer arithmetic guarantees movement by exact bytes, which is necessary for
> reliable heap manipulation and metadata access.





### 


> [!re] Question
> In figure 9.45, why are the allocated size should size of even number of words?


The reason the allocated size must be an even number of words in the `extend_heap` function
(Figure 9.45) is to maintain the **double-word alignment requirement** of the heap.


- **Alignment Constraint**

    - All blocks, including those created by `extend_heap`, must follow double-word alignment.
    - This requires each block's size to be a multiple of 8 bytes (`DSIZE`).


- **Word Size Relationship**

    - Constants used:
        - Word size (`WSIZE`): 4 bytes.
        - Double word size (`DSIZE`): 8 bytes.

    - Since 8 bytes / 4 bytes per word = 2, block sizes must be multiples of two words.


- **Code Implementation**

    - The code ensures the block size (`size`) is a multiple of $2 \times WSIZE$ (i.e., $DSIZE$).
    - It checks if the requested number of `words` is even.
    - If `words` is odd, it adds one before multiplying by `WSIZE`, making the final size a multiple of 8 bytes.




### 



> [!re] Question
>
> In figure 9.46, coalesce function has void * type of argv and return. Is that because the
> datatype at the block pointer is figured on runtime?



- **Generic Pointer Requirement**

    - The dynamic memory allocator manages the heap for the application. It must allocate memory
      that is properly aligned for any data type.

    - In C, the `void *` type is used for generic pointers. This allows the allocator to return
      an address (`bp`) to the application without knowing the specific data type that will be
      stored in that memory block.

- **Consistency with External Interface**

    - The `mm_malloc` function returns a `void *`, and `mm_free` accepts a `void *`.

    - The internal helper function `coalesce` also receives and returns the generic block pointer
      (`bp`) to the payload area of a block being freed or merged. This maintains consistency with
      the untyped interface.

- **Pointer Arithmetic Implementation**

    - Although function signatures use `void *`, the allocator performs explicit casting internally
      (for example, in macros like `HDRP` or `NEXT_BLKP`) to enable low-level, byte-accurate pointer
      arithmetic. Typically, pointers are cast to `char *` or `unsigned int *`.

    - This programming style, which relies on casting and pointer arithmetic to manipulate memory
      directly, is typical of low-level systems programming such as dynamic memory allocation.




### 


> [!qt] In figure 9.46, in case 2, it calls PUT for both Header and Footer. But for footer block, shouldn't it be something like this: PUT(FTRP(NEXT_BLKP(bp)), PACK(size, 0));?
>   󱞪 


In Figure 9.46, the line for Case 2 is:

```c
23 PUT(FTRP(bp), PACK(size,0));
```

This line is correct due to the order of execution and the definition of the `FTRP` macro.

- **Coalescing Goal (Case 2):**
    The current block (`bp`) is merged with the next block (`NEXT_BLKP(bp)`).
    The new free block starts at `bp` and ends at the footer of the original next block.

- **Sequential Update Dependency:**
    - The merged size (`size`) is calculated first (Line 21).
    - The header at `HDRP(bp)` is updated with this new size (Line 22).

- **How `FTRP` Works:**
    The `FTRP` macro finds the footer address using the block pointer (`bp`) and the size in the block's header.

    - `#define FTRP(bp) ((char *)(bp) + GET_SIZE(HDRP(bp)) - DSIZE)`
    - After updating `HDRP(bp)`, `FTRP(bp)` uses the new size to locate the footer of the merged block.

- **Result:**
    `FTRP(bp)` now points to the last word of the merged block, which matches the location of the original next block's footer.


> [!nt]
>
> Using `PUT(FTRP(NEXT_BLKP(bp)), PACK(size, 0));` would only work if `FTRP` or `NEXT_BLKP(bp)` were defined
> differently. By updating the header first and then using `FTRP(bp)`, the allocator consistently locates the
> correct footer for the merged block.
