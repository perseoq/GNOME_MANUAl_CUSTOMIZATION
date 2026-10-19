# Análisis del estado del manual y plan de corrección

**Documento interno de producción.**
**Fecha:** 2026-10-19
**Alcance:** revisión del repositorio completo: capítulos 0–10, apéndices y documentación de producción.
**Estado del repositorio:** limpio y sincronizado con `origin/main`.

---

## 1. Resumen ejecutivo

El proyecto **no está incompleto en contenido**. Existen los 11 capítulos (0–10), los tres apéndices, el índice, las reglas y la documentación de producción.

Sin embargo, **no está listo para publicarse**. Faltan cuatro cosas principales:

1. Licencia y decisiones editoriales cerradas.
2. Capturas y diagramas, especialmente para el público novel.
3. Pruebas reales con lectores noveles y checklists firmadas.
4. Correcciones de cumplimiento: secciones de plantilla, bloques sin código, autonomía de los capítulos 5, 6 y 9, y errores sin log real.

Además, hay que verificar el empaquetado del esquema GSettings del Capítulo 10 y aclarar el alcance real del Capítulo 8.

---

## 2. Bloqueantes para publicar

| Falta | Evidencia |
| :--- | :--- |
| **Licencia** | No existe `LICENSE`. Sigue pendiente en `README.md:97` y `DECISIONES.md:42,84`. |
| **Pruebas con lector novel** | La Regla 10 exige probar cada capítulo con una persona sin experiencia. No hay informes de atasco. `README.md:13-23` reconoce que todas las fases están “pendientes de la prueba con un lector novel”. |
| **Cierre de calidad** | `CHECKLIST-CALIDAD.md` tiene 78 casillas y ninguna marcada. No hay una ficha de cierre por capítulo ni firma de fase. |
| **Capturas e imágenes** | No hay ninguna imagen en el repositorio (`![...]` = 0). El Capítulo 0 pedía captura del escritorio, y `AGENT.md` y `ENTORNO-PRUEBAS.md` hablan de capturas. Para el público objetivo es una carencia importante. |
| **Capítulo 8 sin verificar** | `manual/08-c.md:118` dice “este capítulo no compila una biblioteca” y `ENTORNO-PRUEBAS.md:179` confirma que no se compiló. No hay código C real, solo explicación y un `import` de JS. Si se prometió una “librería C mínima”, falta; si el alcance es conceptual, hay que corregir la promesa en `AGENT.md` y `MANUAL.md`. |

---

## 3. Incumplimientos de la plantilla y de las reglas

### 3.1. Faltan secciones obligatorias

La plantilla de `AGENT.md:126-211` dice “sin excepción”, pero:

- **Capítulos 1, 3, 7 y 8** no tienen la sección `## Ejemplo guiado paso a paso`.
- **Capítulos 0, 1, 3, 7 y 8** no usan el esquema `### Bloque N`.
- El **Capítulo 0** tampoco tiene “Ejemplo guiado”; solo conceptos y ejercicios.

Tienen código y secciones de errores, pero la estructura no coincide con la plantilla.

### 3.2. Hay bloques sin código

- `manual/05-persistencia.md:598`: “Bloque 4: crear la ventana de preferencias” solo remite a la sección 5.3; no muestra el bloque.
- `manual/09-apis.md:513`: “Bloque 1: crear el proveedor” solo remite a 9.3.1.
- `manual/10-empaquetado.md:314`: “Bloque 1: revisar el metadata” no incluye bloque de código.

El código sí existe en el capítulo, pero no se presenta como bloque autónomo, y el lector no puede copiar el bloque completo tal como exige la Regla 11.

### 3.3. Se rompe la autonomía de capítulos

La Regla 7 y `AGENT.md:215` exigen repetir el código necesario para que un capítulo se lea aislado. Sin embargo:

