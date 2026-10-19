# Capítulo 4: El Sistema de Estilos CSS en St

> En este capítulo cambiarás la apariencia del botón que creaste en el Capítulo 3. Aprenderás qué parte de CSS entiende St y qué debes evitar.

## Qué vas a aprender

- Cómo carga GNOME Shell el archivo `stylesheet.css`.
- Usar selectores de clase sin afectar al resto del escritorio.
- Reconocer las propiedades CSS que St soporta y las que no.
- Cambiar el color, el borde y el espaciado de tu botón.
- Usar estilos en línea con `set_style()` cuando el valor cambia en tiempo de ejecución.
- Depurar CSS con Looking Glass y el Inspector.
- Recargar los estilos en Wayland.

Al final del capítulo, tu botón tendrá un color propio y reaccionará a los estados `:hover` y `:active`.

## Antes de empezar necesitas

- Haber completado el Capítulo 3.
- Tener un botón en el panel con la clase `mi-primera-extension`.
- Saber activar, desactivar y leer el log de la extensión.
- GNOME Shell 50.6 sobre Wayland.

## El concepto, con una analogía

CSS es la ropa de la interfaz. El botón ya existe y tiene una forma; CSS decide su color, su borde y su espacio. Una **clase** es como una etiqueta con un nombre: todos los elementos que llevan esa etiqueta reciben el mismo estilo.

St no es un navegador web. Entiende una parte de CSS y tiene sus propias reglas. Si intentas usar una propiedad de página web que St no implementa, no siempre aparece un error: la propiedad se ignora y el resultado no cambia. Por eso este capítulo insiste en comprobar el resultado visual y en leer el log cuando el CSS no carga.

### 4.1. Naturaleza y carga de `stylesheet.css`

#### 4.1.1. Detección automática por convención de nombre

GNOME Shell busca un archivo llamado exactamente `stylesheet.css` en la carpeta de la extensión. No necesitas importarlo desde `extension.js`. La convención es el contrato.

La estructura debe ser:

```text
~/.local/share/gnome-shell/extensions/miprimeraextension@ejemplo.local/
├── extension.js
├── metadata.json
└── stylesheet.css
```

Este bloque es un diagrama de archivos, no un programa. No se ejecuta y no produce errores por sí mismo.

Si el archivo se llama `style.css`, `styles.css` o `estilos.css`, GNOME Shell no lo carga. El nombre debe ser `stylesheet.css`.

**Errores posibles en este bloque:**

**Error 1: el archivo no se carga.**
- **Ubicación:** carpeta de la extensión.
- **Síntoma:** el botón no cambia de color aunque el CSS sea correcto.
- **Causa:** el archivo no se llama `stylesheet.css` o está en otra carpeta.
- **Corrección:** renómbralo a `stylesheet.css` y colócalo junto a `extension.js`. Después de corregir, el estilo se carga al activar la extensión.

**Error 2: el archivo está dentro de una subcarpeta.**
- **Ubicación:** estructura de la extensión.
- **Síntoma:** el estilo no se aplica.
- **Causa:** GNOME Shell busca `stylesheet.css` en la raíz de la extensión, no en `css/` ni en `styles/`.
- **Corrección:** mueve el archivo a la raíz de la carpeta de la extensión. Después de corregir, el estilo se aplica.

#### 4.1.2. Prioridad sobre el tema del sistema

El `stylesheet.css` de la extensión se carga después del tema del sistema. Por eso una clase propia puede sobrescribir el estilo base del botón. La prioridad depende del orden de carga y de la especificidad del selector.

Recomendación: usa una clase propia, como `.mi-primera-extension`, y evita `!important` salvo que tengas una razón concreta. `!important` gana la discusión, pero hace que el estilo sea difícil de mantener.

`@import` permite traer otro archivo CSS, pero cambia el orden de carga y puede hacer que el tema del sistema gane. Para este manual, no uses `@import` en `stylesheet.css`.

**Comprueba que lo entiendes:** explica por qué una clase propia puede cambiar el color de tu botón sin cambiar el color de los demás botones del panel.

#### 4.1.3. Extracción del CSS base del shell con `gresource`

