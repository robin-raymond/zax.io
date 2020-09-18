
# [Zax Programming Language](index.md)

## Concurrency

### Using `atomic` types

Any type can be declared with an `atomic` keyword. When the atomic keyword is applied the type operates as normally the type would except every operator or function call on the type is run exclusively across all CPUs. This ensures the contents of a type cannot be accessed by more than one thread of execution at a time.

The `atomic` keyword can also be applied to intrinsic types, like `Integer`, where specialized atomic code will be used for the fastest possible atomic operations on these intrinsic types.

````zax
puid : Integer atomic

func : ()() = {
    // no matter how many times the function is called, the `uniqueValue` will
    // always contain a unique value because the `puid` Integer is operated upon
    // atomically
    uniqueValue := puid++

    // ...
}


spawnThreadThatCallsFuncForever final : ()() = {
    // ...
    
    forever
        func()

    // ...
}

spawnThreadThatCallsFuncForever()
spawnThreadThatCallsFuncForever()
````


### Using the `deep` type qualifier as a method to ensure type data separation across threads

For speed and efficiency reasons, types may utilize shallow copy and data sharing across type instances when a variable is copied from one type instance to another. A true copy of a type may never actually be performed in such a scenario. Copying the data may be expensive and non desirable.


#### Efficient `immutable` type data sharing and concurrency

Types marked as `immutable` are an excellent example where this kind of optimization can be useful since the immutable type might need to allocate data for storage but once allocated need not modify the content ever again. Copying immutable variables from one instance to another could be expensive given each copy would need another allocation, a copy of the contents and a deallocation when the immutable type is no longer needed.

Rather than performing a copy whenever the immutable type is passed to functions, a simple `handle` pointer to the real data might be utilized. A `handle` pointer keeps a simple reference count to a type and disposes of the data when the final instance of a type is disposed and thus is perfectly suitable for `immutable` data sharing. While a `strong` pointer could be used instead of a `handle` type, a `strong` pointer incurs additional concurrency overhead every time a reference count is incremented or decremented since the reference count for `strong` pointers must be thread safe. This overhead can cause a CPU to operate less efficiently as it can disrupt things like CPU branch predictability and CPU caches.

A qualifier of `deep` can be applied to a type to ensure a full copy of the type is performed prior to transferred the type's instance to a new thread. The `deep` qualifier can be used to ensure a completely independent copy of a type is made so a copy of an immutable type is made whenever the type is passed to a different thread.

A qualifier of `deep` can be applied to the function definition where all input and output arguments automatically have `deep` applied to each argument type where appropriate. Where `deep` is applied to a function, another advantage is any captured values also have the `deep` attribute applied, i.e. any copies performed on captured values will cause a `deep` copy to be performed. Related, the parallel allocators are automatically substituted over the sequential allocators for a function call declared as `deep`. If a non-final function is marked `deep` then any replacement assignment of the function must also be marked as `deep`.

