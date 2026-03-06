# 🧩 Isaac Sim Installation and Configuration Guide (Ubuntu)

This document describes **how to install Isaac Sim**, **configure all required dependencies**, and **ensure that the development environment is fully reproducible** on another machine.

---

## 🧠 Prerequisites

1. Make sure your system is updated:

```bash
sudo apt update && sudo apt upgrade -y
```

2. **NVIDIA GPU with working drivers.**  
If `nvidia-smi` does not work, install or update the driver from:

```
Software & Updates → Additional Drivers
```

(or using your usual method).

Check with:

```bash
nvidia-smi
```

3. **Conda installed.**  
The script uses `conda create` and `conda activate`, so you need **Miniconda, Miniforge, or Anaconda** installed and available in your `PATH`.

4. Useful base packages:

```bash
# (The script will install many of these too, but it is good to have them.)
sudo apt update
sudo apt install -y git git-lfs openssl cmake build-essential unzip
```

---

# 💻 Isaac Sim Installation

## 1. Clone the repository with submodules

The script checks that the folder `teleimager` exists, which comes as a **submodule**, so clone it like this:

```bash
cd ~
git clone --recurse-submodules https://github.com/unitreerobotics/unitree_sim_isaaclab.git
cd unitree_sim_isaaclab
```

If you already cloned it **without submodules**:

```bash
cd ~/unitree_sim_isaaclab
git submodule update --init --depth 1
```

---

# 2. Run the auto-setup for Isaac Sim 5.1

From the root of the repository:

```bash
chmod +x auto_setup_env.sh
bash auto_setup_env.sh 5.1 unitree_sim_env cu126
```

### What this script does

Understanding this is important for debugging.

The script:

- Installs Ubuntu dependencies  
  (`cmake`, `build-essential`, `openssl`, `git-lfs`, `unzip`, etc.)

- Clones related repositories in the **parent directory**:

```
../IsaacLab
../cyclonedds
../unitree_sdk2_python
```

- Initializes repository submodules (`teleimager`)

- Generates **SSL certificates** (`key.pem` / `cert.pem`) and copies them to:

```
~/.config/xr_teleoperate/
```

You will need to press **Enter several times** when generating the certificate.

- Downloads **assets** using `fetch_assets.sh`  
  (uses **git-lfs** and downloads a large zip from **HuggingFace**, extracts it and creates the `assets/` folder in the repo)

- Compiles **CycloneDDS** and defines:

```
CYCLONEDDS_HOME
```

- Creates a **Conda environment with Python 3.11**  
  (required for **Isaac Sim 5.x**)

- Installs:

    - **PyTorch** (for 5.1 it uses `cu126` by default)
    - **Isaac Sim 5.1.0** via pip:

```
isaacsim[all,extscache]==5.1.0
```

    - **Isaac Lab** via:

```
./isaaclab.sh --install
```

    - `unitree_sdk2_python` in **editable mode**

    - repository requirements (`pyzmq`, `onnxruntime`, etc.)

    - `teleimager` in editable mode

- Applies a **patch installing `libstdcxx-ng` from conda-forge**  
  (required for Isaac Sim **5.0 / 5.1** compatibility)

**Important:** Isaac Lab explicitly states that **Isaac Sim 5.x requires Python 3.11.**

---

# 3. Ensure environment variables (recommended)

The script runs:

```
export CYCLONEDDS_HOME=...
```

but that variable only exists in that terminal.

To make it permanent when activating the conda environment:

```bash
conda activate unitree_sim_env
mkdir -p "$CONDA_PREFIX/etc/conda/activate.d"

cat > "$CONDA_PREFIX/etc/conda/activate.d/unitree_dds.sh" << 'EOF'
export CYCLONEDDS_HOME="$HOME/cyclonedds/install"
EOF
```

Adjust the path if `cyclonedds` was cloned somewhere else.

Normally the script clones it in the **parent folder of the repo**, so if the repo is in:

```
~/unitree_sim_isaaclab
```

then CycloneDDS will be in:

```
~/cyclonedds
```

---

# 4. Run a simulator demo

Activate the environment and launch a task:

```bash
conda activate unitree_sim_env
cd ~/unitree_sim_isaaclab

python sim_main.py \
  --device cpu \
  --enable_cameras \
  --task Isaac-PickPlace-Cylinder-G129-Dex1-Joint \
  --enable_dex1_dds \
  --robot_type g129
```

---

# Useful parameters (from the README)

`--task`  
Name of the task. Examples include:

- pick/place
- stacking
- whole-body movement

`--enable_dex1_dds` / `--enable_dex3_dds`  
Enable DDS communication for the **Dex1 gripper** or **Dex3 hand**.

`--robot_type`

```
g129
```

G1 with **29 DoF**

or

```
H1-2
```

27 DoF humanoid (depending on the task).

`--no_render`  
Runs without a window and streams via **WebRTC** (common in Docker or remote setups).

---

# Visual tip

When the scene starts, to view the main camera in Isaac Sim select:

```
PerspectiveCamera → Cameras → PerspectiveCamera
```