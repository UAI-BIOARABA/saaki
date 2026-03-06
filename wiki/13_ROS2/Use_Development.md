# 🧩 Using and Developing in ROS2

To develop our own **ROS2 programs**, it is recommended to work inside our own workspace (`ros2_ws`).

This provides several advantages:

- We can **share our packages on GitHub**
- We avoid problems caused by **Unitree updates**
- We maintain a **more scalable project structure**
- And other workflow improvements

To start development, we adapted the **official examples provided by Unitree** in:

https://github.com/unitreerobotics/unitree_ros2

Our version allows them to be executed using **standard ROS2 commands** and removes redundant code related to other robots.

---

# 🧠 Prerequisites

You must have followed the **ROS2 installation guide**:

```
Instalación.md
```

For a more detailed guide and the source code, visit:

https://github.com/UAI-BIOARABA/saaki-ros2-examples

---

# 💻 Installation Summary

Enter your ROS2 workspace and clone the repository:

```bash
cd ~/ros2_ws/src
git clone https://github.com/UAI-BIOARABA/saaki-ros2-examples.git saaki_ros2_examples
```

Go to the root of the workspace and load the environments:

```bash
cd ~/ros2_ws

# IMPORTANT: Load the Unitree environment BEFORE compiling
source ~/unitree_ros2/setup.sh

# Build the package
colcon build --symlink-install
```

---

# 🏃‍♂️ Running Nodes

You can now run nodes easily.

Example:

```bash
ros2 run saaki_ros2_examples read_low_state_hg
```