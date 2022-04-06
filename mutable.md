
# [Zax Programming Language](index.md)

## Mutability

A `mutable` type is a type that can have its contents modified after a type has been instantiated. An `immutable` type is a type whose contents cannot be modified once a type is constructed (and immutability is enforced by a compiler). A `mutable` variable overrides a type's `constant` or `inconstant` declaration.

An `immutable` variable will respect a type's `constant` or `inconstant` declaration (and makes no promise otherwise). Variables are defaulted and implicitly declared as `immutable` and declaring variables as `immutable` is rarely done when a variable is declared. An `immutable` variable is not the same thing concept as an `immutable` type. For variables, mutability implies if a type's mutability is respected or not whereas as type's immutability implies if contents of a type can be changed or not. A `mutable` variable indicates that immutability of a type should be ignored, but `immutable` on a variable indicates that a type's immutability should be respected.

A `constant` type is a promise not to modify a `mutable` type enforced by a compiler. An `inconstant` type is an allowance to modify a mutable type. A `constant` function inside a type is a promise that a function will not modify a type's `mutable` contents (and enforced by a compiler). A `inconstant` function inside a type is a declaration that a function is allowed to modify contents of a type's `mutable` contents.

A `final` variable is not allowed to change its value once instantiated (and enforced by a compiler). A `final` variables makes no promise about changing `mutable` contents contained within a variable's type, and only applies to the direct value of a variable itself. A `varies` variable allows `mutable` values contents to be changed (and `mutable` contents are allowed to be changed in variables declares as `final` or `varies`).

A `mutable` type can be passed into functions which accept a `mutable` type as `constant` and thus deny further modification of that type's value and contents (which is enforced by a compiler).

Since `immutable` types cannot have their values changed once created, a `constant` or `inconstant` keyword has no applicability to `immutable` types. An `immutable` type is always `immutable` and cannot be made `inconstant`; contents of an immutable type are `final` and `constant` once constructed. However, a `mutable` variable declaration can override a `constant` declaration to become `inconstant` and `final` declaration to become `varies` for a variable inside a `constant` or `immutable` type.

Quick lookup guide:
````
mutable         // (default) applied to a type, contents of a type are
                // modifiable
mutable         // applied to a variable inside a type, contents of a variable
                // can be changed even if a type is `constant` or `immutable`
                // and selects a `mutable` version of a type if available
immutable       // applied to a type, declares a type as being `immutable`
                // where all values are `final` once constructed regardless of
                // a types underlying inherent mutability
immutable       // (default) applied to a variable inside a type, causes a
                // variable to respect mutability of `constant` or
                // `inconstant`, or `mutable` or `immutable` types (i.e.
                // opposite behavior of `mutable` declared on a variable
                // inside a type)
                // NOTE: rarely used unless the default is overridden with a
                // compiler directive
constant        // applied to a type, contents of a `mutable` type are
                // disallowed to be modified (has no applicability for
                // `immutable` types which are effectively always `constant`)
constant        // applied to a function, a function promises to not modify any
                // `mutable` contents within a type
inconstant      // (default) applied to a type, contents of a `mutable` type
                // are allowed to be modified (has no applicability
                // for `immutable` types which are effectively always
                // `constant`)
inconstant      // (default) applied to a function, a function declares it is
                // allowed to modify any `mutable` contents within a type
final           // a variable which receives its final value once constructed
                // (but makes no promise not to modify contents of any
                // contained values within a type)
varies          // (default) a variable which is allowed to have its value
                // change over time (and makes no promise about contents of any
                // contained values within the value)
                // NOTE: rarely used unless the default is overridden with a
                // compiler directive

Opposites:
final vs varies         // applies to the ability to change a variable's
                        // value once initialized (or not) but in both cases
                        // makes no promise about a types contained contents
immutable vs mutable    // when applied to a type, applies to the ability
                        // modify contents a type post construction (or not)
immutable vs mutable    // when applied to a variable, applies to the ability
                        // modify the contents of a type regardless if the
                        // type is `constant` or not
