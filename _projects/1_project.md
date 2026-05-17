---
layout: page
title: Multi-Resolution UAV Path Replanning
description: Real-time onboard replanning for autonomous inspection of tailings and coal-ash dams, with hazard detection and return-to-home logic.
img: assets/img/1.jpg
importance: 1
category: research
selected: true
---

This project develops a multi-resolution motion planner for UAV inspection of large civil infrastructure (tailings and coal-ash dams) under a DOE-funded program at West Virginia University.

**Core contributions:**
- A*/RRT* search over octree and voxel-grid representations (via OMPL and Open3D) that generates collision-free "tunnel" corridors adapted to the local geometry of the structure.
- Multi-resolution replanning: coarse global path refined into a dense local trajectory onboard in real time.
- Hazard detection pipeline integrated with return-to-home (RTH) logic, enabling battery-swap missions over large sites without operator intervention.

**Published at:** *ICUAS 2025*, Charlotte, NC — P. V. G. Simplicio and G. A. S. Pereira.
