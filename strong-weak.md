
# [Zax Programming Language](index.md)

## Strong and Weak Pointers

Pointers qualified as `strong` will automatically track the lifetime of an allocated `type` instance by performing [reference counting](https://en.wikipedia.org/wiki/Reference_counting) so when the last common reference to a `type`'s instance is discarded, the instance becomes deallocated.

The `strong` pointers are a form of [smart pointer](https://en.wikipedia.org/wiki/Smart_pointer) logic as a tool to ensure the lifetime of an `type`'s instance is destroyed when the last referencing pointer is discarded. A [`weak` reference](https://en.wikipedia.org/wiki/Weak_reference) can be used to prevent circular dependencies by detecting when a `type`'s instance kept is  alive by a `strong` pointer or when a `type` instance was already destroyed. The usage of `weak` references is a common technique to prevent memory leakage issue as `strong` pointers are not automatically [garbage collected](https://en.wikipedia.org/wiki/Garbage_collection_(computer_science)).

Thread safety for the reference counting mechanism for `strong` and `weak` pointers is guaranteed. When a `strong` pointer is assigned to new variables in a concurrency conflicts, the reference counting mechanism does not require thread barriers to ensure a reference count is kept accurate. The lifetime is maintained using atomic operations and concurrency of a lifetime is kept accurate across threads. However, this does not imply accessing contents inside a `strong` pointer has any concurrency protection or safety guarantees. If two threads modify contents inside a `type`'s instance pointed to by a `strong` pointer at the same time, then those contents can have concurrency issues as well as undefined behaviors.


### `strong` versus `handle` pointers

Pointers qualified as `strong` operate in the same manner as [`handle` pointers](handle-hint.md) with a few key differences. Whereas `strong` pointers have a `weak` counterpart, `handle` pointers have a `hint` pointer counterpart. Pointers qualified as `strong` have thread safety properties related to reference counting mechanism of a `type`'s instance whereas `handle` pointers do not.

Due to additional overhead related to thread safety guarantees for `strong` pointers, `handle` pointers are more efficient at the cost of thread concurrency safety.


### Allocation of `strong` pointers

Pointers qualified as `strong` are allocated in similar manners to other pointers, such as `own`, `discard`, `handle`, and `collect` pointers. The difference is that `strong` pointers can be co-owned by more than one variable. When the last variable holding a `strong` pointer is discarded (or reset to point to nothing) the allocated `type` instance is destructed and deallocated.

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

printIfValidPointer final : ()(pointerToValue : MyType *) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func final : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the context's
        // allocator and a `strong` pointer is maintained
        value1 : MyType * strong @

        // `value2` is is a `strong` pointer but the value is initialized to
        // point to nothing
        value2 : MyType * strong

        // both `value2` and `value1` have a `strong` pointer to the same
        // `MyType` instance
        value2 = value1

        printIfValidPointer(value1) // will print "true"
        printIfValidPointer(value2) // will print "true"

        assert(value1 == value2)

        // `value2` and `value1` pointers both fall out of scope but only a
        // single `MyType` instance is destructed and deallocated
    }

    return "I'll be back."
}
````


### `strong` pointer value replacement

Pointers qualified as `strong` can only point to a single instance of a `type`. If a pointer is reset to point to a new instance of a `type` then the original ownership claim is released and if the value was the last owner of a `type`'s instance then the `type` instance is discarded and the memory is deallocated.

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

printIfValidPointer final : ()(pointerToValue : MyType *) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func final : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the context's
        // allocator and a `strong` pointer is maintained
        value1 : MyType * strong @

        // the @ operator allocates `value2` dynamically with the context's
        // allocator and a `strong` pointer is maintained to another `MyType`
        // instance
        value2 : MyType * strong @

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


### `strong` pointers lifetime

The lifetime of `strong` pointers is entirely dependent on all `strong` pointers pointing to the same instance of a `type` being discarded (or reset to point to nothing). Only when the final `strong` pointer is discarded or reset will a shared instance of a `type` be released.

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

printIfValidPointer final : ()(pointerToValue : MyType *) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func final : (
    // `stayingAlive` is a `strong` pointer defaulted to point to nothing
    stayingAlive : MyType * strong
)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the context's
        // allocator and a `strong` pointer is maintained
        value1 : MyType * strong @

        // the @ operator allocates `value2` dynamically with the context's
        // allocator and a `strong` pointer is maintained to another `MyType`
        // instance
        value2 : MyType * strong @

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
    // the `strong` pointer returned by `func` is now being kept alive by
    // `liveAndLetDie`
    liveAndLetDie := func()

    printIfValidPointer(liveAndLetDie)  // will print "true"

    // reset `liveAndLetDie` which causes the lifetime originally allocated in
    // `value2` of `func` function to be destructed and deallocated
    liveAndLetDie = #:

    printIfValidPointer(liveAndLetDie)  // will print "false"
}
````


