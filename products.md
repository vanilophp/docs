# Products

Vanilo's product module is really minimalistic at this point of time.

> See the [roadmap](roadmap.md) to see what and when will be implemented
> in this module in the future (refer to versions `0.4` and `0.6`
> particularly)

## Creating Products

One of the very few abilities of the admin panel is to create products.
It should be straightforward enough, so not detailing that part here.

However if you need to create products from code, here are some examples:

**Minimal Product:**

```php
use Vanilo\Product\Models\Product;

// A product must have at least a name and an SKU:
$product = Product::create([
    'name' => 'Dell Latitude E7240 Laptop',
    'sku'  => 'DLL-74237'
]);

echo $product->name;
// "Dell Latitude E7240 Laptop"

echo $product->slug;
// "dell-latitude-e7240-laptop"
```

> The slugs are generated using the
> [Eloquent-Sluggable](https://github.com/cviebrock/eloquent-sluggable)
> library.

**All Product Fields:**

```php
Product::create([
    'name'             => 'Maxi Baxi 2000',
    'sku'              => 'MXB-2000',
    'slug'             => 'maxi-baxi-2000',
    'excerpt'          => 'Maxi Baxi 2000 is the THING you always have dreamt of',
    'description'      => 'Maxi Baxi 2000 makes your dreams come true. See: https://youtu.be/5RKM_VLEbOc',
    'state'            => 'active',
    'meta_keywords'    => 'maxi, baxi, dreams',
    'meta_description' => 'The THING you always have dreamt of'
]);
```

**Product Title:**

Product title is either the `ext_title` field's value or the `name` if
ext_title is empty.

```php
$product = Product::create([
    'name' => 'I am a product',
    'sku'  => 'N1'
]);
echo $product->title();
// "I am a product"

$product->ext_title = 'I am a product with attitudes';

echo $product->title();
// "I am a product with attitudes"

// It can be accessed as property as well:
echo $product->title;
// "I am a product with attitudes"
```

**Product State:**

Product state is an [enum](enums.md).

```php
use Vanilo\Product\Models\Product;
use Vanilo\Product\Models\ProductState;

// Supported product states out of the box:
print_r(ProductState::values());
//[
//    "draft",
//    "inactive",
//    "active",
//    "unavailable",
//    "retired",
//]

$product = new Product();

// Product's state field is an enum:
echo get_class($product->state);
// "Vanilo\Product\Models\ProductState"

// Default value is draft:
echo $product->state->value();
// "draft"

// State can be set via scalar:
$product->state = 'inactive';

// State can be set via enum object:
$product->state = ProductState::INACTIVE();

// Products also have an 'isActive()' method:
echo $product->isActive();
// false

// Alternatively, this can be accessed as property too:
echo $product->is_active;
// false

// The isActive()/is_active flag is actually obtained from the state:
$product->state = ProductState::ACTIVE;
echo $product->isActive();
// true
```

For extending the product model and the product state enum, refer to the
[models](models.md) and [enums](enums.md) sections, respectively.
