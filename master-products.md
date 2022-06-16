# Master Products

> The master products feature is scheduled to be released with [Vanilo 3.1](roadmap.md)

Master products is a module to constitute [Product Variants](product-variants.md) using
the [model based, hierarchical approach](product-variants.md#the_two_approaches).

This module is optional, and if you don't need its features, then stick with
the simple [Product models](products.md).

## Overview

The master products module ships with two additional eloquent models,
besides the simple, regular [Product](products.md) model:

- `MasterProduct`, and
- `MasterProductVariant`.

Unlike with the [Links Module](links.md), the Master Product module expresses
product variant relationships in the model layer, in a very explicit way.

Common attributes (name, description, pictures, etc) are defined in the
`MasterProduct` model, but the variant specifics (size, price, SKU, etc)
are stored with the `MasterProductVariant`.

The master product has no stock, but the individual variants have.

The _master product_ is **not** a `Buyable`, thus it can't be purchased, thrown into the
cart, added to an order, paid, etc. The _variant_ is the `Buyable`, and it always
has a master product linked with it, so these two types always co-exist.

As a consequence, a product of this kind is stored in two tables: `master_products` and `master_product_variants`.

## Usage In A Nutshell

To briefly illustrate the usage of such models, see an example of shoes offered in different sizes:

```php
$shoe = MasterProduct::create(['name' => 'Pazolini', 'price' => 79.99, 'excerpt' => 'Slip-on shoes with Sparkles']);
$shoe->assignProperties(['for' => 'women', 'brand' => 'Pazolini', 'shoe-type' => 'Loafers'])

$shoe->createVariant(['properties' => ['shoe-size' => 36], 'sku' => 'PZLBL-037', 'stock' => 0])
$shoe->createVariant(['properties' => ['shoe-size' => 37], 'sku' => 'PZLBL-037', 'stock' => 1])
$shoe->createVariant(['properties' => ['shoe-size' => 38], 'sku' => 'PZLBL-038', 'stock' => 0])
$shoe->createVariant(['properties' => ['shoe-size' => 39], 'sku' => 'PZLBL-039', 'stock' => 0])
$shoe->createVariant(['properties' => ['shoe-size' => 40], 'sku' => 'PZLBL-050', 'stock' => 2])
```

After having created this, you'll be able to display the product page of the master product,
and allow the shopper to select one of the sizes and throw one of the variants into the cart:

![Variants](img/_variant_model.png)

## Master Product vs. Its Variants

The master product and its variants are basically the division of the
[simple product model](products.md) into:

- one `MasterProduct` model having
- one or more `MasterProductVariant` models.

The `MasterProduct` model also implements the `Product` interface and
the fields are basically the same according to the table below:

| Field              | Product | Master Product | Master Product Variant |
|--------------------|:-------:|:--------------:|:----------------------:|
| `id`               |    ✔    |       ✔        |           ✔            |
| `name`             |    ✔    |       ✔        |           ✔            |
| `sku`              |    ✔    |       ❌        |           ✔            |
| `slug`             |    ✔    |       ✔        |           ❌            |
| `price`            |    ✔    |       ✔        |           ✔            |
| `original_price`   |    ✔    |       ✔        |           ✔            |
| `excerpt`          |    ✔    |       ✔        |           ✔            |
| `description`      |    ✔    |       ✔        |           ❌            |
| `state`            |    ✔    |       ✔        |           ❌            |
| `weight`           |    ✔    |       ✔        |           ✔            |
| `height`           |    ✔    |       ✔        |           ✔            |
| `width`            |    ✔    |       ✔        |           ✔            |
| `length`           |    ✔    |       ✔        |           ✔            |
| `ext_title`        |    ✔    |       ✔        |           ❌            |
| `meta_keywords`    |    ✔    |       ✔        |           ❌            |
| `meta_description` |    ✔    |       ✔        |           ❌            |
| `units_sold`       |    ✔    |       ❌        |           ✔            |
| `last_sale_at`     |    ✔    |       ❌        |           ✔            |

### Reason Behind Dual-present Fields

There are quite several fields that are duplicated across the master product
and the variant models.

The only required fields of the variant are `sku` and `stock`.
It means that if any other fields of a variant are undefined then they are to
be taken from the master product.

Since the `MasterProduct` is not buyable, it doesn't have an `sku`,
`units_sold` and `last_sale_at` fields.

Variants do not have a `slug`, `description`, `state`, `ext_title`,
`meta_keywords` and `meta_description` fields, because variants aren't
intended to be displayed on separate pages
(see [Product Variants - The Two Approaches](product-variants#the-two-approaches)).

The rest of the fields can be specified either in the master product or at
the variants.

#### Wait, What?

The rules of dual-present fields are simple:

1. The variant fields' values always override the master product's fields.
2. If the variant has a field undefined (null in the database) then it's value falls back to the master product.

#### Price & Original Price

Quite frequently, **all the variants have the same price**, eg. in the case of
shoes of sizes 39-43. In such cases it's the easiest to set the price of the
master product and all variants will have that price.

There are cases when **all variants have a different price** eg. a Whiskey in 0.2L,
0.5L and 0.75L bottles. In such cases, you can leave the master price empty and
define the prices of each variant individually.

Another scenario is when **some variants have a different price**.
A typical example is when some piece of clothes with some extreme color
or size are on sale, or some of the most popular ones are more expensive
than the others. In such cases, it is possible to set a default price for
the master product and override specific prices on the variant level.

#### Name

This works the same way as any other dual-present field, i.e. the variant's value
always takes precedence over the master product's value.

**Example 1**:

|           | Master `name` (in DB) | Variant `name` (in DB)  | Resulting `name`       |
|-----------|-----------------------|-------------------------|------------------------|
| Variant 1 | Makita Drill DDF485   | `null`                  | Makita Drill DDF485    |
| Variant 2 | Makita Drill DDF485   | Makita Drill DDF485Z    | Makita Drill DDF485Z   |
| Variant 3 | Makita Drill DDF485   | Makita Drill DDF485RTJ  | Makita Drill DDF485RTJ |


**Example 2**:

|           | Master `name` (in DB)   | Variant `name` (in DB)       | Resulting `name`             |
|-----------|-------------------------|------------------------------|------------------------------|
| Variant 1 | Edinburgh Raspberry Gin | Edinburgh Raspberry Gin 0.2L | Edinburgh Raspberry Gin 0.2L |
| Variant 2 | Edinburgh Raspberry Gin | Edinburgh Raspberry Gin 0.5L | Edinburgh Raspberry Gin 0.5L |
| Variant 3 | Edinburgh Raspberry Gin | Edinburgh Raspberry Gin 0.7L | Edinburgh Raspberry Gin 0.7L |

