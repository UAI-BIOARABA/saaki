# 🧩 Instrucciones de desarrollo Low Level

Este documento describe **instrucciones sobre como desarrollar código a alto nivel** en el robot humanoide Unitree G1. El documento es práctimante igual al de Low Level, ya que son desarrollos que van "de la mano".

---

## 🧠 Requisitos previos

Asegurate de tener instalados ambos SDKs segú la guía proporcionada en la [Guía SDK G1](/wiki/05_SDK_G1/)

Los SDK solo son compatibles con Ubuntu 20.04 y 22.04 (Recomendado).

Al igual que a bajo nivel, recomendamos también conocer el SDK que qureamos utilizar, dedicar un tiempo a entender como funciona, como se comunica (con DDS) y como está dividido. Si has seguido la guía, esto no será un problema, solo repasa las APIs de alto nivel, suelen llamarse LOCO... o SPORTS..., depende de la versión y sobretodo, de como les han querido llamar los desarrolladores en cada robot.

## 🦯 Familiarización

El desarrollo de alto nivel trata de enviar comandos de movimiento o acciones al robot (anda a 5 m/s, saluda, reproduce este audio...). A diferencia deldesarrollo a bajo nivel, aquí no tenemos control total sobre el robot, su equilibrio está siempre en funcionamiento y estamos limitados a las API que nos proporciona el fabricante (o nosotros desarrollamos).

Cabe destacar, que esta forma de desarrollo es muchísimo más sencilla, la más sencilla de todas.

Si ya has conocido el desarrollo a bajo nivel, ya tendrás clonado nuestro repositorio [saaki-core](https://github.com/UAI-BIOARABA/saaki-core) HOME.

Aquí, también podemos desarrollar en 2 lenguajes, cada uno requiere tener su SDK instalado:
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

Si has seguido las recomendaciones y ya conoces minimamente el SDK, es el momento de mirar los ejemplos incluidos en la carpeta high de Python o C++, están altamente documentados, por lo que será sencillo entender que hace cada parte del programa.

## 💻 Desarrollo

Desarrollar ahora es muy sencillo, crea tus programas basándote los ejemplos que has encontrado y sigue las instrucciones del archivo CONTRIBUTING.md que encontrarás en el repositorio.

1. Crea una rama
2. Crea el archivo .py o .cpp en la carpeta correspondiente
3. Desarrolla tu programa
4. Haz commit de tus cambios
5. Haz una PR

¡¡LISTO!!