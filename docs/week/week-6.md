# week-6: Red-Black Tree




> [!nt]
>
> 팀원: 김태성 + 권동하


> [!rf]
> 
> [참고 콤파스](https://jungle-compass.krafton.com/mod/page/view.php?id=6872)
>
> [rbtree 학습내용 정리](https://roll-over-program.tistory.com/17)
> [rbtree simulator](https://www.cs.usfca.edu/~galles/visualization/RedBlack.html)



> [!tdd]
>
> - [x] RB tree 개념 파악하고 팀원간에 스크리닝
>   - [x] 나름대로 개념 파악해서 모이기
> - [x] CSAPP 책 학습 관련 주간 일정 계획
>
> - [x] 팀 레포지토리 괜찮으신가요? 정말로!!?
>   - 이야기해볼 주제
>     - formatter
>     - 개발 환경
>       - OS 환경: 이미 우분투 버전 통일
>       - 도커 vs EC2
>       - 빌드 시스템
> 
> - [x] 종합적인 코어타임 활용 계획
>   - CSAPP
>     - 월/ 상연 (8.1, 8.5)
>     - 화/ 태성
>     - 수/ 동하









## 학습 계획



### CSAPP


- 7 - 1/4/9/15
  - 핵심 키워드

- 8 - 1/5
  - 핵심 키워드

- 9 - all
  - 핵심 키워드



---

> [!lg] 아래는 참고할 레퍼런스

#### 핵심 키워드


- 아래는 예시

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


### 과제: Red-Black Tree



#### 정의

- Red-Black Tree(RBTree)란?

  - 이진 탐색 트리(BST)의 한 종류
  - 트리의 균형을 유지
    - 데이터가 한쪽으로 치우치는 경우에도 **탐색, 삽입, 삭제 연산의 시간 복잡도가 항상 O(log n)으로 유지**
    - **노드의 색상(Red/Black)과 구조적 규칙**을 통해 **트리의 균형 조건**을 엄격하게 관리
    - 삽입 또는 삭제 시 트리의 속성이 깨질 수 있으므로, **색상 변경과 회전(rotate) 연산**을 통해 속성을 복구함


  > [!nt] Red-Black Tree의 5가지 핵심 속성
  >
  > _"균일한 성능을 위해 균형을 유지하는 방법론이다."_
  >
  > 1. 모든 노드는 빨강 또는 검정색
  >    : 색상 정보는 트리의 균형을 유지하는 데 사용
  >
  > 2. 루트 노드는 항상 검정색
  >    : 트리의 최상단에서 균형 조건을 시작
  >
  > 3. 모든 리프(NIL, 즉 자식이 없는 노드)는 검정
  >    : 실제 데이터가 없는 NIL 노드도 균형 계산에 포함
  >
  > 4. 빨강 노드의 자식은 반드시 검정
  >    : 빨강 노드가 연속으로 등장할 수 없으므로, 트리의 높이가 급격히 증가하는 것을 방지
  >    (만약 등장시 균형을 맞추는 작업이 이루어짐)
  >
  > 5. 임의의 노드에서 리프까지 가는 모든 경로에는 동일한 개수의 검정 노드가 존재
  >    : 경로마다 검정 노드의 수가 같으므로, 트리의 균형이 유지됨



#### 균형 유지 방법


- 삽입/삭제 시 위의 5가지 속성이 깨질 수 있으므로, 색상 변경과 좌/우 회전(rotate) 연산을 통해 트리의 균형을 복구함.
- 이러한 과정 덕분에 RBTree는 항상 O(log n) 시간 복잡도를 보장함.

- RBTree 삽입 시 속성 위반이 발생하는 전형적 경우
- 새 노드는 항상 빨강으로 삽입됨. 이로 인해 주로 4번(빨강 노드 연속 금지) 속성이 깨질 수 있음.
- 삽입 후, 부모/삼촌/조부모의 색상과 구조에 따라 아래와 같은 경우가 발생함.

1. **부모가 빨강, 삼촌도 빨강인 경우 (Recoloring Case)**
   - 속성 4(빨강 연속) 위반.
   - **대응 방법:** 부모와 삼촌을 검정으로, 조부모를 빨강으로 색상 변경.  
     이후 조부모를 기준으로 위 과정을 반복(재귀적으로 처리). 만약 조부모가 루트라면 검정으로 변경.

2. **부모가 빨강, 삼촌이 검정(또는 NIL)인 경우 (Rotation Cases)**
   - 속성 4 위반.
   - **대응 방법:** 회전(rotate)과 색상 변경 필요.
     - **삽입 노드가 부모의 오른쪽 자식이고, 부모가 조부모의 왼쪽 자식인 경우 (Left-Right Case):**
       - 부모를 기준으로 좌회전(rotate left) 후, 아래의 Left-Left Case로 변환.
     - **삽입 노드가 부모의 왼쪽 자식이고, 부모가 조부모의 왼쪽 자식인 경우 (Left-Left Case):**
       - 조부모를 기준으로 우회전(rotate right) 후, 부모와 조부모의 색상 교환 (부모를 검정, 조부모를 빨강).
     - **오른쪽 대칭 상황도 동일하게 처리: Right-Left Case (부모를 기준으로 우회전 후 Right-Right로 변환), Right-Right Case (조부모를 기준으로 좌회전 후 색상 교환).**

- **삽입 요약:**
- 삽입 후 위반되는 속성은 주로 "빨강 노드가 연속"되는 경우임.
- 색상 변경(부모/삼촌/조부모)과 회전(좌/우 rotate)을 조합하여 속성을 복구함.
- 이 과정을 루트까지 반복하여 모든 속성이 만족될 때까지 처리함.




- [ ]  RBTree 삭제 시 속성 위반이 발생하는 전형적 경우 추가


#### 구현 예제

```python
class RBNode:
    def __init__(self, key, color=0, left=None, right=None, parent=None):
        self.key = key
        self.color = color  # 0 = RED, 1 = BLACK
        self.left = left
        self.right = right
        self.parent = parent

class RedBlackTree:
    def __init__(self):
        self.NIL = RBNode(None, 1)  # Sentinel NIL node, black
        self.root = self.NIL

    def insert(self, key):
        new_node = RBNode(key, 0, self.NIL, self.NIL, self.NIL)  # New node is red
        parent = None
        current = self.root
        
        while current != self.NIL:
            parent = current
            if key < current.key:
                current = current.left
            else:
                current = current.right
        
        new_node.parent = parent
        if parent is None:
            self.root = new_node
        elif key < parent.key:
            parent.left = new_node
        else:
            parent.right = new_node
        
        if new_node.parent is None:
            new_node.color = 1  # Root must be black
            return
        
        if new_node.parent.parent is None:
            return
        
        self._fix_insert(new_node)
    
    def _fix_insert(self, k):
        while k.parent.color == 0:  # While parent is red
            if k.parent == k.parent.parent.right:
                u = k.parent.parent.left  # Uncle
                if u.color == 0:  # Case 1: Uncle is red
                    u.color = 1
                    k.parent.color = 1
                    k.parent.parent.color = 0
                    k = k.parent.parent
                else:
                    if k == k.parent.left:  # Case 2: k is left child
                        k = k.parent
                        self._right_rotate(k)
                    # Case 3: k is right child
                    k.parent.color = 1
                    k.parent.parent.color = 0
                    self._left_rotate(k.parent.parent)
            else:  # Symmetric for left side
                u = k.parent.parent.right
                if u.color == 0:
                    u.color = 1
                    k.parent.color = 1
                    k.parent.parent.color = 0
                    k = k.parent.parent
                else:
                    if k == k.parent.right:
                        k = k.parent
                        self._left_rotate(k)
                    k.parent.color = 1
                    k.parent.parent.color = 0
                    self._right_rotate(k.parent.parent)
            if k == self.root:
                break
        self.root.color = 1  # Ensure root is black
    
    def _left_rotate(self, x):
        y = x.right
        x.right = y.left
        if y.left != self.NIL:
            y.left.parent = x
        y.parent = x.parent
        if x.parent is None:
            self.root = y
        elif x == x.parent.left:
            x.parent.left = y
        else:
            x.parent.right = y
        y.left = x
        x.parent = y
    
    def _right_rotate(self, x):
        y = x.left
        x.left = y.right
        if y.right != self.NIL:
            y.right.parent = x
        y.parent = x.parent
        if x.parent is None:
            self.root = y
        elif x == x.parent.right:
            x.parent.right = y
        else:
            x.parent.left = y
        y.right = x
        x.parent = y

# Example usage
rbt = RedBlackTree()
rbt.insert(10)
rbt.insert(20)
rbt.insert(30)
print("Tree inserted with keys 10, 20, 30")
```



### Makefile 핵심 요약

> [!rf]
> 
> [Implicit-Rules](https://www.gnu.org/software/make/manual/html_node/Implicit-Rules.html)


#### 예시 Makefile

```Makefile
CC = gcc
CFLAGS = -Wall -g
TARGET = myprogram
SOURCES = main.c utils.c
OBJECTS = $(SOURCES:.c=.o)

# 관습적으로 쓰이는 타겟 이름. 생략하고 사용하는 첫번째 타겟으로들 삼는다.
all: $(TARGET)

# linking 과정
$(TARGET): $(OBJECTS)
	$(CC) $(OBJECTS) -o $(TARGET)

# linking 전단계로 목적 파일을 만든다.
%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@

clean:
	find . -name '*.o' -delete
	rm -f $(TARGET)

.PHONY: all clean
```


#### 구조


##### Basic Terminology

```Makefile
test: test-rbtree
  ./test-rbtree
  valgrind ./test-rbtree
```

- The entire block is a **rule**.

- `test` is the **target** (what the rule builds or acts on).

- `test-rbtree` is a **prerequisite** (or dependency) - _the rule won't run until this is satisfied._

- The _indented lines_ (`./test-rbtree` and `valgrind ./test-rbtree`) are the **recipe** (or commands)
  - The _shell commands_ executed to update the target.
  - `/bin/sh` is used.



##### Execution Order

1. **Explicit rules first**: Make looks for the target in the Makefile's defined rules.
   If found, it uses that rule.

   - e.g., `test:` or `clean:`
   - `Make` **processes dependencies recursively** before executing recipes, ensuring
     prerequisites are up-to-date.


2. **Then implicit rules**: If no explicit rule matches, Make applies built-in implicit rules.

   - e.g., patterns like `%.o: %.c` for compiling C files
   - This can be defined as well; which is called "pattern rule"
   - `Make` **processes dependencies recursively** before executing recipes, ensuring
     prerequisites are up-to-date.



3. **.PHONY role**: `.PHONY` is a declaration that modifies behavior for explicit targets - it
   marks them as non-file targets, **ensuring their recipes always run.** 

   - It doesn't affect the search order; it's checked after finding the rule.
   - Although the listed target is making file, it doesn't check existence of file or
     compare timestamps. It will rebuild anyway.
   - ex)
     ```Makefile
     .PHONY: test
     ```





> [!nt] implicit rule
>
> - 임의로 정의하지 않아도 기본적으로 적용되는 규칙.
> - 확장자명과 timestamps로 작업 내용을 판단한다.
>   - 파일명에 확장자가 없는 경우 linking, `.o`인 경우 목적파일 생성
>   - 생성하려는 타겟 파일과 의존성 파일의 timestamps를 비교. 타겟 파일이 더 old하면 다시 빌드
> - 의존성이 하나만 있다고 가정한다.
>
> - **목적파일 생성 규칙**:
>   ```makefile
>   %.o: %.c
>       $(CC) $(CFLAGS) -c $< -o $@
>   ```
>
>   - ex) `make my.o`:
>     - _(명시적 규칙이 별도로 정의되지 않았다면 암시적 규칙에 따라)_ `my.c`를 찾는다.
>     - 생성하려는 target file(`my.o`)과 의존성(`my.c`)의 timestamps를 비교한다.
>     - 의존성에 최근 변경 있는경우, 컴파일하여 `my.o`를 생성한다.
>
>
> - **링킹 규칙**:
>   ```makefile
>   % : %.o
>       $(CC) $(LDFLAGS) $^ -o $@
>   ```
>
>   - ex) `make my`:
>     - _(명시적 규칙이 별도로 정의되지 않았다면 암시적 규칙에 따라)_ `my.o`를 찾는다.
>     - 생성하려는 target file(`my`)과 의존성(`my.o`)의 timestamps를 비교한다.
>     - 의존성에 최근 변경 있는경우, Linking하여 `my`를 생성한다.



##### etc

- **변수 정의**: 상단에 `CC`, `CFLAGS`, `TARGET` 등 전역 변수 정의.  

  - 서브-Makefile에 자동 상속되지 않으니 필요하면 `export` 사용
  - Avoid spaces around `=` in assignments (e.g., `CC=gcc` not `CC = gcc`).

  - **자동 변수**:  
    - `$@`: 현재 타겟 이름  
    - `$<`: 첫 번째 의존성  
    - `$^`: 모든 의존성 목록

  - **변수 사용**:  
    - `$(CC)`: C 컴파일러(예: gcc)  
    - `$(CFLAGS)`: 컴파일 옵션  
    - 변수는 `$(NAME)` 형태로 참조


- **Target & Dependency**: 
  - Target: 파일 생성, 빌드, 테스트, 청소 등 원하는 작업을 지정
  - Dependency: 타겟을 만들기 전에 필요한 파일이나 타겟. 등록해두면 파일변경을 감지하여
    필요시 자동으로 다시 컴파일됨
  

- **컴파일과 링크 분리**:  
  - `.o` 파일을 먼저 만들고, 마지막에 링크하여 실행파일 생성  
  - 변경된 파일만 다시 컴파일하여 효율적 빌드
  - Linking 가능한 목적파일로 컴파일
    ```Makefile
    %.o: %.c
      $(CC) $(CFLAGS) -c $< -o $@
    ```

    - `-c`: 소스 파일을 오브젝트 파일로만 컴파일(링킹 X)  
    - `$<`: 규칙의 첫 번째 의존성(보통 소스 파일)



- **Common Pitfalls and Advanced Tips**:
  - Use `include` for modular Makefiles (e.g., `include common.mk`).
  - For recursive Make, use `make -C subdir` to build subdirectories.
  - Conditional variables: Use `ifdef` or `ifeq` for cross-platform builds
    (e.g., `ifeq ($(OS),Windows_NT)`).

  - Built-in rules assume standard tools; override if needed
    (e.g., if `CC` is unset, defaults to `cc`).


## (팀)코어타임 기록

### 1차

태성님, 동하님 두 분 모두 워낙 나이스한 분들이라 의견을 나누는데 어려운게 없었다. 근데 내가 뭔가
제안하면 뭔가 보완해서 새롭게 건의한다거나 그런 비판적인 의견이나 반대 의견은 없는것이 아쉽기도
했다.

하지만 걱정할 것 없다. 차차 더 적극적으로 의견을 내 주실것이고, 동시에 적극적으로 받아들일 것이다.
우리 모두가 새로운 사람들의 새로운 방식을 겪어가는 것에 익숙해져 가고 있으니.




### 2차

