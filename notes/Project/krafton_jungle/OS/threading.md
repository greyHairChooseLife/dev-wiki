# Threading


> [!rf]
> 
> [Unix Threads in C - CodeVault](https://www.youtube.com/playlist?list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2)



## what is thread? 

```c
#include <pthread.h>

void* job(void* arg) {
    // Job logic here
    return NULL;
}

int main() {
    pthread_t tid1, tid2;
    void* job_args = NULL;  // Define as needed
    void* job_return1, *job_return2;

    pthread_create(&tid1, NULL, job, job_args);
    pthread_create(&tid2, NULL, job, job_args);
    pthread_join(tid1, &job_return1);
    pthread_join(tid2, &job_return2);

    printf("hello")

    return 0;
}
```

- No hesitation for starting tid2 before or after tid1 finished. 
- Main thread waits until both threads' end before it prints "hello".


## thread vs process

- with/without **process id** 
- share/exclusive **virtual address space**


## race condition

- OS scheduling is NOT always fare enough for threads. 
- accessing followed by writing to the same variable can be mixed up between threads.


## mutex: protect shared data


```man
A mutex is a MUTual EXclusion device, and is useful for protecting shared data structures
from  concurrent  modifica‐ tions, and implementing critical sections and monitors.

A  mutex  has  two possible states: unlocked (not owned by any thread), and locked (owned by
one thread).  A mutex can never be owned by two different threads simultaneously.  A thread
attempting to lock a mutex that is already locked by another thread is suspended until the
owning thread unlocks the mutex first.
```


````c
#include <pthread.h>
#include <stdio.h>

pthread_mutex_t mutex;
int shared_var = 0;

void* thread_func(void* arg) {
    for (int i = 0; i < 100000; i++) {
        pthread_mutex_lock(&mutex);
        shared_var++;
        pthread_mutex_unlock(&mutex);
    }
    return NULL;
}

int main() {
    pthread_t t1, t2;
    
    // Initialize mutex
    pthread_mutex_init(&mutex, NULL);
    
    // Create two threads
    pthread_create(&t1, NULL, thread_func, NULL);
    pthread_create(&t2, NULL, thread_func, NULL);
    
    // Wait for threads to finish
    pthread_join(t1, NULL);
    pthread_join(t2, NULL);
    
    // Destroy mutex
    pthread_mutex_destroy(&mutex);
    
    printf("Final shared_var: %d\n", shared_var);
    return 0;
}
````


## create threads in a loop


````c
#include <pthread.h>
#include <stdio.h>

void* job(void* arg) {
    // Job logic here
    return NULL;
}

int main() {
    int num_threads = 5;  // Example: create 5 threads
    pthread_t threads[num_threads];
    void* job_args = NULL;  // Define as needed
    void* job_returns[num_threads];

    // Create threads in a loop
    for (int i = 0; i < num_threads; i++) {
        pthread_create(&threads[i], NULL, job, job_args);
        // OR pthread_create(threads +i, NULL, job, job_args);
    }

    // Join threads in a loop
    for (int i = 0; i < num_threads; i++) {
        pthread_join(threads[i], &job_returns[i]);
    }

    printf("All threads completed\n");

    return 0;
}
````



## Get return value from a thread (`pthread_join`)


````c
void* thread_func(void* arg) {
    int* result = malloc(sizeof(int));
    *result = 42;
    return result;
}

int main() {
    pthread_t tid;
    int* retval;
    pthread_create(&tid, NULL, thread_func, NULL);
    pthread_join(tid, (void**)&retval);
    printf("%d\n", *retval);
    free(retval); // 반드시 해제해야 함!
    return 0;
}
````


- **직접 메모리 할당/해제**를 신경 써야 함  
  (안 하면 메모리 누수 발생)

- 코드가 복잡해지고 실수할 여지가 많음  
  (예: `free()`를 빼먹거나, 잘못된 포인터를 반환하는 경우 등)



## How to pass arguments to threads in C


C에서 pthread로 쓰레드를 만들 때,  쓰레드 함수의 인자는 반드시 `void*` 타입이어야 합니다.


- 흔한 실수

    반복문에서 쓰레드를 여러 개 만들 때,  
    아래처럼 **반복 변수의 주소**를 넘기는 실수를 많이 합니다:

    ````c
    for (int i = 0; i < N; i++) {
        pthread_create(&tid[i], NULL, thread_func, &i); // ❌
    }
    ````

    이렇게 하면 모든 쓰레드가 **같은 주소(&i)**를 참조하게 되고,  
    쓰레드가 실행될 때마다 i 값이 바뀌어서  
    **예상과 다르게 동작**합니다.



- 올바른 방법

    1. **각 쓰레드마다 고유한 값을 넘겨야 함**
    2. 방법:
       - **동적 메모리 할당(malloc)**
       - **배열의 각 원소 주소 전달**
       - **구조체 사용**


    - 예시 1: 동적 메모리 할당

      ````c
      void* thread_func(void* arg) {
          int value = *(int*)arg;
          printf("Thread %d started\n", value);
          free(arg);   // 동적 할당된 메모리 해제
          return NULL;
      }


      for (int i = 0; i < N; i++) {
          int* arg = malloc(sizeof(int));
          *arg = i;
          pthread_create(&tid[i], NULL, thread_func, arg);
      }
      ````

      - 쓰레드 함수에서는 받은 포인터를 사용하고,  
      - 작업이 끝나면 반드시 `free()`로 메모리 해제해야 합니다.



    - 예시 2: 배열 원소 주소 전달

      ````c
      int args[N];
      for (int i = 0; i < N; i++) {
          args[i] = i;
          pthread_create(&tid[i], NULL, thread_func, &args[i]);
      }
      ````

      - 이 방법은 배열이 **main 함수의 스코프 내에 살아있을 때만** 안전합니다.

## example of multi threading


- **멀티코어 환경**에서는 쓰레드를 나누면 더 빨라질 수 있다.
- **싱글코어**거나, 작업이 작으면 효과가 없다.
- 실제 성능은 CPU 코어 수, 작업량, 쓰레드 오버헤드에 따라 달라진다.


```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>

#define N 1000000
#define THREADS 4

int arr[N];

typedef struct {
    int start;
    int end;
    long long sum;
} ThreadArg;

void* sum_part(void* arg) {
    ThreadArg* t = (ThreadArg*)arg;
    t->sum = 0;
    for (int i = t->start; i < t->end; i++)
        t->sum += arr[i];
    return NULL;
}

int main() {
    // 배열 초기화
    for (int i = 0; i < N; i++)
        arr[i] = 1; // 모든 값 1로 설정 (합은 N)

    pthread_t tid[THREADS];
    ThreadArg args[THREADS];

    int chunk = N / THREADS;
    for (int i = 0; i < THREADS; i++) {
        args[i].start = i * chunk;
        args[i].end = (i == THREADS - 1) ? N : (i + 1) * chunk;
        pthread_create(&tid[i], NULL, sum_part, &args[i]);
    }

    long long total = 0;
    for (int i = 0; i < THREADS; i++) {
        pthread_join(tid[i], NULL);
        total += args[i].sum;
    }

    printf("Total sum: %lld\n", total);
    return 0;
}
```



## Difference between trylock and lock in C

- 원래는 mutex가 unlock 될 때까지 기다린다.
- 근데 trylock은 기다리지 않고 분기되어 할일을 하게 만들 수 있다.


```c
#include <pthread.h>
#include <errno.h>
#include <stdio.h>

int main() {
    pthread_mutex_t mutex;
    pthread_mutex_init(&mutex, NULL);

    int ret = pthread_mutex_trylock(&mutex);
    if (ret == 0) {
        printf("Mutex acquired.\n");
        // Do work
        pthread_mutex_unlock(&mutex);
    } else if (ret == EBUSY) {
        printf("Mutex is busy.\n");
    } else if (ret == EINVAL) {
        printf("Mutex not initialized.\n");
    } else {
        printf("Other error: %d\n", ret);
    }

    pthread_mutex_destroy(&mutex);
    return 0;
}
```



## Condition variables in C - ONE



- **Efficient Waiting**: Condition variables allow threads to wait for specific conditions
  _without consuming CPU cycles,_ unlike _busy-waiting_ loops.

- **Producer-Consumer Synchronization**: Commonly used in scenarios like the gas station example,
  where one thread produces (fills fuel) and another consumes (waits for fuel),
  ensuring safe access to shared resources.

- **Avoiding Race Conditions**: Combined with mutexes, they prevent race conditions by
  ensuring atomic check-and-wait operations.



````c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

pthread_mutex_t mutexFuel;
pthread_cond_t condFuel;
int fuel = 0;
const int FUEL_THRESHOLD = 50;
const int FILL_AMOUNT = 10;

void* car(void* arg) {
    pthread_mutex_lock(&mutexFuel);
        pthread_cond_wait(&condFuel, &mutexFuel);
        // Equivalent to:
        // pthread_mutex_unlock(&mutexFuel);
        // wait for signal on condFuel
        // pthread_mutex_lock(&mutexFuel);
    }
    printf("Car: Fuel is enough (%d), moving!\n", fuel);
    pthread_mutex_unlock(&mutexFuel);
    return NULL;
}

