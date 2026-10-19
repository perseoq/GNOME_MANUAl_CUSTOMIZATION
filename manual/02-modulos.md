# Capítulo 2: El Sistema de Módulos y la API Interna

> En este capítulo aprenderás de dónde vienen las bibliotecas que usa una extensión. También harás que tu extensión muestre una notificación al activarse.

## Qué vas a aprender

- Importar bibliotecas del sistema con `gi://`.
- Distinguir `gi://`, `resource://` y las rutas relativas.
- Escribir correctamente las rutas internas de GNOME Shell.
- Usar `Main.notify()` para mostrar una notificación.
- Reconocer `Main.panel`, `Main.overview` y los módulos internos más usados.
- Diagnosticar errores de importación con el log real.

Al final del capítulo, tu extensión mostrará una notificación al activarse y seguirá escribiendo en el log al desactivarse.

## Antes de empezar necesitas

- Haber completado el Capítulo 1.
- Tener una extensión que se activa y escribe en el log.
- Saber usar `gnome-extensions enable`, `gnome-extensions disable` y `journalctl`.
- GNOME Shell 50.6 sobre Wayland.

## El concepto, con una analogía

Una extensión es como una caja de herramientas. Las herramientas no las fabricas tú: vienen de una caja común y tienen un enchufe estándar. En GNOME, ese enchufe se llama **GObject Introspection** y las herramientas se cargan con `gi://`.

Las rutas `resource://` son puertas internas de la casa de GNOME Shell. Cada puerta tiene un nombre exacto. Si escribes una mayúscula donde va una minúscula, la puerta no existe.

`Main` es el panel de control del shell. Desde allí puedes mostrar una notificación, consultar el panel superior o acceder a la vista de actividades.

### 2.1. Importación de librerías GI con `gi://`

GObject Introspection es el sistema que describe bibliotecas escritas en C para que GJS pueda usarlas. Cuando escribes `import GLib from 'gi://GLib'`, GJS busca la descripción de GLib y te entrega un objeto con sus funciones. Esa descripción se guarda en un archivo llamado **typelib**. Un typelib es el archivo que indica a GJS qué funciones, clases y tipos ofrece una biblioteca.

GTK es otra biblioteca de interfaces, usada por las aplicaciones y por la ventana de preferencias. No se usa en `extension.js`; para la interfaz del shell se usan St y Clutter.

Estas son las bibliotecas que más usarás:

| Biblioteca | Para qué sirve | Se usa en |
| :--- | :--- | :--- |
| `GLib` | Temporizadores, fechas, utilidades del sistema | Capítulos 3, 9 |
| `Gio` | Archivos, ajustes y D-Bus (comunicación entre programas) | Capítulo 5 |
| `St` | Widgets y estilos del shell | Capítulo 3 |
| `Clutter` | Actores, animaciones y entrada | Capítulo 3 |
| `Meta` | Ventanas y espacios de trabajo | Capítulo 6 |
| `Shell` | Utilidades internas del shell | Capítulos 3, 6 |

Ejemplo de importación:

```javascript
import GLib from 'gi://GLib';
import Gio from 'gi://Gio';
import St from 'gi://St';
```

> **Qué hace este bloque:** cada línea carga una biblioteca distinta. `GLib` ofrece funciones generales; `Gio` ofrece archivos y ajustes; `St` ofrece widgets. Importar una biblioteca no ejecuta ninguna acción: únicamente pone sus nombres a disposición del archivo. En este capítulo usarás `Main`; las demás bibliotecas se usan en capítulos posteriores.

**Errores posibles en este bloque:**

**Error 1: `Error: Requiring NoExiste, version none: Typelib file for namespace 'NoExiste' (any version) not found`**
- **Ubicación:** `extension.js`, línea del `import`.
- **Síntoma:** el log muestra `Requiring NoExiste, version none: Typelib file for namespace 'NoExiste' (any version) not found` y la extensión queda en `ERROR`.
- **Causa:** el nombre de la biblioteca no existe o está mal escrito.
- **Corrección:** usa un nombre válido, como `gi://GLib`, `gi://Gio` o `gi://St`. Después de corregir, el módulo se carga.

