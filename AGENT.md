# Skill: Desarrollo de Manual Pedagógico de Extensiones GNOME Shell 50.6 para Novatos Absolutos

## Descripción del Skill

**Nombre:** `gnome-extension-manual-pedagogico`

**Propósito:** Guiar la creación de un manual completo, pedagógico y progresivo para enseñar a personas sin conocimientos previos de programación, JavaScript, CSS ni GNOME a desarrollar sus propias extensiones nativas para GNOME Shell 50.6, sin usar extensiones de terceros.

**Audiencia objetivo:** Personas que nunca han programado, no conocen Linux a fondo, no saben qué es un archivo de configuración y no distinguen entre un editor de texto y una terminal.

**Filosofía del skill:** El manual debe asumir **cero conocimiento previo**. Cada concepto se introduce con una analogía cotidiana antes del código. El lector nunca debe sentir que "le falta algo". El andamiaje es máximo al inicio y decrece gradualmente hasta que el lector construye una extensión completa por sí mismo.

---

## Relación con `RULES.md`

`RULES.md` contiene las reglas de obligado cumplimiento para la producción del manual. Este documento no las sustituye ni las contradice: las aplica. Si una indicación de este skill entra en conflicto con `RULES.md`, prevalece `RULES.md`.

Consecuencias directas para este skill:

- Todo el código de ejemplo vive dentro del texto del manual. No se crean carpetas `etapas/`, `ejemplos/`, ZIPs ni scripts que el lector deba descargar o ejecutar.
- Cada etapa del proyecto se reconstruye transcribiendo el código del capítulo correspondiente, no descargando un archivo.
- El código se presenta en bloques pequeños, con la explicación textual fuera del bloque y una sección "Errores posibles en este bloque" después de cada uno.
- Los errores se documentan con logs reales transcritos, no con descripciones genéricas.
- El código final debe ser robusto y limpiar todos sus recursos en `disable()`.
- El manual es la única fuente de verdad: los enlaces externos únicamente aparecen en "Si quieres saber más" y nunca son requisito para continuar.

---

## Cuándo Usar Este Skill

Activa este skill cuando el usuario pida:
- Crear un manual, curso o guía para aprender extensiones de GNOME Shell desde cero.
- Enseñar GNOME Shell a principiantes absolutos.
- Estructurar contenido pedagógico sobre GJS, CSS en St, o APIs externas para novatos.
- Producir ejercicios, autoevaluaciones y proyectos guiados para aprender extensiones.

**No usar** cuando el usuario pida referencia técnica, documentación de API o contenido para desarrolladores experimentados.

---

## Procedimiento del Skill

### Paso 1: Definir el Perfil del Lector y sus Vacíos

Antes de escribir, produce un documento `PERFIL-LECTOR.md` que responda:

| Pregunta | Respuesta esperada |
| :--- | :--- |
| ¿Sabe qué es un archivo? | No necesariamente |
| ¿Sabe abrir una terminal? | Probablemente no |
| ¿Sabe qué es JavaScript? | No |
| ¿Sabe qué es CSS? | No |
| ¿Sabe qué es GNOME Shell? | Lo usa pero no lo conoce |
| ¿Sabe qué es una extensión? | Quizá ha instalado una, no sabe qué es |
| ¿Sabe qué es un editor de texto? | Quizá usa LibreOffice, no un editor de código |
| ¿Sabe qué es una ruta de archivo? | No con seguridad |

**Regla:** Si alguna respuesta es "no", el manual debe incluir un capítulo o apéndice que cubra ese vacío **antes** de usarlo.

---

### Paso 2: Crear el Capítulo 0 Obligatorio (Nivel Cero)

Todo manual para novatos absolutos debe comenzar con un **Capítulo 0: Antes de Empezar**. Sin excepción. Contenido mínimo:

**0.1. Qué es GNOME Shell y qué es una extensión**
- Analogía: GNOME Shell es como el sistema eléctrico de una casa; una extensión es un aparato que enchufas para añadir una función.
- Captura de pantalla del escritorio con las partes señaladas (panel, actividades, reloj).

**0.2. Qué es un archivo, una carpeta y una ruta**
- Analogía: una carpeta es un cajón; un archivo es un papel dentro; la ruta es la dirección completa del cajón.
- Ejercicio: abrir el gestor de archivos y navegar a una carpeta concreta.

