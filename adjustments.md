# Adjustments

> The Adjustments Module has been first released with [Vanilo v3.1](releases.md).

## Overview

"Adjustments" is an abstract concept at first sight, but it solves a few
important topics in ecommerce like:

- Price discounts,
- Shipping fees,
- Tax handling,
- "Paying" with credits, coupons, etc.

Adjustments can be applied to any eloquent model, but typically they're
applied to the total price of an order/cart, or to an order/cart item or
to an order’s shipment.

## Adjustment

An `Adjustment` object tracks an adjustment to the price of an
[Adjustable](#adjustables) (eg. order, order item, shipment).

### Adjustment Types

Every adjustment is classified by type that can be `promotion`,
`shipping`, `tax` or `misc`. The `AdjustmentType` is an
[enum object](enums.md), therefore additional
[types can be added to it](enums.md#customizing-enums).

### Amount, Charges, Credits

Every adjustment has an amount, that can be either positive or negative.

Adjustments that increase the total are called "charges", while those
that decrease the total are called "credits".

Adjustments can also be 0, for example when there's free shipping above
a certain cart value. In this case we still want to track the adjustment
because when the value of the cart changes, the fee needs to be
recalculated.

### Included Adjustments

Adjustments can be considered included or additional. Included means
that the adjustment is included in price of the adjustable
(eg. order, order item).

An additional or "normal" adjustment is applied on top of the original
item price. A shipping fee is a typical example of this type.

A simple example of an included adjustment is a VAT or sales tax.
The amount of VAT is included in the prices, but with the help of
adjustments we can track their value, and based on certain conditions
it needs to be recalculated. As a result, VAT might either be deducted
from the total, or it needs to be applied at a different rate.

### Locking

Adjustments are recalculated upon certain events like modifying the
contents of a cart. Adjustments can be locked to prevent them from being
subsequently modified. Once an adjustment is locked, it will not be
updated when the next recalculation is triggered.

### Origin

Adjustments have an `origin` field that can hold a reference to an
external source. It can be freely used, and can represent the id of a
promotion entry in the database, or a coupon code, etc.

### Adjustment Data

Adjustments can hold an array of arbitrary data that is designed to
configure the concrete adjuster, primarily used at recalculation.

An example is in the built-in `SimpleShippingFee` adjuster that stores
the `amount` and the `freeThreshold` as data. At every recalculation
this data is being used to determine if the value of the
`$adjustable->itemsTotal()` is higher the `freeTreshold`. If it's higher
then the amount of the adjustment will be set to 0, if it's lower, then
the adjustment amount will be the value of the configured `amount`.

```php
$order = Order::create(['items_total' => 10.99]);

$shippingFee = new SimpleShippingFee($value = 6.99, $freeThreshold = 30);
$order->adjustments()->create($shippingFee);

$order->adjustments()->first()->getData();
// [ 'value' => 6.99, 'freeThreshold' => 30 ]

$order->adjustments()->total();
// 6.99

$order->items_total = 31;
$order->recalculateAdjustments();

$order->adjustments()->total();
// 0
```

## Adjustables

Adjustable models are the subject of the adjustment. They can be
virtually anything that has a price. In the Vanilo Framework the
following entities are adjustable out of the box:

- Carts and Cart Items
- Orders and Order Items,
- Shipments.

### Adjustment Collections

Adjustables have an `adjustments()` method which returns an `AdjustmentCollection` instance.
This interface simplifies the handling of adjustments of an adjustable.

Vanilo contains two implementations of the `AdjustmentCollection` interface, but most probably
you'll only meet the `RelationAdjustmentCollection` variant, which works with eloquent models.

```php
$order = Order::find(1);
$order->adjustments();
//= Vanilo\Adjustments\Support\RelationAdjustmentCollection

$order->adjustments()->create(new SimpleShippingFee(4.99));
$order->count();
//1
$order->adjustments()->total();
// 4.99
```

The `total()` method neglects the _included adjustments_ from the total by default.
To have the _included adjustments_ incorporated in the total sum, pass true as parameter to the `total()` method:

```php
$order->adjustments()->create(Adjustment::create(['amount' => 5, 'is_included' => true]));
$order->adjustments()->create(Adjustment::create(['amount' => 10, 'is_included' => false]));
$order->adjustments()->total();
// 10
$order->adjustments()->total(withIncluded: true);
// 15
```

## Adjusters

Adjusters are classes the hold the logic for the adjustment calculation.
Example adjuster classes are `SimpleShippingFee`, `FixedAmountDiscount`,
`PercentageDiscount`, etc.

The base adjustments package contains several adjustments, and it is
encouraged application developers to write their own adjusters that
encapsulate the custom business logic, the concrete implementation
requires.

Adjusters are designed to be configurable and the work with an
`Adjustable` and an `Adjustment`.

The goal of adjusters is to calculate/recalculate adjustments based on
a given adjustable instance. Adjusters don't store the state, they just
take the input (config, adjustable), do the calculation and as a result
they create or update an adjustment.

### Adjuster Configuration

The configuration of the adjuster is when you specify the parameters of
the adjuster. A good example is a shipping fee where you can configure
the cost, and the minimum amount above which the shipping is free:

```php
$shippingFee = new SimpleShippingFee($shippingCost = 6.99, $freeWhenOrderValueIsMoreThan = 30);
```

### Using an Adjuster to Create an Adjustment

Once you have a configured adjuster instance, it can create an
adjustment using an adjustable model, eg. a Cart:

```php
$shippingFee = new SimpleShippingFee(6.99, 30);
$cart = Cart::model();

$adjustment = $shippingFee->createAdjustment($cart);
```

An even better approach is to let the adjustable do the heavy lifting of
the creation and assignment. The code below will create and adjustment,
calculate the values using the `SimpleShippingFee` adjuster and persist
the adjustment in the database:

```php
$cart->adjustments()->create(new SimpleShippingFee(5, 49));
```
