
# [Zax Programming Language](index.md)

## Concurrency


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

Calling a function labelled as `promise` does not invoke a direct call to the function. Instead a function pointer is returned that can be transferred and executed on another thread or queue later.

````zax
myFunc final : ()(value1 : Integer) promise = {
    //...
}

/*
TemplatedPromiseResult :: type {
    ThenCallbackPrototype final : ()()

    callable : ()()
    then : ()(callback : ThenCallbackPrototype) 
}
*/

// calling laterFunc function causes all the values passed into the function to
// be captured where the function can be executed later
later := myFunc(42)

later.then({
    // called after the `myFunc` is completed with the return result
    // (called from the thread that executed the function when complete)
})

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
    ThenCallbackPrototype final : ()()

    callable : ()()
    then : ()(callback : ThenCallbackPrototype) 
}
*/

// as `String` types are not normally thread safe, all String types must
// be constructed as `deep` when crossing the thread boundary
someString := "apple"

// calling laterFunc function causes all the values passed into the function to
// be captured where the function can be executed later
later1 := myFunc(42, someString)
later2 := myFunc(55, someString)

later1.then({
    // ...
})
later2.then({
    // ...
})

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
    ThenCallbackPrototype final : ()()

    callable : ()()
    then : ()(callback : ThenCallbackPrototype) 
}
*/

// as `String` types are not normally thread safe, all String types must
// be constructed as `deep` when crossing the thread boundary
someString := "apple"

// calling laterFunc function causes all the values passed into the function to
// be captured where the function can be executed later
later1 := myFunc(42, someString)
later2 := myFunc(55, someString)

later1.then({
    // ...
})
later2.then({
    // ...
})

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
    ThenCallbackPrototype final : ()(result : String)

    callable : ()()
    then : ()(callback : ThenCallbackPrototype) 
}
*/

// calling laterFunc function causes all the values passed into the function to
// be captured where the function can be executed later
later := myFunc(42)

later.then({
    // called after the `myFunc` is completed with the return result
    
    resultCallbackFunc final : ()(result : String) deep promise = {
        //...
    }

    // pass the callable function returned from the promise into a
    // function that will execute the result back onto the main thread
    executeCallableOnMainThread(resultCallbackFunc(result).callable)
})

// `myFunc` will get executed when `callable()` is called from the other thread
executeCallableOnAnotherThread(later.callable)
````

