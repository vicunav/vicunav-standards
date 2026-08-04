# Cómo contribuir

Los cambios a estándares afectan potencialmente a todo el ecosistema. Cada cambio debe
tener un issue atómico, una rama, un pull request y un único commit final mediante
squash-merge, según [`docs/git.md`](docs/git.md).

## Antes de proponer una regla

1. Confirma que la necesidad se repite en más de un repositorio.
2. Revisa si una norma existente puede ampliarse sin crear otro archivo.
3. Identifica consumidores y consecuencias de adopción.
4. Define criterios observables para verificar la regla.

Las decisiones arquitectónicas pertenecen a `vicunav-hub`; los detalles exclusivos de
un paquete pertenecen al repositorio propietario.

## Validación

- Revisa manualmente enlaces, títulos, listas, tablas y bloques de código.
- Comprueba que los ejemplos respeten las convenciones vigentes.
- Explica en el pull request qué repositorios deben actualizar su submódulo.
- No actualices consumidores dentro del mismo commit de este repositorio.

Después del merge, cada consumidor adopta el nuevo commit mediante su propio issue y
pull request.
