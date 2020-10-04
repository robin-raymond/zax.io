
# [Zax Programming Language](index.md)

## Aliasing

### Keyword aliasing

Zax supports keyword aliasing using the declaration `alias keyword`. The verbose nature of the default keywords are made to ensure the language is natural rather than using shortened keywords that are inconsistently applied to the language. However, the keywords can be aliased to friendly names to type. The same reserved status applies to any aliased keywords within the context of where the original keyword would be allowed. An alias does not remove the original keyword from being available.

Aliased keywords must be declared prior to usage and they are scoped to the module (unless exported). Attempting to use an aliased keywords prior to declaration will cause the compiler to treat the symbolic name as a normal symbolic declaration instead of its replacement alias.

A word of caution: the temptation can be to use very short abbreviated words to save on typing but reading a language should be natural, not lead to too many variable name conflicts, and be consistent and non-confusing. For projects shared publicly, this can even be a greater source of confusion if different projects use different keywords and may be difficult for newcomers to learn new keyword standards. Besides, modern editors typically have common word completion.

Common aliases are placed in `Module.System.Keywords`:

````zax
// important common keywords that should be familiar to existing programmers
[[resolve=now]] :: import Module.System.Keywords
````

From the `Module.System.Keywords`
````
// some example keywords that are aliased in the keywords module
[[export=always]]

const :: alias keyword constant
property :: alias keyword mutator
static :: alias keyword once
downcast :: alias keyword outercast

// ...

[[export=never]]
````


### Operator and expression aliasing

Zax allows operators and expressions to become aliased into keywords using the declaration `alias operator keyword`. This allows natural language keywords to be applied to operators should a natural language alternative to the operator be desirable. Whenever the aliased keyword is seen, the operator is automatically substituted.

Aliased operators and expressions must be declared prior to usage and they are scoped to the module (unless exported). Attempting to use an aliased operator prior to declaration will cause the compiler to treat the operator as a symbolic name instead of its replacement alias.

A small caveat: comments are not operators and cannot be aliased. The compiler treats comments as a first pass filtering of text meant for humans and they are not treated as part of the language (even where a compiler might capture the comments for documentation purposes).

````zax
add :: alias operator keyword +
not :: alias operator keyword !
ellipses :: alias operator keyword ...
this :: alias operator keyword _

value := 1 add 1
isTrue := not (value != 2)

// "ellipses" becomes `...`
print : ()(ellipses) = {
    // ...
}

MyType :: type {
    value : Integer

    func final : ()(value : Integer) = {
        // `this` is not a keyword, it's an alias of `_`
        this.value = value

        // functionally equivalent to the above statement
        _.value  value
    }
}
````


### Type aliasing

Types can be aliased from their original name to a new name. The `alias` of the original type is equivalent to the original type.

````zax
MyType :: type {
    a : Integer
    b : String
}

type1 : MyType
type1.a = 42
type2.b = "hello"


CoolType :: alias MyType

type2 : CoolType

// the assignment works as `MyType` and `CoolType` are one and the same
type2 = type1
````


### Type aliasing adding or changing qualifications

When a type is aliased, the alias can add or change qualifiers of the original type. This includes specifying the type is a reference, a pointer, an array, a type of pointer, mutability, as well as other options. The underlying alias type remains the same but qualifiers become part of the qualifiers around the aliased type whenever the type is used.

````zax
MyType :: type {
    a : Integer
    b : String
}

DeepMyType :: alias MyType constant & deep

// the `shallow` qualifier will override the previous deep qualifier 
ShallowMyType :: alias DeepMyType shallow

func final : ()(input : DeepMyType) = {
    // `input` is `constant` type of `MyType` with `deep` semantics
    // ...
}

func final : ()(input : ShallowMyType) = {
    // `input` is `constant` type of `MyType` with `deep` semantics
    // ...
}

value : MyType

func(value)             // will call the shallow version since
func(value as deep)     // will call the deep version
````


### Type aliasing for meta types

Aliases can not only refer to specific types but they can be aliases to meta-types and even include meta-type arguments that can become arguments to the underlying aliased meta-type.

````zax
MyType $(TypeA, TypeB) :: type {
    a : $TypeA
    b : $TypeB
}

// creates a concrete alias definition of `MyType` where
// `a` is an `Integer` and `b` is a `String`
MyTypeIntString :: alias MyType$(Integer, String)


// creates a meta type alias definition of `MyType` where
// `a` is an `Integer` and `b` is defined by `$PickType`
MyTypeWithString $(PickType) :: alias MyType$(Integer, $PickType)


// creates a meta type alias definition of `MyType` where
// `a` and `b` are arguments to the type in the same way `MyType` has
// meta-type arguments
AnotherNameForMyType $(UseTypeA, UseTypeB) :: alias MyType$($UseTypeA, $UseTypeB)
````


### Type aliasing of a function

A type of a function can be converted to an alias by creating an alias to a definition of a function variable. The function will automatically become converted to its type where the alias name will represent the underlying function's tpe.

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

// creates an alias to the type of `func` as an alias of a variable
// automatically creates an alias of the type of the variable since variables
// can not be aliased directly (use references to create a type alias)
FuncType :: alias func

// create anotherFunc using the FuncType alias
anotherFunc : FuncType = {
    print(input)
    return intToString(input)
}

// `func` now shares the same definition as `anotherFunc` as `func` can be
// assigned `anotherFunc`
func = anotherFunc
````

