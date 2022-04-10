
# [Zax Programming Language](index.md)

## Flow Control

### `if`

An `if` statement tests a condition and if a condition is `true` then code that follow executes, or `if` a condition is `false` then skips a code block and proceeds to execute code following an `else` statement (if present).

Flow control code blocks following an `if` or `else` condition must either continue on the following line or must be encapsulated inside a scope.

````zax
isNegativeOrGreaterThan10 : (negative : Boolean)(input : Integer) = {

    if input < 0
        return true

    if input > 10 {
        return true
    }

    return false
}

assert( !isNegativeOrGreaterThan10(5) )
assert( isNegativeOrGreaterThan10(-15) )
assert( !isNegativeOrGreaterThan10(10) )
assert( isNegativeOrGreaterThan10(11) )
````

This code will not compile:

````zax
isNegative final : (output: Boolean) (input : Integer) = {

    // ERROR: the condition and statement following have no separation
    if input < 0 return true

    // ERROR: the condition and statement are still not separated and this is
    // not a legal form of the `if` statement
    if input < 0; return true

    // OKAY: This example is allowed
    if input < 0 { return true }

    // OKAY: This example form is okay
    if input < 0
        return true

    // OKAY: This example form is also okay
    if input < 0 {
        return true
    }

    // the `;` operator causes the code statement that follow to be considered
    // part of the same statement at the same scope (even though they are
    // separate statements)
    if input < -1
        ++input;
        value := input * 3;
        input /= value;
        return true

    return false
}
````


#### `if` / `else`

An `if` and `else` statement can be used to run one block of code or another depending `if` a condition is `true` or `false`.

````zax
print final : ()(...) = {
    // ...
}

fruit := "apple"

if fruit == "apple"
    print("yummy")
else
    print("what is it?")


fruit = "durian"

if fruit == "durian" {
    print("yuck!")
    print("I know a few people who love it but I think it's noxious!")
} else
    print("oh good, not that one")
````


#### `if` / `else if`

If an `if` condition is `false` then an `else` statement can immediately be followed by another `if` statement for testing another condition dependent on a first condition being `false`.

````zax
print final : ()(...) = {
    // ...
}

whatsForLunch final : (mood : String)(food : String) = {

    weight : Float

    if food == "apple" {
        weight = 100
        mood = "happy"
    } else if food == "banana" {
        weight = 118
        mood = "delighted"
    } else if food = "durian" {
        weight = 226
        mood = "disgusted"
    } else if food == "potato" {
        weight = 180
        mood = "indifferent"
    }

    if weight < 150 {
        mood = mood + " and hungry"
    }

    return mood
}

print("my disposition is", whatsForLunch("apple"))
print("my disposition is", whatsForLunch("veal"))
````


#### `if` initialization statements and condition

An `if` statement can contain an initialization statement with a condition (which must be separated by a sub-statement separator `;;`) followed by a code block. If a value is declared in an initialization statement then that value's scope only exists within the context of an `if` or `else` control flow.

````zax
// a happy number is less than -5 and even, or less than -10, or divisible by 3
isHappyNumber final : (output : Boolean)(input : Integer) = {

    if positive := input * -1 ;; positive > 5
        return positive > 10 || input % 2 == 0

    return input % 3 == 0
}

assert(!isNegative(5))
assert(isNegative(-15))
````


This form of if statement is useful for error condition handling without a need for exceptions:

````zax
Error :: type {
    // ...
}

loadStringFromUrl final : (
    result : String,    // contents returned from url already base64 decoded
    error : Error       // an error is set when server cannot be reached
)(
    url : String        // the url to fetch the base64 encoded data
) = {
    if base64String:, resourceError: = fetchUrlAsBase64(url) ;; !resourceError {
        result = decodeBase64(base64String)
    } else
        return #, resourceError as Error

    cacheDataForLater(result)
    return result, #
}
````


### ternary operator

A ternary operator is a lightweight `if` statement. A ternary operator performs a conditional test and then chooses one value or another based on a conditional test being `true` or `false`. The ternary operator is split into sections: a conditional test, followed by `??` operator, followed by result if `true`, followed by a sub-statement separator `;;`, followed by a result if `false`.

````zax
a := 5
b := 10
c := 20
d := 30

// `e` is assigned to `c` if `a` is greater than `b` otherwise `e` is assigned
// to `d` all selected using the ternary `??` operator
e := a > b ?? c ;; d

// the above ternary operation is approximately equivalent to the following:
e : Integer = ???
if a > b
    e = c
else
    e = d
````

The ternary operator can be surrounded by `()` to ensure the sub-statement separator is not confused with other code that relies upon sub-statement separators.

