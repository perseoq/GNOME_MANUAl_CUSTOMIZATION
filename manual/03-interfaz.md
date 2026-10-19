# Capítulo 3: Construcción de Interfaz Gráfica con St y Clutter

> En este capítulo crearás un botón visible en el panel superior. Todavía no tendrá un menú complejo: será un icono que confirma que tu extensión puede dibujar en el escritorio.

## Qué vas a aprender

- Distinguir St, Clutter y GTK.
- Crear un botón de panel con `PanelMenu.Button`.
- Añadir un icono con `St.Icon`.
- Registrar el botón con `Main.panel.addToStatusArea()`.
- Usar contenedores y propiedades de expansión de St.
- Animar un actor con Clutter y respetar la configuración de movimiento reducido.
- Destruir el botón en `disable()` para no dejar recursos.

Al final del capítulo, tu extensión mostrará un icono en el panel superior y lo eliminará al desactivarse.

## Antes de empezar necesitas

- Haber completado los capítulos 1 y 2.
- Tener una extensión que se activa, escribe en el log y muestra una notificación.
- Saber usar `gnome-extensions` y `journalctl`.
- GNOME Shell 50.6 sobre Wayland.

## El concepto, con una analogía

St es como el mueblista que fabrica los objetos del escritorio: botones, etiquetas, iconos y cajas. Clutter es el escenario donde esos objetos se colocan, se mueven y se animan. GTK es el mueblista de otra casa: se usa para las aplicaciones y para la ventana de preferencias, no para el panel del shell.

El panel superior es un estante. `Main.panel` es el estante; un indicador es el objeto que colocas en él. Si no retiras el objeto al desactivar la extensión, el estante se llena de objetos duplicados.

### 3.1. La separación de motores: St/Clutter frente a GTK4

#### 3.1.1. Por qué GTK nunca se importa en `extension.js`

GNOME Shell usa **St** y **Clutter** para dibujar el escritorio. Las aplicaciones usan **GTK**, que es otra biblioteca de interfaces. La ventana de preferencias de una extensión también usa GTK4, pero se ejecuta en un proceso separado.

| Motor | Se usa para | Dónde se importa |
| :--- | :--- | :--- |
| St | Widgets del shell, estilos CSS | `extension.js` |
| Clutter | Actores, animaciones, entrada | `extension.js` |
| GTK4 | Aplicaciones y preferencias | `prefs.js` |

Si importas GTK en `extension.js`, puedes provocar un conflicto de versiones. En el Capítulo 2 viste el error real:

```text
Error: Requiring Gtk, version 3.0: Requiring namespace 'Gtk' version '3.0', but '4.0' is already loaded
```

Este bloque es un ejemplo de log, no un programa. No se ejecuta y no produce errores por sí mismo.

**Regla práctica:** en `extension.js` usa St y Clutter. En `prefs.js` usa GTK4 y libadwaita cuando llegues al Capítulo 5.

**Comprueba que lo entiendes:** explica por qué una extensión del panel no debe importar GTK. Menciona el proceso del shell y el proceso de preferencias.

#### 3.1.2. St como toolkit de widgets estilizables con CSS

St significa **Shell Toolkit**. Es la biblioteca que ofrece los widgets del shell: botones, etiquetas, iconos, cajas, listas y vistas de desplazamiento. Cada widget de St es también un actor de Clutter, así que se puede mover, escalar y animar.

Clutter aporta:

- La posición y el tamaño de los actores.
- La opacidad y la escala.
- Las animaciones.
- La entrada del teclado y del ratón.

St aporta:

- Los widgets con apariencia de GNOME.
- Las clases de estilo que se usan en CSS.
- Los contenedores para organizar widgets.

**Comprueba que lo entiendes:** clasifica estos elementos como St o Clutter: `St.Button`, `St.Icon`, `Clutter.Actor`, `St.BoxLayout`. Después explica qué añade St sobre Clutter.

### 3.2. El indicador de panel paso a paso

#### 3.2.1. Instanciación de `PanelMenu.Button`

Un **indicador** es un elemento que se coloca en el panel superior. `PanelMenu.Button` es un botón de panel que puede abrir un menú. Aunque todavía no añadas opciones al menú, el botón ya puede mostrar un icono.

