# Object Aliases

- [Introduction](#introduction)
- [Extending](#extending)
- [Considerations](#considerations)

## Introduction

Object aliases are an attribute of all Silk models, as well as types (`PostType` and `Taxonomy`). Essentially, an alias maps a property or attribute name on the model, to a property on the core WordPress object it wraps.

Aliases are defined by the classes `objectAliases()` method, which returns an array of `aliasName => objectProperty` mappings.  Simply define the aliases you want, and that's it!

Eg:
```php
class User extends Silk\User\Model
{
    protected function objectAliases()
    {
        return [
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

To change or add your own, override the `objectAliases` method in your extend class and manipulate the return as desired.
If you're extending a class that already has some aliases defined, the extended class will inherit those, but you could also add to that by merging in any extras you want.

```php
class Admin extends Silk\User\Model
{
    protected function objectAliases()
    {
        $existing = parent::objectAliases();
        $extra = [
            'emailAddress' => 'user_email',
            // ... etc
        ];
        
        return array_merge($existing, $extra);
    }
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

> Note: Shorthand syntax will only take effect for aliases which resolve to _existing_ properties on the aliased object.  It will not proxy `xyz` to `post_xyz`, unless the `post_xyz` property exists on the model's `WP_Post` object. 

## Considerations

Object aliases are guaranteed to work in the constructor of a model, or in the `Model::create()` method, as aliases are expanded during the filling of properties.

However, when using aliases _as properties_ you should be aware of any possible conflicts with any other public properties your model may have, as the aliases leverage magic getters/setters internally.
A public property by the same name on the model would prevent the magic get/set methods from being invoked, which may even be desired, but could also be frustrating when debugging.
