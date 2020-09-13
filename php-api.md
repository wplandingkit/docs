# PHP API

- [Introduction](#introduction)
- [Usage](#usage)
    - [Inserting a new domain into the database](#inserting-a-new-domain-into-the-database)
    - [Getting an existing domain](#getting-an-existing-domain)
    - [Checking if a domain already exists in the database](#checking-if-a-domain-already-exists-in-the-database)
    - [Checking if an existing domain is active](#checking-if-an-existing-domain-is-active)
    - [Updating a domain](#updating-a-domain)
    - [Activating a domain](#activating-a-domain)
    - [Mapping the domain root](#mapping-the-domain-root)
    - [Controlling fallback behaviour on a domain](#controlling-fallback-behaviour-on-a-domain)
    - [Adding a URL to a domain](#adding-a-url-to-a-domain)
    - [Removing a URL from a domain](#removing-a-url-from-a-domain)
    - [Deleting a domain](#deleting-a-domain)

## Introduction

[WP Landing Kit](https://wplandingkit.com/) provides a simple PHP API to enable developers to programmatically work with domains. The API makes it
possible to:

1. Add new domain entries to the database.
1. Edit existing domain entries.
1. Delete domain entries.

When creating or editing a domain, the API provides control over:

1. Domain URL mappings (you can think of these as routes/endpoints)
    - Mappings can resolve to posts/pages/archives
    - Mappings can also redirect to both relative and absolute URLs
1. Domain settings
    - Protocol enforcement
    - Active status
    - Domain ownership (which WordPress user is the 'author' of the domain)

### Important!

It is important to keep in mind that the API affects the database and should not be used as part of a typical WordPress
page load. Instead, it is designed to be used at strategic points as a means of automating domain creation/manipulation.
e.g; You may choose to run this after a WooCommerce/Easy Digital Downloads transaction or perhaps after user
registration, etc.

## Usage

TODO - once we have all the examples in, let's break it all down a little more so if flows better and builds knowledge
instead of throwing out big snippets.

### Inserting a new domain into the database

You may create new domains using the `wplk_add_domain()` function. The function will return a `WPLK_Domain` object on
success or a `WP_Error` object when the domain is already in the database or some other failure occurs. Newly created
domains are inactive by default — that is, they are in a `draft` status and are not actively handling requests made to
the site. See _[Activating a domain](#activating-a-domain)_.

```php
// Create an inactive domain with no mappings.
$domain = wplk_add_domain( 'mydomain.com' );

// Create an inactive domain with the domain root mapped to a post.
$post_id = 1234;
$domain = wplk_add_domain( 'mydomain.com', $post_id );

// Create an active domain with the domain root mapped to a post.
$post_id = 1234;
$domain = wplk_add_domain( 'mydomain.com', $post_id, true );

// Create an active domain with an owner (user ID), an enforced protocol, and with the root mapped to a post.
$post_id = 1234;
$user_id = 5678;
$domain = wplk_add_domain( 'mydomain.com', [
    'post_id' => $post_id,
    'owner_id' => $user_id,
    'active' => true,
    'enforced_protocol' => 'https',
] );
```

### Getting an existing domain

You may get an existing domain using the `wplk_get_domain()` function. The function will return a `WPLK_Domain` object
if the domain is found otherwise it will return `NULL`. You can pass this function one of the following:

1. The domain post ID.
1. The domain host name. e.g; `mydomain.com`.
1. A full URL containing the domain host name. e.g; `http://mydomain.com/some/path`.
    - In this case, the host name will be extracted from the URL. All other elements of the URL will be ignored.

**Note:** The most efficient lookup is achieved using the post ID so use that if/when possible.

```php
// Lookup using the domain post ID.
$domain = wplk_get_domain( 5 );

// Lookup using the domain host name:
$domain = wplk_get_domain( 'mydomain.com' );

// Lookup using a full URL containing the domain host name:
$domain = wplk_get_domain( 'http://mydomain.com/some/path' );
```

### Checking if a domain already exists in the database

You may check if a domain already exists by using the `wplk_domain_exists()` function. The function will return boolean
`TRUE` or `FALSE`. You can lookup the domain using any of the methods used in [Getting an existing domain](#getting-an-existing-domain).

```php
// Check using the domain post ID.
if( wplk_domain_exists( 5 ) ){
    // …
}

// Check using the domain host name.
if( wplk_domain_exists( 'mydomain.com' ) ){
    // …
}

// Check using a full URL containing the domain host name.
if( wplk_domain_exists( 'http://mydomain.com/some/path' ) ){
    // …
}
```

### Checking if an existing domain is active

You may check if an existing domain is active using the `wplk_domain_is_active()` function. The function can accept
either the domain ID, the domain host name, a full URL containing the host name, or a `WPLK_Domain` instance. The
function will return boolean `TRUE` or `FALSE` if the domain exists. IF the domain cannot be found, a `WP_Error` object
will be returned.

```php
// Check using the domain post ID.
if( wplk_domain_is_active( 5 ) ){
    // …
}

// Check using the domain host name.
if( wplk_domain_is_active( 'mydomain.com' ) ){
    // …
}

// Check using a full URL containing the domain host name.
if( wplk_domain_is_active( 'http://mydomain.com/some/path' ) ){
    // …
}

// Check using a WPLK_Domain instance.
$domain = wplk_get_domain( 'mydomain.com' );
if( $domain and wplk_domain_is_active( $domain ) ){
    // …
}


```

### Updating a domain

To save any changes made on a `WPLK_Domain` object to the database, you may pass the object to the `wplk_update_domain()`
function. The function will return the domain post ID on success or a `WP_Error` object on failure.

```php
$domain = wplk_get_domain( 'mydomain.com' );

$saved = wplk_update_domain( $domain );

if( is_wp_error( $saved ) ){
    // handle errors…
}
```

### Activating a domain

todo

### Mapping the domain root

todo

### Controlling fallback behaviour on a domain

todo

### Adding a URL to a domain

todo

### Removing a URL from a domain

todo

### Deleting a domain

todo



<!-- todo - Focus on getting basic guides in place (enough for people to get started) then add in technical docs for each function  -->
<!-- ## Functions -->

<!-- | Function | Use | -->
<!-- |---|---| -->
<!-- | [wplk_add_domain()](functions/wplk_add_domain.md) | Add new domains to the database. | -->
<!-- | [fn](functions/fn.md) |  | -->

<!-- ## Objects -->

<!-- There are a few underlying objects used by the API functions which you may use if you prefer to work with objects. These -->
<!-- are included in the API and are safe to use as per these documented examples: -->

<!-- - [WPLK_Domain](objects/wplk_domain.md) object -->
<!-- - [WPLK_Mapping](objects/wplk_mapping.md) object -->