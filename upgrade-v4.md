# Upgrade Between Versions

> For older upgrade guides see:
> - [Upgrade Guide v2.x -> v3.x](upgrade-v3.md)
> - [Upgrade Guide v1.x -> v2.x](upgrade-v2.md)
> - [Upgrade Guide v0.2 -> v1.2](upgrade-v1.md)

## 3.x -> 4.0

This guide describes how to upgrade an existing Laravel application that uses version 3.x of the Vanilo framework
or Vanilo components to v4.

For the complete list of changes see the [Changelog of Vanilo v4.0](https://vanilo.io/docs/4.x/releases#400)

###  PHP & Laravel Requirements

Vanilo requires the following PHP & Laravel versions:

| Vanilo        | PHP        | Laravel               |
|:--------------|:-----------|:----------------------|
| 4.0           | 8.2 or 8.3 | 10.43 - 10.48 or 11.x |

If your Laravel version is lower than 10.43, then you'll have to upgrade Laravel in your application.

See the:

- [Laravel 10 Upgrade Guide](https://laravel.com/docs/11.x/upgrade), or the
- [Laravel 11 Upgrade Guide](https://laravel.com/docs/11.x/upgrade) for more details.

### Upgrade Vanilo

Change the requirements of the Vanilo packages your project uses in your `composer.json` file:

- vanilo/framework: `^4.0` (same applies to all standalone modules like `cart`, `order`, `products`, etc)
- vanilo/admin: `^4.0`

Afterwards, run:

```php
composer update -W 'vanilo/*'
```

### Payment Gateway Drivers

If your application is using any of the Paypal, Adyen, Braintree, Mollie, Euplatesc or Netopia modules,
you will need to upgrade their versions to your application.

Vanilo 4 requires the following versions of the payment gateway drivers:

- vanilo/paypal: `^2.0`
- vanilo/adyen: `^3.0`
- vanilo/braintree: `^2.0`
- vanilo/mollie: `^2.0`
- vanilo/netopia: `^3.0`
- vanilo/euplatesc: `^4.0`

### Interface Changes

If you're implementing custom classes of the Vanilo interfaces in your application, then you may need to
adjust your code to comply with the changes introduced in Vanilo 4.

For the complete list of breaking changes see the [Vanilo 4 Changelog](https://vanilo.io/docs/4.x/releases#400)
and look for the lines that start with "BC:". (BC = Breaking Changes).

### Admin Upgrade

In an existing project run `composer req vanilo/admin:^4.0 konekt/appshell:^4.0`

#### Admin Frontend

The v4 Admin's frontend has been upgraded to Bootstrap 5.3.
If you have an existing Laravel mix-based installation, run the following commands to upgrade the packages:

```bash
npm install bootstrap@5.3
npm install @popperjs/core --save
```
