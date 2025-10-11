# Chapter.6 메모리 계층구조


[notebookLM](https://notebooklm.google.com/notebook/f405095d-572f-4992-bf85-e8781645f33a)




- **이 챕터의 목표:**

  메모리 계층(Memory Hierarchy)이 프로그램 성능에 미치는 영향을 이해하고,
  이를 활용하여 더 빠른 프로그램을 작성하는 방법을 배우는 것이다.
  

    - 서로 다른 저장 기술(SRAM, DRAM, 디스크)의 특성과 성능 차이를 이해
    - **지역성(locality)** 의 원리를 파악하고 캐시 친화적인 코드를 작성
    - 메모리 계층의 성능을 시각적으로 분석하는 Memory Mountain과 같은 개념을 통해
      프로그램의 성능을 예측하고 최적화





- **목차**

  1. Storage Technologies
  2. Locality
  3. The Memory Hierarchy
  4. Cache Memories
  5. Writing Cache-Friendly Code
  6. Putting It Together: The Impact of Caches on Program Performance



## Storage Technologies


_각 기술의 물리적 한계와 발전 속도의 차이가 어떻게 메모리 계층 구조라는 필연적인 설계로 이어졌는가_


### RAM (Random Access Memory)


> [!nt]
> capacitor: 전하를 저장
> transistor: 전류의 저항을 조절, 스위치 역할


#### SRAM(정적 RAM)

각 비트를 6개의 transistor로 구현한 회로에 저장

- 활용
  - CPU 레지스터
  - 캐시 메모리(L1, L2, L3)

- 장점
  - 전원이 공급되는 한 데이터를 유지
  - 외부 교란에 강함
    - 전기적 노이즈: 전원 공급의 불안정, 주변 회로에서 발생하는 전자기파,
      외부에서 정전기, 우주에서 오는 고에너지 입자(코스믹 레이)
  - 속도가 빠름

- 단점
  - 구조가 복잡해 밀도가 낮음
  - 비쌈
  - 전력 소모가 큼



#### DRAM(동적 RAM)

각 비트를 하나의 capacitor와 transistor로 구현하여 저장

- 활용
  - 메인 메모리

- 장점
  - 구조가 단순해 밀도가 높음
  - 값이 싸다

- 단점
  - SRAM보다 느림
  - 전하가 빨리 유실되므로 주기적인 재충전이 필요


##### DRAM의 구조: "memory module"

- **관련 개념의 집합 관계** 
  - memory module = _set of_ memory chips
  - memory chips  = _set of_ super cells
  - super cells   = _set of_ cells


- **memory chip:** 수 많은 cell(capaciter+transitor)로 구성된 DRAM chip을 뜻함

  - cell: 1bit를 저장하는 단위
    - row를 표현하는 word line. RAS에 반응
    - column을 표현하는 bit line. CAS에 반응
    - 두 lines가 교차하는 부분이 cell

  - ex) 8M x 8 규격의 DRAM chip이란,
    - 8백만개 고유주소(슈퍼셀 1개마다 1개 주소)를 가지며 주소마다 8비트씩 입출력 가능하다는 뜻
    - 이때의 슈퍼셀(supercell)은 이 8bit 묶음이 됨
    - 즉, 8백만개의 슈퍼셀이 있는 셈


- **memory module:** 수 많은 memory chip의 묶음

  - memory chip 하나로는 bus에 담아줄 word 크기를 못채운다.
    - chip 하나에 슈퍼셀 하나씩 입출력 하니까
  - 그래서 RAS/CAS를 통해 하나의 주소를 8개의 DRAM 칩에 병렬적으로 보낸다.
  - 그러면 각각의 칩에서 1개씩의 슈퍼셀(8bit)을 읽어낸다.
  - 메모리 모듈의 데이터버스 및 조합 회로에서 word 크기로 합쳐준다.
    - 이때 조합하는 순서는 리틀 엔디안 또는 빅 엔디안 방식에 따른다.
  - 이 최종 결과를 memory bus에 담아 보낸다.



##### Memory Controller (I/O bridge에 포함)

_CPU와 DRAM 사이의 데이터 흐름을 관리하는 하드웨어_


