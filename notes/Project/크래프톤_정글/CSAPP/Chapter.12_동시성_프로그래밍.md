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




## Chapter Overview


Network programming enables computers to communicate and share resources. This chapter introduces
the basic ideas and tools needed to build network applications, such as Web servers. _All network
programs use a common structure and set of interfaces._

Network applications use system concepts like processes, signals, byte order, memory mapping, and
dynamic memory allocation. New topics include the client-server model and Internet services. The
chapter ends with building a simple Web server ("Tiny") that serves both static and dynamic content.

### Terminology


- **Host (or Machine):** A computer system capable of running clients and servers.

- **LAN (Local Area Network):** A network spanning a small geographical area, such as a building or campus.

- **WAN (Wide Area Network):** A network spanning larger geographical areas than a LAN, often
  using high-speed point-to-point phone connections.

- **Hub:** A simple box used in Ethernet that copies every bit received on one port to every other
  port, meaning every attached host sees every bit.

- **Bridge:** Used in bridged Ethernets to connect multiple segments. Bridges selectively copy
  frames only when necessary, improving network efficiency.

- **Router:** Specialized computer used to connect incompatible LANs and WANs to form an internet.

    > [!nt]
    >
    >
    > A **router** is a networking device (often a specialized computer or hardware appliance) that
    > forwards data packets between different computer networks. It acts as a traffic director,
    > determining the best path for data to travel based on IP addresses and routing tables. Routers
    > enable communication across networks by handling protocols like IP (Internet Protocol).
    > 
    > - **Incompatible LANs and WANs**: 
    >   - **LANs (Local Area Networks)** are small-scale networks, typically within a single building or
    >   campus, using technologies like Ethernet for high-speed, low-latency connections.
    >   - **WANs (Wide Area Networks)** are larger-scale networks spanning cities, countries, or
    >   continents, often using leased lines, satellites, or the internet backbone for longer-distance
    >   communication.
    >   - "Incompatible" refers to differences in network technologies, protocols, addressing schemes, or
    >   standards that prevent direct connection. For example, a LAN using IPv4 might not seamlessly link
    >   to a WAN using IPv6, or different physical media (wired vs. wireless) could clash. Routers bridge
    >   these incompatibilities by translating protocols, managing traffic, and ensuring data flows
    >   correctly to form a larger "internet" (a network of networks, like the global Internet).
    > 
    > This allows diverse networks to interconnect, enabling global communication.


- **Packet/Datagram:** Discrete chunks of data defined by the internet protocol. They consist of a
  header (containing size and address information) and a payload (the actual data bits).

- **Network Byte Order:** A uniform, defined byte ordering (big-endian) used by TCP/IP for any
  integer data item, such as an IP address, carried across the network.

- **Dotted-Decimal Notation:** The conventional, human-readable format for displaying 32-bit IP
  addresses (e.g., 128.2.194.242).

- **DNS (Domain Name System):** A distributed, worldwide database that maintains the mapping
  between human-friendly domain names (e.g., `cmu.edu`) and numerical IP addresses.

- **Connection:** A reliable, point-to-point communication channel established between a client
  process and a server process for sending and receiving streams of bytes.

- **Socket:** An end point of a connection, defined by a socket address: *address:port*. From a
  program's perspective, it acts like an open file with a descriptor.

- **Port:** A 16-bit integer used with an IP address to form a socket address.
    - **Well-Known Port:** Typically used by servers and permanently associated with a service
      (e.g., Web servers use port 80).
    - **Ephemeral Port:** Automatically assigned by the kernel to a client when it initiates a
      connection request.

- **Listening Socket:** A descriptor that has been converted by the `listen` function to
  passively wait for client connection requests.

- **Connected Descriptor:** A new descriptor created by the `accept` function that represents
  the established communication channel between the client and the server.

- **HTTP (Hypertext Transfer Protocol):** A text-based, application-level protocol used by Web
  clients (browsers) and servers for interaction.

- **URL (Universal Resource Locator):** A unique name used to identify content, specifying the
  location and potential arguments.

- **CGI (Common Gateway Interface):** A standard defining how a Web server handles requests for
  dynamic content by passing arguments and information to an executable program (CGI program) running
  in a child process.


#### 11.1 The Client-Server Programming Model

The basis of all network applications is the client-server model.

- **Server:** A process that manages a resource (e.g., disk files for a Web server) and
  provides services by manipulating that resource.

- **Client:** A process that initiates a service request to the server.

- **Transaction:** The core operation, involving four steps:
    1. Client sends a request (e.g., a Web browser requests a file).
    2. Server receives, interprets the request, and manipulates its resource (e.g., reads a disk file).
    3. Server sends a response to the client.
    4. Client receives and processes the response (e.g., displays the page).

- **Note:** Clients and servers are processes, meaning multiple can run concurrently on the same host.

#### 11.2 Networks

To a host, a network is simply an I/O device used as a source and sink for data. Data transfer is
typically handled by a network adapter plugged into the I/O bus, often using DMA.

- **Hierarchy:** Networks are organized geographically, starting with LANs (Local Area Networks).

    - **Ethernet** is the most popular LAN technology, connecting hosts via wires and hubs. A
      hub copies every bit received on one port to all others.
    - **Bridged Ethernets** connect multiple segments using bridges, which selectively copy
      frames only when necessary, improving bandwidth efficiency.

- **Internets (Interconnected Networks):** Multiple incompatible LANs and WANs (Wide Area
  Networks) are connected by specialized computers called **routers**.

- **Internet Protocol Layer:** A protocol software layer runs on every host and router to
  smooth out technological differences by providing:
    - **Naming Scheme:** A uniform format for host addresses (internet addresses).
    - **Delivery Mechanism:** A uniform way to bundle data into discrete chunks called
      **packets**, consisting of a header (size, addresses) and a payload (data bits).

#### 11.3 The Global IP Internet

The global IP Internet is the most famous implementation of an internet, running the **TCP/IP
protocol** suite.

- **TCP/IP Protocols:**
    - **IP (Internet Protocol):** Provides the basic naming scheme and an unreliable delivery
      mechanism for sending **datagrams** between hosts.
    - **UDP (Unreliable Datagram Protocol):** Extends IP to transfer datagrams between processes.
    - **TCP (Transmission Control Protocol):** Builds on IP to provide reliable, full duplex
      (bidirectional) connections between processes.

- **Programmer's View (IPv4):** The Internet based on IPv4 (32-bit addresses) has three main
  properties:
    1. Hosts map to 32-bit IP addresses.
    2. IP addresses map to Internet domain names.
    3. Processes communicate over reliable connections.

- **IP Addresses:** Unsigned 32-bit integers stored in network byte order (big-endian). They
  are typically displayed in **dotted-decimal notation** (e.g., 128.2.194.242). Functions like
  `inet_pton` and `inet_ntop` handle conversions between formats.

- **Domain Names:** Human-friendly names (e.g., `whaleshark.ics.cs.cmu.edu`) forming a hierarchy. 
  The **DNS (Domain Name System)** maintains a distributed database mapping domain names to IP addresses.

- **Connections:** Clients and servers communicate via reliable streams of bytes over **connections**.
    - **Socket:** An end point of a connection, defined by a socket address: *address:port*.
    - **Port:** A 16-bit integer. Servers use a **well-known port** (e.g., port 80 for Web servers); clients use an **ephemeral port** assigned by the kernel.
    - **Socket Pair:** A connection is uniquely identified by the socket addresses of its two end points: `(cliaddr:cliport, servaddr:servport)`.

#### 11.4 The Sockets Interface

The sockets interface is a set of functions used with Unix I/O to build network applications. A
socket is an open file with a descriptor.

