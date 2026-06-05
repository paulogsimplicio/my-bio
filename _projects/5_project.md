---
layout: page
title: Robust and Intelligent Control of Quadrotors Subject to Wind Gusts
description: Two DNN-augmented control architectures that keep a commercial quadrotor on track during trajectory following under real wind gusts, no wind sensor needed.
img: assets/img/projects/5_project/thumbnail.png
importance: 4
category: work
selected: true
toc:
  sidebar: left
---

<div class="text-center mt-n2 mb-3">
  <p class="mb-1" style="font-size:0.95rem;letter-spacing:0.03em">
    <strong>P. V. G. Simplicio</strong> &middot; J. R. S. Benevides &middot; R. S. Inoue &middot; M. H. Terra
  </p>
  <p class="text-muted mb-0" style="font-size:0.875rem">
    <em>IET Control Theory &amp; Applications, 2024</em>
  </p>
</div>
<hr style="border:none;border-top:2px solid var(--global-theme-color);opacity:0.45;margin-bottom:2rem">

## Overview

> **Standard controllers drift when wind hits. A DNN trained on real flight data steers the controller back on track, no wind sensor required.** Two architectures tested on a commercial Parrot Bebop 2.0 under gusts up to 20.5 m/s; both improve every controller by up to 21%.

{% include figure.liquid loading="eager" path="assets/img/projects/5_project/experiment.jpg" class="img-fluid rounded z-depth-1" caption="Parrot Bebop 2.0 flying under wing-generated wind gusts in the experimental arena, with Vicon markers for motion capture." %}


## Problem

- Quadrotors are highly sensitive to wind gusts, even moderate disturbances cause significant trajectory drift.
- Classical controllers (LQR, PID, feedback linearization) have no built-in mechanism to adapt to unknown external disturbances.
- Dedicated wind sensors add hardware complexity; the goal is disturbance rejection from flight data alone.


## What I built

Two intelligent control architectures that combine a Robust Linear Quadratic Regulator (RLQR) with Deep Neural Networks (DNNs).

- **Architecture 1 — DNN as reference:** the network maps current and desired positions to a corrected reference signal fed to the controller, learned offline from real flights.
- **Architecture 2 — DNN as disturbance estimator (DOBC):** the network estimates the wind disturbance from trajectory error; a Kalman filter smooths the estimate; a compensator cancels it.
- Both architectures are modular, the same DNN wraps any of the four controllers tested (RLQR, LQR, PID, FL).


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
    <text x="90"  y="54" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Flight Data</text>
    <text x="260" y="45" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">DNN</text>
    <text x="260" y="63" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Training</text>
    <text x="430" y="45" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Reference /</text>
    <text x="430" y="63" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Disturbance Est.</text>
    <text x="600" y="45" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">RLQR /</text>
    <text x="600" y="63" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Controller</text>
    <text x="770" y="45" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Trajectory</text>
    <text x="770" y="63" dominant-baseline="central" text-anchor="middle" font-size="13" font-weight="600" font-family="sans-serif" fill="var(--global-text-color)">Tracking</text>
  </svg>
  <figcaption class="caption">Flight data → DNN training → reference signal or disturbance estimate → RLQR/controller → robust trajectory tracking.</figcaption>
</figure>

{% include figure.liquid path="assets/img/projects/5_project/arch1.png" class="img-fluid rounded z-depth-1" caption="Architecture 1: DNN maps current and desired positions to a corrected reference trajectory, fed directly to RLQR, LQR, PID, or FL." %}

**Algorithm 1 — DNN as reference for controllers.** The DNN output q<sup>r</sup> replaces the raw desired trajectory, letting the controller react to a learned, wind-aware reference instead of the raw setpoint.

<pre style="background:var(--global-bg-color);border:1px solid var(--global-theme-color);border-radius:6px;padding:1rem 1.25rem;font-size:0.85rem;line-height:1.8;overflow-x:auto;font-weight:600"><b>Require:</b> Desired trajectory q<sup>d</sup>
<b>Ensure:</b>  Velocity vector ν

Initialise controller gains (RLQR / LQR / PID / FL)

<b>while</b> receiving desired trajectory <b>do</b>
    Retain q<sup>d</sup> and q
    Compute q<sup>r</sup>, q̇<sup>r</sup>  ← DNN({q̃<sub>i</sub>, q̃<sub>i+1</sub>}, q<sup>d</sup>)
    Calculate q̃ ← q<sup>r</sup> − q,  q̇̃,  ∫q̃
    <b>if</b> RLQR <b>or</b> LQR: u<sub>i</sub> ← K<sub>i</sub> x<sub>i</sub>   (Eq. 15)
    <b>if</b> PID:       u<sub>i</sub> ← K<sub>p</sub>q̃ + K<sub>d</sub>q̇̃ + K<sub>i</sub>∫q̃
    <b>if</b> FL:        u<sub>i</sub> ← K<sub>p</sub>q̃ + K<sub>d</sub>q̇̃
    Generate ν via control vector equation
