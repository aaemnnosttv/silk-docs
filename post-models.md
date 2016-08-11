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
- [Making Changes in the Database](#making-changes-in-the-database)
	- [Creating a Post](#creating-a-post)
	- [Updating a Post](#updating-a-post)
	- [Deleting a Post](#deleting-a-post)
- [Post Meta](#post-meta)
- [Permalinks](#permalinks)
- [Querying Posts](#querying-posts)
	- [Get All Posts of the Model's Type](#get-all-posts-of-the-models-type)
	- [Query by Status](#query-by-status)
	- [Set Any Arbitrary Query Argument](#set-any-arbitrary-query-argument)
	- [Limit the Results](#limit-the-results)

## Introduction

```php
use Silk\Post\Model;
```

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

The simplest way to create a new instance is just to instantiate one.

```php
$event = new Event;
```
All properties will be using the defaults of `WP_Post`, with the exception of the `post_type` of course, which is set to `event` in this case.

You may optionally pass a `WP_Post` object, or an array of attributes to fill the model with.

```php
/* @var WP_Post $eventPost */
$event =  new Event($eventPost);
```
or
```php
$event = new Event(['post_title' => '...']);
```

As an alternative to instantiating with `new ModelClass`, you may also use the `ModelClass::make(...)` method, which accepts the same arguments as the constructor, in the form of a static method.

### By ID

```php
$event = Event::fromID(1);
```

### By Slug

```php
$event = Event::fromSlug('new-years-eve');
```
> "slug" in this case being equal to `post_name` for an `event` post type.


### Global `$post`

```php
$event = Event::fromGlobal();
```

All of the above methods will throw relevant exceptions if a post cannot be found by the given criteria.

### Exceptions

If the desired post is unable to be found or does not exist when trying to create a new instance using any of the named constructor methods, a `ModelNotFoundException` will be thrown.

Likewise, if the `Event` model class is attempted to be instantiated with the ID of a `product` post type, a `PostTypeMismatchException` is thrown.

## Making Changes in the Database

### Creating a New Post

Using a fresh instance

```php
$event = new Event;
$event->post_title = "House Party";
$event->save();
```

Using `create()`

```php
$event = Event::create(['post_title' => 'House Party']);
```

### Updating a Post

```php
$event = Event::fromID(123);
$event->post_name = 'new-slug';
$event->save();
```

### Deleting a Post

Trash the post

```php
$event->trash();
```

Bring it back...

```php
$event->untrash();
```

Or delete it permanently

```php
$event->delete();
```

## Post Meta

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

Get _all_ meta for the post as a [Collection](collections.md) 

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

Delete all post meta for the given key

```php
$model->meta('some-key')->delete();
```

or just a specific value

```php
$model->meta('some-key')->delete('new value');
```

## Permalinks

Get the permalink URL for the post

```php
$model->url();
```

## Querying Posts

Queries start with a static method call on the model's class, which creates a new `Silk\Query\Builder` to build a new query with using a fluent api.

The query ends with a call to `results()` to get all of the results as a [Collection](collections.md) of post models.

Queries are performed using `WP_Query`, so you can assume that all defaults are consistent with it.

### Get All Posts of the Model's Type

```php
Event::all()->results();
```

### Query by Status

```php
Event::whereStatus('publish')->results();
```

### Set Any Arbitrary Query Argument

```php
Event::query()
	->set('category_name', 'free')
	->set('offset', 20)
	->results();
```

### Limit the Results

Return a maximum of 50 posts.

```php
Event::query()->limit(50)->results();
```

--

More to come.
