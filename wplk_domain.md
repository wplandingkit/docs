# WPLK_Domain

- [Introduction](#introduction)
- [Creating a new domain](#creating-a-new-domain)
- [Retrieving an existing domain](#retrieving-an-existing-domain)
- [Managing URL mappings](#managing-url-mappings)
    - [Working with the root mapping](#working-with-the-root-mapping)
    - [Working with the fallback mapping](#working-with-the-fallback-mapping)
    - [Adding URL mappings](#adding-url-mappings)
    - [Finding URL mappings](#finding-url-mappings)
    - [Removing URL mappings](#removing-url-mappings)
    - [Getting all URL mappings](#getting-all-url-mappings)

## Introduction

The `WPLK_Domain` object is the backbone of domain management and is the underlying component powering all of the API
functions. The class can be used to:

1. Retrieve existing domain instances from the database and;
1. Creation of new instances for configuration and insertion into the database.

## Creating a new domain

You can create a new domain object by instantiating the object with a variety of contructor args.

```php
// Simply pass the domain host name on instantiation.
$domain = new WPLK_Domain( 'mydomain.com' );

// Note, you can also pass a full URL — the object will extract the `mydomain.com` host name in this case.
$domain = new WPLK_Domain( 'http://mydomain.com/some/url' );

// Passing no args to the constructor requires the use of the set_host() method to set the actual domain host name. If
// no host name is set, subsequent calls to $domain->save() will return a WP_Error object and the domain will not be
// saved to the database.
$domain = new WPLK_Domain;
$domain->set_host( 'mydomain.com' );

// It is also possible to use an array when instantiating a new domain object.
$domain = new WPLK_Domain( [
    'host' => 'mydomain.com',
    'is_active' => true, // (optional)
    'enforced_protocol' => 'https', // (optional)
] );
```

Be mindful that instantiating a domain object does not add it to the database. It merely creates an object for you to
further configure and save. Until a domain is explicitly activated and saved to the database, the domain will not handle
requests made to the site.

The following example is a more complete demonstration of how to create and activate a domain. In this example, we are:

1. Creating a new domain object;
1. Setting the post/page to show as the domain's root/home;
1. Setting the active flag on the domain;
1. Saving the domain to the database;
1. Checking for any errors on save.

```php
$domain = new WPLK_Domain( 'mydomain.com' );
$domain->root()->maps_to_post( $post_id );
$domain->activate();

$saved = $domain->save();

if( is_wp_error( $saved ) ){
    // …do something…
}
```

## Retrieving an existing domain

You can retrieve an instance of a domain that already stored in the database using the `WPLK_Domain::get_instance()`
method. The method returns a `WPLK_Domain` instance when it can find a post or `NULL` when it cannot.

The method accepts either a post ID or a domain host name. e.g;

```php
// Using a post ID.
$domain = WPLK_Domain::get_instance( $post_id );

// Using a host name.
$domain = WPLK_Domain::get_instance( 'mydomain.com' );

// Using a full URL (the host name will be extraced from the URL).
$domain = WPLK_Domain::get_instance( 'http://mydomain.com/some/url' );
```

Be mindful that the most performant option is to use the post ID. Using the host name resorts to searching for the
underlying domain post by title.

## Managing URL mappings

There are a few object methods the enable you to add, edit, and remove, URL mappings for a domain. Unless specified
otherwise, these methods all accept or return one or more [WPLK_Mapping](wplk_mapping.md) objects.

| Method | Description |
|---|---|
| mappings() | Returns an array of all dynamic mapping objects, excluding root and fallback mappings. |
| add_mapping() | Add a new mapping. |
| find_mapping() | Find a specific mapping object. |
| find_mappings() | Find multiple mapping objects. |
| remove_mapping() | Remove a mapping. |
| root() | Get the root mapping object. |
| fallback() | Get the fallback mapping object. |

### Working with the root mapping

The root mapping is a requirement for a domain as it specifies what should happen when a user visits the domain root.
e.g; `http://mydomain.com/`.

There are a number of ways to define a root mapping:

```php
$domain = new WPLK_Domain( 'mydomain.com' );

// Passing a post ID or WP_Post object.
$domain->root( $post );

// Passing a callback.
$domain->root( function( WPLK_Mapping $mapping ){
    $mapping->maps_to_term_archive( $term_id, true );
} );

// Passing an array of mapping args
$domain->root( [ 'post_id' => $post_id ] );
```

#### A note on mapping instances

The `root()` method, when called without method args, will return an instance of [WPLK_Mapping](wplk_mapping.md) for
further configuration. If no root mapping is set, the method will create a new mapping instance and assign it to the
domain. Subsequent calls without method args will return the same object.

```php
$domain = new WPLK_Domain( 'mydomain.com' );
$mapping = $domain->root()->maps_to_post( $post_id );

$mapping === $domain->root(); // TRUE (objects are the same instance)
```

Whenever args are passed to the `root(…$args)` method, an new mapping instance is created an assiged to the root. This
effectively resets the root mapping.

```php
$domain = new WPLK_Domain( 'mydomain.com' );
$mapping = $domain->root()->maps_to_post( $post_id );

// This will reset the root mapping object entirely.
$domain->root( [ 'post_it' => $post_id_2 ] );

$mapping !== $domain->root(); // TRUE (objects are different instances)
```

### Working with the fallback mapping

todo

### Adding URL mappings

### Finding URL mappings

### Removing URL mappings

### Getting all URL mappings

###