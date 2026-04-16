# 🧩 Imitation Learning Guide

The [unitree_lerobot](https://github.com/unitreerobotics/unitree_lerobot) repo connects **Unitree robot data** with the **LeRobot framework**.

It lets you:

* Convert Unitree datasets → LeRobot format
* Train robot policies (imitation learning)
* Run inference (dataset, simulation, or real robot)
* Replay recorded episodes on the robot

Main Workflow:

1. Collect or download a dataset
2. Convert it to LeRobot format
3. Train a policy
4. Evaluate it (dataset → simulation → real robot)

---

## 💻 Installation

```bash
git clone --recurse-submodules https://github.com/unitreerobotics/unitree_lerobot.git
cd unitree_lerobot

conda create -y -n unitree_lerobot python=3.10
conda activate unitree_lerobot

conda install -c conda-forge pinocchio ffmpeg

cd unitree_lerobot/lerobot
pip install -e .

cd ../../
pip install -e .
```

(Optional: install Unitree SDK)

```bash
git clone https://github.com/unitreerobotics/unitree_sdk2_python.git
cd unitree_sdk2_python
pip install -e .
```

---

## ↔️ Dataset Conversion

### 1. Fix episode naming

```bash
python unitree_lerobot/utils/sort_and_rename_folders.py \
    --data_dir /path/to/dataset
```

### 2. Convert to LeRobot

```bash
python unitree_lerobot/utils/convert_unitree_json_to_lerobot.py \
    --raw-dir /path/to/dataset \
    --repo-id your_name/dataset_name \
    --robot_type Unitree_G1_Dex3 \
    --push_to_hub
```

---

## 🦾 Training

```bash
cd unitree_lerobot/lerobot

python src/lerobot/scripts/lerobot_train.py \
    --dataset.repo_id=your_name/dataset_name \
    --policy.type=diffusion \
    --policy.push_to_hub=false
```

Other policy types:

* act
* diffusion
* pi0 / pi05
* groot

---

## 🧪 Evaluation

### On dataset (safe)

```bash
python unitree_lerobot/eval_robot/eval_g1_dataset.py \
    --policy.path=PATH_TO_MODEL \
    --repo_id=your_name/dataset_name \
    --visualization=true \
    --send_real_robot=false
```

### On simulation

```bash
python unitree_lerobot/eval_robot/eval_g1_sim.py ...
```

### On real robot

```bash
python unitree_lerobot/eval_robot/eval_g1.py ...
```

---

## ♻️ Replay Dataset

```bash
python unitree_lerobot/eval_robot/replay_robot.py \
    --repo_id=your_name/dataset_name
```

---

## 💡 Tips

* Start with dataset evaluation (not real robot)
* Use diffusion or act first
* Make sure FFmpeg is installed
* Login to Hugging Face if needed:

```bash
huggingface-cli login
```

---

## 📋 Summary

`unitree_lerobot` is a bridge:

**Unitree data → LeRobot → training → evaluation → robot execution**
