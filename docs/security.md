# Estándar de seguridad

Estas reglas aplican a todo plugin, tema y servicio del ecosistema Vicunav. Una revisión debe bloquear cualquier cambio que incumpla una de ellas.

## 1. Sanitizar antes de guardar

Todo valor externo se debe deslashear y sanitizar según su tipo antes de validarlo o persistirlo. Está prohibido guardar o interpolar directamente valores de `$_POST`, `$_GET`, `$_REQUEST` o cuerpos externos.

```php
$name        = sanitize_text_field( wp_unslash( $_POST['name'] ?? '' ) );
$email       = sanitize_email( wp_unslash( $_POST['email'] ?? '' ) );
$phone       = preg_replace(
	'/[^0-9+(). -]/',
	'',
	sanitize_text_field( wp_unslash( $_POST['phone'] ?? '' ) )
);
$description = wp_kses(
	wp_unslash( $_POST['description'] ?? '' ),
	array( 'strong' => array(), 'em' => array(), 'a' => array( 'href' => array() ) )
);
$quantity    = absint( wp_unslash( $_POST['quantity'] ?? 0 ) );
$website     = esc_url_raw( wp_unslash( $_POST['website'] ?? '' ) );
```

La sanitización no reemplaza la validación: después se deben rechazar emails inválidos, teléfonos fuera del formato admitido, cantidades fuera de rango y URLs no permitidas por la regla de negocio.

## 2. Verificar nonce en escrituras desde el frontend

Toda solicitud del frontend que cree, modifique o elimine datos debe generar y verificar un nonce asociado a la acción. Una solicitud sin nonce válido se rechaza antes de ejecutar lógica de negocio.

```php
<?php wp_nonce_field( 'vicu_update_profile', 'vicu_nonce' ); ?>
```

```php
$nonce = sanitize_text_field( wp_unslash( $_POST['vicu_nonce'] ?? '' ) );

if ( ! wp_verify_nonce( $nonce, 'vicu_update_profile' ) ) {
	wp_die( esc_html__( 'Solicitud inválida.', 'vicunav-plugin-core' ), '', array( 'response' => 403 ) );
}
```

Un nonce comprueba intención, no autorización; la capability se verifica por separado.

## 3. Verificar capability antes de escribir

Toda escritura debe comprobar la capability específica y, cuando aplique, el ID del objeto. `is_user_logged_in()` nunca es una autorización suficiente.

```php
$post_id = absint( wp_unslash( $_POST['post_id'] ?? 0 ) );

if ( ! current_user_can( 'edit_post', $post_id ) ) {
	wp_die( esc_html__( 'No autorizado.', 'vicunav-plugin-core' ), '', array( 'response' => 403 ) );
}

update_post_meta( $post_id, 'vicu_phone', $phone );
```

## 4. Escapar según el contexto de salida

Todo valor dinámico se debe escapar al imprimirlo, incluso si proviene de la base de datos. Se usa `esc_html()` para texto, `esc_attr()` para atributos y `esc_url()` para URLs; HTML permitido se filtra con `wp_kses()` o `wp_kses_post()`.

```php
<a class="<?php echo esc_attr( $css_class ); ?>"
	href="<?php echo esc_url( $profile_url ); ?>">
	<?php echo esc_html( $link_label ); ?>
</a>
```

## 5. Preparar toda query SQL directa

Toda query que incluya valores variables debe usar `$wpdb->prepare()` con el placeholder correcto (`%d`, `%f` o `%s`) y argumentos separados. Los placeholders no llevan comillas en la consulta.

```php
global $wpdb;

$status = $wpdb->get_var(
	$wpdb->prepare(
		"SELECT status FROM {$wpdb->prefix}vicu_orders WHERE id = %d",
		$order_id
	)
);
```

Se deben preferir las APIs de WordPress (`get_post_meta()`, `WP_Query`, opciones, términos) cuando cubran el caso sin SQL directo.

## 6. Autenticar todo webhook externo

Ningún webhook, incluido un futuro webhook de Mercantil, es confiable por provenir de una URL conocida o declarar un encabezado esperado. Antes de procesarlo se debe verificar la firma criptográfica según el contrato del proveedor; si el proveedor ofrece controles de origen, como mTLS o una lista de IPs, también se aplican como defensa adicional.

