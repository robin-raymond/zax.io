
# [Zax Programming Language](index.md)

## Compiler Warnings and Errors

### `error` directive

#### Forcing the compiler to issue an error

An `error` can forcefully be issued by a programmer by using an `[[error="<message>", <optional-registered-error-name>,name=<opt-name>, value=<opt-value>, name..., value...]]` compiler directive. If a compiler compiles-in an `error` directive, a compiler will display an error and halt. A compiler error may optionally include one of the built-in error names or declare a custom error name with a `x-` prefix.

An optional `name` and `value` are any arguments that are part of an error message to display. A `name` value is looked up in the string and substituted with the `value`. Multiple `name` and `value` pairs may exist.

````zax
if size of Integer != 4 {
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
* `literal-contains-invalid-sequence`
    * a literal was found to contain an invalid character sequence
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
    * name=`$file$` - the file name that could not be found
* `asset-not-found`
    * a asset file was requested to be copied but it cannot be located
    * name=`$file$` - the file name that could not be found
* `wild-character-mismatch`
    * a wild character pattern was used in an incorrect way
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
* `constant-overflow`
    * a constant value overflows the underlying `type`
* `needs-dereferencing`
    * an attempt was made to automatically convert from a pointer to a reference without using the dereference operator (`.`)
* `incompatible-types`
    * an attempt was made to use a `type` where another incompatible `type` was expected
* `no-viable-outer-type-cast`
    * an attempt was made to cast from an inner contained `type` to an outer `type` which does not contain the inner `type`
* `function-not-found`
    * an attempt was made to call to a function that was not found
* `type-not-found`
    * an attempt was made to use a type that was not found
* `function-candidate-not-found`
    * an attempt was made to call a function where none of the function candidates available are a viable choice
* `function-candidate-ambiguous`
    * an attempt was made to call a function where two (or more) matches are equally selectable even with all qualifiers considered
* `unsafe-outer-of-ambiguous`
    * an attempt was made to cast from a contained pointer to a container pointer but which from `type` is ambiguous
* `except-ambiguous`
    * an attempt was made to return a value using the `except` statement but the best match for the return `type` is ambiguous or the defaulted output argument name for the `except` is ambiguous
* `own-relationship-access-ambiguous`
    * which `own` variable is correct is ambiguous in this scenario
* `enum-to-underlying-needs-as-operator`
    * an attempt was made to directly convert an enum to its underlying `type` without first casing to the underlying `type`
* `enum-to-incompatible-type`
    * an attempt was made to convert an enum to a `type` that is not its underlying `type`
* `range-iterator-not-found`
    * the `each` iteration `from` a range did not provided a range iterator
* `named-scope-not-found`
    * cannot find the name referenced in a `break` or `continue` statement
* `named-scope-inaccessible`
    * a `break` or `continue` statement was requested to an inaccessible scope
* `bad-alignment`
    * the alignment is unsupported
* `duplicate-case`
    * a `switch` has duplicate `case` values
* `duplicate-symbol`
    * a non-polymorphic variable, `type` or `union` was re-declared within the same scope
* `condition-expects-boolean`
    * a conditional statement (i.e. `if`, `while`, `redo` `while`) expected a `Boolean` type or an `as` convertible type to `Boolean`
* `missing-end-of-quote`
    * a quote `'` or`"` was started but the matching end quote `'` or `"` is missing
* `missing-end-of-comment`
    * a comment `/*` or `/**` was started but the matching end comment `*/` or `**/` is missing
* `compile-directive-error`
    * a compile directive was issued but the value specified does not evaluate to a compile-time constant
* `compiles-directive-error`
    * the compiles directive failed to compile code and thus forced an error
* `requires-directive-error`
    * the requires directive failed to compile or the evaluated code returned `false`
* `value-not-captured`
    * an attempt was made to access a variable outside of a function or `scope` capture barrier
* `unmatched-push`
    * missing a matching `push` on an `error`, `warning`, `panic`, `variables`, `types`, or `functions` directive
* `scope-flow-control-skips-declaration`
    * an attempt was made to use `break` or `continue` within a scope which would cause important declarations to be skipped
* `inline-function-not-final`
    * an attempt was made to declare a non-final function as `[[inline]]`
* `constant-syntax`
    * a constant was found to contain a syntax error
* `syntax`
    * a syntax error was found
* `output-failure`
    * an attempt to generate output or copy an asset to the output failed
    * name=`$file$` - the file name that could not be created or updated
* `explicit-last-cannot-receive-lease`
    * an attempt to call an explicitly by-reference or by-pointer `last` qualified value with a `lease` qualified value
* `explicit-copy-cannot-receive-move`
    * an attempt to call an explicit by-value `copy` with a `move` qualified value
* `forward-symbol-type-mismatch`
    * an `forward` of a symbol was performed but the defined symbol type does not match the `forward`


### Forcing the compiler to issue a warning

A `warning` can forcefully be issued by the compiler by using the `[[warning="<message>", <optional-registered-warning-name>, name=<opt-name>, value=<opt-value>, name..., value...]]` compiler directive. If the compiler compiles-in a `warning` directive, the compiler will display the warning and continue to compile. A compiler warning may optionally include one of the built-in warning names or declare a custom warning name with a `x-` prefix.

The optional `name` and `value` are any arguments that are part of the error message to display. The `name` value is looked up in the string and substituted with the `value`. Multiple `name` and `value` pairs may exist.

````zax
if size of Integer > 4 {
    [[warning="this poorly written code hasn't been testing on Integers larger than 4 bytes"]]
}

// ...

[[warning="random warning for no good reason", x-blue-moon]]

// ...
````


### `warning` directive

#### Enabling/disabling a compiler warning

A warning can be enabled or disabled by using the `[[warning=<option>, <optional-registered-warning-name>]]`. If the compiler compiles-in the `warning` directive, the compiler will enable or disable the compiler warning or treat a specific warning as an error or merely as a warning. All compilers must register their warnings meanings into a shared authoritative registry. Experimental non-standard warnings names must include an `x-` prefix as part of the warning name. Naming a specific warning is optional. If the compiler warning name is not specified, the directive will apply to all warnings.

The options for warnings are:
* `yes` - enables the warning for only to the current statement
* `no` - disables the warning for only to the current statement
* `always` - enables the warnings for all statements that follow
* `never` - disables the warnings for all statements that follow
* `default` - enables or disables the warnings for all statements that follow according to the compiler's defaults
* `error` - forces enabled warnings to be treated as an error for all statements that follow
* `warning` - forces enabled warnings to be treated as merely a warning for all statements that follow
* `lock` - disallows any imported module from changing a warning state
* `unlock` - allows any imported module to change a warning state


````zax
randomValue final : (output : S32)() = {
    // ...
    return output
}

value := randomValue()

[[warning=no, intrinsic-type-cast-overflow]] \
castedValue1 := value unsafe as U32

[[warning=yes, intrinsic-type-cast-overflow]] \
castedValue2 := value unsafe as U32


[[warning=always, intrinsic-type-cast-overflow]]

// ...

[[warning=never, intrinsic-type-cast-overflow]]

// ...

[[warning=default, intrinsic-type-cast-overflow]]

// ...

[[warning=error]]                                   // all warnings are errors
[[warning=warning, intrinsic-type-cast-overflow]]    // this specific warning is
                                                    // just a warning

// ...

[[warning=never, x-strange-experimental-alignment-warning]]
````


#### Warning push and pop

The state of all warnings can be pushed and popped into a compiler stack using the `[[warning=push]]` and `[[warning=pop]]` compiler directives. A `push` operation will keep a copy of all warning states and push these warnings on a compiler's stack. A `pop` operation will take the last pushed compiler warning states and apply these warning states as the current warning states.

Upon importing a module, all warnings are pushed and all warnings are popped at the end of an `import`. This ensures that imported modules cannot affect the warning state of a module performing an import.

````zax
[[warning=push]]

[[warning=never, intrinsic-type-cast-overflow]]

// ... code with the warning disabled

[[warning=pop]]
````


#### Warning registry and meanings

The following are registered warnings, default states, and their meaning:
* `warning-directive` (always)
    * a warning directive was encountered where a specific registered warning name was not provided
* `to-do` (always)
    * a warning directive to indicate code is known to be missing and must be completed at a later point in time
* `intrinsic-type-cast-overflow` (always)
    * an intrinsic type may overflow during the `unsafe as` operator to a type with lower value capacity
* `switch-enum` (always)
    * a `switch` statement lacks `case` statements for one or more defined named enumerations
* `switch-enum-default` (always)
    * a `switch` statement lacks a `default` statement to an `enum` known to be casted from an underlying `type` or where bitwise operators wer used and thus may contain values outside of known defined enumerations
* `condition-not-boolean` (always)
    * the condition passed into an `if`, `while`, `redo` `while` or other conditional statements do not resolve to a `Boolean` type
* `switch-boolean` (always)
    * a switch should not be used to compile a `Boolean` condition
* `shift-count-overflow` (always)
    * a shift operation cause equal or more bits to shift than the type allows
* `shift-negative` (always)
    * a shift operator was given a negative value
* `dangling-reference-or-pointer` (always)
    * code has been found to cause a reference or pointer to a value known to be destructed
* `deprecate-directive` (always)
    * usage of a deprecated function, variable, or type discovered
* `directive-not-understood` (error)
    * usage of a non `x-` prefixed directive was not understood or usage of a non `x-` prefixed directive option was not understood
* `source-not-found` (always)
    * a source file was requested to be parsed but it cannot be located
    * name=`$file$` - the file name that could not be found
* `asset-not-found` (always)
    * a asset file was requested to be copied but it cannot be located
    * name=`$file$` - the file name that could not be found
* `shadowing` (always)
    * a declaration was made which hides another declaration from being accessible
* `uninitialized-data` (always)
    * an attempt was made to access data believed to be uninitialized
* `lifetime-linkage-to-unrelated-pointer` (always)
    * an attempt was made to link a pointer of an unrelated `type` to a `strong` or `handle` pointer
* `naming-convention` (always)
    * a declaration was found to not follow standard naming conventions for the language
* `result-not-captured` (error)
    * every return result needs to be captured (unless marked with `#`)
* `variable-declared-but-not-used` (error)
    * every declared variable must be used (unless marked with `#`)
* `duplicate-specifier` (always)
    * a duplicate qualifier was specified, e.g. `constant`, `immutable`, `mutable`, `deep`, etc.
* `type-mutability-qualifier-not-supported`
    * a specific `type` mutability qualifier was selected which is not supported
* `specifier-ignored` (always)
    * a qualifier was specified which is ignored in the context
* `asynchronous-not-deep` (always)
    * `task`, `promise` and `[[asynchronous]]` functions having pass by-value arguments should have a `deep` qualifier for parallel processing
* `unknown-directive` (error)
    * a directive not prefixed with `x-` was encountered which was not understood
* `unknown-directive-argument` (error)
    * a directive argument not prefixed with `x-` was encountered which was not understood
* `forever` (always)
    * code was detected that appears to run forever without the code that follows including a `[[never]]` directive
* `divide-by-zero` (error)
    * a numerical type was divided by 0
* `always-true` (always)
    * a condition always appears to be `true` (without using the `[[always]]` directive)
* `always-false` (always)
    * a condition always appears to be `false` (without using the `[[never]]` directive))
