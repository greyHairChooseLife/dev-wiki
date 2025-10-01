# union & find: '서로소 집합 찾기'


- cycle 찾기에 사용된다.
- 내 부모 노드의 부모 노드를 찾는다. 재귀적으로!
  - 그러다보면 결국 제일 끝에 조상님을 찾게 된다.
  - 이 원리를 활용하면 한 조상 아래 모인 노드집합을 확인할 수 있다.

- **union find 연산** [reference](https://www.youtube.com/watch?v=DI0e7XcNaTY)

  > **전제: "root는 부모로 자기자신을 갖는다."**
  > 
  > ```py
  > # 초기화 예시: parent = [i for i in range(N+1)]
  > # 0번째는 버리기 위해 위 처럼 
  > 
  > parent = [
  >   1,
  >   2,
  >   3,
  >   ...
  > ]
  > ```
  > 
  > 
  > - `find(x)`: 노드가 속한 집한의 root를 찾는다.
  >
  >   ```py
  >   # node가 root와 같은지 확인한다.
  >   def find(x):
  >       if parent[x] != x:                # 자기자신이 자신의 부모노드가 될 때까지(즉, root노드의 조건) 재귀적으로 시도한다.
  >          return find(parent[x])
  >       return parent[x]
  >   ```
  >   
  >   - **경로 압축 신공: union 연산 및 find 연산을 시간복잡도 거의 O(N)으로 만들어준다.**
  >     ```py
  >     # node가 root와 같은지 확인한다.
  >     def find(x):
  >         if parent[x] != x:
  >            parent[x]  = find(parent[x])   # 여기서 대입 연산자를 통해 계속 부모의 부모를 찾아 대입한다.(재귀)
  >         return parent[x]                  # 최종적으로 return 되는 것은 root 노드로 부모가 대입된 최종본이다.
  >     ```
  >   
  > 
  > - `union(x, y)`: 두 노드가 속한 두 집합을 하나로 합침
  >
  >   ```py
  >   # 두 노드의 root가 다르다면 root를 일치시킨다.
  >   def union(x, y):
  >       rootX = find(x)
  >       rootY = find(y)
  >   
  >       if rootX != rootY:
  >          parent[rootY] = rootX  # 어느쪽으로 해도 상관없다. 다만 조건을 주고 이를 활용할 수 있다.(정렬 등)
  >   ```
  >   
  > 
  > - `same(x, y)`: 두 노드가 동일한 집합에 속하는지(연결요소인지) 확인(`find(x) == find(y)`)
  >
  >   ```py
  >   # 두 노드의 root가 동일한지 확인한다.
  >   def same(x, y):
  >       return find(x) == find(y)
  >   ```