- **Address Structures:** Socket addresses are stored in protocol-specific structures
(`sockaddr_in`) or generic structures (`sockaddr`).

- **Core Functions:**
    - `socket`: Creates a partially opened socket descriptor.
    - `connect`: Used by the client to establish a connection; it blocks until successful.
    - `bind`: Used by the server to associate its socket address with a descriptor.
    - `listen`: Converts an active socket into a **listening socket** ready to accept requests.
    - `accept`: Used by the server to wait for a connection request and return a new **connected descriptor** for communication.

- **Host/Service Conversion:** Modern functions (`getaddrinfo`, `getnameinfo`) convert
  between string names (host/service) and binary socket address structures in a protocol-independent
  manner.
    - `getaddrinfo`: Converts host/service strings into a list of `addrinfo` structures.
      Clients and servers iterate through this list until they successfully create/bind/connect a
      socket.

- **Helper Functions:** `open_clientfd` and `open_listenfd` encapsulate the complex
  sequence of calls (`getaddrinfo`, `socket`, `connect`/`bind`/`listen`) required to set up
  sockets, promoting protocol independence.

#### 11.5 Web Servers

Web clients and servers communicate via **HTTP (hypertext transfer protocol)**.

- **Web Content:** Content is a sequence of bytes with an associated MIME type (e.g., `text/html`).
    - **Static Content:** Fetched from a disk file.
    - **Dynamic Content:** Generated by running an executable file, returning its output.

- **URL:** A unique name for content, specifying the host, port, and filename/arguments.
- **HTTP Requests:** Consist of a request line (`method URI version`), request headers 
  (e.g., `Host`), and an empty line termination. The **GET** method is the primary request type.
- **HTTP Responses:** Consist of a response line (`version status-code status-message`),
  response headers (e.g., `Content-Type`, `Content-Length`), an empty line, and the response body.
- **Dynamic Content (CGI):** The **CGI (Common Gateway Interface)** standard dictates how
  dynamic content is served.
    - Arguments (for GET) are passed in the URI, accessible via the `QUERY_STRING`
      environment variable.
    - The server forks a child process. Before running the CGI program, the child uses `dup2`
      to redirect standard output to the connected descriptor, so the program's output goes directly
      to the client.
- **Process Flow:** Tiny opens a listening descriptor (`open_listenfd`), then enters an
  infinite loop, repeatedly calling `accept`, handling the transaction with `doit`, and closing
  the connection.
- **Static Serving:** Tiny reads the request, verifies the file exists (`stat`) and is
  readable, determines the MIME type, sends HTTP headers, and efficiently transfers the file contents
  using `mmap` (mapping the file to memory) and `rio_writen` (copying the memory region to the
  client descriptor).
- **Dynamic Serving:** Tiny sends the initial response line, forks a child process, sets the
  `QUERY_STRING` environment variable, redirects standard output to the client descriptor
  (`Dup2(fd, STDOUT_FILENO)`), and executes the CGI program (`Execve`). The parent blocks, waiting
  for the child to terminate (`Wait(NULL)`).




### 11.5 Web Servers

- **HTTP Protocol:** Web browsers and servers use HTTP, a text-based protocol.

- **Web Transaction:**
    1.  Browser opens a connection and requests content.
    2.  Server responds and closes the connection.
    3.  Browser displays the content.

- **Content Types:** Data has a MIME type (e.g., `text/html`, `image/gif`).

- **Serving Content:**
    - **Static:** Server sends a file from disk.
    - **Dynamic:** Server runs a program and sends its output.

- **URL:** Unique name for content.

- **HTTP Requests:** Include a request line (method, URI, version), headers, and an empty line. The **GET** method is most common.

- **HTTP Responses:** Include a response line, headers, an empty line, and the content.

- **Dynamic Content (CGI):**
    - Arguments passed in the URI.
    - Server creates a child process, sets environment variables, and runs the CGI program.
    - Child process sends output directly to the client.



### 11.6 Putting It Together: The Tiny Web Server

- **Tiny Server:** Example of a simple, iterative Web server.

- **How It Works:**
    - Opens a listening socket.
    - Loops: accepts a connection, handles the request, closes the connection.

- **Serving Static Content:**
    - Finds file type, sends headers, maps file into memory, and sends it to the client.

- **Serving Dynamic Content:**
    - Sends headers, forks a child, sets environment variables, redirects output, and runs the CGI program.
    - Parent waits for child to finish.











## The Client-Server Programming Model

Every network application uses the client-server model.
A server process provides a resource or service, and one or more client process(es) request and use it.


- **Server Role**

    - A server manages some resource (e.g., disk files or spool files).
    - It provides a service for clients by manipulating that resource.
    - *examples:* A Web server manages disk files it retrieves, or an email server manages a spool
      file it reads and updates.



- **Client Role**

    - A client needs service and initiates a transaction by sending a request to the server.



- **The Client-Server Transaction**

    - The fundamental operation is the transaction, which is defined as a sequence of steps carried
      out by the client and server.

    - *Note:* These transactions are not equivalent to database transactions and do not possess
      properties like atomicity.

    - Transaction Steps:

        1. Client sends request (e.g., a Web browser requesting a file)
        2. Server processes request (e.g., the server reads a disk file)
        3. Server sends response 
        4. Client processes response (e.g., a Web browser displays a page)



- **Processes vs. Hosts**

    - It is critical to realize that clients and servers are **processes**, not machines (or hosts).
    - A single host can run many different clients and servers concurrently.
    - A client and server transaction can occur whether the processes are on the same host or different hosts.



## Networks


### Host Perspective

From a programmer's view, a host treats a network as another I/O device.


- **Network as I/O Device:**
  To a computer, a network is like another input/output device for sending and receiving data.


- **Network Adapter:**
  A network adapter (interface) connects to the I/O bus, providing the physical link to the network.


- **Data Transfer:**

  Data moves _between memory and the network_ using a network adapter.

  Data from the network is copied from the adapter to main memory, usually **via DMA (Direct Memory Access)**.
  Data sent to the network is copied from memory to the adapter in a similar way.




### Network Hierarchy (Physical Organization)

Networks are organized hierarchically by geographical proximity.


#### LAN (Local Area Networks)

Connects computers in a small area (like a building).


  - **Ethernet:** One of the most common LAN technology.

  - **Ethernet Segment:** Made of wires and a hub.

      - The hub copies every bit received on one port to all other ports, so **every host sees all bits.**

  - **Ethernet Adapters:** Each has a unique 48-bit address stored in nonvolatile memory like ROM.

  - **Frame:**
    A frame is a chunk of bits sent from one host to another. It includes a header (source, destination,
    length) and a payload (data). _Only the destination host's adapter reads the frame._



  > [!nt] Contradiction? No, it's "Ethernet Frame Processing"
  >
  > - **Hub Level:**
  >     - Hub broadcasts all bits to all connected ports
  >     - Every device receives all physical signals
  > 
  > - **Network Adapter:**
  >     - Network adapters receive all signals but only process frames with matching MAC addresses
  >     - Hardware-level filtering means the computer's CPU only sees frames intended for it
  >     - This selective processing happens automatically in the network interface card (NIC)


  > [!qt] Is "LAN" a conteptual word and an "ethernet" is one of concrete implementation of it?
  >   󱞪 
  > 
  > Yes, that's correct. 
  >
  > LAN (Local Area Network) is a conceptual term describing a network type that connects devices
  > in a limited area, such as a home or office. Ethernet is a specific, concrete implementation of
  > LAN technology, using wired connections (e.g., cables) to enable data transmission, and it's
  > indeed one of the most common ways to build a LAN.
  >
  > Other common LAN technologies besides Ethernet include:
  > 
  > - Wi-Fi (Wireless LAN): Uses radio waves for wireless connections, based on standards like IEEE 802.11 (e.g., Wi-Fi 6).
  > - Token Ring: An older, less common wired technology where devices pass a "token" to transmit data.
  > - ARCNET: Another legacy wired option, often used in industrial settings for its reliability.