void* gas_station(void* arg) {
    while (fuel < FUEL_THRESHOLD) {
        pthread_mutex_lock(&mutexFuel);
        sleep(1); // Simulate time to fill
        fuel += FILL_AMOUNT;
        printf("Gas Station: Added %d fuel, total: %d\n", FILL_AMOUNT, fuel);
        pthread_cond_signal(&condFuel);
        pthread_mutex_unlock(&mutexFuel);
    }
    return NULL;
}

int main() {
    pthread_t t1, t2;
    pthread_mutex_init(&mutexFuel, NULL);
    pthread_cond_init(&condFuel, NULL);

    pthread_create(&t1, NULL, car, NULL);
    pthread_create(&t2, NULL, gas_station, NULL);

    pthread_join(t1, NULL);
    pthread_join(t2, NULL);

    pthread_mutex_destroy(&mutexFuel);
    pthread_cond_destroy(&condFuel);
    return 0;
}
````


## Condition variables in C - TWO (`pthread_cond_signal` vs `pthread_cond_broadcast`)


- **Scenario Overview**: Multiple worker threads wait on a condition variable for tasks in 
  _a shared queue,_ while a producer adds batches of tasks and broadcasts to wake all workers.

- **Broadcast Usage**: `pthread_cond_broadcast` notifies all waiting threads _simultaneously_,
  ideal for scenarios where multiple consumers need to react to a state change (e.g., new tasks available).
  
