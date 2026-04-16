# Unitree Reinforcement Learning Lab Guide

The [unitree_rl_lab](https://github.com/unitreerobotics/unitree_rl_lab) repo provides **reinforcement learning environments** for Unitree robots using **Isaac Lab (NVIDIA)**.

It allows you to:

* Train locomotion and control policies in simulation
* Run trained policies in simulation (`play`)
* Transfer policies (simulation → simulation or real robot)
* Work with multiple robots (Go2, H1, G1)

Main Workflow:

1. Install Isaac Lab + dependencies
2. Train a policy in simulation
3. Test it with `play`
4. Transfer it (`sim2sim` or `sim2real`)

---

## 💻 Installation

```bash
git clone https://github.com/unitreerobotics/unitree_rl_lab.git
cd unitree_rl_lab

conda create -n unitree_rl python=3.10
conda activate unitree_rl

pip install -e .
```

**Requires **Isaac Lab** properly installed beforehand.**

---

## 🧠 Training

Example (Go2 locomotion):

```bash
python scripts/train.py \
    --task=go2 \
    --headless
```

This will:

* Launch simulation
* Train an RL policy
* Save checkpoints

---

## ▶️ Play (Test Policy)

```bash
python scripts/play.py \
    --task=go2 \
    --checkpoint=PATH_TO_MODEL
```

Use this to:

* Visualize behavior
* Debug policies safely

---

## 🔁 Sim-to-Sim Transfer

Run trained policies in a different simulation setup:

```bash
python scripts/sim2sim.py ...
```

Useful for:

* Testing robustness
* Domain randomization validation

---

## 🤖 Sim-to-Real

Deploy policy to a real robot:

```bash
python scripts/sim2real.py ...
```

**Only recommended after stable simulation results.**

---

## 📂 Structure

* `scripts/` → training, play, deployment
* `tasks/` → robot environments
* `cfg/` → configuration files
* `resources/` → assets and models

---

## 💡 Tips

* Always test with `play` before real deployment
* Use headless mode for faster training
* Check Isaac Lab compatibility (common source of errors)
* Start with Go2 (simplest setup)

---

## 📋 Summary

`unitree_rl_lab` is a pipeline for:

**Simulation → RL training → validation → deployment (sim or real robot)**