# Translations

> The translations module is available since version 5.0

Vanilo comes with a translation module, which allows you to translate any Eloquent models without any modifications
to the models themselves.

This is achieved by using a `Translation` model, which is a simple Eloquent model with a `translatable_id` and
`translatable_type` column.

## Translating Models

```php
// Model to translate:
$product = Product::find(1);

// Create a French translation for the model:
Translation::createForModel($product, 'fr', [
    'name' => 'Quelque chose',
    'slug' => 'quelque-chose',
    'description' => 'Rien n’est facile, mais qui ne veut rien?'
]);
```

## Using Translations

```php
// Retrieve the French translation of the model:
$translation = Translation::findByModel($product, 'fr');

// Fields:
$translation->getLanguage();
// "fr"
// Get any field:
$translation->getTranslatedField('description')
// 'Rien n’est facile, mais qui ne veut rien?'
$translation->getTranslatedField('name')
// // 'Quelque chose'

// The `name` and `slug` fields are explicit, meaning you can retrieve them with a dedicated getter:

$translation->getName();
// 'Quelque chose'
$translation->getSlug()
// 'quelque-chose'
```

### Handling Non-existent Translations and Fields

```php
Translation::findByModel($product, 'ee');
// null if no translation for the given model in the given language

$translation = Translation::findByModel($product, 'fr');
$translation->getTranslatedField('gtin');
// null - the field hasn't been translated
```

### Retrieving by Slug

> Retrieving by slug can be useful in a controller to get the model with translation based on a translated URL parameter

```php
$product = Product::create(['name' => 'What Else', 'slug' => 'what-else']);
Translation::createForModel($product, 'de', ['name' => 'Was anderes', 'slug' => 'was-anderes']);

$translation = Translation::findBySlug('product', 'was-anderes', 'de');

// If you aren't sure about the exact morph type name, use the helper function:
$translation = Translation::findBySlug(morph_type_of($product), 'was-anderes', 'de');

// To get the associated product:
$translation->getTranslatable();
// => The `Product` instance
```

## Blade Helper

To make it simple to work with translated fields of a model, the `_mt($model, 'field')` helper is available:

```blade
<h1>{{ _mt($product, 'title') }}</h1>
<p>{{ _mt($product, 'description') }}</p>
```

This will retrieve the respective fields translated in the current locale based on `app()->getLocale()`.

If you explicitly want to tell which language to use for the translation, pass the language code as the 3rd parameter:

```blade
<h1>{{ _mt($product, 'title', 'es') }}</h1>
```

If the second parameter is null, then the entire `Translation` object is returned:

```php
_mt($product);
// => Translation object of the product in the current locale

_mt($product, null, 'cz');
// => The Czech Translation object of the product
```
