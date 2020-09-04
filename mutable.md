
# [Zax Programming Language](index.md)

## Mutability

A `mutable` type is a type that can have its contents modified after the type has been instantiated. An `immutable` type is a type whose contents cannot be modified once the type is constructed. A `constant` type is a promise not to modify a `mutable` type enforced by the compiler. A `mutable` type can be passed into functions which accept the `mutable` type as `constant` and thus will not perform any modifications to the type despite the type fundamentally being `mutable`. Since `immutable` types cannot have their values changed once created, the `constant` keyword has no applicability to `immutable` types.


### By default types are both `mutable` and `immutable`

Declaring a type without the `mutable` or `immutable` qualifiers implies a type is allowed to be both `mutable` and `immutable`. The `default` mutability of a type is `mutable` if the type is both `mutable` and `immutable`

````zax
// type supports both `mutable` and `immutable` form and defaults as `mutable`
MyType :: type {
    value1 : Integer = 42
    value2 : String = "life"
}

myType1 : MyType                // pick the `default` mutability for the type
                                // (which in this case is `mutable`)
myType2 : MyType mutable        // pick the `mutable` version of a type
myType3 : MyType immutable      // pick the `immutable` version of the type
myType4 : MyType constant       // pick the `default` mutability for the type but
                                // force the type to be in a `constant` state

myType1.value1 = 30             // allowed
myType1.value2 = "meaning"      // allowed
myType2 = myType1               // allowed

myType3.value1 = 30             // ERROR: type is `immutable` and
                                // cannot change its contents
myType3 = myType1               // ERROR: type is `immutable` and
                                // cannot change its contents

myType4.value1 = 30             // ERROR: type is `mutable` and `constant`
                                // cannot change its contents

myType1 = myType3               // allowed (source is `immutable` but
                                // the destination is `mutable`)
````


### `constant` functions and mutability

Functions that are not marked `constant` are not callable if a type is instantiated using its `immutable` form.

````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    value1 : Integer = 42
    value2 : String = "life"

    func1 final : ()(value : Integer) = {
        // ...
        value1 = value
        // ...
    }

    func2 final : ()(value : Integer) = {
        // ...
        print("Are these the same? ", value1, value, " ... you decide...")
        // ...
    }
}

myType1 : MyType                // pick the `default` mutability for the type
                                // (which in this case is `mutable`)
myType2 : MyType mutable        // pick the `mutable` version of a type
myType3 : MyType immutable      // pick the `immutable` version of the type
myType4 : MyType constant       // pick the `default` mutability for the type but
                                // force the type to be in a `constant` state

myType1.func1(1)                // allowed
myType1.func2(-1)               // allowed
myType2.func1(10)               // allowed
myType2.func2(-10)              // allowed
myType3.func1(20)               // ERROR: Cannot call function as
                                // is missing `constant` declaration
myType3.func2(-20)              // allowed
myType4.func1(20)               // ERROR: Cannot call function as function
                                // is `mutable` but `constant`
myType4.func2(-20)              // allowed
````


### Separate `mutable` and `immutable` types

Type implementations can have vastly different implementations of `mutable` and `immutable` types. This allows for optimizations in each type that is better suited based on the type's mutability.

````zax
MyType :: type mutable {
    count : Integer
    capacity : Integer      // capacity may only be applicable to a `mutable`
                            // type since only a changing type could require
                            // additional capacity

    // ...
}

MyType :: type immutable {
    count : Integer

    // no variable named `capacity` in the `immutable` version

    // ...
}

myType1 : MyType                // pick the `default` mutability for the type
                                // (which in this case is `mutable`)
myType2 : MyType mutable        // pick the `mutable` version of a type
myType3 : MyType immutable      // pick the `immutable` version of the type
myType4 : MyType constant       // pick the `default` mutability for the type
                                // but force the type to be `constant`
````


#### Using `default` to specify the `default` mutability

A type by `default` will choose the `mutable` version of a type's implementation unless the programmer overrides the `default` mutability of the type. The `default` keyword can be used to choose either to assume a type is `mutable` or `immutable` when instantiated when no qualification is specified.


##### Using `default` to specify the `default` mutability with distinct types

If a type has a `mutable` and `immutable` version, one of the two type qualifiers can be marked as `default` to indicate which qualified type is instantiated by `default` when neither mutability qualifier is specified.

````zax
MyType :: type mutable {
    // ...
}

