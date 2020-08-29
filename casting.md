
# [Zax Programming Language](index.md)

## Casting

### Intrinsic system literal conversion

Zax does not perform type conversion, promotion or demotion of intrinsic compatible types. Casting operators `as` or `cast` must be used to convert from one intrinsic type to another type.

The `as` operator and the `cast` operator work in similar manners. Both convert an intrinsic value from one type to another. With intrinsic types, the `as` operator should cause a panic situation if the data would overflow if converted from a source type to a destination type. Whereas with intrinsic types, the `cast` operator will not panic ever when converting from one type to another but can cause either loss of information in an overflow, or data corruption / undefined behavior in another extreme. The `as` attempts to do a compatible conversion whereas a `cast` will treat the types as compatible even when they are not compatible.

````zax
i8 := -127 as I8                    // `as` can convert the value into an
                                    // I8 type without overflow
u8 := 255 as U8                     // `as` can convert the value into a
                                    // U8 type without overflow

u8Error := 256 as U8                // `as` will cause compile time
                                    // error as value is overflow
u8CastError := 256 cast U8          // `cast` will succeed despite the overflow
                                    // and the overflow is truncated

value : Integer = 256
u8Panic := value as U8              // `as` will cause runtime panic as
                                    // value is overflow
u8CastIgnorePanic := value cast U8  // `cast` will ignore overflow and the
                                    // overflow value is truncated

// Converting from a `WString`to a `String` is not always safe
// (but this case is safe)
string := w'hello' as String

// An unsafe conversion will cause a compilation error
stringError := w'this embedded literal "' h'16f' \
               w'" is not convertible' as WString

// converting from a `String` to a `WString` is always safe
string := 'always safe no matter what value'
wString := string as WString

// Converting from a `Utf8String` to a `WString` can cause runtime
// panic errors if the value contains an illegal sequence
utf8String := utf8'© Snowman Industries (☃)'

// This can overflow and cause panic if the utf8 contains
// illegal sequences (but won't in this case)
wString := utf8String as WString

// Any overflows will be ignored and will not cause panic for an
// illegal sequences
wString := utf8String cast WString


wideString := w'Runtime value with a non-ascii value "' h'16f' w'" is not ' \
              'legal to express in an ascii string.'

// This will cause a runtime panic since it cannot be converted
// (because the `as` keyword assumes all conversion is entirely legal)
stringIsRuntimePanic := wideString as String

// The overflow will be ignored during the conversion and the
// overflow value is truncated
StringOverflowIsIgnored := wideString cast String

// The wide string is convertible safely into a utf8 string as an no overflow
// is possible
stringIsRuntimeSafe := wideString as Utf8String
````


### Pointer casting using `cast`

Any type can be converted from one pointer type to another pointer type using the `cast` operator. The compiler will not perform any type checking on the types to check if they are compatible. If a pointer is cast to an incompatible type and accessed then the undefined behaviors can result.

A `void*` can be used to hold a generic pointer to anything.


### Casting a by-value type into a pointer

When a value is cast as a pointer, the address of the pointer is taken. However, in many cases manual casing to a pointer type is unnecessary. In Zax, a value type will automatically convert to a pointer `type` implicitly for the same `type` without any conversion being required. For greater explicitness, the `as` operator can convert from a value to a pointer to the same type safely without introducing undefined behaviors.

The `cast` operator will forcefully convert any value type into a pointer of any other type. This type of conversion is not recommended as it can lead to undefined behaviors.

Examples of pointer casting:

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

AnotherType :: type {
    value1 : Float
    value2 : WString
}

func : ()(input : MyType*) = {
    //...
}

myType : MyType
anotherType : AnotherType

myTypePointer1 := myType as MyType*     // allowed
myTypePointer2 := myType as *           // allowed - type is deduced
myTypePointer2 : MyType* = myType       // allowed - implicit casting

func(myType)                            // allowed - implicit casting


// ERROR: cannot convert from myType to AnotherType* as the types do not match
myOtherTypePointer := myType as AnotherType*

