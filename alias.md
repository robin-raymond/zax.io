
# [Zax Programming Language](index.md)

## Aliasing

### Keyword aliasing

Zax supports keyword aliasing using an `alias keyword` declaration. The verbose nature of the default keywords are made to ensure the language is natural rather than using shortened keywords abbreviations that are inconsistently applied to within the language. However, a keyword can be aliased to friendlier names. As with reserved keywords, aliased keywords become newly reserved keywords only within a context where the original non-aliased keywords would apply. Creating an alias of a keyword does not remove the original reserved keyword.

Aliased keywords must be declared prior to their usage and they are scoped to a module (unless explicitly exported). Attempting to use an aliased keywords prior to an alias keyword declaration will cause a compiler to treat an aliased keyword as a symbolic name instead of an alias. Aliased keywords cannot be `forward` declared. A keyword alias either exists or it does not exist.

A word of caution: Using very short abbreviations might be tempting to save on typing but readability of the language is important. Aliased names should not lead to too many variable name conflicts either. Besides, modern editors typically have common word completion to save on typing. Names should also be consistent and non-confusing. For projects shared publicly, abbreviations and keyword aliasing can be a source of confusion if different projects use different aliased keywords and keyword aliasing may be difficult for newcomers to learn new keyword standards.

Common aliases are placed in `Module.System.Keywords`:

````zax
// important common keywords that should be familiar to existing programmers
[[resolve=now]] :: import Module.System.Keywords
````

From the `Module.System.Keywords`:
````
// some example keywords that are aliased in the keywords module
[[export=always]]

const :: alias keyword constant
property :: alias keyword mutator
static :: alias keyword once

// ...

[[export=never]]
````


#### Compound keyword replacement

Zax supports aliasing compound keywords using `alias keyword`. A single keyword or a set of keywords can be used to replace an existing compound keyword.

````zax
// example keyword aliasing and keyword removal
adjourn :: alias keyword yield suspend
go to sleep :: alias keyword yield suspend

// an original `yield suspend` keyword is replaced with a new keyword `adjourn`
// or the phrase `go to sleep`
someTask final : ()() task = {
    // ...
    adjourn
    // ...
    go to sleep
    // ...
}
````

However, only entire keyword sequences can be aliased and not partial keywords from a sequence.

````zax
// ERROR: the keyword is `yield suspend` not `suspend`
nap :: alias suspend
````


### Operator and expression aliasing

Zax allows operators and expressions to become aliased into keywords using an `alias operator` declaration. This allows natural language keywords to be applied to operators should a natural language alternative to an operator be desirable. Whenever an aliased `operator keyword` is seen in a context where an operator is legal, the underlying operator is automatically substituted.

Aliased operators and expressions must be declared prior to their usage and they are scoped to a module (unless exported). Attempting to use an aliased operator prior to its declaration will cause a compiler to treat an natural language operator as a symbolic name instead of an alias. Aliased operators cannot be `forward` declared. An operator alias either exists or it does not exist.

A small caveat: comments are not operators and cannot be aliased. A compiler performs a first pass filtering of text meant for humans and thus comments are stripped and not treated as part of the language (even where a compiler might capture the comments for documentation purposes later). Other operator-like expressions can be remapped like `...` or `+++` even though they are not technically operators but language expressions.

````zax
add :: alias operator +
not :: alias operator !
ellipses :: alias operator ...
this :: alias operator _
downcast :: alias operator unsafe outer of

value := 1 add 1
isTrue := not (value != 2)

// "ellipses" becomes `...`
print : ()(ellipses) = {
    // ...
}

MyType :: type {
    value : Integer

    func final : ()(value : Integer) = {
        // `this` is not a built-in keyword, it's an alias of `_`
        this.value = value

        // functionally equivalent to the above statement
        _.value = value
    }
}
````


### Type aliasing

