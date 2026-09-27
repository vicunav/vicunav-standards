# Tailwind CSS

Esta norma aplica únicamente a un proyecto que eligió Tailwind CSS como su stack de
estilos, según [ADR 0006](https://github.com/vicunav/vicunav-hub/blob/main/docs/adr/0006-stack-de-estilos-por-proyecto.md)
de `vicunav-hub`. Un proyecto con el stack nativo (`theme.json` y CSS por patrón) no
está sujeto a esta página.

## Build local, sin CDN

Tailwind se compila localmente (Tailwind CLI o el plugin de PostCSS integrado en
`@wordpress/scripts`) y el CSS resultante se compromete en el repositorio. Está
prohibido cargar el script "Play CDN" de Tailwind (`<script src="https://cdn.tailwindcss.com">`)
en cualquier plantilla o parte versionada: es la misma regla de "sin recursos remotos
no auditados" que ya rige para JS y CSS de terceros.

## Build reproducible

Un test o paso de CI reconstruye el CSS desde `tailwind.config.js` y confirma que el
archivo comprometido no cambia (`git diff --exit-code` contra el resultado del build),
igual que `vicunav-restaurante` verifica `plugin/build`. Un CSS comprometido que no
coincide con su fuente no es confiable.

## Los tokens vienen de una sola fuente

Si el proyecto es un theme de bloques, `tailwind.config.js` deriva sus colores,
tipografías y escala de espaciado de los valores ya declarados en `theme.json`; no
declara una paleta paralela. Si el proyecto no tiene `theme.json` (por ejemplo, una
capa headless), los tokens se declaran una sola vez, en `tailwind.config.js`, y todo lo
demás los consume desde ahí.

## Sin colisión con las clases de WordPress

Las clases que genera el editor de bloques (`wp-block-*`, `has-*-color`,
`is-layout-*`) y los prefijos propios de este conjunto (`vicu_`, `vicunav-*`, ver
[`naming.md`](naming.md)) siguen existiendo sin importar el stack. Las clases
utilitarias de Tailwind no redefinen ni sobrescriben esas clases; conviven en el mismo
markup. Un patrón o bloque dinámico puede combinar ambas, pero la identidad de marca
(`theme.json`) sigue siendo la fuente única de verdad para color y tipografía.

## Content/purge declarado y revisable

Las rutas de `content` en `tailwind.config.js` (qué archivos escanea Tailwind para
decidir qué utilidades incluir) se comprometen en el repositorio y son parte de la
revisión de cualquier cambio; una ruta demasiado amplia genera CSS sin usar, una
demasiado estrecha rompe estilos en producción.

## Sin excepciones por el stack

Fidelidad visual ([`visual-fidelity.md`](visual-fidelity.md)), accesibilidad
([`accessibility.md`](accessibility.md)) y los presupuestos de rendimiento que ya
tenga el proyecto se cumplen igual, sin importar si el CSS viene de Tailwind o de una
hoja de estilo nativa.

## Sin configuración compartida entre repositorios

Cada proyecto con Tailwind escribe su propio `tailwind.config.js` desde cero. No se
publica ni se consume ningún paquete, preset o config de Tailwind compartido entre
repositorios de Vicunav; adoptarlo en un proyecto no crea una dependencia para los
demás (consecuencia directa del [ADR 0006](https://github.com/vicunav/vicunav-hub/blob/main/docs/adr/0006-stack-de-estilos-por-proyecto.md)).

## Referencias

- [Documentación de Tailwind CSS](https://tailwindcss.com/docs)
- [Instalación de Tailwind con PostCSS](https://tailwindcss.com/docs/installation/using-postcss)
