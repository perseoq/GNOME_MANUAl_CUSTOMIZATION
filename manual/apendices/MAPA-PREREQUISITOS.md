# Mapa de Prerrequisitos

Este mapa indica qué necesitas saber antes de cada capítulo. Si llegas a un capítulo sin el requisito, vuelve al capítulo indicado.

| Capítulo | Necesitas antes | Qué aporta |
| :--- | :--- | :--- |
| **Introductorio** | Nada | Terminal, archivos, editor, rutas, JavaScript y CSS básicos |
| **1. Arquitectura** | Introductorio | GJS, ESM, estructura de la extensión, `enable()` / `disable()` |
| **2. Módulos** | Capítulo 1 | `gi://`, `resource://`, `Main.notify()` |
| **3. Interfaz** | Capítulos 1 y 2 | St, Clutter, panel, iconos, contenedores, animaciones |
| **4. CSS** | Capítulo 3 | `stylesheet.css`, selectores, propiedades, estados |
| **5. Preferencias** | Capítulos 1 a 4 | GSettings, esquema, `prefs.js`, `settings.bind()` |
| **6. Sistema** | Capítulos 1 a 5 | `global`, Meta, ventanas, espacios, Wayland |
| **7. Monkey patching** | Capítulos 1 a 6 | Parches reversibles, Looking Glass, logs |
| **8. C y GI** (opcional) | Capítulos 1 a 7 | GObject, GIR, typelib, límites del código nativo |
| **9. APIs** | Capítulos 1 a 7 | Soup 3, JSON, proveedores, temporizadores |
| **10. Empaquetado** | Capítulos 1 a 9 | Metadata, ZIP, EGO, pruebas finales |
| **Apéndices** | El capítulo que estés leyendo | Glosario, mapa y soluciones |

## Ruta mínima sin C

Si decides no escribir C, puedes seguir esta ruta:

```text
Introductorio → 1 → 2 → 3 → 4 → 5 → 6 → 7 → 9 → 10
```

El Capítulo 8 es opcional y no es requisito para el proyecto.

## Ruta de recuperación

Si te atascas:

1. Vuelve al capítulo anterior y repite su verificación.
2. Revisa el glosario.
3. Compara tu código con la solución del capítulo.
4. Lee el log con `journalctl /usr/bin/gnome-shell -f -o cat`.
5. Si el problema es de entorno, revisa `ENTORNO-PRUEBAS.md`.

## Dependencias del proyecto

| Etapa | Capítulo | Requisito |
| :--- | :--- | :--- |
| 0 | Introductorio | Terminal y archivos |
| 1 | 1 | Extensión que escribe en el log |
| 2 | 2 | Notificación |
| 3 | 3 | Botón en el panel |
| 4 | 4 | CSS propio |
| 5 | 5 | Preferencias persistentes |
| 6 | 6 | Mover ventana |
| 7 | 7 | Monkey patch reversible |
| 8 | 8 | Librería C opcional |
| 9 | 9 | Precio en el panel |
| 10 | 10 | ZIP instalable |
