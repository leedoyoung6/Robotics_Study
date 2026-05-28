# Velocity Kinematics and Statics
- chap 5의 핵심은, joint velocity와 end effector velocity 사이의 관계를 jacobian으로 표현하는 것이다.
- jacobian의 각 열은, 다른 관절은 고정하고 joint i만 unit speed로 움직였을 때의 end effecor의 속도를 의미한다.
- 2r arm에서 j1과 j2가 서로 독립이면 end - effector가 다양한 방향으로 움직일 수 있지만, 두 벡터가 일직선이 되면 특이점이 발생한다.
  - 특이점에서는 jacobian의 rank가 감소하며, 로봇이 특정 방향으로 움직일 자유도를 잃는다.
- joint velocity 제한은 jacobian을 통해 end effector velocity 영역으로 매핑되고, 이것이 manipulability ellipsoid이다.
  - manipulability ellipsoid는 조작 타원체라는 뜻으로, 매핑된 end effector velocity 공간에서 기하학적으로 표현한 것을 의미한다.
- jacobian은 속도뿐 아니라 힘과 토크 관계도 연결한다.
- 즉, end effector force를 만들기 위해 joint torque를 jacobian으로 변환해 계산한다.
- robot은 잘 움직이는 방향에는 큰 힘을 내기 어렵고, 잘 움ㅈ기이지 못하는 방향에는 큰 힘을 내기 쉽다. 
  - 수학적으로, velocity ellipsoid와 force ellipsoid는 서로 reciprocal 관계이다.
- 특이점에서는 특정 방향 힘에 대해, 매우 큰 저항이 가능하다. 예를 들어 팔을 완전히 편 상태에서는 팔 방향 힘이 관절을 거의 돌리지 않으므로, 적은 토크로 큰 힘을 버틴다.
- 이후 chap5 강의로 들어가면, 단순 velocity나 force 대신 twist와 wrench 기반 jacobian으로 일반화해서 배운다.


<br> 

# 5.1.1. Space Jacobian

- 이번 강의는 end effector velocity를 spatial twist로 표현할 때의 jacobian, 즉, space jacobian ( J_s로 표현) 을 유도한다.
- 이전 강의에서도 봤듯, J_s의 각 열은 해당 joint만 unit speed로 움직이고,  나머지는 0일 때의 spatial twist이다. 
- joint i의 jacobian 열은, 그 joint와 space frame 사이에 있는 관절들의 motion 에만 영향을 준다는 게 중요하다.
  - 예를 들어 J_s3는 joint 1, 2 의 값에는 영향을 받으나, joint 4, 5의 영향을 받지 않는다.
- 따라서 space jacobian은 각 screw axis를, 이전 관절들의 exponential transformation으로 space frame에 옮겨 표현한 형태가 된다.
- 즉 jacobian 열은 단순 미분으로 얻는 것이 아닌, screw axis를 adjoint transform으로 이동시켜 계산한다.
- 중요한 특징은 space jacobian이 end effector frame 선택과는 무관하다는 점이다. 오직 joint screw axes와 현재 관절값 만으로만 결정된다.


<br>

# 5.1.2. Body Jacobian

- 이번 강의에서는 end effector velocity를 body twist로 표현할 때의 jacobian, body jacobian을 유도한다.
- J_s는 자기보다 앞쪽 관절들의 영향을 받았지만, J_b는 자기보다 뒤쪽 ( end effector ) 쪽 관절들의 영향을 받는다.
- 따라서 J_b는 각 screw axxis를 뒤쪽 관절들의 transformation으로 body frame에 맞게 변환하여 계산한다.
- 두 jacobian은 adjoint transform으로 서로 변환된다.
- 결국 두 jacobian은, 같은 robot motion을 서로 다른 좌표계에서 표현한 것일 뿐이다.




<br>

# 5.2. Statics of Open Chains

- 이번 강의에서는 jacobian이 end effector wrench와 joint torque 사이를 연결하는 것을 보인다.
- 로봇이 trajectory를 따라 움직일 떄, 원래 필요한 torque가 존재한다.
  - 그런데 외부에서 end effector에 wrench가 가해지면, 로봇은 이를 상쇄하기 위해 반대 방향 wrench를 생성해야 한다.
  - 이때 필요한 추가 joint torque는 jacobian transpose로 계산된다.
  - end effector power와 motor power가 같아야 하기 때문에, 해당 식은 power conservation에서 나온다
- 따라서 jacobian은 joint velocity -> twist 변환기일 뿐 아니라, 그 transpose는 wrench -> joint torque 변환기 역할도 수행하게 된다.
  - 이 관계는 body frame, space frame jacobian에서 둘 다 동일하게 성립한다.
  - force control 에서는 원하는 힘, 토크를 end effector에 가하기 위해 필요한 torque를 해당 식으로 계산하게 된다.
- 결국 jacobian의 본질은, 운동학적으로는 velocity mapping, 동역학적으로는 force, torque mapping 이라고 말할 수 있다.


<br>

# 5.3. Singularities

- jacobian은 6 x n 행렬이며, joint velocity를 end effector twist로, wrench를 joint torque로 변환한다.
- jacobian rank가 최대치이면 full rank, rank가 감소하면 특이점이라고 한다. 특이점에서는 특정 방향 motion을 잃게 된다.
- n < 6 이면, tall jacobian이며, kinematically deficient robot이다. 즉, 모든 6d motion을 만들 수 없다.
- n = 6 이면, square jacobian이고, 일반적인 6d 강체 motion이 가능한 general purpose manipulatior가 된다.
- n > 6 이면, fat jacobian이며, redundant robot이다. 같은 end effector 모션을, 여러 joint velocity 조합으로 만들 수 있다. 사람의 팔도 redundancy를 가진다.
- singular config에서는 jacobian rank가 감소한다. 예를 들면, 팔을 완전히 펴면 수직 방향 motion만 가능하고 수평 방향 속도를 만들 수 없다.
- singularity 에서는 특정 방향 힘이 관절 토크 없이 기주 구조 그 자체로 지탱될 수 있다.
- 즉 jacobian은 로봇이 어떤 방향으로 잘 움직이고, 어떤 방향에서 약한지를 결정하는 핵심 도구이다.



<br>

# 5.4. Manipulability

- manipulability ellipsoid는 현재 configuration에서, 로봇이 어느 방향으로 잘 움직일 수 있는가를 시각화한 것이다.
<img width="552" height="320" alt="image" src="https://github.com/user-attachments/assets/49ff68b9-2dc1-4658-8d4b-2abf11659b69" />

- joint velocity 공간의 unit sphere가 jacobian을 통해 end effector velocity 공간으로 변환되면, ellipse, ellipsoid가 된다.
- jacobian이 singularity에 가까워질 수록 ellipsoid는 한 방향으로 찌그러지며, singularity에서는 선처럼 붕괴한다.
- manipulability ellipsoid는 velocity capability를 의미하고, force ellipsoid는 forch capability를 의미한다.
  - 두 ellipsoid는 reciprocal 관계이다.
- manipulatibility measure는 얼마나 singurlity에 가까운가를 나타내는 수치이며, 대표적으로 axis ratio를 사용한다.
- isotrapic configuration에서는 모든 방향으로 동일하게 움직이기 쉽고, singularity 근처에서는 특정 방향 capability가 거의 사라진다.
- body jacobian은 angular part와 linear part로 분리할 수 있고, 각각 angular manipulability, linear manipulability를 따로 분석할 수 있다.
- chap 5 전체의 핵심은, jacobian이 로봇의 motion capability, force capability, singularity 구조를 결정하는 중심 객체라는 점이다. 
