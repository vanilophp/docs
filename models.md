# Models

Vanilo models are usual
[Eloquent Models](https://laravel.com/docs/5.5/eloquent) prevalent in
Laravel apps.

Vanilo's explicit aim is to refrain from deviations from Laravel's
common sense. But there are some, admittedly. One of them is
[Concord](concord.md) and the fact that every model defined in Vanilo
modules (components) has a default interface (contract) defined.

To see a list of models and their contracts use this command:

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

## Using Custom Models

To use a customized model in your application instead of the default one,
register it in `AppServiceProvide`'s `boot()` method:

```php
// app/Providers/AppServiceProvider.php
namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Vanilo\Product\Contracts\Product as ProductContract;

class AppServiceProvider extends ServiceProvider
{

    public function boot()
    {
        $this->app->concord->registerModel(
            ProductContract::class, \App\Product::class
        );
    }
}
```

If the product model is used in another model's relation, (eg.
`CartItem`) then event those related objects will return `App\Product`
from now on.

Concord also re-wires the interface with Laravel's Container:

```php
use Vanilo\Product\Contracts\Product;

$product = app()->make(Product::class);
echo get_class($product);
// App\Product
```

> Note that `App\Product` should extend the original model class ie. `Vanilo\Products\Models\Product`.

There is a
[detailed example](https://artkonekt.github.io/concord/#/models?id=detailed-example)
available in the Concord Documentation.