- El **Capítulo 6** no contiene ningún `export default class`; solo añade un import, una acción de menú y un método suelto.
- El **Capítulo 9** tampoco contiene la clase completa; solo fragmentos para integrar.
- El **Capítulo 5** tampoco muestra el `extension.js` final completo, solo piezas.

Un lector que abra directamente el Capítulo 6 o el 9 no puede reconstruir la extensión solo con ese capítulo.

### 3.4. No todos los bloques tienen su sección de errores

Recuento aproximado de bloques de código frente a secciones “Errores posibles en este bloque”:

| Capítulo | Bloques aprox. | Secciones de error |
| :--- | ---: | ---: |
| 0 | 30 | 10 |
| 1 | 29 | 18 |
| 2 | 16 | 11 |
| 3 | 18 | 14 |
| 9 | 30 | 26 |

La Regla 12 exige una sección por bloque o comando. Hay bastantes bloques sin ella, sobre todo en el Capítulo 0.

### 3.5. Faltan logs reales en muchos síntomas

La Regla 9 exige transcribir el mensaje real de `journalctl` o Looking Glass. Varias entradas usan descripciones genéricas o síntomas visuales, por ejemplo:

- `manual/03-interfaz.md:148`: “el botón se ve, pero no tiene icono”.
- `manual/05-persistencia.md:466`: “el archivo se genera, pero la extensión no lo encuentra”.

No siempre hay log textual ni corrección con el log posterior.

### 3.6. El glosario no es “vivo”

`AGENT.md:338` pide un glosario con enlace desde cada aparición. Los capítulos no enlazan al glosario en ningún momento, y el glosario no tiene anclas por término. Es un glosario estático, no enlazado.

---

## 4. Posibles errores técnicos que conviene verificar

### 4.1. Empaquetado del esquema GSettings

El Capítulo 10 afirma que el ZIP debe incluir el XML y **no** `gschemas.compiled` (`manual/10-empaquetado.md:121-123,186-190`) y que `gnome-extensions pack` incluye el XML (`:353`). Hay que comprobar el comportamiento real:

- `gnome-extensions pack` detecta automáticamente la carpeta `schemas/`.
- Esa herramienta compila el esquema con `glib-compile-schemas`.
- El ZIP puede acabar incluyendo `schemas/gschemas.compiled`.

Si es así, el comando y el listado esperado del capítulo no coinciden con lo que el lector verá. Hay que probarlo y ajustar la explicación.

### 4.2. Requisito contradictorio del Capítulo 9

- `manual/09-apis.md:20` dice “Haber completado los capítulos 1 a 8”.
- `manual/apendices/MAPA-PREREQUISITOS.md:16` dice que el Capítulo 9 requiere “Capítulos 1 a 7”.
- El Capítulo 8 es opcional.

Debería decir “1 a 7” o “1 a 7, y opcionalmente 8”.

### 4.3. `console.log` en el código final

La Regla 3 pide no usar `console.log` como única depuración en el código final. El Capítulo 9 mantiene `console.log` en el código final (`manual/09-apis.md:611` y `894`). No es el único mecanismo, pero el Capítulo 10 no lo retira.

### 4.4. El título del Capítulo 9 promete más de lo que entrega

`MANUAL.md:239` dice “Clima, Divisas, Criptomonedas”, pero el capítulo implementa solo criptomonedas. El clima queda como ejercicio creativo y **divisas no aparece**. Conviene ajustar el título o añadir contenido.

### 4.5. La `url` del metadata final apunta al repositorio del manual

`manual/10-empaquetado.md:65` usa `https://github.com/perseoq/GNOME_MANUAl_CUSTOMIZATION` como URL de la extensión. Para EGO debería ser la URL del proyecto de la extensión del lector. Es un ejemplo que puede confundir.

---

## 5. Detalles menores de coherencia

