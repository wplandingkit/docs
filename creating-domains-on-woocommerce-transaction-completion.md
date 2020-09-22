The following example hooks into WooCommerce's order status hooks and creates domains on order completion. The example looks for one product (using the product ID) and, if found, creates one or more domains to suit the quantity on order. Each domain is assigned to the customer.

The example is auto-generating a placeholder domain host names which can be edited by the customer later on but you may choose to use some form of input on the order to capture the customers desired domain and use that here instead. 

This example also provides the admin with order notes that communicate what is happening around domain creation as well as order line item meta that shows how many domains were created for each line item and links directly to the domain edit screen.

```php
add_action( 'woocommerce_order_status_completed', 'wplk_on_wc_order_complete', 10, 2 );
function wplk_on_wc_order_complete( $order_id, WC_Order $order ) {

	// This is the product ID the represents a landing page.
	$landing_page_product_id = 999;

	// Loop through all order items and process any with the matching product ID.
	foreach ( $order->get_items() as $item ) {

		if ( $item->get_product_id() === $landing_page_product_id ) {

			// Create matching number of domains to the quantity ordered.
			for ( $i = 0; $i < $item->get_quantity(); $i ++ ) {

				// Establish domain-related variables. The $host variable is being auto-generated based on the order
				// number, the line item ID, and the QTY number. This ensure unique host names are generated for each
				// domain. This can be changed later on at the domain edit screen or some custom order fields could be
				// used here to create domains to order.
				$customer_id = $order->get_customer_id();
				$domain_number = $i + 1;
				$host = sprintf( "order%s-item%s-domain%s.com", $order->get_id(), $item->get_id(), $domain_number );
				$meta_key = "domain_{$domain_number}_created";

				// If the order item already has a domain marked against this meta key, stop here.
				if ( wc_get_order_item_meta( $item->get_id(), $meta_key ) ) {
					return;
				}

				// Insert the domain.
				$domain = wplk_add_domain( $host, [ 'owner_id' => $customer_id ] );

				// If there is an error while creating the domain, add an order note with details and stop there.
				if ( is_wp_error( $domain ) ) {
					$order->add_order_note( "There was a problem creating the $host domain. Error reads: {$domain->get_error_message()}" );

					return;
				}

				// Add order note indicating that the domain was created.
				$order->add_order_note( "Domain with host name $host created" );

				// Build a link to the domain edit screen.
				$domain_edit_anchor = sprintf( '<a href="%s" target="_blank">%s</a>',
					admin_url( 'post.php?post=' . $domain->post_id() . '&action=edit' ),
					$domain->post_id()
				);

				// Save the link against the order item meta.
				wc_add_order_item_meta( $item->get_id(), $meta_key, $domain_edit_anchor );
			}
		}
	}
}
```