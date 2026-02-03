# 🧩 Guía del ecosistema de desarrollo de software

Este documento es una explicación del **ecosistema de desarrollo de software personalizado para el Unitree G1 EDU**. Explicaremos **las áreas de desarrollo que hay y para que sirve cada una**, de esta forma sabremos por donde desarrollar según la capacidad que queramos dotarle.

Principalmente encontramos 6 áreas de desarrollo, unas dentro del apartado más clásico y otras más "modernas".

## ⚙️ Desarrollo Clásico

Control (para programar movimientos explícitos) y Orquestación de Aplicaciones.

### 1 - SDK Low Level (Ingeniería de Control)

- ¿Para qué sirve?: Para investigadores de control clásico o crear movimientos muy específicos. Quieres diseñar tu propio algoritmo matemático (MPC, Whole Body Control) para mover una pierna o mantener el equilibrio. Tienes control total de cada motor (torque, posición).
- Herramientas: Unitree SDK2.
- Flujo de trabajo: Programas en C++ o Python $\rightarrow$ Pruebas en unitree_mujoco $\rightarrow$ Pasas al Robot Real.

### 2 - SDK High Level (Modo Usuario)

- ¿Para qué sirve?: Para mandar al robot a un punto, pedirle que se agache, o leer el estado de la batería. No te preocupas de cómo mueve las piernas.
- Herramientas: Unitree SDK2 (Python/C++).
- Flujo de trabajo: Escribes el script $\rightarrow$ Lo ejecutas en el Robot Real.
- Simulación: Mala por el momento. Los simuladores físicos (MuJoCo) no tienen la lógica para entender comandos High Level.

### 3 - ROS 2 (Aplicaciones y Navegación)

- ¿Para qué sirve?: Para hacer al robot autónomo. Aquí integras cámaras, LiDAR, mapas y todo el software personalizado. Usas paquetes estándar como Nav2 (para ir del punto A al B esquivando cosas) o MoveIt (para mover los brazos y coger objetos).
- Herramientas: unitree_ros2, ROS 2 (Humble/Jazzy), Nav2, MoveIt 2.
- Flujo de trabajo: PC externo $\rightarrow$ ROS 2 $\rightarrow$ Robot Real.
- Simulación: Solo si usas Low Level, ya que ROS2 traduce al lenguaje del robot y se puede simular, High Level no.

## 🧠 Desarrollo Moderno

Inteligencia Artificial / Aprendizaje por Refuerzo. Aquí es donde brilla el G1. No programas "cómo" caminar, el robot lo "aprende".

### 4 - Reinforcement Learning (RL) - Locomoción

- ¿Para qué sirve?: Para que el robot aprenda a caminar por terrenos difíciles, recuperarse de empujones o hacer parkour. La red neuronal aprende por ensayo y error.
- Herramientas:
  - Entrenamiento: NVIDIA Isaac Lab (o Isaac Gym). Es vital porque simula miles de robots a la vez en la GPU para aprender rápido.
  - Despliegue: Unitree SDK2 (Low Level).
- Flujo de trabajo: Entrenas en Isaac Lab (PC potente) $\rightarrow$ Generas un archivo .pt (política neuronal) $\rightarrow$ Lo copias al Robot Real (o simulado) $\rightarrow$ Un script de Python lee la red y manda órdenes Low Level a los motores.

### 5 - Imitation Learning / Teleoperación (Manos y Tareas)

- ¿Para qué sirve?: Para enseñar al robot a manipular objetos (doblar ropa, cocinar, abrir puertas) copiando el movimiento humano.
- Herramientas: 
  - Hardware: Gafas VR (Apple Vision Pro, Oculus) o guantes de captura de movimiento.
  - Software: Repositorios o plataformas propias de Unitree para teleoperación, indicados en la guía de repositorios.
- Flujo de trabajo: Te pones las gafas y mueves tus manos (Teleoperación) $\rightarrow$ Grabas los datos $\rightarrow$ Entrenas una IA $\rightarrow$ La IA controla los brazos del Robot Real (vía SDK Low Level real o simulado).

### 6 - Vision Language Action y World Model Action

- ¿Para qué sirve?: Para enseñar al robot a comprender lenguaje humano, comprender su entorno y actuar en base a ambos tratando de generalizar. Por ejemplo, decirle con lenguaje humano que ordene la mesa y este sea capaz de hacerlo, es un paso más allá del RL e IL.
- Este es un apartado muy avanzado y aún experimental, existen los repositorios en GitHub pero es realmente difícil implementarlos en un humanoide completo, por lo que este apartado aún no lo trataremos.

---

Si queremos integrar u "orquestar" cualquier software independiente que desarrollemos con el robot y el resto de sus capacidades, lo haremos mediante ROS2, es la columna vertebral que nos permite sincronizar diferentes módulos e integrarlos en la realidad.

En resumen, el Unitree G1 EDU es una plataforma de investigación y desarrollo excepcional, destacando sobretodo en su capacidad de desarrollo más "moderno", el cual lo diferencia de la robótica tradicional.