Primero, los imports:

```javascript
import * as Main from 'resource:///org/gnome/shell/ui/main.js';
import * as PanelMenu from 'resource:///org/gnome/shell/ui/panelMenu.js';
import St from 'gi://St';
import {Extension} from 'resource:///org/gnome/shell/extensions/extension.js';
```

> **Qué hace este bloque:** `Main` da acceso al panel y a las notificaciones. `PanelMenu` contiene la clase `Button` para crear indicadores. `St` contiene los widgets y estilos. `Extension` es la clase base de la extensión. La ruta `panelMenu.js` usa una `M` mayúscula; las rutas distinguen mayúsculas de minúsculas.

**Errores posibles en este bloque:**

**Error 1: `ImportError: Unable to load file from: resource:///org/gnome/shell/ui/panelmenu.js (El recurso en «/org/gnome/shell/ui/panelmenu.js» no existe)`**
- **Ubicación:** `extension.js`, línea del import de `PanelMenu`.
- **Síntoma:** la extensión queda en `ERROR` y el log muestra que el recurso no existe.
- **Causa:** escribiste `panelmenu.js` con `m` minúscula.
- **Corrección:** usa `resource:///org/gnome/shell/ui/panelMenu.js` con `M` mayúscula. Después de corregir, el módulo se carga.

**Error 2: `ImportError` en `main.js`.**
- **Ubicación:** línea del import de `Main`.
- **Síntoma:** la extensión no carga.
- **Causa:** la ruta de `main.js` está mal escrita.
- **Corrección:** usa `resource:///org/gnome/shell/ui/main.js`. Después de corregir, `Main` existe.

Ahora crea el botón:

```javascript
this._indicador = new PanelMenu.Button(
    0.0,
    'Mi Primera Extensión',
    false
);
this._indicador.add_style_class_name('mi-primera-extension');
```

> **Qué hace este bloque:** `new PanelMenu.Button(...)` crea un botón de panel. `0.0` alinea el menú a la izquierda. `'Mi Primera Extensión'` es el nombre accesible que se usa en la interfaz. `false` indica que el botón debe crear un menú; todavía no añades elementos, pero el menú existe. `add_style_class_name` añade la clase `mi-primera-extension`, que usarás en el Capítulo 4 para aplicar CSS.

**Errores posibles en este bloque:**

**Error 1: `TypeError: PanelMenu.Button is not a constructor`**
- **Ubicación:** línea de `new PanelMenu.Button`.
- **Síntoma:** la extensión queda en `ERROR`.
- **Causa:** el import de `PanelMenu` no es el módulo correcto o el nombre de la clase está mal escrito.
- **Corrección:** importa `PanelMenu` desde `panelMenu.js` y escribe `PanelMenu.Button` con mayúsculas. Después de corregir, el botón se crea.

**Error 2: `TypeError: this._indicador.add_style_class_name is not a function`**
- **Ubicación:** línea de `add_style_class_name`.
- **Síntoma:** el log muestra que el método no existe.
- **Causa:** `this._indicador` no es un widget de St.
- **Corrección:** comprueba que creaste el objeto con `new PanelMenu.Button(...)`. Después de corregir, el método existe.

#### 3.2.2. Añadir `St.Icon` con nombres simbólicos

Un **icono simbólico** es un icono que usa el color del texto y se adapta al tema. Se identifica con un nombre que termina en `-symbolic`.

```javascript
const icono = new St.Icon({
    icon_name: 'face-smile-symbolic',
    style_class: 'system-status-icon',
});

this._indicador.add_child(icono);
```

> **Qué hace este bloque:** `new St.Icon({...})` crea un icono. `icon_name` indica el nombre del icono en el tema. `style_class: 'system-status-icon'` aplica la clase que usa el panel para dar el tamaño correcto. `add_child` coloca el icono dentro del botón. El icono es un hijo del botón, no un botón independiente.

**Errores posibles en este bloque:**

**Error 1: el icono aparece en blanco.**
- **Ubicación:** panel superior.
- **Síntoma:** el botón se ve, pero no tiene icono.
- **Causa:** el nombre del icono no existe en el tema instalado.
- **Corrección:** usa un nombre simbólico estándar, como `face-smile-symbolic`, `dialog-information-symbolic` o `starred-symbolic`. Después de corregir, el icono aparece.

