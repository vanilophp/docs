# Acl

Vanilo incorporates ACL functionality from the
[konekt/acl](https://github.com/artkonekt/acl) library, which is
actually a "concordified" fork of the
[spatie/laravel-permission](https://github.com/spatie/laravel-permission)
package.

This package allows you to manage user permissions and roles in a database.

Once installed you can do stuff like this:

```php
// Adding permissions to a user
$user->givePermissionTo('create products');

// Adding permissions via a role
$user->assignRole('product manager');

$role->givePermissionTo('create products');
```

Because all permissions will be registered on Laravel's gate, you can
test if a user has a permission with Laravel's default `can()` function:

`$user->can('edit products');`

## Using With Blade

Laravel's native `@can` directive works to check if a user has a certain
permission:

```blade
@can('create products')
  //
@endcan
```

You can also check for roles:

```blade
@role('writer')
    I am a writer!
@else
    I am not a writer...
@endrole
```

Test for any role in a list:

```blade
@hasanyrole($collectionOfRoles)
    I have one or more of these roles!
@else
    I have none of these roles...
@endhasanyrole
<!-- or -->
@hasanyrole('writer|admin')
    I am either a writer or an admin or both!
@else
    I have none of these roles...
@endhasanyrole
```

Test for all roles:

```blade
@hasallroles($collectionOfRoles)
    I have all of these roles!
@else
    I do not have all of these roles...
@endhasallroles
// or
@hasallroles('writer|admin')
    I am both a writer and an admin!
@else
    I do not have all of these roles...
@endhasallroles
```

> For a detailed description of Acl features, refer to the [acl readme](https://github.com/artkonekt/acl).

## Resource Permissions

Vanilo Framework complements the [Laravel Resource](https://laravel.com/docs/5.7/controllers#resource-controllers)
convention by defining resource permissions for all their models:

- 'list *resources*',
- 'create *resources*',
- 'view *resources*',
- 'edit *resources*',
- 'delete *resources*'

## Acl Middleware

All the Admin Panel routes are protected with the `acl` middleware that
checks the resource permissions based on the current action. To make it
clear what it does, here's an example:

When a route hits the `ProductController@index` action, the Acl module
checks for the `list products` permission

**Resource actions vs. permissions mapping example:**

| Action                    | Necessary Permission |
|:--------------------------|:---------------------|
| ProductController@index   | list products        |
| ProductController@create  | create products      |
| ProductController@store   | create products      |
| ProductController@show    | view products        |
| ProductController@edit    | edit products        |
| ProductController@update  | edit products        |
| ProductController@destroy | delete products      |

Resource names are always plural in permissions.

