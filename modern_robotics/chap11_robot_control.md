# 11.1. Control System Overview
- chap 11은, robot이 단순히 motion planning만 하는 게 아니라, sensor feedback을 계속 읽으며, actuactor를 실시간으로 수정하는 closed loop system이라는 점이다.
- controller는 encoder, force sensor, vision sensor 등의 정보를 받아, 보통 1kHz 수준으로 control law를 계산하고, 각 motor에 필요한 torque를 전달한다.
  - 실제 motor toque는 전류에 비례하므로, amplifier는 매우 빠른 inner current loop로 원하는 전류를 유지한다.
- 제어 목표는 단순 trajectory tracking 뿐 아니라, force control, hybrid motion-force control, impedance control까지 포함한다. 
  - 예를 들어, hybrid control은 판 위를 움직이는 motion에 판 위를 누르는 force까지 동시에 제어하는 것이다.
- 실제 로봇 시스템의 feedback loop 구조는 아래와 같다. 
  - controller -> amplifier -> motor dynamics -> robot dynamics -> sensor -> controller
- 이후에 chap 11에서는, robot dynamics 위에 feeback control law를 넣어, 원하는 motion, force behavior를 유도하는 걸 배운다.

<br>

# 11.2.1. Error Response
- controller의 목표는 desired motion과 actual motion 사이의 error를 줄이는 것이다. 
- controller는 원하는 joint angle과 현재 joint anlge을 비교해, 그 error를 줄이도록 torque를 생성한다.
  - error dynamics는 이 error가 시간에 따라 어떻게 변하는 지를 나타내는 방정식이다.
- 좋은 con eroller는 initial error가 있어도 빠르게 줄이고, 최종적으로 0에 수렴해야 한다.
- controller의 성능은 주로 steady - state error, overshoot, settling time으로 평가된다.
  - steady - state eror는 충분히 시간이 지난 뒤 남아있는 최종 오차이다.
  - overshoot는 목표보다 지나치게 넘어가는 현상이다.
  - settling time은 error가 최종값 근처로 들어온 뒤 다시 크게 벗어나지 않게 되는데 걸리는 시간이다.


<br>

# 11.2.2. Linear Error Dynamics

- controller의 성능은 error dynamics의 eigenvalue 구조로 이해할 수 있다.
- mass - spring - damper 시스템처럼, stiffness가 error를 줄이고, damping이 overshoot를 줄이는 역할을 한다.
- controller를 설계하는 것은, 원하는 error dynamics differential equation을 설계하는 것과 같다.
- 고차 미분방정식도 state vector을 정의하면, 어러 개의 1차 방정식으로 바꿀 수 있고, 이걸 matrix 형태로 표현한다.
- 이 system matrix의 eigenvalue가 error response를 결정한다.


<br>

# 11.2.2.1. First-Order Error Dynamics

- 가장 단순한 stable controller의 error dynamics가 exponential decay 형태를 가진다.
- first order에서는 error가 시간에 따라 부드럽게 감소하며, overshoot나 oscilation이 없다.
- 여기서 시간 상수는, error가 얼마나 빨리 줄어드는 지를 결정하는 핵심 parameter이다. 
- stiffness가 커지거나 damping이 작아지면 error 감소 속도가 빨라지고, settling time도 짧아진다. 
  - 반대로 time constant가 음수가 되면, system은 unstable 해지고, error가 점점 커지게 된다.
- first order stable system에서는 steady state error가 0이고, response가 단조롭게 감소한다. 




<br>

# 11.2.2.2. Second-Order Error Dynamics

- second order control system의 response가 damping ratio, natural frequency에 의해 결정된다.
  - natural frequency는 error를 얼마나 빠르게 줄일 지 결정하고, damping ratio는 overshoot, oscillation 정도를 결정한다.
    - damping ratio > 1 이면 overdamped 상태이며, 진동없이 천천히 수렴한다.
    - damping ratio = 1 이면 critically damped 상태이며, overshoot없이 빠르게 목표에 도달한다.
    - damping ratio < 1 이면 underdamped 상태이며, error가 진동하면서 감소한다.


<br>

# 11.3. Motion Control with Velocity Inputs

