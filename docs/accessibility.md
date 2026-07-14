# Accesibilidad

La línea base del ecosistema Vicunav es **WCAG 2.1 nivel AA**. Este checklist es un mínimo obligatorio para implementar y revisar cambios; no sustituye una auditoría de todos los criterios WCAG aplicables.

Un pull request no está listo mientras tenga un punto aplicable sin verificar.

## Alcance obligatorio

- [ ] Todo patrón de interfaz de `vicunav-theme-core` cumple este checklist antes de publicarse como componente reutilizable.
- [ ] Cada integración vuelve a probar el patrón en su contexto; heredar un componente de `vicunav-theme-core` no elimina la responsabilidad del repositorio consumidor.
- [ ] Todo formulario de `vicunav-pagos`, incluido el checkout de pago, cumple este checklist de principio a fin.
- [ ] Los formularios de los verticales también lo cumplen: reservas en `vicunav-hotel`, pedidos en `vicunav-restaurante` y cualquier formulario transaccional futuro.
- [ ] Las variantes de escritorio y móvil se verifican por separado.

## Contraste

- [ ] El texto normal y las imágenes de texto alcanzan una relación de contraste mínima de **4.5:1** respecto del fondo.
- [ ] El texto grande alcanza al menos **3:1**. Se considera grande desde 18 pt (aprox. 24 px) en peso normal o 14 pt (aprox. 18.5 px) en negrita.
- [ ] Los límites y estados visuales necesarios para identificar controles activos alcanzan al menos **3:1** respecto de los colores adyacentes.
- [ ] Enlaces, errores, éxito, selección y campos obligatorios no se distinguen únicamente por color.
- [ ] Se miden los estados normal, hover, focus, active y error; no basta con probar la paleta aislada.
- [ ] El pull request registra la herramienta y las combinaciones de colores medidas cuando introduce o modifica colores.

**Verificación:** medir las relaciones sobre la interfaz renderizada y confirmar que ningún valor se redondea para alcanzar el umbral; 4.49:1 no cumple 4.5:1.

## Navegación por teclado

- [ ] Todas las acciones disponibles con mouse o pantalla táctil se pueden completar con teclado.
- [ ] `Tab` y `Shift+Tab` recorren controles en un orden lógico; `Enter`, `Space`, flechas y `Escape` funcionan según el patrón esperado.
- [ ] Cada elemento interactivo muestra un indicador de foco visible; ningún estilo elimina el outline sin proporcionar un reemplazo perceptible.
- [ ] El foco nunca queda atrapado. Un modal mueve el foco a su contenido, permite cerrarse con teclado y lo devuelve al control que lo abrió.
- [ ] Menús, carruseles, acordeones, calendarios, selectores de fecha y diálogos de `vicunav-theme-core` tienen interacción completa por teclado.
- [ ] El contenido que aparece con hover también aparece al recibir foco y puede cerrarse sin usar un puntero.
- [ ] La página ofrece un mecanismo para saltar bloques repetidos, como un enlace “Saltar al contenido”.

**Verificación:** completar el flujo con el mouse desconectado, desde el primer control hasta la confirmación, y comprobar visualmente la posición del foco en cada paso.

## Texto alternativo en imágenes

- [ ] Cada elemento `<img>` tiene atributo `alt`.
- [ ] Una imagen informativa usa un `alt` breve que comunica su propósito en ese contexto; no usa el nombre del archivo ni frases genéricas como “imagen de”.
- [ ] Una imagen puramente decorativa usa `alt=""` para quedar fuera del árbol de accesibilidad; el atributo no se omite.
- [ ] Una imagen que funciona como enlace o botón tiene un nombre accesible que describe la acción o el destino.
- [ ] Gráficos, diagramas o imágenes complejas ofrecen la misma información mediante texto cercano o una descripción ampliada.
- [ ] Los iconos SVG interactivos tienen nombre accesible; los decorativos se ocultan de tecnologías de asistencia.

