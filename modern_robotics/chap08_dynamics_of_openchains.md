# 8.1. Lagrangian Formulation of Dynamics 
- chap 8의 핵심은 robot dynamics를 관절에 가한 힘과 토크가 로봇의 가속도와 움직임을 어떻게 만드는지 아는 것이다.
- mass matrix는 로봇의 자세에 따라 각 관절이 얼마나 무겁게 느껴지는지를 나타낸다.
- velocity-product term은 로봇이 움직이는 중에 생기는 속도 관련 효과이며, centripetal term과 Coriolis term을 포함한다.
  - centripetal은 회전하는 link가 원운동을 유지하기 위해 필요한 효과이다.
  - coriolis은 여러 관절이 동시에 움직일 때 서로의 운동이 영향을 주며 생기는 coupling effect이다.
- 중요한 점은 관절 가속도가 0이어도, 링크의 질점들은 원운동 중일 수 있으므로, 실제 선형 가속도는 존재할 수 있다는 것이다.
- gravity term은 로봇이 중력 때문에 아래로 처지지 않고 특정 자세를 유지하기 위해 필요한 정적 torque이다.
- 결국 dynamics의 본질은 로봇이 단순히 독립된 관절들의 집합이 아니라, inertia, velocity coupling, gravity가 서로 얽힌 nonlinear coupled system이라는 점이다.


# 8.1.3. Understanding the Mass Matrix
- 이번 강의는 mass matrix가 로봇이 방향마다 얼마나 무겁게 느껴지는지 나타내는 inertia 구조라는 점을 핵심으로 다루고 있다.
- robot kinetic energy는 joint velocity와 mass matrix로 표현된다.
  - mass matrix는 robot configuration에 따라 달라진다. 예를 들어, 팔을 펴면 inertia가 커지고, 접으면 비교적 가볍게 움직인다.
- joint acceleration 공간의 원은 mass matrix를 거치면 torque ellipse로 변환되어, 어떤 방향 acceleration은 더 큰 torque를 요구한다.
- end effector 기준으로 보면, robot은 방향마다 다른 apparent mass를 가진 것처럼 느껴진다. 어떤 방향은 무겁고, 어떤 방향은 가볍다.
- 더 중요한 점은, 일반적으로 applied force 방향과 실제 acceleration 방향이 일치하지 않는다는 것이다. 즉 robot은 단순 point mass처럼 움직이지 않는다.
- dynamics에서는 configuration에 따라 inertia 구조 자체가 변하고, 방향별로 motion response가 달라지는 nonlinear coupled system이라는 점을 유의해야 한다.

# 8.2. Dynamics of a Single Rigid Body 

- 이번 강의는, newton-euler formulation이 rigid body 하나의 운동 방정식을 먼저 세우고, 이를 로봇 링크들에 재귀적으로 적용하는 방식이라는 걸 아는 것이다. 
- Lagrangian 방식은 운동에너지와 위치에너지에서 출발하지만, newton-euler 방식은 각 링크에 직접 힘과 가속도의 관계를 적용한다.
- rigid body의 질량 분포는 center of mass와 inertia matrix로 요약된다.
  - inertia matrix는 물체가 각 축을 중심으로 회전할 때 얼마나 회전하기 어려운지를 나타낸다.
  질량이 어떤 회전축에서 멀리 분포할수록, 그 축에 대한 회전 관성은 커진다.
- principal axes of inertia를 선택하면 inertia matrix가 단순해지고, 회전 운동 방정식도 계산하기 쉬워진다.
- 이후 이 rigid body dynamics를 각 로봇 링크에 적용하면, open chain robot의 inverse dynamics를 효율적으로 계산하는 recursive Newton-euler algorithm으로 이어진다.

# 8.3. Newton-Euler Inverse Dynamics 

- 이번 강의의 핵심은, Newton-Euler inverse dynamics를 robot 전체에 재귀적으로 적용해, 필요한 joint torque를 계산해내는 것이다.
- 먼저 forward iteration을 수행하며, base에서 end effector 방향으로 각 링크의 configuration, twist, acceleration을 차례대로 계산한다.
  - 각 링크의 motion은 이전 링크 motion에 현재 joint motion이 추가되는 방식으로 계산된다.
- 그 다음 backward iteration을 수행하며, end-effector에서 base 방향으로 각 링크에 필요한 wrench와 joint torque를 역으로 계산한다.
  - 각 joint motor는 전체 wrench 중 자신의 screw axis 방향 성분만 실제로 공급하면 되고, 나머지는 기구 구조가 수동적으로 지지한다.
- gravity는 dynamics 안에 자연스럽게 포함된다.
- recursive Newton-Euler algorithm의 핵심은, 각 링크 dynamics를 앞 -> 뒤, 뒤 -> 앞으로 재귀적으로 연결해 robot inverse dynamics를 계산하는 것이다. 

# 8.5. Forward Dynamics of Open Chains

