
# [Zax Programming Language](index.md)

## Concurrency

### Using `atomic` types

Any type can be declared with an `atomic` keyword. When the atomic keyword is applied the type operates as normally the type would except every operator or function call on the type is run exclusively across all CPUs. This ensures the contents of a type cannot be accessed by more than one thread of execution at a time.

The `atomic` keyword can also be applied to intrinsic types, like `Integer`, where specialized atomic code will be used for the fastest possible atomic operations on these intrinsic types.

````zax
puid : Integer atomic

func : ()() {
    // no matter how many times the function is called, the `uniqueValue` will
    // always contain a unique value because the `puid` Integer is operated upon
    // atomically
    uniqueValue := puid++

    // ...
}


spawnThreadThatCallsFuncForever : ()() = {
    //...
    
    while true
        func()

    //...
}

spawnThreadThatCallsFuncForever()
spawnThreadThatCallsFuncForever()
````


### Using the `deep` type qualifier as a method to ensure type data separation across threads

For speed and efficiency reasons, types may utilize shallow copy and data sharing across type instances when a variable is copied from one type instance to another. A true copy of a type may never actually be performed in such a scenario. Copying the data may be expensive and non desirable.


#### Efficient `immutable` type data sharing and concurrency

Types marked as `immutable` are an excellent example where this kind of optimization can be useful since the immutable type might need to allocate data for storage but once allocated need not modify the content ever again. Copying immutable variables from one instance to another could be expensive given each copy would need another allocation, a copy of the contents and a deallocation when the immutable type is no longer needed.

Rather than performing a copy whenever the immutable type is passed to functions, a simple `handle` pointer to the real data might be utilized. A `handle` pointer keeps a simple reference count to a type and disposes of the data when the final instance of a type is disposed and thus is perfectly suitable for `immutable` data sharing. While a `strong` pointer could be used instead of a `handle` type, a `strong` pointer incurs additional concurrency overhead every time a reference count is incremented or decremented since the reference count for `strong` pointers must be thread safe. This overhead can cause a CPU to operate less efficiently as it can disrupt things like CPU branch predictability and CPU caches.

An alternative qualifier of `deep` can be applied to a type to ensure a full copy of the type is performed prior to transferred the type's instance to a new thread. The `deep` qualifier can be used to ensure a completely independent copy of a type is made so a copy of an immutable type is made whenever the type is passed to a different thread.

