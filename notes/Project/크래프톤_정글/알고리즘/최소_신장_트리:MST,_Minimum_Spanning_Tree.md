# 최소 신장 트리:MST, Minimum Spanning Tree

- 가중치 총합이 가장 낮은 Spanning Tree

  > [!NOTE] Spanning Tree
  >
  > - no cycle _(tree니까 당연히)_
  > - connects all nodes _(즉 연결요소가 하나인 것)_
 
  > [!NOTE]
  >
  > - cycle
  >   - 자기 자신에게 돌아오는 것
  > 
  > - tree는 graph의 일종으로, cycle이 없는 것을 뜻 함



- Kruskal's 알고리즘이 가장 유명하다.

  1. edge를 없다고 가정한다.
  2. weight을 기준으로 priority queue에 넣는다.
  3. `while queue:`
     - dequeue
     - cycle detection(by union find)
       - not fount ==> connect 
       - found     ==> skip
