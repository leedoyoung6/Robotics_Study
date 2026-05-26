# Paper Review
GR00T N1: An Open Foundation Model for GeneralistHumanoid Robots, NVIDIA, 2025 ( https://arxiv.org/pdf/2503.14734 )



## Introduction 
- 휴머노이드 로봇을 위한 범용 foundation model, 즉 RFM을 만들고자 하는 논문이다. 
- 기존 로봇 학습은 특정 robot, task, dataset를 학습해 새로운 환경에서 task를 수행하기는 매우 어렵다. 
- Chatgpt 같은 LLM은 대규모의 인터넷 데이터를 끌어모아서 Foundation model로서의 역할을 수행할 수 있지만, robot policy는 이 정도 규모의 데이터로 학습하는 것이 불가능하다.
- 휴머노이드 로봇이 가뜩이나 자유도가 높은데, 대규모 data로 학습하기 어려우니 general한 policy를 만들기 어렵다는 것이다.
- 그래서 본 논문에서는 real robot trajectory, simulation trajectory, neural - generated video, human video를 모두 학습에 활용하는 data pyramid 아이디어를 제시한다.



## Problem
- 로봇마다 sensor, action dimension 등이 다르기에 다양한 dataset들이 coherent하게 사용할 수가 없다. data island 현상이라고 부른다.
- 휴머노이드의 real data는 teleoperation 등으로 얻을 수 있는데, 이런 data는 매우 비싸고 대규모로 얻기가 어렵다. 즉, real data만으로 general한 robot policy를 만드는 것이 현재로써는 불가능에 가깝다. 
- 로봇은 단지 행동하는 게 아니라, 언어로 지시를 받아서 행동을 해야 한다. 이는 현실 세계의 무한에 가까운 경우의 수를 그나마 한 방향으로 흘려주기 위해 langauge instruction을 사용해야 한다는 것을 의미한다.



## Method 
<img width="826" height="422" alt="image" src="https://github.com/user-attachments/assets/2e185c86-5f1a-4e5a-a73d-0ad83aa762c9" />

- Gr00t는 VLA 모델이며, 입력은 observation image, language instruction, 현재 state, noised action 이고, 출력은 로봇의 motor action이다. 
- 전체 구조는 dual system으로 돼있으며, 첫 번째 구조는 image와 language feature를 추출하는 vlm 모델이고, 두 번째 구조는 그 image, language feature와 state를 입력으로 받아 action을 generation하는 Diffusion Transformer 모델이다.
- VLM은 Gr00t N1에서는 Eagle-2, Gr00t N1.7에서는 Qwen3-vl을 활용한다. 
- VLM은 transformer 기반 모델로 Image와 Text를 입력으로 받아 attention을 하다가, 최종적으로 text를 출력하기 이전 중간 layer 쯤에서 feature를 뽑아서 그걸 DIT에 전달해준다.
- DiT는 vision - language token, robot state embedding, noised action chunk를 입력으로 받아 denoising하여 최종 action을 출력한다.
- 이 VLM과 DiT를 end - to - end로 학습하게 된다.
- Gr00t에서 가장 중요한 특징은 embodiment specific한 state / action encoder, decoder를 두고, 각 embodiment의 state와 action을 모두 공통된 임베딩 공간안에 보낸 다음 처리한 뒤 각 robot에 맞게 출력해낸다는 것이다.
- 이것이 gr00t가 robot general policy로써 활용될 수 있는 이유 중 하나가 된다. 



## Experiment
- RoboCasa, DexMimicGen Cross-Embodiment Suite, GR-1 Tabletop tasks에서 BC - transformer와 Diffusion policy와 비교해 앞선다.
- Real world에서 Fourier GR - 1 휴머노이드 로봇으로 pick-and-place, articulated object manipulation, industrial manipulation, multi-agent coordination task를 수행했다. 
<img width="1320" height="722" alt="image" src="https://github.com/user-attachments/assets/1ec29eff-addd-4140-8d6f-94c67b3e0344" />
[출처 : https://www.fftai.com/products-gr1] 
- video generation model이 만든 neural trajectory로 학습했을 때 성능이 향상함을 보여주면서, real trajectory의 부족을 생성형 인공지능이 만든 trajectory로 보완할 수 있다는 걸 수행했다.  



## Contribution
- Generalist Robot Policy를 구현하기 위해 data pyramid 아이디어 제안, vision - language 기반 action 모델 고안, action / state encoder, decoder에 공통 embedding 차원에서 처리하는 아이디어 제안
- generation model을 활용한 neural trajectory와 latent action codebook / inverse dynamics model을 활용한 action - less video 등 general policy를 위한 dataset training 전략에 기여



## Idea
- Positional embedding은 sin, cos을 활용했었는데, 인간의 행동에는 출력해내는 action chunk가 가능한 안정해지고 인공지능 모델링에 활용 가능한 주기나 규칙성이 없을까?
- action을 generation하는 모듈과 end to end로 학습된 VLM은, 웹 데이터로 학습된 VLM과 뭐가 다를까? 정말로 DIT가 Vision Language token을 어떻게 인식하고 있을까? 그저 Image에 존재하는 language를 크게 attention한 정보일까, 아니면 정말 이미지 속 지시라는 점을 이해할 수 있을까?
- inverse dynamics model이 action - less video에서 labelling해줄 수 있다는 점이 놀랍다. 범용 로보틱스의 시작점은 로봇이 스스로 환경에 라벨링을 하고 지속적으로 학습을 할 수 있을 때라는 말을 들은 적이 있는데, 해당 방법론이 더 발전하면 가능해지지 않을까?
- 수행하는 16 step action chunk는 language instruction과 깊은 관계가 있어야 할 거 같다. 다른 state, image에 있어도 동일한 language instruction을 받은 action chunk는 유사성을 가질 수 있을까? 
