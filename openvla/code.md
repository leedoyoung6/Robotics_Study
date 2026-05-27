# OpenVLA : Core code analyze

repo : https://github.com/openvla/openvla



## /openvla/vla-scripts/train.py
vla: VLAConfig = field(
        default_factory=VLAConfig.get_choice_class(VLARegistry.DINOSIGLIP_224PX_MX_OXE_MAGIC_SOUP_PLUS.vla_id)
    )

- 어떤 config로 vla를 학습할 것인가를 정하는 코드이다.
- DINO + Siglib을 vision encoder로 사용하고, 이미지는 224px을 받으며, open - x data mixture를 사용한다는 설정이다.



if cfg.pretrained_checkpoint is not None:
    vlm = load_vla(cfg.pretrained_checkpoint, hf_token=hf_token, load_for_training=True)
else:
    vlm = load(cfg.vla.base_vlm, hf_token=hf_token, load_for_training=True)

- 모델을 불러오는 코드이다.
- pretrained된 VLM, Prismatic vlm을 backbone으로 사용해 action을 출력하게끔 finetuning해 사용한다.



vla_dataset, action_tokenizer, collator = get_vla_dataset_and_collator(
        cfg.data_root_dir,
        cfg.vla.data_mix,
        image_transform=vlm.vision_backbone.get_image_transform(),
        tokenizer=vlm.llm_backbone.get_tokenizer(),
        prompt_builder_fn=vlm.llm_backbone.prompt_builder_fn,
        default_image_resolution=vlm.vision_backbone.default_image_resolution,
        shuffle_buffer_size=cfg.vla.shuffle_buffer_size,
        image_aug=cfg.image_aug,
    )

- open-x embodiment data mixture를, vla가 학습할 수 있는 형태로 바꾸는 코드이다.
- 여기서 image, language, action을 llm이 token으로 학습할 수 있도록 하는 tokenizer, collater, image transform 등이 만들어진다.



train_strategy.run_vla_training(
        vla_dataset,
        collator,
        action_tokenizer,
        metrics,
        save_interval=cfg.save_interval,
    )

- 학습을 수행하는 코드이다. 
- image, language를 입력으로 받고, 다음 action을 예측하는 방식으로 학습이 진행된다.





## /openvla/prismatic/vla/action_tokenizer.py

self.bins = np.linspace(min_action, max_action, self.n_bins)
self.bin_centers = (self.bins[:-1] + self.bins[1:]) / 2.0

- action의 범위를 256개의 bin으로 쪼개어 연속적인 action을 이산화한다. llm token으로 바꾸기 위한 준비를 하는 것이다.
- bin center는 다시 연속적인 값으로 변환할 때 사용된다.


self.action_token_begin_idx: int = int(self.tokenizer.vocab_size - (self.n_bins + 1))

- llm의 기존 token vocabulary에서 마지막 부분을 사용하게 된다.
- 즉, 잘 안쓰는 token을 action 값과 대응되게 하는 것이다.


action = np.clip(action, a_min=float(self.min_action), a_max=float(self.max_action))
discretized_action = np.digitize(action, self.bins)

- action값을 최소부터 최대값으로 clip하고, 그 연속적인 action값이 어떤 이산화된 값에 속하는지 구하는 코드이다.
- 예를 들어 0.37, 0.82라는 action값이 있다면, 그것을 이산화된 값으로 바꿔 tokenize하기 위해 준비하는 것이다.  


return self.tokenizer.decode(list(self.tokenizer.vocab_size - discretized_action))

- 이산화된 action값을 vocab 마지막 token id로 변환하고, llm이 이해할 수 있는 string, sequnce로 mapping된 값을 return하는 코드이다.
- 즉, 잘 안쓰던 마지막 부분 token id에 매핑돼있던 문자열로 변환되어 llm에 입력되게 되는 것이다.
- openvla는 [dx, dy, dz, droll, dpitch, dyaw, gripper] 이렇게 7d action이 있고, 각 action마다 값을 이산화하고 mapping한다.


discretized_actions = self.tokenizer.vocab_size - action_token_ids
discretized_actions = np.clip(discretized_actions - 1, a_min=0, a_max=self.bin_centers.shape[0] - 1)
return self.bin_centers[discretized_actions]

- model이 출력한 token id를 연속적인 action vector로 바꾸는 코드이다.
- inference로 llm이 token id를 출력하면, robot이 수행할 수 있는 action으로 다시 변환해주는 것이다.



## Idea 
- 차원을 256으로 나누고, dx, dy, dz 모두 같은 token id space를 공유하는 것은, llm의 voca를 사용하기 위한 engineering strategy에 가깝다.
- 이 아이디어는 정말 천재적이고 재미있고 놀라웠다. VLM에서 special token을 이런 식으로 mapping하는 건 봤지만, action을 이런 식으로 mapping해 작동할 수 있을지 몰랐다.
- 하지만 서로 다른 action dimension이 정해진 형태로 llm에 입력된다고 해도, 아예 같은 token id, mapped string을 사용하게 되는 것은 학습에 혼동이 있을 거 같다.
- 이러한 방법에 문제의식을 갖고 FAST: Efficient Action Tokenization for Vision-Language-Action Models, VQ-VLA 등의 논문이 나왔다. 향후 공부해봐야겠다.
