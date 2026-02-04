# 󰏢 week-0 [-] 


필수 요구사항

- SSR, Jinja2 활용
- auth 구현, session/jwt 무관하나 이유를 알고 할 것


## 코치님 중간 방문 말씀

- 중요한건 정답을 맞추는게 아니라,
  - 정답을 찾아가기위한 논의의 과정을 겪었나?
  - 그걸 얼마나 잘 겪었나?

- 입학 시험 과제에서는 ajax 요청 후 렌더링을 했다. 그리고 이번 0주차 과제는 template_engine을 활용해
  SSR로 구현해보는거다. 그리고 그 차이를 느껴보라는거다.
  - 단, DX보단 UX위주 차이점을 경험해보는 것이다.





## 회의록


### 1회


- SSR의 장점을 부각하는 아이디어를 선보이자.

> [!tdd]
>
> - [x] CSR vs SSR 설명하기
> 
>   - SSR: backend에서 **완성된** html을 응답한다.
>     - 장점: 초기로딩속도/seo
> 
>   - CSR: client에서 **JS를 이용해** html을 완성한다.
>     - 장점: 풍부한 사용자 경험
>     - 단점: 홈페이지 로딩 다소 느림
>     - html 파일을 응답에 포함시켜도 여전히 CSR일 수 있다. 렌더링을 완성하기 위해 client에서 JS를
>       실행한다면.


### 2회

- SSR의 장점을 부각하는 아이디어를 찾는건 앞뒤가 바뀐것같다. 기술을 위한 아이디어 말고 아이디어를
  위한 기술이 맞지 싶다.

- 일단 어떤 아이디어를 구현하면서 각 요청에 맞는 `SSR/CSR`을 사용하자. 그리고 CSR로 구현했을 때
  대비 SSR이 더 효과적이었던 것을 실제로 보여주자.

  - **운동플래너**: 유저의 상태(인바디)와 목표에 따른 운동 플랜을 자동으로 생성

> [!tdd]
>
> - [x] LLM 요청 결과로 일관된 응답 형식을 받을 수 있는가?
> 
>   - 가능해 보인다. 아래 3단계에 따른다.
>   
>   > 1. openai, claude 등 유명 LLM 서비스업체들이 내놓은 답은 sturctured model output이다.
>   >    가이드라인을 잘 따르는 것이 중요하다.
>   >
>   >    > [!rf]
>   >    > https://platform.openai.com/docs/guides/structured-outputs
>   >
>   >  
>   > 2. 라이브러리로 기본적인 json 형태 오류 개선
>   >    - json-repair(python)
>   >
>   > 3. 최종 결과 파싱 및 검증 후 예외처리
>   >    - pydantic, Guardrails, Outlines, Guidance 등 오픈소스 라이브러리는 LLM의 출력이 스키마에
>   >      맞는지 검증하고, 필요시 재요청하는 기능을 제공합니다.


### 3회


- ai structured output 가능해 보인다. --> 도전 해 보기로
- 플로우차트 구체화 논의

