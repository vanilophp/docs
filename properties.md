# Product Properties

> The Properties Module is WIP at the moment and will be released in [Vanilo v0.5](roadmap.md).

> Most E-commerce systems name this technique as "product attributes".
> Laravel's Eloquent also names fields of models (entities) as "attributes".
> In order to prevent confusion between the two, Vanilo calls these "EAV" style attributes as "properties".
> Spree Commerce for example also has this naming: [Product Properties](https://guides.spreecommerce.org/developer/products.html#product-properties)

## Overview

Product properties track individual attributes of a product which don’t apply to all products.

Examples of product properties are:
 - **material** of a _T-Shirt_,
 - **color** of a _Smartphone_,
 - **RAM size** of a _Laptop_,
 - **alcohol %** of a _Spirit drink_,
 - whether a _Fridge_ **has display**.
