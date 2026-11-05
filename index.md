# Zax programming language

Zax is a general-purpose programming language intended to provide high-level
capabilities without hiding the lower-level behavior, costs, and tradeoffs
producing them.

> **Project status:** Zax is in conceptual design. The current conceptual-design
> pages below own the accepted concerns assigned to them. The root topic pages
> remain legacy design notes being reviewed; none of this material is a formal
> specification or implementation promise.

## Start here

- Read the [Zax language vision](language/vision.md) for its purpose, audience,
  foundational direction, goals, advantages, non-goals, and accepted tradeoffs.
- Read [Zax language principles](language/principles.md) for the reusable
  principles that guide source, APIs, and future language decisions.
- Read [Zax source structure](language/source-structure.md) for statement-level
  newlines, continuation, bodies, semicolon composition, mandatory layout, and
  comments.
- Read [Zax declarations and bindings](language/declarations-and-bindings.md) for
  declaration, initialization, binding visibility, qualifiers, and assignment
  boundaries.
- Read [Zax type definitions](language/type-definitions.md) for named and
  anonymous identity, stored members, type-body organization, self-reference,
  completion, lifecycle handoffs, and `outer tracked` integration.
- Read [Zax unions](language/unions.md) for untagged offset-zero lenses,
  passive/all-bit-safe overlays, jagged storage, and explicit unsafe lens
  responsibility.
- Read [Zax variants](language/variants.md) for absent or named-present managed
  payloads, contained reconstruction with `.=` and reset, checked access,
  transfer, and alternative selection.
- Read [Zax optional values](language/optional-values.md) for absent or present
  single-payload wrappers, proven postfix access, contained reconstruction,
  reset, nested layers, and wrapper-versus-payload qualification.
- Read [Zax namespaces and modules](language/namespaces-and-modules.md) for
  module-local roots, namespace declaration and lookup, generative imports,
  injection, collisions, and the visibility/export baseline.
- Read [Zax composition](language/composition.md) for named containment, data
  publication through `own`, expected-type projection through `preferred`,
  behavior exposure, abstract fulfillment, and outer casting.
- Read [Zax partials](language/partials.md) for adding operators, functions,
  and state to types you did not write, visibility grants, `seal` categories,
  added storage and its lifecycle, and hook points.
- Read [Zax identity types](language/identity-types.md) for transparent aliases,
  distinct identities, admission, projection, exposed and opaque surfaces, and
  declared identity bridges.
- Read [Zax structural shapes and compatibility](language/structural-shapes-and-compatibility.md)
  for identity-versus-shape, compatibility postures, anchored recasting,
  same-storage views, decomposition, recomposition, and transformation.
- Read [Zax conversions and casts](language/casting.md) to choose among
  conversion, stance restatement, compatibility, container navigation, and
  unchecked reinterpretation, and for programmer-declared `as`, `unsafe cast`,
  and pointer/integer conversion.
- Read [Zax qualifiers](language/qualifiers.md) for place replacement, value
  mutability, access capabilities, defaults, indirection, and unsafe escape
  behavior.
- Read [Zax transfer stances](language/transfer-stances.md) for why `copy`,
  `deep`, `move`, and `last` exist; how source and receiver intent select
  consumers; and how source state changes.
- Read [Zax lifetimes and references](language/lifetimes-and-references.md) for
  life paths, instance places, fixed reference binding, replacement, borrowing,
  escape, and capture.
- Read [Zax Nothing instances](language/nothing-instances.md) for pointer
  vacancy, unavailable function values, receiverless type calls,
  compiler-provided/trapping/custom Nothing policies, and unchecked access
  boundaries.
- Read [Zax pointers, allocation, and arenas](language/pointers-and-arenas.md) for
  declaration-bound allocation, raw and managed ownership, allocation policy,
  control blocks, reset, collection, and arena behavior.
- Read [Zax execution context](language/execution-context.md) for the per-thread
  `___` context, its application-wide shape, replacement, and context-provided
  default arenas.
- Read [Zax safety and analysis](language/safety-and-analysis.md) for safe
  guarantees, compiler proof, narrow unsafe responsibility, contract evolution,
  and lint boundaries.
- Read [Zax function invocation](language/function-invocation.md) for ordinary
  calls, arguments, defaults, results, result routing, and callable selection.
- Read [Zax exceptional result flow](language/except.md) for success versus
  named exceptional outcomes, local handling, explicit forwarding, cleanup,
  and conditional result elision.
