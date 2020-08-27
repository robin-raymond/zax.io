
# [Zax Programming Language](index.md)

## [Constructors and Destructors](ctor-dtor.md)

### Basic constructors and destructors

Default initialization, default allocation, initialization with function calls, and `defer` mostly eliminate the need for constructors and destructors. However, some types might require additional steps to prepare the type or the programmer has decided that ensuring some guaranteed pre/post configuration is a good model for their code. Whatever the reason, constructors adn destructors can be added to a type which are always executed upon construction and always executed at destruction.

The triple plus `+++` and triple minus `---` represent reserved function names on types representing their construction and destruction methods to call. Constructors are polymorphic, meaning they can have more than one constructor that is selected based on the construction arguments passed into the type. Constructors can be present without destructors and vice versa.

Constructs and destructors never have return values when called. Zax does not support exceptions, thus the language cannot throw any exceptions either and errors cannot be returned during the construction or destruction process. Constructors and destructors cannot be deferred asynchronously to another thread as they must execute and complete from the thread they are called.

````zax
print : ()(...) = {
    //....
}

generateRandomUuid : (Uuid uuid)() = {
    //...
}

follow : ()(...) = {
    //...
}

unfollow : ()(...) = {
    //...
}

MyType :: type {

    uuid := generateRandomUuid()

    +++ final : ()() = {
        follow(_, uuid)
    }

    +++ final : ()(password : String) = {
        follow(_, uuid, password)
    }

    --- final : ()() = {
        unfollow(_, uuid)
    }
}

MyOtherType :: type {
    // the contained type will be constructed manually
    containedType own : MyType

    +++ final : ()() = {
        // the compiler will register the constructor of the `containedType`
        // has been called manually thus the default constructor will not be
        // called
        containedType.+++("my voice is my passport")
    }
}

````

### Failing to construct a contained type

The compiler

````zax
print : ()(...) = {
    //....
}

generateRandomUuid : (Uuid uuid)() = {
    //...
}

blueMoon : (result : Boolean)() = {
    //...
}

venusInRetrograde : (result : Boolean)() = {
    //...
}

follow : ()(...) = {
    //...
}

unfollow : ()(...) = {
    //...
}

MyType :: type {

    uuid := generateRandomUuid()

    +++ final : ()() = {
        follow(_, uuid)
    }

    +++ final : ()(password : String) = {
        follow(_, uuid, password)
    }

    --- final : ()() = {
        unfollow(_, uuid)
    }
}

MyOtherType :: type {
    containedType own : MyType

    +++ final : ()() = {
        if blueMoon()
            containedType.+++("my voice is my passport")

        if venusInRetrograde() {
            // UNDEFINED BEHAVIOR
            // What happens if it's a blue moon AND venus is in retrograde?
            // Answer: illegal calling of the contained type's constructor
            // multiple times!
            //
            // While the compiler will register the manual construction of the
            // contained type, the compiler will not prevent the constructor
            // from being called multiple time by accident (which is
            // undefined behavior)
            containedType.+++("mars and the sunshine kid")
        }
        
        // UNDEFINED BEHAVIOR
        // unsafe to access the value `uuid` within the `containedType` as
        // the contained type may not been constructed at all if it's not a
        // blue moon nor venus is in retrograde
        //
        // the compiler has registered that the contained type's constructor
        // is called manually in the code and thus will assume the programmer
        // has setup every code path possible to construct the contained type
        print("following the contained type", uuid)
    }
}

````

### Ensuring contained type's values are only accessed after construction

Only access variables from contained types after automatic or manual construction of contained type is performed. Accessing non-constructed contained types can cause undefined behavior.

````zax
print : ()(...) = {
    //....
}

generateRandomUuid : (Uuid uuid)() = {
    //...
}

follow : ()(...) = {
    //...
}

unfollow : ()(...) = {
    //...
}

MyType :: type {

    uuid := generateRandomUuid()

    +++ final : ()() = {
        follow(_, uuid)
    }

    +++ final : ()(password : String) = {
        follow(_, uuid, password)
    }

    --- final : ()() = {
        unfollow(_, uuid)
    }
}