**0.3. Qué es un editor de texto y cuál usar**
- Recomendación: GNOME Text Editor (ya instalado) o VS Code (instalación guiada paso a paso).
- Ejercicio: crear un archivo `hola.txt`, escribir una línea, guardarlo.

**0.4. Qué es una terminal y cómo abrirla**
- Cómo abrir: `Ctrl+Alt+T` o buscar "Terminal" en Actividades.
- Ejercicio: escribir `pwd`, `ls`, `cd` con explicación de cada uno.
- Advertencia: "No copies comandos que no entiendas. Este manual explica cada comando antes de usarlo."

**0.5. Qué es JavaScript y qué es CSS (sin tecnicismos)**
- Analogía: JavaScript es el cerebro que decide qué hacer; CSS es la ropa que decide cómo se ve.
- No se escribe código todavía. Únicamente se muestra un ejemplo visual de 3 líneas y se señala qué es cada parte.

**0.6. Cómo usar este manual**
- Estructura de cada capítulo: objetivo, concepto, ejemplo guiado, errores anticipados, ejercicios, autoevaluación.
- Regla de oro: "Si no entiendes un paso, no avances. El manual está diseñado para que cada paso dependa únicamente del anterior."

**Criterio de salida del Capítulo 0:** El lector sabe abrir una terminal, crear un archivo de texto, guardarlo en una ruta concreta y explicar con sus palabras qué es una extensión.

---

### Paso 3: Aplicar la Regla de las Tres Capas en Cada Concepto

Todo concepto técnico debe presentarse en tres capas sucesivas. Si falta una, el novato se pierde.

**Capa 1: Analogía cotidiana**
- Se presenta el concepto con algo que el lector ya conoce.
- Ejemplo: "Una señal en GJS es como un timbre: tú no vas a la puerta cada segundo; el timbre te avisa cuando alguien llega."

**Capa 2: Ejemplo mínimo funcional**
- Se muestra el código más pequeño posible que usa el concepto.
- Se explica **cada línea** en un párrafo posterior al bloque. Dentro del código únicamente se permiten comentarios puntuales; el comentario no sustituye a la explicación textual.
- Cada bloque se presenta aislado, con una sección "Errores posibles en este bloque" inmediatamente después de su explicación.
- Ejemplo:
```javascript
// Esto es una señal: cuando el botón se pulsa, se ejecuta la función.
boton.connect('clicked', () => {
    console.log('Me pulsaste');
});
```

**Capa 3: Ejercicio de modificación controlada**
- El lector cambia un único parámetro y verifica el resultado.
- Ejemplo: "Cambia `'Me pulsaste'` por `'Hola desde mi extensión'`. Guarda, recarga y verifica que el mensaje cambió en los logs."

**Regla:** Ningún concepto se da por aprendido hasta que el lector completa la Capa 3.

---

### Paso 4: Estructura Fija de Cada Capítulo

Cada capítulo del manual debe seguir esta plantilla sin excepción. La versión lista para copiar está en `PLANTILLA-CAPITULO.md`.

````markdown
# Capítulo N: [Título amigable, no técnico]

## Qué vas a aprender
- Lista de 3 a 5 objetivos en lenguaje llano.
- Ejemplo: "Al final de este capítulo, podrás hacer que tu extensión muestre un mensaje en el panel."

## Antes de empezar necesitas
- Lista de lo que el lector debe tener del capítulo anterior.
- Si falta algo, enlace al capítulo donde se aprendió.

## El concepto, con una analogía
- Explicación sin código, con una comparación cotidiana.
- Captura o diagrama si aplica.

## Ejemplo guiado paso a paso

Cada bloque de código sigue el patrón que se muestra abajo. No se presenta un programa completo en un único bloque.

### Bloque 1: [Responsabilidad del bloque]
```javascript
// Código del bloque. Únicamente comentarios puntuales.
```

> **Qué hace este bloque:** [Explicación textual: qué recibe, qué devuelve, por qué existe y cómo se relaciona con el resto.]

**Errores posibles en este bloque:**

