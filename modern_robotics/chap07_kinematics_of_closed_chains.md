# Kinematics of Closed Chains

- chap 7의 핵심은, closed chain robot의 kinematics와 statics는 open - chain보다 훨씬 복잡하다는 점이다.
- 대표 예시는 parallelogram arm, delta robot, stewart platform이며, 특히 delta, stewart는 parallel robot이다.

<img width="428" height="579" alt="image" src="https://github.com/user-attachments/assets/6f4caa48-177e-465b-86e0-e262aea5e790" />
(delta robot 사진)

- parallel robot은 여러 다리가 동시에 platform을 지지해, workspace는 작지만, 매우 강한 힘을 낼 수 있다.
- open chain은 fk가 쉽고 ik는 어려웠지만, parallel robot은 반대로 ik는 쉽고 fk가 매우 어렵다.
- closed - chain robot은 open chain에 없는 다양한 singularity를 가진다. constraint jacobian rank 감소 때문에 motion ambiguity가 생길 수 있다.
- 특히 parallel mechanism에서는 같은 actuator 값에 대해 여러 end - effector configuration이 존재할 수 있다.
