
# [Zax Programming Language](index.md)

## Pointers and References

### Basics

#### Pointer basics

Pointers do not contain data directly. Instead pointers contain an address in memory where real data is located. Pointers are defaulted to point to nothing and can be reset to point to nothing. Pointers can also point to garbage data or invalid memory if a value being pointed to has been previously discarded.

````zax
A :: type {
    myInteger : Integer
}

func final : ()() = {
    // `value` is a type of `A`
    value : A
    value.myInteger = 42

    // `pointer` is pointer to a type of `A` which points to `value`
    // (taking the address of `value` is not required as it is implied)
    pointer : A * = value

    // the value of `myInteger` in `value` is now `40` instead of `42`
    pointer.myInteger -= 2
    
    value2 : A
    value2.myInteger = 101

    // reassign `pointer` to point to `value2`
    pointer = value2

    // the value of `myInteger` in value is now `103` instead of `101`
    // since pointer is pointing to `value2` now
    pointer.myInteger += 2

    // pointer is reset to the default value of it's own type
    // (which effectively points to nothing)
    pointer = #

    // RUNTIME PANIC: a runtime panic would occur since `pointer` is no
    // longer pointing to a valid instance of type `A`
    pointer.myInteger *= 10
}
````


#### Reference basics

Pointers and references are almost interchangeable but a few key differences exist between their usages. Pointers can point to nothing but references must always point to valid data by convention (although a compiler will not enforce that data is still valid). Pointers can be changed to point to another data location but references are stuck pointing to their original data for the lifetime of a reference.

References are typically used where a value needs to be guaranteed to exist by convention and a value should not be copied when passed as an argument to a function.

````zax
A :: type {
    myInteger : Integer
}

// as `a` is passed by reference, a copy of `a` is not created and `a`'s
// reference points to the original `A` type instance passed in
funcUsingAReference final : ()(a : A &) = {
    // no need to check if `a` points to nothing, as `a` always must point
    // to a valid instance of type `A` by convention
    a.myInteger *= 2
}

funcUsingAPointer final : ()(a : A *) = {
    // functions that take an `a` pointer may be sent a pointer to nothing
    // and depending on the assumptions of a function, a pointer check
    // against nothing might be desired or required
    if !a
        return

    // just like a reference, pointers can manipulate the original value
    // passed into a function
    a.myInteger *= 2
}

func final : ()() = {
    // `value` is a type of `A`
    value : A
    value.myInteger = 42

    // reference is a pointer to type `A` which must always contain a
    // valid pointer to a type of `A` 
    reference : A & = value

    // value of `myInteger` in `value` is now `80` instead of `40`
    reference.myInteger *= 2

    // value of `myInteger` in `value` will become `160` instead of `80`
    funcUsingAReference(value)

    // reference is a pointer to type `A` which must always contain a
    // valid pointer to a type of `A` 
    pointer : A * = value

    // value of `myInteger` in `value` will become `320` instead of `160`
    funcUsingAPointer(pointer)

    // the pointer is reset to point to nothing
    pointer = #

    // value of `myInteger` will not change as the pointer was already reset
    funcUsingAPointer(pointer)

    // Resetting a reference actually does not affect the reference at all but
    // the value pointed to by the reference is changed. The value of
    // `myInteger` will be copy constructed using an empty temporarily
    // constructed instance of type `A` and `myInteger` will become 0.
    reference = #
}
````


#### Pointers and references to simple intrinsic types

Pointers and references do not have to point to custom declared types. They can point to any value including built in intrinsic values.

````zax
// as `myInteger` is passed by reference, a copy of `myInteger` is not created
// as `myInteger`'s reference points to the original `Integer` type passed in
funcUsingReference final : ()(myInteger : Integer &) = {
    // no need to check if `myInteger` points to nothing, it always has to point
    // to a valid value of type `Integer` by convention
    myInteger *= 2
}

