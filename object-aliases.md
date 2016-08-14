# Object Aliases

- [Introduction](#introduction)
- [Extending](#extending)
- [Considerations](#considerations)

## Introduction

Object aliases are an attribute of all Silk models. Essentially, an alias maps a property or attribute name on the model, to a property on the core WordPress object it wraps.

Aliases are stored on the model's `$objectAliases` property as an array of `aliasName => objectProperty`.  Simply define the aliases you want, and that's it!

Eg:
```php
class User extends Silk\User\Model
{
    protected $objectAliases = [
        'username' => 'user_login',
        'password' => 'user_pass'
    ];
}

new User([
    'username' => 'admin',
    'password' => 'secret'
]);

// is the same as :

new User([
    'user_login' => 'admin',
    'user_pass' => 'secret'
]);
```

Properties support aliases as well* ([See considerations](#considerations) below)

```php
$wp_user = new WP_User(1);
$model = User::fromID(1);

$model->username === $wp_user->user_login; // true
```

It's a little bit of syntactical sugar but this allows you to use the properties you want, without needing to change the database schema, which is fixed.

## Extending

To change or add your own, extend the class and set the property like so:

```php
class Admin extends Silk\User\Model
{
    protected $objectAliases = [
        'emailAddress' => 'user_email',
            // ... etc
    ];
}
```

## Shorthand Properties

Shorthand Properties are a set of automatic object aliases which allow you to omit the object/entity type from properties which begin with it.

To use it, simply use the trait on your model.

```php
class Book extends Silk\Post\Model
{
    use Silk\Type\ShorthandProperties;
    
    const POST_TYPE = 'book';
}
```

Now using the extended class, you may use shorthand properties just as you would any other object alias like so:

```php
new Book([
    'title'   => 'Moby Dick',
    'content' => 'Call me Ishmael. Some years ago—never mind how long precisely—having little or no money in my purse, and nothing particular to interest me on shore ...',
    'excerpt' => 'Sailor Ishmael tells the story of the obsessive quest of Ahab, captain of the whaler the Pequod, for revenge on Moby Dick, the white whale which on the previous whaling voyage destroyed his ship and severed his leg at the knee.'
]);
```

Here we used `title` as a shorthand to `post_title`, `content` to `post_content`, and `excerpt` to `post_excerpt`.

Shorthand properties lets us omit the `post_` prefix on post models, `user_` prefix on user models, etc.

## Considerations

Object aliases are guaranteed to work in the constructor of a model, or in the `Model::create()` method, as aliases are expanded during the filling of properties.

However, when using them _as properties_ you should be aware of any possible conflicts with any other public properties your model may have, as aliases leverage magic getters/setters internally.
A public property by the same name on the model would prevent the magic get/set happening, which could be desired, but could also be frustrating when debugging. 

