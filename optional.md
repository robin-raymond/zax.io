
# [Zax Programming Language](index.md)

## Optional Types

Optional values are builtin to the language. An optional value either contains a value or contains uninitialized memory. An optional value can be checked if it contains valid data or not and dereferenced when valid data is confirmed to be present inside an optional value.


### Declaring an optional type

A single question mark (`?`) following a type indicates a type's value may or may not contain valid data. Unlike a pointer, memory for a type's value is reserved for optional types but the data remains uninitialized until an optional instance is constructed.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

// `value` is declared as optional and defaults to contain uninitialized
// memory
value : MyType?
````

### Assigning a value is construction

By assigning an optional value to a value accepted by a constructor of the underlying optional value's type, a new instance of the type is constructed with that value as in input argument.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

// `valueOptional` is declared as optional and defaults to uninitialized memory
valueOptional : MyType?

value : MyType

// `value` is copy constructed into `valueOptional`'s reserved space and the
// optional value now contains valid data
valueOptional = value
````


#### Constructing an optional with no arguments construction

By assigning an optional to an empty constructed type using the multi-value operator `[{` `}]`, an optional instance will become constructed with the underlying type's default constructor in-place. A compiler will optimize the construction into a single construction of a type rather than a default construction follow by another `last` copy construction for that type.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

// `valueOptional` is declared as optional and defaults to contain uninitialized
// memory
valueOptional : MyType?

// Using a multi-value declaration (except without any values specified) will
// force the empty constructor of the underlying `type` to be called and
// the underlying type will be constructed in-place.
valueOptional = [{ }]

// Not as optimal: A temporary `MyType` instance is created and `valueOptional`
// is `last` copy constructed into the optional value's reserved space and the
// optional value now contains valid data
valueOptional = # : MyType

// Using a default value type as an assigned value would cause the optional
// value to be reset to its default uninitialized memory state and the
// underlying `type` would not be constructed (and any value currently in the
// optional `type` would be destructed if applicable)
valueOptional = :

// `valueOptional` is reset to contain uninitialized memory (and any value
// currently in the optional `type` would be destructed if applicable)
valueOptional = #
````


### Resetting the optional to uninitialized data

By assigning the optional value to it's default value (using `= #`), any contained value contained in the optional value is first destructed (if previously constructed) and then the optional value is reset to contain uninitialized memory. If an optional value did not contain any valid data then a reset of the optional value does nothing.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

// `valueOptional` is declared as optional and defaults to contain uninitialized
// memory
valueOptional : MyType?

// `value` is copy constructed into `valueOptional` reserved space and the
// optional now contains valid data
valueOptional = value

// `valueOptional` contents are destructed and reset to uninitialized data 
valueOptional = #
````


### Checking if optional contains data

An `if` statement can be used to check if an optional value contains data in the same manner a pointer can be checked if it points to valid data.

````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    value1 : Integer
    value2 : String
}

// `valueOptional` is declared as optional and defaults to contain uninitialized
// memory
valueOptional : MyType?

if valueOptional        // "false" will print
    print("true")