**Error 1: `[mensaje real del log]`**
- **Ubicación:** [archivo y línea donde ocurre].
- **Síntoma:** [qué ve el lector en el log o en pantalla].
- **Causa:** [por qué ocurre en este bloque].
- **Corrección:** [qué cambiar exactamente; incluye el fragmento corregido y el log posterior sin el error].

**Error 2: [nombre del error]**
- **Síntoma:** [...]
- **Causa:** [...]
- **Corrección:** [...]

### Bloque 2: [Responsabilidad del bloque]
```javascript
// Código del bloque.
```

> **Qué hace este bloque:** [Explicación textual.]

**Errores posibles en este bloque:**

**Error 1: ...**

### Guarda y recarga
[Instrucción exacta de dónde guardar y cómo recargar.]

## Verifica que funciona
- Instrucciones para comprobar que el ejemplo funciona.
- Qué debe aparecer en pantalla o en los logs.

## Errores de integración del capítulo (y cómo se ven)
- 2 a 4 errores que aparecen al combinar los bloques o al ejecutarlos en el entorno real.
- Los errores específicos de cada bloque ya se documentan dentro del ejemplo guiado, en "Errores posibles en este bloque".
- Log real transcrito en texto, no una descripción genérica.
- Explicación de por qué ocurre y cómo corregirlo.
- Si el error depende del entorno (Wayland, versión de GNOME, permisos), se indica explícitamente.

## Ejercicios
### Reproductivo (copia y verifica)
- Instrucción exacta de qué copiar y qué verificar.

### Aplicativo (cambia un parámetro)
- Instrucción de qué modificar y qué resultado esperar.

### Creativo (añade algo sin guía completa)
- Objetivo y pistas, no código completo.

## Autoevaluación
- 3 a 5 preguntas con respuestas razonadas al final del capítulo (no únicamente "verdadero/falso").
- Al menos una pregunta de diagnóstico: "Si tu extensión no aparece en el panel, ¿qué revisarías primero y por qué?"

## Si quieres saber más
- Enlaces opcionales a documentación técnica para quien quiera profundizar.
- Advertencia: "No necesitas esto para continuar."
````

**Regla de bloques:** cada bloque incluye al menos un error de sintaxis o tipado, uno de lógica y uno de integración cuando el bloque pueda producirlos. Los errores específicos van junto al bloque que los produce; la sección de integración del capítulo no los sustituye. Si el bloque no puede fallar de forma aislada, se indica y se remite al bloque del que depende. Si un error es difícil de reproducir, se explica cómo provocarlo deliberadamente.

**Regla de autonomía:** cada capítulo se puede leer de forma aislada. Si necesita un bloque de código de un capítulo anterior, se repite completo en el capítulo actual, con su explicación y su sección de errores posibles.

**Regla de vista general:** si el capítulo incluye una vista del archivo completo, se presenta como índice de bloques —qué función está en qué bloque—, nunca como un volcado monolítico.

---

### Paso 5: Proyecto Hilo Conductor Acumulativo

Define un proyecto llamado **"MiPrimeraExtensión"** que crece capítulo a capítulo. Cada etapa debe ser:

- **Reconstruible de forma aislada:** el lector puede transcribir el código de la etapa N desde el capítulo correspondiente y verla funcionar sin depender de archivos externos ni de etapas anteriores (para recuperarse si se atasca).
- **Visible:** el lector ve un cambio en pantalla en cada etapa.
- **Acumulativa:** la etapa N usa lo aprendido en N-1.

| Etapa | Capítulo | Qué añade | Qué ve el lector |
| :--- | :--- | :--- | :--- |
| 0 | 0 | Nada (únicamente entorno) | Terminal abierta, archivo creado |
| 1 | 1 | Extensión vacía que escribe en el log | Mensaje en `journalctl` |
| 2 | 2 | Importa `Main` y muestra una notificación | Notificación en pantalla |
| 3 | 3 | Botón en el panel con icono | Icono visible en la barra superior |
| 4 | 4 | CSS propio que cambia el color del botón | Botón de color distinto |
| 5 | 5 | Preferencias que guardan un color | El color persiste tras cerrar sesión |
| 6 | 6 | Botón que mueve la ventana activa | Ventana cambia de espacio |
| 7 | 7 | Monkey patch reversible | Comportamiento del shell modificado |
| 8 | 8 | (Opcional) Librería C mínima | El lector decide si lo necesita |
| 9 | 9 | Consumo de API de criptomonedas | Precio en el panel |
| 10 | 10 | Empaquetado en ZIP | Extensión instalable en otra máquina |