````zax
coinFlip final : (result : Boolean)() = {
    // ...
}

random final : (result : Integer)() = {
    // ...
}

// the sub-statement separator for the ternary operator is distinct from the
// sub-statement separator for the `if` with an initialization statement
if i := (coinFlip() ?? random() ;; 10) ;; i > 0 {
    // do something ...
} else {
    // do something else ...
}
````

The `true` or `false` result for a ternary operator must be of exactly the same type (although they may be casted to the same type).

````zax
coinFlip final : (result : Boolean)() = {
    // ...
}

randomU32 final : (result : U32)() = {
    // ...
}

randomU16 final : (result : U16)() = {
    // ...
}

// ERROR: the ternary options are not of the same type for the `true` and
// `false` path
i := coinFlip() ?? randomU32() ;; randomU16()

// OKAY: the types are the same and thus fully compatible for both the `true`
// and `false` path
j := coinFlip() ?? randomU32() ;; randomU16() as U32
````


### `while`

A `while` statement will repeat over a code block `while` a condition is `true`.

````zax
print final : ()(...) = {
    // ...
}

countToOneHundred final : ()(starting : Integer) = {
    // repeat until starting reaches 100
    while starting <= 100 {
        print(starting)
        ++starting
    }
}
````


#### `while` initialization statements and condition

A `while` statement will repeat over a code block `while` a condition is `true`. A `while` statement can contain initialization statements with a condition followed by a repeatable code block which must be separated by a sub-statement separator (`;;`) except for the repeatable code block. If a value is declared in an initialization statement then that value's scope only exists within the context of a `while` control flow.

````zax
print final : ()(...) = {
    // ...
}

fetchNumberFromCosmos final : (output : Integer)() = {
    // ... returns cosmic number
}

countToCosmicNumber final : ()(starting : Integer) = {
    while endingNumber := fetchNumberFromCosmos() ;; starting <= endingNumber {
        print(starting)
        ++starting
    }
}
````


#### `while` initialization statements, condition, and post statements

