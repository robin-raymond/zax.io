
# [Zax Programming Language](index.md)

## Handle Pointers

Pointers marked as `handle` will automatically track the lifetime of an allocated type by performing [reference counting](https://en.wikipedia.org/wiki/Reference_counting) so when the last common reference to a type's instance is discarded, the type becomes deallocated.

The `handle` pointers are a form of [smart pointer](https://en.wikipedia.org/wiki/Smart_pointer) logic as a tool to ensure the lifetime of an type's instance is destroyed when the last referencing pointer is discarded. A [`hint` reference](https://en.wikipedia.org/wiki/Weak_reference) can be used to prevent circular dependencies by detecting when a type's instance is kept alive by a `handle` pointer or already destroyed. The usage of `hint` references is a common technique to prevent memory leakage issue as `handle` pointers are not automatically [garbage collected](https://en.wikipedia.org/wiki/Garbage_collection_(computer_science)).


Thread safety is not addressed with `handle` or `hint` pointers and `handle` pointers should not be used across thread boundaries unless ever time a `handle` pointer is reassigned to another `handle` pointer or transferred to an `own` pointer a thread barrier is place around the assignment. Alternatively a `deep` copy can be performed on a `handle` to ensure the contents are copied and allocated using parallel allocators.


### `handle` versus `strong` pointers

Pointers marked as `handle` operate in the same manner as [`strong` pointers](strong-weak.md) with a few key differences. Whereas `strong` pointers have a `weak` counterpart, `handle` pointers have a `hint` counterpart. Pointers marked as `strong` have thread safety properties related to the lifetime of the instance whereas `handle` pointers do not.

Due to the overhead because of thread safety guarantees for `strong` pointers, `handle` pointers are more efficient at the cost of thread safety.


### Allocation of `handle` pointers

Pointers marked as `handle` are allocated in similar manners to other pointers, such as `own`, `discard`, `strong`, `discard`, and `collect` pointers. The difference is that `handle` pointers can be co-owned by more than one variable. When the last variable holding the `handle` pointer is discarded (or reset to empty) the allocated type is destructed, and deallocated (unless a `hint` pointer still exists to a combined control and allocation block).

````zax
print final : ()(...) = {
    // ...
}

assert final : ()(check : Boolean) = {
    // ...
}

MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

printIfValidPointer final : ()(pointerToValue : MyType*) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func final : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the
        // context's allocator and a `handle` pointer is maintained
        value1 : MyType* handle @

        // `value2` is is a `handle` pointer but the value is initialized to
        // point to nothing
        value2 : MyType* handle

        // both `value2` and `value1` have a handle pointer to the same
        // `MyType` instance
        value2 = value1

        printIfValidPointer(value1) // will print "true"
        printIfValidPointer(value2) // will print "true"

        assert(value1 == value2)

        // `value2` and `value1` pointers both fall out of scope but only
        // the single `MyType` instance is destructed and deallocated
    }

    return "I'll be back."
}
````


### `handle` pointer value replacement

Pointers marked as `handle` can only point to a single instance of a type. If the pointer is reset to point to a new instance of a type then the original ownership claim is released and if the value was the last owner of the type's instance then the type is discarded and the memory is deallocated.

````zax
print final : ()(...) = {
    // ...
}

assert final : ()(check : Boolean) = {
    // ...
}

MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

printIfValidPointer final : ()(pointerToValue : MyType*) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func final : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the
        // context's allocator and a `handle` pointer is maintained
        value1 : MyType* handle @

        // the @ operator allocates `value2` dynamically with the
        // context's allocator and a `handle` pointer is maintained to
        // another `MyType` instance
        value2 : MyType* handle @

        printIfValidPointer(value1) // will print "true"
        printIfValidPointer(value2) // will print "true"

        // `value1` and `value2` point to different `MyType` instances
        assert(value1 != value2)

        // `value2`'s value is replaced, thus `MyType` instance being `own`
        // in `value2` is destructed and deallocated and then both
        // `value1` and `value2` point to the same `MyType` instance
        value2 = value1

        printIfValidPointer(value1) // will print "true"
        printIfValidPointer(value2) // will print "true"

        // `value1` and `value2` point to the same `MyType` instances
        assert(value1 == value2)

        // `value2` and `value1` pointers both fall out of scope but only
        // the single `MyType` instance originally allocated in `value1` is
        // destructed and deallocated
    }

    return "I'll be back."
}
````


### `handle` pointers lifetime

