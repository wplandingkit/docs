# PHP API

- [Introduction](#introduction)
- [Functions](#functions)
- [Objects](#objects)

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

## Functions

todo

## Objects

There are a few underlying objects used by the API functions which you may use if you prefer to work with objects. These
are included in the API and are safe to use as per these documented examples:

- [WPLK_Domain](wplk_domain.md) object
- [WPLK_Mapping](wplk_mapping.md) object