MyOtherType :: type {
    containedType own : MyType

    +++ final : ()() = {
        // UNDEFINED BEHAVIOR:
        // accessing values on a contained type that has not been
        // constructed yet results in undefined behavior
        print("following the contained type", uuid)

        containedType.+++("my voice is my passport")

        // safe to access the value `uuid` within the `containedType` as
        // the contained type has been constructed
        print("following the contained type", uuid)
    }
}

````


### Tricking the compiler to acknowledge a constructor was called

The compiler will scan the constructor for manual calls to contained type's constructors. For ever constructor the compiler sees, the automatic code to construct the contained type is bypassed. This can be used to force the compiler to acknowledge externally performed construction/destruction here the compiler would not be able to recognize the external construction/destruction otherwise.

````zax
print : ()(...) = {
    //....
}

generateRandomUuid : (Uuid uuid)() = {
    //...
}

follow : ()(...) = {
    //...
}

unfollow : ()(...) = {
    //...
}

MyType :: type {

    uuid := generateRandomUuid()

    +++ final : ()() = {
        follow(_, uuid)
    }

    +++ final : ()(password : String) = {
        follow(_, uuid, password)
    }

    --- final : ()() = {
        unfollow(_, uuid)
    }
}

magicFunction : ()(pointer : MyType*) = {
    //...
    pointer.+++("magic value")
    //...
}

MyOtherType :: type {
    containedType own : MyType

    +++ final : ()() = {
        // obtain a pointer to the contained type
        pointerContainedType :* = containedType

        // call the magic function that will cause the pointed to contained
        // type to be constructed as part of that function
        magicFunction(pointerContainedType)

        never final : ()(result : Boolean) = { return false }

        // the compiler will be unaware the construction of the contained
        // type was performed inside the `magicFunction` so to prevent the
        // compiler for calling the default constructor on `containedType`
        // force the compiler to see the type as already having been
        // manually constructed (even though the never clause will never
        // actually execute the constructor manually here)
        if never()
            containedType.+++()
    }

}

````


### Control the destruction order of contained types

The compiler will normally generate the code to destruct contained types automatically. However, if precise control over the order of destruction is needed other than the default FILO construction / destruction then manual destruction can be performed.The compiler will scan the destructor for manual destruction of contained types and suppress the default destruction of contained types.

````zax
print : ()(...) = {
    //....
}

generateRandomUuid : (Uuid uuid)() = {
    //...
}

follow : ()(...) = {
    //...
}

unfollow : ()(...) = {
    //...
}

MyType :: type {

    uuid := generateRandomUuid()

    +++ final : ()() = {
        follow(_, uuid)
    }

    --- final : ()() = {
        unfollow(_, uuid)
    }
}

MyOtherType :: type {
    containedTypeA own : MyType
    containedTypeB : MyType

    --- final : ()() = {
        // normally `containedTypeB` would be destructed prior to
        // `containedTypeB` but manual destruction can be applied
        containedTypeA.---()
        containedTypeB.---()
    }

}

````


### Manual allocation and construction

Allocation and construction can be separated.

````zax
print : ()(...) = {
    //....
}

generateRandomUuid : (Uuid uuid)() = {
    //...
}

follow : ()(...) = {
    //...
}

unfollow : ()(...) = {
    //...
}

MyType :: type {

    uuid := generateRandomUuid()

    +++ final : ()() = {
        follow(_, uuid)
    }

    +++ final : ()(password : String) = {
        follow(_, uuid, password)
    }

    --- final : ()() = {
        unfollow(_, uuid)
    }
}

MyOtherType :: type {
    containedTypeA : MyType own @
    containedTypeB : MyType @

    +++ final : ()() = {

        // both `containedTypeA` and `containedTypeB` are allocated
        // as part of the construction process but they either of these
        // can have their constructor manually called

        containedTypeB.+++("my voice is my passport")

        // only `containedTypeB` will be manually constructed whereas the
        // `containedTypeA` will be allocated and constructed automatically
    }
}

````

### Default constructors

Constructors on types need not be implemented. The compiler will create a few constructors automatically, namely an empty constructor (allowing the type to be instantiated without an assignment), and a copy constructor will be created. If any of the contained types support `deep` copy construction or `last` copy construction, variation of those types of constructors will be created as well if referenced.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

