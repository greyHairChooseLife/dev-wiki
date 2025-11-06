# System-Level I/O and Program Interaction



[notebookLM](https://notebooklm.google.com/notebook/f405095d-572f-4992-bf85-e8781645f33a)



- 10.1 Unix I/O
- 10.2 Files
- 10.3 Opening and Closing Files
- 10.4 Reading and Writing Files
- 10.5 Robust Reading and Writing with the Rio Package
- 10.6 Reading File Metadata
- 10.7 Reading Directory Contents
- 10.8 Sharing Files
- 10.9 I/O Redirection
- 10.10 Standard I/O
- 10.11 Putting It Together: Which I/O Functions Should I Use?
- 10.12 Summary



## Chapter Overview


**Introduction**

Programs rely on the operating system (OS) to interact with I/O devices (like disks and networks)
and communicate with other programs. This interaction is essential for building complex
applications, such as web servers that handle multiple clients at once, and for taking advantage of
multi-core processors.

Chapter 10 explains the basic I/O services in Unix systems, focusing on the simple, low-level
interface called Unix I/O.



### Core Concepts of Unix I/O

- **Files as I/O Devices:**  

  _On Linux, all I/O devices (disks, terminals, networks) are treated as files._ Input copies data
  from a device to memory; output copies data from memory to a device.



- **Descriptors:**  

  When a program opens a file, the OS returns a small non-negative integer called a *descriptor*.
  This number identifies the file for future operations.  

    - Every process starts with three default descriptors:  
      - Standard input (0)  
      - Standard output (1)  
      - Standard error (2)  

    - Constants like `STDIN_FILENO`, `STDOUT_FILENO`, and `STDERR_FILENO` represent these values.

      > [!nt]
      >
      > These constants are predefined macros in Unix-like systems (defined in `<unistd.h>` in C) that
      > represent the integer values of the standard file descriptors:
      > 
      > - `STDIN_FILENO`: 0 (standard input, typically the keyboard or piped input).
      > - `STDOUT_FILENO`: 1 (standard output, typically the terminal or piped output).
      > - `STDERR_FILENO`: 2 (standard error, typically the terminal for error messages).
      > 
      > They make code more readable and portable compared to hardcoding the numbers. For example, in C,
      > you can use `read(STDIN_FILENO, buf, n)` instead of `read(0, buf, n)`.




- **File Position:**  

  The OS tracks a byte offset for each open file.  

    - Reading copies bytes starting at the offset and moves the offset forward.  
    - Writing copies bytes from memory to the file and updates the offset.  
    - If reading starts at or past the end of the file, an **end-of-file (EOF)** condition occurs.




- **Closing Files:**  

  When done, a program closes the file, freeing OS resources. All open files are closed
  automatically when a process ends.




### I/O Layers and Robustness

- Higher-level functions (like `printf` and `scanf` in C) use **buffered I/O,** built on top of Unix I/O.


    - **Why Learn Unix I/O?**  

      - It helps you understand _how I/O interacts with processes and memory._
      - Sometimes you must use Unix I/O directly, such as for file metadata or reliable network programming.



    - **Short Counts:**

      Unix I/O functions (`read`, `write`) may transfer fewer bytes than requested, especially with network sockets.

      - The **Rio (Robust I/O) package** solves this by repeating operations until all requested data is transferred.

        > [!nt]
        >
        > Unix I/O functions like `read` and `write` can return short counts (fewer bytes than
        > requested) _due to interruptions (e.g., signals), end-of-file, or network issues (e.g., sockets
        > buffering data)._
        > 
        > Without Rio, you'd need to manually implement loops to retry operations until all
        > data is transferred, which is error-prone and repetitive. Rio automates this for robustness,
        > especially in network programming where partial transfers are common.
        > 
        > For simple disk I/O, short counts are rare, so Rio isn't always necessary, but it simplifies code.


        > [!nt]
        >
        > Short counts are common with network sockets and terminals. Always check the return value of
        > `read` and `write` to ensure all data is transferred. Use Rio for robust network I/O.



### Key Subjects Explained


#### 10.2 Files

- Linux uses different file types:

    - **Regular File:**  
      Stores _any kind of data._  

      - No distinction between text and binary files.  
      - Text files are lines ending with '\n' (LF). Windows uses '\r\n' (CRLF).


    - **Directory:**  
      Contains links to files and other directories.  
      - Special entries: `.` (self), `..` (parent).


    - **Socket:**
      Used for network communication between processes.


    > [!nt]
    >
    > 소켓 파일은 리눅스에서 프로세스 간 통신을 위해 운영체제가 제공하는 특수 파일로, 개발자가 직접
    > 내용을 읽거나 수정하는 것이 아니라, 각 언어에서 제공하는 소켓 API를 통해 데이터를 송수신하는 용도로
    > 사용된다. 이미 잘 만들어진 소켓 기능과 라이브러리를 활용하여 개발자는 통신 기능을 쉽게 구현할 수
    > 있으며, 소켓 파일 자체는 커널이 관리하는 엔드포인트 역할만 한다.




    > [!nt] There are also ...
    >
    > - **Character Device Files**: Handle data as a stream of characters, like keyboards or serial ports.
    >   e.g., `/dev/tty` for terminals
    > 
    > - **Block Device Files**: Handle data in blocks, typically for storage devices.
    >   e.g., `/dev/sda` for disks
    > 
    > - **Symbolic Links**: (soft links)Pointers to other files or directories.
    > - **FIFOs (Named Pipes)**: Allow inter-process communication via a named pipe.
    > - **Unix Domain Sockets**: Similar to sockets but for local communication.





- Files are organized _in a single hierarchy_ starting at `/`.

    - **Pathnames:**  
      - **Absolute:** Starts with `/` (e.g., `/home/user/file.txt`).  
      - **Relative:** Starts with a filename (e.g., `file.txt` or `./file.txt`).



#### 10.3 Opening and Closing Files

- Files are managed with `open` and `close`.

    - **`open`:**  
      _Converts a filename to a file descriptor._  

      ```c
      int open(char *filename, int flags, mode_t mode)
      ```

      - Returns _the smallest_ available descriptor.

        > [!qt] What is "the smallest" about?
        >
        > The `open` function returns the smallest unused file descriptor number available in the
        > process's descriptor table. Descriptors start from 0 (stdin), 1 (stdout), 2 (stderr), so the first
        > `open` typically returns 3, then 4, and so on. If a descriptor is closed, it can be reused for the
        > next `open`.


      - **Flags:**  

        - `O_RDONLY` (read), `O_WRONLY` (write), `O_RDWR` (read/write)
        - Can combine with `O_CREAT`, `O_TRUNC`, `O_APPEND`

      - **Mode:**  
        - Sets permissions for new files: `mode & ~umask`



      - > Examples (assuming `O_CREAT` flag):
        > 
        > - `open("file.txt", O_CREAT | O_WRONLY, 0644)`:
        >   If `umask` is 0022 (~0022 = 0755), effective permissions are 0644 & 0755 = 0644 (rw-r--r--).
        > 
        > - `open("file.txt", O_CREAT | O_WRONLY, 0755)`:
        >   With same `umask`, effective is 0755 & 0755 = 0755 (rwxr-xr-x).
        > 
        > - `open("file.txt", O_CREAT | O_WRONLY, 0777)`:
        >   Effective is 0777 & 0755 = 0755 (rwxr-xr-x), as `umask` blocks group/other write.





    - **`close`:**  

        _Tells the OS the file is no longer needed._  

        - ```c
          int close(int fd)
          ```


        - **Closing the file descriptor with `close(fd)` does not remove the memory mapping created by `mmap()`.**

          The mapping remains in the process's virtual address space until you explicitly unmap it using
          `munmap()` or the process terminates.

          ```c
          int main() {
              int fd = open("example.txt", O_RDONLY);
              if (fd == -1) return 1;

              // Map the file into memory
              char *map = mmap(NULL, 100, PROT_READ, MAP_PRIVATE, fd, 0);
              if (map == MAP_FAILED) return 1;

              // Close the file descriptor - mapping still exists
              close(fd);

              // Access the mapped memory (assuming file has content)
              printf("First char: %c\n", map[0]);

              // Unmap to free memory
              munmap(map, 100);
              return 0;
          }
          ```



#### 10.4 Reading and Writing Files

- Use `read` and `write` for I/O.

    - **`read`:**  
      Copies up to `n` bytes from file to memory.  
      ```c
      ssize_t read(int fd, void *buf, size_t n)
      ```
      - Returns bytes read, 0 on EOF, -1 on error.


    - **`write`:**  
      Copies up to `n` bytes _from memory to file._  
      ```c
      ssize_t write(int fd, const void *buf, size_t n)
      ```
      - Returns bytes written, -1 on error.



    - **Short Counts:**  
      Sometimes fewer bytes are transferred than requested.  

        - Common with EOF, terminals, or network sockets.  
          - EOF results fewer bytes anyway.

        - Rare with disk files (except at EOF).


#### 10.5 Robust I/O with the Rio Package

- _Rio handles short counts automatically,_ useful for network programming.


    - **Unbuffered Functions:**  

        - `rio_readn`: Reads up to `n` bytes, only short on EOF.  
        - `rio_writen`: Writes `n` bytes, never short.  
        - Automatically _restarts if interrupted._


        > **Unbuffered 함수(`rio_readn`, `rio_writen`)는 언제 쓰냐면:**
        > 
        > - **네트워크 프로그래밍**에서, 소켓으로 데이터를 주고받을 때 정확히 N 바이트만큼 읽거나 써야
        >   할 때 사용합니다. 일반적인 `read`, `write` 함수는 요청한 만큼 다 처리하지 못할 수도 있는데, 이
        >   함수들은 끝까지 다 처리해줍니다.
        > - **부분 읽기/쓰기 처리**가 필요할 때, 즉 한 번에 다 읽거나 쓰지 못할 경우 자동으로 반복해서
        >   끝까지 처리합니다. 신호 등으로 중단(EINTR)이 되어도 자동으로 재시도합니다.
        > - **바이너리 데이터 전송**처럼, 데이터 손실 없이 정확하게 전송해야 할 때 사용합니다.
        > 
        > **요약:** 정확하게 원하는 만큼 데이터를 읽거나 써야 하고, 중간에 끊김 없이 신뢰성 있게
        > 처리해야 할 때 사용합니다.






    - **Buffered Functions:**  

        - Use a buffer (`rio_t`) for efficient reading.  
        - `rio_readinitb`: Initializes buffer for a descriptor.  

        - `rio_readlineb`: Reads a line, includes newline, ends with NULL.  

            - Reads data into a buffer until it encounters a newline (`\n`), reaches the maximum
              buffer size, or hits EOF. _Best for text lines._

              It returns,
                - the number of bytes read (including the newline if present)
                - 0 on EOF
                - or -1 on error


        - `rio_readnb`: Reads up to `n` bytes.  

            - Reads exactly a specified number of bytes into a buffer. _Best for binary data
              or reading fixed amounts._

              It returns,
                - the number of bytes read
                - -1 on error (EOF is an error here)


        - Can mix `rio_readlineb` and `rio_readnb` on the same descriptor.





        > [!nt] 
        >
        > `rio_readlineb` is a blocking operation. It will wait (block) until either:
        > 
        > 1. A complete line (ending with '\n') is available to read
        > 2. The buffer is full
        > 3. EOF is reached
        > 4. An error occurs


        > [!nt]
        >
        > The Rio buffered functions require you to _explicitly allocate and manage a buffer in memory_
        > using the `rio_t` structure.
        > 
        > You initialize it with `rio_readinitb(fd, &rio_buf)` (where `rio_buf` is a `rio_t` variable),
        > and then use functions like `rio_readlineb` or `rio_readnb` to read data into that buffer.
        > 
        > This provides control over buffering for robustness (e.g., handling short counts), unlike
        > standard I/O which hides the buffer.
        > 
        > For example, in C:
        > 
        > ```c
        > rio_t rio;
        > rio_readinitb(fd, &rio);
        > char buf[100];
        > rio_readlineb(&rio, buf, 100);  // Reads a line into buf via the rio buffer
        > ```


        > [!nt]
        >
        > - 이건 읽기만 있다.
        > - 출력은 버퍼 없는쪽걸로 한다.



##### buffered vs unbuffered



- **Use `rio_readn`** when you need to read a specific number of bytes directly from the
  file descriptor without any buffering. It's ideal for one-off reads of known sizes (e.g., reading a
  fixed binary header or large data in a single call), where you want minimal overhead and direct
  control. No `rio_t` initialization needed.

  _example_
  Read exactly 1024 bytes of binary data directly from a socket (e.g., a file header).

  ````c
  char buf[1024];
  ssize_t n = rio_readn(fd, buf, 1024);
  if (n < 0) {
      // Handle error
  }
  // Process buf
  ````





