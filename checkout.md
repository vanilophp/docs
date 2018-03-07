# Checkout

Checkout is one of the most opinionated areas of E-commerce sites. Vanilo's
checkout module was designed to be as flexible as possible, and to support all 3
major checkout types:

- One-page
- Accordion
- Multi-page

Other than these, it optionally allows saving incomplete checkouts to db,
session, cache (redis, memcache), etc.

As of v0.2 only the One-page type / without saving has been implemented yet.

## Checkout Data

Checkout has the following data associated:

- Cart (`CheckoutSubject`)
- CheckoutState (enum)
- BillPayer
    - Customer data
    - Billing Address (`Address`)
- Shipping Address (`Address`)

## Checkout Forms

> For a complete checkout form example refer to the [Vanilo Demo repository](https://github.com/vanilophp/demo/tree/0.2/resources/views/checkout)

## Obtain Checkout Instance

There are 3 usual ways you can use to obtain a checkout instance:

**1. Use the `Checkout` facade**
I know, facades are 🌩 & 🚨 & ☣

```php
use App\Http\Requests\CheckoutRequest;
use Vanilo\Cart\Facades\Cart;
use Vanilo\Checkout\Facades\Checkout;

public function submit(CheckoutRequest $request)
{
    Checkout::setCart(Cart::model());
    Checkout::update($request->all());
    //...
}
```

**2. Use the `vanilo.checkout` service**

```php
$checkout = app('vanilo.checkout');
$cart     = app('vanilo.cart');

$checkout->setCart($cart);
```

**3. Let Laravel Container to inject**

> Refer to
> [Container Automatic Injection](https://laravel.com/docs/5.5/container#automatic-injection)
> an for more details

```php
use App\Http\Requests\CheckoutRequest;
use Vanilo\Checkout\Contracts\Checkout;
use Vanilo\Cart\Contracts\Cart;
use Vanilo\Order\Contracts\OrderFactory;

class CheckoutController extends Controller
{
    protected $checkout;
    
    // Laravel Container will inject the actual checkout/cart
    // instances bound to their interfaces
    public function __construct(Checkout $checkout, Cart $cart)
    {
        $this->checkout = $checkout;
        $this->checkout->setCart($cart);
    }
    
    // Actions now can use the checkout
}
```

## Create Order From Checkout

Vanilo Framework extends the Order module's factory with a `createFromCheckout`
method that accepts a pre-validated checkout as parameter:

```php
namespace App\Http\Controllers;

use App\Http\Requests\CheckoutRequest;
use Vanilo\Checkout\Contracts\Checkout;
use Vanilo\Cart\Contracts\Cart;
use Vanilo\Order\Contracts\OrderFactory;

class CheckoutController extends Controller
{
    protected $checkout;
    
    public function __construct(Checkout $checkout, Cart $cart)
    {
        $this->checkout = $checkout;
        $this->checkout->setCart($cart);
    }
    
    // CheckoutRequest contains your app's validation rules
    public function submit(CheckoutRequest $request, OrderFactory $orderFactory)
    {
        $this->checkout->update($request->all());
        $order = $orderFactory->createFromCheckout($this->checkout);
        //...        
    }
}
```

> See
> [Vanilo Demo's CheckoutController](https://github.com/vanilophp/demo/blob/0.2/app/Http/Controllers/CheckoutController.php)
> for a working example.
