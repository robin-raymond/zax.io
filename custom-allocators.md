
# [Zax Programming Language](index.md)

## Custom Allocators

### Overview

Custom allocators can be used to allocate and deallocate types in custom [allocation arenas](https://en.wikipedia.org/wiki/Region-based_memory_management). This allows for features where partition regions of memory can be used to hold storage for types and then be disposed when the memory is no longer required (and the types allocated within the region are known to be fully destructed).

The allows for a few key advantages:
* Locality of data (to ensure common data is grouped together in RAM which can lead to less CPU cache misses)
* Block deallocation (temporary allocation can be used and disposed of in chunks instead of going to the heap for each allocation)
* Single threaded allocations/deallocations without thread safety concerns (for less thread contention)

A few caveats must be considered:
* Undefined behavior can occur if non-deallocated previously allocated types are access after the memory region is discarded from within the custom allocator 
* If allocation counting is maintained, the system should call a panic if outstanding allocations have not been deallocated
* If pointers defined as `collect` are used, the destructors of those pointers must be called in reverse order of their allocation prior to the memory region disposal
* If a fixed size memory regions are reserved, care must be taken that the allocations can exceed the capacity of the allocator
* If a non-thead aware allocator is passed to a different thread, this can lead to undefined behavior
* If the deallocation function is converted to a ["NOOP"](https://en.wikipedia.org/wiki/NOP_(code)) outstanding allocations may continue to grow beyond the capacity of the custom allocator if the allocator is requested to allocate more types or larger sized types beyond its intended maximum capacity

A programmer can use custom allocators to trade off speed, data locality and block memory disposal against the caveats as they wish.


### Custom allocator example

````zax
:: import Module.System.Allocators

/*

Intrinsic type defined in System.Allocators (and subject to change):

Allocator :: type {
    Allocation :: type {
        sizeInBytes : TypeSize      // how many bytes to allocate
        byteAlignment : TypeSize    // what is the requested alignment for the
                                    // allocation

        destructor : ()(type : Unknown*)*   // function to call if the
                                            // allocator must call the 
                                            // destructor upon deallocation
    }

    allocate : (pointer : Unknown*)(properties : Allocation) = {
        //...
    }

    deallocate : ()(pointer : Unknown*) = {
        //...
    }
}

*/

MyAllocator :: type {

    // An `own` instance that is not a pointer has different meaning than
    // an `own` pointer. All the type's values inside the `own`
    // type are directly accessible as if they were defined directly
    // inside the type that `own'`s` the contained type.
    contains private own : Allocator

    // The `allocate` function already exists within the system `Allocate`
    // type thus the `override` keyword must be used to indicate the original
    // type now has a new definition. The type is assumed to be the same
    // type as the originally declared type, unless polymorphic functions exist
    // which would require a re-declaration of the original type to
    // disambiguate polymorphic functions.
    allocate override := {
        // access variable named `properties` to determine what allocation
        // operations to perform and return a `Unknown*` to the allocated memory
    }

    deallocate override := {
        // release the memory allocated by `pointer`
    }
}

MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

doSomething : ()(myType : MyType*) = {
    //...
}

func : ()() = {
    allocator : MyAllocator     // create an instance of the custom allocator

    myType : MyType @ allocator // allocate using the custom allocator

    doSomething(myType)

    // the `myType` variable will fall out of scope and be automatically
    // destructed and deallocated
}
````


### Replacing the context allocator

The context variable which is passed into all function with the reserve triple underscore named variable `___` holds a pointer to an instance of the allocator type. The standard allocator (`___.allocator`), sequential allocator (`___.sequential.allocator`), and parallel allocator (`___.parallel.allocator`) can all be replaced with new allocators. The standard allocator (`___.allocator`) usually is set to the sequential allocator (`___.sequential.allocator`). However, when the parallel allocator operator (`@@`) is used, the standard allocator is replaced with the parallel allocator temporarily.

This context pointer can be changed to point to a custom allocator thus every allocation that is performed against the default allocators will obtain the allocator to use from the context `___` type and use those allocator functions to allocate or deallocate memory as needed.

````zax
:: import Module.System.Allocators

MyAllocator :: type {
    //...
}

MyType :: type {
    myValue1 : Integer
    myValue2 : String 
}

doSomethingElse : ()(myType : MyType*) = {
    //...
}

doSomething : ()(myType : MyType*) = {
    //...

    // allocate `myOtherType` using the allocator contained within the context
    // `___` automatically (which in this example points to a custom allocator)
    myOtherType : MyType @ = myType.

    doSomethingElse(myOtherType)

    // `myOtherType` will fall out of scope and be automatically
    // destructed and deallocated
}

func : ()() = {
    allocator : MyAllocator         // create an instance of the custom allocator

    // remember the pointers to the old allocator
    oldAllocator := ___.allocator
    oldSequentialAllocator := ___.sequential.allocator

    // change the context's allocators
    ___.allocator = allocator       
    ___.sequential.allocator = allocator

    myType : MyType @               // allocate using a custom allocator by
                                    // using the standard allocator operator `@`
                                    // (without the custom allocator needing
                                    // to be specified since it is obtained from
                                    // the context `___` automatically)

    doSomething(myType)

    // reset back the previous context allocators
    ___.allocator = oldAllocator
    ___.sequential.allocator = oldSequentialAllocator

    // `myType` will fall out of scope and be automatically
    // destructed and deallocated
}
````


### Manual allocation and deallocation

Memory for types can be allocated, constructed, destructed and deallocated entirely manually. This can be useful for creating allocated types which are created emplace in memory locations where using the language allocator method is not desired.

````zax
MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

allocate : (pointer : Unknown*)(bytes : TypeSize, alignment : TypeSize) = {
    //...
}

deallocate : ()(pointer : Unknown*) = {
    //...
}

func : ()() = {
    pointer = allocate(sizeof MyType, alignof MyType)
    
    // convert the raw pointer
    myType : MyType* = pointer cast MyType*
    
    // construct the type (if required)
    myType.+++()

    // ... insert code here ...

    // destruct the the type (if require)
    myType.---()

    deallocate(pointer)
}
````
