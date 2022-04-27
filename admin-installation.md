# Vanilo Admin Installation

The Vanilo Admin package used to be the part of the vanilo/framework package in versions 0.x, 1.x and 2.x.

Beginning with v3.x, the Admin has been extracted from the Framework, and now it is optional.

## Installation

The Admin package can be installed using composer:

1. First, [install the Vanilo Framework](/docs/{{version}}/installation)
2. `composer req vanilo/admin ^3.0`
3. Edit `config/concord.php` and add this content under the **modules** key:
   ```php
    'modules' => [
        //... preserve the existing content of the file!
        Konekt\AppShell\Providers\ModuleServiceProvider::class => [
            'ui' => [
                'name' => 'Shop Admin', // Your app's name to display on admin
                'url'  => '/admin/product', // Base/Home URL after login (eg. dashboard)
            ],
        ],
        Vanilo\Admin\Providers\ModuleServiceProvider::class,  
    ],
   ```
4. Make sure you have [Laravel Authentication](https://laravel.com/docs/9.x/authentication) set up in the target application, eg. using Laravel Breeze.
5. Set up the user model (see instructions below)
6. Create an admin user: `php artisan make:superuser`
7. Add Admin's CSS To Laravel Mix:
   ```javascript
   // webpack.mix.js
   mix.js('resources/js/app.js', 'public/js')
    // Add these 2 lines:
   .js('vendor/konekt/appshell/src/resources/assets/js/appshell.standalone.js', 'public/js/appshell.js')
   .sass('vendor/konekt/appshell/src/resources/assets/sass/appshell.sass', 'public/css')
    // Keep this for the "rest" (usually public frontend)
   .sass('resources/sass/app.scss', 'public/css');
   ```
8. Install the following npm packages: 
   ```bash
   npm add bootstrap@4.6 jquery vue@2.6 popper.js
   ```
9. Compile the assets with mix: `npm run dev`

### Setting Up The User Model

There are multiple ways of setting up the user models. You can find two possibilities below.
Feel free to use your own solution and/or further customize them.

It is also possible to use separate models for shoppers and admins.

#### Variant 1 - Simple

Modify `App\Models\User` so that it extends Vanilo's user model:

```php
// app/Models/User.php
namespace App\Models;

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
// app/Models/User.php
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

### Step 3 - Register The Model

And add this to you `AppServiceProviders`'s boot method:

```php
// app/Providers/AppServiceProvider.php
$this->app->concord->registerModel(\Konekt\User\Contracts\User::class, \App\Models\User::class);
```