constant vs inconstant  // applied to a type, allows modification of a type's
                        // contents (or disallows)
constant vs inconstant  // applied to a function,allows a function to modify
                        // contained types or not
````


### By default types are both `mutable` and `immutable`

Declaring a type without `mutable` or `immutable` qualifiers causes a type to support both `mutable` and `immutable` instances. The `default` mutability of a type is `mutable` if a type is both `mutable` and `immutable`. The `default` mutability for type can be specified, and overridden with explicit qualifiers on instantiation if a type allowing for an alternative mutability.

````zax
// type supports both `mutable` and `immutable` forms and defaults as `mutable`
MyType :: type {
    value1 : Integer = 42
    value2 : String = "life"
}

myType1 : MyType                // pick the `default` mutability for the type
                                // (which in this case is `mutable`)
myType2 : MyType mutable        // pick the `mutable` version of a type
myType3 : MyType immutable      // pick the `immutable` version of the type
myType4 : MyType constant       // pick the `default` mutability for the type
                                // but force the type to be in a `constant`
                                // state

myType1.value1 = 30             // allowed
myType1.value2 = "meaning"      // allowed
myType2 = myType1               // allowed

myType3.value1 = 30             // ERROR: type is `immutable` and cannot change
                                // its contents
myType3 = myType1               // ERROR: type is `immutable` and cannot change
                                // its contents

myType4.value1 = 30             // ERROR: type is `mutable` and `constant`
                                // cannot change its contents

myType1 = myType3               // allowed (source is `immutable` but the
                                // destination is `mutable`)
````


### `constant` functions and mutability

Functions that are not qualified as `constant` are not callable if a type is instantiated using an `immutable` type.

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
myType3.func1(20)               // ERROR: Cannot call function as the function
                                //  is missing `constant` declaration
myType3.func2(-20)              // allowed
myType4.func1(20)               // ERROR: Cannot call function as the type
                                // is `mutable` but `constant`
myType4.func2(-20)              // allowed
````


### Separate `mutable` and `immutable` types

Type implementations can have vastly different implementations of `mutable` and `immutable` types. This allows for optimizations in each type that is better suited based on a type's mutability. One drawback to different implementations would be that conversion from `immutable` to `mutable` would require additional overhead logic. This tradeoff maybe acceptable depending on usage patterns.

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


#### Using `default` to specify a `default` mutability

A type by `default` will choose a `mutable` version of a type's implementation unless a programmer overrides a `default` mutability of a type. A `default` keyword can be used to choose either to assume a type is `mutable` or `immutable` when instantiated when no qualification is specified.


##### Using `default` to specify a `default` mutability with distinct types

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


##### Using `default` to specify a `default` mutability with combined types

If a type has dual support for `mutable` and `immutable`, a type has a `default` mutability of `mutable`. However, the `default` mutability can be explicit. One of the two type qualifiers can be marked as `default` to indicate which qualified type is instantiated by `default` when neither mutability qualifier is specified.

Since a type is by `default` `mutable` when both mutability qualifiers are supported, explicit defaulting of the `mutable` qualifier is unnecessary and redundant. The `default` keyword is placed after the keyword `mutable` or `immutable` to indicate which of the two is the appropriate default.

````zax
// indicate a type implementation supports a `mutable` and `immutable` version
// and the `default` is specified as `immutable`
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

While by default a type is declared to dual support being both `mutable` and `immutable`, a type can specify that it can only support one of the two mutability qualifiers. In such a case, if a programmer uses a type and only declares support for mutability of one of the two types then only that one type of type mutability is supported. Attempting to instantiate a type with an unsupported mutability will cause an error. A `default` keyword is not necessary (and redundant) when only one of the two mutability qualifiers is supported.


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

As a `mutable` and `immutable` type can be entirely different implementations, each type is allowed to contain the other as a self contained type. This can be used to allow for one implementation to borrow attributes in another implementation type implementation through standard [composition](composition.md) mechanisms.

````zax
MyType :: type mutable {
    // ...
}