A `while` statement will repeat over a code block `while` a condition is `true`. A `while` loop can contain an initialization statement, a condition, and a post loop statement followed by a repeatable code block. Each section must be separated with sub-statement separators (';;') except for the repeatable code block. Variables declared in an initialization statement are scoped to an iterated loop. A post loop statement is executed after each completed repeatable code block has executed (assuming a `break` statement was not encountered while executing a `while` loop's repeatable code block).

This code is valid:

````zax
print final : ()(...) = {
    // ...
}

countAndSkipOdds : (output : Integer)() = {
    total := 0
    while i := 0 ;; i < 100 ;; ++i {
        if i % 2 == 0 {
            ++total
            print("Counting", i)
        } else
            print("Not counting", i)
    }
    return total
}
````

Alternative forms using `while` loops:

````zax
print final : ()(...) = {
    // ...
}

doStuff final : (output : Integer)() = {
    total := 0

    // OKAY: a condition statement and repeatable code block are present but no
    // initialization or post loop statement is present
    {
        i := 0
        while i < 100 {
            if i % 2 == 0 {
                ++total
                print("Counting", i)
            } else
                print("Not counting", i)
        }
    }

    // OKAY: an initialization, condition statement, and repeatable code block
    // are present but no post loop statement is present
    while i: ;; i < 100 {
        ++i
        ++total
    }

    // OKAY: an initialization and a post loop statement are present followed by
    // a repeatable code block
    while i: ;; i < 100 ;; ++i {
        ++total
    }

    // ERROR: A sub statement separator `;;` was present indicating a post loop
    // statement should be present; this cause the repeatable code block to
    // treated as the post loop statement by the compiler, and the compiler
    // continues to attempt to locate a repeatable code block which isn't
    // present; thus the compiler will issue an error.
    while i: ;; i < 100 ;; {
        print(i)
        ++i
        ++total
    }

    // OKAY: an initialization and a post loop statement are present and a
    // an empty scope is used in place for the repeatable code block
    while i: ;; i < 100 ;; ++i {}

    // OKAY: a post loop statement is empty but present
    while i: ;; i < 100 ;; {} {
        print(i)
        ++i
        ++total
    }

    // OKAY: an initialization is present and a post loop statement is empty but
    // present; the repeatable code block is present (although it will repeat
    // forever since `i` is always `< 100`)
    while i: ;; i < 100 ;; {}
        print(i)

    // OKAY: An initialization, condition, and post loop statement are present
    // and the repeatable code block is present. The `;` operator causes
    // multiple statements to be combined together and treated as a single
    // statement at the same scope.
    while i: ;; i < 100 ;; ++i; ++total
        print(i)

    // OKAY: An initialization, condition, and post loop statements are present
    // and the repeatable code block is present. The `;` operator causes
    // multiple statements to be combined together and treated as a single
    // statement at the same scope. The repeatable code block is using the `;`
    // operator to indicate that all the loop statements are part of the same
    // statement at the same scope.
    while i: ;; i < 100 ;; ++i; ++total
        print(i);
        print(i % 10)

    // OKAY: An initialization, condition, and post loop statements are present
    // and the repeatable code block are all present. The `;` operator causes
    // multiple statements to be combined together and treated as a single
    // statement at the same scope.
    while i: ;; i < 100 ;; ++i; ++total {
        print(i)
    }

    return total
}
````


### `until`

An `until` statement will repeat over a code block `until` a condition is `true`.

````zax
print final : ()(...) = {
    // ...
}

countToOneHundred final : ()(starting : Integer) = {
    // repeat until `starting` reaches `100`
    until starting > 100 {
        print(starting)
        ++starting
    }
}
````


#### `until` initialization statements and condition

An `until` statement will repeat over a code block `until` a condition is `true`. An `until` statement can contain an initialization statement with a condition followed by a repeatable code block which must be separated by a sub-statement separator (`;;`) except for the repeatable code block. If a value is declared in an initialization statement then that value's scope only exists within the context of an `until` control flow.

````zax
print final : ()(...) = {
    // ...
}

fetchNumberFromCosmos final : (output : Integer)() = {
    // ... returns cosmic number
}

countToCosmicNumber final : ()(starting : Integer) = {
    until endingNumber := fetchNumberFromCosmos() ;; starting > endingNumber {
        print(starting)
        ++starting
    }
}
````


#### `until` initialization statements, condition, and post statements

An `until` statement will repeat over a code block `until` a condition is `true`. An `until` loop can contain an initialization statement, a condition, and a post loop statement followed by a repeatable code block. Each must be separated with sub-statement separators (';;') except for the repeated code block. Variables declared in an initialization statement are scoped to the loop. A post statement is executed after each completed repeatable code block has executed (assuming a `break` statement was not encountered executing an `until` loop's repeatable code block).

This code is valid:

````zax
print final : ()(...) = {
    // ...
}

countAndSkipOdds final : (output : Integer)() = {
    total := 0
    until i := 0 ;; i == 100 ;; ++i {
        if i % 2 == 0 {
            ++total
            print("Counting", i)
        } else
            print("Not counting", i)
    }
    return total
}
````

Alternative forms using the `until` loop:

````zax
print final : ()(...) = {
    // ...
}

doStuff final : (output : Integer)() = {
    total := 0

    // OKAY: only a condition and repeatable code block are present
    {
        i := 0
        until i == 100 {
            if i % 2 == 0 {
                ++total
                print("Counting", i)
            } else
                print("Not counting", i)
        }
    }

    // OKAY: an initialization and condition statement are present with
    // a repeatable code block
    until i: ;; i == 100 {
        ++i
        ++total
    }

    // OKAY: An initialization and condition statement are present and
    // and empty post loop statement. The repeatable code block is present.
    until i: ;; i == 100 ;; {} {
        print(i)
        ++i
        ++total
    }

    // OKAY: An initialization and condition statement are present. A
    // repeatable code block is present. The `;` operator causes multiple
    // statements in the repeatable code block to be combined together and
    // treated as a single statement at the same scope.
    until i: ;; i == 100
        print(i);
        +++i;
        ++total

    // OKAY: The `;` operator causes multiple statements in the post loop
    // statement to be combined together and treated as a single statement at
    // the same scope.
    until i: ;; i == 100 ;; ++i; ++total
        print(i)

    return total
}
````


### `redo` `while`

A `redo` `while` statement will repeat over a code block while a condition is `true` and it will always execute a repeatable code block at least once.

````zax
print final : ()(...) = {
    // ...
}

skipDivisibleBy3 final : ()(starting : Integer, ending : Integer) = {

    redo while starting < ending {
        if starting % 3 == 0
            print("Skip", starting)
        else
            print("Count", starting)

        ++starting
    }
}
````


#### `redo` `while` initialization statements and condition

A `redo` `while` statement will repeat over a code block while a condition is `true` and it will always execute a repeatable code block at least once. A `redo` `while` statement can contain an initialization statement with a condition followed by a repeatable code block which must be separated by a sub-statement separator (`;;`) except for the repeatable code block. If a value is declared in an initialization statement then that value's scope only exists within the context of a `redo` `while` control flow.

````zax
print final : ()(...) = {
    // ...
}

skipDivisibleBy3 final : ()(ending : Integer) = {

    redo while starting := ending - 100 ;; starting < ending {
        if starting % 3 == 0
            print("Skip", starting)
        else
            print("Count", starting)

        ++starting        
    }
}
````


#### `redo` `while` initialization statements, condition, and post statements

A `redo` `while` statement will repeat over a code block while a condition is `true` and it will always execute a repeatable code block at least once. A `redo` `while` statement can contain an initialization statement with a condition and post loop statement followed by a repeatable code block which must be separated by a sub-statement separator (`;;`) except for the repeatable code block. If a value is declared in an initialization statement then that value's scope only exists within the context of a `redo` `while` control flow.

An initialization statement and post loop statement are not mandatory but a repeatable code block must exist.

````zax
print final : ()(...) = {
    // ...
}

skipDivisibleBy3 final : ()(ending : Integer) = {
    redo while starting := ending - 100 ;; starting < ending ;; ++starting {
        if starting % 3 == 0
            print("Skip", starting)
        else
            print("Count", starting)
    }
}
````

An alternative form without pre-initialization:

````zax
print final : ()(...) = {
    // ...
}

skipDivisibleBy3 final : ()(starting: Integer, ending : Integer) = {
    redo while ;; starting < ending ;; ++starting {
        if starting % 3 == 0
            print("Skip", starting)
        else
            print("Count", starting)
    }
}

// OKAY: a single line where repeatable code is surrounded by a scope
// (the `false` will cause the loop to exit after the code block execution)
redo while false { print("hello") }

// OKAY: a single statement is allowed on the following line when a scope is
// not utilized for the repeatable code block
// (the `false` will cause the loop to exit after the code block execution)
redo while false
    print("hello")
````


### `redo` `until`

A `redo` `until` statement will repeat over a code block `until` a condition is `true` and it will always execute a repeatable code block at least once.

````zax
print final : ()(...) = {
    // ...
}

assert final : ()(check : Boolean) = {
    // ...
}

skipDivisibleBy3 final : ()(starting : Integer, ending : Integer) = {
    assert(starting < ending)

    redo until starting == ending {
        if starting % 3 == 0
            print("Skip", starting)
        else
            print("Count", starting)

        ++starting
    }
}
````


#### `redo` `until` initialization statements and condition

A `redo` `until` statement will repeat over a code block `until` a condition is `true` and it will always execute a repeatable code block at least once. A `redo` `until` statement can contain an initialization statement with a condition followed by a repeatable code block which must be separated by a sub-statement separator (`;;`) except for the repeatable code block. If a value is declared in an initialization statement then that value's scope only exists within the context of a `redo` `until` control flow.

````zax
print final : ()(...) = {
    // ...
}

assert final : ()(check : Boolean) = {
    // ...
}

skipDivisibleBy3 final : ()(ending : Integer) = {
    assert(starting < ending)

    redo until starting := ending - 100 ;; starting == ending {
        if starting % 3 == 0
            print("Skip", starting)
        else
            print("Count", starting)

        ++starting        
    }
}
````


#### `redo` `until` initialization statements, condition, and post statements

A `redo` `until` statement will repeat over a code block `until` a condition is `true` and it will always execute a repeatable code block at least once. A `redo` `until` statement can contain an initialization statement with a condition and post loop statement followed by a repeatable code block which must be separated by a sub-statement separator (`;;`) except for the repeatable code block. If a value is declared in an initialization statement then that value's scope only exists within the context of the `redo` `until` control flow.

An initialization statement and a post loop statement are not mandatory but a repeatable code block must exist.

````zax
print final : ()(...) = {
    // ...
}

assert final : ()(check : Boolean) = {
    // ...
}

skipDivisibleBy3 final : ()(ending : Integer) = {
    assert(starting < ending)

    redo until starting := ending - 100 ;; starting < ending ;; ++starting {
        if starting % 3 == 0
            print("Skip", starting)
        else
            print("Count", starting)
    }
}
````

An alternative form without pre-initialization:

````zax
print final : ()(...) = {
    // ...
}

assert final : ()(check : Boolean) = {
    // ...
}

skipDivisibleBy3 final : ()(starting: Integer, ending : Integer) = {
    assert(starting < ending)

    redo until ;; starting < ending ;; ++starting {
        if starting % 3 == 0
            print("Skip", starting)
        else
            print("Count", starting)
    } 
}

// OKAY: a single line where the repeatable code block is surrounded by a scope
// (the `true` will cause the loop to exit following the code block execution)
redo until true { print("hello") }

// OKAY: a single statement is allowed on the following line as a scope
// is not present around the repeatable code block
// (the `true` will cause the loop to exit following the code block execution)
redo until true
    print("hello")
````


### `each`

The `each` keyword and `in` keyword iterate through a type's contents. For enumerators, the contents are the declared enumerator values. For types, the contents are each individual contained variable.

````zax
print final : ()(...) = {
    // ...
}

Fruit :: enum {
    Apple,
    Banana,
    Pear,
    Orange
}

listFruit final : ()() = {
    each fruit : in Fruit {
        print(fruit)
    }
}
````

These alternative versions will not compile:

````zax
listFruitAlt final : ()() = {
    // ERROR: missing `in` keyword and a `;;` is not appropriate since `each`
    // does not contain separated sub-statements in this form
    each fruit: ;; Fruit {
        print(fruit)
    }
}

listFruitAlt2 final : ()() = {
    // ERROR: missed capturing a value
    each Fruit {
        print(Fruit)
    }
}
````


#### Using `each` / `in` to iterate over a type's values

The `each` keyword can be used to iterate over all variables contained in a type. A repeatable code block that follows an `each` keyword will be re-compiled per subtype to ensure that different subtypes remain compile-time strict.

````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    value1 : Integer
    value2 : String
}

myType : MyType

myType.value1 = 42
myType.value2 = "Life"

each value: in MyType
    print(value)            // will print `42` followed by "Life"
````


#### `each` / `in` initializer statement and condition

An `each` statement can contain an initialization statement with a condition followed by repeatable code block which must be separated by a sub-statement separator (`;;`) except for the repeatable code block. If a value is declared in an initialization statement then that value's scope only exists within the context of the `each` control flow.

In the example below, the returned array is captured and iterated one element at a time:

````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    id : String
    value1 : Integer
    value2 : String
}

