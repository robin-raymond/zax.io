
# [Zax Programming Language](index.md)

## Variadic Functions

Current fixed-arity calls, argument/default binding, result routing, and callable
selection are defined by
[Zax function invocation](language/function-invocation.md). This page remains
legacy input for variadic expansion, enumeration, and variadic-specific
selection, which must fit that fixed-arity base model.
Current `copy`/`deep`/`move`/`last` meaning and explicit source restatement are defined
by [Zax transfer stances](language/transfer-stances.md).

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


### Using `count of` with variadic functions

The `count of` operator can be used to count the number of items passed into a variadic function.

````zax
print final : ()(...) = {
    // ...
}

list final : ()(value : String) = {
    print(value)
}

list final : ()(listName : String, ...) = {
    print("list type:", listName, "total items:", count of ...)

    each value: in ... {
        list(value)
    }
}

// first argument is required and not part of the `...` list
list("random items", "marbles", "fudge", "glasses", "bar", "staples", "wine")
````


### Variadic forwarding and transfer stance

Variadic arguments may eventually support forwarding without enumerating each
value:

```zax
forwardAll final : ()(...) = {
  sendAll(...)
}
```

The old proposal automatically applied `last` to some forwarded values. That
behavior is superseded. Zax does not silently choose terminal transfer from
liveness analysis.

Future variadic work must decide:

- whether `...` denotes one pack source or several independently stanced values;
- how a declaration or use-site stance applies to each forwarded value;
- whether mixed `copy`/`deep`/`move`/`last` packs require enumeration;
- how reference and by-value elements preserve their different effects;
- when terminal opportunity requires an intent error;
- how forwarding exposes per-element ambiguity and diagnostics;
- and how result or capture packs preserve exact stance.

The future design must use the current explicit stance and accepted-fallback
model. This page does not establish `... as last` or another pack-restatement
syntax.


### Variadic types

All variadic functions represent a list of values as `...` and a list of types as `$...`. This allows operations to be performed on either argument values or argument types.

````zax
func final : ()(...) = {
    each type in $... {
        // ... operates on the type, not the value
    }
}
````
