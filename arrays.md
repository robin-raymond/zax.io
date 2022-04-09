
# [Zax Programming Language](index.md)

## Arrays

Zax arrays are multi dimensional. Arrays can be of a fixed size or they can be of a dynamic size. All array dimensions have an implicit `length` `mutator` to indicate the length of an array. Arrays are zero based indexes for their lookup. Once arrays are allocated they cannot be resized but their contents can be replaced if an array is `mutable`.

````zax
randomNumber final : (result : Integer)() = {
    // ...
}

// all types are automatically initialized to a `type`'s default value (unless
// an `???` operator is used to leave an underlying `type`'s memory
// uninitialized).
myArray : Integer[1000]

each arrayValue : Integer in myArray {
    arrayValue =  randomNumber()
}

// create a new array of the same length and copy the contents from the original
// array; as this array is a fixed length, dynamic allocation is not needed in
// this particular context (i.e. where a compiler can deduce a fixed length)
anotherArray : Integer[] = myArray

// copy only the first 10 array elements into a new array
yetAnotherArray1 : Integer[10] = myArray

// copy the first 1000 array elements into a new array (all other elements
// will contain a default initialized value)
yetAnotherArray2 : Integer[5000] = myArray

// copy the first 1000 array elements into a new array (all other elements
// will contain uninitialized memory)
yetAnotherArray3 : Integer[5000] = [{ myArray, ??? }]

// copy the first 10 array elements into a new array, followed by 50 elements
// starting at the 50th element and leave the remaining values uninitialized
// (all other elements following will contain uninitialized values)
yetAnotherArray5 : Integer[5000] = [{ myArray.slice(0, 9),
                                      myArray.slice(50, 99),
                                      ??? }]

// fill the initial part of the array with the literal values from the `1`
// through `10` array slice and then fill the remainder of the array with a
// slice from a previous array.
yetAnotherArray6 : Integer[100] = [{ [1, 2, 3, 4, 5, 6, 7, 8, 9, 10 ],
                                     myArray.slice(0, 89)
                                   }]

// Copy the first 1000 array elements into the new array (all other elements
// will contain defaulted values); even though a multi-value operator `[{` `}]`
// is used there's no requirement that multiple values are actually declared;
// but using a multi-value operator in this context is unnecessary;
yetAnotherArray7 : Integer[5000] = [{ myArray }]
````


### Simple array initialization

Arrays can be default initialized with a set of values.

Single dimensional arrays:

````zax
// a singular array slice is passed to the array containing values `1` through
// `5`
myArray : Integer[5] = [ 1, 2, 3, 4, 5 ]
````


Single dimensional arrays:

````zax
// A multiple value operators `[{` on arrays declare that multiple slices will
// initializing an array. In the example below, two slices of arrays initialize
// the array where the first half of the array is initialized with a slice
// containing values `1` through `5` and the second half of the array is
// initialized with a slice containing the values `10` through `6` in reverse
// order.
myArray : Integer[10] = [{ [ 1, 2, 3, 4, 5 ], [ 10, 9, 8, 7, 6 ] }]
````

Multi dimensional arrays:

````zax
// a single multi-dimensional slice is used to initialize the array
myArray : Integer[5][2] = [
    [ 1, -1 ],
    [ 2, -2 ],
    [ 3, -3 ],
    [ 4, -4 ],
    [ 5, -5 ]
]
````

Simple array where the `type` and length is assumed based on the array slice passed into the array:

````zax
myArray : [] = [ 1, 2, 3, 4, 5 ]
````

Simple array which is functionally equivalent to the above array (although the multi-value operators `[{` `}]` are not necessary in this context):

````zax
// array is declared to receives a set of slices but only a single slice is
// passed in using a multi-value operator list (thus multi-value operators
// were unnecessary in this context).
myArray : [] = [{ [ 1, 2, 3, 4, 5 ] }]
````

A seemingly simple array where a compiler will actually error due to a compiler mistaking the code as a function capture rather than as an array declaration:

````zax
// ERROR: is this code an array or a function capture? the intent is not clear
myArray := [ 1, 2, 3, 4, 5 ]
````

Simple array whose declaration is not confusing to a compiler and a compiler will treat the code as a simple array of a fixed length:

````zax
// in this context multi-value operators `[{` `}]` are required otherwise a
// compiler would see an array slice as an attempt to capture values for a
// function
myArray := [{ [ 1, 2, 3, 4, 5 ] }]
````



### Array of types

Arrays can support arbitrary types and need not be limited to simple intrinsic types. 

````zax
MyType :: type {
    value1 : String
    value2 : Integer
}

// create an array of `MyType` types which is default initialized
myArray : MyType[1000]
````


### Array initialization with constructors

In the example below, the `type` has two different constructors (i.e. polymorphic). The `type` can be initialized using either constructor and standard polymorphic rules will apply to choose which constructor was intended.

````zax
MyType :: type {
    value1 : Integer
    value2 : String

    // declare a polymorphic constructor that requires one value
    +++ final : ()(value : Integer) = {
        value1 = value
    }

    // declare a polymorphic constructor that requires two values
    +++ final : ()(
        value1 : Integer,
        value2 : String
    ) = {
        _.value1 = value1
        _.value2 = value2
    }
}

// when a single argument constructor is used, each element's constructor
// argument is separated with a comma (`,`) inside a slice and contains a single
// value per array element and thus the single value constructor is selected
myType1 : MyType[3] = [ 1, 2, 3 ]