- **Mutex Integration**: The _mutex_ protects the shared `task_count`, ensuring _atomic updates_
  and preventing race conditions during wait/signal operations.

- **Practical Benefit**: Efficient for thread pools or event-driven systems, avoiding
  individual signals and _reducing overhead compared to waking threads one by one._


````c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

pthread_mutex_t mutexQueue;
pthread_cond_t condQueue;
int task_count = 0;
const int MAX_TASKS = 5;
const int NUM_WORKERS = 3;

void* worker(void* arg) {
    int worker_id = *(int*)arg;
    while (1) {
        pthread_mutex_lock(&mutexQueue);
        while (task_count == 0) {
            printf("Worker %d: Waiting for tasks...\n", worker_id);
            pthread_cond_wait(&condQueue, &mutexQueue);
        }
        task_count--; // Process a task
        printf("Worker %d: Processed a task, remaining: %d\n", worker_id, task_count);
        pthread_mutex_unlock(&mutexQueue);
        sleep(1); // Take a rest after work!
    }
    return NULL;
}

void* producer(void* arg) {
    for (int i = 0; i < 2; i++) {
        sleep(2);
        pthread_mutex_lock(&mutexQueue);
        task_count += MAX_TASKS; // Father made some businesses!
        printf("Producer: Added %d tasks, total: %d\n", MAX_TASKS, task_count);
        pthread_cond_broadcast(&condQueue); // Wake all workers
        pthread_mutex_unlock(&mutexQueue);
    }
    return NULL;
}

int main() {
    pthread_t t_workers[NUM_WORKERS], t_prod;
    int worker_ids[NUM_WORKERS];
    pthread_mutex_init(&mutexQueue, NULL);
    pthread_cond_init(&condQueue, NULL);

    for (int i = 0; i < NUM_WORKERS; i++) {
        worker_ids[i] = i + 1;
        pthread_create(&t_workers[i], NULL, worker, &worker_ids[i]);
    }
    pthread_create(&t_prod, NULL, producer, NULL);

    // Let it run briefly
    sleep(10);
    return 0;
}
````


## Practical example for `pthread_mutex_trylock`


````c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

#define NUM_STOVES 2
#define NUM_CHEFS 5

pthread_mutex_t stoves[NUM_STOVES];

