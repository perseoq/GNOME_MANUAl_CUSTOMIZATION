# Capítulo 10: Empaquetado y Compatibilidad con GNOME 50

> En este capítulo prepararás la extensión para instalarla en otra máquina y para publicarla. También harás las pruebas finales y revisarás los criterios de extensions.gnome.org.

## Qué vas a aprender

- Verificar `metadata.json` antes de empaquetar.
- Crear un ZIP instalable con `gnome-extensions pack`.
- Incluir módulos propios con `--extra-source`.
- Revisar el contenido del ZIP.
- Cumplir los criterios básicos de revisión de EGO.
- Probar la extensión en un entorno limpio.
- Completar la lista de publicación.

Al final del capítulo, tendrás un ZIP instalable y una lista de verificación para publicar.

## Antes de empezar necesitas

- Haber completado los capítulos 1 a 9.
- Tener la extensión funcionando con panel, preferencias y API.
- Saber activar, desactivar e inspeccionar el estado con `gnome-extensions`.
- Tener una carpeta de pruebas limpia o una máquina virtual.
- GNOME Shell 50.6 sobre Wayland.

## El concepto, con una analogía

Empaquetar una extensión es como preparar una maleta para un viaje. Solo llevas lo necesario: la ropa, los documentos y los adaptadores. Si metes borradores, herramientas de construcción o ropa que no usarás, la maleta pesa más y la revisión se complica.

El ZIP de la extensión debe tener los archivos en la raíz, no dentro de una carpeta extra. El lector debe poder descomprimirlo e instalar la extensión sin buscar archivos perdidos.

### 10.1. Verificación de metadatos

#### 10.1.1. `"shell-version": ["50"]` y versionado

El campo `shell-version` declara las versiones mayores de GNOME Shell compatibles. Para este manual:

```json
"shell-version": ["50"]
```

`50.6` es la versión puntual usada para probar, pero el metadata declara `"50"`. No escribas `"50.6"` en `shell-version`.

El campo `version` lo gestiona extensions.gnome.org. No lo fijes manualmente. Para una versión visible, usa `version-name`.

**Errores posibles en este bloque:** este bloque es una explicación de metadatos, no un programa. No produce errores por sí mismo. Si usas `"50.6"`, la extensión puede marcarse como incompatible.

#### 10.1.2. El campo `version` y su gestión por extensions.gnome.org

`version` es un número entero que la tienda de extensiones incrementa y controla. Si lo escribes, la tienda puede sobrescribirlo o producir un comportamiento inesperado. El manual no lo incluye.

`version-name` es un texto visible para el usuario, como `"1.0"`. Es opcional.

**Errores posibles en este bloque:** este bloque es una explicación, no un programa. No produce errores por sí mismo. Si publicas un `version` manual, la tienda puede reemplazarlo.

#### 10.1.3. Metadatos finales

Un `metadata.json` completo para el proyecto puede ser:

```json
{
    "uuid": "miprimeraextension@ejemplo.local",
    "name": "Mi Primera Extensión",
    "description": "Una extensión de práctica para aprender GNOME Shell.",
    "shell-version": ["50"],
    "url": "https://github.com/perseoq/GNOME_MANUAl_CUSTOMIZATION",
    "settings-schema": "org.gnome.shell.extensions.miprimeraextension",
    "version-name": "1.0"
}
```

> **Qué hace este bloque:** `uuid` identifica la extensión y coincide con la carpeta. `name` y `description` son visibles. `shell-version` declara GNOME 50. `url` apunta al repositorio y es obligatorio para EGO. `settings-schema` conecta con el esquema del Capítulo 5. `version-name` muestra la versión al usuario.

**Errores posibles en este bloque:**

**Error 1: `url` ausente.**
- **Ubicación:** `metadata.json`.
- **Síntoma:** EGO rechaza la publicación.
- **Causa:** el campo `url` es obligatorio para la tienda.
- **Corrección:** añade una URL válida del proyecto. Después de corregir, la publicación puede revisarse.

