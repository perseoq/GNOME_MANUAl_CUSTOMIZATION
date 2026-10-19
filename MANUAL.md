# Índice Detallado y Definitivo del Manual de Extensiones Nativas para GNOME Shell 50.6

Este índice comienza con un Capítulo Introductorio para lectores sin experiencia previa. Después integra JavaScript, CSS, las posibilidades de intervención desde C/C++ (mediante GObject Introspection) y el capítulo dedicado al consumo de APIs externas. Está estructurado para cubrir desde los cimientos hasta las técnicas más avanzadas de modificación manual.


## Capítulo Introductorio: Antes de Empezar

**Objetivo:** Cubrir los conocimientos mínimos que el resto del manual da por supuestos: qué es GNOME Shell, qué es una extensión, cómo se organizan los archivos y las rutas, cómo usar un editor de texto y una terminal, y qué papel cumplen JavaScript y CSS.

> **Numeración interna:** 0. Se corresponde con la etapa 0 del proyecto acumulativo.

**0.1. Qué es GNOME Shell y qué es una extensión**
- Analogía del sistema eléctrico de una casa y el aparato que se enchufa para añadir una función.
- Captura del escritorio con el panel, las actividades y el reloj señalados.

**0.2. Qué es un archivo, una carpeta y una ruta**
- Analogía del cajón, el papel dentro y la dirección completa.
- Ejercicio: abrir el gestor de archivos y navegar a una carpeta concreta.

**0.3. Qué es un editor de texto y cuál usar**
- GNOME Text Editor o VS Code, con instalación guiada paso a paso.
- Ejercicio: crear `hola.txt`, escribir una línea y guardarlo.

**0.4. Qué es una terminal y cómo abrirla**
- Apertura con `Ctrl+Alt+T` o desde Actividades.
- Ejercicio con `pwd`, `ls` y `cd`, explicando cada comando.
- Advertencia: no copiar comandos que no se entiendan.

**0.5. Qué es JavaScript y qué es CSS**
- Analogía del cerebro que decide qué hacer y la ropa que decide cómo se ve.
- Ejemplo visual de tres líneas, sin escribir código todavía.

**0.6. Cómo usar este manual**
- Estructura de cada capítulo: objetivo, concepto, ejemplo guiado, errores, ejercicios y autoevaluación.
- Regla de oro: si no se entiende un paso, no se avanza.

**Criterio de salida:** El lector sabe abrir una terminal, crear un archivo de texto, guardarlo en una ruta concreta y explicar con sus palabras qué es una extensión.

**Capítulo completo:** [Leer el Capítulo Introductorio](manual/00-introductorio.md)

## Capítulo 1: Arquitectura y Fundamentos del Shell

**Objetivo:** Comprender el entorno de ejecución, el sistema de módulos ESM y el contrato de ciclo de vida de una extensión.

**Capítulo completo:** [Leer el Capítulo 1](manual/01-arquitectura.md)

**1.1. El entorno GJS y la migración obligatoria a ESM**
- 1.1.1. Qué es GJS: SpiderMonkey + GObject Introspection 
- 1.1.2. La sintaxis `import`/`export` y la prohibición de `imports.*` desde GNOME 45
- 1.1.3. Los tres espacios de nombres: `gi://`, `resource://` y rutas relativas 

**1.2. Estructura mínima de una extensión manual**
- 1.2.1. El directorio `~/.local/share/gnome-shell/extensions/UUID/`
- 1.2.2. `metadata.json`: campos obligatorios (`uuid`, `name`, `description`, `shell-version`); `settings-schema` es opcional y `url` es obligatorio para extensions.gnome.org (EGO)
- 1.2.3. `extension.js`: la clase `Extension` y el `export default`

**1.3. El contrato `enable()` / `disable()` y la gestión de memoria**
- 1.3.1. Qué crear en `enable()` y qué destruir en `disable()`
- 1.3.2. Desconexión de señales y destrucción de actores St
- 1.3.3. Consecuencias de una limpieza deficiente: fugas y bloqueos


## Capítulo 2: El Sistema de Módulos y la API Interna

**Objetivo:** Dominar la importación correcta de librerías del sistema y los módulos internos del shell.

**Capítulo completo:** [Leer el Capítulo 2](manual/02-modulos.md)

**2.1. Importación de librerías GI con `gi://`**
- 2.1.1. Las librerías esenciales: `Clutter`, `St`, `Meta`, `Shell`, `Gio`, `GLib` 
- 2.1.2. La especificación de versión: `gi://Gtk?version=4.0` (únicamente en prefs)

