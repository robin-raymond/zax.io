
# [Zax Programming Language](index.md)

## Memory Allocation

### Simple allocation with automatic destruction

Types can be allocated and de-allocated automatically when `own` or `unique` pointers fall out of scope. Allocated types with the `@`, `@@` or `@!` allocators are implicitly qualified as `unique`. The `own` qualification requires explicit definition (as `own` pointers have additional control block overhead not present with `unique` pointers). If a pointer is allocated at the same time as being declared then the pointer is implicitly flagged as a `unique` pointer type.

````zax
MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

doSomething final : ()(input : MyType *) = {
    // code that 
}

func final : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the context's
        // allocator and is `unique` implicitly in `value1`
        value1 : MyType * @

        // `value2` is allocated with the same context allocator and
        // `value2` is explicitly qualified as `own`
        value2 : MyType * own @    

        doSomething(value1)
        doSomething(value2)

        // `value2` and `value1` are destructed and deallocated
    }

    return "I'll be back."
}
````


#### Transferring ownership of an `unique` pointer

A pointer qualified as `unique` (implicitly or explicitly) can only be owned by a single variable at a time. When an pointer qualified as `unique` is transferred to another pointer qualified as `unique` the ownership of the pointer is transferred. Only pointers qualified as `unique` can transfer to other pointers qualified as `unique` and cannot be transferred to other qualified pointer types such as `own`, `handle` or `strong`.

````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

printIfValidPointer final : ()(pointerToValue : MyType *) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func final : ()() = {
    value1 : MyType * unique @  // allocated using the context allocator
    value2 : MyType * unique    // no allocation is performed and pointer points
                                // to nothing

    // ownership of the `unique` pointer is transferred from 
    // `value1` to `value2`
    value2 = value1

    printIfValidPointer(value1)     // will print "false"
    printIfValidPointer(value2)     // will print "true"
}
````


#### Transferring ownership of an `own` pointer

A pointer qualified as `own` can only only be owned by a single variable at a time. When an pointer qualified as `own` is transferred to another pointer qualified as `own` the ownership of the pointer is transferred. Only pointers qualified as `own` can transfer to other pointers qualified as `own`, or as an alternative transfer to pointers qualified as `handle` or `strong`.

````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

printIfValidPointer final : ()(pointerToValue : MyType *) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func final : ()() = {
    value1 : MyType * own @  // allocated using the context allocator
    value2 : MyType * own    // no allocation is performed and pointer points
                             // to nothing

    // ownership of the `own` pointer is transferred from `value1` to `value2`
    value2 = value1

    printIfValidPointer(value1)     // will print "false"
    printIfValidPointer(value2)     // will print "true"
}
````


#### Implicit `unique` vs explicit `own` transfer

Transfer of `unique` pointers to `own` pointers cannot be done implicitly (or `discard`, `collect`, `strong`, or `weak` pointer declarations).

Enforcement of explicit ownership is done for these reasons:
* ensures the programmer acknowledges the additional data type overhead needed to track the originating allocator is acceptable (implicit `unique` pointers do not require additional control block overhead, although some additional code overhead is created for the automatic pointer destruction and deallocation)
* ensures any transfer of ownership was done knowingly rather than accidentally

````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

printIfValidPointer final : ()(pointerToValue : MyType *) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func final : ()() = {
    value1 : MyType * @         // allocated using the context allocator and
                                // pointer is `unique` implicitly
    value2 : MyType * own       // no allocation is performed and the `own`
                                // pointer points to nothing
    value3 : MyType * own       // no allocation is performed and the `own`
                                // pointer points to nothing

    // ERROR: The `value1` pointer was implicitly `unique` and thus
    // cannot transfer ownership to `value2` which is qualified
    // explicitly `own`. To correct the error, mark `value1` as `own`
    // explicitly to allow ownership transfer.
    value2 = value1

    // OKAY: allowed to transfer a `unique` pointer to an `own` pointer as
    // the overhead is acknowledged
    value3 = value1 as own

    printIfValidPointer(value1)     // will print "false"
    printIfValidPointer(value2)     // will print "true"
}
````


#### Implicit and explicit raw pointers

````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