While seemingly a [`last` pointers/references](pointers.md#using-the-last-type-qualifier-to-optimize-content-transfer) method can seemingly be used to transfer out the contents of the data to a new type's instance prior to transfer to a new thread but the `last` does not guarantee any shared state is indeed the final copy. This mechanism can only work if the passed in type is truly the last instance of a type before it's disposal.


````zax
MyType :: type {
    animal : String = "alligator"
    //...

    +++ final : ()(rhs : MyType constant &) = {
        // this version of the constructor will be called under normal
        // conditions where a copy of the type needs to be made
        animal = rhs.animal
    }
    +++ final : ()(rhs : MyType last &) = {
        // this version of the constructor will be called when the
        // last instance of a type is being transferred from the source
        // type to the destination type
        animal = rhs.animal
    }
    +++ final : ()(rhs : MyType constant deep &) = {
        // this version of the constructor will be called when a `deep`
        // copy of the contents must be performed
        animal = rhs.animal
    }
}

getAsTemporary : (result : MyType)(input : MyType) = {
    // `input` was passed by value and a shallow copy of `input` was made
    // and the returned MyType automatically has the `last` qualifier applied
    temp : MyType = input
    return temp
}

// declare an instance of `MyType`
myType : MyType

// the default copy constructor is called and a shallow copy of `myType` is
// created
myOtherType : MyType = myType

// in theory, `myTypeFromLast` should be it's own clone of `MyType` but the
// the `MyType last &` constructor only performed a shallow copy so the
// memory backing the types could still be shared
myTypeFromLast := getAsTemporary(myType)

// the `deep` qualifier is applied to the type and the `deep` version of the
// copy constructor will be removed and `myTypeFromDeep` will truly contain
// an independent copy of the type's instance
myTypeFromDeep := myType as deep
````


### Creating asynchronous functions using `promise`

Calling a function labelled as `promise` does not invoke a direct call to the function. Instead a function pointer is returned that can be transferred and executed on another thread or queue later. When the promise is filled a callback to a `then` function occurs indicating the promise is filled and the results are sent to the then function.

````zax
myFunc final : ()(value1 : Integer) promise = {
    //...
}

/*
TemplatedPromiseResult :: type {
    ThenCallbackPrototype final : ()()

    callable : ()()
    then : ()() 
}
*/

// calling laterFunc function causes all the values passed into the function to
// be captured where the function can be executed later
later := myFunc(42)

later.then = {
    // called after the `myFunc` is completed with the return result
    // (called from the thread that executed the function when complete)
}

// create an empty function to be used as a type declaration
EmptyFunctionType final : ()()

executeCallableOnAnotherThread final : ()(callable : EmptyFunctionType) = {
    //...
    callable()
    //...
}

// `myFunc` will get executed when `callable()` is called from the other thread
executeCallableOnAnotherThread(later.callable)
````


#### Performing automatic `deep` copy during `promise` calls on arguments

By declaring pass by-value input or output argument variable types on a `promise` function calls as `deep`, any calls passing of the type will automatically cause a `deep` copy constructor to be called when the copy of the value is created. This ensures a `deep` copy is performed and helps prevent concurrency issues on the types that would normally create shallow copies otherwise.

````zax
myFunc final : ()(value1 : Integer, value2 : String deep) promise = {
    //...
}

/*
TemplatedPromiseResult :: type {
    callable : ()()
    then : ()() 
}
*/

// as `String` types are not normally thread safe, all String types must
// be constructed as `deep` when crossing the thread boundary
someString := "apple"

// calling laterFunc function causes all the values passed into the function to
// be captured where the function can be executed later
later1 := myFunc(42, someString)
later2 := myFunc(55, someString)

later1.then = {
    // ...
}
later2.then = {
    // ...
}

// create an empty function to be used as a type declaration
EmptyFunctionType final : ()()

executeCallableOnAnotherThread final : ()(callable : EmptyFunctionType) = {
    //...
    callable()
    //...
}

// `myFunc` will get executed when `callable()` is called from the other thread
executeCallableOnAnotherThread(later1.callable)
executeCallableOnAnotherThread(later2.callable)
````


#### Performing automatic `deep` copy during `promise` calls on all arguments

By declaring a promise function as `deep`, all pass by-value input or output argument variable types will automatically cause a `deep` copy constructor to be called when the copy of the value is created. This ensures a `deep` copy is performed and helps prevent concurrency issues on the types that would normally create shallow copies otherwise.

````zax
myFunc final : ()(value1 : Integer, value2 : String) deep promise = {
    //...
}

/*
TemplatedPromiseResult :: type {
    callable : ()()
    then : ()() 
}
*/

// as `String` types are not normally thread safe, all String types must
// be constructed as `deep` when crossing the thread boundary
someString := "apple"

// calling laterFunc function causes all the values passed into the function to
// be captured where the function can be executed later
later1 := myFunc(42, someString)
later2 := myFunc(55, someString)

later1.then = {
    // ...
}
later2.then = {
    // ...
}

// create an empty function to be used as a type declaration
EmptyFunctionType final : ()()

executeCallableOnAnotherThread final : ()(callable : EmptyFunctionType) = {
    //...
    callable()
    //...
}

// `myFunc` will get executed when `callable()` is called from the other thread
executeCallableOnAnotherThread(later1.callable)
executeCallableOnAnotherThread(later2.callable)
````


#### Returning variables from an `promise` function asynchronously

A `promise` function can return arguments. The returned arguments are returned by the `promise` function and passed into a `then` function attached to the resulting structure of the promise. A promises results will only ever occur ones. As the `then` function is set before the execution of the `promise` function, the function's results can never complete prior to the `then` function being attached to the returned `promise` type.

````zax
// create an empty function to be used as a type declaration
EmptyFunctionType final : ()()

executeCallableOnMainThread final : ()(callable : EmptyFunctionType) = {
    //...
    callable()
    //...
}

executeCallableOnAnotherThread final : ()(callable : EmptyFunctionType) = {
    //...
    callable()
    //...
}

myFunc final : (result : String)(value1 : Integer) deep promise = {
    //...
}

/*
TemplatedPromiseResult :: type {
    callable : ()()
    then : ()(result : String) 
}
*/

// calling laterFunc function causes all the values passed into the function to
// be captured where the function can be executed later
later := myFunc(42)

later.then = {
    // called after the `myFunc` is completed with the return result
    
    resultCallbackFunc final : ()(result : String) deep promise = {
        //...
    }

    // pass the callable function returned from the promise into a
    // function that will execute the result back onto the main thread
    executeCallableOnMainThread(resultCallbackFunc(result).callable)
}

// `myFunc` will get executed when `callable()` is called from the other thread
executeCallableOnAnotherThread(later.callable)
````


### Using `task` to schedule asynchronous function calling

A `task` operates similar to a `promise`. The key difference is that a `task` can return data more than once and can be scheduled and rescheduled for future processing later. A `task` can also suspend itself allowing for the `then` to proactively request the task be reactivated to continue processing more data. When a `task` is no longer needed, the `task`'s `cancel` function can be called to indicate the `task` should clean itself up and should only continuing scheduling itself for the purpose of final cleanup.

In the example below a generator `task` function lazily counts forever starting at `0`. When the `task` is first executed, the task does not actually run but returns `producer` and `consumer` types. The `producer` type can be sent to a different thread of execution whereupon the task can be scheduled until completed. The `consumer` type can be used to attach a callback to receive a callback every time a result the `task` returns. The `yield` keyword operates like the return keyword, except the function does not exit. Instead the `yield` causes the current values to be returned from the `task` and the `task` will continue to run. The compiler will break the `task` function into sub-functions that can be started and stopped for scheduling purposes.

````zax
// the function runs forever, which is okay for `task` functions as they can
// have their execution cancelled when they are no longer needed
countForever : (result : Integer)() task = {
    while true {
        yield result
        ++result
    }
}

/*
enum Coroutine.Status {
    Suspend,
    Reschedule,
    Complete
}

TemplatedTaskResult :: type {

    producer : :: type {
        // perform the next batch of coroutine work
        callable : (status : Coroutine.Status)()

        // request the start the cancellation process
        cancel : ()()

        // request the suspended task be rescheduled again
        resume : ()()
    }

    consumer : :: type {
        // result is yielded
        then : ()(result : Integer)

        // returns true of the coroutine is completed its execution ()
        isCompleted mutator : (completed : Boolean)()

        // re-activate this suspended co-routine (after `then` was processed)
        // but will not reactivate the coroutine is it was cancelled
        activate : ()()

        // indicate the coroutine will never activate the co-routine so the
        // coroutine needs to clean itself
        cancel : ()()
    }
}
*/

scheduleProducer : ()(producer :) {
    
    invokeCallable final : ()(producer :) = {
        switch status := producer.callable(); status {
            case Coroutine.Suspend:       break
            case Coroutine.Reschedule:    scheduleProducer(producer)
            case Coroutine.Compete:       break
        }
    }

    // ...
    callInvokeCallableLaterWithProducer(producer)
    // ...
}

myTask := countForever()

myTask.consumer.then = {
    // reactivate the task to perform the next count
    defer myTask.consumer.activate()
    //...
}

myTask.producer.resume = [producer = myTask.producer] {
    scheduleProducer(producer)
}

myTask.consumer.activate = [producer = myTask.producer] {
    scheduleProducer(producer)
}

myTask.consumer.cancel = [producer = myTask.producer] {
    producer.cancel()
    scheduleProducer(producer)
}

scheduleProducer(myTask.producer)

// at end of scope cause the consumer to cancel the task
defer myTask.consumer.cancel()

//...
````


#### Using `await` to create asynchronous call chains

A `task` function can call other `task` functions easily by using the `await` keyword to obtain a single result from a called `task` method. This creates a method by which one `task` can begin executing another `task` where all the tasks will be scheduled collectively. At any time the entire chain of tasks can be cancelled where the compiler will schedule cleanup of the tasks.

````zax
scheduleTaskProducer : ()(producer :) {
    //...
}

func1 final : (result : Integer)() task = {
    //...
    return result
}

func2 final : (result : String)(input : Integer) task = {
    
    //...
    value := await func1()
    //...

    return result
}

func3 final : ()(input : Integer) task = {
    //...
    value := await func2(input)
    //...
}

myTask := func3(42)

myTask.consumer.then = {
    //...
}

scheduleTaskProducer(myTask.producer)

defer myTask.consumer.cancel()
````


#### Using `suspend` and the captured `result()`


````zax
scheduleTaskProducer : ()(producer :) {
    //...
}

EmptyFunctionPrototype final : ()()

externalThreadThatWillResumeTask : ()(resume : EmptyFunctionPrototype) = {
    //...
    resume()
    //...
}

func final : (result : Integer)() task = {

    // the `resume` is a function that is auto captured for all task
    // functions; when `resume()` is called the suspended task will
    // automatically resume at the point of `suspend`
    externalThreadThatWillResumeTask(resume)

    // cause the task to `suspend` until `resume()` is called; if `resume()`
    // is called prior to suspending the task will be rescheduled immediately
    suspend

    return result
}


myTask := func(42)

myTask.consumer.then = {
    //...
}

scheduleTaskProducer(myTask.producer)

defer myTask.consumer.cancel()
````


#### `task` cancellation

Whenever a function marked as `task` encounters an `await`, `yield` or a `suspend` statement, the function might cause a quick exit from the current function if the `task` is cancelled. This is done to ensure predictable `task` function exit points and to ensure `task` functions are not left in undetermined state. Function cleanup occurs as needed and the compiler can insert code to cause additional clean-up calls for any `task` scheduler as needed. If `await`, `yield` or `suspend` is called after a task is cancelled during the automatic cleanup, those statements will become new instant exit points in whatever routines called them.

Functions will not throw exceptions upon cancellation. Any construction/assignment of variables from an awaited function will not complete in the event of a cancellation (thus any constructed variables will also not be destructed in turn).

In the example below two function exit points exist in the `fetchRandomDataForever` function. The `await` can be automatically converted to a quick `return` from the function if the awaited task is cancelled. The `yield` can convert into quick `return` from the function.

````zax
fetchRandomNumber : (result : Integer)() task = {
    //...
}

fetchRandomDataForever : (result : Integer)() task = {
    while true {
        // the function can exit if the awaited `task` is cancelled whereupon
        // the `number` type is not constructed nor assigned a value
        number := await fetchRandomNumber()

        // the function can exit if the yielded `task` is cancelled instead of
        // producing another value whereupon the `number` is not returned from
        // the `task` but instead the `task` continues its cancellation process 
        yield number
    }
}

myTask := fetchRandomDataForever()

myTask.consumer.then = {
    // reactivate the task to perform the next fetch
    defer myTask.consumer.activate()

    //...
}

//...

// cause the scheduled `task` to quick exit
myTask.consumer.cancel()

//...
````


#### `task` deep copy

In the same manner `promise` functions can use the `deep` keyword to ensure any types that require deep copy to be passed to another thread, the `deep` keyword can be applied to `task` function arguments as well as to the entire `task` function.

If the `deep` keyword is placed on a function's argument then that argument will cause a deep copy to occur prior to the data being received by a scheduled `task`. If the `deep` keyword is placed on an entire function, then any input or returned arguments that support `deep` copy will automatically cause a `deep` copy to be performed prior to the data being sent to or from the function.

````zax
// only the `input` argument will utilize the `deep` copy mechanism
func1 : (result : Integer)(input : String deep) task = {
    //...
}

// all arguments will utilize the `deep` copy mechanism
func2 : (result : String)(input : String) deep task = {
    //...
}

value := "momma bear"

myTask1 := func1(value)
myTask2 := func2(value)

//...
````


### Allocators and threading

When normal allocation is performed on the context, the allocator will use the standard allocator (i.e. `___.allocator`) which are thread unaware (i.e. thread unsafe). Whenever a `strong` pointer type is allocator or a `deep` copy is performed, the standard allocator in the context is replaced by the parallel allocator temporarily (i.e. assigned to `___.parallel.allocator`) automatically. This is done to ensure that any allocated data is entirely allocated in a thread safe manner (and deallocated later in a thread safe manner). Once the allocation is complete, the original standard allocator is restored allowing any future constructor and allocation of other types to use the faster thread unaware allocator.

Caution: care must be taken when transferring an allocated `own` pointer to a `strong` pointer. Pointers marked as `own` are not allocated using the parallel allocator by default. If an `own` pointer gets transferred later into a `strong` pointer, the standard allocator should be replaced with a thread safe allocator.


### Context and threading

A new context instance must be set-up for any spawned thread/fiber as the context variable's (i.e. `___`) instance is tied to an individual thread/fiber. This allows the context instance to use optimized thread-unaware operations.
