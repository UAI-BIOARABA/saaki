# 🧩 Guía de repositorios oficiales de Unitree Robotics (G1-edu)

Este documento es una breve guía sobre los diferentes **repositorios oficiales de Unitree Robotics**, centrada en los que se utilizan para el robot humanoide **Unitree G1-edu**. Explicaremos **para que sirven, donde se usarán y que relacion tienen entre ellos (los que la tengan)**, ya que al principio puede parecer confuso entender cuales nos interesan o necesitamos instalar.

---

## 🔗 Cuenta oficial de Unitree Robotics en GitHub

- [GitHub Unitree Robotics](https://github.com/unitreerobotics)

---

## 🧬 El Núcleo (Comunicación con el Robot)

Estos son los cimientos. Sin esto, no hablamos con el hardware.

- [unitree_sdk2](https://github.com/unitreerobotics/unitree_sdk2.git) y [unitree_sdk2_python](https://github.com/unitreerobotics/unitree_sdk2_python.git)

¿Qué es?: Es el "driver" oficial. Es una librería de bajo nivel (C++ y Python) que nos permite enviar comandos a los motores (torques, posiciones) y leer el estado del robot (IMU, encoder).

¿Para qué sirve?: Si queremos programar un movimiento a mano (ej. mover un brazo) o crear nuestro propio controlador desde cero sin usar ROS, usamos esto.

Relación con todos los demás repositorios (ROS, simuladores), usan este SDK por debajo para "traducir" sus órdenes al lenguaje del robot.

---

## 🤖 Integración con ROS (Robot Operating System)

Quien venga de la robótica clásica o académica, aquí pasará mucho tiempo.

- [unitree_ros2](https://github.com/unitreerobotics/unitree_ros2.git)

¿Qué es?: Es un envoltorio (wrapper) del SDK2 para ROS 2.

¿Para qué sirve?: Convierte los datos del robot en "tópicos" de ROS. Nos permite usar herramientas estándar de ROS como RViz (para visualizar), Nav2 (para navegar) o comunicar el G1 con otros nodos (cámaras, sensores externos).

Nota: Unitree apuesta fuerte por ROS 2 (no ROS 1) para el G1.

---

## 🧑‍💻 Simulación y Control (Física Pura)

Aquí es donde probamos nuestro código C++/Python antes de romper el robot real.

- [unitree_mujoco](https://github.com/unitreerobotics/unitree_mujoco.git)

¿Qué es?: Un entorno de simulación basado en MuJoCo. MuJoCo es un motor de física muy rápido y preciso, ideal para dinámica de cuerpos rígidos.

¿Para qué sirve?: Este es nuestro banco de pruebas. Si escribimos un script de control usando el unitree_sdk2, podemos ejecutarlo casi sin cambios en este simulador.

Ventaja: Es ligero y simula muy bien la física del contacto. Usar también para validar algoritmos de control (caminar, mantener equilibrio) antes de ir al robot real.

---

## 🧠 Inteligencia Artificial y Aprendizaje (Reinforcement Learning)

Aquí es donde la cosa se suele liar con los nombres (Isaac Gym, Isaac Sim, Isaac Lab). Unitree usa herramientas de NVIDIA para entrenar al robot para que "aprenda" a caminar o manipular objetos.

- [unitree_rl_gym](https://github.com/unitreerobotics/unitree_rl_gym.git) (Legacy/Antiguo)

Estado: Basado en el antiguo "Isaac Gym". Aunque funciona, se está quedando obsoleto frente a Isaac Lab.

Uso: Entrenamiento por refuerzo (RL) para caminar.

- [unitree_rl_lab](https://github.com/unitreerobotics/unitree_rl_lab.git) (El estándar actual)

¿Qué es?: Repositorio basado en Isaac Lab (el framework moderno de NVIDIA).

¿Para qué sirve?: Aquí entrenamos redes neuronales. Si queremos que el robot aprenda a caminar por terreno irregular solo, usamos esto.

Relación con Isaac Sim:

Isaac Sim es el simulador (la aplicación con gráficos bonitos basada en Omniverse).

Isaac Lab es la librería de programación que corre sobre Isaac Sim para facilitar el entrenamiento de robots.

unitree_rl_lab es la configuración específica de Unitree para usar esas herramientas con el G1.

- [unitree_IL_lerobot](https://github.com/unitreerobotics/unitree_IL_lerobot.git)

¿Qué es?: Basado en LeRobot (de Hugging Face).

¿Para qué sirve?: Imitation Learning (Aprendizaje por Imitación). Se usa principalmente si tenemos los brazos con manos diestras. Enseñamos al robot moviendo sus brazos (teleoperación) y él aprende a copiarnos.

---

## 🦾 Manipulación

- [unitree_sim_isaaclab](https://github.com/unitreerobotics/unitree_sim_isaaclab.git)

El Entorno de Simulación para Manipulación

¿Qué es?: Es la configuración específica del G1 para funcionar dentro de NVIDIA Isaac Lab, pero con un enfoque en tareas de manipulación.

Diferencia con unitree_rl_lab:

unitree_rl_lab suele estar configurado con algoritmos de Reinforcement Learning (PPO) para locomoción (aprender a caminar).

unitree_sim_isaaclab nos da el entorno listo para importar el G1 y probar tareas de brazos/manos, o para validar las políticas que hayamos entrenado con Imitation Learning.

Uso: Es nuestro "cuarto de juegos" virtual. Si queremos probar si el G1 puede coger una manzana virtual sin configurar todo el entorno de física desde cero, usamos este repo.

- [xr_teleoperate](https://github.com/unitreerobotics/xr_teleoperate.git)

Control con Realidad Virtual (La fuente de datos)

¿Qué es?: Este es crucial. Permite controlar el G1 (especialmente brazos y manos) usando gafas de Realidad Virtual/Mixta (como las Apple Vision Pro o potencialmente Oculus/Meta Quest).

¿Para qué sirve?:

Demostración: Para mover el robot en tiempo real y que te copie (efecto "Avatar").

Recolección de Datos (Data Collection): Esta es la clave. Para que una IA aprenda a cocinar, primero tú te pones las gafas, "cocinas" controlando al robot, y este repo graba todos esos movimientos.

Relación: Los datos que grabamos aquí son los que luego le damoss de comer a unitree_IL_lerobot.

- [unitree_IL_lerobot](https://github.com/unitreerobotics/unitree_IL_lerobot.git) (Recordatorio)
El Cerebro que imita

Ya lo mencionamos, pero ahora cobra más sentido. Este repo toma los datos grabados con xr_teleoperate y entrena una red neuronal para que el robot haga la tarea solo.

Flujo: xr_teleoperate (Grabar) -> unitree_IL_lerobot (Entrenar) -> unitree_sdk2 (Ejecutar en robot real).

- [kinect_teleoperate](https://github.com/unitreerobotics/kinect_teleoperate.git)
El "Legacy" para el H1

Aviso: Este repositorio se diseñó pensando en el Unitree H1 (el humanoide grande).

¿Cómo funciona?: Usa una cámara Kinect para ver tu esqueleto humano y "retargetear" (traducir) tus articulaciones a las del robot.

¿Sirve para el G1?: Técnicamente la lógica de retargeting sirve, pero los archivos de configuración (URDF, límites de articulaciones) estarán hechos para el H1. A menos que queramos portar el código y ajustar las matemáticas de los ángulos, no es recomendable empezar por aquí con un G1. Es mejor usar los métodos de XR o joysticks del SDK.
