# Links Module

> The Links Module has been first released with [Vanilo v3.0](releases.md).

## Introduction

"Links" is a generic feature to handle various use cases where it is required
to be aware of a collection of other models (eg. products, categories, etc) that are
somehow associated with a given model (most typically products and taxon items).

One of the possible - but not the only - use case of links is to
constitute [Product Variants](product-variants.md) with it.

**Example use cases**:

- **"Colorways"** - the color variants of a certain product;
- **"Matches"** - Products that have a similar, matching style to a given product;
- **"Similar categories"** - When a customer is viewing a category, other, recommended categories can be linked to the category
- **"Customers Also Viewed"** - Products that were also viewed by other shoppers before/after viewing this product;
- **"Upsell"** - A similar but more expensive product - typically offered in cart or at checkout;
- **"Cros sell"** - Accessories or other, typically cheaper products that can be sold as companion products.

### Peer Relationship

Models can be added as each-other's links. The linked model entries are peers,
they're in an equal, and **omni-directional** relationship.

Omni-directional means that if **'Laptop Blue'** is a`color` variant
of **'Laptop Green'**, then if **'Laptop Red'** gets added as a `color` variant to 'Laptop Green',
then it automatically becomes the color variant of 'Laptop Blue' as well.

> In future releases, there might be **unidirectional links** between models,
> eg. "White T-shirt" is linked as "Recommended" to "Blue Jeans", but
> not reverse, ie. the blue jeans isn't recommended at the T-shirt

## Link Types

There can be multiple types of links eg "Upsell" or "Similar products", etc
and these are represented by the `LinkType` model.

Link types are user editable data and each link type has:
 
- `name`: unique eg. "Similar products"
- `slug`: unique, auto-generated eg. "similar-products" 
- `is_active`: true by default, can be used for de-activating link types (eg. seasonal offerings)

**Creating a Link Type**:

```php
$similar = LinkType::create(['name' => 'Similar products']);

echo $similar->name;
// "Similar products"
echo $similar->slug;
// "similar-products"
```

To get the list of available link types use the static `choices()` method.

It returns key value pairs, where the key is the numeric id and the value is the name of the link type:

```php
LinkType::choices();
// [
//    1 => 'Similar products'
//    2 => 'Upsell'
//    3 => 'Variants'
// ]
```

The `choices()` method only returns the active items by default. To retrieve
all the inactive items as well, pass `true` as the first parameter:

```php
LinkType::choices(true);
// [
//    1 => 'Upsell'
//    2 => 'Black Friday 2017'
//    3 => 'Variants'
// ]
```

If you only want to include specific inactive items in the list, pass an array
as the first parameter:

```php
// If an array of **integers** is passed,
// the inactive items will be looked
// up by their id:
LinkType::choices([3]);
// [
//    1 => 'Upsell'
//    3 => 'Easter 2018 Recommendations'
//    5 => 'Accessories'
// ]

// If an array of **strings** is passed,
// the inactive items will be looked
// up by their slug:
LinkType::choices(['x-mas-2019', 'new-year-2020']);
// [
//    1 => 'Upsell'
//    2 => 'X-sell'
//    7 => 'X-mas 2019'
//    8 => 'New Year 2020'
// ]
```

If you pass a second argument to the `choices()` method it returns slugs as keys
instead of the database identifiers.

```php
LinkType::choices(false, true);
// [
//    'upsell' => 'Upsell'
//    'new-year-2023 => 'New Year 2023'
//    'similar-style' => 'Similar style'
// ]
```

## Model Links

Links can be established between any two eloquent models. The linked models
can be mixed types, e.g. you can link a `Taxon` to a `Product`.

Because of this high flexibility, the underlying data structure is somewhat
complicated.

To save you some hassle, there's a simplified set of query builders that can be
used for manipulating links between models.

**Examples**:

```php
// Creating a link between two products:
Establish::an('upsell')->link()->between($product1)->and($product2);

// To retrieve the linked product(s)
Get::the('upsell')->links()->of($product1);
// Collection
// items: [ $product1 ]

// To remove the link between the products:
Eliminate::the('upsell')->link()->between($product1)->and($product2);
```

The code might seem a bit uncommon, but it wraps a lot of logic, and simplifies
the usage very significantly.

### Creating Links

DRAFT

- CHECK: When attempting to add a product to a link_type that the product is already the part of, an error is thrown (due to the unique constraint)
- CHECK: A product cannot be added to the same group twice (an error must be thrown)
- Show it in a code example:
  - Create a link between product "A" and "B".
  - Add the same type of link between "B" and "C".
  - Check if "A" and "C" are now also linked

```php
Establish::an('upsell')->link()->between($product1)->and($product2);

Establish::a('similar')->link()->between($product1)->and($product2);

Establish::a('variant')->link()->basedOn('shoe-size')->between($product1)->and($product2);
```

### Retrieving Links

DRAFT

```php
Get::the('series')->links()->of($this->galaxyS22);

Get::the('variant')->links()->basedOn(22|slug)->of($this->galaxyS22);

Get::variant()->links()->of($this->galaxyS22);
```

```php
Get::usePropertiesModel(Property::class);
```

```php
link_groups('series')->of($this->galaxyS22);
links('variant', 'screen')->of($this->galaxyS22);
```

### Deleting Links

DRAFT

```php
Eliminate::the('upsell')->link()->between($product1)->and($product2);

Eliminate::the('upsell')->group()->constitutedBy($product1)->and($product2);

Eliminate::model($product1)->fromThe('upsell')->group()->of($product2);
```

### Property-based Links

TBW

## Link Groups

DRAFT:
Model links can be created given a product id and a link group id
- CHECK: The product link's `group_id` automatically gets generated if unspecified