**Error 2: `settings-schema` no coincide.**
- **Ubicación:** `metadata.json` y esquema.
- **Síntoma:** la extensión no carga las preferencias.
- **Causa:** el identificador del metadata y el del XML son distintos.
- **Corrección:** usa el mismo identificador en los dos archivos. Después de corregir, `getSettings()` funciona.

**Error 3: `uuid` no coincide con la carpeta.**
- **Ubicación:** `metadata.json`.
- **Síntoma:** la extensión no aparece o se instala con otro nombre.
- **Causa:** el nombre de la carpeta y el `uuid` son distintos.
- **Corrección:** haz que coincidan exactamente. Después de corregir, la extensión se instala correctamente.

### 10.2. Empaquetado manual en ZIP

#### 10.2.1. Contenido en la raíz del ZIP

El ZIP debe contener los archivos en la raíz:

```text
metadata.json
extension.js
prefs.js
stylesheet.css
schemas/
providers/
```

Este bloque es un diagrama de archivos, no un programa. No se ejecuta y no produce errores por sí mismo.

Los archivos imprescindibles son `metadata.json` y `extension.js`. `prefs.js`, `stylesheet.css`, `schemas/` y `providers/` se incluyen cuando la extensión los usa.

#### 10.2.2. Exclusión de archivos innecesarios

No incluyas:

- `.git/` ni el historial del repositorio.
- Borradores y archivos temporales.
- `node_modules/`.
- Capturas de pantalla de trabajo.
- Claves de API.
- `schemas/gschemas.compiled` para EGO; la tienda compila el esquema a partir del XML.

El proyecto local puede conservar `gschemas.compiled` para pruebas. El ZIP de publicación debe incluir el XML del esquema y dejar que EGO lo compile.

#### 10.2.3. Crear el ZIP con `gnome-extensions pack`

Crea una carpeta de salida y empaqueta:

```bash
mkdir -p dist
gnome-extensions pack --force --extra-source=providers --out-dir dist .
```

> **Qué hace este bloque:** `mkdir -p dist` crea la carpeta de salida. `gnome-extensions pack` empaqueta la extensión. `--force` sobrescribe un ZIP anterior. `--extra-source=providers` incluye la carpeta `providers/`, que no se incluye automáticamente; si tu extensión no usa esa carpeta, omite la opción. `--out-dir dist` guarda el ZIP en `dist/`. El punto final indica que la extensión está en la carpeta actual.

Salida esperada:

```text
dist/miprimeraextension@ejemplo.local.shell-extension.zip
```

**Errores posibles en este bloque:**

**Error 1: `gnome-extensions: instrucción no encontrada...`.**
- **Ubicación:** terminal.
- **Síntoma:** no se crea el ZIP.
- **Causa:** GNOME Shell no está instalado o la herramienta no está en el `PATH`.
- **Corrección:** instala GNOME Shell o usa una sesión con las herramientas disponibles. Después de corregir, el comando funciona.

**Error 2: el ZIP no contiene `providers/`.**
- **Ubicación:** contenido del ZIP.
- **Síntoma:** la extensión instalada falla con `ImportError` al cargar el proveedor.
- **Causa:** olvidaste `--extra-source=providers`.
- **Corrección:** repite el comando con `--extra-source=providers`. Después de corregir, el proveedor está dentro del ZIP.

**Error 3: la carpeta `dist/` no existe.**
- **Ubicación:** `--out-dir dist`.
- **Síntoma:** el comando falla o no guarda el ZIP.
- **Causa:** la carpeta de salida no se creó.
- **Corrección:** ejecuta `mkdir -p dist` antes de empaquetar. Después de corregir, el ZIP se guarda.