- `DECISIONES.md:4` sigue diciendo “**Estado: Fase 1 implementada**”, aunque ya se redactaron las fases 2–7.
- `README.md:23` dice que el manual está “completo en contenido”, pero no menciona que el Capítulo 8 no está compilado ni que faltan capturas.
- `ENTORNO-PRUEBAS.md:171` deja `prefs.js` como “pendiente revisión visual”.
- Las decisiones pendientes de `DECISIONES.md:84-88` (licencia, capturas, traducciones, protección de `main`) siguen abiertas.

---

## 6. Lo que sí está completo

- Los 11 capítulos y los tres apéndices existen y están enlazados.
- **No hay enlaces internos rotos** (verificado con script).
- El UUID del proyecto es consistente (`miprimeraextension@ejemplo.local`).
- No hay `var`, `eval` ni `imports.*` en el código de ejemplo.
- No hay carpetas `etapas/`, `ejemplos/`, ZIPs ni scripts prohibidos.
- El repositorio está limpio y sincronizado con `origin/main`.
- Hay evidencia de pruebas técnicas de la mayoría de capítulos en `ENTORNO-PRUEBAS.md`.
- Los apéndices de glosario, mapa de prerrequisitos e índice de soluciones existen y funcionan.

---

## 7. Cómo se hace el punto 3: pruebas con lectores noveles y checklists firmadas

### 7.1. Preparar el entorno y los lectores

1. Congela el contenido: no pruebes un capítulo que vas a reescribir después.
2. Prepara una cuenta limpia o una VM siguiendo `ENTORNO-PRUEBAS.md`:
   - Fedora 44, GNOME Shell 50.6, Wayland.
   - Sin extensiones de terceros.
   - Tema Adwaita y escalado al 100 %.
3. Elige lectores que cumplan el perfil real:
   - No haber programado nunca.
   - No conocer Linux a fondo.
   - No saber qué es GJS, St ni una extensión.
4. Para las métricas de `AGENT.md` necesitas como mínimo:
   - **3 lectores** para el proyecto completo.
   - **2 lectores** para el Capítulo 4.
   - **3 lectores** para el Capítulo 9.
   - Todos deben pasar por el Capítulo 0.

No uses al mismo lector para corregir y volver a probar: ya aprendió. La segunda prueba debe hacerse con otra persona.

### 7.2. Guion de la sesión

La regla principal: **no ayudas**. Solo observas y tomas notas.

1. Entrega al lector únicamente el capítulo y una máquina limpia.
2. Pídele que verbalice lo que piensa mientras avanza.
3. No corrijas errores ni respondas preguntas técnicas.
4. Si se atasca:
   - Anota la frase exacta del manual donde se detuvo.
   - Anota qué hizo.
   - Anota qué esperaba que ocurriera.
   - Anota qué ocurrió realmente.
5. Solo intervén si lleva más de 10–15 minutos bloqueado o si pide ayuda explícitamente. Ese punto también se registra como atasco.
6. Al terminar, haz una entrevista corta:
   - “¿Por qué funciona lo que acabas de hacer?”
   - “¿Qué harías si el botón no aparece?”
   - “¿Qué parte del código no entiendes?”

### 7.3. Registrar cada prueba

Crea un informe interno por capítulo, no publicado dentro del manual. Puede ser una tabla como esta:

| Campo | Ejemplo |
| :--- | :--- |
| Fecha | 2026-10-20 |
| Capítulo | 3 |
| Commit | `abc1234` |
| Lector | L1, sin experiencia en programación |
| Entorno | Fedora 44, GNOME 50.6, Wayland |
| Punto exacto | “Ahora crea el botón”, línea 104 |
| Qué hizo | Abrió `metadata.json` en lugar de `extension.js` |
| Qué esperaba | Pegar el bloque y recargar la extensión |
| Qué ocurrió | La extensión no apareció en el panel |
| Atasco | No sabía en qué archivo iba el bloque |
| Causa | El manual no indica el archivo antes del bloque |
| Corrección | Añadir “Este bloque va dentro de `extension.js`, en `enable()`” |
| Re-prueba | L2 completó el paso sin ayuda |

