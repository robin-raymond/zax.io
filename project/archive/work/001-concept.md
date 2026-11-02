# 001 concept: Zax purpose and design principles

| Field | Value |
| --- | --- |
| Status | Historical working material / non-normative / audit-only |
| Work Item | `001` |
| Created | 2026-08-19 |
| Archived | 2026-08-19 |
| Owns | The bounded review defined by the fixed initiating input |
| Does Not Own | Accepted Zax language design or lasting documentation authority |

## Non-authority notice

This completed working record is retained only for targeted audit and recovery.
It is excluded from ordinary onboarding and language-design reading.

Its promoted findings now belong to their current owner documents. Raw and
deferred findings belong to the indexed future-work inputs created during
closure. This file must not be used as a competing definition.

## Fixed initiating input

This section records the information known and aligned when work item `001` was
created. It is intentionally incomplete rather than exhaustive and must not be
rewritten as the work develops.

### Initiating concern

Develop a coherent human-developer-facing understanding of:

- what Zax is;
- which human developers it is for;
- which problems and tradeoffs motivate it;
- its goals and non-goals;
- the design principles against which later language concepts should be
  evaluated.

Use this work to begin establishing clear documentation ownership boundaries.
Identify which lasting documents should own Zax's purpose, audience, goals,
non-goals, and principles, and distinguish those owners from repository and
website routers. Do not reorganize the documentation merely because a possible
boundary is discovered.

### Motivating pressure

The repository introduction, website introduction, feature catalog, and FAQ
contain overlapping and sometimes differently framed descriptions of Zax.
Before refining individual language features, the project needs a stable
conceptual foundation that can guide those decisions without promoting the
legacy wording wholesale.

This first work item also needs to exercise the numbered-work process with a
useful concern whose immediate reasoning surface is smaller than a
cross-cutting feature such as structural typing.

### Known assumptions

- Human-developer comprehension is the primary documentation goal.
- Human-facing documentation may remain technically thorough.
- Existing language pages are legacy design input, not automatic authority.
- Zax is currently in conceptual language-design refinement, not formal
  specification or implementation.
- Compiler and transpiler feasibility may constrain principles, but
  implementation design does not belong in this repository.
- Important concepts should ultimately have one clear owning document and
  focused reading paths.

### Known inclusions

- Project purpose and motivation.
- Intended human-developer audience.
- Goals and non-goals.
- Guiding design principles and the tradeoffs they express.
- Tensions, ambiguity, duplication, or unsupported claims in the existing
  foundational material.
- Candidate ownership boundaries and promotion destinations for the resulting
  accepted concepts.
- The appropriate boundary between concise routers and substantive owner
  documents.

### Known exclusions

- Resolving the detailed semantics of individual language features.
- Deciding exact structural typing, memory, concurrency, metaprogramming, or
  other feature mechanics.
- Designing a compiler, transpiler, runtime, standard library, or C++ mapping.
- Producing a formal language specification.
- Broadly reorganizing the website or root language pages during the working
  discussion.
- Treating the current feature catalog as an automatically accepted commitment.

These exclusions are known starting boundaries, not an exhaustive prohibition
against discovering and recording material consequences.

### Intentionally unresolved framing

- Whether purpose, audience, goals, non-goals, and principles should ultimately
  share one owner or be split across a small number of owners.
- Which existing wording expresses lasting authorial intent and which wording
  should be revised, deferred, or discarded.
- How much of the feature catalog belongs near the project introduction.
- Which principles are fundamental constraints versus present preferences.
- The final website and repository routing changes needed to expose the lasting
  owners.

### Initial stopping guidance

Stop when the work has:

- reached aligned findings about Zax's purpose, intended audience, goals,
  non-goals, and guiding principles;
- dispositioned material foundational conflicts and uncertainties;
- identified plausible lasting owners and router boundaries;
- captured independently decidable feature questions for later work; and
- gathered enough information to perform the pre-promotion documentation fit dry
  run defined in the
  [documentation architecture](../../documentation.md#pre-promotion-documentation-fit-dry-run).

Do not promote findings, reorganize documentation, or begin work item `002`
without the separately required discussion, alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../../documentation.md) — governs documentation
  ownership, focused reading, numbered work, and eventual promotion.
- [Repository README](../../../README.md) — contains the current repository
  description, goals, non-goals, and project route.
- [Website entry point](../../../index.md), from **Description** through **Credit
  and Inspiration** — contains the public introduction, expanded feature
  catalog, and current website framing. Do not follow its language-topic links
  during initial reading.
- The following sections of the [FAQ](../../../faq.md):
  - **When was the idea of Zax inspired?**
  - **Can Zax be used by beginners?**
  - **Why is the language strongly typed?**
  - **Why is the language compiled?**
  - **Why is the language considered data oriented?**
  - **Why isn't the language functional?**
  - **Why doesn't the language enforce memory safety?**
  - **C++ or _insert language here_ is better in every way?**
  - **Why does the language clearly resemble X language?**
  - **Zax will get lost amongst all the other languages?**
  - **What is special about Zax?**
  - **Isn't Zax just syntax sugar?**

### Conditional

- Read a specific language-topic section only when a foundational claim needs
  evidence from the proposed behavior or a concrete contradiction must be
  checked.
- Read later feature-specific FAQ sections only when discussion crosses their
  boundary.
- Inspect focused Git history only when the provenance or original intent of a
  foundational statement becomes consequential.
- Expand beyond this list whenever discussion or a material finding establishes
  a concrete need. State why the additional reading is relevant and keep it
  focused.