returnAMyType final : (myType : MyType)() = {
    myType.id = "ABC123"
    myType.value1 = 42
    myType.value2 = "Life"
    return myType
}

each myType := returnAMyType() ;; value : in myType {
    print(value, myType.id)             // will print `42` followed by "Life", and each time prints "ABC123"
}
````


#### Using `each` / `in` to iterate over a type's value's metadata

The `each` keyword can be used to iterate over all variables contained in a type and a variable's metadata. A repeatable code block that follows an `each` keyword will be re-compiled per subtype to ensure that different subtypes remain compile-time strict.

````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    value1 : Integer
    value2 : String
}

myType : MyType

myType.value1 = 42
myType.value2 = "Life"

each value:, metadata: in MyType {
    print(value)            // will print `42` followed by "Life"
    print(metadata)         // will print information such as `value1` name
                            // and other variable properties
}
````


### Using `each` / `in` to iterate an array

The `each` will use an array iteration to iterate through all entries in arrays.

````zax
print final : ()(...) = {
    // ...
}

values : String[3]
values[0] = "bird"
values[1] = "plane"
values[2] = "superman"

// the iterated type is treated as a range type and the values are iterated
// based on the range's evaluation
each value: in values
    print(value)    // will print values in order
````


### Using `each` / `from` to iterate a range

