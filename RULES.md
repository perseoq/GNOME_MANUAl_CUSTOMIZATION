# Reglas Inviolables para la Producción del Manual de Extensiones GNOME Shell 50.6

Estas reglas son de obligado cumplimiento para cualquier persona o agente que colabore en la redacción, revisión o ampliación de este manual. Ninguna regla puede ser ignorada, relativizada ni sustituida por criterios personales de estilo. Si una regla entra en conflicto con una preferencia estética o con una supuesta "buena práctica" externa, prevalece la regla.

---

## Regla 1: Todo el código vive dentro del manual

**Ningún ejemplo de código puede existir únicamente en un archivo real del repositorio, del sistema de archivos del autor o de un enlace externo.**

Esto significa:

- Cada fragmento de JavaScript, CSS, XML, JSON, C o cualquier otro lenguaje que se use para enseñar debe estar **transcrito íntegramente dentro del texto del capítulo correspondiente**, en un bloque de código cercado con su lenguaje declarado.
- No se permite la fórmula "el código completo está en `etapa-03/extension.js`, consúltalo allí". El lector debe poder leer, copiar y entender el código sin abrir otro archivo.
- No se permite delegar la explicación a un repositorio externo, a un enlace a GitLab, a un gist o a un archivo comprimido. Los enlaces pueden existir como material complementario, pero **nunca como sustituto del contenido del manual**.
- Si un ejemplo se usa en varios capítulos, debe repetirse completo en cada capítulo donde se necesite, aunque eso alargue el manual. La verbosidad es un requisito, no un defecto.
- Los archivos auxiliares del repositorio del manual (capturas, diagramas, logs) pueden existir, pero el código fuente de los ejemplos **solo** puede residir en el manual.

**Justificación:** El manual es la única fuente de verdad. Un lector no debe depender de archivos que pueden cambiar, perderse o no estar disponibles en su versión del manual. La autonomía del texto es un requisito pedagógico.

---

## Regla 2: Nada de scripts de ejemplo en archivos reales

**No se deben crear, generar ni mantener archivos ejecutables, extensiones instalables, scripts de shell o proyectos de ejemplo fuera del texto del manual.**

Esto significa:

- No se crea una carpeta `ejemplos/` con extensiones funcionales que el lector deba descargar e instalar.
- No se crea una carpeta `etapas/` con el proyecto hilo conductor ya resuelto.
- No se crean scripts de instalación, de compilación o de prueba que el lector deba ejecutar desde el repositorio.
- No se crean archivos `.zip` de extensiones de ejemplo.
- El proyecto hilo conductor "MiPrimeraExtensión" se construye **escribiendo el código en el manual**, paso a paso, y el lector lo transcribe en su propia máquina. El manual no entrega el proyecto hecho.

**Excepción única y estrictamente limitada:** El autor del manual puede tener, en su entorno privado de trabajo, archivos temporales para verificar que los fragmentos que escribe funcionan. Esos archivos **no forman parte del manual, no se publican, no se enlazan y no se mencionan en el texto**. El lector nunca sabe que existen.

**Justificación:** Si el manual entrega archivos reales, el lector deja de leer y empieza a copiar. La pedagogía exige que el lector escriba el código con sus manos, aunque sea transcribiéndolo, para que el acto de teclearlo fuerce la atención.

---

## Regla 3: El código debe ser robusto

**Todo fragmento de código incluido en el manual debe ser funcional, completo en su contexto, seguro y libre de atajos que enseñen malas prácticas.**

Esto significa:

