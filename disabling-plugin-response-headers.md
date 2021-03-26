# Disabling the plugin's response headers

As of version 1.3, the plugin adds a custom response header to all requests that resolve to a mapped domain. The 
response header is beneficial to those wishing to understand whether a response has been handled by WP Landing Kit. This
is particularly useful for us when handling support. 

You may disable the response headers in your WordPress admin as follows:

1. Go to **Settings > WP Landing Kit**.
1. Under **General Settings**, disable the **Response Headers** option.
1. Click the **Save Changes** button.

If you prefer to disable the header using PHP code, you may do so using the `wp_landing_kit/setting/add_response_headers` 
filter as per the following example:

```php
add_filter( 'wp_landing_kit/setting/add_response_headers', '__return_false' );
```