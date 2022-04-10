
# [Zax Programming Language](index.md)

## Mutability

A `mutable` type is a type that can have its contents modified after a type has been instantiated. An `immutable` type is a type whose contents cannot be modified once a type is constructed (and immutability is enforced by a compiler). A `pliable` variable overrides a type's `constant` or `inconstant` qualifier.

An `unpliable` variable will respect a type's `constant` or `inconstant` qualifier (and makes no promise otherwise). Variables are defaulted and implicitly declared as `unpliable` and declaring variables as `pliable` is rarely done when a variable is declared. An `unpliable` variable is not the same concept as an `immutable` type. For variables, pliability affects if a type's `constant` qualifier is respected or not. Whereas a type's immutability indicates if contents of a type can be modified or not. A `pliable` variable indicates that a type's `constant` qualifier should be ignored, but `unpliable` on a variable indicates that a type's `constant` qualifier should be respected.

A `constant` `type` qualifier is a promise not to modify any contents of a normally `mutable` type or `varies` variable, which is enforced by a compiler. An `inconstant` type indicates a `mutable` type's contents may be modified. A function declared as `constant` inside a `type` is a promise that a function will not modify a type's `mutable` contents, which is enforced by a compiler. A function declared as `inconstant` inside a `type` is an announcement that a function may modify a type's `mutable` contents.

A `constant` / `inconstant` type qualifier and a `varies` / `final` variable declaration have no affect on `immutable` contents. By convention, `immutable` types can never have their value or contained contents modified thus a `constant` promise not to modify a `type` that already can't be modified has no meaning. Likewise, `inconstant` cannot be applied to `immutable` types as an `immutable` type can never be modified. All `immutable` types are `constant` and `final` once constructed. Further, `varies`, `pliable`, and `unpliable` variable declarations on `immutable` types have no affect because `immutable` types can never be modified (even by attempting to override a type's `constant` promise by using a `pliable` variable declaration).

A `final` variable is not allowed to change its immediate value once instantiated, which is enforced by a compiler. A `final` variables makes no promise about any `mutable` contents contained within a variable's `type`, and only applies to the immediate a variable's immediate value. A `varies` variable allows `mutable` and `inconstant` type's immediate value to be changed (and `mutable` and `inconstant` contained contents are allowed to be changed regardless if a variable is declares as `final` or `varies`).

A `mutable` type can be passed into functions which accept a `mutable` type as a `constant` type. A constant argument denies further modification of a type's value and contained contents, which is enforced by a compiler.

Quick lookup guide (type qualifiers):
````
mutable         // (default) values and contents inside a type are modifiable
immutable       // all values are `final` once constructed regardless of a
                // type's underlying inherent mutability
constant        // values and contents of a `mutable` type are disallowed to be
                // modified (has no applicability for `immutable` types which
                // are effectively always `constant`)
constant        // when applied to a function, a function promises to not modify
                // any contents within a type (and a `constant` qualifier is
                // ignored if `pliable` is used on a variable)
inconstant      // (default) values and contents of a `mutable` type are allowed
                // to be modified (has no applicability for `immutable` types
                // which are effectively always `constant`)
inconstant      // (default) when applied to a function, a function declares it
                // may modify any `mutable` contents within a type

Mutually exclusive:
immutable vs mutable    // ability modify contents a type's value and contents
                        // post construction
constant vs inconstant  // a promise not to modify of a type's value or contents
                        // (except for `immutable` types which are always
                        // `constant`, and `constant` can be overridden using a
                        // `pliable` declaration on a variable)
constant vs inconstant  // when applied to a function, declaration of a
                        // function's intent to modify a `type`'s contained
                        // values (or not)
````

Quick lookup guide (variable declarations):
````
pliable         // value and contents of a `mutable` `type` can be changed
                // even if a `type` is declared `constant` (although has
                // no affect on `immutable` types as immutable types can never
                // be modified)
unpliable       // (default) causes a variable to respect a `constant` or
                // `inconstant` `type` qualifier on `mutable` types (i.e.
                // opposite behavior of `pliable`)
                // NOTE: rarely used unless a default `unpliable` declaration
                // is overridden with a `variables` compiler directive
final           // a variable which receives its final value once constructed
                // (but makes no promise not to modify any contents of any
                // contained values within a `type`)
varies          // (default) a variable which is allowed to have its value
                // change over time (and makes no promise about contents of any
                // contained values within the value); `varies` has no affect
                // on `constant` or `immutable` types which can not have their
                // value changed (unless `constant` is overridden with a
                // `pliable` declaration)
                // NOTE: rarely used unless a default `varies` is overridden
                // with a `variables` compiler directive

