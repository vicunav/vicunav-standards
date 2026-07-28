# Estándar de pruebas

Este estándar define qué cambios del ecosistema Vicunav requieren pruebas automatizadas con PHPUnit y cuáles se validan mediante renderizado o inspección. La decisión depende del comportamiento que pueda fallar, no del tipo de archivo que contiene la implementación.

## Código que requiere PHPUnit

Toda lógica de negocio real debe tener pruebas automatizadas. Esto incluye:

- Máquinas de estado, con sus transiciones permitidas, transiciones rechazadas y efectos asociados.
- Cálculos de importes, disponibilidad, fechas, vencimientos, cupos u otras reglas del dominio.
- Registro de tipos de contenido personalizados (CPT), incluidos los argumentos y hooks que definen su comportamiento.
- Helpers de seguridad y dispatchers que normalizan, sanitizan, validan, autorizan o enrutan datos externos.

Si una plantilla o un bloque contiene decisiones de negocio, esa lógica se debe extraer a una unidad comprobable y cubrir con PHPUnit. El hecho de que el resultado termine en HTML no elimina la necesidad de probar el comportamiento.

## Artefactos que no requieren PHPUnit

No se exige PHPUnit para artefactos sin lógica de negocio, como:

- Markup estático.
- Patrones de bloques.
- Configuración declarativa en JSON.

Estos artefactos se verifican mediante renderizado e inspección, según corresponda. La revisión debe confirmar que el resultado visual, la estructura generada y la configuración efectiva coinciden con lo esperado. No se deben crear pruebas PHPUnit que solo repitan literalmente HTML o valores declarativos sin comprobar comportamiento.

## Framework

Las pruebas se escriben con PHPUnit y se ejecutan contra el core de WordPress mediante:

- [`wp-phpunit`](https://github.com/wp-phpunit/wp-phpunit), como entorno de pruebas compatible con WordPress.
- [`yoast/wp-test-utils`](https://github.com/Yoast/wp-test-utils), para las utilidades y abstracciones compartidas de pruebas.

Esta combinación es el estándar de la comunidad WordPress para comprobar integraciones reales con el core. Cuando una prueba dependa de APIs, hooks, base de datos o factories de WordPress, debe ejecutarse en este entorno en lugar de simular el core de forma parcial.

## Cobertura mínima esperada

La cobertura mínima se define por comportamiento, no por un porcentaje global de líneas. Cada cambio debe cubrir, como mínimo:

- Cada transición de una máquina de estados, incluidas las rutas válidas, el rechazo de transiciones inválidas y los efectos observables de cada transición.
- Cada regla de sanitización del dispatcher de seguridad, con entradas válidas, entradas transformadas y entradas que deban rechazarse.
- Cada caso límite conocido del dominio, incluido el intento de doble reserva simultánea y la expiración de una solicitud de pago.

Los defectos descubiertos en producción o durante una revisión deben convertirse en casos de regresión cuando afecten lógica incluida en este estándar.

## Fuera de alcance por ahora

Las pruebas de carga y estrés no forman parte del alcance actual. Se revisitarán cuando exista tráfico real que justifique definir volúmenes, patrones de concurrencia y objetivos de rendimiento representativos.