- **Funcional:** El código debe ejecutarse sin errores en GNOME Shell 50.6 sobre Wayland, tal como está escrito en el manual. No se permiten fragmentos incompletos que "el lector debe completar" salvo que el ejercicio lo indique explícitamente y se dé la solución.
- **Completo en su contexto:** Si un fragmento depende de un import, de una variable declarada antes o de una clase que lo envuelve, ese contexto debe estar presente en el mismo bloque de código o en el bloque inmediatamente anterior, claramente señalado.
- **Seguro:** El código no debe introducir fugas de memoria, no debe dejar señales conectadas tras `disable()`, no debe sobrescribir funciones internas del shell sin restaurarlas, no debe hardcodear claves de API ni rutas absolutas del autor.
- **Sin atajos que enseñen mal:** No se permite `var` en lugar de `let`/`const`, no se permite `imports.*` obsoleto, no se permite `eval`, no se permite silenciar errores con `try { } catch { }` vacíos, no se permite `console.log` como única forma de depuración en código final.
- **Con manejo de errores explícito:** Las peticiones de red, el parseo de JSON, la lectura de settings y cualquier operación que pueda fallar deben incluir su bloque de manejo de errores con un mensaje claro para el lector.
- **Con limpieza en `disable()`:** Todo fragmento que cree un recurso (actor, señal, temporizador, petición de red) debe mostrar también cómo se destruye o cancela en `disable()`.

**Justificación:** Un manual para novatos que enseña código frágil produce lectores que escriben código frágil. La robustez se enseña con el ejemplo, no con la advertencia.

---

## Regla 4: El manual debe ser verboso y bien explicado

**Cada concepto, cada línea de código y cada paso debe estar explicado con detalle. La brevedad no es una virtud en este manual.**

Esto significa:

- **Ninguna línea de código queda sin explicación.** Si un bloque tiene diez líneas, las diez tienen su explicación, ya sea en comentarios dentro del bloque o en un párrafo posterior que las recorra.
- **Cada comando de terminal se explica antes de usarse:** qué hace, por qué se usa, qué se espera que devuelva y qué hacer si falla.
- **Cada error anticipado se documenta con su log real**, capturado en texto dentro del manual, con la explicación de por qué ocurre y cómo se corrige.
- **Cada analogía se desarrolla**, no se lanza y se abandona. Si se compara una señal con un timbre, se explica qué es el timbre, quién lo pulsa, quién lo escucha y qué pasa si no hay nadie en casa.
- **Cada capítulo repite lo necesario del anterior** aunque parezca redundante. El lector puede leer los capítulos en desorden o volver a uno antiguo; la redundancia es un servicio, no un defecto.
- **No se usan frases como "como es obvio", "simplemente", "solo hay que", "ya sabes"**. Toda dificultad se reconoce y se acompaña.
- **Los términos técnicos se definen al primer uso y se vuelven a definir brevemente cuando reaparecen tras varios capítulos.**

**Justificación:** El lector objetivo no sabe nada. La verbosidad no es relleno; es el andamiaje que sostiene la comprensión. Un manual breve para novatos es un manual que abandona a los novatos.

---

## Regla 5: Ningún concepto se introduce sin ejercicio de verificación

**Cada concepto nuevo debe ir seguido de una forma concreta de comprobar que el lector lo entendió y que su entorno lo ejecuta correctamente.**

Esto significa:

- No se avanza al siguiente concepto sin un ejercicio reproductivo que confirme que el ejemplo funciona en la máquina del lector.
- No se avanza al siguiente capítulo sin una autoevaluación con al menos una pregunta de diagnóstico.
- Los ejercicios tienen solución publicada en el mismo manual, separada del enunciado para no tentar al lector.
- Si un ejercicio creativo no tiene solución única, se publica una rúbrica de evaluación con criterios objetivos.

**Justificación:** Sin verificación, el lector cree que entendió y descubre en el capítulo siguiente que no. La verificación temprana evita el abandono.

---

## Regla 6: El manual es la única fuente de verdad

**Si algo no está en el manual, no existe para el lector.**

Esto significa:

- No se remite al lector a la documentación oficial de GNOME, de GJS o de Clutter para "completar" lo que el manual no explica.
- No se remite a repositorios de terceros, a foros, a wikis o a videos.
- Los enlaces externos pueden aparecer en la sección "Si quieres saber más" al final de cada capítulo, pero **nunca como requisito para continuar**.
- Si un tema es demasiado avanzado para el alcance del manual, se dice explícitamente: "Este tema queda fuera del manual. No lo necesitas para completar el proyecto."

**Justificación:** Un manual que delega su contenido a otras fuentes no es un manual; es un índice. El lector debe poder completar el proyecto sin salir del texto.

