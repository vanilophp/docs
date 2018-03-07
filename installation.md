# Installation

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
+-----+------------------------+--------+---------+------------------+------------------+
| #   | Name                   | Kind   | Version | Id               | Namespace        |
+-----+------------------------+--------+---------+------------------+------------------+
| 1.  | Konekt Address Module  | Module | 0.9.6   | konekt.address   | Konekt\Address   |
| 2.  | Konekt Customer Module | Module | 0.9.5   | konekt.customer  | Konekt\Customer  |
| 3.  | Konekt User Module     | Module | 0.9.0   | konekt.user      | Konekt\User      |
| 4.  | Konekt Acl Module      | Module | 1.0.0   | konekt.acl       | Konekt\Acl       |
| 5.  | Konekt AppShell Box    | Box    | 0.9.6   | konekt.app_shell | Konekt\AppShell  |
| 6.  | Vanilo Address Module  | Module | 0.2.0   | vanilo.address   | Vanilo\Address   |
| 7.  | Vanilo Product Module  | Module | 0.2.0   | vanilo.product   | Vanilo\Product   |
| 8.  | Vanilo Cart Module     | Module | 0.2.0   | vanilo.cart      | Vanilo\Cart      |
| 9.  | Vanilo Checkout Module | Module | 0.2.0   | vanilo.checkout  | Vanilo\Checkout  |
| 10. | Vanilo Order Module    | Module | 0.2.1   | vanilo.order     | Vanilo\Order     |
| 11. | Vanilo Framework       | Box    | 0.2.0   | vanilo.framework | Vanilo\Framework |
+-----+------------------------+--------+---------+------------------+------------------+
```

Configure `.env`, along with a database. (Also create the db if necessary)

Afterwards run the migrations:

```bash
php artisan migrate
```

Vanilo contains ~20 migrations out of the box

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
   $this->app->concord->registerModel(\Konekt\User\Contracts\User::class, \App\User::class);
```

### Create An Initial Super User

Run command `php artisan appshell:super`.

This will ask several questions and create a proper superuser that you can start working with.

### Add Admin's CSS To Laravel Mix

In `webpack.mix.js` change:
```js
mix.js('resources/assets/js/app.js', 'public/js')
   // Add this line:
   .scripts([
           'public/js/app.js',
           'vendor/konekt/appshell/src/resources/assets/js/appshell.js'
       ], 'public/js/app.js')
   // And replace this line:
   //.sass('resources/assets/sass/app.scss', 'public/css');
   // With this one:
    .sass('vendor/konekt/appshell/src/resources/assets/sass/appshell.sass', 'public/css');
```

Remove the omnipresent Vue instance from Laravel's default app.js file:

```javascript
//resources/assets/js/app.js:
/**
 * First we will load all of this project's JavaScript dependencies which
 * includes Vue and other libraries. It is a great starting point when
 * building robust, powerful web applications using Vue and Laravel.
 */

// REMOVE/COMMENT THIS LINE:
require('./bootstrap');
// ADD THIS LINE (jquery is needed):
window.$ = window.jQuery = require('jquery');

window.Vue = require('vue');

/**
 * Next, we will create a fresh Vue application instance and attach it to
 * the page. Then, you may begin adding components to this application
 * or customize the JavaScript scaffolding to fit your unique needs.
 */

Vue.component('example-component', require('./components/ExampleComponent.vue'));

// REMOVE/COMMENT THESE 3 LINES:
const app = new Vue({
    el: '#app'
});
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
