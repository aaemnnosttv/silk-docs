# Object Aliases

- [Introduction](#introduction)
- [Extending](#extending)
- [Considerations](#considerations)

## Introduction

Object aliases are an attribute of all Silk models. Essentially, an alias maps a property or attribute name on the model, to a property on the core WordPress object it wraps.

Eg:
```php
use Silk\User\Model as User;

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

Properties support aliases as well

```php
$wp_user = new WP_User(1);
$model = User::fromID(1);

$model->email === $wp_user->user_email; // true
```

It's a little bit of syntactical sugar but this allows you to use the properties you want, without needing to change the database schema, which is fixed.

## Extending

Object aliases are stored on the model's `$objectAliases` property as an array of `aliasName => objectProperty`.
To change or add your own, the easiest way would be to extend the class and override the property like so:

```php
class Admin extends Silk\User\Model
{
    protected $objectAliases = [
        'emailAddress' => 'user_email',
            // ... etc
    ];
}
```

## Considerations

Object aliases are guaranteed to work in the constructor of a model, or in the `Model::create()` method, as aliases are expanded during the filling of properties.

However, when using them as properties you should be aware of any possible conflicts with any other public properties your model may have, as aliases leverage magic getters/setters internally.