printIfValidPointer final : ()(pointerToValue : MyType *) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func final : ()() = {
    value1 : MyType * @         // allocated using the context allocator and
                                // pointer is `unique` implicitly
    value2 : MyType * raw       // explicitly marked as a `raw` pointer and no
                                // allocation is performed and the pointer
                                // points to nothing by default
    value3 : MyType * raw @     // explicitly marked as a `raw` pointer and
                                // allocation is performed (this will cause a
                                // `allocation-into-raw-pointer` warning)
    value4 : MyType *           // implicitly marked as a `raw` pointer and no
                                // allocation is performed and the pointer
                                // points to nothing by default

    value2 = value1
    value3 = value1
    value4 = value1

    printIfValidPointer(value1)     // will print "true"
    printIfValidPointer(value2)     // will print "true"
    printIfValidPointer(value3)     // will print "true"
    printIfValidPointer(value4)     // will print "true"

    // `value3` will not be destructed and the memory allocated for `value3`
    // must be deallocated through other undefined means
}
````


#### Transferring `own` pointers across threads

If an `own` pointer will be transferred to a different thread, either a deep copy of the `own` pointer should be performed, or a parallel allocator should be considered. By default, `own` pointers allocate using the standard allocators (i.e. typically the sequential allocator). Allocation of an `own` pointer in one thread and then deallocation of the pointer on a different thread may cause dangling allocations that will only become cleaned during dangling allocation cleanup happens on the originating thread.

While the standard allocators can be replaced with thread safe allocators, the optimized thread-unaware allocators would be replaced by less efficient thread aware counterparts universally (which is often unneeded).


### Allocating with custom allocators

Allocating and deallocating is done by specifying the allocator to use after the `@` operator. The same allocator type instance is used for deallocation later when the allocated type is destroyed and deallocated.

````zax
MyCustomAllocator :: type {
    // ... allocate/deallocate functions documented elsewhere ...
}

MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

doSomething final : ()(input : MyType *) = {
    // ...
}

func final : (result : String)() = {

    allocator : MyCustomAllocator

    scope {
        // the @ operator allocates `value1` dynamically with the
        // custom allocator and is `own` implicitly in `value1`
        value1 : MyType * @ allocator

        // `value2` is allocated with the same custom allocator and
        // `value2` is explicitly qualified as `own`
        value2 : MyType * own @ allocator

        doSomething(value1)
        doSomething(value2)

        // `value2` and `value1` are destructed and deallocated with the
        // custom allocator
    }

    return "I'll be back."

    // custom allocator is destructed
}
````


### Allocating with custom allocators and using constructors

Allocating and deallocating is done by specifying the allocator to use after the `@` operator. The assignment operator can be used to specify constructor arguments. The same allocator type instance is used for deallocation later when the allocated types are destroyed and deallocated.

````zax
MyCustomAllocator :: type {
    // ... allocate/deallocate functions documented elsewhere ...
}

MyType :: type {
    myValue1 : Integer
    myValue2 : String

    +++ final : ()(value : Integer) = {
        myValue1 = value
    }

    +++ final : ()(value1 : Integer, value2 : String) = {
        myValue1 = value1
        myValue2 = value2
    }
}

doSomething final : ()(input : MyType *) = {
    // ...
}

func final : (result : String)() = {

    allocator : MyCustomAllocator

    scope {
        // `value1` is allocated with the same custom allocator and
        // `value1` is constructed using the default constructor
        value1 : MyType * @ allocator

        // the @ operator allocates `value2` dynamically with the
        // custom allocator and is constructed with the value 5
        value2 : MyType * @ allocator = 5

        // the @ operator allocates `value3` dynamically with the
        // custom allocator and is constructed with the value 5 and "hello"
        value2 : MyType * @ allocator = { 5, "hello" }

        doSomething(value1)
        doSomething(value2)
        doSomething(value3)

        // `value3`, `value2` and `value1` are destructed and deallocated with
        // the custom allocator
    }

    return "I'll be back."

    // custom allocator is destructed
}
````


### Allocating using `discard`

The `discard` keyword can be used with a pointer type indicating that a type's destruction must be performed but the memory backing the type is never freed. This type of allocation is useful when a type needs scoped control of destruction but deallocation can happen as a single memory deallocation for all allocations performed with the same allocator. Pointers marked as `discard` do not have control blocks and do not have any reserved space for associated deallocation routines and thus are one of the most efficient allocators. Caution should be used that all types allocated as `discard` are always destroyed prior to deallocation of the underlying memory as undefined behaviors can ensue otherwise.

````zax
MyCustomAllocator :: type {
    // ... allocate/deallocate functions documented elsewhere ...
}

MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

doSomething final : ()(input : MyType *) = {
    // code that 
}

