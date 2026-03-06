# 🧩 Installation and Configuration Guide for the Camera SDK

This document describes **how to install the official SDK**, **configure all required dependencies**, and **ensure that there are no installation or runtime conflicts** on any machine.

The **Unitree G1** includes an **Intel RealSense D435i camera**, which has its own SDK.

- [SDK librealsense](https://github.com/realsenseai/librealsense.git)

The SDK has its own installation guide for different platforms (**Windows, Ubuntu, Jetson, etc.**), but it can be somewhat confusing, so here we complement it.

For installation on our **PC**, we follow the **Ubuntu guide**:

- [Official](https://github.com/realsenseai/librealsense/blob/master/doc/installation.md)

To install it **on the robot**, we follow the **Jetson guide**:

- [Official](https://github.com/realsenseai/librealsense/blob/master/doc/distribution_linux.md#installing-the-packages)

---

## 🧠 Prerequisites

Make sure you have a **PC with Ubuntu** and the **Unitree G1 nearby**.

We assume the use of **Ubuntu 20.04 or 22.04 (recommended)** on the PC.

Ensure both systems are updated:

```bash
sudo apt update && sudo apt upgrade -y
```

---

# 💻 Installation on our PC

This allows us to use the SDK on our PC and process camera data, which means connecting the camera **directly to the PC instead of the robot**.

This is not ideal but can be useful for **debugging and testing**, especially if you have an **extra camera that is not mounted on the robot**.

---

## Installing dependencies

Install the required packages to build **librealsense binaries** and the affected kernel modules:

```bash
sudo apt-get install libssl-dev libusb-1.0-0-dev libudev-dev pkg-config libgtk-3-dev
```

Install the build tools:

```bash
sudo apt-get install git wget cmake build-essential
```

Prepare the Linux backend and development environment.  
Disconnect any RealSense cameras and run:

```bash
sudo apt-get install libglfw3-dev libgl1-mesa-dev libglu1-mesa-dev at
```

---

# Installing librealsense

Clone the repository:

```bash
git clone https://github.com/realsenseai/librealsense.git
```

Run the RealSense permissions script from the librealsense root directory:

```bash
cd librealsense
./scripts/setup_udev_rules.sh
```

Build and apply the kernel modules.

Ubuntu 20/22/24 (focal/jammy/noble) with LTS kernels **5.15, 5.19, 6.5, 6.8, 6.11, 6.14**

```bash
./scripts/patch-realsense-ubuntu-lts-hwe.sh
```

Ubuntu 20 with LTS kernel **(< 5.13)**

```bash
./scripts/patch-realsense-ubuntu-lts.sh
```

---

# Building the librealsense2 SDK

Run:

```bash
cd librealsense
mkdir build && cd build
```

Run the **cmake configuration step**. Some examples:

The default build generates shared object binaries and unit tests in debug mode.  
Use **-DCMAKE_BUILD_TYPE=Release** to compile with optimizations.

Normal build:

```bash
cmake ../
```

With demos and tutorials:

```bash
cmake ../ -DBUILD_EXAMPLES=true
```

For systems without **OpenGL or X11**, build only textual examples:

```bash
cmake ../ -DBUILD_EXAMPLES=true -DBUILD_GRAPHICAL_EXAMPLES=false
```

Recompile and install the librealsense2 binaries:

```bash
sudo make uninstall && make clean && make && sudo make install
```

You can now launch:

```bash
realsense-viewer
```

from the terminal.

---

# 🤖 Installation on the G1

This allows **processing camera data directly on the robot**.

After that, we can **send the data to our PC or process it on the robot itself**.

---

## Installing the packages

Register the public key for the packages:

```bash
# Ensure the directory exists
sudo mkdir -p /etc/apt/keyrings

# Download and dearmor
curl -sSf https://librealsense.realsenseai.com/Debian/librealsenseai.asc | \
gpg --dearmor | sudo tee /etc/apt/keyrings/librealsenseai.gpg > /dev/null
```

Note: The key bundle contains both the **new RS public key** and the **Intel public key for older repositories**, ensuring compatibility with new and existing packages.

Ensure **APT HTTPS support** is installed:

```bash
sudo apt-get install apt-transport-https
```

Add the server to the repository list:

```bash
echo "deb [signed-by=/etc/apt/keyrings/librealsenseai.gpg] https://librealsense.realsenseai.com/Debian/apt-repo `lsb_release -cs` main" | \
sudo tee /etc/apt/sources.list.d/librealsense.list
sudo apt-get update
```

Install the libraries:

```bash
sudo apt-get install librealsense2-dkms
sudo apt-get install librealsense2-utils
sudo apt-get install librealsense2-dev
sudo apt-get install librealsense2-dbg
```

You can now launch:

```bash
realsense-viewer
```

However, if the **G1 does not have a monitor connected** (which is usually the case), you will not see anything.

---

# 🐍 Python packages

To process camera data using the **Python SDK**, install:

```bash
pip install pyrealsense2
pip install pyrealsense2-beta
```

If you create a script on the **G1** containing the following code, you can stream the camera feed directly to your **web browser**:

```python
# RUN ON THE ROBOT (Unitree G1)

from flask import Flask, Response
import pyrealsense2 as rs
import cv2
import numpy as np

app = Flask(__name__)

# Configure the RealSense camera
pipeline = rs.pipeline()
config = rs.config()

# Reduce resolution for smoother Wi-Fi streaming
config.enable_stream(rs.stream.color, 640, 480, rs.format.bgr8, 30)

print("Starting camera...")
pipeline.start(config)

def generar_frames():
    """Continuously capture frames"""
    try:
        while True:
            frames = pipeline.wait_for_frames()
            color_frame = frames.get_color_frame()
            if not color_frame:
                continue

            frame = np.asanyarray(color_frame.get_data())

            ret, buffer = cv2.imencode('.jpg', frame, [cv2.IMWRITE_JPEG_QUALITY, 80])
            frame_bytes = buffer.tobytes()

            yield (b'--frame\r\n'
                   b'Content-Type: image/jpeg\r\n\r\n' + frame_bytes + b'\r\n')
    except Exception as e:
        print(f"Error: {e}")
    finally:
        pass

@app.route('/video_feed')
def video_feed():
    return Response(generar_frames(),
                    mimetype='multipart/x-mixed-replace; boundary=frame')

if __name__ == '__main__':
    try:
        app.run(host='0.0.0.0', port=5000, threaded=True)
    finally:
        pipeline.stop()
```

Now from your **PC**, you can view the stream directly in your browser.

---

# 🦾 ROS2 Wrapper Installation

Once **ROS2 Humble** and the **camera SDK** are correctly installed, you can install the **ROS2 wrapper**.

Enter your workspace and clone the repository:

```bash
cd ~/ros2_ws/src/
git clone https://github.com/realsenseai/realsense-ros.git -b ros2-master
```

Install dependencies:

```bash
cd ~/ros2_ws
sudo apt-get install python3-rosdep -y
sudo rosdep init
rosdep update
rosdep install -i --from-path src --rosdistro $ROS_DISTRO --skip-keys=librealsense2 -y
colcon build
```

Source the workspace:

```bash
ROS_DISTRO=HUMBLE
source /opt/ros/$ROS_DISTRO/setup.bash
cd ~/ros2_ws
. install/local_setup.bash
```

For more information about the wrapper, visit the repository:

- [realsense-ros](https://github.com/realsenseai/realsense-ros.git)