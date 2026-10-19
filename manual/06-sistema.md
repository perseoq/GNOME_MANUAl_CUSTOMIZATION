# Capítulo 6: Interacción con el Sistema (Meta y Mutter)

> En este capítulo harás que el botón del panel mueva la ventana activa a otro espacio de trabajo. Aprenderás a usar `global`, `Meta` y el gestor de espacios de GNOME.

## Qué vas a aprender

- Usar el objeto `global` y sus propiedades principales.
- Acceder a la ventana activa con `global.display.focus_window`.
- Minimizar, maximizar y mover ventanas con Meta.
- Conectar señales de ventanas y espacios de trabajo.
- Entender el modelo dinámico de espacios de trabajo.
- Reconocer las restricciones de Wayland para mover ventanas.

Al final del capítulo, el menú de tu botón tendrá una acción que mueve la ventana activa al espacio siguiente.

## Antes de empezar necesitas

- Haber completado los capítulos 1 a 5.
- Tener un botón en el panel con un menú.
- Tener la extensión activa y el color guardado en GSettings.
- GNOME Shell 50.6 sobre Wayland.
- Al menos una ventana abierta para probar el movimiento.

## El concepto, con una analogía

`global` es como el tablero de control de una sala. Desde allí puedes ver qué pantallas hay, qué ventanas están abiertas y qué espacios de trabajo existen. No necesitas construir el tablero: GNOME Shell ya lo pone a tu disposición.

Meta es el conserje que conoce las ventanas. Puede decirte cuál está enfocada, moverla a otro espacio, minimizarla o maximizarla. Mutter es el compositor que aplica esas órdenes y dibuja el resultado.

En Wayland, el conserje tiene reglas más estrictas: puede mover una ventana entre espacios y minimizarla, pero no puede colocarla en una posición absoluta de la pantalla como si fuera una ventana de X11.

### 6.1. El objeto `global` y el acceso a Mutter

#### 6.1.1. `global.display` y `global.workspace_manager`

`global` es un objeto que GNOME Shell ofrece a las extensiones. No se importa. Sus propiedades principales son:

| Propiedad | Para qué sirve |
| :--- | :--- |
| `global.display` | Pantalla, ventanas y señales del compositor |
| `global.workspace_manager` | Espacios de trabajo |
| `global.get_window_actors()` | Actores visuales de las ventanas |
| `global.get_current_time()` | Marca de tiempo para operaciones del compositor |

Ejemplo de lectura:

```javascript
const ventana = global.display.focus_window;
const espacio = global.workspace_manager.get_active_workspace();

console.log(ventana ? ventana.get_title() : 'Sin ventana activa');
console.log(`Espacio actual: ${espacio.index() + 1}`);
```

> **Qué hace este bloque:** `global.display.focus_window` devuelve la ventana que tiene el foco, o `null` si no hay ninguna. `global.workspace_manager.get_active_workspace()` devuelve el espacio de trabajo activo. `get_title()` devuelve el título de la ventana. `index()` devuelve el número de espacio empezando por cero; por eso se suma uno para mostrarlo al usuario.

**Errores posibles en este bloque:**

**Error 1: `ReferenceError: global is not defined`**
- **Ubicación:** `extension.js`.
- **Síntoma:** la extensión no carga.
- **Causa:** usaste `global` en `prefs.js` o en un proceso que no es GNOME Shell.
- **Corrección:** usa `global` únicamente en `extension.js`. En `prefs.js` no existe. Después de corregir, el archivo se carga.

**Error 2: `TypeError: global.display.focus_window is undefined`**
- **Ubicación:** línea de `focus_window`.
- **Síntoma:** la extensión falla al leer la ventana.
- **Causa:** `global.display` no es el objeto esperado o la propiedad no existe en esa versión.
- **Corrección:** comprueba que usas GNOME Shell 50.6 y que el código está en `extension.js`. Después de corregir, la propiedad existe.

**Error 3: `TypeError: espacio is undefined`**
- **Ubicación:** línea de `get_active_workspace()`.
- **Síntoma:** la extensión falla al leer el espacio.
- **Causa:** `global.workspace_manager` no está disponible en ese contexto.
- **Corrección:** usa `global.workspace_manager` dentro de `extension.js`. Después de corregir, el espacio se obtiene.

#### 6.1.2. `global.get_window_actors()` para inspeccionar ventanas

`global.get_window_actors()` devuelve una lista de actores visuales de las ventanas. Cada actor tiene una ventana asociada.