---

## Regla 7: Prohibido el código duplicado sin propósito pedagógico

**La duplicación de código entre capítulos solo se permite cuando sirve para que un capítulo sea autónomo.**

Esto significa:

- Si un capítulo necesita un bloque de código que ya apareció antes, se repite completo, con la misma explicación o una referencia clara a la explicación anterior.
- No se permite la fórmula "este código es el mismo del Capítulo 3, no lo repetimos". El lector que llega al Capítulo 7 directamente no tiene el Capítulo 3 a mano.
- La duplicación por autonomía es una excepción deliberada a la Regla 1, y la Regla 1 prevalece: el código debe estar en el manual, y si un capítulo lo necesita, debe estar en ese capítulo.

**Justificación:** La autonomía de cada capítulo es un requisito pedagógico. Un lector puede necesitar releer un capítulo aislado; el manual debe servírselo completo.

---

## Regla 8: Prohibido el código ofuscado, minificado o generado

**Todo el código del manual debe ser legible, escrito a mano y comprensible por un novato.**

Esto significa:

- Nada de código minificado, ofuscado o generado por herramientas.
- Nada de nombres de variables de una sola letra (`a`, `b`, `x`) salvo en contadores de bucle triviales, y en ese caso se explica.
- Nada de abreviaturas no explicadas en nombres de funciones o variables.
- Los comentarios en el código son obligatorios cuando la línea no es evidente por sí misma.
- El estilo de código debe ser consistente en todo el manual: comillas simples, punto y coma, indentación de cuatro espacios o dos, pero siempre la misma.

**Justificación:** El lector aprende leyendo el código. Si el código no se puede leer, no se puede aprender.

---

## Regla 9: Todo error documentado debe tener su log real

**Cuando el manual anticipe un error, debe mostrar el log o el mensaje exacto que el lector verá, no una descripción genérica.**

Esto significa:

- Nada de "puede que dé un error". Se muestra el error textual tal como aparece en `journalctl` o en Looking Glass.
- Se explica en qué archivo y en qué línea ocurre, y por qué.
- Se muestra la corrección aplicada y el log posterior sin el error.
- Si el error depende del entorno (versión de GNOME, Wayland, permisos), se indica explícitamente.

**Justificación:** El novato no sabe interpretar errores. Si el manual le muestra el error exacto y le explica qué significa, aprende a diagnosticar. Si le da una descripción vaga, no aprende nada.

---

## Regla 10: El manual se verifica con un lector novel antes de publicarse

**Ningún capítulo se considera terminado hasta que una persona sin experiencia previa lo haya completado sin ayuda externa.**

Esto significa:

- Se registra cada punto de atasco con el capítulo, la frase y el motivo.
- Cada atasco se corrige añadiendo explicación, analogía, paso intermedio o ejercicio.
- Se repite la prueba hasta que el lector novel complete el capítulo sin asistencia.
- El informe de la prueba se conserva como parte de la documentación interna del manual, aunque no se publique.

**Justificación:** El autor no puede evaluar la claridad de lo que escribió porque ya sabe lo que quiso decir. Solo un lector novel revela dónde el manual falla.

---

## Regla 11: El código se presenta en bloques pequeños y explicados, nunca como un monolito

**Aunque el manual es verboso y el código es robusto, está prohibido documentar un programa completo en un solo bloque de código. Cada unidad funcional (una función, un método, una clase, un bloque de imports) debe presentarse en su propio bloque, seguido de la explicación textual de qué hace ese bloque y por qué existe.**

Esto significa:

