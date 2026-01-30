# 🗒️ Resumen de uso de el programa de audios de Saaki

Este documento es una breve descripción sobre **el programa de generación de audios de Saaki**.


## ❗ Problema

El Unitree G1 está conectado a un LLM se GPT en la nube y dispone de un motor TTS interno para poder reproducir mediante voz las respuestas del modelo de lenguaje.

Esto parece una noticia estupenda, pero el problema es que el TTS solo está disponible en **CHINO** e **INGLÉS**, por lo que podemos decir que no habla nuestro idioma.

Además de el idioma, la nube nos supone un problema. En osakidetza no podemos tratar datos personales ni voces de los pacientes en la nube.

## ♻️ Solución

Para poder reproducir sonido en castellano y euskera, habría 2 opciones:
- Instalar un LLM local y TTS en castellano y euskera   $\rightarrow$ Complicado, consume muchísimos recursos, podría ser peligroso para los pacientes
- Generar nosotros los audios que queremos que diga     $\rightarrow$ Menos natural, 100% seguro y fiable

Unitree nos da la opción de cargar audios en el robot desde la app (límite de 10) y desde la programación High-Level(sin límite), esto nos permite reproducirlos cuando queramos.

Para ello hemos desarrollado un programa que nos permite generar audios de voz para Saaki en **CASTELLANO** y **EUSKERA**, permitiendonos ajustar el tono de voz a nuestro gusto para no parecer intimidante. El único inconveniente es que solo se puede usar en Windows, ya que las voces que necesitamos instalar solo se pueden usar en dicho Sistema Operativo.

---

Para acceder al repositorio y a la guía completa:
- [🔗 saaki-audios - Github](https://github.com/UAI-BIOARABA/saaki-audios)