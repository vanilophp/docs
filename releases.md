# Release Notes

> For upcoming releases refer to the [Roadmap](roadmap.md).

## 3.6
##### 2023-03-07

- Added Laravel 10 support
- Added the `CartCreated`, `CartUpdated`, `CartDeleted` and `CartDeleting` events
- Added master product & variant (Foundation) to the morph map
- Added the `processing` order status enum value
- Added the `ready_for_pickup` fulfillment status enum value
- Added the `DetailedAmount` interface (Contracts) and its Dto (Support)
- Added Zone support to shipping methods (optional)
- Added the `Order::getLanguage()` method
- Added the `ShippingMethodSelected` checkout event
- Added Shipping fee calculator support
- Added the flat fee shipping fee calculator (Foundation)
- Added a listener (Foundation) that calculates the shipping fee on checkout shipping method change
- Added the following fields as recognized, regular fields of the checkout:
  - `shipping_method_id`
  - `payment_method_id`
  - `ship_to_billing_address`
  - `notes`
- Added `ArrayAccess` to the CheckoutManager and to the Checkout Stores
- Added `Shippable` to the CheckoutManager and to the Checkout Stores
- Added the `vanilo.checkout.default.weight_unit` config key which defaults to 'kg'
- Added the following methods to the Checkout implementations (Manager, Stores but not the interfaces):
  - `getShipToBillingAddress()`
  - `setShipToBillingAddress()`
  - `getShippingMethodId()`
  - `setShippingMethodId()`
  - `getPaymentMethodId()`
  - `setPaymentMethodId()`
  - `getShippingAmount()`
  - `setShippingAmount()`
  - `getTaxesAmount()`
  - `setTaxesAmount()`
  - `getNotes()`
  - `setNotes()`
- Added optional hooks (callbacks) support to the `OrderFactory::createFromDataArray()` method
- Added `payable_remote_id` field with getter/setter methods to the Foundation Order model
- Added implementation of the `Adjustable` interface to the Foundation Order model
- Added the copying of checkout/cart adjustments to the order created by the Foundation OrderFactory
- Added `int` cast to OrderItem::quantity property
- Added `dimensions()` and `hasDimensions()` methods to the `MasterProductVariant` model
  Added the `Dimension` DTO class in the support module (provides a default implementation for the same name interface)
- Added the vanilo/support ^3.6 dependency to the Product module
- Added the `subtype` field and the `getSubtype()` method to the `Payment` model
- Added the `DeleteCartAdjustments` listener on `CartDeleting` event
- Added the `CalculateShippingFees` listener connected to shipping address/method change and cart updated events
- Added the `clear()` method to `AdjustmentCollection` implementations
- Added the `invalidateAdjustments()` method to `Adjustable` implementations
- Added the `shippingAdjustmentsTotal()`, `taxAdjustmentsTotal()` and `promotionAdjustmentsTotal()` methods to the Adjustable Cart model in foundation
- Refactored the internals of the Checkout stores (session and request)
- Changed the return value of `Product::dimension()` to be a `Dimension` object from the support package instead of an anonymous class object
- Changed minimum Address module requirement to v2.5
- Fixed the data loss issue when using the session checkout driver with cookie session driver in Laravel

## 3.5
##### 2023-02-23

- Added the `description` and `state` fields to the master product variants table
- Added the Address comparison utility
- Added the `reference_number` field to the shipments model
- Added the `shippables` many-to-many, polymorphic relationship.  
  It allows all the following scenarios:  
  a) shipping one order in one shipment  
  b) shipping one order in multiple shipments  
  c) shipping multiple orders in one shipment
- Added `Shipment::orders` and `Order::shipments` to the Foundation models
- Added the `FulfillmentStatus` enum to the Orders module
- Added `fulfillment_status` to the `orders` and `order_items` tables
- Added `language` field to orders
- Added `ordered_at` field to orders (defaults to `created_at` unless explicitly specified)
- Added `shippingMethod` and `customer` fields/relations to the extended (Foundation) Order model
- Changed the random number algo in `TimeHash` generator from `mt_rand` to `random_int` for a decreased collision probability
- Fixed the `Carrier::name()` method to overcome accessor infinite loop
- Fixed the address creation with order factory when passing an explicit address type

## 3.4
##### 2023-01-25

