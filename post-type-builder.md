# Post Type Builder

- [Introduction](#introduction)
- [Creating a New Instance](#creating-a-new-instance)
- [Working with an Instance](#working-with-an-instance)
	- [Setting the Public Visibility](#setting-the-public-visibility)
	- [Configuring the Admin Interface](#configuring-the-admin-interface)
	- [Declaring Feature Support](#declaring-feature-support)
	- [Setting Labels](#setting-labels)
	- [Setting Arbitrary Arguments](#setting-arbitrary-arguments)
	- [Registering the Type](#register-the-type)

## Introduction

```php
use Silk\Post\PostTypeBuilder;
```

The `PostTypeBuilder` class exposes a fluent api for building up, and or manipulating an array of arguments for registering a new post type.

The builder imposes no defaults of its own.  You can assume all defaults are consistent with [`register_post_type()`](https://developer.wordpress.org/reference/functions/register_post_type/).

## Creating a New Instance

```php
$builder = new PostTypeBuilder('my_type', $optional_args);
```

Passing the new post type identifier to the constructor is the minimum needed to construct a new instance, but you may optionally pass an array of arguments (similar to `register_post_type`).

Alternatively, there is a named constructor.

```php
$builder = PostTypeBuilder::make('my_type');
```

> Note: the `make` method only accepts the post type identifier.


## Working with an Instance

### Setting the Public Visibility

Set the type as publicly available

```php
$builder->open()
```

Set the type as non-public

```php
$builder->closed()
```

### Configuring the Admin Interface

Enable admin UI

```php
$builder->withUI()
```

Disable admin UI

```php
$builder->noUI()
```

### Declaring Feature Support

Specify what features the type supports, either as an array, or a list of features as arguments.

```php
$builder->supports('title', 'editor', 'thumbnail')
```

### Setting Labels

Set the singular label

```php
$builder->oneIs('Book')
```

Set the plural label

```php
$builder->manyAre('Books')
```

> Note: The label methods currently only set the individual labels, not every other label that uses or references one of these forms.  This will be enhanced in a future release.

### Setting Arbitrary Arguments

Set any key in the array of post type registration arguments

```php
$builder->set('can_export', false)
```

### Registering the Type

The final call in the process of building up a new type to register is the `register` method!

```php
$type = $builder->register();
``` 

The register method returns a new `PostType` object for the newly registered type.

Jump to [PostType &rarr;](post-types.md)