void* chef(void* arg) {
    int chef_id = *(int*)arg;
    int stove_id = -1;
    while (stove_id == -1) {
        for (int i = 0; i < NUM_STOVES; i++) {
            if (pthread_mutex_trylock(&stoves[i]) == 0) {
                stove_id = i;
                printf("Chef %d: Got stove %d, cooking...\n", chef_id, stove_id);
                sleep(2); // Simulate cooking
                printf("Chef %d: Done with stove %d\n", chef_id, stove_id);
                pthread_mutex_unlock(&stoves[i]);
                break;
            }
        }
        if (stove_id == -1) {
            printf("Chef %d: No free stoves, trying again...\n", chef_id);
            sleep(1); // Wait before retrying
        }
    }
    return NULL;
}

int main() {
    pthread_t chefs[NUM_CHEFS];
    int chef_ids[NUM_CHEFS];
    for (int i = 0; i < NUM_STOVES; i++) {
        pthread_mutex_init(&stoves[i], NULL);
    }
    for (int i = 0; i < NUM_CHEFS; i++) {
        chef_ids[i] = i + 1;
        pthread_create(&chefs[i], NULL, chef, &chef_ids[i]);
    }
    for (int i = 0; i < NUM_CHEFS; i++) {
        pthread_join(chefs[i], NULL);
    }
    for (int i = 0; i < NUM_STOVES; i++) {
        pthread_mutex_destroy(&stoves[i]);
    }
    return 0;
}
````

- 다만, 위 예시는 busy-waiting을 유발하는 단점이 있다.

- 아래처럼 하면 더 좋다.

### 개선 버전

````c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

#define NUM_STOVES 2
#define NUM_CHEFS 5

pthread_mutex_t mutexStoves;
pthread_cond_t condStoves;
int available_stoves = NUM_STOVES;

void* chef(void* arg) {
    int chef_id = *(int*)arg;
    pthread_mutex_lock(&mutexStoves);
    while (available_stoves == 0) {
        printf("Chef %d: No free stoves, waiting...\n", chef_id);
        pthread_cond_wait(&condStoves, &mutexStoves);
    }
    available_stoves--;
    printf("Chef %d: Got a stove, cooking... (available: %d)\n", chef_id, available_stoves);
    pthread_mutex_unlock(&mutexStoves);

    sleep(2); // Simulate cooking

    pthread_mutex_lock(&mutexStoves);
    available_stoves++;
    printf("Chef %d: Done, freeing stove (available: %d)\n", chef_id, available_stoves);
    pthread_cond_broadcast(&condStoves); // Notify all waiting chefs
    pthread_mutex_unlock(&mutexStoves);
    return NULL;
}

int main() {
    pthread_t chefs[NUM_CHEFS];
    int chef_ids[NUM_CHEFS];
    pthread_mutex_init(&mutexStoves, NULL);
    pthread_cond_init(&condStoves, NULL);

    for (int i = 0; i < NUM_CHEFS; i++) {
        chef_ids[i] = i + 1;
        pthread_create(&chefs[i], NULL, chef, &chef_ids[i]);
    }
    for (int i = 0; i < NUM_CHEFS; i++) {
        pthread_join(chefs[i], NULL);
    }
    pthread_mutex_destroy(&mutexStoves);
    pthread_cond_destroy(&condStoves);
    return 0;
}
````


## What is `pthread_exit`?


```c
void* child(void* arg) {
    printf("Child running\n");
    sleep(2);
    return NULL;
}

void* parent(void* arg) {
    pthread_t t;
    pthread_create(&t, NULL, child, NULL);
    printf("Parent exiting\n");
    pthread_exit(NULL); // Child keeps running
    // return NULL;     // Child will be eliminated
}

int main() {
    pthread_t p;
    pthread_create(&p, NULL, parent, NULL);
    pthread_join(p, NULL); // Wait for parent
    // Child is still running; need to join it elsewhere or detach
    return 0;
}
```

- When a thread creates child threads and then calls `pthread_exit` (or returns), the child
  threads _continue executing independently_; the parent's exit doesn't terminate them.

  However:

    - If the exiting thread is _the main thread_, the process may terminate, potentially killing all
      threads (depending on implementation, but in Linux, main exiting often ends the process).

    - Child threads run until they finish, but _to retrieve their return values,_ another thread
      (e.g., the original parent or main) must call `pthread_join` on them before the process ends.

    - _If no thread joins them,_ their resources might not be cleaned up properly, _leading to potential leaks._



## Introduction to barriers (`pthread_barrier`)


`pthread_barrier_wait` is a POSIX threads function used for _barrier synchronization_.

It causes the calling thread to _wait at a barrier until all threads in the associated barrier_ group
have called it, at which point all threads are released simultaneously. It's useful for
synchronizing multiple threads at specific points in execution.



