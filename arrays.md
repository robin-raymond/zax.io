
# [Zax Programming Language](index.md)

## Arrays

Zax arrays are multi dimensional. Arrays can be of a fixed size or they can be a dynamic size. All array dimensions have an implicit `length` mutator to indicate their current size. Arrays are zero based. Once arrays are allocated they cannot be resized but their contents can be replaced.

````zax
randomNumber final : (result : Integer)() = {
    // ...
}

// all types are automatically initialized to the default Integer value of 0
myArray : Integer[1000]

each arrayValue : from myArray {
    arrayValue =  randomNumber()
}

// create a new array of the same size and copy the contents from the original
// array; as the array is fixed size dynamic allocation is not needed
anotherArray : Integer[] = myArray

// copy only the first 10 array elements into a new array
yetAnotherArray1 : Integer[10] = myArray

// copy the first 1000 array elements into the new array
// (all other elements will be blank)
yesAnotherArray2 : Integer[5000] = myArray
````


### Simple array initialization

Arrays can be default initialized with a set of values.


Single dimensional arrays:

````zax
myArray : Integer[5] = {{ "{{" }} 1, 2, 3, 4, 5 {{ }}}}
````

Multi dimensional arrays:

````zax
myArray : Integer[5][2] = {{ "{{" }}
    { 1, -1 },
    { 2, -2 },
    { 3, -3 },
    { 4, -4 },
    { 5, -5}
{{ }}}}
````

Simple array where type and size is assumed:

````zax
myArray := {{ "{{" }} 1, 2, 3, 4, 5 {{ }}}}
````


### Array initialization with constructors

In the example below, a type that has two different constructors options (i.e. polymorphic), the array can be initialized using either constructor and the standard polymorphic algorithms will apply to choose which constructor was intended.

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
// argument is separated with a comma (`,`) and contains a single value
// per array element
myType1 : MyType[3] = {{ "{{" }} 1, 2, 3 {{ }}}}

// when a multiple argument constructor is used, each constructor argument list is
// enclosed in a curly bracket `{}` where argument is separated with a
// comma (`,`) and each curly brace enclosure contains all the constructor
// arguments for the element
myType2 : MyType[3] = {{ "{{" }}
    { 1, "planes" },
    { 2, "trains" },
    { 3, "automobiles" }
{{ }}}}
````


### Type named value initialization or arrays

Named initialization of types is supported in array format. The same methodology is used as would be the case if a single named initialization were done. An array imposes an additional requirement where all the initialized named values are each contained within curly braces `{}` per element and all the collective elements are contained within another set of curly braces.

Named initializers cannot be mixed and matched with constructors. Either named initializers are used or constructors are used but not both at the same time for the same array.

In the example below, an array is assigned values by the value name rather than using any constructor. All unnamed values retain their type's default value.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
    value3 : Float
}

// when a multiple named value initializations are done, each set of named
// values is enclosed in a curly bracket `{}` where each named value is
// separated with a comma (`,`) and each curly brace enclosure contains
// all the named initialized values per element
myType2 : MyType[3] = {{ "{{" }}
    { .value1 = 1, .value2 = "planes", .value3 = 3.14159 },
    { .value3 = 5.0, .value2 = "trains" },
    { .value2 = "automobiles" }
{{ }}}}

// ERROR: The type does not support a single value as a default and thus a
// simple argument list of values is ambiguous to which value should be
// default initialized (although defining `value1`'s Integer type as being
// `own` could resolve this ambiguity)
myType1 : MyType[3] = {{ "{{" }} 1, 2, 3 {{ }}}}
````


### Dynamic array allocation

Arrays can be dynamically allocated and discarded as needed. Arrays cannot be resized but they can be replaced.


Single dimensional:

````zax
// declare the size of an array
size := 55

// allocate the array of the size dynamically
myArray : Integer[size] @

// reset the array to its default state (empty)
myArray =:

// ERROR:
// dynamically sized arrays require dynamic allocation
anotherArray : Integer[size]
````

Multi-dimensional:

````zax
// declare the size of an array
sizeInner := 55
sizeOuter := 2

// allocate the array of the size dynamically
myArray : Integer[sizeOuter][sizeInner] @

