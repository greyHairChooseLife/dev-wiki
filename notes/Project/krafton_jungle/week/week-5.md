# week-5: C언어, 자료구조, 알고리즘



> [!NOTE]
>
> 팀원: 김휘서 + 남현서
>
> - [x] 코어시간 계획하기
>   - 19:00 ~


## 학습 계획

### CSAPP 6장

- 발표 담당 파트

  - 김상연
    6.1 Storage Technologies (쉬움, 직관적)
    6.5 Writing Cache-Friendly Code (중간, 코드 예제 위주)

  - 남현서
    6.2 Locality (쉬움~중간, 직관적)
    6.6 Putting It Together (Memory Mountain) (어려움, 실험 해석)

  - 김휘서
    6.3 The Memory Hierarchy (중간, 큰 그림)
    6.4 Cache Memories (가장 빡셈, 세부 구조)

---

- 6장 Memory Hierarchy 핵심 키워드

  **6.1 Storage Technologies**
    - SRAM (Static RAM): 빠름 :번쩍:, 비쌈 :돈가방:, 주로 캐시에 사용
    - DRAM (Dynamic RAM): 느림 :거북이:, 쌈 :달러:, 메인 메모리에 사용
    - Disk Storage (HDD): 큰 용량, 기계적 움직임 → 매우 느림
    - SSD (Solid State Disk): 플래시 기반, HDD보다 훨씬 빠름
    - Storage Technology Trends: 메모리 밀도 증가, 속도 격차 여전

  **6.2 Locality (국소성)**
    - Temporal Locality: 최근 사용한 데이터/명령어를 곧 다시 사용
    - Spatial Locality: 가까운 메모리 주소를 함께 접근하는 경향
    - 프로그램 성능 = locality 활용 여부

  **6.3 The Memory Hierarchy**
    - 레지스터 → L1 캐시 → L2 캐시 → L3 캐시 → DRAM → SSD/HDD
    - Principle of Locality 덕분에 계층 구조 가능
    - Caching: 작은, 빠른 저장장치로 큰, 느린 저장장치의 성능 개선

  **6.4 Cache Memories**
    - Cache line (block): 캐시에 저장되는 기본 단위
    - Direct-Mapped Cache: 한 주소 → 한 캐시 위치 (충돌 많음)
    - Set-Associative Cache: 여러 자리 중 하나에 저장 가능 (균형)
    - Fully Associative Cache: 어디든 저장 가능 (비용↑)
    - Replacement Policy: LRU (Least Recently Used) 등
    - Write Policy: Write-through, Write-back
    
  **6.5 Writing Cache-Friendly Code**
    - Stride-1 Access: 메모리를 순차적으로 접근 → 캐시 효율 ↑
    - Loop Interchange: 행렬 접근 순서 최적화
    - Blocking (tiling): 데이터 조각 단위로 캐시에 맞춰 처리

  **6.6 Putting It Together**
    - Memory Mountain: 메모리 성능 시각화 (locality 영향 확인)
    - 캐시 친화적 프로그래밍 = 성능 최적화의 핵심


### 과제

- 일단 각자 환경 구성 및 과제 시도해보 다시 논의

[참고 콤파스](https://jungle-compass.krafton.com/course/view.php?id=146)



## (팀)코어타임 기록

### 1차


CSAPP 관련해서 내가 첫번째로 발표를 하게됐다. 공부할 때 좀 어려웠는데, 그래도 마치고 나서 너무
도움됐다고 말씀들을 해 주셔서 아주 뿌듯했다.




### 2차

2025-10-11 (토) 19:00 ~ 22:00

재귀 함수 및 더블포인터로 다른 스택프레임의 구조체를 변조하는 것이 도저히 혼자서는 이해가 안됐다.
24시간 이상 그림 그리고 고민해보고 그런것같다. 거의 다 온것 같았는데.. 결국은 먼저 문제를 푸셨다는
휘서님한테 도움을 청했다. 이미 많이 고민해봤는데도 모르는거보면 이해시키기 만만찮을거래두 흔쾌히
나서주셨다.

역시 쉽지 않았다. 많은 경험으로 직관적인 사용을 해왔더라도 모르는 사람을 알아듣게 설명하는건 참
어려운 일이다. 거의 2.5시간을 토론했다.

메모리 영역을 직접 그림으로 표현해서 결국은 이해를 해냈다. main함수의 실행부터 recursion을
실행하고 제어권이 회수될 때 Heap, Stack 영역에 무엇이 할당되고 수정되는지 하나하나 그려가며 해냈다.

내가 놓쳤던 것은 매개변수도 변수라 스택프레임에 메모리를 한자리 차지한다는 것이다. 생각해보면
나는 인자로 넘겨받은 것은 그냥 함수 시작할 때 기본으로 주어지는 값 같은걸로 여겨온 것 같다.
디버거를 통해 disassemble이나 스택프레임 변화를 보면 엄연히 스택 영역에 한층 쌓이는 것인데.


나 혼자서도 많이 그려봤었는데 이걸 놓쳤던게 문제같다. 바로 이 부분을 휘서님과의 토론에서 발견하게
됐다. 아니었으면 지금까지도 헤맸을 것 같다. 너무 감사했는데, 도리어 이 기회에 자신도 명확하게
알게된것같아 너무 좋았다고 말씀도 해 주셔서 다행이고 또 뿌듯하기도 했다.
