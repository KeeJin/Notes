# VINS-MONO (2018)

!!! info "Opening remarks"

    These notes have been written with the assumption that the reader has little to no background in classical computer vision concepts. However, knowledge in basic linear algebra, calculus, SLAM and dynamics is assumed.

## VINS-MONO in a nutshell
VINS-MONO is one of the many attempts to solve the following problem: with a single camera and single imu, how can I accurately track my motion over time in an efficient way? It is one of the more influential papers for modern classical VIO.

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
vision processing frontend:
- KLT/LK
- RANSAC
  - epipolar constraint
- What is fundamental matrix model
- keyframe selection
imu preintegration: 


## Chapter 5: Estimator Initialization
- vision-only SfM
- visual-inertial alignment
  - gyro bias
  - v, g, s init
  - tangent space refinement of gravity vector

## Chapter 6: Tightly Coupled Monocular VIO

main cost function
imu measurement residual
visual measurement residual
- mention about the typical reprojection error calculation for pinhole cameras (no/low distortion)
- explain spherical model
- predicted feature: pixel in frame i -> 3d point in frame i wrt camera -> 3d point in frame i wrt imu/body
  -> 3d point in world -> 3d point in frame j -> 2d bearing on sphere
marginalization strategy: TODO
camera-rate state estimation:
  - motion-only state estimator running in parallel with the main, lower frequency optimization loop
  - only optimise the poses and velocities of the x latest imu states
  - freeze all other previous states, biases, image feature positions
  - let the main optimization loop do a full calibration on all other variables (more expensive and slower)
  - pose estimates will have small errors, errors will accumulate slightly, but that is ok in the short term. This is the price we pay to get higher update rates.
  - essentially a lightweight incremental solver using fixed linearization points
imu-rate state estimation:
  - to achieve even faster update rates of motion estimates, we can estimate the motion state by purely extrapolating using the latest IMU data (forward propagation of IMU)
  - no optimization is being carried out here, thats why it is so fast and cheap. But drift will accumulate quickly.


## Chapter 7: Relocalization

- Loop Detection
  - BRIEF descriptor: binary string descriptor comprising of neighbouring pixel intensity comparisons (of a patch)
  - DBoW2: Convert image features into BRIEF descriptors, then cluster these descriptors into a tree structure (hierarchical k-means clustering), build a histogram of "visual words" and compare images by some similarity matrix
- Feature Retrieval
  - two-step geometric outlier rejection
- When loop closure is detected, the optimiser goes from optimising for prior + imu + visual to optimising prior + imu + visual + loop closure visual. This is the sliding window optimisation getting augmented. There is also pose graph optimisation happening, covered in next section.


## Chapter 8: Global Pose Graph Optimization and Map Reuse
- why 4 dof optimisation?
- keyframe connection design
- 4 dof pgo
- pose graph load save merge

## Chapter 9: Experimental Results

## Concluding Remarks
