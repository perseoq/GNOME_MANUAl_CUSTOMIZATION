# Capítulo Introductorio: Antes de Empezar

> Este capítulo no enseña a programar todavía. Prepara los conceptos y las herramientas que necesitas para el Capítulo 1.

## Qué vas a aprender

- Explicar qué es GNOME Shell y qué es una extensión.
- Reconocer qué es un archivo, una carpeta y una ruta.
- Abrir un editor de texto y guardar un archivo en una carpeta concreta.
- Abrir una terminal y usar `pwd`, `ls` y `cd`.
- Distinguir JavaScript, que decide qué hace un programa, de CSS, que decide cómo se ve.
- Saber cómo está organizado este manual y cómo recorrerlo.

Al final de este capítulo, podrás crear un archivo de texto, guardarlo en una ruta concreta y explicar con tus palabras qué es una extensión de GNOME Shell.

## Antes de empezar necesitas

- Un equipo con Linux y GNOME Shell 50.6.
- Una sesión de GNOME en Wayland. Wayland es el sistema que gestiona las ventanas y la entrada; en GNOME 50 es la opción predeterminada.
- Una cuenta de usuario normal, con carpeta personal.
- Un editor de texto instalado. GNOME Text Editor suele estar instalado; si no lo está, este capítulo explica cómo usar otro editor.
- Una terminal instalada. GNOME incluye una terminal llamada Terminal.
- Tiempo para probar cada paso. No necesitas conocimientos previos de programación.

Los nombres de menús y carpetas pueden aparecer en otro idioma. Busca el equivalente en tu sistema; por ejemplo, `Documents` en lugar de `Documentos`.

No necesitas instalar extensiones de terceros. Todo lo que aprenderás se escribe desde cero.

## El concepto, con una analogía

Preparar un manual es como preparar una cocina antes de cocinar. Antes de encender el fuego, necesitas conocer los utensilios, saber dónde están los ingredientes y entender para qué sirve cada aparato. Todavía no cocinas, pero sin esta preparación los pasos siguientes fallan.

Este capítulo cumple esa función: prepara las piezas que el resto del manual da por supuestas.

### 0.1. Qué es GNOME Shell y qué es una extensión

Imagina que GNOME Shell es el sistema eléctrico de una casa. Las paredes, los cables y los enchufes ya están instalados. Tú no necesitas reconstruir la casa para usar un aparato. Una extensión es como un aparato que se enchufa en un enchufe: añade una función nueva sin reemplazar la instalación completa.

La comparación tiene límites: una extensión no es un aparato físico y no se conecta a un enchufe. Pero sirve para entender la idea principal: la extensión aprovecha una estructura que ya existe y le añade algo.

GNOME Shell es la capa visual de GNOME. Dibuja el panel superior, el botón de Actividades, el reloj, las notificaciones, el selector de ventanas y el escritorio. También decide qué ocurre cuando abres una aplicación o cambias de ventana.

Una extensión de GNOME Shell es un programa escrito en JavaScript que se carga dentro de GNOME Shell y añade o modifica su comportamiento. Puede crear un botón en el panel, mostrar información, cambiar un atajo o modificar una parte del escritorio.

Una extensión no es:

- Una aplicación independiente que aparece en el menú de aplicaciones.
- Un tema visual completo.
- Una extensión del navegador.
- Un programa independiente que se ejecuta aparte del shell.

Una extensión se ejecuta dentro del mismo programa que dibuja el escritorio. Por eso puede cambiar el shell, y por eso también puede romperlo si está mal escrita. Este manual enseña a crearla, recargarla y desactivarla con cuidado.

Mira tu escritorio e identifica estas partes:

```text
┌──────────────────────────────────────────────┐
│ Actividades     Reloj     Indicadores   Menú │  ← panel superior
├──────────────────────────────────────────────┤
│                                              │
│                  Escritorio                  │
│                                              │
└──────────────────────────────────────────────┘
```

Este bloque es un diagrama, no un programa. No se ejecuta y no produce errores. La apariencia exacta depende del tema y de la versión de GNOME.

