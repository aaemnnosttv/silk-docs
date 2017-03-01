# Hooks

- [Introduction](#introduction)
- [Creating a New Instance](#creating-a-new-instance)
- [Registering a Callback](#registering-a-callback)
  - [Setting the Callback](#setting-the-callback)
  - [Setting the Priority](#setting-the-priority)
  - [Removing the Callback]()
- [Simplify with the `on()` Function](#simplify-with-the-on-function)
- [Never Again; Accepted Number of Arguments](#never-again-accepted-number-of-arguments)
- [More Power: Mediating the Callback](#more-power-mediating-the-callback)
  - [Limiting Invocation](#limiting-invocation)
  - [Conditional Invocation](#conditional-invocation)
  - [Automatic Return](#automatic-return)
- [Remove on Steroids with `off()`](#remove-on-steroids-with-off)  


## Introduction

By "hook" we are referring to an "action" or a "filter" in WordPress.  Since [actions are registered as filters](https://developer.wordpress.org/reference/functions/add_action/#source-code), Silk does not distinguish between them either; hence there is currently only one class for a `Hook`.

The `Silk\Event\Hook` class exposes a fluent api for working with a single hook.

## Creating a New Instance

You can create a new `Hook` instance by instantiating the class with as little as only the hook handle:

```php
use Silk\Event\Hook;

$hook = new Hook('init');
```

Or there is also a named constructor which takes the same parameters as the constructor:

```php
$hook = Hook::on('init');
```

Now we have an instance that we can register a callback on.  The callback is the function/method that you want WordPress to call for the action/filter we are hooking on to.

## Registering a Callback

Registering a callback with Silk is very similar to the way you would with `add_action` or `add_filter`.  The hook name or "tag" that we want to register a callback for we will refer to as the "handle".

### Setting the Callback

To set the callback for a hook, we use the `setCallback` method:

```php
$hook->setCallback('some_function');
```

Because the Hook class uses a fluent api, we can chain these together.

```php
Hook::on('init')->setCallback('some_function');
```

The callback provided here is just a global function, but it can be any valid `callable` syntax, including an anonymous function:

```php
Hook::on('init')->setCallback(function () {
    // do what must be done
});
```

### Setting the Priority

Setting the priority can be done when instantiating, or set/changed later using the `withPriorty` method

```php
Hook::on('init', 1); // early!
Hook::on('init')->withPriority(100); // late
```

At this point, WordPress does not yet know about the callback, it's all still within the hook instance.

To complete the registration of the hook, we need to tell WordPress to `listen` for it.

```php
Hook::on('init')->setCallback('some_function')->listen();
```

This will add the hook in WordPress, on the `init` action handle, with a priority of `10` (WordPress default), which will call the `some_function` global function.

### Removing the Callback

Unsetting or removing the callback in WordPress (essentially undoing the effects of `listen()`) is easy.

```php
$hook->remove();
```

## Simplify with the `on()` Function

Silk exposes a global `on()` helper function for creating a new hook and fully registering it, with a syntax you already know.

```php
on('init', 'some_callable');
on('init', 'some_callable', $optional_priority);
```

The function is just a convenient wrapper for the above, and it still returns the `Hook` instance so you can continue to chain off it or store the reference for later.

## Never again; Accepted number of arguments

Forget about needing to pass in that annoying fourth parameter to `add_action` or `add_filter`.  Silk Hooks are smart enough to know how many your callback expects.

**Eg: [Filtering Post Thumbnail HTML](https://developer.wordpress.org/reference/hooks/post_thumbnail_html/)**

```php
on('post_thumbnail_html', function ($html, $post_id, $post_thumbnail_id, $size, $attr) {
    $html = "<!-- thumbnail $post_thumbnail_id for post $post_id - size: $size --> $html";
    
    return $html;
});
```

## More Power: Mediating the Callback

So far things are looking good.  We've achieved a simpler, more flexible syntax while adding all the power we get with having an instance of the Hook object.

Because our Hook instance is in control of the callback we gave it, it gives us the power to do things that were not as easy before.


### Limiting Invocation

Sometimes we need to limit the number of times that our action or filter callback is executed.  Perhaps there is an expensive operation we only want to perform once every pageload, or a particular transformation our filter performs, but it should only do X number of times.

```php
$hook->onlyXtimes($number_of_times_to_allow);
```

For limiting the invocation to only a single call

```php
$hook->once();
```

The hook instance keeps track of how many times it's callback has been invoked, and once that number has been exceeded, prevents further invocation, without actually removing the hook!

The same thing is possible to do manually as well
```php
$hook->bypass(); // Callback will not be invoked again.
```

### Conditional Invocation

Using conditionals within hook callbacks is extremely common due to the fact that many actions/filters are fired at different times, during different requests, etc.  The need to qualify that certain conditions are met before executing the main body of your callback becomes very important.

Hooks now allow you to register one or more callbacks which define conditions that must be met before the hook's callback will be invoked.  Conditions can be added any time, and are evaluated just before the callback would be invoked.

The main benefit is that it allows you to separate conditional logic from the main business logic, as all of the conditional logic, can be moved into one or more conditional callbacks.  _This also makes both callback and conditional functions more reusable._

Conditions are specific to a Hook instance, and are added using the `->onlyIf(...)` method.

```php
Hook::on('action_or_filter', 'hook_callback')->onlyIf('some_callable');
```

The conditional callback will receive all arguments passed by the action/filter, the same as the hook callback itself.

See how we might write a hook to set the query to return all results, **only if** it is the main query **and** the request is for the front page:

```php
on('pre_get_posts', function (WP_Query $query) {
    $query->set('posts_per_page', -1);
})->onlyIf(function (WP_Query $query) {
    return $query->is_main_query();
})->onlyIf('is_front_page');
```

Now, imagine if we had dedicated (reusable) functions for performing this logic for us.

```php
function query_set_unlimited_results(WP_Query $query)
{
    return $query->set('posts_per_page', -1);
}

function query_is_main_query(WP_Query $query)
{
    return $query->is_main_query();
}
```

Now we could rewrite our example as such:

```php
on('pre_get_posts', 'query_set_unlimited_results')
    ->onlyIf('query_is_main_query')
    ->onlyIf('is_front_page');
```

There's no question what the above code does, and each component is now a handy building block which can be used to quickly compose powerful actions with a single line of code.

### Automatic Return

Hooks now automatically return the first argument passed to the callback if nothing is returned by it.

> Note: Unfortunately there is no way to reliably detect whether a function returns nothing (void) or null, as PHP returns null when no return is made.
Ideally, it might be better to throw an exception in the case of no return made for a filter callback, but automatic return is a nice second best.

This protects a filtered value from being wiped out if nothing is returned, and also enables the ability to use any filter as an action.
Sometimes it can be useful to hook onto a particular filter for the sake of timing, rather than manipulating its value.
We already know that both filters and actions are essentially all filters under the hood, the difference is made by the invoking function (`do_action` or `apply_filters`).


## Remove on Steroids with `off()`

As you might expect, the global `off()` function, is a helper function for removing an action or filter.
It also follows the same syntax as `remove_action` and `remove_filter`.

```php
off('init', 'some_function', $optional_priority);
```

Timing is a bit of a doozy sometimes when trying to remove hooks, often those added by 3rd-parties.

If the callback has not yet been registered on the given handle, Silk will attempt to remove it just-in-time before it is set to fire.

Consider the following example of some hypothetical 3rd-party plugin code, where you need to remove their hooked callback 'my_late_init'.

```php
// some plugin code somewhere
add_action('init', function () {
    if (some_condition()) {
        add_action('init', 'their_late_init', 20);
    }
});
```

Not impossible by any means, but the limited window for removing it may be problematic.

With `off()` you can simply remove it anytime beforehand, registered or not.

```php
// my plugin is loading
off('init', 'their_late_init', 20);
```

With that said, if the callback to remove is set at call time, it will be removed immediately, no magic necessary.
