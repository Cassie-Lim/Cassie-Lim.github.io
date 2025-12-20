---
layout: page
title: Multi-agent Cable System
description: Reinforcement learning implementations for cable-driven parallel robots designed for agricultural monitoring and inspection tasks. The project features both single-agent and multi-agent approaches to optimize path planning and coordination in hydroponic farming environments.
img: assets/img/mrs-cable/env.gif
importance: 4
category: work
---

[![Static Badge](https://img.shields.io/badge/github-repo-blue?logo=github)](https://github.com/harshmuriki/cable-robot-system/tree/main)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)


<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mrs-cable/mrs-cable-poster.png" title="Project Poster" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

## Problem statements

We developed a two-robot cable-driven system, each equipped with a distinct sensor (RGB and hyperspectral), to collectively capture comprehensive plants data over time. We then implemented a reinforcement learning framework to optimize coverage strategies and inter-robot coordination.


<div class="row justify-content-sm-center">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/mrs-cable/intro.png" title="Problem statements" class="img-fluid rounded z-depth-1" %}
  </div>
</div>

## Solution Strategies & Potential Limitation

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mrs-cable/solu-overview.png" title="Project Poster" class="img-fluid rounded z-depth-1" %}
    </div>
</div>


## Simulation Development
We built a multi-cable robot simulation environment with a real-time 3D renderer based on Open3D. The plants are randomly spawned at the start of each episode. The rendered environments are shown in the figure below. Once a plant is visited, it is colored green for visual distinction. Both the number of agents and the grid size are easily configurable.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mrs-cable/env.gif" title="Environment setup" class="img-fluid rounded z-depth-1" %}
    </div>
</div>


## Results

We choose PPO as the base policy. The training logs for both the single-agent and multi-agent settings are shown below. In the single-agent setup, the decreasing training loss and the explained variance approaching 1 indicate stable convergence and accurate value function estimation. In the multi-agent setup, shorter episode lengths, higher cumulative rewards, and lower policy entropy demonstrate improved efficiency and increased confidence in cooperative decision-making. As training progresses, agents reduce redundant movements when visiting plants and learn to adapt their actions in response to the behaviors of other agents.

<div class="row justify-content-sm-center">
    <div class="col-sm-5 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mrs-cable/single-agent-log.png" title="Single Agent Log" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-7 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mrs-cable/mul-agent-log.png" title="Multi Agent Log" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

The behavior for trained agents are shown below:

<div class="row">
<div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mrs-cable/single-agent-ppo.gif" title="Single Agent Log" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mrs-cable/mul-agent-ppo.gif" title="Multi Agent Log" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
