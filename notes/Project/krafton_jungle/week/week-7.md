# week-7: Malloc Lab





> [!NOTE]
>
> 팀원: 배재완 + 박지호



> [!rf]
> 


- 코어타임

  - 코어타임에 바라는것
    - 팀원분들
      - CSAPP에 개념/원리를 혼자 이해하면 구멍이 많고 어려웠다. 그걸 채우고싶다.
      - malloc lab같은 경우 다른 사람은 어떻게 하는지 관점같은 것을 보고싶다.
      - 잘 모르겠다.

    - 나는
      - CSAPP에 명확하지 않은 부분 같이 이해해 보는것이 유익하고 재밌다.
      - malloc lab도 마찬가지. 다른사람은 어떤식으로 만들어 나가는건지 그 흐름이나 방식이 궁금하다.
      - 다른 사람의 코드를 읽고 이해해보는 경험, 나의 코드를 다른사람이 이해하기 수월하게 해 보는
        경험을 해보고싶다.


  - 일단 책 끝내고 malloc lab에 집중하기

  - CSAPP
    - 토: Chapter 9.9
    - 월: Chapter 9.10~12

  - Malloc Lab
    - [x] 해보면서 월요일에 다시 이야기





## 학습 계획

---





## 학습 내용

### QnA

2025-10-24

#### 

**build with -m32 okay in 64 bit?**

- yes

  - File Path: Makefile, 9:9
    ```make
    CFLAGS = -Wall -O2 -m32
    ```



#### 

**what is `traces/` directory?**

A trace (in this context) is a file (typically with a `.rep` extension) containing a sequence of
memory allocation and deallocation operations (e.g., `malloc` and `free` calls with specific
sizes and addresses). These are used by the `mdriver` program to simulate real-world memory usage
patterns, allowing you to test and benchmark your custom memory allocator implementation in `mm.c`
for correctness, efficiency, and fragmentation.

For example, a trace might include lines like:
```
a 100  // allocate 100 bytes
f 0x12345678  // free at address 0x12345678
```


- The `.pl` files (Perl scripts like `gen_binary.pl`) generate synthetic trace files (`.rep`
  files) with predefined patterns of memory operations.

- The `.rep` files are then used by the `mdriver` executable for automated testing and
  benchmarking of your memory allocator, simulating various allocation scenarios to check for issues
  like leaks, fragmentation, or performance.

- _I can use all kinds of trace files while running mdriver_


#### 

**make failed...**

- not my fault.
```ubuntu 22.04
root@sy-x390:/workspace# make
gcc -Wall -O2 -m32   -c -o mdriver.o mdriver.c
In file included from /usr/include/features.h:392,
                 from /usr/include/assert.h:35,
                 from mdriver.c:10:
/usr/include/features-time64.h:20:10: fatal error: bits/wordsize.h: No such file or directory
   20 | #include <bits/wordsize.h>
      |          ^~~~~~~~~~~~~~~~~
compilation terminated.
make: *** [<builtin>: mdriver.o] Error 1
```

- nothing named "bits"
```ubuntu 22.04
root@sy-x390:/workspace# ls /usr/include/ | grep bits
root@sy-x390:/workspace#
```

  > [!NOTE] "usr" in the path(`/usr`) stands for "Unix System Resource"


**solution**

install libc6-dev-i386

```sh
apt-get install libc6-dev-i386
```

or update Dockerfile to install that.

> [!rf]
> 
> https://bugs.launchpad.net/ubuntu/+source/glibc/+bug/2067082



#### 

**let's test step by step**

  1. **short1-bal.rep and short2-bal.rep**: Start here for basic alloc/free correctness and quick debugging.
  2. **binary-bal.rep and binary2-bal.rep**: Test alternating small/large allocations to ensure your allocator handles varying sizes without issues like external fragmentation.
  3. **coalescing-bal.rep**: Verify coalescing (merging freed adjacent blocks) works properly.
  4. **random-bal.rep and random2-bal.rep**: Check robustness with random alloc/free patterns.
  5. **realloc-bal.rep and realloc2-bal.rep**: Test realloc efficiency and internal fragmentation minimization.
  6. **Real program traces (amptjp-bal.rep, cccp-bal.rep, etc.)**: Run these last for end-to-end validation against actual usage.


### Step 1: Dynamic Memory Allocation Fundamentals

Dynamic Memory Allocation (DMA) is the fundamental mechanism allowing C programs to manage memory during runtime, primarily operating on a specific region of virtual memory.


