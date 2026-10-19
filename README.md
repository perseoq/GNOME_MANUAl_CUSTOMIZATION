# Manual de Extensiones Nativas para GNOME Shell 50.6

Proyecto documental para enseñar, desde cero, a crear extensiones nativas de GNOME Shell 50.6 sin usar extensiones de terceros.

El manual está dirigido a personas que nunca han programado y que no conocen JavaScript, CSS ni el funcionamiento interno de GNOME.

---

## Estado actual

**Fase 1: Preparación** implementada, pendiente de revisión del autor.

**Fase 2: Capítulo Introductorio** redactado, pendiente de la prueba con un lector novel.

**Fase 3: Capítulos 1 y 2** redactados, pendientes de la prueba con un lector novel.

**Fase 4: Capítulos 3 y 4** redactados, pendientes de la prueba con un lector novel.

**Fase 5: Capítulos 5 y 6** redactados, pendientes de la prueba con un lector novel.

**Fase 6: Capítulos 7 y 8** redactados, pendientes de la prueba con un lector novel. El Capítulo 8 es opcional.

**Fase 7: Capítulos 9 y 10, apéndices** redactados. El manual está completo en contenido, pendiente de la prueba con lectores noveles y de la publicación final.

---

## Estructura del repositorio

| Archivo | Función |
| :--- | :--- |
| `MANUAL.md` | Índice editorial detallado del manual |
| `docs/index.md` | Índice navegable con enlaces a cada capítulo y sección |
| `docs/00-introductorio.md` | Capítulo Introductorio completo |
| `docs/01-arquitectura.md` | Capítulo 1: arquitectura y fundamentos |
| `docs/02-modulos.md` | Capítulo 2: módulos y API interna |
| `docs/03-interfaz.md` | Capítulo 3: interfaz con St y Clutter |
| `docs/04-css.md` | Capítulo 4: estilos CSS en St |
| `docs/05-persistencia.md` | Capítulo 5: GSettings y preferencias |
| `docs/06-sistema.md` | Capítulo 6: Meta, ventanas y espacios |
| `docs/07-monkey-patching.md` | Capítulo 7: monkey patching y depuración |
| `docs/08-c.md` | Capítulo 8: C y GObject Introspection (opcional) |
| `docs/09-apis.md` | Capítulo 9: APIs externas con Soup 3 |
| `docs/10-empaquetado.md` | Capítulo 10: empaquetado y publicación |
| `docs/apendices/GLOSARIO.md` | Glosario de términos |
| `docs/apendices/MAPA-PREREQUISITOS.md` | Dependencias entre capítulos |
| `docs/apendices/SOLUCIONES.md` | Índice de soluciones |
| `RULES.md` | Reglas obligatorias de producción |
| `AGENT.md` | Especificación pedagógica y procedimiento de trabajo |
| `PERFIL-LECTOR.md` | Perfil y vacíos del lector objetivo |
| `DECISIONES.md` | Decisiones cerradas y pendientes del proyecto |
| `ENTORNO-PRUEBAS.md` | Entorno reproducible y protocolo de pruebas |
| `PLANTILLA-CAPITULO.md` | Plantilla obligatoria de cada capítulo |
| `CHECKLIST-CALIDAD.md` | Verificación antes de cerrar un capítulo o una fase |

---

## Plan de producción

1. Preparación.
2. Capítulo Introductorio.
3. Capítulos 1–2: fundamentos y módulos.
4. Capítulos 3–4: interfaz y CSS.
5. Capítulos 5–6: preferencias y sistema.
6. Capítulos 7–8: técnicas avanzadas.
7. Capítulos 9–10, apéndices y publicación.

Cada fase termina con las pruebas de calidad y con un push al repositorio remoto.

---

## Entorno de referencia

El manual no exige Fedora; este es el entorno usado para validar los ejemplos.

- Fedora Linux 44 Workstation.
- GNOME Shell 50.6.
- GJS 1.88.1.
- Wayland.
- Tema Adwaita para las capturas.
- Escalado al 100 % para las capturas principales.

Los detalles y el procedimiento de prueba están en `ENTORNO-PRUEBAS.md`.

---

## Cómo colaborar

1. Lee `RULES.md` y `AGENT.md`.
2. Revisa `PERFIL-LECTOR.md` antes de escribir.
3. Copia `PLANTILLA-CAPITULO.md` para el capítulo que vayas a redactar.
4. Completa `CHECKLIST-CALIDAD.md` antes de proponer el cierre.
5. No crees archivos de ejemplo, etapas descargables ni ZIPs fuera del manual.

---

## Licencia

Pendiente de decisión. Se documentará en `DECISIONES.md` antes de la publicación final.