### Excluded by default

- `project/archive/`.
- `rfcs/`, including the structural-typing candidate.
- Wholesale reading of all language-topic pages.
- External compiler or implementation repositories.
- Old plans or removed orchestration material recovered from Git history.

These exclusions define the initial reading route, not a ban on later focused
reading when a concrete consequence justifies it.

## Working record

### Baseline status

**Maturity: candidate synthesis for discussion.**

Everything below is a starting interpretation based on the required repository
material, representative language pages already encountered during project
discovery, and general programming-language design knowledge. It is not a claim
that the existing wording is correct or that the language maintainer has accepted
these conclusions.

No external comparative research was performed for this baseline.

### Provisional concise definition

Zax appears to aim at being a general-purpose, natively compiled programming
language for developers who want to move between high-level expression and
low-level control without changing languages or surrendering visibility into
cost.

Its apparent design center is not simply "C++ with different syntax" or "a safer
C." It is an attempt to combine:

- direct access to memory, layout, allocation, and hardware-relevant behavior;
- high-level types, functions, composition, lifetime tools, concurrency tools,
  and metaprogramming;
- language-integrated compile-time execution, reflection, generation, and build
  control;
- explicit selection of cost, safety, ownership, mutability, and abstraction
  policies; and
- a data-oriented model that avoids class inheritance and mandatory runtime
  machinery.

The language appears to trust the programmer to understand consequences. It
tries to make disciplined, safer, and higher-level programming possible without
making one safety model or programming paradigm compulsory.

### Intended human-developer audience

**Maturity: inferred candidate.**

The primary audience appears to be developers who:

- care about runtime performance, memory behavior, layout, and predictable
  overhead;
- want high-level conveniences without a mandatory garbage collector, managed
  runtime, object hierarchy, or prescribed system library;
- are willing to understand the costs and safety implications of their choices;
- want compile-time automation and reflection to be part of the language rather
  than a separate preprocessor, build language, or generator stack;
- work in systems, games, tools, infrastructure, libraries, or other
  performance-sensitive domains, while not limiting Zax to those domains; and
- prefer choosing policies appropriate to a problem over accepting one
  language-wide policy.

The FAQ says beginners can use Zax, but the surrounding material does not appear
to make beginners the primary design center. A more precise candidate position
is:

> Zax should be learnable by a motivated developer, but it does not protect a
> developer from every low-level mistake or remove the need to understand the
> machine-level consequences of code.

That distinction needs confirmation. "Usable by beginners" and "designed around
beginner safety" are different commitments.

### Motivating problem as currently inferred

Existing languages often commit strongly to one or more tradeoffs:

- low-level control at the cost of high complexity or weak high-level
  facilities;
- strong safety guarantees at the cost of restrictions and additional concepts;
- convenient high-level programming at the cost of hidden allocation, runtime
  machinery, garbage collection, or limited layout control;
- functional or object-oriented defaults that steer most designs toward one
  paradigm;
- metaprogramming split across preprocessors, templates, build systems,
  generators, and scripting languages; or
- decades of compatibility constraints that make coherent redesign difficult.

Zax appears motivated by the belief that these tradeoffs should be more visible
and more selectable. The language is trying to provide a coherent toolbox from
which the programmer chooses the required guarantees and costs.

This does not mean all tradeoffs can disappear. A central requirement for later
reviews should be that Zax exposes where a cost or risk moved rather than
describing it as eliminated.

### Inferred design principles

These principles are inferred from the combination of features and rationale,
not merely copied from the current goals list.

#### 1. Make cost visible and selectable

The programmer should be able to reason about allocation, copying, indirection,
reference counting, synchronization, runtime type information, bounds checks,
and other meaningful costs.

"Pay for what you use" appears to mean more than zero-cost abstractions. It also
appears to mean that a programmer may select a more expensive mechanism when its
benefit is worth the cost, without imposing it universally.

#### 2. Span low-level control and high-level expression

Low-level memory access is not intended as an isolated unsafe escape hatch from
an otherwise high-level language. It is part of the normal design range.

At the same time, programmers should not need to remain at the lowest level to
obtain efficient output. Higher-level types, functions, ownership tools,
composition, and compile-time facilities should remain available.

#### 3. Prefer explicit policy over compulsory policy

The language repeatedly offers choices:

- raw, unique, shared, or otherwise qualified lifetime behavior;
- mutable or immutable data;
- shallow or deep movement across boundaries;
- selected runtime information;
- low-level or higher-level allocation; and
- safer conventions or deliberately unsafe operations.

The inferred principle is not "avoid opinions." Zax clearly has opinions about
visibility and explicitness. The principle is to avoid making one policy
mandatory when different domains have legitimate needs.

#### 4. Organize programs around data rather than class hierarchies

Zax appears to favor:

- explicit data shape and layout;
- composition instead of inheritance;
- functions and behavior associated with data without requiring object
  encapsulation;
- array and allocation forms useful for data-oriented processing; and
- visibility into how representations affect execution.

This does not reject constructors, member functions, polymorphism, or other
features often associated with object-oriented languages. It rejects mandatory
object identity and inheritance as the central modeling system.

#### 5. Make compile-time programming a first-class part of the language

Compile-time execution appears intended to absorb several responsibilities that
other ecosystems distribute across:

- textual preprocessors;
- template systems;
- reflection APIs;
- code generators;
- configuration languages;
- build scripts; and
- parts of package or module tooling.

The apparent goal is one language and type system across ordinary code,
metaprogramming, generation, checking, and build decisions.