**Error 4: el ZIP contiene una carpeta raíz extra.**
- **Ubicación:** contenido del ZIP.
- **Síntoma:** al instalar, la extensión no se reconoce.
- **Causa:** empaquetaste la carpeta contenedora en lugar de su contenido.
- **Corrección:** ejecuta `gnome-extensions pack` desde la carpeta de la extensión. Después de corregir, los archivos están en la raíz.

#### 10.2.4. Revisar el contenido del ZIP

Inspecciona el ZIP antes de instalarlo:

```bash
unzip -l dist/miprimeraextension@ejemplo.local.shell-extension.zip
```

> **Qué hace este bloque:** `unzip -l` lista el contenido del ZIP sin extraerlo. Deben aparecer `metadata.json`, `extension.js`, `prefs.js`, `stylesheet.css`, `schemas/...` y `providers/...`. No deben aparecer `.git`, temporales ni claves.

**Errores posibles en este bloque:**

**Error 1: falta un archivo necesario.**
- **Ubicación:** listado del ZIP.
- **Síntoma:** la instalación falla o la extensión no funciona.
- **Causa:** el archivo no estaba en la carpeta o no se incluyó con `--extra-source`.
- **Corrección:** añade el archivo y vuelve a empaquetar. Después de corregir, el archivo aparece.

**Error 2: aparece `gschemas.compiled`.**
- **Ubicación:** listado del ZIP.
- **Síntoma:** el ZIP contiene el archivo compilado.
- **Causa:** se incluyó la carpeta `schemas` completa con el compilado.
- **Corrección:** elimina `gschemas.compiled` del ZIP de publicación. EGO compila el esquema desde el XML. Después de corregir, únicamente aparece el XML.

**Error 3: aparece una clave de API.**
- **Ubicación:** listado o contenido del ZIP.
- **Síntoma:** el secreto está en el paquete.
- **Causa:** se hardcodeó una clave en un archivo.
- **Corrección:** revoca la clave, elimina el secreto y vuelve a empaquetar. Después de corregir, la clave no aparece.

### 10.3. Criterios para la revisión de EGO

#### 10.3.1. Código legible, sin ofuscación

EGO revisa que el código sea legible. No uses minificación, ofuscación ni nombres de una sola letra sin explicación. El manual aplica esta regla desde el Capítulo 1.

#### 10.3.2. `disable()` que limpia todos los recursos

La revisión comprueba que la extensión no deja señales, actores, temporizadores ni peticiones después de desactivar. Revisa:

- Señales desconectadas.
- Actores destruidos.
- Temporizadores eliminados.
- Peticiones abortadas.
- Monkey patches restaurados.

#### 10.3.3. Restauración obligatoria de monkey patches

Si la extensión parchea un método interno, debe restaurarlo en `disable()`. Un parche que sobrevive a la desactivación es un motivo de rechazo y un riesgo para el usuario.

#### 10.3.4. Otros criterios prácticos

| Criterio | Comprobación |
| :--- | :--- |
| `metadata.json` válido | UUID, nombre, descripción, shell-version y URL |
| Sin secretos | No hay claves en código, capturas ni logs |
| Sin dependencias externas innecesarias | No se exige instalar otra extensión |
| Esquema incluido | `schemas/*.gschema.xml` está en el ZIP |
| Código limpio | Sin `eval`, sin `var`, sin `imports.*` |
| Descripción clara | Explica qué hace y qué permisos usa |
| Licencia | El repositorio tiene una licencia compatible |

**Errores posibles en este bloque:** este bloque es una lista de criterios, no un programa. No produce errores por sí mismo. Si un criterio no se cumple, la revisión puede rechazar la extensión.

### 10.4. Pruebas finales en entorno limpio

#### 10.4.1. Activación/desactivación repetida sin errores en logs

Prueba cinco veces seguidas:

```bash
gnome-extensions disable miprimeraextension@ejemplo.local
gnome-extensions enable miprimeraextension@ejemplo.local
```

