# Modelo de ejecución

Esta norma define cómo se comporta el agente en un proyecto que eligió "propietario
ejecuta" como modelo de ejecución, según
[ADR 0007](https://github.com/vicunav/vicunav-hub/blob/main/docs/adr/0007-modelo-de-ejecucion-por-proyecto.md)
de `vicunav-hub`. Un proyecto con el modelo default ("agente ejecuta") no está sujeto a
esta página.

## Qué sigue haciendo el agente

- Crear y mantener la estructura del repositorio: scaffolding, submódulo de estándares,
  CI, plantillas de issue y pull request.
- Redactar y mantener specs, ADR y documentación del proyecto.
- Revisar el trabajo del propietario contra los estándares vigentes de este repositorio.
- Ejecutar o preparar validaciones: lint, tests, checks de CI, verificación de enlaces y
  coherencia documental.

## Qué no hace el agente sin que se le pida explícitamente

El agente no escribe código de la capa visual (theme, patterns, CSS o Tailwind) ni
migra contenido en un proyecto marcado "propietario ejecuta", aunque el pedido lo
sugiera de forma implícita. Si una solicitud es ambigua sobre quién debe ejecutar una
pieza concreta, el agente lo aclara antes de escribir ese código, en vez de asumir que
retomó el trabajo.

## Cómo se reporta el estado

Un proyecto "propietario ejecuta" sin theme o plugin implementados no es un proyecto
atrasado ni una auditoría con hallazgos: su estado real es el que declare su propia
documentación (por ejemplo `docs/architecture.md`), no una expectativa de ritmo del
agente. Al reportar el estado del proyecto, el agente distingue explícitamente entre
"sin empezar porque el propietario no ha comenzado" y "bloqueado por otra causa".

## Sin excepciones a los gates existentes

Fidelidad visual y su aprobación humana final
([`visual-fidelity.md`](visual-fidelity.md)) aplican igual en ambos modelos de
ejecución; "propietario ejecuta" cambia quién escribe el código, no qué se exige para
declarar el proyecto completo.

## Cambiar de modelo

Un proyecto puede cambiar de modelo de ejecución durante su vida. El cambio se
documenta en su propia `docs/architecture.md` en el mismo cambio que lo adopta, y se
refleja en `estado.md` del hub si afecta la lectura del estado global.
