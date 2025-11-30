---
layout: page
title: dllm-rl
description: Reinforcement Learning for Diffusion Language Models
img: assets/img/publication_preview/dllmrl_dyn.png
importance: 1
category: AI
---

Reinforcement learning is an important methodology to improve language models. We propose a comprehensive RL framework for discrete diffusion language models (see [paper](https://arxiv.org/abs/2509.06949)), paired with post-training [repo](https://github.com/Gen-Verse/dLLM-RL).

{% include figure.html path="assets/img/dllmrlposterstart.png" title="" class="img-fluid rounded z-depth-1" %}

##### Insights Overview

1. Prior methods omit **trajectory awareness**, leading to a mismatch between optimization and inference. In practice, diffusion language models employ confidence-driven sampling, which differs from the purely random sampling used in image diffusion.
2. Building on this, we propose [TraceRL](https://arxiv.org/abs/2509.06949), a trajectory-aware RL method applicable to both full- and block-attention DLMs. With trajectory shrinkage and sliced block training, we accelerate training.
3. We introduce a diffusion value model to stabilize training via **token-wise variance-reduction** baselines.
4. We open-source a comprehensive post-training repo [dLLM-RL](https://github.com/Gen-Verse/dLLM-RL) for current open-source diffusion language models, including multiple SFT and RL methods, across math, coding, multimodal and RLHF settings, support process reward model and single/multiple nodes training.
5. We also implement a 7B [long-CoT block diffusion model](https://huggingface.co/collections/Gen-Verse/trado-series). Based on our findings, traditional full-attention DLMs cannot handle long-context settings well; block diffusion is likely to become the dominant approach.

##### Trajectory matters for DLM post-training

This is a simple demonstration experiment comparing different SFT methods for DLMs. Fully random masking is the commonly used SFT approach for full-attention DLMs. However, it overlooks the inherent logical structure of language, especially in reasoning tasks. Semi-AR SFT is the typical training method for block diffusion models: it applies random masks within each block while preserving block-wise causality. But applying Semi-AR training to full-attention models is slow because it requires slicing the data. We also explore trace-wise SFT, where a DLM collects its own confidence-driven trajectories and uses them for self-SFT. We find that, under comparable training time, fully random < Semi-AR < trace-wise.

{% include figure.html path="assets/img/demonexp.png" title="" class="img-fluid rounded z-depth-1 w-60" %}

##### TraceRL

Given the above insight, we now propose TraceRL. For each generated response $\tau_i$ given the task $Q$, we represent it as a trajectory
\[
\tau_i \triangleq \tau_i^{(1)} \cup \cdots \cup \tau_i^{(|\tau_i|)},
\]
where $|\tau_i|$ is the number of decoding steps, and $\tau_i^{(t)}$ is the set of tokens decoded
during the $t$-th step. TraceRL rewards or penalizes the sampling trajectory under policy $\pi_\theta$,
based on the verifiable reward $r_i$ assigned to $\tau_i$. When we use RLVR, the rewards $r_i$ are equivalent to verifiable outcomes. To accelerate training, we aggregate every $s$ neighboring steps. Specifically, we compress the trajectory $\tau_i$ into
\[
\tau_i^{s} \triangleq \tau_i^{s(1)} \cup \cdots \cup \tau_i^{s(|\tau_i^{s}|)},
\]
where
\[
\tau_i^{s(k)} \triangleq \bigcup_{j=s(k-1)+1}^{\min(sk,\,|\tau_i|)} \tau_i^{(j)},
\qquad
|\tau_i^{s}| = \left\lceil \frac{|\tau_i|}{s} \right\rceil.
\]
The policy loss is
\[
J_{\mathrm{policy}}(\theta_p)
=
\mathbb{E}_{Q \sim D_{\mathrm{task}},\, \{\tau_i\}_{i=1}^{G} \sim \pi_{\mathrm{old}}(\cdot \mid Q)}
\left[
\sum_{i=1}^{G}
\sum_{t=1}^{|\tau_i^{s}|}
\sum_{o_k \in \tau_{i,t}^{s}}
\frac{
C_{\epsilon}\!\left(
\frac{\pi_{\theta_p}\!\left(o_k \mid \tau_i^{s}(1{:}(t-1))\right)}
{\pi_{\mathrm{old}}\!\left(o_k \mid \tau_i^{s}(1{:}(t-1))\right)},
A_i
\right)
}{
|\tau_i^{s(t)}|
}
\right]
- \beta\,\mathrm{KL}.
\]





