# Shipping

> The Shipment Module has been released with [Vanilo v3.0](releases.md).

## Overview

The shipment module was designed to support the
[parcel delivery](https://en.wikipedia.org/wiki/Package_delivery) needs of
E-commerce businesses.

In the transportation planning jargon, this is also commonly referred to
as [Last mile transportation](https://en.wikipedia.org/wiki/Last_mile_(transportation)).

Albeit many shops, especially those with high shipping volumes need to deal
with [Freight forwarding](https://en.wikipedia.org/wiki/Freight_forwarder),
this module was not intended to support that part of the supply chain, but
it can serve as a data source for such operations.

## Shippables

The shipment module handles payments for arbitrary Eloquent models like orders
or individual products, etc. commonly referred to as "shippables".

`Shippable` is an interface defined in the `vanilo/contracts` package.
Any Eloquent model can implement this interface.

## Carriers

The `Carrier` model is stored in the `carriers` table and typically represents
a company that performs parcel delivery services, like DHL, UPS or local Post.

These carriers are also commonly referred to as _"last mile carriers"_.

Apart from that, if the shops delivers itself, it can also be listed as a carrier.
Carriers have an `is_active` flag which is true by default.

```php
use Vanilo\Shipment\Models\Carrier;

$dhlNl = Carrier::create(['name' => 'DHL Netherlands'])->fresh();
$dhlNl->is_active;
// true
```

Carrier objects have a configuration array which might be necessary for storing
credentials, service level, or any other carrier related configuration.

```php
$postNord = Carrier::create(['name' => 'Post Nord']);

$postNord->configuration = ['apikey' => '91f146137449a4068b3523a80xxx4f30'];
$postNord->save();
```

Carriers however, **are not equal to shipping methods**, which are the combinations of
a carrier, pricing and other configurations.

## Shipments

Shipments in the system are separate entities and are stored in the `shipments` table.
Each record/entity represents a single parcel. This can be a single item or a set of
items that is being delivered at once or handled as a single unit
(eg. identified by a tracking number) by the shipping carrier.

If the delivery of an order is split into multiple shipments, then each
shipment will have a new entry in the database.

> TO BE FINALIZED BY 9th March, 2022