-   **Purpose of DMA**
    -   Allows C programmers to acquire and manage additional virtual memory while the program is running.
    -   It addresses situations where data structure sizes are unknown until runtime.

-   **The Heap (Memory Location)**
    -   The dynamic memory allocator manages an area of virtual memory known as the **heap**.
    -   The heap typically begins after the uninitialized data area and expands upward (toward higher addresses).
    -   The kernel tracks the top of the heap using the `brk` pointer.

-   **Memory Organization: Blocks**
    -   The heap consists of contiguous chunks of virtual memory called blocks.
    -   Blocks exist in two states:
        -   Allocated: Reserved explicitly for the application's use.
        -   Free: Available to be allocated by the allocator.

-   **Explicit Allocators (Functions)**
    -   The C standard library uses an **explicit allocator** (like the `malloc` package), meaning the application must explicitly free memory.
    -   `malloc(size)`: Returns a pointer to a block of memory of at least `size` bytes, suitably aligned for any data type.
    -   `free(ptr)`: Explicitly frees a previously allocated heap block, making it available for future allocation requests.
    -   *Note on Alignment:* The address returned by `malloc` must be aligned (e.g., a multiple of 8 in 32-bit mode or 16 in 64-bit mode).




### Step 2: Allocator Requirements, Goals, and Fragmentation

The next step in understanding Dynamic Memory Allocation involves
  examining the strict requirements placed on memory allocators
  and the concept of fragmentation, which hinders performance.



-   **Allocator Requirements and Constraints**
    -   **Request Handling**
        -   Must handle arbitrary sequences of allocate and free requests, _without assumptions about ordering or nesting._
        -   Must respond immediately; cannot reorder or buffer requests to improve performance.
    -   **Memory Usage**
        -   _Must store nonscalar data structures used by the allocator within the heap itself to ensure scalability._
        -   Must align blocks correctly so they can hold any data type (alignment requirement).
        -   Cannot modify or move blocks once they are allocated.

-   **Allocator Performance Goals**
    -   **Maximizing Throughput**
        -   Goal: Maximize the number of allocate and free requests completed per unit time.
        -   Method: Minimize the average time required to satisfy requests.
    -   **Maximizing Memory Utilization**
        -   Goal: Maximize peak utilization ($U_{n-1}$) over the entire sequence of requests.
        -   Peak Utilization Definition: The maximum ratio of the aggregate payload ($P_k$, the sum of allocated block payloads) to the current heap size ($H_k$) across all requests.

-   **Fragmentation (Cause of Poor Utilization)**
    -   Fragmentation occurs when unused memory cannot satisfy allocation requests.
    -   **Internal Fragmentation**
        -   Description: The allocated block size is larger than the requested payload.
        -   Causes: Minimum block size constraints or padding added to meet alignment requirements.
        -   Quantification: Relatively straightforward; the sum of the differences between allocated block sizes and their actual payloads.
    -   **External Fragmentation**
        -   Description: Enough aggregate free memory exists to satisfy a request, but no single free block is large enough.
        -   Quantification: Difficult because it depends not only on past requests but also on the pattern of future requests.
        -   Heuristics: Aim to combat external fragmentation by maintaining large free blocks instead of many small ones:
            - **Coalescing**: Immediately merge adjacent free blocks when freeing memory to create larger contiguous spaces.
            - **Placement Policies**: 
              - **First-fit**: Allocate the first suitable free block, minimizing search time.
              - **Best-fit**: Choose the smallest suitable free block to reduce leftover fragments.
            - **Deferred Coalescing**: Delay merging free blocks until the next allocation (not on free) to avoid overhead from frequent checks, trading some fragmentation for performance.




### Step 3: Implementation Issues: Organization and Placement

Step 3 covers the core implementation decisions an explicit dynamic memory allocator must make,
focusing on
how free blocks are structured and chosen.


-   **Allocator Implementation Issues (9.9.5)**
    -   A practical allocator must resolve four key design challenges when managing the heap:
        -   **Free Block Organization:** Determining how to keep track of available (free) blocks.
        -   **Placement:** Choosing a suitable free block to place a newly requested block.
        -   **Splitting:** Managing the remaining space if the allocated block is smaller than the free block chosen.
        -   **Coalescing:** Handling a newly freed block by merging it with adjacent free blocks to prevent fragmentation.

