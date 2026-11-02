# Zax language vision

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers evaluating, learning, or contributing to Zax |
| Applies To | Intended language direction; not a formal specification |
| Implementation State | Not established by this repository |
| Owns | Zax's purpose, audience, foundational language direction, goals, intended advantages, non-goals, accepted high-level tradeoffs, and summary route to reusable language principles |
| Does Not Own | Full reusable [language-principle definitions](principles.md) or exact feature semantics |

## What Zax is

Zax is a general-purpose programming language intended to provide the benefits
of a high-level language without hiding the lower-level behavior, costs, and
tradeoffs producing those benefits.

It aims to let a developer move between expressive application code,
performance-sensitive systems code, direct memory and hardware control,
asynchronous execution, and build-time programming without changing languages
or surrendering visibility into what the program is doing.

Zax is not a formal specification or completed implementation today. This
document defines the direction against which detailed language concepts will be
evaluated.

## Who Zax is for

Zax is for general-purpose programmers who want high-level capabilities while
retaining the ability to understand:

- how data is represented;
- when allocation, copying, indirection, synchronization, or runtime support is
  involved;
- which safety and lifetime policies are active;
- how code interacts with hardware and foreign systems; and
- which work occurs during the build rather than at runtime.

A motivated developer should be able to begin with safer features and
established patterns, then deliberately adopt lower-level or riskier mechanisms
as their knowledge and requirements grow.

Zax does not assume that every programmer must use every advanced capability.
It does assume that programmers who choose raw memory, unchecked lifetime
operations, assembly, foreign interfaces, or advanced concurrency accept
responsibility for understanding those choices.

## The motivating problem

Programming languages commonly make strong tradeoffs:

- low-level control can come with high complexity or weak high-level
  facilities;
- strong compile-time safety can restrict data structures and control flow;
- high-level convenience can hide allocation, indirection, garbage collection,
  runtime metadata, or layout;
- object-oriented or functional defaults can steer most designs toward one
  paradigm;
- build logic, code generation, dependency resolution, and asset processing can
  require several additional languages and toolchains; and
- long compatibility histories can make coherent redesign difficult.

Zax cannot eliminate those tradeoffs. Its goal is to make them more visible and
more selectable. When a cost or risk changes, the language should help the
programmer understand where it moved rather than describing it as gone.

## Reusable language principles

The full human-facing [Zax language principles](principles.md) catalog guides
source, APIs, and future concept work. In summary, Zax:

- provides high-level expression without hiding consequential fundamentals;
- makes meaningful costs visible and selectable;
- supports explicit policy choices rather than one compulsory policy;
- favors expressive flexibility that makes programmer intent clear and rejects
  ambiguous source rather than guessing;
- keeps observable order and optimization behavior trustworthy; and
- prefers familiar, readable forms when they fit the design.

The principles catalog owns the complete reusable statements, their programmer
value, representative applications, and tensions. This vision owns the
foundational direction and feature commitments below.

## Foundational language direction

### Data-oriented organization and composition

Zax puts data shape, representation, and flow ahead of class hierarchy and
object identity.

It favors explicit composition over inheritance. Constructors, functions
associated with types, polymorphism, and other useful techniques may still
exist, but they do not require a traditional object-oriented model.
Current named containment, data publication, expected-type projection, behavior
exposure, abstract fulfillment, and outer casting are described by
[Zax composition](composition.md).

Structural typing is an intended direction for reducing rigid nominal
relationships. [Zax structural shapes and compatibility](structural-shapes-and-compatibility.md)
defines the current identity-versus-shape model, safe binary-compatible
postures, anchored views, and explicit shape-changing operations. Whole-type
contracts, generic structural constraints, and runtime dispatch remain separate
future work.

Current scalar design keeps representation explicit:
[integers](integers.md) define finite `F = 0` coefficients,
[fixed-point scalars](fixed-point-scalars.md) define bounded coefficient scales,
[binary floating-point scalars](floating-point-scalars.md) define ordinary and
legacy exponent formats, and [endianness](endianness.md) defines concrete byte
order across those families.

### Language-integrated build-time execution

Zax code can execute as part of the build to:

- validate inputs;
- generate code and resources;
- precompute data;
- process build assets;
- make build decisions;
- perform related build and post-build actions; and
- share types and implementations between build-time and runtime use.

This reduces dependence on separate scripting languages, generators,
configuration systems, and build tools.

Build-time code is executable code. It must be trusted or run in an appropriate
sandbox. Zax does not make arbitrary third-party build logic inherently safe.

### A bounded safe subset with explicit responsibility

Maximizing compiler-enforced safety is not Zax's organizing principle. Zax
should nevertheless define a safe subset with explicit guarantees.

Code that stays within that subset should receive the guarantees assigned to it.
Operations that leave those guarantees should be recognizable and auditable.
Current proof, unsafe-responsibility, contract-evolution, and lint boundaries are
defined by [Zax safety and analysis](safety-and-analysis.md). Exact unsafe syntax,
category identifiers, and formal guarantees remain future design work.

