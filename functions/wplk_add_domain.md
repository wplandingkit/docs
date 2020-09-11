```php
wplk_add_domain( $domain_name [, $config = [] [, $active = false ]] )
```

## Description

The function inserts new domains into the database.

## Parameters

| Parameter | Type/s | Description |
|---|---|---|
| `$domain_name` | _string_ | The domain host name or a full URL containing the host name. |
| `$config` | _int_\|_array_ | A post ID or an array of settings. |
| `$active` | _boolean_ | Whether or not the domain is currently active (published). |

## Return

Returns WPLK_Domain object on success or a WP_Error object on failure.

## Examples

```php
$home_post_id = 1234;
$domain = wplk_add_domain('mydomain.com', $home_post_id );
```

## Notes