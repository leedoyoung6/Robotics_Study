# Inverse Kinematics of Open Chains

- chap 6의 핵심은, 원하는 end effector pose가 주어졌을 때 그것은 만드는 joint 값을 찾는 inverse kinematics에 대해 아는 것이다.
- forward kinematics는 항상 유일하나, inverse kinematics는 해가 여러 개 일 수 있다.
- analytic inverse kinematics는 기하학과 삼각함수를 이용해 closed form 해를 직접 구하는 방식이다.
- 하지만 일반적인 로봇에서는 해석적 해가 존재하지 않을 수 있어서, 수치적 iterative ik를 사용한다.
- 수치적 ik는, 초기 추정값에서 시작해 jacobian 등을 이용해 점점 목표 pose로 수렴한다.
- 최종적으로, inverse kinematics의 본질은 원하는 손 위치나 자세를 얻기 위해, 관절을 얼마나 움직여야 하는가를 아는 것이다.

<br>

# 6.2. Numerical Inverse Kinematics
- 이번 강의는 inverse kinematics를 newton - raptson 수치적 방법으로 푸는 것이다.
  - 즉, 원하는 end - effector pose를 만들 joint 값을 반복적으로 찾아간다.
- 현재 추정값 근처에서 forward kinematics를 테일러 전개로 선형화하면, jacobian이 작은 joint 변화에서 작은 end effector 변화로 연결한다.
- 하지만 실제 로봇에서는 jacobian이 non - square이거나 singular 일 수 있어, inverse 대신 pseudoinverse를 사용한다.
  - 여기서 pseudoinverse는 redundant robot에서는 가장 작은 joint movement 해를 선택하고, singular, deficient robot에서는 오차를 최소화하는 근사해를 준다.
- newton - raphson inverse kinematics는, 현재 pose 오차 계산 -> jacobian으로 corretion 계산 -> joint 업데이트의 반복 과정이다.
- 초기 guess가 중요하고, 실제 robot controller에서는 이전 joint가 다음 초기 guess역할을 해, 실시간 traking에 잘맞는다.

- 6.2의 두 번째 강의에서는 newton - raphson inverse kinematics를, 최소 좌표 대신 transformation matrix를 사용하는 실제 강체 pose 문제로 확장했다.
- 현재 end effecto pose와 목표 pose 사이의 오차 motion을 계산한다.
- 이후 상대 transformation에 matrix log를 취해, 현재 pose를 목표 pose로 보내는 body twist를 얻는다.
- 즉, 이전의 error vector 대신, 이제는 목표까지 가기 위한 twist 자체를 error로 사용한다.
- joint update는 body jacobian pseudoinverse를 사용해 반복적으로 수행한다.
- chap 6의 핵심을 정리하면, jacobian, matrix exponential/ log, pseudoinverse를 이용해 원하는 강체의 pose를 반복적으로 추정하는 수치적 inverse kinematic framework를 만드는 것이었다.