**Comprueba que lo entiendes:** señala el panel, el botón de Actividades y el reloj. Después explica con tus palabras qué añade una extensión. Si tu explicación menciona “un programa que se enchufa al shell para añadir una función”, vas por buen camino.

### 0.2. Qué es un archivo, una carpeta y una ruta

Imagina un archivador. El archivador es una carpeta grande; dentro hay cajones; dentro de un cajón hay una carpeta de cartón; dentro de esa carpeta hay un papel. La ruta es la dirección completa que indica cómo llegar desde la entrada del archivador hasta ese papel.

En una computadora:

- Un **archivo** es un conjunto de datos con un nombre. Puede ser un texto, una imagen, un programa o una configuración.
- Una **carpeta** es un contenedor que agrupa archivos y otras carpetas. También se llama **directorio**.
- Una **ruta** es la dirección de un archivo o de una carpeta.

Una ruta absoluta empieza en la raíz del sistema, que se escribe `/`. Una ruta relativa empieza desde la carpeta en la que estás en ese momento. La carpeta personal se representa con `~`; para tu usuario, `~` equivale a algo como `/home/ana`.

Ejemplo de ruta absoluta:

```text
/home/ana/Documentos/curso-gnome/hola.txt
```

Este bloque es un ejemplo de ruta, no un programa. No se ejecuta y no produce errores.

Partes de la ruta:

| Parte | Significado |
| :--- | :--- |
| `/` | Raíz del sistema de archivos |
| `home` | Carpeta que contiene las carpetas personales |
| `ana` | Nombre de la cuenta de usuario |
| `Documentos` | Carpeta personal para documentos |
| `curso-gnome` | Carpeta creada para este manual |
| `hola.txt` | Archivo de texto |

La misma ruta se puede escribir de forma relativa si estás dentro de `/home/ana`:

```text
Documentos/curso-gnome/hola.txt
```

Los nombres de archivos y carpetas distinguen mayúsculas de minúsculas. `Hola.txt` y `hola.txt` son dos archivos distintos. Los espacios también forman parte del nombre; por eso una carpeta llamada `curso gnome` necesita un tratamiento especial en la terminal, que verás en la sección 0.4.

Si tu sistema usa nombres de carpeta en inglés, la carpeta puede llamarse `Documents` en lugar de `Documentos`. Usa el nombre que veas en tu gestor de archivos; el manual usa `Documentos` como ejemplo.

**Comprueba que lo entiendes:** abre **Actividades**, escribe `Files` o `Archivos` y pulsa Enter. En la barra lateral, haz clic en `Documentos` y explica en voz alta qué carpeta contiene a cuál. Si tu sistema usa `Documents`, usa ese nombre.

### 0.3. Qué es un editor de texto y cuál usar

Un editor de texto es como un cuaderno en blanco: escribes caracteres y guardas el resultado en un archivo. No es lo mismo que un procesador de textos como LibreOffice Writer. El procesador añade formato, tablas, negritas y estilos ocultos. Para escribir código necesitas texto plano, sin formato oculto.

Recomendación: usa **GNOME Text Editor** si está instalado. Si no aparece, puedes usar **VS Code** o el editor que prefieras, siempre que guarde texto plano.

La **extensión** de un archivo es la parte final de su nombre que indica el tipo de archivo. En `hola.txt`, la extensión es `.txt`, que significa “texto”.

Pasos para crear tu primer archivo:

1. Abre **Actividades** y escribe `Text Editor` o `Editor de texto`.
2. Abre la aplicación.
3. Crea un archivo nuevo con `Ctrl+N`.
4. Escribe esta línea:

```text
Hola, este es mi primer archivo.
```

Este bloque es el contenido de un archivo de texto. No es un programa y no se ejecuta.

5. Pulsa `Ctrl+S` para guardar.
6. En la barra lateral de la ventana de guardado, haz clic en **Documentos**.
7. Crea una carpeta llamada `curso-gnome`: pulsa el botón de menú (los tres puntos o el icono de menú), elige **Nueva carpeta**, escribe el nombre y confirma.
8. Entra en `curso-gnome`, escribe `hola.txt` en el campo **Nombre** y pulsa **Guardar**.
9. Cierra el editor y vuelve a abrir el archivo desde el gestor de archivos. Debes ver el mismo texto.