- **Use `rio_readnb`** when performing multiple or sequential reads (e.g., in a loop for
  parsing lines, chunks, or streaming data like an HTTP body). It uses internal buffering for
  efficiency, reducing system calls, but requires a `rio_t` structure to be initialized first.

  _example_
  Read an HTTP response body in chunks after parsing headers, using the Rio buffer for efficiency.

  ````c
  rio_t rp;
  Rio_readinitb(&rp, fd);
  char buf[4096];
  size_t remaining = content_length;  // From headers
  while (remaining > 0) {
      size_t to_read = (remaining < 4096) ? remaining : 4096;
      ssize_t n = rio_readnb(&rp, buf, to_read);
      if (n <= 0) break;  // Error or EOF
      // Process buf
      remaining -= n;
  }
  ````


#### 10.8 Sharing Files


- Linux uses three _data structures_ for open files:


    - **Descriptor Table:**  

      _Unique_ to each process, indexed by descriptors.  

        - Points to entries in the File Table.



    - **File Table:**  
   
      _Can be Shared_ by all processes.

        - Tracks file position, reference count, and v-node pointer.  
        - Entry deleted when reference count is zero.



    - **Open File Entry Table:**

      _Unique_ to each fd(file descriptor)

      - [ ] 더 해




    - **V-node Table:**  

      _Shared_ by all processes.  

        - Stores file metadata (size, type).



    - **Sharing Effects:**  

        - Opening a file twice creates separate File Table entries (distinct positions).  
        - After `fork`, parent and child share File Table entries (same position).



