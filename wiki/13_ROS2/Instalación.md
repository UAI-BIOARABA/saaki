# 🧩 Guía de instalación y configuración de unitree_ros2

Este documento describe **cómo instalar el repositorio de ROS2 de Unitree**, **configurar todas las dependencias necesarias** y **garantizar que no existan conflictos** de instalación o funcionamiento en ningún equipo.

---

## 🧠 Requisitos previos

Este repositorio solo es compatible con Ubuntu 20.04 y 22.04 (Recomendado).

Asumimos el uso de Ubuntu 22.04.

Asegúrate de tener actualizado tu sistema:

```bash
sudo apt update && sudo apt upgrade -y
```

Antes de nada, asegurate que en tu /.bashrc no tenga source /opt/ros/humble/setup.bash añadido. Pa acomprobarlo:

```
nano ~/.bashrc
```

Busca si existe la linea /opt/ros/humble/setup.bash, si es así, coméntala.

## 💻 Instalación

Usamos nuestros propios Forks en la organización para asegurar estabilidad.

1. Lo clonamos en "~":

```
cd ~
git clone https://github.com/UAI-BIOARABA/unitree_ros2.git
```

2. Instala dependencias:

```
sudo apt install ros-humble-rmw-cyclonedds-cpp
sudo apt install ros-humble-rosidl-generator-dds-idl
sudo apt install libyaml-cpp-dev
```

3. Si usamos Humble en Ubuntu 22.04, no es necesario compilar manualmente Cyclone DDS, ya que la versión que instalamos en el paso anterior es compatible con el robot. Compila los paquetes directamente:

```
source /opt/ros/humble/setup.bash # Ahora puedes descomentar la línea del bash o añadirla si no estaba
cd ~/unitree_ros2/cyclonedds_ws
colcon build
```

4. Modifica los archivos setup.sh, setup_default.sh y default_local.sh y cambia foxy por humble. En el setup.sh, adicionalmente cambia la interfaz de red por la tuya:

```
#!/bin/bash
echo "Setup unitree ros2 environment"
source /opt/ros/humble/setup.bash
source $HOME/unitree_ros2/cyclonedds_ws/install/setup.bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
export CYCLONEDDS_URI='<CycloneDDS><Domain><General><Interfaces>
                            <NetworkInterface name="enp44s0" priority="default" multicast="default" />
                        </Interfaces></General></Domain></CycloneDDS>'
```

## ✅ Comprobación

1. Compila los ejemplos:

```
cd ~/unitree_ros2/example
colcon build
```

2. Haz source normal o local si estás en simulación:

```
source ~/unitree_ros2/setup_local.sh
```

3. Asegúrate de estar conectado al robot o de haber lanzado la simulación y comprueba los tópicos de ROS2:

```
ros2 topic list
```

4. Lanza un ejemplo:

```
cd example
./install/unitree_ros2_example/bin/g1_ankle_swing_example
```

## ♻️ Flujo de trabajo

Crea tu propio espacio de trabajo en tu Home si no lo tienes creado (convención típica en ROS2):

```
cd
mkdir -p ros2_ws/src
cd ~/ros2_ws/src
```

Prepara el entorno antes de trabaja, necesario en cada terminal que abras:

```
source ~/unitree_ros2/setup.sh
```

Crea un paquete nuevo para tu código:

```
# Ejemplo para C++
ros2 pkg create --build-type ament_cmake mi_paquete --dependencies rclcpp unitree_go unitree_api
```

Edita CMakeLists.txt y package.xml, verifica que:
- En package.xml: Que existan las líneas ```<depend>unitree_go</depend>```.
- En CMakeLists.txt: Que exista ```find_package(unitree_go REQUIRED)```.

Crea un repositorio en GitHub para cada paquete y así poder compartirlo en la organización.