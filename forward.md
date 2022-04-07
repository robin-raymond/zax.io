
# [Zax Programming Language](index.md)

## Forward

### Basic forwarding concept

A compiler will attempt to resolve dependencies in late-binding processes but the programmer may have to give hints to the compiler so that all the symbols are recognizable as their appropriate type. Giving hints will assist the compiler to better recognize symbols and categorize these symbols appropriately during the compilation process.

A `forward` keyword is used to assist in symbol recognition. A symbol name is declared, followed by the `forward` declaration followed by a declaration of how to interpret the symbol. Any defined symbols will only be visible available within the context and exporting rules apply as normal.

A `forward` does not define qualifiers for a variable or type. All `forward` definitions are temporary until the real definition becomes available. A `forward-symbol-type-mismatch` error will occur of a forwarded symbol's type does not match a later defined symbol's type.

Ultimately all forward referenced symbols must become resolved or a compiler will issue an error.

````zax
// forward of variable names
variableName :: forward variable

// forward of type names
TypeName :: forward type

// forward of a value within a forwarded `type`
TypeName.value :: forward variable

// forward of enum type and an enum value
EnumType :: forward enum

// order matters as `EnumType` needs to be forwarded prior to `Value`
EnumType.Value :: forward enum value

// forward of compound word operators
run to a store with :: forward operator pre unary
jump farther :: forward operator post unary
connect with :: forward operator binary
roman :: forward operator literal

// forward declare of a compound word operator within a type
TypeName.shake up :: forward operator pre unary

// forward declare a namespace
ImportedNamespace :: forward namespace

// forward declare a type within a namespace
ImportedNamespace.TypeName :: forward type

// forward declare a variable into a known namespace
Module.globalValue :: forward variable

// ...

// entirely composed statements based exclusively on forwarded symbols

run to a store with variableName

variableName connect with Module.globalValue

variableName jump farther

variableName = roman'XIII'

variableName = EnumType.Value as Integer

// defining a new variable based on a forward declared type which is treated as
// a meta-type
unseenVariable := 3.5 : TypeName$(Float)

anotherUnseenVariable : ImportedNamespace.TypeName = "hello there!"

// usage of a forward declared pre-unary operator based on the `type` for
// unseenVariable
shake up unseenVariable
````