### Breaking circular `strong` pointer lifetime

Care must be used when dealing with `strong` pointer lifetimes to ensure a circular dependency is not created where a circular chain is never broken. If a `strong` pointer points to another `strong` pointer that directly or indirectly points back to the original type's instance then the lifetime of the `strong` pointers in the chain will never become destructed nor deallocated.

The example below creates a circular `strong` pointer chain:

````zax
print final : ()(...) = {
    // ...
}

assert final : ()(check : Boolean) = {
    // ...
}

MyType :: type {
    head : MyType * strong
    next : MyType * strong

    myValue1 : Integer
    myValue2 : String

    +++ final : ()() = {
        print("This will be displayed three times in this example.")
    }

    --- final : ()() = {
        print("BAD NEWS! This will never be displayed in this example!!!")
    }
}

printIfValidPointer final : ()(pointerToValue : MyType *) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func final : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the context's
        // allocator and a `strong` pointer is maintained
        value1 : MyType * strong @

        // the @ operator allocates `value2` dynamically with the context's
        // allocator and a `strong` pointer is maintained to another `MyType` 
        // instance
        value2 : MyType * strong @

        // the @ operator allocates `value3` dynamically with the
        // context's allocator and a `strong` pointer is maintained to
        // another `MyType` instance
        value3 : MyType * strong @

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

        // reset all three `strong` pointers to point to nothing
        value1 = #:
        value2 = #:
        value3 = #:

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

        // all of the following circular `strong` pointer prevent
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

The example below creates a circular `strong` pointer chain and manually fixes the issue:

````zax
print final : ()(...) = {
    // ...
}

assert final : ()(check : Boolean) = {
    // ...
}

MyType :: type {
    head : MyType * strong
    next : MyType * strong

    myValue1 : Integer
    myValue2 : String

    +++ final : ()() = {
        print("This will be displayed three times in this example.")
    }

    --- final : ()() = {
        print("GOOD NEWS! This will be called three times in this example.")
    }
}

printIfValidPointer final : ()(pointerToValue : MyType *) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func final : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the context's
        // allocator and a `strong` pointer is maintained
        value1 : MyType * strong @

        // the @ operator allocates `value2` dynamically with the context's
        // allocator and a `strong` pointer is maintained to another `MyType`
        // instance
        value2 : MyType * strong @

        // the @ operator allocates `value3` dynamically with the context's
        // allocator and a `strong` pointer is maintained to another `MyType`
        // instance
        value3 : MyType * strong @

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
        value1.head = #
        value2.head = #
        value3.head = #

        // reset all three `strong` pointers to point to nothing
        value1 = #  // `value1`'s instance will be destructed/deallocated (which
                    // removes the strong pointer to `value2`)
        value2 = #  // `value2`'s instance will be destructed/deallocated (which
                    // removes the strong pointer to `value3`)
        value3 = #  // `value3`'s instance will be destructed/deallocated

        // validate the pointers are indeed pointing to nothing
        assert(!value1)
        assert(!value2)
        assert(!value3)
    }

    return "I'll be back."
}
````


### Using `weak` pointers to break a chain

Pointers qualified as `weak` will only contain a valid pointer to a type's instance so long as the original allocated type is not destructed/deallocated. In other words, `weak` points will not extend the lifetime of a `strong` pointer beyond the last `strong` pointer keeping a type's instance alive.

````zax
print final : ()(...) = {
    // ...
}

assert final : ()(check : Boolean) = {
    // ...
}

MyType :: type {
    head : MyType * weak    // head will not be the lifetime of whatever it
                            // points to alive
    next : MyType * strong  // next will keep whatever it points to alive

    myValue1 : Integer
    myValue2 : String

    +++ final : ()() = {
        print("This will be displayed three times in this example.")
    }

    --- final : ()() = {
        print("GOOD NEWS! This will be called three times in this example.")
    }
}