-   **Free List Organization: Implicit Free Lists (9.9.6)**
    -   This is the simplest way to organize the heap, where allocation and free blocks are stored contiguously.
    -   **Block Format:** Each block contains three parts: a header, the payload, and optional padding.
        -   Header: A one-word header encodes the block size and an allocated bit (indicating if the block is reserved or free).
        -   Encoding: Assuming double-word alignment (8 bytes), the block size is always a multiple of 8, _allowing the low-order bits of the header word to store the allocated/free status._
            > In implicit free lists, the header is a single word (e.g., 4 or 8 bytes) that stores the block size. Since sizes are multiples of 8 (for alignment), the low-order bits (least significant bits) are always 0.
            > 
            > - **Trick**: Use one low-order bit (e.g., the least significant bit, bit 0) to store the allocated/free status:
            >   - If bit 0 is 0: Block is free.
            >   - If bit 0 is 1: Block is allocated.
            > - **Example**: For a 16-byte block:
            >   - Free: Header = 16 (binary: ...00010000, bit 0 = 0).
            >   - Allocated: Header = 17 (binary: ...00010001, bit 0 = 1).
            > - This saves space by not needing a separate field for the status.
            > 
            > The actual size is the header value with the status bit masked out (e.g., size = header & ~1).
            > 
            > This is efficient for simple allocators but requires careful bit manipulation.
    -   **Structure:** Free blocks are linked implicitly via the size fields contained within the headers of all blocks (both allocated and free).
          > In implicit free lists, free blocks aren't connected via
          > explicit pointers (like a linked list). Instead, the "links" are
          > implicit: you traverse the heap by using the size fields in every
          > block's header (allocated or free) to jump to the next block.
          > 
          > - **How it works**:
          >   - Start at the heap's beginning.
          >   - Read the header to get the block size and check if it's free (via the allocated bit).
          >   - Add the size to the current address to reach the next block's header.
          >   - Repeat until finding a suitable free block or reaching the heap end.
          > 
          > - **Example traversal** (simplified, assuming 8-byte aligned blocks):
          >   - Block 1: Header = 16 (free), payload = 8 bytes → Next block starts at current + 16.
          >   - Block 2: Header = 24 (allocated) → Next at current + 24.
          >   - This avoids storing pointers, saving space, but makes searches linear (O(n) time).
          > 
          > This is simple but slow for large heaps, as you scan all blocks.

    -   **Minimum Block Size:** The system imposes a minimum block size (e.g., 16 bytes for double-word alignment) to accommodate the header and satisfy alignment requirements.
    -   **Drawback:** Operations requiring a search of the free list (like finding a block to allocate) are linear in the total number of blocks in the heap.


-   **Placement Policies (9.9.7)**
    -   When an allocator searches the free list for a block large enough to satisfy an allocation request, the policy determines which block is chosen.
    -   **First Fit:**
        -   Chooses the first free block encountered that is large enough.
        -   Tends to keep large free blocks located toward the end of the list.
          **Key Issue**: Excessive splitting creates many small free blocks (external fragmentation), wasting space. Allocators split blocks to allocate smaller sizes, but policies like Best Fit reduce harmful splits. Coalescing merges adjacent free blocks to counteract this.
    -   **Next Fit:**
        -   Similar to First Fit, but starts its search where the previous search ended, rather than restarting from the beginning.
        -   Can run faster than First Fit, but may result in worse memory utilization.
    -   **Best Fit:**
        -   Examines every free block and selects the smallest free block that is still large enough to satisfy the request.
        -   Generally achieves better memory utilization than First Fit or Next Fit, but requires an exhaustive search of the list.

-   **Splitting Free Blocks (9.9.8)**
    -   Once a free block is chosen, the allocator determines how much memory to use.
    -   If the remainder of the free block is large enough (e.g., greater than the minimum block size), the allocator splits the block into two parts:
        -   The first part becomes the newly allocated block.
        -   The remainder becomes a new, smaller free block.
    -   Using the entire free block, regardless of fit, is simpler and faster but increases internal fragmentation.





### Step 4: Coalescing and Advanced Free List Structures


How allocators merge freed memory
and utilize more sophisticated structures
to speed up operations.


-   **Coalescing Free Blocks (9.9.10)**
    -   **Definition:** The process of merging adjacent free blocks into a single larger block when a block is freed.
    -   **Purpose:** To combat **false fragmentation**, which occurs when available memory is chopped into small, unusable free blocks.
    -   **Policy Decisions**
        -   Immediate Coalescing: Merging adjacent blocks every time a block is freed.
        -   Deferred Coalescing: Waiting to merge blocks until a later time, such as when an allocation request fails.

