# Extending Vanilo's Admin Panel

> This section is incomplete, for more details refer to the
[AppShell Documentation](https://konekt.dev/appshell/2.x/customize-admin-ui)

## Views

If you want to define new views for the admin panel you need to use this layout:

`@extends('appshell::layouts.default')`

### Blade Sections

- title: `<title>`
- content: the body of the page
- footer: additional footer content
- scripts: scripts to add below `<script src="/js/appshell.js">`

## Menu

The admin menu was built with the
[konekt/menu](https://github.com/artkonekt/menu) library.

To add a new menu item to the admin you need to locate the menu using
the `Menu` facade:

```php
if ($menu = Menu::get('appshell')) {
    // Add a new group
    $newGroup = $menu->addItem('reports', __('Reports'));
    
    // Add an item to the new group:
    $newGroup
        ->addSubItem('abandoned_carts', __('Abandoned Carts'), '/admin/abandoned-carts')
        ->data('icon', 'accounts');
    
    // Add an item with route name:
    $newGroup
        ->addSubItem('campaigns', __('Campaigns'), [
            'route' => 'reports.campaigns.index'
        ])
        ->data('icon', 'shield-security');
}
```

> For detailed usage instructions on menus, please read the
> [konekt menu readme](https://github.com/artkonekt/menu)).

## Icons

The admin panel uses the
[Material Design Iconic Font](http://zavoloklom.github.io/material-design-iconic-font/icons.html).
Refer to their site for the available glyphs and the names of icons.
