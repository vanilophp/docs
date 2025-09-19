# Upgrade Between Versions

> For older upgrade guides see:
> - [Upgrade Guide v3.x -> v4.x](upgrade-v4.md)
> - [Upgrade Guide v2.x -> v3.x](upgrade-v3.md)
> - [Upgrade Guide v1.x -> v2.x](upgrade-v2.md)
> - [Upgrade Guide v0.2 -> v1.2](upgrade-v1.md)

## 4.x -> 5.0

This guide describes how to upgrade an existing Laravel application that uses version 4.x of the Vanilo framework
or Vanilo components to v5.

For the complete list of changes see the [Changelog of Vanilo v5.0](https://vanilo.io/docs/5.x/releases#500)

###  PHP & Laravel Requirements

Vanilo requires the following PHP & Laravel versions:

| Vanilo | PHP        | Laravel               |
|:-------|:-----------|:----------------------|
| 5.0    | 8.3 or 8.4 | 10.48 or 11.x or 12.x |

If your Laravel version is lower than 10.48, then you'll have to upgrade Laravel in your application.

See the:

- [Laravel 11 Upgrade Guide](https://laravel.com/docs/11.x/upgrade), or the
- [Laravel 12 Upgrade Guide](https://laravel.com/docs/12.x/upgrade) for more details.

### Upgrade Vanilo

Change the requirements of the Vanilo packages your project uses in your `composer.json` file:

- vanilo/framework: `^5.0` (same applies to all standalone modules like `cart`, `order`, `products`, etc)
- vanilo/admin: `^5.0`

Afterward, run:

```php
composer update -W 'vanilo/*'
```

### A Note On Doctrine DBAL

Laravel, until v10 required the doctrine/dbal package for certain types of database migrations.

If you're still on Laravel 10, make sure to install v3.x of the doctrine/dbal package.
For Laravel 11 and 12, the package is not necessary anymore and can be removed unless your app is relying on it for some reason.

### Payment Gateway Drivers

If your application is using any of the Paypal, Adyen, Braintree, Mollie, Euplatesc or Netopia modules,
you will need to upgrade their versions to your application.

Vanilo 5 requires the following versions of the payment gateway drivers:

- vanilo/paypal: `^3.0`
- vanilo/adyen: `^4.0`
- vanilo/braintree: `^3.0`
- vanilo/mollie: `^3.0`
- vanilo/netopia: `^4.0`
- vanilo/euplatesc: `^5.0`

### Interface Changes

If you're implementing custom classes of the Vanilo interfaces in your application, then you may need to
adjust your code to comply with the changes introduced in Vanilo 5.

For the complete list of breaking changes see the [Vanilo 5 Changelog](https://vanilo.io/docs/5.x/releases#500)
and look for the lines that start with "BC:". (BC = Breaking Changes).

### Admin Upgrade

In an existing project run `composer req vanilo/admin:^5.0 konekt/appshell:^4.13 konekt/acl:^2.0 konekt/enum:^4.1 konekt/address:^3.0 konekt/gears:^1.12 konekt/concord:^1.15`

#### Admin Frontend

The v5 Admin's frontend has been upgraded to Bootstrap 5.3.
If you have an existing Laravel mix-based installation, run the following commands to upgrade the packages:

```bash
npm install bootstrap@5.3
npm install @popperjs/core --save
```