- velocity - control robot에서 p control의 한계를 해결하기 위해 pl control과 feedforward control을 도입했다. 
  - p control만 사용하면, constant velocity trajectory를 따라갈 때 항상 steady state lag가 남는다. robot은 error가 있어야 움직이기 때문이다.
  - pl control은 error를 시간에 대해 누적한 적분 항을 추가하여, error가 0이어도 계속 motion command를 만들 수 있게 한다.
- proportional gain은 damping 역할을 하고, integral gain은 spring stiffness 역할을 하며, 그에 따라 response가 결정된다.
- 일반적으로는 critically damped tuning이 가장 좋다고 한다. 빠르게 수렴하며, overshoot와 oscillation이 없기 떄문.
- 여기에 desired velocity를 미리 넣는 feedforward term을 추가시, robot은 error가 생기기 전에 먼저 움직일 수 있다.
- 이후 이 개념은 task space control에도 확장되고, end effector pose error를 twist 형태로 정의해, jacobian 기반으로 제어하게 된다.



<br>

# 11.4. Motion Control with Torque or Force Inputs 

- torque, control robot에서 PID feedback만 쓰는 것보다, robot dynamics model을 함께 사용하는 computed torque control의 성능이 훨씬 좋다.
- PD control은 gravity가 없으면 steady state error를 제거할 수 있으나, gravity가 있으면 일정 error가 남아야 중력을 버틸 torque가 생성된다.
- PID control은 intergral term이 과거 error를 계속 누적해, error가 0이어도 torque를 유지할 수 있다.
  - 하지만 intergral term이 너무 커지면, root가 right half plane으로 이동해 overshoot, oscillation, instability가 발생할 수 있다.
- computed torque control은 dynamics model을 이용해 원래 필요한 torque를 미리 계산한다.
- feedforward dynamics compensation + feedback stabilization을 동시에 수행하는 것이다. 
- model이 충분히 정확하면 dynamics가 거의 linearized 되어 매우 좋은 trajectory traking이 가능하며, 실제 산업용 robot control의 핵심 기반 중 하나이다.



<br>

# 11.5. Force Control

- force control은 환경에 가하는 힘과 토크 자체를 제어하는 것이다. 위치와 속도를 제어하는 motion control과는 다르다.
- robot이 물체를 누르거나, 조립하거나, 벽을 밀 때는, 정확한 위치보다 원하는 wrench를 만드는 것이 더 중요하다.
- 가장 단순한 force control은 gravity compensation과 jacobian transpose를 이용해, 원하는 end - effector의 wrench를 joint의 torque로 변환하는 방식이다.
  - 이 경우, robot은 현재 자세에서 원하는 힘을 만들기 위해 필요한 torque를 계산해서, 관절에 가한다.
- 더 정확한 force control을 위해선, wrist force-torque sensor를 사용해, 실제 wrench를 측정하고, desired wrench와의 error를 feedback control한다.
- 일반적으로는 PI control을 사용하고, derivative control은 force sensor noise를 크게 증폭시켜 거의 사용하지 않는다.


<br>

# 11.6. Hybrid Motion-Force Control

- robot이 환경과 접촉할 떄, 어떤 방향은 motion을 제어하고, 다른 방향은 force를 제어해야 한다. 이것이 hybrid control이다.
  - 예를 들어 문 손잡이를 잡으면, 손잡이는 힌지 방향으로만 움직일 수 있고, 나머지 방향은 constraint가 걸린다.
  - 화이트보드 지우개 예시에서는, x-y 방향 motion은 자유롭게 제어하지만, z 방향은 보드에 누르는 force를 제어한다.

<img width="554" height="358" alt="image" src="https://github.com/user-attachments/assets/ebcfdbac-98fe-49ce-8d0e-e46ce514adee" />

- 즉 environment constraint가 6차원 wrench space를 motion으로 만드는 방향과 constraint force 방향으로 분리한다.
- projection matrix는 wrench를 이 두 공간으로 분해하는 역할을 한다. motion에 기여하는 부분과, constraint를 누르는 부분을 분리하는 것이다.
- hybrid motion - force control은, motion controller 출력에서는 constraint 방향 성분을 제거하고, force controller 출력에서는 motion 방향 성분을 제거한 뒤 둘을 합친다.
- robot이 환경과 접촉할 때 움직여야 하는 방향과 힘을 가해야하는 방향을 동시에 독립적으로 제어하는 것이다. 
