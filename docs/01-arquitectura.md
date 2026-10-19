# Capítulo 1: Arquitectura y Fundamentos del Shell

> En este capítulo crearás tu primera extensión. Todavía no tendrá interfaz: únicamente escribirá mensajes en el log. El **log** (registro) es el texto donde GNOME Shell guarda mensajes, avisos y errores. Es el equivalente a encender la luz antes de amueblar la casa.

## Qué vas a aprender

- Explicar qué es GJS y cómo ejecuta JavaScript dentro de GNOME Shell.
- Entender qué es un módulo ESM y por qué `imports.*` ya no se usa.
- Distinguir los espacios de nombres `gi://`, `resource://` y las rutas relativas.
- Crear la carpeta, el `metadata.json` y el `extension.js` de una extensión.
- Implementar el contrato `enable()` / `disable()`.
- Ver los mensajes de tu extensión en el log de GNOME Shell.
- Reconocer los recursos que deben limpiarse al desactivar una extensión.

Al final del capítulo, tendrás una extensión instalable que escribe un mensaje al activarse y otro al desactivarse.

## Antes de empezar necesitas

- Haber completado el Capítulo Introductorio.
- Saber abrir una terminal y usar `pwd`, `ls` y `cd`.
- Saber crear carpetas y archivos con un editor de texto.
- GNOME Shell 50.6 sobre Wayland.
- Una cuenta de usuario normal, sin permisos de administrador.

### Comprueba tu versión

Abre la terminal y ejecuta:

```bash
gnome-shell --version
```

Salida de ejemplo:

```text
GNOME Shell 50.6
```

> **Qué hace este bloque:** `gnome-shell --version` muestra la versión de GNOME Shell instalada. La opción `--version` pide la versión y no inicia una segunda instancia del shell. Este manual está probado con la versión 50.6.

**Errores posibles en este bloque:**

**Error 1: `bash: gnome-shell: instrucción no encontrada...`**
- **Ubicación:** línea de la terminal.
- **Síntoma:** la terminal muestra `bash: gnome-shell: instrucción no encontrada...`.
- **Causa:** GNOME Shell no está instalado o la terminal no encuentra el programa.
- **Corrección:** comprueba que estás en una sesión de GNOME. Si usas otra distribución, instala GNOME Shell antes de continuar. Después de corregir, `gnome-shell --version` muestra una versión.

**Error 2: la versión no es 50.6.**
- **Ubicación:** salida del comando.
- **Síntoma:** aparece una versión distinta, por ejemplo `GNOME Shell 49.2`.
- **Causa:** el equipo usa otra versión de GNOME.
- **Corrección:** actualiza GNOME o usa una máquina virtual con GNOME Shell 50.6. Algunas rutas y APIs cambian entre versiones.

## El concepto, con una analogía

Una extensión es como un inquilino que entra en una casa. Al entrar, coloca sus muebles y enciende sus aparatos. Al salir, debe llevarse sus cosas y dejar la casa como estaba. Si deja un grifo abierto o un cable enchufado, el problema persiste aunque el inquilino ya no esté.

En una extensión, `enable()` es la entrada y `disable()` es la salida. Todo lo que se crea en `enable()` debe destruirse o desconectarse en `disable()`.

### 1.1. El entorno GJS y la migración obligatoria a ESM

#### 1.1.1. Qué es GJS

GJS es el motor de JavaScript de GNOME. Su nombre significa **GNOME JavaScript**. GJS combina dos piezas:

- **SpiderMonkey:** el motor que interpreta JavaScript.
- **GObject Introspection:** el sistema que permite a JavaScript usar bibliotecas escritas en C.

Imagina un traductor en una reunión. A un lado está JavaScript; al otro, las bibliotecas del sistema escritas en C. El traductor permite que las dos partes se entiendan. Ese traductor es GJS.

GJS no es Node.js y no es el JavaScript de un navegador web. No tiene `document`, no tiene `window` y no usa `npm` para instalar paquetes. En su lugar, usa las bibliotecas de GNOME mediante GObject Introspection.

**Comprueba que lo entiendes:** explica con tus palabras por qué GJS necesita un traductor. Menciona JavaScript, las bibliotecas en C y GObject Introspection.

#### 1.1.2. La sintaxis `import` / `export` y la prohibición de `imports.*`

Un **módulo** es un archivo de JavaScript que puede exportar nombres y importar nombres de otros módulos. ESM significa **ECMAScript Modules** y es el sistema de módulos estándar de JavaScript.

Antes de GNOME 45, las extensiones usaban `imports.*`. Desde GNOME 45, ese sistema se eliminó. Si sigues un tutorial antiguo y ves código como este, no funcionará:

```javascript
const Main = imports.ui.main;
```

Este bloque es un ejemplo de código obsoleto. No lo uses en GNOME Shell 50.6.

**Errores posibles en este bloque:**

**Error 1: `SyntaxError: import declarations may only appear at top level of a module @ resource:///org/gnome/shell/ui/main.js:1:1`**
- **Ubicación:** `extension.js`, línea 3, donde aparece `imports.ui.main`.
- **Síntoma:** GNOME Shell registra el error y la extensión queda en `ERROR`.
- **Causa:** `imports.*` pertenece al sistema anterior a GNOME 45 y no es compatible con ESM.
- **Corrección:** elimina la línea y usa `import * as Main from 'resource:///org/gnome/shell/ui/main.js';`. Después de corregir, el módulo se carga.

En GNOME Shell 50.6 se usa `import`:

```javascript
import {Extension} from 'resource:///org/gnome/shell/extensions/extension.js';
import GLib from 'gi://GLib';
```