**Error 2: `Error: Requiring Gtk, version 3.0: Requiring namespace 'Gtk' version '3.0', but '4.0' is already loaded`**
- **Ubicación:** `extension.js`, línea del `import`.
- **Síntoma:** la extensión no carga y el log muestra el conflicto entre las versiones 3.0 y 4.0 de Gtk.
- **Causa:** el proceso del shell ya tiene cargada otra versión de Gtk y no se pueden cargar dos versiones del mismo espacio de nombres.
- **Corrección:** en `extension.js` no uses GTK. Para la interfaz del shell usa `St` y `Clutter`. En `prefs.js`, GTK4 se importa como `gi://Gtk?version=4.0`; en el Capítulo 5 verás el contexto correcto.

**Error 3: la biblioteca se importa, pero el nombre no se usa.**
- **Ubicación:** `extension.js`, línea del `import`.
- **Síntoma:** no hay error, pero el archivo tiene imports innecesarios.
- **Causa:** copiaste todos los imports del ejemplo sin usar la biblioteca.
- **Corrección:** importa únicamente lo que uses. Un import sin usar no rompe la extensión, pero hace el código más difícil de leer.

**Comprueba que lo entiendes:** relaciona cada biblioteca con su uso: `GLib`, `Gio`, `St`, `Meta`. Después explica qué significa `gi://`.

### 2.2. La trampa de las rutas `resource://`

`resource://` carga módulos JavaScript que vienen dentro de GNOME Shell. Estas rutas distinguen mayúsculas de minúsculas. Un cambio de `shell` a `Shell` hace que el recurso no exista.

Las rutas más importantes para una extensión son:

| Archivo | Import correcto |
| :--- | :--- |
| `extension.js` | `resource:///org/gnome/shell/extensions/extension.js` |
| `extension.js` | `resource:///org/gnome/shell/ui/main.js` |
| `prefs.js` | `resource:///org/gnome/Shell/Extensions/js/extensions/prefs.js` |

Observa la diferencia: en `extension.js`, la ruta empieza por `org/gnome/shell` en minúscula. En `prefs.js`, la ruta de preferencias usa `org/gnome/Shell/Extensions` con mayúsculas. No es un error de escritura: son recursos distintos.

Ejemplo de importación de `Main`:

```javascript
import * as Main from 'resource:///org/gnome/shell/ui/main.js';
import {Extension} from 'resource:///org/gnome/shell/extensions/extension.js';
```

> **Qué hace este bloque:** `import * as Main` importa todo el módulo `main.js` dentro de un objeto llamado `Main`. `Main.notify` será una función de ese objeto. `import {Extension}` importa únicamente la clase `Extension`. La ruta `main.js` está en `org/gnome/shell/ui/`, con `shell` en minúscula.

**Errores posibles en este bloque:**

**Error 1: `ImportError: Unable to load file from: resource:///org/gnome/Shell/ui/main.js (El recurso en «/org/gnome/Shell/ui/main.js» no existe)`**
- **Ubicación:** `extension.js`, línea del `import`.
- **Síntoma:** GNOME Shell registra `ImportError` y la extensión queda en `ERROR`.
- **Causa:** escribiste `Shell` con mayúscula en una ruta de `extension.js`.
- **Corrección:** cambia la ruta a `resource:///org/gnome/shell/ui/main.js`. Después de corregir, el módulo se carga.

**Error 2: `ImportError: Unable to load file from: resource:///org/gnome/shell/ui/Main.js`**
- **Ubicación:** `extension.js`, línea del `import`.
- **Síntoma:** el recurso no se encuentra.
- **Causa:** el archivo se llama `main.js`, no `Main.js`.
- **Corrección:** usa exactamente `main.js` con minúscula. Después de corregir, el módulo se carga.

