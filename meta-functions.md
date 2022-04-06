# [Zax Programming Language](index.md)

## Meta-Functions

### Meta-functions with omitted meta argument types

The compile time language requires that types are known as the compiler doesn't not support runtime variable typed parameters. However, function types can be omitted and caller usage can be determined. Functions will only be considered as valid callee choice if their function signature is compatible with the caller's signature. If the signature of the function is compatible with the caller then the function is considered a candidate so long as a better candidate was not found. Better is subjective, but in Zax it means the function with the best exact match to the input and return types expected.

````zax
add final : (
    result :
)(
    value1 :,
    value2 :
) = {
    return value1 + value2
}

// all three of these variants will compile
result1 := add(1, 2)
result2 := add(3.5, 4.6)
result3 := add("hello ", "world")

// ERROR: the code will not compile as the resulting function will attempt to
// add an Integer type to a Float type (which cannot succeed and the
// compiled version will error inside the function)
result4 := add(3, 4.5)
````


### Meta-functions with labelled meta-argument types

As an alternative to a concrete type, or an omitted type, a variable representing a type can be used when a type is prefixed with a dollar sign `$` operator. This causes a type to be defined as if were representing a type in a function arguments. If any types share a variable type they must be called with the same matching variable types.

````zax
addThenMultiply final : (
    result : $Type
)(
    value1 : $Type,
    value2 : $Type
) = {
    // the type `$Type` is conveniently available as a variable name and thus
    // can be referenced as any other known type
    addedValue : $Type = value1 + value2

    // important note: because `result` must the same type definition of
    // `$Type`, the inferred return type must match the input types or
    // this function will cause an error if the function was called but the
    // return type criteria cannot be met
    return addedValue * value1 * value2
}

// all three of these variants will compile
result1 := addThenMultiply(1, 2)
result2 := addThenMultiply(3.5, 4.6)

// ERROR: strings do match the criteria of the function call and no better
// function candidate exists thus the compiler will issue an error when
// the multiple operator is encountered
result3 := addThenMultiply("hello ", "world")

// ERROR: As `value1` and `value2` both share the same meta-type name of
// `$Type` these types must match or the function will not be chosen
// as a possible candidate; the caller below will not find an `add` function
// compatible to the arguments specified.
result4 := addThenMultiply(3, 4.5)
````


### Conditional meta-function selection

#### Meta-function selection using the `compiles` directive

For meta-functions, a `[[compiles]]` directive can be used as a compile type mechanism to check if a function can be selected as a candidate given input or output arguments specified. If code in a `[[compiles]]` block fails to compile then a meta-function cannot be selected as a legal candidate by a caller. Compiled code is never executed and any values, types or variables declared in a `[[compiles]]` block are discarded and ignored outside of a `[[compiles]]` block.

````zax
next final : (
    result :
)(
    value1 :,
    value2 :
) [[compiles]] {
    if !(value1 is Integer) && \
       !(value1 is Float) {
        [[error]]
    }
} = {
}
````


#### Meta-function selection using a `requires` directive

For meta-functions, a `[[requires]]` directive can be used as a compile type mechanism to check if a function can be selected as a candidate given input or output arguments specified. If code in a `[[requires]]` block fails to compile or returns false then a meta-function cannot be selected as a legal candidate by a caller. Executed code must evaluate to a `true` or `false` statement.

As a side note, replacing a `[[requires]]` and a code block that follows with a literal `true` or `false` will have the same effect if `requires` has returned `true` or `false`. All functions accept an optional `true` or `false` declarative to indicate if they can be selected as a candidate or not. Placing a hard coded `false` on a function will ensure a function can never be used as a candidate. By default all functions are `true` indicating a functions is selectable as a candidate. However, meta-functions use this boolean criteria in candidate selection condition. Other use cases can include intentionally disabling functions based on compile time decisions.

