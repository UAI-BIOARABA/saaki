# 🧩 Guía de instalación y configuración de Isaac Sim (Ubuntu)

Este documento describe **cómo instalar Isaac Sim**, **configurar todas las dependencias necesarias** y **garantizar que el entorno de desarrollo sea completamente reproducible** en otro equipo.

---

## 🧠 Requisitos previos

1. Asegúrate de tener actualizado tu sistema:

``` bash
sudo apt update && sudo apt upgrade -y
```
2. GPU NVIDIA + drivers funcionando. Si ```nvidia-smi``` no funciona, instala/actualiza el driver desde “Software & Updates → Additional Drivers” (o el método que uses normalmente). Comprueba con:

```bash
nvidia-smi
```

3. Conda instalado. El script usa ```conda create``` y ```conda activate```, así que necesitas Miniconda/Miniforge/Anaconda ya instalado y disponible en tu ```PATH```.

4. Paquetes base útiles:

```bash
# (El script también instala varios de estos, pero es buena idea tenerlos.)
sudo apt update
sudo apt install -y git git-lfs openssl cmake build-essential unzip
```

---

## 💻 Instalación de Isaac Sim

### 1. Clona el repo con submódulos

El script comprueba que exista la carpeta ```teleimager```, que viene como submódulo, así que clona así:

```bash
cd ~
git clone --recurse-submodules https://github.com/unitreerobotics/unitree_sim_isaaclab.git
cd unitree_sim_isaaclab
```

Si ya lo clonaste sin submódulos:

```bash
cd ~/unitree_sim_isaaclab
git submodule update --init --depth 1
```

### 2. Ejecuta el auto-setup para Isaac Sim 5.1

Desde la raíz del repo:

```bash
chmod +x auto_setup_env.sh
bash auto_setup_env.sh 5.1 unitree_sim_env cu126
```

Qué hace exactamente este script (importante para entender y depurar):

- Instala deps Ubuntu (```cmake```, ```build-essential```, ```openssl```, ```git-lfs```, ```unzip```, …).

- Clona repos “hermanos” en el directorio padre (```../IsaacLab```, ```../cyclonedds```, ```../unitree_sdk2_python```).

- Inicializa submódulos del repo (teleimager).

- Genera certificados ```key.pem/cert.pem``` y los copia a ```~/.config/xr_teleoperate/``` (te pedirá pulsar Enter varias veces en la creación del cert).

- Descarga assets llamando a ```fetch_assets.sh``` (usa **git-lfs** y clona desde HuggingFace un zip grande, lo descomprime y deja ```assets/``` en el repo).

- Compila ```CycloneDDS``` y define ```CYCLONEDDS_HOME```.

- Crea un entorno conda con Python 3.11 (requisito para Isaac Sim 5.x) e instala:

    - PyTorch (por defecto para 5.1 usa ```cu126```)

    - Isaac Sim 5.1.0 vía pip: ```isaacsim[all,extscache]==5.1.0```

    - Isaac Lab (```./isaaclab.sh --install```)

    - ```unitree_sdk2_python``` editable

    - requirements del repo (pyzmq, onnxruntime, etc.) y ```teleimager``` editable

    - Aplica un “patch” instalando ```libstdcxx-ng``` desde conda-forge (para 5.0/5.1)

*Nota: El propio Isaac Lab deja claro que Isaac Sim 5.x requiere Python 3.11.*

### 3. Asegura variables de entorno (recomendado)

El script hace export CYCLONEDDS_HOME=... pero eso vive solo en esa terminal. Para que siempre esté al activar el entorno:

```bash
conda activate unitree_sim_env
mkdir -p "$CONDA_PREFIX/etc/conda/activate.d"
cat > "$CONDA_PREFIX/etc/conda/activate.d/unitree_dds.sh" << 'EOF'
export CYCLONEDDS_HOME="$HOME/cyclonedds/install"
EOF
```

*(Ajusta la ruta si tu ```cyclonedds``` quedó en otro sitio; el script lo clona en el padre del repo: ```~/cyclonedds``` si estabas en ```~/unitree_sim_isaaclab```.)*

### 4. Ejecuta una demo del simulador

Activa el entorno y lanza una tarea:

```bash
conda activate unitree_sim_env
cd ~/unitree_sim_isaaclab

python sim_main.py \
  --device cpu \
  --enable_cameras \
  --task Isaac-PickPlace-Cylinder-G129-Dex1-Joint \
  --enable_dex1_dds \
  --robot_type g129
```

Parámetros útiles (tal cual README):

```--task```: nombre de tarea (hay varias: pick/place, stack, move wholebody…)

```--enable_dex1_dds``` / ```--enable_dex3_dds```: DDS para gripper o mano Dex3

```--robot_type```: ```g129``` (G1 29DoF), o H1-2 (27DoF) según tarea

```--no_render```: sin ventana, con streaming WebRTC (más típico en Docker/remoto)

*Tip visual: cuando arranca la escena, para ver la vista principal en Isaac Sim, el README indica seleccionar:*
```PerspectiveCamera -> Cameras -> PerspectiveCamera.```