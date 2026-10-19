# Checklist de Calidad

**Documento interno de producción.**
**Uso:** se completa antes de cerrar cada capítulo y cada fase. Si queda un elemento sin marcar, el capítulo o la fase no se publica.

---

## 1. Antes de escribir

- [ ] El capítulo tiene un objetivo visible y concreto.
- [ ] El perfil del lector está revisado en `PERFIL-LECTOR.md`.
- [ ] Los requisitos del capítulo anterior están identificados.
- [ ] Los términos técnicos nuevos están definidos antes de usarse.
- [ ] El capítulo se puede leer de forma aislada; si necesita código de otro capítulo, se repetirá completo.

## 2. Estructura obligatoria

- [ ] Título amigable, no técnico.
- [ ] Sección “Qué vas a aprender” con 3 a 5 objetivos.
- [ ] Sección “Antes de empezar necesitas”.
- [ ] Sección “El concepto, con una analogía”.
- [ ] Sección “Ejemplo guiado paso a paso”.
- [ ] Sección “Verifica que funciona”.
- [ ] Sección “Errores de integración del capítulo”.
- [ ] Sección “Ejercicios” con reproductivo, aplicativo y creativo.
- [ ] Sección “Autoevaluación” con al menos una pregunta de diagnóstico.
- [ ] Sección “Respuestas razonadas”.
- [ ] Sección “Soluciones de los ejercicios”.
- [ ] Sección “Si quieres saber más”, con la advertencia de que es opcional.

## 3. Código y bloques

- [ ] Todo el código está dentro del manual; no se delega a archivos externos, repositorios, ZIPs o enlaces.
- [ ] No se crearon carpetas `etapas/`, `ejemplos/` ni scripts que el lector deba descargar o ejecutar.
- [ ] El código está dividido en bloques pequeños por responsabilidad.
- [ ] Cada bloque indica de qué archivo forma parte y qué implementa.
- [ ] Cada bloque tiene una explicación textual fuera del código.
- [ ] Los comentarios dentro del código son puntuales y no sustituyen la explicación.
- [ ] El código usa `let` o `const`; no usa `var`.
- [ ] No usa `imports.*` obsoleto.
- [ ] No usa `eval`.
- [ ] No silencia errores con `try { } catch { }` vacíos.
- [ ] No hardcodea claves de API ni rutas absolutas del autor.
- [ ] Los nombres de variables y funciones son descriptivos.
- [ ] Las comillas son simples, los puntos y coma son consistentes y la indentación es de cuatro espacios.
- [ ] El código se ejecuta en GNOME Shell 50.6 sobre Wayland tal como está escrito.
- [ ] Los recursos creados se destruyen o cancelan en `disable()`.
- [ ] Las operaciones que pueden fallar tienen manejo de errores explícito.
- [ ] `console.log` no es la única forma de depuración en el código final.

## 4. Errores y logs

- [ ] Cada bloque tiene su sección “Errores posibles en este bloque”.
- [ ] Cada error incluye ubicación, síntoma, causa y corrección.
- [ ] El síntoma usa el texto real del log o de Looking Glass.
- [ ] La corrección incluye el fragmento corregido cuando aplica.
- [ ] Se muestra el log posterior sin el error.
- [ ] Los errores cubren sintaxis o tipado, lógica e integración cuando el bloque puede producirlos.
- [ ] Los errores son específicos del bloque, no genéricos del capítulo.
- [ ] Si un error depende del entorno, se indica.
- [ ] Si un error es difícil de reproducir, se explica cómo provocarlo.
- [ ] La sección de integración del capítulo no sustituye los errores por bloque.

## 5. Ejercicios y soluciones

- [ ] Hay un ejercicio reproductivo con instrucción exacta y verificación.
- [ ] Hay un ejercicio aplicativo con un parámetro controlado.
- [ ] Hay un ejercicio creativo con objetivo y pistas.
- [ ] Cada ejercicio tiene solución publicada o rúbrica.
- [ ] Las soluciones están separadas de los enunciados.
- [ ] El capítulo indica que no se debe intentar el creativo si no se completó el reproductivo.

## 6. Autoevaluación

- [ ] Hay entre 3 y 5 preguntas.
- [ ] Al menos una pregunta es de comprensión.
- [ ] Al menos una pregunta es de diagnóstico.
- [ ] Al menos una pregunta es de transferencia.
- [ ] Las respuestas están razonadas, no únicamente con la solución.
- [ ] La autoevaluación se responde sin consultar el capítulo.

## 7. Verificación con lector novel

- [ ] Una persona sin experiencia previa completó el capítulo sin ayuda externa.
- [ ] Se registró cada atasco con el capítulo y la frase exacta.
- [ ] Cada atasco se corrigió con una analogía, un paso intermedio o un ejercicio.
- [ ] Se repitió la prueba hasta completar el capítulo sin asistencia.
- [ ] El informe de la prueba se conserva como documentación interna.
- [ ] El lector puede explicar por qué funciona el resultado, no únicamente reproducirlo.

## 8. Fuente de verdad y limpieza

- [ ] El manual no depende de documentación externa para continuar.
- [ ] Los enlaces externos aparecen únicamente en “Si quieres saber más”.
- [ ] No hay secretos, claves de API, rutas personales ni datos privados.
- [ ] No hay archivos temporales, borradores ni artefactos de prueba en el repositorio.
- [ ] El capítulo no introduce dos conceptos nuevos en el mismo párrafo.
- [ ] No se usan fórmulas que minimicen la dificultad: “simplemente”, “solo hay que”, “obviamente” o “como sabrás”.
- [ ] Cada comando de terminal se explica antes de usarse.

## 9. Cierre de fase

- [ ] Todos los capítulos de la fase tienen esta checklist completa.
- [ ] Las pruebas con lector novel están registradas.
- [ ] Los logs reales están transcritos en el manual.
- [ ] El índice `MANUAL.md` está actualizado.
- [ ] No hay archivos prohibidos por la Regla 2.
- [ ] `git status` está limpio o únicamente contiene cambios intencionales.
- [ ] El commit describe la fase y los capítulos incluidos.
- [ ] El push a `origin` se completó correctamente.
- [ ] `git status -sb` muestra la rama sincronizada con el remoto.

## 10. Firma de cierre

| Campo | Valor |
| :--- | :--- |
| Fase | [Número y nombre] |
| Fecha | [AAAA-MM-DD] |
| Commit | [Hash] |
| Remoto | `origin/main` |
| Revisor | [Nombre] |
| Resultado | [Aprobado / Rechazado] |
| Observaciones | [Texto] |
