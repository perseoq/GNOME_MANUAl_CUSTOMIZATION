# Capítulo 7: Técnicas Avanzadas: Monkey Patching y Depuración

> En este capítulo modificarás un método interno de GNOME Shell y aprenderás a restaurarlo. También aprenderás a usar Looking Glass y `journalctl` para encontrar errores.

## Qué vas a aprender

- Explicar qué es el monkey patching y cuándo evitarlo.
- Guardar un método original, envolverlo y restaurarlo en `disable()`.
- Parchear `Main.overview.show` como ejemplo reversible.
- Reconocer los riesgos de las APIs internas.
- Usar Looking Glass para inspeccionar actores y ejecutar código.
- Leer logs con `journalctl`.
- Entender por qué `Alt+F2` seguido de `r` no reinicia el shell en Wayland.

Al final del capítulo, tu extensión interceptará la apertura de la vista de Actividades y restaurará el método original al desactivarse.

## Antes de empezar necesitas

- Haber completado los capítulos 1 a 6.
- Tener una extensión activa y saber usar `journalctl`.
- Saber activar y desactivar la extensión.
- GNOME Shell 50.6 sobre Wayland.

## El concepto, con una analogía

Un monkey patch es como colocar un adaptador entre un interruptor y una lámpara. El interruptor sigue existiendo y la lámpara sigue funcionando, pero el adaptador puede observar o cambiar lo que ocurre. Si olvidas retirar el adaptador, la instalación queda modificada aunque tú ya no estés.

El parche debe cumplir tres reglas:

1. Guardar el comportamiento original.
2. Llamar al original desde el nuevo comportamiento.
3. Restaurar el original en `disable()`.

Si falta la tercera regla, el cambio sobrevive a la extensión.

### 7.1. Monkey Patching: qué es y cuándo usarlo

#### 7.1.1. El patrón seguro: guardar original, sobrescribir, restaurar en `disable()`

Un **monkey patch** reemplaza una función o un método en tiempo de ejecución. Se usa cuando una API pública no ofrece la modificación que necesitas. Es una técnica de último recurso: si existe una API pública, úsala.

Este ejemplo se presenta como una extensión independiente para concentrarse en el parche. En tu proyecto, añade únicamente el parche a la clase existente, sin borrar el botón del panel. Ejemplo completo y reversible:

```javascript
import * as Main from 'resource:///org/gnome/shell/ui/main.js';
import {Extension} from 'resource:///org/gnome/shell/extensions/extension.js';

export default class MiPrimeraExtension extends Extension {
    enable() {
        if (this._originalOverviewShow)
            Main.overview.show = this._originalOverviewShow;

        this._originalOverviewShow = Main.overview.show;
        const originalShow = this._originalOverviewShow;
        const uuid = this.uuid;

        Main.overview.show = function (...args) {
            console.log(`[${uuid}] overview.show interceptado`);
            return originalShow.apply(this, args);
        };
    }

    disable() {
        if (this._originalOverviewShow) {
            Main.overview.show = this._originalOverviewShow;
            this._originalOverviewShow = null;
        }
    }
}
```

> **Qué hace este bloque:** si ya había un parche guardado, restaura el original antes de continuar. Después guarda el método original en `this._originalOverviewShow`. La variable `originalShow` conserva una referencia local. `uuid` guarda el identificador de la extensión. Después reemplaza `Main.overview.show` por una función que escribe en el log y llama al original con `apply(this, args)`. `apply` conserva el objeto `this` y pasa todos los argumentos. En `disable()`, restaura el método original y borra la referencia.

**Errores posibles en este bloque:**

**Error 1: el parche sigue activo después de desactivar.**
- **Ubicación:** `disable()`.
- **Síntoma:** después de desactivar, abrir Actividades sigue escribiendo `overview.show interceptado` en el log.
- **Causa:** no se restauró `Main.overview.show`.
- **Corrección:** guarda el original y restáuralo en `disable()`. Después de corregir, el mensaje deja de aparecer.

**Error 2: `RangeError: Maximum call stack size exceeded`.**
- **Ubicación:** wrapper de `Main.overview.show`.
- **Síntoma:** el shell se bloquea o el log muestra una recursión infinita.
- **Causa:** guardaste el original después de sobrescribir el método, así que el wrapper se llama a sí mismo.
- **Corrección:** guarda el original antes de asignar el wrapper. Después de corregir, el wrapper llama al método real.