Si el editor te propone guardar con otro formato, elige **texto plano**. Si te propone una extensión automática, comprueba que el nombre final sea `hola.txt` y no `hola.txt.txt`.

**Errores posibles en este bloque:** este bloque no ejecuta comandos, así que no produce un log de terminal. Un **log** (registro) es un registro de mensajes que el sistema guarda y que puedes leer como texto. Los errores de este bloque se ven en el gestor de archivos.

**Error 1: el archivo se guardó como `hola.txt.txt`.**
- **Síntoma:** en el gestor de archivos aparece `hola.txt.txt` y el editor muestra el nombre completo.
- **Causa:** el editor añadió la extensión `.txt` porque ya estaba seleccionado el formato de texto.
- **Corrección:** renombra el archivo a `hola.txt` y comprueba el nombre antes de guardar. Después de corregir, el gestor de archivos muestra `hola.txt`.

**Error 2: el archivo se guardó en otra carpeta.**
- **Síntoma:** el archivo no aparece dentro de `Documentos/curso-gnome`.
- **Causa:** la ventana de guardado conservó la última carpeta utilizada.
- **Corrección:** vuelve a abrir el archivo, usa **Guardar como** y elige la carpeta correcta. Después de corregir, el archivo aparece dentro de `Documentos/curso-gnome`.

**Error 3: el archivo se guardó como documento de LibreOffice.**
- **Síntoma:** el archivo termina en `.odt` y el editor muestra formato enriquecido.
- **Causa:** se abrió LibreOffice Writer en lugar de un editor de texto plano.
- **Corrección:** abre GNOME Text Editor, copia el texto y guarda con extensión `.txt`. Después de corregir, el archivo termina en `.txt` y se abre como texto plano.

**Comprueba que lo entiendes:** cierra el editor, abre el archivo desde el gestor de archivos y verifica que el texto sigue ahí. Después explica la diferencia entre un editor de texto y un procesador de textos.

### 0.4. Qué es una terminal y cómo abrirla

La terminal es como un intercomunicador directo con el sistema. En lugar de hacer clic en botones, escribes una orden y el sistema responde con texto. No es magia ni una consola reservada a expertos: es otra forma de pedir las mismas cosas que hace el gestor de archivos.

Abre la terminal de una de estas dos formas:

- Pulsa `Ctrl+Alt+T`.
- Abre **Actividades**, escribe `Terminal` y pulsa Enter.

Al abrirse, verás una línea parecida a esta:

```text
ana@equipo:~$
```

Este bloque es un ejemplo de indicador de terminal, no un comando. No se ejecuta.

La línea se llama **indicador** o **prompt**. `ana` es el usuario, `equipo` es el nombre del equipo, `~` es la carpeta personal y `$` indica que la terminal está lista para recibir una orden. El `$` no se escribe: es una señal de la terminal.

Un **argumento** es cada palabra o dato que recibe un comando. Por ejemplo, en `cd practica`, `cd` es el comando y `practica` es el argumento.

Las órdenes distinguen mayúsculas de minúsculas. Escribe los comandos exactamente como aparecen, sin el `$`.

Las rutas y los nombres de las salidas de esta sección son ejemplos. En tu equipo cambiará la parte inicial de la ruta y pueden aparecer otros archivos.

Antes de continuar, crea dentro de `Documentos/curso-gnome` dos carpetas: `practica` y `carpeta con espacios`. Puedes crearlas con el gestor de archivos. Así las órdenes de esta sección funcionarán igual que en el ejemplo.

Abre la terminal. Si no estás dentro de `Documentos/curso-gnome`, entra con `cd ~/Documentos/curso-gnome`. `cd` significa “cambiar de directorio”; lo explicaremos en detalle en el Bloque 3.

#### Bloque 1: saber dónde estás con `pwd`

`pwd` significa “print working directory”, que en español se puede traducir como “muestra el directorio de trabajo”. Te dice en qué carpeta estás.

```bash
pwd
```

Salida de ejemplo:

```text
/home/ana/Documentos/curso-gnome
```