Mutually exclusive:
final vs varies         // ability to change a variable's value once initialized
                        // but in both cases makes no promise about a `type`'s
                        // contained contents (but all `constant` and
                        // `immutable` types are always considered `final`)
pliable vs unpliable    // ability modify the value and contents of a `type`
                        // (regardless if a `type` is `constant` or not)
````


### By default types are both `mutable` and `immutable`

Declaring a `type` without `mutable` or `immutable` qualifiers causes a type to support both `mutable` and `immutable` instances. The `default` mutability of a `type` is `mutable` if a type is both `mutable` and `immutable`. The `default` mutability for `type` can be specified, and overridden with explicit qualifiers on instantiation if a type allows for an alternative mutability.

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
myType4.func1(20)               // ERROR: Cannot call function as the type is
                                // `mutable` but `constant`
myType4.func2(-20)              // allowed
````


### Separate `mutable` and `immutable` types

Type implementations can have vastly different implementations of `mutable` and `immutable` types. This allows for optimizations in each type that is better suited based on a type's mutability. One drawback to different implementations would be that conversion from `immutable` to `mutable` would require additional overhead logic. This tradeoff may be acceptable depending on usage patterns.

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

A `type` by `default` will choose a `mutable` version of a `type`'s implementation unless a programmer overrides a `default` mutability of a type. A `default` keyword can be used to change a `default` assumption if a type is `mutable` or `immutable` when a `type` is instantiated where no mutability qualification is specified.


##### Using `default` to specify a `default` mutability with distinct types

If a type has a `mutable` and `immutable` version, one of the two type qualifiers can be marked as `default` to indicate which mutability qualified `type` is instantiated by `default` (when neither mutability qualifier is specified at instantiation).

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

If a type has dual support for `mutable` and `immutable`, a `type` has a `default` mutability of `mutable`. However, `default` mutability qualification can be explicit. One of the two mutability `type` qualifiers can be marked as `default` to indicate which mutability qualification is selected by `default` (when neither mutability qualifier is specified at instantiation).

Since a `type` is by `default` `mutable` when both mutability qualifiers are supported, explicit defaulting of the `mutable` qualifier is unnecessary and redundant (unless the default mutability qualification was overridden with the `types` compiler directive). A `default` keyword is placed after a keyword `mutable` or `immutable` qualifier to indicate which of the two mutability qualifiers is an appropriate `default`.

````zax
// indicate a `type` implementation supports a `mutable` and `immutable`
// qualification and the `default` is specified as `immutable`
MyType :: type mutable immutable default {
    // ...
}

myType1 : MyType                // pick the `default` mutability for the `type`
                                // (which in this case is `immutable`)
myType2 : MyType mutable        // pick the `mutable` version of a `type`
myType3 : MyType immutable      // pick the `immutable` version of the `type`
myType4 : MyType constant       // pick the `default` mutability for the `type`
                                // (which is `immutable` and thus `constant` is
                                // ignored)
````


### Types only supporting `mutable` or `immutable`

While by default a type is declared to dual support both `mutable` and `immutable` `type` qualifiers, a `type` can specify to only support one of two mutability qualifiers. In such a case, if a programmer uses a `type` and only declares support for one mutability then only that mutability qualifier is supported. Attempting to instantiate a `type` with an unsupported mutability will cause a `type-mutability-qualifier-not-supported` error. A `default` keyword is not necessary (and redundant) when only one of two mutability qualifiers is supported.


````zax
MyType :: type immutable {
    // ...
}

myType1 : MyType                // pick the `default` mutability for the type
                                // (which in this case must be `immutable`)
myType2 : MyType mutable        // ERROR: `mutable` type is unsupported
myType3 : MyType immutable      // pick the `immutable` version of the `type`
myType4 : MyType constant       // pick the `default` mutability for the `type`
                                // (which must be `immutable` and thus
                                // `constant` is ignored)
````


### `immutable` and `mutable` composition

As a `mutable` and `immutable` type can be entirely different implementations, each type is allowed to contain the other as a self contained type. This can be used to allow for one implementation to borrow attributes in another implementation through standard [composition](composition.md) mechanisms.

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

myType1 : MyType                // pick the `default` mutability for the `type`
                                // (which in this case is `immutable`)
