
# [Zax Programming Language](index.md)

## Strong and Weak Pointers

Pointers marked as `strong` will automatically track the lifetime of an allocated type by performing [reference counting](https://en.wikipedia.org/wiki/Reference_counting) so when the last common reference to a type's instance is discarded, the type becomes deallocated.

The `strong` pointers are a form of [smart pointer](https://en.wikipedia.org/wiki/Smart_pointer) logic as a tool to ensure the lifetime of an type's instance is destroyed when the last referencing pointer is discarded. A [`weak` reference](https://en.wikipedia.org/wiki/Weak_reference) can be used to prevent circular dependencies by detecting when a type's instance kept alive by a `strong` pointer is still alive or already destroyed. The usage of `weak` references is a common technique to prevent memory leakage issue as `strong` pointers are not automatically [garbage collected](https://en.wikipedia.org/wiki/Garbage_collection_(computer_science)).

Thread safety for the reference counting mechanism and `weak` pointer referencing is guaranteed. When a `strong` pointer is assigned to new variables across threads, the reference counting mechanism does not require thread barriers to ensure the count is kept accurate. Concurrency of the lifetime is kept accurate across threads. However, this does not imply accessing the contents of a `strong` pointer has any concurrency protection. If two threads modify the contents of type's instance pointed to by a `strong` pointer, the contents can have concurrency issues.


### `strong` versus `handle` pointers

Pointers marked as `strong` operate in the same manner as [`handle` pointers](handle.md) with a few key differences. Whereas `strong` pointers have a `weak` counterpart, `handle` pointers do not have `weak` pointers counterparts. Pointers marked as `strong` have thread safety properties related to the lifetime of the instance whereas `handle` pointers do not.

Due to the overhead because of thread safety guarantees for `strong` pointers, handle pointers are more efficient at the cost of thread safety.


### Allocation of `strong` pointers

Pointers marked as `strong` are allocated in similar manners to other pointers, such as `own`, `discard`, `handle`, and `collect` pointers. The difference is that `strong` pointers can be co-owned by more than one variable. When the last variable holding the `strong` pointer is discarded (or reset to empty) the allocated type is destructed and deallocated.

````zax
MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

print : ()(...) = {
    //...
}

assert : ()(check : Boolean) = {
    //...
}

printIfValidPointer : ()(pointerToValue : MyType*) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the
        // context's allocator and a `strong` pointer is maintained
        value1 : MyType* strong @

        // `value2` is is a `strong` pointer but the value is initialized to
        // point to nothing
        value2 : MyType* strong

        // both `value2` and `value1` have a strong pointer to the same
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


### `strong` pointer value replacement

Pointers marked as `strong` can only point to a single instance of a type. If the pointer is reset to point to a new instance of a type then the original ownership claim is released and if the value was the last owner of the type's instance then the type is discarded and the memory is deallocated.

````zax
MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

print : ()(...) = {
    //...
}

assert : ()(check : Boolean) = {
    //...
}

printIfValidPointer : ()(pointerToValue : MyType*) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the
        // context's allocator and a `strong` pointer is maintained
        value1 : MyType* strong @

        // the @ operator allocates `value2` dynamically with the
        // context's allocator and a `strong` pointer is maintained to
        // another `MyType` instance
        value2 : MyType* strong @

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

The lifetime of `strong` pointers is entirely dependent on all `strong` pointers pointing to the same instance of a type being discarded (or reset to point to empty). Only when the final `strong` pointer is discarded/reset will be pointed instance of a type be released.

````zax
MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

print : ()(...) = {
    //...
}

assert : ()(check : Boolean) = {
    //...
}

printIfValidPointer : ()(pointerToValue : MyType*) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func : (
    // `stayingAlive` is a `strong` pointer defaulted to point to nothing
    stayingAlive : MyType* strong
)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the
        // context's allocator and a `strong` pointer is maintained
        value1 : MyType* strong @

        // the @ operator allocates `value2` dynamically with the
        // context's allocator and a `strong` pointer is maintained to
        // another `MyType` instance
        value2 : MyType* strong @

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

func2 : ()() = {
    // the `strong` pointer returned by `func` is now being kept alive by
    // `liveAndLetDie`
    liveAndLetDie := func()

    printIfValidPointer(liveAndLetDie)  // will print "true"

    // reset `liveAndLetDie` which causes the lifetime originally allocated in
    // `value2` of `func` function to be destructed and deallocated
    liveAndLetDie =:

    printIfValidPointer(liveAndLetDie)  // will print "false"
}
````


### Breaking circular `strong` pointer lifetime

Care must be used when dealing with `strong` pointer lifetimes to ensure a circular dependency is not created where the circular chain is never broken. If a `strong` pointer points to another `strong` pointer that directly or indirectly points back to the same type's instance the lifetime of the `strong` pointers in the chain will never become destructed nor deallocated.

The example below creates a circular `strong` pointer chain:

````zax
print : ()(...) = {
    //...
}

MyType :: type {
    head : MyType* strong
    next : MyType* strong

    myValue1 : Integer
    myValue2 : String

    +++ final : ()() = {
        print("This will be displayed three times in this example.")
    }

    --- final : ()() = {
        print("BAD NEWS! This will never be displayed in this example!!!")
    }
}

assert : ()(check : Boolean) = {
    //...
}

printIfValidPointer : ()(pointerToValue : MyType*) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the
        // context's allocator and a `strong` pointer is maintained
        value1 : MyType* strong @

        // the @ operator allocates `value2` dynamically with the
        // context's allocator and a `strong` pointer is maintained to
        // another `MyType` instance
        value2 : MyType* strong @

        // the @ operator allocates `value3` dynamically with the
        // context's allocator and a `strong` pointer is maintained to
        // another `MyType` instance
        value3 : MyType* strong @

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
print : ()(...) = {
    //...
}

MyType :: type {
    head : MyType* strong
    next : MyType* strong

    myValue1 : Integer
    myValue2 : String

    +++ final : ()() = {
        print("This will be displayed three times in this example.")
    }

    --- final : ()() = {
        print("GOOD NEWS! This will be called three times in this example.")
    }
}

assert : ()(check : Boolean) = {
    //...
}

printIfValidPointer : ()(pointerToValue : MyType*) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the
        // context's allocator and a `strong` pointer is maintained
        value1 : MyType* strong @

        // the @ operator allocates `value2` dynamically with the
        // context's allocator and a `strong` pointer is maintained to
        // another `MyType` instance
        value2 : MyType* strong @

        // the @ operator allocates `value3` dynamically with the
        // context's allocator and a `strong` pointer is maintained to
        // another `MyType` instance
        value3 : MyType* strong @

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

        // reset all three `strong` pointers to point to nothing
        value1 =:   // `value1`'s instance will be destructed/deallocated (which
                    // removes the strong pointer to `value2`)
        value2 =:   // `value2`'s instance will be destructed/deallocated (which
                    // removes the strong pointer to `value3`)
        value3 =:   // `value3`'s instance will be destructed/deallocated

        // validate the pointers are indeed pointing to nothing
        assert(!value1)
        assert(!value2)
        assert(!value3)
    }

    return "I'll be back."
}
````


