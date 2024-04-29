# Upgrade Between Versions

> For older upgrade guides see:
> - [Upgrade Guide v2.x -> v3.x](upgrade-v3.md)
> - [Upgrade Guide v1.x -> v2.x](upgrade-v2.md)
> - [Upgrade Guide v0.2 -> v1.2](upgrade-v1.md)

## 3.x -> 4.0

For the complete list of changes see the [Changelog of v4.0](https://vanilo.io/docs/4.x/releases#400)

###  PHP & Laravel Requirement Changes

| Vanilo Version | Components                        | Framework                         |
|:---------------|:----------------------------------|:----------------------------------|
| 4.0            | PHP 8.2+ & Laravel 10.43+ or 11.x | PHP 8.2+ & Laravel 10.43+ or 11.x |

**Your Action Item**: Upgrade Laravel & PHP if necessary, bump minimum requirements in your
project's `composer.json` file.

### Upgrade Vanilo

Change the requirements of the Vanilo packages your project uses in your `composer.json` file:

- vanilo/framework: `^4.0` (same applies to all standalone modules like `cart`, `order`, `products`, etc)
- vanilo/admin: `^4.0`

The list of the payment module versions that match with Vanilo 4:

- vanilo/paypal: `^2.0`
- vanilo/adyen: `^3.0`
- vanilo/braintree: `^2.0`
- vanilo/mollie: `^2.0`
- vanilo/netopia: `^3.0`
- vanilo/euplatesc: `^4.0`

### Interface Changes

### Admin Upgrade

In an existing project run `composer req vanilo/admin:^4.0 konekt/appshell:^4.0`

#### Admin Frontend

The v4 Admin's frontend has been upgraded to Bootstrap 5.3.
If you have an existing Laravel mix-based installation, run the following commands to upgrade the packages:

```bash
npm install bootstrap@5.3
npm install @popperjs/core --save
```