Después revisa el log:

```bash
journalctl /usr/bin/gnome-shell -n 200 -o cat
```

No debe haber errores de tu extensión. Tampoco deben aparecer iconos duplicados ni peticiones después de desactivar.

**Errores posibles en este bloque:**

**Error 1: aparecen iconos duplicados.**
- **Ubicación:** panel.
- **Síntoma:** cada activación añade un icono.
- **Causa:** `disable()` no destruye el indicador.
- **Corrección:** destruye el indicador y restaura las señales. Después de corregir, únicamente queda un icono.

**Error 2: aparecen errores de recursos.**
- **Ubicación:** log.
- **Síntoma:** mensajes sobre señales, actores o temporizadores.
- **Causa:** `disable()` no limpia todo.
- **Corrección:** revisa la lista de recursos del Capítulo 1. Después de corregir, el log queda limpio.

#### 10.4.2. Persistencia de preferencias tras logout/login

Cambia el color desde la ventana de preferencias, cierra sesión y vuelve a entrar. El color debe seguir ahí.

**Errores posibles en este bloque:**

**Error 1: el color vuelve al valor por defecto.**
- **Ubicación:** GSettings.
- **Síntoma:** la preferencia no persiste.
- **Causa:** se guardó en una variable de JavaScript o en otra clave.
- **Corrección:** usa `settings.set` o `settings.bind` con la clave correcta. Después de corregir, el valor persiste.

**Error 2: la ventana de preferencias no abre.**
- **Ubicación:** `prefs.js`.
- **Síntoma:** no aparece la ventana.
- **Causa:** el import de `ExtensionPreferences` o la ruta del esquema están mal.
- **Corrección:** revisa el Capítulo 5 y la ruta `Shell/Extensions`. Después de corregir, la ventana abre.

#### 10.4.3. Verificación en Wayland

El manual está probado en Wayland. Comprueba:

- La extensión se activa y desactiva.
- El panel muestra el icono y el precio.
- Las preferencias persisten.
- No se usan atajos de X11.
- No se intenta posicionar ventanas con coordenadas absolutas.

**Errores posibles en este bloque:** este bloque es una lista de verificación, no un programa. No produce errores por sí mismo. Si una operación de X11 no funciona, revisa el Capítulo 6 y usa la alternativa de Wayland.

### 10.5. Lista de publicación

Antes de publicar:

- [ ] La extensión se activa y desactiva cinco veces sin errores.
- [ ] El log no muestra excepciones de la extensión.
- [ ] El color persiste tras cerrar sesión.
- [ ] El ZIP contiene todos los archivos necesarios.
- [ ] El ZIP no contiene secretos ni temporales.
- [ ] `metadata.json` tiene UUID, nombre, descripción, `shell-version`, URL y `settings-schema`.
- [ ] El repositorio tiene licencia y README.
- [ ] La descripción de EGO explica qué hace la extensión.
- [ ] Las capturas no muestran datos privados.
- [ ] El commit está subido al repositorio remoto.

**Errores posibles en este bloque:** este bloque es una lista, no un programa. No produce errores por sí mismo. Si falta un elemento, la publicación queda incompleta.

## Ejemplo guiado paso a paso

### Bloque 1: revisar el metadata

Comprueba que `metadata.json` tiene el UUID correcto, `shell-version` `["50"]`, `url` y `settings-schema`. No añadas `version`.

**Errores posibles en este bloque:**

**Error 1: falta `url`.**
- **Ubicación:** `metadata.json`.
- **Síntoma:** EGO rechaza la publicación.
- **Causa:** el campo es obligatorio para la tienda.
- **Corrección:** añade la URL del repositorio. Después de corregir, la publicación puede revisarse.

**Error 2: `shell-version` contiene `"50.6"`.**
- **Ubicación:** `metadata.json`.
- **Síntoma:** la extensión se marca como incompatible.
- **Causa:** el metadata usa una versión puntual en lugar de la mayor.
- **Corrección:** usa `["50"]`. Después de corregir, la extensión es compatible.

