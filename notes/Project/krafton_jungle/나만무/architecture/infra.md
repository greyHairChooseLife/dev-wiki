# infra-structure


**목표**

- 편리하고 빠른 개발서버 실행
- 안전하고 재현성 높은 배포서버 실행

- 개발 편의성 재고
    - shell script를 적극 활용
    - 문서화: 사용법 및 작동 방식과 관련 주요 개념


---


- docker compose로 개발 서버를 구성해야 할 듯
    - e.g. BE 팀은 FE, AI서버 따위를 현재 버전 IMAGE로 실행하고 BE 서비스만 로컬 코드로 실행하는 방식

- AI 팀은 컴퓨팅 성능 문제로 원격 서버를 항시 이용해야할 듯
    - tailscale 도입하여 개발용 인스턴스와 모든 개발자 PC를 묶기 => 고정된 url로 AI 서버와 통신 가능
    - (대안)ssh tunneling을 활용하여 로컬 환경과 최대한 비슷하게 작업



- DB는 로컬 환경에서 다수를 띄워서 사용한다. 추후 env 파일 교체를 통해 별도 서버 또는 mananged 서비스로 갈아탄다.





### 개발 서버 시작

- FE/BE팀

    - 안정화 버전 AI 서버로 접속해서 ssh-tunneling(AI 서버 호출용 host)
    - docker-compose 실행
        - 개발하려는 서비스는 로컬 소스코드 bind-mount
        - 여타 서비스들 registry에서 이미지 가져와 컨테이너 실행
        - DB들은 종류 무관 local containers 활용

- AI팀

    - GPU 인스턴스 실행 및 접속
    - 소스코드 pull / 의존성 설치
        - DB 컨테이너 활용 등을 위해 docker-compose로 실행
    - local에서 vs-code 접속용 host 제공


### 안정화 버전 AI 서버

특정 branch (e.g. release) CICD로 build 된 Image 및 local DB containers로 구성된 도커 컴포즈 실행



