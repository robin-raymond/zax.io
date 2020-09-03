
# [Zax Programming Language](index.md)

## Flow Control

### `if`

The `if` statement tests a condition and if `true` executes the code that follows, or `if` `false` then skips the code and proceeds to execute the code following the `else` if present.

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

    // ERROR: the condition and statement are separated but this is not a
    // legal form of the if statement; surrounding the result
    // with `{}` would be okay 
    if input < 0; return true

    // OKAY: This is allowed
    if input < 0 { return true }

    // OKAY: This is form is okay
    if input < 0
        return true

    // OKAY: This form is also okay
    if input < 0 {
        return true
    }
    return false
}
````


#### `if` / `else`

The `if` and `else` can be used to run one block of code or another depending `if` the condition is `true` or `false`.

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

If an `if` condition is `false` the `else` statement can immediately be followed by another `if` to condition testing for another condition dependent on the first condition being `false`.

````zax
print final : ()(...) = {
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

print("my disposition is ", whatsForLunch("apple"))
print("my disposition is ", whatsForLunch("veal"))
````


#### `if` initialization statements and condition

An `if` statement can contain initialization statements followed by a condition which must be separated by a sub-statement separator (`;;`). If a value is declared, the declared value's scope only exists within the context of the `if` or `else` control flows.

````zax
// a happy number is less than -5 and even, or less than -10, or divisible by 3
isHappyNumber : (output : Boolean)(input : Integer) = {

    if positive := input * -1 ;; positive > 5
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
    if base64String:, resourceError: = fetchUrlAsBase64(url) ;; !resourceError {
        result = decodeBase64(base64String),
    } else
        return , resourceError as Error

    cacheDataForLater(result)
    return result,
}
````


### `while`

A `while` statement will loop over a code block `while` a condition is `true`.

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


#### `while` initialization statements and condition

A `while` statement can contain initialization statements followed by a condition which must be separated by a sub-statement separator (`;;`). If a value is declared, the declared value's scope only exists within the context of the `while` control flows.


````zax
fetchNumberFromCosmos : (output : Integer)() = {
    // ... returns cosmic number
}

print : ()(...) = {
    // ...
}

countToCosmicNumber : ()(starting : Integer) {
    while endingNumber := fetchNumberFromCosmos() ;; starting <= endingNumber {
        print(starting)
        ++starting
    }
}
````


#### `while` initialization statements, condition, and post statements

The `while` loop contains initialization statements, a condition, and a post statements prior to a repeated code block. Each must be separated with sub-statement separators (';;'). The lifetime or variables declared in the initialization statements are scoped to the iterated loop. The post statements are executed after each completed `while`'s code block has completed execution (assuming a `break` statement was not encountered executing the `while` loop's repeated code block).

This code is valid:

````zax
print : ()(...) = {
    //...
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

Alternative forms using the `while` loop:

````zax
print : ()(...) = {
    //...
}

doStuff : (output : Integer)() = {
    total := 0

    // OKAY: only the condition is present
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

    // OKAY: the post-statements are optional
    while i: ;; i < 100 {
        ++i
        ++total
    }

    // OKAY: this code will compile even if post-statements are empty
    while i: ;; i < 100 ;; {
        print(i)
        ++i
        ++total
    }

    // OKAY: this code will compile even if post-statements are empty
    // (although it will loop forever since `i` is always `< 100`)
    while i: ;; i < 100 ;;
        print(i)

    // OKAY: this code will compile and the iterated statement is
    // assumed to be the following line
    while i: ;; i < 100 ;; ++i; ++total
        print(i)

    return total
}
````


### `do` and `while`

A `do` and `while` statement will loop over a code block while the condition is true and will always execute at least once.

````zax
print : ()(...) = {
    // ...
}

skipDivisibleBy3 : ()(starting : Integer, ending : Integer) = {

    do {
        if starting % 3 == 0
            print("Skip", starting)

        print("Count", starting)

        ++starting
    } while starting < ending
}
````


#### `do` and `while` initialization statements and condition

A `do` and `while` statement can contain initialization statements followed by a code block which must be separated by a sub-statement separator (`;;`). If a value is declared, the declared value's scope only exists within the context of the `while` control flows.

````zax
print : ()(...) = {
    // ...
}

skipDivisibleBy3 : ()(ending : Integer) = {

    do starting := ending - 100 ;; {
        if starting % 3 == 0
            print("Skip", starting)

        print("Count", starting)

        ++starting        
    } while starting < ending
}
````


#### `do` and `while` initialization statements, condition, and post statements

A `do` and `while` statement can contain initialization statements followed by a code block which must be separated by a sub-statement separator (`;;`) and a condition followed by post statements separated by sub-statement separators (`;;`). If a value is declared, the declared value's scope only exists within the context of the `while` control flows.

The initialization statements are not mandatory but the code block must be either put on the next line or surrounded with a scope (`{}`).

````zax
print : ()(...) = {
    // ...
}

skipDivisibleBy3 : ()(ending : Integer) = {
    do starting := ending - 100 ;; {
        if starting % 3 == 0
            print("Skip", starting)

        print("Count", starting)
    } while starting < ending ;; ++starting
}
````

An alternative form without pre-initialization:

````zax
print : ()(...) = {
    // ...
}

skipDivisibleBy3 : ()(starting: Integer, ending : Integer) = {
    do {
        if starting % 3 == 0
            print("Skip", starting)

        print("Count", starting)
    } while starting < ending ;; ++starting
}

// OKAY: on a single line where code is surrounded by a scope
// (the false will cause the loop to exit following the code block execution)
do { print("hello") } while false

// OKAY: a single statement is allowed where while is on the following line
// (the false will cause the loop to exit following the code block execution)
do
    print("hello")
while false
````


### `each`

The `each` keyword and `in` keyword iterate through a type's contents. For enumerators, the contents are the declared enumerators. For types, the contents are each individual contained variable.

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
    each fruit : in Fruit {
        print(fruit)
    }
}
````

