
# [Zax Programming Language](index.md)

## Functions

Functions are data types just like other variables and all functions are effectively lambdas. They can be assigned, and potentially reassigned depending on needs. Functions can capture data or be entirely raw functions pointers depending on needs.

````zax
// declare a function with no return results and no arguments.
func: ()() = {
}

// call the function with no results and no arguments.
func()
````

Simple functions have zero or one return results and zero or one arguments.

`````zax
double : (output : Integer)(input : Integer) {
    return input * 2
}

// `result` is assigned the result of the function `double` whose type
// is assumed based on the the function return type.
result := double(5)
````


### Multiple returns and arguments

Functions can have multiple return results and multiple arguments. The return results are always declared before the arguments. The comma operator is used to distinguish between results, and arguments.

````zax
// declare a function with no return results and no arguments.
weigh : (
    grams discard : Float,
    ounces discard : Float
)(
    object : String
) = {
    if object == "letter"
        return 11.25, 0.39683207
    return 0.0, 0.0
}

// Call the function and capture the weight in grams and ounces
grams:, ounces: = weigh("letter")

// Call the function and capture the gram value only
gramOnly := weigh("letter")

// Call the function and capture the ounces value only
, ouncesOnly: = weigh("letter")
````

````zax
welcome : (
    accountId : String,
    secretCode : String
)(
    firstName : String,
    lastName : String,
    message : String
) = {
    // ... do something ...
}

accountId:, secretCode: = welcome("Pat", "Jones", "Would you like some water?")
````

### Error handling using the `except` keyword

````zax
login : (
    lastLogin : String,
    error : Error
)(
    username : String
) {
    if banned(username)
        return , : Error = "You've been banned from our service."

    return "October 7, 2020",
}

renderAccount : (myError : Error)(username : String) {

    // The `except` keyword will capture a return result and if the return
    // result evaluates to true the result will automatically be returned
    // with all other results retaining their defaulted values.
    lastLogin:, except myError = login(username)

    // This is equivalent to doing the following code
    lastLogin:, capturedError = login(username)
    if capturedError
        return , capturedError

    // return the default error value for Error which is assumed
    // to indicate no error in this code.
    return :
}
````

### Functions can capture values

````zax
changeValue : (result : Boolean)() = {
}

print : ()(...) = {
    //...
}

a : Integer = 1
b : Integer = 2

func : ()() = [a, b] {
    // The value of a and b are captured by value and 1 and 2 will
    // always be displayed (unless the captured `a` or `b` is changed
    // locally)
    print(a, b)

    // If the function changeValue() returns true then the captured
    // a will become 42 but the global a will retain its original value.
    if changeValue()
        a = 42
}

// No need to specify the captured values as they are already contained
// within the captured function.
func()
````

#### Function declaration and definition with capturing value reassignment

````zax
print : ()(...) = {
    //...
}

// declare and define a variable and function type with no return results, an
// `Integer` input argument, and with the ability to capture variables and
// assign the function to point to nothing.
magicNumber : ()(value : Integer)
`
// variable representing the current message to display
messageToDisplay : String = "You've found the number"

// assign the `magicNumber` to code to execute and capture the value of
// `messageToDisplay` for later usage
magicNumber = [messageToDisplay] {
    if value == 1001
        print("Horray -=> ", messageToDisplay)
}

// reassign the `messageToDisplay` to an entirely new value
messageToDisplay = "Move along, nothing to see here people"

// execute the `magicNumber`'s code which will display the value of the message
// as it was originally captured and not the new value
magicNumber(1001)   // will display "Horray -=> You've found the number"
````


### Functions can capture reference

Functions can capture by reference (or by other type) by declaring a new variable that refers to the original variable.

````zax
changeValue : (result : Boolean)() = {
}

print : ()(...) = {
    //...
}

assert : ()(okay : Boolean) = {
}

a : Integer = 1
b : Integer = 2

func : ()() = [a:& = a, b:& = b] {
    // The value of a and b are captured by reference and will
    // always be display and reference the contents of the global scope
    // `a` or `b`
    print(a, b)

    // If the function changeValue() returns true then the captured
    // a will become 42 which is a reference of the global value.
    if changeValue()
        a = 42
}

