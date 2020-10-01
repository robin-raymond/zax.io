
# [Zax Programming Language](index.md)

## Composition

### Overview

The Zax programming language does not support inheritance, virtual functions, virtual base classes, abstract classes or other features typically spawned from the Object Orientated world. However, these kind of features are possible within the Zax language and they are done in a way that does not hide these features behind compiler magic, and in a way that can remain even more powerful than their object counterparts.


### Composition and not inheritance

In computer languages inheritance is a methodology that compilers use to create hierarchical object relationships between types. But typically inheritance is merely a fancy automatic composition abstraction layered over composition relations where the inheritance structure is entirely managed by the compiler.


#### Example difference between composition and inheritance

In C++, a base class would become part of the derived class, for example:

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

An instance of `B` is a `class` of both `A` and `B` and they are said to have an [is-a](https://en.wikipedia.org/wiki/Is-a#:~:text=In%20knowledge%20representation%2C%20object%2Doriented,is%20a%20superclass%20of%20A)) relationship. A instance of a `B` `class` has access to both an `int foo` as well as an `int bar`.

In Zax, the same is possible through composition (and the object equivalent would be called a [has-a](https://en.wikipedia.org/wiki/Has-a) relationship):

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


### Merging B contains A into a single type

The keyword `own` applied to a variable name (not to be confused with `own` pointers which are part of the type of a pointer) causes the contain variable's type's declarations to be part of the type of container that owns the variable. To a Object Oriented programmer, this might look like inheritance but the composition relationship remains intact.

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

    // still perfectly legal to access the variable by its contained name
    value.a.foo = 3
    doSomething(value.a)
}
````


#### Multiple `own` variables

When more than one variable is qualified as `own` within a type, the `own` variables all become accessible as if the contained declarations of the `own` variables were part of the container's type. Some ambiguity can occur if two or more contained declarations share the same name. If the ambiguous name is referenced, the compiler will not know which contained variable was intended to be accessed. To solve this issue, the variables remain accessible from their original contained names. In other words, `own` variables are a convenience to see the contained types as a merged type but fundamentally the `own` variables retain their original contained relationship.

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

    // still perfectly legal to access the variable by its contained name
    value.a.foo = 3
    doSomething(value.a)

    value.name = "George"

    // ERROR: The variable `animal` is contained in both `own` `a` and `own`
    // `description` and thus is ambiguous.
    value.animal = "monkey"

    // Disambiguate the variable by specifying the path to the variable
    value.description.animal = "monkey"
}
````


#### Ownership of basic types and other usages

If operating on a function, variable, or an overloadable operator on a type where only a single `own` contained type exposes the requested operation, the compiler will automatically apply the operation to the `own` type. If the container type supports the operation directly then the `own` types will not be queried. If the contained type does not support the operation but multiple `own` types do support it, then the operation is ambiguous and a compiler error will occur.

````zax
MyType :: type {
    pointer own : Integer *
    data own : Integer
    realNumber : Float
}

fetchPointer : (pointer : Unknown *)() = {
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

    // ERROR: Multiple `own` variables support this operation thus the
    // operation is ambiguous
    ++value

    // ERROR: the type does not expose this operator and thus this code will
    // fail to compile
    value += value
}
````


#### Ownership inside a function

The `own` keyword can be used inside a function to cause all contained variables inside a type to be visible to the function without the need to access via the container variable. If any `own` type contained variables share the same name as local variables, the local variables will be visible directly at the scope and the `own` type contained variables will only be accessible via the container's variable.

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

    value1 = 2              // contained variable is assigned the value
    value2 = "hello"        // contained variable is assigned the value
    value3 = "Simpson"      // local variable is assigned the value
    value4 = 4              // local variable is assigned the value

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


### Overriding the defaults of `own` variables

Contained types can have their defaults changed as part of the variable ownership. The `override` keyword can change the default value of an `own` type.

````zax
print final : ()(...) = {
    // ...
}

A :: type {
    foo : Integer
    animal : String = "bear"
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
    print(value.a.animal)   // "mouse" is still printed as the
                            // `animal` value being referenced in both cases
                            // is the identical type instance
}
````


### Abstract Interfaces

While the Zax language is data focused, abstract interface style patterns within a module may be necessary. Zax supports the interface concept through the same composition principle. Functions and variables can be overridden from `own` contained variables and functions have full access to the contents of the container type. The functions declared on the conceptual abstract type need not even be defined as functions can be defaulted to point to nothing.

The `override` keyword declared on a variable that does not override another contained type's variable will force any container types to override the value and prevent the contained type from being instantiated outside of a container type.

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


### Slicing

In Object Orientated languages, [object slicing](https://en.wikipedia.org/wiki/Object_slicing) may be a concern. This happens when a base object is copied by value without carrying the contents of the derived object. Any values contained within the derived object are lost. In Object Oriented languages this can cause undefined behaviors as often a relationship between a base object and a derived object is maintained.

In Zax, the contained type is never an "is-a" relationship where the relationship would be magically hidden. The programmer knows when they are passing around a copy of a contained value or not. This might sound like a draw back but it helps prevent accidental slicing which can happen in complex C++ code where inheritance is used extensively where the value is an "is-a" relationship is exceptionally subjective verses a contained relationship.


#### Example slicing in C++

In C++, a base class would become sliced from the derived class as follows:

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
    // require A/B relationships in data the result can be undefined behavior.

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

As the Zax language does not have inheritance, upcasting and downcasting has a modified meanings. Zax uses a composition model (more of a "has-a" relationship) rather than an "is-a" relationship like Object Oriented languages. Thus the meaning of upcasting and downcasting are not identical. In object languages, the meaning is to convert type type's view from a being one of the "is-a" relationships to another. With Zax composition, the meaning is to access from the container to the contained or vice versa.

#### Upcasting (aka inner casting)

In Object Oriented languages, upcasting would imply converting the object from a derived object to a base object. In languages that have multiple inheritance allowing the same base object to become derived more than once, upcasting can require disambiguating the cast from a derived object to which duplicated base object in the object hierarchy, but the conversion is still considered a straight forward conversion process. In Zax's composition model, upcasting is performed by accessing one (or more) of types contained within a container type.

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

    // value is said to be upcasted to the `A` type, which is equivalent to
    // accessing the contained variable on the `B` type
    doSomething(value.a)
}
````


#### Downcasting

In Object Oriented languages, [downcasting](https://en.wikipedia.org/wiki/Downcasting) performs the a conversion where a base class is converted to a derived class. Unlike the upcasting scenario, downcasting is more tricky. In the upcast scenario, a derived object "is-a" base object so no (obvious) conversion is required (other than what a language constraint might impose). However, downcasting a base object is not as clear. In object languages, multiple objects can derive from a single base object. Thus a base object "might be" a particular derived object and is not guaranteed to be a particular derived object. If conversion is done in these languages, the language either requires the programmer force the type from a base object to a derived object (i.e. the programmer knows for certain they are they same object), or run-time information is contained within the object hierarchy which tells the language if the base class can be converted to a particular derived class. For this reason compilers may include "runtime type information" as part of the executable program.

In the Zax language, converting from a contained type to a container type has the same ambiguity. Zax can allow a forced conversion where the container/contained relationship is assumed, or the programmer can opt to include runtime type information as part of the type information.

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

    // since B "is-a" A the `B` class is treated as if it were an `A` class
    doSomething(&value);
}
````

##### C++ runtime downcast

C++ run time type dynamic cast conversion:

````cpp
class A {
public:
    int foo{};

    // a virtual method is required to force runtime type properties into the
    // class and the code ay be required to compile with RTTI features enabled
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

    // dynamic_cast will return a nullptr if the conversion cannot occur
    B* b = dynamic_cast<B*>(a);

    if (b) {
        // ...
    }
}

void function() {
    B value;
    value.foo = 1;
    value.bar = 2;

    // since B "is-a" A the `B` class is treated as if it were an `A` class
    doSomething(&value);
}
````


##### Zax forced downcast

In the Zax language forcefully converting from a contained type to the type's container requires sing the `outercast` operator. Using this operator must be done with caution as the programmer is forcefully telling the compiler that the composition relationship is guaranteed. If the programmer was wrong then undefined behavior will result.

An `outercast` can be ambiguous if the container type contains two (or more) of the same type which matches the type being casted. The compiler doesn't have any RTTI information necessary to make the appropriate decision as to which of the multiple instance the type is being casted from and thus would issue a `outercast-ambiguous` error. Using `outerof` on a `managed` type would resole this issue. Alternatively, consider containing one of the duplicate types inside another container to make the route to cast to the outer container type non-ambiguous.

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
    // using the `outercast` operators can be used to fast convert from a
    // contained type to a container type -- be sure the `a` is an
    // `a` type within the `B` type or this will be unsafe and will
    // result in undefined behavior
    b1 := a outercast B*

    // ERROR:
    // `a` cannot be converted to a `B*` since it's not a compatible type
    // and without RTTI properties the conversion cannot be said to be safe or
    // unsafe (thus panic detection is not possible)
    b2 := a as B*

    // UNDEFINED BEHAVIOR:
    // `a` is forced to be a type of `B*` by changing the type from
    // an `A*` type to a `B*` forcefully. Unless the memory of the contained
    // type and container type happen to be in the same location this
    // will cause undefined behavior (and should always be considered unsafe)
    b3 := a cast B*

    // ERROR:
    // `a`'s type `A` must be declared as `managed` and thus currently does not
    // include the runtime type information overhead on the type to know if
    // converting from an `A*` to a `B*` is possible
    b4 := a outerof B*
}