- Example 1: Basic Barrier with Two Threads

    This example creates a barrier for 2 threads. Each thread prints a message before and after waiting at the barrier.

    ````c
    #include <pthread.h>
    #include <stdio.h>
    #include <stdlib.h>

    pthread_barrier_t barrier;

    void* thread_func(void* arg) {
        printf("Thread %d: Before barrier\n", *(int*)arg);
        pthread_barrier_wait(&barrier);
        printf("Thread %d: After barrier\n", *(int*)arg);
        return NULL;
    }

    int main() {
        pthread_t t1, t2;
        int id1 = 1, id2 = 2;

        pthread_barrier_init(&barrier, NULL, 2);

        pthread_create(&t1, NULL, thread_func, &id1);
        pthread_create(&t2, NULL, thread_func, &id2);

        pthread_join(t1, NULL);
        pthread_join(t2, NULL);

        pthread_barrier_destroy(&barrier);
        return 0;
    }
    ````


### advanced example


In this code, the barrier ensures no thread starts iteration 2 until all have finished
iteration 1, maintaining _phase synchronization_. Without it, threads could proceed asynchronously,
potentially causing inconsistent shared data handling across iterations.


````c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>

#define NUM_THREADS 3
#define NUM_ITERATIONS 2

pthread_barrier_t barrier;
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
int shared_data = 0;

void* thread_func(void* arg) {
    int id = *(int*)arg;
    for (int iter = 1; iter <= NUM_ITERATIONS; iter++) {
        // Simulate work in this iteration
        printf("Thread %d: Starting iteration %d\n", id, iter);
        // Critical section: update shared data
        pthread_mutex_lock(&mutex);
        shared_data += id;
        printf("Thread %d: Updated shared_data to %d in iteration %d\n", id, shared_data, iter);
        pthread_mutex_unlock(&mutex);
        
        // Wait for all threads to finish this iteration
        pthread_barrier_wait(&barrier);
        printf("Thread %d: All threads done with iteration %d, proceeding\n", id, iter);
    }
    return NULL;
}

int main() {
    pthread_t threads[NUM_THREADS];
    int ids[NUM_THREADS] = {1, 2, 3};

    pthread_barrier_init(&barrier, NULL, NUM_THREADS);

    for (int i = 0; i < NUM_THREADS; i++) {
        pthread_create(&threads[i], NULL, thread_func, &ids[i]);
    }

    for (int i = 0; i < NUM_THREADS; i++) {
        pthread_join(threads[i], NULL);
    }

    pthread_barrier_destroy(&barrier);
    return 0;
}
````



## What is `pthread_t`?


- `pthread_t`

  It is a data type in the POSIX threads (pthreads) library, representing a thread
  identifier. It's an opaque type (often an integer or struct internally) assigned to each thread
  created via `pthread_create`. It uniquely identifies a thread within the process's thread group
  and is used for operations like `pthread_join`, `pthread_cancel`, or `pthread_equal`. The
  actual value is implementation-dependent and not directly portable across systems.


- `SYS_gettid`

  It is a Linux system call (syscall number 186) that retrieves the kernel thread
  ID (TID) of the calling thread. This is a unique integer assigned by the kernel to each thread in
  the system, different from the process ID (PID). It's accessed via `syscall(SYS_gettid)` in C code
  and is useful for low-level system interactions, such as debugging or interfacing with kernel
  features.


- Comparison

  **Scope and Purpose**: `pthread_t` is a user-space identifier for threads managed by the
  pthreads library, focusing on thread lifecycle and synchronization within a process. `SYS_gettid`
  provides a kernel-level TID, which is global across the system and independent of pthreads.



## What are detached threads?


- **What is a Detached Thread?**  

  A detached thread _automatically cleans up_ resources upon termination.  
  The main thread does not need to wait using `pthread_join`; the OS reclaims resources when the thread ends.

  Be cautious, as if the main thread exits first, detached threads may also terminate.



- **Purpose**  

  Suitable for independent background tasks where results are not needed.  
  Examples: Logging, network processing.



### Creation methods

1. After creating a thread, call `pthread_detach(thread_id)`.

    ````c
    #include <pthread.h>
    #include <stdio.h>

    void* thread_func(void* arg) {
        printf("Thread running and will detach\n");
        return NULL;
    }

    int main() {
        pthread_t thread;
        pthread_create(&thread, NULL, thread_func, NULL);
        pthread_detach(thread);  // Detach after creation

        // Main continues; thread cleans up on exit

        //return 0;      // this may terminate child thread
        pthread_exit(0);
    }
    ````


