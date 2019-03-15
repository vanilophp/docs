# Installation

## Requirements

- PHP 7.1.3 or higher
- json, openssl, PDO, mbstring, tokenizer, xml, ctype PHP extensions
- Supported database engines<sup>*</sup>:
    - **MySQL 5.7** or higher,
    - **MariaDB 10.2.7** or higher,
    - **PostgreSQL 9.2** or higher.

> <sup>*</sup> JSON field types are used, therefore make sure to use at least the required minimum
> version of your preferred DB engine.

### With Composer

```bash
# Create a new project (optional):
composer create-project --prefer-dist laravel/laravel vaniloapp
cd vaniloapp

# Install the vanilo package:
composer require vanilo/framework 0.5

# Publish module loader config:
php artisan vendor:publish --provider="Konekt\Concord\ConcordServiceProvider" --tag=config
```

Edit `config/concord.php` and add this content:

```php
<?php

return [
    'modules' => [
        Konekt\AppShell\Providers\ModuleServiceProvider::class => [
            'ui' => [
                'name' => 'Vanilo',
                'url' => '/admin/product'
            ]
        ],
        Vanilo\Framework\Providers\ModuleServiceProvider::class
    ]
];
```

The following [Concord](concord.md) modules should be installed now:

`php artisan concord:modules -a`

```
+-----+------------------------+--------+---------+------------------+------------------+
| #   | Name                   | Kind   | Version | Id               | Namespace        |
+-----+------------------------+--------+---------+------------------+------------------+
| 1.  | Konekt Address Module  | Module | 0.9.7   | konekt.address   | Konekt\Address   |
| 2.  | Konekt Customer Module | Module | 0.9.6   | konekt.customer  | Konekt\Customer  |
| 3.  | Konekt User Module     | Module | 0.9.0   | konekt.user      | Konekt\User      |
| 4.  | Konekt Acl Module      | Module | 1.1.0   | konekt.acl       | Konekt\Acl       |
| 5.  | Konekt AppShell Box    | Box    | 1.3.1   | konekt.app_shell | Konekt\AppShell  |
| 6.  | Vanilo Category Module | Module | 0.5.0   | vanilo.category  | Vanilo\Category  |
| 7.  | Vanilo Product Module  | Module | 0.5.0   | vanilo.product   | Vanilo\Product   |
| 8.  | Vanilo Cart Module     | Module | 0.5.0   | vanilo.cart      | Vanilo\Cart      |
| 9.  | Vanilo Checkout Module | Module | 0.5.0   | vanilo.checkout  | Vanilo\Checkout  |
| 10. | Vanilo Order Module    | Module | 0.5.0   | vanilo.order     | Vanilo\Order     |
| 11. | Vanilo Framework       | Box    | 0.5.0   | vanilo.framework | Vanilo\Framework |
+-----+------------------------+--------+---------+------------------+------------------+
```

Configure `.env`, along with a database. (Also create the db if necessary)

Afterwards run the migrations:

```bash
php artisan migrate
```

Vanilo contains ~20 migrations out of the box

It's not mandatory but recommended to seed the countries table:

```bash
php artisan db:seed --class 'Konekt\Address\Seeds\Countries'
```

## Laravel Auth Support

First, Run `php artisan make:auth`

Modify `App\User` so that it extends Vanilo's user model:

```php
// app/User.php
namespace App;

// No need to use Laravel default traits and properties as
// they're already present in the base class exactly as
// they're defined in a default Laravel installation
class User extends \Konekt\AppShell\Models\User
{
}
```

And add this to you `AppServiceProviders`'s boot method:

```php
// app/Providers/AppServiceProvider.php

   $this->app->concord->registerModel(\Konekt\User\Contracts\User::class, \App\User::class);
```

### Create An Initial Super User

Run command `php artisan appshell:super`.

This will ask several questions and create a proper superuser that you can start working with.

> **NOTE**: Accept `admin` as role for the very first user, because that's the only existing role after installation.

### Add Admin's CSS To Laravel Mix

In `webpack.mix.js` change:
```js
mix.js('resources/js/app.js', 'public/js')
    // Add these 2 lines:   
   .js('vendor/konekt/appshell/src/resources/assets/js/appshell.standalone.js', 'public/js/appshell.js')
   .sass('vendor/konekt/appshell/src/resources/assets/sass/appshell.sass', 'public/css')
    // Keep this for the "rest" (usually public frontend)
   .sass('resources/sass/app.scss', 'public/css');
```

Now compile the assets with mix:

```bash
yarn install
yarn run dev
```

> **TIP:** If you have no yarn on your system refer to the [Install yarn page](https://yarnpkg.com/en/docs/install).

## Go To Admin

After this you can start serving the site with

`php artisan serve`

Now navigate to http://127.0.0.1:8000/admin/customer. You'll be able to
log in with the user just created on the console.

> You may expect to have a store frontend here, but there's none out of the box, and this is
> intentional, so that you can build one exactly your way. To see an example storefront
> implementation for Vanilo go to [the demo project](https://github.com/vanilophp/demo)
