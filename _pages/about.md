---
layout: about
title: about
permalink: /
subtitle: Ph.D. Candidate in Robotics Engineering | <a href='https://mae.statler.wvu.edu/'>West Virginia University</a>

profile:
  align: right
  image: prof_pic.jpg
  image_circular: false # crops the image to make it circular
  more_info: >
    <p>Morgantown, WV</p>
    <p><a href="mailto:paulovgsimplicio@gmail.com">paulovgsimplicio@gmail.com</a></p>

selected_papers: true # includes a list of papers marked as "selected={true}"
social: true # includes social icons at the bottom of the page

announcements:
  enabled: true # includes a list of news items
  scrollable: true # adds a vertical scroll bar if there are more than 3 news items
  limit: 5 # leave blank to include all the news in the `_news` folder

latest_posts:
  enabled: true
  scrollable: true # adds a vertical scroll bar if there are more than 3 new posts items
  limit: 3 # leave blank to include all the blog posts
---

I build the decision-making layer that tells a robot what to map and how to move through it, safely, efficiently, and in the real world.

I'm a final-year Ph.D. candidate in Robotics at West Virginia University, where my research focuses on generating an optimal set of viewpoints under photogrammetric constraints with collision-free trajectory planning through probabilistic occupancy grids using state-of-the-art algorithms such as A* and RRT*. Most recently I extended this into reinforcement learning, training PPO agents in NVIDIA Isaac Lab to learn coverage policies from scratch.

My work spans the full autonomy stack: I have designed multi-resolution motion planners (A\*/RRT\* on octrees via OMPL and Open3D) that generate collision-free inspection corridors, and photogrammetry-aware coverage planners that enforce GSD, image-overlap, and view-geometry constraints for high-fidelity 3D reconstruction. I have also developed real-time onboard replanning with hazard detection and return-to-home logic for long-range, battery-swap missions. Currently, I am developing reinforcement learning-based (PPO) next-best-view planning in NVIDIA Isaac Lab and real-time visual-inertial state estimation using factor graphs (GTSAM).

Before joining WVU, I completed my M.Sc. in Electrical Engineering at the University of São Paulo, where I developed robust and intelligent quadrotor controllers (PID, LQR, MPC, and disturbance-observer-based) for trajectory tracking under wind gusts and parametric uncertainties. I graduated top of my class in B.Eng. Mechatronics Engineering from Tiradentes University.
