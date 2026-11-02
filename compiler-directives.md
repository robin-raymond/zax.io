# [Zax Programming Language](index.md)

## Compiler Directives (deferred legacy input)

> **Routing note.** This page keeps only legacy directive material whose design
> is deferred to future work. It is evidence, not current design.
>
> Current design lives elsewhere:
>
> - What a directive is, how one is written, named, and attached, directive
>   ranges, `deprecate`, source mapping, and the catalog of current directives:
>   [Zax compiler directives](language/compiler-directives.md).
> - Compile-time execution, `compile`, `runtime`, execution policies
>   (`[<execute=auto|runtime|folding|compile>]`), `if compile`,
>   `wellformed{...}`, and the `Compilation` namespace:
>   [Zax compile-time code](language/compile-time-code.md).
> - `inline`: [Zax function invocation](language/function-invocation.md#inlining-hints).
>   `likely`, `unlikely`, `always`, and `never`:
>   [Zax core flow control](language/core-flow-control.md#branch-hints-and-path-promises).
>   Panic controls:
>   [Zax safety and analysis](language/safety-and-analysis.md#controlling-panic-checks).
>   `export`:
>   [Zax namespaces and modules](language/namespaces-and-modules.md#exporting-with-directives).
>   `align`, `reserve`, and `void`:
>   [Zax type definitions](language/type-definitions.md#explicit-member-layout).
>   Declarations that outlive a branch:
>   [Zax declarations and bindings](language/declarations-and-bindings.md#outward-declarations).
> - Qualifier defaults are the same everywhere; see
>   [Zax qualifiers](language/qualifiers.md#defaults-and-resolution).
>
> The sections below remain deferred: source generation, generic candidate
> selection (`requires`, `concept`, and compile-time parameters), source and
> asset inclusion, asynchronous-execution directives, ABI selection, the panic
> function, and `lock-free` for `once` values. Their spellings and meanings are
> not accepted.

### `execute` directive: source generation

Current execution policies are defined by
[Zax compile-time code](language/compile-time-code.md#letting-a-function-decide-execution-policies).
The generating options below remain deferred source-generation input.

The options are as follows:
* `generate` - a code block will evaluate on a host system and resolve any input arguments at compile-time and generate new code as a substitute for the code block (i.e. the code block emits replacement tokens as a string)
* `delegate` - a code block will evaluate on a host system but no input arguments will be resolved; instead all input arguments will be treated as a constant array of string literal values; the code block will generate new code as a substitute for the code block at compile-time (i.e. the code block emits replacement tokens as a string);

````zax
random final : ()() = {
    // ...
}

generateItDouble final [<execute=generate>] : (result : String)(value : Integer) = {
    // code here generates a string result which becomes parsed into tokens
    // which replaces the function call ...
    return result
}

generateSomething1 final [<execute=generate>] : (result : String)(...) = {
    // code here generates a string result based on examining compile-time
    // evaluated inputs and the output string result is parsed into tokens which
    // replaces the function call ...
    return result
}

generateSomething2 final [<execute=delegate>] : (
    result : String
)(
    inputs : String[]
) = {
    // this specific form of `delegate` causes each value passed into the string
    // to not be resolved and the output string result is parsed into tokens
    // which replaces the function call ...
    return result
}

// invoking `generateItDouble` in this context causes it to generate replacement
// code tokens for each execution
value2 := generateItDouble(random())

value2 = generateSomething1(value2)     // `generateSomething1(value2)` will
                                        // be replaced with alternative tokens

// the entire line below will be replaced with generated tokens and the inputs
// will not be be evaluated; each input will be converted to a string in
// an array representing each argument as a single string value in the array
generateSomething2(value2 * 5, "hello")
````


### `compile` directive

A `compile` directive can be used on input arguments to indicate a value must be pre-evaluated at compile-time or an error will be issued. Any input argument declared with a `compile` directive must also be declared as either `readonly` or `immutable`. Any variadic types are treated as `readonly` or `immutable`.

````zax
print final : ()([<compile>] ...) = {
    // ... all input arguments must resolve at compile-time ...
}

func final : ()(input [<compile>] : Integer readonly) = {
    // ... `input` argument must resolve at compile-time ...
}

random1 final [<execute=dual>] : ()() = {
    // ... this random function can execute at compile-time or runtime ...
}

random2 final [<execute=target>] : ()() = {
    // ... this random function can only execute at runtime ...
}

// capture a runtime only value
value := random2()

func(5)            // OKAY: `5` value is constant
func(random1())    // OKAY: `random1` function is allowed to resolves as a
                   // compile-time constant

func(random2())    // ERROR: `random2` function cannot resolve at compile-time
func(value)        // ERROR: `value` is runtime only and it is not constant

print(5, "hello", random1())   // OKAY: all values are compile-time constants
print(random2(), value)        // ERROR: runtime only values cannot become
                               // compile-time constants
````


### `requires` directive

A `[<requires |=<option>|>]` directive evaluates a code block that follows into a compile-time constant of `true` or `false`. A code block that follows a `requires` directive is executed if it compiles. Failure to compile a code block will result in a code block resolving to `false`. A code block must be capable of running at compile-time.


The options for `requires` are as follows:
* `default` (default if not specified) - a failure to compile a code block resolves to a `false` literal constant
* `compiles` - if `compiles` is specified then a failure to compile a code block will evaluate to a real compile-time error
* `error` - if `error` is specified then a failure to compile a code block or a `requires` directive code block returning `false` will evaluate to a real compile-time error

Meta-functions can be selected as a candidate or unselected depending on a `true` or `false` statement being present at the end of a functions declaration. A `[<requires>]` directive can be used in-place of a function's enable or disable boolean to enable or disable a meta-function as a candidate based on a function's arguments. All inputs and outputs are considered captured in a function's context allowing for compile-time reflection of argument types. Any values of input arguments that are not compile-time constants will not be constructed nor destructed and any access to input argument values may cause undefined behaviors. Output arguments are never constructed nor destructed and any access to an output argument may result in undefined behaviors. Any input argument can be checked if it contains a compile-time constant by using a `is host constant` operator.

Intrinsic types sizing and endian encoding are entirely relative to a host system or a target system. Operators exist to specifically query a host system or a target system's sizing and endian encoding. However, a `requires` directive works in a dual mode. A `requires` may be used to evaluate candidate suitability or compile-time evaluation for a target system but a code block actually runs within the context of a host system (and not a target system). As such, system context dependant operators (such as `size of` but not `host size of` nor `target size of`) will evaluate as if they are operating on a target context by default.


````zax
if [<requires>] { return ++value > 2 } {
    doSomething()
} else {
    doSomethingElse()
}

metaFunction : ()(input :) [<requires>] {
    // if the size of `input`'s type is smaller than an `Integer` then do
    // not select this function as a candidate
    if (size of :input) < (size of Integer)
        return false

    // check if `input` is a compile-time constant
    if input is host constant {
        // safe to examine `input`'s value if it is a compile-time constant but
        // the `< 0` operation may fail to compile if `input`'s type does
        // not define an appropriate less-than operator
        if input < 0
            return false
        return true
    }
    return true
} = {
    // ...
}

/*

// if `metaFunction`'s `requires` block evaluates to `true` the function
// becomes selectable as a candidate as the `requires` code block becomes
// a `true` literal:
metaFunction : ()(input :) true = {
    // ...
}

// if `metaFunction`'s `requires` block evaluates to `false` the function
// is not selectable as a candidate as the `requires` code block becomes
// a `false` literal:
metaFunction : ()(input :) false = {
    // ...
}

*/
````


### `concept` directive

A `concept` directive has two usages: `[<concept>]` and `[<concept=<function>>]`. A `concept` is similar to a `requires` directive but instead causes a `final` function to be treated as selection criteria for a meta-function or meta-type when used in meta-programming. A `concept` directive will evaluate a `concept` function whenever a `concept` function is declared in conjunction with a meta-function or meta-type. Inside a `concept` function, code is executed at compile-time to evaluate if a meta-type is compatible with an input or output argument. A `concept` function must return `true` or `false` to indicate if a meta-type is compatible with a `concept`. If a `concept` function fails to compile then a `concept` function will be treated as if it returned a `false`. A `[<compiles=error>]` or `[<requires=error>]` can be used inside a `concept` function to intentionally cause an error condition.

The form `[<concept>]` is declared on a `final` function indicating a function is to be treated as a concept. The second form `[<concept=<function>>]` is declared on a meta-type or a meta-function to indicate a type should use a `concept` function to determine if a type meets the selection criteria necessary (for a given meta-type or meta-function).

Functions using a `concept` have a single input argument representing a type to be evaluated and must return `true` (or a meta-type or meta-function will not be selected). A variable passed into a meta-function is not initialized, never constructed, and never destructed. An input argument is a placeholder to evaluate a type's properties inside a `concept` function. A `concept` directive can use `if` clauses with a `compiles` directive to further evaluate compile-time checks without executing of any statements.

Intrinsic types sizing and endian encoding are entirely relative to a host system or a target system. Operators exist to specifically query a host system or a target system's sizing and endian encoding. However, a `concept` directive works in a dual mode. A `concept` may be used to evaluate candidate suitability or compile-time evaluation for a target system but a code block actually runs within the context of a host system (and not a target system). As such, system context dependant operators (such as `size of` but not `host size of` nor `target size of`) will evaluate as if they are operating on a target context by default.


````zax
IsSelectable$(Type) final : (result : Boolean)(ignored : $Type) [<concept>] = {
    // the type must support these operators or the type will not be selected
    if ![<compiles>] {
        // code must not execute as accessing `ignored` as a value may
        // cause undefined behaviors
        ++ignored
        --ignored
        ignored += 5
        ignored -= 5
    } return false

    // the type must be larger than the size of a `U32` or it is not selectable
    if size of $Type > size of U32
        return false

    return true
}

myFunc$(UseSimpleType [<concept=IsSelectable>]) final : ()(bar : $UseSimpleType) = {
    // ...
}

myFunc final : ()(bar : ) = {
    // ...
}

myFunc(5 as U8)     // first `myFunc` is used as it meets the most specific
                    // selection criteria
myFunc(5 as U64)    // last `myFunc` used as the first `myFunc` is not a
                    // a selectable candidate
````


### `source` directive

A `[<source="<path/file.zax>" |, required=<yes|no|warn>| |, generated=<yes|no>|>]` directive instructs a compiler to pause compiling the current file and continue compiling tokens from a referenced file until that file is completely parsed then resume compiling the current file. A `source` directive always locates files relative to the current file. If a path is not found, then the parent of the current path is attempted (recursively to the root of a module) until a `source` is located or the file is not found (whereupon a compiler will issue an error). Paths are always separated with unix forward slashes ('/') regardless of the platform. File names are recommended to be always lowercase and words should be separated with a dash (`-`) sign.

An optional argument named `required` is available. If the value is `yes` then a file must be found (default behavior) or a `source-not-found` error will occur. If `required` is `no` then a compiler will ignore this file being absent. If `required` is `warn` then a compiler will issue the `source-not-found` warning. A file extension of `zax` is the recommended default extension.

An optional argument named `generated` is available. If the value is `yes`, then a source file needs to be generated by one of a compiler's `execute` directives. Prior to attempting to load and and parse a file, the generated file date is checked and if the compile date is newer than the generated file date then a compiler will issue a `generated-file-not-touched` warning. Generated files must (at minimal) be "touched" by a generator. A default value of `no` is assumed (and redundant to specify).

A [wildcard character](https://en.wikipedia.org/wiki/Wildcard_character) pattern matching notation is allowed. Multiple matched files are imported in ascending ASCII sort order and a source will never import itself when using a wild card.

````zax
/*
file.zax
*/

[<source="options.zax", required=warn>]
[<source="graphics/*.zax">]
[<source="sub-path/sub-file.zax">]
[<source="generated/keyboardMapping.zax", generated=yes>]
````

````zax
/*
sub-path/sub-file.zax
*/

[<source="options.zax", required=no>]
````


### `asset` directive

An `[<asset="<path/file.ext>" |, required=<yes|no|warn>| |, rename="<new-path/new-name.ext>"| |, generated=<yes|no>|>]` compiler directive instructs a compiler copy a file into an output target's asset folder. An `asset` directive always locates files relative to the current file. If a path is not found, then a parent of the current path is attempted (recursively to the root of a module) until an `asset` is located or the file is not found (where a compiler will issue an error). Paths are always separated with unix forward slashes ('/').

An optional argument named `required` is available. If the value is `yes` then a file must be found (default behavior) or an `asset-not-found` error will be issued. If `required` is `no` then a compiler will ignore this file being absent. If `required` is `warn` then a compiler will issue an `asset-not-found` warning.

An optional argument named `generated` is available. If the value is `yes`, then the asset file needs to be generated by one of the compiler's `execute` directives. Prior to attempting to copy a file, the generated file date is checked and if the compile date is newer than the generated file date then a compiler will issue a `generated-file-not-touched` warning. A default value of `no` is assumed (and redundant to specify).

The [wildcard character](https://en.wikipedia.org/wiki/Wildcard_character) pattern matching notation is allowed. Multiple matched files are imported in ascending ASCII sort order. If a wild card character is used with the rename option then the same wild card character must be present in the `rename`. All wild card characters in the rename must be present in the same order or the `wild-character-mismatch` error will be issued.

````zax
/*
file.zax
*/

[<asset="intro.wav", required=warn>]
[<asset="graphics/*.png">]
[<asset="sub-path/example-*.pdf", rename="examples/*.pdf">]
[<asset="palette.png", generated=yes>]
````

````zax
/*
sub-path/sub-file.zax
*/

[<source="options.zax", required=no>]
````


### `synchronous` directive

Function are assumed to be implicitly `asynchronous` for `promise`, `task` or `channel` functions. This default can be overridden by using a `synchronous` directive. When a `synchronous` directive is used, a function is declared to not operate asynchronously and all assumptions about any `asynchronous` intentions are no longer present. A `[<synchronous>]` directive effectively changes the expectations of a function from `asynchronous` to `synchronous` and indicates the code path does not need to be thread-aware.

An implicit assumption for `asynchronous` functions is that pass by-values arguments must be qualified as `deep`. A compiler issues an `asynchronous-not-deep` warning on `asynchronous` functions for any pass by-value arguments that are not explicitly qualified as `deep` (or pre-qualified as `deep` based on a `type`'s definition). This check is done to ensure that values potentially crossing a thread boundary are automatically `deep` copied in an effort to prevent concurrency issues. A function can be labelled explicitly as `deep` or `copy` to suppress this warning by forcing semantics on specific arguments.

If a `promise`, `task`, or `channel` declared function will never be used from a different thread contexts then an `[<synchronous>]` directive can be used to acknowledge a declared function is exclusively synchronously accessed and thus a `deep` qualifier need not be applied. Further, any type declared as `deep` (which normally would cause a `deep` copy to occur) will perform an ordinary `copy` instead. Individual arguments for promises or tasks declared as `deep` explicitly will still perform `deep` copies of any arguments.

If a `promise` or `task` truly is `asynchronous` (as it would be implicitly) but a pass by-value should use ordinary copying, then an explicit `copy` stance can be specified. This changes a pass by-value from being implicitly `deep` to explicitly `copy`, and an `asynchronous-not-deep` warning will be suppressed for that argument.

A `[<synchronous>]` and `[<asynchronous>]` directive are mutually exclusive and they indicate opposite code intentions.

````zax
MyType :: type {
    value1 : Integer * = @
}

func final : ()(myType : MyType) promise [<synchronous>] = {
    // ...
}

myType : MyType

// OKAY: a warning `asynchronous-not-deep` will not be issued
later := func(myType)

// ...

// must be called from the same thread or undefined behaviors may result
callable := later.then = { /* ... */ }
callable()
````


### `asynchronous` directive

Unlike a `promise`, `task`, or `channel`, normal functions are assumed to operate in a `synchronous` fashion. Using an `asynchronous` directive tells a compiler that a function will perform asynchronous operations despite not being a `promise`, `task`, or `channel` (which are already default assumed to be `[<asynchronous>]` implicitly). An `[<asynchronous>]` directive effectively changes a function's expectations from `synchronous` to `asynchronous` and indicates a normal function is designed to be thread-aware.

When a function is labelled as `asynchronous`, a function is excepting that all pass by-value arguments are qualified with a `deep` specifier. A compiler will issue an `asynchronous-not-deep` warning if a `deep` qualifier is missing (as values otherwise use ordinary `copy`). Adding a `deep` qualifier overrides that default. If a value should use ordinary copying then `copy` can be stated either on an individual pass by-value argument or on a function as a whole.

A `[<synchronous>]` and `[<asynchronous>]` directive are mutually exclusive and they indicate opposite code intentions.

````zax
MyType :: type {
    // ...
}

// functions labelled as `asynchronous` expects all pass by-value functions
// to use a `deep` qualifier rather than implicit `copy`, thus a warning
// is issued to indicate the oversight
func1 final : ()(myType : MyType) [<asynchronous>] = {
    // ...
}

// the pass by-value uses explicit `copy`, thus no warning is issued
func2 final : ()(myType : MyType copy) [<asynchronous>] = {
    // ...
}

// the pass by-value is `deep` copied thus no warning is issued
func3 final : ()(myType : MyType deep) [<asynchronous>] = {
    // ...
}

// all pass by-values use `copy`, thus no warning is issued
func4 final : ()(a : MyType, b : MyType) copy [<asynchronous>] = {
    // ...
}

// all pass by-values are `deep` copied thus no warning is issued
func5 final : ()(a : MyType, b : MyType) deep [<asynchronous>] = {
    // ...
}
````


### `abi` directive

An `[<abi=<options>>]` directive can override a calling convention to force a particular ABI for a given function. For ABI compatibility purposes with C/C++, a function within a type can declare `final` function with an alternative ABI. Normally the Zax language makes no ABI commitments across compiled functions as source code is always compiled as a whole and compiled libraries are not considered compatible across compilers or compiler versions.

The ABI options are as follows:
* `virtual` - this causes a function to assume `C++` virtual calling conventions
  to be inserted into a virtual table for a `type` where a `virtual` table for a
  `type` will be auto-created and auto-maintained; the legacy context-state
  assumption is not current and remains future interoperability input
* `c` - this causes a function to assume a `c` style calling convention; the
  legacy receiver-state claim is not current. Foreign receiver and context
  adaptation belongs to [raw interoperability input](project/raw/interop.md),
  and receiverless type calls belong to
  [Zax Nothing instances](language/nothing-instances.md)
* `never` - a function never returns and a compiler can generate jump assembly instructions without an instruction pointer stack frame pushed or return instructions
* `interrupt` - a function will be called directly from an interrupt and a compiler should generate interrupt return instructions instead of standard return instructions

````zax
MyType :: type {
    func1 final [<abi=virtual>] : ()() * = {
    }

    func2 final [<abi=virtual>] : ()() * = {
    }
}
````


### Panic function

When a panic occurs, the context's panic function is called (`___.panic(...)`). Normally an error message is displayed for a programmer to understand a panic and then a program terminates due to an unexpected and unhandled condition. A default `panic` function can be replaced with an alternative function.

A panic function is called with an enumeration representing the current error code and a code location is passed containing the source location of where a panic condition was triggered.


### `lock-free` directive

A `lock-free` directive disables the creation of this safety code around a singleton's instance creation and assumes that construction of a type will happen entirely single threaded without any possibility of multiple threads competing for an singleton's instance creation. Normally, a `once` keyword will automatically generate thread-safe barriers around a singleton's instance construction to ensure only a single instance will ever be constructed of a variable (regardless of the number of threads accessing a variable). However, this adds additional safety overhead that might not always be necessary.

````zax
MyType :: type {
    value1 : String
    value2 : String 
}

giveMeMyType final : (myType : MyType &)() = {
    // no thread locking mechanism will surround this code
    singleton once [<lock-free>] : MyType
    return singleton
}

// force initialization globally on startup (which must be single threaded)
initializeMyType private := giveMeMyType()
````
