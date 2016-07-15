# Post Types

- [Introduction](#introduction)
- [Creating a New Instance](#creating-a-new-instance)
- [Working with an Instance](#working-with-an-instance)
	- [Properties](#properties)
	- [Feature Support](#feature-support)
	- [Unregistering the Type](#unregistering-the-type)
- [Registering a New Type](#registering-a-new-type)
	- [Post Type Builder](#post-type-builder)


## Introduction

```php
use Silk\PostType\PostType;
```

The `PostType` class is a gateway to both modeling an existing post type, and registering a new one, but an instance is only for working with an existing type.

## Creating a New Instance

Create a new instance for the `post` post type.

```php
$type = PostType::make('post');
```

## Working with an Instance

A `PostType` instance is for working with an existing registered post type.

### Properties

A helper for accessing the post type's identifier

```php
$type->slug // 'post'
```

A helper for accessing the singular label

```php
$type->one // 'Post'
```

A helper for accessing the plural label

```php
$type->many // 'Posts'
```


### Feature Support

> All post type feature support methods support arguments passed as an array, or a list of one or more features as arguments.

**Checking for feature support**

Returns true only if all listed features are supported, otherwise false.

```php
if ($type->supports('comments','post-formats')) {
	//
}
```

**Add support for one or more features**

```php
$type->addSupportFor('dogs','cats');
```

**Remove support for one or more features**

```php
$type->removeSupportFor(['title','custom-fields']);
```

### Unregistering the Type

Unregister the instance's post type.

```php
$type->unregister();
```


## Registering a New Type

If the post type identifier passed to `PostType::make()` does not exist as a registered type, then a new `PostTypeBuilder` is returned which provides a fluent api for building up a new post type to register.

```php
$type = PostType::make('my_new_custom')
	// ->
	// ...
	->register();
```

The `register` method returns a new `PostType` instance for the newly registered type.

Jump to [Post Type Builder &rarr;](post-type-builder.md)
