## Public IP는 완전한 정보다

Public IP = 최종 목적지까지의 완전한 주소

``` example
3.34.123.45 매핑:
  → Region: ap-northeast-2
  → IGW: igw-xxxxx
  → VPC: vpc-yyyyy
  → Private IP: 10.0.11.10
  → ENI: eni-zzzzz
  → Resource: NLB
```

## 계층별 라우팅

### 전체 경로

┌─────────────────────────────────────┐
│ **Layer 1: Global (Edge Location)**     │
│ 책임: 리전 찾기                     │
│ 입력: Public IP                     │
│ 출력: 리전                          │
└──────────────┬──────────────────────┘
               ↓
┌──────────────┴──────────────────────┐
│ **Layer 2: Regional Router**            │
│ 책임: IGW 찾기                      │
│ 입력: Public IP                     │
│ 출력: IGW ID                        │
└──────────────┬──────────────────────┘
               ↓
┌──────────────┴──────────────────────┐
│ **Layer 3: IGW**                        │
│ 책임: NAT, VPC 진입                 │
│ 입력: Public IP                     │
│ 출력: Private IP + VPC ID           │
└──────────────┬──────────────────────┘
               ↓
┌──────────────┴──────────────────────┐
│ **Layer 4: VPC Router**                 │
│ 책임: 서브넷 찾기                   │
│ 입력: Private IP                    │
│ 출력: Subnet ID                     │
└──────────────┬──────────────────────┘
               ↓
┌──────────────┴──────────────────────┐
│ **Layer 5: Subnet Routing**             │
│ 책임: ENI 찾기                      │
│ 입력: Private IP                    │
│ 출력: ENI ID                        │
└──────────────┬──────────────────────┘
               ↓
┌──────────────┴──────────────────────┐
│ **Layer 6: ENI**                        │
│ 책임: 최종 전달                     │
│ 입력: 패킷                          │
│ 출력: 리소스로 전달                 │
└─────────────────────────────────────┘

#### e.g.

```
클라이언트 (인터넷)

  ↓
[Layer 1] AWS Edge Location
  - "3.34.123.45는 ap-northeast-2"

  ↓
[Layer 2] AWS 리전 라우터 (Seoul)
  - "3.34.123.45는 igw-xxxxx"

  ↓
[Layer 3] IGW (igw-xxxxx)
  - NAT: 3.34.123.45 → 10.0.11.10
  - "vpc-yyyyy로 전달"

  ↓
[Layer 4] VPC 라우터
  - "10.0.11.10은 어느 서브넷?"
  - Subnet 매핑 테이블 조회
  - "Subnet-B (10.0.11.0/24)"

  ↓
[Layer 5] 서브넷 라우팅
  - "10.0.11.10은 어느 ENI?"
  - ENI 매핑 테이블 조회
  - "eni-zzzzz"

  ↓
[Layer 6] ENI (Elastic Network Interface)
  - 최종 도착: NLB
```


### 각 계층의 라우팅 테이블

- **Layer 1: AWS Edge Location**

    역할: 리전 선택

    ```
    Global Routing Table:

      3.34.123.45    → ap-northeast-2
      52.78.100.1    → ap-northeast-2
      13.124.50.1    → ap-northeast-2
      54.180.1.1     → ap-northeast-1 (Tokyo)
    ```


- **Layer 2: 리전 라우터**

    역할: IGW 선택

    ```
    Seoul Region Routing Table:

      3.34.123.45    → igw-abc123
      52.78.100.1    → igw-abc123
      13.124.50.1    → igw-def456
    ```


- **Layer 3: IGW**

    역할: NAT + VPC 진입

    ```
    IGW (igw-abc123) NAT Table:

      Public IP        Private IP      VPC
      3.34.123.45  →  10.0.11.10      vpc-yyyyy
      52.78.100.1  →  10.0.1.20       vpc-yyyyy
    ```


- **Layer 4: VPC 라우터**

    역할: 서브넷 선택

    ```
    VPC (vpc-yyyyy) Subnet Table:

      10.0.1.0/24    → Subnet-A (subnet-111)
      10.0.2.0/24    → Subnet-B (subnet-222)
      10.0.11.0/24   → Subnet-C (subnet-333)


    10.0.11.10 → 10.0.11.0/24 → Subnet-C
    ```


- **Layer 5: 서브넷 내 라우팅**

    역할: ENI 선택

    ```
    Subnet-C ENI Table:
      10.0.11.10    → eni-zzzzz (NLB)
      10.0.11.20    → eni-aaaaa (EC2-1)
      10.0.11.30    → eni-bbbbb (EC2-2)
    ```


- **Layer 6: ENI**

    패킷 도착!

    ```
    ENI (eni-zzzzz):
      - Type: network_load_balancer
      - Resource: NLB
      - Security Group: sg-xxxxx
    ```




## 리소스 예시

- **EC2 직접 연결**

    - e.g. Public IP: 52.78.100.1 → EC2

        ```
        Layer 1: Edge Location → Seoul
        Layer 2: (Seoul)Rigion Router → igw-abc123
        Layer 3: IGW → NAT (52.78.100.1 → 10.0.1.20)
        Layer 4: VPC Router → Subnet-A
        Layer 5: Subnet Router → eni-ccccc
        Layer 6: ENI → EC2 인스턴스
        ```



- **RDS (Private만 가능)**

    - e.g. (같은 VPC 내) EC2 → RDS: 10.0.3.50 (Private IP)
        - RDS는 Public IP 없음 (보통)
        - 외부에서 직접 접근 불가!

        ```
        Layer 4: VPC Router    - "10.0.3.50은 Subnet-D"
        Layer 5: Subnet Router - "10.0.3.50은 eni-ddddd"
        Layer 6: ENI → RDS
        ```



- **NLB (NLB는 또 다른 라우터!)**

    - e.g. NLB 도착 후:

    ```
    [Layer 7] NLB 내부
      - Target Group 확인
      - Health Check 상태 확인
      - 로드밸런싱 알고리즘
      - Target 선택

      ↓
    [Layer 8] Target (Backend)
      - LiveKit-1: 10.0.12.10
      - LiveKit-2: 10.0.12.20
      - 하나 선택

      ↓
    다시 VPC 라우팅!
      - "10.0.12.10은 Subnet-E"
      - "eni-eeeee"

      ↓
    LiveKit 서버 도착
    ```