**Regla de ubicación:** todo el código de cada etapa se transcribe dentro del capítulo que la construye. No se crean carpetas de etapas, archivos ejecutables, ZIPs ni scripts de instalación fuera del manual. La etapa 10 explica cómo empaquetar la extensión, pero el manual no entrega el ZIP hecho.

**Regla:** Si una etapa no produce un cambio visible en pantalla o en los logs, no es una etapa válida para novatos.

---

### Paso 6: Reglas de Redacción para Novatos

Aplica estas reglas en todo el texto:

| Regla | Ejemplo correcto | Ejemplo incorrecto |
| :--- | :--- | :--- |
| Frases cortas (máx. 20 palabras) | "Abre la terminal. Escribe el comando. Pulsa Enter." | "Una vez que hayas abierto la terminal, procede a introducir el comando y ejecútalo." |
| Voz activa y segunda persona | "Tú escribes el código." | "El código es escrito por el usuario." |
| Un concepto por párrafo | Un párrafo = una idea | Mezclar señales, actores y CSS en un párrafo |
| Definir antes de usar | "Un actor es un elemento visual. Este actor es un botón." | "Instancia un actor con `St.Button`." |
| Sin abreviaturas sin explicar | "GJS (el motor de JavaScript de GNOME)" | "GJS" a secas la primera vez |
| Sin fórmulas que minimicen la dificultad | "Escribe el comando." | "Simplemente escribe el comando." |
| Errores con log real | Log de `journalctl` transcrito en texto mostrando el error | "Puede que dé un error." |
| Cada comando explicado | "`pwd` significa 'print working directory' y te dice dónde estás." | "Ejecuta `pwd`." |

### Reglas de código obligatorias

Además de las reglas de redacción, todo código del manual cumple estas condiciones, que desarrollan las reglas 3, 8 y 11 de `RULES.md`:

- **Robusto:** se ejecuta en GNOME Shell 50.6 sobre Wayland tal como se muestra. Si depende de un import, una variable o una clase, ese contexto aparece en el mismo bloque o en el bloque inmediatamente anterior.
- **Legible:** usa nombres descriptivos, sin abreviaturas no explicadas y sin nombres de una sola letra salvo contadores de bucle triviales, que se explican. Usa comillas simples, punto y coma e indentación consistente de cuatro espacios.
- **Seguro:** no usa `var`, no usa `imports.*`, no usa `eval`, no silencia errores con `try { } catch { }` vacíos, no hardcodea claves de API ni rutas absolutas del autor.
- **Con limpieza:** todo recurso que se crea —actor, señal, temporizador, petición de red— se destruye o cancela en `disable()`.
- **Con errores explícitos:** las peticiones de red, el parseo de JSON, la lectura de settings y cualquier operación que pueda fallar incluyen manejo de errores con un mensaje claro.
- **Sin `console.log` como única depuración en el código final:** en las primeras etapas didácticas se usa el log para verificar que la extensión carga; en el código final se usan mensajes de error explícitos y las herramientas de depuración del Capítulo 7.

---

### Paso 7: Sistema de Ejercicios Graduados

Cada capítulo debe tener tres tipos de ejercicio, en este orden:

**1. Reproductivo (andamiaje máximo)**
- El lector copia exactamente lo que el manual dice.
- Verifica que el resultado coincide con la captura.
- Objetivo: confirmar que el entorno funciona.

**2. Aplicativo (andamiaje medio)**
- El lector cambia un parámetro controlado (un color, un texto, un número).
- Verifica que el cambio se refleja.
- Objetivo: confirmar que entiende qué parte del código hace qué.

**3. Creativo (andamiaje mínimo)**
- El lector recibe un objetivo y pistas, pero no el código completo.
- Ejemplo: "Haz que el botón cambie de color cada vez que lo pulses. Pista: usa una variable y la señal `'clicked'`."
- Objetivo: confirmar que puede aplicar lo aprendido sin guía.

