# 🧩 Vision Language Action y World Model Action

Este documento describe **que son los VLA y WMA** y para que sirven en el robot humanoide Unitree G1.

Al ser tecnologías tan avanzadas, aún no trabajaremos con ellas, por lo que no habrá ejemplos prácticos ni repositorios propios de Bioaraba sobre los que trabajar.

---

## 🧠 ¿Que son?

### 1. Vision-Language-Action (VLA)
Los modelos VLA (como el famoso RT-2 de Google DeepMind) son una evolución de los modelos de lenguaje (LLM) que usamos para chatear. Su arquitectura une tres capacidades en una sola red neuronal:

- Vision (Visión): El modelo "ve" una cámara o imagen.
- Language (Lenguaje): Entiende instrucciones en lenguaje natural (ej. "Limpia el café derramado").
- Action (Acción): En lugar de responder con texto, el modelo genera tokens de control (movimientos de un brazo robótico, giros, etc.).

La clave del VLA: Trata las acciones físicas como si fueran palabras. Si un LLM predice la siguiente palabra en una frase, un VLA predice el siguiente movimiento de un motor basado en lo que ve y lo que se le pidió.

### 2. World Model Action (WMA)
Los World Models (Modelos de Mundo) son un enfoque distinto y, para muchos, más profundo. En lugar de solo reaccionar a una instrucción, el sistema construye una simulación interna de la realidad.

Un World Model intenta responder a la pregunta: "Si yo hago X, ¿qué pasará después en el entorno?".

- Predicción: El modelo puede imaginar futuros posibles. Si un robot va a empujar un vaso, el modelo de mundo "predice" visualmente que el vaso se caerá.
- Simulación: Permite al agente "entrenar" en su mente antes de tocar un objeto real, reduciendo errores y riesgos.
- Action: La acción se toma basándose en cuál de esos "futuros imaginados" es el mejor para cumplir el objetivo.

## ¿Por qué son importantes?

Hasta hace poco, si querías que un robot hiciera algo nuevo, tenías que programarlo desde cero. Con VLA, el robot puede entender "pásame el dinosaurio de juguete" aunque nunca haya visto ese juguete específico, gracias a que "sabe" lo que es un dinosaurio por su entrenamiento con internet. Con los World Models, el robot adquiere "sentido común" físico, evitando movimientos peligrosos porque puede prever el desastre antes de que ocurra.

## ¿Como los integramos en el Unitree G1?

Unitree nos proporciona 2 repositorios para integrar estos modelos en neustro robot. Estos repositorios incluyen algunos datasets de los brazos Z1 (que no tenemos) y del G1 para poder utilizar, el inconveniente es que utilizan grippers (que no tenemos) en lugar de manos (1 grado de libertad en vez de 7), lo cual facilita el entrenamiento y nos hace intuir que con manos es muy difícil de lograr.

### VLA

Nos ofrecen el siguiente repositorio basado en UnifoLM:

[unifolm-vla](https://github.com/unitreerobotics/unifolm-vla)

### WMA

Nos ofrecen el siguiente repositorio basado en UnofLM:

[unifolm-world-model-action](https://github.com/unitreerobotics/unifolm-world-model-action)