#### 10.9 I/O Redirection

- I/O redirection uses `dup2`.

    - **`dup2`:**  
      Copies one descriptor to another, overwriting the target descriptor.  

      ```c
      int dup2(int oldfd, int newfd)
      ```

      - Closes `newfd` if open.
      - After `dup2(4, 1)`, both 4 and 1 refer to the same file table entry.  
      - Data written to standard output (1) goes to the file referenced by 4.

      > [!nt] "Close" means just drop of the file descriptor.
      >
      > When `dup2` closes `newfd`, it removes that file descriptor from the process's descriptor
      > table (making it unavailable for that slot). If no other descriptors (in this or other processes)
      > reference the same File Table entry, the underlying file is closed system-wide.




- Practical example in C:

  Open a file, use `dup2` to redirect stdout (1) to it, then `printf` writes to the file instead of the terminal.

  ```c
  #include <unistd.h>
  #include <fcntl.h>
  #include <stdio.h>

  int main() {
      int fd = open("output.txt", O_WRONLY | O_CREAT | O_TRUNC, 0644);  // fd = 3 (example)
      dup2(fd, 1);  // Copy fd (3) to stdout (1); now 1 points to output.txt
      close(fd);    // Optional, since 1 now handles it
      printf("This goes to output.txt\n");  // Writes to file, not terminal
      return 0;
  }
  ```

  After `dup2(3, 1)`, writing to descriptor 1 (via `printf`) goes to `output.txt`. The original stdout is redirected.




