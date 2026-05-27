# Diffusion Policy Paper Review
Diffusion Policy : Visuomotor Policy Learning via Action Diffusion, Cheng Chi et al.,2023 ( https://arxiv.org/pdf/2303.04137v4 )


## Introduction 
- 기존의 Robot Policy들은 observation -> action으로 이어지는 behavior cloning 방식으로 학습을 진행했고, 본 논문에서는 해당 방식의 한계를 다룬다.

- robot policy는 일반적인 supervised learning과 달리, multimodal distribution, 시간에 따른 연속성, 정밀한 조작 등의 특징을 가지고 있다.

- 아래에 간단히 적을 기존 방법들은 유용했지만, 고차원 action sequence, 복잡한 action distribution을 다루는 데에 한계가 있다.

  - scalar regression : observation -> action을 바로 연속값으로 회귀하는 단순한 policy ( Behavior cloning, BC - RNN 등 )

  - Gaussian Mixture : action distribution을 하나의 gaussian 분포가 아닌 여러 개의 gaussian을 사용해서 예측 ( Robomimic 등 )

  - categorical action : 연속적인 action space를 bin으로 나눠, 회귀가 아닌 분류 문제로 바꾸는 것 ( Behavior transformer 등 )

  - implicit policy : action을 직접 출력하는 게 아닌, energy를 최소화한다는 원칙 하에 observation - action 쌍에 맞는 energy function을 학습 ( Implicit behavior cloning )


## Problem
- multimodal action distribution으로, robot은 특정 지점에 도달하기 위해 왼쪽으로 돌아서 갈 수도, 오른쪽으로 돌아서 갈 수도 있다. 기존 behavior cloning 방식에서는 이 두 가지 경우를 학습하다가 애매한 action을 출력할 수 있다.

- 고차원 action sequence를 시간적으로 일관되게 학습하는 것이 어렵다. 즉, 7d action을 16step 예측하려면 112d가 되는데, 이 고차원 공간 속에서 시간적 일관성을 가지고 행동하는 것이 어렵다.

- implicit policy, energy based model은 multimodal 분포를 학습 가능하나, 고차원 action sequence에 의해 정확한 적분이 거의 불가능에 가까워지면서 생기는 문제, 가짜 action은 에너지를 높게 만드는 학습 중 가짜 action에 따라 학습이 불안정해지는 문제가 발생한다. 


## Method 
<img width="2397" height="679" alt="image" src="https://github.com/user-attachments/assets/8cef0e0b-d628-4411-8f2f-c663915fa593" />

<br>

- diffusion policy는 action sequence를 직접 regression 하지 않고, noise action을 denoising하는 과정을 학습한다.
  
- image generation diffusion model이 noise image를 깨끗한 image로 denoising 하듯이, noise action sequence를 denoising해서 실제 action으로 만들게 학습하는 것이다.

- 학습 시에는 demonstration action sequence에 noise를 추가한다. 이후 image observation, noisy action을 입력으로 받고 noise를 예측하는데, loss는 실제 noise와 예측한 noise의 MSE로 계산된다.

- 추론 시에는 gaussian noise action sequence에서 시작하고, 여러 iteration을 돌며 noise를 줄여 최종적인 action sequence를 출력한다.


## Experiment
- Robomimic, Push-T, BlockPush, Franka Kitchen, real-world Push-T, Mug Flip, Sauce Pouring, Sauce Spreading 등 총 12개 task에서 sota보다 성능 향상을 보였다.

- real world에서 다양한 실험을 했고, 높은 성공률을 보였다.

<br>
  
<img width="591" height="461" alt="image" src="https://github.com/user-attachments/assets/85b975c8-677a-4c74-b60e-90e51c515868" />

<br>
<br>

- task를 수행하기 위해 여러 가능한 mode가 있는데, diffusion은 여러 mode를 탐색할 수 있는 가능성을 보였다.

<br>

<img width="775" height="244" alt="image" src="https://github.com/user-attachments/assets/48060a68-5c65-4a08-8548-e5c76e42f90a" />


## Contribution
- robot visuomotor policy를 conditioning denoising process로 보고, diffusion을 활용해 직접 action을 예측하는 것이 아닌 noise를 예측하고 denosing 하는 방법을 제안했다.

- image generation에 자주 활용되던 diffusion을 action sequence를 예측하는 데에 사용하고 image는 생성하지 않음으로써, real time inference가 중요한 robot 환경에 적합한 방법을 제안했다.  

- receding horizon control을 활용해 시간적인 일관성, 그리고 closed - loop 응답성을 얻고자 했다. 즉, 충분한 step의 action을 예측하되, action 중에 새로운 image를 입력받는 idea를 고안했다.


## Idea
- diffusion policy 에서는 receding horizon control을 활용한다. 즉, 좀 더 장기적인 action step을 바라보는 이득과 현재의 이미지를 업데이트하여 활용하는 이득을 함께 취하는 전략이다.

- feature ensemble이나 chain of thought의 아이디어와 비슷하게, 10 step, 20 step, 50 step, 100 step의 action sequence를 예측하고, 조건부로 적절한 현재 action sequence를 이용하는 전략은 어떨까?
- image를 많이 보는 것이 학습에 도움이 되겠지만, 장시간 운용되는 robot을 생각하면 연산량 문제를 무시할 수 없다.

- 이미지를 연산량에 부담이 덜 되는 fps, 화질, px 등으로 처리하다가, image embedding 차원에서 semantic한 의미가 많이 변할 경우에 고품질의 image를 촘촘하게 보는 방법은 어떨까?
