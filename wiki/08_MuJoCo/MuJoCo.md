# 🧩 MuJoCo Installation and Configuration Guide

This document describes **how to install MuJoCo**, **the official Unitree configuration repository**, and **ensure there are no installation or runtime conflicts** on any machine.

Additionally, we explain how to install **MJLab**, the reinforcement learning training laboratory based on MuJoCo, which is an alternative to **Isaac Lab**.

---

## 🧠 Prerequisites

Make sure you are using **Ubuntu 20.04 or 22.04 (recommended)**.

Ensure your system is updated:

```bash
sudo apt update && sudo apt upgrade -y
```

Also make sure you have installed the **Unitree SDKs** according to the guides provided in the **G1 SDK Guide**:

```
/wiki/05_SDK_G1/
```

---

# ©️ C++ Installation

### Dependencies

```bash
sudo apt install libyaml-cpp-dev libspdlog-dev libboost-all-dev libglfw3-dev
```

Clone the **official Unitree repository**.  
We use **our own forks in the organization** to ensure stability.

We clone it into `~`, but you can move it elsewhere later if needed.

```bash
cd ~
git clone https://github.com/UAI-BIOARABA/unitree_mujoco.git
```

Download **MuJoCo**:

https://github.com/google-deepmind/mujoco/releases

Choose version:

```
mujoco-3.3.6-linux-x86_64.tar.gz
```

Extract it into your home folder inside a hidden directory called **.mujoco**.

Quick commands:

```bash
mkdir ~/.mujoco
tar -xvf mujoco-3.3.6-linux-x86_64.tar.gz -C ~/.mujoco

# At the end you should have a valid path like:
~/.mujoco/mujoco-3.3.6
```

Now enter the cloned repository:

```bash
cd unitree_mujoco/simulate
```

Create a **symbolic link** to your MuJoCo installation.

Make sure the version matches the one you downloaded.

```bash
ln -s ~/.mujoco/mujoco-3.3.6 mujoco
```

Compile the **C++ simulator**:

```bash
mkdir build
cd build
cmake ..
make -j4
```

If everything works correctly, you will get an executable called **unitree_mujoco** that can be launched with:

```bash
cd ~/unitree_mujoco/simulate/build
./unitree_mujoco -r g1 -s scene.xml
```

---

# 🐍 Python Installation

Activate the environment created when installing the SDK:

```bash
conda activate nombre-entorno
```

Now you only need to clone the repository.  
If you already cloned it when installing the C++ version, **do not clone it again**.

```bash
cd ~
git clone https://github.com/UAI-BIOARABA/unitree_mujoco.git
```

Run the Python simulator:

```bash
cd unitree_mujoco/simulate_python
python3 unitree_mujoco.py
```

---

# 🧪 MJLab Installation

Activate the environment created when installing the SDK:

```bash
conda activate nombre-entorno
```

Clone the repository:

```bash
git clone https://github.com/unitreerobotics/unitree_rl_mjlab.git
```

Install dependencies:

```bash
sudo apt install -y libyaml-cpp-dev libboost-all-dev libeigen3-dev libspdlog-dev libfmt-dev
```

Install **Mujoco-Warp**.

> Mujoco-Warp is a GPU-optimized extension of the MuJoCo physics simulator developed by Google DeepMind. It is designed to leverage NVIDIA GPUs for high-performance simulation and rendering.

```bash
pip install "mujoco-warp@git+https://github.com/google-deepmind/mujoco_warp@9491175b7cbea87e28d3e3e67733095317c33398"
```

Install the remaining dependencies:

```bash
cd unitree_rl_mjlab
pip install -e .
```