-   **Boundary Tags (9.9.11)**
    -   **Mechanism for Constant-Time Coalescing**
        -   Boundary tags are footers added to each block that replicate the block's header (size and status).
        -   They allow the allocator to determine the starting location and status of the *previous* block in memory by inspecting its footer, which is one word away from the current block's start.
        -   This enables constant-time merging for both the next and previous adjacent free blocks.
    -   **Overhead:** Requiring both a header and a footer can introduce significant memory overhead for applications manipulating many small blocks.

-   **Explicit Free Lists (9.9.13)**
    -   **Organization**
        -   Free blocks contain explicit `pred` (predecessor) and `succ` (successor) pointers.
        -   These pointers form a doubly linked list entirely within the heap's free blocks.
    -   **Advantage over Implicit Lists**
        -   Allocation search time is reduced to being linear in the **number of free blocks**, rather than the total number of blocks in the heap.
    -   **Disadvantage**
        -   The minimum block size increases because space is needed for the header, footer, and the predecessor/successor pointers, increasing potential internal fragmentation.

-   **Segregated Free Lists (9.9.14)**
    -   **Concept:** Allocators maintain multiple free lists, partitioning all possible block sizes into equivalence classes called **size classes**.
    -   **Benefit:** Greatly reduces search time during allocation requests by limiting the search only to the appropriate size class list.
    -   **Segregated Fits**
        -   This variant is common in production allocators (e.g., GNU `malloc`).
        -   It achieves both fast search times and high memory utilization because a simple first-fit search within a segregated list approximates a best-fit search of the entire heap.






### Step 5: Implementing a Simple Allocator (Implicit Free List)

The complexity of allocators requires careful programming, often relying on error-prone pointer arithmetic.
The implementation focuses on maximizing throughput and utilization while adhering to constraints.


-   **Memory System Model**
    -   The allocator often operates on a model provided by a `memlib.c` package.
    -   `mem_init` initializes the heap memory.
    -   `mem_sbrk` is used by the allocator to request additional heap memory from the virtual memory system.

-   **Heap Structure Invariant (Implicit Free List)**
    -   The allocator uses an **implicit free list** with **boundary tags** for constant-time coalescing.
    -   The heap is initialized into an invariant form consisting of four parts:
        -   Padding: An unused word for alignment.
        -   Prologue Block: A special 8-byte allocated block (header and footer) that is never freed, eliminating edge conditions at the start of the heap.
        -   Regular Blocks: Zero or more allocated or free blocks.
        -   Epilogue Block: A zero-size allocated block at the end, also eliminating edge conditions during coalescing.
    -   A static pointer, `heap_listp`, typically points to the prologue block.

-   **Essential Constants and Macros**
    -   Macros are critical for abstracting casting and pointer arithmetic.
    -   Constants: `WSIZE` (word size, 4 bytes) and `DSIZE` (double word size, 8 bytes) are defined.
    -   Encoding: The block header/footer uses the lowest bit to store the allocated/free status, since block sizes are typically multiples of 8 (double-word alignment).
    -   Key Macros:
        -   `PACK(size, alloc)`: Combines size and status into a header word.
        -   `GET(p)` / `PUT(p, val)`: Read and write a word at address `p`.
        -   `HDRP(bp)` / `FTRP(bp)`: Compute pointers to the header and footer, given a block pointer `bp` (which points to the payload).
        -   `NEXT_BLKP(bp)` / `PREV_BLKP(bp)`: Traverse the heap sequentially using size information in the headers/footers.

-   **Initialization and Extension**
    -   `mm_init`: Creates the initial heap structure (padding, prologue, epilogue) and calls `extend_heap` to add the first large free block.
    -   `extend_heap`: Rounds up the requested size to maintain double-word alignment, calls `mem_sbrk` to acquire memory, initializes the header and footer of the new free block, sets the new epilogue header, and immediately calls `coalesce` in case the previous block was free.

-   **Block Allocation and Freeing**
    -   `mm_malloc`: Adjusts the requested size for alignment and overhead (minimum block size is 16 bytes). It searches the list using a placement policy (`find_fit`) and, if successful, uses a splitting policy (`place`). If no fit is found, it calls `extend_heap`.
    -   `mm_free`: Changes the allocated bit to free in the block's header and footer, then calls `coalesce` to merge adjacent free blocks using boundary tags.



### Tips for implementation