El CSS base de GNOME Shell está dentro de un archivo `gresource`. Es material de consulta, no un requisito para este capítulo.

Si tu distribución incluye la herramienta `gresource`, puedes listar los recursos del tema con:

```bash
gresource list /usr/share/gnome-shell/gnome-shell-theme.gresource
```

> **Qué hace este bloque:** `gresource list` muestra los archivos internos de un paquete de recursos de GLib. La ruta `/usr/share/gnome-shell/gnome-shell-theme.gresource` es el paquete del tema del shell en Fedora. En otras distribuciones la ruta puede cambiar. Para extraer un archivo concreto se usa `gresource extract` con la ruta interna que aparezca en la lista.

**Errores posibles en este bloque:**

**Error 1: `bash: gresource: instrucción no encontrada...`**
- **Ubicación:** línea de la terminal.
- **Síntoma:** la terminal no encuentra la herramienta.
- **Causa:** `gresource` no está instalado; en Fedora suele venir con el paquete de desarrollo de GLib.
- **Corrección:** instala la herramienta desde el gestor de paquetes de tu distribución o continúa sin extraer el CSS. No necesitas este paso para completar el capítulo.

**Error 2: la ruta del archivo no existe.**
- **Ubicación:** argumento de `gresource`.
- **Síntoma:** la terminal muestra que el archivo no existe.
- **Causa:** tu distribución guarda el tema en otra ruta.
- **Corrección:** busca el archivo con el gestor de archivos o consulta la documentación de tu distribución. No modifiques el tema del sistema para este ejercicio.

**Comprueba que lo entiendes:** explica la diferencia entre el CSS del tema del sistema y el CSS de tu extensión. ¿Cuál se carga después?

### 4.2. Selectores válidos en St

#### 4.2.1. Selector de clase (`.mi-clase`)

El selector de clase es el mecanismo principal y seguro. En el Capítulo 3 añadiste la clase `mi-primera-extension` al botón. El CSS se escribe así:

```css
.mi-primera-extension {
    background-color: #3584e4;
}
```

> **Qué hace este bloque:** el punto indica que `.mi-primera-extension` es una clase. La regla se aplica a los elementos que tengan esa clase. `background-color` cambia el color de fondo. `#3584e4` es un color hexadecimal azul. Las llaves agrupan las propiedades.

**Errores posibles en este bloque:**

**Error 1: el botón no cambia de color.**
- **Ubicación:** selector CSS y código de la extensión.
- **Síntoma:** el CSS es válido, pero no se aplica.
- **Causa:** la clase del CSS no coincide con la clase añadida en `extension.js`.
- **Corrección:** compara `.mi-primera-extension` con `add_style_class_name('mi-primera-extension')`. Después de corregir, el estilo se aplica.

**Error 2: escribiste `mi-primera-extension` sin el punto.**
- **Ubicación:** selector CSS.
- **Síntoma:** el estilo no se aplica o se interpreta como un selector de tipo.
- **Causa:** sin el punto, St no busca una clase.
- **Corrección:** añade el punto al principio del selector. Después de corregir, el estilo se aplica.

#### 4.2.2. Selector de tipo (`StLabel`) y sus riesgos de contaminación global

Un selector de tipo se aplica a todos los widgets de ese tipo. Por ejemplo:

```css
StLabel {
    color: red;
}
```

> **Qué hace este bloque:** `StLabel` selecciona todos los widgets de tipo `StLabel` del shell. `color: red` cambia el color de todos los textos de ese tipo, no únicamente del tuyo. Es un ejemplo de selector global; no lo uses para estilos propios.

**Errores posibles en este bloque:**

**Error 1: todos los textos del shell se vuelven rojos.**
- **Ubicación:** selector CSS.
- **Síntoma:** el cambio afecta a menús, relojes y etiquetas que no pertenecen a tu extensión.
- **Causa:** usaste un selector de tipo global.
- **Corrección:** usa una clase propia, como `.mi-primera-extension`, y aplica el color al widget concreto. Después de corregir, únicamente cambia tu widget.