- Read [Zax lambdas and callable composition](language/lambdas-and-callable-composition.md)
  for lambda expressions, captures, callable storage and receiver lifetime,
  partial application, composition, reshape mapping, chaining, reset, and weak
  callbacks.
- Read [Zax intent acknowledgements](language/intent-acknowledgements.md) for
  defined but suspicious-looking source that requires explicit confirmation.
- Read [Zax construction, replacement, and destruction](language/construction-and-destruction.md)
  for constructors, construction packets, generated lifecycle operations,
  same-storage replacement, and destruction.
- Read [Zax arrays and slices](language/arrays-and-slices.md) for fixed and
  resizable arrays, array expressions, usable capacity, storage strategies,
  indexing, slicing, element places, joining, and traversal.
- Read [Zax `using`](language/using.md) for
  parenthesized `using` resource lists, structural `dispose`, disposal and
  destruction order, and explicit disposal bypass.
- Read [Zax core flow control](language/core-flow-control.md) for exact-`Boolean`
  conditions, `if`/`else`, loops, explicit `scope`, flow labels, and
  `break`/`continue`/`next`/`goto`/`return`.
- Read [Zax switch, case, and default](language/switch.md) for
  ordered `switch`/`case` testing, case operators, clause posts, direct body
  entry, `default`, exhaustiveness, and optional or enum selection.
- Read [Zax iteration](language/iteration.md) for `each in`, cursor-driven
  `each from`, entry bindings, progression, active-traversal access, erasure,
  mutation responsibilities, and costs.
- Read [Zax operators](language/operators.md) for the shared operator mental
  model, declarations, receiver discovery, selection, expected results,
  evaluation, protected domains, and fallback.
- Read [Zax operator phrases](language/operator-phrases.md) for word-spelled
  operations, receiver-owned declarations, candidate interpretations, grouping,
  fencing, physical presentation, and phrase-specific diagnostics.
- Use the [Zax operator catalog](language/operator-catalog.md) to look up exact
  symbolic/circumfix forms, precedence, built-in arithmetic, logical, bitwise,
  shift, mutation, result, and availability behavior.
- Read [Zax integers](language/integers.md) for exact and target-selected integer
  types, representation, conversion, arithmetic policy, counts, sizes, and
  portability.
- Read [Zax fixed-point scalars](language/fixed-point-scalars.md) for bounded
  coefficient scales, `I<W>F<F>` and Q names, arithmetic, full-precision
  products, conversion, representation, and costs.
- Read [Zax binary floating-point scalars](language/floating-point-scalars.md)
  for exact and profile-selected formats, rounding, special values, support,
  conversion, representation, Extended80, and MBF40.
- Read [Zax integer literals and realization](language/integer-literals.md) for
  how number literals get a type, how pre-unary `+` states signedness intent,
  how typed operands affect them, and when a value does not fit.
- Read [Zax literal source and literal operators](language/literal-source-and-operators.md)
  for ordinary real and string source, attached literal phrases, payload
  merging, compile-time joining, radix catalogs, and custom literal
  declarations.
- Read [Zax strings and characters](language/strings-and-characters.md) for byte,
  ASCII, Unicode, and legacy character/string identities, literal forms,
  encoding transformations, and compile-time joins.
- Use the [Zax integer operator catalog](language/integer-operator-catalog.md)
  to look up complete protected integer availability, results, policies,
  failures, counts, shifts, conversion, `delta`, and `distance`.
- Read [Zax mixfix operators](language/mixfix-operators.md) for direct
  multi-component tree operations, holes, receiver anchors, matching,
  decomposition, and protected barriers.
- Read [Zax endianness](language/endianness.md) for intrinsic little/big scalar
  specialization, absolute and environment-selected names, numeric conversion,
  byte-preserving coercion, and native versus compiler-host and target byte
  order.
- Read [Zax compile-time code](language/compile-time-code.md) for computing
  values and choosing code while compiling, execution policies, values crossing
  into the program, `wellformed{...}` checks, and compilation information.
- Read [Zax generics](language/generics.md) for generic functions, operators,
  and types, compile-time parameters, how arguments are worked out,
  constraints and `requires`, specializations, extension across modules, and
  instantiation identity.
- Read [Zax compiler directives](language/compiler-directives.md) for what a
  `[<...>]` directive is, where it attaches, directive ranges, deprecation,
  source mapping, and the directive catalog.
- Use [Zax language-design terms](language/terms.md) for cross-cutting
  descriptive vocabulary used by current conceptual design.
