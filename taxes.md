# Taxes

The taxes module makes use of the [adjustments](adjustments.md) module,
and can be used to for various tax calculations, like the Sales Tax,
GST and VAT in most countries. 

## Tax Categories

Most countries are using various rates for different types of articles/services.

As an example, in Austria, there are 3 separate VAT rates (as of 2023):

- 20% the standard rate,
- 13% for plants, live animals and animal food, art, wine
- 10% for rental for the purpose of habitation, food, garbage collection, most transportation

To support this feature, products and services need to be assigned with tax categories in order
to be able to distinguish between a headphone (20%) a rooster (13%) and transportation fees (10%).

Tax category entries are very simple records having an id and a name.

A `tax_category_id` field can be assigned to buyables (products) that can have a value from the tax category list.

## Tax Rates

## Calculators