**Error 2: el estilo afecta a otra extensión.**
- **Ubicación:** selector CSS global.
- **Síntoma:** otra extensión cambia de aspecto sin haberlo pedido.
- **Causa:** el selector de tipo se aplica a todos los widgets del mismo tipo.
- **Corrección:** limita el selector a una clase propia. Después de corregir, el estilo queda aislado.

#### 4.2.3. Selector por `GTypeName` para subclases registradas

Si creas una subclase de un widget St con `GObject.registerClass`, puedes asignarle un `GTypeName` y usar ese nombre en CSS.

```javascript
import GObject from 'gi://GObject';
import St from 'gi://St';

const MiBoton = GObject.registerClass(
    {GTypeName: 'MiBoton'},
    class MiBoton extends St.Button {}
);
```

> **Qué hace este bloque:** `GObject.registerClass` registra una clase nueva. `GTypeName: 'MiBoton'` le da un nombre de tipo. La clase `MiBoton` hereda de `St.Button`. Después puedes usar `MiBoton { ... }` en CSS. Este patrón es opcional; no lo necesitas para el proyecto.

**Errores posibles en este bloque:**

**Error 1: `ReferenceError: GObject is not defined`.**
- **Ubicación:** línea de `GObject.registerClass`.
- **Síntoma:** la extensión no carga.
- **Causa:** falta el import de GObject.
- **Corrección:** añade `import GObject from 'gi://GObject';`. Después de corregir, el archivo se carga.

**Error 2: el selector `MiBoton` no se aplica.**
- **Ubicación:** CSS.
- **Síntoma:** la subclase no recibe el estilo.
- **Causa:** el `GTypeName` no coincide con el selector o la clase no está registrada.
- **Corrección:** usa exactamente el mismo nombre en `GTypeName` y en CSS. Después de corregir, el estilo se aplica.

### 4.3. Propiedades CSS soportadas y no soportadas

#### 4.3.1. Color, fondo, bordes, espaciado y tipografía

St entiende un subconjunto de CSS. Estas propiedades funcionan en la mayoría de los widgets:

| Propiedad | Efecto |
| :--- | :--- |
| `color` | Color del texto y de algunos iconos |
| `background-color` | Color de fondo |
| `border` | Borde |
| `border-radius` | Esquinas redondeadas |
| `padding` | Espacio interior |
| `margin` | Espacio exterior |
| `font-family` | Tipo de letra |
| `font-size` | Tamaño de letra |
| `font-weight` | Grosor de letra |
| `box-shadow` | Sombra |
| `transition-duration` | Duración de una transición de estilo |
| `transition-timing-function` | Curva de la transición |

Ejemplo de hoja de estilos:

```css
.mi-primera-extension {
    background-color: #3584e4;
    border: 1px solid #1c71d8;
    border-radius: 8px;
    padding: 0 8px;
    box-shadow: 0 1px 2px rgba(0, 0, 0, 0.3);
    transition-duration: 150ms;
}
```

> **Qué hace este bloque:** cambia el fondo a azul, añade un borde azul más oscuro, redondea las esquinas, agrega espacio interior, aplica una sombra suave y prepara una transición de 150 milisegundos cuando cambie el estilo. Cada propiedad afecta únicamente al elemento con la clase `mi-primera-extension`.

**Errores posibles en este bloque:**

**Error 1: `Gio.IOErrorEnum: Error parsing stylesheet 'file:///.../stylesheet.css'; errcode:15`**
- **Ubicación:** `stylesheet.css`.
- **Síntoma:** GNOME Shell registra `Error parsing stylesheet` y el estilo no se aplica.
- **Causa:** falta una llave, un punto y coma o hay un carácter inválido.
- **Corrección:** revisa que cada regla tenga su llave de apertura y cierre. Después de corregir, el CSS se carga sin error.

**Error 2: una propiedad no tiene efecto.**
- **Ubicación:** propiedad CSS.
- **Síntoma:** no hay error en el log, pero el resultado visual no cambia.
- **Causa:** St no implementa esa propiedad o el selector no coincide.
- **Corrección:** comprueba la lista de propiedades soportadas y el selector. Después de corregir, el estilo se aplica.