#### 10.11 Choosing I/O Functions

- Unix I/O is the foundation; Rio and Standard I/O are built on top of it.
- How to choose between Unix I/O, Rio, and Standard I/O:

    1. Use Standard I/O (`printf`, `fopen`) for disk and terminal I/O when possible.

    2. Do not use Standard I/O or `rio_readlineb` for binary files.
        - Text functions may fail on binary data.

    3. Use Rio for network sockets.
        - Standard I/O has restrictions with sockets.
        - For formatted output: use `sprintf` then `rio_writen`.
        - For formatted input: use `rio_readlineb` then `sscanf`.


          > [!nt] What is "Formatted I/O"?
          >
          > Formatted I/O involves converting data between binary and text formats for reading/writing.
          > For network sockets, standard I/O (like `printf`) isn't reliable due to buffering issues, so
          > combine Rio with formatting functions.
          > 
          >
          > **Improper approach (avoid for sockets):**
          > Trying `fprintf` or `printf` directly on a socket fd can fail due to buffering or short counts.
          > 
          > ```c
          > // This may not work reliably on sockets
          > fprintf(sockfd, "Age: %d\n", age);  // Buffering issues
          > ```
          > 
          > 
          > **Proper approach (using Rio for sockets):**
          > - Format output with `sprintf`, then send with `rio_writen`.
          > - Read input with `rio_readlineb`, then parse with `sscanf`.
          > 
          > Example (assuming a socket fd):
          > 
          > ```c
          > #include "rio.h"  // Rio header
          > #include <stdio.h>
          > 
          > // Output: Send formatted data
          > char buf[100];
          > int age = 25;
          > sprintf(buf, "Age: %d\n", age);  // Format into buf
          > rio_writen(sockfd, buf, strlen(buf));  // Send via Rio
          > 
          > // Input: Receive and parse
          > rio_t rio;
          > rio_readinitb(sockfd, &rio);
          > char line[100];
          > rio_readlineb(&rio, line, 100);  // Read line
          > int parsed_age;
          > sscanf(line, "Age: %d", &parsed_age);  // Parse
          > ```