**2.2. La trampa de las rutas `resource://`**
- 2.2.1. En `extension.js`: `resource:///org/gnome/shell/ui/main.js` (minúscula) 
- 2.2.2. En `prefs.js`: `resource:///org/gnome/Shell/Extensions/js/extensions/prefs.js` (mayúscula)
- 2.2.3. Ejemplo comparativo lado a lado y errores silenciosos

**2.3. Exploración de los módulos internos del shell**
- 2.3.1. `Main.panel`, `Main.overview`, `Main.notify()`
- 2.3.2. Módulos reutilizables: `PanelMenu`, `PopupMenu`, `QuickSettings`, `ModalDialog` 


## Capítulo 3: Construcción de Interfaz Gráfica con St y Clutter

**Objetivo:** Crear elementos visuales en el escritorio sin usar GTK.

**Capítulo completo:** [Leer el Capítulo 3](manual/03-interfaz.md)

**3.1. La separación de motores: St/Clutter vs. GTK4**
- 3.1.1. Por qué GTK nunca se importa en `extension.js` 
- 3.1.2. St como toolkit de widgets estilizables con CSS

**3.2. El indicador de panel paso a paso**
- 3.2.1. Instanciación de `PanelMenu.Button` 
- 3.2.2. Añadir `St.Icon` con nombres simbólicos
- 3.2.3. Registro con `Main.panel.addToStatusArea()`

**3.3. Contenedores y layouts en St**
- 3.3.1. `St.BoxLayout` (horizontal/vertical), `St.Bin`, `St.ScrollView`
- 3.3.2. Propiedades de expansión y alineación

**3.4. Animaciones con Clutter**
- 3.4.1. `actor.ease()` para transiciones simples
- 3.4.2. `easeAsync()` en GNOME 50 y manejo de promesas 
- 3.4.3. Respeto a la configuración de "Movimiento reducido" de accesibilidad


## Capítulo 4: El Sistema de Estilos CSS en St

**Objetivo:** Controlar la apariencia de los actores de St mediante CSS, comprendiendo sus limitaciones y prioridades.

**Capítulo completo:** [Leer el Capítulo 4](manual/04-css.md)

**4.1. Naturaleza y carga de `stylesheet.css`**
- 4.1.1. Detección automática por convención de nombre
- 4.1.2. Prioridad sobre el tema del sistema (excepto con `@import`) 
- 4.1.3. Extracción del CSS base del shell con `gresource`

**4.2. Selectores válidos en St**
- 4.2.1. Selector de clase (`.mi-clase`): el mecanismo principal y seguro 
- 4.2.2. Selector de tipo (`StLabel`) y sus riesgos de contaminación global
- 4.2.3. Selector por `GTypeName` para subclases registradas

**4.3. Propiedades CSS soportadas y no soportadas**
- 4.3.1. Color, fondo, bordes, espaciado, tipografía
- 4.3.2. Lo que St no soporta igual que un navegador: `display: flex`, el modelo de caja de CSS y las transiciones arbitrarias
- 4.3.3. Lo que St sí soporta: `box-shadow` y transiciones limitadas con `transition-duration` y `transition-timing-function`
- 4.3.4. Estados (`:hover`, `:active`) y su funcionamiento limitado en St

**4.4. Estilos en línea con `set_style()`**
- 4.4.1. Prioridad máxima en la cascada de St 
- 4.4.2. Cuándo usarlo (cambios dinámicos) y cuándo evitarlo (estilo base)

**4.5. Depuración de CSS**
- 4.5.1. Verificación de carga con Looking Glass Evaluator
- 4.5.2. Inspección de propiedades computadas en el Inspector
- 4.5.3. El ciclo de recarga en Wayland: logout/login obligatorio


## Capítulo 5: Persistencia y Preferencias del Usuario

**Objetivo:** Implementar configuraciones persistentes y una ventana de preferencias nativa.

**Capítulo completo:** [Leer el Capítulo 5](manual/05-persistencia.md)

**5.1. GSettings: creación manual del esquema XML**
- 5.1.1. Convención de ID y path: `org.gnome.shell.extensions.*` 
- 5.1.2. Definición de claves: tipos `b`, `i`, `s` y valores por defecto
- 5.1.3. Compilación manual con `glib-compile-schemas schemas/` 

**5.2. Lectura y escritura en `extension.js`**
- 5.2.1. `this.getSettings()` y la vinculación con `metadata.json`
- 5.2.2. La señal `'changed'` para reacción en tiempo real

**5.3. Construcción de la ventana de preferencias (`prefs.js`)**
- 5.3.1. La clase `ExtensionPreferences` y `fillPreferencesWindow()`
- 5.3.2. `Adw.PreferencesPage`, `Adw.PreferencesGroup`, `Adw.ActionRow`
- 5.3.3. Widgets: `Gtk.Switch`, `Gtk.SpinButton`, `Gtk.Entry`
- 5.3.4. Vinculación de widgets a GSettings con `settings.bind()` 