2. Thread attributes (`pthread_attr_t`) to create it.

    ````c
    #include <pthread.h>
    #include <stdio.h>

    void* thread_func(void* arg) {
        printf("Thread running (detached via attributes)\n");
        return NULL;
    }

    int main() {
        pthread_t thread;
        pthread_attr_t attr;
        pthread_attr_init(&attr);
        pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_DETACHED);  // Set detached

        pthread_create(&thread, &attr, thread_func, NULL);

        pthread_attr_destroy(&attr);

        // Main continues; thread cleans up on exit

        pthread_exit(0);
    }
    ````


## Static initializers in the `PTHREAD API`


- You can use multiple mutexes by declaring each one _separately_ and initializing them with `PTHREAD_MUTEX_INITIALIZER`.
- This macro is safe for global, static, and local variables.
- For mutexes inside _dynamically allocated memory_ (e.g., in a struct allocated with `malloc`), use `pthread_mutex_init` instead.
- Always call `pthread_mutex_destroy` when done, even if you used the static initializer.


**Examples:**

- Static initialization:

  ````c
  pthread_mutex_t mutex1 = PTHREAD_MUTEX_INITIALIZER;
  pthread_mutex_t mutex2 = PTHREAD_MUTEX_INITIALIZER;

  // Use mutexes...

  pthread_mutex_destroy(&mutex1);
  pthread_mutex_destroy(&mutex2);
  ````


- Dynamic allocation:

  ````c
  struct Data {
      pthread_mutex_t lock;
  };

  struct Data *d = malloc(sizeof(struct Data));
  pthread_mutex_init(&d->lock, NULL);

  // Use d->lock...

  pthread_mutex_destroy(&d->lock);
  free(d);
  ````




## Deadlocks in C

- **Definition of Deadlock**  

  A state where multiple threads hold resources while waiting for resources held by others,
  leading to _indefinite blocking._


    - Example of Deadlock Occurrence  

      In code using two mutexes (e.g., fuel_mutex, water_mutex),  
      Thread A acquires fuel_mutex first, then tries to acquire water_mutex,  
      While Thread B acquires water_mutex first, then tries to acquire fuel_mutex,  
      Causing both threads to wait for each other's mutex, resulting in deadlock.



- **Methods to Prevent Deadlock**

    - Design to always acquire mutexes in the _same order_
    - Protect multiple resources with a single mutex  
    - Use timeouts or trylock to detect and avoid deadlocks


## Recursive mutexes


- **Recursive mutex**:

  Allows the same thread to lock it _multiple times_ without deadlock, using `PTHREAD_MUTEX_RECURSIVE`.



- **Exclusivity**:

  Even if locked only once, it's _exclusive to the owning thread; others block until unlocked._



- **Unlocking**: 

  Only the owning thread can unlock it (same number of unlocks as locks).



## Introduction to semaphores in C


**sem_init arguments:**

  1. **Pointer to semaphore variable**

  2. **pshared**:  
     - `0` = shared between threads  
     - `1` = shared between processes

  3. **Initial value**:  
     - Number of allowed accesses



**Basic semaphore usage example:**

````c
#include <stdio.h>
#include <pthread.h>
#include <semaphore.h>

sem_t sem;

void* worker(void* arg) {
    sem_wait(&sem); // Acquire semaphore
    printf("Thread %ld entered critical section\n", (long)arg);
    // Simulate work
    sleep(1);
    printf("Thread %ld leaving critical section\n", (long)arg);
    sem_post(&sem); // Release semaphore
    return NULL;
}

int main() {
    pthread_t threads[3];
    sem_init(&sem, 0, 1); // Semaphore shared between threads, initial value 1

    for (long i = 0; i < 3; ++i)
        pthread_create(&threads[i], NULL, worker, (void*)i);

    for (int i = 0; i < 3; ++i)
        pthread_join(threads[i], NULL);

    sem_destroy(&sem);
    return 0;
}
````

This example allows only **one thread** in the critical section at a time (acts like a mutex).