> **Qué hace este bloque:** la primera línea importa el nombre `Extension` desde el módulo interno de GNOME Shell. `import` carga el módulo; `{Extension}` indica el nombre exportado que quieres usar; `from` indica la dirección del módulo. La segunda línea importa la biblioteca `GLib` desde `gi://GLib`; `GLib` se usa para temporizadores, fechas y utilidades del sistema.

**Errores posibles en este bloque:**

**Error 1: `SyntaxError: import declarations may only appear at top level of a module @ resource:///org/gnome/shell/ui/main.js:1:1`**
- **Ubicación:** `extension.js`, línea donde aparece `imports.ui.main`.
- **Síntoma:** GNOME Shell registra `SyntaxError: import declarations may only appear at top level of a module` y la extensión queda en estado `ERROR`.
- **Causa:** usaste `imports.*`, que ya no es compatible con el sistema ESM de GNOME Shell 50.6.
- **Corrección:** sustituye `const Main = imports.ui.main;` por `import * as Main from 'resource:///org/gnome/shell/ui/main.js';`. Después de corregir, la extensión carga y el estado deja de ser `ERROR`.

**Error 2: `SyntaxError: Unexpected identifier`**
- **Ubicación:** `extension.js`, línea del `import`.
- **Síntoma:** la extensión no carga y el log muestra un error de sintaxis.
- **Causa:** falta un punto y coma, una comilla o una llave en la línea del `import`.
- **Corrección:** compara la línea con el bloque correcto y escribe exactamente las comillas y las llaves. Después de corregir, el módulo se carga sin error de sintaxis.

**Error 3: `ImportError: Unable to load file from: ...`**
- **Ubicación:** `extension.js`, línea del `import`.
- **Síntoma:** el log muestra `ImportError: Unable to load file from: ...` y la extensión queda en `ERROR`.
- **Causa:** la ruta del módulo está mal escrita o el recurso no existe.
- **Corrección:** revisa la ruta y las mayúsculas. En `extension.js`, la ruta del shell usa `shell` en minúscula. Después de corregir, el módulo se carga.

#### 1.1.3. Los tres espacios de nombres

Un **espacio de nombres** es un prefijo que indica a qué sistema pertenece un módulo. GNOME Shell usa tres formas de indicar de dónde viene un módulo:

| Espacio de nombres | Para qué sirve | Ejemplo |
| :--- | :--- | :--- |
| `gi://` | Bibliotecas del sistema expuestas por GObject Introspection | `gi://GLib`, `gi://St`, `gi://Meta` |
| `resource://` | Módulos JavaScript internos de GNOME Shell | `resource:///org/gnome/shell/ui/main.js` |
| Ruta relativa | Módulos propios dentro de la extensión | `./miModulo.js` |

`gi://` se usa para bibliotecas escritas en C. `resource://` se usa para archivos JavaScript que vienen dentro de GNOME Shell. Una ruta relativa se usa para tus propios archivos; el Capítulo 9 la utiliza para separar el proveedor de datos.

Las rutas `resource://` distinguen mayúsculas de minúsculas. Esta es una fuente frecuente de errores:

```javascript
import * as Main from 'resource:///org/gnome/shell/ui/main.js';
import {Extension} from 'resource:///org/gnome/shell/extensions/extension.js';
```

> **Qué hace este bloque:** `import * as Main` importa todo el contenido del módulo `main.js` dentro de un objeto llamado `Main`. `import {Extension}` importa únicamente el nombre `Extension`. La ruta `org/gnome/shell` va en minúsculas en `extension.js`. En `prefs.js`, la ruta de preferencias usa `org/gnome/Shell/Extensions` con mayúsculas; lo verás en el Capítulo 5.

**Errores posibles en este bloque:**

**Error 1: `ImportError: Unable to load file from: resource:///org/gnome/Shell/ui/main.js (El recurso en «/org/gnome/Shell/ui/main.js» no existe)`**
- **Ubicación:** `extension.js`, línea del `import`.
- **Síntoma:** GNOME Shell registra `ImportError: Unable to load file from: resource:///org/gnome/Shell/ui/main.js` y la extensión queda en `ERROR`.
- **Causa:** escribiste `Shell` con mayúscula en una ruta que debe ser `shell` en minúscula.
- **Corrección:** cambia la ruta a `resource:///org/gnome/shell/ui/main.js`. Después de corregir, el módulo se carga y la extensión puede activarse.

**Error 2: `Error: Requiring NoExiste, version none: Typelib file for namespace 'NoExiste' (any version) not found`**
- **Ubicación:** `extension.js`, línea del `import` de `gi://`.
- **Síntoma:** el log muestra `Requiring NoExiste, version none: Typelib file for namespace 'NoExiste' (any version) not found`.
- **Causa:** la biblioteca no existe o el nombre del espacio de nombres está mal escrito.
- **Corrección:** usa un nombre válido, como `gi://GLib`, `gi://Gio`, `gi://St`, `gi://Meta` o `gi://Shell`. Después de corregir, el módulo se carga.

No uses `gi://Gtk?version=4.0` en `extension.js`. GTK4 se usa en la ventana de preferencias, que se ejecuta en otro proceso. En `extension.js` se usan St y Clutter. El Capítulo 5 explica la diferencia.

**Comprueba que lo entiendes:** clasifica estas rutas como `gi://`, `resource://` o relativa:

- `gi://St`
- `resource:///org/gnome/shell/ui/main.js`
- `./proveedor.js`

Después explica qué tipo de archivo espera cada una.

### 1.2. Estructura mínima de una extensión manual

#### 1.2.1. El directorio de la extensión

Las extensiones de usuario viven en:

```text
~/.local/share/gnome-shell/extensions/UUID/
```

