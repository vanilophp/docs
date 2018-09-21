# Upgrade Between Versions

## 0.3 -> 0.4

Nothing to consider so far.

## 0.2 -> 0.3

### Minimum Versions

If using the [Vanilo Framework](modules-vs-framework.md), then the minimum versions are:

- Laravel 5.5
- PHP 7.1.3
- GD Extension is required for product images

Minimal requirements for components (without Framework):

- Laravel 5.4
- PHP 7.0

### New Buyable Methods

The `Buyable` contract has 3 new methods, thus if any of your models implements `Buyable`, you also
have to implement them:

1. `hasImage()`
2. `getThumbnailUrl()`
3. `getImageUrl()`

The [support](https://github.com/vanilophp/support) package offers default implementations for the
new, image related methods:

- `BuyableNoImage`: "Null image" trait for products that don't actually have images.
- `BuyableImageSpatieV7`: Adapter for using images with [Spatie's Laravel Media Library V7](https://github.com/spatie/laravel-medialibrary).


### BaseRequest Moved From AppShell To Concord

If you used the `Konekt\AppShell\Contracts\Requests\BaseRequest` in your application, then you need
to use `Konekt\Concord\Contracts\BaseRequest` instead.

As of AppShell v0.9.7 the interface has been moved to Concord v1.1.

### Migrations

There are some, run them:

```bash
php artisan migrate
```

### Storage Link Required

For product image support, your application should have a
[storage link](https://laravel.com/docs/5.6/filesystem#configuration).

To create the symbolic link, you may use the storage:link Artisan command:

```bash
php artisan storage:link
```

### Rebuild The Assets When Using The Admin

If you're using Vanilo's Admin, then rebuild your assets (eg. `yarn run dev`) to catch up with the updated look&feel.

