# Capítulo 9: Extensiones que Consumen APIs Externas

> En este capítulo harás que tu extensión consulte un precio en Internet y lo muestre en el panel. Aprenderás a usar Soup 3, a procesar JSON y a limpiar las peticiones al desactivar.

## Qué vas a aprender

- Crear un cliente HTTP con Soup 3.
- Hacer una petición asíncrona con `send_and_read_async()`.
- Comprobar el código de estado HTTP.
- Decodificar bytes y procesar JSON.
- Separar la lógica de red en un módulo proveedor.
- Actualizar el precio con un temporizador.
- Cancelar peticiones y temporizadores en `disable()`.
- Evitar hardcodear claves de API.

Al final del capítulo, el botón del panel mostrará el precio de Bitcoin y se actualizará cada minuto.

## Antes de empezar necesitas

- Haber completado los capítulos 1 a 8.
- Tener el botón del panel con un icono.
- Tener conexión a Internet para probar la API.
- GNOME Shell 50.6 sobre Wayland.

## El concepto, con una analogía

Una API externa es como el menú de un restaurante. Tú haces un pedido con una dirección y unos datos; el servidor responde con un plato y un estado. Si el restaurante está cerrado, la respuesta es un error. Si el plato llega en un formato distinto, no puedes comerlo.

En este capítulo:

- **Soup** es el camarero que lleva la petición y trae la respuesta.
- **HTTP** es el idioma del pedido y de la respuesta.
- **JSON** es el formato del plato.
- **Tu extensión** es quien decide qué hacer con el dato.

### 9.1. El cliente HTTP nativo: Soup 3

#### 9.1.1. Importación: `import Soup from 'gi://Soup?version=3.0'`

Soup 3 es la biblioteca HTTP de GNOME. Se importa con la versión explícita:

```javascript
import GLib from 'gi://GLib';
import Soup from 'gi://Soup?version=3.0';
```

> **Qué hace este bloque:** `GLib` aporta prioridades y temporizadores. `Soup` carga la biblioteca HTTP en su versión 3.0. La versión es obligatoria para evitar ambigüedades entre Soup 2 y Soup 3. La importación no hace ninguna petición.

**Errores posibles en este bloque:**

**Error 1: `Typelib file for namespace 'Soup' (any version) not found`.**
- **Ubicación:** línea del import.
- **Síntoma:** la extensión no carga.
- **Causa:** Soup 3 no está instalado o no está en la ruta de introspección.
- **Corrección:** instala `libsoup3` en tu distribución. Después de corregir, el import funciona.

**Error 2: `ImportError: Unable to load file from: gi://Soup?version=3.0`.**
- **Ubicación:** línea del import.
- **Síntoma:** el recurso no se encuentra.
- **Causa:** el nombre o la versión están mal escritos.
- **Corrección:** usa exactamente `gi://Soup?version=3.0`. Después de corregir, el módulo se carga.

#### 9.1.2. Creación de `Soup.Session` y `Soup.Message`

Una **sesión** agrupa las peticiones y su configuración. Un **mensaje** representa una petición HTTP concreta.

```javascript
const session = new Soup.Session();
const message = Soup.Message.new(
    'GET',
    'https://api.binance.com/api/v3/ticker/price?symbol=BTCUSDT'
);
```

> **Qué hace este bloque:** `new Soup.Session()` crea una sesión reutilizable. `Soup.Message.new('GET', url)` crea una petición GET para la URL indicada. `GET` significa que solicitas datos sin enviar un cuerpo. El mensaje todavía no se ha enviado.

**Errores posibles en este bloque:**

**Error 1: `TypeError: Soup.Session is not a constructor`.**
- **Ubicación:** línea de `new Soup.Session`.
- **Síntoma:** la extensión queda en `ERROR`.
- **Causa:** el import de Soup no es el módulo correcto.
- **Corrección:** importa `Soup` desde `gi://Soup?version=3.0`. Después de corregir, el constructor existe.

**Error 2: `Soup.Message.new` devuelve `null`.**
- **Ubicación:** línea de `Soup.Message.new`.
- **Síntoma:** la siguiente llamada falla porque el mensaje no existe.
- **Causa:** la URL no es válida o está vacía.
- **Corrección:** comprueba la URL y añade una validación `if (!message)`. Después de corregir, el mensaje se crea.

#### 9.1.3. Peticiones asíncronas: `send_and_read_async()`

Una petición asíncrona no bloquea el shell. GJS convierte el método `_async` en una promesa cuando se usa sin callback:

```javascript
const bytes = await session.send_and_read_async(
    message,
    GLib.PRIORITY_DEFAULT,
    null
);
```

> **Qué hace este bloque:** `send_and_read_async` envía el mensaje y espera la respuesta. `GLib.PRIORITY_DEFAULT` indica la prioridad. El tercer argumento es un `GCancellable`; `null` significa que no se usa uno. `await` espera a que la promesa se resuelva. `bytes` contiene el cuerpo de la respuesta.

**Errores posibles en este bloque:**

**Error 1: `Error: Message was cancelled`.**
- **Ubicación:** `await`.
- **Síntoma:** la petición se interrumpe al desactivar la extensión.
- **Causa:** llamaste a `session.abort()` mientras la petición estaba pendiente.
- **Corrección:** captura el error en `try/catch` y no actualices la interfaz si el label ya no existe. Después de corregir, la cancelación no rompe la extensión.

