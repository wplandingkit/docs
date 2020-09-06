# PHP API

- [Introduction](#introduction)
- [Functions](#functions)
- [Objects](#objects)
  - [WPLK_Domain](#wplk_domain)
  - [WPLK_Mapping](#wplk_mapping)

## Introduction

WP Landing Kit provides a simple PHP API to enable developers to programmatically work with domains. The API makes it
possible to:

1. Add new domain entries to the database.
1. Edit existing domain entries.
1. Delete domain entries.

When creating or editing a domain, the API provides control over:

1. Domain ownership. i.e; Which WordPress user is the 'author' of the domain.
1. Domain URL mappings. i.e; Which URLs map (or redirect) to which posts, pages, etc.
1. Domain settings. i.e; Protocol enforcement, active status, etc.

### Important!

It is important to keep in mind that the API affects the database and should not be used as part of a typical WordPress
page load. Instead, it is designed to be used at strategic points as a means of automating domain creation/manipulation.
e.g; You may choose to run this after a WooCommerce/Easy Digital Downloads transaction or after a user registers on a
site.

## Functions

todo

## Objects

todo

### WPLK_Domain

todo

```php
$obj = new WPLK_Domain();
$obj->set_host('https://sadfsdf.com');
```

### WPLK_Mapping

todo