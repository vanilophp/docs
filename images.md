# Handling Images

The Vanilo Framework supports assigning images to **products**, **taxonomies** and **taxons**.

This functionality is not present in the product and category modules, it is an additional layer
provided by the framework with the help of the
[Spatie Media Library](https://github.com/spatie/laravel-medialibrary) and a few Vanilo traits.

It means that if you're **not** using the Framework, you can either:

- use your very own implementation, or
- you can simply take the solution from the Vanilo Framework and copy it to your app.

## The HasImages Interface

The [contracts](https://github.com/vanilophp/contracts) package defines the `HasImages` interface
that has 6 image related methods:

1. `hasImage(): bool`
2. `imageCount(): int` (since v2.1)
3. `getThumbnailUrl(): ?string`
4. `getThumbnailUrls(): Collection` (since v2.1)
5. `getImageUrl(string $variant = ''): ?string`
6. `getImageUrls(string $variant = ''): Collection` (since v2.1)

The [support](https://github.com/vanilophp/support) package offers the `HasImagesFromMediaLibrary`
trait that can be added to any Eloquent Model. The trait implements the 6 methods listed above from
the `HasImages` interface. Adding the trait along with Spatie's `InteractsWithMedia` will enable the
model class to have images assigned to it.

## Define Image Variants

The Spatie Media library supports defining various image sizes, so-called
[Conversions](https://spatie.be/docs/laravel-medialibrary/v10/converting-images/defining-conversions).

The uploaded images will be then converted to the given sizes with the given parameters.

Vanilo Framework takes a step forward and allows you to define those conversions in the config file.

It's quite common that products and categories need different image sizes in the HTML layout.
Therefore, beginning with v2.1, Vanilo supports defining conversions on a per model base:

```php
// config/vanilo.php
return [
    // ...
    'foundation' => [
        'image'       => [
            'product' => [
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
            ],
            'taxon' => [
                'variants' => [
                    'thumbnail' => [
                        'width'  => 320,
                        'height' => 180,
                        'fit'    => 'crop'
                    ],
                    'banner' => [
                        'width'  => 1248,
                        'height' => 702,
                        'fit'    => 'crop'
                    ]
                ]
            ]
        ]
    ]
    //...
];
```

> Execute the `php artisan media-library:regenerate` command after each modification in the vanilo.php file

The loading of these conversions is happening with the help of the `LoadsMediaConversionsFromConfig`
trait that you can also add to your models. The "key" within the config is derived from the snake
case classname of the given model, eg.:

- Classname: `Product`, config key is `vanilo.framework.image.product.variants`,
- Classname: `Subscription` config key is `vanilo.framework.image.subscription.variants`,
- Classname: `EventRegistration` config key is `vanilo.framework.image.event_registration.variants`,

If you don't need type specific conversions, you can define "generic" conversions under the
`vanilo.framework.image.variants` key. These settings are used as a fallback if there's no type
specific definition in the configuration.

## Retrieving Images

### Thumbnails

Having thumbnails is a highly common scenario, therefore Vanilo plays smartass here, and supports
them by default. This is basically a syntactic sugar, that retrieves the image variant named
"thumbnail".

```php
// Retrieves a single URL of the primary thumbnail image:
$product->getThumbnailUrl(); // 250x250
$taxon->getThumbnailUrl(); // 320x180

// The above calls are equivalent to:
$product->getImageUrl('thumbnail');
$taxon->getImageUrl('thumbnail');
```

### Primary Images

Another common use case is that one specific image is marked as the primary image, dedicated to be
used to display on listing pages, etc.

In the built-in Vanilo Admin this can be achieved by using the "Set as Primary Image" button:

![Set As Primary Image](img/images_primary.png)

Primary image is achieved by setting the `isPrimary`
[custom property](https://spatie.be/docs/laravel-medialibrary/v10/advanced-usage/using-custom-properties)
on the given image.

To set an image as primary from the code, use the `setCustomProperty()` method:

```php
$mediaItem->setCustomProperty('isPrimary', true);
```

> Mind removing the `isPrimary` property from other images assigned to the same model if you're doing this manually

### Retrieve Image Variants

To retrieve image variants in client code:

```php
// Returns the product's primary image URL in the size defined by the `cart` conversion:
$product->getImageUrl('cart'); // 120x90
// Returns the taxon's primary image URL in the size defined by the `banner` conversion:
$taxon->getImageUrl('banner'); // 1248x702
```

### Retrieve Multiple Images

It is possible to retrieve a collection of all the images assigned to a specific model:

```php
// All the thumbnails of the product:
$product->getThumbnailUrls();

// All the banners of the taxon:
$taxon->getImageUrls('banner');
```
