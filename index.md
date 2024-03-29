# Zax Programming Language

## Description

Zax is a data oriented modern refresh of a compile-time language which offers high level language capabilities including low level memory access. The language does not enforce type or memory safety but can be used in a type safe manner if desired.

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
* Full compile-time code execution


## Non Goals

* Preprocessor
* Enforced memory management
* Enforced safety (although compiler rules help safety)
* Enforced language feature usage (containing costly runtime overhead)
* Enforced system library usage
* Exceptions
* Garbage collection
* Package manager (source repository importation is favoured)
* Object oriented modeling


## Key Features

Zax is a strongly typed compile-time language with some key features:

* Low level features
    * Raw memory access
    * Raw memory casting
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
        * `if`, `else`
        * `switch`, `case`, `default`
        * `while` / `until`
        * `redo` `while` / `redo` `until`
        * `each`, `in`
        * `each`, `from`
    * `scope` control and logic grouping with `break` and `continue`
    * Arrays
    * Strongly sized integers, floating points, enums, boolean, strings
    * Enum with underlying type
    * Locally defined variables
    * Locally defined types
* High level features
    * Sized arrays, including strings
    * `unique`, `own`, `strong`, `weak`, `handle`, `hint`, `discard`, and `collect` memory
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
    * `private` and invisible types during importation
    * Type and variable `alias`
    * Partial types
    * Anonymous types
    * `nothing` types instances
    * value polymorphism for functions
* Asynchronous programming
    * Asynchronous function calling
    * Parallel vs sequential allocators
    * Lazy type evaluation
    * Promises
    * Tasks
    * Coroutines
* Meta programming
    * `enum` meta data
    * Automatic compile-time `if` / `else`
    * Custom compile-time literals
    * Variadic functions
    * Easily defined meta-function parameters
    * compile-time code reflection
    * compile-time code execution
    * compile-time code generation
    * compile-time code checking
    * Full compile type meta-data and traits
    * Built-in build control (tooling chain/ide not required to build)
* Other
    * Usage deprecation
    * No header files
    * Runtime code panics (with selective disabling)
    * Harmonized warning suppression code mechanisms across compilers

... and much much more ...

A prototype for the  [zax-compiler](https://github.com/robin-raymond/zax-compiler) is being developed in C++.


## Credit and Inspiration

Zax was inspired by [C++](https://en.wikipedia.org/wiki/C%2B%2B), [C](https://en.wikipedia.org/wiki/C_(programming_language)), [Pascal](https://en.wikipedia.org/wiki/Pascal_(programming_language)), [Java](https://en.wikipedia.org/wiki/Java_(programming_language)), [JavaScript](https://en.wikipedia.org/wiki/JavaScript), [Jai](https://en.wikipedia.org/?title=JAI_(programming_language)), [Objective-C](https://en.wikipedia.org/wiki/Objective-C), [C#](https://en.wikipedia.org/wiki/C_Sharp_(programming_language)), [F#](https://en.wikipedia.org/wiki/F_Sharp_(programming_language)), and [Haskell](https://en.wikipedia.org/wiki/Haskell_(programming_language)) and finally pays homage to [Dr. Seuss](https://en.wikipedia.org/wiki/The_Sneetches_and_Other_Stories#The_Zax).


## [Basics](basics.md)

## [Enums](enums.md)

## [Type Definition](type-definition.md)

## [Flow Control](flow-control.md)

## [Functions](functions.md)

## [Discard Operator](discard.md)

## [Except Error Handling](except.md)

## [Optional Types](optional.md)

## [Alias](alias.md)

## [Scope](scope.md)

## [Lazy Functions](lazy.md)

## [Variadic Functions](variadic.md)

## [Operator Overloading](operator.md)

## [Composition](composition.md)

## [Pointer and References](pointers.md)

## [Memory Allocation](memory-allocation.md)

## [Custom Allocators](custom-allocators.md)

## [Handle and Hint Pointers](handle-hint.md)

## [Strong and Weak Pointers](strong-weak.md)

## [Constructors and Destructors](ctor-dtor.md)

## [Nothing Type Instances](nothing.md)

## [Arrays](arrays.md)

## [Casting](casting.md)

## [Context Type](context.md)

## [Namespacing and Importing Modules](namespacing.md)

## [Forward](forward.md)

## [Mutability](mutable.md)

## [Meta-Functions](meta-functions.md)

## [Meta-Types](meta-types.md)

## [Partial Types](partial.md)

## [Concurrency](concurrency.md)

## [Compiler Directives](compiler-directives.md)

## [Compiler Warnings and Errors](warnings-errors.md)

## [FAQ](faq.md)