### Bloque 2: crear el ZIP

```bash
mkdir -p dist
gnome-extensions pack --force --extra-source=providers --out-dir dist .
```

> **Qué hace este bloque:** crea la carpeta `dist` y empaqueta la extensión con el proveedor incluido. El ZIP queda listo para instalar o publicar.

**Errores posibles en este bloque:**

**Error 1: el ZIP no incluye `providers/`.**
- **Ubicación:** contenido del ZIP.
- **Síntoma:** la extensión falla al cargar el proveedor.
- **Causa:** falta `--extra-source=providers`.
- **Corrección:** repite el comando con la opción. Después de corregir, el proveedor está incluido.

**Error 2: el ZIP incluye `gschemas.compiled`.**
- **Ubicación:** contenido del ZIP.
- **Síntoma:** el paquete contiene el compilado.
- **Causa:** se añadió la carpeta `schemas` completa.
- **Corrección:** usa `gnome-extensions pack`, que incluye el XML y no el compilado. Después de corregir, el ZIP es correcto.

### Bloque 3: revisar el contenido

```bash
unzip -l dist/miprimeraextension@ejemplo.local.shell-extension.zip
```

> **Qué hace este bloque:** lista los archivos del ZIP. Comprueba que están `metadata.json`, `extension.js`, `prefs.js`, `stylesheet.css`, `schemas/` y `providers/`.

**Errores posibles en este bloque:**

**Error 1: falta `prefs.js`.**
- **Ubicación:** listado del ZIP.
- **Síntoma:** la ventana de preferencias no existe.
- **Causa:** el archivo no estaba en la carpeta.
- **Corrección:** crea `prefs.js` y vuelve a empaquetar. Después de corregir, aparece.

**Error 2: aparece un archivo temporal.**
- **Ubicación:** listado del ZIP.
- **Síntoma:** el paquete incluye basura.
- **Causa:** el archivo está en la carpeta y se añadió como extra.
- **Corrección:** elimina el temporal y vuelve a empaquetar. Después de corregir, el ZIP está limpio.

### Bloque 4: instalar y probar

```bash
gnome-extensions install --force dist/miprimeraextension@ejemplo.local.shell-extension.zip
gnome-extensions enable miprimeraextension@ejemplo.local
```

> **Qué hace este bloque:** instala el ZIP en la carpeta de extensiones del usuario y activa la extensión. `--force` reemplaza una instalación anterior. Después comprueba el panel, las preferencias y el log.

**Errores posibles en este bloque:**

**Error 1: la extensión no aparece.**
- **Ubicación:** `gnome-extensions list`.
- **Síntoma:** el UUID no está en la lista.
- **Causa:** el ZIP tiene una carpeta raíz extra o el metadata es inválido.
- **Corrección:** revisa el contenido del ZIP y el metadata. Después de corregir, la extensión aparece.

**Error 2: la extensión queda en `ERROR`.**
- **Ubicación:** `gnome-extensions info`.
- **Síntoma:** no se activa.
- **Causa:** falta un archivo, un import o una dependencia.
- **Corrección:** lee el log y revisa el ZIP. Después de corregir, la extensión se activa.

## Verifica que funciona

La extensión debe:

- Mostrar el icono y el precio en el panel.
- Actualizar el precio cada minuto.
- Abrir la ventana de preferencias.
- Guardar el color tras cerrar sesión.
- Desactivarse sin dejar iconos, temporizadores ni peticiones.

Comprueba el log:

```bash
journalctl /usr/bin/gnome-shell -n 200 -o cat
```

No debe haber excepciones de la extensión.

**Errores posibles en este bloque:**

