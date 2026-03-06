# 🧩 Installation and Configuration Guide for the Official Unitree G1 SDKs

This document describes **how to install the official SDKs**, **configure all required dependencies**, and **ensure there are no installation or runtime conflicts** on any machine.

Unitree provides **two SDKs** to work with:

- **C++ SDK**
- **Python SDK**

In practice, the Python SDK is just a **wrapper around the C++ SDK**, which is faster but also more complex and verbose. In general, it is recommended to install **both**, since other repositories may depend on the original C++ SDK, while Python scripts require the Python one.

---

## 🧠 Prerequisites

The SDKs are only compatible with **Ubuntu 20.04 and 22.04** (recommended).

Make sure your system is updated:

```bash
sudo apt update && sudo apt upgrade -y
```

Before starting, make sure the following dependencies are installed:

- **CMake** (version 3.10 or higher)
- **GCC** (version 9.4.0)
- **Make**

Install dependencies:

```bash
apt-get update
apt-get install -y cmake g++ build-essential libyaml-cpp-dev libeigen3-dev libboost-all-dev libspdlog-dev libfmt-dev
```

---

## 💻 C++ SDK Installation

We use **our own forks in the organization** to ensure stability.

We clone it into `~`, but it can be moved elsewhere later if desired.

```bash
cd ~
git clone https://github.com/UAI-BIOARABA/unitree_sdk2.git
cd unitree_sdk2

# Create build directory
mkdir build
cd build

# Compile configuring the recommended installation path
cmake .. -DCMAKE_INSTALL_PREFIX=/opt/unitree_robotics

# Install (requires sudo)
sudo make install
```

To build the examples included in the repository:

```bash
cd build
cmake ..
make
```

---

## 💻 Python SDK Installation

We also use **our own forks** in the organization to ensure stability.

We clone it into `~`, but it can be moved later if desired.

```bash
cd ~
git clone https://github.com/UAI-BIOARABA/unitree_sdk2_python.git
```

Create a **Conda virtual environment** and install it:

```bash
# Create environment
conda create --name nombre-entorno python=3.10.12

# Activate in every terminal
conda activate nombre-entorno

# Install SDK dependencies in editable mode
cd unitree_sdk2_python

# Install required build dependencies (just in case)
pip install setuptools wheel
pip install -e .

# Install additional dependencies
pip install matplotlib pydot    # Installing them now prevents potential errors
```

You can now test the examples included in the repository by running them in a terminal, either on the **real robot** or in the **MuJoCo simulator**.