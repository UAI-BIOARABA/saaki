# 🧩 Guía de instalación y configuración del SDK de la cámara

Este documento describe **cómo instalar el SDK oficial**, **configurar todas las dependencias necesarias** y **garantizar que no existan conflictos** de instalación o funcionamiento en ningún equipo.

El unitree G1 tiene una cámara Intel Realsense D435i, la cual tiene su SDK propio.
- [SDK librealsense](https://github.com/realsenseai/librealsense.git)

El SDK tiene su propia guía de instalación para las diferentes plataformas (Windows, Ubuntu, Jetson...), pero puede ser un poco confusa, por lo que trataremos de complementarla aquí.

---

## 🧠 Requisitos previos

Asegurate de tener un PC con Ubuntu y el Unitree G1 cerca.

Esto sirve para poder usar el SDK en nuestro PC y procesar datos de la cámara, lo que implica conectar la cámara directamente a nuestro PC en lugar de al robot.

No es lo ideal pero puede servir para depurar y realizar pruebas, sobretodo si disponemos de una cámara extra que no esté fija en el robot.

## 💻 Instalación en nuestro PC

Asumimos el uso de Ubuntu 20.04 o 22.04 (Recomendado).

Asegúrate de tener actualizado tu sistema:

```bash
sudo apt update && sudo apt upgrade -y
```

### Instalación de dependencias

Instala los paquetes necesarios para construir los binarios de librealsense y los módulos de kernel afectados:

```
sudo apt-get install libssl-dev libusb-1.0-0-dev libudev-dev pkg-config libgtk-3-dev
```

Instala las herramientas de construcción:

```
sudo apt-get install git wget cmake build-essential
```

Prepara el Backend de Linux y el entorno de desarrollo. Desconecta cualquier camara RealSense y ejecuta:

```
sudo apt-get install libglfw3-dev libgl1-mesa-dev libglu1-mesa-dev at
```

### Instalación de librealsense

Clona el repositorio

```
git clone https://github.com/realsenseai/librealsense.git
```

Ejecuta el script de permisos Realsense del directorio raíz librealsense2:

```
cd librealsense
./scripts/setup_udev_rules.sh
```

Construye y aplica los modulos de kernel:

Ubuntu 20/22/24 (focal/jammy/noble) with LTS kernel 5.15, 5.19, 6.5, 6.8, 6.11, 6.14
```
./scripts/patch-realsense-ubuntu-lts-hwe.sh
```
Ubuntu 20 with LTS kernel (< 5.13)
```
./scripts/patch-realsense-ubuntu-lts.sh
```

### Construyendo el SDK librealsense2

Ejecuta:

```
cd librealsense
mkdir build && cd build
```

Ejecuta el paso de configuración de cmake. A continuación, se muestran algunos ejemplos:
La compilación predeterminada está configurada para generar los binarios de objetos compartidos principales y pruebas unitarias en modo de depuración. Usa -DCMAKE_BUILD_TYPE=Release para compilar con optimizaciones.

Normal:
```
cmake ../
```
Con demos y tutoriales:

```
cmake ../ -DBUILD_EXAMPLES=true
```
Para sistemas sin OpenGL o X11 construit solo ejemplos textuales:

```
cmake ../ -DBUILD_EXAMPLES=true -DBUILD_GRAPHICAL_EXAMPLES=false
```

Recompila e instala los binarios de librealsense2:

```
sudo make uninstall && make clean && make && sudo make install
```