**Error 1: el precio no se actualiza.**
- **Ubicación:** API.
- **Síntoma:** el panel muestra `--`.
- **Causa:** red, API o temporizador.
- **Corrección:** revisa el log y el intervalo. Después de corregir, el precio aparece.

**Error 2: el color no persiste.**
- **Ubicación:** GSettings.
- **Síntoma:** vuelve al valor por defecto.
- **Causa:** el esquema no está en el ZIP o la clave es distinta.
- **Corrección:** incluye `schemas/*.gschema.xml` y revisa la clave. Después de corregir, el color persiste.

## Errores de integración del capítulo

### Error 1: el ZIP tiene una carpeta raíz

**Síntoma:** al instalar, GNOME no reconoce la extensión.

**Causa:** el ZIP contiene una carpeta que envuelve los archivos.

**Corrección:** empaqueta desde la carpeta de la extensión para que `metadata.json` quede en la raíz. Después de corregir, la extensión se instala.

### Error 2: falta `providers/` en el ZIP

**Síntoma:** la extensión instalada falla al cargar el proveedor.

**Causa:** `gnome-extensions pack` no incluye carpetas adicionales de forma automática.

**Corrección:** usa `--extra-source=providers`. Después de corregir, el proveedor se incluye.

### Error 3: el ZIP incluye secretos

**Síntoma:** una clave aparece en el contenido del paquete.

**Causa:** se hardcodeó o se añadió un archivo de configuración privado.

**Corrección:** revoca la clave, elimina el archivo y vuelve a empaquetar. Después de corregir, el secreto no aparece.

### Error 4: el esquema no se carga en otra máquina

**Síntoma:** la extensión no encuentra GSettings.

**Causa:** el ZIP no incluye `schemas/*.gschema.xml`.

**Corrección:** incluye la carpeta `schemas` con el XML. Después de corregir, el esquema se compila al instalar.

### Error 5: la extensión funciona en la máquina del autor, pero no en otra

**Síntoma:** errores de rutas, dependencias o versiones.

**Causa:** el entorno de prueba tiene archivos o paquetes que la otra máquina no tiene.

**Corrección:** prueba en una cuenta limpia o máquina virtual. Después de corregir, la extensión funciona sin dependencias ocultas.

## Ejercicios

### Reproductivo

1. Revisa `metadata.json` y confirma los campos obligatorios.
2. Crea la carpeta `dist`.
3. Empaqueta con `gnome-extensions pack --force --extra-source=providers --out-dir dist .`.
4. Lista el contenido del ZIP con `unzip -l`.
5. Comprueba que están todos los archivos.
6. Instala el ZIP y activa la extensión.

### Aplicativo

1. Cambia `version-name` a `2.0`.
2. Vuelve a empaquetar.
3. Comprueba el nuevo ZIP.
4. Instala con `--force` y verifica que la extensión sigue funcionando.
5. Explica qué cambió y qué no.

### Creativo

1. Prepara una lista de publicación para una extensión real.
2. Incluye licencia, README, capturas, URL y descripción.
3. Revisa que no haya secretos en el repositorio.
4. Comprueba que el ZIP se instala en una cuenta limpia.
5. Escribe un párrafo explicando qué harías si EGO rechaza la extensión.

**Rúbrica del ejercicio creativo:**

- La lista incluye metadatos, licencia, README y capturas.
- El ZIP se instala en una cuenta limpia.
- No hay secretos en el código ni en las capturas.
- La explicación menciona el motivo del rechazo y la corrección.
- El repositorio está actualizado.

## Autoevaluación

1. ¿Qué campos de `metadata.json` son obligatorios para publicar en EGO?
2. ¿Por qué no debes fijar el campo `version`?
3. ¿Qué hace `gnome-extensions pack` y por qué necesitas `--extra-source=providers`?
4. ¿Qué archivos no deben estar en el ZIP de publicación?
5. La extensión funciona en tu máquina, pero falla en otra. ¿Qué tres cosas revisarías y en qué orden?
6. ¿Por qué el esquema debe incluirse como XML y no como `gschemas.compiled`?