// UNDEFINED BEHAVIOR: casting a pointer to one type into a pointer of another
// can lead to undefined behaviors if the pointers are accessed
myOtherTypePointer := myType cast AnotherType*

// ERROR: cannot convert from `AnotherType` to `MyType*`
func(anotherType)

// ERROR: cannot convert from `AnotherType*` to `MyType*`
func(anotherType cast AnotherType*)

// UNDEFINED BEHAVIOR: casting a pointer to one type into a pointer of another
// can lead to undefined behaviors if the pointers are accessed
func(anotherType cast MyType*)
````



### Casting a pointer to a by-value type

A pointer cannot be converted back to a by-reference type (or to a by-value type). The compiler does not allow this kind of casting to occur because the pointer may point to nothing. The pointer should be checked to verify that the pointer is pointing to a valid type's instance or a panic may occur at runtime. The `as` operator can be used as one method to convert the pointer or a pointer can be converted to a reference to a type by using the dot (`.`) operator.

The `cast` operator will forcefully convert any pointer type into a value reference of any other type but this is not recommended as it can lead to undefined behaviors.

Examples of pointer casting:

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

AnotherType :: type {
    value1 : Float
    value2 : WString
}

funcByValue : ()(input : MyType) = {
    //...
}

funcByRef : ()(input : MyType&) = {
    //...
}

myType : MyType
anotherType : AnotherType

myTypePointer := myType as MyType*      // allowed

myTypeRef1 := myType as MyType&         // allowed - implicit casting 
myTypeRef2 := myType as &               // allowed - deduced reference type
myTypeRef3 : MyType& = myType           // allowed - implicit casting 
myTypeRef4 : & = myType                 // allowed - implicit casting with
                                        // deduced reference type
funcByValue(myType)                     // allowed - copy
funcByRef(myType)                       // allowed


myTypeRef5 := myTypePointer as MyType&  // allowed - implicit casting 
myTypeRef6 := myTypePointer as &        // allowed - deduced reference type

funcByValue(myTypePointer as MyType&)   // allowed - copy
funcByRef(myTypePointer as MyType&)     // allowed - implicit casting


// ERROR: cannot implicitly convert from a pointer type to a reference type
myTypeRef7 : MyType& = myTypePointer 
myTypeRef8 : & = myTypePointer
funcByValue(myTypePointer)
funcByRef(myTypePointer)


myTypeRefA := myTypePointer. as MyType& // allowed - already a reference
myTypeRefB := myTypePointer. as &       // allowed - already a reference

funcByValue(myTypePointer. as MyType&)  // allowed - copy
funcByRef(myTypePointer. as &)          // allowed - already a reference


myTypeRefC : MyType& = myTypePointer.   // allowed - already a reference
myTypeRefD : & = myTypePointer.         // allowed - already a reference
myTypeRefE := myTypePointer.            // allowed - copy with
                                        // deduced type

funcByValue(myTypePointer.)             // allowed - copy
funcByRef(myTypePointer.)               // allowed - already a reference


myTypeCopy1 := myType as MyType         // allowed - copy 
myTypeCopy2 : MyType = myType           // allowed - copy 
myTypeCopy3 := myType                   // allowed - copy with
                                        // deduced reference type 

funcByValue(myType as MyType)           // allowed - copy of a copy
funcByRef(myType as MyType)             // allowed - copy

funcByValue(myType)                     // allowed - copy
funcByRef(myType)                       // allowed


myTypeCopy4 := myTypePointer as MyType  // allowed - implicit copy casting 

// ERROR: cannot implicitly convert from a pointer type to a value copy
myTypeCopy4 : MyType = myTypePointer


funcByValue(myTypePointer as MyType)    // allowed - copy of a copy
funcByRef(myTypePointer as MyType)      // allowed - copy

// ERROR: cannot implicitly convert from a pointer type to a value copy
funcByValue(myTypePointer)
// ERROR: cannot implicitly convert from a pointer type to a reference
funcByRef(myTypePointer)