- Added the `MasterProdcutVariant::findBySku()` method
- Added the `Product::findBySku()` method
- Added the `ConfigurableModel` trait
- Added processing of `configuration` to the order factory, if the checkout item is a configurable
- Added `Configurable` to the `CartItem` model (incl. implementing the interface)
- Added `Configurable` to the `OrderItem` model (incl. implementing the interface)
- Added the `Configurable` and the `Configuration` interfaces
- Added `Configurable` interface to the `PaymentMethod` model
- Added the missing `ShippingMethod` interface and proxy
- Added `Configurable` interface to the `Carrier`, `Shipment` and `ShippingMethod` models
- Changed the `Carrier`, `Shipment` and `ShippingMethod` models to use the `ConfigurableModel` trait
- Changed the `PaymentMethod` model to use the `ConfigurableModel` trait
- Fixed missing required argument from checkout drivers using the `update()` method

## 3.3
##### 2023-01-05

- Fixed the session checkout store persistence error
- Fixed float conversion of master product variant fields (price, original price and dimensions)
- Changed the product stock field to be cast to float
- Removed the Buyable interface/trait from `Foundation\MasterProduct` - it was conceptually wrong;
- Added `Buyable` to `Foundation\MasterProductVariant`
- Added the `Product::findBySku()` method to the base product class
- Added the `MasterProdcutVariant::findBySku()` method

## 3.2
##### 2022-12-08

- Added forwarding/proxying of method calls and property getters from the Checkout manager to the underlying store
- Added `is_active` flag to shipping methods
- Added `Cart::fresh()` method to the Cart facade
- Added the `is_master_product()` helper function (Foundation)
- Added the `MasterProduct::actives()` and `inactives()` query builder scopes
- Added the `Product::inactives()`query builder scope
- Added an extended `MasterProduct` model (Foundation)
- Changed checkout store resolution to happen via the Laravel DI instead of `new SpecificStoreClass()`
- Changed both built-in checkout stores to save unknown properties as custom attributes on the `update()` method call
- Changed minimum Concord version requirement to v1.12
- Fixed missing implementation parts of the checkout session store


## 3.1
##### 2022-11-07

- Added the Master Product module
- Added the Adjustments module
- Added the extended Adjustable Cart model to `Foundation` (original Cart model still there, intact)
- Added `channel_id` to the extended `Order` model in Foundation
- Added Shipping methods
- Added the `getGatewayName()` method to the PaymentMethod class.
  It can retrieve the gateway name without instantiating it,
  thus gateway configuration errors don't affect it
- Added the `PropertyValue::findByPropertyAndValue('color', 'red')` finder method
- Added the `valueOfProperty()`, `assignPropertyValue()` and `assignPropertyValues()` methods to the `HasPropertyValues` trait
- Added the Sluggable behavior to the Channel model
- Added configuration getter and setter methods to the Channel model
- Added PHP 8.2 Support
- Added Enum 4.0 Support
- Changed minimum Laravel requirement to 9.2
- Changed minimum Konekt module requirements to:
  - Address: 2.2
  - Concord: 1.11
  - Customer: 2.3.1
  - Enum: 3.1.1
  - Laravel Migration Compatibility: 1.5
  - User: 2.4

## 3.0
##### 2022-02-28