All inputs and outputs are considered captured in the context allowing for compile time reflection of the types. Memory backing argument values are invalid as the actual value is only evaluated at runtime and `requires` is a compile time evaluation directive. Access to the values is undefined behavior.

````zax
isSelectable final : (result : Boolean)(...) = {
    // ...
}

next final : (
    result :
)(
    value1 :,
    value2 :
) [[requires]] { return isSelectable(value1, value2) } = {
    // ...
}
````


#### Meta-function selection using a `concept` directive

For meta-functions, a `[[concept]]` directive can be used as a compile type mechanism to check if a function can be selected as a candidate for a given input or output argument specified. If code in a `[[concept]]` block fails to compile or returns false then a meta-function cannot be selected as a legal candidate by a caller. Executed code must evaluate to a `true` or `false` statement and follow a `( : Boolean)()` calling convention. Meta argument types are available in a concept function and will mirror arguments passed into a decorated function.

````zax
IsSelectable final : (result : Boolean)(ignored : ) [[concept]] = {
    if size of ignored > size of Integer
        return false
    // ...
    return true
}

next final : (
    result :
)(
    value1 : IsSelectable,
    value2 : IsSelectable
) = {
    // ...
}
````


### Use of `final` with regards to meta-functions

Using a `final` keyword is encouraged when declaring meta-functions but not required. A `final` keyword will ensure additional variables space is not needed per unique usage of a function. Without a `final` keyword, each invoked variation of a function will cause a new instance of a function's variable to be instantiated per unique usage.

Consider the following example:

````zax
add : (
    result : $Type
)(
    value1 : $Type,
    value2 : $Type
) = {
    return value1 + value2
}

result1 := add(1, 2)                // the function variable `add` with
                                    // Integers types is used
result2 := add(3.5, 4.6)            // a different function variable `add` with
                                    // Floats types is used
result3 := add("hello ", "world")   // yet another function variable `add` with
                                    // String types is used


// declare a value to capture later
importantValue : Float = 14.5

// declare a function that matches the signature of the `Float` version of `add`
alternativeAdd final : (
    output : Float
)(
    input1 : Float,
    input2: Float
) = [importantValue] {
    return importantValue + input1 + input2
}

// reassign the `Float` version of the `add` function to `alternativeAdd`
// leaving the `Integer` and `String` versions of `add` pointing to
// their original definitions
add = alternativeAdd

// the result will be 17.5 as the original function `add` has been replaced
// with a new definition
result4 := add(1.0, 2.0)

// the result will be 3
result5 := add(1, 2)

// the result will be "goodbye Sally"
result6 := add("goodbye ", "Sally")
````


#### Use of `final` with regards to meta-functions inside declared types

Usage of a `final` keyword is recommended with meta-functions declarations to ensure that each variation of a function does not create a new instance of a variable containing a function. Without a `final` keyword every unique invocation of a function will cause another variable to be required to hold a unique instance of a functions definition. A `final` keyword causes a compiler to know each unique instance of a function without needing a variable at runtime to hold a pointer to each function definition.

````zax
assert final : ()(check : Boolean) = {
    // ...
}

MyType :: type {
    total := 0

    // WARNING: By not declaring this function as `final` and by virtue of the
    // function being a meta-function, each invocation of the function
    // with different types will create a new `add` variable inside `MyType`
    // per unique type usage! This most likely was not desired and will cause
    // size bloat inside of MyType
    add : ()(value :) = {
        total += value as Integer
    }
}

MyOtherType :: type {
    total := 0

    // This function is `final` thus no space is needing to be allocated inside
    // the `MyOtherType` per unique type usage of the function. This is likely
    // desired.
    add final : ()(value:) = {
        total += value as Integer
    }
}

myType : MyType
myOtherType : MyOtherType

// declare three different data types
value1 : U8 = 12
value2 : Short = -1000
value3 : U64 = h'ABCDEF'

// invoke `add` with three different data types
myType.add(value1)
myType.add(value2)
myType.add(value3)

