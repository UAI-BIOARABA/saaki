# 🧩 Instrucciones de desarrollo Low Level

Este documento describe **instrucciones sobre como desarrollar código a bajo nivel** en el robot humanoide Unitree G1.

---

## 🧠 Requisitos previos

Asegurate de tener instalados ambos SDKs segú la guía proporcionada en la [Guía SDK G1](/wiki/05_SDK_G1/)

Los SDK solo son compatibles con Ubuntu 20.04 y 22.04 (Recomendado).

Recomendamos también conocer el SDK que qureamos utilizar, dedicar un tiempo a entender como funciona, como se comunica (con DDS) y como está dividido.

## 🦯 Familiarización

El desarrollo de bajo nivel trata de enviar inormación directa a los motores (posición, fuerza, amortiguación...). Tenemos control total sobre el robot, ni siquiera su equilibrio está en funcionamiento desarrollando a bajo nivel, por lo que siempre debe estar colgado y en modo "Develop", generalmente manteniendo L2 + R2 con el mando.

Para comenzar a desarrollar nuestros programas, podemos clonar nuestro repositorio [saaki-core](https://github.com/UAI-BIOARABA/saaki-core), en nuestro HOME.

Podemos desarrollar en 2 lenguajes, cada uno requiere tener su SDK instalado:
- Python
- C++

Nos encontramos con una estructura de este estilo:

```
└── saaki-core/                 ← core (Python | C++)
    ├── .vscode                 # Configuración de VS Code (Si se usa)
    ├── build                   # Compilacion de programas en C++
    │
    ├── python/                 # Scripts de control y lógica (SDK Python)
    │   ├── high/    
    │   │   └── ___.py          
    │   └── low/
    │       └── ___.py
    │
    ├── cpp/                    # Código de alto rendimiento (C++)
    │   ├── high/
    │   │   ├── ___.cpp 
    │   │   ├── ...
    │   │   └── CMakeLists.txt
    │   │
    │   └── low/
    │       ├── ___.cpp 
    │       ├── ...
    │       └── CMakeLists.txt
    │
    ├── .gitignore              # Incluye lo que no queremos subir a github
    ├── requirements.txt        # Dependencias de Python del proyecto
    ├── CONTRIBUTING.md         # Como contribuir/colaborar
    └── README.md               # Información del repositorio

                    
unitree_mujoco/               }
unitree_sdk2/                 } # Repositorios oficiales Unitree (Usar forks de BioAraba)
unitree_sdk2_python/          }
```

Si has seguido las recomendaciones y ya conoces minimamente el SDK, es el momento de mirar los ejemplos incluidos en la carpeta Python o C++, están altamente documentados, por lo que será sencillo entender que hace cada parte del programa.

## 💻 Desarrollo

Desarrollar ahora es muy sencillo, crea tus programas basándote los ejemplos que has encontrado y sigue las instrucciones del archivo CONTRIBUTING.md que encontrarás en el repositorio.

1. Crea una rama
2. Crea el archivo .py o .cpp en la carpeta correspondiente
3. Desarrolla tu programa
4. Haz commit de tus cambios
5. Haz una PR

¡¡LISTO!!