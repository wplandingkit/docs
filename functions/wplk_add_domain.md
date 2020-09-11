```php
wplk_add_domain( string $domain_name, int|array $config = [], boolean $active = false )
```

## Description

The function inserts new domains into the database.

## Parameters

**$domain_name** _(string) (Required)_

The domain host name or a full URL containing the host name.

**$config** _(int|array) (Optional)_

A post ID or an array of settings. If passing an array:

- `'post_id'` <br>
  _(int) (optional)_ The ID of the post that renders at the root of the domain.
- `'owner_id'` <br>
  _(int) (optional)_ The ID of the user that owns this domain. This is the domain post author.
- `'active'` <br>
  _(boolean) (optional)_ Whether or not to activate this domain. Note: fails if no `post_id` is available.
- `'enforced_protocol'` <br>
  _(int) (optional)_ The protocol to enforce on this domain.

**$active** _(boolean) (Optional)_

Whether or not the domain is currently active (published).

## Return

Returns `WPLK_Domain` object on success or a `WP_Error` object on failure.

## Examples

### Adding a domain with no config

In this example, the domain cannot be activated yet as it doesn't have a root mapping.

```php
$domain = wplk_add_domain( 'mydomain.com' );
```

To add a root mapping to the created domain before activating it, consider the following:

```php
$post_id = 1234;
$domain = wplk_add_domain( 'mydomain.com' );
$domain->root()->maps_to_post( 1234 );
$domain->activate();
$domain->save();
```

### Adding a domain and mapping the root to a post/page

When a domain has a root mapping — in this case, a post ID — the domain can be activated on creation.

```php
$post_id = 1234;
$domain = wplk_add_domain( 'mydomain.com', $post_id, true );
```

### Adding a domain with additional settings

You may pass an array as the second parameter for more control. e.g;

```php
$post_id = 1234;
$user_id = 5678;
$domain = wplk_add_domain( 'mydomain.com', [
    'post_id' => $post_id,
    'owner_id' => $user_id,
    'active' => true,
    'enforced_protocol' => 'https',
] );
```

## Notes