# Diffusion Policy : Inference & Simulation

- Diffusion Policy pretrained model을 로컬에서 실행하고, robomimic, robosuite, mujoco 기반 lift task simulation으로 결과를 확인한 기록물이다.
- 목표는 image observation을 조건으로 하여 diffusion 방식으로 생성되는 그 action을 simulation으로 확인하는 것이었다.

## Process

1. conda_environment.yaml로 robomimic, robosuite, mujoco_py, diffusers 등 필요한 기초 의존성을 설치했다.

2. lift_image_abs config가 정상적으로 조합되는지 확인했다.

```bash
python train.py --config-name=train_diffusion_transformer_hybrid_workspace task=lift_image_abs --cfg job
```

3. diffusion policy 공식 서버에서 lift_image_transformer pretrained checkpoint를 다운로드했다. 파일 크기는 약 650MB였다.

```bash
wget -O data/checkpoints/lift_image_transformer/latest.ckpt \
https://diffusion-policy.cs.columbia.edu/data/experiments/image/lift_ph/diffusion_policy_transformer/train_0/checkpoints/latest.ckpt
```

4. diffusion policy의 전체 robomimic image dataset은 약 72GB 이상이라 부담이 컸다. 그래서 robomimic downloader를 사용해 Lift`task의 ph (proficient human) image dataset만 다운 받았다.

```bash
python -m robomimic.scripts.download_datasets \
  --tasks lift \
  --dataset_types ph \
  --hdf5_types image \
  --download_dir data/robomimic/datasets
```

6. 받은 dataset의 action은 7D 였고, checkpoint는 image_abs.hdf5를 요구했다. 그래서 repo의 변환 스크립트를 사용해 absolute action dataset으로 변환했다.

```bash
python diffusion_policy/scripts/robomimic_dataset_conversion.py \
  -i data/robomimic/datasets/lift/ph/image.hdf5 \
  -o data/robomimic/datasets/lift/ph/image_abs.hdf5 \
  -n 4
```

7. cpu 노트북 환경인데, 기본 eval 설정 n_envs=28이라 wsl이 멈추는 문제가 발생했다. eval.py를 수정해 eval 환경 수를 줄였다.

```python
cfg.task.env_runner.n_envs = 1
cfg.task.env_runner.n_test = 1
cfg.task.env_runner.n_test_vis = 1
cfg.task.env_runner.n_train = 0
cfg.task.env_runner.n_train_vis = 0
```

8. 최종적으로 pretrained checkpoint를 사용해 eval을 실행했다.

```bash
python eval.py \
  --checkpoint data/checkpoints/lift_image_transformer/latest.ckpt \
  --output_dir data/eval/lift_image_transformer_test_small \
  --device cpu
```

## Result

```json
{
  "test/mean_score": 1.0,
  "test/sim_max_reward_4300000": 1.0,
  "test/sim_video_4300000": "data/eval/lift_image_transformer_test_small/media/514nnjtz.mp4"
}
```

- mean_score = 1.0은 이번에 실행한 1개 lift simulation rollout에서 성공했다는 뜻이다.
- 전체 성능 평가라기보다는, pretrained model inference와 simulation 연결이 정상적으로 작동했음을 확인한 결과이다.

## Output Video

https://github.com/user-attachments/assets/9c76afc9-cd7c-4a0d-bd8b-7345a695f4b8

## 배운 점

- mujoco, robomimic, robosuite에 대한 정확한 차이와 그것들을 설치하고 import하는 과정

- hdf와 robotic dataset이 hdf 로 이루어진 이유

- hydra 와 policy, task에 둘 다 맞는 환경을 구축하는 방법

- dx, dy, dz, roll, pitch, yaw, gripper로 이루어진 7d action 차원을, 절대적 좌표와 6D rotation으로 바꾸는 10D action의 존재
  - 6d rotation의 장점

- 그리고 7D action을 10D action으로 바꾸는 conversion.py의 유용성을 체감 