```javascript
const actores = global.get_window_actors();

console.log(`Ventanas visibles: ${actores.length}`);

for (const actor of actores) {
    const ventana = actor.meta_window;
    console.log(ventana.get_title());
}
```

> **Qué hace este bloque:** `get_window_actors()` devuelve los actores de las ventanas. `actores.length` cuenta cuántos hay. El bucle recorre cada actor, obtiene su `meta_window` y muestra el título. Este bloque es útil para depurar, no para el proyecto principal.

**Errores posibles en este bloque:**

**Error 1: `TypeError: actores is not iterable`**
- **Ubicación:** bucle `for...of`.
- **Síntoma:** la extensión falla al recorrer la lista.
- **Causa:** `get_window_actors()` no devolvió una lista en ese contexto.
- **Corrección:** comprueba que estás en `extension.js` y en GNOME Shell 50.6. Después de corregir, la lista se recorre.

**Error 2: `TypeError: actor.meta_window is undefined`**
- **Ubicación:** dentro del bucle.
- **Síntoma:** el log muestra `undefined` o falla al llamar a `get_title()`.
- **Causa:** el actor no tiene una ventana asociada o la propiedad cambió.
- **Corrección:** comprueba que el actor es una ventana y no otro elemento del escenario. Después de corregir, el título aparece.

**Comprueba que lo entiendes:** explica la diferencia entre `global.display.focus_window` y `global.get_window_actors()`. ¿Cuál usarías para mover la ventana activa?

### 6.2. Manipulación de ventanas con Meta

#### 6.2.1. Obtener la ventana enfocada: `global.display.focus_window`

La ventana enfocada es la que recibe el teclado. Para moverla, primero comprueba que existe:

```javascript
const ventana = global.display.focus_window;

if (!ventana) {
    console.log('No hay una ventana activa');
    return;
}
```

> **Qué hace este bloque:** lee `focus_window`. Si es `null`, escribe un mensaje y termina. La comprobación evita llamar a métodos sobre `null`.

**Errores posibles en este bloque:**

**Error 1: `TypeError: Cannot read property 'change_workspace' of null`**
- **Ubicación:** línea que usa `ventana.change_workspace`.
- **Síntoma:** la extensión falla cuando no hay ventana activa.
- **Causa:** no comprobaste si `focus_window` era `null`.
- **Corrección:** añade la comprobación `if (!ventana)`. Después de corregir, el código no falla sin ventana.

**Error 2: la ventana obtenida no es la que esperabas.**
- **Ubicación:** `focus_window`.
- **Síntoma:** se mueve otra ventana.
- **Causa:** el foco cambió antes de ejecutar la acción.
- **Corrección:** ejecuta la acción desde el menú del panel y comprueba el título en el log. Después de corregir, mueves la ventana correcta.

#### 6.2.2. Operaciones: `minimize()`, `maximize()` y mover a otro espacio

Meta ofrece métodos para modificar una ventana:

| Método | Efecto |
| :--- | :--- |
| `ventana.minimize()` | Minimiza la ventana |
| `ventana.maximize()` | Maximiza la ventana |
| `ventana.unmaximize()` | Restaura el tamaño anterior |
| `ventana.change_workspace(espacio)` | Mueve la ventana a otro espacio |

Ejemplo de minimizar:

```javascript
if (ventana)
    ventana.minimize();
```

> **Qué hace este bloque:** si hay una ventana, llama a `minimize()`. La comprobación evita un error cuando no hay ventana. El método puede fallar si la ventana ya está minimizada o si el compositor no lo permite en ese estado.

**Errores posibles en este bloque:**

**Error 1: la ventana no se minimiza.**
- **Ubicación:** llamada a `minimize()`.
- **Síntoma:** la ventana sigue visible.
- **Causa:** la ventana ya estaba minimizada o el compositor no permite la operación en ese estado.
- **Corrección:** comprueba el estado de la ventana y prueba con una ventana normal. Después de corregir, la ventana se minimiza.

**Error 2: `TypeError: ventana.minimize is not a function`**
- **Ubicación:** línea de `minimize()`.
- **Síntoma:** la extensión falla.
- **Causa:** el objeto no es una `Meta.Window`.
- **Corrección:** obtén la ventana con `global.display.focus_window`. Después de corregir, el método existe.

#### 6.2.3. Conexión a señales: `window-created` y `focus-window-changed`

