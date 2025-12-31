# 컴포넌트 렌더링: Client Component vs Server Component



> [!lg] Log 2025-12-19
>
> 성능을 위시한 사용자 경험 측면에서 Rendering 과정은 프론트엔드 핵심일 것 같다.



## Client Component


### 1. 클라이언트 컴포넌트 렌더링 흐름 (CSR 기준)

사용자의 관점이 틀린 것은 아니나, "어디서" 일어나는지가 핵심입니다.

```text
[ 서버 (Server) ]             [ 브라우저 (Browser / Client) ]
  |                           |
  |---- JS 번들 파일 전송 --->| [ 1. 작성 단계 ]
  |  (index.js, app.js 등)    |       JSX 코드 (<div>Hello</div>)
                              |           |                                                      
                              |           v
                              | [ 2. 컴파일 단계 (Babel/SWC) ]
                              |   React.createElement(...) 호출로 변환
                              |           |
                              |           v
                              | [ 3. React Element 생성 ]
                              |   순수 자바스크립트 객체 (UI의 가벼운 설명서)
                              |           |
                              |           v
                              | [ 4. Render Phase: Fiber 트리 생성/업데이트 ]
                              |   +---------------------------------------+
                              |   | Fiber Node 생성 (Work-in-Progress)     |
                              |   | - 컴포넌트 상태, Hook, Effect 관리      |
                              |   | - 가상 DOM의 실제 구현체                |
                              |   +---------------------------------------+
                              |           |
                              |           v
                              | [ 5. Reconciliation (재조정/Diffing) ]
                              |   Current Fiber 트리  vs  Work-in-Progress 트리 비교
                              |   (어떤 부분이 변경되었는지 계산)
                              |           |
                              |           v
                              | [ 6. Commit Phase: 실제 DOM 반영 ]
                              |   변경된 부분만 실제 브라우저 DOM에 적용 (appendChild, patch 등)
                              |           |
                              |           v
                              | [ 7. 브라우저 페인팅 ]
                              |   사용자 화면에 출력
                              |
```



### 3. 리액트 엘리먼트 vs 파이버 (클라이언트 메모리 상의 모습)

브라우저 메모리에서 두 객체는 다음과 같은 관계를 가집니다.

````javascript
// 1. 리액트 엘리먼트 (설계도)
// 컴포넌트가 호출될 때마다 매번 새로 생성되는 가벼운 객체
const element = {
  type: 'button',
  props: { className: 'blue', children: 'Click me' },
  // ...
};

// 2. 파이버 (작업 단위 및 상태 저장소)
// 리액트 내부에서 관리되는 오래 지속되는 객체 (상태, 이펙트 등을 보관)
const fiber = {
  type: 'button',
  stateNode: domElement, // 실제 DOM 연결
  pendingProps: { className: 'blue' },
  memoizedState: null,   // useState 등의 상태가 여기 저장됨
  return: parentFiber,   // 트리 구조 연결 (부모)
  child: childFiber,    // 트리 구조 연결 (자식)
  sibling: siblingFiber, // 트리 구조 연결 (형제)
  alternate: currentFiber, // 비교를 위한 이전 파이버 (Double Buffering)
};
````





## Server Component


### 2. 전체 렌더링 및 하이드레이션 흐름

서버에서 RSC Payload와 HTML이 생성되어 클라이언트에서 최종적으로 인터랙티브해지는 과정입니다.

```text
[ SERVER SIDE ]
1. SC Execution  : 서버 컴포넌트 실행 -> RSC Payload 생성
                   (CC는 실행하지 않고 "Placeholder/Reference"만 기록)
2. SSR Phase     : RSC Payload + CC 코드 -> 정적 HTML 생성
3. Response      : [ HTML ] + [ RSC Payload ] + [ JS Instructions ] 전송
       |
       v
[ NETWORK ] ----> (HTML이 먼저 도착하여 브라우저에 즉시 표시: Preview)
       |
       v
[ CLIENT SIDE ]
1. HTML Preview  : 사용자는 화면을 즉시 봄 (단, 버튼 클릭 등은 작동 안 함)
2. Reconciliation: RSC Payload를 읽어 Virtual DOM(Fiber Tree) 구축
                   - SC 결과물은 그대로 반영
                   - CC Reference를 보고 실제 JS 번들 로드 및 컴포넌트 채움
3. Hydration     : 로드된 CC 코드와 JS Instructions를 Fiber Tree에 결합
                   - 이벤트 리스너 부착, 상태(State) 초기화
4. Interactive   : 이제 사용자의 클릭 등에 반응하는 완전한 앱이 됨
```



### 1. RSC Payload의 내부 구조 (상세)

RSC Payload는 단순한 JSON이 아니라, 스트리밍 가능한 텍스트 형식으로 서버 컴포넌트의 결과물과 클라이언트 컴포넌트의 연결 고리를 담고 있습니다.

```text
[ RSC Payload Structure ]
---------------------------------------------------------------------------
ID | Type       | Content (Simplified Example)
---------------------------------------------------------------------------
M1 | Reference  | { id: "/js/ClientComp.js", name: "Button", chunks: [...] }
   |            | -> 클라이언트 컴포넌트의 위치 정보 (Reference)
---------------------------------------------------------------------------
J1 | SC Result  | ["$","div", null, {"children": ["$","@M1", null, {"label":"Click"}]}]
   |            | -> 서버 컴포넌트가 렌더링한 결과 (HTML 태그 + CC Placeholder)
   |            | -> @M1은 위에서 정의된 클라이언트 컴포넌트를 참조
---------------------------------------------------------------------------
P1 | Props      | { "label": "Click", "theme": "dark" }
   |            | -> SC가 CC에게 전달하는 Props 데이터
---------------------------------------------------------------------------
```



### 요약하자면
- **RSC Payload**는 서버 컴포넌트의 "결과물"과 클라이언트 컴포넌트의 "설계도(참조)"를 담은 중간 매개체입니다.
- **Placeholder/Reference**는 "여기에 어떤 클라이언트 컴포넌트가 들어갈 것인가"에 대한 정보입니다.
- **Props**는 서버에서 결정된 데이터를 클라이언트 컴포넌트에게 전달하는 통로입니다.
- **JS Instructions**는 클라이언트 컴포넌트를 어떻게 초기화하고 HTML의 어느 부분에 연결할지에 대한 가이드라인

이 과정에서 클라이언트는 서버로부터 받은 RSC Payload를 바탕으로 기존에 서버가 만든 HTML과 자신의 Virtual DOM을 일치시키는 과정을 거치게 됩니다.
