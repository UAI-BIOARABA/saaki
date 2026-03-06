# 🧩 Installation and Configuration Guide for the LiDAR SDK

This document describes **how to install the official SDK**, **configure all the required dependencies**, and **ensure there are no installation or runtime conflicts** on any machine.

The **Unitree G1** includes a **Livox Mid-360 LiDAR**, which has its own SDK and visualization software.

- [Official website](https://www.livoxtech.com/mid-360)

There are **three ways to visualize LiDAR data**:

- Using the **Viewer application**
- Using the **SDK**
- Using **ROS2**

---

## 🧠 Prerequisites

Make sure you have a **PC with Ubuntu** and the **Unitree G1 nearby**.

We assume the use of **Ubuntu 22.04 on the PC and ROS2 Humble**.

Ensure both systems are updated:

```bash
sudo apt update && sudo apt upgrade -y
```

Make sure you know how to connect to the robot as we have done several times before. The process is the same, but the **LiDAR IP address is `192.168.123.120`**. You can ping it to verify the connection.

---

## 💻 Installing LivoxViewer2

1. Go to the official website and open the downloads section:  
   https://www.livoxtech.com/mid-360/downloads  
   Download **"Livox Viewer 2 - Ubuntu"**.

2. Extract the downloaded file (you may rename it to a shorter name).

3. Enter the folder and run the script:

```bash
cd LivoxViewer2 # Or whatever name you gave the folder
source LivoxViewer2.sh
```

4. If the network is correctly configured, the software will automatically detect the **Mid-360 LiDAR**.

5. Turn on the **"Connect"** switch and press **"Play"** (sometimes it starts automatically).  
   You should now see a **3D reconstruction of your environment**.

---

## 💻 SDK Installation

You can find the SDKs and repositories in the **official GitHub organization**:

https://github.com/Livox-SDK

### 1. Install CMake if needed

```bash
sudo apt install cmake
```

### 2. Compile and install Livox-SDK2

```bash
mkdir -p ~/ws_livox/src
cd ws_livox
git clone https://github.com/Livox-SDK/Livox-SDK2.git
cd Livox-SDK2
mkdir build
cd build
cmake .. && make -j
sudo make install
```

### 3. Note

The generated **shared and static libraries** will be installed in:

```
/usr/local/lib
```

Header files will be installed in:

```
/usr/local/include
```

If you ever need to **remove Livox SDK2**, run:

```bash
sudo rm -rf /usr/local/lib/liblivox_lidar_sdk_*
sudo rm -rf /usr/local/include/livox_lidar_*
```

### 4. Configure the example

Go to:

```
samples/livox_lidar_quick_start
```

Edit the file:

```
mid360_config.json
```

Set:

- **Host IP = your PC**
- **LiDAR IP = 192.168.123.120**

### 5. Run an example

You can now run the provided examples or start developing your own programs.

---

## 💻 ROS2 SDK Installation

Make sure **ROS2 Humble** is correctly installed.

### 1. Create the workspace

```bash
mkdir -p ~/ws_livox/src
```

### 2. Install Livox-SDK2

Follow the steps from the previous section.

### 3. Clone the Livox ROS Driver 2

```bash
cd ~/ws_livox/src
git clone https://github.com/Livox-SDK/livox_ros_driver2.git
```

### 4. Compile the driver for ROS2 Humble

```bash
cd ~/ws_livox/src/livox_ros_driver2
source /opt/ros/humble/setup.sh
./build.sh humble
```

---

## 🧩 Modify the configuration file

Open:

```bash
gedit ~/ws_livox/src/livox_ros_driver2/config/MID360_config.json
```

Modify the following fields:

```json
{
  "lidar_summary_info" : {
    "lidar_type": 8
  },
  "MID360": {
    "lidar_net_info" : {
      "cmd_data_port": 56100,
      "push_msg_port": 56200,
      "point_data_port": 56300,
      "imu_data_port": 56400,
      "log_data_port": 56500
    },
    "host_net_info" : {
      "cmd_data_ip" : "192.168.123.x",
      "cmd_data_port": 56101,
      "push_msg_ip": "192.168.123.x",
      "push_msg_port": 56201,
      "point_data_ip": "192.168.123.x",
      "point_data_port": 56301,
      "imu_data_ip" : "192.168.123.x",
      "imu_data_port": 56401,
      "log_data_ip" : "",
      "log_data_port": 56501
    }
  },
  "lidar_configs" : [
    {
      "ip" : "192.168.123.120",
      "pcl_data_type" : 1,
      "pattern_mode" : 0,
      "extrinsic_parameter" : {
        "roll": 0.0,
        "pitch": 0.0,
        "yaw": 0.0,
        "x": 0,
        "y": 0,
        "z": 0
      }
    }
  ]
}
```

Replace **192.168.123.x** with **the IP address of your computer**.

The LiDAR IP **must remain**:

```
192.168.123.120
```

---

## 🔄 Recompile for safety

```bash
cd ~/ws_livox
rm -rf build/ install/ log/
colcon build --symlink-install --cmake-args -DROS_EDITION=ROS2 -DHUMBLE_ROS=humble
source install/setup.bash
```

---

## ▶️ Launch the driver

```bash
ros2 launch livox_ros_driver2 rviz_MID360_launch.py
```

This will launch **RViz** and display the **real-time point cloud from the Mid-360 LiDAR**.