El display emite señales cuando se crea una ventana o cambia el foco. Puedes conectarte para reaccionar. La señal `window-created` se usa igual que `focus-window-changed`; cambia el nombre de la señal y el callback recibe la ventana creada.

```javascript
this._focusChangedId = global.display.connect('focus-window-changed', () => {
    const ventana = global.display.focus_window;
    console.log(ventana ? ventana.get_title() : 'Sin ventana');
});
```

> **Qué hace este bloque:** conecta una función a la señal `focus-window-changed`. Cada vez que cambia la ventana enfocada, la función lee el título y lo escribe en el log. `connect` devuelve un identificador que se guarda en `this._focusChangedId`.

**Errores posibles en este bloque:**

**Error 1: el mensaje aparece varias veces.**
- **Ubicación:** `connect`.
- **Síntoma:** un único cambio de foco genera varios mensajes.
- **Causa:** la señal se conectó más de una vez.
- **Corrección:** desconecta la conexión anterior antes de conectar de nuevo o hazlo únicamente en `enable()`. Después de corregir, cada cambio genera un mensaje.

**Error 2: `TypeError: global.display.connect is not a function`**
- **Ubicación:** línea de `connect`.
- **Síntoma:** la extensión falla.
- **Causa:** `global.display` no es el objeto esperado o el código se ejecuta fuera del shell.
- **Corrección:** usa `global.display` dentro de `extension.js`. Después de corregir, la señal se conecta.

No olvides desconectar la señal en `disable()`:

```javascript
if (this._focusChangedId) {
    global.display.disconnect(this._focusChangedId);
    this._focusChangedId = 0;
}
```

> **Qué hace este bloque:** comprueba que existe un identificador y llama a `disconnect()`. Después lo pone en `0` para evitar desconectar dos veces. Sin esta limpieza, la función sigue ejecutándose después de desactivar.

**Errores posibles en este bloque:**

**Error 1: la función sigue ejecutándose después de desactivar.**
- **Ubicación:** `disable()`.
- **Síntoma:** el log sigue mostrando títulos después de desactivar la extensión.
- **Causa:** la señal no se desconectó.
- **Corrección:** llama a `disconnect()` con el identificador guardado. Después de corregir, la señal deja de ejecutarse.

**Error 2: `TypeError: global.display.disconnect is not a function`**
- **Ubicación:** línea de `disconnect`.
- **Síntoma:** la extensión falla al desactivar.
- **Causa:** el identificador no corresponde a una conexión de `global.display`.
- **Corrección:** guarda el identificador devuelto por `connect` en el mismo objeto. Después de corregir, la desconexión funciona.

### 6.3. Gestión de espacios de trabajo

#### 6.3.1. Modelo dinámico de espacios de trabajo en GNOME 50

GNOME usa un modelo dinámico: la cantidad de espacios puede cambiar según las ventanas abiertas. El espacio activo se obtiene con:

```javascript
const actual = global.workspace_manager.get_active_workspace();
console.log(`Espacio activo: ${actual.index() + 1}`);
```

> **Qué hace este bloque:** obtiene el espacio activo y muestra su número. `index()` empieza en cero; se suma uno para que el usuario vea el número natural. La cantidad de espacios puede cambiar; no escribas código que suponga un número fijo.

**Errores posibles en este bloque:**

**Error 1: el número mostrado no coincide con el de la interfaz.**
- **Ubicación:** `index()`.
- **Síntoma:** el log dice `0` y la interfaz muestra `1`.
- **Causa:** `index()` empieza en cero.
- **Corrección:** suma uno al valor que muestras al usuario. Después de corregir, los números coinciden.

**Error 2: `TypeError: actual is undefined`**
- **Ubicación:** `get_active_workspace()`.
- **Síntoma:** la extensión falla.
- **Causa:** el gestor de espacios no está disponible.
- **Corrección:** usa `global.workspace_manager` dentro de `extension.js`. Después de corregir, el espacio existe.

#### 6.3.2. Cambio y reordenamiento de ventanas entre espacios

Para mover una ventana al espacio siguiente, usa `get_neighbor()` y `change_workspace()`:

```javascript
_moverVentana() {
    const ventana = global.display.focus_window;

    if (!ventana) {
        Main.notify('Mi Primera Extensión', 'No hay una ventana activa.');
        return;
    }

    const actual = global.workspace_manager.get_active_workspace();
    let siguiente = actual.get_neighbor(Meta.MotionDirection.RIGHT);

    if (siguiente === actual)
        siguiente = global.workspace_manager.append_new_workspace(
            false,
            global.get_current_time()
        );

    ventana.change_workspace(siguiente);
    console.log(`[${this.uuid}] ventana movida al espacio ${siguiente.index() + 1}`);
    Main.notify(
        'Mi Primera Extensión',
        `Ventana movida al espacio ${siguiente.index() + 1}.`
    );
}
```

> **Qué hace este bloque:** obtiene la ventana activa. Si no hay, muestra una notificación y termina. Obtiene el espacio actual y su vecino a la derecha. Si no hay vecino, crea uno nuevo con `append_new_workspace(false, ...)`; el primer argumento `false` indica que no se active automáticamente. Después mueve la ventana con `change_workspace()` y muestra una notificación. `global.get_current_time()` proporciona la marca de tiempo que pide el compositor.

**Errores posibles en este bloque:**

**Error 1: `ReferenceError: Meta is not defined`**
- **Ubicación:** línea de `Meta.MotionDirection.RIGHT`.
- **Síntoma:** la extensión queda en `ERROR`.
- **Causa:** falta el import `import Meta from 'gi://Meta';`.
- **Corrección:** añade el import al principio del archivo. Después de corregir, `Meta` existe.

**Error 2: `TypeError: ventana.change_workspace is not a function`**
- **Ubicación:** línea de `change_workspace`.
- **Síntoma:** la ventana no se mueve.
- **Causa:** `ventana` no es una `Meta.Window`.
- **Corrección:** usa `global.display.focus_window` y comprueba que no es `null`. Después de corregir, el método existe.

**Error 3: no se crea un espacio nuevo.**
- **Ubicación:** `append_new_workspace`.
- **Síntoma:** la ventana no se mueve si únicamente hay un espacio.
- **Causa:** el modelo de espacios no permite crear otro o el parámetro de activación es incorrecto.
- **Corrección:** comprueba que usas `append_new_workspace(false, global.get_current_time())` y que el modelo dinámico está activo. Después de corregir, el espacio se crea.

**Error 4: la ventana se mueve, pero no se ve el cambio.**
- **Ubicación:** panel superior.
- **Síntoma:** la notificación aparece, pero sigues en el mismo espacio.
- **Causa:** el código mueve la ventana, pero no activa el espacio destino.
- **Corrección:** este capítulo mueve la ventana sin cambiar de espacio; cambia de espacio con la vista de Actividades o añade `siguiente.activate(global.get_current_time())` como ejercicio. Después de corregir, ves la ventana en el espacio destino.

### 6.4. Restricciones específicas de Wayland en GNOME 50

Wayland protege la privacidad y la integridad de las ventanas. Algunas operaciones de X11 ya no están permitidas:

| Operación | En Wayland |
| :--- | :--- |
| Minimizar | Funciona |
| Maximizar | Funciona |
| Mover a otro espacio | Funciona |
| Cambiar el foco | Funciona con las APIs de Meta |
| Colocar una ventana en una posición absoluta | No está permitido |
| Mover una ventana con `move_frame()` o `move_resize_frame()` | Puede ignorarse o fallar |
| Leer la posición global de una ventana | Limitado |

Una **posición absoluta** es una coordenada exacta de la pantalla, por ejemplo `x=100, y=200`. Wayland no permite que una extensión coloque una ventana arbitrariamente en esas coordenadas. Sí permite moverla entre espacios y cambiar su estado.

**Errores posibles en este bloque:** este bloque es una tabla de limitaciones, no un programa. No produce errores por sí mismo. Si intentas posicionar una ventana de forma absoluta, el síntoma habitual es que la operación se ignora o no tiene efecto.

**Comprueba que lo entiendes:** enumera tres operaciones que funcionan en Wayland y una que no está permitida. Después explica qué significa “posición absoluta”.

## Ejemplo guiado paso a paso

Vas a añadir una acción al menú del botón. La acción moverá la ventana activa al espacio siguiente.

### Bloque 1: importar Meta

```javascript
import Meta from 'gi://Meta';
```

> **Qué hace este bloque:** carga la biblioteca Meta, que contiene `MotionDirection` y las APIs de ventanas y espacios de trabajo. El import va al principio de `extension.js`.

**Errores posibles en este bloque:**