**Error 2: `TypeError: this._indicador.add_child is not a function`**
- **Ubicación:** línea de `add_child`.
- **Síntoma:** la extensión queda en `ERROR`.
- **Causa:** `this._indicador` no es un contenedor de St.
- **Corrección:** crea el botón con `new PanelMenu.Button(...)` antes de llamar a `add_child`. Después de corregir, el icono se añade.

#### 3.2.3. Registro con `Main.panel.addToStatusArea()`

Crear el botón no basta: hay que registrarlo en el panel.

```javascript
Main.panel.addToStatusArea(this.uuid, this._indicador);
```

> **Qué hace este bloque:** `Main.panel` es el panel superior. `addToStatusArea` registra el indicador con el identificador de la extensión y lo coloca en el área de estado. El primer argumento es el rol; el segundo, el actor que se añade. Sin esta llamada, el botón existe en memoria, pero no aparece en el panel.

**Errores posibles en este bloque:**

**Error 1: `TypeError: Main.panel.addToStatusArea is not a function`**
- **Ubicación:** línea de `addToStatusArea`.
- **Síntoma:** el log muestra que el método no existe.
- **Causa:** `Main` no es el módulo correcto o el nombre está mal escrito.
- **Corrección:** importa `Main` desde `resource:///org/gnome/shell/ui/main.js` y escribe `Main.panel.addToStatusArea`. Después de corregir, el indicador se registra.

**Error 2: el botón no aparece en el panel.**
- **Ubicación:** panel superior.
- **Síntoma:** la extensión está activa, pero no ves el icono.
- **Causa:** olvidaste llamar a `addToStatusArea`, el panel no está disponible o el icono no tiene tamaño.
- **Corrección:** comprueba que llamas a `Main.panel.addToStatusArea` y que el icono tiene `style_class: 'system-status-icon'`. Después de corregir, el icono aparece.

#### 3.2.4. Destruir el indicador en `disable()`

El botón es un actor. Si no lo destruyes, queda en el panel y se duplica al volver a activar la extensión.

```javascript
disable() {
    this._indicador?.destroy();
    this._indicador = null;
}
```

> **Qué hace este bloque:** `?.` es el operador de encadenamiento opcional. Si `this._indicador` existe, llama a `destroy()`. Después asigna `null` para indicar que ya no hay botón. `destroy()` elimina el actor y sus hijos del árbol del shell.

**Errores posibles en este bloque:**

**Error 1: aparecen dos iconos después de activar y desactivar.**
- **Ubicación:** panel superior.
- **Síntoma:** cada activación añade un icono nuevo.
- **Causa:** `disable()` no destruye el indicador.
- **Corrección:** llama a `this._indicador.destroy()` en `disable()` y pon la propiedad en `null`. Después de corregir, únicamente queda un icono.

**Error 2: `TypeError: this._indicador.destroy is not a function`**
- **Ubicación:** línea de `destroy()`.
- **Síntoma:** el log muestra que `destroy` no existe.
- **Causa:** `this._indicador` no es un actor de St o se sobrescribió con otro valor.
- **Corrección:** guarda el botón en la misma propiedad que usaste al crearlo. Después de corregir, `destroy()` funciona.

### 3.3. Contenedores y layouts en St

Un **contenedor** es un widget que organiza otros widgets. St ofrece varios:

| Contenedor | Para qué sirve |
| :--- | :--- |
| `St.BoxLayout` | Coloca hijos en fila o en columna |
| `St.Bin` | Contiene un único hijo y lo alinea |
| `St.ScrollView` | Muestra contenido que puede desplazarse |

Propiedades importantes:

| Propiedad | Efecto |
| :--- | :--- |
| `vertical` | En `St.BoxLayout`, coloca los hijos en columna si es `true` |
| `spacing` | Espacio entre hijos |
| `x_expand` | El hijo ocupa el espacio horizontal disponible |
| `y_expand` | El hijo ocupa el espacio vertical disponible |
| `x_align` | Alineación horizontal |
| `y_align` | Alineación vertical |

El botón del panel ya es un contenedor: el icono es su hijo. En el ejercicio creativo añadirás una etiqueta al lado del icono usando `St.BoxLayout`.