**Error 2: la promesa no se resuelve nunca.**
- **Ubicación:** `send_and_read_async`.
- **Síntoma:** el precio no aparece.
- **Causa:** no hay red, el servidor no responde o el `await` está fuera de una función `async`.
- **Corrección:** comprueba la conexión, usa una función `async` y añade un tiempo de espera. Después de corregir, la petición termina.

#### 9.1.4. Verificación del código de estado: `message.get_status() === Soup.Status.OK`

El código de estado HTTP indica si la petición funcionó. `200` significa OK; `404` significa no encontrado; `429` significa demasiadas peticiones.

```javascript
if (message.get_status() !== Soup.Status.OK)
    throw new Error(`HTTP ${message.get_status()}`);
```

> **Qué hace este bloque:** compara el estado del mensaje con `Soup.Status.OK`. Si no coincide, lanza un error con el código. El error lo captura la función que llamó al proveedor. No intentes leer el cuerpo como si fuera un JSON válido cuando el estado no es OK.

**Errores posibles en este bloque:**

**Error 1: `Error: HTTP 429`.**
- **Ubicación:** proveedor.
- **Síntoma:** el panel muestra `--` y el log registra `HTTP 429`.
- **Causa:** la API recibió demasiadas peticiones en poco tiempo.
- **Corrección:** aumenta el intervalo de actualización y evita varias instancias de la extensión. Después de corregir, la API vuelve a responder.

**Error 2: `Error: HTTP 404`.**
- **Ubicación:** proveedor.
- **Síntoma:** el símbolo o la URL no existen.
- **Causa:** el nombre del símbolo o la ruta de la API están mal.
- **Corrección:** usa un símbolo válido, como `BTCUSDT`, y revisa la URL. Después de corregir, la petición responde.

### 9.2. Procesamiento de respuestas JSON

#### 9.2.1. Decodificación de bytes a string con `TextDecoder`

El cuerpo de la respuesta llega como bytes. Para convertirlo en texto se usa `TextDecoder`.

```javascript
const text = new TextDecoder().decode(bytes.get_data());
```

> **Qué hace este bloque:** `bytes.get_data()` devuelve los bytes del cuerpo. `TextDecoder` los convierte en una cadena UTF-8. Sin esta conversión, `JSON.parse` recibiría bytes y fallaría.

**Errores posibles en este bloque:**

**Error 1: `TypeError: bytes.get_data is not a function`.**
- **Ubicación:** línea de decodificación.
- **Síntoma:** la extensión falla después de la petición.
- **Causa:** `bytes` no es un `GLib.Bytes`.
- **Corrección:** usa el resultado de `send_and_read_async` directamente. Después de corregir, `get_data()` existe.

**Error 2: el texto aparece con caracteres extraños.**
- **Ubicación:** `TextDecoder`.
- **Síntoma:** el JSON no se parsea.
- **Causa:** la respuesta no está en UTF-8 o el cuerpo está comprimido.
- **Corrección:** comprueba la codificación de la API y usa la respuesta sin comprimir. Después de corregir, el texto se lee.

#### 9.2.2. `JSON.parse()` y manejo de errores de parseo

`JSON.parse` convierte el texto en un objeto de JavaScript:

```javascript
const data = JSON.parse(text);
```

> **Qué hace este bloque:** convierte el texto JSON en un objeto. Si el texto no es JSON válido, lanza una excepción. Por eso la llamada debe estar dentro de un `try/catch`.

**Errores posibles en este bloque:**

**Error 1: `SyntaxError: JSON.parse: unexpected character`.**
- **Ubicación:** `JSON.parse`.
- **Síntoma:** el precio no se actualiza y el log muestra un error de parseo.
- **Causa:** la respuesta no es JSON, está vacía o es una página de error HTML.
- **Corrección:** comprueba el estado HTTP y el cuerpo de la respuesta. Después de corregir, el JSON se parsea.

**Error 2: `data` es `null` o no tiene los campos esperados.**
- **Ubicación:** líneas que leen `data.price`.
- **Síntoma:** el proveedor lanza un error de precio no válido.
- **Causa:** la API cambió el formato o la petición devolvió otro recurso.
- **Corrección:** revisa la respuesta y adapta el campo. Después de corregir, el precio se lee.

#### 9.2.3. Extracción de campos anidados

Las APIs devuelven campos anidados. Por ejemplo, Open-Meteo devuelve:

```json
{
    "current": {
        "temperature_2m": 18.5
    }
}
```

Este bloque es un ejemplo de respuesta, no un programa. No se ejecuta y no produce errores por sí mismo.

El acceso es:

```javascript
const temperatura = data.current.temperature_2m;
```

> **Qué hace este bloque:** accede a `current` y después a `temperature_2m`. Si `current` no existe, la expresión falla. Por eso se comprueba el dato antes de usarlo.

**Errores posibles en este bloque:**

**Error 1: `TypeError: data.current is undefined`.**
- **Ubicación:** acceso anidado.
- **Síntoma:** el proveedor falla.
- **Causa:** la API devolvió un error o cambió el nombre del campo.
- **Corrección:** comprueba `data.current` antes de acceder. Después de corregir, el campo se lee.