`~` es tu carpeta personal. `.local` es una carpeta oculta. `share` contiene datos compartidos por tus aplicaciones. `gnome-shell/extensions` es la carpeta donde GNOME Shell busca extensiones de usuario. `UUID` es el identificador único de la extensión.

El **UUID** es un nombre único con formato `nombre@dominio`. Por ejemplo:

```text
miprimeraextension@ejemplo.local
```

Para publicar en extensions.gnome.org, usa un dominio que controles. Para practicar en tu equipo, `ejemplo.local` es suficiente.

La carpeta debe llamarse exactamente igual que el campo `uuid` de `metadata.json`. Si no coinciden, la extensión puede no aparecer o puede cargarse con un identificador incorrecto.

Crea la carpeta y entra en ella con estos comandos:

```bash
mkdir -p ~/.local/share/gnome-shell/extensions/miprimeraextension@ejemplo.local
cd ~/.local/share/gnome-shell/extensions/miprimeraextension@ejemplo.local
pwd
```

> **Qué hace este bloque:** `mkdir` significa “make directory” y crea una carpeta. La opción `-p` crea las carpetas intermedias que falten y no falla si la carpeta ya existe. La ruta crea la carpeta de la extensión. `cd` entra en ella. `pwd` confirma la ubicación.

**Errores posibles en este bloque:**

**Error 1: `mkdir: no se puede crear el directorio «/root/curso-gnome»: Permiso denegado`**
- **Ubicación:** línea de la terminal.
- **Síntoma:** la terminal muestra `mkdir: no se puede crear el directorio «/root/curso-gnome»: Permiso denegado`.
- **Causa:** intentaste crear una carpeta en una zona protegida del sistema.
- **Corrección:** usa una ruta dentro de tu carpeta personal, como `~/.local/share/gnome-shell/extensions/`. Después de corregir, `mkdir` termina sin error.

**Error 2: `bash: cd: /home/ana/.local/share/gnome-shell/extensions/miprimeraextension@ejemplo.local: No existe el fichero o el directorio`**
- **Ubicación:** línea de la terminal.
- **Síntoma:** la terminal muestra que la carpeta no existe.
- **Causa:** el comando `mkdir` falló o el nombre de la carpeta está mal escrito.
- **Corrección:** ejecuta `ls ~/.local/share/gnome-shell/extensions` para ver los nombres y repite `mkdir -p` con la ruta exacta. Después de corregir, `pwd` muestra la carpeta de la extensión.

Para crear los archivos dentro de esa carpeta, abre el gestor de archivos y pulsa `Ctrl+H`. Esa combinación muestra u oculta las carpetas que empiezan por un punto, como `.local`. Navega hasta `~/.local/share/gnome-shell/extensions/miprimeraextension@ejemplo.local` y crea allí `metadata.json` y `extension.js` con el editor.

Si acabas de crear la carpeta y `gnome-extensions list` no muestra la extensión, cierra sesión y vuelve a entrar. GNOME Shell descubre las carpetas nuevas al iniciar la sesión.

#### 1.2.2. `metadata.json`: identidad de la extensión

`metadata.json` es un archivo de texto en formato **JSON**. JSON (JavaScript Object Notation) es un formato de texto para guardar datos estructurados. Usa llaves para agrupar datos, comillas dobles para los nombres y los textos, y corchetes para las listas. No admite comentarios.

Crea un archivo llamado `metadata.json` dentro de la carpeta de la extensión con este contenido:

```json
{
    "uuid": "miprimeraextension@ejemplo.local",
    "name": "Mi Primera Extensión",
    "description": "Una extensión de práctica para aprender GNOME Shell.",
    "shell-version": ["50"]
}
```

> **Qué hace este bloque:** `uuid` es el identificador único y debe coincidir con el nombre de la carpeta. `name` es el nombre visible. `description` explica qué hace la extensión. `shell-version` es una lista de versiones de GNOME Shell compatibles; `["50"]` declara compatibilidad con GNOME 50. La versión puntual 50.6 se usa para probar, pero el metadata declara la versión mayor.

`metadata.json` puede incluir campos opcionales:

- `"url"`: dirección del proyecto. Es obligatoria para publicar en extensions.gnome.org (EGO).
- `"settings-schema"`: identificador del esquema de preferencias. Es opcional; se usa a partir del Capítulo 5.
- `"version-name"`: versión visible para el usuario.
- `"version"`: la gestiona EGO. No la fijes manualmente.

**Errores posibles en este bloque:**

**Error 1: `Could not load extension ...: Error: Failed to parse metadata.json: SyntaxError: JSON.parse: expected double-quoted property name at line 6 column 1 of the JSON data`**
- **Ubicación:** `metadata.json`, línea 6.
- **Síntoma:** el log muestra `Failed to parse metadata.json: SyntaxError: JSON.parse: expected double-quoted property name` y la extensión no aparece.
- **Causa:** dejaste una coma después del último campo o usaste comillas simples.
- **Corrección:** elimina la coma final y usa comillas dobles. Después de corregir, `metadata.json` se lee sin error.

**Error 2: la extensión no aparece en `gnome-extensions list`.**
- **Ubicación:** carpeta de la extensión.
- **Síntoma:** el comando no muestra el UUID.
- **Causa:** el nombre de la carpeta no coincide con `uuid`, el archivo no se llama `metadata.json` o el JSON no es válido.
- **Corrección:** compara el nombre de la carpeta con el campo `uuid`, revisa el nombre del archivo y valida las comas y las comillas. Después de corregir, cierra sesión y vuelve a entrar.

