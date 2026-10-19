# Plantilla de Capítulo

**Documento interno de producción.**
**Uso:** copia este archivo, renómbralo con el número y el título del capítulo, y sustituye los textos entre corchetes. No publiques la plantilla como si fuera un capítulo.

Antes de escribir, revisa `CHECKLIST-CALIDAD.md`. Antes de cerrar el capítulo, vuelve a revisarlo.

---

**A partir de aquí empieza la plantilla del capítulo.**

# Capítulo N: [Título amigable, no técnico]

## Qué vas a aprender

- [Objetivo 1 en lenguaje llano.]
- [Objetivo 2.]
- [Objetivo 3.]

Al final de este capítulo, podrás [resultado visible y concreto].

## Antes de empezar necesitas

- [Requisito del capítulo anterior, con enlace.]
- [Herramienta o archivo necesario.]
- [Si falta algo, enlace al capítulo donde se aprendió.]

## El concepto, con una analogía

[Analogía cotidiana. Sin código. Máximo tres párrafos cortos. Explica qué es el concepto, con qué se compara y por qué la comparación ayuda.]

[Define el término técnico en su primer uso. No lo uses antes de definirlo.]

## Ejemplo guiado paso a paso

Cada bloque de código tiene una responsabilidad. El código se presenta en bloques pequeños, con la explicación fuera del bloque. No se presenta un programa completo en un único bloque.

### Bloque 1: [Responsabilidad del bloque]

[Indica de qué archivo forma parte, qué función o clase implementa y qué recibe y devuelve.]

```javascript
// [Código del bloque. Los comentarios son puntuales.]
```

> **Qué hace este bloque:** [Explicación textual. Recorre la lógica línea por línea o por grupos coherentes. Explica por qué está aquí y cómo se relaciona con el resto.]

**Errores posibles en este bloque:**

**Error 1: `[mensaje real del log]`**
- **Ubicación:** [archivo y línea donde ocurre].
- **Síntoma:** [qué ve el lector en el log o en pantalla].
- **Causa:** [por qué ocurre en este bloque].
- **Corrección:** [qué cambiar exactamente; incluye el fragmento corregido y el log posterior sin el error].

**Error 2: [nombre del error]**
- **Ubicación:** [archivo y línea].
- **Síntoma:** [texto real].
- **Causa:** [explicación].
- **Corrección:** [cambio exacto].

**Error 3: [nombre del error]**
- **Ubicación:** [archivo y línea].
- **Síntoma:** [texto real].
- **Causa:** [explicación].
- **Corrección:** [cambio exacto].

[Incluye al menos un error de sintaxis o tipado, uno de lógica y uno de integración cuando el bloque pueda producirlos. Si el bloque no puede fallar de forma aislada, indícalo y remite al bloque del que depende. Si un error es difícil de reproducir, explica cómo provocarlo.]

### Bloque 2: [Responsabilidad del bloque]

[Indica de qué archivo forma parte y qué responsabilidad tiene.]

```javascript
// [Código del bloque.]
```

> **Qué hace este bloque:** [Explicación textual.]

**Errores posibles en este bloque:**

**Error 1: [nombre del error]**
- **Ubicación:** [archivo y línea].
- **Síntoma:** [texto real].
- **Causa:** [explicación].
- **Corrección:** [cambio exacto].

### Guarda y recarga

[Indica exactamente dónde guardar el archivo y cómo recargar la extensión. En Wayland, explica si basta con desactivar y activar o si hace falta cerrar sesión.]

## Verifica que funciona

[Indica qué debe aparecer en pantalla o en los logs. Incluye la captura o el texto real del log. Si el resultado no coincide, indica qué revisar.]

## Errores de integración del capítulo

[Recoge de dos a cuatro errores que aparecen al combinar los bloques o al ejecutarlos en el entorno real. Los errores específicos de cada bloque ya están documentados arriba.]

### Error 1: [nombre del error]

[Log real transcrito en texto.]
[Explica por qué ocurre al integrar bloques o el entorno y cómo corregirlo.]

### Error 2: [nombre del error]

[Log real transcrito en texto.]
[Explica por qué ocurre y cómo corregirlo.]

## Ejercicios

### Reproductivo

[Copia exacta y verificación. El lector debe obtener el mismo resultado que en el ejemplo guiado.]

### Aplicativo

[Indica qué parámetro cambiar y qué resultado esperar.]

### Creativo

[Objetivo y pistas, sin código completo. Si no hay solución única, incluye una rúbrica.]

## Autoevaluación

1. [Pregunta de comprensión.]
2. [Pregunta de diagnóstico: “Si ocurre X, ¿qué revisarías primero y por qué?”]
3. [Pregunta de transferencia: “Si quisieras Y, ¿qué parte cambiarías?”]
4. [Pregunta opcional de comprensión.]

## Respuestas razonadas

[Incluye la respuesta de cada pregunta y explica por qué es correcta. No te limites a decir “verdadero” o “falso”.]

## Soluciones de los ejercicios

[Publica la solución del ejercicio reproductivo y del aplicativo. Para el creativo, publica una solución de ejemplo o una rúbrica con criterios objetivos.]

## Si quieres saber más

[Enlaces opcionales a documentación técnica. Añade la advertencia: “No necesitas esto para continuar.”]

---

## Reglas de esta plantilla

- Cada bloque de código tiene una responsabilidad y una explicación textual fuera del bloque.
- Cada bloque incluye su sección “Errores posibles en este bloque” con ubicación, síntoma, causa y corrección.
- Los errores de integración se separan de los errores por bloque.
- Cada concepto nuevo tiene una verificación.
- Cada ejercicio tiene solución publicada o rúbrica.
- Cada término técnico se define en su primer uso.
- El capítulo se puede leer de forma aislada; si necesita código de otro capítulo, se repite completo.
- No se presenta un programa completo en un único bloque.
- No se crean archivos de ejemplo, etapas descargables, ZIPs ni scripts fuera del manual.
