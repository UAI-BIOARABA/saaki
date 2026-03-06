# 🧩 Vision Language Action and World Model Action

This document describes **what VLA and WMA are** and their role in the **Unitree G1 humanoid robot**.

Since these are **very advanced technologies**, we will not work with them yet. Therefore, there are **no practical examples or BioAraba repositories** related to them at the moment.

---

## 🧠 What are they?

### 1. Vision-Language-Action (VLA)

VLA models (such as the well-known **RT-2 from Google DeepMind**) are an evolution of the **language models (LLMs)** we use for chat applications. Their architecture combines **three capabilities within a single neural network**:

- **Vision:** The model can "see" through a camera or image input.
- **Language:** It understands instructions in natural language (e.g., *"Clean the spilled coffee"*).
- **Action:** Instead of producing text as output, the model generates **control tokens** (robot arm movements, rotations, etc.).

The key idea behind VLA:  
It treats **physical actions as if they were words**.

If an LLM predicts the **next word in a sentence**, a VLA predicts the **next motor movement** based on what it sees and the instruction it receives.

---

### 2. World Model Action (WMA)

**World Models** represent a different—and for many researchers, deeper—approach.

Instead of only reacting to instructions, the system builds an **internal simulation of reality**.

A World Model attempts to answer the question:

> *"If I do X, what will happen next in the environment?"*

Key capabilities include:

- **Prediction:**  
  The model can imagine possible futures. For example, if a robot pushes a glass, the world model predicts that the glass may fall.

- **Simulation:**  
  The robot can effectively **train inside its own internal simulation** before interacting with real objects, reducing mistakes and risks.

- **Action:**  
  The robot selects the action that leads to the **best predicted future outcome** for completing the task.

---

## Why are they important?

Until recently, if you wanted a robot to perform a new task, you had to **program it from scratch**.

With **VLA**, a robot could understand a command such as:

> *"Pass me the toy dinosaur"*

even if it has **never seen that exact toy before**, because it has learned what a **dinosaur** is through large-scale training.

With **World Models**, robots gain a form of **physical common sense**, avoiding dangerous actions because they can **predict negative outcomes before they occur**.

---

## How do we integrate them into the Unitree G1?

Unitree provides **two repositories** to integrate these models into the robot.

These repositories include **datasets from the Z1 robotic arm (which we do not have)** and **datasets from the G1**.

One limitation is that they use **grippers instead of dexterous hands**:

- **Grippers:** 1 degree of freedom  
- **Dexterous hands:** up to 7 degrees of freedom

This greatly simplifies training and suggests that **training with full robotic hands is significantly more difficult**.

---

## VLA

Unitree provides the following repository based on **UnifoLM**:

[unifolm-vla](https://github.com/unitreerobotics/unifolm-vla)

---

## WMA

Unitree provides the following repository based on **UnifoLM**:

[unifolm-world-model-action](https://github.com/unitreerobotics/unifolm-world-model-action)