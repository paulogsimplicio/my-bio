---
layout: page
title: Autonomous Dam Inspection — 3D maps from a commercial drone
description: Plans a UAV mission that builds a centimeter-accurate 3D map of a dam, good enough to find hazards. Validated on a real dam.
img: assets/img/projects/2_project/thumbnail.jpg
importance: 4
category: work
toc:
  sidebar: left
---

## TL;DR

> **Tell it the smallest defect you need to catch; it flies the drone to map the dam in 3D at exactly that resolution. Tested on a real dam — recovered hazards as small as a soccer ball.**

{% include figure.liquid loading="eager" path="assets/img/projects/2_project/hero.jpg" class="img-fluid rounded z-depth-1" caption="Real dam, commercial quadrotor, planned inspection trajectory over the slope." %}


## Did it work?

<div class="row text-center">
  <div class="col-4">
    <h3 style="margin-bottom:0">0.84 cm/px</h3>
    <p class="text-muted">ground resolution achieved</p>
  </div>
  <div class="col-4">
    <h3 style="margin-bottom:0">37.5M</h3>
    <p class="text-muted">points in the 3D cloud</p>
  </div>
  <div class="col-4">
    <h3 style="margin-bottom:0">2&times;</h3>
    <p class="text-muted">faster at near-equal quality</p>
  </div>
</div>

- **Where it ran:** autonomous flights on a commercial quadrotor over a real WV dam slope.
- **What it proved:** every planted hazard (box, two soccer balls) was recoverable in the cloud.

{% include video.liquid path="https://www.youtube.com/embed/SuqJXi0uRdk" class="img-fluid rounded z-depth-1" caption="Field run and resulting 3D reconstruction." %}


## The hard part

{% include figure.liquid path="assets/img/projects/2_project/geometry_trapezoid.png" class="img-fluid rounded z-depth-1" caption="Slope as a trapezoid with UAV and dam frames; the path is rotated by the slope angle so standoff stays constant on every row." %}

- **The challenge:** keeping image resolution uniform across a tilted face — naive paths drift closer/farther and the cloud goes patchy.
- **The key decision:** plan in a dam-fixed frame, then rotate into the world frame so standoff to the slope is constant per row.
- **The insight:** 3D reconstruction needs ~10&times; tighter GSD than the textbook 2D "half the object" rule — found it the hard way in the field.


## What I built

- **I owned:** the mission planner — camera/geometry model, coverage-row math, slope-frame waypoint generation.
- **I integrated:** a commercial UAV via its SDK, wrapped in ROS 2 for autonomous flight and data capture.
- **I validated:** ran the field experiments and the SfM/MVS reconstruction + point-cloud differencing.

`ROS 2` · `UAV SDK` · `coverage path planning` · `photogrammetry (SfM/MVS)` · `point-cloud registration` · `Python`


## Why it matters

- **The problem it solves:** aging dams are under-inspected and manual slope inspection is slow, costly, and dangerous.
- **Where it goes:** same method extends to tailings dams and other large sloped infrastructure.


## Deeper / reference

- **More results:** long-range vs detailed reconstructions, hazard-differencing maps (see paper Figs. 4–6).
- **Paper:** *Mission Planning for Photogrammetry-Based Autonomous 3D Mapping of Dams Using a Commercial UAV*, ICUAS 2024. [PDF]({{ '/assets/pdf/icuas2024_dam.pdf' | relative_url }})
