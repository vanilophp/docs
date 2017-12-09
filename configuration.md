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
                    'files'      => ['admin']
                ],
            // Currency settings (Multi currency support in v0.5)
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
                // Admin path (url) prefix (if changing, also change for AppShell)
                'prefix'     => 'admin', 
                // middlewares for the route group
                'middleware' => ['web', 'auth', 'acl'],
                // Route files to load 
                'files'      => ['web']
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
