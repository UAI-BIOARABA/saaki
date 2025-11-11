# G1 Interface

The provided tutorial will assist you with setting up and operating your G1 bi-pedal robot.
The tutorial topics are listed in the left column and presented in the suggested reading order.

## G1 Quick Start

1. **Power on the robot**.
2. **Connect LAN cable** to the Ethernet port (the top-most available port) located on the back side of the neck.

## Static Network Connection

For first-time setup, you need to connect via LAN cable to configure the robot's network.

**On your PC (Ubuntu):**

1. Navigate to **Settings → Network**.
2. Click on the "+" icon to create a new connection.
3. In **IPv4 Settings**, set the connection method to **Manual**.
4. Enter the following details:
   - **Address IP**: `192.168.123.51`
   - **Netmask**: `24`
5. Save and restart your network.

After a successful connection, check the host PC's local IP:

```bash
ifconfig
```

Ping the robot to verify connectivity:

```bash
ping 192.168.123.164
```

Access the robot via SSH:

```bash
ssh -X unitree@192.168.123.164
```

Default password:

```bash
123
```

## Network Table

| IP Address           | Device            | Username | Password  |
| -------------------- | ----------------- | -------- | --------- |
| 192.168.123.161      | G1 MCU            | x        | x         |
| 192.168.123.164      | G1 Auxiliary PC 2 | unitree  | 123       |
| 192.168.123.164:9000 | G1 Webserver MBS  | x        | mybotshop |
| 192.168.123.120      | Mid360 Lidar      | x        | x         |

> **Note**
> Do **not** set your PC IP as any of the G1 IP addresses listed below.

## Enable Internet LAN

This enables LAN via the ethernet cable. Ensure the cable is connected to a router with internet access.

```bash
sudo ip link set eth1 down && sudo ip link set eth1 up
sudo dhclient eth1
sudo date -s "$(wget --method=HEAD -qSO- --max-redirect=0 google.com 2>&1 | sed -n 's/^ *Date: *//p')"
ping google.com
```

## Enable Internet WiFi (Recommended)

You can attach a hub and WiFi via a hub. A link to a cost-effective hub and WiFi stick is provided in Enhancements.

```bash
sudo ip link set eth0 down && sudo ip link set eth0 up
sudo dhclient eth0
```

## Teleop

This requires installation of the ROS2 modules on G1. If not installed, please follow the **G1 Auxiliary PC Installation** guide.

**Run Teleop for G1:**

```bash
ROS_DOMAIN_ID=10 ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args --remap cmd_vel:=/g1_unit_001/cmd_vel
```

## Visualization

**Launch G1 Viz:**

```bash
ros2 launch g1_viz view_robot.launch.py
```

# G1 ROS2 Modules

> **Note**
> Please ensure that the arms are straight down when you power on the robot.
> If the orientation is different, it will affect the ROS2 control operation.

The ROS2 modules start drivers for operating G1 in **high-level mode** for legs and arms.

> **Important**
> During initialization, the arms will move such that the **forearms will face forward**.

## Launch G1 ROS2 Driver

The driver includes the following modules:

- Domain Bridge
- Joint States
- Robot Description
- Arm Control
- Leg Control (High-level)
- Inertial Measurement Unit Publisher
- Sensor Fusion
- Twist Mux
- Logitech F710 Control
- D435i Depth Camera Driver
- Livox Mid360 Lidar Driver

These modules can also be viewed, activated, or deactivated from the **Webserver**.

```bash
ros2 launch g1_platform highlevel_ros.launch.py
```

## Launch G1 Rviz

```bash
ros2 launch g1_viz view_robot.launch.py
```

## Tele-Operation

To teleoperate the G1 robot:

```bash
ROS_DOMAIN_ID=10 ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args --remap cmd_vel:=/g1_unit_001/hardware/cmd_vel
```

## Core Launch Files

These launch files can also be managed from the webserver. Ensure the **Webserver** is running. Follow the installation procedure first.

```bash
ros2 launch g1_platform highlevel_ros.launch.py
ros2 launch g1_platform domain_bridge.launch.py
ros2 launch g1_platform state_publisher.launch.py
ros2 launch g1_webserver webserver.launch.py
ros2 launch g1_control twistmux.launch.py
ros2 launch g1_lidar livox_mid360.launch.py
ros2 launch g1_platform audio.launch.py
ros2 launch g1_platform led.launch.py
ros2 launch g1_platform videostream.launch.py
```

## Arms 7Dof Core Launch Files

```bash
ros2 launch g1_description g1_29_description.launch.py
ros2 launch g1_platform arm_7dof.launch.py
```

## Arms 5Dof Core Launch Files

