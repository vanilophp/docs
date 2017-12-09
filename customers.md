# Customers

Customers in E-commerce systems are one of the very basic data models.

Some webshops however, don't use them at all: all orders are "anonymus"
without user login, customer account.

It's more likely that E-commerce systems of 2018 need the handling of
customers, therefore they're enabled by default in Vanilo, but it's
possible to opt-out.

> The customer component is built using the
> [konekt/customer](https://github.com/artkonekt/customer) library.

## Creating Customers

_Just go the plain old Laravel way:_

```php
use Vanilo\Framework\Models\Customer;
use Konekt\Customer\Models\CustomerType;

$johnDoe = Customer::create([
    'firstname' => 'John',
    'lastname'  => 'Doe',
    'type'      => CustomerType::INDIVIDUAL
]);

echo $johnDoe->getName();
// John Doe
var_dump($johnDoe->isIndividual());
// bool(true)
var_dump($johnDoe->isOrganization());
// bool(false)

$acmeInc = Customer::create([
   'company_name' => 'Acme Inc.',
   'type'      => CustomerType::ORGANIZATION
]);

echo $acmeInc->getName();
// Acme Inc.
var_dump($acmeInc->isIndividual());
// bool(false)
var_dump($acmeInc->isOrganization());
// bool(true)
```

## Fields

| Name            | Type                              | Notes                                                                                  |
|:----------------|:----------------------------------|:---------------------------------------------------------------------------------------|
| id              | autoinc                           |                                                                                        |
| type            | CustomerType                      | enum                                                                                   |
| email           | string                            |                                                                                        |
| phone           | string(22)                        |                                                                                        |
| firstname       | string                            |                                                                                        |
| lastname        | string                            |                                                                                        |
| company_name    | string                            |                                                                                        |
| tax_nr          | string(17)                        | [Tax/VAT Identification Number](https://www.wikiwand.com/en/VAT_identification_number) |
| registration_nr | Company/Trade Registration Number |                                                                                        |
| is_active       | bool                              | true by default                                                                        |

## Enums

**CustomerType**:

| Const        | Value        | Notes                                                      |
|:-------------|:-------------|:-----------------------------------------------------------|
| ORGANIZATION | organization | (_default_) Use for companies, foundations, GOs, NGOs, etc |
| INDIVIDUAL   | individual   | For natural human customers                                |

## Extending, Customizing

> Please refer to the [Models](models.md) and the [Enums](enums.md)
> sections first for general customization information.

### Customer Type

This is an [enum type](https://artkonekt.github.io/enum/#/) so if you
want to add further variants, extend the class and define new consts.

**1. Create The Extended Class:**

```php
// App\CustomerType.php
class CustomerType extends Konekt\Customer\Models\CustomerType
{
    const ROBOT = 'robot';
    const ALIEN = 'alien';
    
    // You can also change the default value:
    const __default = self::ROBOT;
}
```

**2. Register The Extended Type:**

```php
// app/Providers/AppServiceProvider.php:
use Illuminate\Support\ServiceProvider;
use Konekt\Customer\Contracts\CustomerType as CustomerTypeContract;

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        $this->app->concord->registerEnum(
            CustomerTypeContract::class, \App\CustomerType::class
        );
    }
}
```

So now your extended type is in effect:
```php
use Vanilo\Framework\Models\Customer;

$robot = Customer::create([
    'firstname' => 'R2D2'
]);

var_dump($robot->type);
// object(App\CustomerType)#1050 (1) {
//     ["value":protected]=>
//     string(5) "robot"
//   }
```

### Extending The Customer Model

**1. Create the extended model class:**

```php
// app/Customer.php
class Customer extends Konekt\Customer\Models\Customer
{
    public function getName()
    {
        if ($this->type->isRobot()) {
            return 'Robot ' . $this->firstname;
        }
        
        return parent::getName();        
    }
}
```

**2. Register the model with concord:**

```php
// app/Providers/AppServiceProvider.php:
use Illuminate\Support\ServiceProvider;
use Konekt\Customer\Contracts\Customer as CustomerContract;

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        $this->app->concord->registerModel(
            CustomerContract::class, \App\Customer::class
        );
    }
}
```

So now any other library using the Customer module will be aware of your
new type and it will be used.

If for any reason you need to know what is the current concrete class in
effect, use the
[proxy class](https://artkonekt.github.io/concord/#/models?id=model-proxy-has-even-more):

```php
use Konekt\Customer\Models\CustomerProxy;

echo CustomerProxy::modelClass();
// App\Customer
```

