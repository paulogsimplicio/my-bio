---
layout: page
title: A Behavior Tree Approach for Battery-Aware Inspection of Large Structures Using Drones
description: A behavior tree framework that autonomously manages battery events during UAV inspection missions, returns home, waits for recharge, and resumes from where it left off.
img: assets/img/projects/4_project/thumbnail.png
importance: 3
category: work
selected: true
toc:
  sidebar: left
---

<div class="text-center mt-n2 mb-3">
  <p class="mb-1" style="font-size:0.95rem;letter-spacing:0.03em">
    B. M. Rocamora Jr. &middot; <strong>P. V. G. Simplicio</strong> &middot; G. A. S. Pereira
  </p>
  <p class="text-muted mb-0" style="font-size:0.875rem">
    <em>ICUAS &mdash; Crete, Greece, 2024</em>
  </p>
</div>
<hr style="border:none;border-top:2px solid var(--global-theme-color);opacity:0.45;margin-bottom:2rem">

## Overview

> **The drone runs out of battery mid-inspection. Instead of waiting for a human to intervene, it lands, waits for a battery swap, and picks up exactly where it stopped, fully autonomously.** Field-validated on a real coal ash pit using a commercial quadrotor.

{% include figure.liquid loading="eager" path="assets/img/projects/4_project/hero.png" class="img-fluid rounded z-depth-1" caption="Inspection mission over a tailings dam: blue path flown before the low-battery event, red path resumed automatically after battery replacement." %}


## Problem

- Large-structure inspections routinely exceed a single battery charge, human operators must manually restart mid-mission.
- Manual restarts waste time, introduce coverage gaps, and require an experienced pilot on site.
- Standard path planners treat battery as unlimited; there is no built-in mechanism to pause, return, and resume.


## What I built

A behavior tree (BT) that makes battery management a first-class mission behavior.

- Runs on a Parrot ANAFI USA via ROS 2 + `py_trees`; plugs into any external path planner.
- Monitors battery, GPS, and drone state continuously through a blackboard shared across the tree.
- On low battery: pushes the current coordinate back onto the waypoint queue, flies home, lands, and idles until the battery is replaced, then resumes autonomously.


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
    <text x="90"  y="54" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">State Update</text>
    <text x="260" y="54" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Pre-flight</text>
    <text x="430" y="45" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Battery</text>
    <text x="430" y="63" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Emergency?</text>
    <text x="600" y="45" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Return &amp;</text>
    <text x="600" y="63" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Recharge</text>
    <text x="770" y="45" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Resume</text>
    <text x="770" y="63" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Mission</text>
  </svg>
  <figcaption class="caption">State update → pre-flight checks → battery emergency handler → return &amp; recharge → resume from last waypoint.</figcaption>
</figure>

{% include figure.liquid path="assets/img/projects/4_project/behavior_tree.png" class="img-fluid rounded z-depth-1" caption="Proposed Behavior Tree. The parallel root ticks state-update nodes and the mission sequence simultaneously at 1 Hz. The battery emergency sub-tree preempts normal navigation whenever charge drops below threshold." %}

**Behavior tree structure.** The root parallel node runs state updates and the mission concurrently. The mission sequence handles pre-flight once (OneShot), then loops through in-flight tasks via a priority selector:

<pre style="background:var(--global-bg-color);border:1px solid var(--global-theme-color);border-radius:6px;padding:1rem 1.25rem;font-size:0.85rem;line-height:1.8;overflow-x:auto;font-weight:600">Root: <b>Parallel</b>
├── Update Drone State
│   ├── StateToBlackboard
│   ├── BatteryToBlackboard
│   └── GPSToBlackboard
└── <b>Sequence</b>: Mission
    ├── <b>OneShot</b>: Pre-flight
    │   ├── SetHome  →  Plan
    │   └── <b>Selector</b>: Takeoff
    │       ├── Inverter → State == LANDED?
    │       └── <b>Sequence</b>(M): Takeoff → SetGoal → MoveToGoal
    └── <b>Selector</b>(M): In-Flight Tasks  ← priority order
        ├── <b>Sequence</b>(M): Battery Emergency
        │   ├── <b>OneShot</b>: Battery &lt; Threshold?
        │   ├── MoveToHome → Land → IdleLanding
        │   └── Replan
        ├── <b>Sequence</b>(M): Next Waypoint
        │   ├── State == HOVERING?
        │   ├── PopGoal → MoveToGoal
        └── IdleFlying</pre>

{% include figure.liquid path="assets/img/projects/4_project/architecture.png" class="img-fluid rounded z-depth-1" caption="Control architecture: the BT Manager requests waypoints from the Path Planner and commands the drone through a ROS 2 bridge, reacting to live battery and state feedback." %}


## Experiments

- Field site: abandoned coal ash pit, flown with a Parrot ANAFI USA Gov drone.
- Battery emergency simulated by setting the critical threshold to 45% (well above the drone's internal 15% cutoff).
- 8 waypoints loaded from a Google Earth plan; drone started at 61% battery.

{% include figure.liquid path="assets/img/projects/4_project/planned_path.jpg" class="img-fluid rounded z-depth-1" caption="Mission plan: 8 waypoints selected via Google Earth over the coal ash pit, defining the lawn-mower coverage path flown by the drone." %}


## Results

<div class="row text-center">
  <div class="col-4">
    <h3 style="margin-bottom:0">100%</h3>
    <p class="text-muted">of waypoints covered despite mid-mission battery swap</p>
  </div>
  <div class="col-4">
    <h3 style="margin-bottom:0">0</h3>
    <p class="text-muted">human interventions needed to resume after recharge</p>
  </div>
  <div class="col-4">
    <h3 style="margin-bottom:0">3D map</h3>
    <p class="text-muted">photogrammetric reconstruction generated from collected imagery</p>
  </div>
</div>

{% include figure.liquid path="assets/img/projects/4_project/trajectory.png" class="img-fluid rounded z-depth-1" caption="3D mission trajectory: green line flown before the low-battery event (red dot), magenta line resumed after battery replacement. Battery percentage annotated at key points." %}

- Drone returned home autonomously, landed, and resumed from the exact interruption point after recharge.
- BT executes offboard on a remote computer, the drone control loop never stops during battery replacement.


## Lessons learned

- Behavior trees scale more cleanly than state machines for this problem: adding new fault behaviors is a local tree edit, not a global state redesign.
- Encoding the interrupted waypoint back onto the queue is the key to seamless resumption, no replanning needed.
- Keeping the BT offboard means the mission state survives a battery swap without any special hardware.


## Stack & role

**My role:** co-designed the BT architecture, ROS 2 integration, and field experiment setup.

`ROS 2` &middot; `py_trees` &middot; `behavior trees` &middot; `Parrot ANAFI USA` &middot; `photogrammetry` &middot; `Python`


## Reference

B. M. Rocamora Jr., P. V. G. Simplicio, G. A. S. Pereira, *"A Behavior Tree Approach for Battery-Aware Inspection of Large Structures Using Drones,"* ICUAS 2024.
[PDF]({{ '/assets/pdf/A_Behavior_Tree_Approach_for_Battery-Aware_Inspection_of_Large_Structures_Using_Drones.png' | relative_url }})
