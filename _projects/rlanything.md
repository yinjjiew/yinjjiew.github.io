---
layout: page
title: RLAnything
description: Forge Environment, Policy, and Reward Model in Completely Dynamic RL System
img: assets/img/publication_preview/rlanythingicon.png
importance: 1
category: RL
math: true
---

In real-world applications, an agent must interact with the environment iteratively, often producing long trajectories. In this setting, step-wise signals are especially important. Beyond high-quality reward modeling, task difficulty is known to affect an agent’s learning efficiency. We introduce **RLAnything**, a fully dynamic RL system that tightly couples the environment, policy, and reward model by having them provide feedback to one another, strengthening learning signals and improving overall performance. We conduct extensive experiments across GUI agent, LLM agent, and coding LLM settings. See details in our [paper](https://arxiv.org/abs/2509.06949) and [repository](https://github.com/Gen-Verse/Open-AgentRL).

{% include figure.html path="assets/img/rlanythingoverview.png" title="" class="img-fluid rounded z-depth-1" %}

##### Insights Overview

1. The policy is trained with integrated feedback from outcome signals and step-wise from reward model, better than using outcome only.
2. Reward model is jointly optimized via consistency feedback, which in turn further improves policy training.
3. Our theory-motivated automatic environment adaptation improves training for both the reward and policy models by leveraging critic feedback from each, enabling learning from experience.
4. Through extensive experiments, we demonstrate each added dynamic component consistently improves the overall system.
5. Step-wise signals from optimized reward-model outperform outcome signals that rely on human labels.

<br>
<br>

##### Integrated Feedback for Policy

Given a task \\(q \in Q\\) and policy \\(\pi \\), we obtain a trajectory \\(\tau \sim \pi (\cdot \mid q) \\) and its corresponding outcome \\( O_{\tau} \in \\{-1, 1 \\} \\). For the \\(i\\)-th step \\(\tau_i \\), we query reward model \\(m\\) independent times, yielding reasoning \\( r_{\tau_i, j} \\) and final score \\(S_{\tau_i, j} \in \\{-1, 1\\} \\), \\(1 \le j \le m\\). The the integrated feedback for \\(\tau_i \\) is

$$
R_{\tau_i} = O_{\tau} + \sum_{j = 1}^m S_{\tau_i, j} / m,
$$

where \\( \lambda \\) (1 by default) balances the trade-off between outcome supervision and step-wise supervision. Note that we compute advantages by standardizing rewards across trajectories at the same step index \\(i\\), i.e., over the set \\( \\{ R_{\tau_i} \mid \tau \sim \pi (\cdot \mid q) \\} \\). From the following figures, we find this integrated feedback is vital for long trajectory tasks.

{% include figure.html path="assets/img/rlanything_int_vs_out.png" title="" class="img-fluid rounded z-depth-1" %}

<br>
<br>

##### Consistency Feedback for Reward Model

We further strengthen the policy’s training signals by jointly improving the reward model. We set the consistency feedback for \\( r_{\tau_i, j} \\) as

$$
R_{S_{\tau_i, j}} = R_{\tau_i} \cdot S_{\tau_i, j},
$$

where the supervision is consisted of both outcome and self-consistency signals. We evaluate two aspects of reward modeling. First, we assess the ability to detect errors at the current step (we obtain step-wise ground-truth labels via majority voting from a much stronger reasoning model). Second, we assess its ability to predict the influence on final outcome; we use the episode outcome as the label for this evaluation. From the following figure, we kown the reward quality is improving and further improves policy training.


{% include figure.html path="assets/img/rlanything_rm_and_policy_improve_by_side.png" title="" class="img-fluid rounded z-depth-1" %}

<br>
<br>

##### Balancing Task Difficulty Benefits Both the Reward and Policy Models

We know balancing task difficulty can improve policy training. We present two simple theoretical results showing that, under our reward system, this approach also improves reward-model training. Theorem 1 transforms the objective of predicting each step’s influence on the final outcome, into two terms (\\( p_{+}\\) and \\( p_{-}\\)) that can be estimated from our rollout data.

{% include figure.html path="assets/img/rlanythingthm1.png" title="" class="img-fluid rounded z-depth-1" %}

This theorem highlights the necessity of balancing the importance-sampling ratio between \\( p_{+}\\) and \\( p_{-}\\) to prevent bias in reward-model optimization. We then use the following Theorem 2 to show that our actual optimization target reduces to estimator of \\(\mu \\) and that task-difficulty balancing guarantees a well-behaved importance-sampling ratio.

{% include figure.html path="assets/img/rlanythingthm2.png" title="" class="img-fluid rounded z-depth-1" %}

<br>
<br>

##### Adapting Environment Tasks by critic feedback from both reward and policy models

Building on the above insights, we now propose a targeted and automated approach to task adaptation. If the policy's accuracy on \\(q\\) falls outside \\((\alpha_{\text{low}}, \alpha_{\text{high}})\\), we ask a language model to modify \\(q\\) into \\(q'\\) using a summarized critic derived from \\(\\{r_{\tau_i,j} \mid \tau \sim \pi(\cdot \mid q), \forall i,j\\}\\), i.e., the reward model's previous outputs. The quality of the new task \\(q'\\) is ensured by our acceptance mechanism. If the goal is to make \\(q\\) harder, we replace it with \\(q'\\) only if \\(\alpha_{\text{low}} < acc(q') < acc(q)\\). If the goal is to make \\(q\\) easier, we replace it with \\(q'\\) only if \\(acc(q) < acc(q') < \alpha_{\text{high}}\\). By default, we set \\(\alpha_{\text{low}} = 0.2\\) and \\(\alpha_{\text{high}} = 0.8\\). Note that the correctness of \\(q'\\) is also guaranteed, since it must yield at least one successful rollout (i.e., \\(acc(q') > 0\\)).