#### 4.3.2. Lo que St no soporta igual que un navegador

St no es un navegador. Estas ideas de CSS web no funcionan igual:

- `display: flex` y `display: grid`.
- El modelo de caja completo de CSS.
- Las transiciones arbitrarias con `transition: all 0.3s`.
- Las animaciones CSS con `@keyframes`.
- El posicionamiento absoluto como en una página web.

Para organizar widgets, usa `St.BoxLayout`, `St.Bin` y las propiedades de expansión del Capítulo 3. Para mover o animar actores, usa Clutter.

**Errores posibles en este bloque:** este bloque es una lista de limitaciones, no un programa. No produce errores por sí mismo. Si usas una propiedad no soportada, el síntoma habitual es que no ocurre nada y el log no muestra un error.

#### 4.3.3. Lo que St sí soporta: `box-shadow` y transiciones limitadas

St soporta `box-shadow` y transiciones de estilo limitadas. `transition-duration` y `transition-timing-function` funcionan cuando cambia el nodo de tema de un widget, por ejemplo al pasar de un estado a otro. No sustituyen a las animaciones de Clutter.

```css
.mi-primera-extension {
    box-shadow: 0 1px 2px rgba(0, 0, 0, 0.3);
    transition-duration: 150ms;
    transition-timing-function: ease;
}
```

> **Qué hace este bloque:** añade una sombra y una transición de 150 ms con curva `ease`. La transición se aplica al cambio de estilo del widget. Si quieres mover o escalar un actor, usa `actor.ease()` o `easeAsync()` del Capítulo 3.

**Errores posibles en este bloque:**

**Error 1: la sombra no se ve.**
- **Ubicación:** propiedad `box-shadow`.
- **Síntoma:** el botón no muestra la sombra.
- **Causa:** la sombra se recorta por el tamaño del panel o el fondo es transparente.
- **Corrección:** revisa el `padding` y el fondo del widget; prueba la sombra en un actor más grande. Después de corregir, la sombra se ve.

**Error 2: la transición no se nota.**
- **Ubicación:** `transition-duration`.
- **Síntoma:** el cambio de color es instantáneo.
- **Causa:** el estado no cambia o St no considera que el nodo de tema haya cambiado.
- **Corrección:** comprueba que el estado `:hover` o `:active` se activa y que la duración es mayor que cero. Después de corregir, la transición se nota.

#### 4.3.4. Estados (`:hover`, `:active`) y su funcionamiento limitado en St

Los estados permiten cambiar el estilo cuando el puntero está encima o cuando se pulsa el botón.

```css
.mi-primera-extension:hover {
    background-color: #1c71d8;
}

.mi-primera-extension:active {
    background-color: #1a5fb4;
}
```

> **Qué hace este bloque:** `:hover` se aplica cuando el puntero está sobre el botón. `:active` se aplica mientras se mantiene pulsado. Los colores son más oscuros que el fondo normal. St reconoce estos estados en muchos widgets, pero su comportamiento puede variar según el widget y el tema.

**Errores posibles en este bloque:**

**Error 1: el estado `:hover` no cambia.**
- **Ubicación:** selector CSS.
- **Síntoma:** el botón no reacciona al pasar el puntero.
- **Causa:** el widget no emite el estado o el selector no coincide con la clase.
- **Corrección:** comprueba la clase del botón y prueba el estado en un `St.Button` normal. Después de corregir, el cambio se ve.

**Error 2: el estado `:active` se queda pegado.**
- **Ubicación:** selector CSS.
- **Síntoma:** el botón mantiene el color pulsado después de soltar.
- **Causa:** el estado no se actualizó o el estilo se aplicó con demasiada prioridad.
- **Corrección:** evita `!important` y revisa el estado del widget. Después de corregir, el color vuelve al estado normal.

### 4.4. Estilos en línea con `set_style()`

`set_style()` aplica CSS directamente a un actor. Tiene prioridad sobre la hoja de estilos.

```javascript
this._indicador.set_style('background-color: #3584e4;');
```