**Error 3: el mensaje muestra `undefined` en lugar del UUID.**
- **Ubicación:** dentro del wrapper.
- **Síntoma:** el log muestra `[undefined] overview.show interceptado`.
- **Causa:** usaste `this.uuid` dentro del wrapper; allí `this` es `Main.overview`, no la extensión.
- **Corrección:** guarda `const uuid = this.uuid;` antes de crear el wrapper y usa esa variable. Después de corregir, el UUID aparece.

**Error 4: el parche se aplica dos veces.**
- **Ubicación:** `enable()`.
- **Síntoma:** cada apertura de Actividades genera dos o más mensajes.
- **Causa:** el método ya estaba parcheado y se guardó el wrapper como si fuera el original.
- **Corrección:** restaura el original antes de volver a parchear o aplica el parche únicamente si no está activo. Después de corregir, hay un único wrapper.

#### 7.1.2. Ejemplo: parchear un método de `Panel` o `Overview`

El ejemplo anterior parchea `Main.overview.show`. Es un método interno que se ejecuta al abrir la vista de Actividades. El patrón se aplica igual a otros métodos internos:

- Guarda la función original.
- Comprueba que existe y que es una función.
- Crea un wrapper que registre o modifique el comportamiento.
- Llama al original.
- Restaura en `disable()`.

No parchees un método si existe una señal o una API pública que ya ofrece lo que necesitas. Cada parche es una deuda de mantenimiento.

**Errores posibles en este bloque:** este bloque es una explicación del patrón, no un programa. No produce errores por sí mismo. Si el método no existe, `original` será `undefined` y el wrapper fallará al llamarlo.

### 7.2. Riesgos de la API inestable

#### 7.2.1. Dependencia de nombres y firmas internas

Las APIs internas de GNOME Shell no son un contrato público. Un método puede cambiar de nombre, recibir otros argumentos o desaparecer en una versión menor.

| Riesgo | Consecuencia |
| :--- | :--- |
| El método cambia de nombre | El parche no se aplica o falla al activar |
| Cambia la firma | El wrapper pasa argumentos incorrectos |
| Cambia el comportamiento | El parche produce efectos inesperados |
| Otra extensión parchea el mismo método | Los wrappers se pisan y se pierden |
| No se restaura | El cambio persiste después de desactivar |

#### 7.2.2. Fragilidad ante actualizaciones menores de GNOME 50.x

Una actualización de 50.1 a 50.2 puede cambiar detalles internos. El manual está probado con 50.6, pero no garantiza que el parche funcione en 50.7 o 51.

Antes de publicar una extensión con monkey patching:

- Documenta qué método parcheas y por qué.
- Comprueba que el original existe.
- Restaura siempre en `disable()`.
- Prueba la activación y desactivación repetidas.
- Revisa el log después de cada actualización del shell.

**Errores posibles en este bloque:** este bloque es una explicación de riesgos, no un programa. No produce errores por sí mismo. El síntoma de una actualización incompatible es un parche que no se aplica o una excepción al activar.

### 7.3. Depuración con Looking Glass

#### 7.3.1. Apertura: `Alt+F2` → `lg`

Looking Glass es la consola de depuración de GNOME Shell. Se abre con `Alt+F2`, se escribe `lg` y se pulsa Enter.

No es una terminal del sistema. Es una ventana de GNOME Shell que permite:

- Inspeccionar el árbol de actores.
- Ejecutar JavaScript en vivo.
- Ver errores y propiedades internas.

**Errores posibles en este bloque:** este bloque es una instrucción de interfaz, no un programa. No produce errores por sí mismo. Si `Alt+F2` no responde, comprueba que estás en una sesión de GNOME y que otra aplicación no capturó el atajo.

#### 7.3.2. Inspector: navegación del árbol de actores

El Inspector muestra la jerarquía de actores del shell. Puedes seleccionar un actor y ver sus propiedades, clases y posición.

Úsalo para:

- Comprobar si tu botón está en el panel.
- Ver la clase `mi-primera-extension`.
- Revisar el tamaño y la alineación.
- Confirmar que un actor se destruyó al desactivar.

**Errores posibles en este bloque:** este bloque es una explicación de la interfaz, no un programa. No produce errores por sí mismo. Si no encuentras tu actor, comprueba que la extensión está activa y que llamaste a `addToStatusArea`.

#### 7.3.3. Evaluator: ejecución de código en vivo

El Evaluator permite escribir JavaScript y ver el resultado. Ejemplos:

```javascript
global.get_window_actors().length
```

> **Qué hace este bloque:** cuenta las ventanas visibles en el shell. El Evaluator devuelve el número como resultado. No modifica nada.

**Errores posibles en este bloque:**

