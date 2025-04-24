# Products

## Creating Products

One of the abilities of the admin panel is to create products, but if you need to create products from code, here are some examples:

### Minimal Product

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

### All Product Fields

```php
Product::create([
    'name'             => 'Maxi Baxi 2000',
    'sku'              => 'MXB-2000',
    'stock'            => 123.4567,
    'price'            => 1999.95,
    'original_price'   => 2499.95,
    'weight'           => 1.3,
    'width'            => 27,
    'height'           => 21,
    'length'           => 60,
    'slug'             => 'maxi-baxi-2000',
    'excerpt'          => 'Maxi Baxi 2000 is the THING you always have dreamt of',
    'description'      => 'Maxi Baxi 2000 makes your dreams come true. See: https://youtu.be/5RKM_VLEbOc',
    'state'            => 'active',
    'meta_keywords'    => 'maxi, baxi, dreams',
    'meta_description' => 'The THING you always have dreamt of'
]);
```

## Searching Products

The `ProductSearch` class provides a way to retrieve products and master products together, based on various criteria like
name, slug, taxons, channels, price ranges, and other attributes.

### Finding Products by Name

#### Partial Match

This will return products and master products that contain "Shiny Glue" in their name.

```php
$finder = new ProductSearch();
$result = $finder->nameContains('Shiny Glue')->getResults();
```

#### Starts With

Finds products and master products whose names start with "Mature", such as "Matured Cheese" or "Mature People".

```php
$finder = new ProductSearch();
$result = $finder->nameStartsWith('Mature')->getResults();
```

#### Ends With

Finds products and master products where the name ends with "Transformator", such as "Bobinated Transformator".

```php
$finder = new ProductSearch();
$result = $finder->nameEndsWith('Transformator')->getResults();
```

### Finding Products by Slug

Returns a single product/master product with the exact matching slug, or null if not found:

```php
$product = ProductSearch::findBySlug('nintendo-todd-20cm-plush');
```

The find by slug method has a variant that throws a `ModelNotFoundException` if the product is not found.

```php
use Illuminate\Database\Eloquent\ModelNotFoundException;

try {
    $product = ProductSearch::findBySlugOrFail('non-existent-slug');
} catch (ModelNotFoundException $e) {
    // Handle case where the product is not found
}
```

### Filtering Products

#### By Taxon

```php
use Vanilo\Category\Models\Taxon;

$taxon = Taxon::find(1);
$products = (new ProductSearch())->withinTaxon($taxon)->getResults();
```

#### By Channel

```php
use Vanilo\Channel\Models\Channel;

$channel = Channel::find(1);
$products = (new ProductSearch())->withinChannel($channel)->getResults();
```

#### By Price Range

```php
$products = (new ProductSearch())->priceBetween(100, 500)->getResults();
```

### Filtering by Property Values

#### By Single Property Value

Filters products that have a specific property value.

```php
use App\Models\PropertyValue;

$propertyValue = PropertyValue::find(1);
$products = (new ProductSearch())->havingPropertyValue($propertyValue)->getResults();
```

#### By Multiple Property Values

Filters products that match multiple property values.

```php
$propertyValues = PropertyValue::whereIn('id', [1, 2, 3])->get();
$products = (new ProductSearch())->havingPropertyValues($propertyValues->toArray())->getResults();
```

#### By Property Name and Values

Filters products by a property name and its corresponding values.

```php
$products = (new ProductSearch())->havingPropertyValuesByName('color', ['red', 'blue'])->getResults();
```

#### Using OR Conditions for Property Values

Applies an OR condition when filtering by multiple property values.

```php
$red = PropertyValue::findByPropertyAndValue('color', 'red');
$pink = PropertyValue::findByPropertyAndValue('covercolor', 'ping');

$searcher = new ProductSearch();
$redOrPinkProducts = $searcher
                        ->havingPropertyValues($propertyValues->toArray())
                        ->orHavingPropertyValues($propertyValues->toArray())
                        ->getResults();
```

## Fields