<br>
<br>

##### Each Added Dynamic Component Consistently Improves The Overall System

From the table and figures below, we show that each added dynamic component consistently improves the overall system and enhances generalizability.


{% include figure.html path="assets/img/rlanythingmaintable.png" title="" class="img-fluid rounded z-depth-1" %}


{% include figure.html path="assets/img/rlanything_alfworld_osworld_curve_side_by_side.png" title="" class="img-fluid rounded z-depth-1" %}

<br>
<br>

##### Step-wise Signals from an Optimized Reward Model Outperform Outcome Signals that Rely on Human Labels

We compare two training signals: step-wise signals from the optimized reward model and outcome signals. We find that the optimized reward model provides substantially stronger supervision. In real-world applications such as GUI agents, outcome rewards often require humans to create verifier files, which limits scalability. Motivated by these results, self-exploring agents in large-scale environments such as computers become more promising.

{% include figure.html path="assets/img/rlanything_optimized_rm.png" title="" class="img-fluid rounded z-depth-1" %}

<br>
<br>

##### Adaptation Examples

{% include figure.html path="assets/img/rlanythingexample.png" title="" class="img-fluid rounded z-depth-1" %}

<br>
<br>


##### Scale Training for GUI Agent

We further scale the RLAnything optimization for GUI agent, leading to significant improvements.

{% include figure.html path="assets/img/rlanythingscaleosworld.png" title="" class="img-fluid rounded z-depth-1" %}

{% include figure.html path="assets/img/rlanythingosworldbench.png" title="" class="img-fluid rounded z-depth-1" %}


<br>
<br>


##### Linear Scaling of Environment Tasks

We find that the number of accepted new environment tasks grows approximately linearly, demonstrating the potential for environment scaling. In our text-agent (AlfWorld) and coding settings, new tasks are generated fully automatically by language models. In the GUI-agent setting (OSWorld), we pre-create additional verifier files. Although task modification and hint generation are still automated in the GUI setting, the need to pre-create verifiers can limit automation and scalability, making this an important direction for future work.


{% include figure.html path="assets/img/rlanythinglinearenv.png" title="" class="img-fluid rounded z-depth-1" %}















