# Enums

Vanilo uses enums for quite several occasions using the
[Konekt Enum](https://github.com/artkonekt/enum) library and it's
[Eloquent Binding](https://github.com/artkonekt/enum-eloquent). Enums
are first class citizens in Concord in order to enable
applications to customize them.

## What's An Enum?

Enums are handy when a variable (especially a method parameter) can only
take one out of a small set of possible values.

Examples would be things like type constants (contract status:
“permanent”, “temp”, “apprentice”), or flags (“execute now”, “defer
execution”).

**Quick Example:**

```php
class Status extends \Konekt\Enum\Enum
{
    const PENDING     = 'pending';
    const IN_PROGRESS = 'in progress';
    const COMPLETE    = 'complete';
    const FAILED      = 'failed';
}
```

Enum is an object with value from one of those constants:

```php
$status = new Status('pending');
echo $status->value();
// 'pending'
echo $status->isPending(); // <- one of the nice enum features
// true
```

> For more details refer to the
> [Enum Documentation](https://artkonekt.github.io/enum/#/create).

## Enums As Model Fields

Similarly to Laravel's
[attribute casting](https://laravel.com/docs/5.7/eloquent-mutators#attribute-casting)
feature, eloquent model fields can automatically be casted to-from
enums, using the
[enum-eloquent](https://github.com/artkonekt/enum-eloquent) library.

**Example:**

```php
class Order extends Model
{
    use CastsEnums;

    protected $enums = [
        'status' => Status::class
    ];
}

$order = Order::create([
    'status' => 'pending'
]);

// The status attribute will be an enum object:
echo get_class($order->status);
// output: App\Status

echo $order->status->value();
// output: 'pending'
```

## Listing Enums

To see a list of registered enums and their contracts use this command:

```
$> php artisan concord:enums

+------------------+-------------------------------------------+----------------------------------------+
| Shorthand        | Contract                                  | Concrete                               |
+------------------+-------------------------------------------+----------------------------------------+
| address_type     | Konekt\Address\Contracts\AddressType      | Konekt\Address\Models\AddressType      |
| cart_state       | Vanilo\Cart\Contracts\CartState           | Vanilo\Cart\Models\CartState           |
| checkout_state   | Vanilo\Checkout\Contracts\CheckoutState   | Vanilo\Checkout\Models\CheckoutState   |
| customer_type    | Konekt\Customer\Contracts\CustomerType    | Konekt\Customer\Models\CustomerType    |
| gender           | Konekt\Address\Contracts\Gender           | Konekt\Address\Models\Gender           |
| gravatar_default | Konekt\AppShell\Contracts\GravatarDefault | Konekt\AppShell\Models\GravatarDefault |
| name_order       | Konekt\Address\Contracts\NameOrder        | Konekt\Address\Models\NameOrder        |
| order_status     | Vanilo\Order\Contracts\OrderStatus        | Vanilo\Order\Models\OrderStatus        |
| product_state    | Vanilo\Product\Contracts\ProductState     | Vanilo\Product\Models\ProductState     |
| province_type    | Konekt\Address\Contracts\ProvinceType     | Konekt\Address\Models\ProvinceType     |
| user_type        | Konekt\User\Contracts\UserType            | Konekt\User\Models\UserType            |
+------------------+-------------------------------------------+----------------------------------------+
```

As you see, similarly to [models](models.md), enums also have a "root"
contract (interface) that can be used for overriding enums.

## Customizing Enums

A modified enum can be used in your application instead of the default
one:

**1. Create The Extended Class:**

```php
// app/OrderStatus.php:
namespace App;

class OrderStatus extends \Vanilo\Order\Models\OrderStatus
{
    // Adding a new value:
    const CHECKED_OUT = 'checked_out';
    
    // Changing the default value:
    const __default = self::CHECKED_OUT;    
}
```

**2. Register With Concord:**

```php
// app/Providers/AppServiceProvider.php
namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Vanilo\Order\Contracts\OrderStatus as OrderStatusContract;
use App\OrderStatus;

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        // Replace the OrderStatus enum with the app's one:
        $this->app->concord->registerEnum(
            OrderStatusContract::class,
            OrderStatus::class
        );
    }
}
```

If the original enum is being used anywhere else in the system, the
updated class ie. `App\OrderStatus` will be returned from now on.

Concord also re-wires the interface with Laravel's Container:

```php
use Vanilo\Order\Contracts\OrderStatus;

$status = app()->make(OrderStatus::class);
echo get_class($status);
// App\OrderStatus
```

> Note that `App\OrderStatus` should extend the original enum class ie.
> `Vanilo\Order\Models\OrderStatus`.

There is a
[detailed example](https://artkonekt.github.io/concord/#/enums?id=extending-enums)
available in the Concord Documentation.