> [!tdd]
>
> - [x] 논의한 플로우차트 기반으로 와이어프레임 및 핵심 비즈니스룰 정의
>   - [결과물](https://excalidraw.com/#room=83417094ad144b109c6c,K42oAh9bT9FuIcp40QL-zA)
>
> - [x] 각 단계에 따른 상세 정보 살펴보고 공유하기
>   - 아래 헤더에 정리


> [!rf]
>
> some tips from anonymous redit user
>
> > 프로덕션에서 얻은 조언:
> > 
> > - 가능하다면, OpenAI 의 스키마를 사용하여 구조화된 출력을 사용하세요.
> > - 그렇지 않다면, 응답 내에 포함된 JSON의 쉬운 경우를 포착할 수 있는 파서를 구현하세요 (흔한 실수는 말하다가 JSON을 출력하거나, 따옴표로 감싸는 것 등입니다).
> > - 이렇게 하면 파싱 실패의 90%를 커버할 수 있으며, 나머지 9.9%를 커버하기 위해 LLM에 최신 응답을 다시 보내고, 오류를 알리고, 수정하도록 시도하는 작은 메커니즘을 구현할 수 있습니다. 여러 번 반복할 수도 있습니다.
> > - 가능하다면 필요한 스키마를 단순화하세요.
> > - 더 똑똑한 모델로 업그레이드하세요.
> > - 온도 0.0을 사용하세요.
> > - 스키마 외에도, 프롬프트에 실제 출력 예시를 넣어주세요.
> > 
> > 예상되는 출력의 시작 부분을 어시스턴트 응답에 미리 채워 넣는 것도 시도해볼 수 있습니다.
> > 제 경험상, 이렇게 하면 파싱 실패의 99.9%를 커버할 수 있습니다.

#### STRUCTURED OUTPUT with llm model


1. **LLM structured model output**

  > [!NOTE] openai 공식자료
  > [ref](https://platform.openai.com/docs/guides/structured-outputs)
  >
  > - 다른 라이브러리와 조합도 좋다.
  >   - In addition to supporting JSON Schema in the REST API, the OpenAI SDKs for Python and
  >     JavaScript also make it easy to define object schemas using Pydantic and Zod respectively.
  > 
  > - JSON schema라는 기술을 이용해서 요청을 날리는데, json형태를 정규화하는 기술이다.
  >   - https://json-schema.org/overview/what-is-jsonschema
  > 
  > - 최신 모델에서 지원한다.
  >   - Structured Outputs is available in our latest large language models, starting with GPT-4o.
  > 
  > - 두가지 방식이 존재한다. 'function calling' VS 'text.format' 우리는 text.format을 사용하는게 좋겠다.
  > 
  >   - function calling: 어플리케이션의 내부 api 또는 함수를 model이 실행하기 위한 목적 일 때
  >   - text.format: output을 사용자에게 응답할 목적으로 생성하는 경우
  > 
  > - (기존 JSON mode의 진화된 버전이다.)
  > - (example도 제공함)


  > [!NOTE] how to use text.format?
  > 
  > - Step 1: Define your schema
  >
  > - Step 2: Supply your schema in the API call
  >
  >   > [!qt] 아래와같은 문구가 있는데 subsequent request 여부를 뭘로 구분할까? 시간제한이 있나, id라도 달아주나?
  >   > _Note: the first request you make with any schema will have additional latency as our API
  >   > processes the schema, but subsequent requests with the same schema will not have additional
  >   > latency._
  >   > 󱞪 
  >
  >
  > - Step 3: Handle edge cases
  >   
  >   - because,
  >     - safety reasons (by policy maybe)
  >     - token limit
  >
  >   - 이때 요청에 담긴 json_schema를 그대로 따르지 않고,
  >     - refusal key가 포함된다.


  > [!NOTE] Tips
  >
  > - Handling mistakes
  >   Structured Outputs can still contain mistakes. If you see mistakes, try adjusting your
  >   instructions, providing examples in the system instructions, or splitting tasks into simpler
  >   subtasks. Refer to the prompt engineering guide for more guidance on how to tweak your inputs.
  > 
  > - Avoid JSON schema divergence
  >   To prevent your JSON Schema and corresponding types in your programming language from
  >   diverging, we strongly recommend using the native Pydantic/zod sdk support.




2. **라이브러리로 기본적인 json 형태 오류 개선**

  - json-repair(python) https://github.com/mangiucugna/json_repair
    - demo https://mangiucugna.github.io/json_repair




3. **최종 결과 파싱 및 검증 후 예외처리**

  - pydantic
  - Guardrails
  - Outlines
  - Guidance


#### 테스트 구현해보기

> [!tdd]
>
> - [x] openai + pydantic 라이브러리로 테스트 --> 너무 잘된다.
>
> `cd /home/sy/jg/week-0/structured_output_test && tpl.`


### 4회

(기획발표 후 피드백 받은 상황) 

> [!lg] Log 2025-09-02
>
> 기획발표 후 피드백 받은 상황이다.
> 우리 팀 뿐만 아니라 전체적으로 도전 레벨이 너무 낮다는 지적이 있었다. 정당한 지적이라는 생각을
> 했다.
>
> 또한 나의 예상과는 달리 아이디어를 구현하는 기술적 고려보다는 유저 입장에서 과연 쓰임이 있는
> 서비스를 만들었는지 많이 물어보셨다. 일단 그게 충족이 되면, 즉 풀만한 문제가 되면 그 뒤에
> 기술적 첼린지가 있느냐를 물어보셨다.
>
>   - **--> 우리도 더 많이 노력해보자. 쉬운 100% 완수보다 60%의 완성과 40%의 실패, 그러나 적어도 도전이
>     담긴 실패를 만들어보자.**


> [!tdd]
>
> - [x] 영상 촬영 및 모션 인식 기술 리서치 후 공유
>
> > - 주요 구성 요소
> > 
> >   - 영상 촬영: 스마트폰 카메라로 실시간 또는 저장된 영상을 확보
> >   - 자세 인식: 영상에서 사람의 관절 위치 추출 (Pose Estimation)
> >   - 자세 분석: 추출된 관절 데이터를 기반으로 운동 자세 평가
> >   - 피드백 제공: 실시간 또는 분석 결과를 사용자에게 전달
> > 
> > 
> > - 구현조건 대응
> > 
> >   - 모바일이지만 브라우저 위에서 동작
> > 
> >     -> 브라우저 기반 Pose Estimation
> >       - WebAssembly(WASM) 또는 WebGL을 활용한 경량 모델 필요
> >       - 대표적인 라이브러리: [TensorFlow.js](https://www.tensorflow.org/js), [MediaPipe](https://google.github.io/mediapipe/solutions/pose.html) (Web 지원), [ml5.js](https://ml5js.org/)
> > 
> > 
> > - 순서도
> > 
> >   1. 휴대폰 카메라로 찍는 영상을 브라우저로 가져오기 -> getUserMedia 브라우저 내장 API
> > 
> > 
> > 
> >     _(클라이언트(모바일)에서 분석)_
> > 
> >   2. Pose Estimation 라이브러리로 받아온 영상 분석 후 유저에게 피드백
> > 
> > 
> > 
> >     _(서버(모바일)에서 분석)_
> > 
> >   2. 영상 정보를 서버로 보내서 분석만 서버에서 하고(고성능 모델 사용 가능), 웹소켓으로 유저에게 실시간
> >      피드백


**==> 일단 클라이언트에서 렌더링하는 것 부터 시도해 보기로.**



### 5회


아키텍쳐 / 소스코드의 (모듈 또는 디렉토리) 구조에 대한 의견 통합이 필요하다.


> [!tdc]
>
> - [c] 일단 빨리 끝낼 수 있는 소스코드 구조부터 공부하고 제안해보기.
>   - 약속한 시간에 하기 어려워서 시간을 더 요청했다. 그런데 먼저 제안할 수 있다는 팀원분이 계셔서
>     설득을 당하러 갔다. 보여주세요!


### 6회


- 와이어프레임 재조정

- 역할 분할
  - 로그인 기능 front/back
  - 플랜 생성 기능 back, repository setup, first commit with base dependency(db, pymongo)
  - 플랜 생성 기능 front, openai api with structured output

- 프로젝트 진행사항 공유 -> branch 전략 및 PR 리뷰 규칙 논의
  - main은 배포 전용, dev에서 분기 및 merge, conflict solve 작업

- 일정 계획



## 코딩

### 협업을 위한 git command

#### PR을 fetch해서 로컬에서 변경내역 확인하기


```bash
gh pr list
gh pr checkout <name-what-I-want>
# in DiffviewFileHistory --range=origin/dev..HEAD
```


> [!lg] Log 2025-09-03
>
> **실행 계획:**
> 1. `gh pr list`로 PR 번호를 확인한다.
> 2. PR 번호를 사용해 PR의 head를 fetch한다.
> 3. 로컬 브랜치로 체크아웃한다.
> 4. 원하는 브랜치와 비교한다.
>
> **예시:**
> PR 번호가 123이고, 내 로컬 브랜치가 `main`이라면:
>
> ```bash
> git fetch origin pull/123/head:pr-123
> git checkout pr-123
> git diff main...pr-123
> ```



#### 내가 작업한 브랜치 PR 날리기


  ```bash
  # 현재 브랜치를 원격 저장소로 푸시
  git push origin HEAD

  # GitHub CLI로 PR 생성
  gh pr create --base <target branch>
  ```

  - 이후에 추가적이 변경이 생기면 그냥 commit 후 다시 push하면 자동으로 PR에 반영된다. PR은 remote에
    존재하는 두 브랜치를 비교하는거기 때문에

  - feature 브랜치를 dev 기준으로 rebase하는 체크리스트

    ```bash
    # 1. 원격 dev 브랜치 최신화
    git fetch origin dev

    # 2. 로컬 dev 브랜치 최신화
    git checkout dev
    git merge origin/dev

    # 3. 작업 브랜치(feature)로 이동
    git checkout <feature-branch>

    # 4. dev 브랜치 기준으로 rebase
    git rebase dev

    # 5. (충돌 발생 시)
    - 충돌 파일 수정
    - git add <수정한 파일>
    - git rebase --continue

    # 6. rebase 완료 후 원격 브랜치 강제 푸시, rebase는 해당 브랜치의 부모 커밋 해시를 바꾼다.
    git push origin <feature-branch> --force

    # 7. (rebase되어 기존 맥락을 잃어버리면 헷갈릴 수 있으니 필요시 알려준다.)
    (필요시) PR에서 rebase로 인해 변경된 내용 코멘트 남기기

    # 7. (merged 이후 브랜치 삭제하기)
    (필요시) git push origin --delete 브랜치이름
    ```


### 내 작업 히스토리

#### branch: feat-ask_exercise_plan_module

> [!tdd]
>
> - 문제: 60분 운동하기를 원하는 날인데 30분도 채 안걸리는 플랜을 생성해준다.
> 
> - 해결:
> 
>   - try 1: 
>     - 반복회수 세트수 등을 기반으로 소요시간 계산식을 따르도록 프롬프팅.
>     - 해당 날짜의 요구 시간 x 0.9 이상의 총 운동시간을 확보하도록 플랜을 구성하도록 규칙 프롬프팅
>      
>       **==> 실패,실패,실패,실패,**
> 
>   - try 2:
>     - 운동별 수행 시간을 제한하고, 해당 날짜의 요구시간에 따라 몇가지 운동으로 구성할지 직접 제공
> 
>       **==> 이 방법으로 성공했다.**



#### branch: feat-pages_about_plan_feature


아래와 같이 운동 플랜과 관련된 페이지들의 프론트엔드 부분을 담당했다.

```bash
tree healthking-tomorrow/app/templates/plan/

├── calendar.html
├── form.html
├── info.html
└── main.html
```

[와이어프레임](https://excalidraw.com/#room=83417094ad144b109c6c,K42oAh9bT9FuIcp40QL-zA)


#### branch: feat-calendar_page


월간 플랜 data api를 다 만들어 주셨다. 그래서 나는 그 api가 리턴해주는 데이터를 가지고 달력
페이지를 그려주면 된다.

> [!td]
>
> - [ ] api를 보고 클라이언트가 calendar 페이지와 함께 어떤 데이터를 리턴받는지 정리
>   - 그냥 봐서 모르겠다. 서버를 돌려서 실제 데이터를 입력하고 받아보자.
>   - [x] requirements.txt에 flask-jwt-extended가 없어서 일단 별도 설치
>     - `pip install flask-jwt-extended`
>     - > **실행 방법:**
>       > 
>       > Flask에서 앱 팩토리 패턴을 사용할 때는 `--app` 옵션에 `"패키지명:함수명"` 형태로 지정해야 합니다.
>       > 
>       > 예시:
>       > ```bash
>       > flask --app app:create_app --debug run
>       > ```
>       > 
>       >   - `--debug` 옵션을 추가하면 코드 변경 시 자동으로 서버가 재시작됩니다.
>
>   - [x] mongodb가 내 로컬서버에 돌고 있어야 가능했는데, 없어서 나는 이 서버도 가상환경
>           내에서 돌아간다고 생각했다. 근데 도커컨테이너로 로컬에 mongodb서버 켜는걸
>           도와주셔서 쉽게 했다.
>
> - [ ] 와이어프레임 페이지 이미지 통해 ui를 만든다.
>   - [ ] 일단 전달 받는 데이터와 화면에 표시되는 내용이 일치하는지 살펴본다.
>   - [ ] 더 그려줘야하는 요소가 있으면 그려주고 이미지를 다운받는다.
>   - [ ] LLM에 이미지와 레퍼런스를 주고 페이지를 완성한다.






## 회고


1. 
단순히 어떤 기술을 깊고 정확히 이해했는가 보다는, 고객의 입장을 세심하게 고려했는지가 중요하다.
고객의 입장을 생각해 보는 것이 곧 문제의 발견이고 목표와 방향성을 정하는 것이다. 이 부분이 모호하면
이내 열정도 열심도 무엇을 해야하는지도 모두 와해될 것이다.

코치님들의 리뷰에서 이런 점을 생각해 볼 수 있었다.

2.
끝이 없는 주제겠지만, 소통을 잘 해야한다. 더 잘해야 한다. 대화의 주제가 되는 부분도 잘 해야하지만,
우리가 사람이기 때문에 함께 따라오는 것이 많다.

어디선가 보기를, 상대의 마음 속으로 들어가야 한다했다. 내 머리에서만 이해해보려는게 아니라 정말로
사람의 마음 속으로 들어가 보는 것. 무슨말인지 알것도 같지만 세상에도 내 마음속에도 잡음이 많다.

정글에 있는 동안 마주치는 이 많은 사람들의 마음속에 들어가보려는 노력을 하는것이 맞을까? 아니면
피해를 주지 않기 위해 조심하는것만 해도 만만치 않을까?

