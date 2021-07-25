# Adjustments

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

Adjustment have an `origin` field that can hold a reference to an
"external source" source. It can be freely used, and can represent eg.
the id of a promotion entry in the database, or a coupon code, etc.

## Adjustables

...

## Adjusters

... eg. FixedAmountFee, PercentageFee