// when a multiple argument constructor is used, each constructor argument list
// is enclosed in a multi-value operator `[{` `}]` where each constructor
// argument is separated with a comma (`,`)
myType2 : MyType[3] = [
    [{ 1, "planes" }],
    [{ 2, "trains" }],
    [{ 3, "automobiles" }]
]

// ERROR: mixing an matching different constructor selection within the same
// array slice is not legal
myType3 : MyType[3] = [
    [{ 1, "planes" }],      // multi-value constructor
    2,                      // ERROR: switching to single value constructor
    [{ 3, "automobiles" }]  // ERROR: switching back to multi-value constructor
]
````

// OKAY: mixing an matching different constructor selection from within
// different array slices is perfectly legal
myType3 : MyType[6] = [{
// first slice using the multi-value constructor
[
    [{ 1, "planes" }],        // multi-value constructor used for array index 0
    [{ 2, "trains" }],        // multi-value constructor used for array index 1
    [{ 3, "automobiles" }]    // multi-value constructor used for array index 2
],
// second slice using the single value constructor
[ 4, 5, 6 ]                   // single value constructor used for second slice
                              // whose array index becomes 3 through 5
}]
````


### Type named value initialization or arrays

Named initialization of types is supported in an array slice declaration. The selection rules used exactly as they would be applied to each individualized type's initialization, except done multiple times over in a slice declaration. An array slice declaration imposes an additional requirement where all the initialized named values are each contained within a multi-value operator `[{` }]` per array element and all the collective elements are contained within an array's slice declaration.

Mixing named value declaration and traditional constructor selection within the same array slice is not legal. Either named initializers are used or constructors are used but not both at the same time within the same array slice initialization.

In the example below, an array is assigned values by named values rather than using any constructor. All unnamed values retain its `type`'s default value.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
    value3 : Float
}

// when a multiple named value initializations are done, each set of named
// values is enclosed in a multi-value operator `[{` `}]` where each named
// value is separated with a comma (`,`) and each named value is prefixed with
// a `.`, even if only a single value is being initialized by name
myType2 : MyType[3] = [
    [{ .value1 = 1, .value2 = "planes", .value3 = 3.14159 }],
    [{ .value3 = 5.0, .value2 = "trains" }],
    [{ .value2 = "automobiles" }]
]

// ERROR: The type does not support a single value constructors and thus a
// simple argument list of values is ambiguous to which value should be
// default initialized (although defining `value1`'s `Integer` as being
// `own` could be used to resolve ambiguity in this particular context)
myType1 : MyType[3] = [ 1, 2, 3 ]
````


### Dynamic array allocation

Arrays can be dynamically allocated and discarded as needed. Arrays cannot be resized but they can be replaced. Dynamically allocated arrays are pointers and default to a `unique` pointer type.

Single dimensional:

````zax
// declare the length of an array
length := 55

// allocate the array of that length dynamically
myArray : Integer[length] @

// reset the array to its default state (pointing to nothing)
myArray = #

// ERROR: dynamically sized arrays require dynamic allocation
anotherArray : Integer[length]
````

Multi-dimensional:

````zax
// declare the length of an array dimensions
lengthInner := 55
lengthOuter := 2

// allocate the array of the dimension lengths dynamically
myArray : Integer[lengthOuter][lengthInner] @

// reset the array to its default state (pointing to nothing)
myArray = #

// ERROR: dynamically sized arrays require dynamic allocation
anotherArray : Integer[lengthOuter][lengthInner]
````


### Dynamic array copy

````zax
// declare the length of an array
size := 55

// allocate the array of the length dynamically
myArray : Integer[size] @

// construct an array of a dynamic length and initialize the contents of the
// array
myArrayCopy : Integer[myArray.length] @ = myArray

// shorten to the allocation to the following (where its `type` and length are
// implied)
myOtherArrayCopy : @ = myArray
````


### Slicing arrays

Arrays include a implicit `constant` and `final` function named `slice` that extracts a subset of elements out of an array and returns a newly created array slice view. If `slice` is passed values that exceed an array's available indexed range, an array slice view of reduced length or even a completely empty array slice view is returned. The `length` `mutator` should be checked by a programmer should a programmer wish to ensure all values in a requested range will be satisfied by a `slice` operation in advance.

````zax
myArray : Integer[1000]

// slice from the start of the array and capture 500 elements
value1 := myArray.slice(#, 500)

// slice from the `myArray[5]` element to the end of the array
value2 := myArray.slice(5, #)

// slice from the `myArray[5]` element and extract 50 elements
value3 := myArray.slice(5, 50)
````


### Arrays and data overhead

Arrays not only contain contents, they contain sizing and other attributes. The sizing of each dimension is kept as part of an array's data overhead separate from the raw data matrix. This allows the physical memory layout for data to be placed in memory as a continuous block of types for all array dimensions. Parallel algorithms can then be adapted to use raw data in SIMD or MIMD instructions.

A raw control block for an array can be obtained with an `overhead as` operator.


### Pointer or references to arrays

A pointer or reference can be taken of an array similar to a pointer to any other `type`.

````zax
func1 final : ()(arrayPointer : Integer[][] *) = {
    arrayPointer.[2][1] = -10
}

func2 final : ()(arrayRef : Integer[][] &) = {
    arrayRef[3][0] *= -5
}

myArray : Integer[5][2] = [
    [ 1, -1 ],
    [ 2, -2 ],
    [ 3, -3 ],
    [ 4, -4 ],
    [ 5, -5]
]

func1(myArray)
func2(myArray)
````