##### QnA: Can a host 'A' look into host 'B's frame under same LAN segment?


> [!qt]  네트워크 공부중인데  "Every host adapter sees the frame"가 헷갈려. 그러면 LAN 세그먼트에서는 남의 네트워크도 보려면 볼수있다는거야?
> 
> ```
> Each Ethernet adapter has a globally unique 48-bit address that is stored in
> a nonvolatile memory on the adapter. A host can send a chunk of bits called a
> frame to any other host on the segment. Each frame includes some fixed number
> of header bits that identify the source and destination of the frame and the frame
> length, followed by a payload of data bits. Every host adapter sees the frame, but
> only the destination host actually reads it.
> ```


네트워크 보안 관점에서 중요한 질문이네요. 설명해드리겠습니다:

1. **기본 동작 방식**

   - 이더넷 세그먼트에서는 모든 호스트 어댑터가 물리적으로 프레임을 "볼 수" 있습니다
   - 이는 이더넷의 브로드캐스트 특성 때문입니다


2. **일반적인 상황**

   - 네트워크 카드는 기본적으로 자신의 MAC 주소가 목적지인 프레임만 처리
   - 다른 프레임은 하드웨어 수준에서 무시됨


3. **프로미스큐어스 모드 (Promiscuous Mode)**

   - 특별한 설정을 통해 모든 프레임을 캡처할 수 있음
   - 이것이 바로 Wireshark 같은 패킷 캡처 도구가 작동하는 방식
   - 단, 관리자 권한이 필요함


4. **보안 고려사항**

   - 이런 특성 때문에 중요한 데이터는 반드시 암호화가 필요
   - SSL/TLS, VPN 등의 암호화 프로토콜 사용이 필수적



따라서 기술적으로는 다른 호스트의 트래픽을 "볼 수" 있지만, 

  1. 일반적으로는 네트워크 카드가 자동으로 필터링
  2. 특별한 권한이 필요
  3. 현대의 네트워크는 대부분 암호화되어 있음


##### QnA: Swtich vs Hub


- **허브 (Hub)**

  1. 작동 방식

     - 수신된 데이터를 모든 포트로 단순 브로드캐스트
     - "Every host adapter sees the frame"이 완벽하게 적용되는 장비
     - Layer 1(물리 계층) 장비


  2. 단점

     - 네트워크 대역폭 낭비가 심함
     - 충돌 도메인이 하나로 통합됨
     - 보안에 취약



- **스위치 (Switch)**

  1. 작동 방식

     - MAC 주소 테이블을 유지하면서 학습
     - 목적지 포트로만 데이터 전달
     - Layer 2(데이터링크 계층) 장비


  2. 장점

     - 각 포트별로 충돌 도메인 분리
     - 대역폭 효율성 증가
     - 기본적인 보안 기능 제공


#### Bridged Ethernets

Larger LANs created by connecting Ethernet segments with bridges.


  - Bridges connect Ethernet segments.

  - _Copy frames only when needed_, improving bandwidth usage.

  - Bridges learn which hosts are reachable from which ports automatically.


##### "Bridge" vs "Switch"


- **Bridge**

  1. 기본 특성

     - Layer 2 장비의 초기 형태
     - 두 개의 LAN 세그먼트를 연결
     - MAC 주소를 학습하고 필터링


  2. 주요 기능

     - 충돌 도메인 분리
     - 불필요한 트래픽 필터링
     - 소프트웨어 기반 처리



- **Switch**

  1. 스위치는 "다중 포트 브릿지"

     - 브릿지의 발전된 형태
     - 기본 원리는 동일 (MAC 학습, 필터링)
     - 하드웨어 기반으로 처리 (더 빠름)


  2. **주요 차이점**

     - 포트 수: Bridge(2 ~ 4개) vs Switch(수십 ~ 수백개)
     - 처리 속도: Switch가 훨씬 빠름
     - 가격 효율성: Switch가 더 효율적


  3. 대체 여부

     - 현대 네트워크에서는 대부분 스위치가 브릿지의 역할을 대체
     - 하지만 브릿지의 개념은 여전히 중요 (예: 무선 브릿지)

     즉, 스위치는 브릿지의 발전된 형태로, 대부분의 상황에서 브릿지를 대체했지만,
     완전히 동일한 것은 아닙니다.




#### Internets (Interconnected Networks)

Created by connecting multiple LANs and WANs using routers.


  - **WANs (Wide Area Networks):**
    Cover larger areas than LANs (e.g., high-speed phone connections).

  - Routers have an adapter for each network they connect to.





### Network Protocols

- Protocols are implemented as software mechanisms on each computer and router to handle
  differences between network types.


- Protocols define:
    - **Naming:** Uniform format for addresses or identifications.
    - **Data Delivery:** Routing and reliability like how packages data into chunks called packets (with header and payload).
    - **Communication Processes:** Ensure devices can exchange information effectively.


- Protocols operate at different layers of a network stack, such as the OSI model or TCP/IP stack,
  allowing _modular_ design where each layer handles specific responsibilities.

  > [!nt] Network Layers in the TCP/IP Stack
  >
  > - **Physical Layer:**
  >   Responsible for transmitting raw bits over a physical medium, such as cables or wireless signals.
  >   It defines the electrical, mechanical, and procedural standards for signal transmission.
  >   - *Examples*: Ethernet (IEEE 802.3) for wired connections, Wi-Fi (IEEE 802.11) for wireless.
  >   - *Implementation*: Hardware devices like network interface cards (NICs).
  >
  > - **Network Layer:**
  >   Handles logical addressing, routing, and determining the path for data packets across networks.
  >   It ensures packets reach their correct destination using IP addresses.
  >   - *Examples*: IP (Internet Protocol) for addressing and routing.
  >   - *Implementation*: Routers and operating systems (e.g., Linux IP stack).
  >
  > - **Transport Layer:**
  >   Provides end-to-end communication between devices, including reliability, flow control, and error correction.
  >   It segments data into packets and reassembles them at the destination.
  >   - *Examples*: TCP (Transmission Control Protocol) for reliable delivery, UDP for fast, connectionless transmission.
  >   - *Implementation*: Software libraries (e.g., socket libraries in C or Python).
  >
  > - **Application Layer:**
  >   Defines protocols for user-facing applications, managing data formatting, resource naming, and high-level interactions.
  >   - *Examples*: HTTP for web browsing, DNS for domain name resolution.
  >   - *Implementation*: Web servers (e.g., Apache), client applications (e.g., browsers).
  >
  >
  >   > [!nt]
  >   > Each layer depends on the services provided by the layer below it. Higher-level
  >   > protocols rely on lower-level > layers for data transmission and routing.


#### The Internet Protocol Layer


A protocol layer runs on every host and router to enable communication across different networks.


- **Naming Scheme:**

    - The internet protocol defines a uniform address format (internet address).
        - LANs often use private IP ranges (e.g., 192.168.x.x) or MAC addresses.

    - Each host gets at least one unique internet address.


- **Delivery Mechanism:**

    - Networks encode and package data differently.
    - The internet protocol bundles data into packets.
    - A packet has a header (size, source, destination) and a payload (data).