The lifetime of `handle` pointers is entirely dependent on all `handle` pointers pointing to the same instance of a type being discarded (or reset to point to empty). Only when the final `handle` pointer is discarded/reset will be pointed instance of a type be released.

````zax
print final : ()(...) = {
    // ...
}

assert final : ()(check : Boolean) = {
    // ...
}

MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

printIfValidPointer final : ()(pointerToValue : MyType*) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func final : (
    // `stayingAlive` is a `handle` pointer defaulted to point to nothing
    stayingAlive : MyType* handle
)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the
        // context's allocator and a `handle` pointer is maintained
        value1 : MyType* handle @

        // the @ operator allocates `value2` dynamically with the
        // context's allocator and a `handle` pointer is maintained to
        // another `MyType` instance
        value2 : MyType* handle @

        // `stayingAlive` will point to `value2` thus they will both point to
        // the same `MyType` instance
        stayingAlive = value2

        printIfValidPointer(value1)         // will print "true"
        printIfValidPointer(value2)         // will print "true"
        printIfValidPointer(stayingAlive)   // will print "true"

        // `value1` and `value2` point to different `MyType` instances
        assert(value1 != value2)

        // `value2` and `stayingAlive` point to the same `MyType` instance
        assert(stayingAlive == value2)

        // `value2`'s value is replaced, thus `MyType` instance being `own`
        // in `value2` but the original type's instance in `value2` is not
        // destructed or deallocated as `stayingAlive` will keep the
        // type originally allocated in `value2` alive.
        value2 = value1

        printIfValidPointer(value1)         // will print "true"
        printIfValidPointer(value2)         // will print "true"
        printIfValidPointer(stayingAlive)   // will print "true"

        // `value1` and `value2` point to the same `MyType` instances
        assert(value1 == value2)

        // `stayingAlive` and `value2` point to different `MyType` instances
        assert(stayingAlive != value2)

        // `value2` and `value1` pointers both fall out of scope but only
        // the single `MyType` instance originally allocated in `value1` is
        // destructed and deallocated
    }

    // `stayingAlive` is returned to the caller and thus will not fall out
    // fall out of scope and the type originally allocated in `value2` is
    // kept alive beyond the lifetime of this function call
    return
}

func2 final : ()() = {
    // the `handle` pointer returned by `func` is now being kept alive by
    // `liveAndLetDie`
    liveAndLetDie := func()

    printIfValidPointer(liveAndLetDie)  // will print "true"

    // reset `liveAndLetDie` which causes the lifetime originally allocated in
    // `value2` of `func` function to be destructed and deallocated
    liveAndLetDie =:

    printIfValidPointer(liveAndLetDie)  // will print "false"
}
````


### Breaking circular `handle` pointer lifetime

Care must be used when dealing with `handle` pointer lifetimes to ensure a circular dependency is not created where the circular chain is never broken. If a `handle` pointer points to another `handle` pointer that directly or indirectly points back to the same type's instance the lifetime of the `handle` pointers in the chain will never become destructed nor deallocated.

The example below creates a circular `handle` pointer chain:

````zax
print final : ()(...) = {
    // ...
}

assert final : ()(check : Boolean) = {
    // ...
}

MyType :: type {
    head : MyType* handle
    next : MyType* handle

    myValue1 : Integer
    myValue2 : String

    +++ final : ()() = {
        print("This will be displayed three times in this example.")
    }

    --- final : ()() = {
        print("BAD NEWS! This will never be displayed in this example!!!")
    }
}

printIfValidPointer final : ()(pointerToValue : MyType*) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func final : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the
        // context's allocator and a `handle` pointer is maintained
        value1 : MyType* handle @

        // the @ operator allocates `value2` dynamically with the
        // context's allocator and a `handle` pointer is maintained to
        // another `MyType` instance
        value2 : MyType* handle @

        // the @ operator allocates `value3` dynamically with the
        // context's allocator and a `handle` pointer is maintained to
        // another `MyType` instance
        value3 : MyType* handle @

        // setup pointers to the head of the linked list
        value1.head = value1
        value2.head = value1
        value3.head = value1

        // setup points to the linked list chain
        value1.next = value2
        value2.next = value3

        printIfValidPointer(value1) // will print "true"
        printIfValidPointer(value2) // will print "true"
        printIfValidPointer(value3) // will print "true"

        // reset all three `handle` pointers to point to nothing
        value1 =:
        value2 =:
        value3 =:

        // validate the pointers are indeed pointing to nothing
        assert(!value1)
        assert(!value2)
        assert(!value3)

        // despite `value1`, `value2`, and `value3` being reset and falling
        // out of scope, the original constructed `value1`, `value2`, and
        // `value3` type instances will never be destroyed because they are all
        // keeping their lifetimes alive by pointing to each other in a
        // circular pointer loop

        // `value1` points to itself and `value2`' instance
        // `value2` points to `value1`'s instance and `value3`'s instance
        // `value3` points to `value1`'s instance

        // all of the following circular `handle` pointer prevent
        // `MyType` destruction/deallocation
        // 1 points to 1 (itself)
        // 1 points to 2 points to 1 again
        // 1 points to 2 points to 3 points to 1 again
        // 2 points to 3 which points to 1 which points to 2 again
        // 3 points to 1 which points to 2 which points to 3 again
    }

    return "I'll be back."
}
````

