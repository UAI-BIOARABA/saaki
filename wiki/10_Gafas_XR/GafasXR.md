# 🧩 Guía de instalación y configuración de Isaac Sim (Ubuntu)

Este documento describe **cómo configurar las gafas XR**, para poder teleoperar al robot tanto en simulación como en la realidad. Para ello, utilizaremos el repositorio [xr_teleoperate](https://github.com/unitreerobotics/xr_teleoperate) de Unitree.
---

## 🧠 Requisitos previos

Asegúrate de tener actualizado tu sistema e instala ```openssl```:

``` bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git openssl
```

---

## 💻 Instalación del repositorio xr_teleoperate

### 1. Instalar Conda (x86_64 o ARM aarch64)

Opción A (ya tienes Conda/Miniforge): salta a la sección 2
Opción B (ARM / Jetson, aarch64)

El wiki incluye este flujo con Miniconda en ARM (Ubuntu 22.04 en Jetson Orin NX).

```bash
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-aarch64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh
source ~/miniconda3/bin/activate
conda init --all
```

En x86_64 el instalador sería el de Linux-x86_64 (la lógica es igual).

### 2. Crear el entorno tv (Python 3.10, pinocchio, numpy)

El README y el Install Log x86 usan exactamente esto:
```bash
conda create -n tv python=3.10 pinocchio=3.1.0 numpy=1.26.4 -c conda-forge
conda activate tv
```

### 3. Clonar el repo y los submódulos

Según el README:

```bash
git clone https://github.com/unitreerobotics/xr_teleoperate.git
cd xr_teleoperate
git submodule update --init --depth 1
```

En el install log (x86) se ve que, al menos, trae submódulos como televuer y dex-retargeting.

### 4. Instalar los módulos del proyecto
#### 4.1. teleimager

El README indica instalarlo en editable y sin deps (para no pisar versiones):
```bash
cd teleop/teleimager
pip install -e . --no-deps
cd ../../
```

#### 4.2. televuer (XR streaming/control)

El README y el install log lo instalan con ```pip install -e .```.

```bash
cd teleop/televuer
pip install -e .
```

#### 4.3. Certificados SSL para XR (Pico/Quest o Apple Vision Pro)

Esto es clave: sin certificados, el dispositivo XR suele bloquear la conexión HTTPS/WebRTC. El README lo describe paso a paso.

Pico / Quest (rápido, pero ir rellenando lo que pida para el certificado):

```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout key.pem -out cert.pem
```

Apple Vision Pro (con Root CA + SANs):

```bash
openssl genrsa -out rootCA.key 2048
openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 365 -out rootCA.pem -subj "/CN=xr-teleoperate"
openssl genrsa -out key.pem 2048
openssl req -new -key key.pem -out server.csr -subj "/CN=localhost"
```

Crea ```server_ext.cnf``` (poniendo tus IPs reales; el README da un ejemplo):

```bash
subjectAltName = @alt_names
[alt_names]
DNS.1 = localhost
IP.1 = 192.168.123.164
IP.2 = 192.168.123.2
```

Y firma:

```bash
openssl x509 -req -in server.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial \
  -out cert.pem -days 365 -sha256 -extfile server_ext.cnf
```

En Apple Vision Pro hay que instalar el ```rootCA.pem``` (por AirDrop, etc.).

Abrir puerto firewall (televuer):

```bash
sudo ufw allow 8012
```

Dile a la app dónde están los certs (elige 1 método):

- Opción 1: copiar a config del usuario

```bash
mkdir -p ~/.config/xr_teleoperate/
cp cert.pem key.pem ~/.config/xr_teleoperate/
```

- Opción 2: variables de entorno

```bash
echo 'export XR_TELEOP_CERT="$HOME/xr_teleoperate/teleop/televuer/cert.pem"' >> ~/.bashrc
echo 'export XR_TELEOP_KEY="$HOME/xr_teleoperate/teleop/televuer/key.pem"' >> ~/.bashrc
source ~/.bashrc
```

### 5. Instalar dex-retargeting (retargeting mano/EEF)

En el install log x86 lo instalan así:

```bash
cd teleop/robot_control/dex-retargeting
pip install -e .
cd ../../../
```

### 6. Instalar dependencias Python del repo

El repo fija estos paquetes en requirements.txt.

```bash
pip install -r requirements.txt
```

### 7. Instalar unitree_sdk2_python (comunicación con el robot)

El README indica clonarlo e instalarlo en editable.

```bash
cd ~
git clone https://github.com/unitreerobotics/unitree_sdk2_python.git
cd unitree_sdk2_python
pip install -e .
```

Nota importante del README: para xr_teleoperate v1.1+ pide que unitree_sdk2_python esté en un commit igual o más nuevo que uno específico (lo enlazan en el README), no debería de haber problema ya que nuestros forks están actualizados (revisarlos regularmente).

### 8. “Checklist” de verificación rápida

Vuelve al repo y comprueba que todo importa sin explotar:

```bash
conda activate tv
python -c "import televuer, dex_retargeting; print('OK')"
```

---

## ⚠️ Errores frecuentes

### 1. Conflicto con NumPy 2.x

pip sube automáticamente NumPy a 2.x porque muchos paquetes solo especifican: ```numpy>=1.21```, pero xr_teleoperate NO es compatible con NumPy 2.x.

**Solución:**

- Eliminar NumPy instalado por pip (repetir hasta que diga que no está):

```bash
pip uninstall -y numpy
```

- Instalar versión correcta con conda:

```bash
conda install -y -c conda-forge numpy=1.26.4
```

- Verificar (debe mostrar 1.26.4) :

```bash
python -c "import numpy as np; print(np.__version__)"
```

### 2. ImportError: cannot import name 'Vuer' from 'vuer'

El paquete ```vuer==0.0.60``` instalado desde PyPI: No reexporta ```Vuer``` en ```vuer/__init__.py``` pero la clase sí existe en ```vuer.server```

**Solución:**

- Modificar el import en:

```bash
teleop/televuer/src/televuer/televuer.py
```

- Cambiar:

```bash
from vuer import Vuer
```

- Por:

```bash
from vuer.server import Vuer
```

- Después:

```bash
python -c "import televuer"
```

### 3. ImportError: cannot import name 'Flag' from 'params_proto'

Versión de ```params-proto``` incompatible: Algunas versiones nuevas cambian la API, ```vuer.server``` necesita ```Flag```

**Solución:**

Instalar versión compatible:

```bash
pip uninstall -y params-proto
pip install params-proto==2.13.2
```

Verificar (si importa correctamente, soulcionado):

```bash
python -c "from params_proto import Flag"
```

### 4. Otros errores

Este repositorio contiene muchas dependencias y puede provocar infinidad de conflictos, por lo que, aunque no sea el consejo más profesional, recomendamos encarecidamente tener un LLM a mano para preguntar por los diferentes errores, ya que en la documentación oficial no aparece como solucionarlos.