> [!nt] Overview
>
> - **Transport Layer (TCP/UDP):**
>   Creates segments with source/destination ports, but does not handle MAC addresses. MAC
>   addresses are added at the data link layer.
>
> - **Network Layer (IP-internet protocol):**
>   Bundles data into packets. Each IP packet has a header containing:
>
>     - **Destination information:** The destination IP address (e.g., a public or private IP like
>       192.168.1.1) to route the packet across networks.
>     - **Source in the header:** The source IP address (the sender's unique internet address, as
>       per the naming scheme notes).
>
> - **Data Link Layer (Ethernet):**
>   Encapsulates the IP packet into a frame. The frame header includes source and destination MAC
>   addresses (hardware addresses for local delivery on a LAN).



### Data Transfer Process Example

Example: A client on host A (LAN1) sends data to a server on host B (LAN2) via a router.

1. The client on host A uses a system call to copy data into a kernel buffer.
2. Host A's protocol software creates a LAN1 frame with an internet header (for host B) and a LAN1 frame
   header (for the router). The payload is the internet packet.
3. The LAN1 adapter sends the frame to the network.
4. The router's LAN1 adapter reads the frame and passes it to the router's protocol software.
5. The router uses the packet's internet address and a routing table to find the next hop (LAN2). It removes
   the LAN1 frame header, adds a LAN2 frame header (for host B), and sends the frame to the LAN2 adapter.
6. The LAN2 adapter sends the frame to the network.
7. Host B's adapter reads the frame and passes it to its protocol software.
8. Host B's protocol software removes the packet and frame headers. The data is copied into the server's
   virtual address space via a system call.





---
---

### QnA


> [!qt] Why TCP and IP are paired all the time?
>   󱞪 
> 
> TCP and IP are paired because they work together in the TCP/IP protocol suite (the foundation of the internet):
> 
> - **IP (Internet Protocol):** Handles addressing and routing packets across networks (the
>   "naming" and basic delivery aspect).
> - **TCP (Transmission Control Protocol):** Builds on IP to ensure reliable, ordered, and
>   error-checked data delivery (e.g., retransmitting lost packets).
> 
> They're not always paired—UDP can replace TCP for faster, less reliable transmission—but TCP/IP
> is the standard for most internet applications due to TCP's reliability over IP's basic routing.




> [!qt] LAN frame의 프레임 헤더에는 무슨 정보가 있어?
>   󱞪 
> 
> LAN(이더넷) 프레임 헤더의 주요 구성 요소를 설명해드리겠습니다:
> 
> - 이더넷 프레임 구조
>   ```
>   [Preamble(8bytes)] [Frame Header] [Payload(46-1500bytes)] [FCS(4bytes)]
>   
>   Frame Header 상세:
>   [Destination MAC(6bytes)] [Source MAC(6bytes)] [Type/Length(2bytes)]
>   ```
> 
> - 각 필드 설명
> 
>   1. **Preamble & SFD (8 bytes)**
>   - 7 bytes의 Preamble (10101010 패턴)
>   - 1 byte의 SFD(Start Frame Delimiter, 10101011)
>   - 프레임의 시작을 알리고 동기화
> 
>   2. **Destination MAC Address (6 bytes)**
>   - 목적지 호스트의 MAC 주소
>   - 예: 00:1A:2B:3C:4D:5E
>   - FF:FF:FF:FF:FF:FF는 브로드캐스트
> 
>   3. **Source MAC Address (6 bytes)**
>   - 출발지 호스트의 MAC 주소
>   - 예: 00:1A:2B:3C:4D:5F
> 
>   4. **Type/Length Field (2 bytes)**
>   - 0x0800: IPv4
>   - 0x0806: ARP
>   - 0x86DD: IPv6
>   - 1500 이하의 값: 페이로드 길이를 나타냄
> 
>   5. **FCS (Frame Check Sequence, 4 bytes)**
>   - 프레임 오류 검출을 위한 CRC 값



> [!qt] 프레임의 시작을 알린다는게 무슨말이야? 패킷 스트림 내에서 각 페킷을 구분한다는거야?
>   󱞪 
> 
> 정확합니다! Preamble과 SFD의 목적을 자세히 설명해드리겠습니다:
> 
> - 물리적 신호 전송의 현실
> 
>     1. **동기화 문제**
>     - 네트워크 카드들은 약간씩 다른 클록 속도로 동작
>     - 수신측은 정확히 언제 비트가 시작하는지 알아야 함
> 
>     2. **연속된 비트 스트림의 특성**
>        ```
>        실제 전송되는 비트 스트림 예시:
>        ...idle...10101010 10101010 10101011 [실제 프레임 데이터]...idle...
>                 <-Preamble-> <-SFD->
>        ```
>        - 네트워크는 계속해서 신호가 흐르는 상태
>        - 수신측은 의미 있는 데이터의 시작점을 알아야 함
> 
> - Preamble과 SFD의 역할
> 
>     1. **Preamble (7 bytes)**
>     - 반복되는 '10101010' 패턴
>     - 수신자의 클록을 송신자의 클록과 동기화
>     - 일종의 "주목하세요!" 신호
> 
>     2. **SFD (Start Frame Delimiter, 1 byte)**
>     - '10101011' 패턴
>     - "진짜 데이터가 시작됩니다!" 라고 알림
>     - Preamble과 실제 프레임 데이터를 구분
> 
> - 비유하면
>     - Preamble: "땡땡땡" (주목을 요구하는 종소리)
>     - SFD: "지금부터 공지하겠습니다" 
>     - 그 다음: 실제 메시지 시작
> 
> 이더넷의 초기 버전에서 이런 동기화가 더 중요했는데, 현대 네트워크에서도 여전히 사용되고 있습니다. 




> [!qt] 아래와같은 설명에서 프레임헤더의 무슨 내용이 바뀐다는거야? 
>   󱞪 
> 
> ```
> The router fetches the destination internet address from the internet packet
> header and uses this as an index into a routing table to determine where to
> forward the packet, which in this case is LAN2. The router then strips off the
> old LAN1 frame header, prepends a new LAN2 frame header addressed to
> host B, and passes the resulting frame to the adapter.
> ```
> 
> 
> 라우터가 프레임을 다른 LAN으로 포워딩할 때 바뀌는 프레임 헤더 내용을 설명해드리겠습니다:
> 
> - 변경되는 내용
> 
>     1. **이전 프레임 (LAN1)**
>        ```
>        [Destination MAC: 라우터의 LAN1 MAC주소]
>        [Source MAC: 호스트 A의 MAC주소]
>        [Type/Length]
>        ```
> 
>     2. **새로운 프레임 (LAN2)**
>        ```
>        [Destination MAC: 호스트 B의 MAC주소]  <- 변경
>        [Source MAC: 라우터의 LAN2 MAC주소]    <- 변경
>        [Type/Length]                          <- 동일
>        ```
> 
> - 주요 포인트
> 
>     - MAC 주소는 로컬 네트워크(LAN)에서만 의미가 있음
>     - IP 주소는 변경되지 않음 (IP 패킷 내용은 그대로)
>     - 라우터의 각 인터페이스는 서로 다른 MAC 주소를 가짐
> 
> - 비유하면
> 
>     - IP 패킷: 편지 내용
>     - 프레임 헤더: 편지 봉투
>     - 라우터: 우체국
>       - 우체국이 다음 목적지로 보내기 위해 새 봉투로 갈아끼우는 것과 같음