func()
assert(a == 1 || a == 42)

a = 5

func()
assert(a == 5 || a == 42)
````

### Functions can be reassigned to new code

````zax
print : ()(...) = {
    //...
}

save : ()(...) {
}

func : ()(myValue : Integer) = {
    print(myValue)
}

// will call print(42)
func(42)

func = {
    // the variable `myValue` is known by the declared type of `func`
    save(myValue)
}

// will call save(42)
func(42)
````

### Functions marked as `final` cannot be reassigned

````zax
func final : ()() = {
    // do something
}

// call `func`
func()

// ERROR: `func` is declared as a final value and cannot be reassigned to a
// new value in the future.
func = {
    // do something else
}
````

### Function pointers without ability to capture


Functions will allocate space needed to capture values but function pointers do not have the space to capture any value contents. Function pointers are more space efficient when stored inside types with the tradeoff of being the inability to capture values.

````zax
print : ()(...) = {
    //...
}

// define and declare a function that return no results and takes no input
// arguments and has no ability to capture data and assign the function to
// point to nothing since the function is only a function pointer without
// the storage capacity of captured values
funcWithNoCapture : ()() *

// reassign the function to new executable code
funcWithNoCapture = {
    //...
}

// execute the declared function
funcWithNoCapture()

myMessage : String = "Try to capture me!"

// ERROR: The function variable named `funcWithNoCapture` is not capable of
// capturing data and thus will error if a variable capture was attempted
funcWithNoCapture = [myMessage] {
    //...
}
````


#### More errors when attempting to capture with capture disabled

````zax
value := 42

print : ()(...) = {
    //...
}

// ERROR: The function is a raw function pointer and cannot capture any values.
func : ()()* = [value] {
    print(value)
}

// will call print(42)
func()

// ERROR: Even though no values are captured an error will occur as
// the declared type for `func` cannot capture values.
func = [] {
    // do something else...
}

func()
````


### Compatible definitions can be reassigned

````zax
print : ()(...) = {
    // ....
}

save : (result : Boolean)(...) {
    // ...
}

sound : ()(...) {
    // ...
}

func1 : (output : Boolean)(input : Integer) = {
    print(input)
    return true
}

// will call `print` with `42`
result1 := func1(42)

func2 : (result : Boolean)(value : Integer) = {
    return save(value)
}

// `func11 will now point to `func2` as the definitions of func1 and func2 are
// equivalent and compatible despite having different argument names.
func1 = func2

// will call `save` with `42`
result2 := func1(42)

func1 = {
    // `func1` retains its original argument names' `output` and `input`
    // despite being reassigned later to `func2`
    // (which uses alternative argument names).
    sound(input)
    return true
}

// will call `sound` with `42`
result3 := func1(42)
````

### Functions and the `discard` keyword

#### Functions with `discard` marked return results

Return result arguments on functions marked as `discard` may be ignored by the caller and will not require the compiler to enforce capturing of the function call.

````zax
pushToQueue : (
    newQueueSize : Integer
)(
    value : Integer
) {
    // insert code that pushes to the queue and returns the new queue size
}

// ERROR: The newQueueSize was not captured by the caller and thus will cause
// a compiler error
pushToQueue(42)

// ERROR: Even though the result is acknowledged as existing, the caller must
// capture the result into a variable since the result cannot be discarded
:= pushToQueue(42)

// This is allowed as the value is captured (even if the value is never used)
ignoredResult := pushToQueue(42)


pushToQueueVersion2 : (
    newQueueSize discard : Integer
)(
    value : Integer
) {
    return pushToQueue(value)
}

// This is allowed as the value is marked as `discard` and thus does not
// require the result is captured
pushToQueueVersion2(42 * 2)

// An alternative version which is also allowed where the result type is
// declared with an empty name but not captured into a variable
:= pushToQueueVersion2(42 * 2)
````