> **Qué hace este bloque:** `set_style` recibe una cadena de CSS y la aplica al actor. La propiedad `background-color` cambia el fondo. El punto y coma final cierra la declaración. El estilo en línea gana sobre `.mi-primera-extension` en la hoja.

**Errores posibles en este bloque:**

**Error 1: el estilo no se aplica.**
- **Ubicación:** llamada a `set_style`.
- **Síntoma:** el botón conserva el color anterior.
- **Causa:** la cadena tiene un error de escritura o el actor no es el correcto.
- **Corrección:** revisa la cadena y usa el mismo actor que añadiste al panel. Después de corregir, el estilo se aplica.

**Error 2: el estilo en línea impide cambiar el color desde la hoja.**
- **Ubicación:** `set_style` y `stylesheet.css`.
- **Síntoma:** los cambios de la hoja no se ven.
- **Causa:** el estilo en línea tiene prioridad.
- **Corrección:** usa `set_style(null)` para limpiar el estilo en línea o reserva `set_style` para valores dinámicos. Después de corregir, la hoja vuelve a aplicar su color.

`set_style` es útil para un valor que cambia en tiempo de ejecución, por ejemplo un color elegido por el usuario. Para el estilo base, usa `stylesheet.css`.

**Comprueba que lo entiendes:** explica cuándo usarías `stylesheet.css` y cuándo `set_style()`. Después indica cuál tiene más prioridad.

### 4.5. Depuración de CSS

#### 4.5.1. Verificación de carga con Looking Glass Evaluator

Looking Glass es la consola de depuración del shell. Se abre con `Alt+F2`, se escribe `lg` y se pulsa Enter. En el Evaluator puedes inspeccionar actores y comprobar clases.

Para comprobar que tu botón tiene la clase, puedes inspeccionar el actor del indicador desde el código de la extensión o desde el Inspector. Si el CSS no se aplica, primero confirma que el archivo se cargó y que la clase existe.

#### 4.5.2. Inspección de propiedades computadas en el Inspector

El Inspector de Looking Glass permite seleccionar un actor y ver sus propiedades. Úsalo para comprobar:

- La clase del actor.
- El tamaño y la posición.
- El estilo computado.
- Si el actor está dentro del panel.

No necesitas memorizar el Inspector. La idea es que puedas comprobar el estado real del widget en lugar de suponerlo.

#### 4.5.3. El ciclo de recarga en Wayland

En Wayland, los cambios de CSS pueden requerir cerrar sesión y volver a entrar. El flujo recomendado es:

1. Guarda `stylesheet.css`.
2. Desactiva y activa la extensión.
3. Comprueba el resultado.
4. Si el cambio no aparece, cierra sesión y vuelve a entrar.

No uses `Alt+F2` seguido de `r` para reiniciar el shell en Wayland: ese atajo no reinicia el shell en esta sesión.

**Errores posibles en este bloque:**

**Error 1: el CSS antiguo sigue aplicándose.**
- **Ubicación:** `stylesheet.css`.
- **Síntoma:** el cambio no aparece después de guardar.
- **Causa:** el shell mantiene el estilo anterior en caché.
- **Corrección:** desactiva y activa la extensión; si persiste, cierra sesión y vuelve a entrar. Después de corregir, el CSS nuevo se aplica.

**Error 2: el Inspector no muestra tu actor.**
- **Ubicación:** Looking Glass.
- **Síntoma:** no encuentras el botón en el árbol.
- **Causa:** la extensión no está activa o el actor no se añadió al panel.
- **Corrección:** comprueba el estado con `gnome-extensions info` y revisa `addToStatusArea`. Después de corregir, el actor aparece.

## Ejemplo guiado paso a paso

Vas a crear `stylesheet.css` para el botón del Capítulo 3. No necesitas cambiar `extension.js`: la clase `mi-primera-extension` ya está añadida.

### Bloque 1: color y forma del botón

```css
.mi-primera-extension {
    background-color: #3584e4;
    border: 1px solid #1c71d8;
    border-radius: 8px;
    padding: 0 8px;
}
```

> **Qué hace este bloque:** selecciona el botón por su clase, cambia el fondo a azul, añade un borde, redondea las esquinas y agrega espacio interior. El resto de botones del panel no se ven afectados.

