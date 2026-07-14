# Convenciones de nombres

Estas reglas aplican a todos los repositorios y paquetes del ecosistema Vicunav. Los nombres nuevos deben poder deducirse de este documento sin crear convenciones locales.

## Referencia rápida

| Elemento | Formato | Ejemplo |
| --- | --- | --- |
| Repositorio o slug de paquete | `vicunav-{paquete}` | `vicunav-pagos` |
| Identificador de código WordPress | `vicu_{entidad}` | `vicu_payment_req` |
| Namespace PHP | `Vicu\{Paquete}` | `Vicu\Pagos` |
| Hook | `vicu_{plugin}_{evento}` | `vicu_pagos_payment_confirmed` |
| Text domain | Igual al slug del plugin | `vicunav-pagos` |

## Repositorios y prefijo de código

Los repositorios, plugins y temas usan `vicunav-` seguido de un nombre en minúsculas y `kebab-case`:

- `vicunav-core`
- `vicunav-pagos`
- `vicunav-restaurante`

Los identificadores internos de WordPress usan el prefijo corto `vicu_`, en minúsculas y `snake_case`. No se debe usar `vicunav_` como prefijo de CPT.

La diferencia es intencional: el nombre público prioriza una marca reconocible, mientras que el código necesita evitar colisiones sin agotar el límite de 20 caracteres que WordPress impone a las claves de post type.

## Custom post types

Un CPT nuevo se nombra con la fórmula `vicu_{entidad}`. La entidad debe ser un sustantivo técnico singular en inglés, escrito en `snake_case`.

Antes de registrar un CPT se debe comprobar que:

1. Empieza por `vicu_`.
2. Solo contiene letras minúsculas ASCII, números y guiones bajos.
3. Tiene 20 caracteres o menos, incluido el prefijo.
4. No coincide con otro CPT del ecosistema ni con una clave reservada por WordPress.
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

Todo código PHP propio usa `Vicu` como namespace raíz. El segundo segmento es el paquete en `PascalCase`; los segmentos siguientes describen la responsabilidad, también en `PascalCase`.

```php
namespace Vicu\Core;
namespace Vicu\Pagos;
namespace Vicu\Pagos\Webhook;
namespace Vicu\Restaurante\Orders;
```

No se usan `Vicunav` como raíz, guiones, guiones bajos ni nombres de repositorio completos dentro del namespace.

## Hooks

Todo action o filter propio usa `vicu_{plugin}_{evento}`:

- `{plugin}` es el slug corto del paquete, sin `vicunav-`: `core`, `pagos`, `restaurante`.
- `{evento}` describe el objeto y el hecho o valor en `snake_case`: `payment_confirmed`, `before_order_save`, `order_total`.
- El nombre completo solo contiene minúsculas, números y guiones bajos.

```php
do_action( 'vicu_pagos_payment_confirmed', $payment_id );

$total = apply_filters( 'vicu_restaurante_order_total', $total, $order_id );
```

`vicu_pagos_payment_confirmed` es válido porque identifica propietario y evento. `payment_confirmed`, `vicunav_pagos_payment_confirmed` y `vicu_payment_confirmed` son inválidos porque omiten o alteran la fórmula.

## Text domains

El text domain debe ser exactamente igual al slug del plugin o tema: minúsculas, palabras separadas por guiones y prefijo `vicunav-`.

```php
__( 'Pago recibido.', 'vicunav-pagos' );
__( 'Pedido recibido.', 'vicunav-restaurante' );
```

No se permiten variantes como `vicu-pagos`, `vicunav_pagos` o `VicuPagos`.

## Referencia de WordPress

- [`register_post_type()`](https://developer.wordpress.org/reference/functions/register_post_type/)