Types can be aliased from their original name to declare a new name within a new context. An `alias` of an original `type` is equivalent to the original `type` and can be used anywhere the original `type` would have been legal, except it is declared within the current context.

````zax
MyType :: type {
    a : Integer
    b : String
}

type1 : MyType
type1.a = 42
type2.b = "hello"


CoolType :: alias type MyType

type2 : CoolType

// the assignment works as `MyType` and `CoolType` are the same underlying type
type2 = type1
````


### Type aliasing adding or changing qualifications

When a `type` is aliased, the alias can add or change qualifiers for the original `type`. This includes specifying if a `type` is a reference, a pointer, an array, a pointer qualifier, changes to mutability, as well as other options. The underlying aliased `type` remains the same but qualifiers become part of the qualifiers around an aliased `type` whenever the alias is used.

````zax
MyType :: type {
    a : Integer
    b : String
}

DeepMyType :: alias type MyType constant & deep

// the `shallow` qualifier will override the previous `deep` qualifier 
ShallowMyType :: alias type DeepMyType shallow

func final : ()(input : DeepMyType) = {
    // `input` is `constant` type of `MyType` with `deep` semantics
    // ...
}

func final : ()(input : ShallowMyType) = {
    // `input` is `constant` type of `MyType` with `shallow` semantics
    // ...
}

value : MyType

func(value)             // will call the shallow version since
func(value as deep)     // will call the deep version
````


### Type aliasing for meta-types

Aliases can not only refer to specific types but they can be aliases to meta-types and even include meta-type arguments as part of an underlying aliased meta-type.

````zax
MyType $(A, B) :: type {
    a : $A
    b : $B
}

// creates a concrete alias definition of `MyType` where `A` is an `Integer` and
// `B` is a `String`
MyTypeIntegerString :: alias type MyType$(Integer, String)

// creates a meta-type alias definition of `MyType` where `A` is an `Integer`
// and `B` is defined by `$PickType`
MyTypeWithInteger $(PickType) :: alias type MyType$(Integer, $PickType)

// creates a meta-type alias definition of `MyType` where `A` and `B` are
// arguments to the `type` similar to how `MyType` has meta-type arguments
AliasOfMyType $(UseTypeA, UseTypeB) :: alias type MyType$($UseTypeA, $UseTypeB)
````


### Type aliasing of a function

A `type` of a function can be converted to an alias by creating an alias to a definition of a variable's function `type`. A variable containing a function will automatically become converted to the variable's `type` when using `alias type`, and the alias definition will become the underlying variable's function `type`.

````zax
print final : ()(...) = {
    // ...
}

intToString final : (converted : String)(input : Integer) = {
    // ...
}

func : (output : String)(input : Integer) = {
    // ...
}

// calls `func()` implementation which takes an `Integer` and returns a `String`
resultString1 := func(42)

// Creates an alias to `func`'s ``type`. Automatically creates an alias of a
// variable's `type`. Since variables name can never be never aliased using
// `alias type`, the compiler presumes the alias is intended to be the
// `type` of a variable and not an alias of a variable name.
FuncType :: alias type func

// create anotherFunc using the FuncType alias
anotherFunc : FuncType = {
    print(input)
    return intToString(input)
}

// `func` now shares the same definition as `anotherFunc` and `func` can be
// assigned to the value of `anotherFunc`
func = anotherFunc

// calls `anotherFunc()` implementation which also takes an `Integer` and
// returns a `String`
resultString2 := func(42)
````


### Type aliasing of an anonymous type

A compiler will allow anonymously declared types or functions to become a newly aliased type. There's no distinguishable difference between creating a `type` directly or aliasing of an anonymously declared `type`.

````zax
// The compiler will allow anonymously declared types to become an alias of
MyAliasType :: alias type :: type {
    value1 : Integer
    value2 : String
}

// An alias to an anonymously declared function type
MySimpleFuncPointerAlias :: alias type ()() *
````