The example below creates a circular `handle` pointer chain and manually fixes the issue:

````zax
print final : ()(...) = {
    // ...
}

assert final : ()(check : Boolean) = {
    // ...
}

MyType :: type {
    head : MyType* handle
    next : MyType* handle

    myValue1 : Integer
    myValue2 : String

    +++ final : ()() = {
        print("This will be displayed three times in this example.")
    }

    --- final : ()() = {
        print("GOOD NEWS! This will be called three times in this example.")
    }
}

printIfValidPointer final : ()(pointerToValue : MyType*) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func final : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the
        // context's allocator and a `handle` pointer is maintained
        value1 : MyType* handle @

        // the @ operator allocates `value2` dynamically with the
        // context's allocator and a `handle` pointer is maintained to
        // another `MyType` instance
        value2 : MyType* handle @

        // the @ operator allocates `value3` dynamically with the
        // context's allocator and a `handle` pointer is maintained to
        // another `MyType` instance
        value3 : MyType* handle @

        // setup pointers to the head of the linked list
        value1.head = value1
        value2.head = value1
        value3.head = value1

        // setup points to the linked list chain
        value1.next = value2
        value2.next = value3

        printIfValidPointer(value1) // will print "true"
        printIfValidPointer(value2) // will print "true"
        printIfValidPointer(value3) // will print "true"

        // break apart any way the pointer relationships could be circular
        value1.head =:
        value2.head =:
        value3.head =:

        // reset all three `handle` pointers to point to nothing
        value1 =:   // `value1`'s instance will be destructed/deallocated (which
                    // removes the handle pointer to `value2`)
        value2 =:   // `value2`'s instance will be destructed/deallocated (which
                    // removes the handle pointer to `value3`)
        value3 =:   // `value3`'s instance will be destructed/deallocated

        // validate the pointers are indeed pointing to nothing
        assert(!value1)
        assert(!value2)
        assert(!value3)
    }

    return "I'll be back."
}
````


### Using `hint` pointers to break a chain

Pointers marked as `hint` will only contain a valid pointer to a type's instance so long as the original allocated type is not destructed/deallocated. In other words, `hint` points will not extend the lifetime of a `handle` pointer beyond the last `handle` pointer keeping a type's instance alive.

````zax
print final : ()(...) = {
    // ...
}

assert final : ()(check : Boolean) = {
    // ...
}

MyType :: type {
    head : MyType* hint     // head will not be the lifetime of whatever it
                            // points to alive
    next : MyType* handle   // next will keep whatever it points to alive

    myValue1 : Integer
    myValue2 : String

    +++ final : ()() = {
        print("This will be displayed three times in this example.")
    }

    --- final : ()() = {
        print("GOOD NEWS! This will be called three times in this example.")
    }
}