**Error 1: `ReferenceError: global is not defined`.**
- **Ubicación:** Evaluator.
- **Síntoma:** el código no se ejecuta.
- **Causa:** estás en un contexto que no es GNOME Shell.
- **Corrección:** abre Looking Glass desde el shell y vuelve a ejecutar. Después de corregir, `global` existe.

**Error 2: el resultado es `0`.**
- **Ubicación:** Evaluator.
- **Síntoma:** no hay ventanas.
- **Causa:** todas las ventanas están minimizadas o no hay ninguna abierta.
- **Corrección:** abre una ventana y repite. Después de corregir, el número es mayor que cero.

También puedes comprobar un método:

```javascript
typeof Main.overview.show
```

> **Qué hace este bloque:** devuelve el tipo de `Main.overview.show`. Si el resultado es `function`, el método existe. Si es `undefined`, el nombre cambió o el módulo no está disponible.

**Errores posibles en este bloque:**

**Error 1: `ReferenceError: Main is not defined`.**
- **Ubicación:** Evaluator.
- **Síntoma:** el código no se ejecuta.
- **Causa:** `Main` no está en el contexto del Evaluator.
- **Corrección:** usa `global` para las pruebas o importa el módulo desde una extensión. Después de corregir, el método se puede comprobar.

**Error 2: el resultado es `undefined`.**
- **Ubicación:** Evaluator.
- **Síntoma:** el método no existe.
- **Causa:** el nombre cambió en la versión instalada.
- **Corrección:** revisa la versión de GNOME Shell y la API interna. Después de corregir, el método aparece.

### 7.4. Lectura de logs con `journalctl`

#### 7.4.1. `journalctl /usr/bin/gnome-shell -f -o cat`

Para ver los mensajes del shell en tiempo real:

```bash
journalctl /usr/bin/gnome-shell -f -o cat
```

> **Qué hace este bloque:** `journalctl` consulta el registro del sistema. `/usr/bin/gnome-shell` limita la salida a GNOME Shell. `-f` mantiene la salida abierta. `-o cat` muestra el texto sin metadatos.

**Errores posibles en este bloque:**

**Error 1: `No se pudieron leer los mensajes del registro`.**
- **Ubicación:** terminal.
- **Síntoma:** el comando no muestra mensajes.
- **Causa:** tu usuario no tiene permiso para leer el registro del sistema.
- **Corrección:** consulta la documentación de tu distribución para añadir tu usuario al grupo `systemd-journal`. Después de corregir, los mensajes aparecen.

**Error 2: el comando no muestra nada.**
- **Ubicación:** terminal.
- **Síntoma:** la terminal queda abierta sin salida.
- **Causa:** no ocurrió ningún evento nuevo o el filtro no coincide.
- **Corrección:** activa o desactiva la extensión desde otra terminal. Después de corregir, los mensajes aparecen.

#### 7.4.2. Ausencia de `Alt+F2` + `r` en Wayland

En Wayland, `Alt+F2` seguido de `r` no reinicia el shell. Para recargar la sesión:

1. Cierra sesión.
2. Vuelve a entrar.
3. Activa la extensión y comprueba el log.

No uses atajos de X11 que no funcionan en Wayland. Si un cambio no se aplica, desactiva y activa la extensión; si persiste, cierra sesión.

**Errores posibles en este bloque:** este bloque es una instrucción de entorno, no un programa. No produce errores por sí mismo. Si intentas reiniciar con `r`, el shell no se reinicia y el cambio no se aplica.

## Verifica que funciona

Activa la extensión y abre la vista de Actividades. En el log debes ver:

```text
GNOME Shell-Message: 11:37:08.840: [miprimeraextension@ejemplo.local] overview.show interceptado
```

Después desactiva la extensión y vuelve a abrir Actividades. No debe aparecer un mensaje nuevo. El método original debe estar restaurado.

Comprueba el estado:

```bash
gnome-extensions info miprimeraextension@ejemplo.local
```

El estado debe ser `ACTIVE` mientras la extensión está activa y debe dejar de serlo al desactivar.

**Errores posibles en este bloque:**

**Error 1: el mensaje aparece después de desactivar.**
- **Ubicación:** `disable()`.
- **Síntoma:** el parche sigue activo.
- **Causa:** no se restauró el original.
- **Corrección:** asigna `Main.overview.show = this._originalOverviewShow` en `disable()`. Después de corregir, el mensaje deja de aparecer.

**Error 2: el mensaje no aparece nunca.**
- **Ubicación:** `enable()`.
- **Síntoma:** abres Actividades y no hay log.
- **Causa:** el parche no se aplicó, el método no existe o el log se abrió después.
- **Corrección:** abre `journalctl` antes de activar y comprueba que el método existe. Después de corregir, el mensaje aparece.