> [!qt] 라우터가 어떻게 목적지 호스트의 MAC 주소를 알아내는데?
> 
> 그리고 아래 내용이 무슨말인지 이해가 잘 안가.
>    "MAC 주소는 로컬 네트워크(LAN)에서만 의미가 있음"
>    "라우터의 각 인터페이스는 서로 다른 MAC 주소를 가짐"
> 
> 
> 차근차근 설명해드리겠습니다:
> 
> 1. MAC 주소가 로컬에서만 의미있는 이유
> 
>     - MAC 주소는 "물리 주소"로, 직접 연결된 네트워크에서만 통신 가능
>     - 다른 LAN으로 가려면 반드시 라우터를 거쳐야 함
>     - 예시:
>       ```
>       LAN1                    LAN2
>       [호스트A] --> [라우터] --> [호스트B]
>       MAC_A        MAC1/MAC2    MAC_B
>       ```
>     - 호스트A는 호스트B의 MAC 주소를 직접 사용할 수 없음
>     - 대신 라우터의 MAC1 주소로 보내야 함
> 
> 
> 2. 라우터가 MAC 주소 찾는 방법 (ARP)
> 
>     1. **ARP (Address Resolution Protocol) 사용**
>         ```
>         1. 라우터: "IP가 192.168.1.5인 호스트의 MAC 주소가 뭐야?"
>            (ARP Request 브로드캐스트)
>         
>         2. 해당 IP를 가진 호스트: "제 MAC 주소는 00:1A:2B:3C:4D:5E 입니다"
>            (ARP Reply)
>         
>         3. 라우터: ARP 캐시에 이 정보 저장
>         ```
> 
> 3. 라우터의 인터페이스별 MAC 주소
>     - 라우터는 여러 네트워크를 연결하는 장비
>     - 각 네트워크 인터페이스마다 고유한 MAC 주소 필요
>       ```
>       라우터
>       |- LAN1 인터페이스: MAC1 (00:11:22:33:44:55)
>       |- LAN2 인터페이스: MAC2 (00:11:22:33:44:66)
>       ```
>     - 마치 사람이 회사에서는 사원증, 헬스장에서는 회원카드를 다르게 사용하는 것과 비슷
> 
> 4. 전체 통신 과정
> 
>     ```
>     [호스트A] ---> [라우터] ---> [호스트B]
>     
>     1단계: 호스트A -> 라우터
>     프레임헤더: {목적지: 라우터의 MAC1, 출발지: MAC_A}
>     
>     2단계: 라우터 -> 호스트B
>     프레임헤더: {목적지: MAC_B, 출발지: 라우터의 MAC2}
>     ```





> [!qt] 근데 우리는 동일한 아이피를 사용하기도 하는거 아냐? ISP에 인터넷 상품을 사용하고, 거기서 티비도 보고 컴퓨터도 하잖아.
>   󱞪 
> 
> 네, 맞습니다! 여기서 NAT(Network Address Translation)와 사설 IP에 대해 설명드리겠습니다:
> 
> - 가정에서의 일반적인 네트워크 구조
> 
>     ```
>     인터넷 <-> [ISP] <-> [공유기/라우터] <-> [가정내 기기들]
>                          공인IP: 1.2.3.4   사설IP: 192.168.0.x
>     ```
> 
> - 1. IP 주소 종류
> 
>     1. **공인 IP (Public IP)**
>     - ISP가 제공하는 실제 인터넷 주소
>     - 전 세계적으로 유일한 주소
>     - 예: 1.2.3.4
> 
>     2. **사설 IP (Private IP)**
>     - 내부 네트워크에서만 사용
>     - 주로 사용되는 대역:
>       - 192.168.0.0/16
>       - 10.0.0.0/8
>       - 172.16.0.0/12
> 
> - 2. NAT의 동작
> 
>       ```
>       [노트북]         [공유기/라우터]           [인터넷]
>       192.168.0.2 ---> 1.2.3.4          ---> 웹서버
>                        (주소 변환 발생)
>       
>       예시 테이블:
>       내부주소:포트      외부주소:포트
>       192.168.0.2:3333  1.2.3.4:40000
>       192.168.0.3:4444  1.2.3.4:40001
>       ```
> 
> - 3. 실제 가정내 모습
> 
>       ```
>       [공유기/라우터] (1.2.3.4)
>           ├── TV (192.168.0.2)
>           ├── 컴퓨터 (192.168.0.3)
>           ├── 스마트폰 (192.168.0.4)
>           └── 태블릿 (192.168.0.5)
>       ```
> 
> - 장점
> 
>     1. IP 주소 절약
>     2. 내부 네트워크 보안
>     3. 여러 기기 동시 인터넷 사용 가능



## The Global IP Internet


- **Internet Protocols:** The global Internet uses the TCP/IP protocol family.


- **Main Protocols:**

    - **IP:** Handles addressing and basic delivery of data (datagrams).

    - **UDP/TCP:**
      - UDP: Adds process-to-process delivery, but is unreliable.
      - TCP: Provides reliable, two-way connections between processes.


- **IPv4 Properties:**
    - Computers have 32-bit IP addresses.
    - IP addresses map to human-readable domain names.
    - Processes communicate over reliable connections.


### IP Addresses


- Stored as 32-bit numbers (`struct in_addr`).



- _Use big-endian_ (network byte order). Functions like `htonl` and `ntohl` convert between host and network order.

  > [!nt] Byte Order Conversion Functions
  >
  > - `htonl`: Stands for "host to network long."
  >   It converts a 32-bit integer _**from** the host's native byte order **to** network byte order (big-endian)._
  >
  > - `ntohl`: Stands for "network to host long."
  >   It converts a 32-bit integer _**from** network byte order (big-endian) **to** the host's native byte order._



- Shown as dotted-decimal (e.g., 128.2.194.242). Use `inet_pton` and `inet_ntop` for conversions.

  > [!nt]
  >
  > `inet_pton` and `inet_ntop` are C functions from `<arpa/inet.h>` for converting IP addresses
  > between human-readable (presentation) and binary (network) formats. They support both IPv4
  > and IPv6.
  > 
  > - **`inet_pton` (Presentation to Network):**
  >   Converts a dotted-decimal string (e.g., "128.2.194.242") to binary.
  >   Returns 1 on success, 0 on invalid input, -1 on error.
  >     - Example: `struct in_addr addr; inet_pton(AF_INET, "128.2.194.242", &addr);`
  > 
  > - **`inet_ntop` (Network to Presentation):** Converts binary back to string.
  >     - Example: `char str[INET_ADDRSTRLEN]; inet_ntop(AF_INET, &addr, str, sizeof(str));`
  > 
  > These are preferred over older functions like `inet_aton` and `inet_ntoa` (which are IPv4-only and less robust).



### Domain Names


- Human-friendly names (e.g., `example.com`).


- Domain names are structured like a tree hierarchy.

    For example:

    - Top level: .com, .org, .net (managed globally).
    - Second level: example.com (registered by a user or organization).

      > [!nt]
      >
      > It is registered through accredited domain registrars (like GoDaddy or Namecheap).
      > These registrars submit the registration to the TLD registry (e.g., Verisign for .com),
      > which manages the top-level domain.
      >
      > Once registered, the domain owner controls it, including adding subdomains like `www.example.com`.
      > It "leaves" the global registry in the sense that it's delegated to the owner's
      > DNS servers for management.

    - Subdomains: www.example.com or mail.example.com (controlled by the domain owner).




- **DNS (Domain Name System):** 

  A DNS server is a computer (or service) that stores and provides DNS records. It translates
  human-readable domain names (e.g., example.com) into IP addresses (e.g., 192.0.2.1) when you
  request a website.

  There are types like authoritative servers (for specific domains) and recursive resolvers (like
  Google DNS) that handle lookups.


### Internet Connections


- Data is sent over _reliable_ streams.

    > [!nt]
    >
    > The _reliability_ in "data is sent over reliable streams" refers to protocols like TCP
    > (part of TCP/IP), which ensure:
    > 
    > - **Error checking:** Detects and corrects corrupted data.
    > - **Acknowledgment:** Sender waits for receiver confirmation; retransmits if lost.
    > - **Ordering:** Reassembles packets in the correct sequence.
    > - **Flow control:** Prevents overwhelming the receiver.
    > 
    > This contrasts with unreliable protocols like UDP, which don't guarantee delivery. For code, TCP
    > sockets in languages like C or Python provide this reliability.



