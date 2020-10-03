
# [Zax Programming Language](index.md)

## Pointers and References

### Basics

#### Pointer basics

Pointers do not contain data directly. Instead pointers contain an address in memory where the real data is located. Pointers are defaulted to point to nothing and can be reset to point to nothing. Pointers can also point to garbage data or invalid memory if the value being pointed to is discarded.

````zax
A :: type {
    myInteger : Integer
}

func final : ()() = {
    // `value` is a type of `A`
    value : A
    value.myInteger = 42

    // `pointer` is pointer to a type of `A` which points to `value`
    // (taking the address of `value` is not required)
    pointer : A * = value

    // the value of `myInteger` in `value` is now `40` instead of `42`
    pointer.myInteger. -= 2
    
    value2 : A
    value2.myInteger = 101

    // reassign `pointer` to point 
    pointer = value2

    // the value of `myInteger` in value is now `103` instead of `101`
    // since pointer is pointing to `value2` now
    pointer.myInteger += 2

    // pointer is reset to the default value of it's own type
    // (which effectively points to nothing)
    pointer = #:

    // RUNTIME PANIC: a runtime panic would occur since `pointer` is no
    // longer pointing to a valid instance of type `A`
    pointer.myInteger *= 10
}
````


#### Reference basics

Pointers and references are almost interchangeable but a few key differences exist between their types. Pointers can point to nothing but references must always point to valid data by convention (although the compiler will not enforce the data is still valid). Pointers can be changed to point to another data location but references are stuck pointing to their original data for the lifetime of the reference.

References are typically used where the value needs to be guaranteed to exist by convention and but a value should not be copied when passed as an argument to a function.

````zax
A :: type {
    myInteger : Integer
}

// as `a` is passed by reference, a copy of `a` is not created as `a`
// references (i.e. points to) the original `A` type passed in
funcUsingAReference final : ()(a : A &) = {
    // no need to check if `a` points to nothing, it always has to point
    // to a valid value of type `A` by convention
    a.myInteger *= 2
}

funcUsingAPointer final : ()(a : A *) = {
    // functions that take an `a` pointer may be sent a pointer to nothing
    // and depending on the assumptions of the function a pointer check
    // of nothing might be desired/required
    if !a
        return

    // just like the reference, pointers can manipulate the original
    // value passed into the function
    a.myInteger *= 2
}

func final : ()() = {
    // `value` is a type of `A`
    value : A
    value.myInteger = 42

    // reference is a pointer to type `A` which must always contain a
    // valid pointer to a type of `A` 
    reference : A & = value

    // the value of `myInteger` in `value` is now `80` instead of `40`
    reference.myInteger *= 2

    // the value of `myInteger` in `value` will become `160` instead of `80`
    funcUsingAReference(value)

    // the value of `myInteger` in `value` will become `320` instead of `160`
    funcUsingAPointer(value)

    // resetting a reference actually resets the value in the reference thus
    // the value of `myInteger` in `value` will become 0
    reference = #:
}
````


#### Pointers and references to simple intrinsic types

Pointers and references do not have to point to custom declared types. They can point to any value including built in intrinsic values.

````zax
// as `myInteger` is passed by reference, a copy of `myInteger` is not created
// as `myInteger` references (i.e. points to) the original `Integer` type
// passed in
funcUsingReference final : ()(myInteger : Integer &) = {
    // no need to check if `myInteger` points to nothing, it always has to point
    // to a valid value of type `Integer` by convention
    myInteger *= 2
}

funcUsingPointer final : ()(myInteger : Integer *) = {
    // functions that take a `myInteger` pointer may be sent a
    // pointer to nothing and depending on the assumptions of the function
    // a pointer check of nothing might be desired/required
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

    // reference is a pointer to type `Integer` which must always contain a
    // valid pointer to a type of `Integer` 
    reference : Integer & = value

    // the value of `value` is now `80` instead of `40`
    reference *= 2

    // the value of `value` will become `160` instead of `80`
    funcUsingReference(value)

    // the value of `value` will become `320` instead of `160`
    funcUsingPointer(value)

    // resetting a reference actually resets the value in the reference thus
    // the value of `value` will become 0
    reference = #:
}
````


### Pointer property overhead

Pointers can have additional properties associated with them and may contain additional overhead data more than a raw pointer would typically. Additional keywords such as `own` when applied to a pointer will indicate additional functionality is associated with the pointer. See [Memory Allocation](memory-allocation.html) for additional keywords that can add functionality to pointers.

