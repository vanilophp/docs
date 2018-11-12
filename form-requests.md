# Form Requests

Vanilo uses Laravel's
[Form Requests](https://laravel.com/docs/5.7/validation#form-request-validation)
feature to separate validation logic from controllers.

Other than being a nice example of separation of concerns, this approach
makes it possible to easily modify default validation logic in your
application.

Requests are usually bound to specific models, and there are usually two
types of them for each model like: `create_product`, `update_product`.

> Currently these form requests are only used in Vanilo's Admin.

## Concord's Role

Requests in Concord are very similar to [models](models.md) and
[enums](enums.md). Requests also have a "root" contract (interface) that
can be used for overriding them.

## Listing Requests

```
$> php artisan concord:requests

+---------------------+------------------------------------------------------+-------------------------------------------------+
| Name                | Contract                                             | Concrete                                        |
+---------------------+------------------------------------------------------+-------------------------------------------------+
| create_address      | Konekt\AppShell\Contracts\Requests\CreateAddress     | Konekt\AppShell\Http\Requests\CreateAddress     |
| create_address_form | Konekt\AppShell\Contracts\Requests\CreateAddressForm | Konekt\AppShell\Http\Requests\CreateAddressForm |
| create_customer     | Konekt\AppShell\Contracts\Requests\CreateCustomer    | Konekt\AppShell\Http\Requests\CreateCustomer    |
| create_product      | Vanilo\Framework\Contracts\Requests\CreateProduct    | Vanilo\Framework\Http\Requests\CreateProduct    |
| create_role         | Konekt\AppShell\Contracts\Requests\CreateRole        | Konekt\AppShell\Http\Requests\CreateRole        |
| create_taxon        | Vanilo\Framework\Contracts\Requests\CreateTaxon      | Vanilo\Framework\Http\Requests\CreateTaxon      |
| create_taxon_form   | Vanilo\Framework\Contracts\Requests\CreateTaxonForm  | Vanilo\Framework\Http\Requests\CreateTaxonForm  |
| create_taxonomy     | Vanilo\Framework\Contracts\Requests\CreateTaxonomy   | Vanilo\Framework\Http\Requests\CreateTaxonomy   |
| create_user         | Konekt\AppShell\Contracts\Requests\CreateUser        | Konekt\AppShell\Http\Requests\CreateUser        |
| edit_address_form   | Konekt\AppShell\Contracts\Requests\EditAddressForm   | Konekt\AppShell\Http\Requests\EditAddressForm   |
| save_account        | Konekt\AppShell\Contracts\Requests\SaveAccount       | Konekt\AppShell\Http\Requests\SaveAccount       |
| sync_model_taxons   | Vanilo\Framework\Contracts\Requests\SyncModelTaxons  | Vanilo\Framework\Http\Requests\SyncModelTaxons  |
| update_address      | Konekt\AppShell\Contracts\Requests\UpdateAddress     | Konekt\AppShell\Http\Requests\UpdateAddress     |
| update_customer     | Konekt\AppShell\Contracts\Requests\UpdateCustomer    | Konekt\AppShell\Http\Requests\UpdateCustomer    |
| update_order        | Vanilo\Framework\Contracts\Requests\UpdateOrder      | Vanilo\Framework\Http\Requests\UpdateOrder      |
| update_product      | Vanilo\Framework\Contracts\Requests\UpdateProduct    | Vanilo\Framework\Http\Requests\UpdateProduct    |
| update_role         | Konekt\AppShell\Contracts\Requests\UpdateRole        | Konekt\AppShell\Http\Requests\UpdateRole        |
| update_taxon        | Vanilo\Framework\Contracts\Requests\UpdateTaxon      | Vanilo\Framework\Http\Requests\UpdateTaxon      |
| update_taxonomy     | Vanilo\Framework\Contracts\Requests\UpdateTaxonomy   | Vanilo\Framework\Http\Requests\UpdateTaxonomy   |
| update_user         | Konekt\AppShell\Contracts\Requests\UpdateUser        | Konekt\AppShell\Http\Requests\UpdateUser        |
+---------------------+------------------------------------------------------+-------------------------------------------------+
```

## Customizing Form Requests

Form request customization is usually paired with the customization of
its related model.

Follow these steps to customize a form request:

**1. Create The Extended Class(es):**

```php
//app/Http/Requests/CreateProduct.php
namespace App\Http\Requests;

use Vanilo\Framework\Http\Requests\CreateProduct as BaseCreateProduct;

class CreateProduct extends BaseCreateProduct
{
    public function rules()
    {
        $rules = parent::rules();
        
        // Extend the rules with a new mandatory field:
        $rules['field_added_by_app'] = 'required|min:2|max:255';
        
        return $rules;
    }
}
```

**2. Register With Concord:**

```php
// app/Providers/AppServiceProvider.php
namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Vanilo\Framework\Contracts\Requests\CreateProduct as CreateProductContract;
use App\Http\Requests\CreateProduct;

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        // Replace the CreateProduct type with the app's one:
        $this->app->concord->registerRequest(
            CreateProductContract::class,
            CreateProduct::class
        );
    }
}
```

This way the new validation rule will be passed to the controllers using
the given request type. Similarly to models and enums, the interface
will be bound to the new class in Laravel's container.