These alternative versions will not compile:

````zax
listFruitAlt : ()() = {
    // ERROR: missing the `in` keyword and a `;;` is not a substitute
    each fruit: ;; Fruit {
        print(fruit)
    }
}

listFruitAlt2 : ()() = {
    // ERROR: missing the captured value
    each Fruit {
        print(Fruit)
    }
}
````


#### Using `each` to iterate over a type's values

The `each` keyword can be used to iterate over all of the variables contained with a type. The code block that follows the `each` will be re-compiled per subtype to ensure that different types remain compile time strict.

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
myType.value2 = "Life"

each value: in MyType
    print(value)            // will print `42` followed by "Life"
````


#### `each` initializer statements and condition

An `each` statement can contain a initialization statements followed by a condition which must be separated by a sub-statement separator (`;;`). If a value is declared, the declared value's scope only exists within the context of the `each` control flows.

In the example below, the returned array is captured and iterated one element at a time:

````zax
print : ()(...) = {
    //...
}

MyType :: type {
    id : String
    value1 : Integer
    value2 : String
}

getAMyType : (myType : MyType)() = {
    myType.id = "ABC123"
    myType.value1 = 42
    myType.value2 = "Life"
    return myType
}

each myType := getAMyType ;; value: in myType {
    print(value, myType.id)             // will print `42` followed by "Life", and each time prints "ABC123"
}
````


### Using `for` to iterate a range

The `for` will use range iteration to iterate through the entries in arrays or types that support range operations.

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
for value : in getReversedView(values)
    print(value)    // will print values in reversed order
````


#### `for` initializer statement and range iteration

A `for` statement can contain a initialization statements followed by a condition which must be separated by a sub-statement separator (`;;`). If a value is declared, the declared value's scope only exists within the context of the `for` control flows.

````zax
print final : ()(...) = {
    //...
}

getReversedView(result :)(values : Integer[3]&) {
    // ... logic is covered in ranges ...    
}

getAnArray : (result : )() = {
    values : String[3]
    values[0] = "bird"
    values[1] = "plane"
    values[2] = "superman"
    return values
}

// the iterated type is treated as a range type and the values are iterated
// based on the range's evaluation
for array := getAnArray ;; value : in getReversedView(array)
    print(value, array[0])    // will print values in reversed order, and "bird" each time
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


### `using` statement

The `using` statement is akin to a shortened `if` where the condition is not specified and always assumed to be true. This allows a temporary resource to declared and used within a the `using` scope. Unlike a [`scope`](scope.md) capture where variables outside the `scope` become restricted, the using allows full usage of local variables. If a value is declared, the declared value's scope only exists within the context of the `using` control flows.

````zax
print final : ()() = {
    //...
}

MyType :: type {
    value1 : Integer
    value2 : String
}

