
# [Zax Programming Language](index.md)

## Operator Overloading

### Basic overloading

Overloadable operators (e.g. greater than (`>`), less than (`<`>), equal to (`=`)) can be augmented with new functions. This allows types to support additional operations in symbolic form rather than requiring natural language. Operators can be declared in global scope where two arguments will be required for binary operations and one operator is required for unary operators.

The resulting type for an operator is entirely arbitrary but the recommendation is to follow the normal expectations of a consumer of the programming interface. For example, comparison operators are expected to return `Boolean` values and thus returning a String from these operations would be unnatural.

Adding three new operators for mixing `String` types and `Integer` types:

````zax
operator < final : (result : Boolean)(lhs : Integer, rhs : String) = {
    // ...
}

operator < final : (result : Boolean)(lhs : String, rhs : Integer) = {
    // ...
}

operator += final : (result : String &)(lhs : String &, rhs : Integer) = {
    // ...
    return lhs
}

myValue1 := 5
myValue2 := "apple"

// OKAY: normally this would produce an error, but the
// `operator` `<` is invoked and that function will implement the
// logical comparison of these unique types
if myValue1 < myValue2 {
    // ...
}

// OKAY: both orderings of operator `<` are implemented
if myValue2 < myValue1 {
    // ...
}

// OKAY: an operator `+=` takes a string and an integer
myValue2 += myValue1

// ERROR: the operator `+=` that takes an integer and a string is not found
myValue1 += myValue2
````


### Operator overloading from within types

Rather than declaring a global scope operator, operators can be declared within types. In this scenario, the left hand side in binary operations of the operator is always assumed to be the type, and the right or left hand side of the operator is assumed to be the type for unary operations.

If the type is used on the right handed side of an operator, then a global scope will be required to operator on a different type for the left hand side of an operator.

Operators declared on types are always selected over global scope operators if both a type operator and a global operator satisfy the match criteria during operator selection.

When an operator from an imported from a module, the imported operators are selected with lower priority over type operators, and also with lower priority than global operators within the current module. If a fall back occurs where two modules importing the same operator declaration will select the best match (where type qualifiers matter), or will cause an `function-candidate-ambiguous` error when the best match cannot be determined.

````zax
MyType :: type {
    value1 : Integer
    value2 : String

    operator += final : (result : MyType &)(rhs : Integer) = {
        value1 += rhs
        return _
    }

    operator += final : (result : MyType &)(rhs : String) = {
        value2 += rhs
        return _
    }

    operator ~ final : (result : MyType)() = {
        result.value1 = ~value1
        return result
    }

    operator > final : (result : Boolean)(rhs : Integer) constant = {
        return value1 > rhs
    }

    operator < final : (result : Boolean)(rhs : Integer) constant = {
        return value1 < rhs
    }
}

operator > final : (result : Boolean)(lhs : Integer, rhs : MyType constant &) = {
    return lhs > rhs.value1
}

myType : MyType

// OKAY: both are legal
myType += 5
myType += "hello"

// OKAY: defined as part of the type
if myType > 5 {
    // ...
}

// OKAY: defined in the global scope
if 5 > myType {
    // ...
}


// OKAY: defined as part of the type
if myType < 5 {
    // ...
}

// ERROR: not defined in the global scope
if 5 < myType {
    // ...
}
````


### Literal overloading

Operators overloading of string literals can be created. These kinds of operators create type instances from string literals, such as h'9842ABFD' can be used to create a constant integer given a hexadecimal value. As string literals are constants, they should utilize compile type string literal overloading.

The `[[execute]]` directive will cause the literal operator to be run at compile time and the constant result will be used in the place of a run time calculated value. Literals can not be invoked with runtime arguments as the strings quoted in single (`'`) or double (`"`) quotes are always created at compile time.

The input to literal operators is always a `String` type.

Adding a roman numeral literal operators:

````zax
operator roman'' : (value : )(input : String) [[execute]] = {
    // `value`'s type will be assumed based on the return value type
    // (e.g. a roman number being assigned to a `UShort` rather than an integer)
    
    // ...
}

operator roman'' : (value : Integer)(input : String) [[execute]] = {
    // `value`'s type will return an integer type where the type is
    // not discoverable based on the caller
    
    // ...
}

// `value1` will use the `Integer` version of the roman operator
// with a value of `8`
value1 := roman'VIII'

// `value2` will use the version of the roman operator where the
// result's type is omitted and becomes a `Byte` type with a value of 9
value2 : Byte = roman'IX'

// `value3` will use the version of the roman operator where the
// result's type is omitted and becomes a `Byte` type with a value of 3724
value3 : Word = roman"MMMDCCXXIV"
````