else
    print("false)

value : MyType

// `value` is copy constructed into `valueOptional` reserved space and the
// optional now contains valid data
valueOptional = value

if valueOptional        // "true" will print
    print("true")
else
    print("false)
````


### Dereferencing the optional

The dot (`.`) operator can be used to dereference an optional and access the optional value's contents. Care must be taken to first validate if an optional value contains valid contents otherwise a dereference operation may cause a panic or undefined behavior.

Just like pointers, optional types cannot be implicitly converted to a reference of an underlying `type`. A programmer must explicitly dereference an optional value so the programmer acknowledges the optional must contain a valid data. A compiler will not enforce a valid value must exist but runtime issues will occur if a programmer dereferences an uninitialized optional value.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

func final : ()(input : MyType) = {
    // ...
}

// `valueOptional` is declared as optional and defaults to contain
// uninitialized memory
valueOptional : MyType?

// `value` is copy constructed into `valueOptional`'s reserved space and the
// optional now contains valid data
valueOptional = value

// `valueOptional` is dereference, which converts the optional value to a
// reference to the underlying type
func(valueOptional.)

// ERROR: an optional value cannot be implicitly converted to its underlying
// type
func(valueOptional)

// `valueOptional` is declared as optional and defaults to contain uninitialized
// memory
anotherOptional : MyType?

// PANIC: a dereferenced optional type which does not contain valid data can
// cause a panic
func(valueOptional.)
````


### Defaulting an optional value to a constructed state

By assigning an optional value a new value which the underlying type accepts at construction, any previous valid contents inside an optional value are destroyed and then the optional value is reset to a constructed instance using that new value.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

// `valueOptional` is declared as optional and defaults to contain
// uninitialized memory
valueOptional : MyType?
value : MyType

value.value1 = 5
value.value2 = "beans"

// `value` is copy constructed into `valueOptional`'s reserved space and
// the optional `valueOptional` now contains valid data
valueOptional = value

// ...

value.value1 = 6
value.value2 = "bananas"

// ...

// the previous data within `valueOptional` is automatically destructed;
// `value` is copy constructed again into `valueOptional`'s reserved space;
// the optional `valueOptional` now contains valid data
valueOptional = value
````


### Resetting an optional to a new empty value

By assigning an optional value to empty construction of a type, any previous valid contents inside an optional instance are destroyed and the optional value is reset to a newly constructed instance using the default constructor.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

// `valueOptional` is declared as optional and defaults to contain uninitialized
// memory
valueOptional : MyType?
value : MyType

value.value1 = 5
value.value2 = "beans"

// `value` is copy constructed into `valueOptional` reserved space and
// the optional `valueOptional` now contains valid data
valueOptional = value

// ...

// previous data within `valueOptional` is automatically destructed;
// optional `valueOptional` constructs the underlying type with the 
// empty constructor using named value construction shorthand with
// no values specified
valueOptional = {}
````


### Resetting to a new value versus dereferencing assignment

Resetting an optional value is not identical to dereferencing an optional value with a dot (`.`) operator and then assigning the optional value to another value. In the former, any underlying `type` instance is destructed and a new `type` instance is constructed. In the latter, a reference to the underlying `type` instance is obtained and the underlying `type`'s instance uses its assignment operator to assign new contents to the existing value's reference.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

// `valueOptional` is declared as optional and defaults to contain
// uninitialized memory
valueOptional : MyType?
value : MyType

value.value1 = 5
value.value2 = "beans"

// `value` is copy constructed into `valueOptional` reserved space and
// the optional `valueOptional` now contains valid data
valueOptional = value

// ...

value.value1 = 6
value.value2 = "bananas"

// ...

// previous data within `valueOptional` is automatically destructed;
// `value` is copy constructed again into `valueOptional` reserved space;
// the optional `valueOptional` now contains valid data
valueOptional = value

// the contents of `valueOptional` are dereferenced and a reference to the
// underlying type is obtained; the underlying type uses its assignment
// operator to copy the contents from `value`
valueOptional. = value
````


### Optional values can be passed into functions


````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    value1 : Integer
    value2 : String
}

func final : ()(input : MyType?) = {
    // ...

    if input {
        print("found value: ", input.value1, input.value2)
        // ...
    } else {
        print("no value")
    }

    // ...
}

// `valueOptional` is declared as optional and defaults to contain
// uninitialized memory
valueOptional : MyType?
value : MyType

value.value1 = 5
value.value2 = "beans"

// pass in a optional containing uninitialized data
func(valueOptional)     // will print "no value"

// `value` is copy constructed into `valueOptional` reserved space and the
// optional value now contains valid data
valueOptional = value

// pass in a optional containing valid data
func(valueOptional)     // will print "found value: 5beans"

// pass a default instance value for the argument (which is an optional value
// containing uninitialized memory)
func(:)                 // will print "no value"
````
