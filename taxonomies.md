# Taxonomies

- [Introduction](#introduction)
- [Creating a New Instance](#creating-a-new-instance)
- [Working with an Instance](#working-with-an-instance)
	- [Properties](#properties)
	- [Related Objects](#related-objects)
	- [Unregistering the Taxonomy](#unregistering-the-taxonomy)
- [Registering a Taxonomy](#registering-a-new-taxonomy)


## Introduction

```php
use Silk\Taxonomy\Taxonomy;
```

The `Taxonomy` class is a gateway to both modeling an existing taxonomy, and registering a new one, but an instance is only for working with an existing taxonomy.

## Creating a New Instance

Create a new instance for the `category` taxonomy.

```php
$taxonomy = Taxonomy::make('category');
```

## Working with an Instance

A `Taxonomy` instance is for working with a single existing registered taxonomy.

### Properties

A helper for accessing the taxonomy's identifier

```php
$taxonomy->id   // 'category'
$taxonomy->slug // 'category'
```

A helper for accessing the singular label

```php
$taxonomy->one // 'Category'
```

A helper for accessing the plural label

```php
$taxonomy->many // 'Categories'
```

### Unregistering the Taxonomy

Unregister the instance's taxonomy.

```php
$taxonomy->unregister();
```

## Related Objects

Taxonomy objects make it easy to traverse to it's related objects.

Get all terms for this taxonomy as a [Collection](collections.md).

```php
$terms = $taxonomy->terms()->results();
```

> Note: the `terms()` method returns a `Term\QueryBuilder`, so further methods for modifiying the final set could be called before calling `results()`

Get all post types related to this taxonomy as a [Collection](collections.md) of `Silk\PostType\PostType` objects.

```php
$types = $taxonomy->postTypes();
```

## Registering a New Taxonomy

If the taxonomy identifier passed to `Taxonomy::make()` does not exist as a registered taxonomy, then a new `Taxonomy\Builder` is returned which provides a fluent api for building up a new taxonomy to register.

```php
$taxonomy = Taxonomy::make('my_new_custom')
	// ->
	// ...
	->register();
```

The `register` method returns a new `Taxonomy` instance for the newly registered taxonomy.

Jump to [Taxonomy Builder &rarr;](taxonomy-builder.md)