> **Qué hace este bloque:** ejecuta `pwd`, que muestra la ruta absoluta de la carpeta actual. La ruta concreta cambia en cada equipo. Lo importante es que sea una ruta completa y que coincida con la carpeta en la que crees que estás.

**Errores posibles en este bloque:**

**Error 1: `bash: pdw: instrucción no encontrada...`**
- **Ubicación:** línea de la terminal donde escribiste el comando.
- **Síntoma:** la terminal responde `bash: pdw: instrucción no encontrada...`.
- **Causa:** escribiste `pdw` en lugar de `pwd`; el orden de las letras importa.
- **Corrección:** escribe `pwd` y pulsa Enter. Después de corregir, la terminal muestra una ruta como `/home/ana/Documentos/curso-gnome`.

#### Bloque 2: ver qué hay con `ls`

`ls` significa “list”. Muestra los archivos y carpetas que están dentro de la carpeta actual.

```bash
ls
```

Salida de ejemplo:

```text
carpeta con espacios
hola.txt
notas.txt
practica
```

> **Qué hace este bloque:** ejecuta `ls`, que enumera los nombres de los archivos y carpetas del directorio actual. No muestra el contenido de los archivos; muestra sus nombres. El orden puede variar.

**Errores posibles en este bloque:**

**Error 1: `ls: no se puede acceder a '/ruta/que-no-existe': No existe el fichero o el directorio`**
- **Ubicación:** línea de la terminal.
- **Síntoma:** la terminal muestra `ls: no se puede acceder a '/ruta/que-no-existe': No existe el fichero o el directorio`.
- **Causa:** la ruta que escribiste no existe o está mal escrita.
- **Corrección:** ejecuta `pwd` para comprobar dónde estás y usa `ls` sin ruta para ver la carpeta actual. Después de corregir, `ls` muestra nombres como `hola.txt`, `practica` y `carpeta con espacios`.

#### Bloque 3: entrar en una carpeta con `cd`

`cd` significa “change directory”, o “cambiar de directorio”. Sirve para moverte a otra carpeta.

```bash
cd practica
pwd
```

Salida de ejemplo:

```text
/home/ana/Documentos/curso-gnome/practica
```

> **Qué hace este bloque:** `cd practica` entra en la carpeta `practica`, que está dentro de la carpeta actual. `pwd` confirma la nueva ubicación. La ruta `practica` es relativa porque no empieza por `/`.

**Errores posibles en este bloque:**

**Error 1: `bash: cd: /ruta/que-no-existe: No existe el fichero o el directorio`**
- **Ubicación:** línea de la terminal.
- **Síntoma:** la terminal muestra `bash: cd: /ruta/que-no-existe: No existe el fichero o el directorio`.
- **Causa:** la ruta no existe o tiene un error de escritura.
- **Corrección:** usa `pwd` y `ls` para comprobar la ruta; después escribe el nombre exacto de la carpeta. Después de corregir, `pwd` muestra una ruta como `/home/ana/Documentos/curso-gnome/practica`.

**Error 2: `bash: cd: hola.txt: No es un directorio`**
- **Ubicación:** línea de la terminal.
- **Síntoma:** la terminal muestra `bash: cd: hola.txt: No es un directorio`.
- **Causa:** intentaste entrar en un archivo. `cd` únicamente funciona con carpetas.
- **Corrección:** comprueba con `ls` si el nombre es una carpeta o un archivo; entra únicamente en una carpeta. Después de corregir, `ls` muestra `hola.txt` como archivo y `cd` se usa únicamente con carpetas.

#### Bloque 4: volver a la carpeta anterior con `cd ..`

`..` significa “carpeta superior” o “carpeta madre”. `cd ..` sube un nivel.

```bash
cd ..
pwd
```

Salida de ejemplo:

```text
/home/ana/Documentos/curso-gnome
```

> **Qué hace este bloque:** `cd ..` sube desde `practica` hasta la carpeta que la contiene. `pwd` confirma que volviste. Si escribes `cd` sin argumentos, vuelves a tu carpeta personal.

**Errores posibles en este bloque:** este bloque no produce un error aislado si `..` existe, y siempre existe en una carpeta normal. Si `cd ..` falla, el problema está en la carpeta actual o en el permiso de acceso, no en la orden.

