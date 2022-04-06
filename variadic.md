
# [Zax Programming Language](index.md)

## Variadic Functions

### Enumerating the `...` arguments with `each`

Variadic functions allow a variable list of arguments to be passed into a function. The `each` statement can be used to enumerate all of a variadic function's arguments. For each type of variable in a `...` list, a compiler will create a type safe block of code.

````zax
print final : ()(...) = {
    // ...
}

list final : ()(value : Integer) = {
    print(value)
}

list final : ()(value : String) = {
    print(value)
}

list final : ()(...) = {
    each value: in ... {
        // polymorphic rules will cause the best match to be called (and
        // for single arguments of `Integer` or `String` this variadic function
        // will not be selected)
        list(value)

        // (be careful to not call with an unsupported type or this
        // variadic `...` function or this function will be called in an
        // infinite recursive loop)
    }
}

list("marbles", 10, "ducks", 7, "staples", 150, "good", # : [] = [{ 1, 2, 3}] )
````


### Non optional arguments with variadic arguments

Variadic functions can have one or more arguments prior to a variadic argument list. 

````zax
print final : ()(...) = {
    // ...
}

list final : ()(value : String) = {
    print(value)
}

list final : ()(listName : String, size : Integer, ...) = {
    print("list type:", listName, "total items:", size)

    each value: in ... {
        list(value)
    }
}

// first two function arguments are required and not part of the `...` list
list("random items", 6, "marbles", "fudge", "glasses", "bar", "staples", "wine")
````


### Using `countof` with variadic functions

The `countof` operator can be used to count the number of items passed into a variadic function.

````zax
print final : ()(...) = {
    // ...
}

list final : ()(value : String) = {
    print(value)
}

list final : ()(listName : String, ...) = {
    print("list type:", listName, "total items:", countof ...)

    each value: in ... {
        list(value)
    }
}

// first argument is required and not part of the `...` list
list("random items", "marbles", "fudge", "glasses", "bar", "staples", "wine")
````


### Variadic forwarding and the `last` qualifier

Variadic arguments can be forwarded from one function to another function without ever enumerating the variadic arguments.

A `last` qualifier helps optimize a variadic transfer. Types qualified as `last` can optimize data transfer when passed in as an argument or received as a result.

A `last` qualifier can be applied to a variadic function to cause all passed in values to be qualified as `last` instances (where appropriate) whose contents are being forwarded to another function. The `last` qualifier is implicitly applied to values passed by-value which are known not to be in use further in the code. By-reference arguments will only have `last` automatically applied if a `type` passed into a variadic function was already qualified as `last`. A programmer can add a `last` qualifier to a `...` expression automatically using the `as` operator with a `last` qualifier.

````zax
func final : ()(...) = {
    // ...
}

anotherFunc final : ()(...) = {
    // `last` qualifier is applied to forwarded arguments automatically
    func(...)
}

yetAnotherFunc final : ()(...) = {
    // apply a `last` qualifier explicitly to ensure a compiler default forwards
    // a `last` qualifiers as appropriate
    func(... as last)
}
````


### Variadic types

All variadic functions represent a list of values as `...` and a list of types as `$...`. This allows operations to be performed on either argument values or argument types.

````zax
func final : ()(...) = {
    each type in $... {
        // ... operates on the type, not the value
    }
}
````