> [!qt] are their any points that I need to care about paging or physical memory while implementing different types of allocators?
>   󱞪 
> 
> 
> The implementing allocator generally operates strictly within the **virtual address space** (the heap) and relies on the operating system's Virtual Memory (VM) system to abstract away the details of physical memory management.
> 
> However, while you do not control the physical hardware directly, you must care about the rules and implications of the underlying VM system, particularly regarding performance and address validity.
> 
> -   **Physical Memory Contiguity is Abstracted**
>     -   VM simplifies memory management by providing each process with a large, uniform, and private address space.
>     -   When an allocator requests additional memory (e.g., via `sbrk`), the OS allocates contiguous **virtual** memory pages.
>     -   The OS then handles the complexity by mapping these virtual pages to physical pages that can be scattered randomly in physical memory. The OS does not need to locate contiguous physical memory pages.
>     -   This process works "silently and automatically, without any intervention from the application programmer".
> 
> -   **Paging and Performance Constraints**
>     -   VM treats main memory (DRAM) as a cache for the address space stored on disk. Pages are the transfer unit between disk and main memory.
>     -   Although physical addressing is hidden, an inefficient allocator design can drastically impact performance by causing excessive disk I/O (paging).
>     -   If the set of active pages (the "working set") referenced by the application exceeds the size of physical memory, the system experiences **thrashing**, where pages are continuously swapped in and out. Allocator design choices influence the locality of reference, which can trigger this performance collapse.
> 
> -   **Virtual Memory Safety and Rules**
>     -   The allocator must ensure all blocks are correctly **aligned** (e.g., double-word aligned), a fundamental requirement enforced by the VM system.
>     -   The allocator must prevent improper use of memory (e.g., accessing nonexistent pages or writing to read-only regions) that can result in segmentation or protection faults, which terminate the program. These rules are implemented using permission bits stored in the Page Table Entries (PTEs).
>     -   When an allocated page is referenced but is currently stored only on disk, the VM system triggers a **page fault exception**, transferring control to the kernel to load the page into physical memory. The allocator design affects which pages are kept active, influencing the likelihood of these faults.
>
>
>  
>  > When implementing your own allocator, while physical memory details are abstracted by VM, you must care about paging and performance implications to avoid inefficiencies:
>  > 
>  > - **Locality of Reference**: Design the allocator to promote spatial and temporal locality (e.g., allocate nearby blocks, reuse recent frees). Poor locality increases the working set, causing more page faults as the OS swaps pages in/out of DRAM, leading to thrashing and slowdowns.
>  > 
>  > - **Working Set Size**: Keep active pages small by minimizing scattered allocations. For example, use segregated lists to group similar sizes, reducing the number of pages touched during searches/allocations.
>  > 
>  > - **Paging Overhead**: Avoid designs that trigger excessive page faults (e.g., random access patterns). Implicit lists scan all blocks, potentially touching many pages; explicit lists limit this. Test with large heaps to see paging impact.
>  > 
>  > - **Alignment and Page Boundaries**: Ensure allocations respect page sizes (e.g., 4KB) to avoid partial page usage waste. Misalignment can cause faults if accessing unmapped areas.
>  > 
>  > - **Heap Extension Strategy**: Use `sbrk` for small extensions to keep the heap contiguous in virtual space, aiding VM mapping. For large allocs, `mmap` isolates them, but monitor for address space exhaustion on 32-bit systems.
>  > 
>  > - **Performance Monitoring**: Profile for page faults (e.g., via `perf` or `/proc/[pid]/stat`). An allocator causing high faults indicates poor physical memory usage.
>  > 
>  > In practice, focus on reducing fragmentation and improving locality—VM handles physical mapping, but your design affects paging costs.


## (팀)코어타임 기록


### 0차 2025-10-23 (목) 15:45

매번 그렇지만 이번엔 또 낯설고 특이한 경우다. 또 난이도가 높은것같다.

함께하는 분들, 그중에도 지호님이 코어타임이라던지 팀 활동에 무엇을 바라고 있으신지 알기가 어렵다.

내가 바라는 것은 나의 하드스킬 측면의 성장도 있지만, 다양한 사람들의 다양한 방식에 적응하여
주어진 조건 안에서 즐겁고 유익한 경험을 모두 함께하는것도 큰 부분을 차지한다. 어쩌면 제일
중요한지도 모르겠다. 지금 이 순간 나를 가까이에서 본다면 컴퓨터나 들여다보고 있지만, 한발자국만
멀리서 바라봐도 나는 그저 숨쉬고 있고, 그저 살아가고 있을 뿐이다.



### 1차



---
---