**Error 2: el valor es `undefined`.**
- **Ubicación:** campo anidado.
- **Síntoma:** el panel muestra `NaN` o `undefined`.
- **Causa:** el nombre del campo no coincide.
- **Corrección:** usa el nombre exacto de la respuesta. Después de corregir, el valor aparece.

### 9.3. Patrón de diseño: proveedor de datos intercambiable

#### 9.3.1. Separar la lógica de red en un módulo (`providers/binanceProvider.js`)

Un **proveedor** es un módulo que sabe hablar con una API concreta. La extensión no necesita conocer los detalles de la URL ni del JSON.

Estructura:

```text
miprimeraextension@ejemplo.local/
├── extension.js
├── metadata.json
├── prefs.js
├── stylesheet.css
└── providers/
    └── binanceProvider.js
```

Este bloque es un diagrama de archivos, no un programa. No se ejecuta y no produce errores por sí mismo.

El proveedor de Binance:

```javascript
import GLib from 'gi://GLib';
import Soup from 'gi://Soup?version=3.0';

export async function fetchPrice(session, symbol = 'BTCUSDT') {
    const url = `https://api.binance.com/api/v3/ticker/price?symbol=${symbol}`;
    const message = Soup.Message.new('GET', url);
    if (!message)
        throw new Error('No se pudo crear la petición');

    const bytes = await session.send_and_read_async(
        message,
        GLib.PRIORITY_DEFAULT,
        null
    );

    if (message.get_status() !== Soup.Status.OK)
        throw new Error(`HTTP ${message.get_status()}`);

    const text = new TextDecoder().decode(bytes.get_data());
    const data = JSON.parse(text);

    if (!data || typeof data.price !== 'string')
        throw new Error('Respuesta sin precio');

    const price = Number.parseFloat(data.price);

    if (!Number.isFinite(price))
        throw new Error('Precio no válido');

    return price;
}
```

> **Qué hace este bloque:** define una función `fetchPrice` que recibe una sesión y un símbolo. Construye la URL, crea el mensaje, envía la petición y comprueba el estado. Decodifica el cuerpo, parsea el JSON, convierte `price` a número y valida que sea finito. Devuelve el precio. La función es `async` porque espera la respuesta.

**Errores posibles en este bloque:**

**Error 1: `SyntaxError: Unexpected token 'export'`.**
- **Ubicación:** archivo del proveedor.
- **Síntoma:** la extensión no carga.
- **Causa:** el archivo no se trata como módulo ESM.
- **Corrección:** usa la extensión `.js` y el import relativo desde `extension.js`. Después de corregir, el módulo se carga.

**Error 2: `ImportError: Unable to load file from: ./providers/binanceProvider.js`.**
- **Ubicación:** import relativo.
- **Síntoma:** la extensión no carga.
- **Causa:** la ruta o el nombre del archivo no coinciden.
- **Corrección:** comprueba la carpeta `providers/` y el nombre exacto. Después de corregir, el import funciona.

#### 9.3.2. Interfaz común: `fetch()` que devuelve una Promesa con datos normalizados

La extensión puede cambiar de proveedor sin cambiar la interfaz. Todos los proveedores deben devolver una promesa con un dato normalizado.

Para Binance, el dato normalizado es un número. Para el clima, podría ser un objeto con temperatura y unidad. La extensión decide qué hacer con ese dato.

#### 9.3.3. Ejemplo con múltiples fuentes

Puedes usar APIs sin clave para los ejemplos:

| Fuente | Datos | Clave |
| :--- | :--- | :--- |
| Binance | Precio de criptomonedas | No |
| Open-Meteo | Clima | No |
| OpenWeatherMap | Clima | Sí |
| CoinGecko | Criptomonedas | Puede requerir clave |

Para el proyecto se usa Binance porque el endpoint público no necesita clave. Si usas una API con clave, no la escribas en el código.

### 9.4. Actualización periódica y gestión de errores

#### 9.4.1. Temporizadores con `GLib.timeout_add_seconds()`

Un temporizador ejecuta una función cada cierto número de segundos:

```javascript
this._timeoutId = GLib.timeout_add_seconds(
    GLib.PRIORITY_DEFAULT,
    60,
    () => {
        this._actualizarPrecio();
        return GLib.SOURCE_CONTINUE;
    }
);
```

> **Qué hace este bloque:** `timeout_add_seconds` programa una función cada 60 segundos. `GLib.PRIORITY_DEFAULT` indica la prioridad. El callback devuelve `GLib.SOURCE_CONTINUE` para que el temporizador se repita. El identificador se guarda en `this._timeoutId`.

**Errores posibles en este bloque:**

**Error 1: el temporizador se ejecuta varias veces.**
- **Ubicación:** `timeout_add_seconds`.
- **Síntoma:** el precio se actualiza demasiadas veces.
- **Causa:** se crearon varios temporizadores.
- **Corrección:** crea únicamente uno en `enable()` y elimínalo en `disable()`. Después de corregir, hay una actualización por intervalo.

**Error 2: el temporizador sigue activo después de desactivar.**
- **Ubicación:** `disable()`.
- **Síntoma:** las peticiones continúan.
- **Causa:** no se llamó a `GLib.source_remove`.
- **Corrección:** elimina el temporizador en `disable()`. Después de corregir, las peticiones se detienen.

#### 9.4.2. Manejo de fallos de red: reintentos, tiempos de espera y mensajes en el panel

Una petición puede fallar por falta de red, un error del servidor o un JSON inesperado. La extensión debe:

- Capturar el error con `try/catch`.
- Escribir un mensaje claro en el log.
- Mostrar `--` en el panel.
- No bloquear el shell.
- No lanzar una excepción no controlada.

No añadas reintentos infinitos. Un intervalo de 60 segundos ya reintenta en la siguiente actualización.

**Errores posibles en este bloque:** este bloque es una explicación de manejo de errores, no un programa. No produce errores por sí mismo. Si el `catch` está vacío, el fallo se oculta y el panel se queda con un valor viejo.

#### 9.4.3. Cancelación de peticiones pendientes en `disable()` para evitar fugas

Al desactivar, cancela la sesión y elimina el temporizador:

```javascript
if (this._timeoutId) {
    GLib.source_remove(this._timeoutId);
    this._timeoutId = 0;
}