**Error 3: la extensión aparece, pero está marcada como incompatible.**
- **Ubicación:** `metadata.json`, campo `shell-version`.
- **Síntoma:** `gnome-extensions info` muestra un estado de incompatibilidad.
- **Causa:** escribiste `"50.6"` en lugar de `"50"` o usaste una versión que no coincide.
- **Corrección:** usa `["50"]`. Después de corregir, la extensión puede activarse.

#### 1.2.3. `extension.js`: la clase `Extension` y `export default`

`extension.js` es el archivo que GNOME Shell carga al activar la extensión. Debe exportar una clase como exportación por defecto. La clase hereda de `Extension`.

Primero, el import:

```javascript
import {Extension} from 'resource:///org/gnome/shell/extensions/extension.js';
```

> **Qué hace este bloque:** importa la clase `Extension` desde el módulo interno de GNOME Shell. `Extension` es la clase base que GNOME Shell espera. Sin este import, el nombre `Extension` no existe en tu archivo.

**Errores posibles en este bloque:**

**Error 1: `ReferenceError: Extension is not defined`**
- **Ubicación:** `extension.js`, línea `class MiPrimeraExtension extends Extension`.
- **Síntoma:** el log muestra `ReferenceError: Extension is not defined` y la extensión queda en `ERROR`.
- **Causa:** olvidaste el `import` o escribiste mal el nombre.
- **Corrección:** añade `import {Extension} from 'resource:///org/gnome/shell/extensions/extension.js';`. Después de corregir, el nombre `Extension` existe.

**Error 2: `ImportError: Unable to load file from: ...`**
- **Ubicación:** línea del `import`.
- **Síntoma:** el log muestra `ImportError` y la extensión no carga.
- **Causa:** la ruta del recurso está mal escrita.
- **Corrección:** usa exactamente `resource:///org/gnome/shell/extensions/extension.js`, con `shell` en minúscula.

Ahora, la clase:

```javascript
export default class MiPrimeraExtension extends Extension {
    enable() {
        console.log(`[${this.uuid}] activada`);
    }

    disable() {
        console.log(`[${this.uuid}] desactivada`);
    }
}
```

> **Qué hace este bloque:** `export default` indica que esta clase es la exportación principal del archivo. `class MiPrimeraExtension` declara la clase. `extends Extension` hereda de la clase base de GNOME Shell. `enable()` se ejecuta al activar la extensión y escribe un mensaje en el log. `disable()` se ejecuta al desactivarla y escribe otro mensaje. `this.uuid` es el identificador de la extensión, disponible en la clase. En esta etapa, `console.log` se usa para verificar que la extensión carga; en capítulos posteriores se añaden mensajes de error explícitos y herramientas de depuración.

**Errores posibles en este bloque:**

**Error 1: `TypeError: extensionModule.default is not a constructor`**
- **Ubicación:** `extension.js`, línea de la clase.
- **Síntoma:** GNOME Shell registra `TypeError: extensionModule.default is not a constructor` y la extensión queda en `ERROR`.
- **Causa:** olvidaste `export default` o no exportaste una clase.
- **Corrección:** escribe `export default class MiPrimeraExtension extends Extension { ... }`. Después de corregir, GNOME Shell encuentra la clase y puede activarla.

**Error 2: `SyntaxError: Unexpected token '}'`**
- **Ubicación:** `extension.js`, línea indicada por el error.
- **Síntoma:** la extensión no carga y el log muestra un error de sintaxis.
- **Causa:** falta una llave, un paréntesis o un punto y coma.
- **Corrección:** revisa las llaves de `enable()` y `disable()`. Cada método abre y cierra su propio bloque. Después de corregir, el archivo se carga.

**Error 3: no hay mensaje en el log.**
- **Ubicación:** `console.log` dentro de `enable()`.
- **Síntoma:** la extensión aparece activa, pero no ves el mensaje.
- **Causa:** el comando de `journalctl` está mal escrito, la extensión no se activó o el mensaje se escribió antes de abrir el log.
- **Corrección:** abre `journalctl` antes de activar la extensión y comprueba el estado con `gnome-extensions info`. Después de corregir, el mensaje aparece.

**Comprueba que lo entiendes:** explica qué parte del archivo indica a GNOME Shell cuál es la clase principal y qué métodos se ejecutan al activar y desactivar.

### 1.3. El contrato `enable()` / `disable()` y la gestión de memoria

#### 1.3.1. Qué crear en `enable()` y qué destruir en `disable()`

`enable()` se ejecuta cuando la extensión se activa. `disable()` se ejecuta cuando se desactiva, cuando se recarga o cuando se cierra la sesión. Todo recurso creado en `enable()` debe liberarse en `disable()`.

| Recurso | Se crea en `enable()` | Se libera en `disable()` |
| :--- | :--- | :--- |
| Señal | `connect()` | `disconnect()` |
| Temporizador | `GLib.timeout_add()` | `GLib.source_remove()` |
| Actor St | `new St.Label()` | `actor.destroy()` |
| Petición de red | `Soup.Session` | cancelar o abortar |
| Ajuste de GSettings | `connect('changed')` | `disconnect()` |

Una **señal** es un aviso que un objeto emite cuando ocurre algo. Por ejemplo, `window-created` avisa cuando se crea una ventana. Al conectarte a una señal recibes un número de identificación; ese número se usa para desconectarte.

Un **actor St** es un elemento visual, como una etiqueta o un botón. Se destruye con `destroy()`. En el Capítulo 3 crearás tu primer actor; en `disable()` deberás destruirlo. Un **temporizador** ejecuta una función después de un tiempo. Una **petición de red** solicita datos a un servidor. **GSettings** es el sistema de preferencias de GNOME.

#### 1.3.2. Desconexión de señales y destrucción de actores St

