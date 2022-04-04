
# [Zax Programming Language](index.md)

## Concurrency

### Using the `deep` type qualifier as a method to ensure type data separation across threads

For speed and efficiency reasons, types may utilize a `shallow` copy methodology and data sharing across type instances when a variable is copied from one type instance to another. A true copy of a type may never actually be performed in such a scenario as copying the data may be expensive and not desirable, especially for types that require heap allocations (e.g. variable length strings).


#### Efficient `immutable` type data sharing and concurrency

Types qualified as `immutable` are an excellent example of how qualifiers can help optimization. An `immutable` type might need to allocate data for storage. Once allocated the contents are not modify ever again. Needlessly copying `immutable` variables from one instance to another could be expensive given each copy would need another allocation, a copy of the contents, and a deallocation when an `immutable` type is no longer needed.

Rather than performing a copy whenever an `immutable` type is passed to a function, a simple `handle` pointer to the real data might be utilized. A `handle` pointer keeps a simple reference count to a type and disposes of the data when the final instance of a type is disposed (and thus is perfectly suitable for `immutable` data sharing). While a `strong` pointer could be used instead of a `handle` type, a `strong` pointer incurs additional concurrency overhead every time a reference count is incremented or decremented since `strong` pointer reference counts must be thread-safe across CPU cores. This overhead can cause a CPU to operate less efficiently as it can disrupt things like CPU branch predictability and invalidate CPU caches.

Normally when a type is passed by-value, a `shallow` qualifier is implicitly applied to the value. The `shallow` qualifier allows for optimization by assuming the value is not needing to be truly copied when the value is passed from one function to another. Since this is the default assumed behavior for `synchronous` functions, the keyword `shallow` does not needed to be specified under normal circumstances to indicate `shallow` by-value semantics (as this specification would be redundant).

A qualifier of `deep` can be applied to a type to ensure a full copy of the type is performed prior to transferring a type's instance to a new thread. The `deep` qualifier can be used to ensure a completely independent copy of a type is made prior to thread transference. A copy of an `immutable` type is made whenever the type is passed to a different thread using the `deep` qualifier.

