---
layout: page
title: Sim-to-Real Reinforcement Learning for Autonomous Multi-View UAV Inspection
description: A complete RL-based framework that trains a PPO policy in simulation to select next-best viewpoints for multi-view inspection of industrial structures — deployed on a real UAV with zero hand-crafted rules.
img: assets/img/projects/6_project/thumbnail.png
importance: 5
category: work
selected: true
toc:
  sidebar: left
---

<div class="text-center mt-n2 mb-3">
  <p class="mb-1" style="font-size:0.95rem;letter-spacing:0.03em">
    <strong>P. V. G. Simplicio</strong> &middot; G. A. S. Pereira
  </p>
  <p class="text-muted mb-0" style="font-size:0.875rem">
    <em>Field and Aerial Robotics Laboratory &mdash; West Virginia University</em>
  </p>
</div>
<hr style="border:none;border-top:2px solid var(--global-theme-color);opacity:0.45;margin-bottom:2rem">

## Overview

> **Predefined patterns can't see around corners. This RL policy learns to inspect like an expert — adapting to any geometry, satisfying photogrammetric constraints, and deploying directly to a real drone with zero hand-crafted rules.** Best coverage across all 8 methods tested; 33.92% shorter path than the best baseline.

{% include figure.liquid loading="eager" path="assets/img/projects/6_project/framework.png" class="img-fluid rounded z-depth-1" caption="Full inspection framework: a PPO policy selects next-best viewpoints from the environment representation; a TSP solver orders them; A* generates the collision-free path; ROS executes the flight." %}


## Problem

- Critical infrastructure requires regular inspection; manual surveys are costly and hazardous.
- Predefined patterns (spirals, lawnmowers) assume uniform geometry — they miss occluded surfaces and fail to generalize across structure types.
- No prior work validates a complete learning-based viewpoint selection pipeline on a fully autonomous UAV.


## What I built

A complete RL-based inspection pipeline, from sim training to real-world UAV deployment.

- **PPO agent** trained in NVIDIA Isaac Lab on diverse simulated structures, learning to select next-best viewpoints that maximize multi-view coverage.
- **Multi-view coverage metric** that counts a view only when all three conditions hold: structure hit, GSD within range, and angular diversity from previous views.
- **Deployment pipeline:** RL policy → TSP solver → A* path planning → ROS 2 + Olympe SDK → autonomous UAV flight.


## How it works

<figure>
  <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 860 110" style="width:100%;display:block">
    <defs>
      <marker id="pipeline-arr" markerWidth="8" markerHeight="6" refX="8" refY="3" orient="auto">
        <polygon points="0 0, 8 3, 0 6" fill="var(--global-theme-color)"/>
      </marker>
    </defs>
    <rect x="20"  y="22" width="140" height="64" rx="6" fill="var(--global-bg-color)" stroke="var(--global-theme-color)" stroke-width="2"/>
    <rect x="190" y="22" width="140" height="64" rx="6" fill="var(--global-bg-color)" stroke="var(--global-theme-color)" stroke-width="2"/>
    <rect x="360" y="22" width="140" height="64" rx="6" fill="var(--global-bg-color)" stroke="var(--global-theme-color)" stroke-width="2"/>
    <rect x="530" y="22" width="140" height="64" rx="6" fill="var(--global-bg-color)" stroke="var(--global-theme-color)" stroke-width="2"/>
    <rect x="700" y="22" width="140" height="64" rx="6" fill="var(--global-bg-color)" stroke="var(--global-theme-color)" stroke-width="2"/>
    <line x1="162" y1="54" x2="188" y2="54" stroke="var(--global-theme-color)" stroke-width="2" marker-end="url(#pipeline-arr)"/>
    <line x1="332" y1="54" x2="358" y2="54" stroke="var(--global-theme-color)" stroke-width="2" marker-end="url(#pipeline-arr)"/>
    <line x1="502" y1="54" x2="528" y2="54" stroke="var(--global-theme-color)" stroke-width="2" marker-end="url(#pipeline-arr)"/>
    <line x1="672" y1="54" x2="698" y2="54" stroke="var(--global-theme-color)" stroke-width="2" marker-end="url(#pipeline-arr)"/>
    <text x="90"  y="45" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">PPO Policy</text>
    <text x="90"  y="63" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">(Isaac Lab)</text>
    <text x="260" y="45" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Viewpoint</text>
    <text x="260" y="63" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Set V*</text>
    <text x="430" y="45" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">TSP</text>
    <text x="430" y="63" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Ordering</text>
    <text x="600" y="45" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">A* Path</text>
    <text x="600" y="63" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Planning</text>
    <text x="770" y="45" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">UAV</text>
    <text x="770" y="63" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Flight</text>
  </svg>
  <figcaption class="caption">PPO policy → viewpoint set → TSP ordering → A* path planning → autonomous UAV flight.</figcaption>
</figure>

