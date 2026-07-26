# Compatibilidad

Todo plugin y tema del ecosistema Vicunav debe admitir como mínimo **PHP 8.1** y **WordPress 6.6**. El CI usa PHP 8.4, pero esa versión de validación no eleva el mínimo compatible.

## Verificación

- Los encabezados declaran `Requires PHP: 8.1` y `Requires at least: 6.6`.
- El código y las dependencias no requieren versiones superiores a esos mínimos.
- Las validaciones del proyecto pasan con PHP 8.4 en CI.
