# Upgrade Between Versions


> For older upgrade guides see:
> - [Upgrade Guide v4 -> v5](upgrade-v5.md)
> - [Upgrade Guide v3 -> v4](upgrade-v4.md)
> - [Upgrade Guide v2 -> v3](upgrade-v3.md)
> - [Upgrade Guide v1 -> v2](upgrade-v2.md)
> - [Upgrade Guide v0.2 -> v1.2](upgrade-v1.md)

## 5.x -> 6.0

This guide describes how to upgrade an existing Laravel application that uses version 5 of the Vanilo framework
or Vanilo components to version 6.

For the complete list of changes, see the [Changelog of Vanilo 6.0](https://vanilo.io/docs/6.x/releases#600)

###  PHP & Laravel Requirements

Vanilo requires the following PHP & Laravel versions:

| Vanilo | PHP        | Laravel            |
|:-------|:-----------|:-------------------|
| 6.0    | 8.4 or 8.5 | 12.61.1+ or 13.12+ |

If your Laravel version is lower than 12, then you'll have to upgrade Laravel in your application.

See the:

- [Laravel 12 Upgrade Guide](https://laravel.com/docs/12.x/upgrade) for more details.
- [Laravel 13 Upgrade Guide](https://laravel.com/docs/13.x/upgrade) for more details.

### Upgrade Packages

Change the requirements of the Vanilo packages your project uses in your `composer.json` file:

- vanilo/framework: `^6.0` (the same applies to all standalone modules like `cart`, `order`, `products`, etc.)

If you're using the admin package, update its version too:

- vanilo/admin: `^6.0`

Afterward, run:

```bash
composer update -W 'vanilo/*' 'konekt/*'
```

### Run The Migrations

Once the packages are updated, run the migrations:

```bash
php artisan migrate
```

### Payment Gateway Drivers

If your application is using any of the Paypal, Adyen, Braintree, Mollie, Euplatesc or Netopia modules,
you will need to upgrade their versions to your application.

Vanilo 5 requires the following versions of the payment gateway drivers:

- vanilo/stripe: `^4.0`
- vanilo/paypal: `^4.0`
- vanilo/adyen: `^5.0`
- vanilo/braintree: `^4.0`
- vanilo/mollie: `^4.0`
- vanilo/netopia: `^4.0`
- vanilo/euplatesc: `^6.0`


### Interface Changes

If you're implementing custom classes of the Vanilo interfaces in your application, then you may need to
adjust your code to comply with the changes introduced in Vanilo 6.

For the complete list of breaking changes, see the [Vanilo 6 Changelog](https://vanilo.io/docs/6.x/releases#600)
and look for the lines that start with "BC:" - BC = Breaking Change.