- **Socket:** Endpoint for communication, defined by address and port.



- **Port:** 16-bit number.

    - Servers use well-known ports (e.g., 80 for HTTP).
    - clients use temporary ports.

      > Clients typically let the OS assign ephemeral ports automatically (e.g., 1024-65535),
      > so no need for careful control unless binding to a specific one.
     


- **Socket Pair:** Connection identified by both endpoints: `(client address:port, server address:port)`.




## The Sockets Interface


- The sockets interface is available on most modern systems, including Unix, Windows, and macOS.

- Also known as "Berkeley sockets" because was developed at UC Berkeley in the early 1980s.


- A **socket** is:

    - An endpoint for communication from the Linux kernel's view.
    - An open file with a descriptor from a Linux program's view.


### Socket Address Structures

- Internet socket addresses use 16-byte `sockaddr_in` structures.


- Important fields in `sockaddr_in`:

    - `sin_family`: Protocol family (e.g., `AF_INET`).
    - `sin_port`: 16-bit port number (network byte order).
    - `sin_addr`: 32-bit IP address (network byte order).


- Both IP address and port must be in **network byte order** (big-endian).


- Functions like `connect`, `bind`, and `accept` require a pointer to a generic `sockaddr` structure,
  so protocol-specific structures (like `sockaddr_in`) must be casted.


- Socket address structures are used to store addressing information needed for network
  communication.


    1. **sockaddr_in (IPv4)**

       ````c
       struct sockaddr_in {
           sa_family_t     sin_family;     // Address family (AF_INET)
           in_port_t       sin_port;       // Port number
           struct in_addr  sin_addr;       // IPv4 address
           char            sin_zero[8];    // Padding to match sockaddr size
       };
       ````


    2. **sockaddr_in6 (IPv6)**

       ````c
       struct sockaddr_in6 {
           sa_family_t     sin6_family;    // Address family (AF_INET6)
           in_port_t       sin6_port;      // Port number
           uint32_t        sin6_flowinfo;  // Flow information
           struct in6_addr sin6_addr;      // IPv6 address
           uint32_t        sin6_scope_id;  // Scope ID
       };
       ````


    3. **sockaddr (Generic)**

       ````c
       struct sockaddr {
           sa_family_t  sa_family;     // Address family
           char         sa_data[14];   // Protocol-specific address
       };
       ````

       - `sockaddr` is a generic structure used as a common type for function parameters
       - When calling socket functions, we typically typecast specific address structures to `struct sockaddr *`


#### Example


````c
int main() {
    int server_sock;
    struct sockaddr_in server_addr;
    
    // Create socket
    server_sock = socket(AF_INET, SOCK_STREAM, 0);
    if (server_sock == -1) {
        perror("Socket creation failed");
        exit(1);
    }
    
    // Initialize server address structure
    memset(&server_addr, 0, sizeof(server_addr));
    server_addr.sin_family = AF_INET;
    server_addr.sin_addr.s_addr = htonl(INADDR_ANY);  // Accept connections on any interface
    server_addr.sin_port = htons(8080);               // Port 8080
    
    // Bind socket to address
    if (bind(server_sock, (struct sockaddr*)&server_addr, sizeof(server_addr)) == -1) {
        perror("Bind failed");
        exit(1);
    }
    
    // Listen for connections
    if (listen(server_sock, 5) == -1) {
        perror("Listen failed");
        exit(1);
    }
    
    printf("Server listening on port 8080...\n");
    
    // Accept connection
    struct sockaddr_in client_addr;
    socklen_t client_len = sizeof(client_addr);
    int client_sock = accept(server_sock, (struct sockaddr*)&client_addr, &client_len);
    
    if (client_sock == -1) {
        perror("Accept failed");
        exit(1);
    }
    
    // Print client information
    char client_ip[INET_ADDRSTRLEN];
    inet_ntop(AF_INET, &client_addr.sin_addr, client_ip, INET_ADDRSTRLEN);
    printf("Client connected from %s:%d\n", client_ip, ntohs(client_addr.sin_port));
    
    close(client_sock);
    close(server_sock);
    return 0;
}
````



Key points in this example:

  1. We create a `sockaddr_in` structure for both server and client
  2. `sin_family` is set to `AF_INET` for IPv4
  3. `sin_addr.s_addr` uses `INADDR_ANY` to bind to all interfaces
  4. `sin_port` is set using `htons()` to convert to network byte order
  5. When calling `bind()` and `accept()`, we cast to `struct sockaddr*`
  6. `inet_ntop()` is used to convert the binary IP address to string format



### The socket Function

- **`socket` Function:**

    - Used by clients and servers to create a socket descriptor.

    - The returned descriptor is _only partially opened and cannot be used for I/O yet._

    - Typical arguments:

        - `AF_INET`: Use 32-bit IP addresses.
        - `SOCK_STREAM`: The socket will be a connection endpoint.


    - It is best to use `getaddrinfo` to generate arguments for protocol independence.


    - ````c
      // Example: Create a TCP socket
      int sockfd = socket(AF_INET, SOCK_STREAM, 0);

      if (sockfd < 0) {
          perror("socket creation failed");
          exit(1);
      }
      ````


### **Client-Side Functions**

- **`connect` Function:**

    - Clients call `connect` to establish a connection to a server's socket address.

    - The function _blocks_ until the connection is made or an error occurs.

    - On success, the socket descriptor (`clientfd`) is _ready for I/O._

    - The connection is identified by a socket pair `(x:y, addr.sin_addr:addr.sin_port)`.

        - It's a 4-tuple: `(local IP:local port, remote IP:remote port)`.
        - Here, `x:y` probably represents the local socket (e.g., IP and port of the client/server),
          and `addr.sin_addr:addr.sin_port` is the remote address (from a `struct sockaddr_in`).
        - This pair ensures each connection is distinct, even if multiple connections use the same ports.


    - ````c
      // Example: Connect to a server (assuming sockfd from above)
      struct sockaddr_in server_addr;
      server_addr.sin_family = AF_INET;
      server_addr.sin_port = htons(8080);
      inet_pton(AF_INET, "127.0.0.1", &server_addr.sin_addr);

      if (connect(sockfd, (struct sockaddr*)&server_addr, sizeof(server_addr)) < 0) {
          perror("connection failed");
          exit(1);
      }

      // Now send/recv data...
      ````


### **Server-Side Functions**


- **`bind` Function:**

    - Asks the kernel to associate the server’s socket address in addr with the socket descriptor.

    - ````c
      // Example: Bind a server socket (assuming sockfd from socket example)
      struct sockaddr_in server_addr;
      server_addr.sin_family = AF_INET;
      server_addr.sin_addr.s_addr = INADDR_ANY;  // Bind to all interfaces
      server_addr.sin_port = htons(8080);

      if (bind(sockfd, (struct sockaddr*)&server_addr, sizeof(server_addr)) < 0) {
          perror("bind failed");
          exit(1);
      }
      ````




- **`listen` Function:**

    - Servers wait for client requests.

    - `listen` turns an active socket into a **listening socket** for connection requests.

        > [!nt]
        >
        > By default, the kernel assumes that a descriptor created by the socket function corresponds to
        > an active socket that will live on the client end of a connection. A server calls the listen
        > function to tell the kernel that the descriptor will be used by a server instead of a client.

    - The `backlog` argument hints at the number of queued connection requests.

    - ````c
      // Example: Listen on the bound socket (after bind)
      if (listen(sockfd, 5) < 0) {  // Backlog of 5
          perror("listen failed");
          exit(1);
      }
      ````





