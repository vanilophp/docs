# Payments Module

## Payables

The payment module handles payments for arbitrary models like orders, subscriptions, etc, commonly
referred to as "payables".

Payable is an interface defined in the `vanilo/contracts` package. Any Eloquent model can implement
this interface and becomes.. guess what.. payable ;)

The `Payable` interface enforces classes to define the following information:

- payable id: order id, subscription id, etc
- payable type: the type of the payable eg `order` - The two fields define the polymorphic relationship
- amount: the amount to be paid for the order, subscription, etc. Eg. `11.99`
- currency: the 3 letter ISO currency code, like `USD` or `EUR`
- billpayer: a `Billpayer` object that contains the billing information (name, address, tax information, etc)

## Payments

Payments in the system are separate entities and are stored in the `payments` table.
Each record/entity represents a single payment transaction. Such a transaction (not in the technical
but in its trivial meaning) can represent various forms of payments:

- A single online payment attempt - can be pending, successful, failed, etc
- A single bank transfer payment - can be pending, paid, timed out, etc
- A single cash payment
- A single refund - partial or complete refund

All payments have a status, and they have a lifecycle - from start until finish.
This lifecycle can span from as low as a few seconds (eg. one click payments) until as high as
months, for example B2B Wire Transfer payments.

Payments are always bound to payables - typically orders - via a polymorphic relationship, therefore
you can handle payments of any eloquent model that implements the payable interface.

Payables can have one or more payments, depending on the course of the event.

Payments have `amount`, `currency` and `amount_paid` fields. The amount is the amount intended to
be paid, whereas the `amount_paid` is the amount that has actually been captured. Amount paid is
typically 0 initially and in case of a successful payment it gets set to the actually settled amount.
The settled amount is the intended `amount` in most of the cases, but depending on the channel, it
can happen that the actual amount differs from the intended amount. This fact can be recorded by
storing the actual amount in a separate field.

## Payment Gateways

## Payment Methods