Este ejemplo muestra el patrón de conexión y desconexión de una señal. No forma parte de la etapa 1 del proyecto; se presenta para que veas la estructura completa antes de crear interfaces.

```javascript
import {Extension} from 'resource:///org/gnome/shell/extensions/extension.js';

export default class MiPrimeraExtension extends Extension {
    enable() {
        if (this._windowCreatedId)
            global.display.disconnect(this._windowCreatedId);

        this._windowCreatedId = global.display.connect('window-created', () => {
            console.log('Se creó una ventana');
        });

        console.log('Señal conectada');
    }

    disable() {
        if (this._windowCreatedId) {
            global.display.disconnect(this._windowCreatedId);
            this._windowCreatedId = 0;
            console.log('Señal desconectada');
        }
    }
}
```

> **Qué hace este bloque:** `global` es un objeto que GNOME Shell pone a disposición de las extensiones; no se importa. `global.display` representa la pantalla y las ventanas. `connect('window-created', ...)` conecta una función a la señal que avisa cuando se crea una ventana y devuelve un identificador. Ese identificador se guarda en `this._windowCreatedId`. `disable()` comprueba que existe, llama a `disconnect()` y lo vuelve a poner en `0`. La comprobación evita desconectar dos veces el mismo identificador.

**Errores posibles en este bloque:**

**Error 1: los mensajes aparecen duplicados después de activar y desactivar varias veces.**
- **Ubicación:** `enable()`, llamada a `connect()`.
- **Síntoma:** cada ventana nueva genera dos o más mensajes `Se creó una ventana`.
- **Causa:** la señal anterior no se desconectó en `disable()` y se acumulan conexiones.
- **Corrección:** guarda el identificador y llama a `disconnect()` en `disable()`, como muestra el bloque. Después de corregir, cada ventana genera un único mensaje.

**Error 2: `TypeError: global.display.disconnect is not a function`**
- **Ubicación:** `disable()`, línea de `disconnect()`.
- **Síntoma:** el log muestra que `disconnect` no es una función.
- **Causa:** el objeto no es el que emitió la señal o el identificador está mal guardado.
- **Corrección:** usa el mismo objeto que usaste en `connect()` y guarda el identificador devuelto. Después de corregir, la desconexión funciona.

**Error 3: la extensión se desactiva, pero el mensaje sigue apareciendo.**
- **Ubicación:** `disable()`.
- **Síntoma:** después de desactivar, una ventana nueva sigue escribiendo en el log.
- **Causa:** `disable()` no se ejecutó o no desconectó la señal.
- **Corrección:** comprueba el log de desactivación y revisa que `disable()` llame a `disconnect()`. Después de corregir, el mensaje deja de aparecer.

Los actores St siguen la misma lógica. Si creas un botón en `enable()`, guárdalo en una propiedad y destruye ese botón en `disable()`:

```text
enable():  this._boton = new St.Button(...);
disable(): this._boton.destroy();
```

Este bloque es una vista parcial del patrón; el código completo de un botón se presenta en el Capítulo 3. No se ejecuta en este capítulo y no produce errores por sí mismo.

#### 1.3.3. Consecuencias de una limpieza deficiente

GNOME Shell es un proceso compartido. Un **proceso** es un programa en ejecución. Una extensión no se ejecuta en un programa separado: vive dentro del mismo proceso que dibuja el escritorio. Por eso, un recurso que no se libera sigue consumiendo memoria y puede seguir reaccionando a eventos.

Una limpieza deficiente produce:

- **Fugas de memoria:** los objetos creados siguen ocupando espacio.
- **Señales duplicadas:** una acción se ejecuta varias veces.
- **Errores después de desactivar:** una función intenta usar un actor que ya no existe.
- **Ralentización del shell:** el escritorio responde más lento.
- **Conflictos entre extensiones:** dos extensiones modifican el mismo elemento.

La solución no es desactivar la extensión y olvidarse. La solución es que `disable()` deje el shell como lo encontró.

**Comprueba que lo entiendes:** nombra tres recursos que deben limpiarse en `disable()` y explica qué ocurre si no se limpian.

## Verifica que funciona

Abre una terminal y ejecuta:

```bash
gnome-extensions list
```

Salida de ejemplo:

```text
dash-to-dock@micxgx.gmail.com
miprimeraextension@ejemplo.local
```

> **Qué hace este bloque:** `gnome-extensions list` muestra las extensiones instaladas y reconocidas por GNOME Shell. Tu lista puede contener otras extensiones; lo importante es que aparezca la tuya. Si no aparece, revisa la carpeta, el `metadata.json` y cierra sesión para que el shell vuelva a buscar extensiones.

**Errores posibles en este bloque:**

**Error 1: la extensión no aparece en la lista.**
- **Ubicación:** salida del comando.
- **Síntoma:** `miprimeraextension@ejemplo.local` no está en la lista.
- **Causa:** la carpeta no está en `~/.local/share/gnome-shell/extensions/`, el UUID no coincide o el JSON es inválido.
- **Corrección:** comprueba la ruta con `pwd`, el nombre de la carpeta y el campo `uuid`. Después de corregir, cierra sesión y vuelve a entrar.

Ahora activa la extensión:

```bash
gnome-extensions enable miprimeraextension@ejemplo.local
gnome-extensions info miprimeraextension@ejemplo.local
```

Salida de ejemplo:

```text
miprimeraextension@ejemplo.local
  Nombre: Mi Primera Extensión
  Descripción: Una extensión de práctica para aprender GNOME Shell.
  Ruta: /home/ana/.local/share/gnome-shell/extensions/miprimeraextension@ejemplo.local
  Activado: Sí
  Estado: ACTIVE
```