MyType :: type immutable default {
    // ...
}

myType1 : MyType                // pick the `default` mutability for the type
                                // (which in this case is `immutable`)
myType2 : MyType mutable        // pick the `mutable` version of a type
myType3 : MyType immutable      // pick the `immutable` version of the type
myType4 : MyType constant       // pick the `default` mutability for the type
                                // which is `immutable` and thus `constant` is
                                // ignored
````

##### Using `default` to specify the `default` mutability with combined types

If a type has dual support for `mutable` and `immutable`, the type has a `default` mutability of `mutable`. However, the `default` mutability can be explicit. One of the two type qualifiers can be marked as `default` to indicate which qualified type is instantiated by `default` when neither mutability qualifier is specified.

Since a type is by `default` `mutable` when both mutability qualifiers are supported, explicit defaulting of the `mutable` qualifier is unnecessary.

````zax
// indicate the type implementation supports a `mutable` and `immutable` version and
// the `default` is specified as `immutable`
MyType :: type mutable immutable default {
    // ...
}

myType1 : MyType                // pick the `default` mutability for the type
                                // (which in this case is `immutable`)
myType2 : MyType mutable        // pick the `mutable` version of a type
myType3 : MyType immutable      // pick the `immutable` version of the type
myType4 : MyType constant       // pick the `default` mutability for the type
                                // which is `immutable` and thus `constant` is
                                // ignored
````

### Types only supporting `mutable` or `immutable`

While by default a type is declared to dual support being both `mutable` and `immutable`, a type can specify that it can only support one of the two mutability qualifiers. In such a case, if a programmer uses the type only the supported mutability of the type can be instantiated. Attempting to instantiate a type with an unsupported mutability will cause an error. The `default` keyword is not necessary when only one of the two mutability qualifiers is supported.


````zax
MyType :: type immutable {
    // ...
}

myType1 : MyType                // pick the `default` mutability for the type
                                // (which in this case must be `immutable`)
myType2 : MyType mutable        // ERROR: `mutable` type is unsupported
myType3 : MyType immutable      // pick the `immutable` version of the type
myType4 : MyType constant       // pick the `default` mutability for the type
                                // which must be `immutable` and thus
                                // `constant` is ignored
````


### `immutable` and `mutable` composition

As a `mutable` and `immutable` type can be entirely different implementations, each type is allowed to contain the other qualified version. This can be used to allow for one implementation to borrow the attributes in another implementation through standard [composition](composition.md) mechanisms.

````zax
MyType :: type mutable {
    // ...
}

MyType :: type immutable default {
    // the implementation of the immutable version borrows the implementation
    // of the mutable version (where non `constant` functions become
    // inaccessible)
    contain own : MyType mutable
}

myType1 : MyType                // pick the `default` mutability for the type
                                // (which in this case is `immutable`)
myType2 : MyType mutable        // pick the `mutable` version of a type
myType3 : MyType immutable      // pick the `immutable` version of the type
myType4 : MyType constant       // pick the `default` mutability for the type
                                // which is `immutable` and thus `constant` is
                                // ignored
````

### Automatic conversion of qualifiers

#### Automatic conversion of qualifiers when type supports both qualifiers

When a type supports both `mutable` and `immutable` within the sample type's definition, the conversion between `mutable`, `immutable`, and `constant` is automatic.


````zax
MyType :: type {
    // ...
}

// accept the default mutability of the type (which is `mutable`)
func1 final : ()(value : MyType&) = {
    // ...
}

// accept the `mutable` version of a type
func2 final : ()(value : MyType mutable &) = {
    // ...
}

// accept the `immutable` version of a type
func3 final : ()(value : MyType immutable &) = {
    // ...
}

// accept a `constant` version of a `mutable` type
func4 final : ()(value : MyType constant &) = {
    // ...
}


myType1 : MyType                // pick the `default` mutability for the type
                                // (which in this case is `mutable`)
myType2 : MyType mutable        // pick the `mutable` version of a type
myType3 : MyType immutable      // pick the `immutable` version of the type
myType4 : MyType constant       // pick the `default` mutability for the type
                                // which is `mutable` but make the type
                                // `constant`

// `mutable` type passed into four function variations
func1(myType1)                  // allowed
func2(myType1)                  // allowed
func3(myType1)                  // allowed
func4(myType1)                  // allowed