funcUsingPointer final : ()(myInteger : Integer *) = {
    // functions that take a `myInteger` pointer may be sent a
    // pointer to nothing and depending on the assumptions of the function
    // a pointer check of nothing might be desired or required
    if !myInteger
        return

    // just like the reference, pointers can manipulate the original
    // value passed into the function; also notice the pointer must include
    // a final `.` operator to dereference the pointer's value
    myInteger. *= 2
}

func final : ()() = {
    // `value` is a type of `A`
    value : Integer

    // reference is a pointer to an instance of type `Integer` which must always
    // contain a valid pointer to a type of `Integer` 
    reference : Integer & = value

    // value of `value` is now `80` instead of `40`
    reference *= 2

    // value of `value` will become `160` instead of `80`
    funcUsingReference(value)

    // value of `value` will become `320` instead of `160`
    funcUsingPointer(value)

    // Resetting a reference actually does not affect the reference at all but
    // the value pointed to by the reference is changed. The value of
    // `value` will be copy constructed using an empty temporarily
    // constructed instance of type `Integer` and `value` will become 0.
    reference = #
}
````


### Pointer property overhead

Pointers can have additional properties associated with them and may contain additional overhead data more than a `raw` pointer typically would contain. For example, additional keywords such as `own` when applied to a pointer will indicate additional functionality is associated with a pointer. See [Memory Allocation](memory-allocation.html) for additional keywords that can add functionality to pointers.

In the example below, pointers aren't just `raw` pointers but can contain ownership properties (which allows the pointer type to deallocate the memory associated with the pointer automatically when a variable owning a pointer to a value is discarded). A programmer must acknowledge any property overhead they wish to include on their pointers as desired/needed. 

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
    a : A           // `a` of type `A` is contained within type `C`
    b1 : B * own @  // `b1` of type `B` is initialized after default `own`
                    // allocation
    b2 : B *        // `b2` is an `raw` pointer to a `B` type but the pointer is
                    // defaulted to point to nothing (and no type is allocated)
    b3 : B * own    // `b3` is an `own` pointer to a `B` type but the pointer is
                    // defaulted to point to nothing (and no type is allocated)
    value1 := 0
    value2 := 0
}

func final : ()() = {
    c : C                       // initialize a type of `C` into a variable
                                // named `c`

    c.b1.value1 = 5             // set the value of `c.b1.value1` to `5`
    c.b1.value2 = b1.value1     // copy the value of `c.b1.value1` to
                                // `c.b2.value2` (both `c.b1.value1` and
                                // `c.b2.value2` are now `5`)

    // make `c.b2` point to `c.b1`
    c.b2 = c.b1
    c.b2.value3 = 6             // set the value of `c.b1.value6` to `6`

    // make `c.b3` point and take over ownership of  `c.b1` and `c.b1` points
    // to nothing
    c.b3 = c.b1

    // `c.b2` and `c.b3` both point to the same underlying type's contents thus
    // `c.value1` will contain the value `16` despite the values originating
    // from `c.b2` or `c.b3`
    c.value1 = c.b2.value1 + c.b3.value2 + c.b2.value3

    // attempting to access `c.b1` should cause a panic since `c.b1` is now
    // pointing to nothing (as ownership was transferred to `c.b3`)
    c.b1.value1 = c.value1
}
````

### Pointer casting

Pointers can be casted by way of three casting methods:
* raw conversion of a pointer type using a `unsafe as` operator
* compatible [composition](composition.md) conversion of a pointer type using an `unsafe outer of` operator
* runtime conversation of compatible [composition](composition.md) pointer types using an `outer of` operator

An `as` operator can only be used to convert a pointer to/from an intrinsic numeric type of equal or greater capacity than an original pointer.


#### Pointer raw conversion

Pointers can use a `unsafe as` operator to treat a pointer of one type as a pointer of a different type. The language will perform no pointers validation for compatibility and it will allow any conversion from one pointer type to another type to occur. Programmers are expected to understand if a pointer type is indeed compatible and they must take responsibility of creating undefined behavior. Pointers of any type can be converted to any other type using the `unsafe as` operator regardless of how ridiculously `unsafe` this cast operation might be.

