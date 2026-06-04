---
layout: page
title: Photogrammetry-Aware Coverage Planning for Autonomous Dam Inspection
description: Mission-planning algorithms that turn photogrammetry specs (GSD, overlap, view geometry) into collision-aware UAV flight paths for high-fidelity 3D maps of dams. Field-validated on a real water dam with a commercial drone.
img: assets/img/projects/2_project/thumbnail.jpg
importance: 1
category: work
related_publications: false
toc:
  sidebar: left
---

## The 15-second version

Aging dams need frequent inspection, but sending people onto a steep
downstream slope is slow, expensive, and dangerous. I built the mission-planning
layer that lets a **commercial UAV autonomously fly a dam and produce a
centimeter-accurate 3D map** — good enough to spot hazards like cracks and
seepage. The planner takes a photogrammetry spec (how small a defect you need
to see) and outputs a complete, flyable trajectory. It was validated in the
field on a real water dam, reconstructing the slope densely enough to detect
planted test hazards as small as a soccer ball.

{% include figure.liquid
   loading="eager"
   path="assets/img/projects/2_project/hero_dam_with_trajectory.jpg"
   class="img-fluid rounded z-depth-1"
   caption="The downstream slope of the Upper Deckers Creek dam (WV), the commercial UAV used, and an example inspection trajectory overlaid on the structure." %}


## Why it matters (the problem)

- **The asset base is old and under-inspected.** In the U.S. the average dam is
  ~65 years old against a ~50-year safe-design life. Cracking, seepage, and
  overtopping are leading failure modes, and the common thread is missing or
  infrequent inspection.
- **Manual inspection doesn't scale.** Steep slopes, large surface areas, and
  safety constraints make human inspection slow and costly.
- **Images alone aren't enough.** 2D imagery (even with crack-detection CNNs)
  has no depth, so you can't measure change over time. A **3D point cloud**
  lets you compare today's slope against last quarter's and flag what moved.

The hard part isn't flying the drone — it's deciding **where exactly to fly**
so the resulting images reconstruct into a usable 3D model.


## What I built (the contribution)

A mission-planning pipeline that converts a **detection requirement** ("I need
to see defects ≥ X cm") into a **fully specified, flyable UAV trajectory** that
satisfies photogrammetry constraints over a sloped structure.

- Models the dam's downstream face as a simplified geometric primitive (an
  isosceles trapezoid) so the planner generalizes across sites from a CAD plan
  or a few on-site measurements.
- Generates a **boustrophedon ("lawn-mower") coverage path** with per-row
  waypoints, automatically computing flight distance, number of rows, row
  spacing, and UAV speed from the camera model and the required resolution.
- Handles the **slope geometry explicitly** — rotating the planned path into the
  world frame and setting the camera gimbal tilt so the sensor stays normal to
  the slope (a parameter most planners ignore).
- Runs on **off-the-shelf hardware**: a commercial drone driven through its SDK,
  wrapped in ROS 2 so it plugs into the rest of a robotics stack.


## How it works (methodology)

The pipeline is four stages: **define the target → solve the geometry →
generate waypoints → fly and reconstruct.**

{% include figure.liquid
   path="assets/img/projects/2_project/pipeline_diagram.png"
   class="img-fluid rounded z-depth-1"
   caption="End-to-end pipeline: detection requirement and camera model in, flyable trajectory and 3D map out." %}

**1. Detection requirement → resolution.**
The input is the smallest hazard you need to catch. That sets the Ground
Sampling Distance (GSD, the real-world size of one pixel). A practical finding
from the field work (see *Lessons learned*) is that GSD has to be far tighter
for 3D reconstruction than the textbook "half the object size" rule suggests.

**2. Resolution → flight geometry.**
From the GSD and the camera intrinsics (focal length, sensor size, pixel
count, field of view), the planner computes how far from the slope to fly, the
camera footprint on the ground, how many coverage rows are needed for the
required vertical image overlap, and the spacing between them.

**3. Geometry → waypoints.**
The planner lays out the back-and-forth path in a frame attached to the dam,
then rotates it into the UAV's world frame to account for the slope angle. This
keeps the standoff distance to the slope constant on every row — which is what
keeps resolution uniform across the whole map.

{% include figure.liquid
   path="assets/img/projects/2_project/geometry_trapezoid.png"
   class="img-fluid rounded z-depth-1"
   caption="Dam slope modeled as a trapezoid with both reference frames. The planned path lives in the dam frame, then rotates into the UAV world frame by the slope angle." %}

