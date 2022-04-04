
# [Zax Programming Language](index.md)

## Nothing Type Instances

### Basic Overview

Zax allows types to have a special nothing instance of a type. Whenever a pointer to nothing is created for a type that supports nothing instances, the pointer is automatically set to point to the nothing instance. This ensures that a pointer always points to a real instance of the type even when no allocated instance of the type was ever created.

Nothing instances help reduce code checks required to check the validity of pointers by a allowing pointers to nothing instances to be substituted in the place where a real instance would normally be present and behave as if a real instance existed. The nothing instance literally does nothing (or as close to nothing as possible). This is why Zax has pointers to nothing and Zax does not rely on pointers to null where checks must be performed prior to every usage of a pointer which may be null. Nothing pointers will point to a real instance of a type in memory but that type is coded to perform almost no operations when invoked.

To declare a type as supporting a nothing instance, a `Nothing` type must be declare on a constructor as it's only input argument. This is known as the nothing constructor. Creating a `Nothing` type instance is not possible as the `Nothing` type cannot be directly instantiated. The Zax language treats receiving of a `Nothing` type in the constructor as the indicator the type is being declared as supporting nothing the nothing type and this constructor is used to create a nothing instance for a type.

Unlike other constructors, a nothing constructor may return a pointer to an instance of the constructed type. This is allowed so the constructor may return an alternative pointer representing the nothing instance projected from another constructed type that is binary compatible with the original type. Normally `_` would be returned indicating the nothing pointer is indeed a pointer to the self instance, or the constructor may omit the return type entirely and Zax will default the self instance.

Nothing instances are constructed into global memory by default. A nothing instance should perform no operations for functions called within the type's nothing instance (or as little as possible) and return values that would be considered safe for the caller of the function where possible.

The self pointer (`_`) can be checked within a type's function with `if` to verify if the call was made into the nothing instance or into a real instance of a type.

````zax
MyType :: type {
    +++ final : ()() = {
        // the empty constructor
    }

    // a `Nothing` argument is not an empty argument list or a null type;
    // when used in a constructor, the `Nothing` type indicates the nothing
    // instance is being constructed allowing the object to set itself up to be
    // the nothing instance
    +++ final : (result : MyType *)(:Nothing) = {
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
        // check if self is pointing to the nothing instance and fast fail
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

// attempting to call these functions may cause a panic since the nothing
// and definitely have undefined behaviors; no type was allocated and the
// nothing construction was not supported for this type
myType.doSomething()
myType.doSomethingElse()
myType.print("hello")
````


### Setting values in nothing types

Nothing types are real instance of a type. Thus nothing types have values that map to the underlying type's nothing object. Thus values in nothing objects should not be set, or if they are set then the data contained must be treated as garbage. Nothing objects are not thread safe because they are not designed to be acted upon. Nothing types should be treated as immutable especially for concurrency reasons. If the type is not concurrency safe and modifying the contents causes an issue then undefined behaviors can happen. Accessing final functions (or overridden immutable functions) is considered safe for types that support having a nothing instance.

When values are accessed within a type and the pointer is to nothing, the `pointer-to-nothing-accessed` may be issued (i.e. if not explicitly disabled). For type declared with a nothing constructor, a panic will not be issued when calling nothing functions. This is because function can self protect themselves from access (by validating the `_` is not pointing to the nothing instance) whereas values cannot self protect.

If the nothing constructor is not declared on a type, accessing the type's functions may issue the `pointer-to-nothing-accessed` (i.e. if not disabled).

Nothing instances are not meant to be used as global singletons (although technically they are allocated as global singletons). The usage of nothing pointers as singletons to perform functional work is highly discouraged (but not enforced).

````zax
MyType :: type {
    value1 : Integer
    value2 : String deep

    +++ final : ()(:Nothing) = {
    }
}

// default pointer will point to the nothing instance
myType : MyType *

// nothing prevents the nothing instant values from being set (except a panic
// of `pointer-to-nothing-accessed` may be issued as the pointer is null)
myType.value1 = 5

// the following statement will be true
if myType.value1 == 5
    myType.value2 = "yes, this will become set into value2"
````