> [!qt] Why name like 'wait'?
>   󱞪 
> 
> 
> The naming comes from how semaphores work conceptually:
> 
> - **`sem_wait`**:  
> 
>     The function tries to **decrement** the semaphore’s value.  
>
>     - If the value is **greater than zero**, it decrements and the thread continues.
>     - If the value is **zero**, the thread **waits** (blocks) until another thread calls `sem_post` and increments the value.
> 
>
>     So, "`wait`" means:  
>
>     _Wait until the semaphore is available (i.e., its value is greater than zero), then proceed._
> 
> 
> - **`sem_post`**:  
> 
>   This **increments** the semaphore’s value, potentially waking up a waiting thread.
> 
> 
>   **Summary:**  
>   
>   - `sem_wait`: Wait for permission to enter (may block).
>   - `sem_post`: Signal that you’re done (release permission).
> 



## Practical example using semaphores (Login Queue)


- login queue

````c
#define MAX_USERS 12
#define TOTAL_USERS 16

sem_t semaphore;

void* user_thread(void* arg) {
    int user_id = *(int*)arg;
    printf("User %d trying to log in...\n", user_id);
    
    sem_wait(&semaphore);  // Wait to decrement semaphore (log in)
    printf("User %d logged in\n", user_id);
    
    sleep(1);  // Simulate session time
    
    printf("User %d logging out\n", user_id);
    sem_post(&semaphore);  // Increment semaphore (log out)
    
    return NULL;
}

int main() {
    pthread_t threads[TOTAL_USERS];
    int user_ids[TOTAL_USERS];
    
    sem_init(&semaphore, 0, MAX_USERS);  // Initialize semaphore to 12
    
    for (int i = 0; i < TOTAL_USERS; i++) {
        user_ids[i] = i + 1;
        pthread_create(&threads[i], NULL, user_thread, &user_ids[i]);
    }
    
    for (int i = 0; i < TOTAL_USERS; i++) {
        pthread_join(threads[i], NULL);
    }
    
    sem_destroy(&semaphore);
    return 0;
}
````



## Producer - Consumer Problem in Multi-Threading


````c
#include <pthread.h>
#include <semaphore.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

#define BUFFER_SIZE 5
char* buffer[BUFFER_SIZE];
int in = 0, out = 0;

sem_t empty, full, mutex;

void* producer(void* arg) {
    char* tasks[] = {"Task 1", "Task 2", "Task 3", "Task 4", "Task 5", "Task 6", "Task 7", "Task 8", "Task 9", "Task 10"};
    for (int i = 0; i < 10; i++) {
        sem_wait(&empty);
        sem_wait(&mutex);
        buffer[in] = tasks[i];
        printf("Produced: %s\n", tasks[i]);
        in = (in + 1) % BUFFER_SIZE;
        sem_post(&mutex);
        sem_post(&full);
    }
    return NULL;
}

void* consumer(void* arg) {
    for (int i = 0; i < 10; i++) {
        sem_wait(&full);
        sem_wait(&mutex);
        char* task = buffer[out];
        printf("Consumed: %s\n", task);
        buffer[out] = NULL;
        out = (out + 1) % BUFFER_SIZE;
        sem_post(&mutex);
        sem_post(&empty);
        
        printf("Processing: %s\n", task);     // assume LONG LONG processing
        sleep(1);
    }
    return NULL;
}

int main() {
    pthread_t prod, cons;
    sem_init(&empty, 0, BUFFER_SIZE);
    sem_init(&full, 0, 0);
    sem_init(&mutex, 0, 1);
    
    pthread_create(&prod, NULL, producer, NULL);
    pthread_create(&cons, NULL, consumer, NULL);
    
    pthread_join(prod, NULL);
    pthread_join(cons, NULL);
    
    sem_destroy(&empty);
    sem_destroy(&full);
    sem_destroy(&mutex);
    return 0;
}
````


- Practical Usefulness

    - **Decouples Producers and Consumers**: Allows asynchronous operation, improving responsiveness in systems where production and consumption rates differ.
    - **Resource Management**: Prevents overflow (too many items) and underflow (no items to consume), ensuring stable performance.
    - **Concurrency Control**: Enables safe multi-threading without data races, using semaphores for counting and mutex for mutual exclusion.
    - **Pipelining**: Supports _overlapping_ work stages, boosting throughput in workflows with variable processing times.


- Use Cases:

    - **Web Servers**: Handling incoming requests (producers) and processing them (consumers) with a request queue.
    - **Data Processing Pipelines**: Reading data from files/network (producers) and transforming it (consumers).
    - **Media Streaming**: Buffering video/audio frames for smooth playback, with producers fetching data and consumers rendering it.
    - **Print Spooling**: Jobs added to a queue by multiple users, processed sequentially by a printer.
    - **Thread Pools**: Workers (consumers) pulling tasks from a shared queue filled by submitters (producers).