**Verificación:** inspeccionar el DOM y recorrer la página con lector de pantalla para confirmar que las imágenes informativas se anuncian y las decorativas no.

## Labels y campos de formulario

- [ ] Cada campo visible (`input`, `select` y `textarea`) tiene un label visible y asociado mediante `for`/`id` o una relación HTML equivalente.
- [ ] El placeholder solo aporta un ejemplo; nunca sustituye al label.
- [ ] Los grupos de radios o checkboxes usan `fieldset` y `legend` cuando comparten una pregunta.
- [ ] El estado obligatorio, el formato esperado y las instrucciones necesarias se muestran antes de enviar y están asociados programáticamente al campo.
- [ ] Los campos de datos personales usan valores `autocomplete` apropiados cuando existe un token estándar.
- [ ] Cada error identifica en texto el campo y el problema, queda asociado al control y no depende solo del color.
- [ ] Al fallar el envío, el foco se mueve al resumen o al primer error y los valores válidos ingresados se conservan.

**Verificación:** revisar los nombres y descripciones en el árbol de accesibilidad, y activar cada label para confirmar que enfoca o selecciona su control.

## Formularios transaccionales

Este bloque aplica obligatoriamente al checkout de `vicunav-pagos`, a las reservas de los verticales, a los pedidos y a cualquier flujo futuro que cree una obligación, cambie datos o procese dinero.

- [ ] Todos los pasos, validaciones, selectores y confirmaciones se completan solo con teclado.
- [ ] Antes de confirmar una operación financiera o modificar datos, la persona puede revisar, corregir y confirmar la información.
- [ ] Los errores de validación conservan los datos válidos y ofrecen instrucciones concretas para continuar.
- [ ] Los cambios de estado asíncronos —procesando, confirmado, rechazado o expirado— se anuncian a tecnologías de asistencia sin exigir que el foco encuentre el mensaje.
- [ ] La confirmación final identifica el resultado de la reserva, pedido o pago mediante texto, no solo color o iconos.
- [ ] Componentes de terceros, incluidos campos o iframes de pago, se prueban dentro del flujo completo; su procedencia externa no los exime del requisito.

## Evidencia para aprobar un pull request

- [ ] Se enumeran las páginas, patrones y formularios probados.
- [ ] Se adjuntan los resultados de contraste cuando cambian colores o estados visuales.
- [ ] Se registra una prueba manual de teclado en los breakpoints de escritorio y móvil cuando el cambio afecta interacción.
- [ ] Se ejecuta una auditoría automática como apoyo, sin usarla como reemplazo de las pruebas manuales.
- [ ] Toda desviación encontrada bloquea el merge o queda resuelta dentro del mismo issue antes del squash-merge.

## Referencias WCAG 2.1

- [1.1.1 Contenido no textual](https://www.w3.org/WAI/WCAG21/Understanding/non-text-content.html)
- [1.4.1 Uso del color](https://www.w3.org/WAI/WCAG21/Understanding/use-of-color.html)
- [1.4.3 Contraste mínimo](https://www.w3.org/WAI/WCAG21/Understanding/contrast-minimum.html)
- [1.4.11 Contraste no textual](https://www.w3.org/WAI/WCAG21/Understanding/non-text-contrast.html)
- [2.1.1 Teclado](https://www.w3.org/WAI/WCAG21/Understanding/keyboard.html)
- [2.1.2 Sin trampas de teclado](https://www.w3.org/WAI/WCAG21/Understanding/no-keyboard-trap.html)
- [2.4.7 Foco visible](https://www.w3.org/WAI/WCAG21/Understanding/focus-visible.html)
- [3.3.2 Labels o instrucciones](https://www.w3.org/WAI/WCAG21/Understanding/labels-or-instructions.html)
- [3.3.4 Prevención de errores legales, financieros o de datos](https://www.w3.org/WAI/WCAG21/Understanding/error-prevention-legal-financial-data.html)
- [4.1.3 Mensajes de estado](https://www.w3.org/WAI/WCAG21/Understanding/status-messages.html)
