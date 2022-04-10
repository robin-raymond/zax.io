
# [Zax Programming Language](index.md)

## Composition

### Overview

The Zax programming language does not support inheritance, virtual functions, virtual base classes, abstract classes or other features typically spawned from an Object Orientated world. However, these kind of features are still possible within the Zax language and they are done in a way that does not hide these features behind compiler magic, and in a way that can remain even more flexible than their object counterparts.


### Composition and not inheritance

In computer languages inheritance is a methodology that compilers use to create hierarchical object relationships between types. But typically inheritance is merely a fancy automatic composition abstraction layered over composition relations where an inheritance structure is entirely managed by a compiler. Zax has explicit composition models where all types are described and relationships are fully exposed and understood.


#### Example difference between composition and inheritance

In C++, a base class would become part of a derived class, for example:

````cpp
class A {
public:
    int foo{};
};

class B : public A {
public:
    int bar{};
};

void doSomething(A *a) {
    // ...
}

void function() {
    B value;
    value.foo = 1;
    value.bar = 2;

    doSomething(&value);
}
````

An instance of `B` is a `class` of both `A` and `B` and they are said to have an [is-a](https://en.wikipedia.org/wiki/Is-a) relationship. A instance of a `B` `class` has access to both a `foo` as well as a `bar`.

In Zax, the same structure is possible through composition (and the object equivalent would be called a [has-a](https://en.wikipedia.org/wiki/Has-a) relationship):

````zax
A :: type {
    foo : Integer
}

B :: type {
    a : A
    bar : Integer
}

doSomething final : ()(a : A) = {
    // ...
}

function final : ()() = {
    value : B
    value.a.foo = 1
    value.bar = 2

    doSomething(value.a)
}
````

In Zax, the type `B` contains the type `A`. They are not the same object like C++ as they are two different and unique types. In Zax, `B` contains a type `A` within itself.


### Merging a has-a relationship into single type

A keyword `own` applied to a variable name (not to be confused with `own` pointers which are a type of a pointer) causes a `type`'s contained variables to viewed as if the variables were part of the `type` itself. To a Object Oriented programmer, this might look like inheritance but the composition relationship remains unbroken.

````zax
A :: type {
    foo : Integer
}

B :: type {
    a own : A
    bar : Integer
}

doSomething final : ()(a : A) = {
    // ...
}

function final : ()() = {
    value : B
    value.foo = 1
    value.bar = 2

    doSomething(value)

    // still perfectly legal to access a variable by its contained name
    value.a.foo = 3
    doSomething(value.a)
}
````


#### Multiple `own` variables

When more than one variable is qualified as `own` within a `type`, all `own` variables all become accessible as if contained variables were part of a container's `type`. Some ambiguity can occur if two or more contained variables share a name. If ambiguity exists, a compiler will not know which contained variable was intended to be accessed. To solve this issue, all variables remain accessible from their original sub-contained names. In other words, `own` variables are a convenience to access contained types (as if they are merged types) but fundamentally `own` variables retain their original contained `has-a` relationship.

````zax
A :: type {
    foo : Integer
    animal : String
}

Description :: type {
    name : String
    animal : String
}

B :: type {
    a own : A
    description own : Description
    bar : Integer
}

doSomething : ()(a : A) = {
    // ...
}

function final : ()() = {
    value : B
    value.foo = 1
    value.bar = 2

    // not ambiguous thus value is understood to be an `A` implicitly
    doSomething(value)

    // still perfectly legal to access a variable by its contained name
    value.a.foo = 3
    doSomething(value.a)

    value.name = "George"

    // ERROR: a variable `animal` is contained in both `own` `a` and `own`
    // `description` and thus is ambiguous
    value.animal = "monkey"

    // disambiguate a variable by specifying its path
    value.description.animal = "monkey"
}
````


#### Ownership of basic types and other usages

If accessing a function, variable, or an operator on a `type` where only a single `own` contained type exposes a matching operation, a compiler will automatically apply an operation to an `own` type. If a container type supports an operation directly then any `own` types will not be queried. If a contained type does not support an operation but multiple `own` types do support it then that operation is ambiguous and a compiler `own-relationship-access-ambiguous` error will occur.

````zax
MyType :: type {
    pointer own : Integer *
    data own : Integer
    realNumber own : Float
}

fetchPointer : (pointer : Integer *)() = {
    // ...
}

function final : ()() = {
    value : MyType

    // as each of these are assignment operations using strict types, none
    // of these operations are ambiguous nor are the operations supported
    // directly on the container type (thus all will compile)
    value += 5
    value += 5.0
    value = fetchPointer()

    // ERROR: multiple `own` variables support this operation thus the
    // operation is ambiguous
    ++value

    // ERROR: the `type` (nor any `own` variables) does not expose this operator
    // and thus this code will fail to compile
    value += value
}
````


#### Ownership inside a function

An `own` keyword can be used inside a function to cause a function variable's type's contained variables to be visible inside a function without a need to access the value via the container variable. If any `own` variables contained variables share a name as local variables then local variables will be visible. An `own` variable's type's contained variables will only be accessible via its container variable.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
    value3 : Integer
    value4 : Integer
}

func final : ()() = {
    myType own : MyType
    value3 : String = "Marge"
    value4 : Integer = 5

    value1 = 2              // contained variable is assigned a value
    value2 = "hello"        // contained variable is assigned a value
    value3 = "Simpson"      // local variable is assigned a value
    value4 = 4              // local variable is assigned a value

    if myType.value1 == 2
        print("this will display")

    if myType.value2 == "hello"
        print("this will display")

    if value3 == "Simpson"
        print("this will display")

    if myType.value3 == 0
        print("this will display")

    if value4 == 4
        print("this will display")

    if myType.value4 == 0
        print("this will display")
}
````


### Overriding defaults of `own` variables

Contained types can have their defaults changed as part of ownership. An `override` keyword can change a default value of an `own` type.

````zax
print final : ()(...) = {
    // ...
}

A :: type {
    foo : Integer

    // animal can be overridden is desired and contains a default value if not
    animal override : String = "bear"
}

B :: type {
    a own : A
    bar : Integer

    animal override := "mouse"
}

function final : ()() = {
    value : B
    value.foo = 1
    value.bar = 2

    print(value.animal)     // "mouse" will be printed not "bear"
    print(value.a.animal)   // "mouse" is still printed as the `animal`
                            // value being referenced in both cases is the
                            // identical type instance
}
````


### Abstract Interfaces

While the Zax language is data focused, abstract interface style patterns within a module may be necessary. Zax supports an interface concept through its composition model. Functions and variables can be overridden from `own` contained variables and functions have full access to contents of a contained `type`. Functions declared on an abstract `type` need not even be defined as functions can by default point to `Nothing`.

An `override` keyword declared on a variable that does not `override` another contained `type`'s variable (or does not contain a default value) will force any container types to `override` its value. This prevents a contained `type` with a non defaulted `override` from being instantiated outside without being overridden inside another container `type`.

````zax
MyInterface :: type {
    start override : ()()
    stop override : ()()
    ping override : ()(timeout : Integer)
    add override : (result : Integer)(value : Integer)

    myValue : Float
}

MyType :: type = {
    myInterface own : MyInterface

    start override := {
        // ...
    }
    stop override := {
        // ...
    }
    ping override := {
        // ... (variable `timeout` is accessible) ...        
    }

    myValue override := 5.0     // not forced to override but is
                                // allowed to override

    meaningOfLife := 42         // new variable declared separate from interface

    // ERROR: the `add` variable was declared with `override` but the
    // container type does not override the variable's value
}
````


### Abstract Interfaces Ambiguity

An `override` keyword declared on a variable that does not `override` another contained `type`'s variable (or does not contain a default value) will force any container types to `override` its value. This prevents a contained `type` with a non defaulted `override` from being instantiated outside without being overridden inside another container `type`. In some cases, these overrides can be ambiguous if two interfaces share a name.

````zax
MyInterface1 :: type {
    start override : ()()
    stop override : ()()
    ping override : ()(timeout : Integer)
    add override : (result : Integer)(value : Integer)

    myValue override : Float = #
}

MyInterface2 :: type {
    start override : ()()
    stop override : ()()
}

MyType :: type = {
    myInterface1 own : MyInterface1
    myInterface2 own : MyInterface2

    // to disambiguate between `start` from `MyInterface1` and `MyInterface2`,
    // the fully scoped variable name is used within the context of the
    // appropriate interface
    myInterface1.start override := {
        // ...
    }
    myInterface1.stop override := {
        // ...
    }

    myInterface2.start override := {
        // ...
    }
    myInterface2.stop override := {
        // ...
    }

    ping override := {
        // ... (variable `timeout` is accessible) ...        
    }

    myValue override := 5.0     // not forced to override but is allowed
                                // to override

    meaningOfLife := 42         // new variable declared separate from interface

    // ERROR: the `add` variable was declared with `override` but the
    // container type does not override the variable's value
}
````


### Slicing

In Object Orientated languages, [object slicing](https://en.wikipedia.org/wiki/Object_slicing) may be a concern. This happens when a base object is copied by-value without copying the contents of a derived object. Any values contained within a derived object are lost in a copy operation. In Object Oriented languages this can cause undefined behaviors as often a relationship between a base object and a derived object is maintained.

In Zax, a contained type is never an "is-a" relationship where any relationship would be magically hidden. A programmer knows when they are passing around a copy of a contained value (without a magic relationship). This might sound like a draw back but it helps prevent accidental slicing which can happen in complex C++ code where inheritance is used extensively. The value is an "is-a" relationship is exceptionally subjective whereas a "has-a" relationship has clearer boundaries between container and contained types.


#### Example slicing in C++

In C++, a base class could become sliced from its derived class as follows:

````cpp
class A {
public:
    int foo{};
};

class B : public A {
public:
    int bar{};
};

void doSomething(A a) {

    // BUG OR FEATURE?

    // A copy instance of `A` is created but when a `B` object was passed in
    // then the contents of `B` are lost and `A` becomes separated from `B`.
    // For simple types, this may not be a concern but for complex types that
    // require A/B relationships in data can result can be undefined behavior.

    // ...
}

void function() {
    B value;
    value.foo = 1;
    value.bar = 2;

    doSomething(value);
}
````


### Downcasting and upcasting (aka outer and inner casting)

As the Zax language does not have inheritance, upcasting and downcasting has a modified meanings. Zax uses a composition model (i.e. a "has-a" relationship) rather than an "is-a" relationship like Object Oriented languages. Thus the meaning of upcasting and downcasting are not identical in Zax verses object oriented languages. In object oriented languages, the meaning is to convert a type's object view from a being one of an "is-a" relationships to another object's view of an object. With Zax composition, the meaning is to access from a container value to a contained value (or vice versa).


#### Upcasting (aka inner casting)

In object oriented languages, upcasting would imply converting an object from a derived object to a base object. In languages that have multiple inheritance allowing a base object to become derived more than once, upcasting can require disambiguating when cast from a derived object to a duplicated base object (in the object hierarchy). This conversion is still considered a straight forward. In Zax's composition model, upcasting is performed by accessing one (or more) of a `type`'s contained values.

For example, in C++:

````cpp
class A {
public:
    int foo{};
};

class B : public A {
public:
    int bar{};
};

void doSomething(A *a) {
    // ...
}

void function() {
    B value;
    value.foo = 1;
    value.bar = 2;

    // since B "is-a" A the `B` class is treated as if it were an `A` class
    doSomething(&value);
}
````

Or in the Zax language:

````zax
A :: type {
    foo : Integer
}

B :: type {
    a : A
    bar : Integer
}

doSomething : ()(a : A *) = {
}

function final : ()() = {
    value : B
    value.a.foo = 1
    value.bar = 2

    // value is said to be upcasted to an `A` type, which is equivalent to
    // accessing a contained variable named `a` on a `B` type
    doSomething(value.a)
}
````


#### Downcasting

In object oriented languages, [downcasting](https://en.wikipedia.org/wiki/Downcasting) performs a conversion from a base object to a derived object. Unlike an upcasting scenario, downcasting is trickier. In a simple upcast scenario, a derived object "is-a" base object so no conversion is required (other than what a language constraint might impose). However, downcasting a base object to a derived object is not as clear.

In object oriented languages, multiple objects can derive from a single base object. Thus a base object may or may not be a particular derived object. Any conversion relationship between a base to a derived object is not guaranteed to be proper if more than one derived object exists derived from a base object. If conversion is done in object oriented languages, a language either requires a programmer force a type from a base object to a derived object (i.e. a programmer knows for certain which base/derived object relationship is proper), or runtime information is required in an object hierarchy to verify an base/derived relationship exists prior to any conversion. For this reason compilers may include RTTI (Run Time Type Information) as part of an executable program.

In the Zax language, converting from a contained `type` to a container `type` has the same ambiguity as object oriented language. Zax can allow a forced conversion where a container/contained relationship is assumed by a programmer, or a programmer can opt to include specific RTTI information as part of a type's information to assist in validating container/contained relationships.

##### C++ forced downcast

C++ forced downcast example:

````cpp
class A {
public:
    int foo{};
};

class B : public A {
public:
    int bar{};
};

class C : public A {
public:
    double weight{};
};

void doSomething(A* a) {
    // ...

    // covert from A to B and assume that A "is-a" B and not a C
    B* b = static_cast<B*>(a);
}

void function() {
    B value;
    value.foo = 1;
    value.bar = 2;

    // since B "is-a" A, the `B` class is treated as if it were an `A` class
    doSomething(&value);
}
````


##### C++ runtime downcast

C++ run time type dynamic cast conversion:

````cpp
class A {
public:
    int foo{};

    // a virtual method is required to force runtime type properties into a
    // class and C++ compiler flags may be required to compile with RTTI
    // features enabled
    virtual ~A() {}
};

class B : public A {
public:
    int bar{};
};

class C : public A {
public:
    double weight{};
};

void doSomething(A* a) {
    // ...

    // dynamic_cast will return a nullptr if a conversion cannot occur
    B* b = dynamic_cast<B*>(a);

    if (b) {
        // ...
    }
}

void function() {
    B value;
    value.foo = 1;
    value.bar = 2;

    // since B "is-a" A, the `B` class is treated as if it were an `A` class
    doSomething(&value);
}
````


##### Zax forced downcast

In the Zax language forcefully converting from a contained `type` to a `type`'s container requires using an `unsafe outer of` operator. Using this operator must be done with caution as a programmer is forcefully telling a compiler that a composition relationship is guaranteed between two distinct types. If a programmer was wrong then undefined behavior will result.

An `unsafe outer of` can be ambiguous if converting from a `type` which exists in two or more variables within a container `type`. A compiler won't have information necessary (outside of a `managed` type) to make an appropriate decision to decide which contained value is being converted to its container `type` (and thus a compiler would issue an `unsafe-outer-of-ambiguous` error). Using `outer of` on a `managed` type would resolve this issue. Alternatively, consider containing duplicate types inside another unique container to make a clear method to convert first to a unique container then to a container `type` (thus removing ambiguity for a compiler).

````zax
A :: type {
    foo : Integer
}

B :: type {
    bar : Integer
    a own : A
}

C :: type {
    weight : Double
}

doSomething : ()(a : A*) = {
    // UNSAFE:
    // using an `unsafe outer of` operator can be used to force convert from a
    // contained `type` to a container `type` -- be sure `a` is an `A` `type`
    // contained within a `B` type or this conversion will be unsafe and
    // undefined behaviors may result
    b1 := a unsafe outer of B*

    // ERROR:
    // `a` cannot be converted to a `B*` since it's not a compatible type;
    // without RTTI properties, this conversion cannot be said to be safe or
    // unsafe (as detecting a failure case is impossible)
    b2 := a as B*

    // ERROR:
    // `a`'s type `A` must be declared as `managed` and thus currently does not
    // include the runtime type information overhead on the type to know if
    // converting from an `A*` to a `B*` is safe
    b3 := a outer of B*
}

function final : ()() = {
    value : B
    value.a.foo = 1
    value.bar = 2

    // value is said to be upcasted to an `A` type, which is equivalent to
    // accessing a contained variable on a `B` type
    doSomething(value.a)
}
````


##### Zax runtime downcast

In the Zax language, a `managed` keyword must be included on a type declaration where a type might be used as a source of conversion using an `outer of` operator. An `outer of` operator will include type overhead to include RTTI properties to enable `outer of` conversion safety checks. As runtime type information requires additional resource overhead for a given type, a `managed` keyword signals a type needs to include that overhead whenever a type is instantiated.

````zax
// additional overhead is required on an `A` type to convert from a contained
// `type` to an outer container `type`
A :: type managed {
    foo : Integer
}

B :: type {
    bar : Integer
    a : A           
}

C :: type {
    a : A
    weight : Double
}

doSomething final : ()(a : A*) = {
    // UNSAFE:
    // using the `unsafe outer of` operators can be used to force convert from a
    // contained type to a container type -- be sure the `a` is an `A` `type`
    // within a `B` `type` or this will be unsafe and may result in undefined
    //  behavior
    b1 := a unsafe outer of B*

    // ERROR:
    // while `a` can be converted to a `B*` additional RTTI overhead is required
    // to perform a conversion thus a compiler forces an `outer of` operator to
    // be used to acknowledge any overhead requirements (which is normally not
    // present for much simpler pointer math offset conversions)
    b2 := a as B*

    // SAFEST:
    // probe `a` to see if the instance of `a` is indeed within a `B` `type` and
    // conditionally return a pointer to a `B` type
    b3 := a outer of B*

    if b3 {
        // ...
    }

}

function final : ()() = {
    value1 : B
    value1.a.foo = 1
    value1.bar = 2

    // `value1` is said to be upcasted to an `A` type, which is equivalent to
    // accessing a contained variable on a `B` type
    doSomething(value1.a)

    value2 : C
    value2.a.foo = 1
    value2.weight = 5.0

    // `value1` is said to be upcasted to an `A` type, which is equivalent to
    // accessing a contained variable on a `C` type
    doSomething(value2.a)
}
````
