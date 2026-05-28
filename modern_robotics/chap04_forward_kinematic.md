# 4.1.1. Product of Exponentials Formula in the Space Frame

- chap4의 핵심은 관절 각도가 주어졌을 때, end - effecor frame의 위치와 방향을 구하는 것이다.
- 모든 관절값이 0일 때의 end - effecor configuration이, 로봇의 home position이다.
- 각 관절은 revolute, prismatic 여부와 관계없이 screw axis로 표현된다.
  - 중요한 건 로봇이 home position이 있을 때, space frame을 기준으로 미리 screw axis를 정의한다는 것이다.
  - revolute joint는 회전축 방향과 그 축 때문에 생기는 선속도로 screw axis를 만든다.
  - prismatic joint는 회전 성분이 0, 이동 방향만 가지는 screw axis로 표현된다.
- 각 관절의 움직임은 matrix exponential로 표현된다.
- space frame 기준 screw axis를 쓰면, 최종 자세는 앞에서부터 순서대로 곱하는 product of exponential 공식이 된다 
- 이 방식은, 로봇팔의 모든 관절 운동을 각 관절축에 따른 screw motion의 연속 곱으로 해석하는 방법이다.

<br>

# 4.1.2. Product of Exponentials Formula in the End-Effector Frame
- 해당 영상은 이전 영상에서 배운 space-frame product of exponential 공식을, body frame 기준으로 다시 유도한 것이다.
  - 이전에는 screw axis를 고정된 world frame 기준으로 표현했지만, 이번에는 end - effector frame 기준의 screw axis를 사용한다.
  - 핵심 차이는 transformation을 왼쪽에서 곱하는 게 아닌, 오른쪽에서 곱한다는 것이다.
- 여기서 말하는 screw axis는 robot이 home config에 있을 때, end-effector frame 기준 screw axis이다.
- joint 1은 회전축 방향, 선형 성분은 end - effector 원점 속도로 계산한다.
- prismatic joint는 각 성분이 0이고, 이동 방향 단위벡터만 남는다.
- 중요한 건, 어떤 관절의 screw axis는, 그 관절과 end effector frame 사이에 있는 관절들에 한해서만 영향을 준다는 것이다.
  - 따라서 joint 1의 움직임은 screw axis 2에 영향을 주지 않고, joint 1, 2 의 움직임은 screw axis 3에 영향을 주지 않는다.
- 결국 forward kinematics는 각 joint screw motion의 matrix exponential들을 연속으로 곱해서, 최종 end effector pose를 계산하는 과정이라고 할 수 있다.
- 이후 jacobian, inverse kinematics, dynamics까지 전부 이 screw - axis, exponential framework 위에서 전개된다.



<br>

<img width="957" height="385" alt="image" src="https://github.com/user-attachments/assets/ae8ca90f-d022-434b-a5e4-5946c76e5020" />
