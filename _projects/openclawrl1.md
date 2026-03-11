---
layout: page
title: OpenClaw-RL1
description: Deploy OpenClaw Anywhere, Let Your Language Model Learn From Every Conversation
img: assets/img/publication_preview/openclawrlicon.png
importance: 2
category: RL
math: true
---

What if your personal AI agent could improve just by talking to you?

That is the core idea behind **OpenClaw-RL**. You can deploy **OpenClaw anywhere you want**: on your laptop, on your own server, or in the cloud. Use it however you like. Chat with it, ask it to browse, automate workflows, or help with daily tasks. As long as the model is **hosted by our RL server**, your real interactions can become training signal. The agent does not just respond to you, it **learns from you**. The RL backend continuously collects trajectories, extracts feedback, scores behavior, and updates the model in the background, so the more you use it, the more it adapts to your own habits and preferences. Because the full stack is self-hosted, OpenClaw-RL is also **private by design**: your model is deployed by you, your data stays with you, and the optimization loop is driven entirely by your feedback. [repository](https://github.com/Gen-Verse/OpenClaw-RL) is here.

<video controls preload="metadata" width="100%" class="img-fluid rounded z-depth-1">
  <source src="{{ '/assets/img/openclawrldemo.mp4' | relative_url }}" type="video/mp4">
  Your browser does not support the video tag.
</video>

<br>
<br>

##### Why Fully Asynchronous

{% include figure.html path="assets/img/openclawrlasync.png" title="" class="img-fluid rounded z-depth-1 w-75 mx-auto d-block" %}

You don't want a learning system slows down the product, breaks the API, or interrupts the user. That is why OpenClaw-RL is built around a **fully asynchronous RL framework** that decouples **OpenClaw**, **policy serving**, **PRM / judge models**, and **training workers**, so they do not block one another. As soon as enough usable trajectories have been collected, training starts automatically in the background, while the live agent continues serving requests as usual. Rollout, scoring, and gradient updates proceed concurrently, which means optimization continuously improves the model without interrupting the actual use of the agent.

<br>
<br>

##### Training Method 1: Binary Reward from User and Environment Feedback

Our first training path converts naturally occurring user or environment feedback into a simple binary process reward. For each main-line turn, the policy model generates a response and records token-level log-probabilities; when the next user or environment message arrives, we treat it as feedback on the previous turn. A Process Reward Model (PRM) evaluates the pair multiple times, votes whether the previous response was good, bad, or neutral, and uses the majority vote as the scalar reward for that turn.

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

{% include figure.html path="assets/img/openclawrl2method.png" title="" class="img-fluid rounded z-depth-1 w-60 mx-auto d-block" %}

Our second training path extracts **textual hindsight hints** from the next state and turns them into a stronger, more directional training signal. For each main-line turn, the policy model first responds to the original prompt; when the next state arrives, a judge model determines whether it contains useful hindsight, and if so, it extracts a short textual hint describing what the model should have done differently. We then append that hint to the original prompt to form an enhanced prompt, run the original response under this enhanced prompt to obtain a stronger teacher distribution, and train the student policy toward that teacher.

This gives a token-level advantage defined by the teacher-student log-probability gap:

$$
A_t = \log\pi_{\text{teacher}}(a_t \mid s + \text{hint}) - \log\pi_\theta(a_t \mid s)
$$

We use the same PPO-style clipped surrogate. Unlike binary rewards, these textual signals are richer and directional: they do not just tell the model that something went wrong, they tell the model what to change. In practice, this method works best when users provide more concrete feedback, such as what the model should or should not do.

<br>
<br>


<br>
<br>

##### Why Combine Binary Reward and OPD

Binary reward and on-policy distillation solve **different parts of the same learning problem**.

Binary reward is broad and cheap. It can turn almost any user or environment reaction into training signal, even when the feedback is short or implicit. If a user simply asks the same thing again, rejects an answer, or changes direction, that already tells the model something about whether the previous response was useful. This makes binary reward easy to collect and naturally dense.

On-policy distillation, by contrast, is more selective but much richer. When the next state contains a concrete correction, such as what the model should have checked, avoided, or explained differently, we can extract that hindsight information and turn it into token-level supervision. This gives the model directional guidance, not just a good-or-bad signal.

That is why the two methods are complementary. Binary reward gives **coverage**, while OPD gives **precision**. One captures weak but frequent evaluative feedback, and the other captures strong but sparse directive feedback. In practice, the best strategy is to use both.

| Dimension | Binary RL | OPD | Combined |
|---|---|---|---|
| Signal type | Evaluative (good / bad) | Directional | Evaluative + directional |
| Advantage | Sequence-level scalar | Token-level directional | Mixed sequence and token-level |
| Density | All scored turns | Hint-accepted turns only | All scored turns |
| Feedback type | User / environment | Explicit corrections | Both implicit and explicit feedback |
| Signal richness | 1 scalar per sample | 1 value per token | 1 value per token |

We combine them through a single advantage function. Let the scalar binary reward be \( r_{\text{final}} \), and let the OPD signal be the teacher-student log-probability gap under hindsight-enhanced context. Then the combined advantage is

$$
A_t = w_{\text{binary}} \, r_{\text{final}} + w_{\text{opd}} \left( \log\pi_{\text{teacher}}(a_t \mid s + \text{hint}) - \log\pi_\theta(a_t \mid s) \right).
$$

By default, we use

$$
w_{\text{binary}} = w_{\text{opd}} = 1.
$$

This formulation is simple, but it captures the core intuition: binary reward pushes the policy using broad evaluative feedback, while OPD refines that update using more detailed hindsight information about what should have changed.

<br>
<br>

##### Interesting Experiments

{% include figure.html path="assets/img/openclawrl1performance.png" title="" class="img-fluid rounded z-depth-1 w-100 mx-auto d-block" %}

Our experimental setting is built around a simple but realistic scenario: **both the student and the teacher use OpenClaw, but they want very different kinds of behavior from it**. In the first setting, a student uses OpenClaw to help with homework, but does not want the final response to look obviously AI-generated. The agent therefore needs to do more than solve the problem correctly. It needs to gradually learn a style that feels natural, less polished, and closer to how the student would actually write. In the second setting, a teacher uses OpenClaw to grade homework after the files are completed, and wants comments that are not just correct, but also specific and friendly. This makes the experiment a particularly interesting testbed for personalization: the challenge is not only whether the model can complete the task, but whether it can adapt to subtle, user-dependent preferences that only become clear through repeated interaction.

| Method | Updated 8 steps | Updated 16 steps |
|---|---:|---:|
| Binary RL | 0.25 | 0.23 |
| OPD | 0.25 | 0.72 |
| Combined | 0.76 | 0.81 |

These results illustrate the trade-off clearly. **Binary RL alone** improves only slightly: it is dense, but the signal is coarse. **OPD alone** becomes much stronger later: once enough useful hindsight hints have been collected, token-level supervision starts to produce substantial gains. 
The **combined method** gets the best of both worlds. It improves earlier than OPD alone because binary reward provides immediate coverage, and it improves more strongly than binary reward alone because OPD adds precise directional supervision.





##### Why Computer-Use Agents

The most important agents are not just chatbots. They are agents that can actually do useful work.

This repository is built to make reinforcement learning practical for **useful computer-use agents** in real deployment. Computer-use agents naturally generate grounded feedback during real execution, which makes them an ideal setting for continuous online optimization. Our roadmap is centered on turning that feedback into better behavior at scale.

##### Roadmap

Our long-term goal is to advance **personalized, practically useful agents** with reinforcement learning. The roadmap has two tracks.

The first track focuses on **personal agent optimization**: building agents that improve directly from the interaction patterns of individual users. We have already released **v1 of OpenClaw-RL**, including a fully asynchronous RL framework and two automatic optimization methods based on binary and textual feedback. From here, we plan to expand model support, improve serving efficiency, identify stronger optimization recipes through large-scale experiments and real user feedback, and eventually extend learning beyond the policy itself to components such as skills and memory.

The second track focuses on **general agents optimization**: scaling the same ideas into broader agentic RL infrastructure. Our next milestone, planned for the next **2–3 weeks**, is to release scalable RL infrastructure for more general agents, starting with **computer-use agents**.