- **`accept` Function:**

    - Servers wait for client connections _on the_ **listening descriptor** (`listenfd`).

    - When a request arrives, `accept` fills in the client’s address and returns a new
      **connected descriptor** (`connfd`).

    - The connected descriptor is used for client-server communication via Unix I/O.

    - The listening descriptor persists for the server's lifetime; the connected descriptor
      is created per client and lasts only for that transaction.

    - ````c
      // Example: Accept a connection (after listen)
      struct sockaddr_in client_addr;
      socklen_t addr_len = sizeof(client_addr);
      int new_sockfd = accept(sockfd, (struct sockaddr*)&client_addr, &addr_len);
      if (new_sockfd < 0) {
          perror("accept failed");
          exit(1);
      }
      // Use new_sockfd for send/recv with the client
      ````


### Host and Service Conversion Functions


- These functions convert between _binary socket addresses_ and _string representations (hostnames, service names)_,
  supporting protocol-independent code.

    > [!nt]
    >
    > - **Hostnames**: Human-readable names for hosts (e.g., "www.example.com" or "localhost"), which
    >   resolve to IP addresses. They make addresses easier to remember than numeric IPs.
    > 
    > - **Service names**: Human-readable names for network services (e.g., "http" for port 80,
    >   "ssh" for port 22). They represent port numbers without needing to know the exact number.





- **`getaddrinfo` (Forward Conversion):**

    - Converts hostnames and services to socket address structures.

    - Returns _a linked list of `addrinfo` structures;_ clients and servers iterate through
      them, trying connections/bindings.

      > [!nt]
      >
      > - **`getaddrinfo` data sources**: It queries system name resolution services to populate the
      >   `addrinfo` linked list. On Linux (and most Unix-like systems), this includes:
      >     - DNS lookups via configured resolvers (e.g., from `/etc/resolv.conf`).
      >     - Local files like `/etc/hosts` for static mappings.
      >     - Other services like NIS or LDAP if configured in `/etc/nsswitch.conf`.
      > 
      > - **Process**: Given a hostname/service and hints (e.g., preferred address family), it resolves
      >   to IP addresses/ports, creating `addrinfo` entries for each valid combination. The list is built
      >   dynamically based on available network interfaces and resolution results.


      > [!nt]
      >
      > - **Default behavior**: When calling `getaddrinfo()` without specifying `ai_socktype` in the
      >   hints (or setting it to 0), it returns up to three `addrinfo` structures for each unique IP
      >   address associated with the host:
      > 
      >     - One with `ai_socktype = SOCK_STREAM` (for TCP connections, e.g., reliable stream sockets).
      >     - One with `ai_socktype = SOCK_DGRAM` (for UDP datagrams, e.g., connectionless packets).
      >     - One with `ai_socktype = SOCK_RAW` (for raw sockets, allowing low-level protocol access).
      > 
      > - **Purpose**: This provides options for different socket types, letting the application choose
      >   (e.g., TCP for reliable data, UDP for speed).
      > 
      > - **Restricting with hints**: If you set `ai_socktype = SOCK_STREAM` in the `addrinfo` hints
      >   struct before calling `getaddrinfo()`, it filters the results to at most one `addrinfo` per
      >   unique address—the one suitable for TCP connections. This simplifies the list for stream-based apps,
      >   avoiding unnecessary UDP/raw entries.
      > 
      > - **Note**: The "not covered" likely means the text focuses on connections (TCP), not datagrams or raw sockets.


    - Accepts optional `hints`, like `AI_PASSIVE` (for servers, sets address to wildcard)
      or `AI_ADDRCONFIG` (recommended for connections).

    - Free memory with `freeaddrinfo`.

    - ````c
      // Example: Resolve "www.example.com" and "http" to addresses
      struct addrinfo hints, *result;
      memset(&hints, 0, sizeof(hints));
      hints.ai_family = AF_INET;  // IPv4
      hints.ai_socktype = SOCK_STREAM;

      int status = getaddrinfo("www.example.com", "http", &hints, &result);
      if (status != 0) {
          fprintf(stderr, "getaddrinfo: %s\n", gai_strerror(status));
          exit(1);
      }
      // Use result->ai_addr for socket operations, then freeaddrinfo(result);
      ````





- **`getnameinfo` (Inverse Conversion):**

    - Converts a socket address structure to host and service name strings.

    - Flags like `NI_NUMERICHOST` force numeric address output instead of domain lookup.

    - ````c
      // Example: Get hostname and service from a sockaddr (e.g., from accept)
      struct sockaddr_in addr;  // Assume populated
      char host[NI_MAXHOST], service[NI_MAXSERV];

      int status = getnameinfo((struct sockaddr*)&addr, sizeof(addr), host, sizeof(host), service, sizeof(service), 0);
      if (status != 0) {
          fprintf(stderr, "getnameinfo: %s\n", gai_strerror(status));
          exit(1);
      }
      printf("Host: %s, Service: %s\n", host, service);
      ````


### Helper Functions for the Sockets Interface


- Two high-level helper functions simplify sockets and `getaddrinfo` usage.



- **`open_clientfd`:**

    - Connects to a server using hostname and port.

    - Returns an open socket descriptor for I/O.

    - Iterates through server addresses from `getaddrinfo` until `socket` and `connect` succeed.

    - ````c
      // Example implementation (simplified)
      int open_clientfd(char *hostname, char *port) {
          struct addrinfo hints, *listp, *p;
          int clientfd;

          memset(&hints, 0, sizeof(struct addrinfo));
          hints.ai_socktype = SOCK_STREAM;
          hints.ai_flags = AI_NUMERICSERV | AI_ADDRCONFIG;
          getaddrinfo(hostname, port, &hints, &listp);

          for (p = listp; p; p = p->ai_next) {
              if ((clientfd = socket(p->ai_family, p->ai_socktype, p->ai_protocol)) < 0)
                  continue;
              if (connect(clientfd, p->ai_addr, p->ai_addrlen) != -1)
                  break;
              close(clientfd);
          }
          freeaddrinfo(listp);
          if (!p) return -1;  // No address worked
          return clientfd;
      }
      // Usage: int fd = open_clientfd("localhost", "8080");
      ````




- **`open_listenfd`:**

    - Returns a listening descriptor for a specified port.

    - Uses `getaddrinfo` with `AI_PASSIVE`.

    - Configures the server with `setsockopt` for immediate restart and accepting connections.

    - ````c
      // Example implementation (simplified)
      int open_listenfd(char *port) {
          struct addrinfo hints, *listp, *p;
          int listenfd, optval = 1;

          memset(&hints, 0, sizeof(struct addrinfo));
          hints.ai_socktype = SOCK_STREAM;
          hints.ai_flags = AI_PASSIVE | AI_ADDRCONFIG | AI_NUMERICSERV;
          getaddrinfo(NULL, port, &hints, &listp);

          for (p = listp; p; p = p->ai_next) {
              if ((listenfd = socket(p->ai_family, p->ai_socktype, p->ai_protocol)) < 0)
                  continue;
              setsockopt(listenfd, SOL_SOCKET, SO_REUSEADDR, (const void*)&optval, sizeof(int));
              if (bind(listenfd, p->ai_addr, p->ai_addrlen) == 0)
                  break;
              close(listenfd);
          }
          freeaddrinfo(listp);
          if (!p) return -1;
          if (listen(listenfd, 1024) < 0) {
              close(listenfd);
              return -1;
          }
          return listenfd;
      }
      // Usage: int fd = open_listenfd("8080");
      ````





### Example Echo Client and Server

#### client