**Error 3: `ImportError: Unable to load file from: resource:///org/gnome/Shell/Extensions/js/extensions/prefs.js` en `extension.js`.**
- **Ubicación:** `extension.js`, línea del `import`.
- **Síntoma:** la extensión no carga.
- **Causa:** la ruta de preferencias se usa en `prefs.js`, no en `extension.js`.
- **Corrección:** en `extension.js` importa `Extension` desde `resource:///org/gnome/shell/extensions/extension.js`. La ruta con mayúsculas se usa únicamente en `prefs.js`.

**Comprueba que lo entiendes:** escribe de memoria la ruta de `main.js` y la ruta de `extension.js`. Después explica por qué la ruta de `prefs.js` usa mayúsculas.

### 2.3. Exploración de los módulos internos del shell

`Main` es el punto de entrada a varias partes del shell:

- `Main.panel`: el panel superior. En el Capítulo 3 añadirás un botón con `Main.panel.addToStatusArea()`.
- `Main.overview`: la vista de Actividades. Permite mostrar u ocultar la vista general.
- `Main.notify()`: muestra una notificación en la pantalla.

Una **API** (Application Programming Interface) es un conjunto de funciones y objetos que un programa ofrece a otros programas. La API interna de GNOME Shell es el conjunto de módulos, clases y funciones que el shell usa para construir el escritorio.

Estos son algunos módulos internos que se usan en capítulos posteriores:

| Módulo | Función |
| :--- | :--- |
| `PanelMenu` | Botones y menús del panel |
| `PopupMenu` | Menús desplegables |
| `QuickSettings` | Menú de ajustes rápidos |
| `ModalDialog` | Ventanas modales del shell |

Estos módulos son APIs internas de GNOME Shell. Pueden cambiar entre versiones. El manual usa la versión 50.6 y no presenta funciones que no estén verificadas en esa versión.

La forma más sencilla de mostrar un mensaje es `Main.notify()`. Dentro de `enable()`, después de importar `Main`, la llamada es:

```javascript
Main.notify(
    'Mi Primera Extensión',
    'La extensión se activó correctamente.'
);
```

> **Qué hace este bloque:** `Main.notify` recibe dos textos. El primero es el título de la notificación; el segundo es el cuerpo. La notificación aparece en la parte superior de la pantalla y queda en el historial de notificaciones. No crea un actor propio ni necesita limpieza en `disable()`.

**Errores posibles en este bloque:**

**Error 1: `ReferenceError: Main is not defined`**
- **Ubicación:** `extension.js`, línea de `Main.notify`.
- **Síntoma:** el log muestra `ReferenceError: Main is not defined` y la extensión queda en `ERROR`.
- **Causa:** usaste `Main` sin importarlo.
- **Corrección:** añade `import * as Main from 'resource:///org/gnome/shell/ui/main.js';` al principio del archivo. Después de corregir, `Main` existe.

**Error 2: `TypeError: Main.notify is not a function`**
- **Ubicación:** `extension.js`, línea de `Main.notify`.
- **Síntoma:** el log muestra que `notify` no es una función.
- **Causa:** importaste otro módulo con el nombre `Main` o escribiste mal el nombre de la función.
- **Corrección:** usa la ruta `resource:///org/gnome/shell/ui/main.js` y escribe `Main.notify` con minúscula. Después de corregir, la función existe.

**Error 3: la notificación no aparece.**
- **Ubicación:** pantalla del escritorio.
- **Síntoma:** la extensión está en estado `ACTIVE`, pero no ves la notificación.
- **Causa:** el modo “No molestar” está activo, una aplicación está en pantalla completa o las notificaciones están desactivadas.
- **Corrección:** abre el menú de fecha y hora, desactiva “No molestar” y comprueba la configuración de notificaciones. Después de corregir, la notificación aparece.