Guarda también:

- El log real de `journalctl` si hubo error.
- Capturas de pantalla si el problema fue visual.
- La frase textual del manual que provocó la duda.

### 7.4. Corregir y volver a probar

Cada atasco se corrige con una de estas cuatro acciones:

- Añadir una analogía.
- Añadir un paso intermedio.
- Añadir un ejercicio de verificación.
- Corregir el código o la instrucción.

Después se repite la prueba con otro lector. El capítulo no se cierra hasta que el lector:

- Completa la etapa sin ayuda externa.
- Sabe explicar por qué funciona.
- No se atasca en el Capítulo 0.

### 7.5. Firmar la checklist

`CHECKLIST-CALIDAD.md` es una plantilla. Para que quede constancia real, crea una copia por capítulo, por ejemplo en una carpeta interna:

```text
internal/qa/checklist-00.md
internal/qa/checklist-01.md
...
```

Si el repositorio es público y no quieres publicar los informes, mantén esa carpeta en un repositorio privado o en una rama local, pero no la enlaces desde `MANUAL.md`.

En cada copia:

1. Marca `[x]` en las secciones 1–8.
2. En la sección 7, indica el lector, la fecha y el resultado.
3. En la sección 9, marca el commit y el push.
4. Completa la firma de cierre:

| Campo | Valor |
| :--- | :--- |
| Fase | 3 |
| Fecha | 2026-10-20 |
| Commit | `abc1234` |
| Remoto | `origin/main` |
| Revisor | L1 / L2 |
| Resultado | Aprobado |
| Observaciones | Corregidos 3 atascos y re-probado |

Sin esa firma, el capítulo no está cerrado, aunque el texto esté redactado.

---

## 8. Cómo se hace el punto 4: correcciones de cumplimiento

### 8.1. Reordenar la estructura de cada capítulo

La plantilla exige este orden:

```text
Qué vas a aprender
Antes de empezar necesitas
El concepto, con una analogía
Ejemplo guiado paso a paso
Verifica que funciona
Errores de integración del capítulo
Ejercicios
Autoevaluación
Respuestas razonadas
Soluciones de los ejercicios
Si quieres saber más
```

Hay que hacerlo capítulo por capítulo:

| Capítulo | Acción |
| :--- | :--- |
| **0** | Añadir “Ejemplo guiado paso a paso” para las prácticas de terminal, archivos y editor. Si se considera un capítulo conceptual, documentar la excepción en `RULES.md` con justificación. |
| **1** | Mover los bloques de código de 1.1–1.3 a “Ejemplo guiado paso a paso” y numerarlos como `Bloque 1`, `Bloque 2`, etc. |
| **3** | Igual: mover los bloques de 3.2–3.4 al ejemplo guiado y numerarlos. |
| **7** | Añadir la sección “Ejemplo guiado paso a paso” y numerar los bloques de 7.1–7.4. |
| **8** | Añadir “Ejemplo guiado” o cambiar el alcance del capítulo y corregir `AGENT.md` y `MANUAL.md`. |

No basta con renombrar títulos: cada bloque debe tener delante una frase que diga **de qué archivo forma parte, qué implementa y qué recibe/devuelve**.

### 8.2. Añadir el código que falta en los bloques

- **Capítulo 5, Bloque 4** (`manual/05-persistencia.md:598`): insertar el `prefs.js` completo, con imports, clase, página, grupo, fila, `Gtk.Entry` y `settings.bind()`. Ahora solo remite a la sección 5.3.
- **Capítulo 9, Bloque 1** (`manual/09-apis.md:513`): insertar el código completo de `providers/binanceProvider.js` dentro del bloque, no solo la referencia a 9.3.1.
- **Capítulo 10, Bloque 1** (`manual/10-empaquetado.md:314`): insertar el `metadata.json` final como bloque de código.
- Revisar todos los bloques que solo dicen “usa el código de la sección X”: si el lector tiene que buscar en otra sección, el bloque no es autónomo.

