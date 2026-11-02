
# [Zax Programming Language](index.md)

## FAQ

| Field | Value |
| --- | --- |
| Status | Transitional question-oriented guidance |
| Audience | Human developers evaluating or learning Zax |
| Applies To | Recurring questions; not a formal specification |
| Implementation State | Not established by this repository |
| Owns | Question-specific explanations and routes to current owners |
| Does Not Own | The language vision or detailed feature semantics |

Some feature-specific answers below preserve legacy design input and will be
reviewed with their owning concepts. The
[language vision](language/vision.md) owns Zax's current foundational direction.

### Where did the idea for Zax come from?

Zax grew from long practical experience with programming languages, especially
the desire to combine high-level capabilities with visible low-level behavior
and cost. It draws broadly from systems, procedural, object-oriented, and
functional language design without attempting to reproduce one predecessor.

### Can Zax be used by beginners?

Zax should be learnable by a motivated developer. A developer can begin with
safer facilities and established patterns, then deliberately adopt lower-level
or riskier mechanisms as their understanding and requirements grow.

Zax does not aim to prevent every low-level mistake. Programmers who use raw
memory, unchecked lifetime operations, assembly, foreign interfaces, or advanced
concurrency must understand the consequences.

### Why is the language strongly typed?

Explicit types help make representation, operations, guarantees, and cost
understandable to both developers and build-time code. Structural typing is an
intended direction, but exact identity, equivalence, conversion, and safety rules
remain under design.

### Why is the language compiled?

Zax aims to produce efficient programs with explicit control over memory,
layout, allocation, and runtime support. Compilation also enables
language-integrated build-time execution, where Zax code validates inputs,
generates code and resources, and precomputes work before runtime.

### Why is the language considered data oriented?

Zax puts data shape, representation, and flow ahead of class hierarchy and
object identity. It favors explicit composition over inheritance while retaining
useful facilities such as constructors, functions associated with types, and
polymorphism.

### Why isn't the language functional?

Zax supports useful functional techniques, including first-class functions,
composition, immutability, and explicit transformations. It does not require all
programs to adopt a purely functional model.

### What is Zax's safety direction?

Maximizing compiler-enforced safety is not Zax's organizing principle. Zax
should nevertheless define a safe subset with explicit guarantees and
recognizable boundaries around operations that leave those guarantees.

The exact guarantees and unsafe-boundary mechanism remain future design work.
Memory safety also does not imply freedom from deadlock, resource exhaustion,
logic errors, or security-design mistakes.

### Is another language better than Zax?

Possibly for a particular project. Zax is an attempt to explore a coherent
combination of capabilities and tradeoffs, not a claim that every existing
language is inferior.

### Why does the language clearly resemble X language?

Programming languages share decades of accumulated ideas. Similarity may be
deliberate, independently reasoned, or the result of common design pressures.
Zax adopts ideas according to its own goals rather than preserving another
language's complete model.

### Will Zax get lost among other languages?

Popularity is not a foundational design goal. A coherent and useful language
design can produce valuable knowledge even if its eventual audience is small.

### What is special about Zax?

Zax aims to combine high-level expression, visible low-level behavior,
selectable cost and policy, data-oriented composition, language-integrated
build-time execution, direct hardware access, and first-class async in one
general-purpose language.


### Why are variables declared before types unlike C, C++, or Java?

Not all languages place the type before the variable name. [Pascal](https://en.wikipedia.org/wiki/Pascal_(programming_language)#Data_types) has history longer than [C](https://en.wikipedia.org/wiki/C_(programming_language)) and declared the variable name before the type. Other modern languages such has [Go](https://en.wikipedia.org/wiki/Go_(programming_language)) have made the same decision for their own [reasons](https://golang.org/doc/faq#declarations_backwards).

The beauty of the syntax is subjective, but the reasons distills down to:
* emphasis on the variable name since the grammar is designed to be read left to right
* variable declarations do not require a placeholder keyword to indicate a variable's declaration

````c++
// The variable type is declared first defining the type of the variable
float weight = 0.0;

// Even when the variable type can be deduced, a type placeholder is still
// required.
auto weight = 0.0;
````

````zax
// As the name carries the meaning of the type, the variable name is placed
// first and the type after for emphasis on the variable name when reading
// left to right.
weight : Float = 0.0

// Where the type can be deduced, the type can be entirely eliminated from
// the declaration entirely.
weight := 0.0
````

### Why aren't code examples syntax highlighted?

None of the keyword show up as highlighted:

````zax
:: import Module.System.Types

example :: type MyExample {
    value : Integer
}
````

This site is currently hosted with GitHub which does not appear to support syntax highlighting of languages not officially recognized by GitHub (at least without complex external build processes).


### If this language is data orientated, why does the language support some Object Oriented features like constructors/destructors?

Data orientation puts the focus on data shape, representation, organization, and
flow. Constructors, destructors, functions associated with types, and
polymorphism are useful techniques that do not require class inheritance or a
mandatory object-oriented model.


### Isn't Zax just syntax sugar?

All programming languages provide abstractions over lower-level mechanisms. Zax
is distinguished by the particular guarantees, costs, build-time facilities,
and low-level boundaries those abstractions provide, not merely by alternate
spelling.


### Are virtual functions supported?

Virtual functions are not intended as a first-class Zax concept. Zax can express
explicit function tables, function pointers, composition, and other forms of
dynamic behavior.

C++ virtual dispatch may be accessed through adapters or explicit low-level ABI
work when required. Zax does not need to import C++ virtual semantics merely to
support interoperability.


### Why is there no `null` or `nil` keyword?

The concept of a `null` or `nil` pointer is unsupported. Pointers can be checked if they are valid using the `if` statement. A pointer's default state is to point to nothing. Pointers can be reset to point to nothing. But one key difference exists: Pointers to nothing are not necessarily pointers to the zeroth byte in memory. Pointers to nothing can be defaulted to point to real type instances that is designed to have noop behaviors when invoked. This can help prevent constant checking for nulls where on types where being valid or not has no code impact.

In other words, the language does not have a single magic `null` value indicating if a pointer is valid or not. A pointer to nothing is not valid in the same ways as a pointer to `null` is not valid but the value for a pointer to nothing is not fixed universal value.


### Shouldn't concurrency features just be a library?

Libraries and runtimes will provide important execution policy, but async,
coroutine, and concurrency semantics need language support to remain readable
and to interact coherently with lifetimes, suspension, cancellation, and
build-time analysis.

Programs that do not use those facilities should not incur their runtime cost.