myTypeCopy5 := myTypePointer. as MyType // allowed - copy 
myTypeCopy6 : MyType = myTypePointer.   // allowed - copy 


funcByValue(myTypePointer. as MyType)   // allowed - copy of a copy
funcByRef(myTypePointer. as MyType)     // allowed - copy

funcByValue(myTypePointer.)             // allowed - copy
funcByRef(myTypePointer.)               // allowed



myTypePointerToNothing : MyType*                    // points to nothing

myTypePanic1 := myTypePointerToNothing as MyType&   // PANIC AT RUNTIME
myTypePanic2 := myTypePointerToNothing as &         // PANIC AT RUNTIME
myTypePanic3 := myTypePointerToNothing as MyType    // PANIC AT RUNTIME

funcByValue(myTypePointerToNothing as MyType&)      // PANIC AT RUNTIME
funcByRef(myTypePointerToNothing as MyType&)        // PANIC AT RUNTIME

funcByValue(myTypePointerToNothing as &)            // PANIC AT RUNTIME
funcByRef(myTypePointerToNothing as &)              // PANIC AT RUNTIME

funcByValue(myTypePointerToNothing as MyType)       // PANIC AT RUNTIME
funcByRef(myTypePointerToNothing as MyType)         // PANIC AT RUNTIME

myTypePanic4 := myTypePointerToNothing. as MyType&  // PANIC AT RUNTIME
myTypePanic5 := myTypePointerToNothing. as &        // PANIC AT RUNTIME
myTypePanic6 := myTypePointerToNothing. as MyType   // PANIC AT RUNTIME


// ERROR: cannot implicitly convert from a pointer type to a reference type
myTypePanicA : MyType& = myTypePointerToNothing
myTypePanicB : & = myTypePointerToNothing
myTypePanicC : MyType = myTypePointerToNothing


// ERROR: cannot implicitly convert from a pointer type to a reference type
funcByValue(myTypePointerToNothing)
funcByRef(myTypePointerToNothing)


myTypePanicD : MyType& = myTypePointerToNothing.    // PANIC AT RUNTIME
myTypePanicE : & = myTypePointerToNothing.          // PANIC AT RUNTIME
myTypePanicF : MyType = myTypePointerToNothing.     // PANIC AT RUNTIME

funcByValue(myTypePointerToNothing.)                // PANIC AT RUNTIME
funcByRef(myTypePointerToNothing.)                  // PANIC AT RUNTIME
````


### `type` casting using `as`

A `type` deemed compatible with another `type` be converted from one `type` to another `type` using the `as` operator. Compatibility is determined by ensuring the destination type contains all of the same types in the same order occupying the same space in memory. The variable names for the `type` need not be the same but the types must all be compatible. Likewise important qualifiers must not be lost. A type declared as `final` or `once`.

