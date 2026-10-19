# Entorno de Pruebas

**Documento interno de producción.**
**Estado:** Fase 1 implementada. Entorno verificado en la máquina de referencia.

---

## 1. Objetivo

Definir un entorno reproducible para probar cada capítulo, capturar logs reales y verificar que los ejemplos funcionan en GNOME Shell 50.6 sobre Wayland.

Toda prueba del manual se ejecuta en este entorno o en uno equivalente documentado.

---

## 2. Entorno de referencia verificado

| Componente | Versión o valor | Comprobación |
| :--- | :--- | :--- |
| Distribución | Fedora Linux 44 Workstation | `cat /etc/os-release` |
| GNOME Shell | 50.6 | `gnome-shell --version` |
| GJS | 1.88.1 | `gjs --version` |
| GLib | 2.88.4 | `glib-compile-schemas --version` |
| Git | 2.56.0 | `git --version` |
| Sesión | Wayland | `echo "$XDG_SESSION_TYPE"` |
| Escritorio | GNOME | `echo "$XDG_CURRENT_DESKTOP"` |
| Herramientas de extensión | `gnome-extensions` 50.6 | `gnome-extensions --version` |

La máquina de referencia tiene un tema visual personalizado. Para las pruebas se debe usar una cuenta limpia o restaurar el tema Adwaita, porque las capturas y los estilos deben coincidir con una instalación estándar.

---

## 3. Preparación de una sesión de prueba

1. Crear una cuenta de usuario nueva para las pruebas, por ejemplo `manual-test`.
2. Iniciar sesión en Wayland con el tema Adwaita y el escalado al 100 %.
3. Desactivar todas las extensiones de terceros.
4. Comprobar que no hay extensiones activas:

```bash
gnome-extensions list --enabled
```

`gnome-extensions list --enabled` muestra las extensiones activas. Si aparece alguna extensión que no sea del sistema, se desactiva antes de continuar.

5. Crear una instantánea de la máquina virtual, si se usa una.
6. Verificar las versiones con los comandos de la sección 2.
7. Anotar la fecha, el capítulo y el commit que se va a probar.

---

## 4. Instalación de una extensión de prueba

La extensión se instala manualmente en la carpeta de usuario:

```text
~/.local/share/gnome-shell/extensions/UUID/
```

El lector crea esa carpeta y escribe dentro los archivos del capítulo. Para las pruebas internas se usa la misma ruta.

Para activar y desactivar una extensión:

```bash
gnome-extensions enable UUID
gnome-extensions disable UUID
```

`gnome-extensions enable UUID` activa la extensión cuyo identificador es `UUID`. `gnome-extensions disable UUID` la desactiva. El `UUID` debe coincidir exactamente con el campo `uuid` de `metadata.json`.

En Wayland, los cambios de CSS y de código del shell pueden requerir cerrar sesión y volver a entrar. Después de cada cambio se comprueba si `disable` y `enable` bastan; si no, se registra que hace falta cerrar sesión.

---

## 5. Captura de logs

Para ver los mensajes del shell en tiempo real:

```bash
journalctl /usr/bin/gnome-shell -f -o cat
```

`journalctl` consulta el registro del sistema. La ruta `/usr/bin/gnome-shell` limita la salida a los mensajes de GNOME Shell. `-f` mantiene la salida abierta y muestra los mensajes nuevos. `-o cat` muestra el texto sin metadatos adicionales.

Para guardar una captura de una prueba:

```bash
journalctl /usr/bin/gnome-shell -n 200 -o cat > /tmp/gnome-shell-prueba.log
```

`-n 200` muestra los últimos 200 mensajes. El archivo `/tmp/gnome-shell-prueba.log` es temporal y no se publica; el fragmento relevante se transcribe al manual.

---

## 6. Looking Glass

Looking Glass es la consola de depuración de GNOME Shell. Se abre con `Alt+F2`, se escribe `lg` y se pulsa Enter.

Sirve para:

- Inspeccionar el árbol de actores.
- Ejecutar código JavaScript en vivo.
- Revisar errores de la extensión.

Las capturas y los mensajes de Looking Glass se transcriben en el capítulo cuando se usen como evidencia.

---

## 7. Registro de cada prueba

Cada prueba se registra con esta ficha:

| Campo | Contenido |
| :--- | :--- |
| Fecha | Día de la prueba |
| Capítulo | Número y título |
| Commit | Hash del commit probado |
| Entorno | Distribución, GNOME Shell, GJS y sesión |
| Pasos | Secuencia exacta que siguió el lector |
| Resultado esperado | Qué debía ocurrir |
| Resultado obtenido | Qué ocurrió en realidad |
| Log | Texto real de `journalctl` o Looking Glass |
| Captura | Archivo o descripción de la imagen |
| Atasco | Frase exacta y punto donde se detuvo |
| Corrección | Cambio aplicado al manual |

El registro se conserva como documentación interna. No se publica dentro del manual.

---

## 8. Matriz mínima de pruebas

| Prueba | Condición | Criterio |
| :--- | :--- | :--- |
| Instalación limpia | Cuenta nueva, sin extensiones de terceros | La extensión se activa sin errores |
| Recarga | `disable` y `enable` | No quedan señales, actores ni temporizadores |
| Cierre de sesión | Logout y login en Wayland | La extensión y las preferencias siguen funcionando |
| Tema | Adwaita, escalado 100 % | Las capturas coinciden con el manual |
| Escalado | 200 % | La interfaz no se rompe |
| Log | `journalctl` | No aparecen errores ni advertencias de la extensión |
| Preferencias | Cambio y reinicio | El valor persiste |
| Red | Capítulo 9 | Los errores de red se muestran sin bloquear el shell |

---

## 9. Seguridad de las pruebas

- No se usan claves de API reales en capturas ni en commits.
- Las pruebas de red usan claves de prueba o APIs sin clave.
- No se publican rutas absolutas de la máquina del autor.
- Los logs se revisan antes de transcribirlos para eliminar datos personales.
- Los archivos temporales se guardan en `/tmp` y no se suben al repositorio.

---

## 10. Limitaciones

- X11 queda fuera del alcance del manual.
- GNOME Shell 51 y versiones posteriores quedan fuera del alcance de esta edición.
- No se admiten extensiones de terceros como dependencias.
- Las diferencias entre distribuciones se documentan cuando afecten a un comando.

---

## 11. Historial

| Fecha | Cambio | Autor |
| :--- | :--- | :--- |
| 2026-10-19 | Entorno y protocolo iniciales de la fase 1 | Equipo del manual |
