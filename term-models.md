# Term Models

- [Introduction](#introduction)
- [Strict Typing](#strict-typing)
- [The Modeled Term](#the-modeled-term)
- [A Basic Model](#a-basic-model)
- [Instantiating a Model](#instantiating-a-model)
    - [Constructor](#constructor)
    - [By ID](#by-id)
    - [By Slug](#by-slug)
    - [Exceptions](#exceptions)
- [Checking for Existence](#checking-for-existence)
- [Hierarchy](#hierarchy)
- [Making Changes in the Database](#making-changes-in-the-database)
    - [Creating a Term](#creating-a-term)
    - [Updating a Term](#updating-a-term)
    - [Deleting a Term](#deleting-a-term)
- [Term Meta](#term-meta)
- [Permalinks](#permalinks)
- [Querying Terms](#querying-terms)
    - [Get All Terms of the Model's Type](#get-all-terms-of-the-models-type)
    - [Include Empty Terms](#include-empty-terms)
    - [Set Any Arbitrary Query Argument](#set-any-arbitrary-query-argument)
    - [Limit the Results](#limit-the-results)

## Introduction

```php
use Silk\Term\Model;
```

A term model is a class which models a single instance of a term of a particular taxonomy.

The `Silk\Term\Model` class provides a common base to extend from to create your own models for any taxonomy.

Silk term models implement an _Active Record_ pattern in that one instance maps to one row in the `wp_terms` table (terms are a little more complicated than that.. but basically the same).

## Strict Typing

Unlike the `WP_Term`, a term model can only represent a term of the same taxonomy that it models.  If you have a `ProductCategory` model which is for the `product_category` taxonomy, you can rest assured that if you're working with a `ProductCategory` instance, that it can only be for an existing or new term of that taxonomy.

## The Modeled Term

A term model can resolve its taxonomy in a number of ways:

- Explicitly defining it with a class constant `ProductCategory::TAXONOMY`
- Inheritance

## A Basic Model

```php
class EventType extends Silk\Term\Model
{
    const TAXONOMY = 'event_type';
}
```

A model for an `event_type` taxonomy term.

## Instantiating a Model

Before we can really do anything with it, we need an instance to work with.

Models give you a number of ways to grab an instance.

### Constructor

The simplest way to create a new instance is just to instantiate one.

```php
$eventType = new EventType;
```

All properties are using the defaults of `WP_Term`, with the exception of the `taxonomy` of course, which is set to `event_type` in this case.

You may optionally pass a `WP_Term` object, or an array of attributes to fill the model with.

```php
/* @var WP_Term $eventType */
$eventType =  new EventType($eventType);
``` 
or
```php
$eventType = new EventType(['name' => '...']);
```

As an alternative to instantiating with `new ModelClass`, you may also use the `ModelClass::make(...)` method, which accepts the same arguments as the constructor, in the form of a static method which returns the instance.


### By ID

```php
$eventType = EventType::fromID(1);
```

### By Slug

```php
$eventType = EventType::fromSlug('holiday');
```

> From here on, we will refer to `$eventType` as an instance variable for a term model, but the variable name could of course be anything.

All of the above methods will throw relevant exceptions if a term cannot be found by the given criteria, or if the taxonomy does not exist.

### Exceptions

If the desired term is unable to be found or does not exist when trying to create a new instance using any of the named constructor methods, a `TermNotFoundException` will be thrown.

Likewise, if the `EventType` model class is attempted to be instantiated with the ID of a `event_tag` taxonomy, a `TaxonomyMismatchException` is thrown.

## Checking for Existence

Does the term exist in the database?

```php
$eventType->exists();
```

## Hierarchy

Does the term exist as a child of a parent term?

```php
$eventType->isChildOf($parent);
```
> Where `$parent` is the term model/object/id

Getting the parent term model

```php
$eventType->parent();
```

Get all ancestors of the term as a [Collection](collections.md) of term models

```php
$eventType->ancestors();
```

## Making Changes in the Database

### Creating a Term

Using a fresh instance

```php
$eventType = new EventType;
$eventType->name = "Anniversary";
$eventType->save();
```

Using `create()`

```php
$eventType = EventType::create(['name' => 'Pool Party']);
```

### Updating a Term

```php
$eventType = EventType::fromID(123);
$eventType->slug = 'new-slug';
$eventType->save();
```

### Deleting a Term

```php
$eventType->delete();
```

## Term Meta

Get a single value

```php
$model->meta_key
```

or

```php
$model->meta('some-key')->get();
```

Get all values for the given key as an array

```php
$model->meta('some-key')->all();
```

Get _all_ meta for the term as a [Collection](collections.md) 

```php
$model->meta()->collect();
```

Set a meta value for a given key

```php
$model->meta('some-key')->set('new value');
```

Add a meta value for a given key

```php
$model->meta('some-key')->add('another value');
```

Check if meta exists for the given key

```php
if ($model->meta('some-key')->exists()) {
    //
}
```

Delete all term meta for the given key

```php
$model->meta('some-key')->delete();
```

or just a specific value

```php
$model->meta('some-key')->delete('bad value');
```

## Permalinks

Get the permalink URL for the term's archive

```php
$model->url();
```

## Querying Terms

Queries start with a static method call on the model's class, which creates a new `Silk\Term\QueryBuilder` to build a new query with using a fluent api.

The query ends with a call to `results()` to get all of the results as a [Collection](collections.md) of term models of the same type as the class initializing the query.

Queries are performed using `get_terms`, so you can assume that all defaults are consistent with it.

### Get All Terms of the Model's Type

```php
EventType::all()->results();
```

### Include Empty Terms

```php
EventType::query()->includeEmpty()->results();
```

### Set Any Arbitrary Query Argument

```php
EventType::query()
    ->set('name__like', '% Party')
    ->set('offset', 20)
    ->results();
```

### Limit the Results

Return a maximum of 50 terms.

```php
EventType::query()->limit(50)->results();
```