printIfValidPointer final : ()(pointerToValue : MyType*) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func final : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the
        // context's allocator and a `handle` pointer is maintained
        value1 : MyType* handle @

        // the @ operator allocates `value2` dynamically with the
        // context's allocator and a `handle` pointer is maintained to
        // another `MyType` instance
        value2 : MyType* handle @

        // the @ operator allocates `value3` dynamically with the
        // context's allocator and a `handle` pointer is maintained to
        // another `MyType` instance
        value3 : MyType* handle @

        // setup pointers to the head of the linked list
        value1.head = value1
        value2.head = value1
        value3.head = value1

        // setup points to the linked list chain
        value1.next = value2
        value2.next = value3

        printIfValidPointer(value1) // will print "true"
        printIfValidPointer(value2) // will print "true"
        printIfValidPointer(value3) // will print "true"

        // obtain a `handle` pointer to the head type
        headPointer := value3.head as handle

        printIfValidPointer(headPointer) // will print "true"

        // reset all three `handle` pointers to point to nothing
        value1 =:
        value2 =:
        value3 =:

        // validate the pointers are indeed pointing to nothing
        assert(!value1)
        assert(!value2)
        assert(!value3)

        // validate the original three instances are still alive
        printIfValidPointer(headPointer)            // will print "true"
        printIfValidPointer(headPointer.next)       // will print "true"
        printIfValidPointer(headPointer.next.next)  // will print "true"

        // take the `head` pointer of the `next` pointed type
        copyOfWeakHead : MyType* hint = headPointer.next.head

        // the `MyType` instance pointed to by the `hint` pointer is still
        // alive thus converting the `hint` point to a `handle` pointer will
        // obtain a `handle` pointer reference to the original type's instance
        printIfValidPointer(copyOfWeakHead as handle)   // will print "true"

        // the `handle` pointer both point to the same head type's instance
        assert((copyOfWeakHead as handle) == headPointer)

        // reset the head pointer to point to nothing (which is the only
        // pointer keeping all three `MyType` instances alive thus all three
        // `MyType` instances are destructed/deallocated)
        headPointer =:

        // the `MyType` instance originally pointed to by the copy of the
        // `hint` pointer to the head type's instance is now
        // destructed/deallocated thus a `handle` pointer obtained from the
        // `hint` pointer now points to nothing
        printIfValidPointer(copyOfWeakHead as handle)   // will print "false"

        // all instance of `MyTpe` are already destructed thus nothing further
        // occurs when all the values fall out of scope
    }

    return "I'll be back."
}
````


### Transferring `own` pointers to `handle` pointers

Pointers marked as `own` can be transferred to pointers marked as `handle`. Once the transfer is completed, the original `own` pointer will point to nothing as the `handle` pointer will track the lifetime of the instance. Likewise, pointers marked as `handle` can be transferred to pointers marked as `own` on the condition that no other pointers marked as `handle` point to the same instance of a type otherwise  the resulting `handle` pointer will point to nothing.

````zax
print final : ()(...) = {
    // ...
}

assert final : ()(check : Boolean) = {
    // ...
}

MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

printIfValidPointer final : ()(pointerToValue : MyType*) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func final : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the
        // context's allocator and a `handle` pointer is maintained
        value1 : MyType* own @

        // `value2` is is a `handle` pointer but the value is initialized to
        // point to nothing
        value2 : MyType* handle

        // `value1` used to own the instance but the instance ownership is
        // transferred from a `value1` to `value2` which now keeps the
        // `MyType` instance alive
        value2 = value1

        printIfValidPointer(value1) // will print "false"
        printIfValidPointer(value2) // will print "true"

        assert(value1 != value2)

        // transferring a `handle` pointer to an `own` pointer can be
        // done so long as no other `handle` pointers exist to the same
        // instance (otherwise `value1` would point to nothing)
        value1 = value2

        // `value2`'s `handle` pointer was automatically reset when ownership
        // was exclusively taken over by `value1`
        assert(!value2)

        printIfValidPointer(value1) // will print "true"
        printIfValidPointer(value2) // will print "false"

        assert(value1 != value2)


        // `value3` is is a `handle` pointer but the value is initialized to
        // point to nothing
        value3 : MyType* handle

        // once again transfer ownership from `value1` to `value2`
        value2 = value1

        printIfValidPointer(value1) // will print "false"
        printIfValidPointer(value2) // will print "true"
        printIfValidPointer(value3) // will print "false"

        // `value3` is a `handle` point but ownership in `value1` was already
        // lost thus `value3` will point to nothing
        value3 = value1

        printIfValidPointer(value1) // will print "false"
        printIfValidPointer(value2) // will print "true"
        printIfValidPointer(value3) // will print "false"

        // `value2` and `value3` now point to the same allocated instance
        // originally allocated in `value1`
        value3 = value2

        printIfValidPointer(value1) // will print "false"
        printIfValidPointer(value2) // will print "true"
        printIfValidPointer(value3) // will print "true"

        assert(value1 != value2)
        assert(value2 == value3)


        // `value1` cannot retake ownership from `value2` as another
        // pointer to the same instance of `value2` exist thus `value1` cannot
        // take exclusive ownership and `value1` will point to nothing
        value1 = value2
    }

    return "I'll be back."
}
````


### Transferring `handle` pointers to `strong` pointers

