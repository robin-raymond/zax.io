
# [Zax Programming Language](index.md)

## Namespacing and Importing Modules

### `import` basics

The `import` keyword is used to declare a module import into the current namespace.

In the example below, the `Definition.Container.Module` is presumed to be a previously defined module declaration which declares how to locate a module for importing into the source code. Once located, all the types found are dumped into the namespace declared by the import module name (which is `MyNamespace` in this scenario).

````zax
// import all the types and variables found in the module described by
// `Definition.Container.Module` and place those symbols into the 
// `MyNamespace` declaration
MyNamespace :: import Definition.Container.Module

// declare an instance of `value` using a hypothetical imported type
value : MyNamespace.ImportedType

// call a hypothetical function imported into `MyNamespace`
MyNamespace.doSomething()
````

### Importing symbols into the global namespace

By not specifying an importation declaration name, all symbols are imported into the global namespace.

````zax
// import all the types and variables found in the module described by
// `Definition.Container.Module` and place those symbols into the 
// global namespace
:: import Definition.Container.Module

// declare an instance of `value` using a hypothetical imported type
value : ImportedType

// call a hypothetical function imported into the global namespace
doSomething()
````

#### Conflicting importations into a namespace

If two (or more) modules are imported into the same namespace (including the global namespace), the first definition of a type or variable wins and the later imported type is hidden from view. The later hidden imported type exists, but only the module that imported that type has knowledge of that type's existence.

````zax
Frog :: type {
    commonName : String
    scientificName : String
    subspecies : String
}

// assume that the imported `Definition.Animals` already contains a
// type named `Frog`
:: import Drawing.Animals

frog : Frog

// ERROR: the programmer assumed the declared `frog` was the
// imported `Frog` type from `Drawing.Animals` but a type named
// `Frog` already existed prior to import and thus
// the imported `Frog` type is no longer accessible
frog.display()
````

### Importing intrinsic types

Prior to the compilation of the first line of code, a definition for all intrinsic types exists. However, an importation of these types must be done by importing their built-in module declaration placed into the automatic-declared `Module` namespace.

System types are not defaulted to allow potential importation aliasing as desired and to not pollute the global namespace with declarations that may not be desired to exist. The presumption is that most code will import their needed `System` modules into the global namespace.

Because of the way modules self isolate from each other, one module importing `System` types into the global namespace will have no impact to other modules importing into their namespace.

````zax
:: import Module.System.Types
````

### Exporting types

By default, symbols in code are not exported beyond the boundary of their originating source. However, the `export` keyword can be applied to variables, types, aliases, and even imports to cause symbols to become visible to an imported module. The compiler can allow all global symbols to be exported from source files but this is not recommended. Contained types and variables are all visible unless they are hidden. Local functions inside others functions are effectively hidden (unless they become returned from or passed into a function).

````zax
// nothing imported from this module will be exported (despite being imported
// into the global namespace)
:: import Module.System.Types

myInteger : Integer         // symbol is only accessible by this source

canYouSeeMe export : String // symbol is exported

Cos1Tan2Arc4Sin :: type {
    value1 : Float
    value2 : String
    value3 : Integer
}

// export `Cos1Tan2Arc4Sin` type under an alias of `FriendlyName`
FriendlyName export :: alias Cos1Tan2Arc4Sin

// all exported symbols found when importing `Module.Useful.FileUtilities` are
// re-exported to any module importing this code.
FileUtilities export :: import Module.Useful.FileUtilities
````

#### Hiding exports

Even though a type might be exported, some contained types or variables should be kept hidden from the importer of the model. The language includes the `hidden` and `private` keywords that can help keep types and variables out of view. Take note that these keywords are not named `secret` or `protect` or anything related as they are meant only to keep namespaces from becoming polluted with types and variables that are meant for internal house keeping. Nothing in the language protects a pointer to the type being taken and the raw memory contents of a type being read byte by byte. No cryptographic promises are implied by usage of the `hidden` or `private` keywords.

````zax
:: import Module.System.Types

MyType export :: type {
    value1 : Float              // symbol is visible
    value2 : String             // symbol is visible
    value3 private : Integer    // symbol is private to everything except
                                // functions and types within this type

    value4 hidden : String      // symbol is accessible to other types within
                                // the same set of sources but is not visible
                                // when exported

    addOne final :: ()() = { ++value3 }
}

content : MyType

content.value1 = 4.3
content.value2 = "Hello world!"
content.addOne()                    // value3 is not accessible outside the type
content.value4 = "Goodbye world!"   // value3 is accessible but
                                    // hidden when exported
````

### Defining a new `import` module

A module is a collection of Zax source code that can be fetched and imported as an imported module of types. Once fetched, a module is cached locally where the cached files can be kept as known assets for an official build. Only if the module's definition changes is the cached module refreshed otherwise the cached version is used. For example, if a repository tag was changed the old cached repository would be flushed and replaced with a new cached repository. A commit update on a repository branch will not automatically refresh any cached contents but the `id` field on the module definition can be updated to force a refresh of a cached imported module. Alternatively, deleting the imported module from the cache will cause the cache to refresh the module.

````zax
FastFooMathModule :: type {
    type once final : constant = "git"
    repository once final : constant = "https://github.com/immmutable/repo",
    
    // tag once final : constant = "Version1.2"      // not using a tag
    branch once final : constant = "development"

    // a hash validation of the content can be performed (but is optional)
    hashAlgorithm final : constant = "SHA512"
    hash once final : constant = "f3b85c040bae9b5ce8b914eb8e33ce529f94992b6f9c" \
                                "7dda01f02b45a3bf64394036d9fda526fdc40c63246a" \
                                "326b42deb3de36554d6a21c933e960a430543685"

    id once final : constant = "update-myIdentifier-to-refresh-cache"

    stableCacheId once final : constant = "FastFooMathModule.Unique"
}

