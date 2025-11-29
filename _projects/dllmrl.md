---
layout: page
title: dllm-rl
description: Reinforcement Learning for Diffusion Language Models
img: assets/img/publication_preview/dllmrl_dyn.png
importance: 1
category: AI
---

Reinforcement learning is an important methodology for language models. We propose a comprehensive RL framework for discrete diffusion language models.

##### Insights Overview

1. Prior methods omit trajectory awareness, leading to a mismatch between optimization and inference. In practice, diffusion language models employ confidence-driven sampling, which differs from the purely random sampling used in image diffusion.
2. Building on this, we propose TraceRL, a trajectory-aware RL method applicable to both full- and block-attention DLMs. With trajectory shrinkage and sliced block training, we accelerate training.
3. We introduce a diffusion value model to stabilize training via token-wise variance-reduction baselines.
4. We also implement a 7B long-CoT block diffusion model. Based on our findings, traditional full-attention DLMs cannot handle long-context settings well; block diffusion is likely to become the dominant approach.



