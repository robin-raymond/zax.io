
# [Zax Programming Language](index.md)

## Partial Types

The language has [`partial`](https://en.wikipedia.org/wiki/Class_(computer_programming)#Partial) support for types. However, `partial` types are heavily restricted and caution must be used in creating `partial` types as they can inject variables, types, and functions inside existing declared `type` where the original `type` was not expecting any additional values to exist.

The primary motivation for `partial` is to support:
* extending the builtin context `___` to include additional properties
* add `final` friend functions that should be present on other `type` thus extend their usefulness in other scenarios
* extending other context style types for usage as a catch all inside the context

While other use cases are supported, partial has some restrictions that must be adhered:
* `partial` types can intercept construction after the main type is constructed and cannot prevent a type's original constructor from being called
* `partial` types can intercept destruction before the main type is destructed and cannot prevent a type's original destructor from being called
* `partial` types can intercept `final` operators and functions by using the same prototype definition however extreme caution must be taken not to interfere with the arguments
* `partial` types cannot intercept non-final operators and functions but can replace the functions post construction
* `partial` types are constructed in the order they are imported or declared
* `partial` types cannot rely on other `partial` types as having been initialized during the construction process
* `partial` types can add new variables into to a `type` that occupy memory on the `type`

Some important concerns to consider:
* `partial` types may accidentally inject names into types that could cause code misunderstandings
* `partial` types may fatten the memory requirements for types where new variables are injected
* the memory layout of a `type` may change which may cause any union expectations for a `type` and it may cause undefined behaviors
* the `as` casting operator for compatible types may error if an `as` casting destination type is ever extended to remain compatible with the additional `partial` types
* functions calls cannot be intercepted from the base type (other than constructors)
* creating an instance of a `partial` type separate from the main `type` is not possible
* `partial` types may have name conflicts with other `partial` types and the compiler may error

Needless to repeat, use extreme caution with `partial` types. They are not meant to be the go-to tool in a programmer's toolbox.

````zax
MyExtendedContext :: partial Context {
    logCount : Integer

    log final : ()(...) = {
        // ...
        ++logCount
        // ...
    }
}

___.log("new function to extend context")
````