- 이번 강의의 핵심은, inverse dynamics를 여러 번 호출해서 forward dynamics를 계산할 수 있다는 점이다.
- 먼저 모든 joint acceleration을 0으로 두고 inverse dynamics를 계산하면, gravity·ㅊoriolis·end effector wrench 때문에 필요한 torque만 얻어진다.
- 각 관절 acceleration을 하나씩 1로 두고 inverse dynamics를 반복 실행하면, mass matrix의 각 column을 얻을 수 있다.
  - 즉 inverse dynamics를 여러 번 호출하면 robot dynamics equation의 모든 항을 구성할 수 있다.
- 이후 실제로 motor가 가하는 torque를 알고 있으므로, 남은 것은 mass matrix × acceleration = known vector 형태의 방정식을 푸는 것이다.
- 이렇게 얻은 joint acceleration을 시간 적분하면 robot simulation이 가능하다. 즉 acceleration → velocity → position 순으로 업데이트 하게 된다.
- 결국 forward dynamics의 본질은, 현재 상태와 torque가 주어졌을 때, robot이 다음 순간 어떻게 움직일지를 계산하는 것이다.
- 이것이 simulator, physics engine, control의 이론적 핵심 기반이다.

# 8.6. Dynamics in the Task Space
- 지금까지는 dynamics를 joint angle, velocity, torque 기준으로 표현했지만, 이번 강의에서는 end effector motion과 wrench 기준으로, 그러니까 task space에서 표현하게 된다.
  - Jacobian을 이용하면 joint velocity와 end effector twist를 서로 변환할 수 있으므로, dynamics도 end effector 기준으로 다시 쓸 수 있다.
- 이렇게 변환하면 end-effector wrench는 task-space mass matrix × end-effector acceleration + velocity/gravity 관련 항으로 표현할 수 있게 된다.
- 여기서 task space mass matrix는 손끝, end effector를 특정 방향으로 가속시키기 위해 얼마나 큰 힘이 필요한지를 나타낸다.
  - 즉 robot이 손끝 기준에서 방향마다 얼마나 무겁게 느껴지는지를 표현하는 것이다.
- velocity-product term과 gravity term도 더 이상 joint torque 형태가 아니라, end effector wrench 형태로 표현된다.
- robot 전체의 복잡한 joint dynamics를, 사용자가 실제 상호작용하는 손끝 motion과 힘 기준으로 다시 표현하는 것이다.

# 8.7. Constrained Dynamics

- 이번 내용의 핵심은 robot dynamics에 constraint가 있으면, 단순한 open chain dynamics에 constraint force를 추가해야 한다는 점이다.
  - 예를 들어, constraint는 바퀴의 nonholonomic constraint, parallel robot의 loop closure constraint, humanoid의 발 접촉, 로봇팔이 칠판을 지우는 접촉 같은 상황에서 생긴다.
- constraint는 로봇이 특정 방향으로 움직이지 못하게 만들고, 이를 유지하기 위해 constraint force가 작용한다.
- joint torque는 실제 motion을 만드는 torque와 constraint에 맞서는 torque로 나눌 수 있다.
  - constraint에 맞서는 torque는 로봇의 움직임을 바꾸지 않고, 단지 접촉 조건이나 loop 조건을 유지하는 역할만 한다.
- projection matrix는 전체 joint torque 중에서 실제 motion을 만드는 성분만 남기고, constraint에 맞서는 성분을 제거하는 역할을 한다.
- 이 개념은 chap 11의 hybrid motion-force control로 이어지며, 자유롭게 움직일 수 있는 방향에서는 motion을 제어하고, 막힌 방향에서는 force를 제어하는 데 사용된다.


# 8.9. Actuation, Gearing, and Friction

<img width="806" height="520" alt="image" src="https://github.com/user-attachments/assets/95c4ca3f-5a52-40d0-9f4a-a5cee9db2310" />


- 실제 로봇은 motor + gearhead 구조를 많이 사용하며, gear ratio가 robot dynamics를 크게 바꾼다.
- electric motor는 원래 고속-저토크 특성이 강하기 때문에, gearhead를 사용해 속도를 줄이고 torque를 증폭한다.
- gear ratio가 커질수록 motor rotor는 joint보다 훨씬 빠르게 회전하며, 이 때문에 rotor inertia가 크게 증폭되어 보인다.
  - 중요한 점은 rotor 자체 inertia는 작아도, apparent rotor inertia는 gear ratio 제곱에 비례해서 커진다는 것이다.
- 따라서 실제 robot dynamics에서는 link inertia뿐 아니라 motor rotor inertia도 반드시 고려해야 한다.
- gear ratio가 매우 커지면, 각 joint의 apparent inertia가 지배적이 되어 robot dynamics coupling이 약해지고, robot이 거의 독립 joint들의 집합처럼 행동하게 된다.
- 결국 실제 산업용 로봇 dynamics는 motor, gearhead, friction, transmission까지 포함한 actuator dynamics 전체를 고려해야 한다.
