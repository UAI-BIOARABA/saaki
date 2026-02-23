# 🧩 Uso y desarrollo en ROS2

Para poder desarrollar nuestros propios programas de ROS2, se recomienda trabajar en nuestro propio workspace (ros2_ws):

- Podemos compartir nuestros paquetes en GitHub
- No tendremos problemas con actualizaciones de Unitree
- Podemos tener una estructura más escalable
- ...

Para comenzar el desarrollo, hemos adaptado los ejemplos oficiales que nos proporciona Unitree en [unitree_ros2](https://github.com/unitreerobotics/unitree_ros2) para poder ejecutarlos con los comandos estandar de ROS y eliminar código redundante de otros robots.

## 🧠 Requisitos previos

Haber sguido la [guía de instalación de ROS2](Instalación.md) 😊.

Para ver la guía con más detalle y el código fuente, visita [saaki-ros2-examples](https://github.com/UAI-BIOARABA/saaki-ros2-examples).

## 💻 Resumen de instalación

Entra en tu workspace y clona el repo:

```
cd ~/ros2_ws/src
git clone https://github.com/UAI-BIOARABA/saaki-ros2-examples.git saaki_ros2_examples
```
Ir a la raíz del workspace y carga entornos:

```
cd ~/ros2_ws

# IMPORTANTE: Cargar el entorno de Unitree ANTES de compilar
source ~/unitree_ros2/setup.sh

# Compilar este paquete
colcon build --symlink-install
```

## 🏃‍♂️ Ejecutar nodos

Ahora podrás ejecutar nodos de forma sencilla, por ejemplo:

```
ros2 run saaki_ros2_examples read_low_state_hg
```