**Comprueba que lo entiendes:** mira el panel y explica qué contenedor usarías para colocar un icono y una etiqueta en horizontal. Después indica qué propiedad usarías para separarlos.

### 3.4. Animaciones con Clutter

Para animar actores con Clutter, importa la biblioteca:

```javascript
import Clutter from 'gi://Clutter';
```

> **Qué hace este bloque:** carga la biblioteca Clutter, que contiene `AnimationMode` y las funciones de animación. El import va al principio de `extension.js`, junto a los demás imports.

**Errores posibles en este bloque:**

**Error 1: `ImportError: Unable to load file from: gi://Clutter`**
- **Ubicación:** línea del import.
- **Síntoma:** la extensión no carga.
- **Causa:** el nombre de la biblioteca está mal escrito o no está disponible en el proceso del shell.
- **Corrección:** usa `gi://Clutter` con mayúscula inicial. Después de corregir, el módulo se carga.

**Error 2: `ReferenceError: Clutter is not defined`**
- **Ubicación:** línea que usa `Clutter.AnimationMode`.
- **Síntoma:** la extensión queda en `ERROR`.
- **Causa:** falta el import de Clutter.
- **Corrección:** añade `import Clutter from 'gi://Clutter';` al principio del archivo. Después de corregir, `Clutter` existe.

#### 3.4.1. `actor.ease()` para transiciones simples

`ease()` aplica una animación a las propiedades de un actor. Por ejemplo, cambia la escala del indicador:

```javascript
this._indicador.ease({
    scale_x: 1.1,
    scale_y: 1.1,
    duration: 150,
    mode: Clutter.AnimationMode.EASE_OUT_QUAD,
});
```

> **Qué hace este bloque:** `ease` inicia una animación sobre `this._indicador`. `scale_x` y `scale_y` cambian el tamaño. `duration` indica la duración en milisegundos. `mode` elige la curva de la animación. Al terminar, el actor queda en la escala 1.1; para devolverlo a 1.0 necesitas otra llamada.

**Errores posibles en este bloque:**

**Error 1: `ReferenceError: Clutter is not defined`**
- **Ubicación:** línea de `Clutter.AnimationMode`.
- **Síntoma:** el log muestra que `Clutter` no existe.
- **Causa:** falta el import `import Clutter from 'gi://Clutter';`.
- **Corrección:** añade el import al principio del archivo. Después de corregir, `Clutter` existe.

**Error 2: la animación no se ve.**
- **Ubicación:** panel superior.
- **Síntoma:** el icono no cambia de tamaño.
- **Causa:** el actor no está en el escenario, la animación se interrumpió o el movimiento reducido está activo.
- **Corrección:** comprueba que el indicador está añadido al panel y que `St.Settings.get().enable_animations` es `true`. Después de corregir, la animación se ve.

#### 3.4.2. `easeAsync()` en GNOME 50 y manejo de promesas

GNOME Shell 50 añade `easeAsync()`, la versión asíncrona de `ease()`. Devuelve una promesa que se resuelve cuando la animación termina. El siguiente bloque debe ir dentro de una función `async`:

```javascript
await this._indicador.easeAsync({
    scale_x: 1.0,
    scale_y: 1.0,
    duration: 150,
    mode: Clutter.AnimationMode.EASE_OUT_QUAD,
});
```

> **Qué hace este bloque:** `await` espera a que la animación termine. `easeAsync` devuelve una promesa; si la animación se interrumpe, la promesa se rechaza. La escala vuelve a `1.0`. Este bloque debe estar dentro de una función `async`.

**Errores posibles en este bloque:**

**Error 1: `SyntaxError: await is only valid in async functions`**
- **Ubicación:** línea de `await`.
- **Síntoma:** la extensión no carga.
- **Causa:** usaste `await` dentro de una función que no es `async`.
- **Corrección:** declara la función como `async` o usa `ease()` si no necesitas esperar. Después de corregir, el archivo se carga.

**Error 2: la promesa se rechaza al interrumpir la animación.**
- **Ubicación:** llamada a `easeAsync`.
- **Síntoma:** aparece un error de cancelación en el log.
- **Causa:** otra animación o la destrucción del actor interrumpió la transición.
- **Corrección:** captura el error y distingue la cancelación de un error real. Después de corregir, el log no muestra errores inesperados.

