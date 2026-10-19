# Perfil del Lector

**Documento interno de producción.** No forma parte del manual que lee el alumno.
**Estado:** Fase 1 implementada; pendiente de revisión del autor.
**Audiencia:** personas que nunca han programado y que usan GNOME Shell sin conocer su funcionamiento interno.

---

## 1. Propósito

Este documento define qué sabe y qué no sabe el lector antes de empezar. Su función es evitar que el manual dé por supuestos conocimientos que el lector no tiene.

Cada vacío detectado aquí debe cubrirse en el Capítulo Introductorio o en el capítulo donde se use por primera vez el concepto.

---

## 2. Supuestos de partida

| Pregunta | Respuesta esperada | Consecuencia pedagógica | Dónde se cubre |
| :--- | :--- | :--- | :--- |
| ¿Sabe qué es un archivo? | No necesariamente | Explicar archivo, carpeta y ruta con una analogía cotidiana | Intro 0.2 |
| ¿Sabe abrir una terminal? | Probablemente no | Enseñar a abrirla y explicar cada comando antes de usarlo | Intro 0.4 |
| ¿Sabe qué es JavaScript? | No | Explicar que es el lenguaje que decide qué hacer | Intro 0.5 |
| ¿Sabe qué es CSS? | No | Explicar que es el lenguaje que decide cómo se ve | Intro 0.5 |
| ¿Sabe qué es GNOME Shell? | Lo usa, pero no lo conoce | Explicar panel, actividades, reloj y escritorio | Intro 0.1 |
| ¿Sabe qué es una extensión? | Quizá ha instalado una, pero no sabe qué es | Explicar que añade una función al shell | Intro 0.1 |
| ¿Sabe qué es un editor de texto? | Quizá usa LibreOffice, no un editor de código | Enseñar a crear y guardar un archivo de texto | Intro 0.3 |
| ¿Sabe qué es una ruta de archivo? | No con seguridad | Explicar ruta absoluta, ruta relativa y carpeta personal | Intro 0.2 |
| ¿Sabe qué es un log? | No | Explicar qué es y cómo se lee en `journalctl` | Capítulo 1 |
| ¿Sabe qué es una API? | No | Explicar el concepto antes de usarlo | Capítulo 9 |
| ¿Sabe qué es Git? | No | No se le exige para seguir el manual | No aplica |
| ¿Sabe inglés técnico? | No se asume | Definir cada término en español en su primer uso | Todo el manual |
| ¿Tiene permisos de administrador? | No se asume | Evitar `sudo` en los primeros capítulos | Todo el manual |
| ¿Tiene conexión a Internet? | No se asume para los primeros capítulos | Depender de Internet únicamente en el Capítulo 9 | Capítulo 9 |
| ¿Usa Wayland o X11? | No lo sabe | Explicar Wayland cuando sea relevante y no exigir que lo sepa antes | Capítulos 4 y 7 |

---

## 3. Perfil técnico mínimo

El lector debe poder completar el manual con este entorno:

- Un equipo con Linux y GNOME Shell 50.6.
- Sesión de Wayland.
- Una cuenta de usuario normal, con carpeta personal.
- Acceso a un editor de texto y a una terminal.
- Un navegador para descargar documentación opcional.
- Conexión a Internet para el Capítulo 9; los capítulos anteriores no dependen de ella.

No se exige:

- Saber programar.
- Saber usar la terminal.
- Conocer Linux a fondo.
- Tener permisos de administrador.
- Tener Git instalado.
- Conocer la API interna de GNOME.

---

## 4. Vacíos que el manual debe cubrir antes de usarlos

| Concepto | Se introduce en | Se usa por primera vez en | Verificación |
| :--- | :--- | :--- | :--- |
| GNOME Shell y extensión | Intro 0.1 | Intro 0.1 | El lector explica qué es una extensión con sus palabras |
| Archivo, carpeta y ruta | Intro 0.2 | Intro 0.3 | El lector guarda un archivo en una ruta concreta |
| Editor de texto | Intro 0.3 | Intro 0.3 | El lector crea `hola.txt` y lo guarda |
| Terminal y comandos | Intro 0.4 | Capítulo 1 | El lector ejecuta `pwd`, `ls` y `cd` sin ayuda |
| JavaScript y CSS | Intro 0.5 | Capítulo 1 | El lector distingue qué decide la lógica y qué decide la apariencia |
| Log y `journalctl` | Capítulo 1 | Capítulo 1 | El lector ve el mensaje de su extensión en el log |
| ESM e `import` | Capítulo 1 | Capítulo 1 | El lector escribe un `import` válido |
| St y Clutter | Capítulo 3 | Capítulo 3 | El lector crea un botón visible en el panel |
| CSS en St | Capítulo 4 | Capítulo 4 | El lector cambia el color de su botón |
| GSettings | Capítulo 5 | Capítulo 5 | El lector guarda y recupera una preferencia |
| Meta y Wayland | Capítulo 6 | Capítulo 6 | El lector mueve una ventana de espacio de trabajo |
| Monkey patching | Capítulo 7 | Capítulo 7 | El lector aplica y restaura un parche |
| API HTTP y JSON | Capítulo 9 | Capítulo 9 | El lector muestra un dato externo en el panel |

---

## 5. Criterio de salida del Capítulo Introductorio

El lector debe poder:

1. Abrir una terminal.
2. Crear un archivo de texto.
3. Guardarlo en una ruta concreta.
4. Explicar con sus palabras qué es una extensión de GNOME Shell.

Si falla cualquiera de los cuatro puntos, el Capítulo Introductorio no está terminado.

---

## 6. Cómo validar este perfil

La validación se hace con personas reales, no con suposiciones del autor:

1. Se pide a una persona sin experiencia en programación que lea el Capítulo Introductorio.
2. Se observa dónde se atasca y se anota la frase exacta.
3. Se corrige el capítulo añadiendo una analogía, un paso intermedio o un ejercicio.
4. Se repite la prueba hasta que complete el criterio de salida sin ayuda externa.
5. Se conserva el registro como documentación interna.

El perfil se actualiza si una prueba revela un vacío que no estaba contemplado.

---

## 7. Supuestos prohibidos

El manual no puede asumir que el lector:

- Sabe abrir una terminal.
- Sabe qué es una ruta.
- Sabe programar.
- Sabe qué es un log.
- Sabe qué es una API.
- Tiene permisos de administrador.
- Tiene conexión a Internet en los primeros capítulos.
- Sabe inglés técnico.
- Puede leer código sin explicaciones.

Toda aparición de uno de estos supuestos en un capítulo es un defecto de producción.

---

## 8. Historial

| Fecha | Cambio | Autor |
| :--- | :--- | :--- |
| 2026-10-19 | Perfil inicial para la fase 1 | Equipo del manual |
