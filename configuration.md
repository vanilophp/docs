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
        Vanilo\Foundation\Providers\ModuleServiceProvider::class
    ]
];
```

## Configuring The Framework

The following settings are available for the Vanilo Framework:

```php
// config/concord.php
return [
    'modules' => [
        Vanilo\Foundation\Providers\ModuleServiceProvider::class => [
            // Currency settings
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
> See: [Laravel Package Development](https://laravel.com/docs/9.x/packages#configuration)
