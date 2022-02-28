# Upgrade Between Versions

> For older upgrade guides see: [Upgrade Guide v0.2 - v1.2](upgrade-v1.md)

## 2.0 -> 2.1

1. In the unlikely case if you were implementing the `Payable` interface from v2.0 in your application, update the signature according to the latest version
2. If you were implementing a custom Buyable class, make sure to:
   - Add `string $variant = ''` as a parameter to the `getImageUrl` method
   - Implement the new methods from the `HasImages` interface

## 1.x -> 2.x

### Checklist

- [ ] Update PHP & Laravel
- [ ] Upgrade to Enum V3
- [ ] Update Interfaces
- [ ] Check PropertyValue changes
- [ ] Admin Panel Changes

###  PHP & Laravel Requirement Changes

| Vanilo Version | Components            | Framework*             |
|:---------------|:----------------------|:-----------------------|
| 2.0            | PHP 7.3 & Laravel 6.0 | PHP 7.4 & Laravel 6.18 |

> *: The difference between the requirements is that the Framework is using the Spatie Media Library
> that has higher minimal requirements than the Vanilo components.

**Your Action Item**: Upgrade Laravel & PHP if necessary, bump minimum requirements in your
project's `composer.json` file.

### Enum V3 Upgrade

The underlying Enum package has been upgraded from [2.3 to 3.0](https://konekt.dev/enum/3.0/upgrade).

The only change is that `__default` const is now uppercase `__DEFAULT` in order to fully comply with
the PSR-1 standard.

**Your Action Item**: Search for `__default` Enum constants in your project and rename them to
`__DEFAULT`.


### PHP Interface Changes

Most interfaces have been updated to define return types on the language level.

**Affected Classes**:

- `Vanilo\Contracts\Address`
- `Vanilo\Contracts\Billpayer`
- `Vanilo\Contracts\Buyable`
- `Vanilo\Contracts\CheckoutSubject`
- `Vanilo\Contracts\CheckoutSubjectItem`
- `Vanilo\Contracts\Contactable`
- `Vanilo\Contracts\Customer`
- `Vanilo\Contracts\Organization`
- `Vanilo\Contracts\Person`
- `Vanilo\Category\Contracts\Taxon`
- `Vanilo\Category\Contracts\Taxonomy`
- `Vanilo\Channel\Contracts\Channel`
- `Vanilo\Order\Contracts\Order`
- `Vanilo\Order\Contracts\OrderAwareEvent`
- `Vanilo\Order\Contracts\OrderFactory`
- `Vanilo\Order\Contracts\OrderNumberGenerator`
- `Vanilo\Order\Contracts\OrderStatus`
- `Vanilo\Product\Contracts\Product`
- `Vanilo\Product\Contracts\ProductState`
- `Vanilo\Properties\Contracts\PropertyValue`

**Your Action Item**: Search your application for classes that directly implement these interfaces, and
updated them t* match the new signatures.

Complete list of changes:

- [Interface Changes in Contracts v2.0](https://github.com/vanilophp/contracts/compare/1.2.0..2.0.0)
- [Interface Changes in Category v2.0](https://github.com/vanilophp/category/compare/1.2.0..2.0.0)
- [Interface Changes in Channel v2.0](https://github.com/vanilophp/channel/compare/1.2.0..2.0.0)
- [Interface Changes in Order v2.0](https://github.com/vanilophp/order/compare/1.2.0..2.1.1)
- [Interface Changes in Product v2.0](https://github.com/vanilophp/product/compare/1.2.0..2.0.0)
- [Interface Changes in Properties v2.0](https://github.com/vanilophp/properties/compare/1.2.0..2.0.0)

### Properties Module

The Properties module stores property values as strings in the db, but can casts them to their given
types (int, float, bool, etc).

Until v1.2 the casting has happend when you called `$propertyValue->getValue()`. However, if you
accessed the `$propertyValue->value` field on the same object, you just got the string as it was
stored in the DB. To resolve this confusion and make thing straightforward, the following breaking
changes have been applied:

- The `getCastedValue()` method has been added to the PropertyValue interface
- The`getValue()` method has been renamed to `getCastedValue()` in the PropertyValue model

As a consequence:
- The `getValue` method on the PropertyValue model no longer exists
- Accessing the `value` property on the model works the same way, no change there.
- If you want to get the casted value of the object, call the `$propertyValue->getCastedValue()` method.

**Your Action Item**: Search `getValue()` method calls on property value objects in your code and
depending on your use case change them either to `value` or `getCastedValue()`.

### Admin Panel Changes

The `appshell:super` artisan command has been renamed to `make:superuser`.

Admin Panel was the most deeply refactored area with the release of 2.0.
If you're not using and especially not extending the Admin Panel, then you don't have anything to do
of course.

You definitely need to check the upgrade guide if your application does any of the following items
with the Admin Panel:

- overrides views,
- adds extra assets to the layout,
- defines new permissions,
- adds CRUD entries,
- adds menu items.

**Your Action Item**: Admin Panel is built on top of AppShell, go to the
[AppShell 2.0 Upgrade Guide](https://konekt.dev/appshell/2.x/upgrade) and follow the steps.

Don't worry about the external link, AppShell was written by the same people as Vanilo ;)