- **Un bloque de código, una responsabilidad.** Si una extensión tiene una función para lavar, otra para secar y otra para planchar, el manual no muestra las tres en un solo bloque de cien líneas. Muestra el bloque de `lavar()`, lo explica en texto; luego el bloque de `secar()`, lo explica; luego el bloque de `planchar()`, lo explica; y solo al final, si es necesario, muestra cómo se integran las tres en el flujo principal.
- **La explicación textual va fuera del bloque, no dentro.** Los comentarios dentro del código son puntuales (una línea, una aclaración). La explicación de qué hace el bloque, por qué está ahí y cómo se relaciona con el resto va en párrafos de texto antes y después del bloque. El comentario no sustituye al párrafo.
- **Cada bloque debe poder leerse de forma aislada.** Antes de cada bloque, el manual indica de qué archivo forma parte, qué función o clase implementa, y qué recibe y qué devuelve. Después del bloque, el manual recorre su lógica línea por línea o por grupos de líneas coherentes.
- **Prohibido el "aquí está el código completo de la extensión".** El capítulo puede terminar con una vista general del archivo (un índice de qué función está en qué bloque), pero nunca con un volcado monolítico que el lector deba descifrar solo. Si el lector necesita ver el archivo completo para entenderlo, el manual ha fallado.
- **El orden de presentación de los bloques sigue el orden de lectura del código**, no el orden de escritura del autor. Primero los imports, luego las constantes, luego las funciones auxiliares, luego la clase principal, luego el flujo de `enable()` y `disable()`. Cada bloque se presenta donde el lector lo necesita, no donde el autor lo escribió.
- **Los bloques largos se dividen por lógica, no por número de líneas.** Si una función tiene treinta líneas pero tres partes claramente diferenciadas (validación, transformación, retorno), se presentan como tres bloques con su explicación intercalada, aunque en el archivo real sean una sola función. El manual puede decir: "Estas tres partes van juntas en la misma función, pero las vemos por separado para entenderlas."

**Ejemplo del patrón obligatorio:**

```javascript
// Bloque 1: la función lavar
function lavar(ropa) {
    // ...código de lavado...
}
```

> **Qué hace este bloque:** La función `lavar` recibe la ropa sucia y la devuelve limpia. Recorre cada prenda, aplica agua y jabón, y la enjuaga. No se encarga de secar ni de planchar; solo lava. La llamaremos desde el flujo principal antes de secar.

```javascript
// Bloque 2: la función secar
function secar(ropa) {
    // ...código de secado...
}
```

> **Qué hace este bloque:** La función `secar` recibe la ropa limpia y la devuelve seca. Usa aire caliente durante un tiempo determinado. No lava ni plancha. Depende de que `lavar` haya terminado antes, pero no la llama directamente: el flujo principal las coordina.

```javascript
// Bloque 3: la función planchar
function planchar(ropa) {
    // ...código de planchado...
}
```

> **Qué hace este bloque:** La función `planchar` recibe la ropa seca y la devuelve lista para guardar. Aplica calor y presión. Si la ropa está húmeda, el planchado falla; por eso el flujo principal debe llamar a `secar` antes que a `planchar`.

```javascript
// Bloque 4: el flujo principal que coordina las tres funciones
function cicloDeLavado(ropa) {
    const limpia = lavar(ropa);
    const seca = secar(limpia);
    const planchada = planchar(seca);
    return planchada;
}
```

> **Qué hace este bloque:** El flujo principal no lava, no seca ni plancha: solo coordina. Llama a las tres funciones en orden y pasa el resultado de una a la siguiente. Si quisiéramos añadir un paso de doblado, lo añadiríamos aquí, no dentro de `planchar`.

**Justificación:** Un bloque monolítico obliga al lector a hacer dos trabajos a la vez: entender qué hace el programa y entender qué hace cada parte. Separar los bloques le permite hacer un trabajo a la vez. La explicación textual fuera del bloque obliga al autor a articular el propósito de cada pieza, lo que revela ambigüedades que un comentario dentro del código oculta. Además, un bloque pequeño se puede releer, copiar y verificar de forma aislada, lo que encaja con la Regla 5 (verificación) y con la Regla 3 (robustez: cada bloque se prueba por separado antes de integrarse).

---

## Regla 12: Cada bloque de código debe ir acompañado de una sección de errores posibles

**Después de cada bloque de código y de su explicación textual, el manual debe incluir un apartado explícito titulado "Errores posibles en este bloque" que enumere los fallos que ese bloque concreto puede producir, cómo se manifiestan y cómo se corrigen.**

Esto significa:

- **No se espera al final del capítulo para hablar de errores.** Cada bloque funcional (una función, un método, un import, un bloque CSS, un archivo XML) tiene su propia sección de errores posibles inmediatamente después de la explicación de qué hace.
- **Los errores deben ser específicos de ese bloque, no genéricos del capítulo.** El error "olvidaste el punto y coma" no sirve; el error "la función `secar` recibe `undefined` porque `lavar` devolvió `null` cuando la ropa estaba vacía" sí sirve.
- **Cada error debe incluir tres elementos obligatorios:**
  1. **El síntoma:** qué ve el lector (un log, un comportamiento, una pantalla en blanco, un mensaje en Looking Glass).
  2. **La causa:** por qué ocurre, en términos del código de ese bloque.
  3. **La corrección:** qué cambiar exactamente para resolverlo, mostrando el fragmento corregido si aplica.
- **El síntoma debe mostrarse con texto real, no con una descripción.** Se transcribe el mensaje de error tal como aparece en `journalctl` o en Looking Glass, entre bloques de código, no en una frase narrativa.
- **Si el bloque no puede fallar de forma aislada**, se indica explícitamente: "Este bloque no produce errores por sí mismo, pero puede fallar si el bloque anterior devuelve un valor inesperado", y se remite al bloque del que depende.
- **Los errores posibles deben cubrir al menos tres categorías:**
  1. **Errores de sintaxis o tipado** (el código no carga, el shell muestra un error en el log).
  2. **Errores de lógica** (el código carga pero hace algo distinto de lo esperado, o no hace nada).
  3. **Errores de integración** (el bloque funciona solo, pero falla al combinarse con otro bloque o con el entorno: Wayland, versión de GNOME, permisos).
- **Cuando un error sea difícil de reproducir**, se indica cómo provocarlo deliberadamente para que el lector lo vea: "Para ver este error, borra la línea X y recarga la extensión."

**Ejemplo del patrón obligatorio:**

```javascript
// Bloque 2: la función secar
function secar(ropa) {
    if (!ropa || ropa.length === 0) {
        throw new Error('No hay ropa para secar');
    }
    return ropa.map(prenda => aplicarAireCaliente(prenda));
}
```

> **Qué hace este bloque:** La función `secar` recibe la ropa limpia y la devuelve seca. Primero comprueba que haya ropa; si no hay, lanza un error explícito. Luego recorre cada prenda y le aplica aire caliente. No lava ni plancha.

**Errores posibles en este bloque:**

**Error 1: `Error: No hay ropa para secar`**
- **Síntoma:** En `journalctl` aparece `Error: No hay ropa para secar` y la extensión no termina el ciclo.
- **Causa:** El bloque anterior (`lavar`) devolvió un array vacío o `null` porque la ropa de entrada estaba vacía.
- **Corrección:** Verifica que `lavar` recibe ropa antes de llamar a `secar`. Si el ciclo puede ejecutarse sin ropa, envuelve la llamada en un `if` o captura el error con `try/catch`.

**Error 2: `TypeError: ropa.map is not a function`**
- **Síntoma:** En `journalctl` aparece `TypeError: ropa.map is not a function`.
- **Causa:** `ropa` no es un array, sino un objeto o un string, porque el bloque anterior devolvió un tipo distinto del esperado.
- **Corrección:** Añade una comprobación de tipo antes del `map`: `if (!Array.isArray(ropa)) { throw new Error('secar espera un array'); }`.

**Error 3: La ropa sale húmeda sin error en el log**
- **Síntoma:** No hay error, pero al planchar la ropa sigue húmeda y el planchado falla más adelante.
- **Causa:** `aplicarAireCaliente` no está definida o devuelve la prenda sin modificar; el bloque se ejecuta sin fallar pero no hace su trabajo.
- **Corrección:** Verifica que `aplicarAireCaliente` existe y devuelve una prenda seca. Añade un `console.log` temporal para inspeccionar el valor devuelto.

