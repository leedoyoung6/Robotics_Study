# Act Paper Review
Learning Fine-Grained Bimanual Manipulation with Low-Cost Hardware Tony Z. Zhao et al.,2023 (https://arxiv.org/pdf/2304.13705)

## Introduction
- 고가의 로봇이 아닌, 저가형 로봇 하드웨어가 양손 조작을 수행할 수 있는지를 다룸.
- 인간이 손으로 쉽게 수행하는 배터리 끼우기, 컵 뚜껑 열기 같은 작업은 세밀한 로봇 제어가 필요한데, 이것을 저가형 하드웨어와 end - to - end imitation learning으로 수행하고자 한 논문이다.



## Problem 
- 핵심 문제는 fine manipulation에서, 단순히 behavior cloning 방법을 채택하면 compouding error가 발생한다는 것
- 여기서 behavior cloning은 observation Image와 State를 입력으로 받아, action을 그대로 학습하는 것을 의미한다.
- 하지만 만약 정답 action이 a -> b -> c 였는데, 로봇이 잘못된 조작으로 a -> b -> d로 간다면, d는 정답 action에 없기에 학습 자체에 점점 오류가 누적된다. 이것을 compounding error라고 한다.
- 이것을 demonstration distribution에서 벗어났다고 표현하기도 한다.
- 이러한 문제점은 non - stationary, non - markovian한 환경에서 더욱 문제점이 부각된다.
- non - stationary는 행동의 통계가 변하는 것으로, 예를 들어 사과를 집으려고 할 때 0.5초 멈췄다가 잡을지, 바로 잡을지를 알 수 없다.
- non - markovian은 현재 상태만으로 다음 행동이 결정되지 않는 것으로, 지금 사과 근처에 손이 있는데 이것이 사과를 놓고 다시 돌아오는 건지, 아니면 집으러 가는 건지 알 수 없다. 즉, 현재뿐 아니라 과거의 action, state에 대한 정보도 필요하다.
- 이것은 모두 단일 step action을 출력하기에 생기는 문제점들이다.


## Method
<img width="1267" height="336" alt="image" src="https://github.com/user-attachments/assets/e4ebdebb-925b-457b-9b76-005bd9673ce7" />

- act는 위의 문제점을 해결하고자, 단일 step action을 출력하는 게 아닌, K개의 action sequence를 한 번에 출력한다.
- 직관적으로 보면 이 아이디어는, 만약 배터리를 교체하는 Task를 수행한다면, 첫 번째 action chunk가 배터리까지 손을 갖다 대고, 두 번째 action chunk가 기존 배터리를 뺴내고, 세 번째 action chunk가 새로운 배터리를 넣는 것으로 이해할 수 있다.
- act는 Conditional Variational Autoencoder (CVAE) 구조로 학습된다.
- VAE는 어떤 데이터를 latent vector z로 압축했다가 다시 생성하는 모델이다. 직관적으로 encoder가 action chunk를 받아 그 action을 표현하는 z로 압축하면, 그것을 decoder에 넣어 z를 다시 기존 action chunk로 생성해내는 걸 의미한다.
- 여기서 Conditional이 붙은 이유는, observation Image와 state를 조건으로 받아서 action chunk를 예측하기 때문이다.



## Experiments
- 기존 imitation learning과의 성능을 비교
- action chunk size ablation study 수행
- CVAE objective ablation 수행. multimodal한 환경 속에서 행동하는 인간 demonstration에 대한 통찰 기여



## Contribution
- 저가형 하드웨어로 fine manipulation을 수행하는 양손 로봇 신경망 고안
- action chunk 개념 도입 후 transformer 구조, CVAE를 활용해 단일 행동 step의 한계점을 극복하는 아이디어 제시



## Idea
- action chunk의 경계점에서 불연속적인 변화로 성능이 저하하지 않을까?
- action chunk 방법이 오히려 더 오류를 강하게 만드는 경향도 있지 않을까?
- action chunk를 고차원 임베딩에서 language instruction과 공동 학습해 두 모달이 연결됨으로써 더 유용한 의미를 담을 수 있지 않을까?
