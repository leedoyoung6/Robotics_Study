# 10.1. Overview of Motion Planning
- chap 10은 collision없이 robot을 goal까지 이동시키는, motion planning을 다룬다.
- robot이 움직일 수 있는 전체 configuration 공간을 c-space라하고, obstacle과 충돌하지 않는 영역을 c-free, 충돌 영역을 c-obs라 한다.
- start state부터 goal state까지 연결되는, c-free 내부의 경로를 찾는 문제이다.
- 단순 robot arm뿐 아니라, 자동차처럼 actuator 수가 자유도보다 적은 nonholonomic system도 planning 대상이 된다.
- planner는 단순 geometric path만 찾을 수도, timing, velocity, dynamics까지 포함한 full trajectory를 만들 수도 있다.
- 또한, shortest path, minimum time, minimum energy 같은 최적화 조건을 추가할 수 있다.
- 중요한 개념으로, complete planner, probabilistically complete planner가 있으며, 특히 RRT 같은 sampling based planner는 planning 시간이 충분하면 solution을 찾을 확률이 1에 가까워진다.
- 결국 motion planning의 핵심은, 복잡한 obstacle 환경 속에서 robot dynamics와 constraint를 만족하며, 안전하게 goal까지 도달하는 feasible motion을 찾는 것이다.

<br>


# 10.2.1. C-Space Obstacles

- motion planning 문제를 c - space안의 한 점을 이동시키는 문제로 바라본다.
- 실제 obs는, robot이 충돌하는 모든 configuration의 집합으로 변환되며, 이것이 c-space obstacle이다.
- 따라서 planning은 결국, c-free 내에서, start point와 goal point를 연결하는 경로를 찾는 문제가 된다.
- 중요한 개념은 connected component이다. 두 configuration이 같은 connected component 안에 있어야만, collision - free path가 존재한다.
- 2R robot의 c-space는 겉보기엔 사각형이지만, 실제 topology는 torus라서 좌우, 상하 경계가 연결된다.
- 실제 robot에서는 고차원 c-space obs를 직접 계산하기 어렵기에, 대부분의 planner는 explicit obstacle 대신 collision-checking routine만 사용한다.    
- collision detection은 polygon intersection으로 할 수도 있고, 더 빠르게는 object를 sphere 들의 집합으로 근사해서, sphere center distance만 비교하기도 한다.

<img width="531" height="400" alt="image" src="https://github.com/user-attachments/assets/5b53db6b-b7da-4254-814f-497ae809601f" />

- 모든 obs의 geometry를 정확히 다루는 게 아닌, 매우 빠른 collision checking을 반복 수행하는 것이 중요하다.


<br>

# 10.2.3. Graphs and Trees
- continuous한 c-space를 실제 planning에서는 graph나 tree로 discretize해서 다룬다.
- graph는 node와 edge로 구성되며, motion planning에서는 node가 robot configuration, edge가 가능한 motion path를 의미한다.
- unweighted graph는 모든 edge cost가 동일하고, weighted graph는 거리 시간, 에너지 같은 cost가 edge마다 다르다.
- directed graph에서는 edge 방향이 중요하다. 어떤 경로로는 갈 수 있지만, 반대 방향은 불가능할 수 있다.
  - tree는 특별한 directed graph로, root 하나에서 시작하고, cycle이 존재하지 않는다.
- motion planning에서는 graph 기반 planner와 tree 기반 planner가 매우 중요하며, PRM은 graph 구조, RRT는 tree 구조를 대표적으로 사용한다.
- continuous geometry를 직접 다루는 게 아닌, discretized graph / trre 위에서, path search 문제로 바꾸는 것이다.




<br>

# 10.2.4. Graph Search
- motion planning에서 graph 위 최적 경로 탐색 알고리즘은 A*search를 공부한다.
- A*는 각 node에 대해 지금까지 이동한 실제 비용과 goal까지 남았다고 추정되는 비용을 더해 가장 유망한 경로부터 탐색한다.
- 여기서 heuristic은, optimistic 해야 한다. 즉, 실제 남은 비용보다 절대 크게 추정하면 안된다.
- straight line distance heuristic이 자주 사용되는 이유는 계산이 빠르고 실제 경로보다 항상 짧기 때문이다.
- A*는, goal에 가까워 보이는 방향으로 탐색을 유도하면서도 최적 경로 보장을 유지하는 graph search 방법이라고 정리할 수 있다.


<br>

# 10.3. Complete Path Planners