> [!nt] 트랜잭션(transaction)
> 버스를 통해 데이터를 주고받는 하나의 완전한 과정. read/write 두 가지


> [!nt] DRAM의 인덱싱: supercell의 주소
> - RAS:    Row Access Strobe. 느림(상대적으로)
> - CAS: Column Access Strobe. (상대적으로)빠름
>
>
> > [!nt] "strobe"란?
> >
> > - 전자공학에서 **순간적으로 신호를 보내어 어떤 동작을 트리거(trigger)하는 제어 신호**


> [!nt] control wires(제어 와이어): system bus의 일부로, 오가는 정보의 종류와 흐름을 통제하는 신호
> - transaction 종류 식별: read / write 식별
> - 정보 유형 구분: 버스에 실린 데이터가 '주소'인지 '데이터'인지 식별
> - 대상 장치 지정: 대상지(목적지)가 주기억장치인지 기타 I/O 장치인지 식별



###### Read Transaction

_1. CPU  ---주소 전송--> DRAM_
_2. CPU <--데이터전송--  DRAM_


ex)`movq A-addr, %rax` 명령을 수행하는 과정

   > 1. CPU(내부의 bus interface)가 A주소를 system bus에 싣는다.
   > 2. Memory Controller(I/O bridge 일부)가 받고 control wires를 통해 read/write 여부를 구분 
   > 3. A주소를 RAS/CAS 요청으로 변환, memory bus로 DRAM에 전달
   >    - RAS 먼저 요청해서 전체 행 값(수 KB)을 DRAM 칩 내부의 행 버퍼(internal row buffer)로 복사
   >    - CAS 요청을 통해 행 버퍼에서 정확한 데이터(슈퍼셀 하나)를 찾아냄
   > 4. DRAM이 찾아낸 데이터를 memory bus에 싣는다. 
   > 5. Memory Controller가 이것을 받아서 system bus로 보낸다.
   > 6. CPU bus interface가 읽음



> [!nt] 메모리 칩의 동작 및 RAS와 '지역성'
>
> - chip 내에 하나의 행은 엄청나게 많은 supercell을 가지고 있다.
> - 예를들어, 8M x 8bit 메모리칩은 2048 row * 4096 column으로 구성될 수 있는데
>   이러면 RAS로 1행을 읽을 때 4096개의 슈퍼셀, 즉 4096 Byte == 4KB을 한번에 읽는다.
> - **따라서, 만약 동일한 행에 있는 값을 연속적으로 읽는다면 추가적인 RAS 요청은 생략할 수
>   있다.** **(공간적 지역성이 활용되는 예시)**
>
> - FPM(Fast Page Mode)기술이 바로 이렇게 RAS 요청을 생략하는것을 가능케 한다.
>   이 기술이 없을 땐 RAS로 읽어와 행 버퍼에 저장해둔 값을 날려버렸고,
>   이 기술은 그것을 유지해주는 기술이다.




###### Write Transaction
 
_1. CPU --주소 전송---> DRAM_
_2. CPU --데이터전송--> DRAM_

ex)`moveq %rax, A-addr` 명령을 수행하는 과정

   > 1. CPU(내부의 bus interface)가 A주소를 system bus에 싣는다.
   > 2. Memory Controller(I/O bridge 일부)가 받고 control wires를 통해 read/write 여부를 구분 
   > 3. A주소를 RAS/CAS 요청으로 변환, memory bus로 DRAM에 전달
   >    - RAS 먼저 요청해서 전체 행 값을 DRAM 칩 내부의 행 버퍼(internal row buffer)로 복사
   > 4. 이어서 system bus로부터 받게되는 데이터와 함께 CAS를 전달
   > 5. DRAM은 지정된 column에 데이터를 '쓰기'한다.



### 비휘발성 메모리 (Nonvolatile Memory)


- 전원 공급이 중단된 상태에서도 정보를 유지하는 저장장치


#### ROM (Read-Only Memory)


