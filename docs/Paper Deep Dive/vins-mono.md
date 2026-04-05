# VINS-MONO (2018)

!!! info "Opening remarks"

    These notes have been written with the assumption that the reader has little to no background in classical computer vision concepts. However, knowledge in basic linear algebra, calculus, SLAM and dynamics is assumed.

## Chapter 1: Introduction

### Main points:

- Monocular Visual-Odometry (VO) systems have trouble recovering the metric scale. This means that while it may accurately track the shape of the body's trajectory, the actual scale will more often than not be inaccurate.
- Incorporating an IMU into a VO setup helps improve estimation of the trajectory's true metric scale. It also allows the system to fall back on inertial "dead reckoning" when visual tracking fails over some frames.
- VIO initialisation is known to be non-trivial.
- Long-term drift is an inherent problem in VIO systems. To tackle this issue, we need to introduce concepts like loop detection & closure, global optimization and relocalization.
- VINS-MONO's work consists of the following:
  - a procedure for initializing the estimator (previous work)
  - a tightly-coupled optimization-based monocular VIO algorithm
  - online relocalization (previous work), 4 DOF global pose graph optimisation
  - pose graph reuse (previous work)

### Questions:

1. What is a monocular system?
   - A system that only uses a single camera. This is in contrast with a binocular/stereo system, which employs 2 cameras.
2. What is a tightly coupled system?
   - Explained in next section.
3. Why 4 DOF? What are the 4 DOFs?
   - TODO

## Chapter 2: Related Work

### Characterizing Visual-Inertial Sensor Fusion

- Loose Coupling:
  - Treat vision and IMU as separate modules
  - Optimization occurs separately, fuse results together at a later stage (usually using an EKF)
  - "Let vision estimate pose, let IMU estimate motion, then combine their answers."

- Tight Coupling
  - Jointly optimize visual reprojection errors and IMU preintegration constraints together
  - Let vision estimate pose, let IMU estimate motion, then combine their answers.
  - Batch graph optimization / Bundled adjustment
  - Graph optimisation usually requires more compute, so it is usually done over a fixed-size sliding window + marginalizing past states.

### Characterizing Visual Measurement Processing

- Direct: minimise photometric error
- Indirect: minimise geometric displacement

### Dealing with IMU data

IMU data frequency tends to be a lot higher than camera frame rates. The typical ways to fuse IMU data would be:

- using EKF for fusion
- when formulating problem as a graph optimization problem, an efficient technique known as IMU pre-integration could be used. This technique avoids repeated IMU reintegration (to calculate displacement and velocity in the translational and rotational dimensions)

## Chapter 3: Overview

Nothing much to explain here, main thing to take note on are the notations and frame definitions specified.

## Chapter 4: Measurement Preprocessing

## Chapter 5: Estimator Initialization

## Chapter 6: Tightly Coupled Monocular VIO

## Chapter 7: Relocalization

## Chapter 8: Global Pose Graph Optimization and Map Reuse

## Chapter 9: Experimental Results

## Concluding Remarks
