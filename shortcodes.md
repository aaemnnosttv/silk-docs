# Shortcodes

- [Introduction](#introduction)
- [Creating Hello World](#creating-hello-world)
- [Multiple Shortcodes Per-Class](#multiple-shortcodes-per-class)
- [Working with Attributes](#working-with-attributes)


## Introduction

```php
use Silk\Support\Shortcode;
```

The `Shortcode` class provides is a minimal base class which makes it easy to extend for use with your own shortcode.

Each extended Shortcode class can handle one or more shortcodes registered to it.  When the shortcode itself is called, a new instance of the shortcode class is instantiated, all parameters are stored on the instance as properties and the appropriate handler method is called.

## Creating Hello World

A class is overkill for this example of course, but just stick with it.

```php
class HelloWorld extends Shortcode
{
	protected function handler()
	{
		return 'Hello World!';
	}
}
```

Now in our plugin or theme we just need to register the `hello_world` shortcode.

```php
HelloWorld::register('hello_world');
```

That's it!  We registered the `hello_world` shortcode to be handled by the `HelloWorld` class.

## Multiple Shortcodes Per-Class

You may register as many shortcodes for a given class as you wish.

By default, the `handle` method will be called for each registered shortcode, but you may also delegate each to their own method using the `{shortcode}_handler` method naming convention.

> Note: Due to the limitations of characters for method names, it would not be possible to create a dedicated method for shortcodes with hyphens `-` in the name.  If you wished to use multiple shortcodes with hyphens in the name in the same class, you would need to write your own method routing.  Not hard to do since the `$tag` property holds the name of the shortcode which was called.

### Eg. Icon Font Shortcode

```php
class IconFontShortcode extends Shortcode
{
	protected function glyphicon_handler()
	{
		return $this->html('glyphicon');
	}
	
	protected function fontawesome_handler()
	{
		return $this->html('fa');
	}
	
	protected function html($iconset)
	{
		$icon = $this->attributes()->first();
		
		return "<i class='$iconset $iconset-$icon'></i>"
	}
}
```

Bootstrapping

```php
IconFontShortcode::register('glyphicon');
IconFontShortcode::register('fontawesome');
```

Now it makes sense to use two shortcodes in the same class as they share common functionality.

Using these shortcodes would look like this:

```
[glyphicon ice-lolly-tasted]
```

or

```
[fontawesome beer]
```

We've successfully implemented 2 shortcodes in the same class, where they can live somewhere autoloadable in the codebase!  We also have the added convenience of the shortcode's callback parameters stored into `attributes`, `content`, and `tag` properties respectively where we can easily reach them from any method.


## Working with Attributes

Even though the array of attributes passed to the shortcode is stored as a property, the `attributes` method returns all attributes as a [Collection](collections.md) which can greatly enhance the experience of working with them.

For example, a very common practice is merging a set of defaults for all attributes, even if they're just empty values just to be sure that all the keys exist in the array to avoid PHP notices when trying to access them if they're not defined.

Within a Silk shortcode instance, you can simply get the value you need without any extra checks or pre-processing.

```php
$this->attributes()->get('name', 'Frank');
```

Here we want to get the value of the `name` attribute passed to the shortcode.  If it doesn't exist, then just return `Frank`.  No need to worry about if `name` is set or not.  The second (default) parameter is completely optional; it will simply return `null` if `name` is not defined.