printIfValidPointer final : ()(pointerToValue : MyType *) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func final : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the context's
        // allocator and a `strong` pointer is maintained
        value1 : MyType * strong @

        // the @ operator allocates `value2` dynamically with the context's
        // allocator and a `strong` pointer is maintained to another `MyType`
        // instance
        value2 : MyType * strong @

        // the @ operator allocates `value3` dynamically with the context's
        // allocator and a `strong` pointer is maintained to another `MyType`
        // instance
        value3 : MyType * strong @

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

        // obtain a `strong` pointer to the head type
        headPointer := value3.head as strong

        printIfValidPointer(headPointer) // will print "true"

        // reset all three `strong` pointers to point to nothing
        value1 = #:
        value2 = #:
        value3 = #:

        // validate the pointers are indeed pointing to nothing
        assert(!value1)
        assert(!value2)
        assert(!value3)

        // validate the original three instances are still alive
        printIfValidPointer(headPointer)            // will print "true"
        printIfValidPointer(headPointer.next)       // will print "true"
        printIfValidPointer(headPointer.next.next)  // will print "true"

        // take the `head` pointer of the `next` pointed type
        copyOfWeakHead : MyType * weak = headPointer.next.head

        // the `MyType` instance pointed to by the `weak` pointer is still
        // alive thus converting the `weak` point to a `strong` pointer will
        // obtain a `strong` pointer reference to the original type's instance
        printIfValidPointer(copyOfWeakHead as strong)   // will print "true"

        // the `strong` pointer both point to the same head type's instance
        assert((copyOfWeakHead as strong) == headPointer)

        // reset the head pointer to point to nothing (which is the only
        // pointer keeping all three `MyType` instances alive thus all three
        // `MyType` instances are destructed/deallocated)
        headPointer = #:

        // the `MyType` instance originally pointed to by the copy of the
        // `weak` pointer to the head type's instance is now
        // destructed/deallocated thus a `strong` pointer obtained from the
        // `weak` pointer now points to nothing
        printIfValidPointer(copyOfWeakHead as strong)   // will print "false"

        // all instance of `MyTpe` are already destructed thus nothing further
        // occurs when all the values fall out of scope
    }

    return "I'll be back."
}
````


### Transferring `own` pointers to `strong` pointers

Pointers qualified as `own` can be transferred to pointers qualified as `strong`. Once a transfer is completed, the original `own` pointer will point to nothing as the `strong` pointer will track the lifetime of the instance. Likewise, pointers qualified as `strong` can be transferred to pointers qualified as `own` on the condition that no other pointers qualified as `strong` points to the same `type`'s instance otherwise the resulting `own` pointer will point to nothing.

Caution: care must be taken when transferring an allocated `own` pointer to a `strong` pointer. Pointers qualified as `own` are allocated using the standard allocator which is typically set to the sequential allocator by default. If an `own` pointer gets transferred later into a `strong` pointer, replacing the standard allocator with the parallel allocator may be desired.

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

printIfValidPointer final : ()(pointerToValue : MyType *) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func final : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the context's
        // allocator and a `strong` pointer is maintained
        value1 : MyType * own @

        // `value2` is is a `strong` pointer but the value is initialized to
        // point to nothing
        value2 : MyType * strong

        // `value1` used to `own` the instance but the instance ownership is
        // transferred from a `value1` to `value2` which now keeps the
        // `MyType` instance alive
        value2 = value1

        printIfValidPointer(value1) // will print "false"
        printIfValidPointer(value2) // will print "true"

        assert(value1 != value2)

        // transferring a `strong` pointer to an `own` pointer can be
        // done so long as no other `strong` pointers exist to the same
        // instance (otherwise `value1` would point to nothing)
        value1 = value2

        // `value2`'s `strong` pointer was automatically reset when ownership
        // was exclusively taken over by `value1`
        assert(!value2)

        printIfValidPointer(value1) // will print "true"
        printIfValidPointer(value2) // will print "false"

        assert(value1 != value2)


        // `value3` is is a `strong` pointer but the value is initialized to
        // point to nothing
        value3 : MyType * strong

        // once again transfer ownership from `value1` to `value2`
        value2 = value1

        printIfValidPointer(value1) // will print "false"
        printIfValidPointer(value2) // will print "true"
        printIfValidPointer(value3) // will print "false"

        // `value3` is a `strong` point but ownership in `value1` was already
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

        // `value1` cannot retake ownership from `value2` as another pointer
        // to the same instance of `value2` exists thus `value1` cannot take
        // exclusive ownership and `value1` will point to nothing
        value1 = value2

        printIfValidPointer(value1) // will print "false"
        printIfValidPointer(value2) // will print "true"
        printIfValidPointer(value3) // will print "true"
    }

    return "I'll be back."
}
````


### Transferring `strong` pointers to `handle` pointers

Pointers qualified as `strong` cannot be directly transferred to a pointer qualified as `handle`. The only method by which this transfer can happen is if a `strong` pointer is first transferred to an `own` pointer and then transferred to a `handle` pointer. The vice versa limitation is also true. Pointers qualified as `handle` cannot be directly transferred to a pointer qualified as `strong`. The only method by which this transfer can happen is if a `handle` pointer is first transferred to an `own` pointer and then transferred to a `strong` pointer.

