# Promotions

Promotions are a powerful feature in Vanilo Cloud that allows you to create various types of discounts and special
offers for shoppers.

## Core Concepts

A promotion consists of:

- **Basic** properties (name, description, validity period, etc.)
- **Rules** that determine in _what conditions_ the promotion applies
- **Actions** that define _what the promotion does_
- **Coupon** codes (optional) that can be used to _activate_ the promotion

## Promotion Properties

```php
$promotion = Promotion::create([
    'name' => 'Summer Sale',
    'description' => 'Special summer discount',
    'priority' => 1,
    'is_exclusive' => false,
    'usage_limit' => 100,
    'usage_count' => 0,
    'is_coupon_based' => false,
    'starts_at' => '2025-06-01 00:00:00',
    'ends_at' => '2025-08-31 23:59:59',
    'applies_to_discounted' => true
]);
```

Each promotion has the following configurable properties:

- **Name**: The display name of the promotion
- **Description**: Detailed explanation of the promotion
- **Priority**: Determines which promotion takes precedence when multiple promotions apply (higher number = higher priority)
- **Exclusive**: When enabled, this promotion will be the only one applied to an order
- **Usage Limit**: Maximum number of times the promotion can be used (optional)
- **Coupon Based**: Whether the promotion requires a coupon code
- **Start Date**: When the promotion becomes active (optional)
- **End Date**: When the promotion expires (optional)
- **Applies to Discounted Items**: Whether the promotion can be applied to items already on sale

### Validity

A promotion is considered valid when:

- It has started (if `starts_at` is set)
- It hasn't expired (if `ends_at` is set)
- It hasn't reached its usage limit (if `usage_limit` is set)

If neither start date, end date and usage limit is set, the promotion is considered valid indefinitely.

## Rules

Rules determine when a promotion applies. Each rule must pass for the promotion to be eligible.
Multiple rules can be combined, and all must be satisfied.

To add rules to a promotion, use the `addRule` method:

```php
$promotion->addRule('cart_quantity', ['count' => 3]);
$promotion->addRule('cart_minimum_value', ['amount' => 100]);
```

The first parameter is the rule type, and the second is the rule configuration.

The rule type can be either the ID or the class name of a registered rule type.

### Cart Quantity Rule Type

This rule checks if the cart contains a minimum number of items, like "Buy 3 or more items".

The configuration is as follows:

```json
{
    "count": 3
}
```

The above configuration means "Buy 3 or more items to qualify", where `count` is the minimum number of items required in the cart.

### Cart Minimum Value Rule

This rule checks if the cart total reaches a minimum amount.

Configuration example: "Spend $100 or more to qualify":

```json
{
    "amount": 100
}
```

## Actions

Actions define what happens when a promotion is applied. Multiple actions can be added to a single promotion.

To add actions to a promotion, use the `addAction` method:

```php
$promotion->addAction('cart_fixed_discount', ['amount' => 10]);
$promotion->addAction('cart_percentage_discount', ['percentage' => 20]);
```

The first parameter is the action type, and the second is the action configuration.

The action type can be either the ID or the class name of a registered action type.

### Cart Fixed Discount

Applies a fixed amount discount to the entire cart (items only, excluding shipping fees).

Sample configuration, "$10 off your order":

```json
{
   "amount": 10
}
```

- **amount**: Fixed discount amount to apply
- Example: "$10 off your order"

### Cart Percentage Discount

Applies a percentage discount to the cart, like "20% off your order".

It takes a single configuration property, the `percentage`, which is the discount percentage to apply.

Example "20% off your order":

```json
{
    "percentage": 20
}
```

### Staggered Discount

Applies different discounts on individual cart items, based on the quantity of each line item.

The configuration takes a single `thresholds` property, which is an object mapping cart quantity thresholds to
discount percentages.

Example "Buy 10 get 5% off, 20 to get 7% off; 30 get 10% off":

```json
{
   "thresholds": {
      "10": 5,
      "20": 7,
      "30": 10
   }
}
```

## Coupons

Coupons are optional codes that can be associated with promotions. They provide additional control over promotion usage.

```php
$coupon = Coupon::create([
    'code' => 'SUMMER2025',
    'promotion_id' => $promotion->id,
    'usage_limit' => 50,
    'per_customer_usage_limit' => 1,
    'usage_count' => 0,
    'expires_at' => '2025-08-31 23:59:59'
]);
```

It is possible to directly look up a promotion based on the coupon code:

```php
$promotion = Promotion::findByCouponCode('SUMMER2025');
```

### Coupon Properties

- **Code**: The unique code customers enter
- **Usage Limit**: Maximum number of times the coupon can be used
- **Per Customer Limit**: Maximum number of times a single customer can use the coupon
- **Expiration Date**: When the coupon becomes invalid

### Using Coupons

Customers can enter coupon codes during checkout. The system will:

1. Validate the coupon code
2. Check if the promotion is still valid
3. Verify all rules are satisfied
4. Apply the promotion's actions if eligible

## Best Practices

1. Priority Management
   - Use higher priority numbers for more important promotions
   - Set exclusive promotions carefully to avoid conflicts
2. Usage Limits
   - Set appropriate usage limits to control promotion costs
   - Consider per-customer limits for personalized offers
3. Validity Periods
   - Set clear start and end dates for time-sensitive promotions
   - Monitor usage to adjust limits if needed
4. Rule Combinations
   - Combine rules to create targeted promotions
   - Test rule combinations to ensure they work as intended
5. Action Stacking
   - Be mindful of how multiple actions interact
   - Consider the order of operations when combining different types of discounts

## Extending the System

### Adding New Rule Types

1. Create a new rule class implementing `PromotionRuleType` interface
2. Register the rule type in your application's `AppServiceProvider` class.

```php
class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        PromotionRuleTypes::register('new_rule_type', NewRuleType::class);
    }
}
```

### Adding New Action Types

1. Create a new action class implementing `PromotionActionType` interface
2. Register the action type in your application's `AppServiceProvider` class.

```php
class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        PromotionActionTypes::register('new_action_type', NewActionType::class);
    }
}
```