## Respuestas razonadas

1. `uuid`, `name`, `description`, `shell-version` y `url`. `settings-schema` es necesario si la extensión usa preferencias. `version` lo gestiona EGO.

2. Porque EGO incrementa y controla ese campo. Si lo fijas, la tienda puede sobrescribirlo o producir un comportamiento inesperado. Usa `version-name` para la versión visible.

3. `gnome-extensions pack` crea un ZIP instalable con los archivos conocidos. `--extra-source=providers` añade la carpeta del proveedor, que no se incluye automáticamente.

4. `.git`, borradores, temporales, `node_modules`, capturas de trabajo, claves de API y `gschemas.compiled` para EGO.

5. Primero revisaría el contenido del ZIP y que `metadata.json` esté en la raíz. Después comprobaría las rutas de los imports y las dependencias. Finalmente probaría en una cuenta limpia y leería el log. El orden va del paquete al entorno.

6. Porque EGO compila el esquema al instalar. Incluir el compilado puede entrar en conflicto con la compilación de la tienda y con la versión de GLib del usuario. El XML es la fuente.

## Soluciones de los ejercicios

### Solución del reproductivo

El comando de empaquetado es:

```bash
mkdir -p dist
gnome-extensions pack --force --extra-source=providers --out-dir dist .
```

**Errores posibles en este bloque:**

**Error 1: falta `providers/`.**
- **Ubicación:** ZIP.
- **Síntoma:** la extensión falla al cargar el proveedor.
- **Causa:** falta `--extra-source=providers`.
- **Corrección:** repite el comando con la opción. Después de corregir, el proveedor está incluido.

**Error 2: la carpeta `dist` no existe.**
- **Ubicación:** terminal.
- **Síntoma:** el ZIP no se guarda.
- **Causa:** no se creó la carpeta.
- **Corrección:** ejecuta `mkdir -p dist`. Después de corregir, el ZIP se guarda.

El listado debe incluir:

```text
metadata.json
extension.js
prefs.js
stylesheet.css
schemas/org.gnome.shell.extensions.miprimeraextension.gschema.xml
providers/binanceProvider.js
```

**Errores posibles en este bloque:** si falta un archivo, revisa la carpeta de origen y vuelve a empaquetar. Si aparece una carpeta raíz extra, empaqueta desde dentro de la extensión.

### Solución del aplicativo

Cambia `version-name` en `metadata.json`:

```json
"version-name": "2.0"
```

**Errores posibles en este bloque:** si EGO no muestra la versión, revisa que el campo sea un texto y que el ZIP se haya vuelto a crear. Después de corregir, la versión visible cambia.

### Solución del creativo

Una lista de publicación puede ser:

- [ ] Metadata completo.
- [ ] Licencia.
- [ ] README.
- [ ] Capturas sin datos privados.
- [ ] ZIP instalable.
- [ ] Prueba en cuenta limpia.
- [ ] Repositorio actualizado.

**Errores posibles en este bloque:** si un elemento falta, la publicación queda incompleta. Si EGO rechaza, lee el motivo, corrige el código o el paquete y vuelve a enviar.

## Cierre del manual

Has completado el recorrido:

- Creaste una extensión desde cero.
- Añadiste un panel, CSS, preferencias y una API.
- Aprendiste a depurar y a empaquetar.
- Revisaste los límites de GNOME Shell y de Wayland.

El manual termina aquí. Los apéndices contienen el glosario, el mapa de prerrequisitos y el índice de soluciones.

## Si quieres saber más

- extensions.gnome.org: <https://extensions.gnome.org/>
- Documentación de GNOME Shell: <https://gnome.pages.gitlab.gnome.org/gnome-shell/>
- Guía de GJS: <https://gjs.guide/>

No necesitas estos enlaces para continuar.
