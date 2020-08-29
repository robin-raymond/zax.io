
# [Zax Programming Language](index.md)

## Partial Types

The language has [`partial`](https://en.wikipedia.org/wiki/Class_(computer_programming)#Partial) support for types. However, partial types are heavily restricted and caution must be used in creating `parial` types as they can inject variables, types, and functions inside existing declared types.

The primary motivation for `partial` is to support:
* extending the builtin context `___` to include additional properties
* add friend functions that should be present on other types to extend their usefulness in other scenarios

While other use cases are supported, partial has some restrictions that must be adhered:
* `partial` types can only intercept normally defaulted copy constructors, or all other constructors with the empty constructor
* `partial` types can intercept normal default assignment operators after the main type
* `partial` types are constructed in the order they are imported or declared
* `partial` types cannot rely on other partial types that have not been declared (yet) during the construction process
* `partial` types can add inject new variables to a type
* the order of any interception for constructors and assignment operators is always the base type first followed by the `partial` types in order they were declared

Some important concerns to consider:
* `partial` types may accidentally inject names into types that could cause code misunderstandings
* `partial` types may fatten the memory requirements for types where new variables are injected
* the memory layout of types may change which may cause any union expectations for a type to cause undefined behaviors
* the `as` casting operator for compatible types may error if an `as` casting destination type is ever extended
* functions calls cannot be intercepted from the base type (other than constructors and assignment operators)
* creating an instance of a `partial` type separate from the main type is not possible
* `partial` types may have name conflicts with other partial types and the compiler may error

Needless to repeat, use extreme caution with `partial` types. They are not meant to be the go to tool in the programmer's toolbox.

````zax
MyExtendedContext :: partial Context {
    logCount : Integer

    log final : ()(...) = {
        //...
        ++logCount
        //...
    }
}

___.log("new function to extend context")
````