Safety does not include universal correctness. Memory guarantees do not prevent
deadlock, starvation, resource exhaustion, flawed algorithms, incorrect
authorization, weak cryptography, or other logic errors.

### Multiple lifetime strategies

No single lifetime strategy fits every program.

Zax supports one explicit lifetime foundation with several ownership and
allocation strategies. Every instance lives within a life path; references
borrow fixed instance places without owning them; raw-pointer operations require
proof or unsafe responsibility; unique pointers transfer one owner; and
strong/weak pointers provide shared ownership and observation through visible
control-block costs.
Arenas separately control storage and allocation disposition.

The plurality is intentional. The language and documentation must make the
choice among strategies understandable, including runtime, memory,
synchronization, compile-time, destruction-timing, and storage-recovery costs.
See [Zax lifetimes and references](lifetimes-and-references.md) and
[Zax pointers and arenas](pointers-and-arenas.md).

Exact allocator interfaces, pointer representation, cycle-collection mechanics,
thread-affinity syntax, and async lifetime behavior remain future design work.

### First-class asynchronous programming

Asynchronous execution, coroutines, and concurrency are foundational concerns.
Computer I/O is inherently asynchronous even when exposed through blocking
interfaces.

Zax should provide first-class async semantics without imposing their runtime
cost on programs that do not use them. Programs that do use async may require
state machines, executors, event loops, scheduling, allocation,
synchronization, cancellation, and platform services. Those costs should remain
visible and selectable where practical.

Async I/O, coroutines, concurrency, scheduling, and parallelism are related but
distinct concepts.

### Direct assembly and selected foreign interoperability

Direct integration with assembly is a foundational requirement.

Interoperability with C and C++ is important, but selective in both directions.
Zax-native features need not all have foreign equivalents, and foreign-language
features need not become Zax concepts.

Zax should support selected foreign representations and calling conventions
without importing another language's complete semantic model. Adapters and
explicit low-level boundaries remain appropriate when models differ.

### Self-contained and durable builds

Zax aims to let projects express build logic, source dependency resolution,
generation, resource processing, and related build actions in Zax.

A project that relies on versioned Zax facilities and pinned, declared inputs
should require only a compatible Zax toolchain and those inputs to rebuild. It
should not inherently require:

- auxiliary scripting languages;
- a separate package-management step;
- an official package store; or
- prebuilt binary artifacts.

Zax does not prohibit external tools, host-specific behavior, mutable services,
or binary artifacts. Projects that choose them may weaken portability,
reproducibility, or long-term buildability. Zax provides the means to avoid
those dependencies; it does not guarantee that every project will do so.

Source dependencies may be selected through repositories, tags, branches,
versions, or optional package schemas. Durable builds require human-friendly
selectors to resolve to verifiable immutable content. The exact resolution,
locking, provenance, signing, and sandbox policies remain future design work.

## Foundational goals

### Programmer model

- Provide a coherent language from high-level expression to low-level control.
- Make meaningful runtime and build-time costs understandable.
- Let programmers select policies appropriate to their domain.
- Keep data representation and flow visible where they matter.
- Support progressive use of advanced mechanisms rather than requiring them
  everywhere.

### Performance and systems capability

- Produce efficient compiled programs suitable for performance-sensitive work.
- Support direct memory, layout, alignment, allocation, assembly, and
  ABI-relevant control.
- Avoid mandatory runtime services and overhead unrelated to facilities used.
- Support data-oriented organization and hardware-conscious processing.

### Expressiveness

- Reduce boilerplate without hiding consequential behavior.
- Treat functions, composition, multiple values, and flow control as primary
  building blocks.
- Provide high-level resource-management tools alongside raw mechanisms.
- Support compile-time abstraction without textual preprocessing.

### Build model

- Use Zax for build-time execution, reflection, generation, validation, and
  build decisions.
- Reuse code and types across build time and runtime.
- Integrate source dependency resolution into the build.
- Keep compiler and library responsibilities separable, while still providing the
  language support that source depends on.
- Make self-contained and durable builds achievable without mandatory external
  package or build infrastructure.

### Type and data model

- Provide strong, explicit types while retaining deliberate low-level
  boundaries.
- Develop structural typing and composition without rigid inheritance trees.
- Support types and abstractions appropriate to local, generated, and
  metaprogrammed code.

### Longevity and usability

- Remain general-purpose rather than limited to one industry.
- Favor established ideas when they fit.
- Diverge when a cleaner, more coherent design earns the difference.
- Maintain readable ordinary code and explain advanced costs.
- Minimize unnecessary dependency on auxiliary languages and tools.

## Intended advantages

If the detailed design and tooling deliver these goals, Zax should offer:

- one language across application logic, low-level systems work, build logic,
  generation, and resource processing;