**4. Fly and reconstruct.**
The trajectory runs autonomously on the drone via its SDK (wrapped in ROS 2
for data capture). Captured images go through a Structure-from-Motion /
Multi-View Stereo pipeline to produce a dense 3D point cloud of the slope.

> **One-line summary for the room:** *"You tell it the smallest defect you care
> about; it tells the drone exactly how to fly to see it in 3D."*


## Experiments

Validated in the field on the downstream slope of the **Upper Deckers Creek
dam (West Virginia)** using a commercial quadrotor flown autonomously.

- Flew multiple missions sweeping resolution from coarse/long-range to dense/
  close-range to map the tradeoff between **map quality and flight time**.
- Planted **known test hazards** of different sizes (a box and two soccer balls)
  on the slope to measure whether the resulting point cloud could actually
  resolve them.
- Reconstructed point clouds with open-source SfM/MVS tooling and used point-
  cloud comparison to isolate the hazards against a clean baseline scan.

{% include video.liquid
   path="https://www.youtube.com/embed/SuqJXi0uRdk"
   class="img-fluid rounded z-depth-1"
   caption="Field experiment summary: autonomous dam-inspection flight and the resulting 3D reconstruction." %}


## Results

The planner produced dense 3D maps in which the planted hazards were clearly
recoverable, and it let us trade resolution against flight time on demand.

| Mission        | GSD (cm/px) | Standoff to slope | Coverage rows | Point-cloud points |
|----------------|-------------|-------------------|---------------|--------------------|
| Long-range     | 1.40        | ~33.5 m           | 2             | ~8.5 M             |
| Detailed       | 0.84        | ~21.0 m           | 4             | ~37.5 M            |

- The **long-range** mission hit near-detailed map quality in **half the flight
  time** — the kind of cost/coverage tradeoff an operator actually tunes.
- The **detailed** mission produced a ~37.5M-point cloud where all planted
  hazards were visible.
- Differencing a hazard scan against a clean baseline **automatically
  highlighted the introduced hazards** — the basis for change-over-time
  monitoring.

{% include figure.liquid
   path="assets/img/projects/2_project/pointcloud_3d_map.jpg"
   class="img-fluid rounded z-depth-1"
   caption="Dense 3D reconstruction of the dam's downstream slope." %}

<div class="row">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid
       path="assets/img/projects/2_project/hazards_long_range.jpg"
       class="img-fluid rounded z-depth-1"
       caption="Long-range mission — hazards visible on the slope." %}
  </div>
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid
       path="assets/img/projects/2_project/hazards_detailed.jpg"
       class="img-fluid rounded z-depth-1"
       caption="Detailed mission — denser cloud, hazards sharper." %}
  </div>
</div>


## Lessons learned (what I'd tell another engineer)

- **3D reconstruction is hungrier than 2D detection.** The standard "GSD = half
  the smallest feature" guidance was insufficient for point-cloud recovery; in
  practice ~1/10 of the object size was what actually resolved hazards.
- **Slope alignment matters more than expected.** Coarse gimbal control left
  thin, low-density patches in the reconstruction; precise tilt matching the
  slope angle fixed it.
- **GPS is fine for path-following but weak on precise landing.** Autonomous
  return-to-home landed near, not on, the pad — motivating a vision-based
  landing-pad detector (and RTK as a hardware alternative).


## Tech stack & role

- **My role:** designed and implemented the mission-planning algorithms,
  the camera/geometry models, the ROS 2 SDK wrapper, and ran the field
  experiments and 3D reconstruction analysis.
- **Robotics:** ROS 2, commercial UAV SDK, autonomous waypoint navigation,
  gimbal control.
- **Perception / 3D:** photogrammetry (Structure-from-Motion, Multi-View
  Stereo), dense point clouds, point-cloud registration & differencing.
- **Languages / tools:** Python, open-source SfM/MVS + point-cloud tooling.
- **Domain:** UAV inspection of civil infrastructure, coverage path planning.


## Reference

P. V. G. Simplicio and G. A. S. Pereira, *"Mission Planning for
Photogrammetry-Based Autonomous 3D Mapping of Dams Using a Commercial UAV,"*
2024 International Conference on Unmanned Aircraft Systems (ICUAS),
Chania, Greece, 2024.
[PDF]({{ '/assets/pdf/icuas2024_dam_photogrammetry.pdf' | relative_url }})
&nbsp;·&nbsp;
[Video](https://youtu.be/SuqJXi0uRdk)
