# 3.2.1 Rotation Matrices 
- frame에는 body frame과 space frame이 존재하고, space frame의 차원에서 body frame의 방향을 표현하고자 한다. 
- 강체 orientation의 공간은 3차원이지만, 회전 행렬에는 9개의 숫자가 있다.
  - 6개의 제약조건이 필요하다.
  - 3개는 각 열벡터가 모두 단위벡터라는 조건이고, 나머지 3개는 임의의 두 열벡터 내적이 0이라는 조건, 즉, 세 벡터가 서로 orthogonal하다는 조건이다.

- 회전 행렬의 조건은 아래 네 가지와 같다.
<img width="838" height="444" alt="image" src="https://github.com/user-attachments/assets/a81ee7b4-69cb-4e27-8752-173a57e84b54" />
  - 1. 회전 행렬의 역행렬은 전치 행렬과 같다.
  - 2. 두 회전 행렬의 행렬곱은 다시 회전 행렬이다.
  - 3. 행렬곱은 결합법칙이 성립하지만, 교환 법칙은 성립하지 않는다.
  - 4. 임의의 3차원 벡터 x에 대해, Rx는 x와 길이가 같다.

<br>

- 회전 행렬의 쓰임에는 세 가지가 있다.
  - orientation을 표현한다.
  - reference frame을 변환한다.
  - frame이나 vector 자체를 변환한다.

- 중요한 건, space frame에서 다른 frame을 표현할 수 있다는 것이다.
- 위의 세 가지 쓰임을 달성하기 위해, 첨자 소거법에 따른 회전 행렬의 곱이 잘 쓰인다. 


<br>

# 3.2.2 Angular Velocities

- R이 frame s에 대한 frame b의 회전 행렬이라 했을 때, b의 각속도는 R의 미분으로 구하는 것으로 이해할 수 있다.
- 단위 회전축에 회전율을 곱하면, 각속도 벡터를 얻을 수 있다.
- 원에 접하는 방향으로 선속도를 구할 수도 있다.
- bracket notation을 활용하면, 두 행렬의 cross product를 계산하는 데에 용이하다.
- 다른 프레임에서도 각속도 벡터를 표현할 수 있다.
- R이 보통 space frame에 대한 body frame의 회전 행렬임을 고려하면, body angular velocity와 spatiatl angular velocity는 아래 사진과 같이 표현될 수 있다.

<img width="1086" height="189" alt="image" src="https://github.com/user-attachments/assets/3902d0de-c266-4a70-bdad-55f09f6c9966" />


<br>


# 3.2.3. Exponential Coordinates of Rotation 
- 어떤 방향이든, 단위축에 회전량을 곱하면 그 방향을 얻을 수 있다.
- rotation matrix의 대안적 표현으로, 3개의 파라미터를 지수 좌표라 부르기도 한다.
- 지수 좌표라 부르는 이유는 선형 방정식과의 관계성 때문이며, 최종 방향을 얻기 위해 초기 방향에 각속도를 적분해야 한다.

<br>

- 두 번째 영상에서는, 행렬 지수함수를 회전하는 강체의 각속도의 적분에 활용하는 법을 배운다.
- 어떤 벡터가 회전축을 기준으로 회전할 때, 그 벡터의 최종 위치를 결정하는 것이 목적이다.
- 이를 위해서, p의 운동을 기술하는 미분 방정식을 적분한다.
- Rodrigues 공식을 배우고, 이 공식을 뒤집는 matrix log 를 배운다.
- matrix exponential이 적분과 비슷하다면, matrix log는 미분과 비슷하다.


<br>


# 3.3.1. Homogeneous Transformation Matrices
- body frame의 configuration을 나타내는 위치 p와 orientation을 나타내는 회전 행렬 R, 이것을 묶어 4x4 homogeneous transformation matrix로 나타낸다.
- 해당 matrix는 rotation matrix와 유사한 성질을 가지며, 마찬가지로 세 가지의 사용법을 가진다.
  - 1. 강체의 configuration을 표현한다.
  - 2. vector, frame의 기준 좌표계를 바꾼다.
  - 3. vector나 frame 자체를 displacement 시킨다.
- transformation이 작용하는 위치에 따라 어느 frame에서 표현된 것인지를 알 수 있다.


<br>


# 3.3.2. Twists (Part 1 of 2)

- transformation matrix는 body frame의 configuration을 space frame에 대해 표현하는 법을 배웠고, body frame의 velocity를 표현해야 한다.
- rotation matix의 시간 미분이 각속도의 표현이 아니었듯, transformation의 시간 미분이 강체의 velocity 표현은 아니다.
- pitch는 축 방향 선속도 / 축 방향 각속도로, 비율이다.
- screw axis는 축 위 한 점, 축 방향 단위 벡터, 그리고 pitch로 표현된다.
- 임의의 선형 속도, 각속도를 가지는 강체는 screw axis를 갖는다. 물체의 순간 운동이 screw axis를 중심으로, twisting 하는 것과 같다.
- screw axis가 body frame에서 표현됐을 경우, 이를 body twist라 하고, space frame에서 표현되면, 이를 spatial twist 라고 한다.
- 이 두 twist는 같은 운동을 기술하는, 서로 다른 좌표계에서의 표현이라고 볼 수 있다.
- twist는 각속도 3 vector, 선속도 3 vector를 표현하는 6vector이다.

- 즉, 강체 속도는 6-vector twist로 표현될 수 있고, twist는 임의의 frame에서 표현될 수 있다.
- 각속도의 matrix representation과 유사하게 twist도 표현할 수 있다.  

<br>


# 3.3.3. Exponential Coordinates of Rigid-Body Motion

- 강체의 일반적인 운동은 회전 + 병진이며, 이는 screw axis와 twist로 표현된다.
- exponential coordinates는 screw axis를 따라 각도 만큼 이동한 강체 motion을 의미한다.
- matrix exponential은 little se(3)의 twist를, transformation matrix로 변환한다.
- 순수 병진, 회전을 포함한 screw motion은 모두 closed - form matrix exponential 해를 가진다.
- robot의 revolute, prismatic, helical 운동학은 모두 screw axis와 matrix exponential 방식으로 표현된다.



<br>



# 3.4. Wrenches

- wrench란 힘과 돌림힘을 하나로 묶은 6 vector이다.
- wrench는 각속도와 선속도를 표현하는 twist에 대응되는 표현이다.
- 돌림힘은 위치와 힘의 cross product로 계산된다.
- wrench도 twist와 같이 frame을 바꿔 표현 가능하며, adjoint transform을 사용한다.
- twist와 wrench의 내적은 일률이고, frame에 무관한 값이다.


## conclusion 
- chap 3는 로봇의 3d motion, velocity, force를 표현하는 수학적인 기반에 대해 다루었다. 
- 회전 행렬, matrix representation, twist, wrench, exponential coordinate 같은 robot의 운동을 다루는 행렬 표현을 위주로 배웠다. 