this._session?.abort();
this._session = null;
```

> **Qué hace este bloque:** `source_remove` elimina el temporizador. `abort()` cancela las peticiones pendientes de la sesión. Después se libera la referencia a la sesión. Si no se cancela, una respuesta puede llegar cuando la extensión ya no existe.

**Errores posibles en este bloque:**

**Error 1: `TypeError: this._session.abort is not a function`.**
- **Ubicación:** `disable()`.
- **Síntoma:** la extensión falla al desactivar.
- **Causa:** `this._session` no es una `Soup.Session` o ya se puso en `null`.
- **Corrección:** llama a `abort()` antes de asignar `null`. Después de corregir, la sesión se cancela.

**Error 2: una respuesta llega después de desactivar.**
- **Ubicación:** callback asíncrono.
- **Síntoma:** el log muestra un error o se intenta actualizar un label destruido.
- **Causa:** no se canceló la petición o no se comprobó si el label existe.
- **Corrección:** usa `abort()` y comprueba `this._precioLabel` antes de actualizar. Después de corregir, la respuesta tardía no rompe la extensión.

### 9.5. Manejo de claves de API sin exponerlas

#### 9.5.1. Nunca hardcodear claves en `extension.js`

Una clave escrita en el código se publica con la extensión. Cualquier persona puede extraerla del ZIP o del repositorio. No escribas claves en `extension.js`, `prefs.js`, `metadata.json` ni en capturas.

**Errores posibles en este bloque:** este bloque es una advertencia, no un programa. No produce errores por sí mismo. El fallo es de seguridad: una clave expuesta puede agotar tu cuota o generar cargos.

#### 9.5.2. Preferir APIs que no requieran clave para los ejemplos del manual

Para enseñar, usa APIs públicas:

- Binance para precios.
- Open-Meteo para clima.

Así el lector no necesita registrarse ni manejar secretos. Las APIs con clave se presentan como variante opcional.

#### 9.5.3. GSettings no es almacenamiento seguro: dconf guarda los valores en texto plano

GSettings es útil para preferencias, pero no es una caja fuerte. dconf guarda los valores en texto plano y otros procesos del usuario pueden leerlos. No uses GSettings como si fuera almacenamiento cifrado.

Si necesitas una clave, usa `libsecret` (Secret Service) o pide al usuario que la introduzca en cada sesión. Aun así, ningún almacenamiento local protege de un proceso con los mismos permisos del usuario.

#### 9.5.4. Advertencia sobre la exposición de claves en código público y en repositorios

Antes de publicar:

- Busca claves en el código y en el historial de Git.
- Revisa capturas y logs.
- Usa variables de entorno o `libsecret` si es imprescindible.
- Revoca cualquier clave que hayas publicado.

**Errores posibles en este bloque:** este bloque es una advertencia, no un programa. No produce errores por sí mismo. Si publicas una clave, considérala comprometida.

### 9.6. Ejemplo completo: indicador de criptomonedas

#### 9.6.1. Petición a la API de Binance

El proveedor de la sección 9.3.1 consulta:

```text
https://api.binance.com/api/v3/ticker/price?symbol=BTCUSDT
```

No necesita clave. Devuelve un JSON como:

```json
{
    "symbol": "BTCUSDT",
    "price": "81892.21000000"
}
```

Este bloque es un ejemplo de respuesta, no un programa. No se ejecuta y no produce errores por sí mismo.

#### 9.6.2. Parseo del precio y actualización de un `St.Label` en el panel

La extensión crea una etiqueta, la añade al botón y actualiza su texto cuando llega el precio:

```javascript
this._precioLabel = new St.Label({
    text: '--',
    y_align: Clutter.ActorAlign.CENTER,
});

