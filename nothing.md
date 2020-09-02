
# [Zax Programming Language](index.md)

## Nothing Type Instances

### Basic Overview

Zax allows types to have a special nothing instance of a type. Whenever a pointer to nothing is created for a type that supports nothing instances, the pointer is automatically set to point to the nothing instance. This ensures that an pointer to a real instance of the type always exists even when no allocation for an instance of the type was ever created.

Nothing instances help reduce the code checks required to check the validity of pointers by a allowing nothing instance to be substituted in the place of a real instance and behave as if a real instance exists. The nothing instance literally does nothing (or as close to nothing as possible). This is why Zax has pointers to nothing and not pointers to null. Nothing pointers may actually point to a real instance in memory that does nothing when invoked.

To declare that a type supports nothing instances, an `Unknown` type must be passed into the constructor as it's only input argument. This is known as the nothing constructor. Passing an `Unknown` type instance is not possible as `Unknown` can only exist in its pointer form despite `Unknown` being a legal type. The Zax language treats passing an `Unknown` non-pointer in the constructor as the indicator the nothing type instance is being constructed.

Unlike other constructors, a nothing constructor may return a pointer to an instance of the constructed type. This is allowed so the constructor may return an alternative pointer representing the nothing instance into another constructed type that is binary compatible with the original type. Normally `_` would be returned indicating the nothing pointer is indeed a pointer to the self instance, or the constructor may omit the return type entirely.

Nothing instances are constructed into global memory. A nothing instance should perform no operations for functions called within it (or as little as possible) and return safe values from functions where possible.

The self pointer (`_`) can be checked with `if` to verify if the created instance is the nothing instance or a real instance.

````zax
MyType :: type {
    +++ : ()() = {
        // the empty constructor
    }

    // a `Unknown` argument is not an empty argument list or a null type;
    // when used in a constructor, the `Unknown` type indicates the nothing
    // instance is being constructed allowing the object to set itself up to be
    // the nothing instance
    +++ : (result : MyType*)(:Unknown) = {
        // reset function pointers to nothing
        doSomething = {}
        doSomethingElse = { [[discard]] value }
        return _
    }

    doSomething : ()() = {
        //...
    }

    doSomethingElse : ()(value : Integer) = {
        //...
    }

    print final : ()(...) = {
        // check if self is pointing to the nothing instance and fast fail
        if !_
            return

        //...        
    }
}

myType : myType*

// these functions do not crash or panic; they simply perform no operation
myType.doSomething()
myType.doSomethingElse()
myType.print("hello")
````

Whereas this will crash:

````zax
MyType :: type {

    doSomething : ()() = {
        //...
    }

    doSomethingElse : ()(value : Integer) = {
        //...
    }

    print final : ()(...) = {
        //...        
    }
}

myType : MyType*

// attempting to call these functions may cause a panic since the nothing
// and definitely have undefined behaviors; no type was allocated and the
// nothing construction was not supported for this type
myType.doSomething()
myType.doSomethingElse()
myType.print("hello")
````
