# Installation

## Requirements

### PHP 5.5+

Silk is fundamentally designed to be a modern codebase.  That means we need to be able to use modern features of the language.
Therefore, the minimum supported version of PHP will closely follow the official [minimum supported version](http://php.net/supported-versions.php) of PHP by The PHP Group.  This currently includes all versions which are not EOL, but in the future it may be limited to actively supported versions only.

Silk is also tested for compatibility with HHVM.

### WordPress

Silk is developed with and constantly tested against the current stable version of WordPress.  It may or may not work with older versions, but due to the WordPress organization's commitment to backwards compatibility, there's a very good chance that it will work with older versions as well (within reason).

## Installation Methods

### Via Composer (Recommended)

```
composer require silk/silk:^0.9
```

**Manual Installation**  
If you're not using a Composer-based WordPress project, you will need to require the Composer autoload file manually, or use the plugin.

```php
// eg: loading Composer within a theme functions.php
require_once(__DIR__ . '/vendor/autoload.php');
```

## WordPress Plugin
The WordPress.org plugin repository is not currently accepting library/framework type plugin requests.  

See the [silk-plugin repository](https://github.com/aaemnnosttv/silk-plugin) for instructions on how to install and recommended usage.
