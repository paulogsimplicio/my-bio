---
layout: page
title: Multi-resolution UAV Path Replanning for Inspection of Tailings Dams
description: Combines offline A* planning on voxel grids with real-time octree-based replanning so a commercial UAV stays safe and on-mission over large mining structures.
img: assets/img/projects/3_project/thumbnail.jpg
importance: 2
category: work
toc:
  sidebar: left
---

<div class="text-center mt-n2 mb-3">
  <p class="mb-1" style="font-size:0.95rem;letter-spacing:0.03em">
    <strong>P. V. G. Simplicio</strong> &middot; G. A. S. Pereira
  </p>
  <p class="text-muted mb-0" style="font-size:0.875rem">
    <em>ICUAS &mdash; North Carolina, USA, 2025</em>
  </p>
</div>
<hr style="border:none;border-top:2px solid var(--global-theme-color);opacity:0.45;margin-bottom:2rem">

## Overview

> **You give it a sparse point cloud. It plans a photogrammetry-quality path — and replans in milliseconds when the drone hits an obstacle or needs to return home.** Validated on a real 205,000 m² coal mine tailings dam; octree replanning is up to 2.62× faster than voxel-grid methods.

{% include figure.liquid loading="eager" path="assets/img/projects/3_project/framework.jpg" class="img-fluid rounded z-depth-1" caption="Integrated planning framework: offline global coverage path (solid line) plus online octree-based replanning triggered by battery events or obstacle detections." %}


## Problem

- Tailings dams are massive, hazardous, and chronically under-inspected — manual surveys are slow and dangerous.
- Pre-planned coverage paths break whenever the UAV encounters an obstacle or must detour home for a battery swap.
- Voxel-grid replanning is accurate but too slow for real-time decisions over large structures.


## What I built

A two-layer motion planner for commercial UAVs inspecting large slopes.

- Runs on an off-the-shelf drone (Parrot Anafi USA Gov + ROS 2).
- **Offline global planner:** generates a back-and-forth photogrammetry path on a voxel grid; uses A* to reroute around any detected collisions.
- **Online local planner:** switches to an octree for multi-resolution A* so the drone reacts instantly to battery events or unexpected obstacles.


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
    <text x="90"  y="54" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Sparse Cloud</text>
    <text x="260" y="45" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Plane Seg. &amp;</text>
    <text x="260" y="63" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Voxelization</text>
    <text x="430" y="45" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Global Path</text>
    <text x="430" y="63" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">(A* on Voxels)</text>
    <text x="600" y="45" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Collision</text>
    <text x="600" y="63" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Check</text>
    <text x="770" y="45" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Octree</text>
    <text x="770" y="63" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Replan</text>
  </svg>
  <figcaption class="caption">Sparse cloud → plane segmentation &amp; voxelization → global A* path → collision check → online octree replanning.</figcaption>
</figure>

{% include figure.liquid path="assets/img/projects/3_project/preprocessing.jpg" class="img-fluid rounded z-depth-1" caption="Preprocessing: sparse point cloud decomposed into a voxel grid, an octree, and the dominant inspection plane used to align the coverage path." %}

**Algorithm 1 — global path planning on a voxel grid.** Detects collisions along the pre-planned path and uses A* locally to reroute around each obstacle, then post-processes the result to remove unnecessary waypoints.

<pre style="background:var(--global-bg-color);border:1px solid var(--global-theme-color);border-radius:6px;padding:1rem 1.25rem;font-size:0.85rem;line-height:1.8;overflow-x:auto;font-weight:600"><b>Input:</b>  p<sub>before</sub>, p<sub>after</sub>, v<sub>grid</sub>, δ
<b>Output:</b> 𝒯<sub>p</sub>

Initialize 𝒯<sub>p</sub> ← ∅
x<sub>init</sub> ← ⌊p<sub>before</sub> / δ⌋
x<sub>goal</sub> ← ⌊p<sub>after</sub> / δ⌋
V ← A*(x<sub>init</sub>, x<sub>goal</sub>, v<sub>grid</sub>, δ)
n ← length(V),  i ← 0,  V<sub>p</sub> ← {x<sub>n</sub>}

<b>repeat</b>
    <b>if</b> ObstacleFree(x<sub>n</sub>, x<sub>i</sub>) <b>then</b>
        V<sub>p</sub> ← V<sub>p</sub> ∪ {x<sub>i</sub>}
        Remove intermediate points between x<sub>n</sub> and x<sub>i</sub>
        n ← i,  i ← 0
    <b>else</b>
        i ← i + 1
    <b>end if</b>
<b>until</b> n = 0
𝒯<sub>p</sub> ← 𝒯<sub>p</sub> ∪ V<sub>p</sub>
<b>return</b> 𝒯<sub>p</sub></pre>

**Algorithm 2 — multi-resolution replanning on an octree.** Starts at the coarsest layer and progressively refines the path through finer layers, shrinking the A* search space at each step.

