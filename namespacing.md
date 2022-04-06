
# [Zax Programming Language](index.md)

## Namespacing and Importing Modules

### `import` basics

An `import` keyword is used to declare a module import into the current namespace.

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

By not specifying an importation declaration name, all symbols are imported into a global namespace (which can also be accessed through the `Module` implicit namespace).

````zax
// import all types and variables found in a module described by
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

// assume that the imported `Definition.Animals` also contains a type named
// `Frog`
:: import Drawing.Animals

frog : Frog

// ERROR: a programmer assumed a declared `frog` was the imported `Frog` type
// from `Drawing.Animals` but a type named `Frog` already existing prior to
// import thus the imported `Frog` type was hidden and not selected when the
// `Frog` type was used; the assumed function `display()` does not exist on
// the original `Frog` type selected by the compiler
frog.display()
````


### Importing intrinsic types

Prior to the compilation of the first line of code, a definition for all intrinsic types exists. However, an importation of these types must be done by importing their built-in module declaration placed into the automatic-declared `Module` namespace.

System types are not defaulted to allow potential importation aliasing as desired and to not pollute the global namespace with declarations that may not be desired. The presumption is that most code will import their needed `System` modules into the global namespace.

Because of the way modules self isolate from each other, one module importing `System` types into the global namespace will have no impact to other modules importing into their own namespace.

````zax
:: import Module.System.Types
````


### Exporting types

By default, symbols in code are not exported beyond the boundary of their originating source. However, an `[[export]]` directive can be applied to variables, types, aliases, and even imports to cause symbols to become visible to an importing module. A compiler can allow all global symbols to be exported from source files but this approach is not recommended. Contained types and variables are all visible unless they are hidden. Local functions inside other functions are effectively hidden (unless they become returned from or passed into a function).

````zax
// nothing imported from this module will be exported (despite being imported
// into the global namespace)
:: import Module.System.Types

myInteger : Integer         // symbol is only accessible by this source

[[export]] \
canYouSeeMe : String // symbol is exported

Cos1Tan2Arc4Sin :: type {
    value1 : Float
    value2 : String
    value3 : Integer
}

// export `Cos1Tan2Arc4Sin` type under an alias of `FriendlyName`
[[export]] \
FriendlyName :: alias Cos1Tan2Arc4Sin

// all exported symbols found when importing `Module.Useful.FileUtilities` are
// re-exported to any module importing this code.
[[export]] \
FileUtilities :: import Module.Useful.FileUtilities
````


#### Hiding exports

Even though a type might be exported, keeping some contained types or variables hidden from the importer may be desirable. Zax allows for selective `export` enabling using a `[[export=yes]]` or `[[export=no]]` directive. Likewise, all  `private` declared variables are never visible.

Global variables marked as `[[export=no]]` are still visible to all source files within the same module but not to any importer. Global variables marked as `private` are only visible within the current source file.

Take note: these are not keywords named `secret` or `protected` (or similar). While the language attempts to keep namespaces from becoming polluted with types and variables meant for internal house keeping, nothing in the language protects a pointer to a type being taken, nor prevents raw memory access of a types being read byte-by-byte. No cryptographic promises are implied by usage of the `[[export=no]]` or `private` keyword.

````zax
:: import Module.System.Types

[[export]] \
MyType :: type {
    value1 : Float              // symbol is visible
    value2 : String             // symbol is visible
    value3 private : Integer    // symbol is `private` to everything except
                                // functions and types within this type

    [[export=no]] \
    value4 : String             // symbol is accessible to other types within
                                // the same set of sources but is hidden
                                // when exported

    addOne final :: ()() = { ++value3 }
}

content : MyType

content.value1 = 4.3
content.value2 = "Hello world!"
content.addOne()                    // `value3` is hidden outside of `MyType`
content.value4 = "Goodbye world!"   // `value4` is visible in the current module
                                    // but becomes hidden once imported
                                    // elsewhere
````


### Defining a new `import` module