this._precioLabel.set_text(`$${price.toFixed(2)}`);
```

> **Qué hace este bloque:** crea una etiqueta con `--` como marcador. `y_align` la centra verticalmente. `set_text` cambia el texto cuando llega el precio. `toFixed(2)` muestra dos decimales.

**Errores posibles en este bloque:**

**Error 1: la etiqueta no aparece.**
- **Ubicación:** panel.
- **Síntoma:** el botón no muestra el precio.
- **Causa:** la etiqueta no se añadió al contenedor o el contenedor no se añadió al botón.
- **Corrección:** comprueba las llamadas `add_child`. Después de corregir, la etiqueta aparece.

**Error 2: el texto muestra `undefined`.**
- **Ubicación:** `set_text`.
- **Síntoma:** el panel muestra `$undefined`.
- **Causa:** el precio no es un número o `price` no existe.
- **Corrección:** valida el precio antes de llamar a `toFixed`. Después de corregir, el texto muestra un número.

#### 9.6.3. Menú desplegable con detalles adicionales

Puedes añadir una acción para actualizar manualmente:

```javascript
this._indicador.menu.addAction(
    'Actualizar precio',
    () => this._actualizarPrecio()
);
```

> **Qué hace este bloque:** añade una opción al menú del botón. Al pulsarla, se ejecuta `_actualizarPrecio()` sin esperar al temporizador.

**Errores posibles en este bloque:**

**Error 1: la opción no aparece.**
- **Ubicación:** menú.
- **Síntoma:** el menú está vacío.
- **Causa:** el botón se creó sin menú.
- **Corrección:** crea `PanelMenu.Button` con `dontCreateMenu` en `false`. Después de corregir, la opción aparece.

**Error 2: la opción no actualiza el precio.**
- **Ubicación:** callback.
- **Síntoma:** no ocurre nada al pulsar.
- **Causa:** el nombre del método está mal o la sesión no existe.
- **Corrección:** usa `_actualizarPrecio` y comprueba la sesión. Después de corregir, el precio se actualiza.

## Ejemplo guiado paso a paso

Vas a integrar el proveedor en la extensión. El botón ya existe; ahora mostrará un precio.

### Bloque 1: crear el proveedor

Crea `providers/binanceProvider.js` con el contenido de la sección 9.3.1.

**Errores posibles en este bloque:**

**Error 1: el archivo no está en `providers/`.**
- **Ubicación:** estructura de la extensión.
- **Síntoma:** el import relativo falla.
- **Causa:** la carpeta o el nombre no coinciden.
- **Corrección:** crea `providers/binanceProvider.js` en la raíz de la extensión. Después de corregir, el import funciona.

**Error 2: el proveedor no exporta `fetchPrice`.**
- **Ubicación:** archivo del proveedor.
- **Síntoma:** `fetchPrice is not a function`.
- **Causa:** falta `export` o el nombre está mal.
- **Corrección:** usa `export async function fetchPrice(...)`. Después de corregir, la función existe.

### Bloque 2: importar Soup y el proveedor

```javascript
import GLib from 'gi://GLib';
import Soup from 'gi://Soup?version=3.0';
import St from 'gi://St';
import Clutter from 'gi://Clutter';
import {fetchPrice} from './providers/binanceProvider.js';
```

> **Qué hace este bloque:** carga GLib, Soup, St y Clutter, y el proveedor local. La ruta relativa empieza por `./` y apunta a la carpeta `providers` de la extensión.

**Errores posibles en este bloque:**

**Error 1: `ImportError` en `gi://Soup?version=3.0`.**
- **Ubicación:** línea del import.
- **Síntoma:** la extensión no carga.
- **Causa:** Soup 3 no está instalado.
- **Corrección:** instala `libsoup3`. Después de corregir, el import funciona.

**Error 2: `ImportError` en `./providers/binanceProvider.js`.**
- **Ubicación:** línea del import relativo.
- **Síntoma:** la extensión no carga.
- **Causa:** la ruta o el nombre del archivo no coinciden.
- **Corrección:** revisa la carpeta y el nombre. Después de corregir, el módulo se carga.

### Bloque 3: crear el panel y la etiqueta

```javascript
this._indicador = new PanelMenu.Button(0.0, 'Mi Primera Extensión', false);
this._indicador.add_style_class_name('mi-primera-extension');

const caja = new St.BoxLayout({
    vertical: false,
    style: 'spacing: 6px;',
});

const icono = new St.Icon({
    icon_name: 'face-smile-symbolic',
    style_class: 'system-status-icon',
});

this._precioLabel = new St.Label({
    text: '--',
    y_align: Clutter.ActorAlign.CENTER,
});

caja.add_child(icono);
caja.add_child(this._precioLabel);
this._indicador.add_child(caja);
Main.panel.addToStatusArea(this.uuid, this._indicador);
```

> **Qué hace este bloque:** crea el botón, una caja horizontal, un icono y una etiqueta. `style: 'spacing: 6px;'` aplica el espacio entre hijos mediante CSS; `spacing` no es una propiedad GObject de `St.BoxLayout`. La etiqueta empieza con `--` y se actualiza cuando llega el precio.

**Errores posibles en este bloque:**

**Error 1: `Error: No property spacing on StBoxLayout`.**
- **Ubicación:** constructor de `St.BoxLayout`.
- **Síntoma:** la extensión no carga.
- **Causa:** pasaste `spacing` como propiedad GObject.
- **Corrección:** usa `style: 'spacing: 6px;'`. Después de corregir, el layout se crea.