**Errores posibles en este bloque:**

**Error 1: `Gio.IOErrorEnum: Error parsing stylesheet 'file:///.../stylesheet.css'; errcode:15`**
- **Ubicación:** `stylesheet.css`.
- **Síntoma:** el log muestra `Error parsing stylesheet` y el botón no cambia.
- **Causa:** falta una llave o un punto y coma.
- **Corrección:** revisa la llave de cierre de la regla. Después de corregir, el CSS se carga.

**Error 2: el botón no cambia.**
- **Ubicación:** selector CSS y clase del botón.
- **Síntoma:** no hay error, pero el color sigue igual.
- **Causa:** la clase del CSS no coincide con la clase del botón.
- **Corrección:** usa `.mi-primera-extension` y `add_style_class_name('mi-primera-extension')`. Después de corregir, el estilo se aplica.

### Bloque 2: estados del botón

```css
.mi-primera-extension:hover {
    background-color: #1c71d8;
}

.mi-primera-extension:active {
    background-color: #1a5fb4;
}
```

> **Qué hace este bloque:** cambia el fondo cuando el puntero está encima y cuando el botón se pulsa. Los estados usan la misma clase y añaden `:hover` o `:active`.

**Errores posibles en este bloque:**

**Error 1: el estado no se ve.**
- **Ubicación:** selector CSS.
- **Síntoma:** el botón no cambia al pasar el puntero.
- **Causa:** el selector no coincide o el widget no emite el estado.
- **Corrección:** comprueba la clase y prueba el estado en un `St.Button` normal. Después de corregir, el cambio se ve.

**Error 2: el color se queda fijo.**
- **Ubicación:** `:active`.
- **Síntoma:** el botón mantiene el color de pulsado.
- **Causa:** el estado no se restableció o hay un estilo en línea con prioridad.
- **Corrección:** elimina `set_style` temporalmente y comprueba el estado. Después de corregir, el color vuelve a la normalidad.

### Guarda y recarga

Guarda `stylesheet.css`, desactiva y activa la extensión:

```bash
gnome-extensions disable miprimeraextension@ejemplo.local
gnome-extensions enable miprimeraextension@ejemplo.local
```

Si el color no cambia, cierra sesión y vuelve a entrar. En Wayland, `Alt+F2` seguido de `r` no reinicia el shell.

**Errores posibles en este bloque:**

**Error 1: el cambio no se aplica.**
- **Ubicación:** `stylesheet.css`.
- **Síntoma:** el botón conserva el color anterior.
- **Causa:** el shell mantiene el estilo en caché.
- **Corrección:** desactiva y activa; si persiste, cierra sesión. Después de corregir, el CSS nuevo se aplica.

**Error 2: la extensión no se activa.**
- **Ubicación:** `gnome-extensions enable`.
- **Síntoma:** la terminal muestra que la extensión no existe o queda en `ERROR`.
- **Causa:** el UUID está mal escrito o el CSS tiene un error de carga.
- **Corrección:** copia el UUID de `gnome-extensions list` y revisa el log. Después de corregir, la extensión se activa.

## Verifica que funciona

Activa la extensión y observa el panel:

```bash
gnome-extensions enable miprimeraextension@ejemplo.local
```

El botón debe tener fondo azul, borde y esquinas redondeadas. Al pasar el puntero, el fondo debe oscurecerse; al pulsar, debe oscurecerse más.

Comprueba el log:

```bash
journalctl /usr/bin/gnome-shell -f -o cat
```

No debe aparecer `Error parsing stylesheet`. Si aparece, revisa las llaves y los puntos y coma de `stylesheet.css`.

**Errores posibles en este bloque:**

**Error 1: el botón no cambia de color.**
- **Ubicación:** panel superior.
- **Síntoma:** el botón sigue con el estilo del tema.
- **Causa:** el CSS no se cargó, la clase no coincide o el archivo está mal nombrado.
- **Corrección:** revisa `stylesheet.css`, la clase y el log. Después de corregir, el color cambia.