While [`last` pointers/references](pointers.md#using-the-last-type-qualifier-to-optimize-content-transfer) seemingly could be used to transfer out contents of data prior to transferring a type's instance to a new thread, `last` pointers/references do not guarantee any shared state is indeed the final copy. This mechanism to transfer data across threads could only work if a passed in type was truly the `last` instance of a type before it's normal disposal and thus cannot be relied about as a mechanism to ensure thread safety.


````zax
MyType :: type {
    animal : String = "alligator"
    // ...

    +++ final : ()(rhs : MyType constant &) = {
        // this version of the constructor will be called under normal
        // conditions where a copy of the type needs to be made
        animal = rhs.animal
    }
    +++ final : ()(rhs : MyType & last) = {
        // this version of the constructor will be called when the
        // `last` instance of a type is being transferred from the source
        // type to the destination type
        animal = rhs.animal
    }
    +++ final : ()(rhs : MyType constant & deep) = {
        // this version of the constructor will be called when a `deep`
        // copy of the contents must be performed (as `deep` qualifier applies
        // to all types contained within the reference type passed)
        animal = rhs.animal    // rhs.animal is `deep`
    }

    merge : (result : MyType &)(rhs : MyType constant &) deep = {
        // ...
        return _.
    }
}

returnAsTemporary final : (result : MyType)(input : MyType) = {
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
// the `MyType & last` constructor only performed a shallow copy so the
// memory backing the types could still be shared
myTypeFromLast := returnAsTemporary(myType)

// the `deep` qualifier is applied to the type and the `deep` version of the
// copy constructor will be removed and `myTypeFromDeep` will truly contain
// an independent copy of the type's instance
myTypeFromDeep := myType as deep

anotherType : MyType

// the compiler will treat all input/output arguments as being qualified as
// `deep` types
anotherType.merge(myType)
````


### Adding a deep qualifier on a type

Types can be defaulted to exhibit deep behavior when used as an argument. The example below demonstrates this behavior.

````zax
MyType :: type deep {
    value1 : Integer * @
    value2 : String
}

// Even though the `promise` isn't explicitly declared as `deep`, the type is
// declared as `deep`. The type will automatically be treated as a `deep` type
// during method calls unless overridden with a `shallow` declaration.
myFunc final : ()(myType : MyType) promise = {
    // ...
}


myType : MyType

// a deep copy is performed at this moment on the type
later := myFunc(myType)

callable := later.then = {
    // ...
}

// ...

// ...potentially run on a different thread...
callable()
````


### Creating asynchronous functions from captured function invocations

Using function invocation capturing and function chaining, an invocation of a function can be captured (but not called immediately). Another new function can be created to capture the results of the not yet invoked function and feed that result into another function after. This final newly minted function can be sent to a different thread to execute where the return result will invoke a callback when the function is complete.

One caution, invoked captured functions are not expected to be qualified as `deep` thus the compiler will not issue a warning if the `deep` qualifier was not applied. If a invoked capture function isn't designed for thread safety due to `shallow` copying, unexpected behaviors can ensue.

An example of invocation capture with a later callback invoked from a different thread:

````zax
// a function that invokes a function that has no return result
invokeOnAnotherThread(funcLater :) = {
    // ...
    
    funcLater()

    // ...
}

myFunc final : (result : Integer)(value1 : Integer, value2 : String) = {
    // ...
}

then final : ()(input : Integer) = {
    // ...
}

// capture an invocation of a function (but doesn't actually call the function)
later := [] myFunc(42, "meaning of life" as deep)

// chain the output of the function to the input of the `then` function
laterThen := later | then

// send the function to a different thread to execute
invokeOnAnotherThread(laterThen)
````


### Creating asynchronous function out of `lazy` functions

A `lazy` function can be converted into an asynchronous function (see [lazy functions](lazy.md)). When a function qualified as `lazy` returns a `lazy` function, the `lazy` function can be passed and invoked from an alternative thread.

On caution, `lazy` functions are not expected to be qualified as `deep` where the compiler will not issue a warning if the `deep` qualifier was not applied. If a `lazy` function isn't designed for thread safety, unexpected behaviors can ensue. If a `lazy` function is known to be used in an asynchronous context, adding the `asynchronous` directive to the function to ensure proper `deep` usage is a good solution to re-enable warnings.

````zax
runOnThread final : ()(callable : ) = {
    // ...
    result := callable()
    // ...
}

func final : (value : Double)(algorithm : String deep) lazy [[asynchronous]] = {
    forever {
        // ... complex algorithm ...
        yield return value
    }
    [never]
}

later := func("sha265")

runOnThread(later)

// ...
````


### Creating asynchronous functions using `promise`

Calling a function labelled as `promise` does not invoke a direct call to a function. Instead a function pointer is returned that can be transferred and executed on another thread or queue for later execution. When a `promise` is resolved, a callback to a `then` function occurs indicating the `promise` is resolved and the results are sent to the `then` function.

````zax
myFunc final : ()(value1 : Integer) promise = {
    // ...
}

/*
TemplatedPromiseResult :: type {
    ThenCallbackPrototype final : ()()

    then mutator : (callable : ()())(callback : ThenCallbackPrototype) 
}
*/

// calling laterFunc function causes all the values passed into the function to
// be captured where the function can be executed later
later := myFunc(42)

callable := later.then = {
    // called after the `myFunc` is completed with the return result
    // (called from the thread that executed the function when resolved)
}

executeCallableOnAnotherThread final : ()(callable : ()()) = {
    // ...
    callable()
    // ...
}

// `myFunc` will get executed when `callable()` is called from the other thread
executeCallableOnAnotherThread(callable)
````


#### Performing automatic `deep` copy during `promise` calls on arguments

By declaring pass by-value input or output arguments on a `promise` function calls as `deep`, any calls passing values to the `promise` will automatically cause a `deep` copy constructor to be called when the values are captured. This ensures a `deep` copy is performed and helps prevent concurrency issues on the types that otherwise would normally create `shallow` copies.

````zax
myFunc final : ()(value1 : Integer, value2 : String deep) promise = {
    // ...
}

/*
TemplatedPromiseResult :: type {
    ThenCallbackPrototype final : ()()

    then mutator : (callable : ()())(callback : ThenCallbackPrototype) 
}
*/

// as `String` types are not normally thread safe, all `String` types must
// be constructed as `deep` when crossing the thread boundary
someString := "apple"

// calling laterFunc function causes all the values passed into the function to
// be captured where the function can be executed later
later1 := myFunc(42, someString)
later2 := myFunc(55, someString)

callable1 := later1.then = {
    // ...
}
callable2 := later2.then = {
    // ...
}

// create an empty function to be used as a type declaration
EmptyFunctionType final : ()()

executeCallableOnAnotherThread final : ()(callable : EmptyFunctionType) = {
    // ...
    callable()
    // ...
}

// `myFunc` will get executed when `callable()` is called from the other thread
executeCallableOnAnotherThread(callable1)
executeCallableOnAnotherThread(callable2)
````


#### Performing automatic `deep` copy during `promise` calls on all arguments

By declaring a promise function as `deep`, all pass by-value input or output argument variable types will automatically cause a `deep` copy constructor to be called when the copy of the value is captured. This ensures a `deep` copy is performed and helps prevent concurrency issues on the types that would normally create `shallow` copies otherwise.

````zax
myFunc final : ()(value1 : Integer, value2 : String) deep promise = {
    // ...
}

/*
TemplatedPromiseResult :: type {
    ThenCallbackPrototype final : ()()

    then : (callable : ()())(callback : ThenCallbackPrototype) 
}
*/

// as `String` types are not normally thread safe, all String types must
// be constructed as `deep` when crossing the thread boundary
someString := "apple"

// calling laterFunc function causes all the values passed into the function to
// be captured where the function can be executed later
later1 := myFunc(42, someString)
later2 := myFunc(55, someString)

callable1 := later1.then = {
    // ...
}
callable2 := later2.then = {
    // ...
}

// create an empty function to be used as a type declaration
EmptyFunctionType final : ()()

executeCallableOnAnotherThread final : ()(callable : EmptyFunctionType) = {
    // ...
    callable()
    // ...
}

// `myFunc` will get executed when `callable()` is called from the other thread
executeCallableOnAnotherThread(callable1)
executeCallableOnAnotherThread(callable2)
````


#### Returning variables from an `promise` function asynchronously

A `promise` function can return arguments. The returned arguments are returned by the `promise` function and passed into a `then` function attached to the resulting structure of a `promise`. A promises result will only ever occur once. As a `then` function is set before the execution of a `promise` function, a function's results can never complete prior to a `then` function being attached to the returned `promise` type.

````zax
// create an empty function to be used as a type declaration
EmptyFunctionType final : ()()

executeCallableOnMainThread final : ()(callable : ()()) = {
    // ...
    callable()
    // ...
}

executeCallableOnAnotherThread final : ()(callable : ()()) = {
    // ...
    callable()
    // ...
}

myFunc final : (result : String)(value1 : Integer) deep promise = {
    // ...
}

/*
TemplatedPromiseResult :: type {
    ThenCallbackPrototype final : ()(result : String)

    then mutator : (callable : ()())(thenFunc : ThenCallbackPrototype)
}
*/

// calling laterFunc function causes all the values passed into the function to
// be captured where the function can be executed later
later := myFunc(42)

callable := later.then = {
    // called after the `myFunc` is completed with the return result
    
    resultCallbackFunc final : ()(result : String) deep promise = {
        // ... call back will happen on main thread ...
    }

    // pass the callable function returned from the promise into a
    // function that will execute the result back onto the main thread
    executeCallableOnMainThread(resultCallbackFunc(result).then = callable)
}

// `myFunc` will get executed when `callable()` is called from the other thread
executeCallableOnAnotherThread(callable)
````


### Using `task` to schedule asynchronous function calling

A `task` operates similar to a `promise`. The key difference is that a `task` can return data one or more times and a `task` can be scheduled and rescheduled for future processing later. A `task` can also suspend itself allowing for a `then` callback to proactively request a `task` be reactivated and continue processing. When a `task` is no longer needed, a `task`'s `cancel` function can be called to indicate a `task` should clean itself up and should only continuing scheduling itself for the purpose of final cleanup.

In the example below a generator `task` function lazily counts forever starting at `0`. When the `task` function is first executed, the `task` does not actually run but returns `producer` and `consumer` types. The `producer` type can be sent to a different thread of execution whereupon the `task` can be scheduled until completed. The `consumer` type can be used to attach a callback to receive a callback every time a result the `task` returns. The `yield` keyword operates in conjunction with the `return` keyword, except the function does not exit. Instead the `yield` causes the current values to `return` from the `then` callback and the `task` will resume. The compiler will break the `task` function into sub-functions that can be started and stopped for scheduling purposes.

````zax
// the function runs forever, which is okay for `task` functions as they can
// have their execution cancelled when they are no longer needed

/*
enum Coroutine.Status {
    Ready,
    Suspend,
    Complete
}
*/

countForever final : (result : Integer)() task = {
    /*

    Cancellation :: type {
        // ...
        cancelled mutator final : (isCancelled : Boolean)()
        // ...
    }

    // auto variable declared named `cancellation` auto-captured for tasks
    cancellation : Cancellation

    */

    // checking for cancellation after yielding is appropriate to exiting
    // a task earlier than normal completion
    while !cancellation.cancelled {
        ++result
        yield return result
    }

    // exiting without `return` is legal in a task that is cancelled
}

/*

TemplatedTaskResult :: type {
    ThenCallbackPrototype final : ()(result : Integer)

    Producer :: type {
        CallablePrototype final : (status : Coroutine.Status)()

        // Once a resume function is installed by the scheduler, the callable
        // function will be returned. The callable function is invoked by
        // the scheduler to perform the scheduler work.
        resumable mutator final : (
            callable : CallablePrototype
        )(
            resumeFunc : ()()
        )

        // call this method to cancel the task
        cancel final : ()()

        // call this method to suspend the task prior to the next yield
        suspend final : ()()

        // call this method to resume processing (which may cause a suspended
        // yield result)
        resume final :()()
    }

    then mutator final : (producer : Producer)(thenFunc : ThenCallbackPrototype)
}
*/

scheduleProducer final : ()(producer :) = {

    // ... likely not the way a scheduler would be implemented but ...

    runOnAnotherThread final : ()(producer :) deep {
        //...
        forever {

            // ...  
            reschedule final : ()(producer : ) = {
                // ...  
            }
            suspend final : ()(producer : ) = {
                // ...  
            }
            remove final : ()(producer : ) = {
                // ...  
            }

            callable := producer.resumable = {
                // ...
            }

            switch status := callable() {
                case Coroutine.Run:           {
                    reschedule(producer)
                    break
                }
                case Coroutine.Suspend:       {
                    suspend(producer)
                    break
                }
                case Coroutine.Compete:       {
                    remove(producer)
                    break
                }
            }
            // ...
        }
        //...
    }

    // ...
    runOnAnotherThread(producer)
}

myTask := countForever()

producer := myTask.then = {
    // ...
}

// after the task is setup, a call to `producer.callable()` must be
// invoked at least once
scheduleProducer(producer)

// at end of scope cause the consumer to cancel the task
defer producer.cancel()

// ...
````


#### Using `await` to create asynchronous call chains

A `task` function can call other `task` functions easily by using the `await` keyword to obtain a single result from a called `task` method. This creates a method by which one `task` can begin executing another `task` where all the tasks will be scheduled collectively. At any time the entire chain of tasks can be cancelled where the compiler will schedule cleanup of the tasks.

````zax
scheduleTaskProducer final : ()(producer :) = {
    // ...
}

func1 final : (result : Integer)() task = {
    // ...
    return result
}

func2 final : (result : String)(input : Integer) task = {
    
    // ...
    value := await func1()
    // ...

    return result
}

func3 final : ()(input : Integer) task = {
    // ...
    value := await func2(input)
    // ...
}

myTask := func3(42)

// ... setup ...

producer := myTask.then = {
    // ...
}

scheduleTaskProducer(producer)

defer myTask.consumer.cancel()

// ...
````


#### Using `yield` `suspend` and the captured `result()`

The `yield` combined with `suspend` can be used to cause a task to intentionally sleep until externally resumed.

One caveat, if a task self-suspends itself the task will has no intrinsic method to wake itself up. Likewise, no event occurs to indicate a `suspend` has occurred. Coordinating a self-suspend and external resume is undefined and any appropriate design pattern can be used to solve this concern.

````zax
scheduleTaskProducer final : ()(producer :) = {
    // ...
}

externalThreadThatWillResumeTaskAtSomeFutureTime : ()(producer : ()()) = {
    // ...
    producer.resume()
    // ...
}

func final : (result : Integer)() task = {

    // ...

    // A vanilla yield will not suspend the task but it will cooperatively
    // yield to another task. Also `yield` allows for an external process a
    // key point in a task more ideal to perform operations like external
    // suspending or early cancellation.
    yield

    // ...

    // cause the task to `suspend` until `resume()` is called by an
    // external entity; should `resume()` be called prior to suspending the
    // task will be rescheduled immediately; multiple `resume()` calls
    // prior to suspending will only resume a single time.
    yield suspend

    // ...

    return result
}


myTask := func(42)

// ... setup ...

producer := myTask.then = {
    // ...
}

scheduleTaskProducer(myTask.producer)

// ...

externalThreadThatWillResumeTaskAtSomeFutureTime(producer)

// ...

defer myTask.cancel()
````


#### `task` cancellation

Whenever a function qualified as `task` encounters an `await` or `yield` statement, a function might perform a quick exit from the current function if the `task` is cancelled. This is done to ensure predictable `task` function exit points and to ensure `task` functions are not left in undetermined state. Function cleanup occurs as needed and the compiler can insert code to cause additional clean-up calls for any `task` scheduler as needed. If `await`, `yield` are called after a task is cancelled during the automatic cleanup, those statements will become new instant exit points in whatever routines called them.

Functions will not throw exceptions upon cancellation. Any non-constructed/non-assignment variables from an awaited function will not complete in the event of a cancellation (thus any non-constructed variables will also not be destructed in turn).

In the example below two function exit points exist in the `fetchRandomDataForever` function. The `await` can be automatically converted to a quick exit from the function if the awaited task is cancelled. The `yield` can also convert into quick exit from the function.

````zax
fetchRandomNumber final : (result : Integer)() task = {
    // ...
    return result
}

fetchRandomDataForever final : (result : Integer)() task = {
    forever {
        // the function can exit if the awaited `task` is cancelled whereupon
        // the `number` type is not constructed nor assigned a value
        number := await fetchRandomNumber()

        // the function can exit if the yielded `task` is cancelled instead of
        // producing another value whereupon the `number` is not returned from
        // the `task` but instead the `task` continues its cancellation process 
        yield return number
    }
    [[never]]
}

myTask := fetchRandomDataForever()

provider := myTask.then = {
    // ...
}

// ...

// cause the scheduled `task` to quick exit
provider.cancel()

// ...
````


#### `task` deep copy

In the same manner `promise` functions can use the `deep` keyword to ensure any types that require deep copy to be passed to another thread, the `deep` keyword can be applied to `task` function arguments as well as to the entire `task` function.

If the `deep` keyword is placed on a function's argument then that argument will cause a deep copy to occur prior for the data being received by a scheduled `task`. If the `deep` keyword is placed on an entire function, then any input or returned arguments that support `deep` copy will automatically cause a `deep` copy to be performed prior to the data being sent to or from a function.

````zax
// only the `input` argument will utilize the `deep` copy mechanism
func1 final : (result : Integer)(input : String deep) task = {
    // ...
}

// all arguments will utilize the `deep` copy mechanism
func2 final : (result : String)(input : String) deep task = {
    // ...
}

value := "momma bear"

myTask1 := func1(value)
myTask2 := func2(value)

// ...
````


### Using `channel` to schedule asynchronous function calling

A `channel` operates similar to a `task`. The key difference is that a `channel` can be called once or more times with additional input arguments while continuing to execute within the context of the same function call. Like a `task`, a `channel` can return data one or more times. Like a `task`, a `channel` can be scheduled and rescheduled for future processing later. A `channel` can also suspend itself allowing for a `then` function callback to proactively request the `channel` be reactivated and continue processing. Just like a task, when a `channel` is no longer needed, the `channel`'s `cancel` function can be called to indicate the `channel` should clean itself up and should only continuing scheduling itself for the purpose of final cleanup.

A `channel` function can be suspended and resumed or cancelled in exactly the same manner as a `task`, and a `channel` function can `await` a `task` function.

In the example below a generator `channel` function lazily counts forever starting at `0` and will read from the channel to reset the number at a new sequence. When the `channel` function is first executed, the `channel` function does not actually run but returns `producer` and `consumer` types after performing initial setup with a channel type passed into the `channel` function. The `producer` type can be sent to a different thread of execution whereupon the `channel` can be scheduled until completed. The `consumer` type can be used to attach a callback to receive a callback every time a result the `channel` returns. The `yield` keyword operates like the return keyword, except the function does not exit. Instead the `yield` causes the current values to be returned from the `then` callback and the `channel` will continue to run (after `activate()` is called). The compiler will break the `channel` function into sub-functions that can be started and stopped for scheduling purposes.


````zax
/*
enum Coroutine.Status {
    Ready,
    Suspend,
    Complete
}
*/

scheduleChannelProducer final : ()(producer :) = {
    // ...
}

// the function runs forever, which is okay for `channel` functions as they can
// have their execution cancelled when they are no longer needed
countForever final : (result : Integer)(input : Integer) channel = {
    /*

    Cancellation :: type {
        // ...
        cancelled mutator final : (isCancelled : Boolean)()
        // ...
    }

    // auto variable declared named `cancellation` auto-captured for channels
    cancellation : Cancellation

    */

    // checking for cancellation after yielding is appropriate to exiting
    // a channel earlier than normal completion
    while !cancellation.cancelled {
        ++result
        yield return result

        // `awaitable()` is a compiler provided implicit capture of a function
        // that returns true if there is data available to read
        if awaitable() {
            // read the data from the consumer, if no data was available
            // the function will suspend itself
            result = await
        }
    }

    // exiting without `return` is legal in a channel that is cancelled
}

/*

TemplatedChannelResult :: type {
    ThenCallbackPrototype final : ()(result : Integer)

    Producer :: type {
        CallablePrototype final : (status : Coroutine.Status)()

        // Once a resume function is installed by the scheduler, the callable
        // function will be returned. The callable function is invoked by
        // the scheduler to perform the scheduler work.
        resumable mutator final : (
            callable : CallablePrototype
        )(
            resumeFunc : ()()
        )

        // call this method to cancel the channel
        cancel final : ()()

        // call this method to suspend the channel prior to the next yield
        suspend final : ()()

        // call this method to resume processing (which may cause a suspended
        // yield result)
        resume final :()()

        // additional method not present in a task to send data to the channel
        send : ()(input : Integer)
    }

    then mutator final : (producer : Producer)(thenFunc : ThenCallbackPrototype)
}
*/


myChannel := countForever(myChannel)

producer := myChannel.then = {
    // ... receive results ...
}

scheduleProducer(producer)

// send to the function channel as many data writes as desired
producer.send(33)
producer.send(17)
producer.send(1)

// at end of scope cause the consumer to cancel the channel
defer producer.cancel()

// ...
````


### Allocators and threading

When allocation is performed on the context using the standard allocator operator (`@`), the allocator will use the standard allocator (`___.allocator`) which is usually set to the sequential allocator (`___.sequential.allocator`). The sequential allocator is typically faster than the parallel allocator (`___.parallel.allocator`) as the sequential allocator only needs to allocate memory using thread unaware allocation algorithms and frees using the thread unaware algorithms.

The downside to a sequential allocator is that allocation and deallocation assume thread local heaps which can have unintended consequences. The sequential allocators do not use any thread locking mechanisms as they assume only the current thread can manipulate a thread's heap at any given time without locks. However, if one thread performs a thread heap allocation and another thread performs the deallocation then that memory needs to be reinserted into the original thread's heap (which cannot be done by another thread). In this specific case, the freed memory is inserted into a thread safe dangling list for the original thread that performed the allocation. When the original thread wakes up and decides to examine the dangling points for that thread then the dangling memory blocks can be reinserted back into the original thread heap (which usually happens during parallel allocation of if more space is needed for the thread heap). If the originating thread has quit prior to freeing a thread heap allocation then that dangling memory should be recycled into another thread's heap when freed.

A double "at" symbol, known as the parallel allocator operator (`@@`), is a compiler shortcut to force allocation using parallel/thread safe allocators for the types (and its contained types). When the parallel allocator operator is encountered the standard allocator is replaced by the parallel allocator temporarily for the current allocation (and reset back to the original allocation completes). An "at" symbol with a "bang", known as the sequential allocator operator (`@!`), forces the sequential allocator to be used for the type (and its contained types) by replacing the standard allocator (`___.allocator`) temporarily.

Whenever a `strong` pointer type is allocated, the standard allocator (`___.allocator`) in the context is replaced by the parallel allocator temporarily (i.e. assigned to `___.parallel.allocator`). This is done to ensure that any allocated data is entirely allocated in a thread safe manner (and deallocated later in a thread safe manner). Once the allocation is complete, the original standard allocator is restored allowing any future constructor and allocations of other types to use the potentially faster sequential (`___.sequential.allocator`) allocator if that allocator was previously in use.

Caution: care must be taken when transferring an allocated `own` pointer to a `strong` pointer. Pointers qualified as `unique` or `own` are allocated using the standard allocator which is typically set to the sequential allocator by default. If an `own` pointer gets transferred later into a `strong` pointer or across thread boundaries, then consider using parallel allocators.

A qualifier of `deep` can be applied to the function definition where all input and output arguments automatically have `deep` applied to each argument type where appropriate. Where `deep` is applied to a function, another advantage is any captured values also have the `deep` attribute applied, i.e. any copies performed on captured values will cause a `deep` copy to be performed. If a non-final function is qualified as `deep` then any replacement assignment of the function must also be qualified as `deep`.

Functions declared as `promise`, `task`, or `channel` are implicitly asynchronous. The parallel allocators are automatically substituted over the sequential allocators for a function call implicitly asynchronous or functions declared explicitly asynchronous using the `[[asynchronous]]` directive. The previous allocators are restored automatically upon exit of the asynchronous function's call. When combined with `deep`, copy operations will cause parallel allocators to be used over sequential allocators during `deep` data copies. When all data is copied out from the previous calling thread and the new thread is executing, the sequential allocators will be used as the default allocators on another thread as normal. Asynchronous functions declared with `deep` also have their return arguments passed into a `then` routine for automatic copy of the returned results and the parallel allocators are once again substituted as the default allocator.

A sequential allocator is designed to favour allocations that deallocate from the same thread for speed efficiency reasons. But memory deallocated on different threads should eventually cause dangling memory to become reintegrated into the original thread's heap (at the cost of efficiency).

The language does not enforce the rules of allocation, but instead gives tools to ensure that allocations are as optimal as possible depending on the coder's intentions.


````zax
SimpleType :: type {
    value : Integer
}

MyType :: type {
    value1 : Integer
    value2 : String
    value3 : SimpleType * @     // might be allocated using the parallel or
                                // sequential allocator depending how
                                // the container type allocated the type
}

DoubleType :: type {
    myType1 : MyType * own @    // even though standard allocator was
                                // specified, parallel allocation will happen
                                // in this example (as the container was
                                // allocated using the parallel allocator)
    myType2 : MyType * own @    // same as above

    myType3 : MyType * own @!   // override any request to use the parallel
                                // allocator and allocate using the sequential
                                // allocator
}


doubleType : DoubleType * own @@ // allocate using the parallel allocator
````


### Context and threading

A new context instance must be set-up for any spawned thread/fiber as the context variable's (i.e. `___`) instance is tied to an individual thread/fiber. This allows the context instance to use optimized thread-unaware operations.