<pre style="background:var(--global-bg-color);border:1px solid var(--global-theme-color);border-radius:6px;padding:1rem 1.25rem;font-size:0.85rem;line-height:1.8;overflow-x:auto;font-weight:600"><b>Input:</b>  x<sub>init</sub>, x<sub>goal</sub>, O (octree with layers l<sub>1</sub>,…,l<sub>n</sub>), δ<sub>0</sub>
<b>Output:</b> 𝒫<sub>l<sub>n</sub></sub>

Initialize 𝒫<sub>l<sub>1</sub></sub> ← ∅
Project x<sub>init</sub>, x<sub>goal</sub> → l<sub>1</sub> with resolution δ<sub>0</sub>/2
𝒫<sub>l<sub>1</sub></sub> ← A*(x<sub>init</sub>, x<sub>goal</sub>, l<sub>1</sub>, δ<sub>0</sub>/2)

<b>for</b> i = 1 <b>to</b> n−1 <b>do</b>
    Initialize 𝒯<sub>l<sub>i+1</sub></sub> ← ∅
    <b>for each</b> node v ∈ 𝒫<sub>l<sub>i</sub></sub> <b>do</b>
        𝒯<sub>l<sub>i+1</sub></sub> ← 𝒯<sub>l<sub>i+1</sub></sub> ∪ Subdivide(v)
    <b>end for</b>
    Project x<sub>init</sub>, x<sub>goal</sub> → l<sub>i+1</sub> with resolution δ<sub>0</sub>/2<sup>i+1</sup>
    𝒫<sub>l<sub>i+1</sub></sub> ← A*(x<sub>init</sub>, x<sub>goal</sub>, 𝒯<sub>l<sub>i+1</sub></sub>, δ<sub>0</sub>/2<sup>i+1</sup>)
<b>end for</b>

<b>return</b> 𝒫<sub>l<sub>n</sub></sub></pre>

The key insight: at each layer the search is restricted to the tunnel 𝒯<sub>l<sub>i+1</sub></sub> ⊆ 𝒫<sub>l<sub>i</sub></sub>, so the search space shrinks geometrically — only the path neighborhood is refined, not the entire environment.


## Experiments

- Field site: coal mine tailings dam (~205,152 m²), Greene County, Pennsylvania, USA.
- Commercial UAV: Parrot Anafi USA Gov, flying at 30 m standoff from the slope.
- Ran 8 missions with varying start/goal positions; compared voxel-grid vs. octree replanning times.

{% include figure.liquid path="assets/img/projects/3_project/tailing_dam_astar.png" class="img-fluid rounded z-depth-1" caption="Global inspection path planned over the tailings dam voxel grid, satisfying photogrammetry constraints with 31 coverage lines at 1.2 cm/px GSD." %}

{% include figure.liquid path="assets/img/projects/3_project/voxel_path.png" class="img-fluid rounded z-depth-1" caption="A* path planning over the voxel grid: raw output (left) and post-processed path with unnecessary waypoints removed (right)." %}

{% include figure.liquid path="assets/img/projects/3_project/octree_layers.png" class="img-fluid rounded z-depth-1" caption="Multi-resolution path refinement across 9 octree layers — coarse layers provide a fast initial route; finer layers progressively sharpen it." %}

{% include figure.liquid path="assets/img/projects/3_project/octree_result.jpg" class="img-fluid rounded z-depth-1" caption="Final path computed online by the A* algorithm over the voxel grid, matching the finest octree layer — obstacles avoided while preserving the inspection route." %}


## Results

<div class="row text-center">
  <div class="col-4">
    <h3 style="margin-bottom:0">2.62&times;</h3>
    <p class="text-muted">max speedup of octree over voxel-grid replanning</p>
  </div>
  <div class="col-4">
    <h3 style="margin-bottom:0">1.6&times;</h3>
    <p class="text-muted">average speedup at equivalent resolution</p>
  </div>
  <div class="col-4">
    <h3 style="margin-bottom:0">1.2 cm/px</h3>
    <p class="text-muted">ground sampling distance achieved</p>
  </div>
</div>

{% include figure.liquid path="assets/img/projects/3_project/map_result.jpg" class="img-fluid rounded z-depth-1" caption="High-resolution 3D map of the tailings dam generated through the photogrammetry-based autonomous mission, enabling structural deformation analysis and hazard monitoring." %}

- Octree outperformed the voxel grid in all 8 missions (1.41× – 2.62×).
- Multi-resolution hierarchical search reduces nodes processed without sacrificing path quality.


## Lessons learned

- A sparse initial cloud at 400 ft is enough for safe planning — detail comes from the closer inspection pass.
- Restricting A* to the octree tunnel eliminates the exponential node growth that slows voxel replanning.
- 6-connected grids need post-processing to remove zigzags; a simple obstacle-free shortcutting pass is sufficient.


## Stack & role

**My role:** designed both planners, voxelization pipeline, octree integration, experimental setup; ran field validation.

`ROS 2` &middot; `A* search` &middot; `Open3D` &middot; `octree` &middot; `voxel grids` &middot; `coverage path planning` &middot; `Python`


## Reference

P. V. G. Simplicio, G. A. S. Pereira, *"Multi-resolution UAV Path Replanning for Inspection of Tailings Dams,"* ICUAS 2025.
[PDF]({{ '/assets/pdf/ICUAS2025b.pdf' | relative_url }})