**Error 1: `ImportError: Unable to load file from: gi://Meta`**
- **Ubicación:** línea del import.
- **Síntoma:** la extensión no carga.
- **Causa:** el nombre de la biblioteca está mal escrito o no está disponible.
- **Corrección:** usa `gi://Meta` con mayúscula inicial. Después de corregir, el módulo se carga.

**Error 2: `ReferenceError: Meta is not defined`**
- **Ubicación:** línea que usa `Meta.MotionDirection`.
- **Síntoma:** la extensión queda en `ERROR`.
- **Causa:** falta el import de Meta.
- **Corrección:** añade el import al principio del archivo. Después de corregir, `Meta` existe.

### Bloque 2: añadir la acción al menú

Después de `Main.panel.addToStatusArea(...)` y antes del final de `enable()`, añade:

```javascript
this._indicador.menu.addAction(
    'Mover ventana al espacio siguiente',
    () => this._moverVentana()
);
```

> **Qué hace este bloque:** `this._indicador.menu` es el menú del botón. `addAction` añade una opción con un texto y una función. Cuando el usuario pulsa la opción, se ejecuta `_moverVentana()`. La función se pasa como callback; no se ejecuta en el momento de añadir la acción.

**Errores posibles en este bloque:**

**Error 1: la opción no aparece en el menú.**
- **Ubicación:** `this._indicador.menu.addAction`.
- **Síntoma:** el menú del botón está vacío.
- **Causa:** el botón se creó con `dontCreateMenu` en `true` o el menú no existe.
- **Corrección:** crea el botón con `new PanelMenu.Button(0.0, 'Mi Primera Extensión', false)`. Después de corregir, la opción aparece.

**Error 2: `TypeError: this._indicador.menu.addAction is not a function`**
- **Ubicación:** línea de `addAction`.
- **Síntoma:** la extensión queda en `ERROR`.
- **Causa:** `this._indicador` no es un `PanelMenu.Button` o el menú no está disponible.
- **Corrección:** usa `PanelMenu.Button` y comprueba que el botón se creó antes de añadir la acción. Después de corregir, el método existe.

### Bloque 3: mover la ventana

Añade el método `_moverVentana()` a la clase:

```javascript
_moverVentana() {
    const ventana = global.display.focus_window;

    if (!ventana) {
        Main.notify('Mi Primera Extensión', 'No hay una ventana activa.');
        return;
    }

    const actual = global.workspace_manager.get_active_workspace();
    let siguiente = actual.get_neighbor(Meta.MotionDirection.RIGHT);

    if (siguiente === actual)
        siguiente = global.workspace_manager.append_new_workspace(
            false,
            global.get_current_time()
        );

    ventana.change_workspace(siguiente);
    console.log(`[${this.uuid}] ventana movida al espacio ${siguiente.index() + 1}`);
    Main.notify(
        'Mi Primera Extensión',
        `Ventana movida al espacio ${siguiente.index() + 1}.`
    );
}
```

> **Qué hace este bloque:** comprueba que hay una ventana activa. Obtiene el espacio actual y el siguiente. Si no existe el siguiente, crea uno nuevo. Mueve la ventana y muestra una notificación con el número de espacio. El número se muestra sumando uno porque `index()` empieza en cero.

**Errores posibles en este bloque:**

**Error 1: `ReferenceError: Meta is not defined`**
- **Ubicación:** `Meta.MotionDirection.RIGHT`.
- **Síntoma:** la extensión queda en `ERROR`.
- **Causa:** falta el import de Meta.
- **Corrección:** añade `import Meta from 'gi://Meta';`. Después de corregir, el método funciona.

**Error 2: no hay ventana activa.**
- **Ubicación:** `global.display.focus_window`.
- **Síntoma:** aparece la notificación `No hay una ventana activa.`
- **Causa:** ninguna ventana tiene el foco.
- **Corrección:** haz clic en una ventana antes de usar la acción. Después de corregir, la ventana se mueve.

**Error 3: la ventana no cambia de espacio.**
- **Ubicación:** `change_workspace`.
- **Síntoma:** la notificación aparece, pero la ventana sigue en el mismo espacio.
- **Causa:** el espacio destino es el mismo o la ventana no es válida.
- **Corrección:** comprueba el log `ventana movida al espacio ...` y el índice. Después de corregir, la ventana cambia.

### Guarda y recarga

Guarda `extension.js` y recarga la extensión:

```bash
gnome-extensions disable miprimeraextension@ejemplo.local
gnome-extensions enable miprimeraextension@ejemplo.local
```