**Regla:** Si el lector no completa el reproductivo, no debe intentar el creativo. El manual debe decirlo explícitamente.

**Regla de soluciones:** todo ejercicio tiene solución publicada en el mismo manual, separada del enunciado. Si el ejercicio creativo no tiene solución única, se publica una rúbrica con criterios objetivos.

---

### Paso 8: Autoevaluación con Diagnóstico

Cada autoevaluación debe incluir:

- **Preguntas de comprensión:** "¿Por qué `disable()` debe destruir los actores?"
- **Preguntas de diagnóstico:** "Tu extensión no aparece en el panel. ¿Qué tres cosas revisarías y en qué orden?"
- **Preguntas de transferencia:** "Si quisieras que el botón mostrara la hora, ¿qué parte del código cambiarías?"

Las respuestas deben estar al final del capítulo, razonadas, no únicamente con la solución.

---

### Paso 9: Verificación con Lector Novel

Cada capítulo se prueba antes de publicarse. El procedimiento es:

1. Una persona sin experiencia en programación sigue el capítulo completo, desde el inicio hasta el final.
2. Se registra cada momento en que se atasca, con el capítulo y la frase exacta.
3. Cada atasco se corrige añadiendo una analogía, un paso intermedio o un ejercicio adicional.
4. Se repite hasta que el lector novel completa el capítulo sin ayuda externa.
5. Como prueba de conjunto, la secuencia de los capítulos 0 a 4 se prueba completa para validar los cimientos.

**Criterio de éxito del capítulo:** El lector novel completa la etapa del proyecto correspondiente a ese capítulo sin asistencia y sabe explicar por qué funciona.

**Criterio de éxito de los cimientos:** El lector novel construye la etapa 4 del proyecto (botón con CSS propio) sin asistencia y sabe explicar por qué funciona.

**Registro:** el informe de la prueba se conserva como documentación interna del manual, con el capítulo, la frase exacta del atasco y la corrección aplicada. No se publica. La verificación se completa con `CHECKLIST-CALIDAD.md`.

---

### Paso 10: Publicación con Navegación Pedagógica

El manual publicado debe incluir:

- **Índice temático** (qué se aprende en cada capítulo).
- **Mapa de prerequisitos** (qué necesitas saber antes de cada capítulo).
- **Glosario vivo** (términos definidos al primer uso, con enlace desde cada aparición).
- **Soluciones de ejercicios** (separadas del cuerpo del capítulo para no tentar al lector).
- **Tabla de etapas del proyecto** con el capítulo donde se construye cada una; todo el código de la etapa vive dentro del manual y no se distribuye como archivo independiente.

---

## Plantilla de Capítulo (Copia y Uso Directo)

````markdown
# Capítulo N: [Título amigable]

## Qué vas a aprender
- [Objetivo 1 en lenguaje llano]
- [Objetivo 2]
- [Objetivo 3]

## Antes de empezar necesitas
- [Requisito del capítulo anterior, con enlace]
- [Herramienta o archivo necesario]

## El concepto, con una analogía
[Analogía cotidiana. Sin código. Máximo 3 párrafos cortos.]

## Ejemplo guiado paso a paso

Cada bloque de código sigue el patrón que se muestra abajo. No se presenta un programa completo en un único bloque.

### Bloque 1: [Responsabilidad del bloque]
```javascript
// Código del bloque. Únicamente comentarios puntuales.
```

> **Qué hace este bloque:** [Explicación textual: qué recibe, qué devuelve, por qué existe y cómo se relaciona con el resto.]

**Errores posibles en este bloque:**

**Error 1: `[mensaje real del log]`**
- **Ubicación:** [archivo y línea donde ocurre].
- **Síntoma:** [qué ve el lector en el log o en pantalla].
- **Causa:** [por qué ocurre en este bloque].
- **Corrección:** [qué cambiar exactamente; incluye el fragmento corregido y el log posterior sin el error].

**Error 2: [nombre del error]**
- **Síntoma:** [...]
- **Causa:** [...]
- **Corrección:** [...]

### Bloque 2: [Responsabilidad del bloque]
```javascript
// Código del bloque.
```

> **Qué hace este bloque:** [Explicación textual.]