The `each` will use range iterator to iterate through the entries in a range.

````zax
print final : ()(...) = {
    // ...
}

reverseView final : (result :)(values : Integer[3] &) = {
    // ... logic is covered in ranges ...    
}

values : String[3]
values[0] = "bird"
values[1] = "plane"
values[2] = "superman"

// the iterated type is treated as a range type and the values are iterated
// based on the range's evaluation
each value: in values
    print(value)    // will print values in order

// a range type is returned and the values are iterated based on the range's
// evaluation
each value : from reverseView(values)
    print(value)    // will print values in reversed order
````


#### `each` / `from` initializer statement and range iteration

A `each` statement can contain a initialization statement with a condition followed by a repeatable code block which must be separated by a sub-statement separator (`;;`) except for the repeatable code block. If a value is declared in an initialization statement then that value's scope only exists within the context of the `for` control flow.

````zax
print final : ()(...) = {
    // ...
}

reverseView final : (result :)(values : Integer[3] &) = {
    // ... logic is covered in ranges ...    
}

returnAnArray final : (result : )() = {
    values : String[3]
    values[0] = "bird"
    values[1] = "plane"
    values[2] = "superman"
    return values
}

// an initialization statement is present; a range type is returned and the
// values are iterated based on the range's evaluation
each array := returnAnArray() ;; value : from reverseView(array)
    print(value, array[0])    // will print values in reversed order,
                              // and "bird" each time
````


### `switch`

#### The `switch`, `case` and `default` flow control

A `switch` statement can be used to test a variable against a set of values. A `switch` statement will compare against a value list and execute zero or more statements based on values tested. A `switch` value is compared against each `case` value for equality and if `default` is present then any `switch` value that does not match an existing `case` causes a `default` code block to be executed.

````zax
doSomething final : ()() = {
    // ...
}

