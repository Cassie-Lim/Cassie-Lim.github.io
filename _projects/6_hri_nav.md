---
layout: page
title: Human-in-the-Loop Object-Goal Navigation
description: A human-aware object-goal navigation framework that enables robots to reach target objects while adapting their motion to respect human personal space in dynamic household environments.
img: assets/img/hri-nav/teaser.png
importance: 6
category: work
---

[![Static Badge](https://img.shields.io/badge/-paper-B31B1B?logo=googledocs)](/assets/pdf/hri-nav.pdf)
[![Static Badge](https://img.shields.io/badge/github-repo-blue?logo=github)](https://github.com/NhiNguyencmt8/task-planner)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)


## Introduction & Motivation
Household environments are dynamic, shared spaces where humans move unpredictably, perform tasks, and frequently interact with objects. While robots must navigate efficiently to reach target objects, naïvely optimized object-goal navigation can obstruct human activity, cause discomfort, or disrupt natural human–robot coexistence. Most existing navigation systems focus primarily on collision avoidance, overlooking key aspects of social acceptability such as predictability, legibility, and respect for personal and working spaces. By incorporating humans into the navigation loop and modeling their behaviors, robots can adapt their motion based on human task context, movement patterns, and proximity, enabling safer, more comfortable, and minimally disruptive object-goal navigation in everyday household environments.

## Research Question

We aim to design a household robot capable of navigating toward object goals while dynamically adapting its behavior to human presence in shared environments. Specifically, the robot should minimize disturbance by avoiding overly intrusive actions, adjust its navigation strategy to prioritize humans and avoid interfering with ongoing human–object interactions, and achieve these behaviors without sacrificing efficiency or goal accuracy.
<div class="row justify-content-sm-center">
    <div class="col-sm-7 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/hri-nav/teaser.png" title="Environment setup" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

## Proposed Method
<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/hri-nav/hri-nav-overview.jpg" title="Method Overview" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

We propose a training-free, human-aware object-goal navigation module that augments a standard oracle navigation policy with explicit reasoning about human presence and motion uncertainty. The method decomposes the problem into human modeling under partial observability and robot planning with minimal intervention, enabling socially aware navigation without sacrificing task efficiency.

<div class="row justify-content-sm-center">
    <div class="col-sm-10 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/hri-nav/hri-nav-method1.png" title="Method-1" class="img-fluid rounded z-depth-1" %}
    </div>
</div>


### Human Modeling.
The human is treated as a dynamic, non-cooperative agent with unknown intent. Using segmentation and depth observations, we estimate the human’s ground-plane position when visible and maintain a Gaussian belief over their location when occluded. This belief is propagated forward using a constant-velocity model, capturing uncertainty due to sensing noise and intermittent visibility.

### Predictive Motion Corridor.
From the belief distribution, we predict a short-horizon human motion corridor that represents the region the human is likely to occupy, with uncertainty-aware expansion. When belief uncertainty becomes too large, the corridor is discarded to avoid overly conservative behavior.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/hri-nav/human-corridor.png" title="Intuitions of Human Corridor" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Intuitions of Human Corridor. The motion corridor captures where a human is likely to move in the near future, allowing the robot to proactively adapt its navigation. When the human is visible, the corridor is tightly aligned with the observed position and velocity, enabling precise and minimally conservative avoidance. When the human becomes temporarily occluded, the corridor expands according to growing uncertainty, prompting the robot to behave more cautiously while still maintaining progress toward its goal.
</div>



### Human-Aware Robot Planning.
The robot follows the oracle navigation command by default and intervenes only when necessary. If the robot approaches the predicted human corridor within a safety margin, the control is smoothly modified by slowing down and steering away from the human. Otherwise, the original oracle action is preserved. This constrained control strategy ensures safety and social comfort while maintaining goal accuracy and efficiency.

<div class="row justify-content-sm-center">
    <div class="col-sm-10 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/hri-nav/hri-nav-method2.png" title="Method-2" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

## Experiments
### Setup
We evaluate our human-aware navigation framework in the Habitat 3.0 simulator using PARTNR benchmark assets. Experiments are conducted in a high-fidelity apartment environment containing realistic furniture and a simulated human performing household tasks.

To test generalization rather than map memorization, we construct 8 episodes within the same static scene while varying:

- Object configurations: target objects are randomly spawned in valid locations.
- Human activities: the human executes different rearrangement tasks, resulting in diverse trajectories.
- The robot and human pursue independent, non-collaborative objectives, coexisting in a shared space without explicit coordination.



### Baselines
We compare three conditions:

1. Human-Only: human performs tasks without a robot (upper bound on efficiency).

2. Human + OracleNav: robot follows the default PARTNR object-goal navigation policy.

3. Human + Human-Aware (Ours): robot uses our belief-based human-aware navigation.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/hri-nav/human-only.gif" title="Human Only" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/hri-nav/human-w-robot.gif" title="Human w Robot" class="img-fluid rounded z-depth-1" %}
    </div>

</div>
<div class="caption">
    Example runs. Left: Human only; Right: Human with robot.
</div>


### Evaluation Metrics

We evaluate the trade-off between task efficiency and social compliance.

1. Social Compliance (Proxemics):
    - Disturbance Count: number of robot intrusions within distance thresholds {0.2 m, 0.4 m, 0.7 m, 1.2 m}.
    - Max Disturbance Duration: longest continuous time the robot remains within each threshold.

2. Task Performance:
    - Task completion rate.
    - Episode completion time (min, max, and average across episodes).

## Results Summary

> For more details please refer to the [paper](/assets/pdf/hri-nav.pdf). 

Our human-aware method consistently reduces social intrusions compared to the Oracle baseline, **with up to 29% fewer disturbances in larger personal-space zones**. While the robot may remain near the human for longer durations due to yielding behavior, these interactions are slow or stationary, improving legibility and perceived safety.
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/hri-nav/max_duration_comparison.png" title="" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/hri-nav/disturbance_count_comparison.png" title="" class="img-fluid rounded z-depth-1" %}
    </div>

</div>

Task success rates remain unchanged across all conditions, indicating that social constraints do not block human progress. However, increased social awareness introduces a modest time cost: stricter safety thresholds lead to longer episode durations, highlighting a clear trade-off between spatial efficiency and social caution.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/hri-nav/humanaware_completion_time.png" title="" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