**Errores posibles en este bloque:**

**Error 1: ...**

### Guarda y recarga
[Instrucción exacta de dónde guardar y cómo recargar.]

## Verifica que funciona
[Qué debe aparecer en pantalla o en los logs. Captura.]

## Errores de integración del capítulo (y cómo se ven)
### Error 1: [Nombre del error]
[Log real transcrito en texto.]
[Por qué ocurre al integrar bloques o el entorno. Cómo corregirlo.]

### Error 2: [Nombre del error]
[Log real transcrito en texto.]
[Por qué ocurre. Cómo corregirlo.]

## Ejercicios
### Reproductivo
[Copia exacta y verificación.]

### Aplicativo
[Qué cambiar y qué resultado esperar.]

### Creativo
[Objetivo y pistas, sin código completo.]

## Autoevaluación
1. [Pregunta de comprensión]
2. [Pregunta de diagnóstico]
3. [Pregunta de transferencia]

[Respuestas razonadas al final del capítulo.]

## Si quieres saber más
[Enlaces opcionales. Advertencia: no necesitas esto para continuar.]
````

---

## Reglas Inviolables del Skill

Estas 13 reglas aplican junto con las reglas de `RULES.md`. Si hay conflicto, prevalece `RULES.md`.

1. **Nunca uses un término técnico sin definirlo la primera vez.**
2. **Nunca escribas código sin explicar cada línea en el párrafo que lo acompaña.**
3. **Nunca avances sin un ejercicio de verificación.**
4. **Nunca introduzcas dos conceptos nuevos en el mismo párrafo.**
5. **Nunca uses fórmulas que minimicen la dificultad: "simplemente", "solo hay que", "obviamente" o "como sabrás".**
6. **Nunca des por hecho que el lector sabe abrir una terminal, guardar un archivo o recargar el shell.**
7. **Siempre incluye el log real del error transcrito en texto, no una descripción genérica.**
8. **Siempre ofrece una etapa del proyecto reconstruible de forma aislada, con todo su código dentro del capítulo.**
9. **Siempre incluye una pregunta de diagnóstico en la autoevaluación.**
10. **Siempre verifica el manual con un lector novel real antes de publicar.**
11. **Nunca presentes un programa completo en un único bloque: divide por responsabilidades y explica cada bloque fuera del código.**
12. **Siempre incluye "Errores posibles en este bloque" después de cada bloque de código, con síntoma, causa y corrección.**
13. **Nunca crees archivos de ejemplo, etapas descargables, ZIPs o scripts de instalación fuera del manual.**

---

## Métricas de Éxito del Manual

| Métrica | Umbral mínimo |
| :--- | :--- |
| Cada capítulo probado con lector novel antes de publicar | 100% de los capítulos |
| Lector novel completa Capítulo 4 sin ayuda | 1 de cada 2 lectores probados |
| Lector novel explica por qué `disable()` limpia | 100% de los lectores probados |
| Lector novel construye el proyecto integrador (Cap. 9) | 1 de cada 3 lectores probados |
| Atascos reportados en Capítulo 0 | 0 (si hay, el Capítulo 0 está incompleto) |
| Ejercicios con solución publicada | 100% |
| Errores anticipados con log real transcrito | 100% |
| Bloques de código con su sección de errores posibles | 100% |
| Código de ejemplo fuera del manual | 0 archivos |

---

## Ejemplo de Activación del Skill

**Usuario:** "Quiero hacer un manual para que mi hermano, que no sabe programar, aprenda a hacer extensiones de GNOME."

**Respuesta del skill:**
1. Crear `PERFIL-LECTOR.md` con las preguntas del Paso 1.
2. Producir el Capítulo 0 completo (Paso 2).
3. Definir el proyecto "MiPrimeraExtensión" con sus etapas 0 a 10 (Paso 5), sin crear archivos de etapas fuera del manual.
4. Escribir el Capítulo 1 aplicando las tres capas (Paso 3) y la plantilla fija (Paso 4).
5. Incluir ejercicios graduados (Paso 7) y autoevaluación con diagnóstico (Paso 8), con soluciones publicadas.
6. Programar la prueba con lector novel (Paso 9) antes de continuar al Capítulo 2.


