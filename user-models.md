# User Models

- [Introduction](#introduction)
- [A Basic Model](#a-basic-model)
- [Instantiating a Model](#instantiating-a-model)
	- [Constructor](#constructor)
	- [By ID](#by-id)
	- [By Username](#by-username)
	- [By Email](#by-email)
	- [By Slug](#by-slug)
	- [Current Authenticated User](#current-authenticated-user)
	- [Exceptions](#exceptions)
- [Making Changes in the Database](#making-changes-in-the-database)
	- [Creating a User](#creating-a-user)
	- [Updating a User](#updating-a-user)
	- [Deleting a User](#deleting-a-user)
- [Aliased Properties](#aliased-properties)
- [User Meta](#user-meta)
- [Permalinks](#permalinks)
- [Querying Users](#querying-users)

## Introduction

```php
use Silk\User\Model;
```

A user model is a class which models a single instance of a user.

The `Silk\User\Model` class provides a base to extend from to create your own models.

Silk user models implement an _Active Record_ pattern in that one instance maps to one row in the `wp_users` table.

## A Basic Model

```php
class User extends Silk\User\Model
{
}
```

## Instantiating a Model

Before we can really do anything with it, we need an instance to work with.

Models give you a number of ways to grab an instance.

### Constructor

The simplest way to create a new instance is just to instantiate one.

```php
$user = new User;
```
All properties will be using the defaults of `WP_User`.

You may optionally pass a `WP_User` object, or an array of attributes to fill the model with.

```php
/* @var WP_User $wp_user */
$user =  new User($wp_user);
```
or
```php
$user = new User([
    'user_login' => '...',
    'user_pass' => '...'
]);
```

As an alternative to instantiating with `new ModelClass`, you may also use the `ModelClass::make(...)` method, which accepts the same arguments as the constructor, in the form of a static method.

### By ID

```php
$user = User::fromID(1);
```

### By Username

```php
$user = User::fromUsername('terminator');
```

### By Email

```php
$user = User::fromEmail('arnold@california.us.gov');
```

### By Slug

```php
$user = User::fromSlug('arnold-schwartzenegger');
```
> "slug" in this case being equal to `user_nicename`.


### Current Authenticated User

```php
$user = User::auth();
```

All of the above methods will throw relevant exceptions if a user cannot be found by the given criteria.

### Exceptions

If the desired user is unable to be found or does not exist when trying to create a new instance using any of the named constructor methods, a `ModelNotFoundException` will be thrown.

Likewise, if the `User` model class is attempted to be instantiated with the ID of a `product` user type, a `UserTypeMismatchException` is thrown.

## Making Changes in the Database

### Creating a New User

Using a fresh instance

```php
$user = new User;
$user->user_login = 'terminator;
$user->user_pass = 'illBback84';
$user->save();
```
or
```php
$user = new User([
    'username' => 'sconnor',
    'password' => 'apasswordthatishardtoguess...neener neener neener!'
]);
$user->save();
```
> See [Object Aliases](#object-aliases)

Using `create()`

```php
$user = User::create([
    'user_login' => 'jconnor',
    'user_pass' => 'uphighdownlow2slo'
]);
```

### Updating a User

```php
$user = User::fromID(123);
$user->user_firstname = 'John';
$user->user_lastname = 'Doe';
$user->save();
```

### Deleting a User

Delete the user permanently

```php
$user->delete();
```

## Aliased Properties

The User model comes with a few [object aliases](object-aliases.md) out of the box.
These are properties on the model which translate to a property by a different name on the `WP_User` object.

```
$model->email    => $model->object->user_email
$model->slug     => $model->object->user_nicename
$model->username => $model->object->user_login
$model->password => $model->object->user_pass
```

Aliases may also be used in an array attributes passed in the constructor, `::create()` or `::make()` methods.

## User Meta

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

Get _all_ meta for the user as a [Collection](collections.md) 

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

Delete all user meta for the given key

```php
$model->meta('some-key')->delete();
```

or just a specific value

```php
$model->meta('some-key')->delete('bad value');
```

[For more information see the Meta documentation](meta.md)

## Permalinks

Get the URL for the users's posts

```php
$model->postsUrl();
```

## Querying Users

Queries start with a static method call on the model's class, which creates a new `Silk\Query\Builder` to build a new query with using a fluent api.

The query ends with a call to `results()` to get all of the results as a [Collection](collections.md) of user models.

Queries are performed using `WP_User_Query`, so you can assume that all defaults are consistent with it.

