# Installation

## Requirements

- The standalone components require PHP 7.3 and Laravel 6.0 or higher
- The framework requires PHP 7.4 and Laravel 6.18 or higher
- PHP 8 is supported as of v2.1
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
composer require vanilo/framework

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
+-----+--------------------------+--------+---------+-------------------+-------------------+
| #   | Name                     | Kind   | Version | Id                | Namespace         |
+-----+--------------------------+--------+---------+-------------------+-------------------+
| 1.  | Konekt Address Module    | Module | 2.1.0   | konekt.address    | Konekt\Address    |
| 2.  | Konekt Customer Module   | Module | 2.1.0   | konekt.customer   | Konekt\Customer   |
| 3.  | Konekt User Module       | Module | 2.3.0   | konekt.user       | Konekt\User       |
| 4.  | Konekt Acl Module        | Module | 1.5.0   | konekt.acl        | Konekt\Acl        |
| 5.  | Konekt AppShell Box      | Box    | 2.1.0   | konekt.app_shell  | Konekt\AppShell   |
| 6.  | Vanilo Category Module   | Module | 2.1.0   | vanilo.category   | Vanilo\Category   |
| 7.  | Vanilo Product Module    | Module | 2.1.0   | vanilo.product    | Vanilo\Product    |
| 8.  | Vanilo Properties Module | Module | 2.1.1   | vanilo.properties | Vanilo\Properties |
| 9.  | Vanilo Channel Module    | Module | 2.1.0   | vanilo.channel    | Vanilo\Channel    |
| 10. | Vanilo Cart Module       | Module | 2.1.1   | vanilo.cart       | Vanilo\Cart       |
| 11. | Vanilo Checkout Module   | Module | 2.1.0   | vanilo.checkout   | Vanilo\Checkout   |
| 12. | Vanilo Order Module      | Module | 2.2.0   | vanilo.order      | Vanilo\Order      |
| 13. | Vanilo Payment Module    | Module | 2.1.0   | vanilo.payment    | Vanilo\Payment    |
| 14. | Vanilo Framework         | Box    | 2.1.1   | vanilo.framework  | Vanilo\Framework  |
+-----+--------------------------+--------+---------+-------------------+-------------------+
```

Configure `.env`, along with a database. (Also create the db if necessary)

Afterwards run the migrations:

```bash
php artisan migrate
```

Vanilo contains ~20 migrations out of the box

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
use Konekt\Acl\Traits\HasRoles;

class User extends Authenticatable implements UserContract
{
    use HasRoles;
    
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

Depending on which version of Laravel you have and how you've installed Laravel UI, you may need to
add the following packages:

```bash
yarn add bootstrap jquery vue popper.js
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
