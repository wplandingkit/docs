# Domain Init Action Hook

```php
$args = [
    'host' => 'example.com',
    'domain_id' => 123,
    'protocol' => 'http',
    'path' => 'some/uri/path',
    'query' => [
        'param' => 'value',
        'param2' => 'value2',
    ],
];

do_action( 'wp_landing_kit/domain_init', $args );
```

The hook runs on `init` after a secondary domain has resolved and has been set up. You may use this hook to run any 
custom functionality that is only needed for specific domains or only when a secondary domain is in use. 

## Parameters

**$args**
    
An array of contextual information about the resolved domain and the URL used to make the request.

## Usage Examples

Adding footer code (such as tracking/conversion optimisation code) to all requests made to a particular domain.

```php
add_action( 'wp_landing_kit/domain_init', 'example_tracking_codes' );

function example_tracking_codes( $args ) {
	if ( $args['host'] === 'example.com' ) {
		add_action( 'wp_footer', 'print_example_tracking_codes' );
	}
}

function print_example_tracking_codes() {
	echo "<script><!-- add custom tracking code here --></script>";
}
```