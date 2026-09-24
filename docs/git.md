# Flujo de trabajo con Git

Este estándar aplica a todos los repositorios Vicunav. Cada cambio debe ser rastreable desde un issue hasta un único commit final en `main`.

## Flujo obligatorio

1. Crear o seleccionar un issue atómico.
2. Crear una rama desde `main` para ese issue.
3. Implementar y validar únicamente el alcance del issue.
4. Abrir un pull request hacia `main` y enlazar el issue.
5. Aprobar y aplicar **Squash and merge**.
6. Eliminar la rama después del merge.

`main` solo recibe cambios mediante **squash-merge** de un pull request. Están prohibidos los pushes directos, **Create a merge commit** y **Rebase and merge** hacia `main`.

## Ramas

Toda rama usa la fórmula `tipo/numero-issue-slug`.

- `tipo` coincide con el tipo principal del cambio: `feat`, `fix`, `docs`, `chore`, `refactor`, entre otros aprobados abajo.
- `numero-issue` es el número del issue, sin `#`.
- `slug` resume el alcance en minúsculas ASCII y `kebab-case`.
- Una rama pertenece a un solo issue.

Ejemplos válidos:

```text
feat/12-cpt-faq
fix/31-webhook-duplicado
docs/4-flujo-git
refactor/48-servicio-pedidos
```

Ejemplos inválidos:

```text
feature/CPT_FAQ          # Tipo no aprobado, falta issue y no usa kebab-case.
feat/cpt-faq             # Falta el número del issue.
feat/12_cpt_faq          # El slug no usa kebab-case.
feat/12-faq-y-18-checkout   # Mezcla dos issues.
```

## Commits convencionales

Todo commit final usa Conventional Commits:

```text
tipo(scope opcional): descripción
```

Tipos aprobados:

| Tipo | Uso |
| --- | --- |
| `feat` | Funcionalidad nueva. |
| `fix` | Corrección de un defecto. |
| `docs` | Cambios exclusivos de documentación. |
| `chore` | Mantenimiento sin cambio funcional. |
| `refactor` | Cambio interno sin alterar el comportamiento esperado. |
| `test` | Creación o corrección de pruebas. |
| `ci` | Automatización y pipelines de integración. |
| `build` | Sistema de build o dependencias externas. |
| `perf` | Mejora de rendimiento. |
| `style` | Formato sin cambio de comportamiento. |
| `revert` | Reversión explícita de otro commit. |

El scope es opcional y nombra un área concreta del proyecto, por ejemplo `restaurante`, `theme` o `plugin`. Un cambio incompatible añade `!` antes de `:` y explica la ruptura en el cuerpo o en un footer `BREAKING CHANGE:`.

### Mensajes válidos

```text
feat: register FAQ post type
fix: reject duplicate webhook notifications
docs: define repository naming conventions
refactor(restaurante): extract order status service
feat(plugin)!: replace reservation status values
```

### Mensajes inválidos

```text
Add FAQ post type
# Falta el tipo y los dos puntos.

feature: register FAQ post type
# `feature` no es un tipo aprobado; corresponde `feat`.

fix restaurante webhook
# No cumple la estructura `tipo: descripción`.

feat: register FAQ post type and redesign checkout
# Mezcla alcances que deben pertenecer a issues distintos.
```

## Atomicidad: un issue, un commit final

Cada issue produce una rama, un pull request y un commit final en `main`. Un commit final puede incluir código, pruebas y documentación cuando todos son necesarios para completar el mismo issue, pero nunca puede incluir trabajo de varios issues.

La rama puede contener commits intermedios durante desarrollo o revisión. El squash-merge los combina en un solo commit atómico antes de entrar a `main`; esos commits intermedios no se conservan en la historia principal.

Si durante la implementación aparece trabajo fuera de alcance, se crea otro issue y otra rama. No se añade al commit actual aunque parezca pequeño.

## Pull requests y squash-merge

Antes del merge se debe verificar que:

- El pull request enlaza exactamente un issue, por ejemplo `Closes #12`.
- El diff contiene solo el alcance de ese issue.
- Las validaciones relevantes pasan.
- El título del pull request es el mensaje Conventional Commit que quedará en `main`.
- El método seleccionado es **Squash and merge**.

El resultado aceptado es un único commit del issue sobre `main`, sin merge commit. Si GitHub no ofrece **Squash and merge**, el repositorio debe corregir su configuración; no se usa otro método como sustituto.

## Referencias

- [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/)
- [Métodos de merge en GitHub](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/about-pull-request-merges)