## Errores de integración del capítulo

### Error 1: el parche no se restaura

**Síntoma:** después de desactivar la extensión, abrir Actividades sigue escribiendo en el log.

**Causa:** `disable()` no restaura `Main.overview.show`.

**Corrección:** guarda el original y restáuralo en `disable()`. Después de corregir, el comportamiento vuelve a la normalidad.

### Error 2: recursión infinita

**Síntoma:**

```text
RangeError: Maximum call stack size exceeded
```

**Causa:** el wrapper llama a `Main.overview.show` después de haberlo reemplazado por el wrapper.

**Corrección:** guarda el original antes de reemplazarlo y llama a la variable original. Después de corregir, no hay recursión.

### Error 3: el UUID aparece como `undefined`

**Síntoma:** el log muestra `[undefined] overview.show interceptado`.

**Causa:** usaste `this.uuid` dentro del wrapper, donde `this` es `Main.overview`.

**Corrección:** guarda `const uuid = this.uuid;` antes de crear el wrapper. Después de corregir, el UUID aparece.

### Error 4: otra extensión parchea el mismo método

**Síntoma:** tu wrapper deja de ejecutarse o se ejecuta dos veces.

**Causa:** dos extensiones guardan y reemplazan el mismo método.

**Corrección:** evita parchear métodos que otras extensiones puedan usar; documenta el parche y restaura el original. Después de corregir, el comportamiento es predecible.

### Error 5: el parche funciona hasta una actualización

**Síntoma:** después de actualizar GNOME, el método no existe o cambia la firma.

**Causa:** la API interna cambió.

**Corrección:** revisa la versión, adapta el parche y vuelve a probar. Si no puedes mantenerlo, elimina el parche.

## Ejercicios

### Reproductivo

1. Añade el import de `Main` y la clase del ejemplo.
2. Guarda `Main.overview.show` antes de reemplazarlo.
3. Crea el wrapper con el UUID en una variable local.
4. Activa la extensión y abre Actividades.
5. Comprueba el mensaje en el log.
6. Desactiva la extensión y comprueba que el mensaje ya no aparece.

### Aplicativo

1. Añade un contador de llamadas dentro del wrapper.
2. Abre Actividades tres veces.
3. Comprueba que el contador aumenta.
4. Desactiva la extensión y verifica que el contador deja de ejecutarse.
5. Explica dónde vive el contador y por qué.

### Creativo

1. Parchea también `Main.overview.hide` con el mismo patrón.
2. Guarda los dos originales y restaura los dos en `disable()`.
3. Comprueba que abrir y cerrar Actividades genera dos mensajes distintos.
4. Desactiva la extensión y verifica que ambos métodos vuelven a la normalidad.
5. Explica qué riesgo tiene parchear dos métodos internos a la vez.

**Rúbrica del ejercicio creativo:**

- Los dos métodos se guardan antes de reemplazarlos.
- Los dos wrappers llaman a sus originales.
- Los dos métodos se restauran en `disable()`.
- El log distingue `show` de `hide`.
- La explicación menciona el riesgo de acumular parches.

## Autoevaluación

1. ¿Qué es un monkey patch y por qué es una técnica de último recurso?
2. ¿Cuáles son los tres pasos del patrón seguro?
3. ¿Por qué no debes usar `this.uuid` dentro del wrapper?
4. ¿Qué diferencia hay entre el Inspector y el Evaluator de Looking Glass?
5. Tu parche sigue activo después de desactivar. ¿Qué revisarías y en qué orden?
6. ¿Por qué `Alt+F2` seguido de `r` no reinicia el shell en Wayland?

## Respuestas razonadas

1. Un monkey patch reemplaza una función o método en tiempo de ejecución. Es de último recurso porque depende de APIs internas que pueden cambiar y porque puede afectar a otras extensiones.

2. Guardar el original, reemplazarlo por un wrapper que llame al original, y restaurar el original en `disable()`.

3. Porque dentro del wrapper `this` es el objeto parcheado, no la instancia de la extensión. `this.uuid` sería `undefined`. Se guarda el UUID en una variable local antes de crear el wrapper.

4. El Inspector muestra el árbol de actores y sus propiedades. El Evaluator ejecuta JavaScript en vivo y devuelve resultados. El primero sirve para inspeccionar; el segundo, para probar código.

5. Primero revisaría que el UUID del wrapper sea correcto y que el parche se haya aplicado. Después comprobaría que `disable()` restaure `Main.overview.show`. Finalmente confirmaría que no hay otro parche o una recarga pendiente. El orden va del estado del parche a la restauración.