A module is a collection of Zax source code that can be fetched and imported as an imported module of types. Once fetched, a module is cached locally where cached files can be kept as known assets for an official build. Only if a module's definition changes is a cached module refreshed otherwise a cached version is used. For example, if a repository tag was changed then an old cached repository would be flushed and replaced with a new cached repository. A commit update on a repository branch will not automatically refresh any cached contents outside of automatic branch synchronization polling happening. An `id` field on a module definition can be updated to force a refresh of a cached imported module. Alternatively, deleting an imported module from a cache will cause a cache to refresh a module.

````zax
// declare a module importation definition
FastFooMathModule :: type {
    type final := "git"
    location final := "https://github.com/immmutable/repo"
    
    // tag final := "Version1.2"      // not using a tag
    branch final := "development"

    // a hash validation of the content can be performed (but is optional)
    hashAlgorithm final := "SHA512"
    hash final := "f3b85c040bae9b5ce8b914eb8e33ce529f94992b6f" \
                  "9c7dda01f02b45a3bf64394036d9fda526fdc40c63" \
                  "246a326b42deb3de36554d6a21c933e960a430543685"

    // the API contract version desired from imported module
    // (see `deprecate` compiler directive)
    version final := "1.3"

    id final := "update-myIdentifier-to-force-a-cache-refresh"

    // the requested local storage folder location name for the imported module
    stableCacheId final := "FastFooMathModule.UniqueGuid"
}

// perform the actual importation based on the compiler definition
FastFoo :: import FastFooMathModule
````


### `Module` global scope alias

The global scope has a namespace alias called `Module`. Every type a variable is defined at a global scope the variable is also visible within the built-in `Module` scope.

For example:

````zax
a := 10
b := 20

// `c` and `d` will contain the same value as they refer to the same variables
c := a * b
d := Module.a * Module.b    
````


### Sharing an imported module across imported modules

Modules can import modules which can import other modules. These imported modules may end up being common between different imported modules. For example, a module named `FastFooMathModule` could be used by the local source code as well as an import from a different module. However, only if both modules share an identical module definition will the same imported module code be used and shared. Otherwise two unique importations of the same module may occur. Typically a programmer may only desire a single instance of a module (although this is not universally true).

Zax has a methodology to ensure two modules share the same module definition thus ensuring imported modules can all import the same module definition (depending on their needs).

A `Module` type may include definitions from whatever code imported the current module. If nothing has already imported a module then no pre-existing `Module` definitions exist and thus a previous module importation can be assumed not to have existed. A `compiles` directive can then be used to assist in sharing module importation definitions. If an existing definition for an importation module then that definition can be used otherwise a new localized import definition can be created.

In the example below, both `MySource` and `ThirdParty` wish to import a `FastFooMathModule` module. `MySource` imports `ThirdParty`. They will all share the same definition of `FastFooMathModule` by ensuring only one definition for an `import` module is used. The `MySource` will export its definition of `FastFooMathModule` to `ThirdParty` (as well as some compilation options) during importation of `ThirdParty` so both are able to share their imported module of `FastFooMathModule` without creating two imported instances of `FastFooMathModule`.

While the example code appears on the surface as verbose, the alternative requires external package managers, pre-build setup processes, shared package conflict resolution, or even extremely complex dependency build systems. This methodology helps to simplify the entire package and importation build process and ensures builds can remain stable and repeatable over time.

````zax
/*
   MySource.zax
*/

// By first testing if the module was already defined by another module
// importing `MySource`, the code can be share any definition from an importer.

[[descope]] if ![[compiles]] { testFastFooMathModule : Module.FastFooMathModule } {

    // No previous definition was found because `Module.FastFooMathModule` was
    // never defined so define the module now

    FastFooMathModule :: type {
        type final := "git"
        location final := "https://github.com/immmutable/repo",
        
        branch final := "master"

        id final := "update-myIdentifier-to-force-a-cache-refresh"

        stableCacheId final := "FastFooMathModule.Unique"
    }

} else {

    // Some external importer has already defined the module so use this
    // definition instead of our own definition

    // i.e. the `ThirdPartyModule` was already defined
    // FastFooMathModule :: type { ... }

}

