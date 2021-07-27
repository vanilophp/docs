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

## Payment Methods

Each payment has a payment method associated that facilitates the
initiation and the tracking of the payment.

`PaymentMethod` is an eloquent model and is defined by the users of the
shop, typically at the admin area. A payment method consists of the
combination of a payment gateway and a unique configuration.

## Payment Gateways

The vast majority of online payments happens through payment gateways
like PayPal, Adyen, Stripe, Klarna just to name a few. The Payment
module was implemented so that new payment gateway drivers can be
implemented as plugins.

The Vanilo Payment package contains a single gateway implementation and
that is the `NullGateway` - to be used for offline payments.

The rest of the gateways are implemented as separate packages:

- [PayPal](https://github.com/vanilophp/paypal)
- [Stripe](https://github.com/vanilophp/stripe)
- [SimplePay](https://github.com/vanilophp/simplepay)
- [EuPlatesc](https://github.com/vanilophp/euplatesc)
- [Netopia](https://github.com/vanilophp/netopia)

### Implementing New Payment Gateways

The payment module was designed to be able to handle payment gateways
implemented independently by developers.

To create a new payment gateway the following steps are required:

- Implement a `PaymentGateway` (based on the interface)
- Register the new gateway with `PaymentGateways::register()`

```
use Vanilo\Payment\PaymentGateways;
use Vanilo\Paypal\PaypalPaymentGateway;

PaymentGateways::register('paypal', PaypalPaymentGateway::class);
```