### 8.3. Restaurar la autonomía de los capítulos 5, 6 y 9

Aquí no basta con añadir un párrafo. Hay que repetir el código de partida completo.

**Capítulo 6:** antes de las modificaciones, incluir un bloque “Punto de partida” con el `extension.js` completo que el lector debería tener al terminar el Capítulo 5:

- Imports de `Main`, `PanelMenu`, `St` y `Extension`.
- Clase `MiPrimeraExtension`.
- `enable()` que crea el indicador, el icono y registra el botón.
- `disable()` que destruye el indicador.
- Después, los bloques que añaden `Meta`, la acción de menú y `_moverVentana()`.

**Capítulo 9:** incluir el `extension.js` completo de partida y luego los bloques que añaden:

- `Soup.Session`.
- El proveedor.
- La etiqueta del precio.
- El temporizador.
- La limpieza en `disable()`.

**Capítulo 5:** incluir el `extension.js` de partida y, además, ensamblar el `prefs.js` completo. Ahora el lector tiene piezas en 5.3.1–5.3.4, pero nunca ve el archivo terminado.

La duplicación está permitida por la Regla 7 precisamente para esto.

### 8.4. Completar “Errores posibles en este bloque”

Para cada bloque de código o comando, añade tres errores como mínimo:

1. **Sintaxis o tipado:** el código no carga.
2. **Lógica:** carga, pero hace otra cosa.
3. **Integración:** funciona solo, pero falla al combinarse con otro bloque o con Wayland.

Plantilla:

```markdown
**Errores posibles en este bloque:**

**Error 1: `[mensaje real del log]`**
- **Ubicación:** archivo y línea.
- **Síntoma:** lo que aparece en `journalctl` o en pantalla.
- **Causa:** por qué ocurre en este bloque.
- **Corrección:** qué cambiar exactamente.
- **Log después de corregir:** `[texto real]`
```

Cómo obtener el log real:

1. Abre una terminal con:

```bash
journalctl /usr/bin/gnome-shell -f -o cat
```

2. Provoca el error a propósito:
   - Borra un punto y coma.
   - Escribe mal una ruta.
   - Cambia el nombre de una propiedad.
   - Desactiva una dependencia.
3. Copia el mensaje **exacto**, sin resumirlo.
4. Corrige y vuelve a capturar el log limpio.
5. Si el error es visual y no genera log, usa una captura o el resultado de Looking Glass y explica por qué no hay log.

Los bloques que ahora no tienen sección de errores están sobre todo en:

- Capítulo 0: unos 30 bloques y solo 10 secciones.
- Capítulo 1: unos 29 bloques y 18 secciones.
- Capítulo 2: unos 16 bloques y 11 secciones.
- Capítulo 3: unos 18 bloques y 14 secciones.
- Capítulo 9: unos 30 bloques y 26 secciones.

No hace falta inventar errores imposibles: si un bloque es solo una explicación, se indica “este bloque no produce errores por sí mismo” y se remite al bloque del que depende.

### 8.5. Corregir los puntos técnicos concretos

- **Capítulo 10, esquema GSettings:** probar realmente qué contiene el ZIP que genera `gnome-extensions pack`. Ajustar la explicación según el resultado, porque el capítulo dice que incluye el XML y no `gschemas.compiled`, pero la herramienta compila los esquemas de `schemas/`. Hay que verificar también qué exige EGO para GNOME 45+.
- **Capítulo 9, requisito:** cambiar “capítulos 1 a 8” por “capítulos 1 a 7” o “1 a 7, y opcionalmente 8”, para que coincida con `MAPA-PREREQUISITOS.md`.
- **Capítulo 9, `console.log`:** eliminar o justificar el `console.log` del código final; la Regla 3 pide no usarlo como única depuración en el código definitivo.
- **Capítulo 9, título:** o se añade contenido real de clima y divisas, o se cambia el título de `MANUAL.md` para no prometer lo que no se enseña.
- **Glosario vivo:** añadir anclas a los términos y enlazar al menos la primera aparición de cada capítulo. Si no se quiere enlazar cada aparición, hay que modificar `AGENT.md` y documentar la excepción.