- more deliberate performance and memory decisions;
- high-level convenience without a mandatory managed runtime;
- flexible data modeling through composition and structural relationships;
- build-time automation using the same types and libraries as runtime code;
- lifetime and safety strategies matched to domain needs;
- direct low-level and foreign-system boundaries; and
- fewer external requirements for durable source builds.

These are intended outcomes, not claims about a completed implementation.

## Non-goals

Zax does not aim to require:

- universal enforcement of memory, lifetime, or concurrency safety;
- prevention of every programmer mistake;
- a garbage collector or managed runtime;
- one mandatory memory-management strategy;
- class inheritance as the primary reuse model;
- a purely functional, object-oriented, or other single-paradigm style;
- hidden memory layout, allocation, indirection, or machine cost;
- textual preprocessing;
- exception-based error handling as the required error model;
- one mandatory system library;
- an external package-management step;
- an official or centralized package store;
- prebuilt binary dependencies;
- popularity, novelty, or maximum theoretical power as ends in themselves; or
- immediate specification, implementation, and ecosystem completeness.

These boundaries do not prohibit optional libraries, tools, services, adapters,
or programming styles. They prevent those choices from becoming mandatory
language-wide policy.

### Required language support is not an optional library

Rejecting one mandatory conventional system library does not remove the support
the language itself requires. Distinguish:

- optional library APIs such as collections, I/O, networking, and frameworks,
  which a program may decline entirely; and
- required language support for fundamental types, protected primitive
  operations, and the runtime or lifetime behavior that source actually uses.

An implementation may satisfy a required operation through instructions, constant
folding, emitted target code, an intrinsic, or an automatically linked helper.
Zax does not promise which of those an implementation chooses; the
programmer-visible guarantee is only that the operation and its semantics are
available. A toolchain that cannot supply required support is incomplete rather
than a program that failed to link an optional library.

## Accepted tradeoffs

Programmer choice is not free flexibility.

Providing several lifetime, safety, allocation, concurrency, and build-time
mechanisms increases:

- the conceptual surface of the language;
- programmer responsibility;
- the number of interactions that must remain coherent;
- compiler and tooling complexity;
- the need for precise diagnostics and documentation; and
- the possibility of ecosystem conventions diverging.

Zax accepts those pressures in exchange for explicit control, but advanced
mechanisms must not make ordinary code unnecessarily difficult to read or write.

Full build-time execution also creates security, determinism, caching, and
cross-compilation concerns. First-class async may require runtime or library
support when used. Structural typing must coexist with low-level layout.
Long-term source durability must coexist with language evolution.

Later concept reviews must resolve these pressures rather than hiding them.

## Inspiration

Zax is informed by decades of programming-language design and practical
development experience.

Influential languages include C, C++, Pascal, Java, JavaScript, Jai,
Objective-C, C#, F#, Haskell, and others. Zax adopts, combines, or reworks ideas
according to its own goals rather than attempting to reproduce any one
predecessor.

The name pays homage to the Zax from Dr. Seuss's *The Sneetches and Other
Stories*.

Inspiration does not establish semantic equivalence. A future similarity guide
may help developers compare accepted Zax concepts with other languages without
claiming exact historical provenance.

## Current maturity

This vision is accepted conceptual direction, not a formal compatibility
promise.

The pre-existing topic pages at the repository root predate this ownership model
and remain design input until individually reviewed. Reviewed concept owners
under `language/` state the accepted concerns assigned to them. Accepted
reusable cross-cutting principles are owned by
[Zax language principles](principles.md). Accepted
statement-level newlines, continuation, body formation, layout, and comments are
owned by [Zax source structure](source-structure.md). Accepted declaration,
initialization, binding visibility, qualifier, name-resolution, and assignment
boundaries are owned by
[Zax declarations and bindings](declarations-and-bindings.md).
Accepted transparent aliases, explicit identity types, admission, projection,
and exposure boundaries are owned by
[Zax identity types](identity-types.md). Accepted finite integer families,
representations, conversions, and arithmetic contracts are owned by
[Zax integers](integers.md), with complete protected operation behavior in the
[integer operator catalog](integer-operator-catalog.md).
Accepted bounded fixed-point coefficient/scale formats are owned by
[Zax fixed-point scalars](fixed-point-scalars.md). Accepted ordinary,
profile-selected, and named legacy binary floating formats are owned by
[Zax binary floating-point scalars](floating-point-scalars.md).
Accepted intrinsic little/big scalar specialization,
active/target/compiler-host selection, and numeric-versus-representation endian
behavior are owned by [Zax endianness](endianness.md).
Accepted structural identity separation, direct and flattened shape,
compatibility postures, anchored recasting, and explicit structural
transformation are owned by
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md).

Exact behavior outside the accepted concept owners, along with formal
guarantees, diagnostic contracts, and implementation contracts, remains later
focused work.

For current navigation, return to the [Zax website entry point](../index.md).
