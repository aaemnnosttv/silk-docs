# Taxonomy Builder

- [Introduction](#introduction)
- [Creating a New Instance](#creating-a-new-instance)
- [Working with an Instance](#working-with-an-instance)
  - [Object Types](#object-types)
  - [Setting Labels](#setting-labels)
  - [Setting Arbitrary Arguments](#setting-arbitrary-arguments)
  - [Registering the Taxonomy](#registering-the-taxonomy)


## Introduction

```php
use Silk\Taxonomy\Builder;
```

The `Taxonomy\Builder` class exposes a fluent api for building up, and or manipulating an array of arguments for registering a new taxonomy.

The builder imposes no defaults of its own.  You can assume all defaults are consistent with [`register_taxonomy()`](https://developer.wordpress.org/reference/functions/register_taxonomy/).

## Creating a New Instance

```php
$builder = new Builder('my_type', $optional_args);
```

Passing the new taxonomy identifier to the constructor is the minimum needed to construct a new instance, but you may optionally pass an array of arguments.

Alternatively, there is a named constructor.

```php
$builder = Builder::make('my_type');
```

> Note: the `make` method only accepts the taxonomy identifier.


## Working with an Instance

### Object Types

Set the object types that this taxonomy relates to (important).

```php
$builder->forTypes('post', 'page', 'some_custom');
```

> Note: Types may also be passed as a single array.


### Setting Labels

Set the singular label

```php
$builder->oneIs('Genre');
```

Set the plural label

```php
$builder->manyAre('Genres');
```

> Note: The label methods set default values for all standard taxonomy labels where their respective forms are referenced.

Set a single label

```php
$builder->setLabel('archives', 'Read all the things!');
```

### Setting Arbitrary Arguments

Set any key in the array of taxonomy registration arguments

```php
$builder->set('rewrite', ['slug' => 'g']);
```

### Registering the Taxonomy

The final call in the process of building up a new taxonomy to register is the `register` method!

```php
$taxonomy = $builder->register();
``` 

The register method returns a new `Taxonomy` object for the newly registered taxonomy.

Jump to [Taxonomies &rarr;](taxonomies.md)