```bash
ros2 launch g1_description g1_23_description.launch.py
ros2 launch g1_platform arm_5dof.launch.py
```

## SDK Examples

Build and install the SDK:

```bash
cd /opt/mybotshop/04Aug2025_unitree_sdk2/build/
cmake .. && make && sudo make install
```

Run examples:

```bash
cd /opt/mybotshop/04Aug2025_unitree_sdk2/build/bin
./g1_loco_client --network_interface=eth0 --shake_hand
./g1_audio_client_example eth0
```

# G1 Modes Selection

## Posture & State Commands

The table below lists commands for controlling the robot's posture and state:

| Command             | Description                                       |
| ------------------- | ------------------------------------------------- |
| damp                | Set all motors to damping mode.                   |
| start               | Start locomotion control.                         |
| squat               | Transition to squat posture.                      |
| sit                 | Sit down.                                         |
| stand_up            | Stand up from sitting/squatting.                  |
| zero_torque         | Disable torque on all motors.                     |
| stop_move           | Stop all motion immediately.                      |
| high_stand          | Stand at high position.                           |
| low_stand           | Stand at low position.                            |
| balance_stand       | Stand with balance mode active.                   |
| shake_hand          | Perform handshake sequence (auto-start and stop). |
| wave_hand           | Perform wave motion.                              |
| wave_hand_with_turn | Wave hand and turn simultaneously.                |

## Setter Commands (Change Robot State)

| Command                           | Parameters                                       |
| --------------------------------- | ------------------------------------------------ |
| set_fsm_id=&lt;id&gt;             | Integer FSM ID to switch to.                     |
| set_balance_mode=&lt;0/1&gt;      | Enable (1) or disable (0) balance mode.          |
| set_swing_height=&lt;value&gt;    | Set swing height in meters.                      |
| set_stand_height=&lt;value&gt;    | Set stand height in meters.                      |
| set_velocity="vx vy ω [duration]" | Set velocity (m/s, m/s, rad/s, [duration in s]). |
| move="vx vy ω"                    | Command motion without duration.                 |
| set_task_id=&lt;id&gt;            | Set active task ID.                              |
| set_speed_mode=&lt;mode&gt;       | Change locomotion speed mode.                    |

## Getter Commands (Query Current Robot State)

| Command          | Returns                          |
| ---------------- | -------------------------------- |
| get_fsm_id       | Current finite state machine ID. |
| get_fsm_mode     | Current FSM mode.                |
| get_balance_mode | Current balance mode (0/1).      |
| get_swing_height | Current swing foot height.       |
| get_stand_height | Current standing height.         |
| get_phase        | Current gait phase vector.       |

## Toggle Commands

| Command                             | Parameters | Description                                     |
| ----------------------------------- | ---------- | ----------------------------------------------- |
| continous_gait=&lt;true/false&gt;   | bool       | Enable or disable continuous gait mode.         |
| switch_move_mode=&lt;true/false&gt; | bool       | Switch between movement mode and standing mode. |

# G1 Examples

## Start the robot

```bash
ROS_DOMAIN_ID=10 ros2 service call /g1_unit_001/hardware_modes g1_interface/srv/G1Modes \
'{"request_data": "start"}'
```

## Shake hand

```bash
ROS_DOMAIN_ID=10 ros2 service call /g1_unit_001/hardware_modes g1_interface/srv/G1Modes \
'{"request_data": "shake_hand"}'
```

## Wave hand

```bash
ROS_DOMAIN_ID=10 ros2 service call /g1_unit_001/hardware_modes g1_interface/srv/G1Modes \
'{"request_data": "wave_hand_with_turn"}'
```

## Set swing height

```bash
ROS_DOMAIN_ID=10 ros2 service call /g1_unit_001/hardware_modes g1_interface/srv/G1Modes \
'{"request_data": "set_swing_height=0.12"}'
```

## Move forward

```bash
ROS_DOMAIN_ID=10 ros2 service call /g1_unit_001/hardware_modes g1_interface/srv/G1Modes \
'{"request_data": "set_velocity=0.3 0.0 0.0 2.0"}'
```

## Query current FSM mode

```bash
ROS_DOMAIN_ID=10 ros2 service call /g1_unit_001/hardware_modes g1_interface/srv/G1Modes \
'{"request_data": "get_fsm_mode"}'
```

## Enable continuous gait

```bash
ROS_DOMAIN_ID=10 ros2 service call /g1_unit_001/hardware_modes g1_interface/srv/G1Modes \
'{"request_data": "continous_gait=true"}'
```

# G1 LED Selection

## Set color