// a default empty constructor is created
myType1 : MyType

// a default copy constructor is created
myType2 : MyType = myType1

// a default deep copy constructor is created
myType3 : MyType = myType2 as deep
````

#### Disabling of default constructors

Constructors can be disabled by declaring a constructor as `final` that should not exist and assigning the pointer value to nothing. Any type attempting to access that version of the constructor will be disallowed at compile time since the compiler will recognize that the constructor cannot be called.

##### Disabling the default empty constructor

The default empty constructor can be disabled by the following method:

````zax
MyType :: type {
    value1 : Integer
    value2 : String

    // the default empty constructor is disabled
    +++ final : ()()

    +++ final : ()(value : Integer) = {
        value1 = value
    }
}

// ERROR: a default empty constructor is not available
myType1 : MyType

// use the custom constructor to instantiate the instance
myType2 : MyType = 42

// a default copy constructor is created
myType3 : MyType = myType2

// a default deep copy constructor is created
myType4 : MyType = myType2 as deep
````


##### Disabling the default copy constructor

The default copy constructor can be disabled by the following method:

````zax
MyType :: type {
    value1 : Integer
    value2 : String

    // the default copy constructor is disabled
    +++ final : ()( : MyType& constant)
}

// a default empty constructor is created
myType1 : MyType

// ERROR: the copy constructor is disabled
myType2 : MyType = myType1

// ERROR: the copy constructor is disabled
myType3 : MyType = myType1 as deep
````


##### Disabling alternative `deep` and `last` copy constructors

The default alternative `deep` and `last` copy constructor variations can be disabled by the following method:

````zax
MyType :: type {
    value1 : Integer
    value2 : String

    // the default copy constructor is still available since that version
    // of the function can be automatically generated still

    +++ final : ()( : MyType& last)
    +++ final : ()( : MyType& deep constant)
}

// a default empty constructor is created
myType1 : MyType

// a default copy constructor is created
myType2 : MyType = myType1

// ERROR: the `deep` copy constructor is disabled
myType3 : MyType = myType1 as deep
````


##### Enabling only alternative `deep` and `last` copy constructors

A default copy constructor can be disabled but the default alternative `last` and `deep` constructors can be automatically created by applying the `default` keyword as exampled in the following:

````zax
MyType :: type {
    value1 : Integer
    value2 : String

    // the default copy constructor is still available since that version
    // of the function can be automatically generated still

    +++ final : ()( : MyType& constant)
    +++ final : ()( : MyType& last) = default
    +++ final : ()( : MyType& deep constant) = default

    // INCORRECT: this version would not create a default as the =: would
    // cause the type to point to an empty version of its own type (i.e.
    // a function pointer to nothing)
    // +++ final : ()( : MyType& deep constant) =:
}

// a default empty constructor is created
myType1 : MyType

// ERROR: the default copy constructor is disabled
myType2 : MyType = myType1

// The `deep` copy constructor was declared
myType3 : MyType = myType1 as deep
````


### Named initialization as an alternative to constructors with arguments

Rather than creating a constructor that takes arguments for all the data being initialized, or worse, creating a polymorphic set of constructors for each variant of arguments that a type might want initialized, named value initialization can be used. When a type is instantiated, the contained arguments can be initialized with values by specifying the names of each value to initialized prefixed with a dot operator `.` and the value to initialize. All initialized values must be enclosed in curly braces `{}` so the are distinguished for a constructor argument list. Named arguments need not be in the same order as their listing in the type being initialized.

Named value initialization cannot be mixed with constructor arguments at the same time for the same type. Either values are initialized by name or they are constructed using constructor but not both.

````zax
Animal :: type {
    animal : String
    legs : Integer
    canFly : Boolean
    slimy : Boolean
}

animal1 : Animal = {.animal = "bear", .legs = 2}
animal2 : Animal = {.animal = "spider", .legs = 8}
animal3 : Animal = {.animal = "bird", .canFly = true, .legs = 2}
animal4 : Animal = {.animal = "worm", .slimy = true}
````