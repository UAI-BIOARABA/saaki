# 🧩 Guía de instalación y configuración de MuJoCo

Este documento describe **cómo instalar MuJoCo**, **el repositorio oficial de unitree para configuración** y **garantizar que no existan conflictos** de instalación o funcionamiento en ningún equipo.

---

## 🧠 Requisitos previos

Asegurate de esta utilizando Ubuntu 20.04 o 22.04 (Recomendado).

Asegúrate de tener actualizado tu sistema:

```bash
sudo apt update && sudo apt upgrade -y
```

También asegurate haber instalado los SDK de Unitree según las guías proporcionadas en la [Guía SDK G1](05_SDK_G1/).


## 💻 Instalación C++

Dpendencias:

```
sudo apt install libyaml-cpp-dev libspdlog-dev libboost-all-dev libglfw3-dev
```

Clonamos el repositorio oficial de Unitree. Usamos nuestros propios Forks en la organización para asegurar estabilidad.

Lo clonamos en "~" pero luego lo podemos mover si queremos.

```
cd ~
git clone https://github.com/UAI-BIOARABA/unitree_mujoco.git
```

Descarga [MuJoCo](https://github.com/google-deepmind/mujoco/releases), elige la versión 3.3.6 (mujoco-3.3.6-linux-x86_64.tar.gz).

Extrae el archivo en tu carpeta home dentro de una carpeta oculta llamada .mujoco.

Comandos rápidos:

```
mkdir ~/.mujoco
tar -xvf mujoco-3.3.6-linux-x86_64.tar.gz -C ~/.mujoco
# Al final deberías tener una ruta válida tipo: ~/.mujoco/mujoco-3.3.6
```

Ahora entra en el repostiorio clonado:

```
cd unitree_mujoco/simulate
# IMPORTANTE: Crear el enlace simbólico a tu instalación de MuJoCo.
# Asegúrate de que la versión coincida con la que descargaste (mujoco-3.3.6).
ln -s ~/.mujoco/mujoco-3.3.6 mujoco
```

Compila el simulador C++:

```
# Estando dentro de unitree_mujoco/simulate
mkdir build
cd build
cmake ..
make -j4
```

Si todo sale bien, tendrás un ejecutable llamado unitree_mujoco que puedes lanzar con:

```
cd ~/unitree_mujoco/simulate/build
./unitree_mujoco -r g1 -s scene.xml
```

## 💻 Instalación Python

Activa el entorno creado cuando instalaste el SDK:

```
conda activate nombre-entorno
```

Ahora solo necesitas clonar el repositorio, si ya lo has hecho al instalarlo para C++ no lo vuelvas a clonar:
```
cd ~
git clone https://github.com/UAI-BIOARABA/unitree_mujoco.git
```

Ya puedes lanzarlo:

```
cd unitree_mujoco/simulate_python
python3 unitree_mujoco.py
```