- Removed PHP 7.4 Support
- Removed Laravel 6-8 Support
- Removed Admin from "Framework" - it is available as an optional separate package see [vanilo/admin](https://github.com/vanilophp/admin)
- Added Laravel 9 Support (minimum requirement)
- Added PHP 8.1 Support
- Added the Links module
- Added the Shipment module
- Added `original_price` field to products
- Added product dimension (`width`, `height`, `depth`) and `weight` fields
- BC Change: Renamed Framework Module and Namespace to "Foundation"
- BC Change: Renamed `vanilo.framework.*` config values to `vanilo.foundation.*`
- BC Change: Interfaces have been improved with types and extended with methods
- BC: Added the static `findBySlug(string $slug): ?Property;` method to the Property interface
- Switched to monorepo layout: Core modules are all in the monorepo, separated as readonly subtree split in their own repositories

## 2.2
##### 2021-09-11

- Dropped PHP 7.3 support
- Stripe, PayPal, Adyen, SimplePay and Netopia payment modules
- Added Payment History
- Improved the Payment Module to work with more gateways
- Added Order print in Admin
- Added show/hide closed orders button to order list in admin
- Several minor fixes and improvements

## 2.1
##### 2020-12-31

- Added PHP 8 Support
- Added Payment module
- Added Primary Image feature
- Added Spatie Media Library v9 Support
- Refactored image handling
- Added images to taxonomies and taxons
- Added option to define separate image conversions per entity type (product, taxonomy, taxon)
- Added the `HasImages` interface - extracted it from `Buyable` (in a compatible manner)
- Added an extended `Order` model (in Framework module) that implements the `Payable` interface
- Added Payment method CRUD
- Added dispatching of order cancelled and completed events when updating their status in the admin panel
- Added `HasImagesFromMediaLibrary` trait (support module) for supporting the new `HasImages` interface
- Deprecated the `BuyableImageSpatieV8` and `BuyableImageSpatieV7` traits
- Added generic NanoId generator utility class to support module
- Added `Order::findByNumber()` method
- Added unique index to `orders.number` field
- Added `Taxon::findOneByParentsAndSlug` method
- Added `Taxonomy::findOneBySlug` method
- Added `taxa` (and `taxons` alias) relationship to `Taxonomy` model
- Added payments, payment statuses, payment methods
- Added gateway registry
- Added Payment factory (creates payment from payables and payment methods)
- Added payment events
- Added payment- gateway, method, request and response contracts
- Added NullGateway
- Changed (reverted) the Product model's route key to the default (id) instead of `slug`. Resolving by slug must be done by the host application.
- Changed the internal implementation of the nanoid order number generator to utilize the generic nanoid generator from the support module
- Changed Payable interface signature (since it wasn't used until 2.1 not considering it as BC)
- Fixed model registration to take into account Concord's `register_route_models` setting

## 2.0 (Framework)
##### 2020-10-31

- Themeable Admin
- PHP 7.4+ (Framework only)

## 2.0
##### 2020-10-11

> All components, except the framework have been released.

- Added Laravel 8 support
- Dropped Laravel 5 support
- Dropped PHP 7.2 support
- BC: Upgrade to Enum v3
- BC: Contracts have been updated (mostly missing return types have been added)
- BC: Renamed getValue() -> getCastedValue() in the PropertyValue module
- Added the Payable interface
- Added the Shippable interface
- Added nanoid style order number generator

## 1.2
##### 2020-03-29

- Added Laravel 7 support
- Added PHP 7.4 support
- Dropped PHP 7.1 support
- Combination of PHP 7.4 & Laravel 5.6|5.7 is not recommended
  due to improper order of `implode()` arguments in eloquent-sluggable dependency

## 1.1
##### 2019-11-25

- Fully supports Laravel 6

## 1.0
##### 2019-11-11

- Stable release
- Supports PHP 7.1 - 7.3 & Laravel 5.5 - 5.8
- Added simple product stock
- Added ProductFinder pagination support
- Added Channel Module
- Added custom checkout attributes
- Migration compatibility fixes with bigint user id

## 0.5
##### 2019-02-11

- Arbitrary product properties (eg. color, RAM size, Wheel size, etc)
- Product filtering

## 0.4
##### 2018-11-12

- Product Categories
- Cart can be preserved for a user across devices and logins
- Product sales figures (units sold, last sale date)
- Laravel 5.7 Support
- PHP 7.3 Support (tested against RC)
- Lots of UI improvements

## 0.3
##### 2018-08-19

- Product images
- Custom Product attributes in cart
- Settings/Preferences in admin (arbitrary settings can be added)
- Minimum Laravel is 5.5 if using the Framework, due to Spatie Media Library v7 being incompatible with it
- Individual modules (cart, order, product, etc) still support Laravel 5.4

## 0.2
##### 2018-02-19

- Starting with v0.2 there is an upgrade path to the upcoming versions (0.3, etc).
- Support for Laravel 5.6,
- Bugfixes,
- Cart improvements.

## 0.1
##### 2017-12-11

Vanilo v0.1 was the very first Vanilo release ever.
Consider it as a **TECHNOLOGY PREVIEW**.

This version is not yet ready to create applications with. Feel free to
discover it, play with it, and
[submit issues on github](https://github.com/vanilophp/framework/issues).

### What's in it:

- Concord (foundation for customizing E-commerce components)
- Customers
- Products
- Cart
- Checkout
- Orders
- Admin
    - Users
    - Acl (permissions, roles)
    - Extendable

Please note that Vanilo as of v0.1:
- lacks many features,
- features are incomplete,
- there are shortcuts, bugs, TODOs and other nasty things in the codebase,
- even basic concepts are subject to change,
- 0.1 is not intended to be upgradable to v0.2.

Beginning with version 0.2 Vanilo provides a documented [upgrade path](upgrade.md) up to the 1.0
stable release.

For more details on future releases refer to the [Roadmap](roadmap.md).