**Error 2: el log muestra un error de parseo.**
- **Ubicación:** `stylesheet.css`.
- **Síntoma:** `Error parsing stylesheet` en `journalctl`.
- **Causa:** falta una llave o un punto y coma.
- **Corrección:** corrige la línea indicada. Después de corregir, el CSS se carga.

## Errores de integración del capítulo

### Error 1: el archivo se llama `style.css`

**Síntoma:** el botón no cambia y el log no muestra errores.

**Causa:** GNOME Shell busca `stylesheet.css`, no `style.css`.

**Corrección:** renombra el archivo a `stylesheet.css`. Después de corregir, el estilo se carga.

### Error 2: el selector no coincide con la clase

**Síntoma:** el CSS se carga, pero el botón no cambia.

**Causa:** el CSS usa `.mi-boton` y el código usa `mi-primera-extension`.

**Corrección:** usa la misma clase en los dos lugares. Después de corregir, el estilo se aplica.

### Error 3: falta una llave de cierre

**Síntoma:**

```text
Gio.IOErrorEnum: Error parsing stylesheet 'file:///.../stylesheet.css'; errcode:15
```

**Causa:** una regla CSS no tiene su llave de cierre.

**Corrección:** añade la llave `}` al final de la regla. Después de corregir, el CSS se carga.

### Error 4: el estilo global afecta a otros widgets

**Síntoma:** el color cambia en menús o etiquetas que no son tuyos.

**Causa:** usaste un selector de tipo como `StLabel`.

**Corrección:** limita el selector a una clase propia. Después de corregir, únicamente cambia tu widget.

### Error 5: el estilo en línea impide el cambio

**Síntoma:** cambias `stylesheet.css`, pero el botón conserva el color anterior.

**Causa:** `set_style()` tiene prioridad sobre la hoja.

**Corrección:** elimina o limpia el estilo en línea con `set_style(null)`. Después de corregir, la hoja vuelve a aplicar su color.

## Ejercicios

### Reproductivo

1. Crea `stylesheet.css` en la carpeta de la extensión.
2. Copia la regla `.mi-primera-extension` con fondo, borde, radio y padding.
3. Activa la extensión y comprueba el color del botón.
4. Añade los estados `:hover` y `:active`.
5. Comprueba que el botón reacciona al puntero y al clic.

### Aplicativo

1. Cambia el color de fondo por `#e66100`.
2. Cambia el borde por `#c64600`.
3. Cambia el radio a `12px` y el padding a `0 12px`.
4. Activa la extensión y comprueba el resultado.
5. Desactiva y vuelve a activar para confirmar que el estilo persiste.

### Creativo

1. Añade una sombra con `box-shadow`.
2. Añade `transition-duration: 200ms` y `transition-timing-function: ease`.
3. Haz que el estado `:hover` cambie el color y la sombra.
4. Comprueba el resultado y explica qué propiedad controla la duración.
5. Si no ves la sombra, explica por qué el tamaño del panel puede recortarla.

**Rúbrica del ejercicio creativo:**

- La sombra aparece en el botón.
- La transición se nota al pasar de normal a `:hover`.
- El cambio no afecta a otros botones.
- El CSS no produce errores en el log.
- La explicación menciona `box-shadow` y `transition-duration`.

## Autoevaluación

1. ¿Cómo descubre GNOME Shell el archivo `stylesheet.css`?
2. ¿Por qué es preferible una clase a un selector de tipo?
3. ¿Qué propiedades de CSS web no funcionan igual en St?
4. ¿Cuándo usarías `set_style()` en lugar de `stylesheet.css`?
5. Tu botón no cambia de color y el log no muestra errores. ¿Qué tres cosas revisarías y en qué orden?
6. ¿Qué ocurre si el CSS tiene una llave sin cerrar?

## Respuestas razonadas

1. Por convención de nombre. GNOME Shell busca `stylesheet.css` en la raíz de la extensión y lo carga automáticamente al activarla. No se importa desde `extension.js`.

2. Porque una clase se aplica únicamente a los widgets que la llevan. Un selector de tipo, como `StLabel`, afecta a todos los widgets de ese tipo en el shell y puede contaminar otras extensiones o partes de GNOME.

