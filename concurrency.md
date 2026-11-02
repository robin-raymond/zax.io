
# [Zax Programming Language](index.md)

## Concurrency

Current immutable and unsafe-pliability behavior is defined by
[Zax qualifiers](language/qualifiers.md). Immutability addresses value change,
not lifetime, shared backing storage, synchronization, allocation, safe
transfer, or reference-count safety. The remaining concurrency mechanisms on
this page are legacy input.
Current `copy`/`deep`/`move`/`last` meaning is defined by
[Zax transfer stances](language/transfer-stances.md). Those stances do not by
themselves establish thread safety, sendability, shared-backing safety, or async
lifetime.

Current reference and ownership boundaries are defined by
[Zax lifetimes and references](language/lifetimes-and-references.md#thread-boundary)
and [Zax pointers and arenas](language/pointers-and-arenas.md#arenas), including
its separate [thread-transfer rules](language/pointers-and-arenas.md#thread-transfer).

The retired material in this page proposed automatic `deep` propagation and
automatic `last` results for asynchronous work. Those claims are no longer
current. The unresolved question is which explicit transfer, capture, lifetime,
allocation, and synchronization contracts are required when a value crosses a
thread or suspension boundary. Representative source and deciding pressure are
preserved by [raw async input](project/raw/async.md).


### Creating asynchronous functions from captured function invocations

Using function invocation capturing and function chaining, an invocation of a function can be captured (but not called immediately). Another new function can be created to capture the results of the not yet invoked function and feed that result into another function after. This final newly minted function can be sent to a different thread to execute where the return result will invoke a callback when the function is complete.

The transfer, ownership, lifetime, and synchronization requirements of a
captured invocation crossing a thread boundary remain unresolved. This legacy
example does not establish that one stance makes the capture thread-safe.

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
later := [] myFunc(42, "meaning of life")

// chain the output of the function to the input of the `then` function
laterThen := later | then

// send the function to a different thread to execute
invokeOnAnotherThread(laterThen)
````


### Creating asynchronous function out of `lazy` functions

A `lazy` function can be converted into an asynchronous function (see [lazy functions](lazy.md)). When a function qualified as `lazy` returns a `lazy` function, the `lazy` function can be passed and invoked from an alternative thread.

The transfer and lifetime contract for sending a deferred or lazy callable to
another thread remains future async work.

````zax
runOnThread final : ()(callable : ) = {
    // ...
    result := callable()
    // ...
}

func final : (value : Double)(algorithm : String) lazy [<asynchronous>] = {
    forever {
        // ... complex algorithm ...
        yield return value
    }
    [<never>]
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
    ThenCallbackPrototype :: alias type ()()

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


#### Transfer across a `promise` boundary

Legacy material proposed automatic `deep` capture for individual parameters or
an entire `promise`. That behavior is not current. Future async work must decide
which explicit `copy`, `deep`, `move`, ownership, and lifetime contracts make a
captured value safe to use after suspension or on another thread.


#### Returning variables from an `promise` function asynchronously

A `promise` function can return arguments. The returned arguments are returned by the `promise` function and passed into a `then` function attached to the resulting structure of a `promise`. A promises result will only ever occur once. As a `then` function is set before the execution of a `promise` function, a function's results can never complete prior to a `then` function being attached to the returned `promise` type.

````zax
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

myFunc final : (result : String)(value1 : Integer) promise = {
    // ...
}

/*
TemplatedPromiseResult :: type {
    ThenCallbackPrototype :: alias type ()(result : String)

    then mutator : (callable : ()())(thenFunc : ThenCallbackPrototype)
}
*/

// calling laterFunc function causes all the values passed into the function to
// be captured where the function can be executed later
later := myFunc(42)

callable := later.then = {
    // called after the `myFunc` is completed with the return result
    
    resultCallbackFunc final : ()(result : String) promise = {
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

Fiber :: type {
    CallablePrototype :: alias type (status : Coroutine.Status)()

    callback mutator : ()(callable : CallablePrototype) = {
        // ..
    }

    run final : CallablePrototype = {
    }
}

RunContext :: type {

}

TemplatedTaskResult :: type {
    ThenCallbackPrototype :: alias type ()(result : Integer)
    CallablePrototype :: alias type (status : Coroutine.Status)()

    Producer :: type {

        // call this method to cancel the task early (immediately prior to
        // yield)
        cancel final : ()()

        // call this method to suspend the task (immediately prior to yield
        // next result)
        suspend final : ()()

        // call this method to resume processing (which may cause a suspended
        // yield result)
        resume final :()()
    }

    RunnerPrototype :: alias type (
        producer : Producer
    )(
        // used to allocate a working stack within this fiber
        fiber : ,
        // used to allow a task to probe for early cancellation of an async
        // routine while processing
        cancellation : ,
    )

    then mutator final : (runner : RunnerPrototype)(thenFunc : ThenCallbackPrototype)
}
*/

scheduleProducer final : (producer : )(runner :) = {

    // ... likely not the way a scheduler would be implemented but ...

    fiber := createNewFiber()
    cancellation := createNewCancellation()

    producer = runner.install(fiber,  ) = {
        // ...
    }

    runOnDifferentThread final : ()(fiber :) = {

        forever {
            // ...  
            reschedule final : ()(fiber : ) = {
                // ...  
            }
            suspend final : ()(fiber : ) = {
                // ...  
            }
            remove final : ()(fiber : ) = {
                // ...  
            }

            switch status := fiber.run() {
                case Coroutine.Run:           {
                    reschedule(fiber)
                    break
                }
                case Coroutine.Suspend:       {
                    suspend(fiber)
                    break
                }
                case Coroutine.Compete:       {
                    remove(fiber)
                    break
                }
            }
            // ...
        }
        //...
    }

    // ...
    runOnDifferentThread(fiber)
    return producer
}

myTask := countForever()

runner := myTask.then = {
    // ...
}

// after the task is setup, a call to `producer.callable()` must be
// invoked at least once
producer = scheduleProducer(runner)

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
    [<never>]
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


#### `task` transfer

The transfer, capture, cancellation, and result contracts for `task` remain
future async work. A `task` does not acquire automatic `deep` behavior merely
because it may run on another thread.


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
    ThenCallbackPrototype :: alias type ()(result : Integer)

    Producer :: type {
        CallablePrototype :: alias type (status : Coroutine.Status)()

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

Current pointer and allocator thread behavior is defined by
[Zax pointers and arenas](language/pointers-and-arenas.md#arenas):

- each arena declares whether it is thread-confined, synchronized, movable, or
  able to queue cross-thread release;
- `unique` may cross only when instance and destructor affinity permit it;
- local `strong`/`weak` accounting does not cross threads;
- `strong atomic`/`weak atomic` makes lifetime accounting cross-thread capable;
- pointer-layer atomicity does not make pointee operations data-race safe; and
- references do not safely cross thread boundaries.

Exact thread-affinity syntax, destructor scheduling, cross-thread preparation,
allocator APIs, and async cancellation remain in
[raw async input](project/raw/async.md) and
[raw pointer and arena mechanics](project/raw/pointer-and-arena-mechanics.md).


### Context and threading

The current per-thread `___` model is defined by
[Zax execution context](language/execution-context.md). Exact inheritance,
replacement, and restoration behavior for spawned threads, fibers, and async
tasks remains future [async input](project/raw/async.md).