myType2 : MyType mutable        // pick the `mutable` version of the `type`
myType3 : MyType immutable      // pick the `immutable` version of the `type`
myType4 : MyType constant       // pick the `default` mutability for the `type`
                                // (which is `immutable` and thus `constant` is
                                // ignored)
````


### Automatic conversion of qualifiers

#### Automatic conversion of qualifiers when type supports both qualifiers

When a `type` supports both `mutable` and `immutable` within type's definition, a conversion from `mutable` to `immutable` or `constant` is automatic (although the reverse direction is not allowed).

````zax
MyType :: type {
    // ...
}

// accept the default mutability of a `type` (which is `mutable`)
func1 final : ()(value : MyType &) = {
    // ...
}

// accept the `mutable` version of a `type`
func2 final : ()(value : MyType mutable &) = {
    // ...
}

// accept the `immutable` version of a `type`
func3 final : ()(value : MyType immutable &) = {
    // ...
}

// accept a `constant` version of a `mutable` `type`
func4 final : ()(value : MyType constant &) = {
    // ...
}


myType1 : MyType                // pick the `default` mutability for the `type`
                                // (which in this case is `mutable`)
myType2 : MyType mutable        // pick the `mutable` version of the `type`
myType3 : MyType immutable      // pick the `immutable` version of the `type`
myType4 : MyType constant       // pick the `default` mutability for the `type`
                                // (which is `mutable` but make the `type`
                                // `constant`)

// `mutable` `type` passed into four function variations
func1(myType1)                  // allowed
func2(myType1)                  // allowed
func3(myType1)                  // allowed
func4(myType1)                  // allowed

// `mutable` `type` passed into four function variations
func1(myType2)                  // allowed
func2(myType2)                  // allowed
func3(myType2)                  // allowed
func4(myType2)                  // allowed

// `immutable` `type` passed into four function variations
func1(myType3)                  // ERROR: `func1` expects a `mutable` type
func2(myType3)                  // ERROR: `func2` expects a `mutable` type
func3(myType3)                  // allowed
func4(myType3)                  // ERROR: `func4` expects an a `mutable` but
                                // `constant` type

func2(myType3 as mutable)            // ERROR: `myType3` cannot be safely
                                     // converted as `mutable`
func2(myType3 unsafe as mutable)     // UNSAFE: `myType3` is stripped of its
                                     // `immutable` qualification
func2(myType4 as mutable)            // ERROR: `myType4` was `mutable` and it
                                     // remains `constant`
func2(myType4 unsafe as mutable)     // ERROR: `myType4` was `mutable` and it
                                     // remains `constant`
func2(myType4 as inconstant)         // ERROR: `myType4` cannot be safely
                                     // converted as `inconstant`
func2(myType4 unsafe as inconstant)  // UNSAFE: `myType4` is stripped of its
                                     // `constant` qualification


// constant type passed into four function variations
func1(myType4)                  // ERROR: `func1` expects a non-`constant` type
func2(myType4)                  // ERROR: `func2` expects a non-`constant` type
func3(myType4)                  // allowed
func4(myType4)                  // allowed
````


#### Automatic conversion of qualifiers when two different implementations of mutability exists

When a type supports both `mutable` and `immutable` qualifiers except with two different type implementations, conversion between `mutable`, `immutable`, and `constant` is only automatic for some conversions. Other conversions will fail or require a use of an `as` operator with special conversion logic.

````zax
MyType :: type mutable {
    value : VeryUniqueTypeA
    // ...
}

MyType :: type immutable {
    value : VeryUniqueTypeB
    // ...
}

// accept the default mutability of a `type` (which is `mutable`)
func1 final : ()(value : MyType &) = {
    // ...
}

// accept the `mutable` version of a `type`
func2 final : ()(value : MyType mutable &) = {
    // ...
}

// accept the `immutable` version of a `type`
func3 final : ()(value : MyType immutable &) = {
    // ...
}

// accept a `constant` version of a `mutable` `type`
func4 final : ()(value : MyType constant &) = {
    // ...
}


myType1 : MyType                // pick the `default` mutability for the `type`
                                // (which in this case is `mutable`)
myType2 : MyType mutable        // pick the `mutable` version of the `type`
myType3 : MyType immutable      // pick the `immutable` version of the `type`
myType4 : MyType constant       // pick the `default` mutability for the `type`
                                // which is `mutable` but make the `type`
                                // `constant`

// mutable type passed into four function variations
func1(myType1)                  // allowed
func2(myType1)                  // allowed
func3(myType1)                  // ERROR: `func3` expects a `immutable` `type`
                                // and the `mutable` `type` is incompatible