Pointers marked as `handle` cannot be directly transferred to a pointer marked as `strong`. The only method by which this transfer can happen is if the `handle` pointer is first transferred to an `own` pointer and then transferred to a `strong` pointer. The vice versa limitation is true. Pointers marked as `strong` cannot be directly transferred to a pointer marked as `handle`. The only method by which this transfer can happen is if the `strong` pointer is first transferred to an `own` pointer and then transferred to a `handle` pointer.

Transferring to an `own` pointer has limitations. Only if the pointer marked as `handle` or `strong` is the exclusive reference to the instance of a type can the transfer to an `own` pointer occur.


#### Transferring `handle` pointers across threads

If a `handle` pointer will be transferred to a different thread, either a `deep` copy of the `handle` pointer should be performed or a thread safe allocator should be used to allocate the pointer. By default, `handle` pointers allocate using the standard allocators (i.e. sequential and thread unsafe). Allocation of a `handle` pointer in one thread and then deallocation of the pointer on a different thread may cause undefined behaviors.

While the standard allocators can be replaced with thread safe parallel allocators, the optimized thread-unaware allocators would be replaced by less efficient thread aware counterparts universally (which is often unneeded).


### Casting contained variables into `handle` pointers

#### Converting from a container `handle` pointer to a contained `handle` pointer

The `lifetimemorph` operators can be used to cast a raw pointer to a variable which has the same lifetime as an original `handle` or `strong` pointer to a `handle` or `strong` pointer respectively.

A `handle` pointer to a type's instance may contain other types within the instance that share a common lifetime. While the lifetime of these contained type is the same as the container type, only a `handle` pointer to the container type may exist (despite both types being considered as a single instance). The `lifetimemorph` operator is especially useful to create a `handle` pointer of a contained type from a `handle` pointer to the container's type.

Example as follows:

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

myType : MyType* handle @

// create a pointer to `value` and link the lifetime of `myType` to the pointer
value : Integer* handle = myType.value1 lifetimemorph myType

// resetting the `myType`'s `handle` pointer will not impact the real lifetime
// of the instance connected to `myType` (as the `value` `handle` pointer will
// keep it's container instance alive)
myType =:

// set the `MyType::value1` to `5` (which is still valid as the
// lifetime of of the original `handle` pointer is kept alive)
value. = 5
````


#### Converting from a container `handle` pointer to a contained `handle` pointer

While any pointer to any type can be linked to a `handle` or `strong` pointer using the `lifetimecast` operator, a `lifetimemorph` operator can link a pointer to a contained type back to the original `handle` or `strong` pointer safely by verifying the pointer refers to a memory addresses within the bounds of the allocated `handle` or `strong` pointer.

An example of a runtime `lifetimemorph` being applied onto a `handle` pointer:

````zax
A :: type managed {
    foo : Integer
}

B :: type {
    bar : Integer
    a : A
}

C :: type {
    weight : Double
}

doSomething final : ()(a : A* handle) = {
    // probe `a` to see if it is indeed within a `B` type and if so then
    // return a pointer to a B type and create a `handle` pointer from `a`
    b := (a outermorph B*) lifetimemorph a
}

function final : ()() = {
    value : B* handle @
    value.a.foo = 1
    value.bar = 2

    // link a `handle` pointer to `a` from `value`
    a : A* handle = value.a lifetimemorph value

    doSomething(a)
}
````


#### `lifetimemorph` versus `lifetimecast`

The exclusive difference between these operators is safety. The `lifetimecast` operator will force a conversion of any raw pointer to link to `handle` or `strong` pointer even for unrelated pointers. The `lifetimemorph` operator will validate the raw pointer actually points inside the address boundaries of the `handle` or `strong` pointer. If it does not then `lifetimemorph` will return a pointer to nothing.


### `handle` overhead and control blocks

A `handle` pointer contains a pointer to an instance of a type and a pointer to the control block. When a type is allocated for storage in a `handle` pointer, a control block is typically reserved as part of the allocation of the type.

An example `handle` pointer content and control block:

````zax
/*
HandlePointerControlBlock$(Type) :: type {
    strongCount : Integer
    [[reserve=(sizeof Integer atomic) - sizeof Integer]]
    weakCount : Integer
    [[reserve=(sizeof Integer atomic) - sizeof Integer]]
    allocator : Allocator*
    destructor : ()()*
    deallocateType : Unknown*
    deallocateControl : Unknown*
    type : :: union {
        type : $Type
    }
}

HandlePointerContents$(Type) :: type {
    instance : $Type *
    control : HandlePointerControlBlock$($Type)*
}
*/
````