- Use the grouped design notes below when exploring a specific proposed concept.

## Current conceptual design

- [Language vision](language/vision.md)
- [Language principles](language/principles.md)
- [Source structure](language/source-structure.md)
- [Declarations and bindings](language/declarations-and-bindings.md)
- [Type definitions](language/type-definitions.md)
- [Unions](language/unions.md)
- [Variants](language/variants.md)
- [Namespaces and modules](language/namespaces-and-modules.md)
- [Composition](language/composition.md)
- [Partials](language/partials.md)
- [Identity types](language/identity-types.md)
- [Structural shapes and compatibility](language/structural-shapes-and-compatibility.md)
- [Conversions and casts](language/casting.md)
- [Enums](language/enums.md)
- [Qualifiers](language/qualifiers.md)
- [Transfer stances](language/transfer-stances.md)
- [Lifetimes and references](language/lifetimes-and-references.md)
- [Nothing instances](language/nothing-instances.md)
- [Pointers, allocation, and arenas](language/pointers-and-arenas.md)
- [Execution context](language/execution-context.md)
- [Safety and analysis](language/safety-and-analysis.md)
- [Function invocation](language/function-invocation.md)
- [Exceptional result flow](language/except.md)
- [Lambdas and callable composition](language/lambdas-and-callable-composition.md)
- [Intent acknowledgements](language/intent-acknowledgements.md)
- [Optional values](language/optional-values.md)
- [Construction, replacement, and destruction](language/construction-and-destruction.md)
- [Arrays and slices](language/arrays-and-slices.md)
- [`using`](language/using.md)
- [Core flow control](language/core-flow-control.md)
- [Switch, case, and default](language/switch.md)
- [Iteration](language/iteration.md)
- [Operators](language/operators.md)
- [Operator phrases](language/operator-phrases.md)
- [Operator catalog](language/operator-catalog.md)
- [Integers](language/integers.md)
- [Fixed-point scalars](language/fixed-point-scalars.md)
- [Binary floating-point scalars](language/floating-point-scalars.md)
- [Integer literals and realization](language/integer-literals.md)
- [Literal source and literal operators](language/literal-source-and-operators.md)
- [Strings and characters](language/strings-and-characters.md)
- [Integer operator catalog](language/integer-operator-catalog.md)
- [Mixfix operators](language/mixfix-operators.md)
- [Endianness](language/endianness.md)
- [Compile-time code](language/compile-time-code.md)
- [Generics](language/generics.md)
- [Compiler directives](language/compiler-directives.md)
- [Language-design terms](language/terms.md)

## Legacy language-design notes

These pages preserve substantial prior design work. Their detailed syntax and
semantics remain non-authoritative until individually reviewed and promoted.

### Foundations and flow

- [Basics](basics.md) — legacy foundations; current scalar and literal design is
  owned by [Zax integers](language/integers.md),
  [literal source and operators](language/literal-source-and-operators.md), and
  [strings and characters](language/strings-and-characters.md)
- [Variadic functions](variadic.md)
- [Flow control](flow-control.md) — legacy value-polymorphism input;
  current ordinary flow and runtime selection are owned by
  [core flow control](language/core-flow-control.md) and
  [switch, case, and default](language/switch.md)

### Types and data modeling

- [Optional values](language/optional-values.md) — current optional wrapper,
  construction, transfer, qualification, and proven-access design
- [Partials](language/partials.md) — current partial, visibility-grant, sealing,
  added-storage, and hook design

### Memory and lifetime

- [Lifetimes and references](language/lifetimes-and-references.md) — current
  life-path and non-owning reference design
- [Pointers, allocation, and arenas](language/pointers-and-arenas.md) — current
  dynamic allocation, ownership, disposition, and arena design

### Build-time programming and modules

- [Compiler directives](compiler-directives.md) — deferred legacy input for
  source generation, source inclusion, async, ABI, panic-function, and
  `once`-value directives; current directives are owned by
  [compiler directives](language/compiler-directives.md),
  [compile-time code](language/compile-time-code.md), and
  [generics](language/generics.md)
- [Namespaces and modules](language/namespaces-and-modules.md) — current
  namespace, import, injection, visibility-baseline, alias, and forwarding
  integration

### Execution and concurrency

- [Lazy functions](lazy.md)
- [Concurrency](concurrency.md)
- [Compiler warnings and errors](warnings-errors.md)

## Questions

- [Frequently asked questions](faq.md)