#### 6. Favor composition and structural compatibility over rigid nominal trees

Structural typing, aliases, partial types, local and anonymous types, and
explicit composition suggest a preference for describing what data or behavior
is available rather than forcing every relationship into a declared hierarchy.

This is only a high-level principle. Exact type identity, equivalence,
subtyping, layout, and conversion rules remain unresolved and belong to later
work.

#### 7. Treat functions and flow as primary language-building tools

The emphasis on multiple results, function capture, function composition,
value-based selection, scoped flow control, `except`, lazy behavior, and
compile-time evaluation suggests that Zax wants programmers to express
transformations and control flow directly rather than hiding them behind object
protocols.

This may explain the stated "flow control focused" goal, but that phrase is too
ambiguous to serve as lasting documentation without refinement.

#### 8. Support safer programming without claiming universal enforced safety

Zax appears to want:

- strong and explicit types;
- ownership and lifetime mechanisms;
- controlled mutability;
- compiler diagnostics;
- explicit unsafe operations; and
- conventions that make valid code easier to reason about.

It does not appear to promise that all invalid memory use, data races, lifetime
errors, or unsafe representations will be rejected.

The current phrase "can be used in a type safe manner if desired" needs careful
revision. It may conflate type checking, memory safety, programmer discipline,
and opt-in guarantees.

#### 9. Prefer familiar, proven ideas but permit a new combination

The language borrows recognizable syntax and concepts from established
languages. Novelty appears justified when it creates a clearer combination or
solves an identified problem, not merely because Zax needs unique syntax.

This principle coexists with experimentation: a clean-slate language can revisit
old constraints even while preferring ideas with prior evidence.

#### 10. Preserve source as a durable asset

Long-term source compilation stability, source-based module import, and
source-preservation ideas suggest that Zax wants code to remain buildable and
understandable over long periods without depending on fragile binary packaging
or a particular external build stack.

The intended strength of this promise is unclear. It could mean stable syntax,
stable semantics, reproducible dependency capture, compiler support for old
source, or some combination.

### Candidate goals

The following goal structure may better express the apparent design than the
current flat list.

#### Programmer model

- Provide a coherent language from high-level expression to low-level control.
- Make meaningful runtime and compile-time costs understandable.
- Let programmers select safety, lifetime, allocation, mutability, concurrency,
  and abstraction policies appropriate to their domain.
- Keep data representation and flow understandable rather than hidden behind a
  mandatory object model or runtime.

#### Performance and systems capability

- Produce efficient native code suitable for performance-sensitive software.
- Support direct memory, layout, alignment, allocation, and ABI-relevant control.
- Avoid mandatory runtime services and overhead unrelated to the features used.
- Support data-oriented organization and hardware-conscious processing.

#### Expressiveness

- Reduce boilerplate without hiding consequential behavior.
- Treat functions, composition, multiple values, and flow control as expressive
  building blocks.
- Provide high-level lifetime and resource-management tools alongside raw
  mechanisms.
- Allow strong compile-time abstraction without requiring textual preprocessing.

#### Compile-time and build model

- Use the language itself for compile-time execution, reflection, generation,
  checking, and build decisions.
- Keep compiler and library responsibilities separable.
- Support source-based composition and preservation without requiring one
  package manager or system library.

#### Type and data model

- Provide strong type checking while retaining explicit low-level escape
  mechanisms.
- Use structural typing and explicit composition where they reduce rigid
  hierarchy and duplication.
- Support local, anonymous, partial, and metaprogrammed types when they improve
  expression and reuse.

#### Longevity and familiarity

- Favor understandable, proven language ideas where they fit.
- Maintain a consistent syntax and mental model across features.
- Pursue meaningful long-term source stability.
- Remain general-purpose rather than optimized around only one industry.

### Candidate advantages

These are potential advantages if the design becomes coherent and the tooling
delivers them. They are not current guarantees.

#### One language across an unusually wide abstraction range

A developer could write low-level memory and layout code, ordinary application
logic, high-level abstractions, compile-time generators, and build logic without
switching among several languages and incompatible type systems.

#### More deliberate performance decisions

Explicit ownership, allocation, copy, mutability, and concurrency choices could
make it easier to understand why code has a particular cost and to change that
cost intentionally.

#### High-level convenience without a mandatory managed runtime

Features commonly associated with higher-level languages could be available in
native, runtime-optional software.

#### Flexible data modeling

Structural typing and composition could support reuse without inheritance,
reduce nominal wrappers, and make it easier to adapt data organization to the
problem.

#### Integrated metaprogramming and build control

Using Zax itself for compile-time work could reduce the semantic gap between
program code, templates, generators, configuration, and build scripts.

#### Domain-appropriate safety and lifetime tools

A programmer could choose simple raw mechanisms, unique ownership, shared
lifetime, immutable data, deep transfer, or other policies according to actual
needs rather than one universal ownership strategy.

#### Better source longevity

If source stability and dependency preservation are made concrete, projects may
be less vulnerable to ecosystem churn, abandoned binary artifacts, or external
build-system changes.

#### Room for experimentation without inherited language baggage

A clean slate permits more coherent combinations than an established language
can introduce while preserving every historical rule.

### Candidate non-goals

These are phrased as boundaries rather than claims that the excluded approaches
are universally bad.

#### Universal enforcement of memory or concurrency safety

Zax does not appear intended to prove or enforce that every program is free from
invalid memory access, lifetime errors, data races, or other unsafe behavior.

#### Preventing all programmer mistakes

