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
composer require vanilo/framework ^3.0

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
+-----+--------------------------+--------+---------+-------------------+-------------------+
| #   | Name                     | Kind   | Version | Id                | Namespace         |
+-----+--------------------------+--------+---------+-------------------+-------------------+
| 1.  | Konekt User Module       | Module | 2.3.0   | konekt.user       | Konekt\User       |
| 2.  | Konekt Address Module    | Module | 2.1.2   | konekt.address    | Konekt\Address    |
| 3.  | Konekt Customer Module   | Module | 2.1.0   | konekt.customer   | Konekt\Customer   |
| 4.  | Vanilo Category Module   | Module | 3.0.0   | vanilo.category   | Vanilo\Category   |
| 5.  | Vanilo Product Module    | Module | 3.0.0   | vanilo.product    | Vanilo\Product    |
| 6.  | Vanilo Properties Module | Module | 3.0.0   | vanilo.properties | Vanilo\Properties |
| 7.  | Vanilo Channel Module    | Module | 3.0.0   | vanilo.channel    | Vanilo\Channel    |
| 8.  | Vanilo Cart Module       | Module | 3.0.0   | vanilo.cart       | Vanilo\Cart       |
| 9.  | Vanilo Checkout Module   | Module | 3.0.0   | vanilo.checkout   | Vanilo\Checkout   |
| 10. | Vanilo Order Module      | Module | 3.0.0   | vanilo.order      | Vanilo\Order      |
| 11. | Vanilo Payment Module    | Module | 3.0.0   | vanilo.payment    | Vanilo\Payment    |
| 12. | Vanilo Foundation        | Box    | 3.0.0   | vanilo.foundation | Vanilo\Foundation |
+-----+--------------------------+--------+---------+-------------------+-------------------+
```

Configure `.env`, along with a database. (Also create the db if necessary)

Afterwards run the migrations:

```bash
php artisan migrate
```

Vanilo contains about 40+ migrations out of the box.

It's not mandatory but recommended to seed the countries table:

```bash
php artisan db:seed --class '\Konekt\Address\Seeds\Countries'
```

## Laravel Auth Support

### Step 1

```bash
composer require laravel/ui
php artisan ui:auth
```

### Step 2

#### Variant 1 - Simple

Modify `App\User` so that it extends Vanilo's user model:

```php
// app/User.php
namespace App;

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

   $this->app->concord->registerModel(\Konekt\User\Contracts\User::class, \App\User::class);
   // On fresh Laravel 8+ installations the class is typically \App\Models\User
```