Other considerations:
* types declared as `once` are ignored
* functions declared as `final` do not need to match (as long as they do not capture data)
* values declared which are `constant` or `final` are ignored (as long as an instance of a type is not required inside the type's storage space)
* the source `type` can have more contained values than the `destination` type and still match
* casting `as` a value type will treat the source `type` as a type of `destination` and will use the copy constructor of the destination type to fulfill `cast` request
* a variable's `private` keyword is ignored and a `private` value can be accessed as non-`private` values in the destination (if the destination does not declare the new variable for the `type` as `private`)
* a `type` marked as `constant` must remain `constant` in the destination, including the converting type itself (i.e. `constant` qualification cannot be lost during the conversion)
* the memory layout of a type up to the final type of the destination must be identical (including alignment)

````zax
MyType :: type {
    category final once : String

    age : Integer
    name : String
    height : Float
}

CompatibleType :: type {
    value1 : Integer
    value2 : String
}

IncompatibleType :: type {
    name : String
    height : Float
}

myType : MyType

compatibleType := myType as CompatibleType     // allowed

// ERROR: the destination type is not compatible with the source type
incompatibleType := myType as IncompatibleType

byRefValue1 := myType as CompatibleType&    // allowed

// ERROR: not all of the values in `MyType` are available in `CompatibleType`
byRefValue2 := compatibleType as MyType&
````


### `type` casting using `cast`

A type can be force casted from one type to another using the `cast` operator. The compiler will not make any validation that the source and destination types are compatible and using the `cast` operator to convert one type to another can lead to undetermined behavior.

If a `cast` operator is performing a by-value cast, the source type will be treated as a destination reference type and given as an argument to the copy constructor of the destination type.

Warning: extreme caution must be used with the `cast` operator as it is considered unsafe; the `cast` operator is provided as a tool for programmers who understand the implications of treating raw memory of one type as raw memory of an entirely different type;

The example below performs unsafe `cast` conversions which will likely result in undefined behaviors.

````zax
MyType :: type {
    category final once : String

    age : Integer
    name : String
    height : Float
}

CompatibleType :: type {
    value1 : Integer
    value2 : String
}

IncompatibleType :: type {
    name : String
    height : Float
}

myType : MyType

compatibleType := myType cast CompatibleType        // safe but discouraged

// WARNING: undefined behaviors will occur during copy construction
incompatibleType := myType cast IncompatibleType    // unsafe

byRefValue1 := myType cast CompatibleType&          // safe but discouraged

// WARNING: undefined behaviors will occur if `byRefValue2` is accessed
byRefValue2 := compatibleType cast MyType&          // unsafe
````


### `as` operator overloading

Types can implement an `as` operator to support custom conversion from one type to another. This type of conversion can only be done by-value as by-reference would not be logical (as a new instance is needed for an unrelated destination type to exist as a reference).

````zax
MyType :: type {
    category final once : String

    age : Integer
    name : String
    height : Float

    operator as final : (result : IncompatibleType)() constant = {
        result.name = name
        return result
    }
}

IncompatibleType :: type {
    name : String
    height : Float
}

myType : MyType

// creates a new instance of `IncompatibleType` from `myType`
incompatibleType1 := myType cast IncompatibleType

// ERROR: cannot convert to the destination type by reference
byRefValue1 := myType as IncompatibleType&
````


#### Disable `as` operators

Disabling `as` operators is possible by declaring `as` operator overload functions as `final` with the function declared as pointing to nothing. Individual `as` operators can be enabled by creating a definition for a type, or disabled as needed. All non-specifically enabled or disabled `as` operators can be disabled by declaring a meta-function as final pointing to nothing.

The compiler generates casting a type to a compatible type by-reference using `as` operators are automatic. These `as` operators cannot be overloaded by they can be disabled (in either a catch-all meta-function or by explicit enabling using `default` or explicit disabling by declaring a function that points to nothing).

````zax
MyType :: type {
    category final once : String

    age : Integer
    name : String
    height : Float

    operator as final : (result : IncompatibleType)() constant = {
        result.name = name
        return result
    }

    // explicitly enable the reference conversion
    operator as final : (result : AnotherCompatibleType&)() constant = default

    // all `as` operators that are not explicitly defined will match this
    // lower priority casting meta-function where the compiler will
    // issue an error if this `as` operator is utilized
    operator as final : (result : )() constant
}

CompatibleType :: type {
    value1 : Integer
    value2 : String
}

AnotherCompatibleType :: type {
    value1 : Integer
}

IncompatibleType :: type {
    name : String
    height : Float
}

myType : MyType


myType : MyType

// ERROR: this `as` operator is explicitly disabled
compatibleType := myType cast CompatibleType

incompatibleType := myType as IncompatibleType  // allowed

// ERROR: this `as` operator is explicitly disabled
byRefValue1 := myType as CompatibleType&

// ERROR: cannot convert to the destination type by reference
byRefValue2 := myType as IncompatibleType&

byRefValue3:= myType as AnotherCompatibleType&  // allowed
````