function final : ()() = {
    value : B
    value.a.foo = 1
    value.bar = 2

    // value is said to be upcasted to the `A` type, which is equivalent to
    // accessing the contained variable on the `B` type
    doSomething(value.a)
}
````


##### Zax runtime downcast

In the Zax language, the keyword `managed` must be included on the type declaration where a type might be used as a source of conversion using the `lifetimeof` operator. The `lifetimeof` keyword will use this overhead RTTI properties to perform a conversion if it is allowed. As runtime type information requires additional resource overhead for a given type, the `managed` keyword signals the type needs to include the overhead whenever the type is instantiated.

````zax
A :: type managed {
    foo : Integer
}

B :: type {
    bar : Integer
    a : A           // additional overhead is required on the A type
                    // to allow the conversion to happen
}

C :: type {
    weight : Double
}

doSomething final : ()(a : A*) = {
    // UNSAFE:
    // using the `outercast` operators can be used to fast convert from a
    // contained type to a container type -- be sure the `a` is an
    // `a` type within the `B` type or this will be unsafe and will
    // result in undefined behavior
    b1 := a outercast B*

    // ERROR:
    // while `a` can be converted to a `B*` additional RTTI overhead is required
    // to perform the conversion thus the compiler forces the `outerof`
    // operator to be used to acknowledge the overhead requirements (which are
    // normally not present for simple pointer math offset conversions)
    b2 := a as B*

    // UNDEFINED BEHAVIOR:
    // `a` is forced to be a type of `B*` by changing the type from
    // an `A*` type to a `B*` forcefully. Unless the memory of the contained
    // type and container type happen to be in the same location this
    // will cause undefined behavior (and should always be considered unsafe)
    b3 := a cast B*

    // SAFEST:
    // probe `a` to see if it is indeed within a `B` type and if so then
    // return a pointer to a B type
    b4 := a outerof B*

    if b4 {
        // ...
    }

}

function final : ()() = {
    value : B
    value.a.foo = 1
    value.bar = 2

    // value is said to be upcasted to the `A` type, which is equivalent to
    // accessing the contained variable on the `B` type
    doSomething(value.a)
}
````
