
# [Zax Programming Language](index.md)

## Nothing Type Instances

### Basic Overview

Zax allows types to have a special `Nothing` instance of a type. Whenever a pointer to `Nothing` is created for a type that supports a `Nothing` instance, the pointer is automatically set to point to the `Nothing` instance. This ensures that a pointer always points to a real instance of a type even when no allocated instance of a type was ever created.

`Nothing` instances help reduce code checks required to check the validity of pointers by a allowing pointers to `Nothing` instances to be substituted in places where a real instance would normally be present. The `Nothing` instances allow code to behave as if a real instance existed. The `Nothing` instance literally should do nothing (or as close to nothing as possible). This is why Zax has pointers to `Nothing` and Zax does not rely on pointers to be assigned a `null` value. In other languages, checks must be performed prior to every usage of a pointer which may be `null`. `Nothing` pointers will point to a real instance of a type in memory but that type is coded to perform almost no operations when invoked allowing many checks for a `null` pointer to become unnecessary where utilizing a `Nothing` instance would not affect a code's runtime in important ways.

To declare a type as supporting a `Nothing` instance, a `Nothing` type must be declare on a type's constructor as it's only input argument. This is known as the `Nothing` constructor. Directly creating a `Nothing` type instance is not possible as a `Nothing` type cannot be directly instantiated. The Zax language treats receiving of a `Nothing` type in a constructor as the indicator that the type is being declared to support `Nothing`. This constructor is used to create a `Nothing` instance for a given type.

Unlike other constructors, a `Nothing` constructor may return a pointer to an instance of the constructed type. This is allowed so a constructor may return an alternative pointer representing a `Nothing` instance projected from another constructed type that is binary compatible with the current `Nothing` type instance. Normally `_` would be returned indicating a `Nothing` constructor, or a constructor may omit a return type entirely. Zax will treat a missing return as an assumed return of the self (`_`) instance.

`Nothing` instances are constructed into global memory by default. A `Nothing` instance should perform no operations for functions called within a type's `Nothing` instance (or as little as possible) and return values that would be considered safe for a caller in function (where possible).

The self pointer (`_`) can be checked within a type's function with `if` to verify if a call was made into a `Nothing` instance or if a call was made using a real instance of a type.

````zax
MyType :: type {
    +++ final : ()() = {
        // the empty constructor
    }

    // a `Nothing` argument is not an empty argument list or a `null` type;
    // when used in a constructor, the `Nothing` type indicates a `Nothing`
    // instance is being constructed allowing the instance to set itself up to
    // be the `Nothing` instance
    +++ final : (result : MyType *)(# : Nothing) = {
        // reset function pointers to nothing
        doSomething = {}
        doSomethingElse = { # value }
        return _
    }

    doSomething immutable : ()() = {
        // ...
    }

    doSomethingElse immutable : ()(value : Integer) = {
        // ...
    }

    print final : ()(...) = {
        // check if self is pointing to the `Nothing` instance and fast fail
        if !_
            return

        // ...        
    }
}

myType : myType *

// these functions do not crash or panic; they simply perform no operation
myType.doSomething()
myType.doSomethingElse()
myType.print("hello")
````

Whereas this will crash:

````zax
MyType :: type {

    doSomething final : ()() = {
        // ...
    }

    doSomethingElse final : ()(value : Integer) = {
        // ...
    }

    print final : ()(...) = {
        // ...        
    }
}

myType : MyType *

// attempting to call these functions may cause a panic since `Nothing` is
// undefined and the calls would have undefined behaviors; no type was allocated
// and `Nothing` construction was not supported for this type
myType.doSomething()
myType.doSomethingElse()
myType.print("hello")
````


### Setting values in nothing types

`Nothing` instances are real instance of a type. Thus `Nothing` type instances have values that map to real memory in an underlying type's `Nothing` instance. Thus values in `Nothing` instances should not be set, or if values are allowed to be set then data contained in a `Nothing` instance must be treated as garbage. `Nothing` instances are not thread safe and they are not designed to be acted upon. Nothing types should be treated as `immutable` especially for concurrency reasons. If a `Nothing` instance is not concurrency safe and modifying its contents would cause issues then undefined behaviors can occur. Accessing `immutable` functions should be designed to be safe for types that support having a `Nothing` instance.

When values are accessed within a type not supporting a `Nothing` instance, the `pointer-to-nothing-accessed` may be issued (i.e. if that panic was not explicitly disabled). For type declared with a `Nothing` constructor, a panic will not be issued when calling functions on a `Nothing` instance. Function on a `Nothing` instance can self protect themselves from access (by validating a `_` is not pointing to the `Nothing` instance). Whereas values accessed when an instance is pointing to nothing without a `Nothing` constructor being defined have no method to self protect themselves.

If a `Nothing` constructor is not declared on a type, accessing a type's functions may issue a `pointer-to-nothing-accessed` (i.e. if that panic was not explicitly disabled).

`Nothing` instances are not meant to be used as global singletons (although technically they are allocated as global singletons). The usage of pointers to nothing as singletons to perform functional work is highly discouraged (but not enforced).

````zax
MyType :: type {
    value1 : Integer
    value2 : String deep

    +++ final : ()(:Nothing) = {
    }
}

// default pointer will point to a the `Nothing` instance
myType : MyType *

// nothing prevents the `Nothing` instance's values from being set (where
// normally a panic of `pointer-to-nothing-accessed` may be issued as when
// pointer would point to a `null` value)
myType.value1 = 5

// the following statement will be true
if myType.value1 == 5
    myType.value2 = "yes, this will become set into value2"
````
