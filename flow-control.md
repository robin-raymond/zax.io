
# [Zax Programming Language](index.md)

## Flow Control

### `if`

The flow control statement(s) following an `if` or `else` condition must either continue on the following line or must be encapsulated inside a scope.

````zax
isNegativeOrGreaterThan10 : (input : Integer) -> (negative : Boolean) = {

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
isNegative : (output: Boolean) (input : Integer) = {

    // ERROR: the condition and statement following have no separation
    if input < 0 return true

    // ERROR: the condition is seen as a statement and the statement is
    // seen as the condition
    if input < 0; return true

    // This is form is okay
    if input < 0
        return true

    // This form is also okay
    if input < 0 {
        return true
    }
    return false
}
````


### `if` / `else`

````zax
print : ()(...) = {
    // ...
}

whatsForLunch : (mood : String)(food : String) {

    weight : Float

    if food == "apple" {
        weight = 100
        mood = "happy"
    } else if food == "banana" {
        weight = 118
        mood = "delighted"
    } else if food = "veal" {
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

print("my disposition is ", whatsForLunch("apple"))
print("my disposition is ", whatsForLunch("veal"))
````


#### `if` statement and condition

An `if` statement can contain a statement followed by a condition which must be separated by a `;`. If a value is declared, the declared value's scope only exists within the context of the `if` or `else` control flows.

````zax
// a happy number is less than -5 and even, or less than -10, or divisible by 3
isHappyNumber : (output : Boolean)(input : Integer) = {

    if positive := input * -1; positive > 5
        return positive > 10 || input % 2 == 0

    return input % 3 == 0
}

assert(!isNegative(5))
assert(isNegative(-15))
````

This form of if statement is useful for error condition handling without the need for exceptions:

````zax
Error :: type {
    //...
}

loadStringFromUrl : (
    result : String,    // contents returned from url already base64 decoded
    error : Error       // error is set when server cannot be reached
)(
    url : String        // the url to fetch the base64 encoded data
) = {
    if base64String:, resourceError: = fetchUrlAsBase64(url); !resourceError {
        result = decodeBase64(base64String),
    } else
        return , resourceError as Error

    cacheDataForLater(result)
    return result,
}
````


### `while`

A `while` statement will loop over a code block while a condition is true.

````zax
print : ()(...) = {
    // ...
}

countToOneHundred : ()(starting : Integer) {
    // repeat until starting reaches 100
    while starting <= 100 {
        print(starting)
        ++starting
    }
}
````


#### `while` statement and condition

A `while` statement can contain a statement followed by a condition which must be separated by a `;`. If a value is declared, the declared value's scope only exists within the context of the `while` control flows.


````zax
fetchNumberFromCosmos : (output : Integer)() = {
    // ... returns cosmic number
}

print : ()(...) = {
    // ...
}

countToCosmicNumber : ()(starting : Integer) {
    // repeat until starting reaches 100
    while endingNumber := fetchNumberFromCosmos(); starting <= endingNumber {
        print(starting)
        ++starting
    }
}
````


### `do`/`while`

A `do`/`while` statement will loop over a code block while the condition is true and will always execute at least once.

````zax
print : ()(...) = {
    // ...
}

skipDivisibleBy3 : ()(starting : Integer, ending : Integer) = {

    do {
        if starting % 3 == 0
            print("Skip", starting)

        print("Count", starting)
    } while starting < ending
}
````


### `for`

The for loop contains a pre-statement, a condition, and a post statement followed by the iterated code statement(s). The lifetime or variables declared in the pre-statement are scoped to the iterated loop.

This code is valid:

````zax
print : ()(...) = {
    //...
}

countAndSkipOdds : (output : Integer)() = {
    total := 0
    for i := 0; i < 100; ++i {
        if i % 2 == 0
            print("Not counting", i)
        print("Counting", i)
    }
    return total
}
````

This code will not compile:

````zax
print : ()(...) = {
    //...
}

doStuff : (output : Integer)() = {
    total := 0

    // ERROR: the pre-statement and post statement are missing
    for i: < 100 {
        if i % 2 == 0
            print("Not counting", i)
        print("Counting", i)
    }

    // ERROR: the post-statement is missing
    for i:; i < 100 {
    }

    // this code will compile as the post-statement is empty
    for i:; i < 100; {
        print(i)
        ++i
    }

    // this code will compile and the iterated statement is
    // assumed to be the following line
    for i:; i < 100; ++i
        print(i)

    return total
}
````


### `foreach`

The `foreach` keyword and `in` keyword iterate through an iterable type or value.

````zax
print : ()(...) = {
    //...
}

Fruit :: enum {
    apple,
    banana,
    pear,
    orange
}

listFruit : ()() = {
    foreach fruit : in Fruit {
        print(fruit)
    }
}
````

These alternative versions will not compile:

````zax
listFruitAlt : ()() = {
    // ERROR: missing the `in` keyword and a `;` is not a substitute
    foreach fruit : ; Fruit {
        print(fruit)
    }
}

listFruitAlt2 : ()() = {
    // ERROR: missing the iterated variable
    foreach Fruit {
        print(Fruit)
    }
}
````


#### `foreach` statement and condition

A `foreach` statement can contain a statement followed by a condition which must be separated by a `;`. If a value is declared, the declared value's scope only exists within the context of the `foreach` control flows.

In the example below, the returned array is captured and iterated one element at a time:

````zax
print : ()(...) = {
    //...
}

whatIsIt : (result : String[3])() {
    result[0] = "bird"
    result[1] = "plane"
    result[2] = "superman"
    return result
}

listOptions : ()() = {
    foreach couldBeItems := whatIsIt(); what : in couldBeItems {
        print("is it ", what, " where options are ", couldBeItems)
    }
}
````


#### Using `foreach` to iterate over a type's values

The `foreach` keyword can be used to iterate over all of the variables and types contained with another type. The code block that follows the `foreach` will be re-compiled per subtype to ensure that different types remain compile time strict.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

print final : ()(...) = {
    //...
}

myType : MyType

myType.value1 = 42
myType.value = "Life"

foreach value : in MyType
    print(value)            // will print `42` followed by "Life"
````


#### Using `foreach` to iterate a range

By adding the range keyword, the foreach will use range iteration to iterate through the entries.

````zax
print final : ()(...) = {
    //...
}

getReversedView(result :)(values : Integer[3]&) {
    // ... logic is covered in ranges ...    
}

values : String[3]
values[0] = "bird"
values[1] = "plane"
values[2] = "superman"

// the iterated type is treated as a range type and the values are iterated
// based on the range's evaluation
foreach value : in range getReversedView(values)
    print(value)    // will print values in reversed order
````


### `switch`

#### The `switch`, `case` and `default` flow control

The `switch` statement can be used to test a variable against a set of values. The `switch` statement will compare against a value list and execute zero or more statements based on the value tested. Each value is compared against each `case` value for equality and if `default` is present then any value that does not match an existing `case` causes the `default` code to be executed.

````zax
doSomething : ()() = {
    //...
}

randomBoolean : (result : Boolean)() = {
    // ... return true or false randomly ...
}

func : ()(value : Integer) = {
    switch value {
        case 1 {
            if randomBoolean()
                break
            doSomething()

            // a break is not required between case statements as the code
            // logic will not flow through from one case to another
            // automatically
        }
        case 2
            // single statement is executed then the switch exits
            doSomething()
        case 3
        case 4 {
            // if `value` is `3` or `4` two statements are executed and then
            // the switch exits
            doSomething()
            doSomething()
        }
        case 5
        case 6
            doSomething()
            // ERROR: This code will not compile as multiple statements
            // require using a `{}` scope
            doSomething()
        case 7
        case 8
        default {
            // values not `1` to `6` will execute a default scenario
            doSomething()
            doSomething()
        }
    }
}
````


#### Using `switch` to compare against runtime values

The `switch` statement can be used to compare against other runtime values. The values cannot be computed inside the `case` statement but they can be used as a comparison.

````zax
doSomething : ()() = {
    //...
}

randomBoolean : (result : Boolean)() = {
    // ... return true or false randomly ...
}

uniqueRandomNumber : (result : Integer)() = {
    //.... return a random number never returned before ...
}

func : ()(value : Integer) = {

    a := uniqueRandomNumber()
    b := uniqueRandomNumber()
    c := uniqueRandomNumber()
    d := uniqueRandomNumber()

    switch value {
        case a {
            if randomBoolean()
                break
            doSomething()

            // a break is not required between case statements as the code
            // logic will not flow through from one case to another
            // automatically
        }
        case b
            // single statement is executed then the switch exits
            doSomething()
        case c
        case d {
            // if `value` is `c` or `d` two statements are executed and then
            // the switch exits
            doSomething()
            doSomething()
        }
    }
}
````


#### Using `switch` with complex types

The `switch` can be used to compare against complex types. The complex type must have a comparison operator support.

The example below uses String types but any type could be used.

````zax
doSomething : ()() = {
    //...
}

randomBoolean : (result : Boolean)() = {
    // ... return true or false randomly ...
}

uniqueFruit : (result : String)() = {
    //.... return a unique random fruit name ...
}

func : ()(fruit : String) = {

    a := uniqueFruit()
    b := uniqueFruit()
    c := uniqueFruit()

    realD := uniqueFruit()

    d : String* = realD

    switch fruit {
        case a {
            if randomBoolean()
                break
            doSomething()
        }
        case b
            // single statement is executed then the switch exits
            doSomething()
        case c
        case d. {
            // if `fruit` is `c` or `d` two statements are executed and then
            // the switch exits
            doSomething()
            doSomething()
        }
    }
}
````


#### Using `switch` with alternative operators

The `switch` can be used with other boolean binary operations. When binary operations are used the order of the evaluations of binary conditions occurs in the same order as the `case` conditions.

````zax
doSomething : ()() = {
    //...
}

randomBoolean : (result : Boolean)() = {
    // ... return true or false randomly ...
}

uniqueRandomNumber : (result : Integer)() = {
    //.... return a random number never returned before ...
}

func : ()(value : Integer) = {
    switch value {
        case < a {
            if randomBoolean()
                break
            doSomething()

            // a break is not required between case statements as the code
            // logic will not flow through from one case to another
            // automatically
        }
        case b
            // single statement is executed then the switch exits
            doSomething()
        case > c
        case < d {
            // if `value` is `c` or `d` two statements are executed and then
            // the switch exits
            doSomething()
            doSomething()
        }
    }
}
````


#### `switch` statement and condition

A `switch` statement can contain a statement followed by a variable to test which must be separated by a `;`. If a value is declared, the declared value's scope only exists within the context of the `switch` control flows. This scenario can be useful to capture a computed value, test the computed value, and later access the previously computed value.

````zax
doSomething : ()() = {
    //...
}

randomBoolean : (result : Boolean)() = {
    // ... return true or false randomly ...
}

uniqueRandomNumber : (result : Integer)() = {
    //.... return a random number never returned before ...
}

func : ()(value : Integer) = {

    a := uniqueRandomNumber()
    b := uniqueRandomNumber()
    c := uniqueRandomNumber()
    d := uniqueRandomNumber()

    switch value := uniqueRandomNumber(); value {
        case a {
            // use captured value
            if value < 0 && randomBoolean()
                break
            doSomething()

            // a break is not required between case statements as the code
            // logic will not flow through from one case to another
            // automatically
        }
        case b
            // single statement is executed then the switch exits
            doSomething()
        case c
        case d {
            // if `value` is `c` or `d` two statements are executed and then
            // the switch exits
            doSomething()
            doSomething()
        }
    }
}
````


### polymorphic function preconditions using `if`

The `if` statement can also be used in a function declaration to indicate that the function supports value polymorphism. The choice of which function to call is based on the pre-condition checks for the `if` statement. The compiler will decide the order of testing and care must be taken to not have overlapping pre-conditions. The `[[likely]]` and `[[unlikely]]` can be used to hint to the compiler which execution path is more likely to be followed.

If some value polymorphic functions are declared using the `if` then a single polymorphic version function using the same types can be declared as a catch-all if none of the other conditions succeed (the logical equivalent of a `switch` `default` statement). If no function was found a panic may be issued.

Only functions marked as `final` support value polymorphism. The reason is the conditional check cannot be replaced and any assignment of a changeable function pointer would be ambiguous to which value polymorphic version would be replaced.

````zax
randomButMostlyPositive : (value : Integer)() = {
    //... return a positive or negative integer
}

func final : ()(value : Integer) if [[likely]] { return value > 0 } = {
    //...
}

func final : ()(value : Integer) = {
    //...
}

while true {
    // each time the function is called a different function may be invoked
    func(randomButMostlyPositive())
}
````

Another example computing factorial:

````zax
assert final : ()() = {
    //...
}

factorial final : (r : Integer)(n : Integer) if { return n > 1} = {
    return n * factorial(n - 1)
}

factorial final : (r : Integer)(n : Integer) = {
    return 1
}

assert(120 == factorial(5))
````