**5.4. CSS en `prefs.js`: el mundo GTK4**
- 5.4.1. Separación estricta: el `stylesheet.css` de la extensión no aplica aquí
- 5.4.2. Uso de `Gtk.CssProvider` para estilos personalizados (si es necesario)


## Capítulo 6: Interacción con el Sistema (Meta y Mutter)

**Objetivo:** Controlar ventanas, espacios de trabajo y eventos globales.

**Capítulo completo:** [Leer el Capítulo 6](manual/06-sistema.md)

**6.1. El objeto `global` y el acceso a Mutter**
- 6.1.1. `global.display` y `global.workspace_manager`
- 6.1.2. `global.get_window_actors()` para inspeccionar ventanas

**6.2. Manipulación de ventanas con Meta**
- 6.2.1. Obtener la ventana enfocada: `global.display.focus_window`
- 6.2.2. Operaciones: `minimize()`, `maximize()`, mover a otro espacio
- 6.2.3. Conexión a señales: `'window-created'`, `'focus-window-changed'`

**6.3. Gestión de espacios de trabajo**
- 6.3.1. Modelo dinámico de workspaces en GNOME 50
- 6.3.2. Cambio y reordenamiento de ventanas entre espacios

**6.4. Restricciones específicas de Wayland en GNOME 50**
- 6.4.1. Posicionamiento absoluto de ventanas: limitaciones del protocolo
- 6.4.2. Operaciones que siguen funcionando (minimizar, maximizar, cambiar de espacio)


## Capítulo 7: Técnicas Avanzadas: Monkey Patching y Depuración

**Objetivo:** Modificar el comportamiento interno del shell y solucionar errores de forma sistemática.

**Capítulo completo:** [Leer el Capítulo 7](manual/07-monkey-patching.md)

**7.1. Monkey Patching: qué es y cuándo usarlo**
- 7.1.1. El patrón seguro: guardar original, sobrescribir, restaurar en `disable()`
- 7.1.2. Ejemplo: parchear un método de `Panel` o `Overview`

**7.2. Riesgos de la API inestable**
- 7.2.1. Dependencia de nombres y firmas internas
- 7.2.2. Fragilidad ante actualizaciones menores de GNOME 50.x

**7.3. Depuración con Looking Glass**
- 7.3.1. Apertura: `Alt+F2` → `lg`
- 7.3.2. Inspector: navegación del árbol de actores
- 7.3.3. Evaluator: ejecución de código en vivo

**7.4. Lectura de logs con `journalctl`**
- 7.4.1. `journalctl /usr/bin/gnome-shell -f -o cat`
- 7.4.2. Ausencia de `Alt+F2` + `r` en Wayland: necesidad de logout/login


## Capítulo 8: Intervención desde C/C++ mediante GObject Introspection

**Objetivo:** Comprender cómo el código C/C++ se expone a GJS y cuándo es necesario (o no) escribir código nativo.

**Capítulo completo:** [Leer el Capítulo 8](manual/08-c.md)

**8.1. El modelo de GObject Introspection**
- 8.1.1. Cómo GJS accede a librerías C: anotaciones GI y typelib 
- 8.1.2. La limitación práctica: GJS consume librerías basadas en GObject, no código C arbitrario

**8.2. Qué se puede lograr desde C/C++ que no es posible (o es difícil) en GJS puro**
- 8.2.1. Librerías con requisitos de rendimiento intensivo
- 8.2.2. Integración con APIs de bajo nivel del sistema no expuestas por GObject Introspection
- 8.2.3. El caso de Mutter: `GnomeShellPlugin` es un plugin nativo del compositor, no una extensión de GNOME Shell; queda fuera del alcance de este manual

**8.3. Escribir una librería GObject consumible desde GJS (flujo de trabajo)**
- 8.3.1. Anotaciones GI en cabeceras C (`(transfer none)`, `(transfer full)`)
- 8.3.2. Generación de GIR y typelib con Meson (`gnome.generate_gir`)
- 8.3.3. Importación desde GJS: `import MiLibreria from 'gi://MiLibreria';`

**8.4. Límites prácticos para extensiones de GNOME Shell**
- 8.4.1. Las extensiones se ejecutan dentro del proceso del shell; agregar código C nativo incrementa el riesgo
- 8.4.2. Cuándo es mejor una extensión GJS pura y cuándo justificar una librería externa


## Capítulo 9: Extensiones que Consumen APIs Externas (Clima, Divisas, Criptomonedas)

