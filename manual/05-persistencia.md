# Capítulo 5: Persistencia y Preferencias del Usuario

> En este capítulo harás que el color del botón se guarde. El usuario podrá cambiarlo desde una ventana de preferencias y el valor seguirá ahí después de cerrar sesión.

## Qué vas a aprender

- Crear un esquema de GSettings con un color.
- Compilar el esquema con `glib-compile-schemas`.
- Leer y escribir el color desde `extension.js`.
- Reaccionar al cambio con la señal `changed::color`.
- Construir una ventana de preferencias con `prefs.js`.
- Vincular un `Gtk.Entry` a GSettings con `settings.bind()`.
- Entender por qué `stylesheet.css` no se aplica a la ventana de preferencias.
- Verificar que el color persiste tras cerrar sesión.

Al final del capítulo, el botón del panel usará un color guardado en GSettings y la ventana de preferencias permitirá cambiarlo.

## Antes de empezar necesitas

- Haber completado los capítulos 1 a 4.
- Tener el botón del panel con la clase `mi-primera-extension`.
- Saber activar, desactivar y recargar la extensión.
- GNOME Shell 50.6 sobre Wayland.

## El concepto, con una analogía

GSettings es como un cuaderno de preferencias del sistema. El **esquema** es el formulario impreso: dice qué campos existen, de qué tipo son y qué valor tienen por defecto. La ventana de preferencias es el formulario que el usuario rellena. dconf es el archivador donde se guardan los valores.

La extensión no guarda el color en una variable de JavaScript, porque esa variable desaparece al cerrar sesión. Lo guarda en GSettings, que conserva el valor entre sesiones.

### 5.1. GSettings: creación manual del esquema XML

#### 5.1.1. Convención de ID y ruta

Un esquema de extensión usa un identificador con esta forma:

```text
org.gnome.shell.extensions.miprimeraextension
```

La ruta asociada suele ser:

```text
/org/gnome/shell/extensions/miprimeraextension/
```

El identificador del esquema no tiene que ser igual al UUID de la extensión, pero debe ser único. El campo `settings-schema` de `metadata.json` debe coincidir exactamente con el identificador del esquema.

**Errores posibles en este bloque:** este bloque es una explicación de nombres, no un programa. No produce errores por sí mismo. Si el identificador no coincide, `getSettings()` no encuentra el esquema.

#### 5.1.2. Definición de claves: tipos `s`, `b` e `i`

Cada clave tiene un nombre, un tipo y un valor por defecto. Los tipos más usados son:

| Tipo | Significado | Ejemplo |
| :--- | :--- | :--- |
| `s` | Cadena de texto | `'#3584e4'` |
| `b` | Booleano | `true` o `false` |
| `i` | Número entero | `12` |

Crea la carpeta `schemas` dentro de la extensión y guarda este archivo como `schemas/org.gnome.shell.extensions.miprimeraextension.gschema.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<schemalist>
    <schema id="org.gnome.shell.extensions.miprimeraextension"
            path="/org/gnome/shell/extensions/miprimeraextension/">
        <key name="color" type="s">
            <default>'#3584e4'</default>
            <summary>Color del botón</summary>
            <description>Color hexadecimal del botón del panel.</description>
        </key>
    </schema>
</schemalist>
```

> **Qué hace este bloque:** `<schemalist>` agrupa los esquemas. `<schema>` define un esquema con su identificador y su ruta. `<key name="color" type="s">` declara una clave de texto llamada `color`. `<default>'#3584e4'</default>` fija el valor inicial. `<summary>` y `<description>` documentan la clave. Las comillas simples forman parte del valor por defecto de una cadena.

**Errores posibles en este bloque:**

**Error 1: el tipo de la clave no coincide con el valor por defecto.**
- **Ubicación:** línea `<key>` o `<default>`.
- **Síntoma:** `glib-compile-schemas` muestra un error de análisis y no genera el archivo compilado.
- **Causa:** usaste un tipo como `x` o un valor que no corresponde al tipo.
- **Corrección:** usa `type="s"` para texto y escribe el valor entre comillas simples. Después de corregir, el esquema se compila.

**Error 2: el identificador del esquema no coincide con `metadata.json`.**
- **Ubicación:** atributo `id` del esquema.
- **Síntoma:** `getSettings()` no encuentra el esquema.
- **Causa:** el campo `settings-schema` de `metadata.json` tiene otro nombre.
- **Corrección:** usa el mismo identificador en los dos archivos. Después de corregir, la extensión encuentra la configuración.

#### 5.1.3. Compilación manual con `glib-compile-schemas`

GSettings necesita un archivo compilado llamado `gschemas.compiled`. Se genera con:

```bash
glib-compile-schemas schemas/
```

> **Qué hace este bloque:** `glib-compile-schemas` lee los archivos `.gschema.xml` de la carpeta `schemas/` y genera `gschemas.compiled`. La opción no es un archivo; el argumento es la carpeta que contiene los esquemas. Si todo está correcto, el comando termina sin mostrar nada.

**Errores posibles en este bloque:**

**Error 1: `glib-compile-schemas: instrucción no encontrada...`**
- **Ubicación:** línea de la terminal.
- **Síntoma:** la terminal no encuentra la herramienta.
- **Causa:** GLib no está instalado o la herramienta no está en el `PATH`.
- **Corrección:** instala las herramientas de desarrollo de GLib de tu distribución. Después de corregir, el comando está disponible.

