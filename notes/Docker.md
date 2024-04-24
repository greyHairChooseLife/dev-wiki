## 도커의 구성

- 네트워크
- 볼륨
- 이미지
- 컨테이너
- 환경변수, secret
- 권한
- 로깅

### 네트워크

보안을 위한 원천 차단

- local-network <==> docker-network
- docker-network <==> another-docker-network

### 볼륨

데이터의 영속성을 위하여

- local => container
- container => local
- container => container

### 이미지

버전 관리, 아카이빙

- 버전관리
- 덮어쓰기

### 컨테이너

컨테이너의 상태에 따른 활용

- alive
  - running
  - pause
- dead
- broken

### 환경변수, secret

잘 모른다. 그냥 `.gitignore` 리스트에 넣어두고 원시적으로 관리하는 정도.

### 권한

하나도 신경 안쓰고 있었다.

불편한 거라면 volume으로 설정한 디렉토리의 파일을 직접 손대려고 하면 권한 문제가 발생하는점이다.

### 로깅

하나도 신경 안쓰고 있었다. 뭐가 잘 안되면 `docker logs` 명령으로 살펴보기나 했지.

배포 환경에도 도커를 활용한다면 이렇게 무책임해서는 안되겠다.

# Examples

## best practices

### 1. take advantage of layer caching (as building image)

Dockerfile 작성 시 instructions의 순서를 적절히 배치하여 시간과 컴퓨팅 자원을 효율적으로 사용할 수 있다.

- _나쁜 예_

```Dockerfile
COPY src target
RUN npm install --production  # 소스 한글자만 바꿔도 npm install이 다시 실행된다.
```

- _좋은 예_

```Dockerfile
COPY packages.json packages-lock.json target
RUN npm install --production  # 패키지가 실제로 수정 될 때만 실행된다.
COPY src target

# .dockerignore에서 node_modules/를 제외시켜줘야 완성이다.
```

### 2. multi-stage builds

run-time에 필요한 환경과 build-time에 필요한 환경은 다를 수 있다. 각 단계에서 꼭 필요한 것만 갖다 쓰자.

# QnA

1. Docker-compose는 production에는 별로인것 같다. 컨테이너 단위로 버전관리가 어려운듯?
2. 왜 컨테이너에 생명을 준 내가 권한 문제에 시달리나? 컨테이너 내부의 호스트로써 건드는것도 아닌데
3. 도커 이미지 외부에서 관리하며, 컨테이너 내부로 주입하는 방식 뿐인가?
4. 어떻게 연결(통신)하고 분리시킬 것인가?
5. 마운트와의 차이점이 뭐야?

# Tools

- [github: lazydocker](https://github.com/jesseduffield/lazydocker)

  docker client를 머금은 TUI 패키지다. lazygit을 만든 사람이 만들어서, 둘 다 비슷하고 또 믿을만 하겠다.

  `docker`, `docker compose` 클라이언트의 커맨드(api)를 모두 사용할 수 있다. 사전에 정의해둔 attatch,
  detatch, logs 등 명령어를 단축키에 지정해서 간편하게 사용하는 방식이다.

  docker objects _(service, container, image, volume, network)_ 를 각각의 패널에 나눠서 보여주고, 각각의
  패널에서 커맨드를 실행할 수 있다. 확실히 시각적으로 맥락을 파악하기 좋다.