```bash
ros2 service call /g1_unit_001/hardware/led g1_interface/srv/G1Modes "{request_data: 'color=green'}"
ros2 service call /g1_unit_001/hardware/led g1_interface/srv/G1Modes "{request_data: 'color=red'}"
ros2 service call /g1_unit_001/hardware/led g1_interface/srv/G1Modes "{request_data: 'color=white'}"
ros2 service call /g1_unit_001/hardware/led g1_interface/srv/G1Modes "{request_data: 'color=blue'}"
```

## Set color with hex and brightness

```bash
ros2 service call /g1_unit_001/hardware/led g1_interface/srv/G1Modes "{request_data: 'color=#00a0a0;brightness=60'}"
```

## Set color with RGB

```bash
ros2 service call /g1_unit_001/hardware/led g1_interface/srv/G1Modes "{request_data: 'color=rgb(128,32,200)'}"
```

## Query LED state

```bash
ros2 service call /g1_unit_001/hardware/led g1_interface/srv/G1Modes "{request_data: 'get_brightness'}"
ros2 service call /g1_unit_001/hardware/led g1_interface/srv/G1Modes "{request_data: 'get_color'}"
```

# G1 Audio Selection

## Set volume

```bash
ROS_DOMAIN_ID=10 ros2 service call /g1_unit_001/hardware/audio g1_interface/srv/G1Modes "{request_data: 'volume=80'}"
```

## Speak English

```bash
ROS_DOMAIN_ID=10 ros2 service call /g1_unit_001/hardware/audio g1_interface/srv/G1Modes "{request_data: 'speak=Hello from MY BOT SHOP'}"
```

## Volume + Text

```bash
ROS_DOMAIN_ID=10 ros2 service call /g1_unit_001/hardware/audio g1_interface/srv/G1Modes "{request_data: 'volume=100;speak= Hello from my bot shop. My name is Danny'}"
```

## Get current volume

```bash
ROS_DOMAIN_ID=10 ros2 service call /g1_unit_001/hardware/audio g1_interface/srv/G1Modes "{request_data: 'get_volume'}"
```

## Self Intro

```bash
ROS_DOMAIN_ID=10 ros2 service call /g1_unit_001/hardware/audio g1_interface/srv/G1Modes "{request_data: 'volume=100;speak= Hello from my bot shop. I am Danny, a compact humanoid robot for research and education. My height is 1.32 meters, and my weight is 35 kg. I walk up to 2 meters per second, climb stairs, and keep balance. Hehehe climbing stairs not really. My base model has 23 degrees of freedom where as my educational + version has 43 degrees of freedom. I have 3D liDAR specifically Livox Mid360 and depth camera Intel RealSense D435i. Battery lasts about 2 hours +'}"
```

## Note

More info on these commands is available in the Unitree documentation.

# G1 Arm ROS2 Control

## Start the arms (Mandatory)

```bash
ROS_DOMAIN_ID=10 ros2 service call /g1_unit_001/hardware/control/arm_dof7/start std_srvs/srv/Trigger {}
```

## Stop the arms (Mandatory)

```bash
ROS_DOMAIN_ID=10 ros2 service call /g1_unit_001/hardware/control/arm_dof7/stop std_srvs/srv/Trigger {}
```

## Move dual arms

Will wave on spot — clear the area around the G1.

```bash
ROS_DOMAIN_ID=10 ros2 action send_goal /g1_unit_001/dual_arm/follow_joint_trajectory control_msgs/action/FollowJointTrajectory "{
  trajectory: {
    joint_names: [
      \"left_shoulder_pitch\",\"left_shoulder_roll\",\"left_shoulder_yaw\",
      \"left_elbow\",\"left_wrist_roll\",\"left_wrist_pitch\",\"left_wrist_yaw\",
      \"right_shoulder_pitch\",\"right_shoulder_roll\",\"right_shoulder_yaw\",
      \"right_elbow\",\"right_wrist_roll\",\"right_wrist_pitch\",\"right_wrist_yaw\"
    ],
    points: [
      { positions: [0.0, 0.2, 0.0, 1.57, 0.0, 0.0, 0.0,   0.0, -0.2, 0.0, 1.57, 0.0, 0.0, 0.0],
        time_from_start: {sec: 2, nanosec: 0} },
      { positions: [0.0, 1.57, 1.57, -1.57, 0.0, 0.0, 0.0,   0.0, -1.57, -1.57, -1.57, 0.0, 0.0, 0.0],
        time_from_start: {sec: 4} },
      { positions: [0.0, 1.57, 1.57, -1.0, 0.0, 0.0, 0.0,   0.0, -1.57, -1.57, -1.0, 0.0, 0.0, 0.0],
        time_from_start: {sec: 6} },
      { positions: [0.0, 1.57, 1.57, -1.57, 0.0, 0.0, 0.0,   0.0, -1.57, -1.57, -1.57, 0.0, 0.0, 0.0],
        time_from_start: {sec: 8} },
      { positions: [0.0, 1.57, 1.57, -1.0, 0.0, 0.0, 0.0,   0.0, -1.57, -1.57, -1.0, 0.0, 0.0, 0.0],
        time_from_start: {sec: 10} },
      { positions: [0.0, 1.57, 1.57, -1.57, 0.0, 0.0, 0.0,   0.0, -1.57, -1.57, -1.57, 0.0, 0.0, 0.0],
        time_from_start: {sec: 12} },
      { positions: [0.0, 1.57, 1.57, -1.0, 0.0, 0.0, 0.0,   0.0, -1.57, -1.57, -1.0, 0.0, 0.0, 0.0],
        time_from_start: {sec: 14} },
      { positions: [0.0, 0.2, 0.0, 1.57, 0.0, 0.0, 0.0,   0.0, -0.2, 0.0, 1.57, 0.0, 0.0, 0.0],
        time_from_start: {sec: 16, nanosec: 0} }
    ]
  }
}"
```

