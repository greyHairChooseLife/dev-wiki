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
    - 8백만개 고유주소를 가지며 주소마다 8비트씩 입출력 가능하다는 뜻
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
   >    - CAS 요청을 통해 행 버퍼에서 정확한 데이터를 찾아냄
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























### 디스크 저장소 (Disk Storage)



### SSD (Solid State Disks)






## Locality





## The Memory Hierarchy





## Cache Memories





## Writing Cache-Friendly Code





## Putting It Together: The Impact of Caches on Program Performance





