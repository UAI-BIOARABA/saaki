# 🗒️ Summary of the Saaki Audio Program Usage

This document provides a brief description of **Saaki's audio generation program**.

---

## ❗ Problem

The Unitree G1 is connected to a GPT-based LLM in the cloud and includes an internal TTS engine that allows it to reproduce the responses of the language model using voice.

This sounds like great news, but the problem is that the TTS is only available in **CHINESE** and **ENGLISH**, so we can say that it does not speak our language.

In addition to the language limitation, the cloud also poses a problem. In **Osakidetza**, we cannot process personal data or patient voices in the cloud.

---

## ♻️ Solution

To reproduce audio in Spanish and Basque, there would be two options:

- Install a local LLM and TTS in Spanish and Basque → Complicated, consumes many resources, and could be potentially unsafe for patients  
- Generate the audio files ourselves for what we want the robot to say → Less natural, but **100% safe and reliable**

Unitree provides the option to load audio files onto the robot from the mobile app (limit of 10) and through **High-Level programming (no limit)**, which allows us to play them whenever we want.

For this purpose, we developed a program that allows us to generate voice audio for Saaki in **SPANISH** and **BASQUE**, allowing us to adjust the tone of the voice so that it does not sound intimidating.

The only drawback is that it can **only be used on Windows**, since the voices we need to install are only available for that operating system.

---

To access the repository and the complete guide:

- [🔗 saaki-audios - Github](https://github.com/UAI-BIOARABA/saaki-audios)