## Move left arm

```bash
ROS_DOMAIN_ID=10 ros2 action send_goal /g1_unit_001/left_arm/follow_joint_trajectory \
  control_msgs/action/FollowJointTrajectory \
  "{trajectory: {
      joint_names: [
        left_shoulder_pitch, left_shoulder_roll, left_shoulder_yaw,
        left_elbow, left_wrist_roll, left_wrist_pitch, left_wrist_yaw
      ],
      points: [
        {positions: [0.0, 0.2, 0.0, 1.57, 0.0, 0.0, 0.0],
         time_from_start: {sec: 2, nanosec: 0} }
      ]}}"
```

## Move right arm

```bash
ROS_DOMAIN_ID=10 ros2 action send_goal /g1_unit_001/right_arm/follow_joint_trajectory \
  control_msgs/action/FollowJointTrajectory \
  "{trajectory: {
      joint_names: [
        right_shoulder_pitch, right_shoulder_roll, right_shoulder_yaw,
        right_elbow, right_wrist_roll, right_wrist_pitch, right_wrist_yaw
      ],
      points: [
        {positions: [0.0, -0.2, 0.0, 1.57, 0.0, 0.0, 0.0],
         time_from_start: {sec: 2, nanosec: 0}}
      ]}}"
```

# G1 Depth Camera Realsense D435i

Test the native driver via:

```bash
ros2 launch realsense2_camera rs_launch.py depth_module.depth_profile:=1280x720x30 pointcloud.enable:=true
```

# G1 Simultaneous Localization and Mapping (SLAM)

Ensure `g1_bringup` is running, then launch:

```bash
ros2 launch g1_navigation slam.launch.py
```

Begin mapping using teleop at 2 m/s. Once mapping is complete, save the map:

```bash
ros2 run nav2_map_server map_saver_cli -f map_
```

Rebuild workspace:

```bash
colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release
```

# G1 Odometric Navigation

```bash
ros2 launch g1_navigation odom_navi.launch.py
```

# G1 Map Navigation

Ensure a map is generated and built in the ROS package, then launch:

```bash
ros2 launch g1_navigation map_navi.launch.py
```

# G1 Debugging

**TF**

```bash
ROS_DOMAIN_ID=10 ros2 run rqt_tf_tree rqt_tf_tree --force-discover --ros-args --remap tf:=/g1_unit_001/tf --ros-args --remap tf_static:=/g1_unit_001/tf_static
```

```bash
ROS_DOMAIN_ID=10 ros2 run tf2_tools view_frames.py --force-discover --ros-args --remap tf:=/g1_unit_001/tf --ros-args --remap tf_static:=/g1_unit_001/tf_static
```

**Video Stream**

- **Faster version**

```bash
gst-launch-1.0 udpsrc address=230.1.1.1 port=1720 multicast-iface=eth0 ! \
    application/x-rtp, media=video, encoding-name=H264 ! \
    rtph264depay ! queue max-size-buffers=1 ! h264parse ! queue ! avdec_h264 ! \
    videoconvert ! autovideosink
```

# G1 Enhancements

**WiFi Stick**

- Recommended: TL-WN722N with a long-range antenna (budget-friendly) or any high-end WiFi stick with long-range for remote operation.

**Connection Hub**

- Recommended: USB-C hub such as Ugreen USB-C Hub with HDMI port for testing, debugging, and increasing the number of available ports.

---

[← G1 Operation (Firmware V1.0.4)](operation-1.4.md)

[Volver al Índice Principal](00_Readme.md)