Transferring to an `own` pointer have limitations. Only if a pointer qualified as `strong` or `handle` is the exclusive reference to the instance of a type can the transfer to an `own` pointer occur.


### Casting contained variables into `strong` pointers

#### Converting from a container `strong` pointer to a contained `strong` pointer

The `lifetimeof` operators can be used to cast a raw pointer to a variable which has a lifetime tied to an original `strong` or `handle` pointer.

A `strong` pointer to a type's instance may contain other types within the instance that share a common lifetime. While the lifetime of these contained types are the same as the container type, only a `strong` pointer to the container type may exist (despite both types being considered as a single instance). The `lifetimeof` operator is especially useful to create a `strong` pointer of a contained type from a `strong` pointer to a container's type.

Example as follows:

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

myType : MyType * strong @

// create a pointer to `value` and link the lifetime of `myType` to the pointer
value : Integer * strong = myType.value1 lifetimeof myType

// resetting the `myType`'s `strong` pointer will not impact the real lifetime
// of the instance connected to `myType` (as the `value` `strong` pointer will
// keep it's container instance alive)
myType = #

// set the `MyType::value1` to `5` (which is still valid as the
// lifetime of of the original `strong` pointer is kept alive)
value. = 5
````


#### Converting from a container `strong` pointer to a contained `strong` pointer

While any pointer to any type can be linked to a `strong` or `handle` pointer using an `unsafe lifetimeof` operator, a `lifetimeof` operator can link a pointer to a contained type back to the original `strong` or `handle` pointer safely by verifying a pointer refers to a memory addresses within the bounds of the allocated `strong` or `handle` pointer.

An example of a runtime `lifetimeof` being applied onto a `strong` pointer:

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

doSomething final : ()(a : A * strong) = {
    // probe `a` to see if it is indeed within a `B` type and if so then
    // return a pointer to a B type and create a `strong` pointer from `a`
    b := (a outerof B *) lifetimeof a
}

function final : ()() = {
    value : B * strong @
    value.a.foo = 1
    value.bar = 2

    // link a `strong` pointer to `a` from `value`
    a : A * strong = value.a lifetimeof value

    doSomething(a)
}
````


#### `lifetimeof` versus `unsafe lifetimeof`

The exclusive difference between these operators is safety. An `unsafe lifetimeof` operator will force a conversion of any raw pointer to link to `handle` or `strong` pointer even for unrelated pointers. A `lifetimeof` operator will validate a raw pointer actually points inside the address boundaries of the `handle` or `strong` pointer. If a pointer to memory is not located in the correct allocation boundary then `lifetimeof` will return a pointer to nothing. Thus a programmer can choose their tradeoff between safety and speed.


#### Converting using `unsafe lifetimeof`

Any pointer to any type can be linked to a `handle` or `strong` pointer using an `unsafe lifetimeof` operator. The memory is not checked to see if the memory address being casted resides within the memory space of the original `handle` or `strong` pointer. The programmer must be careful to use this feature carefully since this function will forcefully adopt the lifetime of a `handle` or `strong` pointer.

An example of a runtime `unsafe lifetimeof` being applied onto a `handle` pointer:

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

doSomething final : (result : Unknown * strong)(a : A * strong, unknown : Unknown *) = {
    // forcefully convert from the lifetime of one pointer to another type
    // without conducting any safety checks to ensure the casted pointer lives
    // within the memory range of the original allocation
    b := unknown unsafe lifetimeof a
    assert(b)

    // ...
    return b
}

function final : ()() = {
    value : B * handle @
    value.a.foo = 1
    value.bar = 2

    // link a `strong` pointer to `a` from `value`
    a : A * strong = value.a lifetimeof value

    doSomething(a, value)
}
````


### `strong` and `weak` overhead and control blocks

A `strong` and `weak` pointer contains a pointer to an instance of a type and a pointer to the control block. When a type is allocated for storage in a `strong` pointer, a control block is typically reserved with the allocation of a `type`'s instance.

Since `weak` pointers also need control blocks, `weak` pointers can keep a memory control block alive and possibly a `type`'s reserved memory too if a control block and `type` are allocated within the same memory block. To clean up allocated memory, reset any `weak` pointers to point to nothing after all `strong` pointers are gone.

An example `strong` / `weak` pointer content and control block:

````zax
/*
StrongPointerControlBlock$(Type) :: type {
    strongCount : Atomic$(Integer)
    weakCount : Atomic$(Integer)
    destructor : ()() *
    deallocateType : Unknown *
    deallocateControl : Unknown *
    type : :: union {
        type : $Type
    }
}

StrongPointerContent$(Type) :: type {
    instance : $Type *
    control : StrongPointerControlBlock$($Type) *
}
*/
````
