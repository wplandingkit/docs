# WPLK_Domain

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

There are a few object methods the enable you to add, edit, and remove, URL mappings for a domain.