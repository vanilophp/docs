# Vanilo Admin Installation

The Vanilo Admin package used to be the part of the vanilo/framework package in versions 0.x, 1.x and 2.x.

Beginning with v3.x, the Admin has been extracted from the Framework, and now it is optional.

## Installation

The Admin package can be installed using composer:

1. First, [install the Vanilo Framework](/docs/{{version}}/installation)
2. `composer req vanilo/admin '^4.0'`
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
4. Make sure you have [Laravel Authentication](https://laravel.com/docs/10.x/authentication) set up in the target application, eg. using Laravel Breeze.
5. Set up the [user model](#setting-up-the-user-model) (see instructions below)
6. Create an admin user: `php artisan make:superuser`
7. Install the following npm packages: 
   ```bash
   npm add bootstrap@5.3 alpinejs@3.10 popper.js
   ```
8. Configure the frontend [build tools](#frontend-build)
9. Compile the assets: `npm run dev`

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

### Frontend Build

#### Vite Config

> If you want to use vite, make sure to upgrade the konekt/appshell package to v4.12+ via composer

Update the vite.config.js:

```javascript
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import path from 'path';
import fs from 'fs';
import { join } from 'path';

function createCssDirPlugin() {
    return {
        name: 'create-css-dir',
        buildEnd() {
            const cssDir = join('public', 'css');
            if (!fs.existsSync(cssDir)) {
                fs.mkdirSync(cssDir, { recursive: true });
            }
        }
    };
}

export default defineConfig({
    plugins: [
        laravel({
            input: [
                'resources/js/app.js',
                'vendor/konekt/appshell/src/resources/assets/js/appshell.standalone.esm.js',
                'resources/css/app.css',
                'vendor/konekt/appshell/src/resources/assets/sass/appshell.sass',
            ],
            refresh: true,
        }),
        createCssDirPlugin()
    ],
    resolve: {
        alias: {
            '~bootstrap': path.resolve(__dirname, 'node_modules/bootstrap'),
        },
    },
    build: {
        outDir: 'public',
        rollupOptions: {
            output: {
                assetFileNames: (assetInfo) => {
                    if (assetInfo.name === 'appshell.css') {
                        return 'css/appshell.css';
                    }

                    return 'assets/[name]-[hash][extname]';
                },
            },
        },
        emptyOutDir: false,
    },
});
```

Afterward, create the `resources/views/vendor/appshell/layouts/default/_js.blade.php` file in your application with the following content:

```blade
@vite(['vendor/konekt/appshell/src/resources/assets/js/appshell.standalone.esm.js'])

<style>
    [x-cloak] { display: none !important; }
</style>
```

#### Laravel Mix

1. Install [Laravel Mix](https://laravel-mix.com/docs/6.0/installation)
2. Add Admin's CSS To Laravel Mix:    
   ```javascript
   let mix = require('laravel-mix');
   // webpack.mix.js
   mix.js('resources/js/app.js', 'public/js')
    // Add these 2 lines:
   .js('vendor/konekt/appshell/src/resources/assets/js/appshell.standalone.js', 'public/js/appshell.js')
   .sass('vendor/konekt/appshell/src/resources/assets/sass/appshell.sass', 'public/css')
    // Keep this for the "rest" (usually public frontend)
   .sass('resources/sass/app.scss', 'public/css');
   ```
3. Update the postcss.config.file to ensure compatibility with webpack and laravel-mix:      
    ```javascript
    // From
    export default {
        plugins: {
            tailwindcss: {},
            autoprefixer: {},
        },
    };
    
    // To
    module.exports = {
        plugins: [
            require('autoprefixer')()
        ]
    }
    ```
4. Update the package.json file:  
   If the type field is present either remove it, or rename to "commonjs"    
    ```json
    {
      "type": "module", 
      "scripts": {
        "dev": "mix"
      }
    }
    ```