The language may diagnose many errors and offer safer tools, but it does not aim
to remove programmer responsibility or prohibit every operation that can be
misused.

#### A mandatory garbage collector or managed runtime

Programs should not be required to adopt global garbage collection, runtime
object metadata, or a particular runtime service model.

#### A single mandatory memory-management strategy

No one ownership or allocation model appears intended to fit every program.

#### Class inheritance as the primary reuse and polymorphism model

Zax does not aim to reproduce a traditional class hierarchy, virtual base-class
system, or object-oriented worldview as its central type model.

#### A purely functional, object-oriented, or other single-paradigm language

Functional and object-associated techniques may be available, but Zax does not
appear intended to force all programs into one paradigm.

#### Hidden hardware and representation

Zax does not aim to make memory layout, allocation, mutation, indirection, or
machine cost irrelevant to programmers.

#### Textual preprocessing

Compile-time language facilities appear intended to remove the need for a
separate textual preprocessor.

#### Exception-based error handling as the required model

The current material favors ordinary result values and explicit flow mechanisms
instead of stack-unwinding exceptions. The final error model remains a later
concept.

#### A mandatory system library, package manager, or external build language

The language appears intended to permit library and source-management choices
rather than binding all programs to one official system stack.

Whether Zax should provide optional standard tooling is separate from whether it
must be mandatory.

#### Novelty, popularity, or maximum theoretical power as ends in themselves

The FAQ suggests that useful experimentation, a pleasing development experience,
and sound tradeoffs matter even if Zax does not become the most popular or most
feature-powerful language.

#### Immediate specification or implementation completeness

The current project is refining the conceptual language. It is not currently
promising a finished specification, compiler, standard library, or production
ecosystem.

### Inspiration as currently understood

**Maturity: partly stated, partly inferred.**

The website explicitly names C++, C, Pascal, Java, JavaScript, Jai,
Objective-C, C#, F#, and Haskell as inspirations. It also credits the Dr. Seuss
story for the name. The repository does not yet map every design choice to a
specific influence.

A cautious initial interpretation is:

- **C and C++:** native compilation, direct memory and layout control, systems
  capability, operator overloading, deterministic resource-management ideas,
  and awareness of ABI and hardware.
- **Pascal:** name-before-type declaration syntax and an emphasis on readable
  declaration order.
- **Jai:** data-oriented systems programming, compile-time execution,
  reflection, language-integrated build control, and willingness to reconsider
  C++ assumptions; Zax appears to aim at broader general-purpose use.
- **Java, C#, and Objective-C:** high-level convenience in a strongly typed
  setting, behavior associated with types, reflection or runtime-type ideas, and
  richer function or reference facilities, without necessarily adopting their
  managed-runtime models.
- **JavaScript:** first-class function and closure flexibility may be an
  influence, though the repository evidence is not yet strong enough to be
  specific.
- **F# and Haskell:** function composition, immutable or functional techniques,
  expressive type-oriented programming, and treating functions as primary
  values without making Zax purely functional.

These mappings need confirmation from the language author. Inspiration should
explain design lineage where useful, not be used as authority for importing
another language's semantics.

### Advantages come with design costs

A balanced purpose document should not present selectable policy as free
flexibility.

Potential costs include:

- a large conceptual surface if many ownership, pointer, mutability, copy,
  concurrency, and compile-time mechanisms remain distinct;
- more responsibility and more possible unsafe combinations for programmers;
- inconsistent ecosystem conventions when many policies are selectable;
- substantial compiler, diagnostic, formatter, and language-server complexity;
- difficult interactions between structural typing, low-level layout, overload
  selection, and metaprogramming;
- compile-time performance, reproducibility, and security concerns from full
  compile-time execution;
- tension between long-term source stability and the freedom to improve a new
  language; and
- adoption costs if package, standard-library, and tooling expectations remain
  too unconstrained.

These are not arguments against the goals. They are pressures that later concept
reviews must resolve rather than hide.

### Documentation ownership implications

**Maturity: tentative proposal.**

The foundational material appears to need one substantive owner, provisionally
described as a **language vision and design principles** document. It would own:

- purpose and motivation;
- intended audience;
- design principles;
- goals and non-goals;
- the advantages sought and tradeoffs accepted; and
- the boundary between foundational commitments and feature candidates.

Under this tentative boundary:

- `README.md` would remain a concise repository router with a short project
  description and a link to the owner;
- `index.md` would become a human-developer website router with a concise
  introduction and learning paths;
- `faq.md` would answer genuinely recurring questions and link to the owner
  rather than independently defining the language philosophy;
- a feature overview, if retained, would distinguish accepted concepts from
  candidates and would not own the language's purpose; and
- detailed language pages would own their individual concepts after review.

This is not a promotion plan yet. The review may conclude that audience or
principles deserve separate owners. The pre-promotion documentation fit dry run
must test the boundary after the content is aligned.

### Important questions for language-maintainer correction

1. Is Zax best described as a systems language, a general-purpose native
   language, or another category?
2. Who is the primary audience, as distinct from everyone who could potentially
   learn or use it?
3. What does "compile-time language" mean in Zax, and is it the best phrase for
   first-time readers?
4. Does "small and performant code input" mean concise source, fast compilation,
   efficient authoring, or something else?
5. Which goals are foundational commitments, and which are current feature
   candidates?
6. Does "pay for what you use" include explicit opt-in to more expensive safety
   and convenience mechanisms, or is its meaning narrower?
7. What safety guarantees should Zax provide by default, which should be
   selectable, and which are explicitly outside its ambitions?
