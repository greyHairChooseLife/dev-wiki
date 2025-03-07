# 󰏢 index


## 가상환경 / 패키지 생태계


### 개념

- 가상환경
  - 방대한 파이썬 생태계, 혼란을 막기 위해 가상환경이 필요하다.
  - 가상환경을 추가(생성)하고, 선택(활성화)하고, 또 삭제하며 의존성/호환성을 관리한다.
  - 가상환경의 활성/비활성화는 PATH를 수정하는 것이다. 따라서 (기본적으로는)casecading 되는것이 아니다.


- 패키지 생태계
  - 패키지를 배포하는 서버를 '채널'이라고 부른다. 목적에 따라 다양한 채널이 존재하며, 직접 만들 수도 있다.
    - 기본 채널은 PyPi라고, pip install 하면 기본값으로 이 채널에서 검색한다.
    - 데이터과학/머신러닝 분야에 특화된 conda-forge도 있다. 이것은 커뮤니티 기반 채널이다.


- 배포판
  - anaconda는 데이터과학/머신러닝 분야에 파이썬을 활용하기 위해 필요한 150개 이상의 유명 패키지가
    사전 설치된 배포판이다. conda같은 패키지/가상환경 관리 도구도 사전 설치되어있고, (패키지)채널도
    사전 설정되어 있다.
  - miniforge도 배포판이다. 다만, conda 대신 (더 빠른)mamba도 지원하며 사전 설치된 패키지들이 거의 없다.



### (나만의) 관리 전략


> [!re] 파이썬 패키지를 마구잡이로 설치하다보면 pacman/yay로 관리하는 나의 호스트 시스템이 망가질 수 있다.
>
> 기본적으로 가상환경을 사용하되, 컨테이너에 한번 더 격리하기로 했다.
>
> LSP가 문제인데, volume으로 컨테이너 내부의 가상환경 경로를 호스트와 bind하고, 호스트에서도 이
> 가상환경을 활성화 하는 방식으로 대응한다.


#### `my_python_project/` 파일 구조

```bash
my_python_project/
│
│── volume/
│   ├── conda_env/                    # 컨테이너에서 생성, 호스트와 바인드 마운트, added to .gitignore
│   ├── app/
│── environments/
│   ├── v0.yml                        # 원본 환경 정의
│   ├── exported.yml                  # mamba로 export한 환경
│── entrypoint/activate_conda_env.sh  # docker-compose로 volume을 연결하면 호스트 경로가 우선된다.
│                                     # 도커파일에서 기껏 설치한 것들이 모두 날아가버리는것.
│                                     # 그래서 다시한번 작업해준다.
│── Dockerfile
│── docker-compose.yml
│── manager.sh                        # docker(-compose) commands, conda (de)activate after docker container run/stop
```



- `conda create --prefix my/venv/path`로 만든 가상환경이기 때문에 conda env list로 조회가 안된다.
  이에 아래처럼 작업해줘야함.

  ```bash
  # CD into target VIRTUAL-ENVIRONMENT PATH
  echo $(pwd) >> ~/.conda/environments.txt
  ```
