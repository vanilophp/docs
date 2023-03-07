# Addresses

The Address module adds support for countries, provinces (state, county,
region, etc), cities and addresses.

The Vanilo framework uses the
[konekt/address](https://github.com/artkonekt/address) library. If using
only some components of Vanilo, it's possible to use a different
implementation, the only requirement is to implement the
`Vanilo\Contracts\Address` interface.

Please refer to the original address documentation for more details:

- Models:
    - [Countries](https://konekt.dev/address/2.x/country)
    - [Province](https://konekt.dev/address/2.x/province)
    - [Address](https://konekt.dev/address/2.x/address)
    - [Person](https://konekt.dev/address/2.x/person)
    - [Organization](https://konekt.dev/address/2.x/organization)
- Enums:
    - [Address Type](https://konekt.dev/address/2.x/address-type)
    - [Province Type](https://konekt.dev/address/2.x/province-type)
    - [Gender](https://konekt.dev/address/2.x/gender)
    - [Name Order](https://konekt.dev/address/2.x/name-order)
- [Seeders](https://konekt.dev/address/2.x/seeders)

## Comparing Addresses

It is possible to compare addresses using the `Addresses` utility class.

It can give a versatile comparison of two addresses and their differences.

```php
$address1 = Address::create(['country_id' => 'US', 'city' => 'New York', 'address' => '1234th Street 66']);
$address2 = Address::create(['country_id' => 'US', 'city' => 'New York', 'address' => '1234th Street 67']);

Addresses::are($address1, $address2)->inTheSameCountry();
// true
Addresses::are($address1, $address2)->inTheSameCity();
// true
Addresses::are($address1, $address2)->atDifferentAddresses();
// true
Addresses::are($address1, $address2)->identical();
// false
Addresses::are($address1, $address2)->different();
// true
```

The utility makes distinction between cities of the same name in different countries

```php
$berlinUS = Address::create(['country_id' => 'DE', 'city' => 'Berlin']);
$berlinDE = Address::create(['country_id' => 'US', 'city' => 'Berlin']);

Addresses::are($berlinUS, $berlinDE)->inDifferentCountries();
// true
Addresses::are($berlinUS, $berlinDE)->inDifferentCities();
// true
Addresses::are($berlinUS, $berlinDE)->atDifferentAddresses();
// true
```

It even can compare two cities of the same name in the same country, but in different provinces:

```php
$berlinGeorgia = Address::create(['country_id' => 'US', 'city' => 'Berlin', 'province_id' => 'GA']);
$berlinAlabama = Address::create(['country_id' => 'US', 'city' => 'Berlin', 'province_id' => 'AL']);

Addresses::are($berlinUS, $berlinDE)->inTheSameCountry();
// true
Addresses::are($berlinUS, $berlinDE)->inDifferentCities();
// true
Addresses::are($berlinUS, $berlinDE)->inDifferentProvinces();
// true
```