**Errores posibles en este bloque:**

**Error 1: la extensión no se activa.**
- **Ubicación:** `gnome-extensions enable`.
- **Síntoma:** el estado es `ERROR`.
- **Causa:** el import de Meta o el método `_moverVentana` tiene un error.
- **Corrección:** revisa el log y la sintaxis. Después de corregir, la extensión se activa.

**Error 2: la opción nueva no aparece.**
- **Ubicación:** menú del botón.
- **Síntoma:** el menú sigue igual.
- **Causa:** el módulo anterior sigue cargado.
- **Corrección:** desactiva y activa; si persiste, cierra sesión. Después de corregir, la opción aparece.

## Verifica que funciona

Abre una ventana, por ejemplo el Editor de texto, y haz clic en ella para que tenga el foco. Después abre el menú del botón y elige **Mover ventana al espacio siguiente**.

Debe aparecer una notificación con el número de espacio y el log debe mostrar:

```text
GNOME Shell-Message: 11:27:14.374: [miprimeraextension@ejemplo.local] ventana movida al espacio 2
```

Comprueba la vista de Actividades: la ventana debe estar en el espacio siguiente. Si únicamente había un espacio, la extensión crea uno nuevo.

**Errores posibles en este bloque:**

**Error 1: la notificación dice “No hay una ventana activa”.**
- **Ubicación:** `global.display.focus_window`.
- **Síntoma:** no se mueve ninguna ventana.
- **Causa:** ninguna ventana tiene el foco.
- **Corrección:** haz clic en una ventana antes de usar la acción. Después de corregir, la ventana se mueve.

**Error 2: la ventana no aparece en el espacio siguiente.**
- **Ubicación:** vista de Actividades.
- **Síntoma:** la ventana sigue en el espacio actual.
- **Causa:** el espacio destino es el mismo o `change_workspace` falló.
- **Corrección:** revisa el log y el número de espacios. Después de corregir, la ventana cambia.

**Error 3: `TypeError: ... is not a function`.**
- **Ubicación:** método `_moverVentana`.
- **Síntoma:** la extensión queda en `ERROR`.
- **Causa:** una API de Meta no existe en la versión o el objeto es `null`.
- **Corrección:** comprueba la versión 50.6 y las comprobaciones de `null`. Después de corregir, el método funciona.

## Errores de integración del capítulo

### Error 1: falta el import de Meta

**Síntoma:**

```text
ReferenceError: Meta is not defined
```

**Causa:** usaste `Meta.MotionDirection` sin importar la biblioteca.

**Corrección:** añade `import Meta from 'gi://Meta';`. Después de corregir, la constante existe.

### Error 2: no hay ventana activa

**Síntoma:** la notificación `No hay una ventana activa.` aparece.

**Causa:** `global.display.focus_window` es `null`.

**Corrección:** haz clic en una ventana antes de usar la acción. Después de corregir, la ventana se mueve.

### Error 3: el espacio siguiente no existe

**Síntoma:** la ventana no se mueve cuando únicamente hay un espacio.

**Causa:** `get_neighbor()` devuelve el mismo espacio.

**Corrección:** crea un espacio nuevo con `append_new_workspace(false, global.get_current_time())`. Después de corregir, la ventana se mueve al espacio nuevo.

### Error 4: la ventana se mueve, pero no la ves

**Síntoma:** la notificación aparece, pero sigues en el espacio actual.

**Causa:** el código mueve la ventana sin activar el espacio destino.

**Corrección:** abre la vista de Actividades y entra en el espacio destino, o añade `siguiente.activate(global.get_current_time())` como ejercicio. Después de corregir, ves la ventana.

### Error 5: intentas posicionar la ventana con coordenadas absolutas

**Síntoma:** la ventana no se mueve a la posición esperada.

**Causa:** Wayland no permite colocar ventanas en coordenadas absolutas desde una extensión.

**Corrección:** usa operaciones permitidas, como minimizar, maximizar o cambiar de espacio. Después de corregir, la operación tiene efecto.

## Ejercicios

### Reproductivo

1. Añade el import de Meta.
2. Añade la acción `Mover ventana al espacio siguiente` al menú.
3. Añade el método `_moverVentana()`.
4. Abre una ventana y dale el foco.
5. Usa la acción y comprueba la notificación y el log.
6. Comprueba la ventana en la vista de Actividades.

### Aplicativo