#### 3.4.3. Respeto a la configuración de movimiento reducido

No todas las personas quieren animaciones. GNOME ofrece una configuración de accesibilidad y `St.Settings` expone si las animaciones están habilitadas.

```javascript
const ajustes = St.Settings.get();

if (ajustes.enable_animations) {
    this._indicador.ease({
        scale_x: 1.1,
        scale_y: 1.1,
        duration: 150,
        mode: Clutter.AnimationMode.EASE_OUT_QUAD,
    });
}
```

> **Qué hace este bloque:** `St.Settings.get()` obtiene la configuración global de St. `enable_animations` indica si las animaciones están permitidas. Si es `false`, el bloque no anima. La interfaz sigue funcionando sin animación.

**Errores posibles en este bloque:**

**Error 1: `TypeError: St.Settings.get is not a function`**
- **Ubicación:** línea de `St.Settings.get()`.
- **Síntoma:** el log muestra que `get` no existe.
- **Causa:** `St` no es el módulo correcto o la versión de GNOME no expone esa API.
- **Corrección:** importa `St` desde `gi://St` y comprueba que usas GNOME Shell 50.6. Después de corregir, la consulta funciona.

**Error 2: la animación se ejecuta con movimiento reducido activo.**
- **Ubicación:** condición `if`.
- **Síntoma:** la interfaz anima aunque la accesibilidad pida reducirlo.
- **Causa:** olvidaste comprobar `enable_animations`.
- **Corrección:** envuelve la animación en la condición. Después de corregir, la animación se omite cuando corresponde.

## Verifica que funciona

Activa la extensión:

```bash
gnome-extensions enable miprimeraextension@ejemplo.local
```

Debe aparecer un icono con forma de cara sonriente en el panel superior. En el log debes ver:

```text
GNOME Shell-Message: 11:02:23.241: [miprimeraextension@ejemplo.local] indicador añadido
```

**Errores posibles en este bloque:**

**Error 1: el icono no aparece.**
- **Ubicación:** panel superior.
- **Síntoma:** la extensión está activa, pero no ves el icono.
- **Causa:** no llamaste a `addToStatusArea`, el icono no existe o el nombre del icono es inválido.
- **Corrección:** revisa el código y prueba con `face-smile-symbolic`. Después de corregir, el icono aparece.

**Error 2: el estado es `ERROR`.**
- **Ubicación:** `gnome-extensions info`.
- **Síntoma:** la extensión no se activa.
- **Causa:** un import o una llamada a `PanelMenu` tiene un error.
- **Corrección:** lee el log con `journalctl /usr/bin/gnome-shell -f -o cat`, corrige la línea indicada y vuelve a activar.

Después desactiva la extensión:

```bash
gnome-extensions disable miprimeraextension@ejemplo.local
```

El icono debe desaparecer y el log debe mostrar:

```text
GNOME Shell-Message: 11:02:24.290: [miprimeraextension@ejemplo.local] indicador eliminado
```

**Errores posibles en este bloque:**

**Error 1: el icono sigue apareciendo después de desactivar.**
- **Ubicación:** panel superior.
- **Síntoma:** el icono no desaparece.
- **Causa:** `disable()` no destruye el indicador.
- **Corrección:** añade `this._indicador?.destroy()` y asigna `null`. Después de corregir, el icono desaparece.

**Error 2: aparecen varios iconos.**
- **Ubicación:** panel superior.
- **Síntoma:** cada activación añade un icono nuevo.
- **Causa:** el indicador anterior no se destruyó.
- **Corrección:** destruye el indicador en `disable()` y comprueba que `enable()` no cree uno nuevo sin limpiar el anterior. Después de corregir, únicamente hay un icono.

## Errores de integración del capítulo

### Error 1: el estado es `ERROR` por `panelMenu.js`

**Síntoma:**

```text
ImportError: Unable to load file from: resource:///org/gnome/shell/ui/panelmenu.js (El recurso en «/org/gnome/shell/ui/panelmenu.js» no existe)
```

**Causa:** escribiste `panelmenu.js` con `m` minúscula.

**Corrección:** usa `panelMenu.js` con `M` mayúscula. Después de corregir, la extensión se activa.