**Comprueba que lo entiendes:** explica la diferencia entre `Main.panel`, `Main.overview` y `Main.notify`. Después indica cuál usarías para mostrar un aviso.

## Ejemplo guiado paso a paso

Vas a modificar tu extensión para que muestre una notificación al activarse y escriba un mensaje en el log al desactivarse. El `metadata.json` no cambia.

### Bloque 1: importar `Main` y `Extension`

```javascript
import * as Main from 'resource:///org/gnome/shell/ui/main.js';
import {Extension} from 'resource:///org/gnome/shell/extensions/extension.js';
```

> **Qué hace este bloque:** la primera línea pone el módulo `main.js` dentro de `Main`. La segunda línea importa la clase base `Extension`. Las dos rutas usan `shell` en minúscula.

**Errores posibles en este bloque:**

**Error 1: `ImportError: Unable to load file from: resource:///org/gnome/Shell/ui/main.js`**
- **Ubicación:** línea del primer import.
- **Síntoma:** la extensión queda en `ERROR`.
- **Causa:** `Shell` está en mayúscula.
- **Corrección:** usa `resource:///org/gnome/shell/ui/main.js`. Después de corregir, el import funciona.

**Error 2: `ReferenceError: Main is not defined`**
- **Ubicación:** línea de `Main.notify`, más abajo.
- **Síntoma:** el log muestra que `Main` no existe.
- **Causa:** no importaste `Main` o el import está mal escrito.
- **Corrección:** añade el import completo al principio del archivo. Después de corregir, `Main` existe.

### Bloque 2: clase con notificación

```javascript
export default class MiPrimeraExtension extends Extension {
    enable() {
        console.log(`[${this.uuid}] activada`);
        Main.notify(
            'Mi Primera Extensión',
            'La extensión se activó correctamente.'
        );
    }

    disable() {
        console.log(`[${this.uuid}] desactivada`);
    }
}
```

> **Qué hace este bloque:** `enable()` escribe en el log y muestra una notificación. `disable()` escribe en el log y no modifica la interfaz: las notificaciones ya mostradas las gestiona GNOME Shell y desaparecen solas. `this.uuid` identifica la extensión. No se crean actores ni señales, así que no hay recursos que destruir en `disable()`.

**Errores posibles en este bloque:**

**Error 1: `SyntaxError: Unexpected token '}'`**
- **Ubicación:** línea indicada por el log.
- **Síntoma:** la extensión no carga.
- **Causa:** falta una llave, un paréntesis o una coma en la llamada a `Main.notify`.
- **Corrección:** revisa que cada `Main.notify(` tenga su paréntesis de cierre y que cada método tenga sus llaves completas. Después de corregir, el archivo se carga.

**Error 2: la notificación muestra `undefined`.**
- **Ubicación:** llamada a `Main.notify`.
- **Síntoma:** el título o el cuerpo de la notificación aparece como `undefined`.
- **Causa:** pasaste una variable sin valor o escribiste mal el texto.
- **Corrección:** usa textos entre comillas simples, como en el ejemplo. Después de corregir, la notificación muestra el texto correcto.

**Error 3: la extensión se activa, pero el log no muestra `activada`.**
- **Ubicación:** `enable()`.
- **Síntoma:** el estado es `ACTIVE`, pero no hay mensaje en el log.
- **Causa:** el `console.log` está fuera de `enable()` o el log se abrió después de activar.
- **Corrección:** coloca el `console.log` dentro de `enable()` y abre `journalctl` antes de activar. Después de corregir, el mensaje aparece.

### Guarda y recarga

Guarda `extension.js` y ejecuta:

```bash
gnome-extensions disable miprimeraextension@ejemplo.local
gnome-extensions enable miprimeraextension@ejemplo.local
```

Si la extensión ya estaba activa y el cambio no aparece, desactívala y vuelve a activarla. Si sigue sin aparecer, cierra sesión y vuelve a entrar. En Wayland, `Alt+F2` seguido de `r` no reinicia el shell.