1. Cambia el texto de la acción por `Mover al espacio siguiente`.
2. Cambia la notificación para que incluya el título de la ventana.
3. Si no hay ventana, muestra un mensaje distinto.
4. Comprueba los dos casos: con ventana y sin ventana.
5. Explica qué línea comprueba si hay ventana.

### Creativo

1. Añade una segunda acción que mueva la ventana al espacio anterior.
2. Pista: usa `Meta.MotionDirection.LEFT` y comprueba si el espacio es el mismo.
3. Añade una tercera acción que minimice la ventana activa.
4. Comprueba que las tres acciones aparecen en el menú.
5. Explica qué diferencia hay entre `minimize()` y `change_workspace()`.

**Rúbrica del ejercicio creativo:**

- Las tres acciones aparecen en el menú.
- La acción de la izquierda mueve la ventana sin fallar en el primer espacio.
- La acción de minimizar funciona con una ventana activa.
- La extensión se desactiva sin errores.
- La explicación distingue estado de ventana y espacio de trabajo.

## Autoevaluación

1. ¿Qué es `global` y por qué no se importa?
2. ¿Qué diferencia hay entre `global.display` y `global.workspace_manager`?
3. ¿Qué hace `global.display.focus_window` y qué devuelve si no hay ventana?
4. ¿Cómo se mueve una ventana a otro espacio de trabajo?
5. Tu acción no mueve ninguna ventana. ¿Qué tres cosas revisarías y en qué orden?
6. ¿Qué operaciones de ventanas siguen funcionando en Wayland y cuáles no?

## Respuestas razonadas

1. `global` es un objeto que GNOME Shell pone a disposición de las extensiones. No se importa porque lo inyecta el propio shell. Contiene el display, el gestor de espacios y utilidades del compositor.

2. `global.display` representa la pantalla, las ventanas y las señales del compositor. `global.workspace_manager` gestiona los espacios de trabajo. El primero se usa para ventanas y foco; el segundo, para espacios.

3. `focus_window` devuelve la ventana que tiene el foco. Si no hay ninguna, devuelve `null`. Por eso hay que comprobarlo antes de llamar a sus métodos.

4. Se obtiene la ventana con `focus_window`, se obtiene el espacio destino con `get_neighbor()` o `append_new_workspace()`, y se llama a `ventana.change_workspace(espacio)`.

5. Primero comprobaría que hay una ventana activa y que el foco no es `null`. Después revisaría que el import de Meta existe y que `Meta.MotionDirection` está bien escrito. Finalmente comprobaría el espacio destino y el log. El orden va del estado de la ventana a la API.

6. En Wayland funcionan minimizar, maximizar, cambiar de espacio y cambiar el foco. No funciona colocar una ventana en una posición absoluta ni moverla con coordenadas arbitrarias como en X11.

## Soluciones de los ejercicios

### Solución del reproductivo

El import debe ser:

```javascript
import Meta from 'gi://Meta';
```

**Errores posibles en este bloque:**

**Error 1: `ImportError: Unable to load file from: gi://Meta`**
- **Ubicación:** línea del import.
- **Síntoma:** la extensión no carga.
- **Causa:** el nombre de la biblioteca está mal escrito.
- **Corrección:** usa `gi://Meta`. Después de corregir, el módulo se carga.

**Error 2: `ReferenceError: Meta is not defined`**
- **Ubicación:** línea que usa `Meta.MotionDirection`.
- **Síntoma:** la extensión queda en `ERROR`.
- **Causa:** falta el import.
- **Corrección:** añade el import al principio. Después de corregir, `Meta` existe.

La acción del menú:

```javascript
this._indicador.menu.addAction(
    'Mover ventana al espacio siguiente',
    () => this._moverVentana()
);
```

**Errores posibles en este bloque:**

**Error 1: la acción no aparece.**
- **Ubicación:** menú del botón.
- **Síntoma:** el menú está vacío.
- **Causa:** el botón se creó sin menú.
- **Corrección:** usa `false` como tercer argumento de `PanelMenu.Button`. Después de corregir, la acción aparece.

**Error 2: `TypeError: addAction is not a function`.**
- **Ubicación:** línea de `addAction`.
- **Síntoma:** la extensión falla.
- **Causa:** `this._indicador.menu` no es un menú.
- **Corrección:** crea el botón con `PanelMenu.Button`. Después de corregir, el método existe.

El método de movimiento:

