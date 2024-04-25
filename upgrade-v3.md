# Upgrade Between Versions

> You are browsing an older version of the Upgrade Guide. For the latest version see: [Upgrade Guide](upgrade.md)

> For older upgrade guides see:
> - [Upgrade Guide v1.x -> v2.x](upgrade-v2.md)
> - [Upgrade Guide v0.2 -> v1.2](upgrade-v1.md)

## 2.2 -> 3.0

### Checklist

- [ ] Update PHP & Laravel
- [ ] Update Interfaces
- [ ] Namespace changes
- [ ] Configuration changes
- [ ] Add Admin Panel if still needed

###  PHP & Laravel Requirement Changes

| Vanilo Version | Components              | Framework               |
|:---------------|:------------------------|:------------------------|
| 3.0            | PHP 8.0+ & Laravel 9.0+ | PHP 8.0+ & Laravel 9.0+ |

**Your Action Item**: Upgrade Laravel & PHP if necessary, bump minimum requirements in your
project's `composer.json` file.

### PHP Interface Changes

Several interfaces have been updated to define return types on the language level.

**Affected Classes**:

- `Vanilo\Contracts\Buyable`: [difference](https://github.com/vanilophp/contracts/compare/2.2.0..3.0.0#diff-15363a836d8e421476c2b96ea27ab54febb6514ea4bb55a30ae6d9b1bf67dc1b)
- `Vanilo\Contracts\Shippable`: [difference](https://github.com/vanilophp/contracts/compare/2.2.0..3.0.0#diff-d8c30c79796da48bd30347ee36801809b50640bc4c0f2cf3574bdc016ae2da1c)
- `Vanilo\Properties\Contracts\Property`: [difference](https://github.com/vanilophp/properties/compare/2.2.0..3.0.0#diff-b98cc22a0a24216c1a1e9310ea3714cdaf9f1a0a163f5cc732fa52e28df5aa7a)

**Your Action Item**: Search your application for classes that directly implement these interfaces, and
updated them to match the new signatures.

### Namespace Changes

The `Vanilo\Framework` namespace has been renamed to `Vanilo\Foundation`.
A namespace alias has been created for compatibility, but this will be dropped in the future.

**Your Action Item**: Search your application for classes that use classes from the `Vanilo\Framework`
namespace and replace `Framework` with `Foundation` in the use clause or in the class name.

Mind renaming the namespace in `config/concord.php`.

The `Vanilo\Foundation\Models\PaymentMethod` class has been removed. Use `Vanilo\Payment\Models\PaymentMethod` instead.

### Configuration Path Changes

The `vanilo.framework.*` config values have been renamed to `vanilo.foundation.*`.

**Your Action Item**: Search your application for occurrences where it refers to
`vanilo.framework.*` keys and replace them with `vanilo.foundation.*`

### Add Admin Panel If Needed

The admin panel has been removed from the Vanilo core framework. If you still need it,
add it back to your application's dependencies using: `composer require vanilo/admin`