3. `display: flex`, `display: grid`, el modelo de caja completo, las animaciones `@keyframes` y las transiciones arbitrarias. Para organizar widgets se usan los contenedores de St; para animar actores se usa Clutter.

4. `set_style()` se usa para un valor que cambia en tiempo de ejecución, por ejemplo un color elegido por el usuario. Para el estilo base se usa `stylesheet.css`, que es más fácil de mantener y de depurar.

5. Primero comprobaría que el archivo se llama `stylesheet.css` y está en la raíz de la extensión. Después revisaría que la clase del CSS coincide con la clase del botón. Finalmente desactivaría y activaría la extensión; si persiste, cerraría sesión y volvería a entrar. El orden va del archivo al selector y de ahí a la recarga.

6. GNOME Shell no puede analizar el CSS y registra `Error parsing stylesheet`. La regla no se aplica hasta que se corrige la llave.

## Soluciones de los ejercicios

### Solución del reproductivo

El archivo `stylesheet.css` debe contener:

```css
.mi-primera-extension {
    background-color: #3584e4;
    border: 1px solid #1c71d8;
    border-radius: 8px;
    padding: 0 8px;
}

.mi-primera-extension:hover {
    background-color: #1c71d8;
}

.mi-primera-extension:active {
    background-color: #1a5fb4;
}
```

**Errores posibles en este bloque:**

**Error 1: `Error parsing stylesheet`.**
- **Ubicación:** `stylesheet.css`.
- **Síntoma:** el estilo no se aplica.
- **Causa:** falta una llave o un punto y coma.
- **Corrección:** revisa las llaves y los puntos y coma. Después de corregir, el CSS se carga.

**Error 2: el botón no cambia.**
- **Ubicación:** selector CSS.
- **Síntoma:** no hay error, pero el color sigue igual.
- **Causa:** la clase no coincide con la del botón.
- **Corrección:** usa `.mi-primera-extension` y comprueba `add_style_class_name`. Después de corregir, el estilo se aplica.

### Solución del aplicativo

Los valores nuevos pueden ser:

```css
.mi-primera-extension {
    background-color: #e66100;
    border: 1px solid #c64600;
    border-radius: 12px;
    padding: 0 12px;
}
```

**Errores posibles en este bloque:** si el color no cambia, revisa que el archivo sea `stylesheet.css` y que la clase coincida. Si el cambio no aparece, desactiva y activa la extensión.

### Solución del creativo

Una solución válida añade sombra y transición:

```css
.mi-primera-extension {
    background-color: #3584e4;
    border-radius: 8px;
    padding: 0 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
    transition-duration: 200ms;
    transition-timing-function: ease;
}

.mi-primera-extension:hover {
    background-color: #1c71d8;
    box-shadow: 0 3px 6px rgba(0, 0, 0, 0.4);
}
```

**Errores posibles en este bloque:**

**Error 1: la sombra no se ve.**
- **Ubicación:** `box-shadow`.
- **Síntoma:** el botón no muestra la sombra.
- **Causa:** el panel recorta la sombra o el fondo es transparente.
- **Corrección:** aumenta el padding del botón y comprueba el fondo. Después de corregir, la sombra se ve.

**Error 2: la transición no se nota.**
- **Ubicación:** `transition-duration`.
- **Síntoma:** el cambio es instantáneo.
- **Causa:** el estado no cambia o la duración es demasiado corta.
- **Corrección:** usa `:hover` y una duración de 200 ms. Después de corregir, la transición se nota.

## Si quieres saber más

- Documentación de St: <https://gnome.pages.gitlab.gnome.org/gnome-shell/st/>
- Referencia de CSS en MDN: <https://developer.mozilla.org/es/docs/Web/CSS>
- Guía de GJS: <https://gjs.guide/>

No necesitas estos enlaces para continuar. El manual contiene lo necesario para el proyecto.

## Siguiente paso

Cuando el botón tenga el color y los estados que definiste, continúa con el Capítulo 5. Allí guardarás preferencias y crearás una ventana de configuración para que el usuario pueda cambiar el color sin editar el CSS.