#### Bloque 5: entrar en una carpeta con espacios

Los espacios separan argumentos en la terminal. Si una carpeta se llama `carpeta con espacios`, la terminal puede interpretar que le estás dando tres nombres distintos. Para evitarlo, escribe el nombre entre comillas simples.

```bash
cd 'carpeta con espacios'
pwd
```

Salida de ejemplo:

```text
/home/ana/Documentos/curso-gnome/carpeta con espacios
```

> **Qué hace este bloque:** las comillas simples le dicen a la terminal que `carpeta con espacios` es un único nombre. `pwd` confirma la entrada. Las comillas no forman parte del nombre de la carpeta; únicamente se usan para agruparlo.

**Errores posibles en este bloque:**

**Error 1: `bash: cd: demasiados argumentos`**
- **Ubicación:** línea de la terminal.
- **Síntoma:** la terminal muestra `bash: cd: demasiados argumentos`.
- **Causa:** escribiste `cd carpeta con espacios` sin comillas; la terminal vio tres argumentos.
- **Corrección:** escribe `cd 'carpeta con espacios'` con comillas simples alrededor del nombre completo. Después de corregir, `pwd` muestra una ruta como `/home/ana/Documentos/curso-gnome/carpeta con espacios`.

**Error 2: `bash: cd: carpeta con espacios: No existe el fichero o el directorio`**
- **Ubicación:** línea de la terminal.
- **Síntoma:** la terminal muestra que la carpeta no existe.
- **Causa:** el nombre está mal escrito, la carpeta está en otra ubicación o las comillas no coinciden.
- **Corrección:** ejecuta `ls` para ver el nombre exacto y comprueba que las comillas de apertura y cierre sean iguales. Después de corregir, `pwd` muestra una ruta como `/home/ana/Documentos/curso-gnome/carpeta con espacios`.

### 0.5. Qué es JavaScript y qué es CSS

JavaScript es el cerebro que decide qué hacer. CSS es la ropa que decide cómo se ve. Esta comparación es útil, pero tiene un límite: un cerebro y una ropa no son programas. En una extensión, JavaScript contiene las instrucciones y CSS contiene las reglas visuales.

**JavaScript** es un lenguaje de programación. En GNOME Shell se ejecuta con **GJS** (GNOME JavaScript), el motor que conecta JavaScript con las bibliotecas del sistema. Con JavaScript puedes reaccionar a un clic, leer un dato, cambiar un texto o decidir qué ocurre cuando se abre una ventana.

**CSS** es un lenguaje de estilos. Describe colores, tamaños, márgenes, bordes y tipografía. En GNOME Shell, la biblioteca **St** dibuja los elementos visuales y entiende una parte de CSS. Una **biblioteca** es un conjunto de código reutilizable que ofrece funciones ya escritas. St no es un navegador web: algunas propiedades de CSS funcionan y otras no. El Capítulo 4 explica cuáles.

Una **variable** es un nombre que guarda un valor. Una **constante** es una variable que no se reasigna después de crearse.

Ejemplo visual de JavaScript:

```javascript
// JavaScript: guarda un texto en una variable.
const saludo = 'Hola, mundo';
```

> **Qué hace este bloque:** declara una constante llamada `saludo`. `const` indica que la variable no se reasignará. `saludo` es el nombre de la variable. `=` asigna el valor. `'Hola, mundo'` es una cadena de texto entre comillas simples. El punto y coma cierra la instrucción. Este fragmento guarda un texto, pero no lo muestra en pantalla por sí mismo.

**Errores posibles en este bloque:** este bloque es una ilustración y no se ejecuta en este capítulo, así que no produce errores por sí mismo. Si en el Capítulo 1 olvidas cerrar la comilla, verás un error de sintaxis en el log; un **error de sintaxis** es un aviso de que el texto no cumple las reglas del lenguaje. Allí aprenderás a leerlo.

Ejemplo visual de CSS:

```css
/* CSS: pinta de azul el texto del elemento con clase saludo. */
.saludo {
    color: blue;
}
```