> **Qué hace este bloque:** `gnome-extensions enable` activa la extensión y ejecuta `enable()`. `gnome-extensions info` muestra su nombre, descripción, ruta y estado. `ACTIVE` significa que se cargó correctamente.

**Errores posibles en este bloque:**

**Error 1: `La extensión «miprimeraextension@ejemplo.local» no existe`**
- **Ubicación:** salida de `gnome-extensions enable`.
- **Síntoma:** la terminal muestra que la extensión no existe.
- **Causa:** GNOME Shell no ha reconocido la carpeta, el UUID está mal o el `metadata.json` no es válido.
- **Corrección:** ejecuta `gnome-extensions list`, revisa el UUID y el JSON, y cierra sesión si acabas de crear la carpeta. Después de corregir, el comando encuentra la extensión.

**Error 2: `Estado: ERROR`**
- **Ubicación:** salida de `gnome-extensions info`.
- **Síntoma:** la extensión aparece activada, pero el estado es `ERROR`.
- **Causa:** hay un error de importación, de sintaxis o de exportación.
- **Corrección:** abre otra terminal y ejecuta `journalctl /usr/bin/gnome-shell -f -o cat` para leer el error exacto. Corrige el archivo indicado y vuelve a activar.

Para ver el log, abre una segunda terminal y ejecuta:

```bash
journalctl /usr/bin/gnome-shell -f -o cat
```

> **Qué hace este bloque:** `journalctl` consulta el registro del sistema. `/usr/bin/gnome-shell` limita la salida a los mensajes de GNOME Shell. `-f` mantiene la salida abierta. `-o cat` muestra el texto sin metadatos adicionales.

**Errores posibles en este bloque:**

**Error 1: `No se pudieron leer los mensajes del registro` o `Access denied`.**
- **Ubicación:** salida de `journalctl`.
- **Síntoma:** la terminal no permite leer el registro.
- **Causa:** tu usuario no tiene permiso para leer los mensajes del sistema.
- **Corrección:** consulta la documentación de tu distribución para añadir tu usuario al grupo `systemd-journal`. No uses `sudo` para ejecutar la extensión.

**Error 2: el comando queda abierto y no muestra nada.**
- **Ubicación:** terminal del log.
- **Síntoma:** `journalctl` no termina y no aparece ningún mensaje.
- **Causa:** no ocurrió ningún evento nuevo o el filtro no coincide con el proceso del shell.
- **Corrección:** desactiva y activa la extensión desde otra terminal. Después de corregir, los mensajes aparecen.

Desactiva y vuelve a activar la extensión desde la primera terminal:

```bash
gnome-extensions disable miprimeraextension@ejemplo.local
gnome-extensions enable miprimeraextension@ejemplo.local
```

En la segunda terminal debes ver mensajes parecidos a estos:

```text
GNOME Shell-Message: 10:39:55.082: [miprimeraextension@ejemplo.local] activada
GNOME Shell-Message: 10:39:57.310: [miprimeraextension@ejemplo.local] desactivada
GNOME Shell-Message: 10:40:02.119: [miprimeraextension@ejemplo.local] activada
```

**Errores posibles en este bloque:**

**Error 1: no aparece ningún mensaje.**
- **Ubicación:** salida de `journalctl`.
- **Síntoma:** la extensión está activa, pero el log no muestra los mensajes.
- **Causa:** abriste `journalctl` después de activar la extensión, el comando está mal escrito o la extensión no se recargó.
- **Corrección:** abre `journalctl` antes de activar, desactiva y vuelve a activar. Después de corregir, los mensajes aparecen.

**Error 2: el mensaje de desactivación no aparece.**
- **Ubicación:** `disable()`.
- **Síntoma:** ves `activada`, pero no `desactivada`.
- **Causa:** `disable()` no está definido, tiene un error o la extensión no se desactivó.
- **Corrección:** revisa el método `disable()` y ejecuta `gnome-extensions info` para confirmar que la extensión está desactivada. Después de corregir, el mensaje aparece.

Si acabas de modificar `extension.js` y el cambio no se refleja, desactiva y vuelve a activar. Si sigue sin aparecer, cierra sesión y vuelve a entrar. En Wayland, `Alt+F2` seguido de `r` no reinicia el shell.

## Errores de integración del capítulo

### Error 1: la extensión está en la carpeta equivocada

**Síntoma:** `gnome-extensions list` no muestra la extensión.

**Causa:** guardaste los archivos en `~/Documentos/curso-gnome/` en lugar de `~/.local/share/gnome-shell/extensions/miprimeraextension@ejemplo.local/`.

**Corrección:** mueve la carpeta completa a `~/.local/share/gnome-shell/extensions/`. Después de corregir, cierra sesión y vuelve a entrar.

### Error 2: el UUID de la carpeta no coincide con `metadata.json`

**Síntoma:** la extensión no aparece o aparece con un estado extraño.

**Causa:** la carpeta se llama `mi-extension` y el campo `uuid` dice `miprimeraextension@ejemplo.local`.

**Corrección:** renombra la carpeta para que coincida exactamente con el UUID. Después de corregir, cierra sesión y vuelve a entrar.

### Error 3: el estado es `ERROR` por un import incorrecto

**Síntoma:**

```text
ImportError: Unable to load file from: resource:///org/gnome/Shell/ui/main.js (El recurso en «/org/gnome/Shell/ui/main.js» no existe)
```

**Causa:** escribiste `Shell` con mayúscula en `extension.js`.

**Corrección:** usa `resource:///org/gnome/shell/ui/main.js` con `shell` en minúscula. Después de corregir, la extensión se activa.

### Error 4: falta `export default`

**Síntoma:**