MyType :: type immutable default {
    // the implementation of the `immutable` version borrows the implementation
    // of the `mutable` version (where non `constant` functions become
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

When a type supports both `mutable` and `immutable` within the sample type's definition, a conversion from `mutable` to `immutable`, and `constant` is automatic (although the reverse direction is not allowed).

````zax
MyType :: type {
    // ...
}

// accept the default mutability of the type (which is `mutable`)
func1 final : ()(value : MyType &) = {
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

func2(myType3 as mutable)         // ERROR: `myType3` cannot be safely converted
                                  // as `mutable`
func2(myType3 unsafe as mutable)  // UNSAFE: `myType3` is stripped of its
                                  // `immutable` qualification
func2(myType4 as mutable)         // ERROR: `myType4` was `mutable` and it
                                  // remains `constant`
func2(myType4 unsafe as mutable)  // ERROR: `myType4` was `mutable` and it
                                  // remains `constant`
func2(myType4 as inconstant)      // ERROR: `myType4` cannot be safely converted
                                  // as `inconstant`
func2(myType4 unsafe as mutable)  // UNSAFE: `myType4` is stripped of its
                                  // `constant` qualification


// constant type passed into four function variations
func1(myType4)                  // ERROR: `func1` expects a non-`constant` type
func2(myType4)                  // ERROR: `func2` expects a non-`constant` type
func3(myType4)                  // allowed
func4(myType4)                  // allowed
````


#### Automatic conversion of qualifiers when two different implementations of mutability exists

When a type supports both `mutable` and `immutable` except with two different type implementations, conversion between `mutable`, `immutable`, and `constant` is only automatic for some conversions. Other conversions will fail or require a use of an `as` operator with special conversion logic.

````zax
MyType :: type mutable {
    value : VeryUniqueTypeA
    // ...
}

MyType :: type immutable {
    value : VeryUniqueTypeB
    // ...
}

// accept the default mutability of the type (which is `mutable`)
func1 final : ()(value : MyType &) = {
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
                                // and the `mutable` type is incompatible
func4(myType1)                  // allowed

// mutable type passed into four function variations
func1(myType2)                  // allowed
func2(myType2)                  // allowed
func3(myType2)                  // ERROR: `func3` expects a `immutable` type
                                // and the `mutable` type is incompatible
func4(myType2)                  // allowed

// immutable type passed into four function variations
func1(myType3)                  // ERROR: `func1` expects a `mutable` type
func2(myType3)                  // ERROR: `func2` expects a `mutable` type
func3(myType3)                  // allowed
func4(myType3)                  // ERROR: `func2` expects a
                                // `constant` `mutable` type

func2(myType3 as mutable)         // ERROR: `myType3` cannot be safely converted
                                  // as `mutable`
func2(myType3 unsafe as mutable)  // ERROR: `myType3` has no conversion to a
                                  // `mutable` version even using `unsafe as`
                                  // (would need to be treated as a raw pointer
                                  // cast which would have undefined behavior)
func2(myType4 as mutable)         // ERROR: `myType4` was `mutable` and it
                                  // remains `constant`
func2(myType4 unsafe as mutable)  // ERROR: `myType4` was `mutable` and it
                                  // remains `constant`
func2(myType4 as inconstant)      // ERROR: `myType4` cannot be safely converted
                                  // as `inconstant`
func2(myType4 unsafe as mutable)  // ERROR: `myType4` has no conversion to a
                                  // `mutable` version even using `unsafe as`
                                  // (would need to be treated as a raw pointer
                                  // cast which would have undefined behavior)


// constant type passed into four function variations
func1(myType4)                  // ERROR: `func1` expects a non-`constant` type
func2(myType4)                  // ERROR: `func2` expects a non-`constant` type
func3(myType4)                  // ERROR: `func3` expects a `immutable` type
func4(myType4)                  // allowed
````


### Conversion using an `as` operator

When a type supports both `mutable` and `immutable` except with two different type implementations, a conversion from one implementation to another using the `as` operator is required where accepting the other form would not be legal.

Important caveat: even though adding an `as` operator to convert from `immutable` to `mutable` is allowed, a returned converted type will be a temporary which will result in any modified contents of a `mutable` type being discarded when the temporary is discarded (since changes are applied to a temporary value not the original `immutable` version).

````zax
MyType :: type mutable {
    // ...

    operator as final : (result : MyType immutable)() constant = {
        // ...
    }
}

MyType :: type immutable {
    // ...

    operator as final : (result : MyType mutable)() constant = {
        // ...
    }
}

// accept the `default` mutability of the type (which is `mutable`)
func1 final : ()(value : MyType &) = {
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
func3(myType4 as immutable)     // allowed - the `mutable` is converted to
                                // an `immutable` by the system automatically
                                // and the `as` operator is never performed;
                                // the `constant` qualifier becomes redundant;
func4(myType4)                  // allowed
````


### `mutable` variables

Variables contained with types qualified as `immutable` or `constant` cannot have their values changed. In both of these cases, not changing contained values is both a promise and enforced by a compiler. However, cases do exist where a variable might need its contents changed despite promising contents of a type do not change.

To circumvent a compiler's enforcement of non-changeable contained types, a `mutable` keyword must be declared on a variable inside a type's definition. Declaring a variable as `mutable` is not the same as declaring a type as `mutable`. A variable declared as `mutable` implies a type's variable will be treated as non-`constant` even if the calling function is executed in a from `constant` function or from within an `immutable` type.

A variable can be declared `mutable` entirely separate from a type's mutability. For example, `mutable` variable could be a pointer type to an immutable type named `SomeType`, i.e. `variable mutable : SomeType immutable *`.

Using `mutable` should be done with extreme caution as memory backing a type might change when code was expecting a type's contents to never change. Some values inside a type might be implicitly `mutable` despite being inside an `immutable` type. An example would be `handle` or `strong` pointers which maintain reference counts in a control block which may be technically contained within the memory space of a type's instance. Thus a C style `memcpy` of a type's instance might encounter changing memory data for an instance despite an immutable type being used (should features like `mutable`, `handle`, or `strong` pointers be used).

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

Types declared as `immutable` may declare traditionally `mutable` operators such as the assignable operators (`=`, `+=`, `-=`, etc) even if the type is inherently `immutable`. This can allow a type to be reassigned to a new value even if the underlying type's contents is normally `immutable`.

A classic example of an immutable type supporting an assignment operator is a `String` type:

````zax
animal : String = "bear"    // by `default`, strings are `immutable`

animal = "fox"              // the assignment to a new value for the type
                            // is allowed despite the type being `immutable`

animal += "lamb"            // the contents of the original `String` are not
                            // modified but rather a new `String` replaces
                            // the old `String` entirely with a new value

animal[2] = c'u'            // ERROR: the `String` is `immutable`
````

By allowing overloading of assignment operators, `immutable` types can utilize techniques such as [COW (Copy on Write)](https://en.wikipedia.org/wiki/Copy-on-write) under the covers. Using COW can allow for greater efficiency by ensuring a single `handle` to a type's immutable data exists where every modification creates a new immutable copy of an original value (where modifications occurs only during the copy process).


### Mutability of String types

Zax declares all `String` types as `immutable` by `default`. Since most of operators are available on `String` types, there's not a strong incentive to use a `mutable` form of a string (except in the rarer use case where direct "in-place" string manipulation is performed).


### Thread safety with `immutable`

A type declared as `immutable` is not necessarily thread safe (although making a type `immutable` can help with thread concurrency issues). A reference to an object on a different thread could exist when the original object becomes deallocated. A type may contain `mutable` variables which were not designed to be thread safe, or a variable may contain `handle` pointers which are not inherently thread safe.

For `immutable` types with shared state between instances (e.g. a `String` type), a `deep` qualifier can help ensure a `deep` copy of a type is performed prior a value being copied and passed to another thread. See the [concurrency](concurrency.md) section for more details.