**Justificación:** Un manual que solo muestra el camino feliz enseña al lector a escribir código que funciona una vez y falla en cuanto cambia una condición. Al documentar los errores posibles de cada bloque, el lector aprende a leer los síntomas, a relacionarlos con la causa en el código y a corregirlos. Además, obliga al autor a probar cada bloque en condiciones adversas antes de publicarlo, lo que refuerza la Regla 3 (robustez) y la Regla 9 (logs reales). La sección de errores por bloque es también el insumo natural para los ejercicios de diagnóstico de la Regla 5: el lector puede practicar la corrección de errores que ya ha visto documentados.

---

## Regla 13: Cada fase termina con un push al repositorio remoto

**Al terminar cada fase del plan de producción, y únicamente después de que sus pruebas de calidad hayan pasado, se debe confirmar y subir todo el trabajo al repositorio remoto `https://github.com/perseoq/GNOME_MANUAl_CUSTOMIZATION.git`.**

Esto significa:

- **La fase no se considera terminada hasta que el push se completa.** Un commit local no basta.
- **El push incluye todos los cambios de la fase:** capítulos, ejemplos, capturas, correcciones, pruebas y documentación interna que corresponda publicar.
- **Antes del push se verifica que no se suban secretos, claves de API, rutas absolutas del autor, archivos temporales, ZIPs de ejemplo ni código que deba vivir únicamente dentro del manual.**
- **Si el push falla, la fase queda abierta.** Se resuelve el error de credenciales, conflicto o conexión, y se repite antes de pasar a la siguiente fase.
- **El mensaje de commit describe la fase cerrada** y menciona qué capítulos o documentos incluye.
- **No se hace push de trabajo a medias como si la fase estuviera terminada.** Si se necesita un respaldo intermedio, se usa una rama de trabajo o un commit marcado explícitamente como borrador, sin declarar la fase cerrada.
- **El push se dirige a la rama principal configurada en el repositorio remoto.**

**Justificación:** El repositorio remoto es la copia publicada y recuperable del manual. Si una fase se cierra únicamente en la máquina del autor, el trabajo puede perderse o quedar desincronizado. El push al final de cada fase convierte cada hito pedagógico en una versión verificable y permite que cualquier colaborador parta del mismo estado.

---

## Resumen de las Reglas

| # | Regla | Consecuencia de incumplimiento |
| :--- | :--- | :--- |
| 1 | Todo el código vive dentro del manual | El capítulo se rechaza y se reescribe |
| 2 | Nada de scripts de ejemplo en archivos reales | Se elimina el archivo y se transcribe al manual |
| 3 | El código debe ser robusto | El fragmento se corrige hasta que sea seguro y completo |
| 4 | El manual debe ser verboso y bien explicado | Se amplía la explicación antes de continuar |
| 5 | Ningún concepto sin ejercicio de verificación | Se añade el ejercicio o se elimina el concepto |
| 6 | El manual es la única fuente de verdad | Se elimina la dependencia externa o se transcribe el contenido |
| 7 | Prohibido el código duplicado sin propósito pedagógico | Se repite el código en el capítulo que lo necesita |
| 8 | Prohibido el código ofuscado, minificado o generado | Se reescribe a mano con nombres legibles |
| 9 | Todo error documentado debe tener su log real | Se captura el log y se añade al capítulo |
| 10 | El manual se verifica con un lector novel | El capítulo no se publica hasta pasar la prueba |
| 11 | Código en bloques pequeños y explicados, nunca monolito | Se divide el bloque y se añade la explicación textual |
| 12 | Cada bloque lleva su sección de errores posibles | Se añade la sección con síntoma, causa y corrección |
| 13 | Cada fase termina con push al repositorio remoto | La fase no se cierra hasta que el push se complete |

---

## Aplicación de las Reglas

Estas reglas se aplican a:

- Todo capítulo, subcapítulo, sección y párrafo del manual.
- Todo bloque de código, comando de terminal, archivo de configuración y log de error.
- Todo ejercicio, autoevaluación y solución publicada.
- Toda revisión, ampliación o corrección posterior a la publicación.
- El cierre de cada fase del plan de producción, incluido el push al repositorio remoto.

**Ninguna regla admite excepciones no documentadas. Si una excepción es necesaria, se documenta en este mismo archivo con su justificación, y solo el autor principal del manual puede aprobarla.**
```