```javascript
_moverVentana() {
    const ventana = global.display.focus_window;

    if (!ventana) {
        Main.notify('Mi Primera Extensión', 'No hay una ventana activa.');
        return;
    }

    const actual = global.workspace_manager.get_active_workspace();
    let siguiente = actual.get_neighbor(Meta.MotionDirection.RIGHT);

    if (siguiente === actual)
        siguiente = global.workspace_manager.append_new_workspace(
            false,
            global.get_current_time()
        );

    ventana.change_workspace(siguiente);
    console.log(`[${this.uuid}] ventana movida al espacio ${siguiente.index() + 1}`);
    Main.notify(
        'Mi Primera Extensión',
        `Ventana movida al espacio ${siguiente.index() + 1}.`
    );
}
```

**Errores posibles en este bloque:**

**Error 1: `ReferenceError: Meta is not defined`.**
- **Ubicación:** línea de `Meta.MotionDirection`.
- **Síntoma:** la extensión falla.
- **Causa:** falta el import.
- **Corrección:** añade `import Meta from 'gi://Meta';`. Después de corregir, el método funciona.

**Error 2: no hay ventana activa.**
- **Ubicación:** `focus_window`.
- **Síntoma:** aparece la notificación de que no hay ventana.
- **Causa:** ninguna ventana tiene el foco.
- **Corrección:** haz clic en una ventana. Después de corregir, la ventana se mueve.

### Solución del aplicativo

El texto de la acción puede ser:

```javascript
this._indicador.menu.addAction(
    'Mover al espacio siguiente',
    () => this._moverVentana()
);
```

**Errores posibles en este bloque:** si la opción no aparece, revisa que el menú exista y que el texto no esté vacío. Si el método no se ejecuta, revisa el callback.

La notificación puede incluir el título:

```javascript
Main.notify(
    'Mi Primera Extensión',
    `Ventana "${ventana.get_title()}" movida al espacio ${siguiente.index() + 1}.`
);
```

**Errores posibles en este bloque:** si el título aparece como `undefined`, comprueba que `ventana` no es `null`. Si la notificación no aparece, revisa “No molestar”.

### Solución del creativo

Una solución añade acciones para la izquierda y para minimizar:

```javascript
this._indicador.menu.addAction(
    'Mover ventana al espacio anterior',
    () => this._moverVentanaIzquierda()
);

this._indicador.menu.addAction(
    'Minimizar ventana activa',
    () => this._minimizarVentana()
);
```

**Errores posibles en este bloque:** si la acción de la izquierda falla en el primer espacio, comprueba que `get_neighbor` devuelve el mismo espacio y evita mover. Si minimizar falla, comprueba que hay una ventana activa.

Los métodos pueden ser:

```javascript
_moverVentanaIzquierda() {
    const ventana = global.display.focus_window;
    if (!ventana)
        return;

    const actual = global.workspace_manager.get_active_workspace();
    const anterior = actual.get_neighbor(Meta.MotionDirection.LEFT);

    if (anterior === actual)
        return;

    ventana.change_workspace(anterior);
}

_minimizarVentana() {
    const ventana = global.display.focus_window;
    if (ventana)
        ventana.minimize();
}
```

**Errores posibles en este bloque:**

**Error 1: la ventana no se mueve a la izquierda.**
- **Ubicación:** `_moverVentanaIzquierda`.
- **Síntoma:** no ocurre nada en el primer espacio.
- **Causa:** no hay espacio anterior.
- **Corrección:** comprueba que `anterior !== actual` antes de mover. Después de corregir, la acción no falla.

**Error 2: la ventana no se minimiza.**
- **Ubicación:** `_minimizarVentana`.
- **Síntoma:** la ventana sigue visible.
- **Causa:** no hay ventana activa o ya está minimizada.
- **Corrección:** comprueba el foco y el estado de la ventana. Después de corregir, la ventana se minimiza.

## Si quieres saber más

- Documentación de Meta: <https://gnome.pages.gitlab.gnome.org/mutter/meta/>
- Documentación de Mutter: <https://gnome.pages.gitlab.gnome.org/mutter/>
- Guía de GJS sobre Meta: <https://gjs.guide/guides/meta/>

No necesitas estos enlaces para continuar. El manual contiene lo necesario para el proyecto.

## Siguiente paso

Cuando la acción mueva la ventana y la notificación muestre el espacio destino, continúa con el Capítulo 7. Allí aprenderás a modificar el comportamiento interno del shell con monkey patching y a depurar con Looking Glass.