````zax
print final : ()(...) = {
    // ...
}

func : ()(pointer : U32 *) = {
    // treat the U32 pointer as a U16 pointer
    u16Pointer := pointer as U16 *

    // access the pointer as if it were a 16 bit unsigned integer pointer...
    print(u16Pointer.)

    // POSSIBLE UNDETERMINED BEHAVIORS:
    // while converting from a `U32 *` to a `U16 *` might appear safe, the
    // resulting data might end up being the high order bytes on a big endian
    // system and the low order bytes on a little endian system

    // if the CPU is a strange architecture, a crash might occur if the
    // memory pointer is on a misaligned boundary for the type after the
    // straight conversion between types (even if this is unlikely on most
    // modern systems)
}

value : U32 = h'ABCDEF12'

func(value)
````


#### Function pointer casting

Functions can be cast to and from a Unknown types using the `unsafe copy as` operator. The `unsafe copy as` operator is necessary to create a copy of captured values otherwise captured values referenced in a function might become lost.

````zax
print final : ()(...) = {
    // ...
}

simpleFunc final : ()(value : Integer)

func1 final : simpleFunc = {
    print(value)
}

func2 final : simpleFunc = {
    if value > 3
        print(value, "> 3")
}

// declare an Unknown type (which can hold function pointers with captured data)
pointer1 : Unknown
pointer2 : Unknown

// cast into an `Unknown` type (a copy of the captured variable is created)
pointer1 = func1 unsafe copy as Unknown
pointer2 = func2 unsafe copy as Unknown

// cast back from an `Unknown` using the type of the function definition
func3 := pointer1 unsafe copy as :simpleFunc
func4 := pointer2 unsafe copy as :func2

func3() // will execute the code defined in func1
func4() // will execute the code defined in func2
````


##### Function pointer casting with capture

Functions can be cast to a raw pointers using the `unsafe as` operator and from a raw pointer using the `unsafe copy as` operator. The `unsafe copy as` operator is necessary to acknowledge the overhead involved with the copying of captured values. When converting from a raw pointer, the function is casted and treated as the expected function type and the captured variables are copied as part of the operation. Using the `unsafe as` operator or `as` operator is disallowed when converting from raw pointers to functions that can capture values.

````zax
print final : ()(...) = {
    // ...
}

simpleFunc final : ()(value : Integer)

message := "Is this good?"

func1 : simpleFunc = [message] {
    print(message, value)
}

message = "Are you happy with this value?"

func2 : simpleFunc = [message] {
    if value > 3
        print(message, value, "> 3")
}

// declare a raw Unknown pointer
pointer1 : Unknown *
pointer2 : Unknown *

// cast the func1
pointer1 = func1 unsafe as Unknown *
pointer2 = func2 unsafe as Unknown *

// cast back from a raw `Unknown *` using the type of the function definition
// (even though captured values were not copied into the Unknown * the
// `Unknown *` is treated as the original function type so a copy of captured
// values is created)
func3 := pointer1 unsafe copy as :simpleFunc
func4 := pointer2 unsafe copy as :func2

// reset the original `func1` and `func2`
func1 = #
func2 = #

func3() // will execute the code defined in func1 and still displays
        // the message "Is this good?"
func4() // will execute the code defined in func2 and still displays
        // the message "Are you happy with this value?"
````


##### Function pointer casting without capture ability

Pointers to functions without the ability to capture can be casted from a raw pointer using the `unsafe as` operator and not the `unsafe copy as` operator since the functions have no ability to capture values and thus do not need the programmer to acknowledge any additional value copying overhead in the pointer conversion.

````zax
print final : ()(...) = {
    // ...
}

// the function is defined as pointer to a function and cannot capture values
simpleFunc final : ()(value : Integer) *

// not legal to capture any values
func1 final : simpleFunc = {
    print(value)
}

