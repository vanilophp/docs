# Concord

Vanilo uses Concord for managing their modules.

Concord is a **Laravel Extension** that serves as a foundation to build
**Modules for Laravel Applications** on top of Laravel's built in *Service
Providers*.

Modules are usually external libraries (aka extensions) installed with
composer in the target application.

For more details check Concord on
[github](https://github.com/artkonekt/concord) or refer to its
[Documentation](https://artkonekt.github.io/concord).

When you're using Vanilo for your web application or API, it's likely
that you won't interact much with Concord.

However if you plan to create re-usable extensions for Vanilo, Concord
provides quite several solutions for that.

## Concord Overview

The most important information is that Concord modules are Laravel
[Service Providers](https://laravel.com/docs/5.5/providers) with some
extra functionality. Concord also uses conventions (customizable) so
that modules are using an identical structure.

### Listing Modules

Use the `php artisan concord:modules -a` command to list the installed
modules.

### Contents Of A Module

A module encapsulates "parts" like:

- models (Eloquent),
- migrations, seeds,
- views,
- routes,
- middleware,
- controllers,
- requests,
- frontend assets,
- any other class or file.

### Module Directory Structure

Default convention:

```
module-src/
    Console/
        Commands/
    Contracts/
    Events/
    Exceptions/
    Helpers/
    Http/
        |-- Controllers/
        |-- Middleware/
        |-- Requests/
        |-- Resources/
    Jobs/
    Listeners/
    Models/
    Providers/
        |-- ModuleServiceProvider.php
        |-- EventServiceProvider.php
    Services/
    resources/
        |-- assets/
        |-- config/
            |-- module.php
        |-- database/
            |-- migrations/
            |-- seeds/
        |-- lang/
        |-- routes/
            |-- api.php
            |-- web.php
        |-- views/
        |-- manifest.php
```

## Customization

One of Concord's main goals is to enable applications to easily
customize the modules.

This can be achieved by:

- overriding/extending eloquent models,
- extending enums and/or changing their defaults,
- ignoring migrations if necessary,
- altering views,
- modifying/disabling routes,
- disabling event listener bindings

### Customizing Eloquent Models

Eloquent models are likely the most wanted targets when customizing a
module.

Concord modules define an interface for every Eloquent model. If you
want to extend a model you can tell concord to use another class for
that interface:

```php
concord()->registerModel(\Module\Contracts\Model::class, \App\ExtendedModel::class);
```

This results that if the model is used in a relation, the extended model
class will be used even there.

To get a list of models available for customization use the command:

```bash
$> php artisan concord:models

+--------------+---------------------------------------+------------------------------------+
| Entity       | Contract                              | Model                              |
+--------------+---------------------------------------+------------------------------------+
| Address      | Konekt\Address\Contracts\Address      | Vanilo\Address\Models\Address      |
| Cart         | Vanilo\Cart\Contracts\Cart            | Vanilo\Cart\Models\Cart            |
| CartItem     | Vanilo\Cart\Contracts\CartItem        | Vanilo\Cart\Models\CartItem        |
| Country      | Konekt\Address\Contracts\Country      | Konekt\Address\Models\Country      |
| Customer     | Konekt\Customer\Contracts\Customer    | Vanilo\Framework\Models\Customer   |
| Order        | Vanilo\Order\Contracts\Order          | Vanilo\Order\Models\Order          |
| OrderItem    | Vanilo\Order\Contracts\OrderItem      | Vanilo\Order\Models\OrderItem      |
| Organization | Konekt\Address\Contracts\Organization | Konekt\Address\Models\Organization |
| Permission   | Konekt\Acl\Contracts\Permission       | Konekt\Acl\Models\Permission       |
| Person       | Konekt\Address\Contracts\Person       | Konekt\Address\Models\Person       |
| Product      | Vanilo\Product\Contracts\Product      | Vanilo\Framework\Models\Product    |
| Profile      | Konekt\User\Contracts\Profile         | Konekt\User\Models\Profile         |
| Province     | Konekt\Address\Contracts\Province     | Konekt\Address\Models\Province     |
| Role         | Konekt\Acl\Contracts\Role             | Konekt\Acl\Models\Role             |
| User         | Konekt\User\Contracts\User            | Konekt\AppShell\Models\User        |
+--------------+---------------------------------------+------------------------------------+
```

For more details refer to the Concord
[Models Documentation](https://artkonekt.github.io/concord/#/models).