**Errores posibles en este bloque:**

**Error 1: `La extensión «miprimeraextension@ejemplo.local» no existe`**
- **Ubicación:** salida de `gnome-extensions`.
- **Síntoma:** el comando no encuentra la extensión.
- **Causa:** el UUID está mal escrito o la extensión no fue reconocida.
- **Corrección:** ejecuta `gnome-extensions list` y copia el UUID exacto. Después de corregir, el comando funciona.

**Error 2: el cambio no se aplica.**
- **Ubicación:** comportamiento de la extensión.
- **Síntoma:** sigues viendo la notificación o el mensaje anterior.
- **Causa:** GNOME Shell mantiene cargado el módulo anterior.
- **Corrección:** cierra sesión y vuelve a entrar. Después de corregir, el código nuevo se carga.

## Verifica que funciona

Activa la extensión y observa la pantalla:

```bash
gnome-extensions enable miprimeraextension@ejemplo.local
```

Debe aparecer una notificación con el título `Mi Primera Extensión` y el cuerpo `La extensión se activó correctamente.` El comando activa la extensión y ejecuta `enable()`; el log confirma que el método se ejecutó.

En la terminal del log debes ver:

```text
GNOME Shell-Message: 10:40:49.541: [miprimeraextension@ejemplo.local] activada
```

**Errores posibles en este bloque:**

**Error 1: no aparece la notificación.**
- **Ubicación:** pantalla.
- **Síntoma:** la extensión está activa, pero no ves nada.
- **Causa:** “No molestar” está activo, la aplicación está en pantalla completa o las notificaciones están desactivadas.
- **Corrección:** desactiva “No molestar” y revisa la configuración de notificaciones. Después de corregir, la notificación aparece.

**Error 2: el estado es `ERROR`.**
- **Ubicación:** `gnome-extensions info`.
- **Síntoma:** la extensión no se activa.
- **Causa:** un import o una llamada a `Main.notify` tiene un error.
- **Corrección:** lee el log con `journalctl /usr/bin/gnome-shell -f -o cat`, corrige la línea indicada y vuelve a activar.

Después desactiva la extensión:

```bash
gnome-extensions disable miprimeraextension@ejemplo.local
```

Al desactivar no se muestra una notificación nueva, pero el log debe mostrar:

```text
GNOME Shell-Message: 10:40:51.591: [miprimeraextension@ejemplo.local] desactivada
```

El comando ejecuta `disable()` y confirma que la extensión se desactivó.

**Errores posibles en este bloque:**

**Error 1: el mensaje `desactivada` no aparece.**
- **Ubicación:** log de GNOME Shell.
- **Síntoma:** la extensión se desactiva, pero no hay mensaje de desactivación.
- **Causa:** `disable()` no existe, tiene un error o el log se abrió después de desactivar.
- **Corrección:** revisa `disable()` y abre el log antes de desactivar. Después de corregir, el mensaje aparece.

**Error 2: la extensión sigue activa.**
- **Ubicación:** `gnome-extensions info`.
- **Síntoma:** el estado sigue siendo `ACTIVE` después de `disable`.
- **Causa:** el UUID está mal escrito o `disable()` lanzó una excepción.
- **Corrección:** copia el UUID exacto de `gnome-extensions list` y revisa el log. Después de corregir, el estado deja de ser `ACTIVE`.

## Errores de integración del capítulo

### Error 1: `Main` no está importado

**Síntoma:**

```text
GNOME Shell-CRITICAL **: Extension miprimeraextension@ejemplo.local: ReferenceError: Main is not defined
```

**Causa:** llamaste a `Main.notify` sin el import de `main.js`.

**Corrección:** añade `import * as Main from 'resource:///org/gnome/shell/ui/main.js';`. Después de corregir, `Main` existe y la notificación se muestra.