**Error 2: `Falló al analizar el valor <default> del tipo «x» ... Se ha ignorado este archivo completamente.`**
- **Ubicación:** archivo `.gschema.xml`, línea indicada.
- **Síntoma:** el archivo no se compila.
- **Causa:** el tipo de la clave no coincide con el valor por defecto.
- **Corrección:** corrige el tipo o el valor y vuelve a ejecutar el comando. Después de corregir, se genera `gschemas.compiled`.

**Error 3: el archivo se compila, pero la extensión no encuentra el esquema.**
- **Ubicación:** carpeta `schemas/`.
- **Síntoma:** la extensión queda en `ERROR` con un mensaje sobre el esquema.
- **Causa:** el archivo compilado no está en `schemas/`, el nombre del esquema no coincide o el campo `settings-schema` falta.
- **Corrección:** comprueba los tres puntos y vuelve a compilar. Después de corregir, `getSettings()` funciona.

### 5.2. Lectura y escritura en `extension.js`

#### 5.2.1. `this.getSettings()` y la vinculación con `metadata.json`

La clase `Extension` ofrece `getSettings()`. El método busca el esquema indicado en el campo `settings-schema` de `metadata.json`.

Añade el campo a `metadata.json`:

```json
{
    "uuid": "miprimeraextension@ejemplo.local",
    "name": "Mi Primera Extensión",
    "description": "Una extensión de práctica para aprender GNOME Shell.",
    "shell-version": ["50"],
    "settings-schema": "org.gnome.shell.extensions.miprimeraextension"
}
```

> **Qué hace este bloque:** `settings-schema` indica a `getSettings()` qué esquema debe cargar. El valor debe coincidir con el atributo `id` del archivo `.gschema.xml`. Los demás campos siguen siendo los mismos del Capítulo 1.

**Errores posibles en este bloque:**

**Error 1: `Error: Expected type string for argument 'schema_id' but got type undefined`**
- **Ubicación:** `metadata.json`.
- **Síntoma:** la extensión queda en `ERROR` al llamar a `getSettings()`.
- **Causa:** falta el campo `settings-schema`.
- **Corrección:** añade el campo con el identificador del esquema. Después de corregir, `getSettings()` recibe un texto.

**Error 2: `Error: Schema org.gnome.shell.extensions.miprimeraextension could not be found for extension ...`**
- **Ubicación:** carpeta `schemas/` o `metadata.json`.
- **Síntoma:** la extensión no carga la configuración.
- **Causa:** el esquema no está compilado, el identificador no coincide o el archivo compilado no está en la carpeta.
- **Corrección:** ejecuta `glib-compile-schemas schemas/` y comprueba el identificador. Después de corregir, la configuración se carga.

Ahora, en `enable()`, carga la configuración y conecta la señal de cambio:

```javascript
try {
    this._settings = this.getSettings();
} catch (error) {
    console.error(`No se pudo cargar la configuración: ${error.message}`);
    return;
}

this._colorChangedId = this._settings.connect('changed::color', () => {
    this._actualizarColor();
});
```

> **Qué hace este bloque:** `getSettings()` carga el esquema. `try/catch` captura el error si el esquema no existe o no está compilado. `connect('changed::color', ...)` ejecuta la función cuando cambia la clave `color`. El identificador de la conexión se guarda para desconectarla en `disable()`.

**Errores posibles en este bloque:**

**Error 1: `No se pudo cargar la configuración: ...`**
- **Ubicación:** `enable()`.
- **Síntoma:** el log muestra el mensaje y la extensión no crea el botón.
- **Causa:** el esquema no está compilado, el campo `settings-schema` falta o el identificador no coincide.
- **Corrección:** corrige el esquema o el metadata y vuelve a activar. Después de corregir, la configuración se carga.

**Error 2: la señal no se ejecuta.**
- **Ubicación:** `connect('changed::color', ...)`.
- **Síntoma:** cambias el color con `gsettings`, pero el botón no cambia.
- **Causa:** el nombre de la clave no coincide o la señal se conectó antes de cargar la configuración.
- **Corrección:** usa exactamente `changed::color` y conecta después de `getSettings()`. Después de corregir, el cambio se propaga.

#### 5.2.2. La señal `'changed'` para reacción en tiempo real

La función que aplica el color lee el valor actual y lo escribe en el botón. Coloca `COLOR_PATTERN` antes de `export default class` y `_actualizarColor()` dentro de la clase:

```javascript
const COLOR_PATTERN = /^#[0-9a-fA-F]{6}$/;

_actualizarColor() {
    if (!this._indicador || !this._settings)
        return;

    const color = this._settings.get_string('color');

    if (!COLOR_PATTERN.test(color)) {
        console.error(`Color no válido: ${color}`);
        return;
    }

    this._indicador.set_style(`background-color: ${color};`);
}
```

> **Qué hace este bloque:** `COLOR_PATTERN` es una expresión regular que comprueba el formato `#RRGGBB`. `^` marca el inicio, `#` exige el símbolo, `[0-9a-fA-F]{6}` exige seis dígitos hexadecimales y `$` marca el final. `get_string('color')` lee el valor. `test(color)` devuelve `true` si el formato es válido. `set_style` aplica el color al botón. El punto y coma dentro de la plantilla cierra la declaración CSS.

**Errores posibles en este bloque:**

**Error 1: `Color no válido: ...`**
- **Ubicación:** `_actualizarColor()`.
- **Síntoma:** el color no cambia y el log muestra el valor rechazado.
- **Causa:** el valor no tiene formato `#RRGGBB`.
- **Corrección:** escribe un color hexadecimal de seis dígitos, por ejemplo `#e66100`. Después de corregir, el color se aplica.

