# 🧩 Guía de instalación y configuración del SDK del LiDAR

Este documento describe **cómo instalar el SDK oficial**, **configurar todas las dependencias necesarias** y **garantizar que no existan conflictos** de instalación o funcionamiento en ningún equipo.

El unitree G1 tiene un LiDAR livox Mid-360, el cual tiene su SDK y progama de visualización propio.
- [Web oficial](https://www.livoxtech.com/mid-360)

Hay 3 formas de visualizar los datos del LiDAR:
- Mediante la aplicación viewer
- Mediante su SDK
- Mediante ROS2

---

## 🧠 Requisitos previos

Asegurate de tener un PC con Ubuntu y el Unitree G1 cerca.

Asumimos el uso de Ubuntu 22.04 en el PC y ROS2 Humble.

Asegúrate de tener actualizados ambos sistemas:

```bash
sudo apt update && sudo apt upgrade -y
```

Asegúrate de saber conectarte al robot como ya hemos hecho varias veces, es lo mismo solo que la IP del LiDAR es 192.168.123.120, puedes hacerle ping para comprobar que funciona.

## 💻 Instalación LivoxViewer2

1. Navega a la web oficial y entra en [downloads](https://www.livoxtech.com/mid-360/downloads) y descarga "Livox Viewer 2 - Ubuntu".
2. Descomprime el archivo descargado (Puedes cambiarle el nombre a uno más corto).
3. Entra a la carpeta y ejecuta el comando LivoxViewer2.sh:

```
cd LivoxViewer2 # O el nombre que le hayas puesto
source LivoxViewer2.sh
```

4. Si la red está bien configurada, el software detectará automáticamente el LiDAR Mid-360.
5. Dale al interruptor de "Connect" y luego al botón de "Play" (si no se ha hecho solo al lanzarse). Deberías ver la reconstrucción 3D de tu entorno. 

## 💻 Instalación SDK

Podrás ver los SDKs y repositorios en [su GitHub oficial](https://github.com/Livox-SDK).

1. Instala CMake si no lo tienes:
```
$ sudo apt install cmake
```

2. Compila e instala Livox-SDK2:

```
mkdir -p ~/ws_livox/src
cd ws_livox
git clone https://github.com/Livox-SDK/Livox-SDK2.git
cd Livox-SDK2
mkdir build
cd build
cmake .. && make -j
sudo make install
```

3. Nota:
La biblioteca compartida y la biblioteca estática generadas se instalan en el directorio "/usr/local/lib". Los archivos de encabezado se instalan en el directorio "/usr/local/include". Tips: Elimina Livox SDK2:

```
sudo rm -rf /usr/local/lib/liblivox_lidar_sdk_*
sudo rm -rf /usr/local/include/livox_lidar_*
```

4. Ve a la carpeta samples/livox_lidar_quick_start y edita el archivo mid360_config.json (IP del Host = Tu PC, IP del LiDAR = 192.168.123.120).

5. Ya puedes ejecutar un ejemplo o empezar a desarrollar tus propios programas.

## 💻 Instalación SDK ROS2

Asegúrate de tener bien instalado ROS2 Humble.

1. Abre tu terminal y ejecuta:

```
mkdir -p ~/ws_livox/src
```

2. Instalar Livox-SDK2:

Simplemente sigue los pasos del apartado anterior.

3. Clonar Livox ROS Driver 2:

```
cd ~/ws_livox/src
git clone https://github.com/Livox-SDK/livox_ros_driver2.git
```

4. Compilar el Driver para ROS2 Humble:

```
cd ~/ws_livox/src/livox_ros_driver2
source /opt/ros/humble/setup.sh
./build.sh humble
```

5. Modificar el archivo de Configuración

```
gedit ~/ws_livox/src/livox_ros_driver2/config/MID360_config.json
```

Cambia estas lineas:
```
{
  "lidar_summary_info" : {
    "lidar_type": 8
  },
  "MID360": {
    "lidar_net_info" : {
      "cmd_data_port": 56100,
      "push_msg_port": 56200,
      "point_data_port": 56300,
      "imu_data_port": 56400,
      "log_data_port": 56500
    },
    "host_net_info" : {
      "cmd_data_ip" : "192.168.123.x",  <-- PON AQUÍ LA IP DE TU ORDENADOR
      "cmd_data_port": 56101,
      "push_msg_ip": "192.168.123.x",   <-- PON AQUÍ LA IP DE TU ORDENADOR
      "push_msg_port": 56201,
      "point_data_ip": "192.168.123.x", <-- PON AQUÍ LA IP DE TU ORDENADOR
      "point_data_port": 56301,
      "imu_data_ip" : "192.168.123.x",  <-- PON AQUÍ LA IP DE TU ORDENADOR
      "imu_data_port": 56401,
      "log_data_ip" : "",
      "log_data_port": 56501
    }
  },
  "lidar_configs" : [
    {
      "ip" : "192.168.123.120",   <-- ESTA ES LA IP DEL LIDAR DEL UNITREE G1
      "pcl_data_type" : 1,
      "pattern_mode" : 0,
      "extrinsic_parameter" : {
        "roll": 0.0,
        "pitch": 0.0,
        "yaw": 0.0,
        "x": 0,
        "y": 0,
        "z": 0
      }
    }
  ]
}
```

6. Recompila por seguridad:

```
cd ~/ws_livox
rm -rf build/ install/ log/
colcon build --symlink-install --cmake-args -DROS_EDITION=ROS2 -DHUMBLE_ROS=humble
source install/setup.bash
```

7. Lanza el driver

```
ros2 launch livox_ros_driver2 rviz_MID360_launch.py
```