**Error 2: la etiqueta no aparece.**
- **Ubicación:** panel.
- **Síntoma:** únicamente se ve el icono.
- **Causa:** la etiqueta no se añadió a la caja o la caja no se añadió al botón.
- **Corrección:** comprueba las llamadas `add_child`. Después de corregir, la etiqueta aparece.

### Bloque 4: actualizar el precio y crear el temporizador

```javascript
async _actualizarPrecio() {
    if (!this._session)
        return;

    try {
        const price = await fetchPrice(this._session);
        if (this._precioLabel)
            this._precioLabel.set_text(`$${price.toFixed(2)}`);
        console.log(`[${this.uuid}] precio actualizado: ${price.toFixed(2)}`);
    } catch (error) {
        console.error(`[${this.uuid}] error al obtener el precio: ${error.message}`);
        if (this._precioLabel)
            this._precioLabel.set_text('--');
    }
}
```

> **Qué hace este bloque:** comprueba que la sesión siga existiendo, llama al proveedor, muestra el precio con dos decimales y escribe el resultado en el log. Si falla, muestra `--` y registra el error. La comprobación `if (!this._session)` evita iniciar una petición después de desactivar. La comprobación `if (this._precioLabel)` evita actualizar una etiqueta destruida.

**Errores posibles en este bloque:**

**Error 1: `Error: HTTP 429`.**
- **Ubicación:** proveedor.
- **Síntoma:** el panel muestra `--`.
- **Causa:** demasiadas peticiones.
- **Corrección:** aumenta el intervalo. Después de corregir, la API responde.

**Error 2: el precio no aparece.**
- **Ubicación:** panel.
- **Síntoma:** la etiqueta sigue en `--`.
- **Causa:** la petición falló, la red no está disponible o el JSON cambió.
- **Corrección:** revisa el log y el estado HTTP. Después de corregir, el precio aparece.

El temporizador se crea en `enable()`:

```javascript
this._timeoutId = GLib.timeout_add_seconds(
    GLib.PRIORITY_DEFAULT,
    60,
    () => {
        this._actualizarPrecio();
        return GLib.SOURCE_CONTINUE;
    }
);
```

> **Qué hace este bloque:** actualiza el precio cada 60 segundos. El callback devuelve `GLib.SOURCE_CONTINUE` para repetirse. El identificador se guarda para eliminarlo en `disable()`.

**Errores posibles en este bloque:**

**Error 1: se crean varios temporizadores.**
- **Ubicación:** `enable()`.
- **Síntoma:** el precio se actualiza varias veces por minuto.
- **Causa:** se llamó a `timeout_add_seconds` más de una vez.
- **Corrección:** crea el temporizador una sola vez. Después de corregir, hay una actualización por intervalo.

**Error 2: el temporizador sigue activo al desactivar.**
- **Ubicación:** `disable()`.
- **Síntoma:** las peticiones continúan.
- **Causa:** no se llamó a `GLib.source_remove`.
- **Corrección:** elimina el temporizador en `disable()`. Después de corregir, las peticiones se detienen.

### Bloque 5: limpiar en `disable()`

```javascript
disable() {
    if (this._timeoutId) {
        GLib.source_remove(this._timeoutId);
        this._timeoutId = 0;
    }

    this._session?.abort();
    this._session = null;
    this._precioLabel = null;
    this._indicador?.destroy();
    this._indicador = null;
}
```

> **Qué hace este bloque:** elimina el temporizador, cancela la sesión, libera la etiqueta y destruye el botón. El orden importa: primero se detienen las tareas, después se destruyen los actores.

**Errores posibles en este bloque:**

**Error 1: `TypeError: this._session.abort is not a function`.**
- **Ubicación:** `disable()`.
- **Síntoma:** la extensión falla al desactivar.
- **Causa:** la sesión ya se puso en `null` o no es una `Soup.Session`.
- **Corrección:** llama a `abort()` antes de asignar `null`. Después de corregir, la sesión se cancela.

**Error 2: una respuesta llega después de destruir el botón.**
- **Ubicación:** callback asíncrono.
- **Síntoma:** error al actualizar `this._precioLabel`.
- **Causa:** no se comprobó si la etiqueta existía.
- **Corrección:** comprueba `this._precioLabel` antes de actualizar. Después de corregir, la respuesta tardía no rompe la extensión.

## Verifica que funciona

Activa la extensión y espera unos segundos:

```bash
gnome-extensions enable miprimeraextension@ejemplo.local
```

El panel debe mostrar un precio como `$81884.84`. En el log debes ver:

```text
GNOME Shell-Message: 11:57:02.342: [miprimeraextension@ejemplo.local] precio actualizado: 81884.84
```

**Errores posibles en este bloque:**

**Error 1: el panel sigue mostrando `--`.**
- **Ubicación:** panel.
- **Síntoma:** no llega el precio.
- **Causa:** no hay red, la API cambió o el proveedor falló.
- **Corrección:** revisa el log con `journalctl` y prueba la URL con un navegador. Después de corregir, el precio aparece.

**Error 2: `HTTP 429`.**
- **Ubicación:** log.
- **Síntoma:** la API rechaza las peticiones.
- **Causa:** demasiadas solicitudes.
- **Corrección:** aumenta el intervalo y espera. Después de corregir, la API responde.