8. Is the large menu of lifetime and pointer policies itself a goal, or evidence
   of unresolved consolidation?
9. How strong is the intended source-stability promise?
10. Is source-based dependency import a principle, a current mechanism, or a
    rejection of package management generally?
11. How should Zax balance familiar proven ideas against clean-slate
    experimentation?
12. Which claimed inspirations were genuinely formative, and what did Zax learn
    or deliberately reject from each?
13. Should the public foundation explicitly acknowledge the complexity and
    responsibility created by programmer choice?
14. Is a single language-vision owner the right boundary, or should purpose,
    principles, and audience evolve independently?

### Aligned direction after language-maintainer review

**Maturity: aligned findings within work item `001`.**

The findings in this section reflect the reviewed direction for this work item.
They remain non-authoritative until promotion into lasting owner documents.

#### Central idea

Zax is a general-purpose programming language intended to provide the benefits
of a high-level language without hiding the lower-level behavior, costs, and
tradeoffs producing those benefits.

Its primary audience is a general-purpose programmer who wants high-level
capabilities while retaining the ability to understand what the language and
generated program are doing.

Zax should permit a developer to begin with safer features and established
patterns, then deliberately adopt lower-level or riskier mechanisms as their
understanding and requirements grow.

#### Build-time execution

Build-time execution literally means that the compiler evaluates and executes
Zax code during the build.

Its aligned purposes include:

- generating source or program structures without requiring a separately
  installed scripting language;
- avoiding the need to learn and maintain different languages for program and
  build logic;
- creating, transforming, validating, or embedding related resources and
  artifacts;
- integrating post-build behavior into the same build definition;
- precomputing CPU-intensive assets or values instead of deferring the work to
  runtime;
- sharing code paths, types, and invariants between build-time and runtime
  library behavior;
- performing partial evaluation and target-specific specialization;
- failing the build early when schemas, resources, configurations, or generated
  structures are invalid;
- supporting typed generation of bindings, serialization, lookup tables,
  protocols, and similar derived code; and
- reducing runtime dependencies, startup work, or repeated computation where
  build-time preparation is appropriate.

Fast builds are desirable but are not a primary language goal. Build-time
execution can add cost to compilation. The relevant comparison is often against
separate generators, scripts, asset processors, or post-build tools that would
otherwise run outside the language build.

The public phrase "compile-time language" may need replacement or immediate
explanation. "Language-integrated build-time execution" communicates more of
the intended benefit, while the final terminology remains open.

#### Build-time trust and sandboxing

Build-time Zax code is executable code. It is trusted unless the build
environment deliberately constrains it. An untrusted project must be built in
an appropriate sandbox.

Two complementary sandbox layers are recognized:

1. **Restricted build-time capabilities:** only approved libraries, compiler
   services, and host capabilities are available. Filesystem, network, process,
   environment, dynamic loading, and FFI access may be withheld or constrained.
2. **Externally isolated builds:** a disposable machine, VM, container, or
   hosted worker receives no trusted credentials, sensitive mounts, privileged
   sockets, or unnecessary network access.

This is not unique to Zax. CMake, Python, shell scripts, package build hooks,
compiler plugins, and other build systems can execute destructive code. Building
real projects is inherently a code-execution and supply-chain trust boundary.

The future build model must address:

- host versus target execution;
- deterministic and environment-dependent build behavior;
- capability declarations;
- filesystem, network, process, environment, and FFI access;
- diagnostics for executed build code;
- caching and incremental invalidation;
- cross-compilation;
- transitive build-code auditing; and
- the relationship between language restrictions and external isolation.

#### Runtime performance and "pay for what you use"

The earlier phrase "small and performant code input" does not primarily mean
fast compilation or terse source.

The goal is performant runtime output while allowing explicit tradeoffs among:

- executable size;
- memory use;
- runtime speed;
- compile-time cost;
- safety;
- convenience; and
- implementation complexity.

If a program does not use a particular language facility, it should not incur
that facility's runtime cost. When a feature has additional runtime or
compile-time cost, its use should make that tradeoff understandable enough for
the programmer to choose deliberately.

#### Foundational commitments

The following are aligned as foundational direction rather than commitments to
the exact mechanisms currently documented:

- general-purpose applicability;
- high-level capabilities with visible lower-level behavior;
- explicit, selectable, and understandable costs;
- no runtime cost for unused features;
- language-integrated build-time execution;
- reuse of code and types between build time and runtime;
- data-oriented organization and composition rather than class inheritance;
- multiple intentional lifetime-management strategies;
- an identifiable safe subset without making Rust-like enforcement the
  organizing constraint of the language;
- source dependency resolution integrated into the build;
- no required external package-management step;
- no mandated official package store;
- direct assembly integration;
- selected C and C++ interoperability;
- first-class asynchronous programming;
- familiar and readable syntax unless divergence earns its cost;
- elegance without cryptic compression; and
- tools that make durable, self-contained builds achievable.

The exact structural typing rules, lifetime qualifiers, unsafe markers,
dependency schema, reflection API, build directives, async primitives, error
model, and interop surface remain mechanism-level candidates for later work.

#### Safety direction

Maximizing compiler-enforced safety is not Zax's organizing goal. Zax should
nevertheless define a safe subset with explicit guarantees.

Code using only operations and interfaces classified as safe should receive the
guarantees assigned to that subset. Operations that leave those guarantees must
be recognizable and auditable without forcing the entire language into a
restrictive ownership model.