- roadmap이라는 특별한 graph를 만들면, path가 존재할 때 반드시 찾을 수 있는 complete planner를 만들 수 있다.
- visibility graph는 obstacle corner들을 node로 하고, 서로 직선으로 collision 없이 연결 가능하면 edge를 만드는 roadmap이다.
- start와 goal도 visibility graph에 연결한 뒤 A* search를 수행하면 shortes collision free path를 찾을 수 있다.
- 이런 shortes path는 obstacle 경계를 스치듯 지나가는 특징이 있고, 필요하면 obstacle을 인위적으로 조금 키워 더 안전한 경로를 만든다.
- 하지만 실제 고차원 robotics 문제에서는, 완전한 roadmap 구성 자체가 너무 복잡하기에 practical planner들은 보통 probabilistic completeness 수준만 목표로 한다.

   

<br>

# 10.4. Grid Methods for Motion Planning

- continuous c-space를 uniform grid로 discretize해서, graph search 문제로 바꿀 수 있다.
- 각 grid cell 중심이 node가 되고, 인접한 free cell끼리 edge를 연결한 뒤 A*로 shortest path를 찾는다.
- 이 방법은 resolution complete하다. 즉, 현재 grid resolution 수준에서 path가 존재하면 반드시 찾는다.
- 하지만 자유도가 증가하면 필요한 cell 수가 기하 급수적으로 증가하는 curse of dimensionality 문제가 발생한다.
- 이를 줄이기 위해 넓은 공간은 coarse grid, 복잡한 좁은 공간을 fine grid를 사용하는 quadtree.octree 같은 multi-resolution 방법을 사용한다.


<br>

# 10.5. Sampling Methods for Motion Planning 

- PRM은 random sampling으로 free c-space를 근사하는 probabilistic roadmap planner이다.
- 먼저 collision free configuration 들을 무작위로 sampling해 graph node로 만들고, 가까운 node끼리, straight-line local planner로 연결한다.
- edge가 collision-free이면, graph에 추가되고, 이렇게 만들어진 graph가 free c-space의 구조를 근사한다.
- sample 수가 충분히 많아질 수록 true roadmap에 가까워지므로, solution을 찾을 확률이 100퍼센트에 가까워지는 probabilistically complete planner가 된다.
- PRM은 roadmap을 미리 구축해, 여러 planning query를 빠르게 해결하는 multiple query plnner라는 점이 핵심 특징이다.

- RRT는 arbitrary dynamics를 가진 robot에도 적용 가능한 sampling based motion planner이다.
- 핵심 아이디어는 random sample이 tree를 끌어당기듯이 확장시키며, state sapce를 빠르게 탐색하는 것이다.
- 알고리즘의 흐름은, random state sampling -> 기존 tree에서 nearest node 선택 -> sampled 방향으로 local motion 생성 -> collision free면 tree에 추가이다.
- local planner는 robot dynamics에 맞게 자유롭게 설계 가능하며, 자동차 같은 nonholonomic system도 처리 가능하다.
- 기존 RRT는 빠르게 feasible path를 찾는데 강하지만, optimality는 약하고, 이를 개선한 것이 RRT - star이다.


  
<br>

# 10.6. Virtual Potential Fields
- potential field planning은 robot을 goal로 끌어당기는 attractive potential과 obstacle에서 밀어내는 repulsive potential을 합쳐 motion으로 만드는 방법이다.
- attractive potential은, goal까지의 거리를 기반으로 만들어지며, gradient를 취하면 robot을 goal 방향으로 당기는 force가 된다.
- damping이 없으면 robot은 관성 때문에 진동하며 goal을 지나치므로, damping term을 추가해 에너지를 줄이고 안정적으로 수렴시킨다.
- obstacle 주변에는 repulsive potential을 정의해 강하게 밀어내도록 한다.
- 최종 motion은 attractive force와 repulsive force의 합으로 결정되며, 실시간 게산이 빠르다는 장점이 있다.
- 하지만 local minimum이 핵심 단점이다. 즉, goal이 아닌 곳에서도 힘의 합이 0이 되어서, robot이 갇힐 가능성이 크다.
- 실제 구현에서는 robot 위 control point에 대해 obstacle 과의 거리 기반 repulsive force를 계산하고, jacobian transpose로 이를 joint torque로 변환한다. 


<br>

# 10.7. Nonlinear Optimization
- motion planning을 최적화 문제로 바꾸는 nonlinear optimization 기반 planning이다.
- 목표는 control trajectory와 motion trajectory를 동시에 찾아서, collision 없이 goal에 도달하면서 enery, time 같은 cost를 최소화하는 것이다.
- trajectory가 obstacle과 충돌하면, obstacle distance gradient를 이용해 trajectory를 조금씩 변형하며 constraint를 만족시키도록 수정한다.
- shooting method는 control만 최적화하고 trajectory는 simulation으로 얻는 방식이고, collocation은 trajectory와 control을 동시에 최적화하는 방식이다.
- 이 방법은 gradient 기반 local optimization이라 local minimum에 빠질 수 있으므로, 실제로는 RRT 같은 planner가 만든 초기 경로를 refinement하는 용도로 자주 사용된다. 
