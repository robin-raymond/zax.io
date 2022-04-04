
# [Zax Programming Language](index.md)

## Lazy Functions

A function can be declared as `lazy` where the function does not execute immediately and can return multiple results until the function is complete. The first call to a function declared as `lazy` (with optional arguments) merely returns a `lazy` function which can be invoked repeatedly to obtain multiple results (via `yield`) until the `lazy` function decides to perform a final `return`. Once a `lazy` function has returned, any further calling of a `lazy` function afterwords may cause a `lazy-already-complete` panic (if not disabled) or undefined behavior otherwise.

As a function declared as `lazy` is not immediately executed, the first execution of the function occurs upon the first call to the resulting `lazy` function. Repeated calls continue executing from the `yield` point until a `return` statement is hit.

A returned `lazy` function can be disposed prior to completion of a function declared as `lazy`. In such a case, the `return` is never reached. Likewise a function declared as `lazy` can execute forever and thus does not require a `return` statement (e.g. a lazy random number generator). Every `yield` becomes a potential implicit `return` point to facilitate disposing of `lazy` functions.

An example of a function declared as `lazy` running forever:

````zax
print final : ()(...) = {
    // ...
}

countForever final : (value : Integer)(starting : Integer) lazy = {
    forever {
        ++starting
        yield starting
    }
    [[never]]
}

later = countForever(5)

print(later())  // will print "6"
print(later())  // will print "7"
print(later())  // will print "8"

// dispose of the `lazy` function explicitly
// (can be implicit too by falling out of scope)
later = #:
````


#### Functions qualified as `lazy` which complete

Functions qualified as `lazy` may eventually complete (or complete after at least one call). Upon completion a `lazy` function may not be called again without a panic or undefined behavior. Either a function should only ever be expected to be called once, or a fixed number of times, or a function should use some kind signal to prevent a `lazy` function being called beyond it's lifetime.

One simple signalling technique is to return an optional result (which may not have a value). When a function qualified as `lazy` has exhausted its results, that function can return a defaulted optional using `return #`. The calling code will have to verify a result is valid and stop calling a `lazy` function once the first empty optional result is received.

````zax
print final : ()(...) = {
    // ...
}

nextNumberUntil100 final : (value? : Integer)(starting : Integer) lazy = {
    forever {
        if (starting >= 100)
            break

        ++starting
        yield starting
    }
    return #
}

later = nextNumberUntil100(5)

while result := later() {
    print(result.)  // will print 5 to 99
}

print(later())  // a panic will occur
````


#### Functions qualified as `lazy` are not automatically `deep`

Unlike `promise` or `task`, functions qualified as `lazy` are not expected be qualified as `deep` (and thus don't require a `[[sequential]]` compiler directive to suppress warnings about `deep` not being utilized). The use case for a `lazy` functions is presumed to mostly be related to synchronous programming. In fact, `lazy` functions make great iterators or generators which are entirely unrelated to [concurrency](concurrency.md).
