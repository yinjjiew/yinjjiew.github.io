---
layout: page
title: OpenClaw-RL
description: Deploy OpenClaw Anywhere, Let Your Language Model Learn From Every Conversation
img: assets/img/publication_preview/openclawrlicon.png
importance: 1
category: RL
math: true
---

What if your personal AI agent could improve just by talking to you?

That is the core idea behind **OpenClaw-RL**. You can deploy **OpenClaw anywhere you want**: on your laptop, on your own server, or in the cloud. Use it however you like. Chat with it, ask it to browse, automate workflows, or help with daily tasks. As long as the model is **hosted by our RL server**, your real interactions can automatically become training signal. The agent does not just respond to you, it **learns from you**. The RL backend continuously collects trajectories, extracts feedback, scores behavior, and updates the model in the background, so the more you use it, the more it adapts to your own habits, preferences, and workflows. Because the full stack is self-hosted, OpenClaw-RL is also **private by design**: your model is deployed by you, your data stays with you, and the optimization loop is driven entirely by your own feedback. We release our [repository](https://github.com/Gen-Verse/OpenClaw-RL).

<video controls preload="metadata" width="100%" class="img-fluid rounded z-depth-1">
  <source src="{{ '/assets/img/openclawrldemo.mp4' | relative_url }}" type="video/mp4">
  Your browser does not support the video tag.
</video>

<br>
<br>

##### Why Fully Asynchronous Matters

{% include figure.html path="assets/img/openclawrlasync.png" title="" class="img-fluid rounded z-depth-1" %}


If a learning system slows down the product, breaks the API, or interrupts the user, it is not ready for real deployment. That is why OpenClaw-RL is built around a **fully asynchronous RL framework** that decouples **OpenClaw**, **policy serving**, **PRM / judge models**, and **training workers**, so they do not block one another. As soon as enough usable trajectories have been collected, training starts automatically in the background, while the live agent continues serving requests as usual. Rollout, scoring, and gradient updates proceed concurrently, which means optimization continuously improves the model without interrupting the actual use of the agent.

<br>
<br>

##### Training Method 1: Binary Reward from User and Environment Feedback

Our first training path converts naturally occurring user or environment feedback into a simple binary process reward. For each main-line turn, the policy model generates a response and records token-level log-probabilities; when the next user or environment message arrives, we treat it as feedback on the previous turn. A **Process Reward Model (PRM)** evaluates the pair multiple times, votes whether the previous response was good, bad, or neutral, and uses the majority vote as the scalar reward for that turn.

$$
r \in \{-1, 0, +1\}
$$

We then broadcast this scalar reward across all response tokens:

$$
A_t = r
$$

The policy is optimized with a PPO-style clipped objective:

$$
\rho_t = \frac{\pi_\theta(a_t \mid s_t)}{\pi_{\text{old}}(a_t \mid s_t)}
$$

$$
\mathcal{L}_{\text{pg}} = -\mathbb{E}_t\left[\min\big(\rho_t A_t,\ \mathrm{clip}(\rho_t, 1-\varepsilon, 1+\varepsilon_{\text{high}}) A_t\big)\right]
$$

and the total loss is

$$
\mathcal{L} = \mathcal{L}_{\text{pg}} + \beta_{\text{KL}} \mathcal{L}_{\text{KL}}.
$$

This signal is easy to collect during normal use and gives dense supervision without any manual annotation, although it remains coarse: it tells the model whether a response was good or bad, but not exactly how to improve. In practice, this method works best when users provide frequent lightweight feedback, such as simple approval or disapproval.

<br>
<br>

##### Training Method 2: On-Policy Distillation with Hindsight Hints

Our second training path extracts **textual hindsight hints** from the next state and turns them into a stronger, more directional training signal. For each main-line turn, the policy model first responds to the original prompt; when the next state arrives, a judge model determines whether it contains useful hindsight, and if so, it extracts a short textual hint describing what the model should have done differently. We then append that hint to the original prompt to form an enhanced prompt, run the original response under this enhanced prompt to obtain a stronger teacher distribution, and train the student policy toward that teacher.

This gives a token-level advantage defined by the teacher-student log-probability gap:

$$
A_t = \log\pi_{\text{teacher}}(a_t \mid s + \text{hint}) - \log\pi_\theta(a_t \mid s)
$$

We use the same PPO-style clipped surrogate:

$$
\rho_t = \frac{\pi_\theta(a_t \mid s_t)}{\pi_{\text{old}}(a_t \mid s_t)}
$$

$$
\mathcal{L}_{\text{pg}} = -\mathbb{E}_t\left[\min\big(\rho_t A_t,\ \mathrm{clip}(\rho_t, 1-\varepsilon, 1+\varepsilon_{\text{high}}) A_t\big)\right]
$$

and the same overall objective:

$$
\mathcal{L} = \mathcal{L}_{\text{pg}} + \beta_{\text{KL}} \mathcal{L}_{\text{KL}}.
$$

Unlike binary rewards, these textual signals are richer and directional: they do not just tell the model that something went wrong, they tell the model what to change. In practice, this method works best when users provide more concrete feedback, such as what the model should or should not do.

<br>
<br>

##### Why Computer-Use Agents Matter

The most important agents are not just chatbots. They are agents that can actually do useful work.

This repository is built to make reinforcement learning practical for **useful computer-use agents** in real deployment. Computer-use agents naturally generate grounded feedback during real execution, which makes them an ideal setting for continuous online optimization. Our roadmap is centered on turning that feedback into better behavior at scale.

##### Roadmap

1. **Personal agent optimization:** keep improving personalized agents that learn directly from their own conversation streams, making them more useful for each individual user over time.
2. **General agent optimization:** extend the same asynchronous RL infrastructure to broader and more scalable agent settings, starting with computer-use agents.
3. **Stronger training signals:** continue improving how language feedback and environment feedback are converted into targeted supervision for long-horizon agent behavior.
4. **More practical deployment:** keep pushing the system toward real-world usability, where agents stay always available while continuing to improve in the background.



