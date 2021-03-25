# Installation

## Requirements

- Vanilo 3 requires PHP 8.0 or 8.1 and Laravel 9.0 or higher
- json, openssl, PDO, mbstring, tokenizer, xml, ctype PHP extensions
- Supported database engines<sup>*</sup>:
    - **MySQL 5.7** or higher,
    - **MariaDB 10.2.7** or higher,
    - **PostgreSQL 9.2** or higher.

> <sup>*</sup> JSON field types are used, therefore make sure to use at least the required minimum
> version of your preferred DB engine.

### With Composer

> If you're still on PHP 7 and can't upgrade yet, try installing [Vanilo 2](/docs/2.x/installation)

```bash
# Create a new project (optional):
composer create-project --prefer-dist laravel/laravel vaniloapp ^9.0
cd vaniloapp

# Install the vanilo package:
composer require vanilo/framework ^3.1

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
| 1.  | Konekt User Module           | Module | 2.8.0   | konekt.user           | Konekt\User          |
| 2.  | Konekt Address Module        | Module | 2.8.0   | konekt.address        | Konekt\Address       |
| 3.  | Konekt Customer Module       | Module | 2.4.1   | konekt.customer       | Konekt\Customer      |
| 4.  | Vanilo Adjustments Module    | Module | 3.8.2   | vanilo.adjustments    | Vanilo\Adjustments   |
| 5.  | Vanilo Category Module       | Module | 3.8.0   | vanilo.category       | Vanilo\Category      |
| 6.  | Vanilo Product Module        | Module | 3.8.0   | vanilo.product        | Vanilo\Product       |
| 7.  | Vanilo Properties Module     | Module | 3.8.0   | vanilo.properties     | Vanilo\Properties    |
| 8.  | Vanilo Channel Module        | Module | 3.8.0   | vanilo.channel        | Vanilo\Channel       |
| 9.  | Vanilo Cart Module           | Module | 3.8.0   | vanilo.cart           | Vanilo\Cart          |
| 10. | Vanilo Checkout Module       | Module | 3.8.2   | vanilo.checkout       | Vanilo\Checkout      |
| 11. | Vanilo Order Module          | Module | 3.8.0   | vanilo.order          | Vanilo\Order         |
| 12. | Vanilo Payment Module        | Module | 3.8.0   | vanilo.payment        | Vanilo\Payment       |
| 13. | Vanilo Links Module          | Module | 3.8.0   | vanilo.links          | Vanilo\Links         |
| 14. | Vanilo Master Product Module | Module | 3.8.0   | vanilo.master_product | Vanilo\MasterProduct |
| 15. | Vanilo Shipment Module       | Module | 3.8.0   | vanilo.shipment       | Vanilo\Shipment      |
| 16. | Vanilo Taxes Module          | Module | 3.8.0   | vanilo.taxes          | Vanilo\Taxes         |
| 17. | Vanilo Foundation            | Box    | 3.8.2   | vanilo.foundation     | Vanilo\Foundation    |
+-----+------------------------------+--------+---------+-----------------------+----------------------+

```

Configure `.env`, along with a database. (Also create the db if necessary)

Afterwards run the migrations:

```bash
php artisan migrate
```

Vanilo contains about 50 migrations out of the box.

It's not mandatory but recommended to seed the countries table:

```bash
php artisan db:seed --class '\Konekt\Address\Seeds\Countries'
```

## Laravel Auth Support

### Step 1

#### Install Laravel Breeze

Laravel Breeze is a minimal, simple implementation of all of Laravel's authentication features, including login, registration, password reset, email verification, and password confirmation.

```bash
composer require laravel/breeze --dev
php artisan breeze:install
npm install
npm run dev
php artisan migrate
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

In case you don't want to extend the Konekt User, then it's sufficient just to implement its
interface:

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
    
    // Implement these methods from the required Interface:
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
   $this->app->concord->registerModel(\Konekt\User\Contracts\User::class, \App\User::class);
   // On fresh Laravel 8+ installations the class is typically \App\Models\User
```

### Create An Initial Super User

Run command `php artisan make:superuser`.

This will ask several questions and create a proper superuser that you can start working with.

> **NOTE**: Accept `admin` as role for the very first user, because that's the only existing role after installation.

### Add Admin's CSS To Laravel Mix

In `webpack.mix.js` change:
```js
// Keep this for the "rest" (usually public frontend)
mix.js('resources/js/app.js', 'public/js').postCss('resources/css/app.css', 'public/css', [
    require('postcss-import'),
    require('tailwindcss'),
    require('autoprefixer'),
]);

// Add these 2 lines
mix.js('vendor/konekt/appshell/src/resources/assets/js/appshell.standalone.js', 'public/js/appshell.js')
   .sass('vendor/konekt/appshell/src/resources/assets/sass/appshell.sass', 'public/css')
```
