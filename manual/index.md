# Índice del Manual

**Manual de Extensiones Nativas para GNOME Shell 50.6**

Este es el índice navegable del manual. Cada enlace lleva al capítulo y a la sección correspondiente. El índice editorial detallado sigue en [`MANUAL.md`](../MANUAL.md).

---

## Cómo usar este índice

- Empieza por el [Capítulo Introductorio](00-introductorio.md#capítulo-introductorio-antes-de-empezar) si nunca has programado.
- Si ya sabes programar, puedes ir directamente al capítulo que necesites.
- Cada capítulo incluye objetivos, ejemplo guiado, verificación, errores, ejercicios, autoevaluación y soluciones.
- Los enlaces externos de cada capítulo están únicamente en “Si quieres saber más” y son opcionales.

## Ruta recomendada

```text
Introductorio → 1 → 2 → 3 → 4 → 5 → 6 → 7 → 9 → 10
```

El Capítulo 8 es opcional. No es requisito para completar el proyecto.

## Capítulos

### [Capítulo Introductorio: Antes de Empezar](00-introductorio.md#capítulo-introductorio-antes-de-empezar)

Conceptos mínimos: GNOME Shell, archivos, rutas, editor de texto, terminal, JavaScript y CSS.

- [Qué vas a aprender](00-introductorio.md#qué-vas-a-aprender)
- [Antes de empezar necesitas](00-introductorio.md#antes-de-empezar-necesitas)
- [El concepto, con una analogía](00-introductorio.md#el-concepto-con-una-analogía)
    - [0.1. Qué es GNOME Shell y qué es una extensión](00-introductorio.md#01-qué-es-gnome-shell-y-qué-es-una-extensión)
    - [0.2. Qué es un archivo, una carpeta y una ruta](00-introductorio.md#02-qué-es-un-archivo-una-carpeta-y-una-ruta)
    - [0.3. Qué es un editor de texto y cuál usar](00-introductorio.md#03-qué-es-un-editor-de-texto-y-cuál-usar)
    - [0.4. Qué es una terminal y cómo abrirla](00-introductorio.md#04-qué-es-una-terminal-y-cómo-abrirla)
        - [Bloque 1: saber dónde estás con pwd](00-introductorio.md#bloque-1-saber-dónde-estás-con-pwd)
        - [Bloque 2: ver qué hay con ls](00-introductorio.md#bloque-2-ver-qué-hay-con-ls)
        - [Bloque 3: entrar en una carpeta con cd](00-introductorio.md#bloque-3-entrar-en-una-carpeta-con-cd)
        - [Bloque 4: volver a la carpeta anterior con cd ..](00-introductorio.md#bloque-4-volver-a-la-carpeta-anterior-con-cd-)
        - [Bloque 5: entrar en una carpeta con espacios](00-introductorio.md#bloque-5-entrar-en-una-carpeta-con-espacios)
    - [0.5. Qué es JavaScript y qué es CSS](00-introductorio.md#05-qué-es-javascript-y-qué-es-css)
    - [0.6. Cómo usar este manual](00-introductorio.md#06-cómo-usar-este-manual)
- [Verifica que funciona](00-introductorio.md#verifica-que-funciona)
- [Errores de integración del capítulo](00-introductorio.md#errores-de-integración-del-capítulo)
- [Ejercicios](00-introductorio.md#ejercicios)
    - [Reproductivo](00-introductorio.md#reproductivo)
    - [Aplicativo](00-introductorio.md#aplicativo)
    - [Creativo](00-introductorio.md#creativo)
- [Autoevaluación](00-introductorio.md#autoevaluación)
- [Respuestas razonadas](00-introductorio.md#respuestas-razonadas)
- [Soluciones de los ejercicios](00-introductorio.md#soluciones-de-los-ejercicios)
    - [Solución del reproductivo](00-introductorio.md#solución-del-reproductivo)
    - [Solución del aplicativo](00-introductorio.md#solución-del-aplicativo)
    - [Solución del creativo](00-introductorio.md#solución-del-creativo)
- [Si quieres saber más](00-introductorio.md#si-quieres-saber-más)
- [Siguiente paso](00-introductorio.md#siguiente-paso)

### [Capítulo 1: Arquitectura y Fundamentos del Shell](01-arquitectura.md#capítulo-1-arquitectura-y-fundamentos-del-shell)

GJS, módulos ESM, estructura de una extensión y contrato `enable()` / `disable()`.

- [Qué vas a aprender](01-arquitectura.md#qué-vas-a-aprender)
- [Antes de empezar necesitas](01-arquitectura.md#antes-de-empezar-necesitas)
    - [Comprueba tu versión](01-arquitectura.md#comprueba-tu-versión)
- [El concepto, con una analogía](01-arquitectura.md#el-concepto-con-una-analogía)
    - [1.1. El entorno GJS y la migración obligatoria a ESM](01-arquitectura.md#11-el-entorno-gjs-y-la-migración-obligatoria-a-esm)
        - [1.1.1. Qué es GJS](01-arquitectura.md#111-qué-es-gjs)
        - [1.1.2. La sintaxis import / export y la prohibición de imports.*](01-arquitectura.md#112-la-sintaxis-import--export-y-la-prohibición-de-imports)
        - [1.1.3. Los tres espacios de nombres](01-arquitectura.md#113-los-tres-espacios-de-nombres)
    - [1.2. Estructura mínima de una extensión manual](01-arquitectura.md#12-estructura-mínima-de-una-extensión-manual)
        - [1.2.1. El directorio de la extensión](01-arquitectura.md#121-el-directorio-de-la-extensión)
        - [1.2.2. metadata.json: identidad de la extensión](01-arquitectura.md#122-metadatajson-identidad-de-la-extensión)
        - [1.2.3. extension.js: la clase Extension y export default](01-arquitectura.md#123-extensionjs-la-clase-extension-y-export-default)
    - [1.3. El contrato enable() / disable() y la gestión de memoria](01-arquitectura.md#13-el-contrato-enable--disable-y-la-gestión-de-memoria)
        - [1.3.1. Qué crear en enable() y qué destruir en disable()](01-arquitectura.md#131-qué-crear-en-enable-y-qué-destruir-en-disable)
        - [1.3.2. Desconexión de señales y destrucción de actores St](01-arquitectura.md#132-desconexión-de-señales-y-destrucción-de-actores-st)
        - [1.3.3. Consecuencias de una limpieza deficiente](01-arquitectura.md#133-consecuencias-de-una-limpieza-deficiente)
- [Verifica que funciona](01-arquitectura.md#verifica-que-funciona)
- [Errores de integración del capítulo](01-arquitectura.md#errores-de-integración-del-capítulo)
- [Ejercicios](01-arquitectura.md#ejercicios)
    - [Reproductivo](01-arquitectura.md#reproductivo)
    - [Aplicativo](01-arquitectura.md#aplicativo)
    - [Creativo](01-arquitectura.md#creativo)
- [Autoevaluación](01-arquitectura.md#autoevaluación)
- [Respuestas razonadas](01-arquitectura.md#respuestas-razonadas)
- [Soluciones de los ejercicios](01-arquitectura.md#soluciones-de-los-ejercicios)
    - [Solución del reproductivo](01-arquitectura.md#solución-del-reproductivo)
    - [Solución del aplicativo](01-arquitectura.md#solución-del-aplicativo)
    - [Solución del creativo](01-arquitectura.md#solución-del-creativo)
- [Si quieres saber más](01-arquitectura.md#si-quieres-saber-más)
- [Siguiente paso](01-arquitectura.md#siguiente-paso)

### [Capítulo 2: El Sistema de Módulos y la API Interna](02-modulos.md#capítulo-2-el-sistema-de-módulos-y-la-api-interna)

Imports `gi://`, rutas `resource://` y módulos internos del shell.

- [Qué vas a aprender](02-modulos.md#qué-vas-a-aprender)
- [Antes de empezar necesitas](02-modulos.md#antes-de-empezar-necesitas)
- [El concepto, con una analogía](02-modulos.md#el-concepto-con-una-analogía)
    - [2.1. Importación de librerías GI con gi://](02-modulos.md#21-importación-de-librerías-gi-con-gi)
    - [2.2. La trampa de las rutas resource://](02-modulos.md#22-la-trampa-de-las-rutas-resource)
    - [2.3. Exploración de los módulos internos del shell](02-modulos.md#23-exploración-de-los-módulos-internos-del-shell)
- [Ejemplo guiado paso a paso](02-modulos.md#ejemplo-guiado-paso-a-paso)
    - [Bloque 1: importar Main y Extension](02-modulos.md#bloque-1-importar-main-y-extension)
    - [Bloque 2: clase con notificación](02-modulos.md#bloque-2-clase-con-notificación)
    - [Guarda y recarga](02-modulos.md#guarda-y-recarga)
- [Verifica que funciona](02-modulos.md#verifica-que-funciona)
- [Errores de integración del capítulo](02-modulos.md#errores-de-integración-del-capítulo)
- [Ejercicios](02-modulos.md#ejercicios)
    - [Reproductivo](02-modulos.md#reproductivo)
    - [Aplicativo](02-modulos.md#aplicativo)
    - [Creativo](02-modulos.md#creativo)
- [Autoevaluación](02-modulos.md#autoevaluación)
- [Respuestas razonadas](02-modulos.md#respuestas-razonadas)
- [Soluciones de los ejercicios](02-modulos.md#soluciones-de-los-ejercicios)
    - [Solución del reproductivo](02-modulos.md#solución-del-reproductivo)
    - [Solución del aplicativo](02-modulos.md#solución-del-aplicativo)
    - [Solución del creativo](02-modulos.md#solución-del-creativo)
- [Si quieres saber más](02-modulos.md#si-quieres-saber-más)
- [Siguiente paso](02-modulos.md#siguiente-paso)

### [Capítulo 3: Construcción de Interfaz Gráfica con St y Clutter](03-interfaz.md#capítulo-3-construcción-de-interfaz-gráfica-con-st-y-clutter)

St, Clutter, indicador de panel, contenedores y animaciones.

- [Qué vas a aprender](03-interfaz.md#qué-vas-a-aprender)
- [Antes de empezar necesitas](03-interfaz.md#antes-de-empezar-necesitas)
- [El concepto, con una analogía](03-interfaz.md#el-concepto-con-una-analogía)
    - [3.1. La separación de motores: St/Clutter frente a GTK4](03-interfaz.md#31-la-separación-de-motores-stclutter-frente-a-gtk4)
        - [3.1.1. Por qué GTK nunca se importa en extension.js](03-interfaz.md#311-por-qué-gtk-nunca-se-importa-en-extensionjs)
        - [3.1.2. St como toolkit de widgets estilizables con CSS](03-interfaz.md#312-st-como-toolkit-de-widgets-estilizables-con-css)
    - [3.2. El indicador de panel paso a paso](03-interfaz.md#32-el-indicador-de-panel-paso-a-paso)
        - [3.2.1. Instanciación de PanelMenu.Button](03-interfaz.md#321-instanciación-de-panelmenubutton)
        - [3.2.2. Añadir St.Icon con nombres simbólicos](03-interfaz.md#322-añadir-sticon-con-nombres-simbólicos)
        - [3.2.3. Registro con Main.panel.addToStatusArea()](03-interfaz.md#323-registro-con-mainpaneladdtostatusarea)
        - [3.2.4. Destruir el indicador en disable()](03-interfaz.md#324-destruir-el-indicador-en-disable)
    - [3.3. Contenedores y layouts en St](03-interfaz.md#33-contenedores-y-layouts-en-st)
    - [3.4. Animaciones con Clutter](03-interfaz.md#34-animaciones-con-clutter)
        - [3.4.1. actor.ease() para transiciones simples](03-interfaz.md#341-actorease-para-transiciones-simples)
        - [3.4.2. easeAsync() en GNOME 50 y manejo de promesas](03-interfaz.md#342-easeasync-en-gnome-50-y-manejo-de-promesas)
        - [3.4.3. Respeto a la configuración de movimiento reducido](03-interfaz.md#343-respeto-a-la-configuración-de-movimiento-reducido)
- [Verifica que funciona](03-interfaz.md#verifica-que-funciona)
- [Errores de integración del capítulo](03-interfaz.md#errores-de-integración-del-capítulo)
- [Ejercicios](03-interfaz.md#ejercicios)
    - [Reproductivo](03-interfaz.md#reproductivo)
    - [Aplicativo](03-interfaz.md#aplicativo)
    - [Creativo](03-interfaz.md#creativo)
- [Autoevaluación](03-interfaz.md#autoevaluación)
- [Respuestas razonadas](03-interfaz.md#respuestas-razonadas)
- [Soluciones de los ejercicios](03-interfaz.md#soluciones-de-los-ejercicios)
    - [Solución del reproductivo](03-interfaz.md#solución-del-reproductivo)
    - [Solución del aplicativo](03-interfaz.md#solución-del-aplicativo)
    - [Solución del creativo](03-interfaz.md#solución-del-creativo)
- [Si quieres saber más](03-interfaz.md#si-quieres-saber-más)
- [Siguiente paso](03-interfaz.md#siguiente-paso)

### [Capítulo 4: El Sistema de Estilos CSS en St](04-css.md#capítulo-4-el-sistema-de-estilos-css-en-st)

`stylesheet.css`, selectores, propiedades soportadas y depuración de CSS.

- [Qué vas a aprender](04-css.md#qué-vas-a-aprender)
- [Antes de empezar necesitas](04-css.md#antes-de-empezar-necesitas)
- [El concepto, con una analogía](04-css.md#el-concepto-con-una-analogía)
    - [4.1. Naturaleza y carga de stylesheet.css](04-css.md#41-naturaleza-y-carga-de-stylesheetcss)
        - [4.1.1. Detección automática por convención de nombre](04-css.md#411-detección-automática-por-convención-de-nombre)
        - [4.1.2. Prioridad sobre el tema del sistema](04-css.md#412-prioridad-sobre-el-tema-del-sistema)
        - [4.1.3. Extracción del CSS base del shell con gresource](04-css.md#413-extracción-del-css-base-del-shell-con-gresource)
    - [4.2. Selectores válidos en St](04-css.md#42-selectores-válidos-en-st)
        - [4.2.1. Selector de clase (.mi-clase)](04-css.md#421-selector-de-clase-mi-clase)
        - [4.2.2. Selector de tipo (StLabel) y sus riesgos de contaminación global](04-css.md#422-selector-de-tipo-stlabel-y-sus-riesgos-de-contaminación-global)
        - [4.2.3. Selector por GTypeName para subclases registradas](04-css.md#423-selector-por-gtypename-para-subclases-registradas)
    - [4.3. Propiedades CSS soportadas y no soportadas](04-css.md#43-propiedades-css-soportadas-y-no-soportadas)
        - [4.3.1. Color, fondo, bordes, espaciado y tipografía](04-css.md#431-color-fondo-bordes-espaciado-y-tipografía)
        - [4.3.2. Lo que St no soporta igual que un navegador](04-css.md#432-lo-que-st-no-soporta-igual-que-un-navegador)
        - [4.3.3. Lo que St sí soporta: box-shadow y transiciones limitadas](04-css.md#433-lo-que-st-sí-soporta-box-shadow-y-transiciones-limitadas)
        - [4.3.4. Estados (:hover, :active) y su funcionamiento limitado en St](04-css.md#434-estados-hover-active-y-su-funcionamiento-limitado-en-st)
    - [4.4. Estilos en línea con set_style()](04-css.md#44-estilos-en-línea-con-set_style)
    - [4.5. Depuración de CSS](04-css.md#45-depuración-de-css)
        - [4.5.1. Verificación de carga con Looking Glass Evaluator](04-css.md#451-verificación-de-carga-con-looking-glass-evaluator)
        - [4.5.2. Inspección de propiedades computadas en el Inspector](04-css.md#452-inspección-de-propiedades-computadas-en-el-inspector)
        - [4.5.3. El ciclo de recarga en Wayland](04-css.md#453-el-ciclo-de-recarga-en-wayland)
- [Ejemplo guiado paso a paso](04-css.md#ejemplo-guiado-paso-a-paso)
    - [Bloque 1: color y forma del botón](04-css.md#bloque-1-color-y-forma-del-botón)
    - [Bloque 2: estados del botón](04-css.md#bloque-2-estados-del-botón)
    - [Guarda y recarga](04-css.md#guarda-y-recarga)
- [Verifica que funciona](04-css.md#verifica-que-funciona)
- [Errores de integración del capítulo](04-css.md#errores-de-integración-del-capítulo)
- [Ejercicios](04-css.md#ejercicios)
    - [Reproductivo](04-css.md#reproductivo)
    - [Aplicativo](04-css.md#aplicativo)
    - [Creativo](04-css.md#creativo)
- [Autoevaluación](04-css.md#autoevaluación)
- [Respuestas razonadas](04-css.md#respuestas-razonadas)
- [Soluciones de los ejercicios](04-css.md#soluciones-de-los-ejercicios)
    - [Solución del reproductivo](04-css.md#solución-del-reproductivo)
    - [Solución del aplicativo](04-css.md#solución-del-aplicativo)
    - [Solución del creativo](04-css.md#solución-del-creativo)
- [Si quieres saber más](04-css.md#si-quieres-saber-más)
- [Siguiente paso](04-css.md#siguiente-paso)

### [Capítulo 5: Persistencia y Preferencias del Usuario](05-persistencia.md#capítulo-5-persistencia-y-preferencias-del-usuario)

GSettings, esquema XML, `prefs.js` y persistencia de la configuración.

- [Qué vas a aprender](05-persistencia.md#qué-vas-a-aprender)
- [Antes de empezar necesitas](05-persistencia.md#antes-de-empezar-necesitas)
- [El concepto, con una analogía](05-persistencia.md#el-concepto-con-una-analogía)
    - [5.1. GSettings: creación manual del esquema XML](05-persistencia.md#51-gsettings-creación-manual-del-esquema-xml)
        - [5.1.1. Convención de ID y ruta](05-persistencia.md#511-convención-de-id-y-ruta)
        - [5.1.2. Definición de claves: tipos s, b e i](05-persistencia.md#512-definición-de-claves-tipos-s-b-e-i)
        - [5.1.3. Compilación manual con glib-compile-schemas](05-persistencia.md#513-compilación-manual-con-glib-compile-schemas)
    - [5.2. Lectura y escritura en extension.js](05-persistencia.md#52-lectura-y-escritura-en-extensionjs)
        - [5.2.1. this.getSettings() y la vinculación con metadata.json](05-persistencia.md#521-thisgetsettings-y-la-vinculación-con-metadatajson)
        - [5.2.2. La señal 'changed' para reacción en tiempo real](05-persistencia.md#522-la-señal-changed-para-reacción-en-tiempo-real)
        - [5.2.3. Limpieza en disable()](05-persistencia.md#523-limpieza-en-disable)
    - [5.3. Construcción de la ventana de preferencias (prefs.js)](05-persistencia.md#53-construcción-de-la-ventana-de-preferencias-prefsjs)
        - [5.3.1. La clase ExtensionPreferences y fillPreferencesWindow()](05-persistencia.md#531-la-clase-extensionpreferences-y-fillpreferenceswindow)
        - [5.3.2. Adw.PreferencesPage, Adw.PreferencesGroup y Adw.ActionRow](05-persistencia.md#532-adwpreferencespage-adwpreferencesgroup-y-adwactionrow)
        - [5.3.3. Widgets: Gtk.Switch, Gtk.SpinButton y Gtk.Entry](05-persistencia.md#533-widgets-gtkswitch-gtkspinbutton-y-gtkentry)
        - [5.3.4. Vinculación de widgets a GSettings con settings.bind()](05-persistencia.md#534-vinculación-de-widgets-a-gsettings-con-settingsbind)
    - [5.4. CSS en prefs.js: el mundo GTK4](05-persistencia.md#54-css-en-prefsjs-el-mundo-gtk4)
        - [5.4.1. Separación estricta: el stylesheet.css de la extensión no aplica aquí](05-persistencia.md#541-separación-estricta-el-stylesheetcss-de-la-extensión-no-aplica-aquí)
        - [5.4.2. Uso de Gtk.CssProvider para estilos personalizados](05-persistencia.md#542-uso-de-gtkcssprovider-para-estilos-personalizados)
- [Ejemplo guiado paso a paso](05-persistencia.md#ejemplo-guiado-paso-a-paso)
    - [Bloque 1: crear y compilar el esquema](05-persistencia.md#bloque-1-crear-y-compilar-el-esquema)
    - [Bloque 2: cargar la configuración en enable()](05-persistencia.md#bloque-2-cargar-la-configuración-en-enable)
    - [Bloque 3: aplicar el color y limpiar](05-persistencia.md#bloque-3-aplicar-el-color-y-limpiar)
    - [Guarda y recarga](05-persistencia.md#guarda-y-recarga)
    - [Bloque 4: crear la ventana de preferencias](05-persistencia.md#bloque-4-crear-la-ventana-de-preferencias)
- [Verifica que funciona](05-persistencia.md#verifica-que-funciona)
- [Errores de integración del capítulo](05-persistencia.md#errores-de-integración-del-capítulo)
- [Ejercicios](05-persistencia.md#ejercicios)
    - [Reproductivo](05-persistencia.md#reproductivo)
    - [Aplicativo](05-persistencia.md#aplicativo)
    - [Creativo](05-persistencia.md#creativo)
- [Autoevaluación](05-persistencia.md#autoevaluación)
- [Respuestas razonadas](05-persistencia.md#respuestas-razonadas)
- [Soluciones de los ejercicios](05-persistencia.md#soluciones-de-los-ejercicios)
    - [Solución del reproductivo](05-persistencia.md#solución-del-reproductivo)
    - [Solución del aplicativo](05-persistencia.md#solución-del-aplicativo)
    - [Solución del creativo](05-persistencia.md#solución-del-creativo)
- [Si quieres saber más](05-persistencia.md#si-quieres-saber-más)
- [Siguiente paso](05-persistencia.md#siguiente-paso)

### [Capítulo 6: Interacción con el Sistema (Meta y Mutter)](06-sistema.md#capítulo-6-interacción-con-el-sistema-meta-y-mutter)

`global`, Meta, ventanas, espacios de trabajo y límites de Wayland.

- [Qué vas a aprender](06-sistema.md#qué-vas-a-aprender)
- [Antes de empezar necesitas](06-sistema.md#antes-de-empezar-necesitas)
- [El concepto, con una analogía](06-sistema.md#el-concepto-con-una-analogía)
    - [6.1. El objeto global y el acceso a Mutter](06-sistema.md#61-el-objeto-global-y-el-acceso-a-mutter)
        - [6.1.1. global.display y global.workspace_manager](06-sistema.md#611-globaldisplay-y-globalworkspace_manager)
        - [6.1.2. global.get_window_actors() para inspeccionar ventanas](06-sistema.md#612-globalget_window_actors-para-inspeccionar-ventanas)
    - [6.2. Manipulación de ventanas con Meta](06-sistema.md#62-manipulación-de-ventanas-con-meta)
        - [6.2.1. Obtener la ventana enfocada: global.display.focus_window](06-sistema.md#621-obtener-la-ventana-enfocada-globaldisplayfocus_window)
        - [6.2.2. Operaciones: minimize(), maximize() y mover a otro espacio](06-sistema.md#622-operaciones-minimize-maximize-y-mover-a-otro-espacio)
        - [6.2.3. Conexión a señales: window-created y focus-window-changed](06-sistema.md#623-conexión-a-señales-window-created-y-focus-window-changed)
    - [6.3. Gestión de espacios de trabajo](06-sistema.md#63-gestión-de-espacios-de-trabajo)
        - [6.3.1. Modelo dinámico de espacios de trabajo en GNOME 50](06-sistema.md#631-modelo-dinámico-de-espacios-de-trabajo-en-gnome-50)
        - [6.3.2. Cambio y reordenamiento de ventanas entre espacios](06-sistema.md#632-cambio-y-reordenamiento-de-ventanas-entre-espacios)
    - [6.4. Restricciones específicas de Wayland en GNOME 50](06-sistema.md#64-restricciones-específicas-de-wayland-en-gnome-50)
- [Ejemplo guiado paso a paso](06-sistema.md#ejemplo-guiado-paso-a-paso)
    - [Bloque 1: importar Meta](06-sistema.md#bloque-1-importar-meta)
    - [Bloque 2: añadir la acción al menú](06-sistema.md#bloque-2-añadir-la-acción-al-menú)
    - [Bloque 3: mover la ventana](06-sistema.md#bloque-3-mover-la-ventana)
    - [Guarda y recarga](06-sistema.md#guarda-y-recarga)
- [Verifica que funciona](06-sistema.md#verifica-que-funciona)
- [Errores de integración del capítulo](06-sistema.md#errores-de-integración-del-capítulo)
- [Ejercicios](06-sistema.md#ejercicios)
    - [Reproductivo](06-sistema.md#reproductivo)
    - [Aplicativo](06-sistema.md#aplicativo)
    - [Creativo](06-sistema.md#creativo)
- [Autoevaluación](06-sistema.md#autoevaluación)
- [Respuestas razonadas](06-sistema.md#respuestas-razonadas)
- [Soluciones de los ejercicios](06-sistema.md#soluciones-de-los-ejercicios)
    - [Solución del reproductivo](06-sistema.md#solución-del-reproductivo)
    - [Solución del aplicativo](06-sistema.md#solución-del-aplicativo)
    - [Solución del creativo](06-sistema.md#solución-del-creativo)
- [Si quieres saber más](06-sistema.md#si-quieres-saber-más)
- [Siguiente paso](06-sistema.md#siguiente-paso)

### [Capítulo 7: Técnicas Avanzadas: Monkey Patching y Depuración](07-monkey-patching.md#capítulo-7-técnicas-avanzadas-monkey-patching-y-depuración)

Monkey patching reversible, Looking Glass y lectura de logs con `journalctl`.

- [Qué vas a aprender](07-monkey-patching.md#qué-vas-a-aprender)
- [Antes de empezar necesitas](07-monkey-patching.md#antes-de-empezar-necesitas)
- [El concepto, con una analogía](07-monkey-patching.md#el-concepto-con-una-analogía)
    - [7.1. Monkey Patching: qué es y cuándo usarlo](07-monkey-patching.md#71-monkey-patching-qué-es-y-cuándo-usarlo)
        - [7.1.1. El patrón seguro: guardar original, sobrescribir, restaurar en disable()](07-monkey-patching.md#711-el-patrón-seguro-guardar-original-sobrescribir-restaurar-en-disable)
        - [7.1.2. Ejemplo: parchear un método de Panel o Overview](07-monkey-patching.md#712-ejemplo-parchear-un-método-de-panel-o-overview)
    - [7.2. Riesgos de la API inestable](07-monkey-patching.md#72-riesgos-de-la-api-inestable)
        - [7.2.1. Dependencia de nombres y firmas internas](07-monkey-patching.md#721-dependencia-de-nombres-y-firmas-internas)
        - [7.2.2. Fragilidad ante actualizaciones menores de GNOME 50.x](07-monkey-patching.md#722-fragilidad-ante-actualizaciones-menores-de-gnome-50x)
    - [7.3. Depuración con Looking Glass](07-monkey-patching.md#73-depuración-con-looking-glass)
        - [7.3.1. Apertura: Alt+F2 → lg](07-monkey-patching.md#731-apertura-altf2--lg)
        - [7.3.2. Inspector: navegación del árbol de actores](07-monkey-patching.md#732-inspector-navegación-del-árbol-de-actores)
        - [7.3.3. Evaluator: ejecución de código en vivo](07-monkey-patching.md#733-evaluator-ejecución-de-código-en-vivo)
    - [7.4. Lectura de logs con journalctl](07-monkey-patching.md#74-lectura-de-logs-con-journalctl)
        - [7.4.1. journalctl /usr/bin/gnome-shell -f -o cat](07-monkey-patching.md#741-journalctl-usrbingnome-shell--f--o-cat)
        - [7.4.2. Ausencia de Alt+F2 + r en Wayland](07-monkey-patching.md#742-ausencia-de-altf2--r-en-wayland)
- [Verifica que funciona](07-monkey-patching.md#verifica-que-funciona)
- [Errores de integración del capítulo](07-monkey-patching.md#errores-de-integración-del-capítulo)
- [Ejercicios](07-monkey-patching.md#ejercicios)
    - [Reproductivo](07-monkey-patching.md#reproductivo)
    - [Aplicativo](07-monkey-patching.md#aplicativo)
    - [Creativo](07-monkey-patching.md#creativo)
- [Autoevaluación](07-monkey-patching.md#autoevaluación)
- [Respuestas razonadas](07-monkey-patching.md#respuestas-razonadas)
- [Soluciones de los ejercicios](07-monkey-patching.md#soluciones-de-los-ejercicios)
    - [Solución del reproductivo](07-monkey-patching.md#solución-del-reproductivo)
    - [Solución del aplicativo](07-monkey-patching.md#solución-del-aplicativo)
    - [Solución del creativo](07-monkey-patching.md#solución-del-creativo)
- [Si quieres saber más](07-monkey-patching.md#si-quieres-saber-más)
- [Siguiente paso](07-monkey-patching.md#siguiente-paso)

### [Capítulo 8: Intervención desde C/C++ mediante GObject Introspection](08-c.md#capítulo-8-intervención-desde-cc-mediante-gobject-introspection)

GObject Introspection, GIR, typelib y límites del código nativo. Capítulo opcional.

- [Qué vas a aprender](08-c.md#qué-vas-a-aprender)
- [Antes de empezar necesitas](08-c.md#antes-de-empezar-necesitas)
- [El concepto, con una analogía](08-c.md#el-concepto-con-una-analogía)
    - [8.1. El modelo de GObject Introspection](08-c.md#81-el-modelo-de-gobject-introspection)
        - [8.1.1. Cómo GJS accede a librerías C: anotaciones GI y typelib](08-c.md#811-cómo-gjs-accede-a-librerías-c-anotaciones-gi-y-typelib)
        - [8.1.2. La limitación práctica: GJS consume librerías basadas en GObject, no código C arbitrario](08-c.md#812-la-limitación-práctica-gjs-consume-librerías-basadas-en-gobject-no-código-c-arbitrario)
    - [8.2. Qué se puede lograr desde C/C++ que no es posible (o es difícil) en GJS puro](08-c.md#82-qué-se-puede-lograr-desde-cc-que-no-es-posible-o-es-difícil-en-gjs-puro)
        - [8.2.1. Librerías con requisitos de rendimiento intensivo](08-c.md#821-librerías-con-requisitos-de-rendimiento-intensivo)
        - [8.2.2. Integración con APIs de bajo nivel del sistema no expuestas por GObject Introspection](08-c.md#822-integración-con-apis-de-bajo-nivel-del-sistema-no-expuestas-por-gobject-introspection)
        - [8.2.3. El caso de Mutter: GnomeShellPlugin como punto de entrada C del shell](08-c.md#823-el-caso-de-mutter-gnomeshellplugin-como-punto-de-entrada-c-del-shell)
    - [8.3. Escribir una librería GObject consumible desde GJS (flujo de trabajo)](08-c.md#83-escribir-una-librería-gobject-consumible-desde-gjs-flujo-de-trabajo)
        - [8.3.1. Anotaciones GI en cabeceras C](08-c.md#831-anotaciones-gi-en-cabeceras-c)
        - [8.3.2. Generación de GIR y typelib con Meson (gnome.generate_gir)](08-c.md#832-generación-de-gir-y-typelib-con-meson-gnomegenerate_gir)
        - [8.3.3. Importación desde GJS: import MiLibreria from 'gi://MiLibreria';](08-c.md#833-importación-desde-gjs-import-milibreria-from-gimilibreria)
    - [8.4. Límites prácticos para extensiones de GNOME Shell](08-c.md#84-límites-prácticos-para-extensiones-de-gnome-shell)
        - [8.4.1. Las extensiones se ejecutan dentro del proceso del shell; agregar código C nativo incrementa el riesgo](08-c.md#841-las-extensiones-se-ejecutan-dentro-del-proceso-del-shell-agregar-código-c-nativo-incrementa-el-riesgo)
        - [8.4.2. Cuándo es mejor una extensión GJS pura y cuándo justificar una librería externa](08-c.md#842-cuándo-es-mejor-una-extensión-gjs-pura-y-cuándo-justificar-una-librería-externa)
- [Verifica que funciona](08-c.md#verifica-que-funciona)
- [Errores de integración del capítulo](08-c.md#errores-de-integración-del-capítulo)
- [Ejercicios](08-c.md#ejercicios)
    - [Reproductivo](08-c.md#reproductivo)
    - [Aplicativo](08-c.md#aplicativo)
    - [Creativo](08-c.md#creativo)
- [Autoevaluación](08-c.md#autoevaluación)
- [Respuestas razonadas](08-c.md#respuestas-razonadas)
- [Soluciones de los ejercicios](08-c.md#soluciones-de-los-ejercicios)
    - [Solución del reproductivo](08-c.md#solución-del-reproductivo)
    - [Solución del aplicativo](08-c.md#solución-del-aplicativo)
    - [Solución del creativo](08-c.md#solución-del-creativo)
- [Si quieres saber más](08-c.md#si-quieres-saber-más)
- [Siguiente paso](08-c.md#siguiente-paso)

### [Capítulo 9: Extensiones que Consumen APIs Externas](09-apis.md#capítulo-9-extensiones-que-consumen-apis-externas)

Soup 3, JSON, proveedores de datos y actualización periódica.

- [Qué vas a aprender](09-apis.md#qué-vas-a-aprender)
- [Antes de empezar necesitas](09-apis.md#antes-de-empezar-necesitas)
- [El concepto, con una analogía](09-apis.md#el-concepto-con-una-analogía)
    - [9.1. El cliente HTTP nativo: Soup 3](09-apis.md#91-el-cliente-http-nativo-soup-3)
        - [9.1.1. Importación: import Soup from 'gi://Soup?version=3.0'](09-apis.md#911-importación-import-soup-from-gisoupversion30)
        - [9.1.2. Creación de Soup.Session y Soup.Message](09-apis.md#912-creación-de-soupsession-y-soupmessage)
        - [9.1.3. Peticiones asíncronas: send_and_read_async()](09-apis.md#913-peticiones-asíncronas-send_and_read_async)
        - [9.1.4. Verificación del código de estado: message.get_status() === Soup.Status.OK](09-apis.md#914-verificación-del-código-de-estado-messageget_status--soupstatusok)
    - [9.2. Procesamiento de respuestas JSON](09-apis.md#92-procesamiento-de-respuestas-json)
        - [9.2.1. Decodificación de bytes a string con TextDecoder](09-apis.md#921-decodificación-de-bytes-a-string-con-textdecoder)
        - [9.2.2. JSON.parse() y manejo de errores de parseo](09-apis.md#922-jsonparse-y-manejo-de-errores-de-parseo)
        - [9.2.3. Extracción de campos anidados](09-apis.md#923-extracción-de-campos-anidados)
    - [9.3. Patrón de diseño: proveedor de datos intercambiable](09-apis.md#93-patrón-de-diseño-proveedor-de-datos-intercambiable)
        - [9.3.1. Separar la lógica de red en un módulo (providers/binanceProvider.js)](09-apis.md#931-separar-la-lógica-de-red-en-un-módulo-providersbinanceproviderjs)
        - [9.3.2. Interfaz común: fetch() que devuelve una Promesa con datos normalizados](09-apis.md#932-interfaz-común-fetch-que-devuelve-una-promesa-con-datos-normalizados)
        - [9.3.3. Ejemplo con múltiples fuentes](09-apis.md#933-ejemplo-con-múltiples-fuentes)
    - [9.4. Actualización periódica y gestión de errores](09-apis.md#94-actualización-periódica-y-gestión-de-errores)
        - [9.4.1. Temporizadores con GLib.timeout_add_seconds()](09-apis.md#941-temporizadores-con-glibtimeout_add_seconds)
        - [9.4.2. Manejo de fallos de red: reintentos, tiempos de espera y mensajes en el panel](09-apis.md#942-manejo-de-fallos-de-red-reintentos-tiempos-de-espera-y-mensajes-en-el-panel)
        - [9.4.3. Cancelación de peticiones pendientes en disable() para evitar fugas](09-apis.md#943-cancelación-de-peticiones-pendientes-en-disable-para-evitar-fugas)
    - [9.5. Manejo de claves de API sin exponerlas](09-apis.md#95-manejo-de-claves-de-api-sin-exponerlas)
        - [9.5.1. Nunca hardcodear claves en extension.js](09-apis.md#951-nunca-hardcodear-claves-en-extensionjs)
        - [9.5.2. Preferir APIs que no requieran clave para los ejemplos del manual](09-apis.md#952-preferir-apis-que-no-requieran-clave-para-los-ejemplos-del-manual)
        - [9.5.3. GSettings no es almacenamiento seguro: dconf guarda los valores en texto plano](09-apis.md#953-gsettings-no-es-almacenamiento-seguro-dconf-guarda-los-valores-en-texto-plano)
        - [9.5.4. Advertencia sobre la exposición de claves en código público y en repositorios](09-apis.md#954-advertencia-sobre-la-exposición-de-claves-en-código-público-y-en-repositorios)
    - [9.6. Ejemplo completo: indicador de criptomonedas](09-apis.md#96-ejemplo-completo-indicador-de-criptomonedas)
        - [9.6.1. Petición a la API de Binance](09-apis.md#961-petición-a-la-api-de-binance)
        - [9.6.2. Parseo del precio y actualización de un St.Label en el panel](09-apis.md#962-parseo-del-precio-y-actualización-de-un-stlabel-en-el-panel)
        - [9.6.3. Menú desplegable con detalles adicionales](09-apis.md#963-menú-desplegable-con-detalles-adicionales)
- [Ejemplo guiado paso a paso](09-apis.md#ejemplo-guiado-paso-a-paso)
    - [Bloque 1: crear el proveedor](09-apis.md#bloque-1-crear-el-proveedor)
    - [Bloque 2: importar Soup y el proveedor](09-apis.md#bloque-2-importar-soup-y-el-proveedor)
    - [Bloque 3: crear el panel y la etiqueta](09-apis.md#bloque-3-crear-el-panel-y-la-etiqueta)
    - [Bloque 4: actualizar el precio y crear el temporizador](09-apis.md#bloque-4-actualizar-el-precio-y-crear-el-temporizador)
    - [Bloque 5: limpiar en disable()](09-apis.md#bloque-5-limpiar-en-disable)
- [Verifica que funciona](09-apis.md#verifica-que-funciona)
- [Errores de integración del capítulo](09-apis.md#errores-de-integración-del-capítulo)
- [Ejercicios](09-apis.md#ejercicios)
    - [Reproductivo](09-apis.md#reproductivo)
    - [Aplicativo](09-apis.md#aplicativo)
    - [Creativo](09-apis.md#creativo)
- [Autoevaluación](09-apis.md#autoevaluación)
- [Respuestas razonadas](09-apis.md#respuestas-razonadas)
- [Soluciones de los ejercicios](09-apis.md#soluciones-de-los-ejercicios)
    - [Solución del reproductivo](09-apis.md#solución-del-reproductivo)
    - [Solución del aplicativo](09-apis.md#solución-del-aplicativo)
    - [Solución del creativo](09-apis.md#solución-del-creativo)
- [Si quieres saber más](09-apis.md#si-quieres-saber-más)
- [Siguiente paso](09-apis.md#siguiente-paso)

### [Capítulo 10: Empaquetado y Compatibilidad con GNOME 50](10-empaquetado.md#capítulo-10-empaquetado-y-compatibilidad-con-gnome-50)

Metadatos, ZIP, criterios de EGO y pruebas finales en entorno limpio.

- [Qué vas a aprender](10-empaquetado.md#qué-vas-a-aprender)
- [Antes de empezar necesitas](10-empaquetado.md#antes-de-empezar-necesitas)
- [El concepto, con una analogía](10-empaquetado.md#el-concepto-con-una-analogía)
    - [10.1. Verificación de metadatos](10-empaquetado.md#101-verificación-de-metadatos)
        - [10.1.1. "shell-version": ["50"] y versionado](10-empaquetado.md#1011-shell-version-50-y-versionado)
        - [10.1.2. El campo version y su gestión por extensions.gnome.org](10-empaquetado.md#1012-el-campo-version-y-su-gestión-por-extensionsgnomeorg)
        - [10.1.3. Metadatos finales](10-empaquetado.md#1013-metadatos-finales)
    - [10.2. Empaquetado manual en ZIP](10-empaquetado.md#102-empaquetado-manual-en-zip)
        - [10.2.1. Contenido en la raíz del ZIP](10-empaquetado.md#1021-contenido-en-la-raíz-del-zip)
        - [10.2.2. Exclusión de archivos innecesarios](10-empaquetado.md#1022-exclusión-de-archivos-innecesarios)
        - [10.2.3. Crear el ZIP con gnome-extensions pack](10-empaquetado.md#1023-crear-el-zip-con-gnome-extensions-pack)
        - [10.2.4. Revisar el contenido del ZIP](10-empaquetado.md#1024-revisar-el-contenido-del-zip)
    - [10.3. Criterios para la revisión de EGO](10-empaquetado.md#103-criterios-para-la-revisión-de-ego)
        - [10.3.1. Código legible, sin ofuscación](10-empaquetado.md#1031-código-legible-sin-ofuscación)
        - [10.3.2. disable() que limpia todos los recursos](10-empaquetado.md#1032-disable-que-limpia-todos-los-recursos)
        - [10.3.3. Restauración obligatoria de monkey patches](10-empaquetado.md#1033-restauración-obligatoria-de-monkey-patches)
        - [10.3.4. Otros criterios prácticos](10-empaquetado.md#1034-otros-criterios-prácticos)
    - [10.4. Pruebas finales en entorno limpio](10-empaquetado.md#104-pruebas-finales-en-entorno-limpio)
        - [10.4.1. Activación/desactivación repetida sin errores en logs](10-empaquetado.md#1041-activacióndesactivación-repetida-sin-errores-en-logs)
        - [10.4.2. Persistencia de preferencias tras logout/login](10-empaquetado.md#1042-persistencia-de-preferencias-tras-logoutlogin)
        - [10.4.3. Verificación en Wayland](10-empaquetado.md#1043-verificación-en-wayland)
    - [10.5. Lista de publicación](10-empaquetado.md#105-lista-de-publicación)
- [Ejemplo guiado paso a paso](10-empaquetado.md#ejemplo-guiado-paso-a-paso)
    - [Bloque 1: revisar el metadata](10-empaquetado.md#bloque-1-revisar-el-metadata)
    - [Bloque 2: crear el ZIP](10-empaquetado.md#bloque-2-crear-el-zip)
    - [Bloque 3: revisar el contenido](10-empaquetado.md#bloque-3-revisar-el-contenido)
    - [Bloque 4: instalar y probar](10-empaquetado.md#bloque-4-instalar-y-probar)
- [Verifica que funciona](10-empaquetado.md#verifica-que-funciona)
- [Errores de integración del capítulo](10-empaquetado.md#errores-de-integración-del-capítulo)
- [Ejercicios](10-empaquetado.md#ejercicios)
    - [Reproductivo](10-empaquetado.md#reproductivo)
    - [Aplicativo](10-empaquetado.md#aplicativo)
    - [Creativo](10-empaquetado.md#creativo)
- [Autoevaluación](10-empaquetado.md#autoevaluación)
- [Respuestas razonadas](10-empaquetado.md#respuestas-razonadas)
- [Soluciones de los ejercicios](10-empaquetado.md#soluciones-de-los-ejercicios)
    - [Solución del reproductivo](10-empaquetado.md#solución-del-reproductivo)
    - [Solución del aplicativo](10-empaquetado.md#solución-del-aplicativo)
    - [Solución del creativo](10-empaquetado.md#solución-del-creativo)
- [Cierre del manual](10-empaquetado.md#cierre-del-manual)
- [Si quieres saber más](10-empaquetado.md#si-quieres-saber-más)

## Apéndices

### [Glosario](apendices/GLOSARIO.md#glosario)

Términos del manual y el capítulo donde aparecen por primera vez.

- [Términos relacionados](apendices/GLOSARIO.md#términos-relacionados)

### [Mapa de Prerrequisitos](apendices/MAPA-PREREQUISITOS.md#mapa-de-prerrequisitos)

Qué necesitas saber antes de cada capítulo y rutas de recuperación.

- [Ruta mínima sin C](apendices/MAPA-PREREQUISITOS.md#ruta-mínima-sin-c)
- [Ruta de recuperación](apendices/MAPA-PREREQUISITOS.md#ruta-de-recuperación)
- [Dependencias del proyecto](apendices/MAPA-PREREQUISITOS.md#dependencias-del-proyecto)

### [Índice de Soluciones](apendices/SOLUCIONES.md#índice-de-soluciones)

Acceso rápido a las soluciones y respuestas de cada capítulo.

- [Cómo usar las soluciones](apendices/SOLUCIONES.md#cómo-usar-las-soluciones)

## Etapas del proyecto

El proyecto **MiPrimeraExtensión** crece capítulo a capítulo. Todo el código de cada etapa está dentro del capítulo correspondiente.

| Etapa | Capítulo | Qué se construye |
| :---: | :--- | :--- |
| 0 | [Introductorio](00-introductorio.md#capítulo-introductorio-antes-de-empezar) | Preparar el entorno y crear los primeros archivos. |
| 1 | [1](01-arquitectura.md) | Extensión vacía que escribe en el log. |
| 2 | [2](02-modulos.md) | Notificación con `Main.notify()`. |
| 3 | [3](03-interfaz.md) | Botón con icono en el panel. |
| 4 | [4](04-css.md) | CSS propio para cambiar el color del botón. |
| 5 | [5](05-persistencia.md) | Preferencias persistentes con GSettings. |
| 6 | [6](06-sistema.md) | Acción que mueve la ventana activa a otro espacio. |
| 7 | [7](07-monkey-patching.md) | Monkey patch reversible y depuración. |
| 8 | [8](08-c.md#capítulo-8-intervención-desde-cc-mediante-gobject-introspection) | Librería C opcional (capítulo conceptual). |
| 9 | [9](09-apis.md) | Precio de criptomoneda en el panel mediante una API. |
| 10 | [10](10-empaquetado.md) | ZIP instalable y listo para publicar. |

---

## Documentos de producción

- [Índice editorial detallado](../MANUAL.md)
- [Presentación del repositorio](../README.md)
- [Reglas de producción](../RULES.md)
- [Especificación pedagógica](../AGENT.md)
- [Perfil del lector](../PERFIL-LECTOR.md)
- [Decisiones del proyecto](../DECISIONES.md)
- [Entorno de pruebas](../ENTORNO-PRUEBAS.md)
- [Plantilla de capítulo](../PLANTILLA-CAPITULO.md)
- [Checklist de calidad](../CHECKLIST-CALIDAD.md)