coinFlip final : (result : Boolean)() = {
    // ... return `true` or `false` randomly ...
}

func final : ()(value : Integer) = {
    switch value {
        case 1 {
            if coinFlip()
                break
            doSomething()

            // unlike other languages, a `break` is not required between
            // `case` statements as the code logic will not flow through from
            // one case to another automatically
        }
        case 2
            // single statement is executed then the `switch` exits
            doSomething()
        case 3
        case 4 {
            // if `value` is `3` or `4` two statements are executed and then
            // the `switch` exits
            doSomething()
            doSomething()
        }
        case 5
        case 6
            doSomething()
            // ERROR: this code will not compile as multiple statements are
            // present thus requiring using a `{}` scope or the `;` operator
            doSomething()
        case 7
        case 8
            // the `;` operator causes both statements to be joined as a
            // single statement at the same scope
            doSomething();
            doSomething()
        case 9
        case 10
        default {
            // values not `1` through `8` will execute this default scenario
            doSomething()
            doSomething()
        }
    }
}
````


#### Using `switch` to compare against runtime values

A `switch` statement can be used to compare against other runtime values. Values should not be computed inside a `case` statement but values can be used as a comparisons.

````zax
doSomething final : ()() = {
    // ...
}

coinFlip final : (result : Boolean)() = {
    // ... return `true` or `false` randomly ...
}

uniqueRandomNumber final : (result : Integer)() = {
    // .... return a random number never returned before ...
}

func final : ()(value : Integer) = {

    a := uniqueRandomNumber()
    b := uniqueRandomNumber()
    c := uniqueRandomNumber()
    d := uniqueRandomNumber()

    switch value {
        case a {
            if coinFlip()
                break
            doSomething()

            // a `break` is not required between `case` statements as the code
            // logic will not flow through from one case to another
            // automatically
        }
        case b
            // single statement is executed then the `switch` exits
            doSomething()
        case c
        case d {
            // if `value` is `c` or `d` two statements are executed and then
            // the `switch` exits
            doSomething()
            doSomething()
        }
    }
}
````


#### Using `switch` with complex types

A `switch` statement can be used to compare against complex types. Complex types must have comparison operator support.

The example below uses String types (but any type could be used):

````zax
doSomething final : ()() = {
    // ...
}

coinFlip final : (result : Boolean)() = {
    // ... return `true` or `false` randomly ...
}

uniqueFruit final : (result : String)() = {
    // .... return a unique random fruit name ...
}

func final : ()(fruit : String) = {

    a := uniqueFruit()
    b := uniqueFruit()
    c := uniqueFruit()

    realD := uniqueFruit()

    d : String * = realD

    switch fruit {
        case a {
            if coinFlip()
                break
            doSomething()
        }
        case b
            // single statement is executed then the `switch` exits
            doSomething()
        case c
        case d. {
            // if `fruit` is `c` or `d` two statements are executed and then
            // the `switch` exits
            doSomething()
            doSomething()
        }
    }
}
````


#### Using `switch` with alternative operators

A `switch` statement can be used with other binary operators that return a `Boolean` value. When binary operators are used, evaluation ordering of binary conditions occur in the same order as `case` conditions.

````zax
doSomething final : ()() = {
    // ...
}

coinFlip final : (result : Boolean)() = {
    // ... return `true` or `false` randomly ...
}

uniqueRandomNumber final : (result : Integer)() = {
    // .... return a random number never returned before ...
}

func final : ()(value : Integer) = {
    switch value {
        case < a {
            if coinFlip()
                break
            doSomething()

            // a `break` is not required between `case` statements as the code
            // logic will not flow through from one `case` to another
            // automatically
        }
        case b
            // single statement is executed then the `switch` exits
            doSomething()
        case > c
        case < d {
            // if `value` is `c` or `d` two statements are executed and then
            // the `switch` exits
            doSomething()
            doSomething()
        }
    }
}
````


#### `switch` statement and condition

A `switch` statement can contain a statement followed by a variable to test which must be separated by a sub statement separator (`;;`) operator. If a value is declared in an initialization statement then that value's scope only exists within the context of a `switch` control flow. This scenario can be useful to capture a computed value, test the computed value, and later access the previously computed value.

````zax
doSomething final : ()() = {
    // ...
}

coinFlip final : (result : Boolean)() = {
    // ... return `true` or `false` randomly ...
}

uniqueRandomNumber final  : (result : Integer)() = {
    // .... return a random number never returned before ...
}