// not legal to capture any values
func2 final : simpleFunc = {
    if value > 3
        print(value, "> 3")
}

// declare a raw Unknown pointer
pointer1 : Unknown *
pointer2 : Unknown *

// cast the func1
pointer1 = func1 unsafe as Unknown *
pointer2 = func2 unsafe as Unknown *

// cast back from a raw pointer using the type of the variables definition
func3 := pointer1 unsafe as simpleFunc
func4 := pointer2 unsafe as func2

// reset the original `func1` and `func2`
func1 = #
func2 = #

func3() // will execute the code defined in func1
func4() // will execute the code defined in func2
````


### Using the `last` type qualifier to optimize content transfer

Normally references and pointers `lease` their ownership to other functions for a period of time. The true owner of a reference or pointer is not relevant but an owner must keep the lifetime of an underlying type alive while a reference or pointer is in use. A `lease` reference and pointer qualifier indicates that a reference is leasing it's lifetime from somewhere else. Be default, all references and pointers are pre-qualified with `lease` and thus this qualifier is redundant and need not to be specified.

Alternatively, references and pointers can have a `last` qualifier. A `last` qualifier on a reference or pointer indicates a receiver of this type will inherit all contents of a type as this reference or pointer is the very `last` owner to an underlying type's contents. Contents contained in a `type` will be discarded if not claimed. When a `last` qualifier is specified on a `type`'s instance, ownership of any contents can be claimed by a receiving function. This helps optimization by transferring contents out of one `type`'s instance into another prior to an instance's disposal rather than making content copies. Later when an instance is disposed, any claimed contents will have already been transferred out of a reference or pointer. This saves contents from having to be cloned first only to have any original contents disposed moments later. 

Types qualified as `last` cannot be `constant` or `immutable` types as these types cannot have their contents transferred out due to internal their contents being effectively `immutable`. A `lease` option is required (and defaulted) for any `constant` and `immutable` references or pointers and a `last` qualifier is incompatible.

Types passed by-value do not require a `last` qualifier as arguments passed by-value can always have their contents transferred out already. Passing by-value always causes a fresh copy of a `type`'s instance's contents rather than providing any `lease` reference or pointer to an existing type's contents (except `strong` or `handle` pointers which are designed implicitly to have shared ownership of a common instance).

An `as` operator can be used to change a `last` or `lease` qualifier on a type. The `last` and `lease` qualifiers are mutually exclusive and a `last` cannot be applied to a `type`'s value that is currently qualified as `constant` or `immutable`.


#### Temporary variables

When a compiler detects that a type's instance is about to be disposed, and an instance does not need to be leased, a compiler can automatically apply a `last` qualifier rather than defaulting to a `lease` qualifier. This optimization is applied to temporary `type` instances where a value never captured into a named variable. Temporary variables passed by-value to a function may be converted automatically to a `last` reference, or a programmer can manually convert an instance into a `last` pointer or reference.

````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    SubType :: type {
        animal : String
    }

    value : Integer
    name : String

    subvalue : SubType * own
}

func final : (result : MyType)() = {
    result.value2 = "my string"

    return result
}

augmentFunc final : (result : MyType)(input : MyType constant &) = {
    result.value = input.value
    result.name = "Big " + input.name

    // since `input` is not the `last` instance, `input`'s contents cannot be
    // lifted and transferred into the return value and thus a new copy
    // of the value must be made
    result.subvalue = #: @ // allocate a new instance of the subtype
    result.subvalue.animal = input.subvalue.animal

    return result
}

augmentFunc final : (result : MyType)(input : MyType & last) = {
    result.value = input.value
    result.name = "Happy " + input.name

    // ownership is transferred out of input because the original input
    // value will be disposed anyway and thus does not require a copy be made
    result.subvalue = input.subvalue

    return result
}

myTemporary1 : MyType

myTemporary1.name = "Fred"
myTemporary1.value = 5
myTemporary1.subvalue = # : @
myTemporary1.subvalue.animal = "Frog"