### Using `weak` pointers to break a chain

Pointers marked as `weak` will only contain a valid pointer to a type's instance so long as the original allocated type is not destructed/deallocated. In other words, `weak` points will not extend the lifetime of a `strong` pointer beyond the last `strong` pointer keeping a type's instance alive.

````zax
print : ()(...) = {
    //...
}

MyType :: type {
    head : MyType* weak     // head will not be the lifetime of whatever it
                            // points to alive
    next : MyType* strong   // next will keep whatever it points to alive

    myValue1 : Integer
    myValue2 : String

    +++ final : ()() = {
        print("This will be displayed three times in this example.")
    }

    --- final : ()() = {
        print("GOOD NEWS! This will be called three times in this example.")
    }
}

assert : ()(check : Boolean) = {
    //...
}

printIfValidPointer : ()(pointerToValue : MyType*) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the
        // context's allocator and a `strong` pointer is maintained
        value1 : MyType* strong @

        // the @ operator allocates `value2` dynamically with the
        // context's allocator and a `strong` pointer is maintained to
        // another `MyType` instance
        value2 : MyType* strong @

        // the @ operator allocates `value3` dynamically with the
        // context's allocator and a `strong` pointer is maintained to
        // another `MyType` instance
        value3 : MyType* strong @

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
        copyOfWeakHead : MyType* weak = headPointer.next.head

        // the `MyType` instance pointed to by the `weak` pointer is still
        // alive thus converting the `weak` point to a strong pointer will
        // obtain a strong pointer reference to the original type's instance
        printIfValidPointer(copyOfWeakHead as strong)   // will print "true"

        // the strong pointer both point to the same head type's instance
        assert((copyOfWeakHead as strong) == headPointer)

        // reset the head pointer to point to nothing (which is the only
        // pointer keeping all three `MyType` instances alive thus all three
        // `MyType` instances are destructed/deallocated)
        headPointer =:

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

Pointers marked as `own` can be transferred to pointers marked as `strong`. Once the transfer is completed, the original `own` pointer will point to nothing as the `strong` pointer will track the lifetime of the instance. Likewise, pointers marked as `strong` can be transferred to pointers marked as `own` on the condition that no other pointers marked as `strong` point to the same instance of a type otherwise the resulting `strong` pointer will point to nothing.

Caution: care must be taken when transferring an allocated `own` pointer to a `strong` pointer. Pointers marked as `own` are allocated using the standard allocator which is typically set to the sequential allocator by default. If an `own` pointer gets transferred later into a `strong` pointer, the standard allocator should be replaced with the parallel allocator.

````zax
MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

print : ()(...) = {
    //...
}

assert : ()(check : Boolean) = {
    //...
}

printIfValidPointer : ()(pointerToValue : MyType*) = {
    if pointerToValue
        print("true")
    else
        print("false")
}