## Unix I/O


### Why Learn Unix I/O?

Unix I/O is fundamental for understanding how systems work at a low level. Although most programs
use high-level I/O functions like `printf` and `scanf`, these are built on Unix I/O calls
provided by the kernel.


- **Key Reasons:**

    - **System Concepts:** I/O is closely linked to processes and virtual memory. Learning Unix I/O helps
        connect these core system ideas.

    - **Limitations of High-Level I/O:**
        - Standard I/O functions cannot access file metadata (such as size or creation time).
        - High-level I/O can be unsafe or unsuitable for network programming.

### Files as I/O Devices

- **File Structure:** In Linux, a file is a sequence of $m$ bytes: $B_0, B_1, \dots, B_{m-1}$.

- **Device Uniformity:** All I/O devices (networks, disks, terminals) are treated as files.

- **I/O Operations:**
    - Input copies data from a device to memory.
    - Output copies data from memory to a device.

### File Descriptors and File Position

- **Getting a Descriptor:** To use a file, an application asks the kernel to open it. The kernel returns a
    small non-negative integer called a **descriptor**.
    - The descriptor identifies the file for all later operations.
    - The kernel manages file details; the application only needs the descriptor.

- **Default Descriptors:** Every process starts with three open files:
    - Standard input (descriptor 0, `STDIN_FILENO`)
    - Standard output (descriptor 1, `STDOUT_FILENO`)
    - Standard error (descriptor 2, `STDERR_FILENO`)
    - These are defined in `<unistd.h>`.

- **File Position ($k$):** The kernel tracks a file position $k$ (initially 0) for each open file.
    - $k$ is the byte offset from the start of the file.
    - Applications can set $k$ using a seek operation.

### Reading, Writing, and Closing Files

- **Reading:** Reads $n > 0$ bytes from a file to memory, starting at position $k$, then increases $k$ by $n$.
- **Writing:** Writes $n > 0$ bytes from memory to a file, starting at $k$, then updates $k$.
- **End-of-File (EOF):** If $k \geq m$ (file size), a read triggers EOF. There is no special EOF character.
- **Closing Files:**
    - Applications tell the kernel to close files when done.
    - The kernel frees resources and makes the descriptor available again.
    - When a process ends, the kernel closes all open files automatically.

> [!nt]
>
> Unix I/O is essential for low-level programming and system understanding. It provides direct access to
> files and devices, which is necessary for tasks that high-level I/O cannot handle.






## Files









---

## Opening and Closing Files









---

## Reading and Writing Files









---

## Robust Reading and Writing with the Rio Package









---

## Reading File Metadata









---

## Reading Directory Contents









---

## Sharing Files









---

## I/O Redirection









---

## Standard I/O









---

## Putting It Together: Which I/O Functions Should I Use?









---

## Summary









---