func final : ()(value : Integer) = {

    a := uniqueRandomNumber()
    b := uniqueRandomNumber()
    c := uniqueRandomNumber()
    d := uniqueRandomNumber()

    switch value := uniqueRandomNumber() ;; value {
        case a {
            // use captured value
            if value < 0 && coinFlip()
                break
            doSomething()

            // a `break` is not required between `case` statements as the code
            // logic will not flow through from one `case` to another
            // automatically
        }
        case b
            // single statement is executed then the `switch` exits
            doSomething()
        case c
        case d {
            // if `value` is `c` or `d` two statements are executed and then
            // the `switch` exits
            doSomething()
            doSomething()
        }
    }
}
````

#### `switch` statement and `case continue`

A `case continue` statement will cause the next case clause to be processed as if the following `case` (or `default`) condition were true. Effectively this allows for one `case`'s statement to fallthrough to another `case`'s statement.

````zax
doSomething final : ()(value : Integer) = {
    // ...
}

coinFlip final : (result : Boolean)() = {
    // ... return `true` or `false` randomly ...
}

func final : ()(value : Integer) = {
    switch value {
        case 1 {
            if coinFlip()        // `if coinFlip()` is `true`
                case continue    // will cause the next `case 2` to `continue`
                                 // processing as if it were a true thus causing
                                 // `doSomething(2)` to execute but not
                                 // `doSomething(1)`

            doSomething(1)

            // unlike other languages, a `break` is not required between
            // `case` statements as the code logic will not flow through from
            // one case to another automatically
        }
        case 2
            // single statement is executed then the `switch` exits
            doSomething(2)
        case 3
        case 4 {
            // if `value` is `3` or `4` two statements are executed and then
            // the `switch` exits
            doSomething(3)
            doSomething(4)
        }
        case 5
        case 6
            doSomething(5)
            // ERROR: this code will not compile as multiple statements are
            // present thus requiring using a `{}` scope or the `;` operator
            doSomething(6)
        case 7
        case 8
            // the `;` operator causes both statements to be joined as a
            // single statement at the same scope
            doSomething(7);
            doSomething(8)
        case 9
        case 10
        default {
            // values not `1` through `8` will execute this default scenario
            doSomething(9)
            doSomething(10)
        }
    }
}
````


### `using` statement

A `using` statement is akin to a shortened `if` statement where a condition is not specified and always assumed to be `true`. This allows a temporary resource to declared and used within a `using` scope. Unlike a [`scope`](scope.md) capture where variables outside the `scope` become restricted, a `using` allows full usage of local variables. If a value is declared in an initialization statement then that value's scope only exists within the context of a `using` control flow.

````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    value1 : Integer
    value2 : String
}

func final : (myType : MyType)() = {
    // ...
    return myType
}

using value := func()
    doSomething(value)

using value := func() {
    print(value.value1)
    print(value.value2)
}

using value own := func() {
    print(value1)
    print(value2)
}

using value own := func()
    doSomething(value);
    print(value2)
````


### `forever`

A `forever` statement will repeat over a code block until that code issues a `break` (or `continue` with a named scope). A forever statement is akin to a shorthand `while` where a condition is not specified and always assumed to be `true`. This can also be useful for logic that might have a mid-loop conditional exit.

````zax
print final : ()(...) = {
    // ...
}

countToOneHundred final : ()(starting : Integer) = {
    // repeat until starting is larger than 100
    forever {
        print(starting)
        if starting > 100
            break
        ++starting
    }
}
````


#### `forever` initialization statement

A `forever` statement can contain an initialization statement. If a value is declared in the initialization statement then that value's scope only exists within the context of a `while` control flow.


````zax
print final : ()(...) = {
    // ...
}

fetchNumberFromCosmos final : (output : Integer)() = {
    // ... returns cosmic number
}

countToCosmicNumber final : ()(starting : Integer) = {
    forever endingNumber := fetchNumberFromCosmos() {
        print(starting)
        if (starting > endingNumber)
            break
        ++starting
    }
}
````


#### `forever` initialization statement and post loop statement

A `forever` loop can contain an initialization and a post loop statement prior to a repeatable code block. All must be separated with sub-statement separators (';;') except for the repeatable code block. Variable declared in the initialization statement are scoped to the iterated loop. A post loop statement is executed after each completed `forever`'s repeatable code block has executed (assuming a `break` statement was not encountered executing the `forever` loop's repeatable code block).

This code is valid:

````zax
print final : ()(...) = {
    // ...
}

countAndSkipOdds final : (output : Integer)() = {
    total := 0
    forever i := 0 ;; ++i {
        if i % 2 == 0 {
            ++total
            print("Counting", i)
            if i > 100
                break
        } else
            print("Not counting", i)
    }
    return total
}
````

Alternative forms using a `forever` loop:

````zax
print final : ()(...) = {
    // ...
}

