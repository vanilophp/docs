# Vanilo's Admin

The Admin Panel was built on top of the
[AppShell](https://github.com/artkonekt/appshell) library.

AppShell is being composed of the following parts:
- [User Module](users.md)
- [Acl Module](acl.md)
- [Menu Module](https://github.com/artkonekt/menu)
- [Address Module](addresses.md)

Vanilo adds the handling of products and orders.

The Admin Panel has a Bootstrap 4 based UI. Probably it'll be themable
in the future if there'll be such demand.

The Admin's side menu was designed to be extensible, so it's possible to
add new menu items to it, or to alter existing ones.

## Changing Base URL Prefix

Admin URLs start with '/admin/` by default. This can be changed in the
configuration (`config/concord.php`):

```php
// config/concord.php
// Change both occurences
return [
    'modules' => [
        Konekt\AppShell\Providers\ModuleServiceProvider::class => [
            'routes' => [
                'prefix' => 'admin', 
            ],
        ],
        Vanilo\Framework\Providers\ModuleServiceProvider::class => [
            'routes' => [
                'prefix' => 'admin', 
            ],  
        ]
    ]
];
```
## Change Branding

This very minimal right now, but you can change the App's name displayed
on admin, the admin start URL and the icon in top left.

```php
// config/concord.php
return [
    'modules' => [
        Konekt\AppShell\Providers\ModuleServiceProvider::class => [
            'ui' => [
                // Your app's name to display on admin
                'name' => 'Vanilo',
                // Admin Panel's base (eg. dashboard) URL
                'url'  => '/admin/product'
            ]
        ],
    ]
];
```

To set the admin's top left icon add the following file to your project:

`public/images/appshell/logo.svg`


