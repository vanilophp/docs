# Searching and Querying Products

Retrieving a list of products can be as simple as querying the `Product` model.
But real E-commerce systems attribute lots of features to products that involve other models and DB tables like:

- Return [simple products](products.md) and [master products](master-products.md) with all their variants;
- Retrieve products within one or more [categories](categorization.md);
- Query products having `red` and `blue` color [properties](properties.md);
- Get products that are assigned to a certain [channel](channels.md);
- Find products with their name containing a search term.

Imagine combining these like: _"Get products from category "Men's shoes" that have color 'red' and 'yellow' and
style 'casual' properties, containing 'Retro' in their names but only those within the 'retail' channel.
Get both plain and master products and paginate them in chunks of 50 item"_.

This is a pretty common scenario in E-commerce, but can be a hell of a complexity when working with data structures.
Vanilo has the `ProductSearch` class to simplify your life with complex, multi-model queries.

```php
use Vanilo\Foundation\Search\ProductSearch;

$products = (new ProductSearch())
    ->withinTaxon(Taxon::findBySlug('mens-shoes'))
    ->withinChannel(Channel::findBySlug('retail'))
    ->havingPropertyValuesByName('color', ['red', 'yellow'])
    ->havingPropertyValuesByName('style', ['casual'])
    ->nameContains('Retro')
    ->paginate(50);
```