In the example below, pointers aren't just raw pointers but can contain ownership properties (which allows the pointer type to deallocate the memory associated with the pointer automatically when a variable owning a pointer to a value is discarded). The programmer acknowledges the the property overhead they wish to include on their pointers as desired/needed. 

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
    b1 : B * own @  // `b1` of type `B` is initialized after default
                    // `own` allocation
    b2 : B *        // `b2` is an raw pointer to a `B` type but the pointer is
                    // defaulted to point to nothing
                    // (and no type is allocated)
    b3 : B * own    // `b3` is an `own` pointer to a `B` type but the pointer is
                    // defaulted to point to nothing
                    // (and no type is allocated)
    value1 := 0
    value2 := 0
}

func final : ()() = {
    c : C                       // initialize a type of `C`
                                // into a variable named `c`

    c.b1.value1 = 5             // set the value of `c.b1.value1` to `5`
    c.b1.value2 = b1.value1     // copy the value of `c.b1.value1` to
                                // `c.b2.value2` (both `c.b1.value1` and
                                // `c.b2.value2` are now `5`)

    // make `c.b2` point to `c.b1`
    c.b2 = c.b1
    c.b2.value3 = 6             // set the value of `c.b1.value6` to `6`

    // make `c.b3` point and take over ownership of
    // `c.b1` and `c.b1` points to nothing
    c.b3 = c.b1

    // `c.b2` and `c.b3` both point to the same underlying type's contents
    // thus `c.value1` will contain the value `16` despite the values
    // originating from `c.b2` or `c.b3`
    c.value1 = c.b2.value1 + c.b3.value2 + c.b2.value3

    // attempting to access `c.b1` should cause a panic since `c.b1` is now
    // pointing to nothing (as ownership was transferred to `c.b3`)
    c.b1.value1 = c.value1
}
````

### Pointer casting

Pointers can be casted by way of three casting methods:
* raw conversion of the pointer type using the `cast` operator
* compatible [composition](composition.md) conversion of the pointer type using the `outercast` operator
* runtime conversation of compatible [composition](composition.md) pointer types using the `outerof` operator

The `as` operator can only be used to convert a pointer to/from an intrinsic number type of equal or greater capacity than the pointer.


#### Pointer raw conversion

Pointers can use the `cast` operator to treat a pointer of one type as a pointer of a different type. The language will perform no validation that the pointers are indeed compatible and will allow any conversion from one pointer type to another type to occur. Programmers are expected to understand if the pointer types are indeed compatible and they must take responsibility of the risk of creating undefined behavior. Pointers of any type can be converted to any other type using the `cast` operator regardless of how ridiculous and unsafe this cast operation might be.

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

Functions can be cast to a raw pointers using the `cast` operator and from a raw pointer using the `copycast` operator. The `copycast` operator is necessary to create a copy of captured values.

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

// declare a raw Unknown pointer
pointer1 : Unknown *
pointer2 : Unknown *

// cast the func1
pointer1 = func1 cast Unknown *
pointer2 = func2 cast Unknown *

// cast back from a raw pointer using the type of the variables definition
func3 = pointer1 copycast :simpleFunc
func4 = pointer2 copycast :func2

func3() // will execute the code defined in func1
func4() // will execute the code defined in func2
````


##### Function pointer casting with capture

Functions can be cast to a raw pointers using the `cast` operator and from a raw pointer using the `copycast` operator. The `copycast` operator is necessary to acknowledge the overhead involved with the copying of captured values. When converting from a raw pointer, the function is casted and treated as the expected function type and the captured variables are copied as part of the operation. Using the `cast` operator or `as` operator is disallowed when converting from raw pointers to functions that can capture values.

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
pointer1 = func1 cast Unknown *
pointer2 = func2 cast Unknown *

// cast back from a raw pointer using the type of the variables definition
func3 = pointer1 copycast :simpleFunc
func4 = pointer2 copycast :func2

// reset the original `func1` and `func2`
func1 = #:
func2 = #:

func3() // will execute the code defined in func1 and still displays
        // the message "Is this good?"
func4() // will execute the code defined in func2 and still displays
        // the message "Are you happy with this value?"
````


##### Function pointer casting without capture ability

