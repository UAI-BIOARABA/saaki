# 🧩 Guía de instalación y configuración del SDK de la cámara

Este documento describe **cómo instalar el SDK oficial**, **configurar todas las dependencias necesarias** y **garantizar que no existan conflictos** de instalación o funcionamiento en ningún equipo.

El unitree G1 tiene una cámara Intel Realsense D435i, la cual tiene su SDK propio.
- [SDK librealsense](https://github.com/realsenseai/librealsense.git)

El SDK tiene su propia guía de instalación para las diferentes plataformas (Windows, Ubuntu, Jetson...), pero puede ser un poco confusa, por lo que trataremos de complementarla aquí.

Para la instalación enn nuestro PC sguiremos la guía de instalación en Ubuntu:
- [Oficial](https://github.com/realsenseai/librealsense/blob/master/doc/installation.md)

Para instalarlo en el robot, la de Jetson.
- [Oficial](https://github.com/realsenseai/librealsense/blob/master/doc/distribution_linux.md#installing-the-packages)

---

## 🧠 Requisitos previos

Asegurate de tener un PC con Ubuntu y el Unitree G1 cerca.

Asumimos el uso de Ubuntu 20.04 o 22.04 (Recomendado) en el PC.

Asegúrate de tener actualizados ambos sistemas:

```bash
sudo apt update && sudo apt upgrade -y
```

## 💻 Instalación en nuestro PC

Esto sirve para poder usar el SDK en nuestro PC y procesar datos de la cámara, lo que implica conectar la cámara directamente a nuestro PC en lugar de al robot.

No es lo ideal pero puede servir para depurar y realizar pruebas, sobretodo si disponemos de una cámara extra que no esté fija en el robot.

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

Ya puedes lanzar realsense-viewer en la terminal.

## 🤖 Instalación en el G1

Esto sirve para procesar los datos direcamente en el robot.

Después, podemos enviar esos datos a nuestro PC o tratarlos en el propio robot.

### Instalando los paquetes

Registra la clave pública de los paquetes:
```
# Asegúrate de que el directorio existe
sudo mkdir -p /etc/apt/keyrings

# Descarga y desarma
curl -sSf https://librealsense.realsenseai.com/Debian/librealsenseai.asc | \
gpg --dearmor | sudo tee /etc/apt/keyrings/librealsenseai.gpg > /dev/null
```

Nota: El conjunto de claves contiene tanto la nueva clave pública de RS como la clave pública de Intel para los repositorios antiguos, lo que garantiza la compatibilidad con paquetes nuevos y existentes.

Asegúrate de que la compatibilidad con HTTPS de apt esté instalada: 

```
sudo apt-get install apt-transport-https
```

Agrega el servidor a la lista de repositorios

```
echo "deb [signed-by=/etc/apt/keyrings/librealsenseai.gpg] https://librealsense.realsenseai.com/Debian/apt-repo `lsb_release -cs` main" | \
sudo tee /etc/apt/sources.list.d/librealsense.list
sudo apt-get update
```

Instala las librerías:

```
sudo apt-get install librealsense2-dkms
sudo apt-get install librealsense2-utils
sudo apt-get install librealsense2-dev
sudo apt-get install librealsense2-dbg
```

Ya puedes lanzar realsense-viewer en la terminal, pero si no tienes un monitor conectado al G1 (que es lo normal) no verás nada.

## 🐍 Paquetes de Python

Para poder procesar los datos con el SDK de Python, debes instalar lo siguiente:

```
pip install pyrealsense2 # Stable
pip install pyrealsense2-beta # Beta
```

Si ahora creas un script en el G1 que contenga lo siguiente, podrás enviar los datos de la cámara directamente a tu navegador:

```
# EJECUTAR EN EL ROBOT (Unitree G1)
from flask import Flask, Response
import pyrealsense2 as rs
import cv2
import numpy as np

app = Flask(__name__)

# 1. Configurar la cámara RealSense
pipeline = rs.pipeline()
config = rs.config()
# Bajamos resolución a 640x480 para asegurar fluidez por Wi-Fi
config.enable_stream(rs.stream.color, 640, 480, rs.format.bgr8, 30)

print("Iniciando cámara...")
pipeline.start(config)

def generar_frames():
    """Esta función captura frames continuamente"""
    try:
        while True:
            # Capturar datos
            frames = pipeline.wait_for_frames()
            color_frame = frames.get_color_frame()
            if not color_frame:
                continue

            # Convertir a imagen normal
            frame = np.asanyarray(color_frame.get_data())

            # Codificar a JPEG (Comprimido para viajar rápido por red)
            ret, buffer = cv2.imencode('.jpg', frame, [cv2.IMWRITE_JPEG_QUALITY, 80])
            frame_bytes = buffer.tobytes()

            # Crear estructura MJPEG (El estándar de video web)
            yield (b'--frame\r\n'
                   b'Content-Type: image/jpeg\r\n\r\n' + frame_bytes + b'\r\n')
    except Exception as e:
        print(f"Error: {e}")
    finally:
        # Nota: En un servidor real esto rara vez se alcanza, 
        # pero es bueno tenerlo en mente.
        pass

@app.route('/video_feed')
def video_feed():
    """Ruta web donde se verá el video"""
    return Response(generar_frames(),
                    mimetype='multipart/x-mixed-replace; boundary=frame')

if __name__ == '__main__':
    # host='0.0.0.0' hace que sea visible desde otros PCs en la red
    # port=5000 es el puerto estándar
    try:
        app.run(host='0.0.0.0', port=5000, threaded=True)
    finally:
        pipeline.stop()
```

Ahora en tu PC, podrás verlos directamente en tu navegador.