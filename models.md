# Models

Vanilo models are usual
[Eloquent Models](https://laravel.com/docs/10.x/eloquent) prevalent in
Laravel apps.

Vanilo's explicit aim is to refrain from deviations from Laravel's
common sense. But there are some, admittedly. One of them is
[Concord](concord.md) and the fact that every model defined in Vanilo
modules has a default interface (contract) defined.

To see a list of models and their contracts use this command:

```
$> php artisan concord:models

+----------------+-------------------------------------------+----------------------------------------+
| Entity         | Contract                                  | Model                                  |
+----------------+-------------------------------------------+----------------------------------------+
| Address        | Konekt\Address\Contracts\Address          | Vanilo\Foundation\Models\Address       |
| Billpayer      | Vanilo\Order\Contracts\Billpayer          | Vanilo\Order\Models\Billpayer          |
| Cart           | Vanilo\Cart\Contracts\Cart                | Vanilo\Cart\Models\Cart                |
| CartItem       | Vanilo\Cart\Contracts\CartItem            | Vanilo\Cart\Models\CartItem            |
| Channel        | Vanilo\Channel\Contracts\Channel          | Vanilo\Channel\Models\Channel          |
| Country        | Konekt\Address\Contracts\Country          | Konekt\Address\Models\Country          |
| Customer       | Konekt\Customer\Contracts\Customer        | Vanilo\Foundation\Models\Customer      |
| Invitation     | Konekt\User\Contracts\Invitation          | Konekt\User\Models\Invitation          |
| Order          | Vanilo\Order\Contracts\Order              | Vanilo\Foundation\Models\Order         |
| OrderItem      | Vanilo\Order\Contracts\OrderItem          | Vanilo\Order\Models\OrderItem          |
| Organization   | Konekt\Address\Contracts\Organization     | Konekt\Address\Models\Organization     |
| Payment        | Vanilo\Payment\Contracts\Payment          | Vanilo\Payment\Models\Payment          |
| PaymentHistory | Vanilo\Payment\Contracts\PaymentHistory   | Vanilo\Payment\Models\PaymentHistory   |
| PaymentMethod  | Vanilo\Payment\Contracts\PaymentMethod    | Vanilo\Payment\Models\PaymentMethod    |
| Person         | Konekt\Address\Contracts\Person           | Konekt\Address\Models\Person           |
| Product        | Vanilo\Product\Contracts\Product          | Vanilo\Foundation\Models\Product       |
| Profile        | Konekt\User\Contracts\Profile             | Konekt\User\Models\Profile             |
| Property       | Vanilo\Properties\Contracts\Property      | Vanilo\Properties\Models\Property      |
| PropertyValue  | Vanilo\Properties\Contracts\PropertyValue | Vanilo\Properties\Models\PropertyValue |
| Province       | Konekt\Address\Contracts\Province         | Konekt\Address\Models\Province         |
| Taxon          | Vanilo\Category\Contracts\Taxon           | Vanilo\Foundation\Models\Taxon         |
| Taxonomy       | Vanilo\Category\Contracts\Taxonomy        | Vanilo\Foundation\Models\Taxonomy      |
| User           | Konekt\User\Contracts\User                | Konekt\User\Models\User                |
+----------------+-------------------------------------------+----------------------------------------+
```

## Using Custom Models

To use a customized model in your application instead of the default one,
register it in `AppServiceProvider::boot()` method:

```php
// app/Providers/AppServiceProvider.php
namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Vanilo\Product\Contracts\Product as ProductContract;
use Vanilo\Product\Contracts\Order as OrderContract;

class AppServiceProvider extends ServiceProvider
{

    public function boot()
    {
        $this->app->concord->registerModel(
            ProductContract::class, \App\Models\Product::class
        );
        $this->app->concord->registerModel(
            OrderContract::class, \App\Models\Order::class
        );
    }
}
```

If the product model is used in another model's relation, (eg.
`CartItem`) then even those related objects will return `App\Models\Product`
from now on.

> Depending on the nature of the changes, it might be necessary to
> customize the model's related [form requests](form-requests.md) as well.

Concord also re-wires the interface with Laravel's Container:

```php
use Vanilo\Product\Contracts\Product;

$product = app()->make(Product::class);
echo get_class($product);
// App\Models\Product
```

> Note that `App\Models\Product` should extend the original model class ie.
> `Vanilo\Products\Models\Product`. It's not a requirement however, the
> only requirement is that the new class must also implement the Product
> interface (`Vanilo\Product\Contracts\Product`).

There is a
[detailed example](https://konekt.dev/concord/1.8/models#detailed-example)
available in the Concord Documentation.