**Error 2: `TypeError: this._indicador.set_style is not a function`**
- **Ubicación:** `_actualizarColor()`.
- **Síntoma:** el log muestra que `set_style` no existe.
- **Causa:** `this._indicador` no es un widget de St o todavía no se creó.
- **Corrección:** llama a `_actualizarColor()` después de crear el botón. Después de corregir, el estilo se aplica.

**Error 3: el color no cambia en tiempo real.**
- **Ubicación:** señal `changed::color`.
- **Síntoma:** el valor cambia en GSettings, pero el botón conserva el color anterior.
- **Causa:** la señal no está conectada o el identificador se perdió.
- **Corrección:** conecta `changed::color` y comprueba el log de `_actualizarColor`. Después de corregir, el botón cambia.

#### 5.2.3. Limpieza en `disable()`

Al desactivar, desconecta la señal y destruye el botón:

```javascript
disable() {
    if (this._colorChangedId) {
        this._settings.disconnect(this._colorChangedId);
        this._colorChangedId = 0;
    }

    this._indicador?.destroy();
    this._indicador = null;
    this._settings = null;
}
```

> **Qué hace este bloque:** `disconnect` elimina la conexión a `changed::color`. El identificador se pone en `0` para no desconectarlo dos veces. Después se destruye el botón y se liberan las referencias. Si la señal queda conectada, la función sigue ejecutándose después de desactivar.

**Errores posibles en este bloque:**

**Error 1: la función se ejecuta después de desactivar.**
- **Ubicación:** `disable()`.
- **Síntoma:** cambias el color y el log sigue mostrando mensajes aunque la extensión esté desactivada.
- **Causa:** no se desconectó `changed::color`.
- **Corrección:** desconecta el identificador guardado. Después de corregir, la señal deja de ejecutarse.

**Error 2: `TypeError: this._settings.disconnect is not a function`**
- **Ubicación:** `disable()`.
- **Síntoma:** el log muestra que `disconnect` no existe.
- **Causa:** `this._settings` se sobrescribió o se puso en `null` antes de desconectar.
- **Corrección:** desconecta antes de asignar `null`. Después de corregir, la desconexión funciona.

### 5.3. Construcción de la ventana de preferencias (`prefs.js`)

#### 5.3.1. La clase `ExtensionPreferences` y `fillPreferencesWindow()`

La ventana de preferencias se escribe en un archivo llamado `prefs.js`, en la raíz de la extensión. Se ejecuta en un proceso separado y usa GTK4 y libadwaita.

Los imports son:

```javascript
import Adw from 'gi://Adw';
import Gio from 'gi://Gio';
import Gtk from 'gi://Gtk?version=4.0';
import {ExtensionPreferences} from 'resource:///org/gnome/Shell/Extensions/js/extensions/prefs.js';
```

> **Qué hace este bloque:** `Adw` carga libadwaita, que ofrece las páginas y filas de preferencias. `Gio` se usa para `Gio.SettingsBindFlags`. `Gtk` carga GTK4 con la versión explícita. `ExtensionPreferences` es la clase base del proceso de preferencias. Observa que la ruta usa `Shell/Extensions` con mayúsculas; es distinta de la ruta de `extension.js`.

**Errores posibles en este bloque:**

**Error 1: `ImportError: Unable to load file from: resource:///org/gnome/shell/Extensions/js/extensions/prefs.js`**
- **Ubicación:** línea del import de `ExtensionPreferences`.
- **Síntoma:** la ventana de preferencias no abre.
- **Causa:** usaste `shell/extensions` en minúscula.
- **Corrección:** usa `resource:///org/gnome/Shell/Extensions/js/extensions/prefs.js`. Después de corregir, el módulo se carga.

**Error 2: `ImportError` en `gi://Gtk`**
- **Ubicación:** línea del import de Gtk.
- **Síntoma:** la ventana de preferencias no abre.
- **Causa:** falta la versión o GTK4 no está instalado.
- **Corrección:** usa `gi://Gtk?version=4.0` y comprueba que GTK4 está instalado. Después de corregir, el módulo se carga.

La clase principal se declara así:

```javascript
export default class MiPrimeraExtensionPreferences extends ExtensionPreferences {
    fillPreferencesWindow(window) {
        const settings = this.getSettings();
        // Aquí se construye la interfaz.
    }
}
```

> **Qué hace este bloque:** `export default` exporta la clase de preferencias. `extends ExtensionPreferences` hereda la clase base. `fillPreferencesWindow(window)` recibe la ventana de preferencias y la rellena. `this.getSettings()` carga el mismo esquema que usa `extension.js`.

**Errores posibles en este bloque:**

**Error 1: `TypeError: extensionModule.default is not a constructor`**
- **Ubicación:** `prefs.js`.
- **Síntoma:** la ventana de preferencias no abre.
- **Causa:** falta `export default` o la clase no hereda de `ExtensionPreferences`.
- **Corrección:** exporta una clase que herede de `ExtensionPreferences`. Después de corregir, la ventana abre.

**Error 2: la ventana abre vacía.**
- **Ubicación:** `fillPreferencesWindow`.
- **Síntoma:** no aparece ninguna página ni fila.
- **Causa:** no se añadió ningún widget a la ventana.
- **Corrección:** añade una página y una fila antes de terminar el método. Después de corregir, la ventana muestra la configuración.

#### 5.3.2. `Adw.PreferencesPage`, `Adw.PreferencesGroup` y `Adw.ActionRow`

La interfaz se organiza en página, grupo y fila:

```javascript
const page = new Adw.PreferencesPage();
const group = new Adw.PreferencesGroup({
    title: 'Apariencia',
    description: 'Elige el color del botón del panel.',
});
page.add(group);
window.add(page);
```

