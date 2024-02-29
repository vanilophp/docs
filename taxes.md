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

A Tax Category consists of an id, a name.

## Tax Rates

Tax rates contain the final rules for tax calculation, including the rate that needs to be applied to
the product/service.

Tax rates can be looked up based on a given billing (or shipping) address that is in a specific [tax zone](zones.md),
and the tax category.

The tax rate can then be passed to the selected tax calculator which will return the tax as
[adjustment](adjustments.md) to the cart/order and/or its items.

A tax rate consists of a:

- Tax Category;
- Taxation [Zone](zones.md);
- Validity date range (optional);
- Rate (%);
- Calculator, and
- Configuration (array stored as json).

The configuration is required for the calculator, and is specific to the selected tax calculator.
The configuration will be passed over to the calculator when calculating taxes.

## Calculators

The actual calculation is being done by the specific tax calculators that serve as bridges
between the Tax and the [Adjustment](adjustments.md) system.

Calculators receive the tax subject and the configuration from the tax rate and calculate the tax amount(s).

Their configuration is crucial, and can vary from one tax system to the other.
As an example, the Canadian Sales Tax Calculator needs to receive 3 values, the HST, GST and PST rates to
be able to calculate. The simple calculator requires a rate.

Therefore, each calculator has to define its own configuration schema.
