
# [Zax Programming Language](index.md)

## Context Type]

### Automatic system context

A system type is automatically constructed and passed between functions. The Zax language will setup an implicit context variable with a reserved triple underscore `___` name. The context variable points to a context type containing things like allocators, loggers of whatever other extended context data is attached to the context type. Every function called is passed the context pointer implicitly. The types that exist within the context type can be interacted with by any function and specific types within the context can be modified to optionally change their values. One example scenario would be to replace the allocator context member to point to a new allocator.

Care must be taken when new threads are created to ensure that a new context object is created for any new thread and thread safety concerns are factored into whatever variables are placed inside the context type. One strategy, for example, is to ensure that each thread receives it's own allocator which uses a local memory pool that is unaware of thread safety concerns backed by a larger pool of memory which can expand the local thread pool's memory as needed. This allows threads to allocate memory using thread specific memory regions much more efficiently than their thread safe counterparts.

````zax
print final : ()(...) = {
    // ...
}

printIfPointerIsValid final : ()(pointer : *) = {
    if pointer
        print("true")
    else
        print("false")
}

MyType :: type {
    myValue1 : Integer
    myValue2 : String
}

func final : ()() = {
    testFunc final : ()() = {
        printIfPointerIsValid(___)          // will print "true"

        a : MyType @                        // allocate `a` using the context's
                                            // allocator implicitly

        b : MyType @ ___.allocator          // allocate `b` using the context's
                                            // allocator explicitly

        printIfPointerIsValid(a)             // will print "true"
        printIfPointerIsValid(b)             // will print "true"
    }

    printIfPointerIsValid(___)              // will print "true"
    printIfPointerIsValid(___.allocator)    // will print "true"

    value : MyType @ ___.allocator          // allocate using the allocator
                                            // within the context type
                                            
    printIfPointerIsValid(___)              // will print "true"

    testFunc()                              // will successfully allocate and
                                            // deallocate two types

    ___.allocator =:                        // reset the allocator pointer
                                            // to point to nothing

    printIfPointerIsValid(___)              // will print "true"
    printIfPointerIsValid(___.allocator)    // will print "false"

    // PANIC BEHAVIOR
    // the pointer to the context's allocator was reset to point to nothing thus
    // when `testFunc()` is called the function may cause a runtime panic
    // as the context's allocator functions no longer point to any valid code
    // allocation routines
    testFunc()                              // will cause `testFunc()` to panic
}
````
