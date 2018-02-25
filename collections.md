# Collections

Silk leverages the power of Collections in many places favor of plain arrays.

Currently the only dependency of the codebase, Silk leverages the `Illuminate\Support\Collection` class as part of the [`tightenco/collect`](https://github.com/tightenco/collect) package. As of v0.12, Silk uses a namespaced class `Silk\Support\Collection` which simply extends the class provided by the package for backwards compatiblity. In the future, Silk will likely change to a fully namespaced version of the library rather than relying on a 3rd-party dependency.

For comprehensive examples and documentation on using Collections, see the [Laravel documentation](https://laravel.com/docs/5.4/collections).
