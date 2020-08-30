# Zax Programming Language

## Description

Zax is a modern refresh of a compile time language which offers high level language capabilities through to low level memory access. The language does not enforce type or memory safety but can be used in a type safe manner if desired.

## Goals

* Small and performant code input
* Fast and efficient code output
* Data orientated design
* Structural typing system
* Flow control focused
* Full meta programming and reflection
* Prioritization of proven language features
* Long term source compilation stability
* Pay (in terms of cost/efficiency) for what you use
* Build control within the language
* Full compile time code execution

## Non Goals

* Preprocessor
* Enforced memory management
* Enforced safety
* Enforced language feature usage (containing costly runtime overhead)
* Enforced system library usage
* Exceptions
* Garbage collection
* Package manager (source importation is favoured)
* Object oriented modeling

## Key Features

Zax is a strongly typed compile time language with some key features:

* Low level features
    * Raw memory access
    * Raw memory `cast`
    * Bitwise operations
    * Sized types and alignment
    * Manual / raw memory allocation / deallocation
    * Memory clear and memory copy
    * Custom allocators
    * Collective allocation/deallocation
    * Raw pointers and references
    * Uninitialized memory type control
    * Direct type casting
    * Allocation vs initialization control
    * `union` types
    * Structure of arrays or array of structure support
* Type and Flow control features
    * Familiar flow controls
        * `if`/`else`
        * `switch`, `case`, `default`
        * `while`
        * `do`/`while`
        * `for`, `foreach`
    * `scope` control and logic grouping with `break` and `continue`
    * Arrays
    * Strongly sized integers, floating points, enums, boolean, strings
    * Enum with underlying type
    * Locally defined variables
    * Locally defined types
* High level features
    * Sized arrays, including strings
    * `own`, `strong`, `weak`, `handle` and `collect` memory
    * Default type initialization
    * Optional functional arguments
    * Multiple functional argument returns
    * Polymorphic types and functions
    * `mutable` and `immutable` types
    * Operator overloading
    * Constructors and destructors
    * Type default value initialization (including memory allocation)
    * Lambda functions with capture
    * Member functions
    * Namespace importation control
    * Module importation direct from source control
    * Multi-shared module compilation control
    * Source preservation by asset/module management
    * Library / compiler separation
    * Type composition with inner/outer casting (without inheritance)
    * Safe type conversion using `as`
    * Selective runtime type information
    * `defer` end of scope code execution
    * `private` and `hidden` types during importation
    * Type and variable `alias`
    * Type extension
    * Anonymous types
* Asynchronous programming
    * `atomic` variables and atomic operations
    * Asynchronous function calling
* Meta programming
    * `enum` meta data
    * Automatic compile time `if` / `else`
    * Custom compile time literals
    * Variadic functions
    * Easily defined meta-function parameters
    * Compile time code reflection
    * Compile time code execution
    * Compile time code generation
    * Compile time code checking
    * Full compile type meta-data and traits
    * Built-in build control (tooling chain/ide not required to build)
* Other
    * Usage deprecation
    * No header files
    * Runtime code panics (with selective disabling)
    * Harmonized warning suppression code mechanisms across compilers

## Credit and Inspiration

Zax was inspired by [C++](https://en.wikipedia.org/wiki/C%2B%2B), [C](https://en.wikipedia.org/wiki/C_(programming_language)), [Pascal](https://en.wikipedia.org/wiki/Pascal_(programming_language)), [Java](https://en.wikipedia.org/wiki/Java_(programming_language)), [JavaScript](https://en.wikipedia.org/wiki/JavaScript), [Jai](https://en.wikipedia.org/?title=JAI_(programming_language)), [Objective-C](https://en.wikipedia.org/wiki/Objective-C), and finally pays homage to [Dr. Seuss](https://en.wikipedia.org/wiki/The_Sneetches_and_Other_Stories#The_Zax).


## [Basics](basics.md)

## [Enums](enums.md)

## [Type Definition](type-definition.md)

## [Flow Control](flow-control.md)

## [Scope](scope.md)

## [Composition](composition.md)

## [Pointer and References](pointers.md)

## [Memory Allocation](memory-allocation.md)

## [Custom Allocators](custom-allocators.md)

## [Handle Pointers](handle.md)

## [Strong and Weak Pointers](strong-weak.md)

## [Functions](functions.md)

## [Optional Types](optional.md)

## [Constructors and Destructors](ctor-dtor.md)

## [Arrays](arrays.md)

## [Casting](casting.md)

## [Context Type](context.md)

## [Namespacing and Importing Modules](namespacing.md)

## [Mutability](mutable.md)

## [Meta-Functions](meta-functions.md)

## [Partial Types](partial.md)

## [Concurrency](concurrency.md)

## [Compiler Directives](compiler-directives.md)

## [Compiler Warnings and Errors](warnings-errors.md)

## [FAQ](faq.md)