> **Qué hace este bloque:** `.saludo` es el selector. Un **selector** es la parte que indica a qué elemento se aplica la regla. El punto indica que busca un elemento con la clase `saludo`; una **clase** es un nombre que se asigna a uno o varios elementos para aplicarles el mismo estilo. Las llaves agrupan las reglas. `color` es la propiedad, es decir, lo que se cambia. `blue` es el valor; en español, azul. El comentario explica la intención y no se aplica al estilo. Este bloque es una regla CSS completa, pero necesita un elemento con la clase `saludo` para verse.

**Errores posibles en este bloque:** este bloque no se ejecuta en este capítulo y no produce errores por sí mismo. En el Capítulo 4 aprenderás a cargar CSS en una extensión y a comprobar si una propiedad es válida en St.

**Comprueba que lo entiendes:** señala en el ejemplo de JavaScript qué parte guarda el texto y en el ejemplo de CSS qué parte decide el color. Si puedes explicar que JavaScript decide la lógica y CSS decide la apariencia, has entendido la diferencia.

### 0.6. Cómo usar este manual

Cada capítulo de este manual sigue una estructura fija. Conocerla te ayuda a saber qué hacer en cada momento.

| Sección | Para qué sirve |
| :--- | :--- |
| Qué vas a aprender | Resume el resultado visible del capítulo |
| Antes de empezar necesitas | Indica lo que debes tener del capítulo anterior |
| El concepto, con una analogía | Explica la idea sin código |
| Ejemplo guiado paso a paso | Muestra el código en bloques pequeños y explica cada bloque |
| Verifica que funciona | Comprueba que el resultado es el esperado |
| Errores posibles en este bloque | Explica qué puede fallar en cada bloque y cómo corregirlo |
| Errores de integración | Explica fallos al combinar bloques o al ejecutar en el entorno |
| Ejercicios | Propone práctica reproductiva, aplicativa y creativa |
| Autoevaluación | Comprueba que entendiste antes de avanzar |
| Respuestas razonadas | Explica por qué las respuestas son correctas |
| Soluciones de los ejercicios | Permite comparar tu resultado |
| Si quieres saber más | Ofrece material opcional, nunca obligatorio |

La regla de oro es esta: si no entiendes un paso, no avances. Vuelve al párrafo anterior, prueba otra vez y anota qué ocurrió. El manual está diseñado para que cada paso dependa únicamente del anterior.

Escribe el código con tus manos. Copiar y pegar puede parecer más rápido, pero escribir obliga a fijarse en cada símbolo. Al principio, los errores de escritura son normales; forman parte del aprendizaje.

**Comprueba que lo entiendes:** nombra tres secciones del manual y explica para qué sirve cada una. Después di qué harás si un paso no funciona.

## Verifica que funciona

Antes de continuar, comprueba estos puntos:

- [ ] Sé señalar el panel, el botón de Actividades y el reloj en mi escritorio.
- [ ] Puedo explicar qué es GNOME Shell y qué añade una extensión.
- [ ] Distingo un archivo de una carpeta y sé qué es una ruta.
- [ ] Creé `hola.txt` y lo guardé dentro de `Documentos/curso-gnome`.
- [ ] Abrí la terminal y ejecuté `pwd`, `ls` y `cd`.
- [ ] Sé volver a la carpeta anterior con `cd ..`.
- [ ] Distingo JavaScript de CSS.
- [ ] Sé que debo escribir el código y no únicamente copiarlo.

Si algún punto no está marcado, vuelve a la sección correspondiente antes de continuar.

## Errores de integración del capítulo

Estos errores aparecen cuando se combinan varios pasos del capítulo.

### Error 1: el archivo no aparece donde lo buscas

**Síntoma:**

```text
ls: no se puede acceder a 'hola.txt': No existe el fichero o el directorio
```

**Causa:** estás en una carpeta distinta de la que contiene `hola.txt`, o el archivo se guardó en otra ubicación.

**Corrección:** ejecuta `pwd` para ver dónde estás, `ls` para ver qué hay y `cd` para entrar en `Documentos/curso-gnome`. Si el archivo no está allí, búscalo en el gestor de archivos. Después de corregir, `ls` muestra `hola.txt` dentro de la carpeta correcta.

