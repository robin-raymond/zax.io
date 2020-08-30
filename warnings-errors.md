
# [Zax Programming Language](index.md)

## Compiler Warnings and Errors

### `error` directive

#### Forcing the compiler to issue an error

An `error` can forcefully be issued by the compiler by using the `[[error="message", optional-registered-error-name]]` compiler directive. If the compiler compiles-in an `error` directive, the compiler will display the error and halt. A compiler error may optionally include one of the built-in error names or declare a custom error name with a `x-` prefix.

````zax
if sizeof Integer != 4 {
    [[error="this poorly written code assumes an Integer is 4 bytes"]]
}

[[error="drugs are bad, m'kay", x-drugs-are-bad]]
````


#### Error registry and meanings

The following are registered errors, and their meaning:
* `error-directive-encountered`
    * an error directive was encountered where a specific registered error name was not provided
* `literal-contain-illegal-sequence`
    * a literal was found to contain an illegal character sequence


### Forcing the compiler to issue a warning

A `warning` can forcefully be issued by the compiler by using the `[[warning="message", optional-registered-warning-name]]` compiler directive. If the compiler compiles-in a `warning` directive, the compiler will display the warning and continue to compile. A compiler warning may optionally include one of the built-in warning names or declare a custom warning name with a `x-` prefix.

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

A warning can be enabled or disabled by using the `[[warning=option, optional-registered-warning-name]]`. If the compiler compiles-in the `warning` directive, the compiler will enable or disable the compiler warning or treat a specific warning as an error or merely as a warning. All compilers must register their warnings meanings into a shared authority registry. Experimental non-standard warnings names must include an `x-` prefix as part of the warning name. Naming a specific warning is optional. If the compiler warning name is not specified, the directive will apply to all warnings.

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
* `intrinsic-type-cast-overflow` (always)
    * an intrinsic type may overflow during the `cast` operator to a type with lower bit sizing
* `optional-source-not-found` (always)
    * a compiler directive to load a source was found but the file cannot be located