{% include figure.liquid path="assets/img/projects/6_project/mdp.png" class="img-fluid rounded z-depth-1" caption="MDP formulation: the PPO agent observes the voxel grid state and historic poses, acts by selecting next-best viewpoints, and is rewarded by the multiview coverage gain ΔQ." %}

**MDP formulation.** The viewpoint selection problem is cast as a Markov Decision Process where the agent iteratively builds an inspection plan maximising multi-view coverage:

<pre style="background:var(--global-bg-color);border:1px solid var(--global-theme-color);border-radius:6px;padding:1rem 1.25rem;font-size:0.85rem;line-height:1.8;overflow-x:auto;font-weight:600"><b>State</b>   S = { H, V<sub>prev</sub>, ΔQ<sub>i</sub> }
         H        — voxel occupancy &amp; coverage history
         V<sub>prev</sub>   — previously selected viewpoints
         ΔQ<sub>i</sub>    — coverage gain at step i

<b>Action</b>  A = { v<sub>i</sub> (next best viewpoint),  ΔQ<sub>i</sub> = Q<sub>i+1</sub> − Q<sub>i</sub> }

<b>Reward</b>  R = ΔQ<sub>i</sub>  (multiview gain)   maximised over episode

<b>Coverage metric</b>  Q<sub>t</sub> = Σ<sub>v∈S</sub> min(ν<sub>t</sub>(v), K<sub>req</sub>) / (|S| · K<sub>req</sub>)

A view ν<sub>t</sub>(v) counts for voxel v <b>only if all three hold:</b>
  ✓  Structure hit      — ray from viewpoint intersects voxel
  ✓  GSD range         — distance within valid photogrammetry zone
  ✓  Angular diversity  — incidence angle θ ≤ 10° from surface normal</pre>

{% include figure.liquid path="assets/img/projects/6_project/training.png" class="img-fluid rounded z-depth-1" caption="Training (top): PPO agent learns across diverse simulated structures. Inference (bottom): the policy selects progressively better viewpoints — from 1 to 30, coverage improves and views per voxel increase." %}


## Experiments

- **Simulation:** 8 methods compared (random sampling, uniform hemisphere, POI circle, spiral, lawnmower, and ours) across multiple structure geometries in Isaac Lab.
- **Real deployment:** policy transferred zero-shot to a physical UAV (Olympe SDK + ROS 2) on an actual industrial structure — no parameter tuning.
- **Generalization:** tested on structures with different geometries without changing the policy or architecture.

{% include figure.liquid path="assets/img/projects/6_project/deployment.png" class="img-fluid rounded z-depth-1" caption="Deployment platform: the RL policy output feeds a ground control station (ROS 2 + Olympe SDK) that commands the physical drone, closing the sim-to-real loop." %}

{% include figure.liquid path="assets/img/projects/6_project/results_real.png" class="img-fluid rounded z-depth-1" caption="Real-world experiment: the UAV autonomously inspects an industrial structure following the RL-generated viewpoint plan; the resulting 3D reconstruction confirms complete multi-view coverage." %}


## Results

<div class="row text-center">
  <div class="col-4">
    <h3 style="margin-bottom:0">96.16%</h3>
    <p class="text-muted">C1 coverage on a real structure — best across all 8 methods</p>
  </div>
  <div class="col-4">
    <h3 style="margin-bottom:0">89.10%</h3>
    <p class="text-muted">C2 triple-redundancy — guaranteed reconstruction quality</p>
  </div>
  <div class="col-4">
    <h3 style="margin-bottom:0">33.92%</h3>
    <p class="text-muted">shorter path vs. best baseline (spiral / lawnmower)</p>
  </div>
</div>

{% include figure.liquid path="assets/img/projects/6_project/results_sim.png" class="img-fluid rounded z-depth-1" caption="Simulated results: our policy (★) achieves the highest C1, C2, and C3 coverage across all compared methods while generating the most efficient path (97.03 m, 0.84 views/m)." %}

- Policy generalises to unseen structure geometries with zero hand-crafted rules or structure-specific tuning.
- Highest coverage-per-metre efficiency among all 8 baselines tested.


## Lessons learned

- A learned policy naturally discovers occlusion-aware viewpoints that fixed patterns systematically miss.
- The multi-view coverage metric (GSD + angular diversity + hit) is the key supervision signal — it encodes photogrammetric quality directly into the reward.
- Sim-to-real transfer requires diverse training environments, not domain randomization of physics — geometry variety is what generalises.


## Stack & role

**My role:** designed the MDP formulation, coverage metric, PPO training pipeline, sim-to-real transfer, and real-world deployment.

`NVIDIA Isaac Lab` &middot; `PPO` &middot; `reinforcement learning` &middot; `next-best-view planning` &middot; `A*` &middot; `ROS 2` &middot; `Python`


## Reference

P. V. G. Simplicio, G. A. S. Pereira, *"Autonomous Multi-View UAV Inspection via Reinforcement Learning with Sim-to-Real Validation,"* West Virginia University — Field and Aerial Robotics Laboratory.
[PDF]({{ '/assets/pdf/RL_Policy.pdf' | relative_url }})
