
# [Zax Programming Language](index.md)

## Memory Allocation

### Simple allocation with automatic destruction

Types can be allocated and then de-allocated automatically when owning pointers fall out of scope.

````zax
MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

doSomething : ()(input : MyType*) = {
    // code that 
}

func : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the
        // context's allocator and is `own` implicitly in `value1`
        value1 : MyType* @

        // `value2` is allocated with the same context allocator and
        // `value2` is explicitly marked as `own`
        value2 : MyType* own @    

        doSomething(value1)
        doSomething(value2)

        // `value2` and `value1` are destructed and deallocated
    }

    return "I'll be back."
}
````


#### Transferring ownership of an `own` pointer

A pointer marked as `own` can only only be owned by a single variable at a time. When an pointer marked as `own` is transferred to another pointer marked as `own` the ownership of the pointer is transferred. Only pointers marked as `own` can transfer to other pointers marked as `own` or alternative transfer to pointers marked as `strong`.

````zax
MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

print : ()(...) = {
    //...
}

printIfValidPointer : ()(pointerToValue : MyType*) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func : ()() = {
    value1 : MyType* own @  // allocated using the context allocator
    value2 : MyType* own    // no allocation is performed and pointer points
                            // to nothing

    // ownership of the own pointer is transferred from value1 to value2
    value2 = value1

    printIfValidPointer(value1)     // will print "false"
    printIfValidPointer(value2)     // will print "true"
}
````

#### Implicit ownership vs explicit ownership transfer

Implicitly `own` pointers cannot be transferred to explicitly `own` pointers (or `discard`, `collect`, `strong`, or `weak` pointer declarations).

Enforcement of explicit ownership is done for these reasons:
* ensures the programmer acknowledges the additional data type overhead needed to track the originating allocator is acceptable (implicit `own` pointers do not require additional space overhead although additional code overhead is created for the automatic pointer destruction and deallocation)
* ensures any transfer of ownership was done knowingly rather than accidentally

````zax
MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

print : ()(...) = {
    //...
}

printIfValidPointer : ()(pointerToValue : MyType*) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func : ()() = {
    value1 : MyType* @          // allocated using the context allocator and
                                // pointer is `own` implicitly
    value2 : MyType* own        // no allocation is performed and the `own`
                                // pointer points to nothing

    // ERROR: The `value1` pointer was implicitly `own` and thus cannot transfer
    // ownership to `value2` which is marked explicitly `own`. To correct the
    // error, mark `value1` as `own` explicitly to allow ownership transfer.
    value2 = value1

    printIfValidPointer(value1)     // will print "false"
    printIfValidPointer(value2)     // will print "true"
}
````


### Allocating with custom allocators

Allocating and deallocating is done by specifying the allocator to use after the `@` operator. The same allocator type instance is used for deallocation later when the allocated types are destroyed and deallocated.

````zax
MyCustomAllocator :: type {
    //... allocate/deallocate functions documented elsewhere ...
}

MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

doSomething : ()(input : MyType*) = {
    // code that 
}

func : (result : String)() = {

    allocator : MyCustomAllocator

    scope {
        // the @ operator allocates `value1` dynamically with the
        // custom allocator and is `own` implicitly in `value1`
        value1 : MyType* @ allocator

        // `value2` is allocated with the same custom allocator and
        // `value2` is explicitly marked as `own`
        value2 : MyType* own @ allocator

        doSomething(value1)
        doSomething(value2)

        // `value2` and `value1` are destructed and deallocated with the
        // custom allocator
    }

    return "I'll be back."

    // custom allocator is destructed
}
````

### Allocating using `discard`

The `discard` keyword can be used with a pointer type indicating that the type destruction is performed but the memory backing the type is never freed. This type of allocation is useful when the type needs scoped control of destruction but deallocation can happen as a single memory deallocation for all allocations performed within the same allocator.

````zax
MyCustomAllocator :: type {
    //... allocate/deallocate functions documented elsewhere ...
}

MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

doSomething : ()(input : MyType*) = {
    // code that 
}

func : (result : String)() = {

    allocator : MyCustomAllocator

    scope {
        // the @ operator allocates `value1` dynamically with the
        // custom allocator but the value is merely destructed but not
        // deallocated when the value falls out of scope
        value1 : MyType* discard @ allocator

        doSomething(value1)

        // `value1` is destructed but not freed when the value
        // falls out of scope
    }

    return "I'll be back."

    // custom allocator is destructed and memory backing used earlier for
    // `value1` is discarded.
}
````


