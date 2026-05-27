# Open VLA Paper Review
OpenVLA : An Open-Source Vision-Language-Action Model, MooJin Kim et al.,2024 ( https://arxiv.org/pdf/2406.09246 )


## Introduction 
- robot 분야에서 general한 policy를 만들고, 그것을 open source로 공개한 논문이다. 
- 기존 policy는 특정 task나 dataset, robot에 맞춰 학습돼있어 웹 상의 대규모 데이터에 대한 prior, 사전 지식을 갖고 있는 clip, siglip 같은 VLM과 연결해서 action을 출력하고자 시도했다.


## Problem
- 기존 모델, 그리고 성능이 좋은 RT - 2 - x 같은 모델들이 모두 closed 돼있었다는 게 가장 큰 문제이다. 구조, 학습과정 등이 완전히 공개되지 않아서 엔지니어들이 이 모델을 implement 하는 데에 어려움을 겪었다.
- VLA 모델을 새로운 로봇 setup이나 task에 효율적으로 fine tuning하는 방법이 잘 연구되지 않았다. 
- general한 robot policy로써의 역량이 부족하다. unseen background, distractor, object apperance, object position, language instruction 등을 처리하기 위해서는 general한 역량이 필요하지만, policy마다의 학습 task, dataset이 다르고, robotics real dataset은 얻기가 어려워 그것이 어렵다. 



## Method 
<img width="1556" height="623" alt="image" src="https://github.com/user-attachments/assets/ed58989c-cfc1-4923-8b5a-b62cf0b13f38" />

- Openvla는 vla모델로, 입력은 image observation과 language instruction이고, 출력은 7d robot action control이다. 
- dinov2, siglip + llama로 이루어진 pretrained vlm이 action을 출력할 수 있도록 finetuning한다. 
- vision encoder를 dinov2, siglip 두 개를 사용한 이유는, dinov2가 spatial reasoning에 강하고, siglip이 semantic reasoning에 강하기 때문이다. 두 encoder에서 나온 feature를 concat해서 사용하게 된다.
- mlp projector는 vision encoder에서 나온 feature를 llama가 받아들일 수 있는 embedding space로 바꿔준다.
- 이후 image feature와 language token이 함께 llama model에 들어가서, 마치 언어모델이 text를 출력하듯 action을 출력한다.



## Experiment
- widowx robot과 google robot에서 out of the box로 평가된다. 또한, 다양한 task에서 octo, rt-1-x보다 성능이 좋다. 또한 55B인 rt-2-x 와 성능이 비슷하거나, 더 좋다.
<img width="980" height="980" alt="image" src="https://github.com/user-attachments/assets/9eef70cd-b6a5-4d91-be0c-4b5b733c40ea" />
( 출처 : https://www.trossenrobotics.com/widowx-250 )

- franka - tabletop, franka - droid로 finetuning 한 게 diffusion policy보다 좋았다. 특정 single task에서는 diffusion policy가 좋았지만, 여러 object, instruction이 공존하는 task에서는 openvla의 성능이 더 좋았다. general robot policy로써의 가치를 입증했다.
  
- trainable parameter를 약 1.4%로 줄이고 난 이후에도 성능이 비슷한, parameter efficient 기법의 효과를 입증했다. 


## Contribution
- data, weights, code, fine tuning pipeline을 공개해 large robotics policy로의 접근성을 더욱 쉽게 했다.
- Open-X embodiment ( https://robotics-transformer-x.github.io/ ) 의 970k robot episode로 학습했다. 이것은 다양한 robot embodiment, dataset, task를 포함하므로, 이것으로 학습한 모델은 general robot policy가 될 수 있다.
- LoRa ( Low Rank Adapter ) 와 quantization 등 parameter efficent하게 하기 위해 노력했다. hardware인 robot을 조작하기 위해 모델 경량화는 필수적이다.


## Idea
- Image와 Text를 입력으로 받아 action을 출력한다. 이건 인간과 매우 비슷하게 행동을 하는 것이다. 하지만 인간은 이미지와 언어만으로 행동하진 않는다. 예를 들어서 한동안 손에 혈액 순환이 안되게 하다가 어떤 물체를 잡으려고 하면 잘 잡지 못하게 된다.
- 하지만 인간이 수용하는 감각을 모두 데이터로 넣기에는 robotics 자체가 이미 경우의 수가 너무나 많은 task이다. end effector의 위치와 각도가 매우 중요한데, 그것은 무한대에 가깝다. 그리고 제한하지 않는 한 수행해야 할 task가 너무나 많다.
- 수행해야 할 task 마다 robot이 출력해야 할 위치, 각도는 달라진다. 이게 robotics의 학습 난이도를 높이는 주요 원인일 것이다.
- VLM의 loss를 semantic한 관점에서 보면 터무니 없을 때가 있다. '방이 곧 어두워질 것이다'와 '빛이 사라질 것이다'는 같은 semantic에 속하지만, loss 측면에서 보면 완전히 직교한다.
- 얀 르쿤의 JEPA VL에서 이런 내용을 다루며, 이러한 일반적 생성 모델의 특징은 robotics 분야에서 더욱 난이도를 어렵게 만드는 원인이 된다.
- JEPA VLA이 연구중에 있다면 임베딩 차원에서 같은 semantic한 action 들을 통합할 수 있는 구조가 나올 것인데, 그 방향에서도 신경망 한계의 돌파구를 생각해야 한다.
- parameter efficient하게 만들면서 downstream task에 대한 성능도 유지가 되는 걸까? 