### Error 2: el icono está en blanco

**Síntoma:** el botón aparece, pero no muestra ningún icono.

**Causa:** el nombre del icono no existe en el tema.

**Corrección:** usa un icono simbólico estándar, como `face-smile-symbolic` o `dialog-information-symbolic`. Después de corregir, el icono aparece.

### Error 3: el botón no aparece

**Síntoma:** la extensión está en `ACTIVE`, pero el panel no cambia.

**Causa:** olvidaste `Main.panel.addToStatusArea()`.

**Corrección:** registra el indicador con `Main.panel.addToStatusArea(this.uuid, this._indicador)`. Después de corregir, el botón aparece.

### Error 4: el icono se duplica

**Síntoma:** después de desactivar y activar, hay dos iconos.

**Causa:** `disable()` no destruye el actor.

**Corrección:** llama a `destroy()` en `disable()` y asigna `null`. Después de corregir, únicamente queda un icono.

### Error 5: el botón desaparece al cambiar de tema

**Síntoma:** el icono deja de verse después de cambiar el tema del sistema.

**Causa:** el icono o el estilo dependen de un nombre que el tema nuevo no incluye.

**Corrección:** usa nombres simbólicos estándar y una clase CSS propia. Después de corregir, el icono se adapta al tema.

## Ejercicios

### Reproductivo

1. Añade los imports de `Main`, `PanelMenu`, `St` y `Extension`.
2. Crea un `PanelMenu.Button` y añade la clase `mi-primera-extension`.
3. Crea un `St.Icon` con `face-smile-symbolic` y añádelo al botón.
4. Registra el botón con `Main.panel.addToStatusArea()`.
5. Activa la extensión y comprueba el icono en el panel.
6. Desactiva la extensión y comprueba que el icono desaparece.

### Aplicativo

1. Cambia el icono por `dialog-information-symbolic`.
2. Activa la extensión y comprueba que el icono cambia.
3. Desactiva y vuelve a activar.
4. Comprueba que no aparecen iconos duplicados.
5. Explica qué línea destruye el icono.

### Creativo

1. Añade una etiqueta con el texto `Hola` al lado del icono.
2. Pista: crea un `St.BoxLayout` horizontal con `spacing: 6`, añade el icono y la etiqueta, y coloca la caja dentro del botón.
3. Comprueba que el botón muestra icono y texto.
4. Desactiva la extensión y comprueba que todo desaparece.
5. Explica qué contenedor usaste y por qué.

**Rúbrica del ejercicio creativo:**

- El botón muestra el icono y la etiqueta.
- La etiqueta está dentro de un `St.BoxLayout`.
- El `St.BoxLayout` está dentro del botón.
- La extensión se desactiva sin errores.
- La explicación menciona `vertical: false` o la orientación horizontal.

## Autoevaluación

1. ¿Qué diferencia hay entre St y Clutter?
2. ¿Por qué GTK no se importa en `extension.js`?
3. ¿Qué hace `Main.panel.addToStatusArea()`?
4. ¿Qué ocurre si no destruyes el indicador en `disable()`?
5. Tu icono no aparece en el panel. ¿Qué tres cosas revisarías y en qué orden?
6. ¿Por qué una animación debe comprobar `St.Settings.get().enable_animations`?

## Respuestas razonadas

1. St ofrece widgets con apariencia de GNOME y clases CSS. Clutter ofrece actores, posición, escala, opacidad y animaciones. Cada widget de St es también un actor de Clutter.

2. Porque GTK pertenece a las aplicaciones y al proceso de preferencias. El shell usa St y Clutter. Importar GTK en `extension.js` puede provocar conflictos de versiones y no es el toolkit del panel.

3. Registra un indicador en el panel superior. Sin esa llamada, el botón existe en memoria, pero no se muestra.

4. El actor sigue en el panel y se duplica al volver a activar. Además, consume recursos y puede reaccionar a eventos aunque la extensión esté desactivada.

5. Primero revisaría que la extensión esté en estado `ACTIVE`. Después comprobaría que llamas a `addToStatusArea`. Finalmente revisaría el nombre del icono y su `style_class`. El orden va del estado general al detalle visual.