func : (result : String)() = {

    allocator : MyCustomAllocator

    scope {
        // the @ operator allocates `value1` dynamically with the
        // custom allocator but the value is merely destructed but not
        // deallocated when the value falls out of scope
        value1 : MyType * discard @ allocator

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

Similar to a pointer qualified as `unique`, a pointer qualified as `discard` can only only be owned by a single variable at a time. When an pointer qualified as `discard` is transferred to another pointer qualified as `discard` the ownership of the pointer is transferred.

````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

printIfValidPointer final : ()(pointerToValue : MyType *) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func final : ()() = {
    value1 : MyType * discard @  // allocated using the context allocator
    value2 : MyType * discard    // no allocation is performed

    // ownership of the discard pointer is transferred from value1 to value2
    value2 = value1

    printIfValidPointer(value1)     // will print "false"
    printIfValidPointer(value2)     // will print "true"
}
````


### Allocating using `collect`

The `collect` keyword can be used with a pointer type indicating that the type destruction and deallocation is performed at the time when the allocator used to allocate the type released the underlying memory containing the allocated type. The allocations are said to be collected in the allocator. This type of allocation is useful to collectively destruct with a single deallocation for all allocations performed within the same allocator. The order of destruction is respected by reversing the order of construction of the allocated types.


````zax
MyCustomAllocator :: type {
    // ... allocate/deallocate functions documented elsewhere ...
}

MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

doSomething final : ()(input : MyType *) = {
    // ...
}

func final : (result : String)() = {

    allocator : MyCustomAllocator

    scope {
        // the @ operator allocates `value1` dynamically with the
        // custom allocator but the value is not destructed nor
        // deallocated until the custom allocator is destructed
        value1 : MyType * collect @ allocator

        doSomething(value1)

        // `value1` is no longer referenced but the type is
        // NOT destructed nor deallocated
    }

    return "I'll be back."

    // custom allocator is destructed and `value1` is now destructed and
    // the memory backing value1 is discarded
}
````


#### Transferring ownership of an `collect` pointer

Similar to a pointer qualified as `unique`, a pointer qualified as `collect` can only only be owned by a single variable at a time. When an pointer qualified as `collect` is transferred to another pointer qualified as `collect` the ownership of the pointer is transferred. However, as the type's lifetime is tied to the allocator where the type was allocated thus the need to transfer ownership of `collect` pointers is only done for convenience of ensuring only one variable contains the pointer to a type and to allow common transfer behavior for `own`, `discard` and `collect` pointers.

````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

printIfValidPointer final : ()(pointerToValue : MyType *) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func final : ()() = {
    value1 : MyType * collect @      // allocated using the context allocator
    value2 : MyType * collect        // no allocation is performed

    // ownership of the `collect` pointer is transferred from
    // `value1` to `value2`
    value2 = value1

    printIfValidPointer(value1)     // will print "false"
    printIfValidPointer(value2)     // will print "true"
}
````


### Dangling pointers with `own` memory

Care must be taken when using `raw` pointers to memory as nothing ensures the pointer is invalidated when the underlying type being pointed to is destroyed. If automatic invalidation is desired then `strong` and `weak` pointers can be used as an alternative.

````zax
MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

pointerToAValue : MyType *  // the pointer will point to nothing by default

doSomething final : ()(input : MyType *) = {
    // ...

    pointerToAValue = input
}

func final : (result : String)() = {


    scope {
        // the @ operator allocates `value1` dynamically with the
        // context's allocator
        value1 : MyType * own @

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

doSomething final : ()(input : MyType *) = {
    // ...
}

func final : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the
        // context's allocator
        value1 : MyType * own @

        // the @ operator allocates `value2` dynamically with the
        // context's allocator (which is verbosely expressed but is
        // functionally equivalent to `value1`'s allocation)
        value1 : MyType * own @ ___.allocator

        doSomething(value1)

        // `value1` is no longer referenced and the type is destroyed
        // and the memory deallocated
    }

    return "I'll be back."
}
````


### `own` overhead and control blocks

An `own` pointer contains a pointer to an instance of a type and a pointer to the control block. When a type is allocated for storage in a `own` pointer, a control block is typically reserved as part of the allocation of the type.

A small implementation detail, a `strong`, `weak`, `handle` `hint`, and `own` control blocks will likely all be contained within a union of all their control blocks (with some reserved space to align values) thus ensuring that each control block can be quick casted into another control block with minimal overhead and without needing reallocation of the control blocks during the conversion process.

An example `own` pointer content and control block:

````zax
/*
OwnPointerControlBlock$(Type) :: type {
    [[reserve=sizeof Atomic$(Integer)]]
    [[reserve=sizeof Atomic$(Integer)]]
    allocator : Allocator *
    destructor : ()() *
    deallocateType : Unknown *
    deallocateControl : Unknown *
    type : :: union {
        type : $Type
    }
}

OwnPointerContents$(Type) :: type {
    instance : $Type *
    control : OwnPointerControlBlock$($Type) *
}
*/
````