### Error 2: la carpeta con espacios no se abre

**Síntoma:**

```text
bash: cd: demasiados argumentos
```

**Causa:** escribiste el nombre sin comillas y la terminal interpretó cada palabra como un argumento.

**Corrección:** escribe `cd 'carpeta con espacios'` con comillas simples. Después de corregir, `pwd` muestra la ruta de la carpeta con espacios.

### Error 3: escribes el símbolo del indicador

**Síntoma:**

```text
bash: $: instrucción no encontrada...
```

**Causa:** copiaste el `$` del indicador. El `$` es una señal de la terminal, no parte del comando.

**Corrección:** escribe únicamente el comando, sin `$`. Después de corregir, la orden se ejecuta y la terminal responde con su resultado.

### Error 4: intentas entrar en un archivo

**Síntoma:**

```text
bash: cd: hola.txt: No es un directorio
```

**Causa:** `cd` sirve para entrar en carpetas, no para abrir archivos.

**Corrección:** usa `ls` para distinguir carpetas de archivos. Para ver el contenido de un archivo de texto, ábrelo con el editor. Después de corregir, el editor muestra el contenido de `hola.txt`.

### Error 5: el archivo tiene una extensión doble

**Síntoma:** en el gestor de archivos aparece `hola.txt.txt`.

**Causa:** el editor añadió `.txt` a un nombre que ya incluía la extensión.

**Corrección:** renombra el archivo a `hola.txt` y comprueba el nombre completo antes de guardar. Después de corregir, el gestor de archivos muestra `hola.txt`.

## Ejercicios

### Reproductivo

1. Abre la terminal y ejecuta `pwd`. Copia la ruta que aparece.
2. Ejecuta `ls`. Escribe tres nombres que aparezcan en la salida.
3. Entra en una carpeta con `cd`, comprueba con `pwd` y vuelve con `cd ..`.
4. Crea `hola.txt` con el texto `Hola, este es mi primer archivo.` y guárdalo en `Documentos/curso-gnome`.
5. Escribe una frase que explique qué es una extensión de GNOME Shell.

### Aplicativo

1. Cambia el contenido de `hola.txt` por `Estoy aprendiendo GNOME Shell.` y guarda.
2. Cierra el editor, vuelve a abrirlo y comprueba que el cambio persiste.
3. Crea una carpeta llamada `practica-gnome` dentro de `curso-gnome`.
4. Mueve `hola.txt` a `practica-gnome` con el gestor de archivos.
5. Explica cuál es la nueva ruta absoluta del archivo.

### Creativo

1. Crea esta estructura dentro de tu carpeta personal:

```text
curso-gnome/
└── practicas/
    └── hola.txt
```

2. Abre la terminal y llega hasta `practicas` usando `cd`.
3. Comprueba la ruta con `pwd`.
4. Crea una carpeta llamada `carpeta con espacios` y entra en ella usando comillas.
5. Explica por qué las comillas son necesarias.

**Rúbrica del ejercicio creativo:**

- La estructura de carpetas existe y se puede navegar.
- El archivo `hola.txt` está dentro de `practicas`.
- La terminal muestra una ruta coherente con `pwd`.
- La carpeta con espacios se abre con comillas.
- La explicación menciona que la terminal separa argumentos por espacios.

## Autoevaluación

1. ¿Qué es GNOME Shell y qué añade una extensión?
2. Guardaste `hola.txt`, pero `ls` no lo muestra. ¿Qué tres cosas revisarías y en qué orden?
3. Si quieres cambiar el color de un botón, ¿usarías JavaScript o CSS? ¿Por qué?
4. La terminal responde `bash: cd: /ruta/que-no-existe: No existe el fichero o el directorio`. ¿Qué significa y qué harías?
5. ¿Qué comando muestra la carpeta actual y qué comando muestra su contenido?

## Respuestas razonadas

1. GNOME Shell es la capa visual de GNOME: dibuja el panel, las actividades, el reloj, las notificaciones y el escritorio. Una extensión es un programa en JavaScript que se carga dentro del shell y añade o modifica una función. No es una aplicación independiente.