> **Qué hace este bloque:** `Adw.PreferencesPage` es la página principal. `Adw.PreferencesGroup` agrupa opciones relacionadas y muestra un título y una descripción. `page.add(group)` coloca el grupo en la página. `window.add(page)` coloca la página en la ventana. Sin `window.add(page)`, la ventana queda vacía.

**Errores posibles en este bloque:**

**Error 1: la página no aparece.**
- **Ubicación:** `fillPreferencesWindow`.
- **Síntoma:** la ventana abre, pero está vacía.
- **Causa:** no llamaste a `window.add(page)`.
- **Corrección:** añade la página a la ventana. Después de corregir, la página aparece.

**Error 2: `TypeError: page.add is not a function`**
- **Ubicación:** línea `page.add(group)`.
- **Síntoma:** la ventana no abre.
- **Causa:** `page` no es un `Adw.PreferencesPage`.
- **Corrección:** crea la página con `new Adw.PreferencesPage()`. Después de corregir, el grupo se añade.

#### 5.3.3. Widgets: `Gtk.Switch`, `Gtk.SpinButton` y `Gtk.Entry`

Cada tipo de dato tiene un widget adecuado:

| Dato | Widget | Ejemplo de clave |
| :--- | :--- | :--- |
| Booleano | `Gtk.Switch` | `true` o `false` |
| Número entero | `Gtk.SpinButton` | `12` |
| Texto | `Gtk.Entry` | `'#3584e4'` |

Para el color usarás `Gtk.Entry`, porque el valor es una cadena de texto.

```javascript
const row = new Adw.ActionRow({
    title: 'Color del botón',
    subtitle: 'Formato hexadecimal, por ejemplo #3584e4.',
});

const entry = new Gtk.Entry({
    text: settings.get_string('color'),
    valign: Gtk.Align.CENTER,
    width_chars: 10,
});

row.add_suffix(entry);
group.add(row);
```

> **Qué hace este bloque:** `Adw.ActionRow` crea una fila con título y subtítulo. `Gtk.Entry` crea un campo de texto. `text` muestra el valor actual. `valign` centra el campo verticalmente. `width_chars` controla el ancho. `row.add_suffix(entry)` coloca el campo al final de la fila. `group.add(row)` añade la fila al grupo.

**Errores posibles en este bloque:**

**Error 1: el campo no aparece en la fila.**
- **Ubicación:** línea `row.add_suffix(entry)`.
- **Síntoma:** la fila aparece, pero sin campo de texto.
- **Causa:** no añadiste el widget a la fila.
- **Corrección:** llama a `row.add_suffix(entry)`. Después de corregir, el campo aparece.

**Error 2: `TypeError: Gtk.Entry is not a constructor`**
- **Ubicación:** línea `new Gtk.Entry`.
- **Síntoma:** la ventana de preferencias no abre.
- **Causa:** importaste GTK sin la versión correcta o el módulo no es GTK4.
- **Corrección:** usa `gi://Gtk?version=4.0`. Después de corregir, el widget se crea.

#### 5.3.4. Vinculación de widgets a GSettings con `settings.bind()`

`settings.bind()` sincroniza una clave con una propiedad de un widget:

```javascript
settings.bind(
    'color',
    entry,
    'text',
    Gio.SettingsBindFlags.DEFAULT
);
```

> **Qué hace este bloque:** `'color'` es la clave de GSettings. `entry` es el widget. `'text'` es la propiedad del widget. `Gio.SettingsBindFlags.DEFAULT` indica una vinculación bidireccional: si cambia el ajuste, cambia el campo; si el usuario escribe, cambia el ajuste. No necesitas escribir un callback para guardar el valor. El campo no valida el formato; la validación real está en `_actualizarColor()`.

**Errores posibles en este bloque:**

**Error 1: el campo no guarda el valor.**
- **Ubicación:** llamada a `settings.bind`.
- **Síntoma:** escribes un color y el ajuste no cambia.
- **Causa:** la propiedad del widget no se llama `text` o la clave no existe.
- **Corrección:** usa `'text'` y comprueba que la clave `color` existe en el esquema. Después de corregir, el valor se guarda.

**Error 2: `TypeError: settings.bind is not a function`**
- **Ubicación:** línea `settings.bind`.
- **Síntoma:** la ventana no abre.
- **Causa:** `settings` no es un objeto `Gio.Settings`.
- **Corrección:** usa `this.getSettings()` en `fillPreferencesWindow`. Después de corregir, la vinculación funciona.

**Comprueba que lo entiendes:** explica qué parte de `prefs.js` crea la página, qué parte crea el campo y qué parte guarda el valor en GSettings.

### 5.4. CSS en `prefs.js`: el mundo GTK4

#### 5.4.1. Separación estricta: el `stylesheet.css` de la extensión no aplica aquí

El `stylesheet.css` de la extensión se aplica a los actores de St en el proceso del shell. La ventana de preferencias se ejecuta en otro proceso y usa GTK4 y libadwaita. Por eso las clases CSS de `extension.js` no afectan a `prefs.js`.

No necesitas CSS propio para esta ventana: libadwaita ya aplica el estilo del sistema. Si añades CSS, debe ser CSS de GTK4, no CSS de St.

**Errores posibles en este bloque:** este bloque es una explicación, no un programa. No produce errores por sí mismo. Si usas `stylesheet.css` para intentar estilizar la ventana de preferencias, el estilo no se aplica.

#### 5.4.2. Uso de `Gtk.CssProvider` para estilos personalizados