func : (myType : MyType)() = {
    //...
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
````


### `forever`

A `forever` statement will loop over a code block until the code is broken. The forever statement is akin to a shorthand `while` where the condition is not specified and always assumed to be `true`. This can also be useful for logic that might have mid-loop conditional exit.

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


#### `forever` initialization statements

A `forever` statement can contain initialization statements. If a value is declared, the declared value's scope only exists within the context of the `while` control flows.


````zax
fetchNumberFromCosmos : (output : Integer)() = {
    // ... returns cosmic number
}

print : ()(...) = {
    // ...
}

countToCosmicNumber : ()(starting : Integer) {
    forever endingNumber := fetchNumberFromCosmos() {
        print(starting)
        if (starting > endingNumber)
            break
        ++starting
    }
}
````


#### `forever` initialization statements and post statements

The `forever` loop contains initialization statements, and a post statements prior to a repeated code block. Both must be separated with sub-statement separators (';;'). The lifetime or variables declared in the initialization statements are scoped to the iterated loop. The post statements are executed after each completed `forever`'s code block has completed execution (assuming a `break` statement was not encountered executing the `forever` loop's repeated code block).

This code is valid:

````zax
print : ()(...) = {
    //...
}

countAndSkipOdds : (output : Integer)() = {
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

Alternative forms using the `forever` loop:

````zax
print : ()(...) = {
    //...
}

doStuff : (output : Integer)() = {
    total := 0

    // OKAY: only the condition is present
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

    // OKAY: this code will compile even if post-statements are empty
    forever i: ;; {
        ++i
        if i > 100
            break
        ++total
    }

    // OKAY: this code will compile even if post-statements are empty
    // (although it will loop forever)
    forever i: ;;
        print(i)

    // OKAY: this code will compile
    // (although it will loop forever)
    while i: ;; ++i; ++total
        print(i)

    return total
}
````


### Value polymorphism using `if`

The `if` statement can also be used in a function declaration to indicate that the function supports value polymorphism. The choice of which function to call is based on the pre-condition checks for the `if` statement. The compiler will execute the order of test based on the order of appearance in the code. If no match is found (and if present) then the undecorated version will be executed. The compiler may decide to reorder tests if reordering will have no net resulting impact on the code flow. Care should be taken to not have overlapping pre-conditions if code order cannot be preserved or guaranteed. The `[[likely]]` and `[[unlikely]]` can be used to hint to the compiler which execution path is more likely to be followed.

If some value polymorphic functions are declared using the `if` then a single polymorphic version function using the same types can be declared as a catch-all if none of the other conditions succeed (the logical equivalent of a `switch` `default` statement). If no function was found a panic may be issued.

Only functions marked as `final` support value polymorphism. The reason is the conditional check cannot be replaced and any assignment of a changeable function pointer would be ambiguous to which value polymorphic version would be replaced.

````zax
random : (value : Integer)() = {
    //... return a positive or negative integer
}

func final : ()(value : Integer) if { return value > 0 } = {
    //...
}

func final : ()(value : Integer) = {
    //...
}

while true {
    // each time the function is called a different function may be invoked
    func(random())
}
````

Another example computing factorial:

````zax
assert final : ()() = {
    //...
}

factorial final : (r : Integer)(n : Integer) if { return n > 1 } = {
    return n * factorial(n - 1)
}

factorial final : (r : Integer)(n : Integer) = {
    return 1
}

assert(120 == factorial(5))
````

An example of the children's game of FizzBuzz using value polymorphism:

````zax
print final : ()(...) = {
    //...
}

toString final : (result : String)(value : Integer) = {
    //...
}

next : (s: String)(i : Integer) if [[unlikely]] { return i % 15 == 0 } = {
    return "FizzBuzz"
}

next : (s: String)(i : Integer) if { return i % 3 == 0 } = {
    return "FizzBuzz"
}

next : (s: String)(i : Integer) if { return i % 5 == 0 } = {
    return "Buzz"
}

next : (s: String)(i : Integer) [[likely]] {
    return toString(i)
}

// displays: 1, 2, Fizz, 4, Buzz, Fizz, 7, 8, Fizz, Buzz, ... 14, FizzBuz, ...
while i := 1 ;; i < 100 ;; ++i {
    print(next())
}
````


#### Value polymorphism using `if` on nothing instances

A [nothing instance](nothing.md) can filter between normal function calls and functions that are called via the nothing instance. By checking if the self pointer  (`_`) is valid inside the `if` condition of the value polymorphic function, the code can decide to execute the nothing function or the normal function.

````zax
MyType :: type {
    +++ final : ()(:Unknown) = {
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

myType1 : MyType*       // points to nothing
MyType2 : MyType* @     // points to an allocated instance

myType1.doSomething()   // does nothing
myType2.doSomething()   // does something
````
