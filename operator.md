
# [Zax Programming Language](index.md)

## Operator Overloading

### Basic overloading

Overloadable operators (e.g. greater than (`>`), less than (`<`>), equal to (`=`)) can be augmented with new functions. This allows types to support additional operations in symbolic form rather than requiring natural language function calls. Operators can be declared in a global scope where two arguments will be required for binary operations and one operator is required for unary operators.

The resulting type for an operator is entirely arbitrary but the recommendation is to follow the normal expectations of a consumer of the programming interface. For example, comparison operators are expected to return `Boolean` values and thus returning a String from these operations would be unexpected.

Adding three new operators for mixing `String` types and `Integer` types and a pre/post unary operator for `String` types:

````zax
operator binary '<' final : (result : Boolean)(lhs : Integer, rhs : String) = {
    // ...
}

operator binary '<' final : (result : Boolean)(lhs : String, rhs : Integer) = {
    // ...
}

operator binary '+=' final : (
    result : String &
)(
    lhs : String &, rhs : Integer
) = {
    // ...
    return lhs
}

operator pre unary `~` final : (result : String&)(rhs : String &) = {
    // ...
    return rhs
}

operator post unary `++` final : (result : String&)(lhs : String &) = {
    // ...
    return lhs
}

myValue1 := 5
myValue2 := "apple"

// OKAY: normally this would produce an error, but the `operator` `<` is invoked
// and that function will implement a logical comparison of these unique types
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

// OKAY: a pre-unary operator defined taking a string (and returns a string) 
~myValue2

// OKAY: a post-unary operator defined taking a string (and returns a string) 
myValue2++
````


### Operator overloading from within types

Rather than declaring a global scope operator, operators can be declared within types. In this scenario, the left hand side in binary operations of the operator is always assumed to be the current type's instance, and the right or left hand side of the operator is assumed to be the current type's instance for unary operations.

If a different type instance is required on the left handed side of an operator then a global scope will be required declaring both types for the left and right side of an operator.

Operators declared on types are always selected over globally scope operators if both a type operator and a global operator satisfy the match criteria during operator selection.

When an operator is imported from a module, the imported operators are selected with lower priority over a type operators, and also with lower priority than global operators within the current module. If the current type and global scope do not define an operator but two different imported modules define an implementation then the best match is assumed (where type qualifiers matter), or the first module imported will be assumed to be the correct match based on order of importation.

````zax
MyType :: type {
    value1 : Integer
    value2 : String

    operator binary '+=' final : (result : MyType &)(rhs : Integer) = {
        value1 += rhs
        return _
    }

    operator binary '+=' final : (result : MyType &)(rhs : String) = {
        value2 += rhs
        return _
    }

    operator pre unary '~' final : (result : MyType)() = {
        result.value1 = ~value1
        return result
    }

    operator binary '>' final : (result : Boolean)(rhs : Integer) constant = {
        return value1 > rhs
    }

    operator binary '<' final : (result : Boolean)(rhs : Integer) constant = {
        return value1 < rhs
    }
}

operator binary '>' final : (result : Boolean)(lhs : Integer, rhs : MyType constant &) = {
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

// ERROR: not defined in any scope
if 5 < myType {
    // ...
}
````


### Literal overloading

Operators overloading of string literals can be created. These kinds of operators create type instances from string literals. As an example, h'9842ABFD' can be used to create a constant integer given a hexadecimal value. As string literals are constants, they should utilize compile type string literal overloading.

An `[[execute]]` directive will cause a literal operator to be run at compile time and a constant result will be used in-place of a run time calculated value. Literals cannot be invoked with runtime arguments as the strings quoted in single (`'`) or double (`"`) quotes are always resolved at compile time.

An input to literal operators is always a `String` type.

Adding a roman numeral literal operators:

````zax
operator literal 'roman' : (value : )(input : String) [[execute]] = {
    // `value`'s type will be assumed based on the return value type
    // (e.g. a roman number being assigned to a `UShort` rather than an integer)
    
    // ...
}

operator literal 'roman' : (value : Integer)(input : String) [[execute]] = {
    // `value`'s type will return an integer type where the type is
    // not discoverable based on the caller
    
    // ...
}

// `value1` will use the `Integer` version of the roman operator with a value
// of `8`
value1 := roman'VIII'

// `value2` will use the version of the roman operator where the
// result's type is omitted and becomes a `Byte` type with a value of 9
value2 : Byte = roman'IX'

// `value3` will use the version of the roman operator where the
// result's type is omitted and becomes a `Byte` type with a value of 3724
value3 : Word = roman"MMMDCCXXIV"
````


### Word operators and compound word operators

Word operators can be used to create new operators from language words. These operators can be singular words such as `run` or compound words such as `run fast`.

Word operators can overload existing operators that are not technically overloadable such as `unsafe as`. In these cases word operators are of lower priority than the built-in word operators and only apply when the default operation is not available (such as casting from a `String` to a `WideString`).

Selection rules will cause the longest compound word to be matched first and lessor length compounded words to be matched later.

````zax
// forward definitions are required for named operators that are not already
// system defined so they can be recognized by the compiler as operators in the
// current context; however, they are only required to be forwarded if the
// operator is not already defined in the global namespace

// technically these forward declares are not required as a definition for
// these operators are defined immediately below prior to usage
run :: forward operator pre unary
run fast :: forward operator pre unary
run fast from  :: forward operator binary


operator pre unary 'run' final : (result : String &)(rhs : String &) = {
    // ...
    return rhs
}

operator pre unary 'run fast' final : (result : String &)(rhs : String &) = {
    // ...
    return rhs
}

operator binary 'run fast from' final : (result : String &)(lhs : String &, rhs : Integer) = {
    // ...
    return lhs
}

value : String

// OKAY: pre `run` operator is selected
run value

// OKAY: pre `run fast` operator is selected, not `run` operator because
// `run fast` is the longer match
run fast value

// ERROR: `run` is not a post-unary operator
value run

// OKAY: the binary operator `run fast from` is selected
value run fast from 5

// ERROR: no binary or post operator `run fast` exists and thus an error is
// issued
value run fast 5
````