While seemingly a [`last` pointers/references](pointers.md#using-the-last-type-qualifier-to-optimize-content-transfer) method can seemingly be used to transfer out the contents of the data to a new type's instance prior to transfer to a new thread but the `last` does not guarantee any shared state is indeed the final copy. This mechanism can only work if the passed in type is truly the `last` instance of a type before it's disposal.


````zax
MyType :: type {
    animal : String = "alligator"
    // ...

    +++ final : ()(rhs : MyType constant &) = {
        // this version of the constructor will be called under normal
        // conditions where a copy of the type needs to be made
        animal = rhs.animal
    }
    +++ final : ()(rhs : MyType last &) = {
        // this version of the constructor will be called when the
        // `last` instance of a type is being transferred from the source
        // type to the destination type
        animal = rhs.animal
    }
    +++ final : ()(rhs : MyType constant deep &) = {
        // this version of the constructor will be called when a `deep`
        // copy of the contents must be performed
        animal = rhs.animal
    }

    merge : (result : MyType&)(rhs : MyType constant &) deep = {
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
// the `MyType last &` constructor only performed a shallow copy so the
// memory backing the types could still be shared
myTypeFromLast := returnAsTemporary(myType)

// the `deep` qualifier is applied to the type and the `deep` version of the
// copy constructor will be removed and `myTypeFromDeep` will truly contain
// an independent copy of the type's instance
myTypeFromDeep := myType as deep

anotherType : MyType

// the compiler will treat all input/output arguments as being defined as
// `deep` types
anotherType.merge(myType)
````


### Adding a deep qualifier on a type

````zax
MyType :: type deep {
    value1 : Integer* @
    value2 : String
}

// even though the `promise` isn't declared as `deep` the type is declared as
// `deep` this the type will automatically be treated as a deep type during
// calls to `promise` or `task` functions
myFunc final : ()(myType : MyType) promise = {
    // ...
}


myType : MyType

// a deep copy is performed at this moment on the type
later := myFunc(myType)

later.then = {
    // ...
}

// ...

// ... to be potentially run on a different thread...
later.callable()
````


### Creating asynchronous functions from captured function invocations

Using function invocation capturing and function chaining, an invocation of a function can be captured (but not called immediately). Another new function can be created to capture the results of the not yet invoked function and feed that result into another function after. This final newly minted function can be sent to a different thread to execute where the return result will invoke a callback when the function is complete.

On caution, invoked captured functions are not expected to be marked as deep `deep` where the compiler will not issue a warning if the `deep` qualifier was not applied. If a invoked capture function isn't designed for thread safety, unexpected behaviors can ensue.

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

// capture an invocation of a function
// (doesn't actually call the function)
later := [] myFunc(42, "meaning of life" as deep)

// chain the output of the function to the input of the `then` function
laterThen := later | then

// send the function to a different thread to execute
invokeOnAnotherThread(laterThen)
````


### Creating asynchronous function out of `lazy` functions

A `lazy` function can be converted into an asynchronous function (see [lazy functions](lazy.md)). When a function marked as `lazy` returns a `lazy` function, the `lazy` function can be passed and invoked from an alternative thread.

On caution, `lazy` functions are not expected to be marked as deep `deep` where the compiler will not issue a warning if the `deep` qualifier was not applied. If a `lazy` function isn't designed for thread safety, unexpected behaviors can ensue.

````zax

runOnThread final : ()(callable : ) = {
    // ...
    result := callable()
    // ...
}

func final : (value : Double)(algorithm : String deep) lazy = {
    forever {
        // ... complex algorithm ...
        yield value
    }
    [never]
}

later := func()

runOnThread(later)

// ...
````


### Creating asynchronous functions using `promise`

Calling a function labelled as `promise` does not invoke a direct call to the function. Instead a function pointer is returned that can be transferred and executed on another thread or queue later. When the promise is filled a callback to a `then` function occurs indicating the promise is filled and the results are sent to the then function.

````zax
myFunc final : ()(value1 : Integer) promise = {
    // ...
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
    // ...
    callable()
    // ...
}

// `myFunc` will get executed when `callable()` is called from the other thread
executeCallableOnAnotherThread(later.callable)
````


#### Performing automatic `deep` copy during `promise` calls on arguments

By declaring pass by-value input or output argument variable types on a `promise` function calls as `deep`, any calls passing of the type will automatically cause a `deep` copy constructor to be called when the copy of the value is created. This ensures a `deep` copy is performed and helps prevent concurrency issues on the types that would normally create shallow copies otherwise.

````zax
myFunc final : ()(value1 : Integer, value2 : String deep) promise = {
    // ...
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
    // ...
    callable()
    // ...
}

// `myFunc` will get executed when `callable()` is called from the other thread
executeCallableOnAnotherThread(later1.callable)
executeCallableOnAnotherThread(later2.callable)
````


#### Performing automatic `deep` copy during `promise` calls on all arguments

By declaring a promise function as `deep`, all pass by-value input or output argument variable types will automatically cause a `deep` copy constructor to be called when the copy of the value is created. This ensures a `deep` copy is performed and helps prevent concurrency issues on the types that would normally create shallow copies otherwise.

````zax
myFunc final : ()(value1 : Integer, value2 : String) deep promise = {
    // ...
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
    // ...
    callable()
    // ...
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
    // ...
    callable()
    // ...
}

executeCallableOnAnotherThread final : ()(callable : EmptyFunctionType) = {
    // ...
    callable()
    // ...
}

myFunc final : (result : String)(value1 : Integer) deep promise = {
    // ...
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
        // ...
    }

    // pass the callable function returned from the promise into a
    // function that will execute the result back onto the main thread
    executeCallableOnMainThread(resultCallbackFunc(result).callable)
}

// `myFunc` will get executed when `callable()` is called from the other thread
executeCallableOnAnotherThread(later.callable)
````


### Using `task` to schedule asynchronous function calling

A `task` operates similar to a `promise`. The key difference is that a `task` can return data once or more times and can be scheduled and rescheduled for future processing later. A `task` can also suspend itself allowing for the a `then` callback to proactively request the `task` be reactivated and continue processing. When a `task` is no longer needed, the `task`'s `cancel` function can be called to indicate the `task` should clean itself up and should only continuing scheduling itself for the purpose of final cleanup.

In the example below a generator `task` function lazily counts forever starting at `0`. When the `task` function is first executed, the `task` does not actually run but returns `producer` and `consumer` types. The `producer` type can be sent to a different thread of execution whereupon the `task` can be scheduled until completed. The `consumer` type can be used to attach a callback to receive a callback every time a result the `task` returns. The `yield` keyword operates like the return keyword, except the function does not exit. Instead the `yield` causes the current values to be returned from the `then` callback and the `task` will continue to run (after `activate()` is called). The compiler will break the `task` function into sub-functions that can be started and stopped for scheduling purposes.

````zax
// the function runs forever, which is okay for `task` functions as they can
// have their execution cancelled when they are no longer needed
countForever final : (result : Integer)() task = {
    forever {
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
        // the consumer scheduler will call this routine to perform the
        // next batch of task work
        callable : (status : Coroutine.Status)()

        // this routine tells the co-routine to start the cancellation process
        // the next time `callable()` is invoked
        cancel : ()()

        // this function must be installed by the consumer which will
        // schedule a call to the co-routine `callable()` function; this
        // function is called by the producer when it needs to schedule
        // itself for more work after an external event has completed
        resume : ()()
    }

    consumer : :: type {
        // result is yielded
        then : ()(result : Integer)

        // returns true if the co-routine is completed its execution
        isCompleted mutator : (completed : Boolean)()

        // consumer side can install a helper routine which captures a producer
        // and schedules a call the producer.callable() later
        // (this routine should be called after receiving a `then` callback)
        activate : ()()

        // consumer side can install a helper routine which captures a producer
        // type, calls the `producer.cancel()` when invoked followed by
        // scheduling a call the `producer.callable()` later
        cancel : ()()
    }
}
*/

scheduleProducer : ()(producer :) = {
    
    invokeCallable final : ()(producer :) = {
        switch status := producer.callable() {
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
    // ...
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

// after the task is setup, a call to `producer.callable()` must be
// invoked at least once
scheduleProducer(myTask.producer)

// at end of scope cause the consumer to cancel the task
defer myTask.consumer.cancel()

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

myTask.consumer.then = {
    // ...
}

scheduleTaskProducer(myTask.producer)

defer myTask.consumer.cancel()
````


#### Using `suspend` and the captured `result()`


````zax
scheduleTaskProducer final : ()(producer :) = {
    // ...
}

EmptyFunctionPrototype final : ()()

externalThreadThatWillResumeTask : ()(resume : EmptyFunctionPrototype) = {
    // ...
    resume()
    // ...
}

func final : (result : Integer)() task = {

    // `resume` is a function that is implicitly captured for all task
    // functions; this function was setup by the consumer; when the
    // method `resume()` is called, the suspended task will ask the consumer's
    // resume() function to reschedule a call to `producer.callable()` which
    // causes the task to resume from the point of suspension.
    externalThreadThatWillResumeTask(resume)

    // cause the task to `suspend` until `resume()` is called by an
    // external entity; should `resume()` be called prior to suspending the
    // task will be rescheduled immediately
    suspend

    return result
}


myTask := func(42)

// ... setup ...

myTask.consumer.then = {
    // ...
}

scheduleTaskProducer(myTask.producer)

defer myTask.consumer.cancel()
````


#### `task` cancellation

Whenever a function marked as `task` encounters an `await`, `yield` or a `suspend` statement, the function might cause a quick exit from the current function if the `task` is cancelled. This is done to ensure predictable `task` function exit points and to ensure `task` functions are not left in undetermined state. Function cleanup occurs as needed and the compiler can insert code to cause additional clean-up calls for any `task` scheduler as needed. If `await`, `yield` or `suspend` is called after a task is cancelled during the automatic cleanup, those statements will become new instant exit points in whatever routines called them.

Functions will not throw exceptions upon cancellation. Any construction/assignment of variables from an awaited function will not complete in the event of a cancellation (thus any constructed variables will also not be destructed in turn).

In the example below two function exit points exist in the `fetchRandomDataForever` function. The `await` can be automatically converted to a quick `return` from the function if the awaited task is cancelled. The `yield` can convert into quick `return` from the function.

````zax
fetchRandomNumber final : (result : Integer)() task = {
    // ...
}

fetchRandomDataForever final : (result : Integer)() task = {
    forever {
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

    // ...
}

// ...

// cause the scheduled `task` to quick exit
myTask.consumer.cancel()

// ...
````


#### `task` deep copy

In the same manner `promise` functions can use the `deep` keyword to ensure any types that require deep copy to be passed to another thread, the `deep` keyword can be applied to `task` function arguments as well as to the entire `task` function.

If the `deep` keyword is placed on a function's argument then that argument will cause a deep copy to occur prior to the data being received by a scheduled `task`. If the `deep` keyword is placed on an entire function, then any input or returned arguments that support `deep` copy will automatically cause a `deep` copy to be performed prior to the data being sent to or from the function.

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
// the function runs forever, which is okay for `channel` functions as they can
// have their execution cancelled when they are no longer needed
countForever final : (result : Integer)(input : Integer) channel = {
    forever {
        yield result
        ++result

        // `awaitable()` is a compiler provided implicit capture of a function
        // that returns true if there is data available to read
        if awaitable() {
            // read the data from the consumer, if no data was available
            // the function would suspend itself
            result = await
        }
    }
    [[never]]
}

/*
enum Coroutine.Status {
    Suspend,
    Reschedule,
    Complete
}

TemplatedChannelResult :: type {

    producer : :: type {
        // the consumer scheduler will call this routine to perform the
        // next batch of channel work
        callable : (status : Coroutine.Status)()

        // this routine tells the co-routine to start the cancellation process
        // the next time `callable()` is invoked
        cancel : ()()

        // this function must be installed by the consumer which will
        // schedule a call to the co-routine `callable()` function; this
        // function is called by the producer when it needs to schedule
        // itself for more work after an external event has completed
        resume : ()()
    }

    consumer : :: type {
        // function is installed by the compiler and assigned to point
        // to the `writer` function provided by the channel type
        operator () : ()(input : Integer)

        // result is yielded
        then : ()(result : Integer)

        // returns true if the co-routine is completed its execution
        isCompleted mutator : (completed : Boolean)()

        // consumer side can install a helper routine which captures a producer
        // and schedules a call the producer.callable() later
        // (this routine should be called after receiving a `then` callback)
        activate : ()()

        // consumer side can install a helper routine which captures a producer
        // type, calls the `producer.cancel()` when invoked followed by
        // scheduling a call the `producer.callable()` later
        cancel : ()()
    }
}
*/

scheduleProducer : ()(producer :) = {
    
    invokeCallable final : ()(producer :) = {
        switch status := producer.callable() {
            case Coroutine.Suspend:       break
            case Coroutine.Reschedule:    scheduleProducer(producer)
            case Coroutine.Compete:       break
        }
    }

    // ...
    callInvokeCallableLaterWithProducer(producer)
    // ...
}

MyChannel :: type {
    ConsumerDataWrittenNotificationPrototype final : ()()
    ConsumerWriteFunctionPrototype final: ()(input : Integer)
    ProducerReadFunctionPrototype final: (input : Integer)()

    operator : (
        consumerWriteData : ConsumerWriteFunctionPrototype,
        producerReadData : ProducerReadFunctionPrototype
    )(
        callWhenConsumerDataWritten : ConsumerDataWrittenNotificationPrototype
    ) = {
        //...
    }
}

myChannel : MyChannel

myChannelFunc := countForever(myChannel)

/*

// Compiler will perform the following steps:

result : TemplatedChannelResult

writer: , reader: = myChannel(channelFunction.notifyDataWrittenByConsumer)

result.consumer.operator() = writer

channelFunction.internalBind(reader)

*/


myChannelFunc.consumer.then = {
    // reactivate the task to perform the next count
    defer myChannelFunc.consumer.activate()
    // ...
}

myChannelFunc.producer.resume = [producer = myChannelFunc.producer] {
    scheduleProducer(producer)
}

myChannelFunc.consumer.activate = [producer = myChannelFunc.producer] {
    scheduleProducer(producer)
}

myChannelFunc.consumer.cancel = [producer = myChannelFunc.producer] {
    producer.cancel()
    scheduleProducer(producer)
}

// after the task is setup, a call to `producer.callable()` must be
// invoked at least once
scheduleProducer(myChannelFunc.producer)

// send to the function channel as many data writes as desired
myChannel.consumer(33)
myChannel.consumer(17)
myChannel.consumer(1)

// at end of scope cause the consumer to cancel the task
defer myChannelFunc.consumer.cancel()

// ...
````


### Allocators and threading

When normal allocation is performed on the context using the standard allocator operator (`@`), the allocator will use the standard allocator (`___.allocator`) which is usually set to the sequential allocator (`___.sequential.allocator`). The sequential allocator is typically faster than the parallel allocator (`___.parallel.allocator`) as the sequential allocator only needs to allocate memory using thread unaware allocation algorithms.

A double at symbol, known as the parallel allocator operator (`@@`), is a compiler shortcut to request allocation using parallel/thread safe allocators for the types and its contained types. When the parallel allocator operator is encountered the standard allocator is replaced by the parallel allocator temporarily automatically. An at symbol with a bang, known as the sequential allocator operator (`@!`), forces the sequential allocator to be used for the type and its contained types by replacing the standard allocator (`___.allocator`) temporarily automatically.

Whenever a `strong` pointer type is allocator or a `deep` copy is performed, the standard allocator (`___.allocator`) in the context is replaced by the parallel allocator temporarily (i.e. assigned to `___.parallel.allocator`) automatically. This is done to ensure that any allocated data is entirely allocated in a thread safe manner (and deallocated later in a thread safe manner). Once the allocation is complete, the original standard allocator is restored allowing any future constructor and allocations of other types to use the potentially faster sequential (`___.sequential.allocator`) allocator if that was previously in use.

Caution: care must be taken when transferring an allocated `own` pointer to a `strong` pointer. Pointers marked as `own` are allocated using the standard allocator which is typically set to the sequential allocator by default. If an `own` pointer gets transferred later into a `strong` pointer, the standard allocator should be replaced with the parallel allocator.


````zax
SimpleType :: type {
    value : Integer
}

MyType :: type {
    value1 : Integer
    value2 : String
    value3 : SimpleType* @      // might be allocated using the parallel or
                                // sequential allocator depending how
                                // the container type allocated the type
}

DoubleType :: type {
    myType1 : MyType* own @     // even though standard allocator was
                                // specified, parallel allocation will happen
                                // in this example (as the container was
                                // allocated using the parallel allocator)
    myType2 : MyType* own @     // same as above

    myType3 : MyType* own @!    // override any request to use the parallel
                                // allocator and allocate using the sequential
                                // allocator
}


doubleType : DoubleType* own @@ // allocate using the parallel allocator
````


### Context and threading

A new context instance must be set-up for any spawned thread/fiber as the context variable's (i.e. `___`) instance is tied to an individual thread/fiber. This allows the context instance to use optimized thread-unaware operations.