Functions with multiple arguments can mark each argument with `discard` to ensure which arguments must be captured by default and which arguments can be discarded and ignored.

````zax
queuedValue : (value : Integer)() = {
    //...
}

queueSize : (size : Integer)() = {
    //...
}

readNextValue : (
    nextValue : Integer,
    remaining : Integer
)() = {
    return queuedValue(), queueSize()
}

// Allowed sa both results are captured
nextValue:, remaining: = readNextValue()

// ERROR: The function returns two values with no results being marked
// as `discard`  thus both result must be captured
nextValue2 := readNextValue()

// ERROR: Even though an acknowledgement is made of the second argument, the
// argument is still not captured and thus will cause a compiler error
nextValue3:, = 

readNextValueVersion2 : (
    nextValue : Integer,
    remaining discard : Integer
)() = {
    return queuedValue(), queueSize()
}

// Allowed to discard the second return result argument as the value
// is marked as `discard`
nextValue4 := readNextValueVersion2()

// An alternative allowed version where the second argument is acknowledged
// as existing but is not captured
nextValue5:, = readNextValueVersion2()
````

#### Functions with `discard` marked input arguments

````zax
func : ()(input : Integer) = {
    // ERROR: The variable named `input` is declared but never used
}

funcVersion2 : ()(input discard : Integer) = {
    // This is allowed since the input variable is discarded intentionally
}

funcVersion3 : ()(discard : Integer) = {
    // This is allowed as the argument is knowingly not important to capture
    // and may be discarded
}

funcVersion4 : ()(: Integer) = {
    // This is allowed as the argument is knowingly declared with an unnamed
    // variable and thus may be discarded
}

func(42)
funcVersion2(42)
funcVersion3(42)
funcVersion4(42)

// ERROR: An input argument was declared but not acknowledged and thus the
// compiler will issue an error. The `discard` has no impact on the caller
// of the function.
funcVersion2()  // ERROR
funcVersion3()  // ERROR
funcVersion4()  // ERROR
````

#### Functions with `discard` marked local variables

Variables that are declared but never used must be marked as `discard` or the compiler will error. The compiler enforces that all declared named variables are used or marked with `discard`.

````zax
func : (result : Integer)() = {
    return 42
}

print : ()(...) = {
    //...
}

// Allowed since the result is captured and used elsewhere
value := func()
print(value)

// ERROR: If this variable was captured as a result but never used again
// then the compiler will issue an compiler error as all results must have
// defined usage elsewhere.
valueNeverUsed := func()

// Allowed since the result is captured as per requirement of the
// called function and marked as `discard` to indicate that the value is
// knowingly being tossed out
ignoredValue discard := func()

// ... insert code that never uses ignoredValue ...
````

### Default arguments

#### Functions with default arguments

Unlike results which must be captured, arguments can default their values so long as any placeholder for the argument is acknowledged as existing.

````zax
func : ()(value : Integer) = {
    //...
}

// Allowed to call the function with a value
func(42)

// Allowed to call the function with the default value
// (by declaring an empty variable)
func(:)

// ERROR: The argument for the function is not acknowledged as existing
// thus the compiler cannot determine if ignoring the argument was done
// intentionally or not
func()
````

Or a function with two arguments defaulted:

````zax
func : ()(
    value1 : Integer,
    value2 : Integer
) = {
    //...
}

// Allowed as two arguments are acknowledged of having existed which matches
// the function's type specification
func(,)
````

Or a function with multiple arguments defaulted:

````zax
func : ()(
    value1 : Integer,
    value2 : Integer,
    value3 : Integer
) = {
    //...
}

// Allowed as all arguments are acknowledged of having existed which matches
// the function's type specification
func(,42,)
````

#### Functions with defaulted argument values

Unlike arguments that can be passed default values, defaulted arguments do not need to be acknowledged and the function can be called without passing in the 


````zax
func : ()(value : Integer = 42) = {
    //...
}

// Allowed to call the function with a value
func(103)

// Allowed to call the function with the default value being assumed
func()
````

Or a function with two arguments where the second argument is defaulted:

