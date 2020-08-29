
# [Zax Programming Language](index.md)

## Optional Types

Optional types are builtin to the language. An optional `type` either contains a value or contains uninitialized memory. An optional `type` can be checked if it contains valid data or not and dereferenced when the data is known to be contained inside the optional `type`.


### Declaring an optional type

A single question mark (`?`) following a type indicates the type may or may not valid. Unlike a pointer, the memory for the type is reserved for optional types but the data remains uninitialized until the object is constructed.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

// the value is declared as optional and defaults to not being initialized
value : MyType?
````

### Assigning a value is construction

By assigning an optional to a value accepted by a constructor of the underlying optional type, a new version of the type is instantiated and constructed with the argument.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

// the `valueOptional` is declared as optional and
// defaults to not being initialized
valueOptional : MyType?

value : MyType

// `value` is copy constructed into `valueOptional` reserved space and the
// optional now contains valid data
valueOptional = value
````


#### Constructing an optional with no arguments construction

By assigning an optional to an empty constructed type, the optional will become constructed with the empty default constructor. The compiler will optimize the construction into a single construction of the type rather than an empty construction follow by a `last` copy construction of the type.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

// the `valueOptional` is declared as optional and
// defaults to not being initialized
valueOptional : MyType?

// `valueOptional` is constructed using the empty constructor into the
// optional value's reserved space and the optional now contains valid data
valueOptional = : MyType

// alternative: a special shorthand is reserved to default the optional type to
// a constructed empty value (thus not requiring the type be specified)
valueOptional = {}
````


### Resetting the optional to uninitialized data

By assigning the he optional to it's default (using `=:`), any contained value is first destructed if previously constructed and the option is reset to contain uninitialized data. If the optional did not contain any valid data then the reset does nothing.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

// the `valueOptional` is declared as optional and
// defaults to not being initialized
valueOptional : MyType?

// `value` is copy constructed into `valueOptional` reserved space and the
// optional now contains valid data
valueOptional = value

// `valueOptional` contents are destructed and reset to uninitialized data 
valueOptional =:
````


### Checking if the optional contains data

An `if` statement can be used to check if an optional type contains data in the same manner a pointer can be checked if it points to valid data.

````zax
print final : ()(...) = {
    //...
}

MyType :: type {
    value1 : Integer
    value2 : String
}

// the `valueOptional` is declared as optional and
// defaults to not being initialized
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

The dot (`.`) operator can be used to dereference an optional and access the value's contents. Care must be taken to first validate the optional contains valid contents otherwise the dereference operation may cause a panic or undefined behavior.

Just like pointers, optional types cannot be implicitly converted to a reference of the underlying `type`. The programmer must explicitly dereference the `type` so the programmer acknowledges the optional must contain a valid data valid constructed `type` within this context. The compiler will not enforce a valid `type` must exist but runtime issues will occur if the programmer dereferences an uninitialized optional `type`.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

func final : ()(input : MyType) = {
    //...
}

// the `valueOptional` is declared as optional and
// defaults to not being initialized
valueOptional : MyType?

// `value` is copy constructed into `valueOptional` reserved space and the
// optional now contains valid data
valueOptional = value

// `valueOptional` is dereference, which converts the optional to a
// reference to the underlying type
func(valueOptional.)

// ERROR: an optional cannot be implicitly converted to its underlying type
func(valueOptional)

// the `valueOptional` is declared as optional and
// defaults to not being initialized
anotherOptional : MyType?

// PANIC: a dereferenced optional type which does not contain valid data
// can cause a panic
func(valueOptional.)
````


### Defaulting the optional to a constructed state

By assigning an optional to a new value the underlying type accept at construction, any previous valid contents inside the optional are destroyed and the optional is reset to a new value.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

// the `valueOptional` is declared as optional and
// defaults to not being initialized
valueOptional : MyType?

// `value` is copy constructed into `valueOptional` reserved space and
// the optional `valueOptional` now contains valid data
valueOptional = value

//...

// the previous data within `valueOptional` is automatically destructed;
// `value` is copy constructed again into `valueOptional` reserved space;
// the optional `valueOptional` now contains valid data
valueOptional = value
````


### Resetting the optional to a new empty value

By assigning an optional to an empty construction of the type, any previous valid contents inside the optional are destroyed and the optional is reset to a new empty value.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

// the `valueOptional` is declared as optional and
// defaults to not being initialized
valueOptional : MyType?

// `value` is copy constructed into `valueOptional` reserved space and
// the optional `valueOptional` now contains valid data
valueOptional = value

//...

// the previous data within `valueOptional` is automatically destructed;
// the optional `valueOptional` constructs the underlying type with the 
// empty constructor using named value construction shorthand with
// no values specified
valueOptional = {}
````


### Resetting to a new value versus dereferencing assignment

Resetting an option is not the same as dereferencing wih the dot (`.`) operator and then assigning the value to another value. In the former, any underlying `type` is destructed and a new `type` is constructed. In the latter, a reference to the underlying `type` is obtained and the `type` uses its assignment operator to assign new contents to the existing value's reference.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

// the `valueOptional` is declared as optional and
// defaults to not being initialized
valueOptional : MyType?

// `value` is copy constructed into `valueOptional` reserved space and
// the optional `valueOptional` now contains valid data
valueOptional = value

//...

// the previous data within `valueOptional` is automatically destructed;
// `value` is copy constructed again into `valueOptional` reserved space;
// the optional `valueOptional` now contains valid data
valueOptional = value

// the contents of `valueOptional` are dereferenced and a reference to the
// underlying type is obtained; the underlying type uses its assignment
// operator to copy the contents from value
valueOptional. = value
````


### Optional values can be passed into functions


````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

print final : ()(...) = {
    //...
}

func final : ()(input : MyType?) = {
    //...

    if input {
        print("found value")
        //...
    } else {
        print("no value")
    }

    //...
}

// the `valueOptional` is declared as optional and
// defaults to not being initialized
valueOptional : MyType?

// pass in a optional containing uninitialized data
func(valueOptional)     // will print "no value"

// `value` is copy constructed into `valueOptional` reserved space and the
// optional now contains valid data
valueOptional = value

// pass in a optional containing valid data
func(valueOptional)     // will print "found value"

// pass a default value for the argument
func(:)                 // will print "no value"
````