2. Primero ejecutaría `pwd` para comprobar en qué carpeta estoy. Después ejecutaría `ls` para ver si el archivo está ahí. Finalmente revisaría en el gestor de archivos si se guardó en otra carpeta o con otro nombre, por ejemplo `hola.txt.txt`. El orden va de lo más inmediato a lo más general.

3. Usaría CSS, porque el color es una propiedad visual. JavaScript podría cambiar el color dinámicamente, pero la regla de apariencia se expresa en CSS. La lógica de cuándo cambia el color sí podría ir en JavaScript.

4. Significa que la ruta no existe, está mal escrita o no tienes acceso. Ejecutaría `pwd` para ver dónde estoy, `ls` para ver las carpetas disponibles y comprobaría el nombre exacto antes de volver a intentarlo.

5. `pwd` muestra la carpeta actual. `ls` muestra los archivos y carpetas que contiene.

## Soluciones de los ejercicios

### Solución del reproductivo

La salida de `pwd` depende de tu equipo. Debe ser una ruta absoluta, por ejemplo:

```text
/home/ana/Documentos/curso-gnome
```

La salida de `ls` debe incluir al menos los nombres que creaste. Un ejemplo válido es:

```text
hola.txt
practica
```

El archivo `hola.txt` debe contener:

```text
Hola, este es mi primer archivo.
```

Una explicación válida de extensión es: “Una extensión es un programa que se carga dentro de GNOME Shell para añadir o cambiar una función del escritorio”.

### Solución del aplicativo

El archivo debe contener:

```text
Estoy aprendiendo GNOME Shell.
```

La ruta nueva debe ser parecida a:

```text
/home/ana/Documentos/curso-gnome/practica-gnome/hola.txt
```

La parte `/home/ana` cambia según tu usuario. Lo importante es que la carpeta `practica-gnome` contenga el archivo y que la ruta incluya todos los niveles.

### Solución del creativo

La estructura debe quedar así:

```text
/home/ana/Documentos/curso-gnome/practicas/hola.txt
```

Desde la carpeta personal, una secuencia válida es:

```bash
cd curso-gnome
cd practicas
pwd
```

La última orden debe mostrar una ruta que termine en `curso-gnome/practicas`.

**Errores posibles en este bloque:**

**Error 1: `bash: cd: curso-gnome: No existe el fichero o el directorio`**
- **Ubicación:** línea de la terminal.
- **Síntoma:** la terminal muestra `bash: cd: curso-gnome: No existe el fichero o el directorio`.
- **Causa:** la carpeta no está en la carpeta personal o el nombre es distinto.
- **Corrección:** ejecuta `pwd` y `ls` para comprobar dónde estás y crea la carpeta si falta. Después de corregir, `pwd` muestra una ruta que termina en `curso-gnome/practicas`.

Para la carpeta con espacios:

```bash
cd 'carpeta con espacios'
pwd
```

Las comillas son necesarias porque la terminal usa el espacio para separar argumentos. Sin comillas, `cd` recibe tres nombres y responde `bash: cd: demasiados argumentos`.

**Errores posibles en este bloque:**

**Error 1: `bash: cd: demasiados argumentos`**
- **Ubicación:** línea de la terminal.
- **Síntoma:** la terminal muestra `bash: cd: demasiados argumentos`.
- **Causa:** escribiste el nombre sin comillas y la terminal interpretó cada palabra como un argumento.
- **Corrección:** escribe `cd 'carpeta con espacios'` con comillas simples. Después de corregir, `pwd` muestra una ruta que termina en `carpeta con espacios`.

## Si quieres saber más

- Documentación de GNOME: <https://help.gnome.org/>
- Guía de GJS para extensiones: <https://gjs.guide/>
- Referencia de CSS en MDN: <https://developer.mozilla.org/es/docs/Web/CSS>

No necesitas estos enlaces para continuar. El manual contiene todo lo necesario para el proyecto.

## Siguiente paso

Cuando completes la lista de verificación y puedas explicar qué es una extensión, continúa con el Capítulo 1. Allí crearás tu primera extensión vacía y verás un mensaje en el log de GNOME Shell.