- File Path: Echo/echo_client.c
  ```c
  #include "../csapp.h"
  
  int main(int argc, char** argv) {
      int clientfd;  // host port를 넣어주기 위해
      char *host, *port;
      rio_t rio;
      char buf[MAXLINE];
  
      if (argc != 3)
      {
          printf("input fuck.");
          exit(-1);
      }
      host = argv[1];
      port = argv[2];
      clientfd = Open_clientfd(host, port);
  
      if (clientfd < 0)
      {
          if (clientfd == -1)
          {
              printf("connect fuck.");
              exit(-1);
          }
  
          else if (clientfd == -2)
          {
              printf("something fuck.");
              exit(-1);
          }
  
          else
          {
              printf("somthing really fucked");
              exit(-1);
          }
      }
  
      Rio_readinitb(&rio, clientfd);  // 리오 버퍼 초기화
  
      while (Fgets(buf, MAXLINE, stdin) != NULL)
      {
          Rio_writen(clientfd, buf, strlen(buf));
          Fputs("============================== start of res\n", stdout);
  
          while (Rio_readlineb(&rio, buf, MAXLINE) > 0)
          {
              if (strcmp(buf, END_OF_SERVER_RES "\n") == 0)
                  break;  // 들을만큼 들었으면 이제 "요청"하러 나가기
              Fputs(buf, stdout);
          }
          Fputs("============================== end of res\n\n", stdout);
      }
  
      exit(0);
  }
  ```


#### server


- File Path: Echo/echo_server.c
  ```c
  #include "../csapp.h"
  
  int main(int argc, char** argv) {
      int listenfd, connfd;
      char* port;
      char userbuf[MAXLINE], resBuf[MAXLINE];
  
      rio_t rio;
  
      if (argc != 2)
      {
          printf("input fuck");
          exit(-1);
      }
  
      port = argv[1];
      listenfd = Open_listenfd(port);
  
      if (listenfd < 0)
      {
          printf("socket covert into listen fail.. fuck");
          exit(-1);
      }
  
      while (1)
      {
          struct sockaddr_in client_addr;
          socklen_t client_len;
          char client_hostname[MAXLINE], client_port[MAXLINE];
  
          client_len = sizeof(client_addr);
          // client_addr에 client 주소 정보 담아줘
          connfd = Accept(listenfd, (SA*)&client_addr, &client_len);
          // client_hostname[MAXLINE] client_port[MAXLINE]에다가 최종적으로 담아줘
          Getnameinfo((SA*)&client_addr, client_len, client_hostname, MAXLINE,
                      client_port, MAXLINE, 0);
  
          if (connfd < 0) continue;
  
          Fputs("connected!!\n", stdout);
  
          rio_readinitb(&rio, connfd);
          while (rio_readlineb(&rio, userbuf, MAXBUF) != 0)
          {
              printf("==================== start of req: %s, %s\n",
                     client_hostname, client_port);
              printf("user said: %s", userbuf);
  
              strtok(userbuf, "\n");  // 마지막 줄바꿈 제거
              sprintf(
                  resBuf,
                  "server: I got your message!\nserver: you said \"%s\"\n%s\n",
                  userbuf,
                  END_OF_SERVER_RES);  // Format into buf
              rio_writen(connfd, resBuf, strlen(resBuf));
              printf("==================== end of req: %s, %s\n\n",
                     client_hostname, client_port);
          }
          Close(connfd);
      }
  }
  ```



## Web Servers


Web servers utilize fundamental network programming concepts to provide content, ranging from basic
text and graphics to complex dynamic output. The interaction between Web clients and servers relies
on the text-based application-level protocol known as HTTP.

### 11.5.1 Web Basics

- The core interaction model involves a client (browser) and a server:

    - The browser opens an Internet connection to the server.
    - The browser requests content.
    - The server responds with the content.
    - The server then closes the connection.
    - The browser reads and displays the content.


- Web services are distinguished by their reliance on HTML (hypertext markup language).

    - HTML uses instructions (tags) to tell the browser how to display text and graphical objects
      (e.g., `<b> Make me bold! </b>`).
    - HTML supports hyperlinks (pointers) that reference content stored on any Internet host.


- The World Wide Web was invented by Tim Berners-Lee, who proposed a distributed hypertext system
  in 1989. A pivotal event in its growth was the 1993 release of the graphical browser Mosaic.


### 11.5.2 Web Content


- Content is defined as a sequence of bytes associated with a MIME type.
  (Multipurpose Internet Mail Extensions)


- Examples of common MIME types include:

    - `text/html`: HTML page.
    - `text/plain`: Unformatted text.
    - `image/gif`: Binary image encoded in GIF format.
    - `image/jpeg`: Binary image encoded in JPEG format.


- Web servers provide content in two primary ways:

    - **Static content:** Fetching a disk file and returning its contents.
    - **Dynamic content:** Running an executable file and returning its runtime output.



- Every piece of content is associated with a unique URL (Universal Resource Locator).

    - _Clients use the URL prefix_ (e.g., `http://www.google.com:80`) to determine:
        - The type of server to contact.
        - Where the server is located.
        - The port it is listening on (defaults to port 80 for HTTP).

    - _The server uses the URL suffix_ (e.g., `/index.html`) to:
        - Locate the file on its filesystem.
        - Determine if the request is for static or dynamic content.



- Server Interpretation of the URL suffix:

    - The initial `/` in the suffix denotes the home directory configured for the content, not
      the Linux root directory.

    - The minimal URL suffix, `/`, is expanded by servers to a default home page, such as
      `/index.html`.

### 11.5.3 HTTP Transactions

- An HTTP transaction involves a request sent by the client and a response returned by the server.


- **HTTP Requests**

    - An HTTP request consists of three parts:
        - a request line
        - zero or more request headers
        - an empty text line that terminates the headers

    - _Request Line format:_
        - `method URI version`
        - The `GET` method is the most common workhorse, requesting the content identified by the URI.
        - The version field indicates the HTTP version (e.g., HTTP/1.1).

    - _Request Headers:_
        - Use the format `header-name: header-data`
        - The `Host` header is required in HTTP/1.1 requests; it identifies the origin server
          domain name and is useful for proxy caches.



- **HTTP Responses**

    - An HTTP response consists of
        - a response line
        - zero or more response headers
        - an empty line
        - the response body


    - _Response Line format:_ `version status-code status-message`.

        - `status-code` is a three-digit integer indicating the request disposition.
        - Common status codes and messages:

            - `200 OK`: Request was handled without error.
            - `404 Not found`: Server could not find the requested file.
            - `501 Not implemented`: Server does not support the request method.


    - _Response Headers:_ provide information about the content.

        - `Content-Type` tells the client the MIME type (e.g., `text/html`).
        - `Content-Length` indicates the content size in bytes.


    - _Response body:_ contains the requested content


### 11.5.4 Serving Dynamic Content (CGI)


- Dynamic content serving is managed by the **CGI (Common Gateway Interface)** standard.


- **Passing Arguments from Client to Server:**

    - For `GET` requests, arguments are passed in the URI.
    - Arguments are separated from the filename by `?` and from each other by `&`.



- **Passing Arguments from Server to Child Process:**

    - The server creates a child process using `fork` and then runs the CGI program using `execve`.

    - Before `execve`, the child process sets the CGI environment variable `QUERY_STRING`
      which is containing the arguments.

    - The CGI program accesses these arguments at runtime using the Linux `getenv` function.

    - Other CGI environment variables are also set, such as `SERVER_PORT` and `REQUEST_METHOD`.



- **Child Process Output to Client:**

    - A CGI program sends its dynamic content to standard output.

    - Before the CGI program runs, the child process uses `dup2` to redirect standard output to
      the connected descriptor (`connfd`) associated with the client.

        - Consequently, everything the CGI program writes to standard output goes directly to the
          client _without parent intervention._

    - The CGI program is responsible for generating the necessary response headers, specifically
      `Content-type` and `Content-length`, and the empty line that terminates the headers.




