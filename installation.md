# Installation

## Requirements

- Vanilo 4 requires PHP 8.2 or higher and Laravel 10 or 11
- json, openssl, PDO, mbstring, tokenizer, xml, ctype PHP extensions
- Supported database engines<sup>*</sup>:
    - **MySQL 5.7** or higher,
    - **MariaDB 10.2.7** or higher,
    - **PostgreSQL 9.2** or higher,
    - **SQLite 3.35** or higher.

> <sup>*</sup> JSON field types are used, therefore make sure to use at least the required minimum
> version of your preferred DB engine.

### With Composer

```bash
# Create a new project (optional):
composer create-project --prefer-dist laravel/laravel vaniloapp ^11.0
cd vaniloapp

# Install the vanilo package:
composer require vanilo/framework

# Publish module loader config:
php artisan vendor:publish --provider="Konekt\Concord\ConcordServiceProvider" --tag=config
```

Edit `config/concord.php` and add this content:

```php
<?php

return [
    'modules' => [
        Vanilo\Foundation\Providers\ModuleServiceProvider::class
    ]
];
```

The following [Concord](concord.md) modules should be installed now:

`php artisan concord:modules -a`

```
+-----+------------------------------+--------+---------+-----------------------+----------------------+
| #   | Name                         | Kind   | Version | Id                    | Namespace            |
+-----+------------------------------+--------+---------+-----------------------+----------------------+
| 1.  | Konekt User Module           | Module | 3.0.0   | konekt.user           | Konekt\User          |
| 2.  | Konekt Address Module        | Module | 3.4.1   | konekt.address        | Konekt\Address       |
| 3.  | Konekt Customer Module       | Module | 3.2.0   | konekt.customer       | Konekt\Customer      |
| 4.  | Vanilo Adjustments Module    | Module | 4.2.0   | vanilo.adjustments    | Vanilo\Adjustments   |
| 5.  | Vanilo Category Module       | Module | 4.2.0   | vanilo.category       | Vanilo\Category      |
| 6.  | Vanilo Product Module        | Module | 4.2.0   | vanilo.product        | Vanilo\Product       |
| 7.  | Vanilo Properties Module     | Module | 4.2.0   | vanilo.properties     | Vanilo\Properties    |
| 8.  | Vanilo Channel Module        | Module | 4.2.0   | vanilo.channel        | Vanilo\Channel       |
| 9.  | Vanilo Cart Module           | Module | 4.2.0   | vanilo.cart           | Vanilo\Cart          |
| 10. | Vanilo Checkout Module       | Module | 4.2.0   | vanilo.checkout       | Vanilo\Checkout      |
| 11. | Vanilo Order Module          | Module | 4.2.0   | vanilo.order          | Vanilo\Order         |
| 12. | Vanilo Payment Module        | Module | 4.2.0   | vanilo.payment        | Vanilo\Payment       |
| 13. | Vanilo Links Module          | Module | 4.2.0   | vanilo.links          | Vanilo\Links         |
| 14. | Vanilo Master Product Module | Module | 4.2.0   | vanilo.master_product | Vanilo\MasterProduct |
| 15. | Vanilo Shipment Module       | Module | 4.2.0   | vanilo.shipment       | Vanilo\Shipment      |
| 16. | Vanilo Taxes Module          | Module | 4.2.0   | vanilo.taxes          | Vanilo\Taxes         |
| 17. | Vanilo Promotion Module      | Module | 4.2.0   | vanilo.promotion      | Vanilo\Promotion     |
| 18. | Vanilo Foundation            | Box    | 4.2.0   | vanilo.foundation     | Vanilo\Foundation    |
+-----+------------------------------+--------+---------+-----------------------+----------------------+
```

Configure `.env`, along with a database. (Also create the db if necessary)

Afterward, run the migrations:

```bash
php artisan migrate
```

Vanilo contains about 80 migrations out of the box.

It's not mandatory but recommended to seed the countries table:

```bash
php artisan db:seed --class '\Konekt\Address\Seeds\Countries'
```

## Laravel Auth Support

Depending on your needs, Laravel offers you several starter kits for authentication: https://laravel.com/docs/11.x/starter-kits

As a reference, here we're showing you how to install breeze with blade, but you can choose any other solution you like.

### Step 1 - Installing Breeze

```bash
composer require laravel/breeze --dev

php artisan breeze:install
```

### Step 2

#### Variant 1 - Simple

Modify `App\Models\User` so that it extends Vanilo's user model:

```php
// app/Models/User.php
namespace App\Models;

// No need to use Laravel default traits and properties as
// they're already present in the base class exactly as
// they're defined in a default Laravel installation
class User extends \Konekt\User\Models\User
{
}
```

#### Variant 2 - Flexible

In case you don't want to extend the base User, then it's sufficient just to implement its interface:

```php
// app/User.php
// ... The default User model or arbitrary code for your app

// You can use any other base class eg: TCG\Voyager\Models\User
use Illuminate\Foundation\Auth\User as Authenticatable;
use Konekt\User\Contracts\Profile;
use Konekt\User\Contracts\User as UserContract;

class User extends Authenticatable implements UserContract
{
    // ...
    
    // Add these methods to comply with the required Interface:
    public function inactivate()
    {
        $this->is_active = false;
        $this->save();
    }

    public function activate()
    {
        $this->is_active = true;
        $this->save();
    }

    public function getProfile(): ?Profile
    {
        return null;
    }
    
    // ...
}
```

> You may also completely leave empty the implementations of the new methods.

### Step 3 - Register The Model

And add this to you `AppServiceProviders`'s boot method:

```php
// app/Providers/AppServiceProvider.php

   $this->app->concord->registerModel(\Konekt\User\Contracts\User::class, \App\Models\User::class);
```

## Next Steps

Now you have installed the Vanilo Framework, you can start building the application.
Please note that Vanilo comes with no frontend or routes out of the box. As a reference here are a few tips where to
continue:

1. Install the optional [Admin Panel](/docs/{{version}}/admin-installation)
2. Check the sample vanilo application on Github to see how to build the StoreFront: https://github.com/vanilophp/demo