**Error 3: `JSON.parse` falla.**
- **Ubicación:** proveedor.
- **Síntoma:** el precio no se actualiza.
- **Causa:** la respuesta no es JSON o está vacía.
- **Corrección:** revisa el estado HTTP y el cuerpo. Después de corregir, el JSON se parsea.

Desactiva la extensión y comprueba que no hay nuevas peticiones:

```bash
gnome-extensions disable miprimeraextension@ejemplo.local
```

El temporizador y la sesión deben quedar cancelados.

## Errores de integración del capítulo

### Error 1: Soup 3 no está instalado

**Síntoma:**

```text
Typelib file for namespace 'Soup' (any version) not found
```

**Causa:** falta `libsoup3` o su typelib.

**Corrección:** instala `libsoup3`. Después de corregir, el import funciona.

### Error 2: la URL devuelve 404

**Síntoma:** el log muestra `HTTP 404`.

**Causa:** el símbolo o la ruta de la API están mal.

**Corrección:** usa una URL válida y un símbolo como `BTCUSDT`. Después de corregir, la petición responde.

### Error 3: la respuesta no es JSON

**Síntoma:**

```text
SyntaxError: JSON.parse: unexpected character
```

**Causa:** la API devolvió HTML, un texto vacío o un formato distinto.

**Corrección:** comprueba el estado y el cuerpo antes de parsear. Después de corregir, el JSON se lee.

### Error 4: el panel muestra `$NaN`

**Síntoma:** el texto contiene `NaN`.

**Causa:** `data.price` no es un número.

**Corrección:** valida con `Number.parseFloat` y `Number.isFinite`. Después de corregir, el precio es numérico.

### Error 5: la petición sigue después de desactivar

**Síntoma:** el log muestra actividad después de desactivar.

**Causa:** no se eliminó el temporizador o no se abortó la sesión.

**Corrección:** usa `GLib.source_remove` y `session.abort()` en `disable()`. Después de corregir, la actividad se detiene.

### Error 6: una clave de API aparece en el repositorio

**Síntoma:** la clave es visible en el código o en el historial.

**Causa:** se hardcodeó o se subió una captura con la clave.

**Corrección:** revoca la clave, elimina el secreto y usa `libsecret` o una API sin clave. Después de corregir, la clave ya no está expuesta.

## Ejercicios

### Reproductivo

1. Crea `providers/binanceProvider.js` con el código del capítulo.
2. Importa el proveedor desde `extension.js`.
3. Crea la etiqueta y añádela al panel.
4. Llama a `_actualizarPrecio()` al activar.
5. Comprueba el precio en el panel y en el log.
6. Desactiva la extensión y comprueba que no hay nuevas peticiones.

### Aplicativo

1. Cambia el símbolo de `BTCUSDT` a `ETHUSDT`.
2. Cambia el intervalo del temporizador a 30 segundos.
3. Activa la extensión y comprueba el precio de Ethereum.
4. Espera una actualización y comprueba el log.
5. Explica qué línea controla el intervalo.

### Creativo

1. Crea un proveedor para Open-Meteo.
2. Usa `https://api.open-meteo.com/v1/forecast?latitude=40.4168&longitude=-3.7038&current=temperature_2m`.
3. Extrae `current.temperature_2m`.
4. Muestra la temperatura en el panel.
5. Explica qué cambiarías para usar otra ciudad.

**Rúbrica del ejercicio creativo:**

- El proveedor usa una URL de Open-Meteo.
- El campo `current.temperature_2m` se extrae correctamente.
- El panel muestra la temperatura con una unidad.
- La petición se cancela en `disable()`.
- La explicación menciona latitud y longitud.

## Autoevaluación

1. ¿Qué es Soup 3 y para qué se usa?
2. ¿Por qué una petición debe ser asíncrona en una extensión?
3. ¿Qué hace `send_and_read_async()` y qué devuelve?
4. ¿Cómo se comprueba que una respuesta HTTP es correcta?
5. El panel muestra `--` y el log dice `HTTP 429`. ¿Qué harías y en qué orden?
6. ¿Por qué no debes guardar una clave de API en GSettings?

## Respuestas razonadas

1. Soup 3 es la biblioteca HTTP de GNOME. Se usa para hacer peticiones a servidores y recibir respuestas. En GJS se importa con `gi://Soup?version=3.0`.

2. Porque el shell es un proceso interactivo. Una petición síncrona bloquearía la interfaz. `async/await` permite que el shell siga respondiendo mientras llega la respuesta.

3. `send_and_read_async()` envía el mensaje y lee el cuerpo. Devuelve una promesa que se resuelve con los bytes de la respuesta. También se puede usar con callback.

4. Se compara `message.get_status()` con `Soup.Status.OK`. Si no coincide, se lanza un error con el código. No se parsea el cuerpo como JSON si el estado no es correcto.

5. Primero esperaría, porque 429 significa demasiadas peticiones. Después aumentaría el intervalo del temporizador. Finalmente comprobaría que no hay varias instancias o temporizadores activos. El orden va de la causa temporal a la configuración.

6. Porque GSettings no cifra los valores: dconf los guarda en texto plano y otros procesos del usuario pueden leerlos. Para secretos se usa `libsecret` o una API sin clave.

## Soluciones de los ejercicios

### Solución del reproductivo

