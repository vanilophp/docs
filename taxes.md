# Taxes

The taxes module makes use of the [adjustments](adjustments.md) module,
and can be used to for various tax calculations, like the Sales Tax,
GST and VAT in most countries. 

Most countries are using various rates for different types of articles/services.

As an example, in Austria, there are 3 separate VAT rates (as of 2023):

- 20% the standard rate,
- 13% for plants, live animals and animal food, art, wine
- 10% for rental for the purpose of habitation, food, garbage collection, most transportation

## Tax Categories

Tax Categories can be freely defined and assigned to products (buyables) via the `tax_category_id` field.

As an example, food in most countries falls in a different, reduced tax category.
Tax categories don't have rates, since the tax rate of food varies from country to country.

A Tax Category consists of an id, a name and a type.

### Tax Category Type

Each tax category can be assigned, to a broader, generic type, which are:

- Physical Goods
- Digital Goods/Services (E-Services)
- Transport Services
- Telecommunications Services
- Broadcasting Services
- Standard Services
- Real Estate Services
- Event-Related Services

The type field is optional, but certain Tax engines* might need it.

> *: The Cross-border EU VAT engine uses the type to distinguish whether the shipping or
> the billing address country needs to be taken into account when looking up the tax rates

## Tax Engines

Tax Engines are the basic entry points of tax calculations.
Their primary scope is to return the tax rate based on a taxable, a shipping and/or billing address.
This simple operation may involve complex logic like validating tax identifiers based on the configuration,
merchant attributes and other specific rules.

Vanilo ships with two engines built-in: the "null" and the "simple" driver.

The `vanilo.taxes.engine.driver` configuration tells Vanilo which engine to use.

### The No-Tax Scenario

To **default** driver is `none` that implies the NullDriver, which doesn't do any tax calculation.

This is the _most common scenario_, meaning the product prices are the final, tax-inclusive retail prices,
there are neither cross-border nor net-based pricing implications in the system.

### The Simple Tax Driver

The built-in simple tax driver can be activated by setting the configuration:

```php
// config/vanilo.php
return [
    'taxes' => [
        'engine' => [
            'driver' => 'simple',
        ],
    ],
];
```

This engine will use the rules based on tax categories, tax zones, buyables and tax rates to calculate taxes.

By default, the simple driver uses the billing address for looking up the tax zone. To use the shipping address instead,
set the `vanilo.taxes.engine.simple.use_shipping_address` config value to **true**.

### Custom Tax Engine

To use your custom tax engine, create a class, that implements the `TaxRateResolver` interface, and register the engine:

```php
//AppServiceProvider.php
public function boot()
{
    TaxCalculators::register('canada', CanadianSalesTaxCalculator::class);
}
```

To activate the driver, set the driver in the configuration:

```php
// config/vanilo.php
return [
    'taxes' => [
        'engine' => [
            'driver' => 'canada',
        ],
    ],
];
```

## Tax Rates

Tax rates contain the final rules for tax calculation, including the rate that needs to be applied to
the product/service.

The tax engine (driver) will use its own logic to look up the tax rate for a given scenario (cart, checkout, order),
based on the shipping/billing address, [tax zone](zones.md), merchant config, buyable items, etc.

The tax rate will then be passed to the selected tax calculator, and Vanilo will apply the tax as
[adjustment](adjustments.md) to the cart/order and/or its items.

A tax rate consists of a:

- Tax Category;
- Taxation [Zone](zones.md);
- Validity date range (optional);
- Rate (%);
- Calculator, and
- Configuration.

The configuration is required for the calculator, and is specific to the selected tax calculator.
The configuration will be passed over to the calculator when calculating taxes.

## Calculators

The actual calculation is being done by the specific tax calculators that serve as bridges
between the Tax and the [Adjustment](adjustments.md) system.

Calculators receive the tax subject and the configuration from the tax rate and calculate the tax amount(s).

Their configuration is crucial, and can vary from one tax system to the other.
As an example, the Canadian Sales Tax Calculator needs to receive 3 values, the HST, GST and PST rates to
be able to calculate. The simple calculator requires a rate.

Therefore each calculator has to define its own configuration schema.
