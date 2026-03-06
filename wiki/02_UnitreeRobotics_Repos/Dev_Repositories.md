# 🧩 Guide to Official Unitree Robotics Repositories

This document is a brief guide to the different **official Unitree Robotics repositories**, focused on those used for the **Unitree G1 EDU** humanoid robot. We will explain **what they are used for, where they will be used, and what relationships exist between them (when applicable)**, since at first it can be confusing to understand which ones we are interested in or need to install.

**NOTE:** When cloning repositories, use the **FORKS from UAI-BIOARABA**, so we ensure stability and can modify whatever we want. If the FORK of the repository you want to clone does not exist yet, first create the FORK and then clone it.

---

## 🔗 Official GitHub Accounts

- [GitHub Unitree Robotics](https://github.com/unitreerobotics)
- [GitHub UAI-BIOARABA](https://github.com/UAI-BIOARABA)

---

## 🧬 The Core (Communication with the Robot)

These are the foundations. Without this, we cannot talk to the hardware.

- [unitree_sdk2](https://github.com/unitreerobotics/unitree_sdk2.git) and [unitree_sdk2_python](https://github.com/unitreerobotics/unitree_sdk2_python.git)

What is it?: It is the official "driver". It is a low-level library (C++ and Python) that allows us to send commands to the motors (torques, positions) and read the robot’s state (IMU, encoder).

What is it for?: If we want to program a movement manually (e.g., move an arm) or create our own controller from scratch without using ROS, we use this.

Relationship: All the other repositories (ROS, simulators) use this SDK underneath to "translate" their commands into the robot's language.

---

## 🤖 Integration with ROS (Robot Operating System)

Anyone coming from classical or academic robotics will spend a lot of time here.

- [unitree_ros2](https://github.com/unitreerobotics/unitree_ros2.git)

What is it?: It is a wrapper around SDK2 for ROS 2.

What is it for?: It converts robot data into ROS "topics". It allows us to use standard ROS tools such as RViz (for visualization), Nav2 (for navigation), or communicate the G1 with other nodes (cameras, external sensors).

Note: Unitree strongly focuses on ROS 2 (not ROS 1) for the G1.

---

## 🧑‍💻 Simulation and Control (Pure Physics)

This is where we test our C++/Python code before breaking the real robot.

- [unitree_mujoco](https://github.com/unitreerobotics/unitree_mujoco.git)

What is it?: A simulation environment based on MuJoCo. MuJoCo is a very fast and precise physics engine, ideal for rigid body dynamics.

What is it for?: This is our testing ground. If we write a control script using unitree_sdk2, we can run it almost unchanged in this simulator.

Advantage: It is lightweight and simulates contact physics very well. It is also used to validate control algorithms (walking, balance) before running them on the real robot.

---

## 🦿 Locomotion (Reinforcement Learning)

This is where things usually get confusing with the names (Isaac Gym, Isaac Sim, Isaac Lab). Unitree uses NVIDIA tools to train the robot to "learn" how to walk or manipulate objects.

- [unitree_rl_gym](https://github.com/unitreerobotics/unitree_rl_gym.git) (Legacy/Old)

Status: Based on the old "Isaac Gym". Although it works, it is becoming obsolete compared to Isaac Lab.

Use: Reinforcement Learning (RL) training for walking.

- [unitree_rl_lab](https://github.com/unitreerobotics/unitree_rl_lab.git) (The current standard)

What is it?: A repository based on Isaac Lab (NVIDIA’s modern framework).

What is it for?: This is where we train neural networks. If we want the robot to learn how to walk on uneven terrain by itself, we use this.

Relationship with Isaac Sim:

Isaac Sim is the simulator (the application with nice graphics based on Omniverse).

Isaac Lab is the programming library that runs on top of Isaac Sim to make robot training easier.

unitree_rl_lab is the specific configuration by Unitree to use these tools with the G1.

- [unitree_rl_mjlab](https://github.com/unitreerobotics/unitree_rl_mjlab)

What is it?: A repository based on MjLab (MuJoCo Training Laboratory). This is basically an alternative to Isaac Lab.

What is it for?: This is where we train neural networks. If we want the robot to learn how to walk on uneven terrain by itself, we use this.

Relationship with MuJoCo:

MuJoCo is the simulator.

MjLab is the programming library that runs on top of MuJoCo to facilitate robot training.

unitree_rl_mjlab is the specific configuration by Unitree to use those tools with the G1.

---

## 🦾 Manipulation (Imitation Learning)

This section is similar to locomotion, but we use other techniques so the robot can manipulate objects.

- [unitree_sim_isaaclab](https://github.com/unitreerobotics/unitree_sim_isaaclab.git)

The Simulation Environment for Manipulation

What is it?: It is the specific configuration of the G1 to work inside NVIDIA Isaac Lab, but focused on manipulation tasks.

Difference from unitree_rl_lab:

unitree_rl_lab is usually configured with Reinforcement Learning algorithms (PPO) for locomotion (learning to walk).

unitree_sim_isaaclab gives us the environment ready to import the G1 and test arm/hand tasks, or to validate policies we trained using Imitation Learning.

Use: It is our virtual "playroom". If we want to test whether the G1 can pick up a virtual apple without configuring the whole physics environment from scratch, we use this repo.

- [xr_teleoperate](https://github.com/unitreerobotics/xr_teleoperate.git)

Control with Virtual Reality (The Data Source)

What is it?: This is crucial. It allows controlling the G1 (especially arms and hands) using Virtual/Mixed Reality headsets (such as Apple Vision Pro or potentially Oculus/Meta Quest).

What is it for?:

Demonstration: Move the robot in real time so it copies you (the "Avatar" effect).

Data Collection: This is the key. If we want an AI to learn how to cook, first you put on the headset, "cook" by controlling the robot, and this repository records all those movements.

Relationship: The data recorded here is what we later feed into unitree_IL_lerobot.

- [unitree_IL_lerobot](https://github.com/unitreerobotics/unitree_IL_lerobot.git) (Reminder)

What is it?: Based on LeRobot (from Hugging Face).

What is it for?: Imitation Learning. It is mainly used if we have arms with dexterous hands. We teach the robot by moving its arms (teleoperation) and it learns to copy us.

Flow: xr_teleoperate (Record) -> unitree_IL_lerobot (Train) -> unitree_sdk2 (Execute on the real robot).

- [kinect_teleoperate](https://github.com/unitreerobotics/kinect_teleoperate.git)

The "Legacy" option for the H1

Warning: This repository was designed with the Unitree H1 (the large humanoid) in mind.

How does it work?: It uses a Kinect camera to track your human skeleton and "retarget" (translate) your joints to the robot’s joints.

Does it work for the G1?: Technically the retargeting logic works, but the configuration files (URDF, joint limits) are made for the H1. Unless we want to port the code and adjust the joint angle mathematics, it is not recommended to start here with a G1. It is better to use XR methods or the SDK joysticks.

---

## 🧠 Generalization (VLA and WMA)

This goes one step beyond RL and IL, a very difficult step to achieve.

- [unifolm-vla](https://github.com/unitreerobotics/unifolm-vla)

What is it?: A framework to develop Vision Language Action models. These models attempt to unify vision, natural language, and action within a single neural network. In other words, they associate pixels, human language, and action in the same place. For example, they associate a messy table and phrases like "tidy the table" with the required motor movements needed to organize it, frame by frame.

What is it for?: Generalization. If we want a single neural network to do everything (extremely difficult today). It is often complemented with World Models, which we will see next.

- [unifolm-world-model-action](https://github.com/unitreerobotics/unifolm-world-model-action)

What is it?: A framework to develop World Model Action systems. These models try to predict the future in order to choose the best action to successfully complete a task requested by a human. They predict the future by generating the next video frames using a diffusion policy.

What is it for?: Generalization. If we want a single neural network to do everything (extremely difficult today). It often complements a VLA so that it performs the action predicted by the WM (without the A of Action, since it would not act in that case).