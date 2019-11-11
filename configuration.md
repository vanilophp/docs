# Configuration

Vanilo uses the
[Concord module loader](https://github.com/artkonekt/concord) thus its
configuration can be found in the `config/concord.php` file.

The basic idea is that modules (Laravel Service Providers, actually) can
be configured from within one place with a syntax like that:

```php
<?php

return [
    // the modules array defines which modules to load
    'modules' => [
        // Module_Service_Provider => configuration_array
        Konekt\AppShell\Providers\ModuleServiceProvider::class => [
            'ui' => [
                'name' => 'Vanilo',
                'url' => '/admin/product'
            ]
        ],
        // The configuration part is optional: 
        Vanilo\Framework\Providers\ModuleServiceProvider::class
    ]
];
```

## Configuring The Framework

The following settings are available for the Vanilo Framework:

```php
// config/concord.php
return [
    'modules' => [
        Vanilo\Framework\Providers\ModuleServiceProvider::class => [
            'routes' => [
                    // Admin path (url) prefix (if changing, also change for AppShell)
                    'prefix'     => 'admin', 
                    // middlewares for the route group
                    'middleware' => ['web', 'auth', 'acl'],
                    // Route files to load 
                    'files'      => ['admin'],
                    // Route name prefix
                    'as'         => 'vanilo.'
                ],
            // Currency settings (Multi currency support in v1.5)
            'currency'    => [
                'code'   => 'USD',
                'sign'   => '$',
                // For the format_price() template helper method:
                'format' => '%2$s%1$g' // see sprintf. Amount is the first argument, currency is the second
                /* EURO example:
                'code'   => 'EUR',
                'sign'   => '€',
                'format' => '%1$g%2$s'
                */
            ]            
        ],
        //...    
    ]
];
```

> **OVERRIDING VALUES**: It's important, that you need to include all the default values in overridden
> configuration arrays. Laravel only merges the first level of the configuration array. If you
> partially define a multi-dimensional configuration array, the missing options will not be merged.
>
> Eg. if you want to use a custom route prefix, you need to add all the values for the route config
> (middleware, files, route) as well.
>
> See: [Laravel Package Development](https://laravel.com/docs/5.6/packages#configuration)

## Configuring The Admin Shell

Vanilo's admin is based on
[AppShell](https://github.com/artkonekt/appshell) which is also a
Concord module.

It has the following configuration options:

```php
// config/concord.php
return [
    'modules' => [
        Konekt\AppShell\Providers\ModuleServiceProvider::class => [
            // If false the module's listeners won't be bound to events
            'event_listeners' => true, 
            'routes' => [
                // Admin path (url) prefix (if changing, also change for Vanilo Framework)
                'prefix'     => 'admin', 
                // middlewares for the route group
                'middleware' => ['web', 'auth', 'acl'],
                // Route files to load 
                'files'      => ['web'],
                // Route name prefix
                'as'         => 'appshell.'
            ],
            'breadcrumbs' => true, // to disable breadcrumbs
            'components' => [
                'breadcrumbs' => [
                    // the view for rendering breadcrumbs
                    'view' => 'appshell::widgets.breadcrumbs'
                ]
            ],
            'ui' => [
                // Your app's name to display on admin
                'name' => 'Vanilo',
                // Admin Panel's base (eg. dashboard) URL
                'url'  => '/admin/product'
            ]
        ],
        //...
    ]
];
```