func : (result : String)() = {

    scope {
        // the @ operator allocates `value1` dynamically with the
        // context's allocator and a `strong` pointer is maintained
        value1 : MyType* own @

        // `value2` is is a `strong` pointer but the value is initialized to
        // point to nothing
        value2 : MyType* strong

        // `value1` used to own the instance but the instance ownership is
        // transferred from a `value1` to `value2` which now keeps the
        // `MyType` instance alive
        value2 = value1

        printIfValidPointer(value1) // will print "false"
        printIfValidPointer(value2) // will print "true"

        assert(value1 != value2)

        // transferring a `strong` pointer to an `own` pointer can be
        // done so long as no other `strong` pointers exist to the same
        // instance (otherwise `value1` pointer to nothing)
        value1 = value2

        // `value2`'s strong pointer was automatically reset when ownership
        // was exclusively taken over by `value1`
        assert(!value2)

        printIfValidPointer(value1) // will print "true"
        printIfValidPointer(value2) // will print "false"

        assert(value1 != value2)


        // `value3` is is a `strong` pointer but the value is initialized to
        // point to nothing
        value3 : MyType* strong

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


        // `value1` cannot retake ownership from `value2` as another
        // pointer to the same instance of `value2` exist thus `value1` cannot
        // take exclusive ownership and `value1` will point to nothing
        value1 = value2
    }

    return "I'll be back."
}
````


### Transferring `strong` pointers to `handle` pointers

Pointers marked as `strong` cannot be directly transferred to a pointer marked as `handle`. The only method by which this transfer can happen is if the `strong` pointer is first transferred to an `own` pointer and then transferred to a `handle` pointer. The vice versa limitation is true. Pointers marked as `handle` cannot be directly transferred to a pointer marked as `strong`. The only method by which this transfer can happen is if the `handle` pointer is first transferred to an `own` pointer and then transferred to a `strong` pointer.

Transferring to an `own` pointer have limitations. Only if the pointer marked as `strong` or `handle` is the exclusive reference to the instance of a type can the transfer to an `own` pointer occur.


### Casting contained variables into `strong` pointers

#### Converting from a container `strong` pointer to a contained `strong` pointer

The `lifelink` operators can be used to cast a raw pointer to a variable which has the same lifetime as an original `strong` or `handle` pointer to a `strong` or `handle` pointer respectively.

A `strong` pointer to a type's instance may contain other types within the instance that share a common lifetime. While the lifetime of these contained type is the same as the container type, only a `strong` pointer to the container type may exist (despite both types being considered as a single instance). The `lifelink` operator is especially useful to create a `strong` pointer of a contained type from a `strong` pointer to the container's type.

Example as follows:

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

myType : MyType* strong @

// create a pointer to `value` and link the lifetime of `myType` to the pointer
value : Integer* strong = myType.value1 lifelink myType

// resetting the `myType`'s `strong` pointer will not impact the real lifetime
// of the instance connected to `myType` (as the `value` `strong` pointer will
// keep it's container instance alive)
myType =:

// set the `MyType::value1` to `5` (which is still valid as the
// lifetime of of the original `strong` pointer is kept alive)
value. = 5
````


#### Converting from a container `strong` pointer to a contained `strong` pointer

While any pointer to any type can be linked to a `strong` or `handle` pointer using the `lifecast` operator, a `lifelink` operator can link a pointer to a contained type back to the original `strong` or `handle` pointer safely by verifying the pointer refers to a memory addresses within the bounds of the allocated `strong` or `handle` pointer.

An example of a runtime `lifelink` being applied onto a `strong` pointer:

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

doSomething : ()(a : A* strong) {
    // probe `a` to see if it is indeed within a `B` type and if so then
    // return a pointer to a B type and create a `strong` pointer from `a`
    b := (a outerlink B*) lifelink a
}

function : ()() = {
    value : B* strong @
    value.a.foo = 1
    value.bar = 2

    // link a `strong` pointer to `a` from `value`
    a : A* strong = value.a lifelink value

    doSomething(a)
}
````


#### `lifelink` versus `lifecast`

The exclusive difference between these operators is safety. The `lifecast` operator will force a conversion of any raw pointer to link to `strong` or `handle` pointer even for unrelated pointers. The `lifelink` operator will validate the raw pointer actually points inside the address boundaries of the `strong` or `handle` pointer. If it does not then `lifelink` will return a pointer to nothing.


### `strong` and `weak` overhead and control blocks

A `strong` and `weak` pointers contain a pointer to an instance of a type and a pointer to the control block. When a type is allocated for storage in a `strong` pointer, a control block is typically reserved as part of the allocation of the type.

Since `weak` pointers also need control blocks, `weak` pointers can keep the memory for a control block alive and possibly the `type` too if the control block and `type` are allocated within the same memory space. To clean up allocated memory, reset any `weak` pointers to point to nothing after all `strong` pointers are gone.

An example `strong` / `weak` pointer content and control block:

````zax
/*
StrongPointerControlBlock {
    strongCount : Integer atomic
    weakCount : Integer atomic
    destructor : ()()*
    deallocateType : void*
    deallocateControl : void*
    control : void*
    reservedStorage : Byte[ /* alignment size needed + storage space for type */ ]
}

StrongPointerContents$(Type) :: type {
    instance : $Type *
    control : void*
}
*/
````
