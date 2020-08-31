
# [Zax Programming Language](index.md)

## Other Compiler Directives

### The `source` directive

The `source` compiler directive `[[source="<path/file.zax>",required=<value>]]` instructs the compiler to pause compiling the current file and continue compiling tokens from the mentioned file until completely parsed then resume compiling the current file. The `source` directive always locates files relative to the current file. If a path is not found, then the parent of the current path is attempted and so on until the `source` is located or the file is not found (where the compiler will issue an error). Paths are always separated with unix forward slashes ('/'). File names are recommended to be always lowercase and words should be separated with a dash sign (`-`).

An optional argument named `required` is available. If the value is `yes` then the file must be found (default behavior) or the `source-not-found` error will occur. If `required` is `no` then the compiler will ignore this file being absent. If `required` is `warn` then the compiler will issue the `source-not-found` warning. The file extension `zax` is the recommended default extension.

The [wildcard character](https://en.wikipedia.org/wiki/Wildcard_character) pattern matching notation is allowed. Multiple matched files are imported in ascending ASCII sort order.

````zax
/*
file.zax
*/

[[source="options.zax", required=warn]]
[[source="graphics/*.zax"]]
[[source="sub-path/sub-file.zax"]]
````

````zax
/*
sub-path/sub-file.zax
*/

[[source="options.zax", required=no]]
````


### The `asset` directive

The `asset` compiler directive `[[asset="<path/file.zax>",required=<value>,rename="<new-name>"]]` instructs the compiler copy the file into the output target's assets. The `asset` directive always locates files relative to the current file. If a path is not found, then the parent of the current path is attempted and so on until the `asset` is located or the file is not found (where the compiler will issue an error). Paths are always separated with unix forward slashes ('/').

An optional argument named `required` is available. If the value is `yes` then the file must be found (default behavior) or the `asset-not-found` error will occur. If `required` is `no` then the compiler will ignore this file being absent. If `required` is `warn` then the compiler will issue the `asset-not-found` warning. The file extension `zax` is the recommended default extension.

The [wildcard character](https://en.wikipedia.org/wiki/Wildcard_character) pattern matching notation is allowed. Multiple matched files are imported in ascending ASCII sort order. If a wild card character is used with the rename option then the same wild card character must be present in the `rename`. All wild card characters in the rename must be present in the same order or the `wild-character-mismatch` error will be issued.

````zax
/*
file.zax
*/

[[asset="intro.wav", required=warn]]
[[asset="graphics/*.png"]]
[[asset="sub-path/example-*.pdf",rename="examples/*.pdf"]]
````

````zax
/*
sub-path/sub-file.zax
*/

[[source="options.zax", required=no]]
````


### The `panic` directive

#### The `panic` function

When a panic occurs, the context's panic function is called (`___.panic(...)`). Normally an error message is displayed for the programmer to understand the panic and then the program terminates due to the unexpected an unhandled condition. The default `panic` function can be replaced with an alternative function.

The panic function is called with an enumeration representing the current error code and a code location type containing the source location of where the error was triggered.


#### The pre-panic check function

When a panic occurs in code that is always compiled in (and cannot be removed by direct compiler directive), the context's pre-panic check function is called (`___.prePanicCheck(...)`). This function accepts an enumeration representing the error where a lookup table can be searched to see if the panic is enabled and a code location type. If the panic is enabled the context's panic function is called. If the panic is suppressed the panic function is never called.

A pointer to the lookup table is maintained within the context object. The compiler will insert code to swap in/out the lookup table pointer to other known compiler states if a code section has different run-time only panic scenarios.


#### Enabling/disabling a compiler panics

Code generation for panic can conditions can be enabled or disabled by using the `[[panic=<option>, registered-<panic-name>]]`. If the compiler compiles-in the `panic` directive, the compiler will enable or disable the compiler panic code generation. All compilers must register their panic options and meanings into a shared authority registry. Experimental non-standard panic names must include an `x-` prefix as part of the panic name. Naming a specific panic is optional. If the compiler panic name is not specified, the directive will apply to all panic conditions.

Caution: disabling panics does not prevent the panic scenario; disabling merely removes additional compiler generated protective code that would call a panic function. Without compiling in panic detection, the code may silently fail with undefined behaviors.

The options for panic conditions are:
* `yes` - enables the panic for only to the current statement
* `no` - disables the panic for only to the current statement
* `always` - enables the panic for all statements that follow
* `never` - disables the panic for all statements that follow
* `default` - enables or disables the panic for all statements that follow according to the compiler's defaults
* `lock` - disallows any imported module from changing a panic states
* `unlock` - allows any imported module from changing a panic states


````zax
randomValue final : (output : S32)() = {
    //...
    return output
}

value := randomValue()

[[panic=no, intrinsic-type-cast-overflow]] \
castedValue1 := value as U16

[[panic=yes, intrinsic-type-cast-overflow]] \
castedValue2 := value cast U8

[[panic=always, intrinsic-type-cast-overflow]]

//...

[[panic=never, intrinsic-type-cast-overflow]]

//...

[[panic=default, intrinsic-type-cast-overflow]]

//...

[[panic=never, x-strange-experimental-panic]]
````


#### Panic push and pop

The state of all panics can be pushed and popped into a compile stack using the `[[panic=push]]` and `[[panic=pop]]` compiler directives. A push operation will keep a copy of all panic states and push these panic states on the compiler stack. A pop operation will take the last pushed compiler panic states and apply these panic states as the current panic states.

Upon importing a module, all panic states are pushed and all panic states are popped at the end of an `import`. This ensures that imported modules cannot affect the panic state of the importing module.

````zax
[[panic=push]]

[[panic=never, intrinsic-type-cast-overflow]]

//... code with the panic checks disabled

[[panic=pop]]
````


#### Panic registry and meanings

The following are registered panic scenarios, default states, and their meaning:
* `out-of-memory` (always)
    * memory was requested to be allocated but insufficient memory exists to fill the request
* `intrinsic-type-cast-overflow` (always)
    * an intrinsic type may overflow during the `as` operator to a type with lower bit sizing
* `string-conversion-contains-illegal-sequence` (always)
    * a string literal conversion was found to contain an illegal character sequence during the conversion process
* `reference-from-pointer-to-nothing` (always)
    * a pointer was converted to a reference but the pointer points to nothing
* `pointer-to-nothing-accessed` (always)
    * a value (or function) was accessed but the pointer points to nothing
* `not-all-pointers-deallocated-during-allocator-cleanup`
    * memory cleanup is being performed but not all the allocated memory from the allocator was deallocated


### Deprecation directive

The deprecation directive `[[deprecate=<option>,context=<context>,error,min="<x.x>",max="<x.x>"]]` can be used to cause API usages to be considered deprecated. Any usages found to be deprecated will issue the `deprecate-directive` warning or error. This directive is useful for allow grace periods to exist when upgrading to access older obsoleted APIs as well as an upgrade process to newer APIs while still maintaining compatibility with an older requested API.


The options for panic conditions are:
* `yes` (default) - enables the deprecation for only to the current statement
* `no` - disables the deprecation for only to the current statement
* `always` - enables the deprecation for all statements that follow
* `never` - disables deprecation for all statements that follow (`min`,`max`, `context` are ignored)

The `error` argument is optional and if specified the warning is forced into an error without the possibility to treat the deprecation as only a warning.

Context for the deprecate warning:
* `import` (default) - only warn on usages from module performing the import
* `all` - any usage of this item is to be treated as deprecated
* `local` - any usage of this item outside the local context is deprecated

The `min` option requires the importing module must declare an import of at least this version to use the API. The `max` option requires the importing module must not declare an import version greater than this version. The compiler will treat versions as [point release](https://en.wikipedia.org/wiki/Point_release) notations. The version can be specified using a `version` `String` declaration in the import statement. The `min` and `max` keywords are generally not used together but they can be.

Only one `always` deprecation can be active at a time. The `never` directive will disable any active `always`. Usage of `never` cannot be declared with `error`, `min`, `max` or `context` as they have no applicability and will issue an `incompatible-directive` error. Individual `yes` or `no` temporarily override any `always` deprecations directives for the active statement. Any active deprecation state will not apply to imported modules. There's no way to push or pop deprecations unlike `panic`, `warning`, or `error` directives.

````zax
[[deprecate]] \
MyOldBadlyDesignedType :: type {
    //...
}

// usage of this type should only be performed when
// requesting this version number at minimum
[[deprecate,min="2.3"]] \
MyShinyNewType :: type {
    //...
}

ValidType :: type {

    // all of the functions and variables below are now obsoleted and
    // cannot be used beyond version 1.1
    [[deprecate=always,error,max="1.1"]]

    mrT80sFunc : ()() = {
        //...
    }

    markyMarkFunc : ()() = {
        //...
    }

    eightTrack : MyOldBadlyDesignedType

    // disable the last `always` deprecation directive
    [[deprecate=never]]


    // this function is deprecated if used accessed outside the local context
    [[deprecate,context=local]] \
    needsRedesignFunc : ()() = {
        //..
    }

    // this function is always warned as being deprecated
    [[deprecate,context=all]] \
    mightNeedThisSoNotReadyToRemoveFunc : ()() = {
        //...
    }

    newerOkayFunc : ()() = {
        //..
    }
}
````
