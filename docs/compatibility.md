# Compatibilidad

Todo plugin y tema del ecosistema Vicunav debe admitir como mínimo **PHP 8.1** y **WordPress 6.6**. El CI valida contra una versión reciente (PHP 8.4), pero esa versión de validación no eleva el mínimo compatible.

## Verificación

- Los encabezados declaran `Requires PHP: 8.1` y `Requires at least: 6.6`.
- El código y las dependencias no requieren versiones superiores a esos mínimos.
- El CI ejecuta las pruebas en una matriz con **al menos dos combinaciones**: el
  mínimo declarado (PHP 8.1 / WordPress 6.6) y una versión reciente (por ejemplo PHP
  8.4 / WordPress 6.9). Validar solo contra la versión reciente no detecta sintaxis o
  funciones que existen ahí pero no en el mínimo declarado, porque una versión más
  nueva de PHP sigue ejecutando sintaxis más nueva sin fallar; el mínimo solo se
  verifica ejecutándolo de verdad. `vicunav-restaurante` ya implementa esta matriz en
  su workflow de CI y es la referencia a replicar en el resto de repositorios.
