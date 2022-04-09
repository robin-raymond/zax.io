
# [Zax Programming Language](index.md)

## Discard Operator

### discard operator on previously declared variables

The discard operator (`#`) can be applied to values after declaration which will force the compiler to treat the type as unimportant and not complain if the type was not referenced.

````zax
func : ()(input : Integer) = {
    // ...
}

// ...

// replace func with a new implementation that ignores the input variable
func = {
    # input
    // ...
}
````


### Functions with discard operator on return results

Return result arguments on functions marked as discard using the discard operator (`#`) may be ignored by the caller and will not require the compiler to enforce capturing of the function call's result. Without this directive the `result-not-captured` warning will occur if a function result is not captured.

````zax
pushToQueue final : (
    newQueueSize : Integer
)(
    value : Integer
) = {
    // insert code that pushes to the queue and returns the new queue size
}

// ERROR: The newQueueSize was not captured by the caller and thus will cause
// a compiler error
pushToQueue(42)

// OKAY: The result is acknowledged as existing; the caller is ignoring the
// result using the discard operator as a placeholder
# := pushToQueue(42)

// This is allowed as the value is captured (even if the value is never used)
ignoredResult := pushToQueue(42)


pushToQueueVersion2 final : (
    newQueueSize # : Integer
)(
    value : Integer
) = {
    return pushToQueue(value)
}

// This is allowed as the value is marked as `#` and thus does not
// require the result is captured
pushToQueueVersion2(42 * 2)

// An alternative version which is also allowed where the result type is
// declared with an empty name but not captured into a variable using the
// discard operator (`#`) as a placeholder
# := pushToQueueVersion2(42 * 2)
````

Functions with multiple arguments can optionally mark each argument with `#` to ensure which arguments must be captured by default and which arguments can be discarded and ignored.

````zax
queuedValue final : (value : Integer)() = {
    // ...
}

queueSize final : (size : Integer)() = {
    // ...
}

readNextValue final : (
    nextValue : Integer,
    remaining : Integer
)() = {
    return queuedValue(), queueSize()
}

// Allowed sa both results are captured
nextValue:, remaining: = readNextValue()

// ERROR: The function returns two values with no results being marked
// as `#` thus both result must be captured
nextValue2 := readNextValue()

// OKAY: An acknowledgement is made of the second argument, the
// argument is not captured but the variable is intentionally discarded
nextValue3:, # = readNextValue()

readNextValueVersion2 final : (
    nextValue : Integer,
    remaining # : Integer
)() = {
    return queuedValue(), queueSize()
}

// Allowed to discard the second return result argument as the value
// is marked as `#`
nextValue4 := readNextValueVersion2()

// An alternative allowed version where the second argument is acknowledged
// as existing but is not captured
nextValue5: , # = readNextValueVersion2()
````


### Functions with discard operator on input arguments

````zax
func final : ()(input : Integer) = {
    // ERROR: The variable named `input` is declared but never used
}

funcVersion2 final : ()(input # : Integer) = {
    // this allows the input argument to be optional and when the input argument
    // is not specified then the value is defaulted to the default value of the
    // type 
}

funcVersion3 final : ()(ignoredButRequired : Integer) = {
    // the argument is requires as an argument but the value is discarded
    // internally within the function
    # ignoredButRequired
}

funcVersion4 final : ()(# : Integer, # : Integer) = {
    // the arguments are not important and any values passed in are entirely
    // discarded; alternatively the argument can be specified by a type and
    // not a value
}

funcVersion5 final : ()(# : Integer) = {
    // the argument is not important and any values passed in are entirely
    // discarded; alternatively the argument can be specified by a type and
    // not a value
}

funcVersion5 final : ()(# : String) = {
    // the argument is not important and any values passed in are entirely
    // discarded; alternatively the argument can be specified by a type and
    // not a value
}

func(42)
funcVersion2(42)
funcVersion3(42)
funcVersion4(42)
funcVersion4(42, 52)

// ERROR: An input argument was declared but not acknowledged and thus the
// compiler will issue an error. The local discard has no impact on the caller
// of the function.
funcVersion3()  // ERROR

// OKAY: both arguments are discarded
funcVersion4()

// this version selects the function based on a type being specified but no
// value is actually sent into the function
funcVersion5(42)         // Integer version called and value is discarded
funcVersion5("hello")    // String version called and value is discarded
funcVersion5(Integer)    // Integer version is called with no value to discard
funcVersion5(String)     // String version is called with no value to discard
````


### discard operator on local variables

Variables that are declared but never used must be marked as discard or the compiler will generate a `variable-declared-but-not-used` warning. The compiler enforces that all declared named variables are referred to or marked with a discard operator.

````zax
print final : ()(...) = {
    // ...
}

func final : (result : Integer)() = {
    return 42
}

// Allowed since the result is captured and used elsewhere
value := func()
print(value)

// WARNING: If this variable was captured as a result but never used again
// then the compiler will issue an compiler warning as all results must have
// defined usage elsewhere.
valueNeverUsed := func()

// Allowed since the result is captured as per requirement of the
// called function using the discard operator to indicate that the value is
// knowingly being tossed out
# := func()

// ... insert code that never uses ignoredValue ...
````



### discard operator on types

Types declared with the discard operator may be constructed without ever being further referenced. This directive allows a variable of the type to be declared without further referencing the type elsewhere thus suppressing all `variable-declared-but-not-used` warnings for this type.

````zax
MyMutex :: type {
    // ...
}

MyLock # :: type {
    // ...

    +++ final : ()(lock : MyLock &) = {
        // ...
    }
}

mutex : MyMutex

// normally `lock` being unreferenced would issue an error but the type is a
// simple RAII type and thus does not need further usage beyond merely
// constructing the value
lock : MyLock = mutex

// ... code which never referenced `lock` variable ...
````