6. Porque algunas personas activan la configuración de movimiento reducido por accesibilidad. Si las animaciones están deshabilitadas, la interfaz debe seguir funcionando sin animar.

## Soluciones de los ejercicios

### Solución del reproductivo

El archivo `extension.js` debe quedar así:

```javascript
import * as Main from 'resource:///org/gnome/shell/ui/main.js';
import * as PanelMenu from 'resource:///org/gnome/shell/ui/panelMenu.js';
import St from 'gi://St';
import {Extension} from 'resource:///org/gnome/shell/extensions/extension.js';

export default class MiPrimeraExtension extends Extension {
    enable() {
        if (this._indicador) {
            this._indicador.destroy();
            this._indicador = null;
        }

        this._indicador = new PanelMenu.Button(
            0.0,
            'Mi Primera Extensión',
            false
        );
        this._indicador.add_style_class_name('mi-primera-extension');

        const icono = new St.Icon({
            icon_name: 'face-smile-symbolic',
            style_class: 'system-status-icon',
        });

        this._indicador.add_child(icono);
        Main.panel.addToStatusArea(this.uuid, this._indicador);
        console.log(`[${this.uuid}] indicador añadido`);
    }

    disable() {
        this._indicador?.destroy();
        this._indicador = null;
        console.log(`[${this.uuid}] indicador eliminado`);
    }
}
```

**Errores posibles en este bloque:**

**Error 1: `ImportError` en `panelMenu.js`.**
- **Ubicación:** línea del import.
- **Síntoma:** la extensión queda en `ERROR`.
- **Causa:** escribiste `panelmenu.js` con minúscula.
- **Corrección:** usa `panelMenu.js` con `M` mayúscula. Después de corregir, la extensión se activa.

**Error 2: el icono se duplica.**
- **Ubicación:** `enable()` y `disable()`.
- **Síntoma:** aparecen varios iconos.
- **Causa:** no se destruye el indicador anterior.
- **Corrección:** destruye el indicador en `disable()` y al inicio de `enable()` si ya existe. Después de corregir, únicamente queda un icono.

### Solución del aplicativo

Cambia el nombre del icono:

```javascript
const icono = new St.Icon({
    icon_name: 'dialog-information-symbolic',
    style_class: 'system-status-icon',
});
```

**Errores posibles en este bloque:** si el icono aparece en blanco, el nombre no existe en tu tema. Prueba con `face-smile-symbolic` o `starred-symbolic`. Después de corregir, el icono aparece.

### Solución del creativo

Una solución válida crea una caja horizontal dentro de `enable()`:

```javascript
const caja = new St.BoxLayout({
    vertical: false,
    spacing: 6,
});

const icono = new St.Icon({
    icon_name: 'face-smile-symbolic',
    style_class: 'system-status-icon',
});

const etiqueta = new St.Label({
    text: 'Hola',
});

caja.add_child(icono);
caja.add_child(etiqueta);
this._indicador.add_child(caja);
```

**Errores posibles en este bloque:**

**Error 1: la etiqueta no aparece.**
- **Ubicación:** panel superior.
- **Síntoma:** únicamente se ve el icono.
- **Causa:** la etiqueta no se añadió a la caja o la caja no se añadió al botón.
- **Corrección:** comprueba las dos llamadas `add_child`. Después de corregir, se ven el icono y el texto.

**Error 2: el texto no se lee.**
- **Ubicación:** etiqueta del panel.
- **Síntoma:** la etiqueta aparece, pero el color no contrasta con el fondo.
- **Causa:** el color del texto y el fondo del botón son demasiado parecidos.
- **Corrección:** ajusta `color` en el CSS del Capítulo 4. Después de corregir, el texto se lee.

## Si quieres saber más

- Documentación de St: <https://gnome.pages.gitlab.gnome.org/gnome-shell/st/>
- Documentación de Clutter: <https://gnome.pages.gitlab.gnome.org/gnome-shell/clutter/>
- Guía de GJS sobre actores: <https://gjs.guide/guides/gobject/>

No necesitas estos enlaces para continuar. El manual contiene lo necesario para el proyecto.

## Siguiente paso

Cuando el icono aparezca y desaparezca correctamente, continúa con el Capítulo 4. Allí aplicarás CSS propio para cambiar el color del botón y aprenderás qué propiedades entiende St.
