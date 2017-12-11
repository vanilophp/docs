# Users

The users in Vanilo are built with the default Laravel user model + auth
scaffolding.

Vanilo adds some extensions, namely:

- The `is_active` flag;
- The `activate()` and `inactivate()` methods;
- The `UserType` enum (admin or client);
- Login data;
- Model related events;
- SoftDelete;
- Profile (broken as of v0.1).

## Laravel Auth Support

In order to keep Laravel auth working but use the extended User model,
you should modify your `App\User` class:

```php
namespace App;

// No need to use Laravel default traits and properties as
// they're already present in the base class
class User extends \Konekt\AppShell\Models\User
{
}
```

And add this to you AppServiceProviders's boot method:

`$this->app->concord->registerModel(\Konekt\User\Contracts\User::class,
\App\User::class);`

**Alternatively,**

you can also delete App\User and tell Laravel auth to use Vanilo's class.

Modify your app's `config/auth.php` file:

```php
    //...
    'providers' => [
        'users' => [
            'driver' => 'eloquent',
            // 'model' => App\User::class <- change this to:
            'model' => Konekt\AppShell\Models\User::class,
        ],
    //...
```

## User's Active Status

The User model has an `is_active` boolean attribute, that is true by
default for newly created users.

You can inactivate a user with the following method:

```php
$user->inactivate();
```

This will emit a `UserWasInactivated` event.

An inactive user can be activated with:

```php
$user->activate();
```

This will emit a `UserWasActivated` event.

## User Type

The `$user->type` field is a `UserType` [enum](enums.md) that can be
either:

- 'client' (default)
- 'admin'

Vanilo doesn't permit non-admin users to log in to the admin panel.

> For modifying the `UserType` enum, refer to the [enums](enums.md) section.

## Login Data

Users have two login related fields:

- `last_login_at` (datetime)
- `login_count` (int)

Every time a user logs in, these two fields will be updated.

## Events

The user module fires the following events:

- `UserWasCreated`
- `UserWasActivated`
- `UserWasInactivated`
- `UserWasDeleted`
