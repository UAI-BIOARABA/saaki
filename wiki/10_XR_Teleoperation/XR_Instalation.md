# 🧩 XR Glasses Setup Guide for Teleoperation (Ubuntu)

This document describes **how to configure XR glasses** to teleoperate the robot both **in simulation and in the real world**.  
For this purpose, we will use the Unitree repository:

- https://github.com/unitreerobotics/xr_teleoperate

---

# 🧠 Prerequisites

Make sure your system is updated and install `openssl`:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git openssl
```

---

# 💻 Installing the xr_teleoperate Repository

## 1. Install Conda (x86_64 or ARM aarch64)

Option A: If you **already have Conda / Miniforge installed**, skip to section 2.

Option B: **ARM / Jetson (aarch64)**

The wiki includes this installation flow using **Miniconda on ARM** (Ubuntu 22.04 on Jetson Orin NX).

```bash
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-aarch64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh
source ~/miniconda3/bin/activate
conda init --all
```

For **x86_64**, use the Linux-x86_64 installer instead (same logic).

---

# 2. Create the tv Environment (Python 3.10, Pinocchio, NumPy)

Both the README and the x86 install logs use exactly this configuration:

```bash
conda create -n tv python=3.10 pinocchio=3.1.0 numpy=1.26.4 -c conda-forge
conda activate tv
```

---

# 3. Clone the Repository and Submodules

According to the README:

```bash
git clone https://github.com/unitreerobotics/xr_teleoperate.git
cd xr_teleoperate
git submodule update --init --depth 1
```

The install logs show that the repository includes submodules such as:

- televuer
- dex-retargeting

---

# 4. Install the Project Modules

## 4.1 teleimager

The README instructs installing it **in editable mode without dependencies** to avoid overriding versions.

```bash
cd teleop/teleimager
pip install -e . --no-deps
cd ../../
```

---

## 4.2 televuer (XR streaming / control)

The README and install logs install it with:

```bash
cd teleop/televuer
pip install -e .
```

---

## 4.3 SSL Certificates for XR (Pico / Quest or Apple Vision Pro)

This step is **critical**.  
Without certificates, XR devices usually block **HTTPS/WebRTC connections**.

---

### Pico / Quest (simpler)

```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout key.pem -out cert.pem
```

Fill in the certificate fields when prompted.

---

### Apple Vision Pro (Root CA + SANs)

Generate Root CA:

```bash
openssl genrsa -out rootCA.key 2048
openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 365 -out rootCA.pem -subj "/CN=xr-teleoperate"
```

Generate server key:

```bash
openssl genrsa -out key.pem 2048
openssl req -new -key key.pem -out server.csr -subj "/CN=localhost"
```

Create `server_ext.cnf` with your real IP addresses:

```bash
subjectAltName = @alt_names
[alt_names]
DNS.1 = localhost
IP.1 = 192.168.123.164
IP.2 = 192.168.123.2
```

Sign the certificate:

```bash
openssl x509 -req -in server.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial \
  -out cert.pem -days 365 -sha256 -extfile server_ext.cnf
```

For **Apple Vision Pro**, install `rootCA.pem` on the device (AirDrop or similar).

---

# Open the firewall port for televuer

```bash
sudo ufw allow 8012
```

---

# Tell the application where the certificates are

Choose **one method**.

### Option 1: Copy to user config

```bash
mkdir -p ~/.config/xr_teleoperate/
cp cert.pem key.pem ~/.config/xr_teleoperate/
```

---

### Option 2: Environment variables

```bash
echo 'export XR_TELEOP_CERT="$HOME/xr_teleoperate/teleop/televuer/cert.pem"' >> ~/.bashrc
echo 'export XR_TELEOP_KEY="$HOME/xr_teleoperate/teleop/televuer/key.pem"' >> ~/.bashrc
source ~/.bashrc
```

---

# 5. Install dex-retargeting (Hand / EEF Retargeting)

From the x86 install log:

```bash
cd teleop/robot_control/dex-retargeting
pip install -e .
cd ../../../
```

---

# 6. Install Python Dependencies

The repository defines its dependencies in `requirements.txt`.

```bash
pip install -r requirements.txt
```

---

# 7. Install unitree_sdk2_python (Robot Communication)

The README instructs cloning and installing it in editable mode.

```bash
cd ~
git clone https://github.com/unitreerobotics/unitree_sdk2_python.git
cd unitree_sdk2_python
pip install -e .
```

Important note from the README:

xr_teleoperate v1.1+ requires `unitree_sdk2_python` to be **at least a specific commit version**.

This should not be a problem since our **forks are kept updated**, but they should be **checked periodically**.

---

# 8. Quick Verification Checklist

Return to the repository and check that everything imports correctly:

```bash
conda activate tv
python -c "import televuer, dex_retargeting; print('OK')"
```

---

# ⚠️ Common Errors

## 1. NumPy 2.x Conflict

pip often upgrades NumPy automatically to **2.x** because many packages only specify:

```
numpy>=1.21
```

However, **xr_teleoperate is NOT compatible with NumPy 2.x**.

### Solution

Remove NumPy installed by pip (repeat until pip says it is not installed):

```bash
pip uninstall -y numpy
```

Install the correct version with conda:

```bash
conda install -y -c conda-forge numpy=1.26.4
```

Verify the version:

```bash
python -c "import numpy as np; print(np.__version__)"
```

Expected output:

```
1.26.4
```

---

## 2. ImportError: cannot import name 'Vuer' from 'vuer'

The PyPI package `vuer==0.0.60`:

- Does **not re-export `Vuer` in `vuer/__init__.py`**
- But the class exists in `vuer.server`

### Solution

Edit:

```
teleop/televuer/src/televuer/televuer.py
```

Change:

```python
from vuer import Vuer
```

to:

```python
from vuer.server import Vuer
```

Then verify:

```bash
python -c "import televuer"
```

---

## 3. ImportError: cannot import name 'Flag' from 'params_proto'

This is caused by **incompatible versions of `params-proto`**.

Some newer versions changed the API and `vuer.server` requires `Flag`.

### Solution

Install a compatible version:

```bash
pip uninstall -y params-proto
pip install params-proto==2.13.2
```

Verify:

```bash
python -c "from params_proto import Flag"
```

If it imports correctly, the problem is solved.

---

## 4. Other Errors

This repository has **many dependencies** and conflicts may occur.

Although it may not be the most professional advice, we strongly recommend **having an LLM available to help debug errors**, since the official documentation often does not include solutions for many of them.