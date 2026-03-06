# 🧩 Software Development Ecosystem Guide

This document explains the **custom software development ecosystem for the Unitree G1 EDU**. We will outline the **different development areas and their specific purposes**, helping you decide which path to take based on the capabilities you wish to implement.

There are primarily 6 development areas, divided between "Classic" approaches and more "Modern" methods.

## ⚙️ Classic Development

Control (for programming explicit movements) and Application Orchestration.

### 1 - SDK Low Level (Control Engineering)

- **Purpose:** For classic control researchers or creating highly specific movements. You want to design your own mathematical algorithm (MPC, Whole Body Control) to move a leg or maintain balance. You have total control over every motor (torque, position).
- **Tools:** Unitree SDK2.
- **Workflow:** Program in C++ or Python $\rightarrow$ Test in `unitree_mujoco` $\rightarrow$ Deploy to the Real Robot.

### 2 - SDK High Level (User Mode)

- **Purpose:** To send the robot to a specific point, ask it to squat, or read battery status. You don't worry about how the legs move.
- **Tools:** Unitree SDK2 (Python/C++).
- **Workflow:** Write the script $\rightarrow$ Run it on the Real Robot.
- **Simulation:** Limited at the moment. Physics simulators (MuJoCo) lack the logic to interpret High Level commands.

### 3 - ROS 2 (Applications and Navigation)

- **Purpose:** To make the robot autonomous. Here you integrate cameras, LiDAR, maps, and all custom software. You use standard packages like Nav2 (to go from point A to B avoiding obstacles) or MoveIt (to move arms and grasp objects).
- **Tools:** `unitree_ros2`, ROS 2 (Humble/Jazzy), Nav2, MoveIt 2.
- **Workflow:** External PC $\rightarrow$ ROS 2 $\rightarrow$ Real Robot.
- **Simulation:** Only available when using Low Level, as ROS2 translates to the robot's language and can be simulated; High Level cannot.

## 🧠 Modern Development

Artificial Intelligence / Reinforcement Learning. This is where the G1 shines. You don't program "how" to walk; the robot "learns" it.

### 4 - Reinforcement Learning (RL) - Locomotion

- **Purpose:** For the robot to learn how to walk on difficult terrain, recover from pushes, or perform parkour. The neural network learns through trial and error.
- **Tools:**
  - **Training:** NVIDIA Isaac Lab (or Isaac Gym). Vital because it simulates thousands of robots simultaneously on the GPU for rapid learning.
  - **Deployment:** Unitree SDK2 (Low Level).
- **Workflow:** Train in Isaac Lab (powerful PC) $\rightarrow$ Generate a `.pt` file (neural policy) $\rightarrow$ Copy it to the Real (or simulated) Robot $\rightarrow$ A Python script reads the network and sends Low Level commands to the motors.

### 5 - Imitation Learning / Teleoperation (Hands and Tasks)

- **Purpose:** To teach the robot to manipulate objects (folding clothes, cooking, opening doors) by copying human movement.
- **Tools:** - **Hardware:** VR Headsets (Apple Vision Pro, Meta Quest) or motion capture gloves.
  - **Software:** Unitree's proprietary repositories or platforms for teleoperation, as indicated in the repository guide.
- **Workflow:** Put on the headset and move your hands (Teleoperation) $\rightarrow$ Record the data $\rightarrow$ Train an AI $\rightarrow$ The AI controls the Real Robot's arms (via real or simulated Low Level SDK).

### 6 - Vision Language Action and World Model Action

- **Purpose:** To teach the robot to understand human language, perceive its environment, and act based on both while attempting to generalize. For example, telling it in plain English to "tidy the table" and having it perform the task. This is a step beyond RL and IL.
- This is a highly advanced and still experimental field. While GitHub repositories exist, implementing them on a full humanoid is exceptionally difficult, so we will not cover this section for now.

---

If we want to integrate or "orchestrate" any independent software we develop with the robot and its other capabilities, we will do so through **ROS2**. It is the backbone that allows us to synchronize different modules and integrate them into reality.

In summary, the Unitree G1 EDU is an exceptional research and development platform, standing out especially in its "modern" development capabilities, which differentiate it from traditional robotics.