doStuff final : (output : Integer)() = {
    total := 0

    // no initialization or post-loop statement before the repeatable code block
    {
        i := 0
        forever {
            if i % 2 == 0 {
                ++total
                print("Counting", i)
                if i > 100
                    break
            } else
                print("Not counting", i)
        }
    }

    // an initialization statement is present prior to the repeatable code block
    forever i: {
        ++i
        if i > 100
            break
        ++total
    }

    // an initialization statement and an empty post loop statement is present
    // followed by a repeatable code block
    forever i: ;; {} {
        ++i
        if i > 100
            break
        ++total
    }

    // an initialization statement and post loop statement is present followed
    // by a repeatable code block (although it will repeat forever)
    forever i: ;; ++i
        print(i)

    // The `;` operator is used to combine a post loop statement as if it were
    // a single statement.
    forever i: ;; ++i; ++total
        print(i)

    // The `;` operator is used to combine a repeatable code block as if it
    // were a single statement.
    forever i: ;; ++i
        print(i);
        print(i % 2)

    return total
}
````


### Value polymorphism using `if`

An `if` statement can also be used in a function declaration to indicate that a function supports value polymorphism. Which function to call is based on a pre-condition check for a given `if` statement. The compiler will execute each test based on the order of appearance in code if no specific order has a bias. If no match is found (and if present) then a undecorated version of a function will be executed. A compiler may decide to reorder tests if the reordering will have no net resulting impact on the code flow. Care should be taken to not have overlapping pre-conditions if code order cannot be preserved or guaranteed. The `[[likely]]` and `[[unlikely]]` compiler directives can be used as a hint to a compiler which execution path is more likely to be followed (thus tests can be reordered appropriately).

If some value polymorphic functions are declared using an `if` statement then a single polymorphic version function using the same types can be declared as a catch-all if none of the other conditions succeed (i.e. the logical equivalent of a `switch` `default` statement). If no function was found a panic may be issued.

Only functions marked as `final` support value polymorphism. A conditional check on a function cannot be replaced and any assignment of a changeable functions would be ambiguous to which value polymorphic version should be replaced. However, a function without any value polymorphism `if` condition can be `varies` allowing the function to be reassigned to a new function implementation that will assume to replace only a default non-conditional version (i.e. a version that does not contain value polymorphism).

````zax
random final : (value : Integer)() = {
    // ... return a positive or negative integer
}

func final : ()(value : Integer) if { return value > 0 } = {
    // ...
}

func final : ()(value : Integer) = {
    // ...
}

forever {
    // each time the function is called a different function may be invoked
    func(random())
}
````

Another example computing factorial:

````zax
assert final : ()() = {
    // ...
}

factorial final : (r : Integer)(n : Integer) if { return n > 1 } = {
    return n * factorial(n - 1)
}

factorial final : (r : Integer)(n : Integer) = {
    return 1
}

assert(120 == factorial(5))
````

An example of a children's game of FizzBuzz using value polymorphism:

````zax
print final : ()(...) = {
    // ...
}

toString final : (result : String)(value : Integer) = {
    // ...
}

next final : (s: String)(i : Integer) if [[unlikely]] { return i % 15 == 0 } = {
    return "FizzBuzz"
}

next final : (s: String)(i : Integer) if [[likely]] { return i % 3 == 0 } = {
    return "FizzBuzz"
}

next final : (s: String)(i : Integer) if { return i % 5 == 0 } = {
    return "Buzz"
}

// next is not marked as `final` and can be replaced with an alternative
// implementation
next : (s: String)(i : Integer) [[likely]] = {
    return toString(i)
}

// displays: 1, 2, Fizz, 4, Buzz, Fizz, 7, 8, Fizz, Buzz, ... 14, FizzBuz, ...
while i := 1 ;; i < 100 ;; ++i {
    print(next())
}
````


#### Value polymorphism using `if` on nothing instances

A [nothing instance](nothing.md) can filter between normal function calls and functions that are called on a nothing instance. By checking if a self pointer  (`_`) is valid inside an `if` condition of a value polymorphic function, code can decide if a nothing version of a function or a normal version function should be called.

````zax
MyType :: type {
    +++ final : ()(:Nothing) = {
        // instance to a nothing type
    }

    doSomething final : ()( : Integer) if [[unlikely]] { return !_ } = {
        // do nothing -- inside nothing instance of MyType
    }
    doSomething final : ()(value : Integer) = {
        // ...
        // do something -- normal instance of MyType
        // ...
    }
}

myType1 : MyType *      // points to nothing
MyType2 : MyType * @    // points to an allocated instance

myType1.doSomething()   // does nothing
myType2.doSomething()   // does something
````
