# [Zax Programming Language](index.md)

## Meta-Types

### Meta-types with omitted meta argument types

Meta-types are normal types except one or more types used within the meta-type is selected at compile time. This allows a type to be variable.

````zax
MyType$(Type) :: type {
    value : $Type
}

myType1 : MyType$(Integer)
myType1.value = 3

myType2 : MyType$(String)
myType2.value = "hello"

myType3 : MyType$(String)
myType3.value = "goodbye"

// OKAY: the `myType2` is of the same type as `myType3` and thus will compile
myType2 = myType3

// ERROR: this will cause a compiler error since `myType` and `myType2` are
// not compatible
myType1 = myType2
````


### Meta-types with multiple omitted meta argument types

More than one argument can be specified to a meta-type allowing for multiple types to be defined at compile time.

````zax
MyType$(TypeA, TypeB) :: type {
    value1 : $TypeA
    value2 : $TypeB
}

myType1 : MyType$(Integer, String)
myType1.value1 = 3
myType1.value2 = "apple"

myType2 : MyType$(String, Integer)
myType2.value1 = "hello"
myType2.value2 = 7

myType3 : MyType$(String, Integer)
myType3.value1 = "goodbye"
myType3.value2 = -7

// OKAY: the `myType2` is of the same type as `myType3` and thus will compile
myType2 = myType3

// ERROR: this will cause a compiler error since `myType` and `myType2` are
// not compatible
myType1 = myType2
````


### Meta-types with assumed meta argument types

Meta-types can be compile time determined if the input argument passed into a constructor can be automatically determined based on called function's signature. This can be done for both constructors and `once` declarations.

````zax
MyType$(Type) :: type {
    value : $Type

    +++ final : ()(input : $Type) = {
        value = input
    }

    func once : ()(whatever : Integer, input : $Type) = {
        // ...
    }
}

// the `$Type` is automatically assumed based on the `input` to the constructor
myType1 : MyType = 5
myType1.value = 3

// the `$Type` is automatically assumed base on the `input` to the
// `once` function
MyType.func(5, "hello")
````


### Meta-types with defaulted types

A meta-type can have types defaulted if the type cannot be assumed through other methods. This allows a type to be assumed without specification.

````zax
MyType$(Type = Integer) :: type {
    value : $Type

    +++ final : ()() = {
    }

    +++ final : ()(input : $Type) = {
        value = input
    }
}

// the `$Type` is automatically assumed based on the `input` to the constructor
myType1 : MyType = "hello"
myType1.value = "goodbye"

// the `$Type` is defaulted to `Integer` since it cannot be assumed based
// on a constructor that takes no arguments
myType2 : MyType
// OKAY: the `value` was assumed to be an `Integer` by default
myType2.value = 5

// the `$Type` is defaulted to `Integer` since it cannot be assumed based
// on a constructor that takes no arguments
myType2 : MyType
// ERROR: the `value` is a type of `Integer` by default and not a `String`
myType2.value = "not going to work"
````


### Meta-types with compile time constant values

A meta-type can can have arguments that are compile time constants as an input to the definition of the meta-type.

````zax
MyType$(Value : Integer) :: type {
    value : Integer[$Value]
}

// the `$Value` is specified at compile time 
myType : MyType$(5)

// OKAY: based on the compile time definition this code will compile
myType.value[0] = 55
myType.value[1] = -43
myType.value[2] = 42
myType.value[3] = 117
myType.value[4] = 1009
// ERROR: `5` is out of range
myType.value[5] = 101
````


### Meta-types with defaulted compile time constant values

A meta-type can can have arguments that are compile time constants as an input to the definition of the meta-type and these constants can be defaulted if not specified.

````zax
MyType$(Bits : Integer = sizeof Integer) :: type {
    bits : Boolean[$Bits]
}

// the `$Bits` is automatically assumed based on the default argument
myType : MyType

// OKAY: based on the compile time definition this code will compile
myType.value[0] = true
myType.value[1] = false
myType.value[2] = true
// ERROR: `10000` is out of range
myType.value[10000] = false
````


#### Meta-function selection using the `concept` directive

For meta-functions, the `[[concept]]` directive can be used as a compile type mechanism to check if the function can be selected as a candidate for a given input, or output argument specified. If the the code in the `[[concept]]` block fails to compile or returns false then the meta-function cannot be selected as a legal candidate by the caller. The executed code must evaluate to a `true` or `false` statement.

````zax
IsSelectable final : (result : Boolean)(ignored : ) [[concept]] = {
    if sizeof ignored > sizeof Integer
        return false
    // ...
    return true
}

MyType$(UseType = IsSelectable) :: type {
    valueA : $UseType
}

MyType$(UseType) :: type {
    valueB : $UseType
}

// the first `MyType` definition is the most specific and it is chosen
myType1 : MyType$(Integer)
myType1.valueA = 5

// the first `MyType` definition is most specific but this type
// fails the concept evaluation and thus the second definition is chosen
myType1 : MyType$(Integer[5])
myType1.valueB[0] = 5
````