print("Hello there!")

// explicitly indicate `myTemporary1` is no longer needed and will be disposed
// so the contents of this variable can be lifted/consumed
myTemporary2 := augmentFunc(myTemporary1 as last)

print(myTemporary2.name) // prints "Happy Fred"

// since `myTemporary2` is no longer referenced, and no reference to the
// variable could have been made, the `last` property will be automatically
// applied to the type since there is a `last` version to the invoked
// `augmentFunc` function's argument
myTemporary3 := augmentFunc(myTemporary2)

print(myTemporary3.name) // prints "Happy Happy Fred"

// `myTemporary3` is used as a reference in another function later thus
// the `last` attribute will not be automatically applied
myTemporary4 := augmentFunc(myTemporary3)

// `myTemporary3` is used as a reference in another function earlier thus
// a dangling reference may have been made thus the variable
// cannot be qualified as `last`
myTemporary5 := augmentFunc(myTemporary3)

print(myTemporary4.name) // prints "Big Happy Happy Fred"
print(myTemporary5.name) // prints "Big Happy Happy Fred"


myTemporary6 : MyType

myTemporary6.name = "Sally"
myTemporary6.value = 7
myTemporary6.subvalue = # : @
myTemporary6.subvalue.animal = "Hippo"

// `myTemporary6` passed into `augmentFunc` is not used again and thus will be
// considered the `last` instance of the variable before disposal; which returns
// a new temporary `MyType` which is always treated as the `last` instance of
// `MyType` since no named variable captured the returned result
myTemporary7 := augmentFunc(augmentFunc(myTemporary6))

print(myTemporary7.name) // prints "Happy Happy Sally"
````


#### `lease` a `last` reference or pointer

A function that receives a `last` reference may wish to call other functions that will operate on the `last` instance prior to disposal. Caution must be used or subsequent calls to other functions may also assume a `last` qualifier and transfer contents out of the instance.

A `lease` or `last` qualifier should be reapplied when sending the reference for use into other functions. The compiler will issue a `lease-or-last` warning where ambiguity exists. A compiler will assume `lease` qualification where ambiguity exists.

````zax
MyType ::type {
    SubType :: type {
        animal : String
    }

    value : Integer
    name : String

    subvalue : SubType * own @
}

display final : ()(value : MyType &) = {
    // ...
}

display final : ()(value : MyType & last) = {
    // ...
}

doSomething final : ()(value : MyType & last) = {
    // ...
    // WARNING: `lease-or-last` is issued as the intention to call
    // the `lease` or `last` version of the `display` function is unclear
    display(value)

    // no warning is issued as the intention to `lease` the type is clear
    display(value as lease)

    // no warning is issued as the intention to make the instance the `last`
    // owner is clear
    display(value as last)

    //...
}

myType : MyType

// ...

doSomething(myType as last)

//...
````


#### `last` versus explicit `lease` qualification

By default all functions that receive references or pointers have `lease` qualification applied unless a `last` qualification is explicitly applied. The `lease` and `last` qualifiers are mutually exclusive. A `lease` qualifier is redundant as it is automatically applied by default. One key difference does exist between explicitly and implicit qualifying a reference or pointer argument as `lease`. Arguments that are explicitly qualified as `lease` cannot ever receive a `last` type and any attempt to pass a `last` type into an explicitly `lease` qualified type will cause an `explicit-last-cannot-receive-least` error.

````zax
MyType ::type {
    SubType :: type {
        animal : String
    }

    value : Integer
    name : String

    subvalue : SubType * own @
}

display final : ()(value : MyType & last) = {
    // ...
}

doSomething final : ()(value : MyType & last) = {
    // ...

    // ERROR: `explicit-last-cannot-receive-lease` error as an explicit
    // `lease` qualifier is not allowed to receive a `lease` qualified type
    display(value as lease)

    //...
}

myType : MyType

// ...

doSomething(myType as last)

//...
````