Pointers to functions without the ability to capture can be casted from a raw pointer using the `cast` operator and not the `copycast` operator since the functions have no ability to capture values and thus do not need the programmer to acknowledge any additional value copying overhead in the pointer conversion. Using the `copycast` operator is disallowed when converting from raw pointers to pointers to function types.

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
pointer1 = func1 cast Unknown *
pointer2 = func2 cast Unknown *

// cast back from a raw pointer using the type of the variables definition
func3 = pointer1 cast :simpleFunc
func4 = pointer2 cast :func2

// reset the original `func1` and `func2`
func1 = #:
func2 = #:

func3() // will execute the code defined in func1
func4() // will execute the code defined in func2
````


### Using the `last` type qualifier to optimize content transfer

Normally references and pointers `lease` their ownership to other functions for a period of time. The true owner of the reference or pointer is not relevant but the owner much keep the lifetime of the underlying type alive while the reference or pointer is in use. The `lease` reference and pointer qualifier indicates that the reference is leasing it's lifetime from somewhere else. Be default, all references and pointers are pre-qualified with `lease` and thus this qualifier is redundant and need not to be specified.

Alternatively, references and pointers can have a qualifier of `last` appended to their type. The `last` qualifier on a reference or pointer indicates the receiver of this type will inherit the contents of the type as this reference or pointer is the very `last` owner to the underlying type's contents. The contents contained in the type will be discarded if not claimed. By knowing the `last` qualifier is specified on a type's instance, the ownership of the contents can be claimed by a receiving function. This helps optimization by transferring the contents out of one type's instance into another prior to an instance's disposal rather than making content copies. Later when the instance is disposed, the claimed contents will have already been transferred out of the referred or pointer. This saves contents from having to be cloned only to have the original contents disposed moments later. 

Types qualified as `last` cannot be `constant` or `immutable` types as these types cannot have their contents transferred out due to the the contents being effectively `immutable`. The `lease` option is required (and defaulted) for any `constant` and `immutable` references or pointers.

Types passed by-value do not require the `last` qualifier as value passed types can always have their contents transferred out already. Passing by-value always causes a fresh copy of the type's instance's contents rather than providing any `lease` reference or pointer to an existing type's contents (except `strong` or `handle` pointers which are designed specifically to have shared ownership to a common pointed to instance).

The `as` operator can be used to change the `last` or `lease` qualifier on a type. The `last` and `lease` qualifiers are mutually exclusive and a `last` cannot be applied to a type that is currently qualified as `constant` or `immutable`.


#### Temporary variables

When the compiler detects that a type's instance is about to be disposed, and the instance does not need to be leased, the compiler can automatically apply the `last` qualifier rather than defaulting to the `lease` qualifier. This optimization is applied to temporary type instane whose instance is never captured into a named variable. Temporary variables passed by-value to a function can be converted automatically, or the programmer can manually convert an instance into a `last` pointer or reference.

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
    result.subalue.animal = input.subvalue.animal

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

A function that receives a `last` reference may wish to call other functions that will operate on the `last` instance prior to disposal. Caution must be used or subsequent calls to other functions may also assume the `last` qualifier and transfer the contents out of the function.

The `lease` or `last` qualifier should be reapplied when sending the reference for use into other functions. The compiler will issue a `lease-or-last` warning where ambiguity exists. The compiler will assume `lease` qualification where the ambiguity exists.

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

By default all functions that receive references or pointers have `lease` qualification applied unless the `last` qualification is explicitly applied. The `lease` and `last` qualifiers are mutually exclusive. The `lease` qualifier is redundant as it is automatically applied by default. One key difference does exist between explicitly and implicit qualifying a reference or pointer argument as `lease`. Arguments that are explicitly qualified as `lease` cannot ever receive a `last` type and any attempt to pass a `last` type into an explicitly `lease` qualified type will cause an `explicit-lease-cannot-receive-last` error.

````zax
MyType ::type {
    SubType :: type {
        animal : String
    }

    value : Integer
    name : String

    subvalue : SubType * own @
}

display final : ()(value : MyType & lease) = {
    // ...
}

doSomething final : ()(value : MyType & last) = {
    // ...

    // ERROR: `explicit-lease-cannot-receive-last` error as the explicit
    // `lease` qualifier is not allowed to receive a `last` qualified type
    display(value)

    //...
}

myType : MyType

// ...

doSomething(myType as last)

//...
````
