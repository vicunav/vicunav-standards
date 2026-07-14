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
	wp_die( esc_html__( 'Solicitud inválida.', 'vicunav-core' ), '', array( 'response' => 403 ) );
}
```

Un nonce comprueba intención, no autorización; la capability se verifica por separado.

## 3. Verificar capability antes de escribir

Toda escritura debe comprobar la capability específica y, cuando aplique, el ID del objeto. `is_user_logged_in()` nunca es una autorización suficiente.

```php
$post_id = absint( wp_unslash( $_POST['post_id'] ?? 0 ) );

if ( ! current_user_can( 'edit_post', $post_id ) ) {
	wp_die( esc_html__( 'No autorizado.', 'vicunav-core' ), '', array( 'response' => 403 ) );
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

## Referencias

- [Seguridad en WordPress](https://developer.wordpress.org/apis/security/)
- [Sanitización de datos](https://developer.wordpress.org/apis/security/sanitizing/)
- [Escape de datos](https://developer.wordpress.org/apis/security/escaping/)
- [Nonces](https://developer.wordpress.org/apis/security/nonces/)
- [`current_user_can()`](https://developer.wordpress.org/reference/functions/current_user_can/)
- [`wpdb::prepare()`](https://developer.wordpress.org/reference/classes/wpdb/prepare/)