Si necesitas un estilo propio en la ventana de preferencias, GTK4 ofrece `Gtk.CssProvider`. Se usa para cargar una hoja de estilos GTK y aplicarla al `Gdk.Display`. Este manual no lo necesita para el proyecto.

**Errores posibles en este bloque:** este bloque es una explicación, no un programa. No produce errores por sí mismo. Si cargas CSS de St en GTK4, las propiedades no coinciden y el estilo se ignora.

## Ejemplo guiado paso a paso

Vas a integrar la configuración en la extensión. El botón ya existe; ahora leerá el color de GSettings y reaccionará a los cambios.

### Bloque 1: crear y compilar el esquema

Crea `schemas/org.gnome.shell.extensions.miprimeraextension.gschema.xml` con el contenido de la sección 5.1.2 y ejecuta:

```bash
glib-compile-schemas schemas/
```

> **Qué hace este bloque:** compila el esquema y genera `schemas/gschemas.compiled`. Sin este archivo, `getSettings()` no encuentra la configuración.

**Errores posibles en este bloque:**

**Error 1: `Falló al analizar el valor <default> ...`**
- **Ubicación:** archivo `.gschema.xml`.
- **Síntoma:** no se genera `gschemas.compiled`.
- **Causa:** el tipo y el valor por defecto no coinciden.
- **Corrección:** corrige el tipo o el valor. Después de corregir, el esquema se compila.

**Error 2: el archivo se genera, pero la extensión no lo encuentra.**
- **Ubicación:** carpeta `schemas/`.
- **Síntoma:** `getSettings()` falla.
- **Causa:** el archivo compilado no está en la carpeta o el identificador no coincide.
- **Corrección:** comprueba la ruta y el identificador. Después de corregir, la configuración se carga.

### Bloque 2: cargar la configuración en `enable()`

Añade al principio de `enable()`:

```javascript
try {
    this._settings = this.getSettings();
} catch (error) {
    console.error(`No se pudo cargar la configuración: ${error.message}`);
    return;
}

this._colorChangedId = this._settings.connect('changed::color', () => {
    this._actualizarColor();
});
```

> **Qué hace este bloque:** carga el esquema, captura el error si no existe y conecta el cambio de color. La función `_actualizarColor` se ejecuta cada vez que cambia la clave.

**Errores posibles en este bloque:**

**Error 1: `No se pudo cargar la configuración: ...`**
- **Ubicación:** `enable()`.
- **Síntoma:** la extensión no crea el botón.
- **Causa:** el esquema no está compilado o el metadata no tiene `settings-schema`.
- **Corrección:** corrige el esquema o el metadata. Después de corregir, la extensión se activa.

**Error 2: la señal no se ejecuta.**
- **Ubicación:** `connect('changed::color', ...)`.
- **Síntoma:** el color no cambia en tiempo real.
- **Causa:** la clave o la señal están mal escritas.
- **Corrección:** usa `changed::color`. Después de corregir, el cambio se propaga.

### Bloque 3: aplicar el color y limpiar

Añade el método `_actualizarColor()` y actualiza `disable()`:

```javascript
const COLOR_PATTERN = /^#[0-9a-fA-F]{6}$/;

_actualizarColor() {
    if (!this._indicador || !this._settings)
        return;

    const color = this._settings.get_string('color');

    if (!COLOR_PATTERN.test(color)) {
        console.error(`Color no válido: ${color}`);
        return;
    }

    this._indicador.set_style(`background-color: ${color};`);
}
```

> **Qué hace este bloque:** valida el formato del color y lo aplica al botón con `set_style`. Si el valor no es válido, escribe un error y no cambia el estilo.

**Errores posibles en este bloque:**

**Error 1: `Color no válido: ...`**
- **Ubicación:** `_actualizarColor()`.
- **Síntoma:** el botón conserva el color anterior.
- **Causa:** el valor no tiene seis dígitos hexadecimales.
- **Corrección:** usa un valor como `#e66100`. Después de corregir, el color se aplica.

**Error 2: `TypeError: this._indicador.set_style is not a function`**
- **Ubicación:** `_actualizarColor()`.
- **Síntoma:** la extensión queda en `ERROR`.
- **Causa:** el botón no se creó antes de llamar al método.
- **Corrección:** llama a `_actualizarColor()` después de crear el botón. Después de corregir, el estilo se aplica.

El método `disable()` debe desconectar la señal antes de destruir el botón:

```javascript
disable() {
    if (this._colorChangedId) {
        this._settings.disconnect(this._colorChangedId);
        this._colorChangedId = 0;
    }

    this._indicador?.destroy();
    this._indicador = null;
    this._settings = null;
}
```

> **Qué hace este bloque:** desconecta `changed::color`, destruye el botón y libera la configuración. La desconexión evita que la función se ejecute después de desactivar.

**Errores posibles en este bloque:**

**Error 1: la señal sigue activa.**
- **Ubicación:** `disable()`.
- **Síntoma:** el log sigue mostrando cambios de color después de desactivar.
- **Causa:** no se desconectó la señal.
- **Corrección:** desconecta `this._colorChangedId` antes de destruir el botón. Después de corregir, la señal deja de ejecutarse.

**Error 2: `TypeError: this._settings.disconnect is not a function`**
- **Ubicación:** `disable()`.
- **Síntoma:** el log muestra que `disconnect` no existe.
- **Causa:** `this._settings` se puso en `null` antes de desconectar.
- **Corrección:** desconecta antes de asignar `null`. Después de corregir, la desconexión funciona.

### Guarda y recarga

Guarda los archivos, compila el esquema y recarga la extensión:

```bash
glib-compile-schemas schemas/
gnome-extensions disable miprimeraextension@ejemplo.local
gnome-extensions enable miprimeraextension@ejemplo.local
```