// invoke `add` with three different data types
myOtherType.add(value1)
myOtherType.add(value2)
myOtherType.add(value3)

// the size of `MyType` will be larger than the size of `MyOtherType` as
// `MyType` will contain three versions of `add` and `MyOtherType` contains
// a definition that is known at compile type and thus does not require any
// allocation space occupied inside `MyOtherType` for the three definitions
assert(size of MyType > size of MyOtherType)
````


### Meta-typed functions with meta-arguments

Function definitions can be prefixed with a meta list of variables which are type or constant inputs into functions. Meta-arguments can be explicitly defined or implied if a value of a meta-argument can be deduced base on usage.

````zax
add final : $(
    ResultType = Integer
)(
    result: $ResultType
)(
    value1 : $Type,
    value2 : $Type
) = {
    return (value1 + value2) as $ResultType
}

// invoke a very specific instance of add with a very specific type specified
result1 := add$(U8)(100, 50)

// invoke an implied version of add which presumes the result type based on
// the destination value's type
result2 : Short = add(100, 50)
````


#### Reassigning meta-typed functions with meta-arguments to a new function

Meta-typed functions with meta-arguments can be reassigned to a new function instance if a function variable was not declared as `final`.

In the example below, a meta-function is declared which is not `final` and thus specific versions of the function can be reassigned to point to a new function definition at runtime.

````zax
add : $(
    ResultType = Integer,
    Type = Integer
)(
    result : $ResultType
)(
    value1 : $Type,
    value2 : $Type
) = {
    return (value1 + value2) as $ResultType
}

// invoke a very specific instance of add with very specific type specified
result1 := add$(U8)(100, 50)

// invoke an implied version of add which presumes a result type based on
// the destination value's type
result2 : Short = add(100, 50)

// some value to capture
myValue : U8 = 8

alternativeAdd : [myValue] (
    result : U8
)(
    value1 : Integer,
    value2 : Integer
) = {
    return myValue + ((value1 + value2) as U8)
}

add = alternativeAdd

// the result will be 158 and not 150 since the `alternativeAdd` definition
// has replaced the `add$(U8)(# : Integer, # : Integer)` version
result3 := add$(U8)(100, 50)
````


#### Meta-functions with value meta-arguments

Meta-arguments on meta-functions can be values and not just type specifiers and these values can be defaulted if not specified.

In the example below, `fillArray` has two meta-arguments and both have default values. This allows a caller an option of specifying no meta-arguments, either meta-argument, or both meta-arguments.

````zax
fillArray final : $(
    Length = 10,
    ResultType = Integer
)(
    result : 
)(
    value : $Type
) = {
    myArray : $ResultType[$Length]
    for elem: in myArray {
        elem = value as $ResultType
    }
    return myArray
}

// returned an array of `10` `Integer` elements filled with `5`
result1 := fillArray(5)

// returned an array of `5` `Integer` elements filled with `10`
result2 := fillArray$(5)(10)

// returned an array of `3` `Integer` elements filled with `17`
result3 := fillArray$(3)(17)

// returned an array of `10` `U8` elements filled with `128`
result4 := fillArray$(#, U8)(128)
````


#### Meta-functions with forced meta-arguments

If a meta-argument is not defaulted, a meta-argument will be required if it cannot be deduced. This will ensure that an invocation of a meta-function with meta-arguments has intentionally defined arguments without any ambiguity with inferred values.

````zax
add final : $(
    Type
)(
    result : $Type
)(
    value1 : $Type,
    value2 : $Type
) = {
    return value1 + value2
}

// all three of these variants will compile but they require a specific
// meta-argument type be specified since the type is not defaulted
result1 := add$(Integer)(1, 2)
result2 := add$(Float)(3.5, 4.6)
result3 := add$(String)("hello ", "world")

// ERROR: the code cannot compile as the `$Type` specifier was not defaulted
// and must be specified for the meta-function since ambiguity exists in the
// specified types
result4 := add(3, 4.5)
````
