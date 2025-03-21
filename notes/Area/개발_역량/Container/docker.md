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

#### QnA

1. Docker-compose는 production에는 별로인것 같다. 컨테이너 단위로 버전관리가 어려운듯?
2. 왜 컨테이너에 생명을 준 내가 권한 문제에 시달리나? 컨테이너 내부의 호스트로써 건드는것도 아닌데
3. 도커 이미지 외부에서 관리하며, 컨테이너 내부로 주입하는 방식 뿐인가?
4. 어떻게 연결(통신)하고 분리시킬 것인가?
5. 마운트와의 차이점이 뭐야?

#### Tips

- 일반 사용자로 docker 사용하기, bye bye sudo

  도커 쓰는 내내 관리자로 작업하거나, 또는 sudo를 갖다 붙이는 것은 어렵다. shell script를 짜기도 어렵고.

  이럴 때 필요한 것이 'docker group'이다. group에 속한 (system)user는 sudo docker를 사용 할 수 있다.

  1. `whoami`: username 확인
  2. `sudo usermod -aG docker $USER`: 'docker group'에 추가
  3. user 로그아웃했다 오거나, 껏다 켠다.

  끝!


## Dockerfile Best Practices


### 빌드 시간 단축

레이어 캐싱을 활용한다.

도커 이미지 빌드는 언제 캐싱을 무시하고 새롭게 레이어를 만드나?

  - `Dockerfile` 내용 변경
  - `COPY` 명령으로 다루는 파일의 변경
  - 앞서 변경이 발생한 이후에는 모두 변경


### 빌드 과정에서 다루는 용량, 이미지 용량 축소

- `node_modeles/`처럼 굳이 복사할 필요 없는애들 생략(`.dockerignore` 적극 활용)

- multi-stage
  `FROM`을 두번 사용하면 앞쪽 스테이지에 따른 결과는 **특정되지 않는 한** 뒤쪽 스테이지로 넘어가지
  않는다. 앞쪽 결과물 중 필요한 것만 쏙 빼먹으면 그만

  - 앞쪽 스테이지 `AS builder`
  - 뒤쪽 스테이지 `COPY --from=builder /from/prev/stage/path /to/curr/stage/path`

- layer squashing
  : 빌드 과정에서 생긴 쓸데없는 파일의 제거. ex) npm 캐싱

  - 개별 `RUN 커맨드`를 실행할 때마다 레이어가 생성
  - 도커 레이어는 불변성을 보장하기 때문에 앞쪽의 레이어에서 생성된 파일을 뒤쪽의 레이어에서
    삭제할 수 없음(삭제 되었다는 표시만)
  - 따라서 뭔가 삭제할 일이 있다면 새로운 `RUN 커맨드`로 삭제하는게 아니라, 삭제할 파일이 생성된
    단계에서 삭제까지 마무리 지어야한다.

    _예시_

    ```Dockerfile
    # Bad case
    RUN npm install --production
    RUN npm cache clean --force
    RUN rm -rf /root/.npm
    COPY . .
    RUN npm run build
    ```

    ```Dockerfile
    # Good case
    RUN npm install --production && \
        npm run build && \
        npm cache clean --force && \
        rm -rf /root/.npm
    COPY . .
    ```



### Tools

- dive: 레이어 톺아보기
- SlimToolkit: 이미지 최적화(뭔지 잘 모름)
