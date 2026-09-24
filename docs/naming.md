# Convenciones de nombres

Estas reglas aplican a todos los repositorios de Vicunav. Los nombres nuevos deben poder deducirse de este documento sin crear convenciones locales.

## Referencia rápida

| Elemento | Formato | Ejemplo |
| --- | --- | --- |
| Repositorio | `vicunav-{proyecto}` | `vicunav-restaurante` |
| Identificador de código WordPress | `vicu_{entidad}` | `vicu_payment_req` |
| Namespace PHP | `Vicu\{Proyecto}` | `Vicu\Restaurante` |
| Hook | `vicu_{plugin}_{evento}` | `vicu_restaurante_pedido_confirmado` |
| Text domain | Igual al slug del plugin | `vicunav-restaurante` |

## Repositorios y prefijo de código

Los repositorios usan `vicunav-` seguido de un nombre en minúsculas y `kebab-case`. Los plugins y temas de cada proyecto llevan el nombre que corresponda a ese proyecto, en minúsculas y `kebab-case`:

- `vicunav-standards`
- `vicunav-repo-template`
- `vicunav-hub`
- `vicunav-restaurante`
- `vicunav-bhoga-yoga`
- `vicunav-gutenberg`

Los identificadores internos de WordPress usan el prefijo corto `vicu_`, en minúsculas y `snake_case`. No se debe usar `vicunav_` como prefijo de CPT.

La diferencia es intencional: el nombre público prioriza una marca reconocible, mientras que el código necesita evitar colisiones sin agotar el límite de 20 caracteres que WordPress impone a las claves de post type.

## Custom post types

Un CPT nuevo se nombra con la fórmula `vicu_{entidad}`. La entidad debe ser un sustantivo técnico singular en inglés, escrito en `snake_case`.

Antes de registrar un CPT se debe comprobar que:

1. Empieza por `vicu_`.
2. Solo contiene letras minúsculas ASCII, números y guiones bajos.
3. Tiene 20 caracteres o menos, incluido el prefijo.
4. No coincide con otro CPT del mismo proyecto ni con una clave reservada por WordPress.
5. Se añade a la tabla viva de este documento en el mismo cambio que lo registra.

Ejemplo: para una reserva se usa `vicu_reservation`. Si el nombre supera el límite, se acorta la entidad sin eliminar `vicu_`; `vicu_restaurant_booking` se reduce a `vicu_booking`.

### Registro vivo de CPTs

Esta tabla es una referencia viva, no una lista cerrada. Se actualiza cada vez que se aprueba, renombra o retira un CPT; una clave retirada no se reutiliza para otra entidad.

| CPT | Entidad | Longitud |
| --- | --- | ---: |
| `vicu_faq` | FAQ | 8 |
| `vicu_testimonial` | Testimonio | 16 |
| `vicu_payment_req` | Solicitud de pago | 16 |
| `vicu_menu_item` | Elemento de menú | 14 |
| `vicu_order` | Pedido | 10 |

## Namespaces PHP

Todo código PHP propio usa `Vicu` como namespace raíz. El segundo segmento es el proyecto en `PascalCase`; los segmentos siguientes describen la responsabilidad, también en `PascalCase`.

```php
namespace Vicu\Restaurante;
namespace Vicu\Restaurante\Orders;
namespace Vicu\Bhoga;
namespace Vicu\Bhoga\Content;
```

No se usan `Vicunav` como raíz, guiones, guiones bajos ni nombres de repositorio completos dentro del namespace.

## Hooks

Todo action o filter propio usa `vicu_{plugin}_{evento}`:

- `{plugin}` es el slug corto del plugin, sin el prefijo `vicunav-`: `restaurante`.
- `{evento}` usa el estado o transición de negocio definido por el proyecto: `creado`, `confirmado`, `rechazado`, `expirado`.
- El nombre completo solo contiene minúsculas, números y guiones bajos.

Cuando el evento refleja un estado o una transición de negocio ya definida por el proyecto, se conserva su nombre en español; no se traduce a inglés.

```php
do_action( 'vicu_restaurante_pedido_confirmado', $order_id );

do_action( 'vicu_restaurante_pedido_rechazado', $order_id );
```

`vicu_restaurante_pedido_confirmado` es válido porque identifica propietario y evento con la fórmula. `restaurante_pedido_confirmado`, `vicunav_restaurante_pedido_confirmado` y `vicu_pedido_confirmado` son inválidos porque omiten o alteran la fórmula.

## Text domains

El text domain debe ser exactamente igual al slug del plugin o tema: minúsculas y palabras separadas por guiones (por ejemplo `vicunav-restaurante` o `bhoga-yoga-content`).

```php
__( 'Pedido recibido.', 'vicunav-restaurante' );
```

No se permiten variantes como `vicu-restaurante`, `vicunav_restaurante` o `VicuRestaurante`.

## Referencia de WordPress

- [`register_post_type()`](https://developer.wordpress.org/reference/functions/register_post_type/)
