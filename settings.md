# Settings

> Implemented using the [Gears](https://github.com/artkonekt/gears)
> library. Refer to
> [Gears Documentation](https://artkonekt.github.io/gears/#/) for all
> the features.

## Overview

Settings allow you to manage _settings_ and _preferences_ in your
application.

- **Settings** are user defined values that **apply to the
  application**.
- **Preferences** are user defined values that **apply to a specific
  user**.

**Setting examples:**

- API keys,
- Enable or disable features,
- Account related data (Billing data, plan, etc).

**Preference examples:**

- UI preferences like color scheme, font size, etc,
- Language,
- Timezone.

Settings and preferences are being managed separately. Values (by
default) are being saved to the database (`settings` and `preferences`
tables) and are cached with the
[configured cache](https://laravel.com/docs/5.6/cache) for your
application.

## Creating Settings

Settings & Preferences are identified by a key (string) and **need to be
registered** first in order to be used.

The recommended place to register settings is your application's
`AppServiceProvider::boot()` method:

```php
$settingsRegistry = $this->app['gears.settings_registry'];

$settingsRegistry->addByKey('mailchimp.api_key');
$settingsRegistry->addByKey('mailchimp.list_id');
```

### Settings Defaults

It is possible to define a default value for a setting:

```php
use \Konekt\Gears\Defaults\SimpleSetting;

// Set default value of `start_url` to '/dashboard'
$settingsRegistry->add(new SimpleSetting('start_url', '/dashboard'));
```

### Settings Options

You can also set a list of possible options (useful for dropdowns):

```php
// Default `theme` is dark, available options are dark and light
$settingsRegistry->add(new SimpleSetting('theme', 'dark', ['dark', 'light']));
```

> The [Gear Documentation](https://artkonekt.github.io/gears/#/defining-settings?id=custom-setting-classes)
> also has an example of creating custom setting classes.

## Creating Preferences

Preferences need to be registered the same way as described above by
Settings; preferably in your application's `AppServiceProvider::boot()`
methodL

```php
use \Konekt\Gears\Defaults\SimplePreference;

$prefsRegistry = $this->app('gears.preferences_registry');

// Simplest way, adding only by key:
$prefsRegistry->addByKey('secondary_email');

// Defining a default ('en'):
$prefsRegistry->add(new SimplePreference('language', 'en'));

// Defining a default (yellow) and 3 available options:
$prefsRegistry->add(new SimplePreference('color_scheme', 'yellow', ['green', 'red', 'yellow']));
```

## Reading & Writing Setting Values

> Remember to register settings before using them.

The easiest way is using the facades:

```php
use Konekt\Gears\Facades\Settings;

Settings::set('mailgun.api_key', '123456789abcdef');
echo Settings::get('mailgun.api_key');
// '123456789abcdef'
```

If you don't prefer using facades you can get the service from the container:

```php
$settings = app('gears.settings');
$settings->set('mailgun.api_key', 'fbcdef');
echo $settings->get('mailgun.api_key');
// fbcdef
```
### Multiple Setting Values At Once

```php
use Konekt\Gears\Defaults\SimpleSetting;
use Konekt\Gears\Facades\Settings;

$reg = app('gears.settings_registry');
$reg->addByKey('postmark.server_token');
$reg->addByKey('postmark.send_method');
$reg->add(new SimpleSetting('postmark.api_version', 'v3'));


// Saving multiple settings:
Settings::update([
   'postmark.server_token' => '9988776655',
   'postmark.send_method'  => 'smtp' 
]);

// Retrieving all settings:
var_dump(Settings::all());
// [
//     'postmark.server_token' => '988776655',
//     'postmark.send_method' => 'smtp',
//     'postmark.api_version' => 'v3',  <--- it returns the default if not explicitly set
// ]
```

### Removing Settings (Resetting Their Values)

If you remove a setting it's value will be reset to it's default value:

```php
use Konekt\Gears\Defaults\SimpleSetting;
use Konekt\Gears\Facades\Settings;

// Define a setting without default value:
app('gears.settings_registry')->addByKey('reports_to');

Settings::set('reports_to', 'me@where.com');
echo Settings::get('reports_to');
// 'me@where.com'
Settings::forget('reports_to');
var_dump(Settings::get('reports_to'));
// NULL

// Define a setting having a default value:
app('gears.settings_registry')->add(new SimpleSetting('lang', 'en'));

Settings::set('lang', 'nl');
echo Settings::get('lang');
// 'nl'
Settings::forget('lang');
echo Settings::get('lang');
// 'en'
```

It is possible to reset multiple values at once:

```php
use Konekt\Gears\Facades\Settings;

app('gears.settings_registry')->addByKey('billing_name');
app('gears.settings_registry')->addByKey('billing_address');

Settings::set('billing_name', 'My Company');
Settings::set('billing_address', 'Bernauer Str. 12, Berlin, Germany');

var_dump(Settings::all());
// [
//     'billing_name' => 'My Company',
//     'billing_address' => 'Bernauer Str. 12, Berlin, Germany'
// ]

Settings::reset(['billing_name', 'billing_address']);

var_dump(Settings::all());
// [
//     'billing_name' => NULL,
//     'billing_address' => NULL
// ]
```

## Reading & Writing Preference Values

> Remember to register preferences before using them.

```php
use App\User;
use Illuminate\Support\Facades\Auth;
use Konekt\Gears\Facades\Preferences;

// Save preference for a user
$userId = 1;
Preferences::set('color_scheme', 'green', $userId);
echo Preferences::get('color_scheme', $userId);
// 'green'

// You can also pass a user object:
$user = User::find(1);
Preferences::set('color_scheme', 'green', $user);
echo Preferences::get('color_scheme', $user);
// 'green'

// If you don't pass a user, then Auth::user() gets picked:
Preferences::set('color_scheme', 'purple');
echo Preferences::get('color_scheme', Auth::user());
// purple

// which equals to:
echo Preferences::get('color_scheme');
// purple
```

### Multiple Preference Values At Once

```php
use Konekt\Gears\Facades\Preferences;

Preferences::update([
   'color_scheme' => 'orange',
   'font' => 'Adelle Sans' 
], $userId);

var_dump(Preferences::all($userId));
// [
//     'color_scheme' => 'orange',
//     'font' => 'Adelle Sans',
// ]
```

### Removing Preferences (Resetting Their Values)

```php
// Reset a preference for user with id 7
$userId = 7;

// Forget a setting:
Preferences::forget('color_scheme', $userId);
var_dump(Preferences::get('color_scheme', $userId));
// NULL

// Reset multiple preferences at once:
Preferences::reset(['color_scheme', 'font'], $userId);
var_dump(Preferences::all($userId));
// [
//     'color_scheme' => NULL,
//     'font' => NULL,
// ]
```


## Adding Setting To The UI

Vanilo's Admin base (called [AppShell](https://github.com/artkonekt/appshell))
is prepared for plugging custom settings into it.

Settings are available at the Sidebar -> Settings -> Settings.

```php
$settingsTreeBuilder = $this->app['appshell.settings_tree_builder'];

// To add a new setting to the existing "General" tab, in the existing "Application" box:
$settingsTreeBuilder
    ->addSettingItem('general_app', ['text', ['label' => __('My Setting')]], 'my.setting.key');

// To add a new setting in a new box under the existing "General" tab:
$settingsTreeBuilder
    ->addChildNode('general', 'my_section_box', __('My Section Box'))
    ->addSettingItem('my_section_box', ['text', ['label' => __('My Setting')]], 'my.setting.key');


// To create a new setting under a completely new tab:
$settingsTreeBuilder
    ->addRootNode('mytab', __('My Tab'))
    ->addChildNode('mytab', 'my_section_box', __('My Section Box'))
    ->addSettingItem('my_section_box', ['text', ['label' => __('My Setting')]], 'my.setting.key');
```

The appropriate html will be generated for you and the settings will be
automatically saved when changing their values and hitting the save button.

## Adding Preferences To The UI

> This feature is not implemented yet.

Preferences will be available in the upcoming Profile page under the
User account dropdown menu (dead link atm).

> For more details refer to the [Building UI](https://artkonekt.github.io/gears/#/building-ui)
> section of the Gears Documentation.


