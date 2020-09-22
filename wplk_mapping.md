# WPLK_Mapping

- [Introduction](#introduction)
    - [Read only properties](#read-only-properties)
- [Creating a new mapping](#creating-a-new-mapping)
- [Getting the mapping ID](#getting-the-mapping-id)
- [Mapping a URL to a post](#mapping-a-url-to-a-post)
- [Mapping a URL to a term archive](#mapping-a-url-to-a-term-archive)
- [Mapping a URL to a post type archive](#mapping-a-url-to-a-post-type-archive)
- [Mapping a URL to a redirect](#mapping-a-url-to-a-redirect)

## Introduction

`WPLK_Mapping` objects are used to define URL path mapping behaviour for a domain. The class is part of the API and can be used directly if needed but that will likely be a rare case. It is more likely that instances of this class will be modified as a result of working with methods on the [WPLK_Domain](wplk_domain.md) object. 

## Read only properties

The properties on the `WPLK_Mapping` object are **read only**. You may read from them, if you need to, but you cannot write to them directly. 

```php
$mapping = new WPLK_Mapping( 'some/url' );
$mapping->maps_to_post( 1234 );

// This will return the post ID.
$mapping->post_id;

// This will cause an error.
$mapping->post_id = 1234;
```

The class has been designed this way as mappings of various types have specific combinations of required properties. Using the methods outlined below ensures the correct properties are set.

## Creating a new mapping

You may create new mapping objects by instantiating the `WPLK_Mapping` class. 

```php
$mapping = new WPLK_Mapping( 'some/url' );

// Setting the path outside of the constructor.
$mapping = new WPLK_Mapping();
$mapping->set_url_path( 'some/url' );
```

If using a RegEx pattern as the path, you must tell the object the path is RegEx. You may do so using a few different options.

```php
// Specify on instance creation.
$mapping = new WPLK_Mapping( 'some/url/.+', true );

// Specify when setting the path outside of the constructor.
$mapping = new WPLK_Mapping();
$mapping->set_url_path( 'some/url/.+', true );

// Specify using a method.
$mapping = new WPLK_Mapping();
$mapping->set_url_path( 'some/url/.+' );
$mapping->set_is_regex( true );
```

## Getting the mapping ID

The mapping ID is generated automatically but there may be times where you wish to use it — e.g; finding a mapping by ID via a [WPLK_Domain](wplk_domain.md) object. To get the mapping ID, use the `id()` method.

```php
$mapping = new WPLK_Mapping( 'some/url' );
$mapping->id();
```

Note that you can also call the `mapping_id` object property directly but this isn't advisable as the `id()` method will generate a mapping ID if it does not already exist. 

## Mapping a URL to a post

You may map a path to a post of any post type using the `maps_to_post()` method. The method accepts either a post ID or `WP_Post`
object and will return either boolean `TRUE` on success or a `WP_Error` object where the post cannot be found.

```php
$mapping = new WPLK_Mapping( 'some/url' );
$mapping->maps_to_post( $post );
```

## Mapping a URL to a term archive

You may map a path to a taxonomy term archive using the `maps_to_term_archive()` method. The method will accept a term ID or a 
`WP_Term` object and will return either boolean `TRUE` on success or a `WP_Error` object where the term cannot be found.

```php
$mapping = new WPLK_Mapping( 'some/url' );
$mapping->maps_to_term_archive( $term );
```

By default, pagination is supported. You may disable pagination support by passing false as the second method parameter.

```php
$mapping->maps_to_term_archive( $term, false );
```

## Mapping a URL to a post type archive

You may map a path to a post type archive using the `maps_to_post_type_archive()` method. The method will accept a post type slug and will return boolean `TRUE` on success.

```php
$mapping = new WPLK_Mapping( 'some/url' );
$mapping->maps_to_post_type_archive( 'my_post_type' );
```

By default, pagination is supported. You may disable pagination support by passing false as the second method parameter.

```php
$mapping->maps_to_post_type_archive( 'my_post_type', false );
```

## Mapping a URL to a redirect

You may redirect a path using the `redirects_to()` method. The method will accept a URL — either absolute or relative — and an
optional HTTP status code. The method returns boolean `TRUE`.

```php
$mapping = new WPLK_Mapping( 'some/url' );

// Redirect URL can be relative.
$mapping->redirects_to( '/some/path', 302 );

// Or it can be absolute.
$mapping->redirects_to( 'http://example.com/some/path', 302 );
```
