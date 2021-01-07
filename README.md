
	global $woocommerce, $porto_settings;

	if ( 'none' == $porto_settings['minicart-type'] ) {
		return '';
	}

	if ( $porto_settings['catalog-enable'] ) {
		if ( $porto_settings['catalog-admin'] || ( ! $porto_settings['catalog-admin'] && ! ( current_user_can( 'administrator' ) && is_user_logged_in() ) ) ) {
			if ( ! $porto_settings['catalog-cart'] ) {
				return '';
			}
		}
	}

	$minicart_type = porto_get_minicart_type();

	ob_start();
	if ( class_exists( 'Woocommerce' ) || ( defined( 'PORTO_DEMO' ) && PORTO_DEMO ) ) :
		$icon_class = 'minicart-icon';
		if ( empty( $porto_settings['minicart-icon'] ) ) {
			$icon_class .= ' porto-icon-bag-2';
		} else {
			$icon_class .= ' ' . trim( $porto_settings['minicart-icon'] );
		}
		?>
		<div id="mini-cart" class="mini-cart <?php echo esc_attr( $minicart_type ), isset( $porto_settings['minicart-content'] ) && $porto_settings['minicart-content'] ? ' minicart-offcanvas' : ''; ?>">
			<div class="cart-head">
			<?php
			if ( 'minicart-inline' == $minicart_type ) {
				/* translators: %s: Cart quantity */
				$format = '<span class="cart-icon"><i class="' . esc_attr( $icon_class ) . '"></i><span class="cart-items">%s</span></span><span class="cart-subtotal">' . esc_html__( 'Cart %s', 'porto' ) . '</span>';
				if ( defined( 'WP_CACHE' ) && WP_CACHE ) {
					$_cart_qty   = '<i class="fas fa-spinner fa-pulse"></i>';
					$_cart_total = $_cart_qty;
				} else {
					$_cart_qty   = $woocommerce->cart ? $woocommerce->cart->cart_contents_count : 0;
					$_cart_total = $woocommerce->cart ? $woocommerce->cart->get_cart_subtotal() : 0;
				}
				printf( $format, $_cart_qty, $_cart_total );
			} else {
				$format = '<span class="cart-icon"><i class="' . esc_attr( $icon_class ) . '"></i><span class="cart-items">%s</span></span><span class="cart-items-text">%s</span>';
				if ( ! class_exists( 'Woocommerce' ) && defined( 'PORTO_DEMO' ) && PORTO_DEMO ) {
					$_cart_qty  = 1;
					$_cart_qty1 = 1;
				} elseif ( defined( 'WP_CACHE' ) && WP_CACHE ) {
					$_cart_qty  = '<i class="fas fa-spinner fa-pulse"></i>';
					$_cart_qty1 = $_cart_qty;
				} else {
					$_cart_qty = $woocommerce->cart ? $woocommerce->cart->cart_contents_count : 0;
					/* translators: %s: Cart quantity */
					$_cart_qty1 = sprintf( _n( '%d item', '%d items', $_cart_qty, 'porto' ), $_cart_qty );
				}

				printf( $format, $_cart_qty, $_cart_qty1 );
			}
			?>
			</div>
		<?php if ( ! defined( 'PORTO_MINICART_INIT' ) ) : ?>
			<div class="cart-popup widget_shopping_cart">
				<div class="widget_shopping_cart_content">
				<?php if ( class_exists( 'Woocommerce' ) ) : ?>
					<div class="cart-loading"></div>
				<?php else : ?>
					<ul class="cart_list py-3 px-0 mb-0"><li class="empty pt-0"><?php esc_html_e( 'WooCommerce is not installed.', 'porto' ); ?></li></ul>
				<?php endif; ?>
				</div>
			</div>
		<?php endif; ?>
		<?php
		if ( isset( $porto_settings['minicart-content'] ) && $porto_settings['minicart-content'] && ! defined( 'PORTO_MINICART_INIT' ) ) {
			define( 'PORTO_MINICART_INIT', true );
			echo '<div class="minicart-overlay"><svg viewBox="0 0 32 32" xmlns="http://www.w3.org/2000/svg"><defs><style>.minicart-svg{fill:none;stroke:#fff;stroke-linecap:round;stroke-linejoin:round;stroke-width:2px;}</style></defs><title/><g id="cross"><line class="minicart-svg" x1="7" x2="25" y1="7" y2="25"/><line class="minicart-svg" x1="7" x2="25" y1="25" y2="7"/></g></svg></div>';
		}
		?>
		</div>
		<?php
	endif;

	return apply_filters( 'porto_minicart', ob_get_clean() );
