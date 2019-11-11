# Release Notes

> For upcoming releases refer to the [Roadmap](roadmap.md).

## 1.0
##### 2019-11-11

- Stable release
- Supports PHP 7.1 - 7.3 & Laravel 5.5 - 5.8
- Added simple product stock
- Added ProductFinder pagination support
- Added Channel Module
- Added custom checkout attributes
- Migration compatibility fixes with bigint user id

## 0.5
##### 2019-02-11

- Arbitrary product properties (eg. color, RAM size, Wheel size, etc)
- Product filtering

## 0.4
##### 2018-11-12

- Product Categories
- Cart can be preserved for a user across devices and logins
- Product sales figures (units sold, last sale date)
- Laravel 5.7 Support
- PHP 7.3 Support (tested against RC)
- Lots of UI improvements

## 0.3
##### 2018-08-19

- Product images
- Custom Product attributes in cart
- Settings/Preferences in admin (arbitrary settings can be added)
- Minimum Laravel is 5.5 if using the Framework, due to Spatie Media Library v7 being incompatible with it
- Individual modules (cart, order, product, etc) still support Laravel 5.4

## 0.2
##### 2018-02-19

- Starting with v0.2 there is an upgrade path to the upcoming versions (0.3, etc).
- Support for Laravel 5.6,
- Bugfixes,
- Cart improvements.

## 0.1
##### 2017-12-11

Vanilo v0.1 was the very first Vanilo release ever.
Consider it as a **TECHNOLOGY PREVIEW**.

This version is not yet ready to create applications with. Feel free to
discover it, play with it, and
[submit issues on github](https://github.com/vanilophp/framework/issues).

### What's in it:

- Concord (foundation for customizing E-commerce components)
- Customers
- Products
- Cart
- Checkout
- Orders
- Admin
    - Users
    - Acl (permissions, roles)
    - Extendable

Please note that Vanilo as of v0.1:
- lacks many features,
- features are incomplete,
- there are shortcuts, bugs, TODOs and other nasty things in the codebase,
- even basic concepts are subject to change,
- 0.1 is not intended to be upgradable to v0.2.

Beginning with version 0.2 Vanilo provides a documented [upgrade path](upgrade.md) up to the 1.0
stable release.

For more details on future releases refer to the [Roadmap](roadmap.md).
