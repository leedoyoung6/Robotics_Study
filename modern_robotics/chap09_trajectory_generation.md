# 9.1 and 9.2. Point-to-Point Trajectories

- 이번 강의의 핵심은, trajectory를 경로 + 시간 스케일링으로 분리해서 생각하는 것이다.
- 경로는 어디를 지나갈 것인가, 시간 스케일링은 얼마나 빠르게 지나갈 것인가를 결정한다.
- 가장 기본적인 trajectory 생성은 cubic polynomial scaling이며, 시작과 끝 속도를 0으로 만든다.
  - 더 부드러운 motion이 필요하면, quintic polynomial scaling을 사용해, 시작과 끝 가속도까지도 0으로 만든다.
- 산업용 로봇에서는 trapezoida velocity profile도 많이 사용한다. 가속 -> 정속 -> 감속의 형태이다.
- jerk ( 가속도의 시간 미분 ) 까지 부드럽게 만들고 싶으면, S - curve profile을 사용한다. 실제 고급 모션제어에서 매우 중요하다.
- 결국 trajectory generation의 본질은, 기하학적 경로와 동적 속도 프로파일을 분리해서 설계하여, 로봇이 부드럽고 안정적으로 움직이게 만드는 것이다.

<br> 

# 9.3. Polynomial Via Point Trajectories

- 이번 강의의 핵심은 단순 start, end trajectory가 아니라, 여러 via point를 통과하도록 trajectory shape 자체를 설계하는 것이다.
  - via point는 특정 시간에 지나가야 하는 robot configuration이며, path의 모양과 속도를 함께 결정한다.
- via point에서 velocity까지 직접 지정하면 trajectory shape를 세밀하게 조절할 수 있지만, acceleration discontinuity가 생길 수 있다.
  - 이를 개선하기 위해 via velocity를 직접 지정하지 않고, 가속도 연속성까지 강제하는 spline - like interpolation을 사용하기도 한다.
- B - spline 방식은 trajectory가 control point를 정확히 지나가지는 않지만, convex hull 내부에 머무르므로 joint limit violation을 막기 쉽다.
- 원하는 style, smoothness, dynamic feasibility를 만족하도록 전체 motion curve를 설계하는 것이, 궤적 생성에서 핵심이다. 

<br> 

# 9.4. Time-Optimal Time Scaling

- 이 절에서는 주어진 path 위에서 actuator torque limit을 만족하며 가장 빠르게 움직이는 time scailing을 찾는 것이 중요하다.
- dynamics를 path parameter 기준으로 바꾸면, 문제는 joint dynamics가 아니라 path와 path의 미분 dynamics 제로 축소된다.
- 각 상태마다 가능한 acceleration 범위가 존재하고, 이게 feasible motion cone을 만든다.
- time - optimal trajectory는, 가능한 계속 최대 acceleration을 사용하다가, 적절한 시점에 최대 deceleration으로 switching 하는 bang - bang control 형태가 된다.
  - 하지만 실제로는 속도 한계 curve 때문에, 단순 가속 -> 감속 만으로는 목표에 도달하지 못할 수도 있다. 특정 속도 이상에서는 actuator가 path를 유지할 수 없기 떄문이다.
  - 이 경우 tangent point를 찾아 최대 가속과 감속 trajectory를 여러 번 switching 하며 이어 붙인다. 
- robot dynamics와 actuator limit을 고려할 때, 어떤 속도 profile이 물리적으로 가능한지를 기하학적으로 이해하고, 그 한계에서 최적 trajectory를 만드는 것이다. 


