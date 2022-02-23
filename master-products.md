# Master Products

> The master products feature is scheduled to be released with [Vanilo 3.1](roadmap.md)

Master products is a module to constitute [Product Variants](product-variants.md) using
the [model based, hierachical approach](product-variants.md#the_two_approaches).

This module is optional, and if you don't need its features, then stick with
the simple [Product models](products.md).

## Overview

The master products module ships with two additional product model,
besides the regular [Product](products.md) model:

- `MasterProduct`, and
- `ProductVariant`.

Unlike with the [Links Module](links.md), the Master Product module expresses
product variant relationships in the model layer, in a very explicit way.

Common attributes (name, description, pictures, etc) are defined in the
`MasterProduct` model, but the variant specifics (size, price, SKU, etc)
are stored with the `ProductVariant`.

The master product is not Buyable, thus it can't be purchased, thrown into the
cart, added to an order, paid, etc. The variant is the `Buyable`, and it always
has a master product linked with it.

As a consequence, a product is stored in two separate tables.
