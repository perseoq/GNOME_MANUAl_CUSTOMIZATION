# Glosario

Este glosario reúne los términos que aparecen en el manual. La columna “Primera aparición” indica el capítulo donde se introduce el término.

| Término | Definición | Primera aparición |
| :--- | :--- | :--- |
| **API** | Conjunto de funciones y objetos que un programa ofrece a otros programas. | Capítulo 2 |
| **Actor** | Elemento visual de Clutter. Los widgets de St también son actores. | Capítulo 1 |
| **API externa** | Servicio de Internet al que una extensión envía peticiones y del que recibe datos. | Capítulo 9 |
| **Clutter** | Biblioteca de actores, animaciones y entrada del shell. | Capítulo 3 |
| **dconf** | Sistema que almacena los valores de GSettings. | Capítulo 5 |
| **EGO** | extensions.gnome.org, la tienda oficial de extensiones de GNOME. | Capítulo 10 |
| **ESM** | ECMAScript Modules, el sistema de módulos con `import` y `export`. | Capítulo 1 |
| **Extensión** | Programa en JavaScript que se carga dentro de GNOME Shell y añade o modifica una función. | Introductorio |
| **`extension.js`** | Archivo principal de la extensión. Exporta la clase que hereda de `Extension`. | Capítulo 1 |
| **GIR** | Descripción XML de una biblioteca GObject Introspection. | Capítulo 8 |
| **GJS** | GNOME JavaScript, el motor que ejecuta JavaScript dentro de GNOME. | Capítulo 1 |
| **`gi://`** | Espacio de nombres para bibliotecas del sistema expuestas por GObject Introspection. | Capítulo 1 |
| **GLib** | Biblioteca de utilidades, temporizadores y tipos básicos. | Capítulo 2 |
| **GNOME Shell** | Capa visual de GNOME: panel, actividades, reloj, notificaciones y escritorio. | Introductorio |
| **GObject** | Sistema de objetos de GNOME. Es la base de muchas bibliotecas GI. | Capítulo 8 |
| **GObject Introspection** | Sistema que describe bibliotecas C basadas en GObject para que GJS pueda usarlas. | Capítulo 8 |
| **GSettings** | Sistema de preferencias persistentes de GNOME. | Capítulo 5 |
| **HTTP** | Protocolo de comunicación usado por las APIs web. | Capítulo 9 |
| **JSON** | Formato de texto para guardar datos estructurados. | Capítulo 1 |
| **Log** | Registro de mensajes, avisos y errores del sistema. | Introductorio |
| **Looking Glass** | Consola de depuración de GNOME Shell. Se abre con `Alt+F2` y `lg`. | Capítulo 2 |
| **Meta** | Biblioteca de ventanas y espacios de trabajo de Mutter. | Capítulo 6 |
| **Monkey patch** | Reemplazo de una función o método en tiempo de ejecución. | Capítulo 7 |
| **Mutter** | Compositor y gestor de ventanas de GNOME. | Capítulo 6 |
| **`metadata.json`** | Archivo que identifica la extensión: UUID, nombre, descripción y versiones compatibles. | Capítulo 1 |
| **Panel** | Barra superior de GNOME Shell. | Introductorio |
| **`prefs.js`** | Archivo de la ventana de preferencias. Se ejecuta en un proceso GTK4/Adw. | Capítulo 5 |
| **Proceso** | Programa en ejecución. GNOME Shell es un proceso compartido por sus extensiones. | Capítulo 1 |
| **Proveedor** | Módulo que encapsula la lógica de una API externa. | Capítulo 9 |
| **`resource://`** | Espacio de nombres para módulos JavaScript internos de GNOME Shell. | Capítulo 1 |
| **Señal** | Aviso que emite un objeto cuando ocurre algo. | Capítulo 1 |
| **Soup 3** | Biblioteca HTTP nativa de GNOME. | Capítulo 9 |
| **St** | Shell Toolkit: widgets y estilos del shell. | Capítulo 3 |
| **`stylesheet.css`** | Hoja de estilos de la extensión para los actores de St. | Capítulo 4 |
| **Typelib** | Archivo binario generado a partir de un GIR que GJS puede importar. | Capítulo 8 |
| **UUID** | Identificador único de la extensión, con formato `nombre@dominio`. | Capítulo 1 |
| **Wayland** | Sistema de ventanas y entrada usado por GNOME 50. | Introductorio |
| **X11** | Sistema de ventanas anterior a Wayland. El manual no lo usa. | Capítulo 6 |
| **ZIP** | Archivo comprimido que contiene la extensión empaquetada. | Capítulo 10 |

## Términos relacionados

- **Widget:** un elemento de St como un botón, una etiqueta o una caja. Todos son actores.
- **Contenedor:** un widget que organiza otros widgets, como `St.BoxLayout`.
- **Selector:** la parte de una regla CSS que indica a qué elemento se aplica.
- **Clase CSS:** nombre que se asigna a un widget para aplicarle un estilo.
- **Clave GSettings:** nombre de un valor dentro de un esquema.
- **Esquema GSettings:** definición de las claves, tipos y valores por defecto.
- **Sesión:** entorno de escritorio iniciado por un usuario.
- **Petición asíncrona:** operación que no bloquea el programa mientras espera una respuesta.