El proveedor es el de la sección 9.3.1. La parte de la extensión que muestra el precio es:

```javascript
this._precioLabel = new St.Label({
    text: '--',
    y_align: Clutter.ActorAlign.CENTER,
});
```

**Errores posibles en este bloque:**

**Error 1: `No property spacing on StBoxLayout`.**
- **Ubicación:** constructor de la caja.
- **Síntoma:** la extensión no carga.
- **Causa:** usaste `spacing` como propiedad GObject.
- **Corrección:** usa `style: 'spacing: 6px;'`. Después de corregir, la caja se crea.

**Error 2: la etiqueta no aparece.**
- **Ubicación:** panel.
- **Síntoma:** únicamente se ve el icono.
- **Causa:** falta `add_child`.
- **Corrección:** añade la etiqueta a la caja y la caja al botón. Después de corregir, la etiqueta aparece.

La actualización:

```javascript
async _actualizarPrecio() {
    if (!this._session)
        return;

    try {
        const price = await fetchPrice(this._session);
        if (this._precioLabel)
            this._precioLabel.set_text(`$${price.toFixed(2)}`);
        console.log(`[${this.uuid}] precio actualizado: ${price.toFixed(2)}`);
    } catch (error) {
        console.error(`[${this.uuid}] error al obtener el precio: ${error.message}`);
        if (this._precioLabel)
            this._precioLabel.set_text('--');
    }
}
```

**Errores posibles en este bloque:**

**Error 1: `HTTP 429`.**
- **Ubicación:** proveedor.
- **Síntoma:** el panel muestra `--`.
- **Causa:** demasiadas peticiones.
- **Corrección:** aumenta el intervalo. Después de corregir, el precio vuelve.

**Error 2: `Error: Message was cancelled`.**
- **Ubicación:** petición.
- **Síntoma:** la petición se cancela al desactivar.
- **Causa:** `session.abort()` se ejecutó.
- **Corrección:** captura el error y no actualices la etiqueta si ya no existe. Después de corregir, la cancelación no rompe la extensión.

### Solución del aplicativo

Cambia el símbolo y el intervalo:

```javascript
const price = await fetchPrice(this._session, 'ETHUSDT');

this._timeoutId = GLib.timeout_add_seconds(
    GLib.PRIORITY_DEFAULT,
    30,
    () => {
        this._actualizarPrecio();
        return GLib.SOURCE_CONTINUE;
    }
);
```

**Errores posibles en este bloque:**

**Error 1: `HTTP 400`.**
- **Ubicación:** proveedor.
- **Síntoma:** el símbolo no es válido.
- **Causa:** escribiste mal `ETHUSDT`.
- **Corrección:** usa el símbolo exacto. Después de corregir, la API responde.

**Error 2: el intervalo no cambia.**
- **Ubicación:** `timeout_add_seconds`.
- **Síntoma:** se sigue actualizando cada 60 segundos.
- **Causa:** el valor anterior sigue en el código.
- **Corrección:** cambia el segundo argumento a `30`. Después de corregir, el intervalo es de 30 segundos.

### Solución del creativo

Una solución crea `providers/openMeteoProvider.js`:

```javascript
import GLib from 'gi://GLib';
import Soup from 'gi://Soup?version=3.0';

export async function fetchTemperature(session, latitude, longitude) {
    const url = `https://api.open-meteo.com/v1/forecast?latitude=${latitude}&longitude=${longitude}&current=temperature_2m`;
    const message = Soup.Message.new('GET', url);
    if (!message)
        throw new Error('No se pudo crear la petición');

    const bytes = await session.send_and_read_async(
        message,
        GLib.PRIORITY_DEFAULT,
        null
    );

    if (message.get_status() !== Soup.Status.OK)
        throw new Error(`HTTP ${message.get_status()}`);

    const text = new TextDecoder().decode(bytes.get_data());
    const data = JSON.parse(text);
    const temperature = data.current?.temperature_2m;

    if (!Number.isFinite(temperature))
        throw new Error('Temperatura no válida');

    return temperature;
}
```

**Errores posibles en este bloque:**

**Error 1: `TypeError: data.current is undefined`.**
- **Ubicación:** proveedor.
- **Síntoma:** no se lee la temperatura.
- **Causa:** la API devolvió otro formato o un error.
- **Corrección:** comprueba `data.current` y el estado HTTP. Después de corregir, el campo existe.

**Error 2: la temperatura no aparece.**
- **Ubicación:** etiqueta.
- **Síntoma:** el panel sigue en `--`.
- **Causa:** el proveedor falló o el label no se actualizó.
- **Corrección:** revisa el log y la llamada a `set_text`. Después de corregir, la temperatura aparece.

## Si quieres saber más

- Documentación de Soup 3: <https://libsoup.gnome.org/libsoup-3.0/>
- Documentación de GLib: <https://docs.gtk.org/glib/>
- API de Binance: <https://developers.binance.com/docs/binance-spot-api-docs>
- API de Open-Meteo: <https://open-meteo.com/en/docs>

No necesitas estos enlaces para continuar. El manual contiene lo necesario para el proyecto.

## Siguiente paso

Cuando el precio se actualice y se cancele correctamente al desactivar, continúa con el Capítulo 10. Allí empaquetarás la extensión y harás las pruebas finales.