// `mutable` type passed into four function variations
func1(myType2)                  // allowed
func2(myType2)                  // allowed
func3(myType2)                  // allowed
func4(myType2)                  // allowed

// `immutable` type passed into four function variations
func1(myType3)                  // ERROR: `func1` expects a `mutable` type
func2(myType3)                  // ERROR: `func2` expects a `mutable` type
func3(myType3)                  // allowed
func4(myType3)                  // allowed

// constant type passed into four function variations
func1(myType4)                  // ERROR: `func1` expects a non-`constant` type
func2(myType4)                  // ERROR: `func2` expects a non-`constant` type
func3(myType4)                  // allowed
func4(myType4)                  // allowed
````

#### Automatic conversion of qualifiers when two different implementations of mutability exists

When a type supports both `mutable` and `immutable` except with two different type implementations, the conversion between `mutable`, `immutable`, and `constant` is only automatic for some conversions. Other conversions will fail or require the use of an `as` operator.

````zax
MyType :: type mutable {
    // ...
}

MyType :: type immutable {
    // ...
}

// accept the default mutability of the type (which is `mutable`)
func1 final : ()(value : MyType&) = {
    // ...
}

// accept the `mutable` version of a type
func2 final : ()(value : MyType mutable &) = {
    // ...
}

// accept the `immutable` version of a type
func3 final : ()(value : MyType immutable &) = {
    // ...
}

// accept a `constant` version of a `mutable` type
func4 final : ()(value : MyType constant &) = {
    // ...
}


myType1 : MyType                // pick the `default` mutability for the type
                                // (which in this case is `mutable`)
myType2 : MyType mutable        // pick the `mutable` version of a type
myType3 : MyType immutable      // pick the `immutable` version of the type
myType4 : MyType constant       // pick the `default` mutability for the type
                                // which is `mutable` but make the type
                                // `constant`

// mutable type passed into four function variations
func1(myType1)                  // allowed
func2(myType1)                  // allowed
func3(myType1)                  // ERROR: `func3` expects a `immutable` type
func4(myType1)                  // allowed

// mutable type passed into four function variations
func1(myType2)                  // allowed
func2(myType2)                  // allowed
func3(myType2)                  // ERROR: `func3` expects a `immutable` type
func4(myType2)                  // allowed

// immutable type passed into four function variations
func1(myType3)                  // ERROR: `func1` expects a `mutable` type
func2(myType3)                  // ERROR: `func2` expects a `mutable` type
func3(myType3)                  // allowed
func4(myType3)                  // ERROR: `func2` expects a
                                // `constant` `mutable` type

// constant type passed into four function variations
func1(myType4)                  // ERROR: `func1` expects a non-`constant` type
func2(myType4)                  // ERROR: `func2` expects a non-`constant` type
func3(myType4)                  // ERROR: `func3` expects a `immutable` type
func4(myType4)                  // allowed
````


### Conversion using the `as` operator

When a type supports both `mutable` and `immutable` except with two different type implementations, a conversion from one implementation to another using the `as` operator is required where accepting the other form would not be legal.

Important caveat: even though adding an `as` operator to convert from `immutable` to `mutable` is allowed, the returned converted type will be a temporary which (unless captured) will result in any modified contents of the `mutable` type being discarded when the temporary is discarded.

````zax
MyType :: type mutable {
    // ...

    operator as final : (result : MyType immutable)() constant = {
        // ...
    }
}

MyType :: type immutable {
    // ...

    operator as final : (result : MyType immutable)() constant = {
        // ...
    }
}

// accept the `default` mutability of the type (which is `mutable`)
func1 final : ()(value : MyType&) = {
    // ...
}

// accept the `mutable` version of a type
func2 final : ()(value : MyType mutable &) = {
    // ...
}

// accept the `immutable` version of a type
func3 final : ()(value : MyType immutable &) = {
    // ...
}

// accept a `constant` version of a `mutable` type
func4 final : ()(value : MyType constant &) = {
    // ...
}


myType1 : MyType                // pick the `default` mutability for the type
                                // (which in this case is `mutable`)
myType2 : MyType mutable        // pick the `mutable` version of a type
myType3 : MyType immutable      // pick the `immutable` version of the type
myType4 : MyType constant       // pick the `default` mutability for the type
                                // which is `mutable` but make the type
                                // `constant`

// mutable type passed into four function variations
func1(myType1)                  // allowed
func2(myType1)                  // allowed
func3(myType1 as immutable)     // allowed
func4(myType1)                  // allowed

