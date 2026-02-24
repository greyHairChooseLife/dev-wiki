# 나만무

<details><summary><h2>기여</h2></summary>

- [개발 환경 세팅](기여/개발_환경_세팅.md)
- [개발 서버 세팅](기여/개발_서버_세팅.md)
- [Continuous Integration: AI 리뷰](기여/Continuous_Integration:_AI_리뷰.md)

- 재현가능성 확보

    > [!NOTE]
    >
    > - https://github.com/Namanmoo-Damso/ops-infra/pull/3
    > - https://github.com/Namanmoo-Damso/ops-api/pull/9


- ai기능 개선

    > [!NOTE]
    >
    > [관련 이슈](https://github.com/Namanmoo-Damso/ops-api/issues/12.md)
    >
    > - api 서버
    > - https://github.com/Namanmoo-Damso/ops-api/pull/17
    >
    > - infra 서버; 환경변수 추가
    > - https://github.com/Namanmoo-Damso/ops-infra/pull/5


- gpu quota 신청 관련

    > [!NOTE]
    >
    > 조사 및 신청내역 문서화: https://github.com/Namanmoo-Damso/infra/issues/15


- v1 배포

    > [!NOTE]
    >
    > - 배포 시점 문서화:  https://github.com/Namanmoo-Damso/infra/issues/16
    > - 배포 관련 리소스 관리(s3) 문서화: https://github.com/Namanmoo-Damso/infra/issues/18
    > - v1 배포 자동화 및 문서화: 패키지화(ghcr), terraform 리팩토링 및 배포 자동화
    >   https://github.com/Namanmoo-Damso/infra/pull/19


- 중복/무용한 코드 제거 및 레포지토리 분리

    > [!NOTE]
    >
    > - https://github.com/Namanmoo-Damso/ops-api/pull/73
    > - https://github.com/Namanmoo-Damso/ops-web/pull/55


- livekit server 분리(중앙화)

    - 이 중계서버는 cpu집약적인데, 개발편의상 api 등 여타 서버들과 모두 함께 운영되었다.
    - 이것을 terraform을 활용해 독립적인 프로젝트로 관리
        - 모든 팀원이 중앙에 존재하는 동일한 livekit 서버를 통해 편리하게 개발
        - 향후 autoscaling 필요한 포인트일 수 있으므로 자동화되고 재현가능한 분리(terraform)

    > [!NOTE]
    >
    > - https://github.com/Namanmoo-Damso/ops-api/pull/78
    > - https://github.com/Namanmoo-Damso/infra/pull/21


- 필요 없는 환경변수 등 제거하고, 기본적인 리팩토링(환경변수는 한곳에서 읽기)

    > [!NOTE]
    >
    > - https://github.com/Namanmoo-Damso/ops-web/issues/123
    > - https://github.com/Namanmoo-Damso/ops-web/pull/124
    > - https://github.com/Namanmoo-Damso/ops-api/pull/94


- 브랜치 전략 실수와 해결

    - feature --> dev --> main
    - to-dev-PR 및 to-main-PR 모두 squash merge를 선택했다.
    - 이와중에 기존 dev를 그대로 사용했다.
        - **이것이 잘못된 점**
        - squash merge 한 시점부터 다시 dev:2 따위를 새로 브랜칭해서 사용해야한다.
        - 혹은 squash merge 대신 fast-forward merge를 해야했다.
    - 그래서 기존 main을 지우고 다시 만들었다.
        - 이 과정에서 지난번 v1을 만들며 기록해둔 issue와 archiving branch가 매우 든든했다.

    ```sh
    ops-web$ gh pr create --base main
    Warning: 1 uncommitted change

    Creating pull request for archive/dev-v1 into main in Namanmoo-Damso/ops-web

    ? Title (required) create version 1
    ? Body <Received>
    ? What's next? Add metadata
    ? What would you like to add? Assignees
    ? Assignees tomk1002
    ? What's next? Submit
    https://github.com/Namanmoo-Damso/ops-web/pull/138
    ```

    ```sh
    ops-api$ gh pr create --base main
    Warning: 4 uncommitted changes

    Creating pull request for archive/dev-v1 into main in Namanmoo-Damso/ops-api

    ? Title (required) create version 1
    ? Body <Received>
    ? What's next? Add metadata
    ? What would you like to add? Assignees
    ? Assignees tomk1002
    ? What's next? Submit
    https://github.com/Namanmoo-Damso/ops-api/pull/118
    ```


- v2 배포
    - 빌드 및 컨테이너 이미지 관리 스크립트
        - 특정 branch의 commit hash를 통해 소스코드 clone
        - build & push to ghcr
    - terraform 및 user-data를 활용해 서버 실행 및 어플리케이션 실행
        - livekit server 전용 인스턴스를 띄우고 실행
        - 그밖에 사항은 v1과 같음
            - private s3에서 env, docker-compose.yaml 로딩
            - docker-compose에 정의된 ghcr 주소에서 version 2 이미지를 pull하고 실행

    > [!NOTE]
    >
    > [관련 commit](https://github.com/Namanmoo-Damso/infra/commit/ad1e69ee7168f36d6e77aecdbead3a84aa0a5730)


- LLM을 자체모델로 운영하기
    - 기존에는 api로 구현함
        - aws bedrock
    - network latency 및 간헐적 응답 거절 문제
    - ollama를 엔진으로 자체모델로 전환 
        - llama3.1
        - exaon3.5
    - factory pattern을 적용하여 기존 방식도 환경변수 조절로 사용 할 수 있도록 함

    > [!NOTE]
    >
    > - https://github.com/Namanmoo-Damso/ops-agent/issues/39
    > - https://github.com/Namanmoo-Damso/ops-agent/pull/40
              

- STT를 자체모델로 운영하기
    - 기존에는 api로 구현함
        - aws transriber

    > [!NOTE]
    >
    > - https://github.com/Namanmoo-Damso/ops-agent/pull/41


- 프롬프팅 구조 개선
    - 파이썬 코드가 아니라 yaml 형식의 파일로 작성 및 jinja2 템플릿으로 주입
    - 용도가 뒤섞이거나 중복된 코드 통합 등 리팩토링

    > [!NOTE]
    >
    > - https://github.com/Namanmoo-Damso/ops-agent/pull/42


- 할머니봇 infra 구성 - terraform 프로젝트
    재완쓰


- v3-production 배포

    - tmux & tmuxp 등은 매우 유용하다. 거의 필수다. ssh, ssm은 생각보다 연결 지속성이 병약하다.
    - 간단히 파일을 옮기는 portal이나, 쉘 등 기본 도구들의 alias 따위를 잘 모아두고 가볍게
    서버에서 사용할 수 있게 스크립트를 준비해두면 좋겠다. lambda로 하나 말아놓거나 해도 좋을듯?
    - 컨테이너가 시작조차 못한것은 빌드된 것에 문제가 있을 수 있따.
    - health check가 실패하면 docker inspect 명령으로 살펴보자.

    > [!NOTE]
    >
    > 배포 버전 
    > - https://github.com/Namanmoo-Damso/ops-web/commits/v3/
    > - https://github.com/Namanmoo-Damso/ops-api/commits/v3/
    > - https://github.com/Namanmoo-Damso/ops-agentb/commits/v3/
    > - https://github.com/Namanmoo-Damso/infra/commits/v3/
    > - https://github.com/Namanmoo-Damso/infra/pull/28


> [!lg] Log 2026-01-01
> 근데 이건 기여가 (아직은) 아닌것 같기도하고..?

- [terraform 테스트](기여/terraform_테스트.md)

</details>

<details><summary><h2>나만무 회고</h2></summary>

- [2025-12-28](회고/2025-12-28.md)
- [2025-12-29](회고/2025-12-29.md)
- [2025-12-30](회고/2025-12-30.md)
- [2025-12-31](회고/2025-12-31.md)
- [2026-01-01](회고/2026-01-01.md)
- [2026-01-02](회고/2026-01-02.md)
- [2026-01-05](회고/2026-01-05.md)
- [2026-01-06](회고/2026-01-06.md)
- [2026-01-16](회고/2026-01-16.md)


<details><summary>저랑 같이 해보니까 어떠신가요?</summary>

- [Phase.1: 기획](./동료로서/Phase.1:_기획.md)
- Phase.2: 개발
- Phase.3: 후기

</details>



</details>

<details><summary><h2>아키텍쳐</h2></summary>

- AI
    - [model serving framework](architecture/model_serving_framework.md)

- infra
    - [infra-structure](architecture/infra.md)
    - [개발서버 터널링 이슈](architecture/개발서버_터널링_이슈.md)
    - [개발서버용 인스턴스 템플릿](architecture/개발서버용_인스턴스_템플릿.md)
    - [aws resources](architecture/aws_resources.md)

    - terraform 테스트
        - [terraform](architecture/terraform.md)

    - [aws 자원에 접근하는 순서도](architecture/aws_자원에_접근하는_순서도.md)

</details>

<details><summary><h2>회의</h2></summary>

- [idea brainstorming with AI](/Project/크래프톤_정글/회의/brainstorming_with_AI.md)
- [001](회의/001.md)
- [002](회의/002.md)
- [003](회의/003.md)
- [007](회의/007.md)
- [008](회의/008.md)
- [009](회의/009.md)
- [010](회의/010.md)

> [!lg] Log 2026-01-01
> 기획회의 이후로는 회의가 급격히 줄어든 느낌이다.
> 회의 준비를 할 것도 딱히 없고, 그냥 하고 있는 작업 공유 및 의사 결정이다.

</details>

<details><summary><h2>학습</h2></summary>

### 기초

- [web basic](학습/web_basic.md)
- [next js basic](학습/next_js_basic.md)

### React

- [why react?](학습/React/why_react?.md)
- [learn react](학습/React/learn_react.md)
- [react router](학습/React/why_react_router.md)

### Next.js

- [why next.js?](학습/Next.js/why_next.js.md)
- [Routing](학습/Next.js/Routing.md)
- [Dynamic(가변적) Routing](학습/Next.js/Dynamic_Routing.md)
- [SPA, `<Link>`](학습/Next.js/SPA.md)
- [정적 자원 사용하기](학습/Next.js/정적_자원_사용하기.md)
- [Server Component vs Client Component](학습/Next.js/ServerComponent_vs_ClientComponent.md)

> [!td]
>
> - [ ] 완성 필요
>   - 나만무 레벨에서 완성 해야할까?
>   [컴포넌트 렌더링: Client Component vs Server Component](학습/Next.js/컴포넌트_렌더링:_Client_Component_vs_Server_Component.md)


### NestJS

> [!lg] Log 2025-12-19
> 큰일이네 얼른 공부해야하는데...

> [!td]
>
> - [ ] Throw하면 실행 흐름이 어디로 이어지지? 각종 Decorator에 걸리면?
> - [ ] Module이라는 개념적 아이디어와 구현상 아이디어에 대해 알아야 해
>   - import / provider / controller / export 같은 것들이 있다.


### AI


- 우리는 api를 사용하기엔 실시간 응답성이 중요하다.(전화 통화니까)
- 학습이 필요할 것 같다.(한국어, 사투리, 뭉게진 발음...?)


RAG pipeline
https://wikidocs.net/231393

Loading: 로컬 파일시스템, 구글 드라이브, 슬랙 등등 외부에서 데이터를 가져옴.
Text Split: 가져온 문서를 분해해서 검색 용이한 형태로 쪼개기. 빠르고 정확한 검색을 위한 단위로 나눔.
Indexing: Text chunks를 Embedding하여 vectorDB에 저장
User Input: 질문
Retrieval: 유저의 질문을 통해 vectorDB에서 적합한 데이터를 다각도로 검색(다양한 방법론이 있다)
Generation: (Retrieved Documents + 질문)으로 답변 생성

1.  **Loading (Data Ingestion):** PDF, 웹페이지, API 등 다양한 소스에서 원시 데이터를 가져오는 단계입니다. (Document Loader 사용)
2.  **Text Split (Chunking):** LLM의 입력 토큰 제한(Context Window)과 검색 정확도를 위해 문서를 의미 단위로 자릅니다. 너무 잘게 자르면 문맥을 잃고, 너무 크게 자르면 검색 정확도가 떨어집니다.
3.  **Indexing (Embedding & Storage):** 텍스트를 숫자로 된 벡터(Vector)로 변환(Embedding)하여 Vector DB(Pinecone, Chroma, FAISS 등)에 저장합니다.
4.  **User Input:** 사용자의 질문이 들어옵니다.
5.  **Retrieval:** 사용자의 질문도 벡터로 변환한 뒤, Vector DB에서 가장 유사도가 높은 텍스트 조각(Chunk)들을 찾아옵니다. (Keyword 검색과 Vector 검색을 섞는 Hybrid Search도 많이 씀)
6.  **Generation:** 찾아온 문서 조각들을 "참고 자료"로 삼아 프롬프트를 구성하고, LLM에게 답변을 생성하도록 요청합니다.


3. 나는 실시간 화상통화를 위해 즉각적인 대화를 제공해야해. RAG 파이프라인을 통해서. 이때 적절한 프레임워크 또는 프레임워크의 조합을 추천하고, aws를 활용한 인프라 아키텍쳐를 제안해봐. 개발 속도를 위한 가장 단순한 버전과 성능을 위한 복잡한 버전 두가지를 각각 추천해줘. 특히 몇개의 구분된 서버가 필요한지도 잘 표현하고말야.

#### **B. 성능과 실시간성을 위한 복잡한 버전 (Production)**

레이턴시를 극한으로 줄이고, 보안과 안정성을 확보하는 아키텍처입니다.

*   **서버 구성:** **최소 2~3개 군집 (API Gateway/Backend, Vector DB, LLM Serving)**
*   **인프라:** AWS EKS (Kubernetes) 또는 EC2 + GPU Instances

**[아키텍처 구성도]**

1.  **Frontend (Client):** WebRTC 화상통화 + WebSocket 연결 (실시간 양방향 통신)
2.  **Load Balancer (AWS ALB):** 트래픽 분산 및 WebSocket 지원
3.  **Backend Server (EC2/EKS - Node.js or Go):**
    *   **역할:** WebSocket 세션 유지, 비즈니스 로직 처리.
    *   LangChain을 쓰지 않고 직접 경량화된 로직 구현 권장 (오버헤드 제거).
4.  **RAG Engine Server (EC2/EKS - Python):**
    *   **역할:** LangChain/LlamaIndex를 사용하여 검색 및 프롬프트 구성.
    *   **Vector DB:** **Redis Enterprise** (AWS ElastiCache for Redis) 또는 **Milvus**를 로컬/VPC 내부에 구축. (메모리 기반이라 디스크 기반 DB보다 훨씬 빠름)
5.  **LLM Serving Server (AWS EC2 g5.xlarge 등 GPU 인스턴스):**
    *   **역할:** 외부 API(OpenAI)를 쓰지 않고 **sLLM(Small Large Language Model)**을 직접 호스팅 (예: Llama-3-8B, Mistral-7B).
    *   **도구:** vLLM 또는 TGI(Text Generation Inference) 사용하여 추론 속도 극대화.
    *   **이유:** 외부 API 왕복 시간(Network Latency) 제거 및 보안 강화.

**데이터 흐름**
`User Voice` -> `STT(AWS Transcribe or Whisper)` -> `Backend` -> `RAG Engine(Redis 검색)` -> `LLM Server(vLLM)` -> `Streaming Response` -> `TTS` -> `User Audio`

**[핵심 포인트]**
*   **WebSocket:** HTTP 요청/응답 오버헤드를 줄이기 위해 필수.
*   **Local Vector DB (Redis):** 외부 SaaS(Pinecone 등)를 타지 않고 VPC 내부에서 메모리로 검색하여 속도 단축.
*   **Self-hosted LLM:** 작은 모델(7B~8B 파라미터)을 GPU 서버에 띄워 네트워크 지연 없이 즉각 추론.
*   **Streaming:** LLM이 답변을 다 만들 때까지 기다리지 않고, 한 글자씩 생성되는 대로 클라이언트에 전송.

**[요약]**
*   **고성능 버전:** AWS EC2(GPU) + vLLM(로컬 모델) + Redis(벡터 검색) + WebSocket 서버


</details>

<details><summary><h2>기타</h2></summary>

- [시작하기에 앞서](/Project/크래프톤_정글/시작하기에_앞서.md)
- [feedback](feedback.md)
- [refactoring with AI from AI-agent-code](refactoring_with_AI_from_AI-agent-code.md)
- [how to claude-code](how_to_claude-code.md)


> [!qt] 할머니, 할아버지.. 전화 하고 싶으실까? 그렇게 만들려면?
>   󱞪 


> [!qt] 기관 사용자는 정확히 누구고, 무엇에 유용함을 느낄까?
>   󱞪 


> [!lg] Log 2026-01-30
> 
> 인터페이스가 하나의 전문 영역이 될수도 있겠다.
> 
> - 무엇과 무엇 사이?
>     - 사람, 컴퓨터, 핸드폰, AI, 자연, 서비스, ...


> [!lg] Log 2026-01-30
>
> prod와 dev환경이 결국은 비슷하고 일치해야겠다. 또는 stage 환경이 필요하다.
>
> - 부하 테스트하기가 어려웠다.



> [!td]
>
> - [ ] 브라우저에 대해서도 알아야겠다.
> - [x] window, document 객체란?
>   - window: 브라우저 api 등 내부 객체에 접근할 수 있는 진입점
>   - document: DOM 객체 진입점
> - [ ] 담당 정하기: 기획 / UIUX / 배포 / CICD / repository / LangChain

</details>