````zax
func : ()(
    value1 : Integer,
    value2 : Integer = 103
) = {
    //...
}

// Allowed as the first argument is acknowledged of having existed which matches
// the function's type specification as the second argument can be defaulted
func(42)
````

Or a function with multiple arguments defaulted:

````zax
func : ()(
    value1 : Integer,
    value2 : Integer = 42,
    value3 : Integer
) = {
    //...
}

// Allowed as all arguments are acknowledged of having existed which matches
// the function's type specification and the middle argument is overridden to
// contain the value 42 instead of the default value of the Integer type
// (which is 0)
func(,,)

// Allowed and the default is overridden for the second argument (i.e. 99)
func(,99,)
````


### Functions within types

````zax
MyType :: type {
    value1 := 0
    value2 := 0
    value3 := 0

    bucket : (output : Integer)() = {
        // `value1`, `value2`, `value3` are accessible as local variables
        // with an implicit this pointer being passed to the function
        return (value1 + value2 + value3) % 17
    }
}

myType : MyType
myType.value1 = 1
myType.value2 = 55
myType.value3 = 1001

// call the function that treats `myType` as an automatic invisible argument
// to the function named `bucket`
bucket := myType.bucket()
````

### Functions marked as `final`

If a function should never have its associated code changed, the function should be declared as `final`. Functions that are not marked `final` require additional storage capacity to accommodate a function pointer and may incur calling overhead to access the function via a function table instead of optimizing the call directly to code. Functions marked as `final` cannot have their code segment reassigned once declared.

````zax
MyType :: type {
    value1 := 0
    value2 := 0
    value3 := 0

    bucket final : (output : Integer)() = {
        // allowed to access variables since the contents of the variables
        // are not changed
        return (value1 + value2 + value3) % 17
    }
}

myType : MyType
myType.value1 = 1
myType.value2 = 55
myType.value3 = 1001

// call the function that exists within the type
bucket := myType.bucket()

// ERROR: The function `bucket` is declared as `final` and thus cannot be
// reassigned to a new code segment.
myType.bucket = {
    // do something else
}
````

### Functions marked as `constant` in function types

Functions marked as `constant` inside a type may not change any values or call any the type's functions that are not marked as `constant`. This advertises the type's function as being safe to call without any risk to the underlying data within the type being changed as a result of calling the function.

````zax
saveToDisk final :: ()(value : Integer) = {
    //...
}

MyType :: type {
    value1 := 0
    value2 := 0
    value3 := 0

    bucket final : (output : Integer)() constant = {
        // allowed to access variables since the contents of the variables
        // are not changed
        return (value1 + value2 + value3) % 17
    }

    save final : ()() constant = {
        saveToDisk(value1)
        saveToDisk(value2)
        saveToDisk(value3)
    }

    resetValues : ()(value := 0) = {
        value1 = value
        value2 = value
        value3 = value
    }

    saveThenResetValues : ()(value := 0) constant = {
        // allowed to call save since the function is declared as `constant`.
        save()

        // ERROR: The function was marked as `constant` thus changing any of
        // the type's values or calling non-constant type function for the
        // `constant` type is disallowed.
        resetValues(value)
    }
}

myType : MyType
myType.value1 = 1
myType.value2 = 55
myType.value3 = 1001

myType.saveThenResetValues()
bucket := myType.bucket()
````


#### Variables marked as `mutable` in relation to `constant` function types

````zax
saveToDisk final :: ()(value : Integer) = {
    //...
}

Mutex :: type {
    lock final : ()() = {
        //...
    }
    unlock final : ()() = {
        //...
    }
}

MyType :: type {

    mutex mutable : Mutex
    value1 := 0
    value2 := 0
    value3 := 0

    save final : ()() constant = {
        // allowed to call non-`constant` functions on the `constant` mutex value
        // since the value is marked as `mutable`
        mutex.lock()
        saveToDisk(value1)
        saveToDisk(value2)
        saveToDisk(value3)
        mutex.unlock()
    }
}

myType : MyType
myType.value1 = 1
myType.value2 = 55
myType.value3 = 1001

myType.save()
````