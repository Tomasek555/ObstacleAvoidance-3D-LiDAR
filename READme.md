# Obstacle Avoidance Using 3D LiDAR (“BounceAlgorithm”)

This project comes from my bachelor thesis in Automation Engineering and implements a real-time obstacle avoidance system for a Clearpath Jackal mobile robot using a Velodyne VLP-16 3D LiDAR and ROS 2.

The goal of the project was to move the Jackal from simple pre-defined paths to reactive navigation in a dynamic environment (e.g. corridors, lab or warehouse-like spaces with moving people and equipment). The system runs on ROS 2 and processes the 3D LiDAR point cloud in real time to detect obstacles and adjust the robot’s motion accordingly.

---

## Project overview

The main pipeline is:

1. **Point cloud acquisition**  
   Velodyne VLP-16 3D LiDAR mounted on top of the Jackal publishes raw point clouds into ROS 2.

2. **Ground and overhead filtering**  
   A custom C++ point cloud processor:
   - Estimates ground candidates using an angle-based method on consecutive LiDAR beams,
   - Fits a ground plane (e.g. with RANSAC),
   - Removes points close to this plane (ground) and points above a certain height (overhead obstacles the robot can safely pass under).

3. **Nearest obstacle detection**  
   For the filtered point cloud, the algorithm computes the horizontal distance of each point and selects the closest one as the nearest obstacle in front of the robot.

4. **“Bounce” obstacle avoidance behaviour**  
   While driving straight, the robot constantly checks whether an obstacle enters a configurable safety envelope. If an obstacle is detected:
   - first it slows down,
   - then stops if the distance keeps decreasing,
   - and finally “bounces” away by turning away from the nearest point until the path is clear again.

5. **Commanding the Jackal**  
   A velocity controller node publishes linear and angular velocity commands on `cmd_vel` (simulation or real robot) based on the obstacle avoidance state.

---

## Software architecture

The implementation was built on ROS 2 (e.g. Humble) and written in C++. The main components are:

- **LiDAR driver package** – brings VLP-16 data into ROS 2 as a point cloud topic.
- **BounceAlgorithm package** – core logic for:
  - Ground / overhead filtering,
  - Nearest obstacle detection,
  - Obstacle avoidance state machine (“bounce” behaviour).
- **Point cloud processor class** – encapsulates ground plane estimation and nearest point calculation.
- **Velocity controller** – translates obstacle information into `cmd_vel` commands for the Jackal.
- **Config and launch files** – YAML parameters (thresholds, velocities, simulation/real switch) and a launch file to start everything with one command.

---

## Technologies used

- **Robot platform:** Clearpath Jackal UGV  
- **Sensor:** Velodyne VLP-16 3D LiDAR  
- **Middleware:** ROS 2  
- **Language:** C++  
- **Simulation:** Gazebo with Jackal + LiDAR model  
- **Hardware:** Onboard computer on the robot + external laptop for development

---

## Results

The system was tested both in Gazebo and on the real Jackal robot. The robot was able to:

- Drive forward in a cluttered environment,
- Detect obstacles entering the safety zone,
- Slow down, stop and steer away using the bounce behaviour,
- Continue towards free space once the path was clear again.

The thesis was evaluated as a high-quality work by the examination board and showed that 3D LiDAR-based obstacle avoidance with a relatively simple reactive algorithm is feasible in real time on an embedded platform.

---

## Thesis

This project is documented in my bachelor thesis **“[Obstacle Avoidance Using 3D LiDAR]([url](https://www.theseus.fi/handle/10024/881192))”**, which describes the theory, algorithm design, ROS 2 architecture and experimental results in more detail.
