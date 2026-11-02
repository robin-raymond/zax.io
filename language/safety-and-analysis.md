# Zax safety and analysis

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers reasoning about Zax's safe subset, compiler proof, unsafe responsibility, and source portability |
| Applies To | Cross-cutting programmer-visible safety and static-analysis behavior; not a formal proof system or diagnostic specification |
| Implementation State | Not established by this repository |
| Owns | The safe-subset model; required proof; narrow unsafe assertions and permissions; known-invalid boundaries; distinction from intent and lints; language-contract versus compiler-analysis evolution; panic-check control attachment, ranges, import boundary, and build starting state; cross-cutting safety diagnostics and source stability |
| Does Not Own | Domain-specific validity and admission rules, including [enum admission](enums.md#admission-and-reachable-values); exact future unsafe syntax and category registry; intent acknowledgements ([intent acknowledgements](intent-acknowledgements.md)); or compiler implementation algorithms |
| Source / Provenance | Current intent, construction, invocation, optional, lifetime, and pointer safety boundaries |
| Supersedes | Accepted general safety direction formerly preserved only as project input |

## Safety is a selected contract

Zax does not organize the entire language around maximizing compiler-enforced
safety. It does define a safe subset with explicit guarantees.

Code using only safe operations and interfaces receives the guarantees assigned
to the selected language contract. Operations outside those guarantees must be
recognizable and auditable.

The central rule is:

> For every property mandatory under the selected language contract, Zax
> attempts to prove the operation valid. If proof cannot be established, the
> operation is rejected unless a narrow unsafe assertion or permission provides
> a valid lower-level interpretation. Unsafe source cannot make a known
> impossibility valid.

The contract decides what must be proved. One executable compiler's current
cleverness does not redefine portable source validity.

## Four analysis outcomes

### Proven safe

The compiler establishes every required fact:

```zax
value : MyValue
view : MyValue readonly & = value

inspect(view)
```

No unsafe marker is needed.

### Unproved but valid

The operation may be valid, but analysis lacks a required fact because code is
opaque, alias relationships are complex, or another recognized proof boundary
intervenes.

Illustrative future syntax:

```zax
/// The callback consumes the view synchronously and stores nothing.
unsafe<lifetime-escape>{
  invokeOpaqueCallback(view)
}
```

The assertion says that a fact is true. It does not change runtime behavior.

### Defined unsafe permission

Some operations have a coherent lower-level meaning but are deliberately
excluded from ordinary safe source:

```zax
// Illustrative category identifier; exact name remains unsettled.
/// The member place and successor representation remain suitable.
unsafe<replacement-member-successor>{
  inspect(memberView)
}
```

Here the enclosing instance was completely replaced, so the old member resident
instance ended. The permission deliberately accesses the completely established
successor through the continuing member place.

An unsafe permission differs from an assertion:

- an assertion supplies an unproved fact required by an otherwise safe
  operation;
- a permission deliberately crosses a defined safe-language boundary.

Both must identify the exact property and source region they affect.

### No valid interpretation

Unsafe source cannot manufacture:

- a resident instance where none exists;
- a life path after it ended;
- ownership that was never acquired;
- initialization that did not occur;
- a reference rebound to another place;
- a value of an incompatible type or representation;
- or any other known-impossible state.

```zax
value.---()
inspect(value) // error: no resident instance exists
```

There is no unsafe spelling that makes this ordinary access valid without first
establishing another instance through a real lifecycle operation.

The threshold for declaring a case impossible is intentionally high.
Conservative analysis belongs in the unproved or defined-unsafe categories
rather than being mislabeled as universal invalidity.

## Unsafe is narrow

Unsafe control is not a broad alternate language mode.

Every unsafe operation or enclosure must eventually:

- use the literal `unsafe`;
- identify the exact category;
- name or enclose the affected operation, binding, member, or expression;
- apply at the narrowest useful source location;
- permit attached documentation explaining the responsibility;
- alter only the stated proof or permission boundary; and
- require no runtime check unless the underlying selected operation independently
  specifies one.

One enclosure may identify several exact categories without granting unrelated
authority.

Exact syntax, category identifiers, nesting, reflection, and extension
provenance remain future analysis-control work.

## False unsafe claims

A false lifetime, alias, presence, construction, or transition assertion may
have undefined consequences. The language does not require a runtime check.

Compiler debugging modes may instrument unsafe claims and panic when misuse is
detected. Such instrumentation is a tooling feature, not a portable language
guarantee.

An assertion may fill a proof gap. It cannot contradict a fact the compiler
already knows:

```zax
value.---()

unsafe<lifetime>{
  inspect(value) // error: known-ended lifetime
}
```

## Diagnosable unchecked Nothing access

Nothing-instance access has one deliberate low-level boundary that does not
require proof before every operation. Postfix pointer dereference is unchecked:

```zax
pointer : MyValue * = choosePointer()
view : MyValue & = pointer.
```

If analysis proves `pointer` vacant, the compiler reports an error. If runtime
vacancy escapes analysis, the reference mechanically reaches the type's Nothing
backing. Prepared reads may succeed, unprepared access uses its defined panic
path where available, and a write to compiler-provided shared backing is
invalid.

Zax does not require a generated vacancy check on every read or store. Suitable
target protection traps writes and requested trapping-policy reads; debug
instrumentation may detect additional misuse. When the target lacks the needed
trap, a missed access can read prepared backing, corrupt shared backing, or have
undefined consequences.

Compiler improvements may diagnose more certainly invalid source without
requiring every implementation to use one proof algorithm. How a compiler acts
on a high-confidence but unproved flow remains future safety/error design. Any
unsafe responsibility may belong at the call or handoff that exposes a possibly
vacant pointer to a writer, rather than on the eventual store; it is not an
intent acknowledgement. The future unsafe catalog retains provisional
`possible-nothing-write` and `possible-nothing-read-trap` categories for those
two uncertain-flow classes.

This is not an unsafe assertion silently inserted by the compiler. It is a
domain rule for unchecked pointer access whose exact behavior is owned by
[Zax Nothing instances](nothing-instances.md#pointer-dereference-is-unchecked).

## Intent, unsafe, and linting are different

| Mechanism | Programmer statement |
| --- | --- |
| `intent<...>{...}` | “This defined but suspicious interpretation is deliberate.” |
| Unsafe assertion | “This required fact is true although the compiler cannot prove it.” |
| Unsafe permission | “I deliberately accept this defined lower-level boundary.” |
| Lint suppression | “Do not report this advisory diagnostic here.” |

### Intent does not accept safety responsibility

```zax
intent<terminal-source-reuse>{
  resource.releaseRemainingHandle()
}
```

This confirms defined terminal-source behavior. It does not grant lifetime,
ownership, mutation, or alias authority.

An interior pointer may require intent because replacement will make it observe
a successor member resident instance. The operation is defined; the
acknowledgement confirms that surprising meaning.

### Lints do not change semantics

A reference may safely observe a value changed through another authorized path.
A lint can ask whether that observation was intended without making the source
unsafe.

Lint suppression:

- changes no language behavior;
- creates no proof;
- grants no permission;
- suppresses no required semantic error; and
- should not require `unsafe`.

## Domain rules remain with their owners

This document owns the reusable safety model. Domain owners define the facts to
which it applies.

Examples:

- [construction and destruction](construction-and-destruction.md) defines when
  instances and members are live or complete;
- [lifetimes and references](lifetimes-and-references.md) defines reference
  targets, escape, and replacement boundaries;
- [pointers and arenas](pointers-and-arenas.md) defines ownership, weak
  acquisition, arena obligations, and raw-pointer responsibility;
- [optional values](optional-values.md) defines presence and boxed access;
- [function invocation](function-invocation.md) defines result and parameter
  completion;
- [composition](composition.md) distinguishes proof-required and tracked checked
  outer casting, defines the receiver-origin proof required for non-optional
  outer result remapping, and owns `tracked via`, `unsafe via`, and
  `unsafe outer cast`;
- [transfer stances](transfer-stances.md) defines moved-from and terminal source
  states;
- integer owners define overflow, narrowing, and required-result behavior; and
- [enums](enums.md) defines strict and flags admission domains and the values
  produced by enum operations.

An unsafe category does not become a duplicate definition of the domain rule it
qualifies.

In particular, an intent acknowledgement cannot authorize a composition
provenance claim. `unsafe via` and `unsafe outer cast` accept responsibility for
an unproved relationship at the exact forwarding or outer-cast site. A false
claim has the unsafe consequences defined by the composition operation.

### Unsafe union lens validity

A plain [union](unions.md) admits only passive, all-bit-pattern-valid lenses, so
every lens remains safe after every write.

`union unsafe` admits passive representations with restricted value domains.
The selected language contract may prove one lens valid from zero-fill, a
preceding write, exact representation facts, or preserved flow. When it cannot,
interpreting the backing through that lens uses the exact unsafe category
`union-lens-validity`.

The category asserts:

> The current union backing is a valid ordinary value representation for this
> requested lens.

It neither converts the bits nor starts a lens lifetime. A false claim has
undefined consequences. Known-invalid representation remains an error.

Private compiler analysis may optimize or advise, but it does not silently
change portable assertion requirements. Stronger source validity belongs to a
selected contract as described under
[language contracts and compiler analysis](#language-contracts-and-compiler-analysis).

### Checked variant access

Named [variant](variants.md) access has a runtime-checkable selection condition:

- proved active removes the check;
- proved inactive is an error;
- unresolved selection performs the registered
  `inactive-variant-access` check and panics on mismatch; and
- disabling that category promises the requested name is active, with undefined
  consequences if the promise is false.

Disabling the check never suppresses a statically proved mismatch and does not
affect `.=` or switch routing, which establish selection through their own
operations.

This supplies a concrete distinction among proved invalid source,
runtime-checkable uncertainty, an uncheckable proof gap, and high-confidence
compiler suspicion. Warning policy for the last category remains future
analysis-control work; source validity does not vary with an implementation's
confidence heuristic.

### Enum raw-admission permission

```zax
raw : U8 = readByte()
value := MyEnum unsafe from raw
```

Enum `unsafe from` is a defined permission to bypass the enum owner's ordinary
admission policy while preserving the exact backing representation. A strict
enum need not name the value, and a flags value may contain bits outside its
safe allowed mask. The result is still a defined enum value whose available
operations follow their declarations.

This permission does not perform integer conversion or accept a different
backing type. It changes no `optional from` result and does not expand ordinary
safe admission. Complete enum consequences are defined by
[Zax enums](enums.md#unsafe-from).

## Representative lifecycle boundaries

### Partial construction

A constructor helper may need access to already-live members before the complete
enclosing instance exists. That bounded access differs from publishing the
incomplete instance through a global, callback, or stored reference.

Unsafe controls must distinguish:

- bounded partial-instance access;
- escape or external publication;
- asserting that opaque work established a member, which
  [`opaque-construction`](construction-and-destruction.md#construction-by-an-opaque-operation)
  does at the establishing operation and which also takes the member out of
  automatic construction;
- suppressing a generated destruction; and
- claiming path completeness or at-most-once behavior.

### Replacement and aliases

A replacement right-hand operand may alias the destination or one of its
members. Analysis must account for whether the operation:

- copies required values before ending them;
- consumes an alias before renewal;
- delegates exact self-alias handling to the selected operation;
- preserves only storage or resources rather than an old member lifetime; or
- deliberately crosses a defined unsafe successor boundary.

### Returned references

A returned reference must target a place whose life path outlives every use.
Returning a local or temporary is known invalid when it escapes. An opaque but
valid origin may need an unsafe assertion or future callable-origin contract.

### Raw pointers

A non-vacant raw pointer does not prove:

- live pointee storage;
- a resident instance;
- valid provenance;
- alignment;
- permitted aliasing; or
- ownership.

A raw-pointer operation is safe when analysis proves every fact required by the
selected contract. A structurally bounded pointer whose allocation,
construction, use, destruction, and recovery remain inside one containing life
path may be fully provable.

`?rawPointer` proves only non-vacancy. Existing origin analysis may prove the
remaining facts, or an escape, opaque operation, owner release, reset,
relocation, or arena teardown may invalidate that proof.

Raw-pointer operations require explicit unsafe responsibility only where the
safe contract requires a fact the compiler cannot prove. A known-invalid
pointee remains invalid inside unsafe source.

The same boundary governs raw allocation adoption and reset:

```zax
manual : MyValue * = @<
reset manual
```

Safe reset requires proof that `manual` identifies the allocation root and that
the operation has sole disposition authority. When the relationship may be valid
but provenance or alias information is opaque, narrow unsafe responsibility may
assert those facts. A pointer proved to identify an interior member, ended
allocation, stack/global place, or allocation with a competing disposer remains
known-invalid.

Transfer of scheduled or managed ownership into an ordinary raw destination
deliberately opens the allocation. It likewise requires another proved
disposition owner or narrow unsafe responsibility.

Reallocation through an open-ended raw destination does not reset its old
allocation:

```zax
open : MyValue * = @<
open = @<{ anotherArena }
```

This is safe when analysis proves `open` is vacant or its prior allocation
was dispositioned. A proved live overwrite is a known resource loss and is
rejected. If opaque code may have reset or transferred the prior allocation,
narrow unsafe responsibility may assert that missing fact. Intent
acknowledgement cannot supply lifecycle proof.

Raw `vacate` follows the same distinction. Plain use requires proof that
discarding the address loses no required disposition authority. `unsafe vacate`
may assert an opaque but potentially valid external relationship, but it cannot
legalize a proved last usable address to a live allocation. Managed pointers
reject `vacate` even under `unsafe` because bypassing their release guarantees a
leak or corrupts ownership accounting.

### Opaque owner and observer recovery

`OpaqueOwner`, `OpaqueObserver`, and `OpaqueReferenceObserver` preserve a private
exact type/capability witness while hiding typed access.

`value is type MyType` is a safe nonexecuting proof operation. It transfers
nothing, creates no access path, and does not prove liveness. Safe recovery still
checks ownership role, origin, and qualification authority:

- owner or pointer recovery can report failure through a vacant typed pointer;
- reference-observer recovery panics on mismatch because a reference cannot be
  vacant; and
- a successful preceding `is type` test supplies the exact type fact to its
  proven flow path.

`unsafe transfer (opaqueOwner as last)` is a defined unsafe assertion that
bypasses the type-witness check while performing an ownership transition. It
cannot recover an erased interior target from allocation-root ownership, regain
lost qualifications, or turn a nonowner into an owner. A known mismatch remains
an error; a false opaque assertion has undefined consequences.

Complete domain behavior belongs to
[Zax pointers, allocation, and arenas](pointers-and-arenas.md#type-erased-ownership-and-observation).

### Array bounds, slices, and disabled checks

When the compiler can prove that an intrinsic array operation is invalid—for
example, indexing a fixed five-element array at index ten—it rejects the source.
When the deciding index, length, or source count is known only while running,
the required operation checks it and panics on failure.

The array failures are kept separate: element bounds, slice bounds/order,
requested length, initializer element count, invalidated slice, foreign slice
origin, broken iterable count promise, storage-size overflow, and required
backing allocation.

A programmer may independently disable one registered failure category. That
choice tells the compiler to assume the failure cannot happen and permits it to
remove the corresponding check. If the promise is false, behavior is undefined.

Optional/reporting array operations retain the checks required to produce their
declared results even when the corresponding required-operation panic is
disabled. A slice validity check may carry origin and generation cost; disabling
it transfers responsibility rather than turning an invalid slice into an empty
one.

Array and storage providers may cooperate through future stability
kinds/tokens and observed versions. The provider reports changed raw mappings;
the array determines which element-place guarantees ended. A version mismatch
is the runtime evidence for the existing invalidated-slice panic, not a second
kind of slice failure.

Array and storage providers may cooperate through future stability
kinds/tokens and observed versions. The provider reports changed raw mappings;
the array determines which element-place guarantees ended. A version mismatch
is the runtime evidence for the existing invalidated-slice panic, not a second
kind of slice failure.

Complete array conditions and programmer-visible behavior belong to
[Zax arrays and slices](arrays-and-slices.md#required-reporting-and-unchecked-behavior).
Exact registry syntax and identifiers remain analysis-control work.

## Runtime failure and unsafe failure

Defined runtime failure is not the same as unsafe undefined behavior.

Examples of defined failure include:

- a failed shared-to-unique ownership claim producing an empty unique pointer;
- a failed weak acquisition producing an empty strong pointer;
- a non-panicking allocation producing a vacant pointer;
- a panicking allocation failing its arena request;
- a checked operation returning absence; and
- a required runtime check panicking.

A false unsafe assertion may instead have undefined consequences with no
required check.

Future contracts should state which failure model applies rather than leaving it
to compiler accident.

## Language contracts and compiler analysis

Source may eventually select a language-contract version through source,
project, build, or toolchain configuration. Omitting an explicit version accepts
the active environment's contract; it does not claim compatibility with every
past or future contract.

The selected contract defines portable required analysis.

The source-validity outcomes are:

| Proof status | Portable source consequence |
| --- | --- |
| Selected-contract-required proof succeeds | Use the safe unmarked form; a redundant `unsafe` assertion is an error, while a defined but suspicious alternate safe mode may require intent acknowledgement; every conforming compiler must recognize the proof |
| Selected contract does not require the proof | An unchecked operation still needs its narrow `unsafe` assertion; one compiler's stronger private proof may optimize or advise, but cannot make omission portable or reject the retained assertion |
| Explicitly selected stronger extension proof succeeds | That proof may become canonical, making the unmarked form required and redundant `unsafe` an error for that source |
| The asserted fact is proved false | Reject the operation even when it is marked `unsafe` |

Composition provenance is a concrete example:

```zax
proved : Container & =
  memberReference outer cast Container.member

deliberatelyTracked : Container & ? =
  intent<redundant-outer-tracking>{
    memberReference tracked outer cast Container.member
  }
```

If the selected contract requires and establishes exact-origin proof at this
site, plain `outer cast` produces the non-optional result without tracking, while
retaining an `unsafe` provenance assertion is an error. A deliberately retained
tracked operation requires `intent<redundant-outer-tracking>`, preserving its
optional result and potential tracking costs without forcing a physical metadata
read.

Without contract-required proof, plain `outer cast` is rejected. The programmer
may write the optional `tracked outer cast` directly or retain an `unsafe`
assertion for an unchecked non-optional result, even if one compiler privately
proves the origin. That private proof cannot require the intent category, change
the tracked result shape, or reject the portable unsafe assertion. An explicitly
selected stronger extension may make plain `outer cast` canonical and make
tracked use acknowledgement-required. A compiler-proved impossible origin
rejects the plain and unsafe forms; the tracked checked form instead produces
absence.

The composition owner defines the exact origin proof class and applies the same
distinction to callable result mapping: plain `via` for a proved non-optional
outer result, `tracked via` for a checked optional result, and `unsafe via` for
an unproved non-optional assertion.

### A compiler may prove more

Suppose source contains an unsafe assertion because an older compiler cannot
prove a valid member-lifetime relationship.

A newer compiler may prove it under the same language contract. The assertion
may then be unnecessary for that implementation's generated code, but it is at
most advisory when the contract did not require every compiler to recognize the
case.

Removing it may make the source depend on the newer compiler's stronger
analysis.

### A later or extension contract may require more

A later mainline contract, or an explicitly selected extension contract, may
require conforming compilers for that contract to recognize the case as safe.

Under that explicitly selected contract:

- the obsolete assertion may be a hard error;
- every conforming compiler must accept the unmarked valid operation; and
- a defined alternate safe mode may become acknowledgement-required when the
  contract proves it redundant; and
- a compiler that still demands the assertion is nonconforming.

This rule applies to every mandatory analysis, not only lifetime or optional
presence.

### Analysis provenance

Future controls need visible provenance:

- language-contract categories;
- compiler or vendor experimental categories; and
- community extensions implemented by multiple tools.

An unknown unsafe extension can be ignored only when the compiler independently
proves the source valid without it. Otherwise the compiler must report that the
required semantic extension is unsupported.

## Panic boundary

A panic blocks the operation that encountered its condition. A narrowly
applicable helper may repair that condition and allow the same operation to
complete as though the failure had not occurred. Otherwise the process crashes
gracefully.

Resolution does not:

- skip the failed operation;
- supply an unrelated substitute result;
- unwind completed members;
- roll back construction or replacement;
- continue after incomplete destruction; or
- expose partial lifecycle state to ordinary code.

This exact boundary applies the reusable
[Panic is not alternate control flow](principles.md#panic-is-not-alternate-control-flow)
principle. A concept owner links here when lack of unwinding changes local
construction, mapping, disposal, or replacement behavior rather than redefining
panic independently.

Panic categories can be enabled or disabled independently. Disabling a category
promises that its condition cannot occur, and lets the compiler omit the check.
If the condition occurs anyway, behavior is undefined. Other panic categories
remain enabled.

For allocation, this is not a third unchecked operator: `@` remains panicking
and `@!` must still detect request failure to return a vacant pointer.

### Controlling panic checks

A panic control is a [compiler directive](compiler-directives.md) naming one
category. Attached, `[<panic=no, ...>]` disables the check for one statement
or declaration, and `[<panic=yes, ...>]` enables it:

```zax
// Category names are illustrative; the registry is future work.
[<panic=no, fixed-arithmetic-overflow>]
total = total + delta   // no overflow check for this statement
```

As a [range](compiler-directives.md#directive-ranges), unattached state markers
set the check's state for the code that follows, inside one opening or file:
`never` disables it, `always` enables it, `inherit` restores the state the
opening started with, and `default` restores the language's default:

```zax
MyMixer :: type {

  [<panic=never, fixed-arithmetic-overflow>]

  accumulate final : ()() = {
  }

  [<panic=inherit, fixed-arithmetic-overflow>]

  audit final : ()() = {
  }

}
```

Here `accumulate` has no overflow check, and `audit` is back to the state
`MyMixer` started with. The closing brace restores the surrounding state in any
case.

Rules that keep these promises honest:

- **Name each category.** There is no "disable every check" form, so a category
  added to the language later is never disabled without someone naming it.
- **Imports are a wall.** An importer's panic state never reaches the modules it
  imports, so no module makes undefined-behavior promises about code it did not
  write.
- **Build configuration sets the starting state.** The build may set a module's
  starting state before its own source runs, and the module's directives refine
  it from there. A build that disables a check for a dependency is an explicit,
  auditable choice by whoever owns the build.
- **Not a build mode.** Whether a check exists never depends on debug or release
  mode, only on these controls.

Exact category names remain future analysis-control work.

## Diagnostics

Safety diagnostics should identify:

- the selected language contract;
- the exact required property;
- the source operation and affected place, value, pointer, or result;
- the available proof and the missing or contradictory fact;
- whether an assertion or permission can apply;
- the consequence of a false claim;
- and any runtime check or failure that the selected operation guarantees.

Diagnostics should distinguish:

- unproved valid operation;
- defined unsafe permission;
- known-invalid operation;
- defined but suspicious intent;
- advisory lint;
- unsupported experimental analysis; and
- compiler nonconformance with a selected contract.

Do not report a vague “unsafe” or “lifetime” error when the actual issue is an
ended member path, missing allocation root, possible callback escape, unknown
presence, duplicate disposition, or another precise fact.

## Costs

Static proof may increase compile time and diagnostic complexity while adding no
runtime state.

Runtime-checked policies may add:

- branches;
- generations or state flags;
- control blocks;
- synchronization;
- allocation;
- panic paths; or
- larger values.

Unsafe assertions can remove required runtime checks where the contract permits
that model, but they transfer correctness responsibility to the programmer.
They are not optimization hints detached from semantics.

## Source stability

Portable source validity follows the selected language contract.

Compiler improvements must not silently:

- reinterpret references or pointers;
- choose another transfer stance;
- add ownership;
- remove required synchronization;
- turn an unsafe permission into ordinary safe meaning;
- or change defined runtime failure into undefined behavior.

A new contract may deliberately strengthen mandatory proof or reject obsolete
controls. Such changes must be explicit and reproducible.

## Boundaries and maturity

This document establishes current conceptual safety direction. It is not a
formal safe-subset specification, proof calculus, diagnostic registry, or
compiler conformance suite.

Still deferred:

- exact unsafe syntax and category names;
- mandatory proof sets for each contract version;
- formal analysis provenance and extension rules;
- debug instrumentation;
- formal panic behavior;
- data-race guarantees;
- FFI and assembly trust boundaries;
- resource exhaustion, deadlock, and starvation;
- and broader security correctness.