| Name                              | Type          | Notes                                                                                                                                 |
|-----------------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------|
| id                                | autoinc       |                                                                                                                                       |
| name                              | string        | Required                                                                                                                              |
| sku                               | string        | Required                                                                                                                              |
| slug                              | string        | Nullable, default value is auto generated by [Eloquent-Sluggable](https://github.com/cviebrock/eloquent-sluggable) using product name |
| stock                             | decimal(15,4) | Default value is 0                                                                                                                    |
| price                             | decimal(15,4) | Nullable                                                                                                                              |
| original_price                    | decimal(15,4) | Nullable                                                                                                                              |
| weight                            | decimal(15,4) | Nullable                                                                                                                              |
| width                             | decimal(15,4) | Nullable                                                                                                                              |
| height                            | decimal(15,4) | Nullable                                                                                                                              |
| length                            | decimal(15,4) | Nullable                                                                                                                              |
| excerpt (aka "short description") | string        | Nullable                                                                                                                              |
| description                       | string        | Nullable                                                                                                                              |
| state                             | ProductState  | See [product state section](#product-state) for detailed info                                                                         |
| ext_title                         | string(511)   | Nullable, see [product title section](#product-title) for detailed info                                                               |
| meta_keywords                     | text          | Nullable                                                                                                                              |
| meta_description                  | text          | Nullable                                                                                                                              |

### Product Title

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

### Product Stock

```php
$product = Product::create([
    'name' => 'Yet Another Product',
    'sku'  => 'YAP',
]);

echo $product->stock;
// 0

echo ($product->isOnStock() ? 'On Stock' : 'Not on Stock');
// "Not on Stock"

$product->stock = 34;

echo ($product->isOnStock() ? 'On Stock' : 'Not on Stock');
// "On Stock"

```

### Product State

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

#### Meanings of Product States

You may wonder, what do these product states represent, why not just go with an `is_active` flag?

The answer is that in many ecommerce businesses products may go through different lifecycle stages,
which need slightly different handling. Vanilo doesn't enforce any workflow for this, but here's
the interpretation of the built-in states, and how they can possibly be handled:

| State         | Meaning                                                                                                                                                                                                | Common way of handling the state                                                                                      |
|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| `draft`       | The product data is incomplete                                                                                                                                                                         | Never display it on the StoreFront, do not allow it in any transaction, but allow editing in Admin                    |
| `inactive`    | The product data is complete but has not been published yet                                                                                                                                            | Hide from the StoreFront, eventually allow in transactions; eg. backorders, early closed-group sales, etc             |
| `active`      | The product is active                                                                                                                                                                                  | Make it available everywhere                                                                                          |
| `unavailable` | The product is **temporarily** unavailable; due to supplier issues, legal reasons, quality problems, production blockages or any other reason that prevents the product from being ordered and shipped | Display it on the StoreFront, but disallow new orders                                                                 |
| `retired`     | The product is **permanently** unavailable and will never come back to the product range                                                                                                               | Do not list it on the StoreFront, but allow opening it when directly accessing its product link. Disallow new orders. |

## Searching Products

The `ProductSearch` class provides a way to retrieve products and master products together, based on various criteria like
name, slug, taxons, channels, price ranges, and other attributes.

### Finding Products by Name

#### Partial Match

This will return products and master products that contain "Shiny Glue" in their name.

```php
$finder = new ProductSearch();
$result = $finder->nameContains('Shiny Glue')->getResults();
```

#### Starts With

Finds products and master products whose names start with "Mature", such as "Matured Cheese" or "Mature People".

```php
$finder = new ProductSearch();
$result = $finder->nameStartsWith('Mature')->getResults();
```

#### Ends With

Finds products and master products where the name ends with "Transformator", such as "Bobinated Transformator".

```php
$finder = new ProductSearch();
$result = $finder->nameEndsWith('Transformator')->getResults();
```

### Finding Products by Slug

Returns a single product/master product with the exact matching slug, or null if not found:

```php
$product = ProductSearch::findBySlug('nintendo-todd-20cm-plush');
```

The find by slug method has a variant that throws a `ModelNotFoundException` if the product is not found.

```php
use Illuminate\Database\Eloquent\ModelNotFoundException;

try {
    $product = ProductSearch::findBySlugOrFail('non-existent-slug');
} catch (ModelNotFoundException $e) {
    // Handle case where the product is not found
}
```

### Filtering Products

#### By Taxon

```php
use Vanilo\Category\Models\Taxon;

$taxon = Taxon::find(1);
$products = (new ProductSearch())->withinTaxon($taxon)->getResults();
```

#### By Channel

```php
use Vanilo\Channel\Models\Channel;

$channel = Channel::find(1);
$products = (new ProductSearch())->withinChannel($channel)->getResults();
```

#### By Price Range

```php
$products = (new ProductSearch())->priceBetween(100, 500)->getResults();
```

### Filtering by Property Values

#### By Single Property Value

Filters products that have a specific property value.

```php
use App\Models\PropertyValue;

$propertyValue = PropertyValue::find(1);
$products = (new ProductSearch())->havingPropertyValue($propertyValue)->getResults();
```

#### By Multiple Property Values

Filters products that match multiple property values.

```php
$propertyValues = PropertyValue::whereIn('id', [1, 2, 3])->get();
$products = (new ProductSearch())->havingPropertyValues($propertyValues->toArray())->getResults();
```

#### By Property Name and Values

Filters products by a property name and its corresponding values.

```php
$products = (new ProductSearch())->havingPropertyValuesByName('color', ['red', 'blue'])->getResults();
```

#### Using OR Conditions for Property Values

Applies an OR condition when filtering by multiple property values.

```php
$red = PropertyValue::findByPropertyAndValue('color', 'red');
$pink = PropertyValue::findByPropertyAndValue('covercolor', 'pink');

$searcher = new ProductSearch();
$redOrPinkProducts = $searcher
    ->havingPropertyValues($propertyValues->toArray())
    ->orHavingPropertyValues($propertyValues->toArray())
    ->getResults();
```

## Product Images

> Refer to the [Modules vs. Framework](modules-vs-framework.md) page for understanding the
> difference between the two approaches.

### Product Module Only - Without Framework

In case you're using the standalone product module only, then there's no product image support by
default.

The [contracts](https://github.com/vanilophp/contracts) package defines the `Buyable` interface
which extends the `HasImages` interface that has 6 image related methods:

1. `hasImage(): bool`
2. `imageCount(): int` (since v2.1)
3. `getThumbnailUrl(): ?string`
4. `getThumbnailUrls(): Collection` (since v2.1)
5. `getImageUrl(string $variant = ''): ?string`
6. `getImageUrls(string $variant = ''): Collection` (since v2.1)

The [support](https://github.com/vanilophp/support) package offers 2 traits for implementing the
image-related functionality of the `Buyable` interface:

- `BuyableNoImage`: "Null image" trait for products that don't actually have images
- `HasImagesFromMediaLibrary`: adapter for using images with [Spatie's Laravel Media Library](https://github.com/spatie/laravel-medialibrary)

> `BuyableImageSpatieV7` and `BuyableImageSpatieV8` traits are deprecated as of Vanilo v2.1, please
> use the `HasImagesFromMediaLibrary` trait instead

In case you want to compose your own product model with Spatie image support:

```php
namespace App;

use Spatie\MediaLibrary\HasMedia;
use Spatie\MediaLibrary\InteractsWithMedia;
use Vanilo\Contracts\Buyable;
use Vanilo\Product\Models\Product as BaseProduct;
use Vanilo\Support\Traits\BuyableModel;
use Vanilo\Support\Traits\HasImagesFromMediaLibrary;

class Product extends BaseProduct implements Buyable, HasMedia
{
    use BuyableModel; // Implements Buyable methods for common Eloquent models
    use HasImagesFromMediaLibrary; // Implements Buyable's image methods using Spatie Media Library
    use InteractsWithMedia; // Spatie package's default trait
}
```

It is also possible to add your very own implementation of these methods.
The traits from the support package provide a convenient and common default implementation.

### With The Framework

If you're using the [Vanilo Framework](modules-vs-framework.md) (and not just the product module),
it already ships with an extended product model (`Vanilo\Framework\Models\Product`) with image
support via Spatie Media Library.

> Refer to the [Spatie Media Library Documentation](https://docs.spatie.be/laravel-medialibrary/v11/introduction),
> to discover all the possibilities.

### Add Multiple Product Images

```php
// Example: in a controller action
public function store(Request $request)
{
    $product = Product::create();
    
    if (!empty($request->files->filter('images'))) {
        $product->addMultipleMediaFromRequest(['images'])->each(function ($fileAdder) {
            $fileAdder->toMediaCollection();
        });
    }    
}
```

### Define Image Variants

For more details about Vanilo Image handling, refer to the [Handling Images](images.md) section of
this Documentation.
