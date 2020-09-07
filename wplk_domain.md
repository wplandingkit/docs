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

// Chaining mapping methods.
$domain->root()->maps_to_post( $post_id );

// Passing a post ID or WP_Post object.
$domain->root( $post );

// Passing a callback.
$domain->root( function( WPLK_Mapping $mapping ){
    $mapping->maps_to_term_archive( $term_id, true );
} );

// Passing an array of mapping args
$domain->root( [ 'post_id' => $post_id ] );
```

#### A note on how root mapping instances are handled

The `root()` method, when called without method args, will return an instance of [WPLK_Mapping](wplk_mapping.md) for
further configuration. If no root mapping is set, the method will create a new mapping instance and assign it to the
domain. If method args are passed on this call, they'll be used to configure the newly created instance. Subsequent
calls without method args will return the same object.

```php
$mapping = $domain->root()->maps_to_post( $post_id );

// TRUE (objects are the same instance)
$mapping === $domain->root();
```

Whenever args are passed to the `root(…$args)` method, an new mapping instance is created an assiged to the root. This
effectively resets the root mapping.

```php
$mapping = $domain->root()->maps_to_post( $post_id );

// This will reset the root mapping object entirely.
$domain->root( [ 'post_it' => $post_id_2 ] );

// TRUE (objects are different instances)
$mapping !== $domain->root();
```

### Working with the fallback mapping

The fallback mapping functions exactly the same as the root method (see [Working with the root mapping](#working-with-the-root-mapping)).
The only difference here is that you would call the `fallback()` method as opposed to the `root()` method. e.g;

```php
$domain = new WPLK_Domain( 'mydomain.com' );
$domain->fallback()->redirects_to( $url, $http_status );
```

### Adding URL mappings

You may add as many URL mappings as you desire. The only URL mapping that is required is the root mapping so you don't
have to add any if you don't need to.

URL mappings are added to the domain in the order they are registered in the code and when matching a request to a
mapping, the plugin will start at the root, work through all mappings in order, then check the fallback mapping. The
first URL mapping it finds to match a users' request will be used.

URL mappings are added using the `add_mapping()` method. The method can be used a number of ways:

```php
$domain = new WPLK_Domain( 'mydomain.com' );

// Pass no args and manipulate the returned instance.
$mapping = $domain->add_mapping();
$mapping->set_url_path( 'some/url' );
$mapping->maps_to_post( $post_id );

// Pass only a URL path and manipulate the returned instance.
$mapping = $domain->add_mapping( 'some/url' );
$mapping->maps_to_post( $post_id );

// Map a post ID to a URL path.
$domain->add_mapping( 'some/url', $post_id );

// Map a post object to a URL path.
$domain->add_mapping( 'some/url', get_post( $post_id ) );

// Use a callback to configure the mapping object.
$domain->add_mapping( function( WPLK_Mapping $mapping){
    $mapping->set_url_path( 'some/url' )->maps_to_post( $post_id );
} );

// Use RegEx in the URL.
$domain->add_mapping( 'some/url/.+', $post_id, true );

// Map using a URL path and a callback.
$domain->add_mapping( 'some/url', function( WPLK_Mapping $mapping){
    $mapping->maps_to_post( $post_id );
} );

// Pass a WPLK_Mapping instance.
$mapping = new WPLK_Mapping( 'some/url' );
$mapping->maps_to_post( $post_id );
$domain->add_mapping( $mapping );

// Pass a WPLK_Mapping instance and configure it using a callback.
$mapping = new WPLK_Mapping( 'some/url' );
$domain->add_mapping( $mapping, function( WPLK_Mapping $mapping){
   $mapping->maps_to_post( $post_id );
} );

// Pass an array of WPLK_Mapping args.
$domain->add_mapping( [
    'url_path' => 'some/url',
    'post_type' => $post_type,
    'post_id' => $post_id,
] );

// Pass an array of WPLK_Mapping args and configure using a callback.
$domain->add_mapping( [
    'url_path' => 'some/url',
    'post_type' => $post_type,
], function( WPLK_Mapping $mapping){
    $mapping->maps_to_post( $post_id );
} );
```

### Finding URL mappings

If you need to find a single mapping already on the domain object, you may do so using the `find_mapping()` method. The
method will return an instance of `WPLK_Mapping` or `FALSE` where it cannot find the mapping.

You can search for a mapping by either the mapping ID or using a callback function to compare data on the mapping
objects. If using a callback, returning `TRUE` from within the callback will stop the search and the function will
return that particular object being evaluated.

```php
$added_mapping = $domain->add_mapping( 'some/url', $post_id );

// Searching via mapping ID.
$mapping = $domain->find_mapping( $added_mapping->mapping_id );

// Searching via callback.
$mapping = $domain->find_mapping( function( WPLK_Mapping $mapping ){
    return $mapping->url_path == 'some/url';
} );
```

If you need to find multiple mapping objects, you may pass callback to the `find_mappings()` method. This method will
loop through all mappings — excluding the root and fallback mappings — and return an array of WPLK_Mapping objects that
match the conditions set within the callback. If no matches are found, the method returns an empty array.

```php
$domain->add_mapping( 'some/url', $post_id );
$domain->add_mapping( 'some/other/url', $post_id_2 );
$domain->add_mapping( 'some/third/url', $post_id_3 );

// Searching for any mappings that match.
$mappings = $domain->find_mapping( function( WPLK_Mapping $mapping ){
    if($mapping->url_path === 'some/url'){
        return true;
    }

    if($mapping->url_path === 'some/other/url'){
        return true;
    }

    return false;
} );

count( $mappings ) === 2; // TRUE
```

### Removing URL mappings

### Getting all URL mappings

###