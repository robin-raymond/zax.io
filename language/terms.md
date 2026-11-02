# Zax language-design terms

| Field | Value |
| --- | --- |
| Status | Current conceptual terminology |
| Audience | Human developers reading, writing, discussing, or documenting Zax |
| Applies To | Cross-cutting human-language terms used by current conceptual design |
| Implementation State | Not applicable |
| Owns | Concise meanings and usage boundaries for cross-cutting descriptive terms |
| Does Not Own | Keyword catalogs or the complete behavior owned by the concept documents linked here |

## Purpose

This document defines the human-language terms used to discuss Zax consistently.
It is not a list of reserved words or operators. A term may describe a concept
without being available as source syntax.

Behavior remains with its concept owner. This page gives readers enough meaning
to follow documentation and links to the relevant owner when the distinction
affects programming behavior.

## Active execution environment

The **active execution environment** is the environment whose execution
semantics currently govern an operation. It may be the generated target or the
compiler host during applicable build-time execution. An unqualified
environment-relative scalar name, including its concrete endianness, selects
against this environment. See [Zax endianness](endianness.md) and
[Zax integers](integers.md#native-representation-and-software-emulation).

The scalar-family root is the canonical current-environment namespace:

```zax
Scalars.Integers.I32
Scalars.Fixed.I16F8
Scalars.Floating.Binary32
```

## All-bit-pattern-valid

A type is **all-bit-pattern-valid** when every possible pattern in its complete
storage envelope represents an ordinary valid value. Padding and non-value bits
are ignored by that type and may have any contents.

Plain union lenses must be both union-admissible and all-bit-pattern-valid. See
[Zax unions](unions.md#every-bit-pattern-is-valid-in-a-plain-union).

## Access path

An **access path** is one particular route through which code observes or acts
on a value. A name, reference, pointer dereference, member projection, parameter,
or capture may provide an access path.

Different paths to the same value may have different capabilities. See
[Zax qualifiers](qualifiers.md).

## Allocation disposition

An **allocation disposition** is the allocation-time choice that determines when
the resident instance is destroyed and when its backing block becomes reusable.

It is independent from pointer ownership. See
[Zax pointers, allocation, and arenas](pointers-and-arenas.md#allocation-disposition).

## Allocation policy

An **allocation policy** combines an allocation disposition with independent
choices such as collection eligibility, object and control-block arenas, and
control-block placement. Failure and open-ended raw behavior are selected by the
exact `@` allocation form rather than by policy values.

See [Zax pointers, allocation, and arenas](pointers-and-arenas.md#allocation-forms).

## Allocation record

An **allocation record** stores or reaches the allocation root, object arena,
destructor, size, alignment, disposition, and any additional metadata needed for
correct release.

Every dynamic allocation has recoverable allocation-record information. An
allocation record is distinct from the ownership control block used for
shareable and shared ownership. See
[Zax pointers, allocation, and arenas](pointers-and-arenas.md#allocation-roots-and-records).

## Allocation root

An **allocation root** is the top-level life path and typed instance place
created directly by one dynamic allocation operation. Its allocation record
governs the complete allocated storage and final disposition. An interior member
or array element is not an independent allocation root merely because a pointer
can target it.

See
[Zax pointers, allocation, and arenas](pointers-and-arenas.md#allocation-roots-and-records).

## Array expression

An **array expression** is `[ ... ]` source that constructs one owning intrinsic
array. Every ordinary entry—including an array or slice—contributes one element.
Contextual array-entry `from` contributes a sequence.

Nested array source uses `[ [` while contiguous `[[` opens lambda capture. See
[Zax arrays and slices](arrays-and-slices.md#array-expressions).

## Fixed array and resizable array

A **fixed array** owns an exact type-level number of element places and cannot
insert, remove, or resize. Ordinary element access does not relocate them;
complete array replacement or an explicit future storage transition may end
them.

A **resizable array** owns a current logical length within a type-level admitted
length range and may add, end, renew, or relocate element places through its
declared operations.

Both are intrinsic owning array roles. See
[Zax arrays and slices](arrays-and-slices.md#fixed-arrays-resizable-arrays-and-slices).

## Logical length, capacity, and suggested capacity

An array's **logical length** counts live elements. Its **capacity** counts
usable element slots in the current backing while respecting the array's
declared maximum. Its optional
**suggested capacity** records programmer guidance without guaranteeing
reservation.

Provider backing may physically exceed usable array capacity. Capacity is never
an uninitialized-element count. See
[Zax arrays and slices](arrays-and-slices.md#length-capacity-and-suggestion-are-different).

## Array storage provider and profile

An array **storage provider** owns raw bytes, physical slot/chunk mapping, gross
alignment, and storage stability. One provider may serve several arrays; each
array owns one unique handle for its backing region and independently manages
its element lifetimes.

The **resolved storage profile** records the provider type/instance and
capabilities that affect one owning array's representation and operations.
Storage does not change logical array identity. See
[Zax arrays and slices](arrays-and-slices.md#storage-strategies).

## Storage-erased array reference

A **storage-erased array reference** borrows an array without requiring its
inline or provider-backed storage profile. It carries the common operations
needed to locate elements through that source storage, so one concrete function
can accept compatible arrays backed in different ways.

The erased reference guarantees ordinary array access, not one contiguous
layout or provider-specific APIs. See
[Zax arrays and slices](arrays-and-slices.md#passing-an-array-by-reference).

## Slice and splice

A **slice** is a non-owning descriptor over selected array element places. It
owns no element and cannot widen beyond its current view.

A **splice** is bracket source containing inclusive `..` or half-open `..<`
range components. It may produce a slice or another custom declared result.
Direct splice assignment is one mixfix mutation rather than assignment through
a hidden slice proxy. See [Zax arrays and slices](arrays-and-slices.md#slicing).

## Binding

A **binding** associates a source-level name with a declaration. The binding
becomes visible at the declaration boundary even while its initialization is
still being resolved.

A binding is not the same as the value it names or the storage place holding
that value. See [Zax declarations and bindings](declarations-and-bindings.md).

## Call completion

**Call completion** is the synchronous boundary after the selected callable has
bound all parameters, executed its body, completed one declared outcome, and
mapped that outcome into the surrounding context.

An argument or result temporary required by the call remains live through the
part of this boundary that uses it. Async suspension requires a broader future
completion model. See [Zax function invocation](function-invocation.md).

## Completion outcome

A callable **completion outcome** is either its complete ordinary success shape
or one named exceptional result. One invocation selects exactly one.

Success publishes every ordinary result. An exceptional outcome publishes only
its declared `except` payload. See
[Zax exceptional result flow](except.md#callable-completion-outcomes).

## Callable

A **callable** is a function value, declared function, constructor, operator, or
other language-defined entity that has a prototype and may be selected and
invoked.

The callable category determines discovery and any category-specific behavior.
Ordinary argument binding, results, and fixed-arity selection are defined by
[Zax function invocation](function-invocation.md).

## Bound and unbound callable

A **bound callable** has a receiver available to its implementation through `_`.
An **unbound callable** has no receiver.

A fixed bound member receives the instance used for its call. Receiver-capable
varying storage may instead install an unbound implementation, a borrowed
receiver, or an applicable unique/strong/weak receiver relationship. The
installed receiver type belongs to its minted implementation and may be erased
from the visible result/input prototype.

See
[Zax declarations and bindings](declarations-and-bindings.md#bound-and-unbound-function-storage)
and
[Zax lambdas and callable composition](lambdas-and-callable-composition.md#callable-prototypes-and-storage).

## Lambda and capture receiver

A **lambda** is a callable expression combining a delayed body with an explicit
capture list.

A capturing lambda constructs an anonymous **capture receiver** containing its
captured declarations. Ordinary capture copies; explicit reference capture
borrows one fixed place. See
[Zax lambdas and callable composition](lambdas-and-callable-composition.md).

## Callable binding kind

A callable's **binding kind** is the mode currently installed in
receiver-capable storage: unavailable, unbound, borrowed bound, unique, strong,
strong atomic, weak, or weak atomic. It is distinct from the static maximum
capacity of the slot.

## Liveness probe

A **liveness probe** is a nonacquiring momentary query asking whether strong
ownership is open for a weak relationship. Its Boolean result may become stale
immediately and grants no lifetime proof. Presence `?` separately asks whether
the immediate pointer, owner, or callable relationship is stored.

## Compatibility anchor

A **compatibility anchor** is a source-relative resident-storage path that
selects where one contiguous compatible region begins. The expected destination
supplies the region's required extent and resulting type.

It is distinct from a mixfix receiver anchor and a numeric type anchor. See
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md#select-one-contiguous-region-with-anchor).

## Compatibility posture

A **compatibility posture** is declaration metadata stating which safe
binary-compatible structural relationship a source may offer when another
concrete type is already expected. Omission resolves to
`compatible strict`.

Compatibility posture is not type identity and is distinct from transfer
stance. See
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md#compatibility-posture).

## Coercive view

A **coercive view** gives one stored region another reviewed typed lens without
translating its bytes. It is available only when the destination interpretation
provides useful structure beyond arbitrary raw-byte access.

Changing numeric value is not inherently unsafe. Safe coercion proves
destination validity and, for writable access, preservation of source validity.
`unsafe as coercive` instead accepts an unproved validity precondition or a
restoration obligation after a write. Both forms are local reference views,
preserve available qualification authority, and remain distinct from unchecked
[`unsafe cast`](casting.md#unchecked-reinterpretation-with-unsafe-cast). See
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md#coercive-reference-views).

## Compiler-directive enclosure

A **compiler-directive enclosure** uses contiguous `[<` and `>]` delimiters to
attach recognized compiler metadata:

```zax
if condition [<likely>] {
  handleExpectedCase()
}
```

The enclosure is not an array expression, lambda capture, or grouping.
Contiguous `[[ ... ]]` belongs to lambda capture. See
[Zax source structure](source-structure.md#compiler-directive-enclosure). What a
directive is, where it attaches, and which directives exist are described by
[Zax compiler directives](compiler-directives.md).

## Commitment boundary

A **commitment boundary** selects one concrete scalar type for uncommitted
numeric source. Integer realization then checks sign intent and range; real
realization applies the selected fixed-point or floating format.

Commitment may come from an inferred default, an explicitly typed declaration,
a selected callable/operator input, a protected integer operand anchor, or a
conditional convergence result, or generic literal specialization. See
[Zax integer literals and realization](integer-literals.md) and
[literal source and operators](literal-source-and-operators.md#every-invocation-has-one-concrete-result).

## Completion mode and contextual completion

A declaration's **completion mode** says whether a supported source context may
provide one missing typed value.

- `contextual` permits one bounded construction in a context defined to support
  it.
- `explicit` requires source to provide the concrete receiver or input before
  the compiler may select a constructor or operator candidate that needs it; it
  is the omission default.

**Contextual completion** is the act of applying that permission. It does not
authorize general implicit conversion, construction chains, or fallback after
an ordinary ambiguity or selected failure. See
[Zax operators](operators.md#contextual-completion).

## Control block

A **control block** stores or reaches the state required for shared pointer
ownership, weak observation, ownership closure, and the allocation's disposition
and arena relationships.

It may be coallocated with the instance or stored in a separate control-block
arena. It is distinct from the allocation record available to raw and blockless
`unique` allocations. See
[Zax pointers and arenas](pointers-and-arenas.md#object-and-control-block-arenas).

## Compiler host

The **compiler host** is the environment running the compiler and applicable
build-time code. It is distinct from the generated target even when both happen
to use the same CPU model or representation. See
[Zax integers](integers.md#native-representation-and-software-emulation).

## Compile point

A **compile point** is a `compile` expression or `compile{...}` block. It runs
once, when the compiler compiles the place where it is written, whether or not
the running program ever reaches that place. Only compile points in discarded
compile-time branches or inside a `wellformed{...}` check never run:

```zax
myTableSize := compile cubed(4)   // runs while compiling; the program holds 64
```

See [Zax compile-time code](compile-time-code.md#when-compile-points-run).

## Compile-time value

A **compile-time value** is a value known while compiling: a literal, an
expression built only from compile-time values, a compile point's result, the
result of a `compile` or constant-argument `folding` call, a compile-time query
such as `size of`, or a `final` binding initialized from one. `is
constant` answers by this rule, never by what the optimizer did. See
[Zax compile-time code](compile-time-code.md#compile-time-values).

## Circumfix operator

A **circumfix operator** is one complete operation with a language-recognized
opening component, closing component, and enclosed operand:

```zax
|value|
```

The opening and closing symbols are not independent pre/post unary operations.
Recognized forms and built-in availability are defined by the
[operator catalog](operator-catalog.md#circumfix-forms).

## Construction packet

A **construction packet** is the `[{ ... }]` construct that supplies positional
constructor arguments, named constructor arguments, and direct stored-member
initializers.

Packet order controls input evaluation and binding. It does not reorder member
construction. See
[Zax construction, replacement, and destruction](construction-and-destruction.md#construction-packets).

`[{}]` is the canonical zero-entry construction packet. A packet supplies
construction inputs at a destination; it is not independently a value or an
anonymous structure.

## Contained reconstruction

**Contained reconstruction** keeps one semantic wrapper lifetime while ending
and constructing fresh contained state. Protected `.=` re-delivers direct or
packet construction inputs to an optional payload or variant payload/selection:

```zax
optional .= value
variant.text .= value
variant .= [{ .text = value }]
```

It is distinct from complete-value `.=` replacement and ordinary assignment
through an already live payload. See
[construction, replacement, and destruction](construction-and-destruction.md#wrapper-owned-contained-reconstruction-with-dot-equals).

## Contextual keyword

A **contextual keyword** is a spelling that has keyword status only where that
keyword's construct is grammatically permitted. Outside those positions, the same
spelling is not a keyword and may name an ordinary entity.

The term describes recognition position, not a reserved-word list. See
[Zax source structure](source-structure.md#contextual-keyword-recognition).

## Keyword-role conflict

A **keyword-role conflict** occurs when a word is grammatically eligible as a
non-operator keyword while the programmer intends it as an identifier or
operator-phrase component.

This is a lexical-role question rather than unsafe behavior or operator
overload ambiguity. Words inside a declared or fenced operator phrase carry
phrase roles instead of keyword roles; see
[Zax operator phrases](operator-phrases.md#keyword-words-in-phrase-roles).

## Keyword-neutral source

**Keyword-neutral source** is one independently complete expression or effective
statement inside `bare{...}` whose non-operator keyword roles are strictly
suppressed. The enclosure creates no scope or final expression-tree boundary.
See [Zax source structure](source-structure.md#keyword-neutral-bare-source).

## Current instance

The **current instance** is the instance whose type-defined function body is
executing.

Use this term when discussing the body independent of invocation or overload
selection. At a call or operator-selection boundary, use **receiver operand**.
Do not use "receiver object"; Zax is not object-oriented.

The current instance is not the same term as the **resident instance** occupying
an arbitrary instance place.

## Deliberate intent or layout error

A **deliberate intent or layout error** is the umbrella category for a bounded,
deliberately selected source pattern whose presentation conflicts with parsed
structure or whose natural form is gated because it strongly resembles a likely
mistake.

It is distinct from ordinary syntax rejection and semantic error. Source-layout
cases are owned by [Zax source structure](source-structure.md); flow-shape cases
by [Zax core flow control](core-flow-control.md).

An **acknowledgement-required intent error** has one defined but suspicious
interpretation and may be confirmed by an applicable
`intent<category>{...}` enclosure. A **non-acknowledgeable intent error**
recognizes a malformed or forbidden near miss that must be rewritten; an intent
enclosure cannot make it valid. See
[Zax intent acknowledgements](intent-acknowledgements.md#non-acknowledgeable-intent-errors).

## Declaration-attached raw allocation

A **declaration-attached raw allocation** uses a raw pointer value while its
destination declaration's life path independently schedules allocation
disposition. Copying the raw address does not copy that schedule. An accepted
terminal transfer leaves the source pointer vacant, so its later scheduled
cleanup is a no-op.

See
[Zax pointers, allocation, and arenas](pointers-and-arenas.md#scheduled-raw-allocations).

## Confusable-form intent error

A **confusable-form intent error** rejects natural source for a coherent
recognized form because it overwhelmingly resembles a damaged neighboring form
and cannot be clarified through ordinary whitespace or grouping.

```zax
||value| // candidate asymmetric circumfix resembles a missing final |
```

Unlike an operator-attachment error, the intended form needs an explicit
[intent acknowledgement](intent-acknowledgements.md) rather than ordinary
punctuation repair.

## Layout-intent error

A **layout-intent error** occurs when indentation, brace placement, clause
attachment, or physical-line position contradicts the parsed source structure.

The remedy is to correct presentation or explicit structure. It does not require
a keyword-neutral or confusable-form escape.

## Directive range

A **directive range** is the stretch of source after an unattached state marker
such as `[<deprecate=always>]`. The state lasts until a later marker for the
same directive changes it, the closing brace of the opening it is in, or the end
of its file, and it never crosses an `import`. Only `deprecate`, panic controls,
and `export` have ranges. See
[Zax compiler directives](compiler-directives.md#directive-ranges).

## Direct storage

**Direct storage** means that a value occupies the storage place declared for
it, rather than being reached through a pointer or reference.

For a directly stored value, the declared binding and value share one immediate
replacement boundary. Indirection introduces additional boundaries.

## Encoded legacy string

An **encoded legacy string** is a string whose concrete identity carries one
native-profile or named code-page interpretation rather than ordinary
`String`, ASCII, or Unicode text semantics.

An MBCS string is length-tracked, permits embedded NUL, and has no required
sentinel. Baseline `Legacy.CharString` and optional `Legacy.WideString` are
likewise length-tracked and permit NUL.

A **terminated legacy string** is a distinct concrete specialization whose
policy defines terminator units, in-band acceptance/escaping, contiguity,
physical suffix, mutation, and admission. A NUL policy may reject embedded NUL
and provide `.c_str()` semantics. See
[Zax strings and characters](strings-and-characters.md#legacy-native-and-encoded-characters).

## Eager operator

An **eager operator** evaluates every runtime operand hole required by the
selected operation exactly once in visible source order before entering its
body.

This does not evaluate both arms of a conditional expression. Non-Boolean
overloads of symbolic logical operators are ordinary eager operations.

## Exact value

An **exact value** is the same mathematical or semantic value requested by an
operation, without rounding, truncation, wrapping, saturation, substitution, or
discarded information.

An exact-width integer instead describes a type whose logical width is fixed.
An exact conversion preserves the source value. An exact arithmetic result is
the mathematical result without another policy transformation.

## Exact uncommitted real

An **exact uncommitted real** is a finite mathematical rational value with no
concrete fixed-point or floating representation yet selected.

Its initial exact operations include grouping, sign, arithmetic with rational
results, equality, and ordering. Commitment realizes the final rational once in
the selected scalar type. Compiler capacity failure is distinct from hidden
rounding in a compiler-host float. See
[literal source and operators](literal-source-and-operators.md#ordinary-numeric-source).

## Execution policy

An **execution policy** is a function's declared answer to "when may I run?":
`auto` (the default: at runtime, with invisible pre-computation allowed),
`runtime`, `folding` (at compile time whenever every argument is a
compile-time value), or `compile` (always at compile time). It is written as
`[<execute=...>]` after `final` and is part of the visible prototype. See
[Zax compile-time code](compile-time-code.md#letting-a-function-decide-execution-policies).

## First construction

**First construction** is `.=` applied to a place that is declared but still
empty: a result slot without an initializer, or a constructor member under
explicit construction control. It means what the same source would mean as a
declaration initializer and needs no replacement permission. A later `.=` on
the now-live place is reconstructive replacement. See
[Zax construction, replacement, and destruction](construction-and-destruction.md#construct-into-an-existing-place-with-dot-equals).

## Flow label

A **flow label** is a separately shaped name category that names eligible
entry, completion, or exit points of a flow construct. Most labels name one
complete flow statement. A tested runtime-selection case label additionally
names its test entry for `continue`, body entry for `goto`, and current-case
completion for `next`. A transfer-only case label has no test entry: it names
body entry for `goto` and current-case completion for `next` while active.

A flow label is not an ordinary identifier and does not share ordinary-identifier
lookup. See
[Zax core flow control](core-flow-control.md#flow-labels-and-transfer-targets)
and [Zax switch, case, and default](switch.md#labels-and-target-visibility).

## Forward anchor

A **forward anchor** is a source-ordered promise that one name has a stated
declaration category and will receive exactly one compatible direct or
exact-alias completion. It fixes root lookup while leaving category-dependent
facts pending. See
[Zax declarations and bindings](declarations-and-bindings.md#forward-anchors).

## Hook point

A **hook point** is a type member whose contract of `abstract` roles may be
fulfilled by any number of the type's partials. The type calls the roles, and
each call runs every fulfillment; if no partial fulfills a role, the compiler
removes the call. It is the only route by which a type's own code reaches its
partials. See
[Zax partials](partials.md#hooks).

## Host world

The **host world** is the copy of a program that runs on the build machine
while compiling. It has its own globals, its own `___`, and its own memory,
separate from the target program's. It affects the target program only through
the plain values its compile-time work produces. See
[Zax compile-time code](compile-time-code.md#two-programs-from-one-source).

## Indirection level

An **indirection level** is one pointer or reference layer between an access path
and a value or another pointer/reference value.

Each level may have its own place and access qualifications. See
[Zax qualifiers](qualifiers.md).

## Identity boundary

An **identity boundary** separates types that are not interchangeable even when
their representation is the same. Assignment, argument passing, return, and
operator matching cross it only through declared projection, admission, or a
direct bridge. See
[Zax identity types](identity-types.md#identity-boundary-and-underlying-type).

## Identity type

An **identity type** is a distinct outer type based on an existing underlying
type. Its declaration explicitly chooses admission and an exposed or opaque
underlying surface. See [Zax identity types](identity-types.md).

## Import injection

**Import injection** inserts declarations into one new generative module
instance before that module's own source resolves. Injected declarations use
target-module ownership and collision rules while retaining lexical access to
the injection site. See
[Zax namespaces and modules](namespaces-and-modules.md#inject-declarations-before-module-source).

## Integer realization

**Integer realization** checks an uncommitted integer's mathematical value and
sign intent against one selected concrete integer type, then creates that value
directly in the selected type.

It is not conversion from a hidden `Integer` and introduces no runtime range
check. See [Zax integer literals and realization](integer-literals.md).

## Interior pointer

An **interior pointer** is an ordinary `strong` or `weak` pointer whose target is
a direct member of a managed allocation rather than its root:

```zax
engine : Engine * strong = inner car.engine
```

It shares the allocation's control block and keeps the whole allocation alive.
It can never become blockless `unique`. See
[Zax pointers and arenas](pointers-and-arenas.md#interior-pointers).

## Intent acknowledgement

An **intent acknowledgement** confirms that the compiler's defined but
suspicious interpretation is deliberate:

```zax
intent<terminal-source-reuse>{
  resource.releaseRemainingHandle()
}
```

It grants no capability and cannot make invalid source valid. See
[Zax intent acknowledgements](intent-acknowledgements.md).

## Literal phrase and payload

A **literal phrase** is the one quoted lower-case word declared by an
`operator literal`. At a use, a quote delimiter attaches directly to that name
or qualified path.

The **literal payload** is the admitted UTF-8 source content between delimiters,
after any implicit adjacent or explicit `<|>` source merge and before the
selected literal operator interprets it. Zax performs no built-in escaping or
normalization.

See [Zax literal source and literal operators](literal-source-and-operators.md).

## Literal source merge and literal join

A **literal source merge** combines adjacent payload segments that resolve to
one literal declaration. `<|>` is its optional explicit spelling.

A **literal join** uses overloadable `<+>` to combine already parsed, concrete
constant results during compilation. Merge changes parser input; join invokes
each parser independently and then performs a value operation.

## Uncommitted generic result

An **uncommitted generic result** is a scalar literal declaration slot whose one
concrete result type is selected from payload, candidate-hole, sign-intent, and
suggestion facts before the literal body is processed and invoked.

It is generic specialization state, not an anonymous type or runtime value. See
[every invocation has one concrete result](literal-source-and-operators.md#every-invocation-has-one-concrete-result).

## Lifecycle operation

A **lifecycle operation** establishes, transitions, or ends a value or member
lifetime.

Ordinary `+++`, contextual `+++ replacement`, `---`, and the compiler-owned
generated replacement skeleton participate in lifecycle operations. An
arbitrary operator is not a lifecycle operation merely because it uses `=`.
See [Zax construction, replacement, and destruction](construction-and-destruction.md).

## Life path

A **life path** bounds the existence of one or more instances. Every instance is
constructed, lives, and is destroyed within a life path and never outlives it.

Paths may be lexical, structural, conditional, or dynamically owned. See
[Zax lifetimes and references](lifetimes-and-references.md#start-with-a-life-path).

## Minted implementation

A **minted implementation** is one callable body after it has been checked
against its own implementation prototype and its internal operations have been
selected. A compatible visible prototype may adapt call-boundary setup and
outward presentation but does not reprocess or remint that body.

Future generic specialization is different: a generic body may be reprocessed
for each demanded concrete argument set, and each result becomes a separate
minted implementation.

See
[Zax function invocation](function-invocation.md#minted-implementation-model).

## Module instance

A **module instance** is one generative import of resolved source under one
complete injection environment. It owns an isolated `Module` root, declarations,
and module state. Equal import inputs do not merge instances; exact injected
aliases may deliberately share particular declarations or dependencies. See
[Zax namespaces and modules](namespaces-and-modules.md#generative-imports-and-injection).

## Namespace

A **namespace** is a named declaration scope owned within one module instance.
It may be declared at module root or inside another namespace and may be reopened
by owner-authorized source. It is not a function-, block-, or type-local lifetime
container. See
[Zax namespaces and modules](namespaces-and-modules.md#namespaces-organize-one-module).

## Mixfix operator

A **mixfix operator** is one complete overloadable operation selected from an
expression-tree pattern containing several recognized operator components and
operand holes.

```zax
a[b] = c
```

A direct mixfix may consume the index and assignment nodes as one operation.
Complete behavior is defined by [Zax mixfix operators](mixfix-operators.md).

## Mixfix-consumption barrier

A **mixfix-consumption barrier** is a resolved source node that a user mixfix may
accept as a completed operand-hole value but may not consume as one of its key
components.

Exact Boolean short-circuit operations are barriers because consumption would
erase their conditional runtime evaluation. Compiler-owned lifecycle and flow
boundaries may be barriers or noncomponents for their own reasons. This differs
from signature protection.

## Moved-from state

A **moved-from state** is the live source state established by an accepted `move`
consumer. The original owner still destroys the value later. The type's
contract decides which other operations remain meaningful. See
[Zax transfer stances](transfer-stances.md#move).

## Native endianness

**Native endianness** is the active byte order of the environment whose execution
semantics are currently in effect.

It is distinct from **compiler-host endianness**, the byte order of the
environment running the compiler, from **target endianness**, the byte order
selected for generated target behavior, and from the absolute orders **big
endian** and **little endian**. See [Zax endianness](endianness.md).

Every concrete intrinsic scalar specialization has absolute little or big
endianness. Native or "agnostic" describes a selector resolved before a concrete
type exists, not accepted source syntax or per-value runtime state.

## Native representation

A scalar type has a **native representation** when the applicable CPU provider
classifies its format as directly represented by the CPU. This does not promise
that every operation has a direct instruction or one uniform cost. See
[Zax integers](integers.md#native-representation-and-software-emulation) and
[binary floating-point support](floating-point-scalars.md#environment-support).

## Nothing instance and Nothing policy

A **Nothing instance** is the per-concrete-type semantic target used by vacant
pointers and receiverless type calls. It is not a universal `Nothing` type or a
source-level null value.

A type's **Nothing policy** selects compiler-prepared readable backing, trapping
access, or dedicated custom backing prepared by `+++ final once`.
Defined types default to readable backing. Built-in types use one canonical
trapping policy across modules.

A **prepared Nothing representation** is the compiler-established member
representation used for readable compiler backing. It does not imply ordinary
construction of the complete enclosing type.

A **custom Nothing instance** has dedicated type-owned storage and is intended
for receiverless type calls. A **trapping Nothing policy** retains presence
testing but requests target-supported trapping on dereference or member access.
When the target lacks the needed trap, Zax does not insert universal software
checks and an access relying on that trap has undefined behavior.

See [Zax Nothing instances](nothing-instances.md).

## Observable effect

An **observable effect** is anything a computation does that is visible outside
it: output, file or device access, reading the clock, or changing state that
outlives the call. Work with no observable effect may be pre-computed under the
`auto` execution policy without changing the program's meaning. See
[Zax compile-time code](compile-time-code.md#auto-stays-invisible).

## Opaque owner and observers

An **`OpaqueOwner`** retains a managed allocation-root ownership relationship
while hiding typed access.

An **`OpaqueObserver`** is a possibly vacant, nonowning type-erased pointer
target. An **`OpaqueReferenceObserver`** is a non-vacant, fixed type-erased
reference target.

All preserve private exact type/origin/capability facts for safe recovery
without creating an arbitrary “any” value. See
[Zax pointers, allocation, and arenas](pointers-and-arenas.md#type-erased-ownership-and-observation).

## Normal completion

**Normal completion** is relative to the construct being discussed. A body
normally completes when it reaches its end without an abrupt transfer or panic;
that completion runs any applicable post operation and satisfies required state.

`break`, `continue`, `next`, `goto`, and `return` are abrupt relative to
enclosing flow bodies. An explicit `return` may still be a normal function exit,
as opposed to panic or non-completion, and must satisfy the function's result
obligations. See
[Zax core flow control](core-flow-control.md),
[Zax function invocation](function-invocation.md), and
[Zax construction, replacement, and destruction](construction-and-destruction.md).

## Panic

A **panic** blocks the operation that encountered its condition. A matching
helper may repair the condition and let that same operation resume and complete;
otherwise the process crashes gracefully.

Panic never skips the operation, returns a substitute, unwinds scopes or
completed members, rolls work back, or returns partial lifecycle state to
ordinary execution. See
[Zax safety and analysis](safety-and-analysis.md#panic-boundary).

## Open-ended raw allocation

An **open-ended raw allocation** has no declaration-attached or managed
disposition owner. Safe use requires another proved owner or a complete manual
`reset` protocol; otherwise the programmer assumes narrow unsafe responsibility.

See
[Zax pointers, allocation, and arenas](pointers-and-arenas.md#open-ended-raw-allocations).

## Operand hole

An **operand hole** is one expression position in a mixfix tree skeleton. Source
at that position resolves as a complete ordinary expression and supplies one
mixfix input.

See [Zax mixfix operators](mixfix-operators.md#trees-components-and-holes).

## Operator component

An **operator component** is one recognized pre-unary, post-unary, binary,
circumfix, call, index, or phrase form fixed inside a mixfix tree skeleton.

Consumed components do not execute independently when the mixfix is selected.
See [Zax mixfix operators](mixfix-operators.md).

## Operator form

An **operator form** is the general category for a language-recognized symbolic,
phrase, circumfix, call/index, or mixfix operation when the exact source category
does not matter.

## Operator phrase

An **operator phrase** is an operator form written as one or more exact words
rather than a punctuation-only symbol:

```zax
ready := left logical nand right
converted := source as DestinationType
```

A phrase is not a second dispatch mechanism; it uses the shared callable and
operator model. See [Zax operator phrases](operator-phrases.md),
[Zax operators](operators.md#operator-phrases), and the
[operator catalog](operator-catalog.md#operator-forms).

## Optional wrapper and boxed value

An **optional wrapper** is one live `T?` value whose state is absent or present.
When absent, it contains no live boxed value. When present, it owns exactly one
live **boxed value** of type `T`.

The wrapper and boxed value have separate lifetimes and qualifications. Postfix
optional access crosses the wrapper boundary and produces access under the boxed
qualifications after presence proof. See
[Zax optional values](optional-values.md).

## Phrase word

A **phrase word** is one canonical word in an operator phrase. It matches
`[a-z][a-z0-9]*`: an ASCII lower-case letter followed by ASCII lower-case letters
or digits.

The complete word and presentation rules are owned by
[Zax operator phrases](operator-phrases.md#exact-finite-forms).

## Phrase component

A **phrase component** is one contiguous exact word sequence participating as an
operator or as one component of a
[mixfix](mixfix-operators.md#phrase-components) skeleton.

A component carries exactly one fixity: pre-unary, post-unary, or binary.

## Phrase fence

A **phrase fence** is explicit local source requiring one exact phrase component:

```zax
foo 'bar'
```

It eliminates identifier, keyword, literal, and differently worded phrase
interpretations for the fenced words. It does not group, add a precedence
boundary, or select an implementation. See
[Zax operator phrases](operator-phrases.md#exact-phrase-fencing).

## Protected form

A **protected form** is an exact operator form that user code cannot declare for
any receiver, such as `as move`, `size of`, `unsafe cast`, or `outer cast`.
Protecting a form holds it for the language even when some of its domain
behavior remains future work.

A protected form differs from a
[protected intrinsic signature](#protected-intrinsic-signature), which leaves the
same spelling open for programmer types. The protected forms are listed by the
[operator catalog](operator-catalog.md#protected-forms).

## Operator-attachment intent error

An **operator-attachment intent error** occurs when whitespace, adjacency, or
grouping presents another fixity or number of operator applications than the
programmer intends.

```zax
!!value   // cannot mean two independent ! applications
!(!value) // corrected through grouping
```

It is repaired through ordinary source presentation, unlike a confusable-form
intent error.

## Ordinary syntax rejection

**Ordinary syntax rejection** means source that matches no legal production. The
parser reports the legal token or construct expected at that point.

The language does not enumerate every unrelated thing a programmer could write,
and this is distinct from a semantic error or a deliberate intent or layout
error.

## Parameter slot

A **parameter slot** is one ordered input position declared by a callable
prototype.

Positional, named, omitted, or routed source input may bind the slot. The slot is
complete before the callable body begins. See
[Zax function invocation](function-invocation.md).

## Partial

A **partial** is a named, add-only contribution of declarations to an existing
concrete type. Its additions are visible only where a scope grants the partial,
and the type's `seal` clause decides which kinds of addition are allowed. A
partial's name identifies the contribution; it is not a type. See
[Zax partials](partials.md).

## Place

An **instance place**, usually shortened to **place**, is the stable typed
position within a life path through which a resident instance is reached.
Replacing a varying place changes which resident instance that place exposes
without ending the place itself.

Place replacement is distinct from changing the contents of the value already
stored there. See [Zax qualifiers](qualifiers.md) and
[Zax lifetimes and references](lifetimes-and-references.md#life-path-instance-place-and-resident-instance).

## Pointee

A **pointee** is the place or value targeted by a pointer.

The binding storing a pointer and its pointee are distinct qualification layers.
A vacant pointer semantically targets its pointee type's Nothing instance.
Unchecked pointer dereference may mechanically bind a reference to that special
backing; the reference itself has no vacancy state.

## Post operation

A **post operation** is the construct-specific flow-header section that runs on
normal completion, and on a `next` transfer, while header bindings remain alive.

`break`, `continue`, `goto`, and `return` skip it when they cross that phase. It
is ordinary work, not a destructor or guaranteed cleanup hook. See
[Zax core flow control](core-flow-control.md).

## Projection

A **projection** creates an access path to part of a value, such as member access
through a container.

A projection derives capabilities from the path used to reach the containing
value. Complete projection behavior belongs with the relevant language concept.

An **identity projection** is a separate qualified use: an explicit conversion
from an identity value to its immediate underlying type. It does not mean member
access and need not create an access path. Documentation uses the qualified term
when that distinction matters; see
[Zax identity types](identity-types.md#identity-projection).

A **preferred projection** lets a container stand in for one of its
stored members when the surrounding expression already expects that member's
type. The member is declared `preferred`; data publication through `own` is an
independent choice:

```zax
Car :: type {
  engine preferred : Engine
}

tune(car) // `tune` expects Engine, so `car.engine` may be selected
```

This projection travels from container to member. It helps bind an already
known destination; it does not search the member for functions or operators.
See [Zax composition](composition.md#expected-type-projection-with-preferred).

## Published member path

A **published member path** is a shortened access path that `own` makes
available for a contained stored instance member. It is not another declaration
or another storage location:

```zax
car.rpm        // published path
car.engine.rpm // physical path to the same Integer
```

Both paths reach the same place and therefore the same resident instance,
lifetime, and qualifications. See
[Zax composition](composition.md#publishing-stored-data-with-own).

## Outward declaration

An **outward declaration** is a declaration marked `outward` inside a branch,
which belongs to the scope around the nearest enclosing `if`, `switch`, or
`scope` instead of to the branch. Every path that reaches later code must
declare it exactly once:

```zax
if useLarge {
  limit outward final : Integer = 1000
} else {
  limit outward final : Integer = 10
}
```

See [Zax declarations and bindings](declarations-and-bindings.md#outward-declarations).

## Outer cast

An **outer cast** starts with a reference or pointer to a stored member and
casts outward to the immediate container that holds the member at a named
physical path:

```zax
car : Car & = engine outer cast Car.engine
possibleCar : Car & ? = engine tracked outer cast Car.engine
```

Plain `outer cast` requires a proof mandated by the selected language contract
and returns a non-optional reference without runtime tracking.
`tracked outer cast` checks the placement at runtime; a reference operand
produces an optional reference, and a pointer operand produces a same-role
pointer that is vacant on failure. `unsafe outer cast` instead asserts that the
placement is correct. An outer cast of a `strong` or `weak` pointer keeps its
ownership role. The exact path matters when a container
stores several members of the same type.

The word `outer` has a separate contextual type use inside an abstract
composition role, where it denotes the immediate container that activates the
role. See [abstract roles and explicit fulfillment](composition.md#abstract-roles-and-explicit-fulfillment)
and
[Zax composition](composition.md#outer-casting-to-an-immediate-container).

## Protected intrinsic signature

A **protected intrinsic signature** is an exact operator signature whose every
operand belongs to a closed intrinsic family. User declarations cannot replace
it or claim a currently unavailable all-intrinsic combination.

Protection preserves predictable primitive behavior and future language
evolution. A mixed signature containing a custom operand remains extensible.
Examples include exact `Boolean` logical phrases, scalar arithmetic and
conversion, pointer queries such as `liveness probe`, and `inner` applied to a
pointer-rooted member path. See
[Zax operators](operators.md#protected-intrinsic-domains).

## Receiver anchor

A **receiver anchor** is the operand hole whose resolved type owns and supplies
type-defined discovery for a mixfix declaration.

Other holes affect viability but do not contribute type-defined mixfix
declarations. See [Zax mixfix operators](mixfix-operators.md#receiver-anchor).

## Receiver operand

The **receiver operand** is the implicit operand representing the instance on
which a type-defined function or operator acts.

Use the complete term where context permits. "Receiver" alone has other
meanings, and "receiver object" incorrectly suggests an object-oriented model.

All three qualifier axes may constrain a receiver operand. See
[Zax qualifiers](qualifiers.md#receiver-operands).

## Receiverless type call

A **receiverless type call** invokes a `once bound` declaration through its
containing type rather than through an instance expression:

```zax
MyType.inspect()
```

`_` identifies the type's Nothing instance, `?_` is false, and the synthesized
receiver offers `copy`. An instance-qualified call to the same declaration uses
its actual receiver. See
[Zax Nothing instances](nothing-instances.md#receiverless-and-instance-calls).

## Receiver stance

A **receiver stance** is the transfer stance offered by the receiver operand
when selecting a type-defined function or operator:

```zax
document : Document deep
document.publish()
```

It is independent from parameter and result stances. See
[Zax transfer stances](transfer-stances.md#receiver-stance).

## Redundant-structure intent error

A **redundant-structure intent error** occurs when source supplies two competing
mechanisms for one structural role, such as explicit `\` where an open delimiter
or recognized separator already continues the newline.

The remedy is to remove the redundant marker. This is distinct from harmless
explicit source that merely repeats information.

## Result-routing group

A **result-routing group** evaluates one producer, then maps or discards its
ordered results through explicit source-result and destination cursors.

The producer executes once. See
[Zax function invocation](function-invocation.md#result-routing-groups).

## Result shape

A **result shape** is one ordered sequence of zero or more result slots declared
by a callable prototype. The ordinary success outcome has its complete ordinary
result shape; each exceptional outcome has its one named payload shape.

Several results are not implicitly one tuple or structural value. See
[Zax function invocation](function-invocation.md#result-slots).

## Result slot

A **result slot** is one ordered output position declared by a callable
prototype.

An ordinary slot begins as an output obligation and must contain one complete
value whenever success is selected. A result initializer may opt into
construction before body entry; that constructed/unconstructed entry state
participates in compatible prototype behavior. An exceptional slot begins
unconstructed and is constructed only when its outcome is selected. See
[Zax function invocation](function-invocation.md#result-slots).

## Provisional result

A **provisional result** is a live result instance whose completion outcome has
not yet committed it to an outer destination. Its current provisional owner
must destroy it if another outcome is selected.

Successful mapping transfers that cleanup responsibility outward, including
when several calls elide into final destination storage. See
[Zax exceptional result flow](except.md#conditional-result-elision).

## Reshape

A **reshape** is a no-storage, directional declaration mapping source member
paths to destination member paths for `-<>-` transformation, callable results
to inputs, or exceptional outcome labels to other exceptional labels.

It has no runtime instance or anchor. Explicit entries use source-to-destination
order, and reversing a map requires another declaration. A use accepts one
inline mapping or one named/anonymous reshape declaration; reshape entries never
use commas. See
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md#reusable-mapping-with-reshape).

## Reconstructive replacement

**Reconstructive replacement** is the protected `.=` transition that ends one
complete mutable or immutable value lifetime and establishes a successor in the
same storage.

The compiler owns the complete lifecycle skeleton. It selects a replacement
constructor when a viable customization exists; otherwise it uses the generated
fallback of enclosing `---` followed by ordinary `+++`.

It requires a varying place through a declaration-side varying, writable access
path. A final place or readonly path cannot select it. On an empty result slot
or an empty explicitly controlled constructor member, `.=` instead performs
first construction, which needs none of those permissions. See
[Zax construction, replacement, and destruction](construction-and-destruction.md#reconstructive-replacement)
and [Zax qualifiers](qualifiers.md#reconstructive-replacement).

## Replacement constructor

A **replacement constructor** is an optional type-defined customization selected
within the compiler-owned reconstructive-replacement operation.

It is written with contextual `+++ replacement`. When selected, it runs instead
of the generated fallback's enclosing `---` followed by ordinary `+++` and may
recycle the previous representation and resources while establishing the
complete replacement instance. Like an ordinary constructor, it declares no
results; the `.=` expression produces access to the replaced place. Protected
`.=` supplies its direct or positional/named packet inputs. Complete behavior is defined by
[Zax construction, replacement, and destruction](construction-and-destruction.md#custom-replacement).
The qualification boundary is defined by
[Zax qualifiers](qualifiers.md#reconstructive-replacement).

## Referent

A **referent** is the place or value reached through a reference.

The binding storing a reference and its referent are distinct qualification
layers.

## Reference binding

A **reference binding** permanently associates one reference instance with one
instance place. The resident instance in a varying place may be renewed, but the
reference never retargets to another place.

See
[Zax lifetimes and references](lifetimes-and-references.md#references-never-rebind).

## Resident instance

The **resident instance** is the completely constructed instance currently
available through an instance place.

A varying place may expose successive resident instances. Do not use this term
for `_` merely because a type-defined body is executing; that established role
is the **current instance**. See
[Zax lifetimes and references](lifetimes-and-references.md#life-path-instance-place-and-resident-instance).

## Semantic error

A **semantic error** is source that parses successfully but violates a type,
target, name-resolution, lifetime, or completion rule.

It is distinct from ordinary syntax rejection and from a deliberate intent or
layout error. The violated rule and its behavior remain with the applicable
concept owner.

## Scalar format

A **scalar format** is the complete type-level interpretation of one scalar's
logical bits and storage envelope. Applicable properties include width,
signedness, coefficient scale, exponent/fraction partition, value encoding,
endianness, valid patterns, non-value bits, and normalization.

Scalar format is distinct from public type identity, target support, operation
cost, and ABI compatibility. See
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md#scalar-compatibility),
[fixed-point scalars](fixed-point-scalars.md), and
[floating-point scalars](floating-point-scalars.md).

## Sealed type

A type is **sealed** to a category of addition when [partials](partials.md)
may not add that kind of declaration to it. A type's `seal` clause opens or
closes each category: `callable`, `nested`, `once`, `storage`, and `module`.
By default, storage is sealed and the other additions are open. Language-provided
types are sealed to storage and open to added functions. See
[what a type allows others to add](partials.md#what-a-type-allows-others-to-add).

## Short-circuit operator

A **short-circuit operator** has language-defined evaluation in which an earlier
operand result may determine that a later runtime operand is not evaluated.

The skipped operand still resolves and validates at compile time. Short-circuit
behavior is not general laziness: the language fixes which operand may be
skipped, and a user overload does not receive an arbitrary unevaluated
expression.

## Software emulation

**Software emulation** implements a scalar operation through several native
operations or automatically supplied helper behavior when the CPU does not
natively represent or directly operate on the requested width.

A **software fallback** is a type-selection result that uses software emulation
because no native candidate satisfies the request. See
[Zax integers](integers.md#native-representation-and-software-emulation) and
[floating-point environment support](floating-point-scalars.md#environment-support).

## Shareable unique pointer

A **shareable unique pointer** has exclusive ownership and a dormant control
block reserved for entry into local or atomic strong/weak ownership.

It is written `T * unique shareable`. See
[Zax pointers and arenas](pointers-and-arenas.md#shareable-unique-ownership).

## Strong and weak pointers

A **strong pointer** participates in shared ownership and keeps its allocation
root alive. A **weak pointer** observes that ownership without keeping it open.

Weak-to-strong construction may fail and produce an empty strong pointer.
Pointer-layer `atomic` controls cross-thread lifetime accounting, not pointee
thread safety. See [Zax pointers and arenas](pointers-and-arenas.md).

## Suggested commitment

A **suggested commitment** is the concrete type a generic literal result selects
when no destination or candidate hole supplies another type. It is considered
before realization and range checking; it is not an already selected type and
does not itself establish sign intent.

## Unicode scalar and grapheme cluster

A **Unicode scalar** is a code point from `U+0000` through `U+10FFFF`, excluding
the surrogate range `U+D800..U+DFFF`. `Rune` represents one scalar.

A **grapheme cluster** is a presentation-oriented sequence that may contain
several scalars while being displayed as one perceived character. It is not one
`Rune`, and Zax strings do not implicitly normalize or validate grapheme
presentation. See [Zax strings and characters](strings-and-characters.md).

## Union-admissible type and union lens

A **union-admissible type** has passive storage that may be overwritten, copied
as part of complete union backing, and abandoned without bypassing required
lifecycle, ownership, reference-binding, or placement work.

A **union lens** is one named offset-zero typed interpretation of complete
untagged union backing. It is not an independently constructed resident member
and does not become active. See [Zax unions](unions.md).

## Symbolic operator

A **symbolic operator** is an operator whose recognized source form is written
primarily with punctuation, such as `+`, `&~`, or `<<%`.

The language owns the closed symbolic catalog and precedence. Declarations may
overload permitted forms but cannot invent arbitrary punctuation. See the
[operator catalog](operator-catalog.md).

## Type argument

A **type argument** is the concrete type identity supplied to a
[type parameter slot](#type-parameter-slot):

```zax
converted := source as DestinationType
```

A type argument has no runtime storage, lifetime, or evaluation. See
[Zax function invocation](function-invocation.md#type-parameter-slots).

## Type anchor

A **type anchor** is a parsed operand or type argument that supplies the one
concrete type a contextual operation may use to complete a missing numeric
receiver or peer.

```zax
myValue := 0 + (: MyInteger = 5)
myByte := 55 as U8
```

The first source uses the concrete peer's type; the second uses the written type
argument. An anchor does not authorize type search or construction by itself.
See
[Zax integer literals and realization](integer-literals.md#when-a-visible-type-may-complete-an-operand).

## Target environment

The **target environment** is the environment selected for generated program
behavior. It is distinct from the compiler host and may be named explicitly by
environment-relative type paths. See
[Zax integers](integers.md#native-representation-and-software-emulation).

## Terminal source state

A **terminal source state** is the destruction-valid state established by an
accepted `last` consumer. The outer source lifetime remains owned by its original
declaration and its destructor still runs. Ordinary later use requires a
defined terminal-state operation and an intent acknowledgement. See
[Zax transfer stances](transfer-stances.md#last).

## Transfer stance

A **transfer stance** says how one complete source or receiver is offered to a
consumer. `copy`, `deep`, `move`, and `last` are the four stances.

The **offered stance** comes from the declaration or a one-use `as <stance>`
restatement. The **accepted stance** is the consumer selected after fallback.
The accepted consumer, not the stance expression itself, performs transfer and
establishes source state.

See [Zax transfer stances](transfer-stances.md).

## Transparent alias

A **transparent alias** is another source name for the same canonical type
identity. A concrete alias may overlay requested qualifications, stance,
indirection, and compatibility posture without converting a value or creating
another identity. `alias type`, `alias union`, and `alias variant` preserve
their respective declaration categories. An alias creates no conversion or
overload domain. See
[Zax identity types](identity-types.md#transparent-aliases).

An **exact variable alias** similarly adds another declaration name for the same
variable slot or approved polymorphic variable family. It is not a runtime
reference or compatible callable wrapper. See
[Zax declarations and bindings](declarations-and-bindings.md#exact-aliases-and-property-overlays).

## Type parameter slot

A **type parameter slot** is a prototype input completed by one concrete type
identity rather than a runtime value:

```zax
DestinationType : type
```

It occupies its ordered slot for viability, preference, and diagnostics like any
other parameter. See
[Zax declarations and bindings](declarations-and-bindings.md#type-parameter-slots-and-type-arguments).

## Type-qualified operator

A **type-qualified operator** is owned and discovered through a concrete type
identity used as a lookup anchor:

```zax
BackingType :: alias type EnumType underlying type
```

An `unbound` declaration has no runtime receiver and `_` is unavailable. A
`once bound` declaration deliberately supports both type-qualified
receiverless and instance-qualified routes.

Type qualification does not make an operation inherently compile-time. See
[Zax declarations and bindings](declarations-and-bindings.md#type-qualified-operators).

## Type use

A **type use** is an occurrence of a type in a declaration or expression
context, as distinct from the type's definition.

A type use may carry contextual qualification without changing the underlying
type definition.

## Underlying type, value, and place

An identity type's **underlying type** is the existing type on which its
representation begins. The **underlying value** is the value used to create the
identity or returned by identity projection. See
[Zax identity types](identity-types.md#identity-boundary-and-underlying-type).

The **underlying place** is the immediate storage place beneath an identity
boundary. Access to it is authority- and qualification-bound and does not by
itself promise a first-class reference or escaping alias. A composed stored
member instead has an ordinary physical member path and may also have a
published path. See
[Zax identity types](identity-types.md#immediate-underlying-operations-do-not-forward).

## Uncommitted integer

An **uncommitted integer** is a compiler-known mathematical integer value with
unknown, signed, or unsigned intent but no concrete type, logical width,
storage, identity, or runtime representation.

It must reach a commitment boundary before it can become a runtime value. See
[Zax integer literals and realization](integer-literals.md).

## Uncommitted integer evaluation

**Uncommitted integer evaluation** applies the closed language-defined family
of width-independent mathematical operations before a concrete integer type is
selected.

It ends for an expression node when that node receives a concrete type. General
typed expression resolution and constant evaluation may continue afterward.
See [Zax integer literals and realization](integer-literals.md).

## Width invariance

An integer operation is **width-invariant** for given uncommitted operands when
every sufficiently wide concrete interpretation that can contain the operands
and mathematical result produces the same decoded number.

```zax
myBits := 55 ^ 77 // width-invariant mathematical 122
myComplement := ~1 // error: signed and unsigned results differ
```

This test decides whether the operation may run before a concrete integer width
is selected. See
[Zax integer literals and realization](integer-literals.md#which-calculations-need-an-integer-width).

## Variant wrapper and active alternative

A **variant wrapper** is one live variant value whose state is absent or names
exactly one active alternative.

An **active alternative** owns the conditional payload life path selected by
that name. Reset, contained reconstruction, complete wrapper replacement, or
destruction ends it. Alternative names remain distinct even when their payload
types match. See [Zax variants](variants.md).

## Value

A **value** is data together with the language-level meaning of its type. A value
may occupy a place, be copied into a new place, be moved, or be observed through
multiple access paths.

Value mutation, place replacement, and access capability are separate concerns.
See [Zax qualifiers](qualifiers.md).

## Vacant pointer and vacate

A **vacant pointer** is a live pointer value that semantically targets its
pointee type's Nothing instance rather than an ordinary target. Pointer-role
presence remains distinct from pointee lifetime and provenance.

`vacate` is the protected raw-pointer operation that discards one address and
installs vacancy without disposition. Plain use requires proof that no
disposition authority is lost; `unsafe vacate` accepts responsibility only for
an opaque but potentially valid raw relationship. Managed pointers and
guaranteed leaks reject it even under `unsafe`.

See [Zax pointers and arenas](pointers-and-arenas.md#vacating-a-raw-pointer).

## Well-formedness check

A **well-formedness check**, `wellformed{...}`, is a compile-time `Boolean`
answering whether the enclosed source would form valid code at that point. The
payload is checked but never run:

```zax
if wellformed{ ++myValue } {
  scaleUp(myValue)
}
```

See [Zax compile-time code](compile-time-code.md#checking-whether-code-is-well-formed-wellformed).

## Visibility grant

A **visibility grant**, written `Path :: expose partial`, makes one partial's
additions visible from that point to the end of the enclosing block, including
nested blocks. Without a grant, a partial's names are not visible, and its
candidates take no part in selection. See
[Zax partials](partials.md#making-a-partial-visible).

## Value lifetime

A **value lifetime** is the period during which one particular value exists in a
place as its resident instance.

Construction establishes a value lifetime. Destruction or reconstructive
replacement ends it. Complete replacement renews every member resident
instance. See
[Zax construction, replacement, and destruction](construction-and-destruction.md#mental-model)
and [Zax lifetimes and references](lifetimes-and-references.md).
