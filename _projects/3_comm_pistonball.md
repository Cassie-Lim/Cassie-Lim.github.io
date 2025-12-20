---
layout: page
title: Learning to Communicate in Multi-Agent Environments
description: Investigates the use of communication between agents in collaborative 
    multi-agent environments.
img: assets/img/comm/pistonball-ours.gif
importance: 3
category: work
---

[![Static Badge](https://img.shields.io/badge/-paper-B31B1B?logo=googledocs)](/assets/pdf/comm.pdf)
[![Static Badge](https://img.shields.io/badge/-video-red?logo=airplayvideo)](/assets/img/comm/comm-presentation.mp4)
[![Static Badge](https://img.shields.io/badge/-slide-green?logo=googleslides)](/assets/pdf/comm-slide.pdf)
[![Static Badge](https://img.shields.io/badge/github-repo-blue?logo=github)](https://github.com/Cassie-Lim/MRS-Collaborative-Communication)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)


## Motivation

Multi-Agent Reinforcement Learning (MARL) studies how multiple agents learn to act in a shared environment, where each agent’s actions can influence the observations and decisions of others. Traditional MARL methods often train agents independently, without explicit communication, despite the fact that humans rely heavily on shared signals such as visual cues and intent to coordinate in dynamic settings. Motivated by this gap, our project explores how enabling communication between agents can improve decision-making by sharing information such as observations or actions. We evaluate this idea in cooperative and semi-adversarial tasks using Proximal Policy Optimization (PPO), and investigate two communication mechanisms—vector encoding and multi-head attention—to demonstrate how inter-agent communication leads to richer coordination and improved task performance in simulation.


## Method Overview

We study how explicit communication improves coordination in Multi-Agent Reinforcement Learning by comparing communicating agents against a non-communicating PPO baseline. To isolate the effect of communication, we use **Proximal Policy Optimization (PPO)** as the shared training backbone and add communication modules that augment each agent’s local observation before action selection.

### PPO Backbone
All agents share a single policy and value function trained with PPO’s clipped objective, which provides stable updates and allows us to focus on communication design rather than algorithmic tuning.

### Communication Strategy 1: Vector Encoding

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/comm/comm-method-vector.png" title="Communication Vector" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Each agent encodes its local observation into a **communication scalar**. Scalars from other agents (or neighbors) are aggregated into a **communication vector**, which is concatenated with the agent’s own observation features (and optionally an agent ID) and fed into the shared policy and value networks.
<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/comm/algo-comm-vector.png" title="Communication Vector Algorithom" class="img-fluid rounded z-depth-1" %}
    </div>
</div>




### Communication Strategy 2: Multi-Head Attention
To make communication more expressive and scalable, we replace the fixed aggregation with **multi-head attention**. Each agent forms a communication token from its observation features (and ID), then attends over other agents’ tokens to produce a context vector. This attention-based context is concatenated with the local observation and passed to the PPO policy, enabling agents to emphasize relevant teammates and ignore irrelevant signals.

Across both variants, we collect trajectories from all agents and update the shared PPO networks using the aggregated experience.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/comm/algo-multi-head-attn.png" title="Multi-head Attention Algorithom" class="img-fluid rounded z-depth-1" %}
    </div>
</div>


## Experimental Results

### Environments
We evaluate communication-enabled multi-agent policies in the (**Pistonball**)[https://pettingzoo.farama.org/environments/butterfly/pistonball/] environment, a cooperative physics-based task where 20 agents must coordinate to push a ball across the arena. While we initially considered( **MultiCarRacing**)[https://github.com/igilitschenski/multi_car_racing/] as a semi-adversarial benchmark, practical constraints related to deprecated dependencies and training cost led us to focus our analysis on Pistonball.

### Communication Improves Coordination
We compare three approaches: a non-communicating PPO baseline, PPO with a **communication vector**, and PPO with **multi-head attention**. Both communication-based methods significantly outperform the baseline, successfully solving the environment within 500 training epochs, while the baseline fails to converge. The attention-based method learns faster but exhibits higher variance, whereas the communication vector approach achieves slightly higher peak rewards at the cost of increased instability.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/comm/reward-plot.png" title="Reward Plot" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

### Effect of Neighborhood Range
We analyze how limiting communication to local neighborhoods affects performance. Results show that restricting communication to **3–5 neighboring agents** yields the best returns. Communicating with all agents leads to information overload, while overly limited communication (e.g., a single neighbor) provides insufficient context. This suggests that local communication strikes a balance between relevance and scalability.
<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/comm/attn-neighbor-range.png" title="Effect of Neighborhood Range" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

### Interpreting Attention
Visualizing attention weights reveals that agents primarily attend to themselves and nearby neighbors. This behavior aligns with task dynamics, as coordination is most critical among spatially adjacent agents. The attention mechanism therefore learns to prioritize locally relevant information, supporting efficient and interpretable multi-agent coordination.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/comm/attn-heatmap.png" title="Interpreting Attention" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