```text
TypeError: extensionModule.default is not a constructor
```

**Causa:** la clase existe, pero no se exportó como exportación por defecto.

**Corrección:** escribe `export default class MiPrimeraExtension extends Extension`. Después de corregir, la extensión se activa.

### Error 5: una señal sigue activa después de desactivar

**Síntoma:** después de desactivar la extensión, una acción sigue generando mensajes en el log.

**Causa:** `disable()` no desconecta la señal.

**Corrección:** guarda el identificador de la conexión y llama a `disconnect()` en `disable()`. Después de corregir, el mensaje deja de aparecer.

## Ejercicios

### Reproductivo

1. Crea la carpeta `~/.local/share/gnome-shell/extensions/miprimeraextension@ejemplo.local`.
2. Crea `metadata.json` con el contenido del capítulo.
3. Crea `extension.js` con la clase y los métodos `enable()` y `disable()`.
4. Cierra sesión y vuelve a entrar si la extensión no aparece en `gnome-extensions list`.
5. Actívala y comprueba el mensaje `activada` en el log.
6. Desactívala y comprueba el mensaje `desactivada`.

### Aplicativo

1. Cambia el mensaje de `enable()` por `Mi Primera Extensión se ha activado`.
2. Cambia el mensaje de `disable()` por `Mi Primera Extensión se ha desactivado`.
3. Cambia el campo `description` de `metadata.json`.
4. Desactiva y activa la extensión.
5. Comprueba que los nuevos mensajes aparecen en el log y que la descripción cambió en `gnome-extensions info`.

### Creativo

1. Crea una segunda extensión con un UUID distinto, por ejemplo `misegundaextension@ejemplo.local`.
2. Haz que su mensaje de activación incluya su propio UUID.
3. Activa las dos extensiones y comprueba que los mensajes aparecen por separado.
4. Desactiva una y verifica que la otra sigue funcionando.
5. Explica por qué las dos carpetas necesitan UUID distintos.

**Rúbrica del ejercicio creativo:**

- La segunda extensión tiene una carpeta propia.
- El campo `uuid` coincide con el nombre de la carpeta.
- El mensaje de log identifica la extensión correcta.
- Las dos extensiones se pueden activar y desactivar de forma independiente.
- La explicación menciona que el UUID evita conflictos entre extensiones.

## Autoevaluación

1. ¿Qué es GJS y en qué se diferencia de Node.js?
2. ¿Por qué `imports.*` ya no funciona en GNOME Shell 50.6?
3. ¿Cuál es la diferencia entre `gi://` y `resource://`?
4. ¿Qué debe hacer `disable()` con los recursos creados en `enable()`?
5. Tu extensión aparece en la lista, pero su estado es `ERROR`. ¿Qué tres cosas revisarías y en qué orden?
6. ¿Por qué una señal no desconectada puede seguir ejecutándose después de desactivar la extensión?

## Respuestas razonadas

1. GJS es el motor de JavaScript de GNOME. Usa SpiderMonkey y GObject Introspection para conectar JavaScript con bibliotecas en C. Node.js también ejecuta JavaScript, pero no tiene las bibliotecas de GNOME ni el objeto `global` del shell; además, usa otro sistema de módulos y otro entorno de ejecución.

2. Porque GNOME Shell adoptó ESM a partir de GNOME 45. `imports.*` era el sistema anterior y se eliminó. El código nuevo usa `import ... from 'gi://...'` o `import ... from 'resource://...'`. Un tutorial anterior a GNOME 45 puede mostrar `imports.*`, pero no es compatible con 50.6.

3. `gi://` carga bibliotecas del sistema expuestas por GObject Introspection, como GLib, Gio, St o Meta. `resource://` carga módulos JavaScript internos de GNOME Shell, como `main.js` o `extension.js`. Las rutas `resource://` distinguen mayúsculas de minúsculas.

4. `disable()` debe desconectar señales, eliminar temporizadores, destruir actores, cancelar peticiones y desconectar ajustes. Debe dejar el shell como estaba antes de activar la extensión.

5. Primero revisaría el log con `journalctl /usr/bin/gnome-shell -f -o cat` para leer el error exacto. Después revisaría la ruta del `import`, porque una mayúscula incorrecta produce `ImportError`. Finalmente comprobaría que la clase tenga `export default` y que el archivo no tenga errores de sintaxis. El orden va del mensaje real a la causa más probable.

6. Porque la conexión sigue registrada en el objeto que emite la señal. Aunque la extensión esté desactivada, el shell conserva la función conectada y la ejecuta cuando ocurre el evento. Para evitarlo, `disable()` debe llamar a `disconnect()`.

## Soluciones de los ejercicios

### Solución del reproductivo

La estructura debe ser:

```text
~/.local/share/gnome-shell/extensions/miprimeraextension@ejemplo.local/
├── metadata.json
└── extension.js
```

`metadata.json`:

```json
{
    "uuid": "miprimeraextension@ejemplo.local",
    "name": "Mi Primera Extensión",
    "description": "Una extensión de práctica para aprender GNOME Shell.",
    "shell-version": ["50"]
}
```

**Errores posibles en este bloque:**

**Error 1: `Failed to parse metadata.json: SyntaxError: JSON.parse: expected double-quoted property name at line 6 column 1`**
- **Ubicación:** `metadata.json`, última línea.
- **Síntoma:** la extensión no aparece en `gnome-extensions list`.
- **Causa:** dejaste una coma después del último campo.
- **Corrección:** elimina la coma final. Después de corregir, el archivo se lee y la extensión aparece.

