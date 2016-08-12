# Meta

- [Introduction](#introduction)
- [Object Meta](#object-meta)
- [Meta](#meta)

## Introduction

The Meta API in WordPress is a simple implementation of an [Entity Attribute Value (EAV)](https://en.wikipedia.org/wiki/Entity%E2%80%93attribute%E2%80%93value_model) model.

Because the database schema for the various entities in WordPress (posts, terms, users..) is not intended to be changed, the Meta API allows one to associate arbitrary additional data to a particular entity (eg: post object).
Internally, WordPress has dedicated tables for all the metadata belonging to these entities.

Silk models all expose the same API for working with meta for all entities using the `->meta()` method.
As such, this section applies to all kinds of meta.

## Object Meta

```php
use Silk\Meta\ObjectMeta;
```

More often than not, the `ObjectMeta` instance will be retrieved from a model, using the `->meta()` method.
 
> Note: The model's `meta()` method also accepts a meta key as a parameter, in which case it returns a `Meta` object instead.

`ObjectMeta` can also be instantiated by passing the object type (entity name), and the object ID.

```php
$meta = new ObjectMeta('user', 1);
```

### Methods

Get a `Meta` object for the given key ([see `Meta` below](#meta))

```php
$meta = $objectMeta->get('some-key');
```

Set the value for a given meta key (fluent).

```php
$objectMeta->set('key', 'value');
```
or
```php
$objectMeta
    ->set('one'  , 'I')
    ->set('two'  , 'II')
    ->set('three', 'III');
```

Get all meta for the entity instance as a [Collection](collections.md) of `Meta` objects

```php
$collection = $objectMeta->collect();
```

Get all metadata for the entity instance as an array

```php
$allMeta = $objectMeta->toArray();
```

## Meta

```
use Silk\Meta\Meta;
```

The `Meta` class represents a single metadata value for a given entity and key.  By single value, meaning all metadata for the specific entity's key.
WordPress supports multiple values for the same key, although it's most commonly used to interact with a single value.

> Note: A key difference between `Meta` and other models (eg. Post, User), is that there is no `save()` method.
Methods for `set` and other value-manipulation actions persist the change at the time of call. 

### Methods

Get the single value.  For keys which have multiple values, this will return the first.

```php
$meta->get();
```

Get all values an array (ie: `$single = false`)

```php
$meta->all();
```

Get all values as a [Collection](collections.md) 

```php
$meta->collect();
```

Add a value

```php
$meta->add('a new or additional value');
```

Check if any meta exists

```php
if ($meta->exists()) {
	//
}
```

Set the value

```php
$meta->set('new value');
```

> Note: If the key has multiple values, calling `set` will not reduce them to a single value, but change them all to be the same new value.
This may be unintended.  However, if reducing to a single value is the desired result, simply call `->delete()` first and then `->set($newValue)`. 

Replace a value, for metadata with multiple values.

```php
$meta->replace('old value', 'new value');
```

Delete all post meta for the given key

```php
$meta->delete();
```

or just a specific value

```php
$meta->delete('bad value');
```