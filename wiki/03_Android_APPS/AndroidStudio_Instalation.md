# 🧩 Android Studio Installation and Configuration Guide (Ubuntu)

This document describes **how to install Android Studio**, **configure all the necessary dependencies**, and **ensure that the development environment is fully reproducible** on another machine.

---

## 🧠 Prerequisites

Make sure your system is up to date:

```bash
sudo apt update && sudo apt upgrade -y
```

Install some base utilities:

```bash
sudo apt install wget unzip git curl -y
```

---

## 💻 Installing Android Studio

### 🔹 Option 1: Quick installation with Snap (recommended)

```bash
sudo snap install android-studio --classic
```

Verify the installation:

```bash
snap info android-studio
```

Run Android Studio:

```bash
android-studio
```

---

### 🔹 Option 2: Manual installation from the official website

1. Download Android Studio from:  
   👉 https://developer.android.com/studio

2. Extract the downloaded file:

```bash
tar -xvf android-studio-*-linux.tar.gz
```

3. Move the folder to `/opt`:

```bash
sudo mv android-studio /opt/
```

4. Start Android Studio:

```bash
/opt/android-studio/bin/studio.sh
```

---

## ☕ Install Java (JDK)

Android Studio requires **OpenJDK 17 or higher**:

```bash
sudo apt install openjdk-17-jdk -y
```

Verify the installation:

```bash
java -version
```

---

## ⚙️ Install Android emulator dependencies

To use the **Android emulator (AVD)** on Ubuntu, install the virtualization packages:

```bash
sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager -y
sudo adduser $USER kvm
```

> 🔁 Log out or reboot to apply the changes.

---

## 🧱 Initial Android Studio setup

1. Open Android Studio (`android-studio`)  
2. Follow the **initial setup wizard**:
   - Install the **SDK** (Android 14 or higher recommended)
   - Accept the licenses
   - Download the **Build-tools**
   - Install the **Android Emulator**

3. Verify the tools from:

```
Android Studio
Tools → SDK Manager
Tools → AVD Manager
```

Recommended components to install:

- ✅ Android SDK Platform
- ✅ Android SDK Build-Tools
- ✅ Android Emulator
- ✅ Android SDK Command-line Tools
- ✅ Google APIs / Google Play (if you will use Google services)

---

## 🧩 Verifying SDK and tools

To view SDK versions and paths:

```bash
echo $ANDROID_HOME
ls $ANDROID_HOME/platforms
```

If the variable is not defined, add it to your `.bashrc`:

```bash
echo 'export ANDROID_HOME=$HOME/Android/Sdk' >> ~/.bashrc
echo 'export PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools' >> ~/.bashrc
source ~/.bashrc
```

---

## 🧰 Install Git

Git is used for project version control.

```bash
sudo apt install git -y
git --version
```

Configure your credentials:

```bash
git config --global user.name Your_Name
git config --global user.email youremail@example.com
```

---

## 🧠 Create a new Android project with Kotlin

1. Open Android Studio  
2. Select **New Project → Empty Activity**
3. Language: **Kotlin**
4. Minimum SDK: **API 24 (Android 7.0)** or according to your needs
5. Finish and wait for the Gradle synchronization.

---

## 🧪 Run on emulator or physical device

- **Emulator:** create a virtual device from  
  `Tools → Device Manager → Create Device`
  
  For example choose:  
  - Device: *Pixel 3*  
  - System: *Android 6.0 (x86)* or higher  
  - Image: **x86 (Google APIs)** for better compatibility  

- **Physical device:**  
  1. Enable “USB Debugging” in *Developer Options*  
  2. Connect the phone with a USB cable  
  3. Authorize the connection when Android Studio prompts it

---

## 📦 Project structure and dependencies

### 📁 `gradle-wrapper.properties`

Contains the Gradle version used:

```files
distributionUrl=https\://services.gradle.org/distributions/gradle-8.7-bin.zip
```

### 📁 `build.gradle` (root)

Defines plugin and Kotlin versions:

```gradle
classpath("com.android.tools.build:gradle:8.7.0")
classpath(kotlin("gradle-plugin", version = "1.9.24"))
```

### 📁 `app/build.gradle`

Defines SDK versions and libraries:

```gradle
android {
    compileSdk = 34

    defaultConfig {
        minSdk = 24
        targetSdk = 34
    }
}

dependencies {
    implementation("androidx.core:core-ktx:1.12.0")
    implementation("androidx.appcompat:appcompat:1.7.0")
    implementation("com.google.android.material:material:1.12.0")
    implementation("androidx.constraintlayout:constraintlayout:2.1.4")
}
```

---

## 📚 References

- https://developer.android.com/studio  
- https://developer.android.com/kotlin  
- https://gradle.org/  
- https://developer.android.com/studio/run/managing-avds