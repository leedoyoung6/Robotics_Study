# Grasping and Manipulation
- chap 12는 robot manipulation을 위해 contack mechanics를 이해하는 장이다.
- manipulation은 단순 grasping 뿐 아니라, pushing, sliding 등 모든 물체 사애 변화 행동을 포함한다
- 핵심 문제는 contact가 물체 motion을 어떻게 제한하는지, 그리고 friction force가 어떻게 전달되는지를 모델링하는 것이다.
- 느린 manipulation에서는 quasistatic assumption을 사용해 ineria를 무시하고, contact force와 gravity force의 balance만 고려한다.
- 이후 manipulation 분석의 기반으로 linear span, positive span, convex span 개념을 도입하며, 특히 friction cone과 grasp stability 분석에 매우 중요하게 사용된다.

<br>

# 12.1.1. First-Order Analysis of a Single Contact
- contact가 강체 motion을 어떻게 제한하는지 분석한다.
- 두 물체 사이 거리의 시간 변화가 양수면 contact가 끊어지고, 음수면 penetration이 발생하므로 허용되지 않는다.
- 따라서 강체 contact 유지 조건은 거리와 그 시간 미분들이 모두 0이어야 한다는 것이다.
- 여기서 가장 중요한 정보는 contact normal이며, 이는 contact surface에 수직인 방향으로 침투 불가능한 방향을 의미한다.
- 이 절에서는 curvature 같은 고차 정보를 무시하고, tangent plane과 contact normal만 사용하는 first order contact만 다룬다.


<br>

  
# 12.1.2. Contact Types: Rolling, Sliding, and Breaking

- contact는 body들의 가능한 twist를 제한하며, 핵심 조검은 penetration이 일어나면 안된다는 impenetrability constraint
이다.
- contact normal 방향 relative velocity가 양수면 contact가 끊어지고, 음수면 penetration이라 불가능하며, 0이면 contact가 유지된다.
- relative velocity가 tangent 방향만 존재하면 sliding contact, 모든 relative velocity가 0이면 rolling contact이다.
- rolling은 sliding보다 더 강한 constraint이며, spatial body에서는 relative twist에 대해 3개의 equality constraint를 만든다.
- 결국 contact mechanics의 핵심은 어떤 twist들이 feasible한가를 contact normal 기반 inequality / equality constraint로 분류하는 것이다.


<br>


# 12.1.3. Multiple Contacts

- multiple contact에서는 각 contact가 feasible twist half-space를 만들고, 전체 feasible motion은 이 half space들의 교집합으로 결정된다.
- 모든 fixture가 stationary면, feasible twist 집합은 polyhedral convex cone이 된다.
  
<img width="252" height="240" alt="image" src="https://github.com/user-attachments/assets/8fac1701-ee0e-4d93-9140-92a20e7918ed" />

- cone 내부 twist는 여러 contact를 동시에 breaking 시키고, con face위 twist는 일부 contact에서 sliding, rolling을 만든다.
- contact mode는 각 contact의 상태를 문자열처럼 합친 것으로, 예를 들어 SB는 한 contact는 sliding, 다른 하나는 breaking이라는 뜻이다.
- 여러 contact constraint의 교집합 결과 feasible twist가 오직 zero twist만 남으면 object는 immobilized되며, 이를 form closure라고 부른다. 

<br>



# 12.1.7. Form Closure
- form closure는 object가 어떤 nonzero twist도 할 수 없어서 완전히 immobilized된 상태를 의미한다.
- first order form closure 조건은 feasible twist가 오직 zero twist만 존재하는 것이며, 이는 contact normal wrench들의 positive span이, 전체 wrench space를 덮는 것과 동치이다.
  - 그래서 planar body는 최소 4개, spatial body는 최소 7개의 point contact가 필요하다.
- contact wrench matrix의 rank가 full rank이고, positive coefficient 조합으로 zero wrench를 만들 수 있으면 first order form closuer이다.
- 중요한 건, first order analysis는 conservative하다는 것이다. 즉, first order에서는 closure가 아니어도, curvature 같은 higher order geometry까지 고려하면 실제로는 closure가 될 수 있다.




<br>


# 12.2.1. Friction

- coulomb friction의 핵심은, 마찰력은 sliding을 방해하는 방향으로 작용한다는 것이다.
- sliding이 없을 때는 마찰력이 특정 값으로 고정되지 않고, 최대 크기인 friction coefficient x normal force 범위 안 어디든 가능하다.
- sliding이 시작되면, 마찰력 크기는 정확히 friction coefficient x normal force가 되고, 방향은 velocity 반대 방향이다.
- 가능한 모든 contact force 집합을 friction cone이라 하며, planar case에서는 cone edge force 들의 positive span으로 표현된다.
- 여러 contact가 있으면 각 contact의 friction cone이 wrench cone으로 변환되고, 이 둘을 합친 composite wrench cone이 object에 전달 가능한 전체 force / moment 공간을 나타낸다.  


<br>

# 12.2.3. Force Closure

- force closure는 contact들의 friction cone이 만드는 wrench들의 positive span이 전체 wrench space를 덮는 상태이다.
  - 즉 어떤 외력, 토크가 와도 대응 wrench를 만들 수 있다.
- 판단 조건은 friction cone edge들로 만든 wrench matrix가 full rank이고, positive coefficient 조합으로 zero wrench를 ㅁ나들 수 있는가이다.
- friction이 0이면 friction cone이 contact normal 하나로 줄어들어서, force closure first order form closuer와 동일해진다.
- friction이 있으면, 더 적은 contact로 closure가 가능해지며, planar는 최소 2 contacts, spatial은 최소 3contacts로 가능하다.
- 실제 grasp에서는 이론적으로 가능한 wrench space와 손가락 actuactor가 실제로 낼 수 있는 힘은 다르므로, force closure여도 충분한 squeezing force가 없으면 grasp가 실패할 수 있다.

<br>


# 12.2.4. Duality of Force and Motion Freedoms

- contact는 breaking, sliding, rolling 세 가지 상태를 가지며, 각 상태마다 motion constraint와 force constraint가 다르게 정해진다.
  - breaking contact에서는 상대 velocity 자유도가 가장 크고, contact force는 반드시 0이어야 한다.
  - sliding contact에서는 normal direction motion은 막히지만, tangential sliding은 가능하고, friction force는 friction cone edge 위에 있어야 한다.
  - rolling contact에서는 상대 velocity가 완전히 0이라 motion constraint가 가장 강하지만, force는 friction cone 내부 아무 방향이나 가능하다.
- 핵심은 motion freedom이 많아질수록 force freedom은 줄어들고, 각 contact가 제공하는 총 constraint수는 항상 일정하다는 점이다.