FastFoo :: import FastFooMathModule
````

### Sharing an imported module across imported modules

Modules can import modules which can import other modules. These imported modules may end up being common between different imported modules. For example, a module named `FastFooMathModule` could be used by the local source code as well as an imported module might use the same module. However, only if both modules share an identical module definition will the same imported module be used otherwise two unique importations of the same module may occur. Typically a programmer may only want a single instance of a module (although this is not universally true).

Zax has a methodology to ensure two modules share the same module definition thus ensuring imported modules can all import the same module definition (depending on their needs).

The generated `Module` type includes definitions from whatever code imported the current module. If nothing imported the current module then only standard `System` modules exist. By checking of instantiated a type compiles using the `compiles` keyword, an existing definition for a module that is desired to become imported can be validated before creating a new import definition.

In the example below, both `MySource` and `ThirdParty` wish to import a `FastFooMathModule` module and `MySource` imports `ThirdParty`. They will all share the same definition of `FastFooMathModule` by ensuring only one definition for the `import` module is used. The `MySource` will export its definition of `FastFooMathModule` to `ThirdParty` (as well as some compilation options) so they are able to share their imported module without creating two imported instances of `FastFooMathModule`.

While the example code appears on the surface as verbose, the alternative requires external package managers, pre-build setup processes, shared package conflict resolution, or even extremely complex dependency build systems. This methodology helps to simplify the entire package and importation build process and ensures builds can remain stable and repeatable over time.

````zax
/*
   MySource.zax
*/

// By first testing if the module was already defined by another module
// importing `MySource`, the code can be share any definition from an importer.

if !compiles { testFastFooMathModule : Module.FastFooMathModule } {

    // No previous definition was found because `Module.FastFooMathModule` was
    // never defined so define the module now

    FastFooMathModule :: type {
        type once final : constant = "git"
        repository once final : constant = "https://github.com/immmutable/repo",
        
        branch once final : constant = "master"

        id once final : constant = "update-myIdentifier-to-refresh-cache"

        stableCacheId once final : constant = "FastFooMathModule.Unique"
    }
} else {

    // Some external importer has already defined the module so use this
    // definition instead of our own definition

    FastFooMathModule :: alias Module.FastFooMathModule
}

// Another test to ensure `Module.ThirdPartyModule` isn't already defined
if !compiles { testThirdPartyModule : Module.ThirdPartyModule } {
    ThirdPartyModule :: type {
        type once final : constant = "git"
        repository once final : constant = "https://github.com/immmutable/repo2",

        tag once final : constant = "Version1.2"

        id once final : constant = "update-myIdentifier-to-refresh-cache"

        stableCacheId once final : constant = "ThirdPartyModule.Unique"
    }
} else {
    ThirdPartyModule :: alias Module.ThirdPartyModule
}

FastFoo :: import FastFooMathModule

ThirdParty :: import Module.ThirdPartyModule = {

    // as `ThirdPartyModule` imports the `FastFooMathModule`, export our
    // definition of this imported module to the imported `ThirdPartyModule`
    FastFooMathModule :: alias FastFooMathModule

    // export other compilation options for ThirdPartyModule
    Options :: type {
        debug final : constant = true
    }
}

assert : ()(value : Boolean) = {
    //....
}

importantNumber : (result : Integer)() = {
    // as both modules use the same `FastFooMathModule` under the covers,
    // requesting the same value that originates from `FastFoo` directly or
    // via an indirect route of `ThirdParty` must return the same result

    assert(FastFoo.whatIsTheMeaningOfLife() == ThirdParty.lifesMeaning())

    return FastFoo.whatIsTheMeaningOfLife()
}
````

````zax
/*
  ThirdParty.zax
*/

// This module will verify if a `Module.FastFooMathModule` was already defined
// and in this particular example, an existing module would be detected so
// the definition of the module will be taken from the importer's definition

if !compiles { testFastFooMathModule : Module.FastFooMathModule } {

    // In this example, this definition will not be used

    FastFooMathModule :: type {
        type once final : constant = "git"
        repository once final : constant = "https://github.com/immmutable/repo",
        
        tag once final : constant = "official-2.3"

        id once final : constant = "some-other-identifier"

        stableCacheId once final : constant = "FastFooMathModule.AnotherUnique"
    }
} else {
    // In this example, the importer's definition will be used
    FastFooMathModule :: alias Module.FastFooMathModule
}


// Test for additional compilation options specified by the importer of
// this module
if !compiles { testOptions : Module.Options } {

    // In this example, this definition of options would not be used
    Options :: type {
        debug final : constant = true
    }
} else {
    // In this example, this definition from the importer would be used
    Options :: alias Module.Options
}


FastFoo :: import FastFooMathModule

print : ()(...) = {
    //...
}

lifesMeaning export final : (result : Integer)() = {
    // a compile time test to see if compiling in debug mode
    if Options.debug {
        print("The meaning of life is ", FastFoo.whatIsTheMeaningOfLife(), ".")
    }

    return FastFoo.whatIsTheMeaningOfLife()
}
````

````zax
/*
  FastFooMath.zax
*/

whatIsTheMeaningOfLife final export : (result : Integer)() = {
    return 42
}
````