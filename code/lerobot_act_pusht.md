# Act - Pusht 

## Training

lerobot-train \
  --dataset.repo_id=lerobot/pusht \
  --policy.type=act \
  --output_dir=outputs/train/act_pusht_test \
  --steps=200 \
  --batch_size=8 \
  --policy.device=cpu

- 위의 명령어로 act policy를 pusht dataset으로 학습한다.
- act는 action chunking transformer로, 하나의 action을 학습하고 출력하는 것이 아니라 transformer model 구조를 활용해  여러 action을 덩어리로 학습하고 출력하는 policy이다.
- pusht는 T자 모양의 블록을, 점 모양의 agent를 사용해 정해진 위치에 밀어넣는 것이다. 



## Training - Log (중요 설정)
  
`type: act`
- act policy를 활용한다.

'chunk_size': 100, 'n_action_steps': 100`
- 한 번에 100개의 action을 예측하고 출력한다.

`vision_backbone: resnet18`, `pretrained_backbone_weights: ImageNet ResNet18`
- ImageNet으로 사전학습된 resnet18로 이미지의 feature를 추출한다.

`use_vae: True`, `latent_dim: 32`, `kl_weight: 10.0` 
- vae, variational autoencoder를 사용한다. z라는 벡터로 압축하고, 디코더 부분에서 이미지, z 벡터, 상태를 입력으로 받아 action chunk를 예측하는 방식이다.
- latent의 차원은 32이고, vector 간에 너무 분산되지 않도록 하는 항을 위한 파라미터를 설정한다.
 
`dim_model: 512`, `n_encoder_layers: 4`, `n_decoder_layers: 1`, `n_heads: 8`
- Transformer의 정보와 VAE의 층수이다.



## Training - Output
<img width="390" height="552" alt="image" src="https://github.com/user-attachments/assets/5d7eb053-8a98-4d41-994a-1b1a959c156a" />

- Training 이후 나온 output들이다.
- config.json에는 모델의 차원, 입력 및 출력 유형, backbone 종류 등 학습된 policy, 즉 act에 대한 정보가 들어있다.
- train_config.json에는 이미지 전처리 방법, 최적화 함수 및 파라미터 등 학습에 필요한 정보들이 담겨있다.
- model.safetensors가 바로 학습된 모델이다. 약 200MB 정도 된다.



## inference

lerobot-eval \
  --policy.path=/home/leedoyoung/robotics/lerobot/outputs/train/act_pusht_test/checkpoints/002000/pretrained_model \
  --env.type=pusht \
  --eval.batch_size=1 \
  --eval.n_episodes=1 \
  --policy.use_amp=false \
  --policy.device=cpu

  - evaluation을 하는 코드이다.



## inference - Result
<img width="618" height="619" alt="image" src="https://github.com/user-attachments/assets/8c536ddb-3e72-43ac-972e-ee080fcb292f" />
<img width="616" height="620" alt="image" src="https://github.com/user-attachments/assets/09823bc2-f07c-4436-9607-bc14df5a0fbe" />

- video 형태로 inference 결과가 출력됐다. 초록색 T자 모양으로 회색 T자를 이동시키는 것이 관건이다.
- 처음 20step정도를 학습했을 때는 agent 역할을 하는 파란색 점이 뚜렷한 목적지가 없이 떠돌았었다.
- 위의 이미지는 5000step을 학습한 것으로, 시작 위치가 상당히 어려웠지만 어떻게든 초록색 목적지로 끌고 가려는 모습, 방향을 맞추려는 모습을 보였다.