**Errores posibles en este bloque:**

**Error 1: `glib-compile-schemas: instrucción no encontrada...`**
- **Ubicación:** terminal.
- **Síntoma:** no se genera el archivo compilado.
- **Causa:** faltan las herramientas de GLib.
- **Corrección:** instala las herramientas de desarrollo de GLib. Después de corregir, el comando funciona.

**Error 2: la extensión no se activa.**
- **Ubicación:** `gnome-extensions enable`.
- **Síntoma:** la extensión queda en `ERROR`.
- **Causa:** el esquema no se compiló o el metadata no coincide.
- **Corrección:** revisa el log y compila de nuevo. Después de corregir, la extensión se activa.

### Bloque 4: crear la ventana de preferencias

Crea `prefs.js` con los imports y la estructura de la sección 5.3. Integra los bloques en el orden en que aparecen. La ventana debe mostrar una página llamada `Apariencia`, un grupo con el título `Color del botón` y un campo de texto.

**Errores posibles en este bloque:**

**Error 1: la ventana no abre.**
- **Ubicación:** `prefs.js`.
- **Síntoma:** `gnome-extensions prefs` no muestra nada.
- **Causa:** el import de `ExtensionPreferences` usa la ruta equivocada o falta `export default`.
- **Corrección:** usa la ruta con `Shell/Extensions` y exporta la clase. Después de corregir, la ventana abre.

**Error 2: la ventana abre vacía.**
- **Ubicación:** `fillPreferencesWindow`.
- **Síntoma:** no aparece ninguna fila.
- **Causa:** falta `window.add(page)` o no se añadió la fila al grupo.
- **Corrección:** añade la página a la ventana y la fila al grupo. Después de corregir, la fila aparece.

## Verifica que funciona

Comprueba el valor actual:

```bash
gsettings --schemadir schemas/ get org.gnome.shell.extensions.miprimeraextension color
```

Salida de ejemplo:

```text
'#3584e4'
```

> **Qué hace este bloque:** `gsettings` consulta el valor de la clave `color` usando el esquema de la carpeta `schemas/`. Las comillas simples forman parte de la salida de un valor de texto.

**Errores posibles en este bloque:**

**Error 1: `No such schema “org.gnome.shell.extensions.miprimeraextension”`**
- **Ubicación:** terminal.
- **Síntoma:** el esquema no se encuentra.
- **Causa:** `--schemadir` apunta a otra carpeta o el archivo no se compiló.
- **Corrección:** ejecuta `glib-compile-schemas schemas/` y revisa la ruta. Después de corregir, el valor aparece.

**Error 2: el valor no es el esperado.**
- **Ubicación:** salida de `gsettings`.
- **Síntoma:** aparece otro color.
- **Causa:** el usuario o una prueba anterior cambiaron el valor.
- **Corrección:** usa `gsettings ... set` para escribir el color deseado. Después de corregir, el valor coincide.

Activa la extensión y cambia el color:

```bash
gnome-extensions enable miprimeraextension@ejemplo.local
gsettings --schemadir schemas/ set org.gnome.shell.extensions.miprimeraextension color '#e66100'
```

El botón debe cambiar a naranja. En el log debes ver:

```text
GNOME Shell-Message: 11:23:49.807: [miprimeraextension@ejemplo.local] color aplicado: #3584e4
GNOME Shell-Message: 11:23:50.823: [miprimeraextension@ejemplo.local] color aplicado: #e66100
```

**Errores posibles en este bloque:**

**Error 1: el botón no cambia.**
- **Ubicación:** panel superior.
- **Síntoma:** el color sigue igual.
- **Causa:** la señal no está conectada, el color no es válido o la extensión no está activa.
- **Corrección:** revisa el log y el formato del color. Después de corregir, el botón cambia.

**Error 2: el valor no se guarda.**
- **Ubicación:** `gsettings`.
- **Síntoma:** el valor vuelve al anterior.
- **Causa:** el esquema está en una ruta distinta o el comando usa otro `--schemadir`.
- **Corrección:** usa la misma ruta de `schemas/` y comprueba el valor con `get`. Después de corregir, el valor persiste.

Abre la ventana de preferencias:

```bash
gnome-extensions prefs miprimeraextension@ejemplo.local
```

Cambia el valor del campo y cierra la ventana. El botón debe actualizarse. Cierra sesión y vuelve a entrar: el color debe seguir siendo el que elegiste.

**Errores posibles en este bloque:**

**Error 1: `gnome-extensions prefs` no abre la ventana.**
- **Ubicación:** terminal.
- **Síntoma:** no aparece ninguna ventana.
- **Causa:** `prefs.js` tiene un error de importación o la extensión no está activa.
- **Corrección:** revisa el log de preferencias y la ruta de `ExtensionPreferences`. Después de corregir, la ventana abre.

**Error 2: el color no persiste.**
- **Ubicación:** cierre de sesión.
- **Síntoma:** el color vuelve al valor por defecto.
- **Causa:** el valor se guardó en otra clave o el esquema no es el mismo.
- **Corrección:** comprueba la clave `color` y el identificador del esquema. Después de corregir, el color persiste.

## Errores de integración del capítulo

### Error 1: falta `settings-schema` en `metadata.json`

**Síntoma:**

```text
Error: Expected type string for argument 'schema_id' but got type undefined
```

**Causa:** `getSettings()` no recibe el identificador del esquema.

**Corrección:** añade `"settings-schema": "org.gnome.shell.extensions.miprimeraextension"` al metadata. Después de corregir, la extensión carga la configuración.

