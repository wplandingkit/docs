# Creating domains on Easy Digital Downloads (EDD) transaction completion

The following example hooks into EDD's purchase completion hook and creates domains on payment completion. The example looks for one product (using the product ID) and, if found, creates one or more domains to suit the quantity on order. Each domain is assigned to the customer.

The example is auto-generating a placeholder domain host names which can be edited by the customer later on but you may choose to use some form of input on the order to capture the customers desired domain and use that here instead. 

This example also provides the admin with payment notes that communicate what is happening around domain creation and links directly to the domain edit screen.

```php
add_action( 'edd_complete_purchase', 'wplk_on_edd_order_complete', 10, 3 );
function wplk_on_edd_order_complete( $payment_id, EDD_Payment $payment, EDD_Customer $customer ) {

	// This is the product ID the represents a landing page.
	$landing_page_product_id = 1025;

	// Cart details
	$cart_items = edd_get_payment_meta_cart_details( $payment_id );

	// Loop through all order items and process any with the matching product ID.
	foreach ( $cart_items as $i => $item ) {

		// Skip line items that don't match our product ID.
		if ( $item['id'] !== $landing_page_product_id ) {
			continue;
		}

		// Establish domain-related variables. The $host variable is being auto-generated based on the order
		// number and the quantity. This ensure unique host names are generated for each domain. This can be changed
		// later on at the domain edit screen or some custom order fields could be used here to create domains to order.
		$customer_id = $customer->id;
		$domain_number = $i + 1;
		$host = sprintf( "order%s-domain%s.com", $payment_id, $domain_number );
		$meta_key = "domain_{$domain_number}_created";

		// If the order item already has a domain marked against this meta key, stop here.
		if ( edd_get_payment_meta( $payment_id, $meta_key ) ) {
			return;
		}

		// Insert the domain.
		$domain = wplk_add_domain( $host, [ 'owner_id' => $customer_id ] );

		// If there is an error while creating the domain, add a payment note with details and stop there.
		if ( is_wp_error( $domain ) ) {
			edd_insert_payment_note( $payment_id, "There was a problem creating the $host domain. Error reads: {$domain->get_error_message()}" );

			return;
		}

		// Build a link to the domain edit screen.
		$domain_edit_anchor = sprintf( '<a href="%s" target="_blank">%s</a>',
			admin_url( 'post.php?post=' . $domain->post_id() . '&action=edit' ),
			$domain->post_id()
		);

		// Add order note indicating that the domain was created.
		edd_insert_payment_note( $payment_id, "Domain with host name $host created: $domain_edit_anchor" );

		// Save the link against the payment meta.
		edd_update_payment_meta( $payment_id, $meta_key, $domain->post_id() );
	}
}
```

What this doesn't cover is how to allow the customer to edit their domain. If you wish to provide them with access to
the WordPress admin, you can assign custom capabilities to their user role allowing them varying degrees of access to
manage domains. See [Controlling edit access to domains](controlling-edit-access-to-domains.md) for more information.