func4(myType1)                  // allowed

// mutable type passed into four function variations
func1(myType2)                  // allowed
func2(myType2)                  // allowed
func3(myType2)                  // ERROR: `func3` expects a `immutable` `type`
                                // and the `mutable` `type` is incompatible
func4(myType2)                  // allowed

// immutable type passed into four function variations
func1(myType3)                  // ERROR: `func1` expects a `mutable` `type`
func2(myType3)                  // ERROR: `func2` expects a `mutable` `type`
func3(myType3)                  // allowed
func4(myType3)                  // ERROR: `func2` expects a
                                // `constant` `mutable` `type`

func2(myType3 as mutable)           // ERROR: `myType3` cannot be safely
                                    // converted as `mutable`
func2(myType3 unsafe as mutable)    // ERROR: `myType3` has no conversion to a
                                    // `mutable` version even using `unsafe as`
                                    // (would need to be treated as a raw
                                    // pointer cast which would have undefined
                                    // behavior)
func2(myType4 as mutable)           // ERROR: `myType4` was `mutable` and it
                                    // remains `constant`
func2(myType4 unsafe as mutable)    // ERROR: `myType4` was `mutable` and it
                                    // remains `constant`
func2(myType4 as inconstant)        // ERROR: `myType4` cannot be safely
                                    // converted as `inconstant`
func2(myType4 unsafe as inconstant) // UNSAFE: `myType4` is `mutable` and
                                    // `unsafe as` would strip the `constant`
                                    // qualification


// constant type passed into four function variations
func1(myType4)                  // ERROR: `func1` expects a non-`constant` type
func2(myType4)                  // ERROR: `func2` expects a non-`constant` type
func3(myType4)                  // ERROR: `func3` expects a `immutable` type
func4(myType4)                  // allowed
````


### Conversion using an `as` operator

When a type supports both `mutable` and `immutable` except with two different `type` implementations, a conversion from one implementation to another using the `as` operator is required where accepting the other qualifier form would not be legal.

Important caveat: even though adding an `as` operator to convert from `immutable` to `mutable` is allowed, a returned converted type will be a temporary copy which will result in any modified contents of a `mutable` type being discarded when the temporary is discarded (since changes are applied to a temporary value and not the original `immutable` version).

````zax
MyType :: type mutable {
    // ...

    operator binary 'as' final : (result : MyType immutable)(# : MyType immutable) constant = {
        // ...
    }
}

MyType :: type immutable {
    // ...

    operator binary 'as' final : (result : MyType mutable)(# : MyType mutable constant) constant = {
        // ...
    }
}

// accept the `default` mutability of a `type` (which is `mutable`)
func1 final : ()(value : MyType &) = {
    // ...
}

// accept the `mutable` version of a `type`
func2 final : ()(value : MyType mutable &) = {
    // ...
}

// accept the `immutable` version of a `type`
func3 final : ()(value : MyType immutable &) = {
    // ...
}

// accept a `constant` version of a `mutable` `type`
func4 final : ()(value : MyType constant &) = {
    // ...
}


myType1 : MyType                // pick the `default` mutability for the `type`
                                // (which in this case is `mutable`)
myType2 : MyType mutable        // pick the `mutable` version of the `type`
myType3 : MyType immutable      // pick the `immutable` version of the `type`
myType4 : MyType constant       // pick the `default` mutability for the `type`
                                // which is `mutable` but make the `type`
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
func1(myType4 as mutable)       // ERROR: `func1` expects a non-`constant` type
                                // and the `type` is already `mutable`
func2(myType4 as mutable)       // ERROR: `func2` expects a non-`constant` type
                                // and the `type` is already `mutable`
func3(myType4 as immutable)     // OKAY - the `mutable` is converted to
                                // an `immutable` `type` by the `as` operator;
                                // the `constant` qualifier becomes redundant
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


### Mutability of String types

Zax declares all `String` types as `mutable` by `default`.


### Thread safety with `immutable`

A type declared as `immutable` is not necessarily thread-safe (although making a type `immutable` can help with thread concurrency issues). A reference to an object on a different thread could exist when the original object becomes deallocated. A `type` declared as `immutable` may only contain other `immutable` types, but that `type` might contain a `handle`, or a pointer or other declarations which are not inherently thread-safe.

For `immutable` types with shared state between instances (e.g. an `String immutable` type), a `deep` qualifier can help ensure a `deep` copy of a type is performed prior a value being copied and passed to another thread. See the [concurrency](concurrency.md) section for more details.