### Error 2: el esquema no está compilado

**Síntoma:**

```text
Error: Schema org.gnome.shell.extensions.miprimeraextension could not be found for extension miprimeraextension@ejemplo.local. Please check your installation
```

**Causa:** falta `schemas/gschemas.compiled` o el identificador no coincide.

**Corrección:** ejecuta `glib-compile-schemas schemas/` y comprueba el identificador. Después de corregir, el esquema se encuentra.

### Error 3: el color no se actualiza en vivo

**Síntoma:** cambias el valor con `gsettings`, pero el botón conserva el color anterior.

**Causa:** la señal `changed::color` no está conectada o se desconectó antes de tiempo.

**Corrección:** conecta la señal después de `getSettings()` y desconéctala únicamente en `disable()`. Después de corregir, el cambio se refleja.

### Error 4: la ventana de preferencias está vacía

**Síntoma:** `gnome-extensions prefs` abre una ventana sin opciones.

**Causa:** no se añadió la página a la ventana o la fila al grupo.

**Corrección:** añade `window.add(page)` y `group.add(row)`. Después de corregir, la opción aparece.

### Error 5: el color no persiste tras cerrar sesión

**Síntoma:** el botón vuelve al color por defecto al volver a entrar.

**Causa:** el valor se guardó en otra clave, en otro esquema o en una variable de JavaScript.

**Corrección:** usa la clave `color` del esquema y comprueba el valor con `gsettings get`. Después de corregir, el color persiste.

## Ejercicios

### Reproductivo

1. Crea la carpeta `schemas` y el archivo `.gschema.xml` con la clave `color`.
2. Ejecuta `glib-compile-schemas schemas/`.
3. Añade `settings-schema` a `metadata.json`.
4. Añade la carga de configuración y la señal `changed::color` a `extension.js`.
5. Añade el método `_actualizarColor()` y la limpieza en `disable()`.
6. Activa la extensión y comprueba el color por defecto.

### Aplicativo

1. Cambia el color con `gsettings` a `#e66100`.
2. Comprueba que el botón cambia sin recargar la extensión.
3. Abre la ventana de preferencias y cambia el valor desde el campo.
4. Cierra sesión y vuelve a entrar.
5. Comprueba que el color elegido persiste.

### Creativo

1. Añade una segunda clave al esquema llamada `mostrar-texto`, de tipo `b`, con valor por defecto `true`.
2. Usa esa clave para mostrar u ocultar una etiqueta junto al icono del botón.
3. Añade un `Gtk.Switch` en `prefs.js` y vincúlalo con `settings.bind()`.
4. Comprueba que el cambio se refleja en el botón.
5. Explica qué diferencia hay entre una clave `s` y una clave `b`.

**Rúbrica del ejercicio creativo:**

- La clave `mostrar-texto` existe en el esquema y está compilada.
- El `Gtk.Switch` está vinculado a la clave.
- El botón muestra u oculta la etiqueta según el valor.
- El cambio se refleja sin recargar la extensión.
- La explicación distingue texto de booleano.

## Autoevaluación

1. ¿Qué es un esquema de GSettings y para qué sirve?
2. ¿Qué relación hay entre `settings-schema` y el atributo `id` del esquema?
3. ¿Qué hace `settings.bind()` y por qué es útil en `prefs.js`?
4. ¿Por qué `stylesheet.css` no se aplica a la ventana de preferencias?
5. Tu extensión queda en `ERROR` al llamar a `getSettings()`. ¿Qué tres cosas revisarías y en qué orden?
6. ¿Qué debe hacer `disable()` con la señal `changed::color`?

## Respuestas razonadas

1. Un esquema de GSettings es la definición de las claves de configuración: nombre, tipo, valor por defecto y documentación. Permite que la extensión guarde valores persistentes y que la ventana de preferencias los edite.

2. Deben coincidir exactamente. `settings-schema` indica a `getSettings()` qué esquema cargar; el atributo `id` del XML es el nombre real del esquema. Si no coinciden, la búsqueda falla.

3. `settings.bind()` sincroniza una clave de GSettings con una propiedad de un widget. Si cambia el ajuste, cambia el widget; si el usuario edita el widget, cambia el ajuste. Evita escribir callbacks manuales para guardar cada cambio.

4. Porque la ventana de preferencias se ejecuta en otro proceso y usa GTK4/libadwaita, no St. El `stylesheet.css` de la extensión se aplica a los actores del shell, no a los widgets GTK.

5. Primero revisaría que `metadata.json` tenga `settings-schema`. Después comprobaría que el identificador coincide con el atributo `id` del esquema. Finalmente verificaría que `schemas/gschemas.compiled` existe y está actualizado. El orden va del metadata al esquema compilado.

6. Debe desconectar la señal con `disconnect()` y poner el identificador en `0` antes de destruir el botón o liberar la configuración. Así la función deja de ejecutarse después de desactivar.

## Soluciones de los ejercicios

### Solución del reproductivo

El esquema debe quedar así:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<schemalist>
    <schema id="org.gnome.shell.extensions.miprimeraextension"
            path="/org/gnome/shell/extensions/miprimeraextension/">
        <key name="color" type="s">
            <default>'#3584e4'</default>
            <summary>Color del botón</summary>
            <description>Color hexadecimal del botón del panel.</description>
        </key>
    </schema>