// reset the array to its default state (empty)
myArray =:

// ERROR:
// dynamically sized arrays require dynamic allocation
anotherArray : Integer[sizeOuter][sizeInner]
````


### Dynamic array copy

````zax
// declare the size of an array
size := 55

// allocate the array of the size dynamically
myArray : Integer[size] @

// construct an array of a dynamic size and copy the contents of the array
myArrayCopy : Integer[myArray.length] @ = myArray

// shorten to the following (the type is implied and allocated)
myOtherArrayCopy : @ = myArray
````


### Array of types

Arrays can support arbitrary types and need not be limited to simple intrinsic types. 

````zax
MyType :: type {
    value1 : String
    value2 : Integer
}

// create an array of `MyType` types
myArray : MyType[1000]
````


### SoA vs AoS

By default, Zax creates all arrays as an [AoS (Array of Structures)](https://en.wikipedia.org/wiki/AoS_and_SoA#Array_of_Structures). Each full type is layed out sequentially in the array in memory. Zax does support creating alternative representations where the array is reformed into a [SoA (Structure of Arrays)](https://en.wikipedia.org/wiki/AoS_and_SoA#Structure_of_Arrays). In the SoA mode, each of the type's contained variables becomes it's own individual array layed out array-by-array of variables sequentially in memory.

````zax
MyType :: type {
    value1 : Float
    value2 : Integer
    value3 : Float
}

func : ()(value : MyType*) = {
    // ...
}

// construct an array of structures implicitly as aos
myArray1 : MyType[100]

myArray1[0].value2 = 10
myArray1[1].value1 = 23.0

func(myArray1[0])
func(myArray1[1])


// same as above, except declared explicitly as aos
myArray2 : MyType[100] aos

myArray2[22].value1 = 10.0
myArray2[99].value2 = 23

func(myArray2[22])
func(myArray2[99])


// arrange the memory of the array an SoA manner
myArray3 : MyType[100] soa

myArray3[11].value1 = 91.77
myArray3[23].value2 = 11

// ERROR: Cannot convert an soa array entry back to its original type as the
// contents of type have been split apart in memory and can't be put together 
func(myArray[11])

// This will succeed as the source type and the destination type are
// both declared as `soa`
myArray4 : MyType[] soa @ = myArray3

// ERROR: Cannot copy from an AoS type into an SoA type (or vice versa)
myArray5 : MyType[] @ = myArray3
````


### Splicing arrays

Arrays include a implicit `constant` and `final` function named `splice` that extracts a subset of elements out of the array and return a newly created mutable array. If splice is passed values that exceed the ranges, a reduced size or even completely empty element array is returned. The `length` `mutator` should be checked by the developer if they wish to ensure the values in their range will be satisfied by the splice function. Splicing is supported on both `soa` and `aos` arrays.

````zax
myArray : Integer[1000]

// splice from the start of the array and capture 500 elements
value1 := myArray.splice(, 500)

// splice from the `myArray[5]` element to the end of the array
value2 := myArray.splice(5,)

// splice from the `myArray[5]` element and extract 50 elements
value3 := myArray.splice(5, 50)
````


### Arrays and data overhead

Arrays not only contain contents, they contain sizing and other attributes. The sizing of each dimension is kept as part of the array data separate from the data matrix. This allows the physical memory layout to be placed in memory as a continuous block of types for all array dimensions. Parallel algorithms can adapted to use the data as SIMD or MIMD input. Another advantage to this layout is the ability to use `soa` or `aos` with relatively the same memory layout being used (aside from potential alignment concerns).

A raw control block for the array can be obtained with the `overhead` operator.


### Pointer or references to arrays

A pointer or reference can be taken of an array similar to a pointer to any other type.

````zax
func1 final : ()(arrayPointer : Integer[][]*) = {
    arrayPointer.[2][1] = -10
}
func2 final : ()(arrayRef : Integer[][]&) = {
    arrayPointer[3][0] *= -5
}

myArray : Integer[5][2] = {{ "{{" }}
    { 1, -1 },
    { 2, -2 },
    { 3, -3 },
    { 4, -4 },
    { 5, -5}
{{ }}}}

func1(myArray)
func2(myArray)
````
