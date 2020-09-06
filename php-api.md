# PHP API

- [Introduction](#introduction)
- [Functions](#functions)
- [Objects](#objects)
  - [WPLK_Domain](#wplk_domain)
    - [Creating a new domain](#Creating a new domain)
    - [Retrieving an existing domain](#retrieving an existing domain)
  - [WPLK_Mapping](#wplk_mapping)

## Introduction

WP Landing Kit provides a simple PHP API to enable developers to programmatically work with domains. The API makes it
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

## Functions

todo

## Objects

todo

### WPLK_Domain

The `WPLK_Domain` object is the backbone of domain management and is the underlying component powering all of the API
functions. The class can be used to:

1. Retrieve existing domain instances from the database and;
1. Creation of new instances for configuration and insertion into the database.

#### Creating a new domain

```php
// Simply pass the domain host name on instantiation.
$domain = new WPLK_Domain('mydomain.com');
$domain->save();
```

```php
// Note, you can also pass a full URL — the object will extract the `mydomain.com` host name in this case.
$domain = new WPLK_Domain('http://mydomain.com/some/url');
$domain->save();
```

```php
// Passing no args to the constructor requires the use of the set_host() method to set the actual domain host name. If
// no host name is set, subsequent calls to $domain->save() will return a WP_Error object and the domain will not be
// saved to the database.
$domain = new WPLK_Domain;
$domain->set_host('mydomain.com');
$domain->save();
```

```php
$domain = new WPLK_Domain([
    'host' => 'mydomain.com',
    'is_active' => true,
    'enforced_protocol' => 'https',
]);
$domain->save();
```

#### Retrieving an existing domain

todo

### WPLK_Mapping

todo