</schemalist>
```

**Errores posibles en este bloque:**

**Error 1: `Falló al analizar el valor <default> ...`**
- **Ubicación:** archivo `.gschema.xml`.
- **Síntoma:** no se genera `gschemas.compiled`.
- **Causa:** el tipo y el valor no coinciden.
- **Corrección:** usa `type="s"` y un valor entre comillas simples. Después de corregir, el esquema se compila.

**Error 2: el esquema no se encuentra.**
- **Ubicación:** `metadata.json`.
- **Síntoma:** `getSettings()` falla.
- **Causa:** el identificador no coincide o falta `settings-schema`.
- **Corrección:** usa el mismo identificador en los dos archivos. Después de corregir, la configuración se carga.

El método de actualización del color:

```javascript
const COLOR_PATTERN = /^#[0-9a-fA-F]{6}$/;

_actualizarColor() {
    if (!this._indicador || !this._settings)
        return;

    const color = this._settings.get_string('color');

    if (!COLOR_PATTERN.test(color)) {
        console.error(`Color no válido: ${color}`);
        return;
    }

    this._indicador.set_style(`background-color: ${color};`);
}
```

**Errores posibles en este bloque:**

**Error 1: `Color no válido: ...`**
- **Ubicación:** método `_actualizarColor`.
- **Síntoma:** el color no se aplica.
- **Causa:** el valor no tiene formato `#RRGGBB`.
- **Corrección:** usa un color hexadecimal válido. Después de corregir, el color se aplica.

**Error 2: `TypeError: this._indicador.set_style is not a function`**
- **Ubicación:** método `_actualizarColor`.
- **Síntoma:** la extensión queda en `ERROR`.
- **Causa:** el botón no existe todavía.
- **Corrección:** llama al método después de crear el botón. Después de corregir, el estilo se aplica.

### Solución del aplicativo

El comando para cambiar el color es:

```bash
gsettings --schemadir schemas/ set org.gnome.shell.extensions.miprimeraextension color '#e66100'
```

**Errores posibles en este bloque:**

**Error 1: `No such schema ...`**
- **Ubicación:** terminal.
- **Síntoma:** el comando no encuentra el esquema.
- **Causa:** la carpeta `schemas/` no contiene el archivo compilado.
- **Corrección:** ejecuta `glib-compile-schemas schemas/`. Después de corregir, el comando funciona.

**Error 2: el botón no cambia.**
- **Ubicación:** panel.
- **Síntoma:** el valor se guarda, pero el color no cambia.
- **Causa:** la señal `changed::color` no está conectada.
- **Corrección:** conecta la señal y comprueba el log. Después de corregir, el botón cambia.

### Solución del creativo

Una solución añade al esquema:

```xml
<key name="mostrar-texto" type="b">
    <default>true</default>
    <summary>Mostrar texto</summary>
    <description>Muestra una etiqueta junto al icono.</description>
</key>
```

**Errores posibles en este bloque:**

**Error 1: el tipo no es booleano.**
- **Ubicación:** `<key type="b">`.
- **Síntoma:** `glib-compile-schemas` falla.
- **Causa:** usaste `s` o un valor que no es `true` o `false`.
- **Corrección:** usa `type="b"` y un valor booleano. Después de corregir, el esquema se compila.

**Error 2: el `Gtk.Switch` no cambia el valor.**
- **Ubicación:** `prefs.js`.
- **Síntoma:** el interruptor se mueve, pero la etiqueta no cambia.
- **Causa:** la clave o la propiedad del widget no coinciden.
- **Corrección:** vincula `'mostrar-texto'` con la propiedad `'active'` del `Gtk.Switch`. Después de corregir, el cambio se refleja.

Dentro de `fillPreferencesWindow`, después de crear `group`, añade la fila del interruptor:

```javascript
const switchRow = new Adw.ActionRow({
    title: 'Mostrar texto',
});

const switchWidget = new Gtk.Switch({
    active: settings.get_boolean('mostrar-texto'),
    valign: Gtk.Align.CENTER,
});

settings.bind(
    'mostrar-texto',
    switchWidget,
    'active',
    Gio.SettingsBindFlags.DEFAULT
);

switchRow.add_suffix(switchWidget);
group.add(switchRow);
```

> **Qué hace este bloque:** crea una fila, un interruptor y lo vincula a la clave booleana `mostrar-texto`. `get_boolean` lee el valor inicial. `settings.bind` usa la propiedad `active` del interruptor. Al cambiar el interruptor, el valor se guarda; al cambiar el valor, el interruptor se actualiza.

**Errores posibles en este bloque:**

**Error 1: el interruptor no refleja el valor guardado.**
- **Ubicación:** `settings.bind`.
- **Síntoma:** el valor es `true`, pero el interruptor aparece apagado.
- **Causa:** la propiedad `active` no está vinculada o la clave no es booleana.
- **Corrección:** usa `'active'` y una clave de tipo `b`. Después de corregir, el interruptor refleja el valor.

**Error 2: `TypeError: settings.get_boolean is not a function`.**
- **Ubicación:** línea de `get_boolean`.
- **Síntoma:** la ventana de preferencias no abre.
- **Causa:** `settings` no es un objeto `Gio.Settings`.
- **Corrección:** usa `this.getSettings()` en `fillPreferencesWindow`. Después de corregir, el método existe.

## Si quieres saber más

- Documentación de GSettings: <https://docs.gtk.org/gio/class.Settings.html>
- Documentación de libadwaita: <https://gnome.pages.gitlab.gnome.org/libadwaita/doc/>
- Guía de GJS sobre preferencias: <https://gjs.guide/extensions/development/preferences.html>

No necesitas estos enlaces para continuar. El manual contiene lo necesario para el proyecto.

## Siguiente paso

Cuando el color persista y la ventana de preferencias funcione, continúa con el Capítulo 6. Allí usarás Meta para mover la ventana activa a otro espacio de trabajo.