### Error 2: la ruta de `Main` usa mayúsculas

**Síntoma:**

```text
ImportError: Unable to load file from: resource:///org/gnome/Shell/ui/main.js (El recurso en «/org/gnome/Shell/ui/main.js» no existe)
```

**Causa:** escribiste `Shell` con mayúscula en `extension.js`.

**Corrección:** usa `resource:///org/gnome/shell/ui/main.js`. Después de corregir, el módulo se carga.

### Error 3: una biblioteca GI no existe

**Síntoma:**

```text
Error: Requiring NoExiste, version none: Typelib file for namespace 'NoExiste' (any version) not found
```

**Causa:** el nombre de la biblioteca está mal escrito o no está instalada.

**Corrección:** usa una biblioteca válida, como `gi://GLib`, `gi://Gio`, `gi://St`, `gi://Meta` o `gi://Shell`. Después de corregir, el import funciona.

### Error 4: la notificación está silenciada

**Síntoma:** la extensión está activa y el log muestra `activada`, pero no aparece ninguna notificación.

**Causa:** el modo “No molestar” está activo, hay una aplicación en pantalla completa o las notificaciones están desactivadas.

**Corrección:** abre el menú de fecha y hora, desactiva “No molestar” y revisa la configuración de notificaciones. Después de corregir, la notificación aparece.

### Error 5: el cambio de código no se aplica

**Síntoma:** después de guardar, sigues viendo el comportamiento anterior.

**Causa:** GNOME Shell conserva el módulo cargado.

**Corrección:** desactiva y activa la extensión. Si el cambio sigue sin aparecer, cierra sesión y vuelve a entrar.

## Ejercicios

### Reproductivo

1. Añade el import de `Main` a `extension.js`.
2. Añade `Main.notify` dentro de `enable()`.
3. Activa la extensión y comprueba que aparece la notificación.
4. Desactiva la extensión y comprueba que no aparece una notificación nueva.
5. Revisa el log y confirma los mensajes `activada` y `desactivada`.

### Aplicativo

1. Cambia el título de la notificación por el nombre de tu extensión.
2. Cambia el cuerpo por `La extensión funciona en GNOME Shell 50.6`.
3. Añade el UUID de la extensión al cuerpo usando `${this.uuid}`.
4. Activa y desactiva la extensión.
5. Verifica que la notificación muestra el UUID correcto.

### Creativo

1. Haz que la notificación de activación incluya el nombre del equipo.
2. Pista: importa `GLib` desde `gi://GLib` y usa `GLib.get_host_name()`.
3. Comprueba que la notificación muestra un nombre y no `undefined`.
4. Explica por qué `GLib` se importa con `gi://` y `Main` con `resource://`.

**Rúbrica del ejercicio creativo:**

- La extensión sigue activándose sin errores.
- La notificación muestra el nombre del equipo.
- El import de `GLib` usa `gi://GLib`.
- El import de `Main` usa `resource://`.
- La explicación distingue una biblioteca del sistema de un módulo interno del shell.

## Autoevaluación

1. ¿Qué diferencia hay entre `gi://` y `resource://`?
2. ¿Por qué `resource:///org/gnome/Shell/ui/main.js` falla en `extension.js`?
3. ¿Qué hace `Main.notify()` y qué dos argumentos recibe?
4. ¿Cuándo usarías `Main.panel` y cuándo `Main.overview`?
5. Tu extensión está en estado `ERROR` después de añadir `Main.notify`. ¿Qué revisarías y en qué orden?
6. ¿Por qué una notificación mostrada en `enable()` no necesita limpieza en `disable()`?

## Respuestas razonadas

1. `gi://` carga bibliotecas del sistema mediante GObject Introspection, como GLib, Gio, St o Meta. `resource://` carga módulos JavaScript internos de GNOME Shell, como `main.js` o `extension.js`. Las rutas `resource://` distinguen mayúsculas de minúsculas.

