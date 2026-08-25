# Fidelidad visual

Este estándar aplica a cualquier repositorio Vicunav que cree o modifique una
interfaz, transforme un diseño aprobado o consuma presentación producida por otro
paquete. La fidelidad visual es una dimensión independiente de la funcionalidad, la
accesibilidad, la estructura y el rendimiento.

Un pull request con impacto visual no está listo mientras carezca de baseline,
evidencia comparable o diferencias explícitamente aprobadas.

## Clasificar el impacto

Todo issue y pull request selecciona una categoría:

| Categoría | Uso | Evidencia mínima |
| --- | --- | --- |
| `ninguno` | No cambia markup renderizado, estilos, contenido visible, assets ni estado de interfaz | Justificación verificable |
| `cambio-visual` | Cambia una interfaz sin contrato de paridad con una fuente externa | Capturas antes y después en viewports afectados |
| `paridad-1-1` | Traduce o corrige una interfaz contra un diseño aprobado | Contrato de fuente, matriz completa y comparación lado a lado u overlay |

Cambiar copy, fuentes, datos visibles, orden del DOM, breakpoints, un bloque dinámico o
una configuración de Global Styles cuenta como impacto visual aunque el diff no
contenga CSS.

Una clasificación `ninguno` incorrecta bloquea el merge. La ausencia de una etiqueta
o campo en la plantilla no convierte el impacto en inexistente.

## Fijar el baseline

Antes de implementar una migración `paridad-1-1`, registrar:

- repositorio y commit inmutables de la fuente;
- comando de instalación, build y ejecución verificado;
- navegador, versión, viewport, escala y densidad usados;
- locale, timezone, fuentes y preferencia de movimiento;
- ruta o estado, datos y autenticación necesarios para reproducir cada captura;
- copy, assets, licencias y faltantes conocidos;
- interacciones y estados que no aparecen en la vista inicial;
- defectos de la fuente que deben corregirse por responsive o accesibilidad.

Las capturas sueltas sin estos datos no constituyen baseline. Si la fuente cambia, se
aprueba otro commit y se identifica qué unidades deben recalibrarse.

## Mantener una matriz de evidencia

Cada fila de evidencia identifica como mínimo:

- página, ruta o estado estable;
- viewport y densidad;
- estado interactivo, cuando aplique;
- captura fuente;
- captura objetivo del mismo commit probado;
- resultado de comparación;
- diferencia conocida, propietario y aprobación;
- fecha, navegador y commit de cada repositorio implicado.

La matriz incluye desktop amplio, tablet vertical y móvil estrecho. El contrato del
proyecto añade otros viewports cuando el diseño define breakpoints o defectos
específicos.

Para interfaces con estado se cubren, según aplique: hover, focus, active, selected,
expanded, loading, empty, error, success, disabled y reduced motion. Una captura de la
vista inicial no acredita esos estados.

La evidencia se enlaza desde el pull request y se conserva en el repositorio o sistema
autorizado. No se usan rutas personales, URLs temporales ni referencias genéricas a
una carpeta sin índice.

## Comparar en condiciones equivalentes

Fuente y objetivo se capturan con:

- el mismo navegador y tamaño de viewport;
- la misma escala, densidad, locale y timezone;
- fuentes completamente cargadas;
- datos deterministas equivalentes;
- animaciones estabilizadas en el mismo punto;
- barras, avisos o sesiones ajenas al producto eliminadas del encuadre.

La revisión lado a lado u overlay comprueba por separado:

- jerarquía, orden, anchos, alturas y ritmo de secciones;
- alineación, gaps, padding, solapes y posición sticky;
- familia, peso, tamaño, tracking, line-height y saltos de línea;
- colores, gradientes, contraste, bordes, radios y sombras;
- recorte, ratio, foco, calidad y carga de media;
- navegación, llamadas a la acción y estados interactivos;
- reordenamiento, wrapping, ocultamiento y overflow responsive.

Una herramienta de diferencia de píxeles ayuda a localizar cambios, pero no sustituye
la inspección. Tampoco se fija un porcentaje universal que permita ignorar una
diferencia perceptible. Cada diferencia visible se corrige o se registra con aprobación
humana explícita.

## Verificar la configuración efectiva

La existencia de `theme.json`, una style variation, CSS compilado, opciones o registros
en base de datos no prueba que WordPress los aplique.

Cuando el cambio depende de configuración visual, las pruebas comprueban:

- presets y custom properties presentes en el CSS final;
- valores calculados en un elemento representativo;
- fuentes realmente cargadas y utilizadas;
- frontend y Site Editor coherentes;
- prioridad correcta entre defaults, theme, variación y personalizaciones;
- ausencia de caché o datos persistidos que oculten el resultado.

Una prueba que solo consulta el archivo o el registro persistido es insuficiente.

## Respetar la propiedad

- El theme posee valores visuales compartidos, estilos globales, templates, template
  parts, patterns y estilos editoriales reutilizables.
- Un plugin con interfaz posee markup semántico, comportamiento, composición intrínseca y
  representación de estados funcionales. Consume presets públicos y fallbacks
  neutrales; no incorpora valores de una marca particular.
- El repositorio de demo posee copy, media, composición y selección de identidad de su
  marca. No duplica lógica reusable.
- El repositorio que descubre un defecto fuera de su propiedad crea un issue atómico
  en el propietario y espera su versión antes de aprobar la integración.

No se copian estilos entre repositorios para acelerar una captura ni se promueve una
geometría específica del demo a un theme compartido sin reutilización demostrada.

## Separar gates

Un gate visual no sustituye y no puede ser sustituido por:

- rutas 200, HTML válido, H1 único o ausencia de overflow;
- PHPUnit, pruebas E2E funcionales o ausencia de errores de consola;
- WCAG, teclado, foco o contraste;
- Lighthouse, peso de assets o Core Web Vitals;
- editabilidad y ausencia de bloques inválidos en Site Editor.

Todos los gates aplicables deben pasar. El estado funcional puede registrarse como
completo mientras el visual sigue pendiente, pero el producto integrado no se declara
completo hasta aprobar ambos.

## Bloqueos obligatorios

El pull request no se fusiona cuando:

- falta el baseline o no corresponde al commit aprobado;
- falta una fila aplicable de la matriz;
- fuente y objetivo no usan condiciones comparables;
- una diferencia visible no está corregida ni aprobada;
- un asset original falta y no existe una sustitución aprobada;
- la configuración existe pero no se demuestra efectiva;
- frontend y editor divergen de forma no documentada;
- la evidencia pertenece a otro commit;
- se intenta usar una prueba estructural o funcional como aprobación visual.

## Privacidad y licencias

- No se versionan credenciales, cookies, tokens, rutas personales ni datos privados.
- Las fixtures visuales usan datos ficticios o anonimizados.
- Cada asset conserva procedencia, licencia y texto alternativo.
- Las capturas con información sensible se almacenan solo en el entorno autorizado y
  se enlazan mediante una referencia que no exponga el contenido.

## Adopción

Después de publicar este estándar, cada consumidor actualiza el submódulo mediante un
issue y un pull request propios. La adopción prioritaria corresponde a:

1. `vicunav-transform-claude-to-gutenberg`;
2. `vicunav-repo-template`;
3. `vicunav-theme-core`;
4. plugins y verticales con interfaz pública;
5. repositorios de demo y el hub coordinador.

La actualización del submódulo no acredita por sí sola cumplimiento. Cada repositorio
debe integrar la clasificación, evidencia y comandos aplicables a su propio flujo.
