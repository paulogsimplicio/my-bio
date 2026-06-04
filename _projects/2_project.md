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

<figure>
  <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 860 110" style="width:100%;display:block">
    <defs>
      <marker id="pipeline-arr" markerWidth="8" markerHeight="6" refX="8" refY="3" orient="auto">
        <polygon points="0 0, 8 3, 0 6" fill="var(--global-theme-color)"/>
      </marker>
    </defs>
    <!-- Boxes -->
    <rect x="20"  y="22" width="140" height="64" rx="6" fill="var(--global-bg-color)" stroke="var(--global-theme-color)" stroke-width="2"/>
    <rect x="190" y="22" width="140" height="64" rx="6" fill="var(--global-bg-color)" stroke="var(--global-theme-color)" stroke-width="2"/>
    <rect x="360" y="22" width="140" height="64" rx="6" fill="var(--global-bg-color)" stroke="var(--global-theme-color)" stroke-width="2"/>
    <rect x="530" y="22" width="140" height="64" rx="6" fill="var(--global-bg-color)" stroke="var(--global-theme-color)" stroke-width="2"/>
    <rect x="700" y="22" width="140" height="64" rx="6" fill="var(--global-bg-color)" stroke="var(--global-theme-color)" stroke-width="2"/>
    <!-- Arrows -->
    <line x1="162" y1="54" x2="188" y2="54" stroke="var(--global-theme-color)" stroke-width="2" marker-end="url(#pipeline-arr)"/>
    <line x1="332" y1="54" x2="358" y2="54" stroke="var(--global-theme-color)" stroke-width="2" marker-end="url(#pipeline-arr)"/>
    <line x1="502" y1="54" x2="528" y2="54" stroke="var(--global-theme-color)" stroke-width="2" marker-end="url(#pipeline-arr)"/>
    <line x1="672" y1="54" x2="698" y2="54" stroke="var(--global-theme-color)" stroke-width="2" marker-end="url(#pipeline-arr)"/>
    <!-- Labels -->
    <text x="90"  y="54" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Detection Need</text>
    <text x="260" y="54" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Resolution</text>
    <text x="430" y="54" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Flight Geometry</text>
    <text x="600" y="54" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Waypoints</text>
    <text x="770" y="45" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Fly &amp;</text>
    <text x="770" y="63" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Reconstruct</text>
  </svg>
  <figcaption class="caption">Detection need → resolution → flight geometry → waypoints → fly &amp; reconstruct.</figcaption>
</figure>

{% include figure.liquid path="assets/img/projects/2_project/geometry_trapezoid.png" class="img-fluid rounded z-depth-1" caption="Slope modeled as a trapezoid; path is rotated by the slope angle to keep standoff constant across rows." %}

**Core algorithm — waypoint generation.** Lays out the back-and-forth coverage path in the dam frame, then rotates it by the slope angle into the world frame so standoff stays constant on every row.

{% highlight text linenos %}
Input:  N, d_s, d_r, h, w_d, θ, γ
Output: ^wτ  (ordered set of waypoints ^wx, ^wy, ^wz)

δ_w(0) ← 0
^dx(0) ← 0
^dy(0) ← ( h − d_r·(N−1) ) / 2
^dz(0) ← d_s
X ← ^dx(0),  Y ← ^dy(0),  Z ← ^dz(0)

for k = 0 to N do
    if k is even:
        X ← X ∪ [ ^dx(0)+δ(k),  ^dx(0)−w_d−δ(k) ]
    else:
        X ← X ∪ [ ^dx(0)−w_d−δ(k),  ^dx(0)+δ(k) ]
    Y ← Y ∪ [ ^dy(0)+k·d_r,  ^dy(0)+k·d_r ]
    Z ← Z ∪ [ ^dz(0),  ^dz(0) ]
    δ_w(k+1) ← δ_w(k) + tan(θ)·d_r
end for

^wτ ← rot_{x,γ}( [X, Y, Z] )
return ^wτ
{% endhighlight %}

The final rotation `rot_{x,γ}` about the x-axis is the key step — it tilts the planar path onto the dam's slope, holding camera-to-slope distance fixed so resolution is uniform across the whole map.


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

{% include figure.liquid path="assets/img/projects/2_project/registration.png" class="img-fluid rounded z-depth-1" caption="Hazards picked out automatically: a clean baseline cloud differenced against a cloud with planted hazards (detailed mission, GSD 1.40 cm/px). The highlighted regions are the detected hazards." %}

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