<b>end while</b></pre>

{% include figure.liquid path="assets/img/projects/5_project/arch2.png" class="img-fluid rounded z-depth-1" caption="Architecture 2 (DOBC): DNN estimates wind disturbance from trajectory error; Kalman filter smooths the estimate; compensator cancels the disturbance before it reaches the plant." %}

**Algorithm 2 — DNN as disturbance estimator.** The disturbance estimate d̂ feeds a compensator that subtracts the wind effect from the control signal, keeping all four controllers tighter on the reference.

<pre style="background:var(--global-bg-color);border:1px solid var(--global-theme-color);border-radius:6px;padding:1rem 1.25rem;font-size:0.85rem;line-height:1.8;overflow-x:auto;font-weight:600"><b>Require:</b> Desired trajectory q<sup>d</sup>
<b>Ensure:</b>  Velocity vector ν

Initialise controller gains (RLQR / LQR / PID / FL)

<b>while</b> receiving desired trajectory <b>do</b>
    Retain q<sup>d</sup> and q
    Compute q̃, q̇̃, ∫q̃
    Compute controller signal u<sub>i</sub>  (same as Alg. 1)
    Estimate d̂  ← DNN(q̃<sub>x</sub>, q̃<sub>y</sub>)
    Compute filtered d̂  ← Kalman filter
    Compute d̈  ← derivative of d̂
    u<sup>pd</sup><sub>dist,i</sub> ← α K<sub>com,i</sub> d̂<sub>i</sub> + ϱ d̂<sub>i+1</sub>
    u<sub>com,i</sub> ← u<sub>i</sub> + u<sup>pd</sup><sub>dist,i</sub>
    Generate ν via control vector equation
<b>end while</b></pre>


## Experiments

- Platform: Parrot Bebop 2.0 quadrotor, ROS, Vicon motion capture system at 100 Hz.
- Desired trajectory: circular path, radius 0.3 m, velocity 0.15 m/s in the xyz plane.
- Wind disturbance: brushless motor + ESC generating gusts from 1.0 to 20.5 m/s.
- Each architecture tested 5 times per controller; metrics averaged across runs.

{% include figure.liquid path="assets/img/projects/5_project/trajectory_standalone.png" class="img-fluid rounded z-depth-1" caption="3D trajectory tracking with standalone controllers under wind gusts. RLQR (purple) stays closest to the desired circular path (dashed)." %}

{% include figure.liquid path="assets/img/projects/5_project/trajectory_arch1.png" class="img-fluid rounded z-depth-1" caption="Trajectory tracking with Architecture 1 (DNN as reference). All controllers tighten around the desired path compared to their standalone versions." %}


## Results

<div class="row text-center">
  <div class="col-4">
    <h3 style="margin-bottom:0">21%</h3>
    <p class="text-muted">max improvement in tracking error (Architecture 2, LQR)</p>
  </div>
  <div class="col-4">
    <h3 style="margin-bottom:0">0.0679 m</h3>
    <p class="text-muted">best MAE achieved — RLQR + DOBC architecture</p>
  </div>
  <div class="col-4">
    <h3 style="margin-bottom:0">230 Hz</h3>
    <p class="text-muted">control loop update rate maintained with DNN overhead</p>
  </div>
</div>

{% include figure.liquid path="assets/img/projects/5_project/boxplot.png" class="img-fluid rounded z-depth-1" caption="Box-plot of mean absolute error across all controllers and architectures. Both DNN architectures outperform standalone controllers; Architecture 2 (DOBC) achieves the lowest error spread." %}

- Architecture 2 (DOBC) outperforms Architecture 1 across all four controllers (18–21% vs. 14–17% improvement).
- RLQR is the strongest standalone controller and benefits most from DNN augmentation in Architecture 1.


## Lessons learned

- DNNs can learn disturbance patterns purely from position error data, no dedicated wind sensor required.
- The RLQR's inherent robustness to parametric uncertainty makes it the best pairing with either DNN architecture.
- A Kalman filter between the DNN and compensator is essential: raw network output is too noisy for direct compensation.


## Stack & role

**My role:** lead author, model derivation, DNN design and training, controller implementation, experimental setup, data analysis, and writing.

`ROS` &middot; `Python` &middot; `Keras / TensorFlow` &middot; `RLQR` &middot; `deep neural networks` &middot; `Kalman filter` &middot; `Parrot Bebop 2.0` &middot; `Vicon`


## Reference

P. V. G. Simplicio, J. R. S. Benevides, R. S. Inoue, M. H. Terra, *"Robust and intelligent control of quadrotors subject to wind gusts,"* IET Control Theory & Applications, 2024.
[PDF]({{ '/assets/pdf/control.pdf' | relative_url }}) &middot; [DOI](https://doi.org/10.1049/cth2.12615)
