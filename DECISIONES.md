# Decisiones del Proyecto

**Documento interno de producción.**
**Estado:** Fase 1 implementada; pendiente de revisión del autor.

---

## 1. Jerarquía documental

Si hay conflicto entre documentos, prevalece este orden:

1. `RULES.md`
2. `AGENT.md`
3. `MANUAL.md`
4. El resto de documentos de producción

Ninguna decisión de este archivo puede contradecir `RULES.md`. Si una excepción es necesaria, se documenta aquí y la aprueba el autor principal.

---

## 2. Decisiones cerradas

| Área | Decisión | Motivo |
| :--- | :--- | :--- |
| Público | Personas sin experiencia en programación, Linux, JavaScript, CSS ni GNOME | Es la audiencia definida en `AGENT.md` |
| Producto | Manual pedagógico, no una extensión ni un curso en video | El repositorio contiene documentación |
| Shell objetivo | GNOME Shell 50.6 | Es el entorno de referencia disponible |
| Sesión | Wayland | Es el entorno por defecto de GNOME 50 |
| `shell-version` | `["50"]` | GNOME usa la versión mayor para validar extensiones |
| Código de ejemplo | Vive únicamente dentro del manual | Regla 1 de `RULES.md` |
| Etapas del proyecto | Se reconstruyen transcribiendo el código del capítulo | Regla 2 de `RULES.md` |
| Estructura del código | Bloques pequeños con explicación textual y errores por bloque | Reglas 11 y 12 de `RULES.md` |
| Idiomas del manual | Español | Audiencia hispanohablante |
| Tratamiento | Segunda persona, “tú” | Estilo directo y cercano |
| Comillas y estilo | Comillas simples, punto y coma, indentación de cuatro espacios | Regla 8 de `RULES.md` |
| Nombres de variables | Descriptivos; una sola letra únicamente en contadores triviales explicados | Regla 8 de `RULES.md` |
| Pruebas | Cada capítulo se prueba con un lector novel antes de publicarse | Regla 10 de `RULES.md` |
| Errores | Log real transcrito, con ubicación, síntoma, causa y corrección | Regla 9 de `RULES.md` |
| Publicación | Push al final de cada fase a `https://github.com/perseoq/GNOME_MANUAl_CUSTOMIZATION.git` | Regla 13 de `RULES.md` |
| Rama principal | `main` | Rama creada en el repositorio remoto |
| Capítulo inicial | “Capítulo Introductorio”, numeración interna 0 | Separa los fundamentos sin romper la numeración 1–10 |
| Licencia | Pendiente de decisión | Debe elegirse antes de publicar el manual |
| Distribución de referencia | Fedora Linux 44 Workstation | Es el entorno de pruebas; el manual no exige una distribución concreta |
| Navegador y herramientas | GNOME Text Editor, Files, Terminal y un navegador | Herramientas disponibles en una instalación GNOME estándar |

---

## 3. Estructura de archivos

Los documentos de producción viven en la raíz del repositorio. Los capítulos redactados se guardan en `manual/`:

```text
AGENT.md                 Especificación pedagógica
RULES.md                 Reglas obligatorias
MANUAL.md                Índice del manual
PERFIL-LECTOR.md         Perfil del público
DECISIONES.md            Decisiones del proyecto
ENTORNO-PRUEBAS.md       Entorno y protocolo de pruebas
PLANTILLA-CAPITULO.md    Plantilla de capítulo
CHECKLIST-CALIDAD.md     Lista de verificación
README.md                Presentación del repositorio
manual/00-introductorio.md  Capítulo Introductorio completo
manual/01-arquitectura.md    Capítulo 1: arquitectura
manual/02-modulos.md         Capítulo 2: módulos
manual/03-interfaz.md        Capítulo 3: interfaz
manual/04-css.md             Capítulo 4: estilos
manual/05-persistencia.md    Capítulo 5: preferencias
manual/06-sistema.md         Capítulo 6: Meta y espacios
```

Los capítulos se crean en `manual/` y `MANUAL.md` sigue siendo el índice enlazado. No se crearán carpetas de etapas ni de ejemplos ejecutables.

---

## 4. Decisiones pendientes

| Decisión | Estado | Cuándo se resuelve |
| :--- | :--- | :--- |
| Licencia del manual | Pendiente | Antes de publicar la fase 7 |
| Distribución exacta que se documentará como referencia | Fedora 44 verificada; confirmar si se añade Ubuntu | Antes de publicar el manual |
| Herramienta de capturas en Wayland | Pendiente | Antes de añadir capturas a los capítulos 3 y 4 |
| Política de traducciones | Pendiente | Después de la fase 7 |
| Protección de la rama `main` | Pendiente | Antes de aceptar colaboraciones externas |

---

## 5. Historial

| Fecha | Cambio | Autor |
| :--- | :--- | :--- |
| 2026-10-19 | Decisiones iniciales de la fase 1 | Equipo del manual |
| 2026-10-19 | Capítulo Introductorio redactado (fase 2); pendiente de prueba con lector novel | Equipo del manual |
| 2026-10-19 | Capítulos 1 y 2 redactados (fase 3); pendientes de prueba con lector novel | Equipo del manual |
| 2026-10-19 | Capítulos 3 y 4 redactados (fase 4); pendientes de prueba con lector novel | Equipo del manual |
| 2026-10-19 | Capítulos 5 y 6 redactados (fase 5); pendientes de prueba con lector novel | Equipo del manual |