```php
$body      = (string) file_get_contents( 'php://input' );
$signature = sanitize_text_field( wp_unslash( $_SERVER['HTTP_X_MERCANTIL_SIGNATURE'] ?? '' ) );
$expected  = hash_hmac( 'sha256', $body, VICU_MERCANTIL_WEBHOOK_SECRET );

if ( '' === $signature || ! hash_equals( $expected, $signature ) ) {
	status_header( 401 );
	exit;
}
```

Solo después de autenticar el webhook se valida su esquema, se comprueba idempotencia y se ejecutan escrituras. Nunca se registran el secreto, la firma completa ni datos sensibles del payload.

## 7. Verificar mismo origen en escrituras anónimas

Un nonce solo protege una sesión que ya existe. Cuando una escritura la origina un
visitante sin cuenta ni sesión previa (crear una reserva, un pedido de invitado, un
mensaje de contacto), no hay nonce que verificar todavía. Esa escritura debe exigir
como mínimo que `Origin` o, en su defecto, `Referer` coincidan exactamente con el
sitio (esquema, host y puerto) antes de ejecutar lógica de negocio. Sin esta
verificación, cualquier sitio externo puede enviar la solicitud en nombre de un
visitante.

```php
function vicu_request_is_same_origin( WP_REST_Request $request ): bool {
	$source = trim( (string) $request->get_header( 'origin' ) );

	if ( '' === $source ) {
		$source = trim( (string) $request->get_header( 'referer' ) );
	}

	$source_parts = wp_parse_url( $source );
	$home_parts   = wp_parse_url( home_url( '/' ) );

	if ( ! is_array( $source_parts ) || ! is_array( $home_parts ) ) {
		return false;
	}

	return strtolower( (string) ( $source_parts['scheme'] ?? '' ) ) === strtolower( (string) ( $home_parts['scheme'] ?? '' ) )
		&& strtolower( (string) ( $source_parts['host'] ?? '' ) ) === strtolower( (string) ( $home_parts['host'] ?? '' ) )
		&& ( $source_parts['port'] ?? null ) === ( $home_parts['port'] ?? null );
}
```

Si la escritura anónima además establece una sesión propia (por ejemplo un carrito),
la primera escritura de esa sesión se protege con el chequeo de origen, y las
siguientes agregan un token CSRF emitido junto con la sesión. Un endpoint de solo
lectura no requiere esta verificación.

## 8. Proteger archivos PHP sueltos con un guard de acceso directo

Todo archivo PHP que no sea cargado exclusivamente a través del bootstrap de
WordPress (una clase, una plantilla, un patrón) debe rechazar la ejecución directa
por URL:

```php
defined( 'ABSPATH' ) || exit;
```

Esta línea va inmediatamente después del docblock del archivo, antes de cualquier
`namespace`, `use` o declaración. No sustituye ninguna otra verificación de esta
página: es una defensa adicional para hosting que permite ejecutar PHP bajo
`wp-content/` fuera de una petición de WordPress.

## 9. Documentar el modelo de confianza de una API de proceso

Un método estático público que otro plugin del ecosistema invoca directamente en el
mismo proceso PHP (no vía REST, AJAX o un hook disparado por una solicitud externa) no
tiene forma de verificar por sí mismo `current_user_can()` u otra autorización: no
conoce el contexto de quien lo llama, y ese contexto puede no incluir ningún usuario
de WordPress autenticado (un cron, un webhook ya autenticado por firma). Añadir una
verificación de capability genérica dentro de esa API puede ser tan incorrecto como no
verificar nada, si bloquea un llamador legítimo sin sesión de usuario.

Cuando una API así no verifica autorización propia, el contrato público del paquete
que la expone debe decir explícitamente:

- Que es una API de proceso que confía en el llamador.
- Qué debe verificar el llamador antes de invocarla (capability y nonce si el origen
  es una acción de wp-admin; firma criptográfica si el origen es un webhook, según la
  regla 6).
- Qué plugin es el único consumidor conocido hoy, para poder auditarlo cuando cambie.

Esto convierte una omisión silenciosa en una decisión de arquitectura explícita y
revisable, en vez de un hallazgo repetido en cada auditoría.

## Referencias

- [Seguridad en WordPress](https://developer.wordpress.org/apis/security/)
- [Sanitización de datos](https://developer.wordpress.org/apis/security/sanitizing/)
- [Escape de datos](https://developer.wordpress.org/apis/security/escaping/)
- [Nonces](https://developer.wordpress.org/apis/security/nonces/)
- [`current_user_can()`](https://developer.wordpress.org/reference/functions/current_user_can/)
- [`wpdb::prepare()`](https://developer.wordpress.org/reference/classes/wpdb/prepare/)