**Error 2: el UUID no coincide con la carpeta.**
- **Ubicación:** campo `uuid`.
- **Síntoma:** la extensión no aparece o se comporta de forma extraña.
- **Causa:** el nombre de la carpeta y el valor de `uuid` son distintos.
- **Corrección:** haz que coincidan exactamente. Después de corregir, cierra sesión y vuelve a entrar.

`extension.js`:

```javascript
import {Extension} from 'resource:///org/gnome/shell/extensions/extension.js';

export default class MiPrimeraExtension extends Extension {
    enable() {
        console.log(`[${this.uuid}] activada`);
    }

    disable() {
        console.log(`[${this.uuid}] desactivada`);
    }
}
```

**Errores posibles en este bloque:**

**Error 1: `TypeError: extensionModule.default is not a constructor`**
- **Ubicación:** clase del archivo.
- **Síntoma:** la extensión queda en `ERROR`.
- **Causa:** falta `export default`.
- **Corrección:** exporta la clase como `export default class MiPrimeraExtension extends Extension`. Después de corregir, la extensión se activa.

**Error 2: `ImportError: Unable to load file from: ...`**
- **Ubicación:** línea del `import`.
- **Síntoma:** la extensión no carga.
- **Causa:** la ruta del recurso está mal escrita.
- **Corrección:** usa `resource:///org/gnome/shell/extensions/extension.js` con `shell` en minúscula. Después de corregir, el módulo se carga.

Al activar, el log debe mostrar un mensaje con `activada`. Al desactivar, debe mostrar `desactivada`.

### Solución del aplicativo

Los mensajes deben cambiar a:

```javascript
enable() {
    console.log('Mi Primera Extensión se ha activado');
}

disable() {
    console.log('Mi Primera Extensión se ha desactivado');
}
```

**Errores posibles en este bloque:**

**Error 1: `SyntaxError: Unexpected token '}'`**
- **Ubicación:** línea indicada por el log.
- **Síntoma:** la extensión no carga.
- **Causa:** los métodos se copiaron fuera de la clase o falta una llave.
- **Corrección:** coloca los métodos dentro de `export default class ... extends Extension` y cierra cada método. Después de corregir, el archivo se carga.

**Error 2: sigue apareciendo el mensaje anterior.**
- **Ubicación:** log de GNOME Shell.
- **Síntoma:** el mensaje nuevo no aparece.
- **Causa:** GNOME Shell conserva el módulo anterior.
- **Corrección:** desactiva y activa la extensión; si persiste, cierra sesión y vuelve a entrar. Después de corregir, aparece el mensaje nuevo.

La descripción de `metadata.json` debe ser la nueva. `gnome-extensions info miprimeraextension@ejemplo.local` debe mostrar la descripción actualizada.

### Solución del creativo

Una solución válida crea una segunda carpeta:

```text
~/.local/share/gnome-shell/extensions/misegundaextension@ejemplo.local/
├── metadata.json
└── extension.js
```

El `metadata.json` debe tener:

```json
{
    "uuid": "misegundaextension@ejemplo.local",
    "name": "Mi Segunda Extensión",
    "description": "Una segunda extensión de práctica.",
    "shell-version": ["50"]
}
```

**Errores posibles en este bloque:**

**Error 1: `Failed to parse metadata.json: SyntaxError: JSON.parse: expected double-quoted property name`**
- **Ubicación:** `metadata.json`, última línea.
- **Síntoma:** la segunda extensión no aparece.
- **Causa:** coma final o comillas simples.
- **Corrección:** elimina la coma final y usa comillas dobles. Después de corregir, el archivo se lee.

**Error 2: las dos extensiones comparten el mismo UUID.**
- **Ubicación:** campo `uuid`.
- **Síntoma:** una extensión sustituye a la otra o no aparecen las dos.
- **Causa:** copiaste el UUID de la primera extensión.
- **Corrección:** usa un UUID distinto, como `misegundaextension@ejemplo.local`. Después de corregir, las dos aparecen por separado.

El `extension.js` puede mostrar su propio UUID:

```javascript
import {Extension} from 'resource:///org/gnome/shell/extensions/extension.js';

export default class MiSegundaExtension extends Extension {
    enable() {
        console.log(`[${this.uuid}] segunda extensión activada`);
    }

    disable() {
        console.log(`[${this.uuid}] segunda extensión desactivada`);
    }
}
```

**Errores posibles en este bloque:**

**Error 1: `TypeError: extensionModule.default is not a constructor`**
- **Ubicación:** clase del archivo.
- **Síntoma:** la segunda extensión queda en `ERROR`.
- **Causa:** falta `export default` o el nombre de la clase no se exportó.
- **Corrección:** escribe `export default class MiSegundaExtension extends Extension`. Después de corregir, la extensión se activa.

**Error 2: `ImportError: Unable to load file from: ...`**
- **Ubicación:** línea del `import`.
- **Síntoma:** la segunda extensión no carga.
- **Causa:** la ruta del recurso está mal escrita.
- **Corrección:** usa `resource:///org/gnome/shell/extensions/extension.js`. Después de corregir, el módulo se carga.

Las dos extensiones se activan y desactivan de forma independiente porque tienen UUID y carpetas distintas.

## Si quieres saber más

- Guía de GJS para extensiones: <https://gjs.guide/extensions/>
- Documentación de GNOME Shell: <https://gnome.pages.gitlab.gnome.org/gnome-shell/>
- Referencia de GObject Introspection: <https://gi.readthedocs.io/>

No necesitas estos enlaces para continuar. El manual contiene lo necesario para el proyecto.

## Siguiente paso

Cuando tu extensión escriba los mensajes en el log y puedas activarla y desactivarla sin errores, continúa con el Capítulo 2. Allí importarás módulos internos del shell y mostrarás una notificación.
