
> [!nt]
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



