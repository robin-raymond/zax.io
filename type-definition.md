
# [Zax Programming Language](index.md)

## Type Definition

### Trivial type definitions

Trivial types contain basic types such as booleans, integers, and floats, raw pointers and other trivial types. Trivial types can be cloned with a memory copy of the member contents.

````zax
:: import Module.System.Types

Point :: type {
    x : Integer     // type is defined as an `Integer` and
                    // initialized to 0 by default
    y : Integer     // type is defined as an `Integer` and
                    // initialized to 0 by default
}

Size :: type {
    w := 0          // type is assumed to be an `Integer`
                    // based on the default value `0`
    h := 0          // type is assumed to be an `Integer`
                    // based on the default value `0`
}

Rectangle :: type {
    // each value will be defaulted to 0
    point : Point   // Rectangle contains a `Point` type
    size : Size     // Size contains a `Size` type
}

Vector :: type {
    point : Point       // `Vector` type contains a `Point` type
    magnitude := 0.0    // type is assumed to be a `Float`
                        // based on the decimal value
    direction := 0.0    // type is assumed to be a `Float`
                        // based on the decimal value
}
````


Strings are an intrinsic types but they are not trivial type. String can allocate memory.

````zax
// The follow is not a trivial type as strings can cause memory allocation.
Error :: type {
    code := -1          // type is assumed to be an `Integer`
                        // based on the default value `-1`
    reason : String     // `String` type whose value that is defaulted to empty
}
````


### Uninitialized values

Types by default will automatically initialize to a known default value. However, for optimization (or other) reasons, types can be left uninitialized using a triple question mark (`???`). Whatever random bits happen to be in memory when the type is instantiated will become embedded into the type's values.

````zax
:: import Module.System.Types

A :: type {
    a : Integer = ???   // reading uninitialized values is undefined behavior
    b : Float = ???
    c : Float = 2.0     // only the `c` value has a initialized value
}
````


### Type instantiation

Instances of types can be created by declaring a variable name for the type and utilizing the type inside functions and scopes, contained within other types, or as part of dynamic allocation and construction.

````zax
A :: type {
    value1 := 0
    value2 := 0
    value3 := 0
}

B :: type {
    value1 := 0
    value2 := 0
    value3 := 0
}

C :: type {
    a : A           // type C contains a type `A` named `a`
    b : B           // type C contains a type `A` named `a`
    value1 := 0
    value2 := 0
}

func final : ()() = {
    // create `a`, `b`, and `c` with default values
    a : A
    b : B
    c : C

    // initialize `a` with values
    a.value1 = 5
    a.value2 = 3
    a.value3 = 7

    // initialize `b` with values
    b.value1 = a.value1 * a.value2 * a.value3
    b.value2 = a.value1 + a.value2 + a.value3
    b.value3 = a.value1 - a.value2 - a.value3

    // copy contents of `a` and `b` into `c.a` and `c.b`
    c.a = a
    c.b = b
    // copy specific values from the `c.a` and `c.b` types
    c.value1 = c.a.value1
    c.value2 = c.b.value2
}
````


### Local variable type declarations and definition

Types can be declared globally, inside other types, inside functions and inside locally defined types within functions. The type system is flexible to allow types as needed where needed.

````zax
func final : ()() = {
    // declare a variable and define its anonymous type in a single step
    myType : :: type {
        value1 : Integer
        value2 : Float

        // define a local type
        MyOtherType :: type {
            a : String
            b : U32
        }

        // use the local definition of a type
        value3 : MyOtherType
    }

    myType.value1 = 17
    myType.value2 = 3.14159

    myType.value3.a = "banana"
    myType.value3.b = h'ABCDEF01'
}
````


### Union type definition

Unions are data types where all of the types within share the same memory space. Typically unions are used for two main reasons:
* only one of the types can exist at a time and using a union incurs less overhead than declaring multiple optional data types
* the underlying data aligns to the same memory layout in such a way that the type of input value can be remapped to another type of output value and the data type still functions in properly definable behavior

None fo the union variables can have default values and the programmer is to assume all of the memory backing the union contains garbage data until a particular variable becomes initialized. Setting a value in a union of one variable and reading the data back as a different variable can have undefined behaviors if the underlying types are not memory layout compatible. Unions do not account for little or bid endian memory differences, nor alignment concerns (except that a union will have an alignment of the common alignment factor of all contained data types).

````zax
A :: type {
    animal : String
}

B :: type {
    size : Float
    planet : String
}

MyUnion :: union {
    myInt : Integer
    myFloat : Float
    a : A
    b : B
}


myUnion : MyUnion

// initialize the simple type to 3
myUnion.myInt = 3

// construct, fill, and destruct the union `a` variable
myUnion.a.+++()
myUnion.a.animal = "racoon"
myUnion.a.---()

// construct, fill, and destruct the union `b` variable
myUnion.b.+++()
myUnion.b.size = 5.5
myUnion.b.planet = "Mars"
myUnion.b.---()

// simple types can be set without needing construction but the language
// does provide a constructor for universality of type construction
myUnion.myFloat.+++()

// clearer just to initialize the float using the standard type reset method
// since it's a simple type but effectively `+++` construction of a simple
// type causes identical behavior
myUnion.myFloat =:

// UNDEFINED BEHAVIOR: resetting a non simple type will cause any destructor
// to get called but the `b` variable was not in a constructed state
myUnion.b =:
````


### Function type definition

Functions are a type just as any other type except they do not have a formal type declaration. Instead the type is always inlined in a lambda style definition as part of a declared function. Functions can be declared and defined at global space, inside other types, and inside other functions.

More information about functions can be found in the [functions](functions.md) section.


#### Simple function declaration and definition

````zax
// declare and define a variable and function type with no return results, no
// input arguments, and with the ability to capture variables and assign the
// function to point to nothing.
myFunc1 : ()()

// declare and define the same function type as above but prevent the function
// from ever having a value (other than to point to nothing); used to define
// a type of function without having actual code definition
myFunc2 final : ()()
````


#### Functions declaration and definition with arguments

````zax
// declare and define a function that returns nothing and takes a variadic input
// value and assigns code to execute
print final : ()(...) = {
    // ...
}

// declare and define a function that returns a `true` or `false` value and
// takes a single `Integer` input and assign code to execute
greaterThanThree final : (result : Boolean)(value : Integer) = {
    return value > 3
}

if greaterThanThree(2)
    print "Sorry, not greater than three!"

if greaterThanThree(10)
    print "Yes, we found a number bigger than three!"
````


#### Function declaration and definition with capturing

````zax
print final : ()(...) = {
    // ...
}

// declare and define a function that returns nothing and takes a single
// `String` argument and has the ability to capture values 
compareToKnownAnimals final : ()(value : String) = {

    // declare and define a function that returns nothing and takes a single
    // `String` argument and captures the `value` variable value
    printIfEqual : ()(compare : String) = [value] {
        if value == compare
            print("they are the same", value, compare)
        else
            print("they are not the same", value, compare)
    }

    printIfEqual("bear")
    printIfEqual("squirrel")
    printIfEqual("monkey")
    printIfEqual("ant")
    printIfEqual("butterfly")
}

// invoke call to `compareToKnownAnimals`
compareToKnownAnimals("elk")        // prints list of animals with no match
compareToKnownAnimals("ant")        // prints list of animals with one match
compareToKnownAnimals("butterfly")  // prints list of animals with one match
````
