---
layout: page
title: OpenClaw-RL II
description: Scalable RL Infrastructure for Real-World Terminal, GUI, SWE, and Tool-Call Agents
img: assets/img/publication_preview/openclawrl2icon.png
importance: 1
category: RL
math: true
---

An RL system optimize not just a personal assistant, but a wide range of **real-world useful agents**。

That is **OpenClaw-RL II**. In practice, the most important agents are not pure chatbots. They are agents that can **use terminals, operate graphical interfaces, modify codebases, and call external tools**. These are the settings where language models start to become truly useful: writing and debugging code, navigating software, interacting with web services, and completing long-horizon tasks that require real execution rather than text-only reasoning.

OpenClaw-RL is built for exactly this regime. As long as an agent interacts with an environment and receives a next state after each action, that interaction can become training signal. This page focuses on the **general-agent track** of OpenClaw-RL: scalable reinforcement learning for **terminal**, **GUI**, **SWE**, and **tool-call** agents. The same asynchronous RL backbone that powers our personal-agent setting can also support large-scale optimization for these broader real-world environments. The [repository](https://github.com/Gen-Verse/OpenClaw-RL) is here.

<br>
<br>

##### Four Real-World Agent Settings

The goal of agent research should not be limited to generating better text. It should be about building agents that can actually **do useful work** in realistic environments. That is why we focus on four settings that cover many of the most important deployment scenarios.

**Terminal agents** are one of the most practical forms of computer-use agents. They can read files, run commands, inspect logs, execute scripts, and interact with software stacks in a cheap and highly scalable way. Many coding, DevOps, and data-processing workflows are naturally exposed through the terminal, making it one of the most important interfaces for real deployment.

**GUI agents** matter because many real applications are not accessible from the command line. A large fraction of useful digital work still happens through browsers, desktop applications, menus, forms, buttons, and visual layouts. If we want agents that can use computers like humans do, GUI interaction is indispensable.

**SWE agents** are especially important because software engineering is one of the clearest high-value applications of language models. These agents operate over repositories, tests, diffs, and execution feedback. They do not just generate code, they iteratively diagnose, edit, execute, and verify changes in a grounded loop.

**Tool-call agents** are equally critical because modern language-model systems increasingly rely on tools for retrieval, computation, API access, and structured execution. Tool use improves factuality, expands capabilities, and turns a language model into a more reliable action-taking system.

Taken together, these four settings cover a broad and practically meaningful slice of **general computer-use agents**. They also highlight an important point: the future of agent learning should be **general**, not tied to a single benchmark or interface. A good RL framework should not care whether the next-state signal comes from a shell command, a GUI transition, a failed unit test, or a function return value. It should be able to learn from all of them.

<br>
<br>

##### One Infrastructure for All of Them



OpenClaw-RL is **asynchronous**. Environment hosting, policy serving, PRM or judge inference, and model training are decoupled from one another, so no single component blocks the rest of the pipeline. The live agent can keep serving requests while reward models score previous interactions and training workers update the policy in the background.

This matters even more in real-world agent settings than in pure chat. Long-horizon agent trajectories often have large variance in runtime: some terminal jobs finish quickly, some GUI tasks are slow and visually complex, some SWE trajectories involve repeated testing, and some tool-calling tasks branch into many external calls. A synchronous system wastes time waiting for the slowest environment. Asynchronous design avoids that bottleneck and keeps the overall pipeline efficient.

The same design also makes **large-scale environment parallelization** natural. Environments can be hosted on cloud machines and scaled out independently, allowing many trajectories to be collected at once. In our experiments, this lets us train across heterogeneous real-world settings while preserving a unified infrastructure.

<br>
<br>

##### Experimental Support Across Terminal, GUI, SWE, and Tool-Call Agents

We validate OpenClaw-RL across all four general-agent settings. This includes text-based terminal interaction, visual GUI control, software engineering tasks with executable verification, and structured tool-calling tasks. Together, these experiments show that the framework is not tied to a single type of environment, but can support a broad range of practically relevant agents.

Specifically, we use large-scale environment parallelization during training: **128** parallel environments for terminal agents, **64** for GUI agents, **64** for SWE agents, and **32** for tool-call agents. This setup allows us to scale RL training across different model sizes, modalities, and interaction horizons within one system.

{% include figure.html path="assets/img/openclaw2allcurve.png" title="" class="img-fluid rounded z-depth-1" %}

OpenClaw-RL is not only a personalization framework for conversational agents. It is also a **general-purpose RL infrastructure** for real-world agent settings.

<br>
<br>

##### Integrated Reward: Combining Outcome and Process Signals

{% include figure.html path="assets/img/openclawrl2method.png" title="" class="img-fluid rounded z-depth-1 w-60 mx-auto d-block" %}

For long-horizon agents, terminal success alone is often too sparse. If a trajectory only receives supervision at the very end, most intermediate actions remain weakly trained. OpenClaw-RL therefore supports an **integrated reward** that combines terminal outcome supervision with step-wise process reward from a judge model.

Given a task \( q \in Q \) and policy \( \pi \), we obtain a trajectory \( \tau \sim \pi(\cdot \mid q) \) with terminal outcome \( O_{\tau} \). At each interaction step, the agent produces an action and receives a next-state signal, such as a command result, a visual transition, a test verdict, or a tool return value. A PRM or judge evaluates this local transition and assigns step-wise reward values \( r_t^{(1)}, \ldots, r_t^{(m)} \). We combine them with the outcome reward using

$$
R_t = O_{\tau} + \frac{1}{m} \sum_{i=1}^{m} r_t^{(i)}.
$$

This gives each step access to both **global supervision** and **local progress information**. Intuitively, the outcome reward says whether the full task eventually succeeded, while the process reward says whether the current step appears to be moving in the right direction. This combination is especially valuable in long-horizon environments, where many actions matter before the final result becomes visible.

In practice, we find that integrated rewards lead to stronger optimization than outcome-only training, although they require additional compute to host the PRM or judge model.

| Setting | Integrated reward | Outcome only |
|---|---:|---:|
| Tool-call | 0.30 | 0.17 |
| GUI | 0.33 | 0.31 |

These results show that step-wise reward is helpful even when a verifiable terminal outcome is already available. For real-world agents, the next-state signal contains valuable training information throughout the trajectory, not just at the end.

<br>
<br>

##### Why This Matters

OpenClaw-RL II is built around a simple belief: a useful RL framework for agents should match the environments where agents are actually deployed. That means **terminal agents**, **GUI agents**, **SWE agents**, and **tool-call agents**. It also means training systems that can scale in the cloud, run asynchronously, and learn from the signals generated during normal execution.


This is the second track of OpenClaw-RL: turning the same ideas that enable personal-agent adaptation into a broader infrastructure for **general computer-use agents**. The underlying principle remains the same. Once an agent acts in the world and the world responds, that response can become learning signal.
