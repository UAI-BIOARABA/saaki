# 🧩 Installation and Configuration Guide for unitree_ros2

This document describes **how to install the Unitree ROS2 repository**, **configure all required dependencies**, and **ensure there are no installation or runtime conflicts** on any machine.

---

## 🧠 Prerequisites

This repository is only compatible with **Ubuntu 20.04 and 22.04 (recommended)**.

We assume the use of **Ubuntu 22.04**.

Make sure your system is updated:

```bash
sudo apt update && sudo apt upgrade -y
```

Before starting, make sure your `~/.bashrc` does **not automatically source ROS2 Humble**.

Check it with:

```bash
nano ~/.bashrc
```

Look for a line containing:

```
/opt/ros/humble/setup.bash
```

If it exists, **comment it out**.

---

# 💻 Installation

We use **our own forks in the organization** to ensure stability.

## 1. Clone the repository

Clone it into your home directory:

```bash
cd ~
git clone https://github.com/UAI-BIOARABA/unitree_ros2.git
```

---

## 2. Install dependencies

```bash
sudo apt install ros-humble-rmw-cyclonedds-cpp
sudo apt install ros-humble-rosidl-generator-dds-idl
sudo apt install libyaml-cpp-dev
```

---

## 3. Build the workspace

If you are using **ROS2 Humble on Ubuntu 22.04**, it is **not necessary to manually compile Cyclone DDS**, since the version installed in the previous step is compatible with the robot.

Build the packages directly:

```bash
source /opt/ros/humble/setup.bash
cd ~/unitree_ros2/cyclonedds_ws
colcon build
```

At this point you can **uncomment the ROS setup line in your `.bashrc`** or add it if it was not there.

---

## 4. Configure setup scripts

Edit the following files:

- `setup.sh`
- `setup_default.sh`
- `default_local.sh`

Replace **`foxy` → `humble`**.

Additionally, in `setup.sh` change the **network interface** to match your machine.

Example:

```bash
#!/bin/bash
echo "Setup unitree ros2 environment"
source /opt/ros/humble/setup.bash
source $HOME/unitree_ros2/cyclonedds_ws/install/setup.bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
export CYCLONEDDS_URI='<CycloneDDS><Domain><General><Interfaces>
                            <NetworkInterface name="enp44s0" priority="default" multicast="default" />
                        </Interfaces></General></Domain></CycloneDDS>'
```

Replace `enp44s0` with **your network interface name**.

---

# ✅ Verification

## 1. Compile the examples

```bash
cd ~/unitree_ros2/example
colcon build
```

---

## 2. Source the environment

Use the **normal setup** or the **local setup** if running in simulation:

```bash
source ~/unitree_ros2/setup_local.sh
```

---

## 3. Verify ROS2 topics

Make sure you are **connected to the robot** or **running the simulator**.

Then check the available ROS2 topics:

```bash
ros2 topic list
```

---

## 4. Run an example

```bash
cd example
./install/unitree_ros2_example/bin/g1_ankle_swing_example
```

---

# ♻️ Development Workflow

Create your own **ROS2 workspace** in your home directory if you do not have one yet (standard ROS2 convention):

```bash
cd
mkdir -p ros2_ws/src
cd ~/ros2_ws/src
```

---

## Prepare the environment

You must run this in **every new terminal** before working:

```bash
source ~/unitree_ros2/setup.sh
```

---

## Create a new package

Example for **C++**:

```bash
ros2 pkg create --build-type ament_cmake mi_paquete --dependencies rclcpp unitree_go unitree_api
```

---

## Configure package files

Edit:

- `CMakeLists.txt`
- `package.xml`

Make sure:

### In `package.xml`

```
<depend>unitree_go</depend>
```

exists.

### In `CMakeLists.txt`

```
find_package(unitree_go REQUIRED)
```

exists.

---

## Version control

Create a **separate GitHub repository for each package** so it can be shared within the organization.