## What are binary semaphores? Difference between Mutexes


A binary semaphore and a mutex are similar—they both allow only one thread to access a
critical section at a time. 


- Differences:

    - **Ownership**:  
      - A mutex can only be unlocked by the thread that locked it _(ownership enforced)_.
      - A binary semaphore can be signaled (posted) by any thread, not just the one that waited (no ownership).

    - **Use cases**:  
      - Mutexes are for mutual exclusion—protecting shared data.
      - Binary semaphores are often used for signaling _between threads_ (e.g., thread A _notifies_ thread B to proceed).



- Summary:

  - Use a mutex for protecting shared resources.  

  - Use a binary semaphore for thread synchronization or signaling,
    especially when you don’t need strict ownership.


## Getting the value of a semaphore


````c
int main() {
    sem_t semaphore;
    int value;

    sem_init(&semaphore, 0, 1);

    
    sem_getvalue(&semaphore, &value);   // Get the current value of the semaphore
    printf("Current semaphore value: %d\n", value);

    sem_destroy(&semaphore);
    return 0;
}
````



## Parallelism vs Concurrency


- **Parallelism**

    - Multiple tasks are executed truly simultaneously.
    - Performance scales linearly with available resources (e.g., more CPU cores → more throughput).
    - Distributing _independent_ tasks across them.
    - Example: Four CPU cores processing four separate jobs at the same time.


- **Concurrency**

    - Multiple tasks are managed so their execution _overlaps_ in time, even if not truly simultaneous.
    - _Pipelining_ is used to keep resources busy by switching between tasks when one is waiting (e.g., for I/O).
    - The key to performance is _minimizing idle time_ **by donating or reallocating resources to other tasks.**
    - Example: A single CPU switches rapidly between tasks, so while one waits, another runs.



- **Summary**

    - Both parallelism and concurrency aim to maximize resource utilization and throughput.

    - Parallelism achieves this by running tasks at the same time; concurrency does so by
      overlapping tasks and reducing idle periods.



## Thread Pools in C (using the PTHREAD API)


````c
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t cond = PTHREAD_COND_INITIALIZER;
int task_available = 0;

void* worker(void* arg) {
    while (1) {
        pthread_mutex_lock(&mutex);
        while (!task_available) {
            pthread_cond_wait(&cond, &mutex);  // Wait efficiently, no busy waiting
        }
        task_available = 0;  // Reset flag
        pthread_mutex_unlock(&mutex);
        printf("Worker %lu: Task executed\n", pthread_self());
        sleep(1);
    }
    return NULL;
}

int main() {
    pthread_t threads[2];
    for (int i = 0; i < 2; i++) {
        pthread_create(&threads[i], NULL, worker, NULL);
    }
    sleep(2);
    // Signal tasks
    for (int i = 0; i < 4; i++) {
        pthread_mutex_lock(&mutex);
        task_available = 1;
        pthread_cond_signal(&cond);  // Wake one worker
        pthread_mutex_unlock(&mutex);
        sleep(1);
    }
    // Cleanup (simplified)
    return 0;
}
````



## Thread Pools with function pointers in C


````c
#define NUM_WORKERS 2
#define NUM_TASKS 4

typedef struct {
    void (*myTask)(void* arg);
    void* arg;
} Task;

Task tasks[NUM_TASKS] = {
    {task_a, "Task A1"},
    {task_b, "Task B1"},
    {task_a, "Task A2"},
    {task_b, "Task B2"}
};

int task_index = 0;
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t cond = PTHREAD_COND_INITIALIZER;

void* worker(void* arg) {
    while (1) {
        pthread_mutex_lock(&mutex);
        while (task_index >= NUM_TASKS) {
            pthread_cond_wait(&cond, &mutex);
        }
        Task t = tasks[task_index++];
        pthread_mutex_unlock(&mutex);
        t.myTask(t.arg);
    }
    return NULL;
}

void task_a(void* arg) {
    printf("A: %s\n", (char*)arg);
}

void task_b(void* arg) {
    printf("B: %s\n", (char*)arg);
}

int main() {
    pthread_t workers[NUM_WORKERS];
    for (int i = 0; i < NUM_WORKERS; i++) {
        pthread_create(&workers[i], NULL, worker, NULL);
    }
    sleep(1);  // Let workers start
    pthread_cond_broadcast(&cond);  // Signal all tasks available
    sleep(2);  // Wait for completion
    return 0;
}
````