2. Porque en `extension.js` la ruta correcta usa `org/gnome/shell` con `shell` en minúscula. `Shell` con mayúscula corresponde a otra ruta, la de las preferencias, y el recurso no existe en ese contexto.

3. `Main.notify()` muestra una notificación. Recibe dos textos: el título y el cuerpo. No crea actores propios; la notificación la gestiona el shell.

4. `Main.panel` se usa para acceder al panel superior, por ejemplo para añadir un botón. `Main.overview` se usa para interactuar con la vista de Actividades. `Main.notify` se usa para mostrar un aviso.

5. Primero leería el log con `journalctl /usr/bin/gnome-shell -f -o cat` para ver el error exacto. Después revisaría el import de `Main` y la ruta `resource://`. Finalmente comprobaría que `Main.notify` esté escrito correctamente y que la extensión tenga `export default`. El orden va del mensaje real a la causa más probable.

6. Porque la notificación no es un actor ni una señal creada por la extensión: la crea y la gestiona GNOME Shell. La extensión únicamente solicita que se muestre; el shell se encarga de retirarla.

## Soluciones de los ejercicios

### Solución del reproductivo

El archivo `extension.js` debe quedar así:

```javascript
import * as Main from 'resource:///org/gnome/shell/ui/main.js';
import {Extension} from 'resource:///org/gnome/shell/extensions/extension.js';

export default class MiPrimeraExtension extends Extension {
    enable() {
        console.log(`[${this.uuid}] activada`);
        Main.notify(
            'Mi Primera Extensión',
            'La extensión se activó correctamente.'
        );
    }

    disable() {
        console.log(`[${this.uuid}] desactivada`);
    }
}
```

**Errores posibles en este bloque:** revisa los errores de los bloques 1 y 2 del ejemplo guiado. Si el estado es `ERROR`, el log indica la línea exacta. Si no aparece la notificación, revisa “No molestar”.

### Solución del aplicativo

Sustituye el método `enable()` completo de la solución del reproductivo por este:

```javascript
enable() {
    console.log(`[${this.uuid}] activada`);
    Main.notify(
        'Mi Primera Extensión',
        `La extensión ${this.uuid} funciona en GNOME Shell 50.6`
    );
}
```

**Errores posibles en este bloque:** si aparece `undefined`, comprueba que `this.uuid` esté dentro de un método de la clase. Si la notificación no aparece, revisa “No molestar”.

### Solución del creativo

Una solución válida importa `GLib` y lo usa en el cuerpo:

```javascript
import GLib from 'gi://GLib';
import * as Main from 'resource:///org/gnome/shell/ui/main.js';
import {Extension} from 'resource:///org/gnome/shell/extensions/extension.js';

export default class MiPrimeraExtension extends Extension {
    enable() {
        console.log(`[${this.uuid}] activada`);
        Main.notify(
            'Mi Primera Extensión',
            `Equipo: ${GLib.get_host_name()}`
        );
    }

    disable() {
        console.log(`[${this.uuid}] desactivada`);
    }
}
```

**Errores posibles en este bloque:** si `GLib` no está definido, falta el import `gi://GLib`. Si `get_host_name` no existe, comprueba que escribiste el nombre con guiones bajos. Si la notificación muestra `undefined`, revisa la llamada a `GLib.get_host_name()`.

## Si quieres saber más

- Guía de GJS sobre bibliotecas GI: <https://gjs.guide/guides/gobject/>
- Documentación de GNOME Shell: <https://gnome.pages.gitlab.gnome.org/gnome-shell/>
- Referencia de St: <https://gnome.pages.gitlab.gnome.org/gnome-shell/st/>

No necesitas estos enlaces para continuar. El manual contiene lo necesario para el proyecto.

## Siguiente paso

Cuando la notificación aparezca al activar y desactivar la extensión, continúa con el Capítulo 3. Allí crearás un botón en el panel superior y aprenderás a construir interfaces con St y Clutter.