---

## 9. Orden recomendado de trabajo

No hagas las pruebas de lector antes de arreglar la estructura, porque estarías probando un texto que va a cambiar.

1. **Correcciones estructurales:** secciones que faltan, bloques sin código, autonomía de 5, 6 y 9.
2. **Pasada de errores y logs:** completar secciones y capturar logs reales.
3. **Correcciones técnicas puntuales:** Capítulo 10, Capítulo 9, `console.log`, glosario.
4. **Pruebas con lectores noveles:** capítulos 0–4 primero, luego 5–7, luego 8–10.
5. **Correcciones de los atascos y re-prueba.**
6. **Checklists firmadas y cierre de fase.**

---

## 10. Checklist de ejecución

### Bloqueantes

- [ ] Elegir y añadir `LICENSE`.
- [ ] Cerrar las decisiones pendientes de `DECISIONES.md`.
- [ ] Añadir capturas y diagramas del Capítulo 0 y de la interfaz.
- [ ] Verificar o redefinir el alcance del Capítulo 8.
- [ ] Crear los informes de prueba por capítulo.
- [ ] Firmar las checklists por capítulo y por fase.

### Cumplimiento de plantilla

- [ ] Añadir `## Ejemplo guiado paso a paso` en los capítulos 0, 1, 3, 7 y 8.
- [ ] Numerar los bloques como `### Bloque N` en los capítulos 0, 1, 3, 7 y 8.
- [ ] Insertar el código completo en los bloques 5.4, 9.1 y 10.1.
- [ ] Repetir el código de partida completo en los capítulos 5, 6 y 9.
- [ ] Ensamblar el `prefs.js` completo del Capítulo 5.
- [ ] Añadir “Errores posibles en este bloque” a todos los bloques que falten.
- [ ] Sustituir síntomas genéricos por logs reales.
- [ ] Añadir anclas y enlaces al glosario.

### Correcciones técnicas

- [ ] Verificar el contenido real del ZIP del Capítulo 10.
- [ ] Corregir el requisito del Capítulo 9.
- [ ] Retirar o justificar `console.log` en el código final.
- [ ] Ajustar el título del Capítulo 9.
- [ ] Sustituir la URL de ejemplo del metadata por una instrucción genérica.

### Pruebas

- [ ] Probar el Capítulo 0 con 3 lectores.
- [ ] Probar los capítulos 1–4 con 2 lectores.
- [ ] Probar los capítulos 5–7 con 2 lectores.
- [ ] Probar los capítulos 8–10 con 3 lectores.
- [ ] Registrar cada atasco con la frase exacta.
- [ ] Corregir y re-probar con lectores distintos.
- [ ] Completar las métricas de `AGENT.md`.

---

## 11. Conclusión

**No falta ningún capítulo.** El manual está redactado, pero no está cerrado editorialmente.

Para considerarlo terminado faltan:

1. **Licencia y decisiones editoriales cerradas.**
2. **Capturas y diagramas**, sobre todo en el Capítulo 0 y en la interfaz.
3. **Pruebas reales con lectores noveles y checklists firmadas.**
4. **Correcciones de cumplimiento:** secciones de plantilla, bloques sin código, autonomía de los capítulos 5, 6 y 9, y errores sin log real.

Además, hay que verificar el empaquetado del esquema GSettings del Capítulo 10 y aclarar el alcance real del Capítulo 8.
