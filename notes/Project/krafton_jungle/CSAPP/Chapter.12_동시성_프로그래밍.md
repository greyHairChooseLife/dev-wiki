# Concurrency Programming (Chapter 12)


[notebookLM](https://notebooklm.google.com/notebook/f405095d-572f-4992-bf85-e8781645f33a)




응용 프로그램 수준에서의 동시성(Concurrency) 구현 방식과, 공유 자원에 대한 접근을 안전하게 관리하는 방법


  - 12.1 Concurrent Programming with Processes

  - 12.2 Concurrent Programming with I/O Multiplexing

  - 12.3 Concurrent Programming with Threads

  - 12.4 Shared Variables in Threaded Programs

  - 12.5 Synchronizing Threads with Semaphores

  - 12.6 Using Threads for Parallelism

  - 12.7 Other Concurrency Issues

  - 12.8 Summary


---




Concurrency refers to multiple logical flows running or overlapping in time. It is essential in
applications for tasks such as overlapping work with slow I/O, interacting with users, reducing
latency, serving multiple network clients, and exploiting parallelism on multi-core processors.

- **Application-Level Concurrency Uses:**
    
    - Overlap work with slow I/O devices.
    - Allow simultaneous user interactions (e.g., printing while resizing a window).
    - Reduce latency by deferring low-priority work.
    - Serve multiple network clients, dedicating a logical flow to each client.
    - Achieve faster execution by exploiting multi-core parallelism.


- Operating systems provide three main approaches for building concurrent programs:

    - Processes
    - I/O multiplexing
    - Threads

---

### II. Approaches to Concurrent Programming

- **A. Processes**

    - Logical flows are scheduled and managed by the kernel.
    - Each process has a separate virtual address space, preventing accidental memory overwrites.
    - Communication requires explicit interprocess communication (IPC) mechanisms (e.g., pipes, sockets).
    - **Pros:** Strong isolation between flows.
    - **Cons:** High overhead for process control and IPC.
    - **Example:** A server uses `fork` to create a child process for each client.

- **B. I/O Multiplexing**

    - Applications schedule their own logical flows within a single process.
    - Flows are modeled as state machines.
    - The `select` function suspends the process until I/O events occur.
    - Flows share the same address space.
    - **Pros:** Easy data sharing, easier debugging, high efficiency.
    - **Cons:** Complex coding, cannot fully utilize multi-core processors.

- **C. Threads**

    - Threads are logical flows within a single process, scheduled by the kernel.
    - Threads share the address space but maintain separate contexts (TID, stack, registers).
    - Context switching is faster than with processes.
    - **Pthreads:** Standard C interface for threads.
        - Created with `pthread_create`.
        - Can be joinable (explicitly reaped) or detached (reaped automatically).

---

### III. Shared Variables and Synchronization

- **A. Shared Variables**

    - A variable is shared if multiple threads reference it.
    - Threads share heap, global, static variables, code, and open files.
    - Registers are never shared. Thread stacks are part of shared memory but usually accessed independently.
    - **Memory Mapping:**
        - Global/static variables: shared read/write area.
        - Local automatic variables: private stack per thread.

- **B. Synchronization with Semaphores**

    - Synchronization prevents errors from unpredictable instruction interleaving (races).
    - A *critical section* manipulates shared data and requires mutual exclusion.
    - **Progress Graphs:** Model thread execution and define unsafe regions.
    - **Semaphores:** Global variables manipulated by atomic P (`sem_wait`) and V (`sem_post`) operations.
        - **Mutual Exclusion:** Binary semaphores (mutexes) protect critical sections.
        - **Scheduling:** Counting semaphores schedule access to shared resources (e.g., producer-consumer model).

---

### IV. Parallelism and Concurrency Challenges

- **A. Parallelism on Multi-core Systems**

    - Concurrent programs on multi-core processors are called parallel programs.
    - To maximize performance, minimize synchronization overhead.
    - Algorithms using private local variables before merging (e.g., `psum-local`) are faster than those using constant mutex locking (e.g., `psum-mutex`).

- **B. Thread Safety and Races**

    - **Thread Safety:** A function is thread-safe if it always produces correct results when called concurrently.
    - **Thread-Unsafe Classes:**
        - Class 1: Does not protect shared variables.
        - Class 2: Keeps state across invocations (e.g., `rand`).
        - Class 3: Returns pointer to static variable (fixed by lock-and-copy).
        - Class 4: Calls thread-unsafe functions.
    - **Reentrancy:** Functions that do not reference shared data; usually more efficient.
    - **Races:** Occur when program correctness depends on unpredictable execution order, leading to non-repeatable errors.

- **C. Deadlocks**

    - Deadlocks occur when threads are blocked indefinitely, each waiting for a condition another blocked thread must satisfy.
    - Induced by overlapping forbidden regions in a progress graph.
    - **Mutex Lock Ordering Rule:** Acquire all mutexes in a fixed order, release in reverse order to prevent deadlocks.

---

### V. Error Handling Conventions

- Use wrapper functions (e.g., in `csapp.c`) to check error codes from system-level functions.
- System-level functions report errors in three styles:

    - Unix-Style: Returns -1 and sets `errno` (e.g., `wait`).
    - Posix-Style: Returns 0 on success or nonzero error code, results via reference arguments (e.g., `pthread_create`).
    - GAI-Style: Returns 0 on success or nonzero error value (e.g., `getaddrinfo`).
