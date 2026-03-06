# 🧩 Low-Level Development Instructions

This document describes **instructions on how to develop low-level code** for the **Unitree G1 humanoid robot**.

---

## 🧠 Prerequisites

Make sure you have **both SDKs installed** according to the guide provided in:

```
/wiki/05_SDK_G1/
```

The SDKs are only compatible with **Ubuntu 20.04 and 22.04 (recommended)**.

It is also recommended to become familiar with the SDK you want to use. Spend some time understanding **how it works, how it communicates (via DDS), and how it is structured internally**.

---

# 🦯 Familiarization

Low-level development involves sending **direct commands to the motors** (position, torque, damping, etc.).

You have **full control over the robot**.  
Even its **balance controller is disabled** during low-level development, so the robot must always:

- Be **suspended** (hanging)
- Be in **"Develop" mode**
- Usually while holding **L2 + R2 on the controller**

To start developing your own programs, you can clone our repository:

https://github.com/UAI-BIOARABA/saaki-core

Clone it into your **HOME directory**.

---

# Supported languages

You can develop using two languages, each requiring its corresponding SDK:

- **Python**
- **C++**

---

# Repository structure

You will find a structure similar to this:

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
    ├── requirements.txt        # Python dependencies
    ├── CONTRIBUTING.md         # Contribution guidelines
    └── README.md               # Repository information


unitree_mujoco/               }
unitree_sdk2/                 }  # Official Unitree repositories
unitree_sdk2_python/          }  # (Use BioAraba forks)
```

---

# Understanding the examples

If you followed the recommendations and are already somewhat familiar with the SDK, the next step is to examine the **examples included in the `low` folders** for both:

- Python
- C++

These examples are **heavily documented**, making it easier to understand what each part of the program does.

---

# 💻 Development Workflow

Developing new programs is now straightforward.

1. **Create a branch**
2. **Create the `.py` or `.cpp` file** in the corresponding folder
3. **Develop your program** based on the examples
4. **Commit your changes**
5. **Create a Pull Request**

Follow the detailed instructions in the `CONTRIBUTING.md` file inside the repository.

---

# ✅ Done!

Once the PR is reviewed and merged, your code will be integrated into the project.