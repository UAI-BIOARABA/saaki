# 🧩 High-Level Development Instructions

This document describes **instructions on how to develop high-level code** for the **Unitree G1 humanoid robot**.  
The document is practically the same as the Low-Level guide, since both types of development usually **go hand in hand**.

---

## 🧠 Prerequisites

Make sure you have **both SDKs installed** according to the guide provided in:

```
/wiki/05_SDK_G1/
```

The SDKs are only compatible with **Ubuntu 20.04 and 22.04 (recommended)**.

As with low-level development, we also recommend becoming familiar with the SDK you plan to use. Spend some time understanding **how it works, how it communicates (via DDS), and how it is structured**.

If you followed the installation guide, this should not be a problem. Just review the **high-level APIs**, which are usually named something like:

- `LOCO...`
- `SPORTS...`

depending on the version and, most importantly, on how the developers named them for each robot.

---

## 🦯 Familiarization

High-level development involves sending **movement commands or actions** to the robot, such as:

- Walk at **5 m/s**
- **Wave**
- **Play an audio file**

Unlike low-level development, here we **do not have full control over the robot**. The robot's **balance controller is always active**, and we are limited to the **APIs provided by the manufacturer** (or ones we develop ourselves).

It is important to note that **this form of development is significantly easier**, in fact **the easiest approach overall**.

If you have already explored low-level development, you should already have cloned our repository:

https://github.com/UAI-BIOARABA/saaki-core

into your **HOME directory**.

---

# Supported languages

You can develop using two languages, each requiring its corresponding SDK:

- **Python**
- **C++**

---

# Repository structure

You will find a structure similar to the following:

```
└── saaki-core/                 ← core (Python | C++)
    ├── .vscode                 # VS Code configuration (if used)
    ├── build                   # Compiled C++ programs
    │
    ├── python/                 # Control scripts and logic (Python SDK)
    │   ├── high/
    │   │   └── ___.py
    │   └── low/
    │       └── ___.py
    │
    ├── cpp/                    # High-performance code (C++)
    │   ├── high/
    │   │   ├── ___.cpp
    │   │   ├── ...
    │   │   └── CMakeLists.txt
    │   │
    │   └── low/
    │       ├── ___.cpp
    │       ├── ...
    │       └── CMakeLists.txt
    │
    ├── .gitignore              # Files we do not want to upload to GitHub
    ├── requirements.txt        # Python project dependencies
    ├── CONTRIBUTING.md         # Contribution / collaboration guidelines
    └── README.md               # Repository information


unitree_mujoco/               }
unitree_sdk2/                 }  # Official Unitree repositories
unitree_sdk2_python/          }  # (Use BioAraba forks)
```

---

# Understanding the examples

If you followed the recommendations and already have a basic understanding of the SDK, the next step is to examine the **examples included in the `high` folders** for both:

- Python
- C++

These examples are **well documented**, making it easier to understand what each part of the program does.

---

# 💻 Development Workflow

Developing new programs is straightforward.

1. **Create a branch**
2. **Create the `.py` or `.cpp` file** in the corresponding folder
3. **Develop your program** based on the examples
4. **Commit your changes**
5. **Create a Pull Request**

Follow the detailed instructions in the `CONTRIBUTING.md` file inside the repository.

---

# ✅ Done!

Once the PR is reviewed and merged, your code will be integrated into the project.