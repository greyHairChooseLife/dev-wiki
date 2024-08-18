## NoSQL이란?

    ### 등장 배경

        #### RDB의 단점

            1. (비지니스 고도화에 따른)유연한 확장성 부족
                - column을 추가하려면 schema를 변경해야 한다.
                - 대량의 데이터가 쌓여있다면, downtime이 발생할 수 있다.
            2. 중복 제거 및 정규화 => 복잡한 join => 성능 저하
            3. scale up(replica)은 그렇다 쳐도, scale out(multi-master, sharding)은 까다롭다.
            4. transaction ACID 특성을 보장하기 위해, 복잡한 구조(높은 리소스 소비)를 가져야 한다.

        #### 인터넷 발달과 트래픽 증가

            1. **대용량 데이터**를 **빠르게 처리**할 수 있는 방법이 필요
            2. 짧은 간격의 (소비자 분석 <-> 서비스 개선) 패턴 => **비정형 데이터** 증가


    ### 특징

        1. flexible schema
            - 애초에 column을 정의하지 않아도 된다.
            - 데이터를 write, read 할 때 column을 지정 또는 생성한다.

              ex) mongDB의 경우 JSON 형태로 넣어서, schema구조가 2단계 3단계 깊어질 수 있다.

            => schema 관리를 DB가 아니라 application 레벨에서 관리한다. (개발자 단계에서 부담 증가)

        2. 중복 허용(join 회피)
            - 중복된 데이터 만큼 저장 공간이 늘어난다.
            - 중복된 데이터를 모두 최신 상태로 유지하는 관리를 application 레벨에서 해야 한다.

        3. scale-out이 용이
            - 애초에 중복을 허용하는 컨셉이니 scale-out 되어도 여기저기 클러스터에서 데이터를 모아올 필요가
              없다.
            - 보통 서버 여러대로 하나의 클러스터를 구성해서 사용한다.

        4. ACID의 일부를 포기하고 high-throughput, low-latency를 추구한다.
            - 금융 시스템처럼 consistency(정확도, 일관성)가 매우 중요한 서비스가 아니라면 고려할 만 하다.


## 별책: Redis

    ### 특징

        - in-memory
        - key-value database
        - 다양한 data type
          ex) string, list, set, sorted set, hash ...
        - hash-based sharded cluster
        - High Availability를 위한 replication, automatic failover 기능 지원

    ### 용도 예시

        - 빠르니까, 본격적인 DB 서버 앞단에서 cache 서버로 활용한다.