**Objetivo:** Construir extensiones que obtengan datos de Internet usando las APIs nativas de GNOME (Soup 3) y muestren la información en el panel.

**Capítulo completo:** [Leer el Capítulo 9](manual/09-apis.md)

**9.1. El cliente HTTP nativo: Soup 3**
- 9.1.1. Importación: `import Soup from 'gi://Soup?version=3.0'` 
- 9.1.2. Creación de `Soup.Session` y `Soup.Message`
- 9.1.3. Peticiones asíncronas: `send_and_read_async()` y el callback de finalización 
- 9.1.4. Verificación del código de estado: `message.get_status() === Soup.Status.OK`

**9.2. Procesamiento de respuestas JSON**
- 9.2.1. Decodificación de bytes a string con `TextDecoder` 
- 9.2.2. `JSON.parse()` y manejo de errores de parseo
- 9.2.3. Extracción de campos anidados (ej. `data.main.temp`, `ticker.price`)

**9.3. Patrón de diseño: proveedor de datos intercambiable**
- 9.3.1. Separar la lógica de red en un módulo (`providers/binanceProvider.js`)
- 9.3.2. Interfaz común: `fetch()` que devuelve una Promesa con datos normalizados
- 9.3.3. Ejemplo con fuentes sin clave (Open-Meteo, Binance) y una fuente con clave como variante opcional

**9.4. Actualización periódica y gestión de errores**
- 9.4.1. Temporizadores con `GLib.timeout_add_seconds()`
- 9.4.2. Manejo de fallos de red: reintentos, timeouts, mensajes en el panel
- 9.4.3. Cancelación de peticiones pendientes en `disable()` para evitar fugas

**9.5. Manejo de claves de API sin exponerlas**
- 9.5.1. Nunca hardcodear claves en `extension.js`
- 9.5.2. Preferir APIs que no requieran clave para los ejemplos del manual
- 9.5.3. GSettings no es almacenamiento seguro: dconf guarda los valores en texto plano; para una mayor protección, usar `libsecret` (Secret Service)
- 9.5.4. Advertencia sobre la exposición de claves en código público y en repositorios

**9.6. Ejemplo completo: indicador de criptomonedas**
- 9.6.1. Petición a la API de Binance (endpoint público) o CoinGecko (con clave de prueba)
- 9.6.2. Parseo del precio y actualización de un `St.Label` en el panel
- 9.6.3. Menú desplegable con detalles adicionales y sparkline (opcional) 


## Capítulo 10: Empaquetado y Compatibilidad con GNOME 50

**Objetivo:** Preparar la extensión para uso diario o distribución pública.

**Capítulo completo:** [Leer el Capítulo 10](manual/10-empaquetado.md)

**10.1. Verificación de metadatos**
- 10.1.1. `"shell-version": ["50"]`: se declara la versión mayor; 50.6 es la versión puntual usada para probar
- 10.1.2. El campo `version` lo gestiona extensions.gnome.org; el desarrollador no debe fijarlo y puede usar `version-name` para la versión visible

**10.2. Empaquetado manual en ZIP**
- 10.2.1. Contenido en la raíz del ZIP (`metadata.json`, `extension.js`, `prefs.js`, `stylesheet.css`, `schemas/`, `providers/`)
- 10.2.2. Exclusión de archivos innecesarios (`.git`, borradores, `node_modules`)

**10.3. Criterios para la revisión de EGO**
- 10.3.1. Código legible, sin ofuscación
- 10.3.2. `disable()` que limpia todos los recursos
- 10.3.3. Restauración obligatoria de monkey patches

**10.4. Pruebas finales en entorno limpio**
- 10.4.1. Activación/desactivación repetida sin errores en logs
- 10.4.2. Persistencia de preferencias tras logout/login
- 10.4.3. Verificación en Wayland (entorno por defecto en GNOME 50)


## Apéndices

- [Glosario](manual/apendices/GLOSARIO.md)
- [Mapa de prerrequisitos](manual/apendices/MAPA-PREREQUISITOS.md)
- [Índice de soluciones](manual/apendices/SOLUCIONES.md)

## Resumen de Cobertura por Lenguaje/Tecnología

| Tecnología | Capítulos donde se desarrolla |
| :--- | :--- |
| **JavaScript (GJS/ESM)** | 1, 2, 3, 5, 6, 7, 9 |
| **CSS (St)** | 3 (introducción), 4 (capítulo completo), 5.4 (GTK4 en prefs) |
| **C/C++ (GObject Introspection)** | 8 (capítulo completo), con referencia a Mutter en 6.1 |
| **APIs externas (HTTP/JSON)** | 9 (capítulo completo) |