// mutable type passed into four function variations
func1(myType2)                  // allowed
func2(myType2)                  // allowed
func3(myType2 as immutable)     // allowed
func4(myType2)                  // allowed

// immutable type passed into four function variations
func1(myType3 as mutable)       // allowed
func2(myType3 as mutable)       // allowed
func3(myType3)                  // allowed
func4(myType3 as mutable)       // allowed

// constant type passed into four function variations
func1(myType4)                  // ERROR: `func1` expects a non-`constant` type
func2(myType4)                  // ERROR: `func2` expects a non-`constant` type
func3(myType4 as immutable)     // ERROR: `func3` expects a `immutable` type
func4(myType4)                  // allowed
````


### Mutable variables

Variables contained with types marked as `immutable` or `constant` cannot have their values changed. In both of these cases, the inability to change contained values is both a promise and enforced by the compiler. However, cases do exist where a variable might need its contents changed despite the promise the contents of the type are not changing.

To circumvent a compiler's enforcement of non changeable contained types, the `mutable` keyword must be declared on a variable. Declaring a variable as `mutable` is not the same as declaring a type as `mutable`. A variable declared as `mutable` implies the type's variable will be treated as non-`constant` even if the calling function is executed in a from `constant` function or from within an `immutable` type.

A variable can be declared `mutable` entirely separate from the type's mutability. For example, mutable variable could be a pointer type to an immutable type named `SomeType`, i.e. `variable mutable : SomeType immutable*`.

````zax
MyType :: type {
    value1 mutable : Integer
    value2 : String

    func1 final : ()() = {
        // ...
    }

    func2 final : ()() constant = {
        // ...

        ++value1        // allowed as `value1` is `mutable`
                        // despite the `constant` qualifier
        value2 = "hi"   // ERROR: value2 cannot be modified
    }
}

// accept the `mutable` version of a type
func1 final : ()(value : MyType mutable &) = {
    ++value.value1          // allowed
    value.value2 = "howdy"  // allowed
}

// accept the `immutable` version of a type
func2 final : ()(value : MyType immutable &) = {
    ++value.value1          // allowed as `value` is `mutable`
                            // despite the `immutable` qualifier
    value.value2 = "hiya"   // ERROR: `value2` is `immutable`
}

// accept a `constant` version of a `mutable` type
func3 final : ()(value : MyType constant &) = {
    ++value.value1          // allowed as `value` is `mutable`
                            // despite the `constant` qualifier
    value.value2 = "hiya"   // ERROR: `value2` is `constant`
}
````


### Using `immutable` with traditionally `mutable` operators

Types declared as `immutable` may declare traditionally `mutable` operators such as the assignable operators (`=`, `+=`, `-=`, etc) even if the type is inherently `immutable`. This can allow a type to be reassigned to a new value even if the underlying type's contents is normally immutable.

A classic example of an immutable type supporting an assignment operator is the `String` type:

````zax
animal : String = "bear"    // by `default`, strings are `immutable`

animal = "fox"              // the assignment to a new value for the type
                            // is allowed despite the type being `immutable`

animal += "lamb"            // the contents of the original `String` are not
                            // modified but rather a new `String` replaces
                            // the old `String` entirely with a new value

animal[2] = c'u'            // ERROR: the `String` is `immutable`
````

By allowing overloading of the assignment operators, the immutable type can utilize techniques such as [COW (Copy on Write)](https://en.wikipedia.org/wiki/Copy-on-write) under the covers. Using COW can allow for greater efficiency by ensuring a single `handle` to a type exists where every modifying creates a new immutable copy of the original (with optional modifications having been performed at the same time).


### Mutability of String types

Zax declares all `String` types as `immutable` by `default`. Since most of the operators are available on the `String` type, there's not a strong reason to use a mutable form except to directly access and modify the raw characters in a `String`. 


### Thread safety with `immutable`

A type declared as `immutable` is not necessarily thread safe (although making a type immutable can help with thread concurrency issues). A reference to an object on a different thread could exist and the original object becomes deallocated. The type may contain `mutable` variables which were not designed to be thread safe, or the variable may contain `handle` pointers and copied to an alternative thread without accounting for thread safety.

For `immutable` types with shared state between instances (e.g. the `String` type), the `deep` qualifier can help ensure a `deep` copy of a type is performed prior a value being copied and passed to another thread. See the [concurrency](concurrency.md) section for more details.