The final mechanism need not be an `unsafe { ... }` scope. A block can appear to
introduce runtime execution scope even when it is only marking a compile-time
trust boundary. Narrow operation markers, unsafe declarations or interfaces,
effect propagation, and reviewed safe wrappers around unsafe implementations
all require later evaluation.

The safe subset should also support progressive learning: a programmer can use
safer features and patterns before taking responsibility for raw memory,
unchecked lifetime manipulation, FFI, assembly, or other advanced mechanisms.

#### Rust as a comparison point

Rust is not an opponent and Zax is not making the same safety claim.

Safe Rust provides valuable memory-safety and data-race guarantees, subject to
its trusted compiler, standard library, and unsafe implementations. It does not
guarantee:

- freedom from deadlock or starvation;
- logic correctness;
- resource or stack exhaustion;
- panic freedom;
- correct concurrency architecture;
- correct cryptography or key handling;
- correct authorization or business rules; or
- safety of foreign or unsafe implementations that violate their contracts.

The following comparison pressures are retained as raw input for future work:

- borrow-checker restrictions can require ownership-oriented redesign;
- `Arc`, `Mutex`, `RefCell`, state machines, and indirection can increase
  cognitive and architectural complexity, although they may expose real costs
  that other languages hide;
- unsafe implementations narrow an audit boundary but remain part of real
  dependency chains;
- FFI requires manual preservation of layout, aliasing, ownership, and other
  invariants;
- memory-safety guarantees do not replace broader correctness and security
  analysis;
- C++ can mitigate many risks through disciplined RAII, ownership patterns,
  smart pointers, sanitizers, static analysis, review, and other tools without
  enforcing Rust's complete model;
- AI-assisted review can examine a broader class of memory, concurrency, logic,
  and security errors, but is probabilistic and does not replace deterministic
  guarantees; and
- CHERI-style capability hardware may strengthen pointer integrity but does not
  automatically solve temporal lifetime, concurrency, resource, or logic
  errors, and its future availability must not be assumed.

Future safety work must define Zax's guarantees first, then compare approaches
carefully rather than creating an anti-Rust argument.

#### Lifetime strategies

The plurality of lifetime and pointer policies is intentional.

Zax should support multiple lifetime strategies because they provide different
guarantees and carry different runtime, memory, synchronization, and
compile-time costs. Policies may support meaningful upgrades or downgrades when
their guarantee boundaries permit them.

A future owner will need to define:

- the final policy set;
- guarantees and failure modes;
- representation and runtime cost;
- allowed conversions;
- upgrade and downgrade behavior;
- thread and async implications; and
- a readable decision guide.

The complexity requires excellent documentation and tooling, but it is not by
itself evidence that the policies should be collapsed into one model.

#### Durable builds and source dependencies

The project does not promise that every Zax program will remain reproducible or
buildable forever.

The goal is to provide the integrated tools needed to avoid common sources of
build fragility:

- required auxiliary scripting languages;
- separate package-management steps;
- unnecessary binary artifact dependencies;
- disconnected build systems; and
- duplicated build-time and runtime implementations.

Projects can still reduce stability through system-specific build behavior,
mutable external resources, unavailable repositories, unpinned dependencies,
optional external tooling, or artifact stores.

Source dependencies are integrated into the build. Packages are source
repositories selected through tags, branches, versions, or another package
schema. Zax does not mandate an official package store or prevent optional
catalogs and package services from being built.

The actual non-goals are therefore:

- no required external package-management tool or step;
- no required centralized package registry;
- no required official package store; and
- no requirement that dependencies be consumed as prebuilt binary artifacts.

#### Immutable dependency identity and provenance

A branch or tag is a human-friendly selector, not an immutable identity.

A future dependency model should consider recording:

- repository identity;
- requested branch, tag, version, or package selector;
- resolved commit identity;
- a canonical source-tree or content digest;
- recursive dependency identities;
- applicable signatures or provenance; and
- build-time capabilities required by the dependency.

An existing locked build should continue using its recorded immutable content if
a tag is later moved. An explicit update can report that the selector resolves
to different content and require deliberate acceptance.

Development may permit intentionally floating branches, while durable or
release builds can require locked identities.

Threats retained as raw future input include:

- moved, deleted, or recreated tags;
- compromised repository accounts;
- repository deletion and namespace takeover;
- dependency confusion;
- transitive dependency mutation;
- Git submodules, LFS, generated content, or external downloads;
- malicious build-time code;
- cache poisoning;
- unsigned or unverifiable history;
- redirects and repository identity changes;
- hash algorithm migration or collision concerns; and
- source behavior that changes according to host state.

Recording both VCS identity and a canonical content digest may provide a
stronger durable identity than relying on a mutable name or one hosting system.
The exact locking, signing, provenance, cache, and update mechanisms remain
future work.

#### Familiarity and elegance

Familiarity is a default preference, not an absolute constraint.

Zax should prefer familiar forms when they express the design cleanly. It may
depart from convention when a concrete improvement justifies the learning and
tooling cost.

Such decisions remain case-by-case. Evaluation should consider:

- whether the unfamiliar design is easier to reason about after learning it;
- whether it removes a real ambiguity, limitation, or inherited accident;
- whether the benefit is visible at the use site;
- whether ordinary code remains readable without expert-level knowledge;
- whether the design composes coherently with the rest of Zax; and
- whether the teaching, diagnostic, formatting, and tooling costs are justified.

Elegance is paramount, but does not mean terseness alone. Zax needs the
technical range to express difficult systems while avoiding syntax or semantics
so cryptic that only a master can read ordinary code.

#### Programmer responsibility

Zax favors informed choice over mandatory policy.

