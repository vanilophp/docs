# Products

## Creating Products

One of the very few abilities of the admin panel is to create products.
It should be straightforward enough, so not detailing that part here.

However if you need to create products from code, here are some examples:

#### Minimal Product:

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

#### All Product Fields:

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

#### Product Title:

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

#### Product State:

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

## Product Images

> Refer to the [Modules vs. Framework](modules-vs-framework.md) page for understanding the
> difference between the two approaches.

### Product Module Only - Without Framework

In case you're using the standalone product module only, then there's no product image support by
default.

The [contracts](https://github.com/vanilophp/contracts) package defines the `Buyable` interface
across modules that has 3 image related methods:

1. `hasImage(): bool`
2. `getThumbnailUrl(): ?string`
3. `getImageUrl(): ?string`

The [support](https://github.com/vanilophp/support) package offers 2 traits for implementing the
image related functionality of the `Buyable` interface:

- `BuyableNoImage`: "Null image" trait for products that don't actually have images
- `BuyableImageSpatieV7`: Adapter for using images with [Spatie's Laravel Media Library V7](https://github.com/spatie/laravel-medialibrary)

In case you want to compose your own product model with Spatie image support:

```php
namespace App;

use Spatie\MediaLibrary\HasMedia\HasMedia;
use Spatie\MediaLibrary\HasMedia\HasMediaTrait;
use Vanilo\Contracts\Buyable;
use Vanilo\Product\Models\Product as BaseProduct;
use Vanilo\Support\Traits\BuyableModel;
use Vanilo\Support\Traits\BuyableImageSpatieV7;

class Product extends BaseProduct implements Buyable, HasMedia
{
    use BuyableModel; // Implements Buyable methods for common Eloquent models
    use BuyableImageSpatieV7; // Implements Buyable's image methods using Spatie Media Library
    use HasMediaTrait; // Spatie package's default trait
}
```

It is also possible to add your very own implementation of these methods.
The traits from the support package provide a convenient and common default implementation.

### With The Framework

If you're using the [Vanilo Framework](modules-vs-framework.md) (and not just the product module),
it already ships with an extended product model (`Vanilo\Framework\Models\Product`) with image
support via Spatie Media Library.

> Refer to the [Spatie Media Library Documentation](https://docs.spatie.be/laravel-medialibrary/v7/introduction),
> to discover all the possibilities.

#### Add Multiple Product Images

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

#### Define Image Variants

It is possible to define various sizes (thumbnails, etc) of the images in the config file:

```php
// config/vanilo.php
return [
    // ...
    'image'       => [
        'variants' => [
            'thumbnail' => [ // Name of the image variant
                'width'  => 250,
                'height' => 250,
                'fit'    => 'crop'
            ],
            'cart' => [ // Image variant names can be arbitrary
                'width'  => 120,
                'height' => 90,
                'fit'    => 'crop'
            ]
        ]
    ]
    //...
];
```

To retrieve image variants in client code, use:

```php
$product->getFirstMediaUrl('default', 'thumbnail'); // 250x250
$product->getFirstMediaUrl('default', 'cart'); // 120x90
```

> Refer to the [Retrieving Converted Images](https://docs.spatie.be/laravel-medialibrary/v7/converting-images/retrieving-converted-images)
> section in Media Library Documentation for more details.
