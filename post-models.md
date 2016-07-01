# Post Models

- [Introduction](#introduction)
- [Strict Typing](#strict-typing)
- [The Modeled Type](#the-modeled-type)
- [A Basic Model](#a-basic-model)
- [Instantiating a Model](#instantiating-a-model)
	- [Constructor](#constructor)
	- [By ID](#by-id)
	- [By Slug](#by-slug)
	- [By Object](#by-object)
	- [Global `$post`](#global-post)
	- [Exceptions](#exceptions)

## Introduction

A post model is a class which models a single instance of a post of a particular post type.

The `Silk\Post\Model` class provides a common base to extend from to create your own models for any post type.

Silk post models implement an _Active Record_ pattern in that one instance maps to one row in the `wp_posts` table.

## Strict Typing

Unlike the `WP_Post`, a post model can only represent a post of the same type that it models.  If you have a `Product` model which is for the `product` post type, you can rest assured that if you're working with a `Product` instance, that it can only be for an existing or new post of that type.

## The Modeled Type

A post model can resolve it's post type in a number of ways:

- Explicitly defining it with a class constant
- Using the `ClassNameAsPostType` Trait
- Inheritance
- Overriding the `postTypeId()` method and rolling your own

## A Basic Model

```php
class Event extends Silk\Post\Model
{
	const POST_TYPE = 'event';
}
```

A model for an `event` post_type post.

## Instantiating a Model

Before we can really do anything with it, we need an instance to work with.

Models give you a number of ways to grab an instance.

### Constructor

The simplest way to create a new instance is just to instantiate one with no arguments.

```php
$event = new Event;
```
All properties are using the defaults of `WP_Post`, with the exception of the `post_type` of course, which is set to `event` in this case.

### By ID

```php
$event = Event::fromID(1);
```

### By Slug

```php
$event = Event::fromSlug('new-years-eve');
```
> "slug" in this case being equal to `post_name` for an `event` post type.

### By Object

```php
/* @var WP_Post $post */
$post = get_post(1214);

$event = Event::fromWpPost($post);
```

### Global `$post`

```php
$event = Event::fromGlobal();
```

All of the above methods will throw relevant exceptions if a post cannot be found by the given criteria.

### Exceptions

If the desired post is unable to be found or does not exist when trying to create a new instance using any of the named constructor methods, a `ModelNotFoundException` will be thrown.

Likewise, if the `Event` model class is attempted to be instantiated with the ID of a `product` post type, a `PostTypeMismatchException` is thrown.