* `float-equal` (always)
    * a floating point was used in an `==` comparison
* `size-of-zero` (always)
    * a `size-of` operator was used on a `type` whose size is always zero
* `cpu-alignment-not-supported` (always)
    * a `type` is using an alignment for a `type` which the CPU knowingly cannot support or access
* `upgrade-directive` (always)
    * usage of an obsolete directive was found and should be upgraded to its replacement (or removed)
* `statement-separator-operator-redundant` (always)
    * a statement separator and combine operator (`;`) was found but is not connected to another statement in this context
* `export-disabled-from-export-never` (always)
    * an export keyword was encountered on a `type` that cannot be exported due to the `[[export=never]]` directive
* `redundant-access-via-self` (always)
    * an attempt to access a value via the self variable (`_`) was made in a non-ambiguous situation
* `redundant-access-via-own` (always)
    * an attempt to access a value via an `own` declared contained variable name where the variable name is not ambiguous
* `bad-style` (always)
    * the style of the code is found to be undesirable and language or compiler changes in the future may be breaking
* `descope-directive-required` (always)
    * calling an `[[inline-descope]]` function requires the `[[descope]]` declaration to acknowledge the current scope is polluting with new variables from an inlined function
* `lease-or-last` (error)
    * the compiler is uncertain if a `lease` or `last` polymorphic version of a function should be used where a function that received a `last` instance is then passing that instance to another function which accepts both a `lease` or a `last` instance
* `copy-or-move` (error)
    * the compiler is uncertain if a `move` or `copy` polymorphic version of a function should be used where a function that received a `move` instance is then passing that instance to another function which accepts both a `move` or a `copy` instance
* `newline-after-continuation` (always)
    * expecting new line after continuation operator
* `allocation-into-raw-pointer` (always)
    * the compiler detected an attempt to allocate into a raw pointer rather than using a `unique` or other managed pointer type
* `generated-file-not-touched` (error)
    * the compiler attempted to load a source file that was not touched by a generator routine