#### Transferring ownership of a `discard` pointer

Similar to a pointer marked as `own`, a pointer marked as `discard` can only only be owned by a single variable at a time. When an pointer marked as `discard` is transferred to another pointer marked as `discard` the ownership of the pointer is transferred.

````zax
MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

print : ()(...) = {
    //...
}

printIfValidPointer : ()(pointerToValue : MyType*) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func : ()() = {
    value1 : MyType* discard @  // allocated using the context allocator
    value2 : MyType* discard    // no allocation is performed

    // ownership of the discard pointer is transferred from value1 to value2
    value2 = value1

    printIfValidPointer(value1)     // will print "false"
    printIfValidPointer(value2)     // will print "true"
}
````


### Allocating using `collect`

The `collect` keyword can be used with a pointer type indicating that the type destruction and deallocation is performed at the time when the allocator used to allocate the type released the underlying memory containing the allocated type. The allocations are said to be collected in the allocator. This type of allocation is useful to collectively destruct with a single deallocation for all allocations performed within the same allocator.


````zax
MyCustomAllocator :: type {
    //... allocate/deallocate functions documented elsewhere ...
}

MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

doSomething : ()(input : MyType*) = {
    //...
}

func : (result : String)() = {

    allocator : MyCustomAllocator

    scope {
        // the @ operator allocates `value1` dynamically with the
        // custom allocator but the value is not destructed nor
        // deallocated until the custom allocator is destructed
        value1 : MyType* collect @ allocator

        doSomething(value1)

        // `value1` is no longer referenced but the type is
        // not destructed nor deallocated
    }

    return "I'll be back."

    // custom allocator is destructed and `value1` is now destructed and
    // the memory backing value1 is discarded
}
````

#### Transferring ownership of an `collect` pointer

Similar to a pointer marked as `own`, a pointer marked as `collect` can only only be owned by a single variable at a time. When an pointer marked as `collect` is transferred to another pointer marked as `collect` the ownership of the pointer is transferred. However, as the type's lifetime is tied to the allocator where the type was allocated thus the need to transfer ownership of `collect` pointers is only done for convenience of ensuring only one variable contains the pointer to a type and to allow common transfer behavior for `own`, `discard` and `collect` pointers.

````zax
MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

print : ()(...) = {
    //...
}

printIfValidPointer : ()(pointerToValue : MyType*) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func : ()() = {
    value1 : MyType* collect @      // allocated using the context allocator
    value2 : MyType* collect        // no allocation is performed

    // ownership of the `collect` pointer is transferred from
    // `value1` to `value2`
    value2 = value1

    printIfValidPointer(value1)     // will print "false"
    printIfValidPointer(value2)     // will print "true"
}
````


### Dangling pointers with `own` memory

Care must be taken when using raw pointers to memory as nothing ensures the pointer is invalidated when the underlying type being pointed to is destroyed. If automatic invalidation is desired then `strong` and `weak` pointers can be used as an alternative.

````zax
MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

pointerToAValue : MyType*   // the pointer will point to nothing by default

doSomething : ()(input : MyType*) = {
    //...

    pointerToAValue = input
}

func : (result : String)() = {


    scope {
        // the @ operator allocates `value1` dynamically with the
        // context's allocator
        value1 : MyType* own @

        doSomething(value1)

        // `value1` is no longer referenced and the type is destroyed
        // and the memory deallocated
    }

    // UNDEFINED BEHAVIOR:
    // `value1` was allocated but destroyed yet `pointerToAValue` is still
    // pointing to the previously allocated and now deallocated memory
    if pointerToAValue.myValue1 > 10
        return "In your dreams."

    return "I'll be back."
}
````

### Context allocator

Every function receives a context type and passes the context type to any other function called in the call stack. The context type is named with triple underscores `___` and the context type contains helpful types and support functions for use across functions. Once such important type is an allocator within the context.

````zax
MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

doSomething : ()(input : MyType*) = {
    //...
}

func : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the
        // context's allocator
        value1 : MyType* own @

        // the @ operator allocates `value2` dynamically with the
        // context's allocator (which is verbosely expressed but is
        <!-- // functionally equivalent to `value1`'s allocation) -->
        value1 : MyType* own @ ___.allocator

        doSomething(value1)

        // `value1` is no longer referenced and the type is destroyed
        // and the memory deallocated
    }

    return "I'll be back."
}
````