// Another test to ensure `Module.ThirdPartyModule` isn't already defined
[[descope]] if !compiles { testThirdPartyModule : Module.ThirdPartyModule } {

    ThirdPartyModule :: type {
        type final := "git"
        location final := "https://github.com/immmutable/repo2",

        tag final := "Version1.2"

        id final := "update-myIdentifier-to-force-a-cache-refresh"

        stableCacheId final := "ThirdPartyModule.Unique"
    }

} else {
    // Some external importer has already defined the module so use this
    // definition instead of our own definition

    // i.e. the `ThirdPartyModule` was already defined
    // ThirdPartyModule :: type { ... }
}

assert final : ()(value : Boolean) = {
    // ....
}

FastFoo :: import Module.FastFooMathModule

ThirdParty :: import Module.ThirdPartyModule {

    // as `ThirdPartyModule` imports the `FastFooMathModule`, export our
    // definition of this imported module into the imported `ThirdPartyModule`
    FastFooMathModule :: alias Module.FastFooMathModule

    // export other compilation options for ThirdPartyModule
    Options :: type {
        debug final : constant = true
    }
}

importantNumber final : (result : Integer)() = {
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

[[descope]] if ![[compiles]] { testFastFooMathModule : Module.FastFooMathModule } {

    // In this example, this definition will not be used

    FastFooMathModule :: type {
        type final := "git"
        location final := "https://github.com/immmutable/repo",
        
        tag final := "official-2.3"

        id final := "some-other-identifier"

        stableCacheId final := "FastFooMathModule.AnotherUnique"
    }

} else {
    // In this example, the importer's definition will be used
    // FastFooMathModule :: type { ... }
}


// Test for additional compilation options specified by the importer of
// this module
[[descope]] if ![[compiles]] { testOptions : Module.Options } {

    // In this example, this definition of options would not be used
    Options :: type {
        debug final : constant = false
    }

} else {
    // In this example, this definition from the importer would be used
    // Options :: alias Module.Options
}


print final : ()(...) = {
    // ...
}

FastFoo :: import Module.FastFooMathModule

[[export]] \
lifesMeaning final : (result : Integer)() = {
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

[[export]] \
whatIsTheMeaningOfLife final : (result : Integer)() = {
    return 42
}
````


### Variable and namespace resolution and name shadowing

When a name or dotted name is encountered, a name is resolved by componentizing a name first where all components are read left to right. Each name is checked against the most local names in scope first and if that name is not found then an outer scope is checked. If that name is found then the next name component is checked (if any are present) to see if matching sub-components of the found name exist. If all component names match then a name is considered resolved. If component names are not found then outer scopes continue to be checked until a full match can be found. If no match can be made then an error is reported by a compiler.

Since the same name can exist in an inner scope as an outer scope, an outer name might end up becoming shadowed and hidden from view. By intentional design, the language can only check from inner to outer scope. Once a match is found any hidden names will not be visible from that scope. No language operator can be used to start a search from the global scope. However, all global scope types and variables are visible within the built-in scope named `Module` (which is a referenced alias to the current module's global scope). An `alias` keyword can be used to give a new name to a shadowed name so items can be located by an `alias` name rather than a hidden shadowed name.

````zax
MyType :: type {
    value1 : Integer
    value2 : String
}

NonShadowedName :: alias MyType

func final : ()() = {
    MyType :: type {
        name : String
        value : Integer
    }

    // the local `MyType` definition will be used and the global `MyType`
    // definition will be shadowed inside this function and invisible
    valueA : MyType

    // the global `MyType` definition will be used as the alias name
    // is visible (i.e. not shadowed)
    valueB : NonShadowedName
}
````


### Operator namespacing

Unlike other namespaced type and variables, operators declared in a global scope do not have a method to be located by namespace. Instead, a search for matching operators is done by searching the current module first, then searching each module for global matching operators in the order they were imported. Operators contained within imported modules of imported modules are not searched for matches as imports of imports are never visible (unless intentionally re-exported).
