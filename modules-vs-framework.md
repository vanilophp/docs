# Vanilo Modules vs. Vanilo Framework

## Modules

Vanilo consists of standalone modules like product, cart, checkout,
order, etc. They can be used independently from each other, even by
arbitrarily picking one or more of them. The single "glue" between them
is the `vanilo\contracts` package. And Laravel + Concord of course.

All these modules have their standalone github repositories and can be
separately installed via composer.

### List Of Modules

- [AppShell](https://github.com/artkonekt/appshell): App + Admin basics:
    - [User Module](users.md)
    - [Acl Module](acl.md)
    - [Menu Module](https://github.com/artkonekt/menu)
    - [Address Module](addresses.md)
- [Product Module](products.md)
- [Cart Module](cart.md)
- [Checkout Module](checkout.md)
- [Order Module](orders.md)

### Vanilo Contracts Package

The contracts package defines interfaces shared across various Vanilo
modules. It also aims to support the separate use of modules, so that you
can choose for example to use the checkout component without the cart.

This package contains solely interfaces, no actual code.

## Vanilo Framework

The Vanilo framework contains all the modules and adds the bindings
between them + the admin panel. Using the framework is the
recommended way.
