
# [Zax Programming Language](index.md)

## Compiler Warnings and Errors

### `error` directive

#### Forcing the compiler to issue an error

An `error` can forcefully be issued by the compiler by using the `[[error="<message>", <optional-registered-error-name>]]` compiler directive. If the compiler compiles-in an `error` directive, the compiler will display the error and halt. A compiler error may optionally include one of the built-in error names or declare a custom error name with a `x-` prefix.

````zax
if sizeof Integer != 4 {
    [[error="this poorly written code assumes an Integer is 4 bytes"]]
}

[[error="these are not the droids you are looking for", x-not-droids]]
````


#### Error registry and meanings

The following are registered errors, and their meaning:
* `error-directive`
    * an error directive was encountered where a specific registered error name was not provided
* `missing-argument`
    * an input or output argument was either not provided or not capture
* `literal-contains-illegal-sequence`
    * a literal was found to contain an illegal character sequence
* `incompatible-directive`
    * a directive was used in an incompatible way
* `deprecate-directive`
    * usage of a deprecated function, variable, or type discovered which is forced to error
* `imported-module-not-found`
    * a request to import a module was made but the imported module cannot be found
* `imported-module-failure`
    * a requested import module was found but the module cannot be imported due to a failure
* `source-not-found`
    * a source file was requested to be parsed but it cannot be located
* `asset-not-found`
    * a asset file was requested to be copied but it cannot be located
* `wild-character-mismatch`
    * a wild character pattern was in an incorrect way
* `final-function-points-to-nothing`
    * an attempt was made to call a function marked as `final` which is known to point to nothing
* `dereference-pointer-to-nothing`
    * an attempt was made to dereference a value known to point to nothing
* `token-expected`
    * a specific token or set of tokens was expected but not found
* `token-unexpected`
    * a token was encountered which was not expected
* `as-conversion-not-compatible`
    * a request to convert one type to another using the `as` where no compatible conversion was found


### Forcing the compiler to issue a warning

A `warning` can forcefully be issued by the compiler by using the `[[warning="<message>", <optional-registered-warning-name>]]` compiler directive. If the compiler compiles-in a `warning` directive, the compiler will display the warning and continue to compile. A compiler warning may optionally include one of the built-in warning names or declare a custom warning name with a `x-` prefix.

````zax
if sizeof Integer > 4 {
    [[warning="this poorly written code hasn't been testing on Integers larger than 4 bytes"]]
}

//...

[[warning="random warning for no good reason", x-blue-moon]]

//...
````

### `warning` directive

#### Enabling/disabling a compiler warning

A warning can be enabled or disabled by using the `[[warning=<option>, <optional-registered-warning-name>]]`. If the compiler compiles-in the `warning` directive, the compiler will enable or disable the compiler warning or treat a specific warning as an error or merely as a warning. All compilers must register their warnings meanings into a shared authority registry. Experimental non-standard warnings names must include an `x-` prefix as part of the warning name. Naming a specific warning is optional. If the compiler warning name is not specified, the directive will apply to all warnings.

The options for warnings are:
* `yes` - enables the warning for only to the current statement
* `no` - disables the warning for only to the current statement
* `always` - enables the warnings for all statements that follow
* `never` - disables the warnings for all statements that follow
* `default` - enables or disables the warnings for all statements that follow according to the compiler's defaults
* `error` - forces enabled warnings to be treated as an error for all statements that follow
* `warning` - forces enabled warnings to be treated as merely a warning for all statements that follow
* `lock` - disallows any imported module from changing a warning states
* `unlock` - allows any imported module from changing a warning states


````zax
randomValue final : (output : S32)() = {
    //...
    return output
}

value := randomValue()

[[warning=no, intrinsic-type-cast-overflow]] \
castedValue1 := value cast U32

[[warning=yes, intrinsic-type-cast-overflow]] \
castedValue2 := value cast U32


[[warning=always, intrinsic-type-cast-overflow]]

//...

[[warning=never, intrinsic-type-cast-overflow]]

//...

[[warning=default, intrinsic-type-cast-overflow]]

//...

[[warning=error]]                                   // all warnings are errors
[[warning=warning, intrinsic-type-cast-overflow]]    // this specific warning is
                                                    // just a warning

//...

[[warning=never, x-strange-experimental-alignment-warning]]
````


#### Warning push and pop

The state of all warnings can be pushed and popped into a compile stack using the `[[warning=push]]` and `[[warning=pop]]` compiler directives. A push operation will keep a copy of all warning states and push these warnings on the compiler stack. A pop operation will take the last pushed compiler warning states and apply these warning states as the current warning states.

Upon importing a module, all warnings are pushed and all warnings are popped at the end of an `import`. This ensures that imported modules cannot affect the warning state of the importing module.

````zax
[[warning=push]]

[[warning=never, intrinsic-type-cast-overflow]]

//... code with the warning disabled

[[warning=pop]]
````


#### Warning registry and meanings

The following are registered warnings, default states, and their meaning:
* `warning-directive` (always)
    * a warning directive was encountered where a specific registered warning name was not provided
* `to-do`
    * a warning directive to indicate code is known to be missing and must be completed at a later point in time
* `intrinsic-type-cast-overflow` (always)
    * an intrinsic type may overflow during the `cast` operator to a type with lower bit sizing
* `switch-enum` (always)
    * the `switch` statement lacks `case` statements for one or more defined named enumerations
* `switch-enum-default` (always)
    * the `switch` statement lacks a `default` statement to an `enum` known to be casted from an underlying type or used with bitwise operators and thus may contain values outside of known defined named enumerations
* `condition-not-boolean` (always)
    * the condition passed into an `if`, `while`, `do`/`while` or other conditional statements do not resolve to a `Boolean` type
* `switch-boolean` (always)
    * a switch should not be used to compile a `Boolean` condition
* `shift-count-overflow` (always)
    * a shift operation cause equal or more bits to shift than the type allows
* `dangling-reference-or-pointer` (always)
    * code has been found to cause a reference or pointer to a known destructed value
* `deprecate-directive` (always)
    * usage of a deprecated function, variable, or type discovered
* `directive-not-understood` (always)
    * usage of a non `x-` prefixed directive was not understood or usage of a non `x-` prefixed directive option was not understood
* `source-not-found` (always)
    * a source file was requested to be parsed but it cannot be located
* `asset-not-found` (always)
    * a asset file was requested to be copied but it cannot be located
* `shadow-found` (always)
    * a declaration was made which hides another declaration from being accessible
* `uninitialized-data` (always)
    * an attempt was made to access known uninitialized data
