# 🧩 Guía de instalación y configuración de los SDK oficiales del Unitree G1

Este documento describe **cómo instalar los SDK oficiales**, **configurar todas las dependencias necesarias** y **garantizar que no existan conflictos** de instalación o funcionamiento en ningún equipo.

Unitree nos proporciona 2 SDK sobre los que trabajar:
- SDK C++
- SDK Python

Realmente el SDK en Python es solo un "envoltorio" del SDK en C++, el cual es más rápido pero más difícil y verboso. En general, se recomienda tener ambos instalados, ya que otros repositorios pueden depender del SDK original (C++) pero para probar scripts en Python necesitaremos el otro también.

---

## 🧠 Requisitos previos

Los SDK solo son compatibles con Ubuntu 20.04 y 22.04 (Recomendado).

Asegúrate de tener actualizado tu sistema:

```bash
sudo apt update && sudo apt upgrade -y
```

Antes de nada, asegurate que las siguientes dependencias están instaladas:

- CMake (versión 3.10 o superior)
- GCC (versión 9.4.0)
- Make

Instala dependencias:
```
apt-get update
apt-get install -y cmake g++ build-essential libyaml-cpp-dev libeigen3-dev libboost-all-dev libspdlog-dev libfmt-dev
```

## 💻 Instalación SDK C++

Usamos nuestros propios Forks en la organización para asegurar estabilidad.

Lo clonamos en "~" pero luego lo podemos mover si queremos.

```
cd ~
git clone https://github.com/UAI-BIOARABA/unitree_sdk2.git
cd unitree_sdk2

# Crear carpeta de compilación
mkdir build
cd build

# Compilar configurando la ruta de instalación recomendada
cmake .. -DCMAKE_INSTALL_PREFIX=/opt/unitree_robotics

# Instalar (requiere sudo)
sudo make install
```

Para hacer build de los ejemplos del repositorio:

```
cd build
cmake ..
make
```

## 💻 Instalación SDK Python

Usamos nuestros propios Forks en la organización para asegurar estabilidad.

Lo clonamos en "~" pero luego lo podemos mover si queremos.

```
cd ~
git clone https://github.com/UAI-BIOARABA/unitree_sdk2_python.git
```

Creamos un entorno virtual con Conda y lo instalamos:

```
# Crear entorno
conda create --name nombre-entorno python=3.10.12

# Activar en cada terminal
conda activate nombre-entorno

# Instalar dependencias del SDK en modo editable
cd unitree_sdk2_python

# Instalar las dependencias de compilación necesarias (por si acaso)
pip install setuptools wheel
pip install -e .

# Instalar dependencias adicionales
pip install matplotlib pydot    # Las instalamos ahora porque si no puede dar error
```

Ya podemos probar los ejemplos incluidos en el repositorio lanzándolos en una terminal, ya sea en el robot real o en el simulador MuJoCo.
