
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

````zax
double : (output : Integer)(input : Integer) = {
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
weigh final : (
    grams # : Float,
    ounces # : Float
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
#, ouncesOnly: = weigh("letter")
````

````zax
welcome final : (
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


### Function polymorphism

The language support polymorphism based on strong type matching. Two (or more) functions can share the same name and the compiler will select the function with the best match of the arguments. Types, references, pointers, mutability, `constant` types, return types, and other factors are considered during the function selection process. The full discussion for what is considered a best match is beyond the scope of this section. 

[Value polymorphism](flow-control.md) is also supported which is discussed in the [flow control](flow-control.md) section.

````zax
func final : ()(value : String) = {
    // ...
}

func final : ()(value : Integer) = {
    // ...
}

func("hello")   // the String version of function will be called
func(42)        // the Integer version of function will be called
````


### Functions value capturing

````zax
print final : ()(...) = {
    // ...
}

changeValue final : (result : Boolean)() = {
    // ...
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
print final : ()(...) = {
    // ...
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
print final : ()(...) = {
    // ...
}

assert final : ()(okay : Boolean) = {
    // ...
}

changeValue : (result : Boolean)() = {
    // ...
}

a : Integer = 1
b : Integer = 2

// both `a` and `b` both capture a reference, `a` maintains the
// `a` variable name whereas `b` is captured as a reference into a
// new value `altB`
func : ()() = [&a, altB : & = b] {
    // The value of a and b are captured by reference and will
    // always be display and reference the contents of the global scope
    // `a` or `altB`
    print(a, altB)

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


### Function input capturing and chaining

#### Functions input / output composition

If a function's output matches the input of another function they can be bound together as a newly created function using the function composition operator (`>>`). Each of the output arguments from the first function must match the input arguments from the second function. Functions can be composed together in chains of two or more functions where each input is chained to another output.

````zax
func1 final : (result : Integer)(input : String) = {
    // ...
}
func2 final : (result : String)(input : Integer) = {
    // ...
}

// func3 takes a `String` and returns a `String`
func3 final := func1 >> func2

// func4 takes a `String` an returns an `Integer`
func4 final := func3 >> func1

// bind four functions together in a longer chain
func5 final := func1 >> func2 >> func1 >> func2

// calling func4() actually calls func1(func2(func1()))
value : Integer = func4("5")
````


#### Function argument capturing and composition

A function can captured a full invocation of a function as a new function. When captured, the function invocation is not actually called immediately. Instead the capture returns a new function which has all the input arguments captured. The return values of the new function match the original arguments of the captured function. This allows the newly captured and created function to be called later and this new function can be invoked more than once.

````zax
func final : (result : String)(input1 : Integer, input2 : String) = {
    // ...
}

// capture a function call invocation for later
// (don't actually call the function now)
funcLater := [] >> func(2, "hello")

// call the previously captured function
result := funcLater()

// call the previously captured function again
result := funcLater()
````


#### Function input argument capturing and composition

A function's input argument can be captured and automatically becomes an argument input argument to function call creating a new function which has one (or more) less input argument required. If the name of an argument match the name of the function's input argument then the variable can be captured just by-name alone. If the name of an argument does not match any input argument name and no re-assigned name is given, then the argument is attempted to be matched positionally (excluding any positions which already have a match).

````zax
print final : ()(...) = {
    // ...
}

func1 final : ()(name : String, age : Integer) = {
    print("name:", name, "age:", age)
}

myNameIs final := "Slim"

func2 final := [name = myNameIs] >> func1

// will print "name: Slim age: 47"
func2(47)

// will print "name: Slim age: 48"
func2(48)


name final := "Shady"

// capture the `name` variable which is the functions input variable `name`
func3 final := [name] >> func1

// will print "name: Shady age: 47"
func3(47)

// capture the `name` variable, and capture by-value positionally
// (excluding name which is already matched)
func4 final := [name, 48] >> func1

// will print "name: Shady age: 48"
func4()
````


#### Function invocation chaining

The invocation chaining operator (`|>`) can be used to feed the result of one function as the input to the next function. Input arguments are attempted to be matched by name first. After any output to matching to output matching are removed, then the renaming input and output arguments are matched positional. Finally the remaining arguments that do not match will be taken from the function's invocation input argument list.

An initial input value (which is not a function) can be chained as the first input argument to chain into the first link of a function invocation chain.

The invocation chaining operator (`|>`) should not be confused with the pipe operator (`|`) or the shift right operator (`>>`). On many languages those operators would take the output of one function and apply those operators into the output of another function. Often the output of the first (and all subsequent operations) is the very type used to start the chain with those operators. This is how `std::cout` works in C++. The result of `std::cout << value` is the `std::cout&` object itself (i.e. `return *this`). Whereas in Zax, the chaining operator (`|>`) literally takes the output of one function and feeds it as the input to the next function. 

````zax
double : (result : Integer)(input : Integer) = {
    return input * 2
}

square : (result : Integer)(input : Integer) = {
    return input * input
}

half : (result : Integer)(input : Integer) = {
    return input / 2
}

squareAndAdd : (result : Integer)(input : Integer, add : Integer) = {
    return input * input + add
}

// the result of this function is "50", ((5 * 2) * (10)) / 2
result := 5 |> double() |> square() |> half()

// the result of this function is "38", (((4 * 2) * (8)) + 12) / 2
result = 4 |> double() |> squareAndAdd(12) |> half()
````


### Functions can be reassigned to new code

````zax
print final : ()(...) = {
    // ...
}

save final : ()(...) = {
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
print final : ()(...) = {
    // ...
}

// define and declare a function that return no results and takes no input
// arguments and has no ability to capture data and assign the function to
// point to nothing since the function is only a function pointer without
// the storage capacity of captured values
funcWithNoCapture : ()() *

// reassign the function to new executable code
funcWithNoCapture = {
    // ...
}

// execute the declared function
funcWithNoCapture()

myMessage : String = "Try to capture me!"

// ERROR: The function variable named `funcWithNoCapture` is not capable of
// capturing data and thus will error if a variable capture was attempted
funcWithNoCapture = [myMessage] {
    // ...
}
````


#### More errors when attempting to capture with capture disabled

````zax
print final : ()(...) = {
    // ...
}

value := 42

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
print final : ()(...) = {
    // ...
}

save final : (result : Boolean)(...) = {
    // ...
}

sound final : ()(...) = {
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


### Default arguments

#### Functions with default arguments

Unlike results which must be captured, arguments can default their values so long as any placeholder for the argument is acknowledged as existing and can use the discard operator (`#`) or other placeholders.

````zax
func final : ()(value : Integer) = {
    // ...
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
func final : ()(
    value1 : Integer,
    value2 : Integer
) = {
    // ...
}

// Allowed as two arguments are acknowledged of having existed which matches
// the function's type specification
func(#,#)
````

Or a function with multiple arguments defaulted:

````zax
func final : ()(
    value1 : Integer,
    value2 : Integer,
    value3 : Integer
) = {
    // ...
}

// Allowed as all arguments are acknowledged of having existed which matches
// the function's type specification
func(#, 42, #)
````


#### Functions with defaulted argument values

Unlike arguments that can be passed default values, defaulted arguments do not need to be acknowledged and the function can be called without passing in the 


````zax
func : ()(value : Integer = 42) = {
    // ...
}

// Allowed to call the function with a value
func(103)

// Allowed to call the function with the default value being assumed
func()
````

Or a function with two arguments where the second argument is defaulted:

````zax
func final : ()(
    value1 : Integer,
    value2 : Integer = 103
) = {
    // ...
}

// Allowed as the first argument is acknowledged of having existed which matches
// the function's type specification as the second argument can be defaulted
func(42)
````

Or a function with multiple arguments defaulted:

````zax
func final : ()(
    value1 : Integer,
    value2 : Integer = 42,
    value3 : Integer
) = {
    // ...
}

// Allowed as all arguments are acknowledged of having existed which matches
// the function's type specification and the middle argument is overridden to
// contain the value 42 instead of the default value of the Integer type
// (which is 0)
func(#, #, #)

// Allowed and the default is overridden for the second argument (i.e. 99)
func(#, 99, #)
````


### Functions within types

````zax
MyType :: type {
    value1 := 0
    value2 := 0
    value3 := 0

    bucket final : (output : Integer)() = {
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


### `mutator` functions

Function variables declared with `mutator` become [mutator methods](https://en.wikipedia.org/wiki/Mutator_method). Access the name of the function as a value will call a method which matches a function prototype of returning the type requested with no input arguments. Assigning a value to the function with a value will call a method which matches a function prototype of accepting the assigned type as an input argument and returning no results.

Functions that return a value marked as `mutator` are especially useful when a data variable is calculated rather than being a natural data type. As `mutator` functions add overhead in each function call, care must be done to ensure `mutator` functions are not overly accessed in CPU cycle sensitive code. Likewise, a value change in a seemingly unrelated variable on a type might impact a `mutator` which might not be as obvious given a function is being called under the scenes.

Function variables marked as `mutator` can be polymorphic. The compiler will attempt to match the input or output arguments by type to the best matching `mutator` function.

The example below creates a `mutator` that returns a value:

````zax
// length is a calculated value type and not a true independent value
length mutator final : (output : Integer)() = {
    // ...
    return output
}

// the `length` function is called which returns a value to a function
roomSize := 5 + length

// ERROR: `length` has no `mutator` that accepts an `Integer` value
length = 10
````

The example below creates a `mutator` that returns a value and a `mutator` that accepts a value:

````zax
// length is a calculated value type and not a true independent value
length mutator final : (output : Integer)() = {
    // ...
    return output
}
length mutator final : ()(input : Integer) = {
    // ...
}

// the `length` function is called which returns an `Integer` value
roomSize := 5 + length

// the `length` function is called which accepts an `Integer` value
length = 10
````

The example below creates two polymorphic `mutator` that both accept a value:

````zax
calculateHistoricalAge final : (output : Integer)() = {
    // ...
}

person mutator final : ()(input : Integer) = {
    // ...
}
person mutator final : ()(input : String) = {
    // ...
}

// the `person` function is called that accepts an `Integer` value
person = calculateHistoricalAge()

// the `person` function is called that accepts a `String` value
person = "Socrates Johnson"
````


#### Assign or replace a function `mutator` implementation

If a `mutator` function (not marked as `final`) accepts a value is instead assigned a function pointer to a function that matches its own function prototype, the function will be replaced rather than attempting to match another `mutator` function. Priority of `mutator` functions are always given to matching function prototypes than attempting to find a `mutator` function which accepts a function pointer as its function's input or output argument.

Likewise `mutator` functions (not marked as `final`) that return a value can instead be assigned a function pointer to a function that matches its own function prototype. This assignment will cause the definition of the `mutator` function to be replaced.

The example below replaces a `mutator` function that accepts an input value:

````zax
length mutator : ()(input : Integer) = {
    // ... do something ...
}

// the seemingly complex declaration below does the following:
// * declare an anonymous value
// * associate the type of the anonymous value to a function prototype
//   matching the original `length` `mutator`
// * assign the function `length` to a new replacement function
length = : ()(input : Integer) = {
    // ... do something else ...
}

// the second function definition is called and not the first definition
length = 10
````

The example below replaces a `mutator` function that returns an output value:

````zax
length mutator : (output : Integer)() = {
    // ... do something ...
    return output
}

// the seemingly complex declaration below does the following:
// * declare an anonymous value
// * associate the type of the anonymous value to a function prototype
//   matching the original `length` `mutator`
// * assign the function `length` to a new replacement function
length = : (output : Integer)() = {
    // ... do something else ...
    return output
}

// the second function definition is called and not the first definition
value := length
````


### Declare and immediately call functions

Anonymous functions can be called immediately upon construction by proceeding the closing curly brace (`}`) with an open bracket (`(`), arguments and a close bracket (`)`). Once the function's declaration completes the function is executed. Anonymous functions using the discard operator (`#`) do not need to use the `final` keyword as the function is implicitly final since no value captures the function pointer.

Each of the functions below is executed after declaration:

````zax
// declare a function that takes zero arguments and returns no results
# : ()() = {
    // ...
}()

// declare a function that takes one argument and returns no results
# : ()(input : Integer) = {
    // ...
}(5)


newValue := : (output : Integer)() = {
    // ...
    return output
}(10)
````

### Functions marked as `final`

If a function should never have its associated code changed, the function should be declared as `final`. Functions that are not marked `final` require additional storage capacity to accommodate a function pointer and may incur calling overhead to access the function via a function table instead of optimizing the call directly to code. Functions marked as `final` cannot have their code segment reassigned once declared.

````zax
MyType :: type {
    value1 := 0
    value2 := 0
    value3 := 0

    bucket : (output : Integer)() = {
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


### Functions qualified as `constant` in function types

Functions qualified as `constant` inside a type may not change any values or call any the type's functions that are not qualified as `constant`. This advertises the type's function as being safe to call without any risk to the underlying data within the type being changed as a result of calling the function.

````zax
saveToDisk final :: ()(value : Integer) = {
    // ...
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

    resetValues final : ()(value := 0) = {
        value1 = value
        value2 = value
        value3 = value
    }

    saveThenResetValues final : ()(value := 0) constant = {
        // allowed to call save since the function is declared as `constant`.
        save()

        // ERROR: The function was qualified as `constant` thus changing any of
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


#### Variables qualified as `mutable` in relation to `constant` function types

````zax
saveToDisk final :: ()(value : Integer) = {
    // ...
}

Mutex :: type {
    lock final : ()() = {
        // ...
    }
    unlock final : ()() = {
        // ...
    }
}

MyType :: type {

    mutex mutable : Mutex
    value1 := 0
    value2 := 0
    value3 := 0

    save final : ()() constant = {
        // allowed to call non-`constant` functions on the `constant`
        // mutex value since the value is qualified as `mutable`
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


### By-value `move` arguments

The `move` qualifier can be applied to by-value input arguments. This changes the calling semantics where the callee receives full ownership of the by-value type and the callee is responsible for destroying the type upon function exit. Once a type is moved, the by-value the caller no longer has visibility to the moved value as the callee will have destroyed the type.

To pass an input argument by-value the `as move` qualification must be added to a defaulted by-value `copy` to signify the ownership transfer.

Pointers and references to types cannot be moved as the ownership is being leased by whomever created the pointer or reference.

The `last` keyword is similar to `move` but `last` only applies to pointers and references whereas `move` only applies to by-value arguments. This allows for functions to inherit ownership of the contents within a pointer or reference type but the construction and destruction of the type's instance remains as normal.


````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    // ...
    --- final : ()() = {
        print("destroyed")
    }
}

func1 final : ()(value : myType move) = {
    // `value` is not copy constructed as it was moved into place

    // ...

    // as normally would occur, `value` is destroyed as it is passed by-value
}

func2 final : ()(value : myType) = {
    // `value` may or may not be copy constructed depending if it was moved
    // into this function or not

    // ...

    // as normally would occur, `value` is destroyed as it is passed by-value
}

a : MyType
func1(a as move)
// illegal to refer to `a` after this call

b : MyType
func2(b as move)
// illegal to refer to `b` after this call

c : MyType
func2(c)
// safe to refer to `c` after this call as a copy of `c` was given to `func2`

// `a` nor `b` are destructed
// `c` is destructed
````


#### Polymorphism `move` selection

The `move` is a factor in deciding which polymorphic function should be called. This can allow for a function that accepts a by-value `move` qualifier and a function that accepts a defaulted by-value `copy` qualifier.

````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    // ...
    --- final : ()() = {
        print("destroyed")
    }
}

func final : ()(value : myType) = {
    // `value` is not copy constructed as it was moved into place

    // ...

    // as normally would occur, `value` is destroyed as it is passed by-value
}

func final : ()(value : myType move) = {
    // `value` may or may not be copy constructed depending if it was moved
    // into this function or not

    // ...

    // as normally would occur, `value` is destroyed as it is passed by-value
}

a : MyType
func(a)

b : MyType
func(b as move)
// illegal to refer to `b` after this call

// `b` is not destructed
// `a` is destructed
````


#### `move` qualification is automatically applied

The `move` qualification is automatically applied to subsequent calls to other called functions when receiving an input `move` qualified by-value type. However, the `copy-or-move` warning will be issued if a called polymorphic function supports both `move` or `copy` semantics. The `as move` or `as copy` must be performed on a type to re-apply the `move` or `copy` semantics to distinguish the two scenarios. This warning is issued by the compiler to prevent surprising automatic moved type destruction when calling other functions.

````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    // ...
    --- final : ()() = {
        print("destroyed")
    }
}

func final : ()(value : myType) = {
    // this function will receive a copy of `a` (but never an `b`)

    // ...
}

func final : ()(value : myType move) = {
    // this function will receive a moved copy of `b` (but never an `a`)

    // ...
}

funcAlwaysMoved1 : ()(value : myType move) = {
    if value is move
        print("yes, this type was moved")   // this will print

    // WARNING: `copy-or-move` as a move type may be attempting to call the
    // `move` or `copy` version of the polymorphic `func`
    func(value)
}

funcAlwaysMoved2 : ()(value : myType move) = {
    if value is move
        print("yes, this type was moved")   // this will print

    // OKAY: the intent is clear to call the `move` version
    func(value as move)
}

a : MyType
funcAlwaysMoved1(a as move)

b : MyType
funcAlwaysMoved2(b as move)

// `a`'s ownership is moved into `funcAlwaysMoved1`
// `b`'s ownership is moved into `funcAlwaysMoved2`
````


#### `move` versus explicit `copy` qualification

By default all functions that receive by-value arguments have the `copy` qualification applied unless the `move` qualification is explicitly applied. The `copy` and `move` qualifiers are mutually exclusive. The `copy` qualifier is redundant as it is automatically applied by default. One key difference does exist between explicitly and implicit qualifying a by-value argument as `copy`. Arguments that are explicitly qualified as `copy` cannot ever receive a `move` type and any attempt to pass a `move` type into an explicitly `copy` qualified type will cause an `explicit-copy-cannot-receive-move` error.

````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    // ...
    --- final : ()() = {
        print("destroyed")
    }
}

func final : ()(value : myType copy) = {
    // this function will receive a copy of `a` (but never an `b`)

    // ...
}

a : MyType
// OKAY: a copy of the value is made
func(a)

b : MyType
// ERROR: `explicit-copy-cannot-receive-move` error
func(b as move)
````


#### `move` on output arguments

The `move` qualifier can be applied to output arguments. This causes the return value to not be copied followed by a  destruction by the callee by transferring the responsibility of destruction to the caller. Normally types have return value optimization (i.e. copy elision) enabled where return values are implied `move` out of the called functions. However, the `copy` or `move` qualifier can be applied to ensure the compiler is enforcing the proper semantics on a return value.

````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    // ...
    --- final : ()() = {
        print("destroyed")
    }
}

func1 final : (myType : MyType move)() = {
    // ...
}

func2 final : (myType : MyType copy)() = {
    // ...
}

// `a` receives a moved result (thus is not constructed/destructed again)
a := func1()
// `b` receives a copied result (thus the callee and caller both destroy the
// type as the returned type was definitely copied)
b := func2()
````


### Split and combine argument operators

### Splitting type into a function call

The argument split (`<-`) operator with named declarations `{}` can be used to fill arguments passed into a function with a newly declared anonymous type. Each argument is matched with an input argument. All arguments those arguments are considered satisfied by the split (`<-`) operator as if those arguments were not present in the argument list and the rules standard rules remain for any arguments unspecified.


````zax
print final : ()(...) = {
    // ...
}

func final : ()(
    age : Integer,
    name : String,
    weight : Float,
    defaultSmiley : Rune
) = {
    // ...
}

func(42, <- := { .name = "Boothby", .age = 61, .weight = 120 }, r'😀')
````


### Splitting type into a function call

The split operator (`<-') can take a type and create an argument list for a function to satisfy the functions argument list. For any matchings type names to the argument names (not already fulfilled arguments) will be automatically filled as arguments to the function. Any additional type values unmatched will be ignored. Any unfulfilled arguments will need to be filled as per standard argument passing rules.

````zax
print final : ()(...) = {
    // ...
}

func final : ()(
    age : Integer,
    name : String,
    weight : Float,
    defaultSmiley : Rune
) = {
    // ...
}

MyType :: type {
    name : String
    famousQuote : String
    age : Integer
    weight : Float
}

myType : MyType

func(42, <- myType, r'😀')

// print the first result
print(value)

print(myType.name)
print(myType.famousQuote)
print(myType.age)
print(myType.weight)
````


#### Combining argument results into an anonymous type

The argument combine operator (`->`) can be used to define and declare a new anonymous type whose values contain the results of the remaining arguments returned from a function.

````zax
print final : ()(...) = {
    // ...
}

func final : (
    output1 : Integer,
    output2 : String,
    output3 : Float,
    output4 : Rune
)() = {
    // ...
    return output1, output2, output3, output4
}

// creates a new anonymous type and fill the results with the remaining
// arguments in the function return
value1 :, remaining : -> = func()

// print the first result
print(value)

// print the other resulting values
print(remaining.output2)
print(remaining.output3)
print(remaining.output4)
````


#### Combining argument results into an existing type

The argument combine operator (`->`) can be used to assign values returned from function directly into an existing type. For the names of return arguments unfulfilled in existing return results, the return argument names are matched to the names in the type declared and any matching names are treated as if they were removed from the return result list as they are fulfilled. Any non matching names can be declared as additional arguments need to be captured as per standard argument returning rules. If none of the names match when using the combine operator (`->`), an attempt is made to apply an automatic `as` operator from the remaining returned types (as if they were a combined type) up to the final value present in the destination type. If the types are deemed compatible (as per `as` casting rules) those arguments are considered fulfilled and treated as if they were removed from the returned argument list. If neither method results in any matches then the compiler will issue an error. Any unmatched values present in the result results will need to be fulfilled as per standard argument returning rules.

````zax
print final : ()(...) = {
    // ...
}

func final : (
    age : Integer,
    name : String,
    weight : Float,
    defaultSmiley : Rune
)() = {
    // ...
}

MyType :: type {
    name : String
    famousQuote : String
    age : Integer
    weight : Float
}

// uses existing `MyType` type and fills the results with the arguments
// as returned from the function
value1 :, myType : MyType ->, defaultSmiley: = func()

// print the first result
print(value)

print(myType.name)
print(myType.famousQuote)
print(myType.age)
print(myType.weight)
````