This provides flexibility and cost control, but creates responsibility and a
larger conceptual surface. A programmer using raw memory, manual lifetime
assertions, assembly, FFI, unchecked operations, or advanced concurrency must
understand the relevant consequences.

The public foundation should state this tradeoff without presenting advanced
power as a requirement for ordinary use.

#### Assembly and foreign interoperability

Direct integration with assembly is a foundational requirement.

Binding to C and C++ is important, but interoperability is deliberately
selective in both directions:

- Zax-native features need not all have foreign-language equivalents.
- C++ features need not become Zax concepts merely to support direct binding.
- C-compatible representations, functions, symbols, and calling conventions
  are likely the most direct boundary.
- Selected C++ constructs may receive support when their ABI and invariants are
  sufficiently explicit.
- Adapter code is preferred when the semantic models differ.
- Manual ABI emulation may remain possible through raw pointers, assembly, or
  other explicit low-level mechanisms without receiving first-class language
  guarantees.

C++ virtual dispatch is a representative boundary. Zax does not have virtual
functions as a language concept and should not gain them merely to mirror C++.
A C++ adapter, explicit function table, or manually interpreted known ABI may
bridge a specific case. Direct vtable interpretation remains compiler-, ABI-,
layout-, and version-sensitive and belongs at an explicit low-level trust
boundary.

The general interop principle is:

> Zax supports selected foreign representations and calling conventions without
> importing the foreign language's complete semantic model.

#### First-class asynchronous programming

Asynchronous programming, coroutines, and concurrency are foundational
concerns. Computer I/O is inherently asynchronous even though many programs
present it through legacy blocking interfaces.

Zax should provide first-class async semantics without imposing their runtime
cost on programs that do not use them.

Using async may require:

- coroutine state machines;
- an executor or event loop;
- scheduling;
- allocation;
- synchronization;
- cancellation support;
- platform I/O integration; and
- runtime or library services.

Those costs should be explicit and selectable where possible. Async I/O,
coroutines, concurrency, scheduling, and parallelism are related but distinct
concepts whose exact model remains future work.

#### Vision-document ownership

One lasting language-vision document is the preferred initial ownership
boundary.

It should own:

1. what Zax is;
2. who it is for;
3. the problem it addresses;
4. design principles;
5. foundational goals;
6. intended advantages;
7. explicit non-goals;
8. accepted tradeoffs and programmer responsibility; and
9. the relationship to detailed language-concept documentation.

Detailed safety guarantees, build execution, dependencies, lifetime policies,
async semantics, and interop belong in later focused owners. Cross-document
implications should link to the defining owner rather than duplicate its
definition.

The repository README, website index, and FAQ should route to or briefly
summarize the vision owner rather than independently defining Zax's philosophy.

#### Public build-time terminology

The preferred public umbrella term is **language-integrated build-time
execution**.

Use the related terms as follows:

- **Build-time execution:** Zax code executed as part of the complete build,
  including generation, resource processing, validation, dependency decisions,
  and related build actions.
- **Compile-time evaluation:** the narrower case where the compiler evaluates
  code while compiling program structure or values.
- **Runtime execution:** code executed by the resulting program.

Do not describe Zax as a "build-time language" or rely on "compile-time language"
as its category. Zax is a general-purpose language with language-integrated
build-time execution.

Candidate public wording is:

> Zax supports language-integrated build-time execution. Zax code can run during
> the build to validate inputs, generate code and resources, precompute data,
> resolve build decisions, and perform related build steps. Libraries can share
> code and types between build-time and runtime use without requiring a separate
> scripting language. Build-time code is executable code and must be trusted or
> appropriately sandboxed.

#### Self-contained and durable builds

The preferred goal name is **self-contained and durable builds**, replacing the
overstated phrase "long-term source compilation stability."

Candidate public wording is:

> Zax aims to make builds self-contained and durable by allowing build logic,
> source dependency resolution, generation, resource processing, and related
> build actions to be expressed in Zax. A project that relies on versioned Zax
> facilities and pinned, declared inputs should require only a compatible Zax
> toolchain and those inputs to rebuild. It should not inherently require
> auxiliary scripting languages, a separate package manager, an official
> package store, or prebuilt artifacts.

The boundary is:

> Zax does not prohibit external tools, host-specific behavior, mutable
> services, or binary artifacts. Projects that choose those dependencies may
> weaken portability, reproducibility, or long-term buildability. Zax provides
> the means to avoid those dependencies; it does not guarantee that every
> project will do so.

These related properties remain distinct:

- **Self-contained:** build behavior can be expressed in Zax.
- **Durable:** declared, pinned inputs can be preserved and resolved.
- **Reproducible:** the same inputs produce the same result.
- **Hermetic:** undeclared environmental access is prohibited.

Zax directly targets the first two. Reproducibility and hermeticity may be
supported policies without becoming universal guarantees.

#### Public inspiration posture

The vision should make broad, defensible inspiration claims without attributing
individual features to languages when the historical lineage is uncertain.

Candidate vision wording is:

> Zax is informed by decades of programming-language design and practical
> development experience. Influential languages include C, C++, Pascal, Java,
> JavaScript, Jai, Objective-C, C#, F#, Haskell, and others. Zax adopts,
> combines, or reworks ideas according to its own goals rather than attempting
> to reproduce any one predecessor.

The earlier inferred feature-to-language associations remain useful raw notes,
not historical fact. Inspiration, semantic similarity, and foreign
interoperability are separate concerns.

A future similarity guide should help readers navigate in both directions:

- from a familiar language to related, different, or absent Zax concepts; and
- from a Zax concept to comparable constructs in other languages.

It must distinguish syntax, conceptual similarity, semantic differences, cost
and safety differences, absent equivalents, migration traps, and actual
interoperability. It must not define Zax semantics or claim exact historical
provenance.

The raw requirements for that future guide are retained in
[the similarity input](../../raw/similarity.md).

#### Documentation structure remains a dry-run output

The project is ready to identify ownership requirements but not yet ready to
commit to its long-term directory tree.

`project/raw/` is being used only as an indexed temporary holding area for the
known future similarity-guide input. This does not establish maturity-based
organization as the long-term structure. Safety and dependency-provenance input
will remain in active `001` until the dry run evaluates all retained future
inputs together.

Before `001` is archived, every retained future input must move to an
intentional, discoverable location. The pre-promotion documentation fit dry run
must produce a proposed owner map and minimal structure able to hold:

- the language vision;
- current human-facing concept owners;
- raw future safety input;
- raw future build and dependency input;
- future interop and async work;
- project workflow guidance; and
- historical work records.

The proposal must prevent ordinary readers from encountering raw material as
current guidance while preventing future inputs from becoming unindexed or
lost. It may recommend retaining much of the current layout if that remains the
smallest coherent option.

### Explicit deferrals after review

The following concerns are material but do not need resolution to complete the
foundational vision:

1. **Safety guarantees and unsafe boundaries:** reopen before defining public
   safety guarantees or unsafe syntax.
2. **Lifetime policies:** reopen before promoting pointer and lifetime
   documentation.
3. **Dependency security and execution policy:** reopen before defining the
   lasting build/dependency owner or an implementation contract.
4. **C/C++ interoperability:** reopen before defining FFI, ABI, or binding
   behavior.
5. **Async execution:** reopen before promoting async language documentation or
   designing its lowering and runtime boundary.
6. **Language similarity guide:** reopen after enough Zax concepts are accepted
   to support accurate comparisons.

The aligned build-time terminology, durable-build wording, and broad inspiration
posture no longer block this work item.

### Pending closure work

The remaining `001` task is the pre-promotion documentation fit dry run. It must
produce:

- a proposed owner map;
- a minimal directory and index structure;
- website and repository reading paths;
- locations for retained raw safety, dependency, and similarity input;
- dispositions for current root pages and the existing RFC area;
- migration and URL consequences;
- the exact proposed promotion change set; and
- one recommended structure with rejected alternatives.

The dry-run structure remains a proposal until separately discussed and aligned.

## Dispositions and promotion dry run

### Dry-run outcome

The pre-promotion documentation fit dry run concluded that the aligned
foundational findings fit one language-vision owner, but the existing repository
README, website index, and FAQ could not absorb them without duplicate
authority.

The aligned minimal structure was:

```text
README.md
index.md
language/
  vision.md
project/
  README.md
  documentation.md
  handoff.md
  rehydrate.md
  work/
  raw/
  archive/
```

Existing root language-topic pages remain in place as legacy design input until
individually reviewed.

### Promoted owner

The following findings were promoted to `language/vision.md`:

- Zax as a general-purpose language;
- intended human-developer audience;
- motivating tradeoffs;
- high-level capability without hidden lower-level behavior;
- visible and selectable cost;
- data-oriented organization and composition;
- language-integrated build-time execution;
- bounded safe-subset direction and programmer responsibility;
- intentional lifetime-policy plurality;
- familiarity, readability, and elegance;
- first-class async direction;
- direct assembly and selected foreign interoperability;
- self-contained and durable builds;
- foundational goals;
- intended advantages;
- explicit non-goals;
- accepted high-level costs; and
- broad, non-attributed inspiration.

Detailed syntax, guarantees, conversion graphs, ABI behavior, runtime design,
and dependency policy were not promoted.

### Router dispositions

- `README.md` became the repository router.
- `index.md` became the website router and grouped legacy-note index.
- `faq.md` retained question-specific explanations and routes to the vision
  rather than independently defining the language foundation.
- Existing root topic pages remained unchanged and retained their public paths.

### Raw future-work dispositions

The following indexed raw inputs preserve evidence-backed future pressure:

- `project/raw/safety.md`;
- `project/raw/build-and-dependencies.md`;
- `project/raw/lifetimes.md`;
- `project/raw/interop.md`;
- `project/raw/async.md`;
- `project/raw/similarity.md`;
- `project/raw/feature-catalog.md`; and
- `project/raw/structural-typing.md`.

The former structural-typing RFC was relocated as explicitly agent-authored,
non-authoritative input. The inactive RFC process and old paths were retired.

### Remaining closure boundary

Promotion does not authorize archival or the next work item.

Promotion validation passed:

- the vision contains every required ownership section;
- all 34 legacy topic pages remain routed exactly once from the website index;
- all eight raw inputs are indexed;
- current documentation links resolve;
- the inactive RFC files and routes are retired;
- Markdown formatting checks pass; and
- the staged comparison baseline remains unchanged.

The currently published `zax.io` website is rendered from an upstream repository
and is intentionally outside this promotion. Upstream synchronization and pull
requests are deferred until this repository is brought into shape.

Before `001` closes:

1. Confirm that every aligned and deferred finding has a disposition.
2. Discuss and align the initiating input and subject-specific filename for the
   next work item.
3. Wait for explicit authorization to archive `001`, create the next item, and
   update the project current-work index.
4. Stop before analyzing the next work item unless the language maintainer
   explicitly reassigns the session.