6. Porque Wayland no permite reiniciar el shell con ese atajo. El comando `r` pertenece al entorno X11. En Wayland se cierra sesión y se vuelve a entrar.

## Soluciones de los ejercicios

### Solución del reproductivo

El código completo es el del ejemplo de la sección 7.1.1. La parte esencial es:

```javascript
enable() {
    if (this._originalOverviewShow)
        Main.overview.show = this._originalOverviewShow;

    this._originalOverviewShow = Main.overview.show;
    const originalShow = this._originalOverviewShow;
    const uuid = this.uuid;

    Main.overview.show = function (...args) {
        console.log(`[${uuid}] overview.show interceptado`);
        return originalShow.apply(this, args);
    };
}

disable() {
    if (this._originalOverviewShow) {
        Main.overview.show = this._originalOverviewShow;
        this._originalOverviewShow = null;
    }
}
```

**Errores posibles en este bloque:**

**Error 1: el parche no se restaura.**
- **Ubicación:** `disable()`.
- **Síntoma:** el mensaje sigue apareciendo.
- **Causa:** falta la asignación del original.
- **Corrección:** restaura `Main.overview.show`. Después de corregir, el mensaje deja de aparecer.

**Error 2: `originalShow is not a function`.**
- **Ubicación:** wrapper.
- **Síntoma:** la extensión falla al abrir Actividades.
- **Causa:** el método original no existe o se guardó mal.
- **Corrección:** comprueba que `Main.overview.show` existe antes de guardarlo. Después de corregir, el wrapper funciona.

### Solución del aplicativo

El contador puede vivir en el cierre de `enable()`:

```javascript
let llamadas = 0;

Main.overview.show = function (...args) {
    llamadas++;
    console.log(`[${uuid}] llamada ${llamadas} a overview.show`);
    return originalShow.apply(this, args);
};
```

**Errores posibles en este bloque:**

**Error 1: el contador siempre muestra `1`.**
- **Ubicación:** wrapper.
- **Síntoma:** cada llamada reinicia el contador.
- **Causa:** el contador se declara dentro del wrapper.
- **Corrección:** declara `let llamadas = 0;` fuera del wrapper. Después de corregir, el contador aumenta.

**Error 2: el contador sigue aumentando después de desactivar.**
- **Ubicación:** `disable()`.
- **Síntoma:** el parche no se restauró.
- **Causa:** falta restaurar el original.
- **Corrección:** restaura `Main.overview.show` en `disable()`. Después de corregir, el contador deja de ejecutarse.

### Solución del creativo

Guarda y restaura los dos métodos:

```javascript
enable() {
    this._originalShow = Main.overview.show;
    this._originalHide = Main.overview.hide;

    const originalShow = this._originalShow;
    const originalHide = this._originalHide;
    const uuid = this.uuid;

    Main.overview.show = function (...args) {
        console.log(`[${uuid}] show interceptado`);
        return originalShow.apply(this, args);
    };

    Main.overview.hide = function (...args) {
        console.log(`[${uuid}] hide interceptado`);
        return originalHide.apply(this, args);
    };
}

disable() {
    if (this._originalShow) {
        Main.overview.show = this._originalShow;
        this._originalShow = null;
    }

    if (this._originalHide) {
        Main.overview.hide = this._originalHide;
        this._originalHide = null;
    }
}
```

**Errores posibles en este bloque:**

**Error 1: uno de los dos métodos no se restaura.**
- **Ubicación:** `disable()`.
- **Síntoma:** únicamente uno de los mensajes sigue apareciendo.
- **Causa:** falta una de las dos asignaciones.
- **Corrección:** restaura `show` y `hide` por separado. Después de corregir, ambos vuelven a la normalidad.

**Error 2: los wrappers se pisan.**
- **Ubicación:** `enable()`.
- **Síntoma:** uno de los mensajes no aparece.
- **Causa:** se guardó el wrapper de una ejecución anterior como original.
- **Corrección:** restaura los originales antes de volver a parchear. Después de corregir, ambos wrappers funcionan.

## Si quieres saber más

- Documentación de Looking Glass: <https://github.com/GNOME/gnome-shell/blob/main/docs/looking-glass.md>
- Guía de GJS: <https://gjs.guide/>
- Documentación de GNOME Shell: <https://gnome.pages.gitlab.gnome.org/gnome-shell/>

No necesitas estos enlaces para continuar. El manual contiene lo necesario para el proyecto.

## Siguiente paso

Cuando el parche se aplique y se restaure correctamente, continúa con el Capítulo 8. Allí verás cuándo tiene sentido escribir una biblioteca en C y cuándo conviene quedarse en GJS.