- 역사적 맥락에서 ROM(Read-Only Memories)로 통칭 
- 일부 유형은 쓰기(Write)도 가능

  > - PROM(Programmable ROM): 1회 쓰기 가능
  > 
  > - EPROM(Erasable Programmable ROM): 1,000회 정도 지우고 쓰기 가능
  > 
  > - EEPROM(Electrically Erasable PROM): 
  >   물리적으로 분리된 프로그래밍 장치가 필요 없고 **인쇄 회로 기판 위 제자리에서**
  >   약 100,000회 쓰기 가능



#### 플래시 메모리 (Flash Memory)


- EEPROM 기반으로 발전
- 내구성 좋음
- 전력 소모 낮음


#### 펌웨어(Firmware)의 역할

- ROM 장치에 저장된 **프로그램**
- 컴퓨터 시스템이 작동하는 데 필수적인 저수준 기능
  1. 시스템 부팅
  2. I/O 기능
  3. 장치 제어



### 회전 디스크

- 주기억장치(DRAM)보다 느림(Read/수천 배)

- 구조: 플래터, 트랙, 섹터, 실린더

  [참고영상1](https://www.youtube.com/watch?v=7n71z7KIoXo)
  [참고영상2](https://www.youtube.com/shorts/KH9mZB5nR7s)


  - 플래터(platter)라는 양면이, 자기기록 물질로 코팅된 1장 이상의 원형 판으로 구성
    - 각 플래터 표면은 트랙(tracks)으로 구성, 트랙은 섹터(sectors)로 분할
    - 각 섹터는 일반적으로 512 Byte
    - 섹터 단위로 데이터를 Read/Write
    - 섹터의 칸칸에 자기장을 활용해 1과 0을 구분하는 방식
  - 각 플레터에는 데이터를 Read/Write하는 헤드가 있음
  - 모든 헤드는 동일한 실린더에 위치(제각각이 아님)

  - 실린더(cylinder):
    - 실린더는 스핀들(spindle) 중앙으로부터 동일한 거리에 있는 모든 표면의 (수직)트랙 컬렉션을 뜻함

  - 블록(Block): OS의 논리적 추상화 단위
    - 다중 플래터 트랙 섹터 등등 다 필요 없고 그냥 '블록의 연속'이라고 추상화하여 사용
    - 블록 크기는 섹터 크기와 같음
    - 디스크 컨트롤러가 **메모리 맵 I/O 메커니즘**을 통해 추상화된 블록을 통한 CPU 명령을 실제
      디스크에 적용할수 있도록 해석
  


#### 디스크 접근 시간 (Seek + Rotational Latency + Transfer)

- 탐색시간
  - 대상 섹터가 포함된 트랙으로 헤드가 움직이는 시간
  - 현대엔 일반적으로 3ms에서 9ms 정도

- 회전 지연시간
  - 정확한 섹터 위로 헤드가 위치할 때까지 플래터를 돌리는 시간
  - 회전 속도에 따라 다르고 또 시작 위치와 대상 섹터의 거리에 따라 다름
  - 이것도 수ms 정도

- 전송시간
  - 탐색 및 회전을 완료한 시점부터 시간을 재는데, 거의 의미없다.


#### 논리적 블록 (Logical Disk Blocks) 및 디스크 컨트롤러


- 디스크 컨트롤러(Disk Controller):

  - 디스크 패키지 내부에 있는 작은 하드웨어/펌웨어 장치
  - 논리적 블록 번호와 실제 물리적 섹터 주소 (표면, 트랙, 섹터) 간의 매핑을 유지 관리



- DMA(Direct Memory Access)

  - **CPU의 개입 없이** 데이터를 주기억장치로 직접 Read/Write
  - **디스크 I/O 속도가 매우 느리기 때문에** CPU가 대기하는 비효율을 막기 위한 메커니즘

  - **읽기:** CPU가 디스크의 데이터를 주 메모리로 Load

    > 1. CPU의 요청 시작:
    >    - "읽어라"
    >    - "이 부분"(논리적 블록 번호 I/O port)
    >    - "여기에 복사"(데이터가 저장될 주 메모리 주소)
    >
    > 2. CPU 독립 작업:
    >    요청 후, 디스크 I/O 작업이 완료되는 긴 시간 동안 CPU는 다른 작업을 수행
    >
    > 3. DMA 전송:
    >    - 디스크 컨트롤러는 논리적 블록 번호를 실제 디스크 섹터 주소로 변환하여 데이터를 읽기
    >    - 읽은 데이터를 주 메모리로 직접 전송(DMA 전송)
    >
    > 4. CPU 알림:
    >    DMA 전송이 완료되면, 디스크 컨트롤러는 **인터럽트 신호**를 CPU에 보내 완료 통지


  - **쓰기:** CPU가 주 기억장치 메모리 내용을 디스크 주소 A에 저장

    > 1. CPU의 요청 시작:
    >    - "써라"(CPU는 디스크 컨트롤러에 명령)
    >    - "이 부분에"(논리적 블록 번호 I/O port)
    >    - "여기서 복사 해 와서"(데이터가 저장되어있는 주 메모리 주소)
    >
    > 2. CPU 독립 작업:
    >    요청 후, 디스크 I/O 작업이 완료되는 긴 시간 동안 CPU는 다른 작업을 수행
    >
    > 3. DMA 전송 및 저장:
    >    - 디스크 컨트롤러는 CPU 개입 없이 메모리 버스를 통해 주 메모리에서 데이터를 가져와서
    >      디스크에 저장








### SSD (Solid State Disks)


- 플래시 메모리 기반

  - 플래시 메모리 칩 + '디스크 컨트롤러'와 같은 역할을 하는 플래시 변환 계층

    > [!nt] 플래시 변환 계층(Flash translation layer)
    >
    > - OS의 파일 시스템 요청(Read/Write/Delete)을 플래시 메모리의 특성에 맞게 변환해주는
    >   소프트웨어 계층
    > - 플래시 메모리는 쓰기(W)전에 반드시 지워야 함
    > - 논리적 주소를 실제 물리적 주소로 바꿔주는 역할 등


- 표준 디스크 슬롯(일반적으로 USB 또는 SATA)에 연결



#### 기존의 회전 디스크와 비교

- SSD는 움직이는 부품이 없다.
  - 빠름
  - 어느 주소에 접근하든 접근 시간이 동일함
  - 전력소모 적음
  - 견고함

- 하지만,
  - 30배 비싸고
  - 반복적 쓰기 작업 후 마모되는 잠재적 문제가 있음
    - 마모된 부분은 Read/Write가 안될 수 있음. Bad Block이라고 부름






#### 구조적 특징 및 쓰기 문제


- 플래시는 블록(Block)의 연속으로 추상화
- 각 블록(Block)은 여러 페이지(Page)를 포함
- 이때의 Page단위로 Read/Write
- 용량
  - 512 Byte ~ 4KB / Page
  - 32 ~ 128 Pages / Block



- **독특한 쓰기 메커니즘:**

  - 페이지에 데이터를 쓰기 전에 해당 페이지가 속한 전체 블록을 먼저 지워야 함
    - 일반적으로 모든 비트를 1로 설정
    - 지운 후에 블록 내의 각 페이지를 추가적인 지우기 없이 한 번씩 쓰기 가능

  - 읽기보다 느림
    1. 블록을 지우는데 (Read/Write 대비)상대적으로 긴 시간이 소요
    2. 기존 데이터라도 있으면 다른데 옮겨야 함



- 내구성 및 마모
  - 플래시 메모리 특성상 블록은 반복적 쓰기로 인해 마모됨
  - 수명은 100,000회 쓰기 정도
  - Wear-leveling(마모 평준화) 기능으로 쓰기 작업이 모든 블록에 분산될 수 있도록 함





## Locality

- 지역성: 메모리의 시간적/공간적으로 인접한 영역을 더 많이 접근하는 경향

- 지역성의 활용 => Cache


> [!td]
>
> - [ ] 지역성이 어디에, 어떻게 적용되었나?

- 지역성이 어디에 어떻게 적용되었나?
  - 캐시 메모리
  - 페이징 메커니즘
    - 추가적인 페이지를 가져오기 위해 뭔가 쫓아낸다면 무엇을 쫓아내야 효과적일까?
      ==> 지역성을 고려한 알고리즘으로 선택된다.
  - 디스크 I/O
  - 파일 시스템
  



## The Memory Hierarchy





## Cache Memories





## Writing Cache-Friendly Code





## Putting It Together: The Impact of Caches on Program Performance





