# 🧩 Guía de instalación y configuración de Android Studio (Ubuntu)

Este documento describe **cómo instalar Android Studio**, **configurar todas las dependencias necesarias** y **garantizar que el entorno de desarrollo sea completamente reproducible** en otro equipo.

---

## 🧠 Requisitos previos

Asegúrate de tener actualizado tu sistema:

```bash
sudo apt update && sudo apt upgrade -y
```

Instala algunas utilidades base:

```bash
sudo apt install wget unzip git curl -y
```

---

## 💻 Instalación de Android Studio

### 🔹 Opción 1: Instalación rápida con Snap (recomendada)

```bash
sudo snap install android-studio --classic
```

Verifica la instalación:

```bash
snap info android-studio
```

Ejecuta Android Studio:

```bash
android-studio
```

---

### 🔹 Opción 2: Instalación manual desde el sitio oficial

1. Descarga Android Studio desde:  
   👉 [https://developer.android.com/studio](https://developer.android.com/studio)

2. Extrae el archivo descargado:

   ```bash
   tar -xvf android-studio-*-linux.tar.gz
   ```

3. Mueve la carpeta a `/opt`:

   ```bash
   sudo mv android-studio /opt/
   ```

4. Inicia Android Studio:

   ```bash
   /opt/android-studio/bin/studio.sh
   ```

---

## ☕ Instalar Java (JDK)

Android Studio requiere **OpenJDK 17 o superior**:

```bash
sudo apt install openjdk-17-jdk -y
```

Verifica la instalación:

```bash
java -version
```

---

## ⚙️ Instalar dependencias del emulador Android

Para usar el **emulador (AVD)** en Ubuntu, instala los paquetes de virtualización:

```bash
sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager -y
sudo adduser $USER kvm
```

> 🔁 Cierra sesión o reinicia para aplicar los cambios.

---

## 🧱 Configuración inicial de Android Studio

1. Abre Android Studio (`android-studio`)  
2. Sigue el **Asistente de configuración** inicial:
   - Instala el **SDK** (Android 14 o superior recomendado)
   - Acepta las licencias
   - Descarga los **Build-tools**
   - Instala el **Android Emulator**
3. Verifica las herramientas desde:

   ``` Android Studio
   Tools → SDK Manager
   Tools → AVD Manager
   ```

Recomendado instalar:

- ✅ Android SDK Platform
- ✅ Android SDK Build-Tools
- ✅ Android Emulator
- ✅ Android SDK Command-line Tools
- ✅ Google APIs / Google Play (si usarás servicios de Google)

---

## 🧩 Verificación de SDK y herramientas

Para ver versiones y rutas del SDK:

```bash
echo $ANDROID_HOME
ls $ANDROID_HOME/platforms
```

Si no está definida la variable, agrégala a tu `.bashrc`:

```bash
echo 'export ANDROID_HOME=$HOME/Android/Sdk' >> ~/.bashrc
echo 'export PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools' >> ~/.bashrc
source ~/.bashrc
```

---

## 🧰 Instalar Git

Git se usa para control de versiones del proyecto.

```bash
sudo apt install git -y
git --version
```

Configura tus credenciales:

```bash
git config --global user.name Tu_Nombre
git config --global user.email tuemail@ejemplo.com
```

---

## 🧠 Crear un nuevo proyecto Android con Kotlin

1. Abre Android Studio  
2. Selecciona **New Project → Empty Activity**
3. Lenguaje: **Kotlin**
4. SDK mínimo: **API 24 (Android 7.0)** o según tu necesidad
5. Finaliza y espera la sincronización de Gradle.

---

## 🧪 Ejecutar en emulador o dispositivo físico

- **Emulador:** crea un dispositivo virtual desde  
  `Tools → Device Manager → Create Device`
  
  Elige por ejemplo:  
  - Dispositivo: *Pixel 3*  
  - Sistema: *Android 6.0 (x86)* o superior  
  - Imagen: **x86 (Google APIs)** para mejor compatibilidad  

- **Dispositivo físico:**  
  1. Activa “Depuración por USB” en las *Opciones de desarrollador*  
  2. Conecta el teléfono con un cable USB  
  3. Autoriza la conexión cuando Android Studio lo solicite

---

## 📦 Estructura del proyecto y dependencias

### 📁 `gradle-wrapper.properties`

Contiene la versión de Gradle usada:

``` files
distributionUrl=https\://services.gradle.org/distributions/gradle-8.7-bin.zip
```

### 📁 `build.gradle` (raíz)

Define versiones de plugins y Kotlin:

```gradle
classpath("com.android.tools.build:gradle:8.7.0")
classpath(kotlin("gradle-plugin", version = "1.9.24"))
```

### 📁 `app/build.gradle`

Define versiones del SDK y librerías:

``` gradle
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

## 📚 Referencias

- [Android Studio Documentation](https://developer.android.com/studio)
- [Kotlin for Android](https://developer.android.com/kotlin)
- [Gradle Build System](https://gradle.org/)
- [Android Virtual Device Manager](https://developer.android.com/studio/run/managing-avds)
