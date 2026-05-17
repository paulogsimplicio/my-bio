---
layout: page
title: RL-Based Next-Best-View Planning
description: Training PPO agents in NVIDIA Isaac Lab to learn autonomous next-best-view policies from raw point clouds under photogrammetric constraints.
img: assets/img/3.jpg
importance: 3
category: research
selected: true
---

This ongoing project casts the next-best-view (NBV) problem as a reinforcement learning task, replacing hand-crafted heuristics with a learned policy that directly optimizes coverage over raw point clouds.

**Core contributions:**
- PPO agent trained end-to-end in NVIDIA Isaac Lab / Isaac Sim, operating directly on point cloud observations.
- Reward shaped by new-area coverage gain subject to photogrammetric constraints (GSD, overlap, view angle).
- Sim-to-real transfer pipeline targeting deployment on physical UAV platforms.

**Status:** Ongoing — West Virginia University Robotics Lab.
