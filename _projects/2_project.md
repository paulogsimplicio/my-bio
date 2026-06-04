---
layout: page
title: Photogrammetry-Aware Coverage Planning for Autonomous Dam Inspection
description: Turns a photogrammetry spec into a flyable UAV trajectory that builds centimeter-accurate 3D maps of dams. Field-validated on a real dam.
img: assets/img/projects/2_project/thumbnail.png
importance: 1
category: work
toc:
  sidebar: left
---

## TL;DR

> **You tell it the smallest defect you need to see. It tells the drone exactly how to fly to capture it in 3D.** Field-tested on a real dam — resolved hazards as small as a soccer ball.

{% include figure.liquid loading="eager" path="assets/img/projects/2_project/hero_dam_with_trajectory.png" class="img-fluid rounded z-depth-1" caption="Real dam, real drone, planned trajectory overlaid on the slope." %}


## Problem

- Dams are old and under-inspected; manual inspection is slow, costly, risky.
- 2D photos can't measure change over time — you need a **3D point cloud**.
- The hard part isn't flying — it's deciding **where** to fly so the images reconstruct.


## What I built

A planner that converts a **detection requirement** into a **flyable trajectory** for a sloped structure.

- Runs on an off-the-shelf drone (SDK + ROS 2).
- Auto-computes standoff, rows, spacing, speed from the camera model.
- Handles the slope explicitly — most planners don't.


## How it works

{% include figure.liquid path="assets/img/projects/2_project/pipeline.svg" class="img-fluid rounded z-depth-1" caption="Detection need → resolution → flight geometry → waypoints → fly & reconstruct." %}

{% include figure.liquid path="assets/img/projects/2_project/geometry_trapezoid.png" class="img-fluid rounded z-depth-1" caption="Slope modeled as a trapezoid; path is rotated by the slope angle to keep standoff constant across rows." %}


## Experiments

- Field site: a real dam slope, autonomous flight on a commercial quadrotor.
- Swept resolution from long-range to detailed to map quality-vs-time.
- Planted known hazards (box + 2 soccer balls) to test what the cloud resolves.

{% include video.liquid path="https://www.youtube.com/embed/SuqJXi0uRdk" class="img-fluid rounded z-depth-1" caption="Field experiment + 3D reconstruction." %}


## Results

<div class="row text-center">
  <div class="col-4">
    <h3 style="margin-bottom:0">2&times;</h3>
    <p class="text-muted">faster at near-equal quality (long-range vs detailed)</p>
  </div>
  <div class="col-4">
    <h3 style="margin-bottom:0">37.5M</h3>
    <p class="text-muted">points in the detailed cloud</p>
  </div>
  <div class="col-4">
    <h3 style="margin-bottom:0">0.84 cm/px</h3>
    <p class="text-muted">best ground resolution</p>
  </div>
</div>

{% include figure.liquid path="assets/img/projects/2_project/Figure_1_2.png" class="img-fluid rounded z-depth-1" caption="Long-range (left) vs. detailed (right) — hazards visible in both; detailed cloud is denser and sharper." %}

- All planted hazards recoverable in the dense cloud.
- Differencing against a clean baseline auto-highlights new hazards &rarr; change monitoring.


## Lessons learned

- 3D reconstruction needs ~10x tighter GSD than the 2D "half the object" rule.
- Precise gimbal-to-slope alignment removed low-density patches.
- GPS is fine for path-following, weak for precise landing &rarr; vision-based landing next.


## Stack & role

**My role:** designed the planner, camera/geometry models, ROS 2 wrapper; ran field tests + 3D analysis.

`ROS 2` &middot; `UAV SDK` &middot; `coverage path planning` &middot; `photogrammetry (SfM/MVS)` &middot; `point-cloud registration` &middot; `Python`


## Reference

P. V. G. Simplicio, G. A. S. Pereira, *"Mission Planning for Photogrammetry-Based Autonomous 3D Mapping of Dams Using a Commercial UAV,"* ICUAS 2024.
[PDF]({{ '/assets/pdf/icuas2024_dam_photogrammetry.pdf' | relative_url }}) &middot; [Video](https://youtu.be/SuqJXi0uRdk)
