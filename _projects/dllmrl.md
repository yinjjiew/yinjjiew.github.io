---
layout: page
title: dLLM-RL
description: Reinforcement Learning for Diffusion Language Models
img: assets/img/publication_preview/dllmrl_dyn.png
importance: 1
category: RL
math: true
---


Reinforcement learning is an important methodology to improve language models. We propose a comprehensive RL framework for discrete diffusion language models (see [paper](https://arxiv.org/abs/2509.06949)), paired with the most comprehensive post-training [repository](https://github.com/Gen-Verse/dLLM-RL) for dLLMs.

{% include figure.html path="assets/img/dllmrlposterstart.png" title="" class="img-fluid rounded z-depth-1" %}

##### Insights Overview

1. Prior methods omit **trajectory awareness**, leading to a mismatch between optimization and inference. In practice, diffusion language models employ confidence-driven sampling, which differs from the purely random sampling used in image diffusion.
2. Building on this, we propose [TraceRL](https://arxiv.org/abs/2509.06949), a trajectory-aware RL method applicable to both full- and block-attention DLMs. With trajectory shrinkage and sliced block training, we accelerate training.
3. We introduce a diffusion value model to stabilize training via **token-wise variance-reduction** baselines.
4. We open-source a comprehensive post-training repo [dLLM-RL](https://github.com/Gen-Verse/dLLM-RL) for current open-source diffusion language models, including multiple SFT and RL methods, across math, coding, multimodal and RLHF settings, support process reward model and single/multiple nodes training.
5. We also implement a 7B [long-CoT block diffusion model](https://huggingface.co/collections/Gen-Verse/trado-series). Based on our findings, traditional full-attention DLMs cannot handle long-context settings well; block diffusion is likely to become the dominant approach.

##### Trajectory matters for DLM post-training

This is a simple demonstration experiment comparing different SFT methods for DLMs. Fully random masking is the commonly used SFT approach for full-attention DLMs. However, it overlooks the inherent logical structure of language, especially in reasoning tasks. Semi-AR SFT is the typical training method for block diffusion models: it applies random masks within each block while preserving block-wise causality. But applying Semi-AR training to full-attention models is slow because it requires slicing the data. We also explore trace-wise SFT, where a DLM collects its own confidence-driven trajectories and uses them for self-SFT. We find that, under comparable training time, fully random < Semi-AR < trace-wise.

{% include figure.html path="assets/img/demonexp.png" title="" class="img-fluid rounded z-depth-1 w-75 mx-auto d-block" %}

##### TraceRL

{% include figure.html path="assets/img/dllmrl_method.png" title="" class="img-fluid rounded z-depth-1 w-75 mx-auto d-block" %}

Given the above insights, we propose **TraceRL**. For each generated response $\tau_i$ given the task $Q$, we represent it as a trajectory


where the advantages are standardized based on the rewards. TraDo Instruction models are trained solely with RL based on SDAR models, see results below.

{% include figure.html path="assets/img/dllmrl_maintable.png" title="" class="img-fluid rounded z-depth-1 w-75 mx-auto d-block" %}

##### Diffusion Value Model

Diffusion value model provides a token-wise variance-reduction baseline. In our approach, we firstly derive step-wise rewards $r_t^{\star}$ from token-wise rewards $r_j$, and derive step-wise baseline values $V_t^{\star, old}$ from the value model’s token-wise outputs $V_j^{old}$. Specifically,



See the following table, using value model can drop 45.5% variance in training.

{% include figure.html path="assets/img/dllmrl_variance.png" title="" class="img-fluid rounded z-depth-1 w-50 mx-auto d-block" %}

##### Long-CoT Models & Importance of Block Diffusion


We select 75K long-CoT samples from Openthoghts3-1.2M to conduct SFT to TraDo-8B-Instruct model, resulting in TraDp-8B-Thinking model. Note that full-attention models cannot handle long-context settings well, because the prefill pass is extremely computationally expensive for long contexts. We believe the future of diffusion language models lies in block diffusion.


```
@article{wang2025revolutionizing,
  title={Revolutionizing reinforcement learning framework for diffusion large language models},
  author={Wang, Yinjie and Yang, Ling and Li, Bowen and Tian, Ye and Shen, Ke and Wang, Mengdi},
  journal={arXiv preprint arXiv:2509.06949},
  year={2025}
}
```

Some related works on dLLM: [SDAR](https://github.com/JetAstra/SDAR), [LLaDA](https://github.com/ML-GSAI/LLaDA), [Dream](https://github.com/DreamLM/Dream), [block diffusion](https://arxiv.org/abs/2503.09573), [Fast-dLLM](https://github.com/NVlabs/Fast-dLLM/tree/main).











