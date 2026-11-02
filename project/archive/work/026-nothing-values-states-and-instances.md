# 026: Nothing values, states, and instances

| Field | Value |
| --- | --- |
| Status | Historical working record / non-normative / audit-only / closed |
| Work Item | `026` |
| Created | 2026-09-18 |
| Closed | 2026-09-19 |
| Owns | The bounded review defined by the fixed initiating input |
| Does Not Own | Complete optional APIs, pointer ownership, general function invocation, global/`once` lifecycle ordering, variants/unions, compiler implementation, ABI, or runtime representation except where a concrete `Nothing` decision constrains them |

## Non-authority notice

This file is a collaborative working record. Existing statements, new
observations, candidate wording, and aligned findings remain non-authoritative
until a separately discussed, aligned, and explicitly authorized promotion
incorporates them into their lasting owners.

## Fixed initiating input

This section records the information aligned when work item `026` was created.
It is intentionally incomplete and must not be rewritten as work develops.

### Initiating concern

Establish a coherent programmer-facing model for what Zax means by `Nothing`.
Current and legacy material use the word around pointer vacancy, optional
absence, default function values, missing current instances during type calls,
real or custom instances, generated checks, and representation sentinels without
one reviewed conceptual owner establishing which uses are one concept and which
must remain distinct.

The maintainer-supplied
[Nothing maintainer notes](../raw/nothing-maintainer-notes.md) are the latest
primary input. Read and disposition their complete contents when this work item
is assigned. They may add to, replace, supersede, or reject older material.

The indexed [Nothing instances input](../raw/nothing-instances.md) preserves
global/custom instance, pointer, monitor, representation, and generated-check
pressure not yet given a lasting owner.

### Motivating pressure

Several current owners already rely on behavior called `Nothing`:

- a pointer can contain a vacant state;
- a default function value has a non-callable state;
- a type-qualified call to a `once` function supplies no receiver instance;
- an optional can be absent without constructing its boxed type;
- allocation reporting can produce pointer vacancy;
- source and diagnostics must distinguish absence from uninitialized storage,
  an ended lifetime, a missing result, and an unsupported operation; and
- future custom/global `Nothing` behavior may affect identity, storage,
  construction, lifetime, safety, and reflection.

Without a cohesive model, documentation can reuse one word while implying
different identity, value, lifetime, ownership, representation, or failure
semantics.

### Known starting evidence

- Optional absence is wrapper state and does not silently construct a boxed
  value.
- `unsafe ???` means initialization was bypassed; it is not an ordinary
  `Nothing` state.
- A reference always has a live referent and cannot contain pointer-style
  vacancy.
- Pointer presence, optional presence, and function callability currently have
  distinct domain contracts even where documentation uses similar language.
- A type-qualified `once` call has no receiver instance available through `_`;
  that fact must not manufacture an ordinary instance.
- Source, declaration, import, or analysis order does not silently choose among
  several possible meanings.

These are constraints supplied by current owners, not a conclusion that one
`Nothing` type or representation already exists.

### Known inclusions

- Whether `Nothing` names a type, value, state, instance, family, sentinel, or
  several deliberately related concepts.
- Pointer vacancy and its relationship to pointer identity, representation,
  presence, dereference, reset, ownership, and allocation failure.
- Optional absence and whether it shares any value/identity relationship with
  pointer or function vacancy.
- Default function values, callability checks, and panic behavior.
- The no-receiver state used by type-qualified `once` functions and
  type-receiver operations.
- Construction, default initialization, copying, assignment, destruction, and
  lifetime of any real `Nothing` value or instance.
- Global, language-provided, type-provided, or programmer-defined `Nothing`
  instances where evidence supports them.
- Qualification, transfer stance, reference/pointer behavior, structural
  compatibility, and identity boundaries.
- Presence/truth operations, overload selection, custom operations, and
  diagnostics.
- Storage size, valid representations, sentinel/tag behavior, source
  reflection, and runtime reflection pressure at programmer depth.
- Clear distinction from uninitialized storage, ended lifetimes, zero values,
  empty collections, absent results, and operations returning no results.
- Complete disposition of maintainer notes, indexed raw input, and reached
  legacy/current evidence.

### Known starting boundaries

- Redesigning complete optional construction, nesting, and transfer behavior.
- Redesigning pointer ownership families, allocation policy, or arena
  mechanics.
- Complete callable selection, function capture, async behavior, or invocation
  routing.
- Complete global/module/`once` initialization, concurrency, and teardown.
- General variants, unions, pattern matching, result/error families, or
  exception-like control flow.
- General user-defined generics, compile-time execution, and reflection APIs.
- Compiler data structures, pointer tagging algorithms, ABI, or lowering.

These boundaries do not erase consequences. Preserve every material pressure in
a current owner or indexed raw destination rather than leaving it only in the
eventual archive of this work item.

### Intentionally unresolved framing

- Whether one canonical `Nothing` identity exists.
- Whether vacancy is a value of the pointer/function type or a relationship to
  a separate `Nothing` value.
- Whether optional absence contains, references, or merely resembles
  `Nothing`.
- Whether `_` during a type call denotes a real language-provided instance, a
  statically recognized unavailable receiver, or another mechanism.
- Whether custom types may define their own `Nothing`, and what declaration or
  generated-operation authority that requires.
- Whether any `Nothing` instance has storage, address, lifetime, destructor,
  qualifications, or transfer behavior.
- Whether distinct vacancy domains share source spelling, presence operations,
  diagnostics, or representation.
- Which facts are conceptual language guarantees and which remain future
  representation/ABI choices.

### Initial stopping guidance

Stop when the work has:

- established a teachable common model and terminology;
- separated concepts that only share legacy wording;
- defined pointer, optional, function-value, and no-receiver consequences at
  programmer depth;
- defined any accepted real/custom/global `Nothing` value or instance behavior;
- distinguished `Nothing` from uninitialized, ended, zero, empty, and
  no-result states;
- reconciled source, construction, qualifier, transfer, lifetime, operator,
  invocation, safety, storage, diagnostics, and reflection pressure;
- assigned global/`once`, representation, variant, generic, async, and
  implementation consequences to current or indexed future owners;
- dispositioned all useful refreshed and reached legacy material; and
- completed the required documentation-fit dry run.

Do not promote findings, archive this work item, begin work item `027`, or
redesign complete optional, pointer, function, global-lifetime, variant,
generic, reflection, async, or implementation behavior without the separately
required discussion, alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  decision maturity, promotion, deferral, and closure.
- [Nothing maintainer notes](../raw/nothing-maintainer-notes.md) - latest
  maintainer-supplied primary input; read and disposition it completely after
  assignment.
- [Raw Nothing instances](../raw/nothing-instances.md) - preserves unresolved
  global/custom instance, pointer, monitor, representation, and generated-check
  pressure.
- Legacy `nothing.md` - principal legacy language evidence, read and
  dispositioned by value during this work, then removed after promotion.
- [Optional values](../../language/optional-values.md) - owns current absence,
  presence, boxed access, construction, reset, and transfer behavior.
- [Pointers, allocation, and arenas](../../language/pointers-and-arenas.md) -
  owns current pointer vacancy, presence, ownership, allocation failure, reset,
  and disposition.
- [Declarations and bindings](../../language/declarations-and-bindings.md) -
  owns default function values, type-callable `once` declarations, default
  initialization, and uninitialized storage distinctions.
- [Function invocation](../../language/function-invocation.md) - owns calling a
  vacant function value and type/instance `once` calls.
- [Construction, replacement, and destruction](../../language/construction-and-destruction.md) -
  owns lifecycle states and the distinction between storage, construction,
  absence, and destruction.
- [Lifetimes and references](../../language/lifetimes-and-references.md) - owns
  live instance/place requirements and why references cannot contain vacancy.
- [Operators](../../language/operators.md) and the
  [operator catalog](../../language/operator-catalog.md) - own presence,
  fallback, reset, call, and recognized source-form pressure.
- [Language-design terms](../../language/terms.md) - owns existing
  cross-cutting vocabulary to revise or extend.

### Consequence-driven

- [Identity types](../../language/identity-types.md) when a canonical or custom
  `Nothing` identity, admission, projection, or representation relationship
  becomes concrete.
- [Qualifiers](../../language/qualifiers.md) and
  [transfer stances](../../language/transfer-stances.md) when any real
  `Nothing` value, pointer/function vacancy, or no-receiver state receives
  qualification or transfer behavior.
- [Safety and analysis](../../language/safety-and-analysis.md) and
  [raw analysis controls](../raw/analysis-controls.md) when vacancy checks,
  disabled panic categories, unsafe assertions, or impossible states become
  concrete.
- [Execution context](../../language/execution-context.md) and
  [raw global and once lifetimes](../raw/global-and-once-lifetimes.md) when a
  language-provided/global `Nothing` instance or shared initialization becomes
  concrete.
- [Raw variants and unions](../raw/variants-and-unions.md) when `Nothing`
  becomes an active alternative, tag, or sentinel whose relationship to
  general variants must be preserved.
- [Raw reflection](../raw/reflection.md) when type/value/state metadata or source
  reflection becomes concrete.
- Relevant legacy pointer, function, type, allocation, or compiler-directive
  material only when a concrete question cannot be resolved from the primary
  notes, required legacy page, current owners, and focused raw input.

### Audit-only

- Archived work only when a concrete provenance or regression question cannot
  be answered from current owners and live raw input.

## Initial working posture

Begin with four programmer-visible examples: a vacant pointer, an absent
optional, a default function value, and a type-qualified `once` call. State what
the programmer may observe and do in each case before proposing that they share
one type, value, representation, or instance.

Use the maintainer notes as the latest primary evidence. Preserve current owner
constraints and treat legacy material as input rather than authority.

## Working record

### Aligned findings

The findings in this section are aligned for the current review scope. They
remain non-authoritative until separately authorized promotion incorporates
them into their lasting owners.

#### Start with the programmer-visible states

Zax has no universal `Nothing` type and no source-level value admitted into
unrelated types. The legacy concrete `Nothing` type is retired.

Several domains instead have distinct states:

```zax
pointer : MyValue * // vacant pointer
optional : MyValue? // absent optional wrapper
callback : ()()     // unavailable function value
```

- A vacant pointer semantically targets its pointee type's **Nothing
  instance**. `?pointer` is false.
- An absent optional is a live wrapper with no boxed lifetime. It does not
  contain or target a Nothing instance.
- A default function value is initialized but unavailable. `?callback` is
  false, and calling it diagnoses or panics through a signature-compatible
  failed-call behavior.
- A `once bound` function can be invoked with an instance receiver or through a
  **receiverless type call**.

These states may share `?`/`!` spelling without sharing identity, storage,
lifetime, representation, or domain behavior.

An optional pointer demonstrates the separation:

```zax
vacantPointer : MyValue *
presentPointer : MyValue * ? = vacantPointer

if ?presentPointer {
  if !presentPointer.
    reportPresentOptionalContainingVacantPointer()
}
```

The optional is present while its boxed pointer is vacant.

#### A Nothing instance is special, not an ordinary `T`

Each concrete type can have a compiler-provided Nothing instance used by vacant
pointers and receiverless type calls. It is a special type-directed entity, not
an ordinary `T` established by invoking a `T` constructor.

When demanded by reachable source, the compiler prepares the applicable
Nothing backing during global initialization. A type that never needs a
Nothing receiver or pointer target need not materialize backing. Exact
dependency order, concurrency, failure, and teardown belong to future global
and `once` lifecycle work.

Compiler preparation recursively establishes useful member representations:

- integer-like trivial members use their language default, such as `0`;
- a pointer member is vacant for its pointee type;
- an optional member is absent;
- a varying function member uses its unavailable panic behavior;
- a representation-trivial identity starts from its prepared underlying
  default;
- an identity with additional resident data follows the aggregate member rules;
  and
- a member without a valid prepared representation panics when accessed.

Arrays currently have no general prepared Nothing representation and therefore
panic when such access would require live array elements. Future array work may
define a narrower prepared form without changing the Nothing model.

The prepared members do not imply that the enclosing `T` is an ordinary
complete instance or that its constructors, cross-member invariants, or
destructor ran. A type's ordinary zero-input constructor is unrelated.

Compatible compiler-provided Nothing instances may share physical backing.
The language promises the type-directed behavior, not simultaneous ordinary
resident lifetimes at that address. An implementation may instead use tags,
dedicated sentinels, target-protected storage, or another representation.
Source behavior must not rely on address zero or a CPU trap.

#### Type-owned Nothing policies

Omission uses the effective compiler-provided default policy. A type may state
one of three explicit policies through the proposed `once` constructor family:

```zax
MyDefaultType :: type {
  +++ final once : ()() = default
}

MyTrappingType :: type {
  +++ final once : ()() = trap
}

MyCustomType :: type {
  value : Integer

  +++ final once : ()() = {
    _.value = 0
  }
}
```

- `= default` explicitly requests compiler-prepared readable Nothing behavior.
- `= trap` retains pointer vacancy and receiverless presence behavior but makes
  dereference, reference formation, or member access through the Nothing
  instance panic. Member-independent receiverless code remains usable.
- A body prepares one custom Nothing instance in dedicated type-owned storage.
  It may hold state and may be writable. Its synchronization remains the
  programmer's responsibility.

The `once` constructor takes no arguments, is not directly callable, and does
not create an ordinary zero-input constructor. An ordinary constructor does not
branch to discover whether it is constructing Nothing.

There is no direct source such as `MyType._` for obtaining the complete custom
Nothing instance. Deliberate valid access is through a receiverless type call.
A pointer that is unexpectedly vacant may mechanically reach compiler-provided
or custom Nothing backing when static analysis did not prove the misuse; that
containment behavior is not a direct custom-Nothing API.

#### Scoped Nothing-policy directive

The compiler directive:

```zax
[<nothing-instance-default=trap>]
```

changes the omission default for types declared in the attached module or
namespace opening. `default` restores compiler-prepared readable behavior.

The precedence is:

1. an explicit type `+++ final once` declaration;
2. the nearest attached namespace-opening default;
3. the module default; and
4. the language default, compiler-prepared readable Nothing.

The directive is lexically scoped to one namespace opening, not stored on the
namespace identity:

```zax
[<nothing-instance-default=trap>]
namespace Services {
  First :: type {
  }

  [<nothing-instance-default=default>]
  namespace Testing {
    Fake :: type {
    }
  }

  Second :: type {
  }
}
```

`First` and `Second` inherit `trap`; `Fake` inherits `default`. Leaving
`Testing` restores `Services`' setting.

Different openings of one namespace may use different defaults because the
effective policy is resolved separately for each declared type. Reopening a
namespace does not inherit a previous opening's local directive. Conflicting
values attached to one opening are errors; compatible repetition is redundant.
A module-level default is one module property, so conflicting module defaults
are errors.

This attachment model avoids source-order state that could silently change
unrelated later declarations after file reordering.

#### Receiverless type calls

One `once bound` declaration has two invocation routes:

```zax
MyCounter :: type {
  count : Integer

  inspect final once : (result : Integer)() = {
    if !_
      return _.count

    return _.count
  }
}

counter : MyCounter

fromType := MyCounter.inspect() // receiverless type call; ?_ is false
fromValue := counter.inspect()  // instance call; ?_ is true
```

On the receiverless route, `_` identifies the type's Nothing instance. On the
instance route, `_` identifies the evaluated receiver source.

Inside a `once` function:

- `?_` is the protected receiver-presence operation;
- `?_.` first dereferences `_` and then applies ordinary `?T`;
- `?_` in a non-`once` bound function is a non-acknowledgeable intent error;
- `_` is pointer-shaped;
- `_.` supplies the dereferenced form;
- mapping to an expected `T *` prefers `_`, while an expected `T` may use the
  automatic `_.` upgrade;
- `_` and `_.` may be passed or returned; and
- a `self` result may return the actual instance receiver or the receiverless
  route's Nothing receiver.

A receiverless type call has no source declaration stance and synthesizes a
`copy`-stanced Nothing receiver before callable selection:

```zax
MyResource :: type {
  inspect final once : ()() = {
  }

  consume final once : ()() last = {
  }
}

MyResource.inspect()             // receiverless copy uses unstanced fallback
(resource as last).consume()     // instance receiver supplies last
MyResource.consume()             // error: copy cannot select exact last
```

There is no runtime conversion from `last` to `copy`. The source form determines
the offered receiver stance statically. Aliases, forwarding, and future closed
callables must preserve which invocation route they represent.

An `unbound` function remains different: it has no receiver slot and `_` is
unavailable.

There is no separate `operator type` declaration or type-receiver category.
A type identity may anchor discovery of an `unbound` type-owned operator, where
`_` is unavailable. An operator uses ordinary `once bound` only when the same
declaration deliberately supports both type-qualified receiverless and
instance-qualified routes.

#### Function-value availability and reset

Recognized function presence returns exactly `Boolean`:

```zax
useCallback final : ()(callback : ()()) = {
  if ?callback
    callback()
}
```

A known fixed `final` function is statically present. Its `?` result is `true`
and its `!` result is `false`. A varying function value starts unavailable;
when not statically known, presence observes its runtime function-value state.

Calling a proved unavailable function is diagnosed. Otherwise the
signature-compatible unavailable implementation panics without returning
arbitrary results or relying on a null machine address.

`reset` returns a writable varying function slot to that state:

```zax
callback varying : ()() = makeCapturingCallback()

reset callback
// Owned callable representation and captures are released.
// !callback is now true.
```

Typed default assignment remains the explicit long form:

```zax
callback = (: ()())
```

`vacate` is not used for function values because a function value may own
capture state that must be dispositioned.

#### Pointer vacancy and ownership

A pointer is a live pointer value even while vacant. Vacancy means it
semantically targets its pointee type's Nothing instance rather than an
ordinary target.

Aligned pointer behavior:

- default pointer initialization is vacant;
- exact same-type vacant pointers compare equal;
- `?pointer` observes semantic presence, not numeric address;
- raw presence proves no pointee lifetime, provenance, alignment, or authority;
- a vacant managed pointer owns no target and does not count, retain, release,
  or destroy the Nothing instance;
- failed `@!`/`@!<`, weak acquisition, or ownership transition produces the
  destination pointer role's vacancy;
- safe type-aware conversion preserves vacancy by producing the destination
  role's Nothing representation; and
- view-shaped `unsafe cast` preserves raw representation and does not remap a
  source sentinel. A false claim has undefined consequences.

Safe pointer arithmetic that requires an ordinary target may need a
category-specific check and panic. Disabling that check asserts the target is
valid; actual arithmetic from Nothing then has undefined consequences.

`reset pointer` performs the pointer role's release, disposition, or observation
work and leaves the pointer vacant. It remains unavailable for a borrowed raw
pointer that has no disposition authority.

`vacate` performs the distinct value-only operation:

```zax
vacate borrowedRaw
unsafe vacate opaqueRaw
```

It discards one raw address and installs vacancy without disposition.

- Plain `vacate` is available when analysis proves a non-owning raw relationship
  and proves that discarding the address loses no required disposition
  authority.
- `unsafe vacate` is available only for a raw pointer whose potentially valid
  external disposition relationship is opaque.
- A proved last usable address to a live open-ended allocation, or a scheduled
  raw pointer still carrying its live disposition responsibility, is rejected
  even under `unsafe`.
- `unique`, `strong`, `weak`, anchored, and other self-managing pointers reject
  `vacate` even under `unsafe`; bypassing their release is a guaranteed leak or
  ownership-accounting violation.

Unsafe may accept an unproved but potentially valid raw relationship. It does
not legalize a guaranteed leak.

#### Dereference, references, reads, and writes

Postfix pointer dereference is unchecked:

```zax
pointer : MyValue *
view : MyValue & = pointer.
```

The source form is legal and does not ordinarily perform a vacancy check.
Static analysis diagnoses the operation when it proves the pointer vacant.
When runtime vacancy escaped that proof, the reference mechanically reaches the
type's Nothing backing. A reference has no independent vacancy state or
presence operation.

This narrows the current absolute statement that a reference can never target
Nothing. Ordinary references still do not contain a null-like representation
and never rebind; unchecked pointer dereference can bind one to special Nothing
backing.

Subsequent behavior follows the selected type policy:

- compiler-prepared readable members return their prepared values;
- a member without readable preparation panics;
- `= trap` panics at dereference/reference formation or member access;
- a custom Nothing instance executes its custom type-call behavior when reached;
  and
- a write to compiler-provided shared Nothing backing is invalid.

Writing compiler-provided Nothing has no mandatory runtime check because a
check on every ordinary store would impose unacceptable cost. The compiler
diagnoses it when analysis proves the target. Debug instrumentation or target
memory protection may detect additional cases; otherwise a missed write can
corrupt shared Nothing backing or have undefined consequences.

A custom Nothing instance may be writable during its intended receiverless type
call. Reaching it accidentally through a vacant pointer remains pointer misuse
even if its custom behavior contains the failure.

Compiler analysis is intentionally incomplete here. The language defines the
invalid condition; implementations may diagnose more certain instances as
analysis improves rather than limiting errors to one fixed proof algorithm.

#### Costs and representation

Programmers need to discover:

- whether a type uses readable, trapping, or custom Nothing behavior;
- whether a member path returns a prepared value, reaches a generated panic, or
  relies on target trap support;
- whether a presence fact was proved or merely unchecked;
- whether debug or target protection can detect an invalid write;
- whether a receiverless call requires custom global initialization;
- whether a function-value reset disposes captures;
- whether a raw pointer is reset or merely vacated; and
- whether a cast semantically remaps vacancy or preserves representation.

The language does not prescribe sentinel address, tag position, pointer width,
callable thunk, compatible backing overlap, CPU page protection, ABI, or
interoperability representation.

### Explicit deferrals

- **Global and `once` lifetime:** initialization dependency order, startup
  publication, any permitted lazy/concurrent first use, failure/retry,
  module-instance identity, teardown, and dynamic loading for custom Nothing
  storage remain in
  `project/raw/global-and-once-lifetimes.md`. Current work requires preparation
  before publication and no automatic process-global uniqueness.
- **Generics:** each concrete specialization may need distinct semantic Nothing
  behavior even when backing is physically shared. Specialization identity and
  generation remain in `project/raw/type-parameters-and-generics.md`.
- **Reflection:** metadata must distinguish compiler-default, trapping, custom,
  and inherited policy; written versus inherited directive source; runtime
  pointer/function presence; and physical representation from semantic state.
  API and syntax remain in `project/raw/reflection.md`.
- **Analysis controls:** exact diagnostic categories, panic registry entries,
  debug instrumentation controls, proved-versus-suspected findings,
  standard-versus-`x-` diagnostics, handoff-oriented Nothing-write
  responsibility, and unsafe syntax remain in
  `project/raw/analysis-controls.md`.
- **Callable captures:** concrete capture representation and reset cost remain
  in `project/raw/function-composition-and-chaining.md`; reset must disposition
  whatever representation is eventually accepted.
- **ABI and interoperability:** foreign nullability, sentinels, pointer/function
  layout, target trap capabilities, and adapter behavior remain in
  `project/raw/interop.md`.
- **Pointer representation and provenance:** exact tags, address rules,
  arithmetic, and cast mechanics remain in
  `project/raw/pointer-and-arena-mechanics.md`.
- **Arrays:** current Nothing access requiring array elements is invalid and
  traps or panics when target support permits; relying on unavailable hardware
  trapping is undefined. A future readable prepared-array form must preserve
  array element-lifetime rules.
- **Concurrency:** custom Nothing mutation is ordinary shared-state mutation
  with no intrinsic thread safety.
- **Built-ins and target profiles:** built-in identities and their trapping
  Nothing policy are canonical across modules. CPU read/write/execute trap
  capability and debugger-versus-trap reporting remain in
  `project/raw/cpu-provider-model.md`.

### Evidence disposition

The complete refreshed maintainer notes are dispositioned as follows:

- retire the concrete `Nothing` type and `null`-like source value;
- retain the per-type Nothing-instance concept;
- retain receiverless and instance-qualified `once` calls;
- retain prepared trivial/member values and panic-backed function members;
- retain compatible compiler-backing overlap without ordinary `T`
  construction;
- retain dedicated custom storage through `+++ final once`;
- retain custom mutability and thread-safety responsibility;
- retain pointer presence as semantic Nothing comparison without requiring
  address comparison;
- retain safe vacancy remapping and raw unsafe-cast preservation;
- supersede `#` reset candidates with `reset` and `vacate`; and
- route exact global ordering, compiler controls, reflection, and
  representation as listed above.

The legacy `nothing.md` material is dispositioned by value:

- `+++(: Nothing)` and the concrete `Nothing` type are superseded;
- returning a binary-compatible fallback pointer is rejected as an implicit
  cross-identity bridge;
- unguarded vacant-pointer use is not promoted as a Null Object API;
- custom Nothing behavior survives only as intended receiverless type-call
  behavior and failure containment for uncaught pointer misuse;
- undefined/null CPU-fault wording is superseded by prepared, panic, trap, and
  unchecked-write behavior;
- mutable global/thread-safety pressure is retained; and
- legacy `if !_` intent is retained as protected `?_` inside `once`.

The indexed raw Nothing-instance input is fully consumed:

- its default/custom split becomes compiler-default/trap/custom policy;
- monitoring becomes static diagnostics, selected panic behavior, optional
  debug instrumentation, and unchecked-write responsibility rather than one
  mandatory check;
- compatible overlap and representation freedom move to the current Nothing
  owner;
- pointer and optional distinctions move to current owners; and
- global lifetime, reflection, analysis controls, ABI, and pointer mechanics
  move to their existing indexed raw owners.

### Post-promotion maintainer refinements

The following refinements were aligned and applied directly after review of the
promoted teaching:

- delete the obsolete root `nothing.md` rather than retain a routing stub;
- teach identity defaults inside one containing type example instead of
  introducing a disconnected identity example;
- state that read/write trapping depends on target hardware and that Zax never
  emulates missing support with a check before every possible access;
- use hardware write protection for compiler-provided Nothing backing whenever
  available, independently of a readable-versus-trapping read policy;
- treat generated panic functions and debugger detection as useful but not
  equivalent to guaranteed hardware trapping;
- explain explicitly that `+++ final once` defines a custom Nothing because
  `+++` prepares the state and `once` selects its receiverless constructor;
- require custom preparation before publication without requiring delayed
  first-use construction; ordinary global startup is the expected common case;
- show `_` preferring a `T *` destination and automatically using `_.` for a
  `T` destination;
- clarify that vacancy describes the pointer's stored state, not a live
  ordinary pointee;
- replace dense pointer summaries with separate raw, owning, weak,
  non-panicking-allocation, and cast examples;
- permit Nothing instances for trivial types such as `U8`;
- require Nothing backing only when reachable observable behavior needs it and
  permit compiler elimination or sharing when access is proved impossible;
- make trapping the canonical built-in policy across modules rather than expose
  a per-module built-in directive;
- distinguish proved invalid access from high-confidence but unproved flow.
  The latter is an unsafe rather than intent concern, may belong at a pointer
  handoff rather than the eventual write, and uses the provisional
  `possible-nothing-write` and `possible-nothing-read-trap` catalog categories
  while future work defines exact triggers and standard-versus-`x-` diagnostic
  policy.

Target trap capability and uncertain-analysis control are preserved in the
CPU-provider, analysis-control, safety, reflection, generic, and global-lifetime
raw owners.

### Superseded initial reconstruction

The initial reconstruction below is retained as discussion history. Its
“fallback” terminology, proposed resident-instance split, open questions, and
write-check recommendation were superseded by the aligned findings above and
are not inputs to the dry-run result.

This reconstruction is candidate analysis, not an aligned finding. It starts
from the maintainer notes, tests them against the current conceptual owners, and
keeps disagreements visible.

The consequence-driven reading crossed five boundaries named by the reading
scope:

- identity, because aliases, distinct identities, and custom fallback instances
  cannot silently share one identity;
- qualifiers and transfer, because a real fallback instance would need place,
  value, access, and source-state behavior;
- safety, because debug-only monitoring cannot by itself support a safe
  programmer-facing access rule;
- global/`once` lifetime, because a custom fallback is proposed as persistent
  type-owned storage; and
- variants, arrays, and reflection, because tagged vacancy, overlapping storage,
  default member state, and observable state metadata create concrete pressure
  there.

No conclusion below promotes those consequence-driven concerns into their
owners.

### Review entry point

#### Candidate programmer model

The strongest current candidate is that Zax has no universal `Nothing` type and
no one value that inhabits unrelated types. Instead, several domains have their
own states:

- a pointer may be **vacant**;
- an optional wrapper may be **absent**;
- a replaceable function value may have a **default unavailable** state; and
- a type-qualified `once bound` call has **no source receiver instance**.

These states can share presence spelling and diagnostic vocabulary without
sharing value identity, lifetime, storage, representation, or operations.
`?value` asks the selected domain's presence question; it does not compare every
domain with one hidden universal value.

The refreshed notes add a narrower mechanism that still needs a settled name:
a type may have a fallback target used when pointer or type-qualified receiver
behavior needs somewhere to route. A programmer-provided fallback could be a
real, persistent instance of that type. A compiler-provided fallback cannot yet
be called an ordinary resident instance without contradicting current
construction and lifetime rules.

Candidate terminology for discussion:

- use **vacant pointer** for a pointer whose `?` result is false;
- retain **absent optional** for a wrapper with no boxed lifetime;
- use **default unavailable function value** for the initialized function state
  that diagnoses or panics when called;
- use **receiverless type call** or **unavailable receiver state** for a type
  call before deciding whether `_` reaches storage;
- use **fallback receiver** for the special member-call route; and
- reserve **custom fallback instance** for a real programmer-constructed global
  resident instance.

This vocabulary deliberately avoids deciding that all of those are “Nothing
instances.” The spelling and final public terms remain open.

#### Most important contradiction

The maintainer notes say the compiler-provided fallback:

- is a legitimate instance of `T`;
- may let trivial fields be read at their default values;
- may contain default panic function values;
- must not ordinarily be written;
- may overlap fallback storage for several types; and
- may omit required checks outside debugging builds.

The current lifecycle and safety owners say:

- a resident instance exists only after complete construction;
- ordinary access cannot manufacture a resident instance where none exists;
- every live member needs a valid lifetime and representation;
- shared address and bytes do not establish simultaneous typed resident
  instances;
- safe runtime-dependent validity needs a required check or static proof; and
- omitting a required check makes the programmer's responsibility explicit and
  unsafe rather than silently build-mode-dependent.

Arbitrary `T` may have no zero-input constructor, may contain members with no
default construction, may enforce cross-member invariants, or may require
destruction. Zero-filled bytes therefore cannot generally be a legitimate
`T`. An array member likewise has live elements only when its required elements
were constructed; current array behavior does not make zeroed backing storage a
constructed array.

Two coherent directions are visible:

1. **Ordinary resident direction.** Every fallback is a completely constructed
   `T` with an ordinary life path. This requires a viable construction rule,
   ordinary member invariants, destruction and synchronization behavior, and
   distinct identity-preserving storage. Storage overlap is then unavailable
   unless another reviewed alias or overlay mechanism proves that the same
   resident instance satisfies both identities.
2. **Special fallback direction.** A compiler-provided fallback is not an
   ordinary `T` resident instance. It is a language-recognized receiver/pointer
   state with type-directed access behavior. Its physical storage may overlap
   because those bytes do not establish ordinary typed lifetimes. A
   programmer-provided custom fallback is different: it is a real `T` instance
   with ordinary construction and lifetime.

The second direction currently composes better with the existing lifecycle
model and preserves the optimization freedom sought by the notes. Its cost is
that “nothing instance” cannot mean one uniform thing, and every operation that
appears to expose the default fallback as an ordinary `T`, `T &`, or `T *` must
be specified or rejected. Maintainer review should begin here.

#### Four baseline examples

These examples separate current observations from decisions still needed.

##### Vacant pointer

```zax
pointer : MyValue *

if ?pointer
  use(pointer.)

reset pointer
```

Current owners establish that default initialization and `reset` leave
`pointer` in its pointer-domain `Nothing` state and that `?pointer` is false.
For a raw pointer, presence proves only non-vacancy; lifetime, provenance,
alignment, and access still need independent proof.

The candidate model calls this state **vacant** and treats any sentinel address,
tag, or synthesized target as representation. It does not make that
representation a source-level `Nothing` value.

Still undecided: whether `pointer.` on a possibly vacant pointer is unavailable
without proof, performs a required checked fallback, or may deliberately invoke
fallback behavior. Current pointer documentation explicitly defers this.

##### Absent optional

```zax
empty : MyValue?

vacantPointer : MyValue *
presentPointer : MyValue * ? = vacantPointer

if ?presentPointer {
  if !presentPointer.
    reportPresentOptionalContainingVacantPointer()
}
```

`empty` is a live absent wrapper with no boxed `MyValue` lifetime.
`presentPointer` is a present optional with one live boxed pointer value, and
that pointer is independently vacant. The outer `?` and inner `!` answer
different domain questions.

This rules out models in which optional absence contains or references the
pointer fallback target. It also rules out a universal `Nothing` value that
collapses the two states.

##### Default function value

```zax
callback : ()()

callback()
// statically diagnosed when the default state is proved;
// otherwise the invocation panics
```

The current function owners establish a valid initialized function value and a
defined failed-call boundary. The refreshed notes plausibly explain one
representation: the value may route to a signature-compatible panic
implementation instead of containing an invalid machine address. That is not a
promise that every function representation contains a thunk.

Calling the default state does not execute a no-op, invent results, perform
undefined control transfer, or rely on a CPU fault. A future opt-in no-op or
default-result function policy would be a distinct function-type contract.

Still undecided: whether function values receive recognized `?`/`!` presence,
how a function value is explicitly reset to its default state, and whether a
custom default implementation remains semantically unavailable or becomes an
ordinary callable value.

##### Type-qualified `once` call

```zax
MyCounter :: type {
  count : Integer

  inspect final once : (result : Integer)() = {
    if !_
      return 0

    return _.count
  }
}

counter : MyCounter

fromType := MyCounter.inspect() // no receiver expression; ?_ is false
fromValue := counter.inspect()  // counter is evaluated once; ?_ is true
```

Current declaration and invocation owners establish one callable and two call
forms. The type call performs lookup through `MyCounter`, evaluates no receiver
expression, and makes `_` report unavailable. The instance call supplies the
ordinary receiver.

The guard above is coherent without deciding that `_` is an ordinary
`MyCounter`. The harder unguarded form remains a decision:

```zax
readCount final once : (result : Integer)() = {
  return _.count
}

value := MyCounter.readCount()
// Refreshed-note candidate: return Integer's default value, 0.
// Current-owner pressure: no ordinary MyCounter resident instance was supplied.
```

Allowing the read requires either a real constructed fallback `MyCounter` or a
special receiver rule that can produce a valid `Integer` member value without
claiming that a complete ordinary `MyCounter` exists.

### Domain reconstruction

#### There is no useful universal `Nothing` identity

The refreshed notes explicitly retire the legacy `Nothing` type. Current
identity rules support that direction:

- a type identity determines which values and operations belong to the type;
- independently declared identities do not become related because their
  representations match;
- an alias preserves its canonical target identity rather than introducing a
  second fallback;
- admission or projection across an identity boundary must be declared; and
- representation equality does not create a value bridge.

A universal `Nothing` value would need implicit admission into pointers,
optionals, function values, receiver states, and possibly future variants while
preserving each domain's distinct behavior. That would add an exceptional
cross-identity conversion mechanism without providing a programmer-visible
value that needs to be passed around.

Candidate conclusion: retire both the concrete `Nothing` type and any model in
which source compares unrelated values with one canonical `Nothing` identity.
The capitalized name remains in current documentation only as terminology
awaiting this review, not evidence that a type exists.

#### Pointer vacancy is pointer state, not absence of a pointee value

A pointer instance is live even while vacant. Its pointer value then identifies
no ordinary owned or borrowed target. Candidate rules supported by current
owners and the refreshed notes:

- default pointer initialization produces vacancy;
- `reset pointer` performs the pointer role's release/disposition work and then
  produces vacancy;
- non-panicking allocation and conditional ownership transitions can report
  failure through destination-pointer vacancy;
- `?pointer` observes semantic pointer presence, not whether represented bits
  are numerically nonzero;
- raw-pointer presence does not prove a live pointee or provenance;
- a managed vacant pointer owns no target, keeps no allocation alive, and does
  not retain or destroy fallback storage;
- a custom fallback's useful behavior does not make failed allocation present;
  and
- pointer vacancy remains distinct from weak ownership that has terminated.

The pointer representation may be a dedicated address, a shared compatible
address, a tag, a reserved invalid address, or another form. Source semantics
must not depend on a CPU trapping at address zero.

Safe type-aware pointer conversion should preserve semantic vacancy by producing
the destination pointer role's vacancy. In contrast, the refreshed-note rule for
view-shaped `unsafe cast` is coherent: it preserves the source representation
without remapping a sentinel. If a vacant `Bar *` representation is not a valid
vacant `Foo *` representation, later use has the ordinary undefined consequences
of the false unsafe claim. Coincidental success remains permitted but
unreliable.

Open pointer questions:

- whether equality treats all vacant values of one exact pointer type as equal;
- whether any pointer arithmetic on vacancy is rejected, checked, or unsafe;
- whether member call, member read, member write, and general postfix
  dereference have different fallback behavior;
- whether taking a reference or pointer to fallback storage is ever permitted;
- whether a custom fallback can be deliberately obtained as an ordinary value
  rather than reached only through fallback routing;
- whether pointer roles need source-visible distinction between vacancy and a
  live pointer to a programmer-owned object that happens to implement neutral
  behavior; and
- how raw, unique, strong, weak, and anchored representations identify vacancy
  without adding ownership to a sentinel.

#### Optional absence is complete without `Nothing`

The optional owner already has a cohesive model:

- the wrapper is a live value;
- absence means no boxed lifetime exists;
- presence means exactly one boxed lifetime exists;
- default construction is absent;
- reset destroys a present box and leaves the same wrapper absent; and
- each optional layer is independent.

No `Nothing` value, fallback target, sentinel instance, or boxed default is
needed at the conceptual level. A layout niche may reuse a representation
available in the boxed type, but that does not merge semantic states. In
particular, optional pointer/function vacancy remains a present boxed state.

Candidate conclusion: optional documentation should eventually stop calling
pointer or function vacancy a sibling `Nothing` value and instead teach the
independent layers positively. Representation sharing remains a future layout
choice.

#### Default function state is defined behavior, not a null call

A replaceable function slot needs a valid initialized representation before a
programmer assigns an implementation. The refreshed notes sharpen the current
contract:

- a fixed `final` function has a known implementation and no replaceable slot;
- a varying function slot defaults to a signature-compatible failed-call
  behavior;
- a proved call of that state is diagnosed;
- an unproved call enters the ordinary panic boundary;
- the panic never manufactures results, returns past the blocked call, or relies
  on exception unwinding; and
- the representation may use a panic thunk, tag, or another mechanism.

Calling the state is therefore defined to fail even though the value may be
represented with callable machine code. “Contains a panic function” is an
implementation-permitted explanation, not enough by itself to decide semantic
presence or identity.

The following distinctions remain:

- a default-unavailable function value;
- a final function declaration with no per-instance storage;
- a varying function value after assignment;
- a future explicit no-op/default-result fallback policy; and
- an unbound function, which lacks a receiver but is not an unavailable
  function value.

#### Receiver absence belongs to invocation, not function-value vacancy

`once bound` and `unbound` solve different problems:

- an unbound declaration has no receiver slot and `_` is unavailable in its
  body;
- a `once bound` declaration has a receiver slot and one type-owned
  implementation; its type-qualified call supplies the unavailable receiver
  state, while its instance-qualified call supplies a normal receiver.

The type-qualified call does not prove that a hidden ordinary `T` value exists.
It only proves that the selected body can observe the no-source-receiver branch
through `?_`. A type-receiver operator has the same missing-instance pressure
even though a concrete type identity supplies discovery.

Candidate principle local to this concern: **type identity used for lookup is
not a runtime receiver instance**. If `_` reaches fallback storage, that is an
additional language mechanism whose lifetime, access, and cost must be taught.

Open receiver questions:

- whether `?_` is a protected receiver-state operation rather than ordinary
  `?T`;
- whether `_` in the unavailable state may be passed as a value argument;
- whether a reference or pointer result may return `_`;
- whether a `self` result can be satisfied on the type-qualified path;
- whether taking a member reference from `_` is legal;
- which receiver qualifications and transfer stance apply to default and custom
  fallback routes; and
- whether a member-independent `once` body pays any runtime receiver-state
  cost.

Until those are answered, saying that `_` is a legitimate ordinary `T` exposes
more behavior than the notes define.

### Default and custom fallback receivers

#### Candidate split

The evidence supports distinguishing two fallback kinds.

**Compiler-provided default fallback**

- exists semantically for every type that participates in fallback routing;
- reports absent through the applicable receiver or pointer presence operation;
- owns no programmer-visible ordinary `T` lifetime;
- may use shared or synthesized representation;
- may allow selected reads whose complete result can be defined without
  violating a `T` invariant;
- rejects or checks operations that require an ordinary live `T`; and
- cannot be retained, destroyed, mutated, or synchronized as a normal global
  instance.

**Programmer-provided custom fallback instance**

- is one real, completely constructed `T` resident instance;
- occupies persistent type-owned storage;
- receives ordinary declared behavior when routed as fallback;
- may hold state and may permit mutation;
- remains semantically absent for the pointer/receiver presence question;
- is not automatically thread-safe;
- must obey ordinary type invariants, qualification, construction, and
  destruction rules; and
- cannot share typed storage with a fallback of another identity merely because
  their layouts match.

This split keeps the useful Null-Object-like customization without pretending
that arbitrary compiler-synthesized bytes are an ordinary `T`.

#### Illustrative custom declaration

The maintainer notes propose using `once` on a constructor because the
combination otherwise has no established meaning:

```zax
MyCounter :: type {
  count : Integer

  +++ final : ()() = {
    // Ordinary zero-input construction.
  }

  // Illustrative candidate syntax only.
  +++ final once : ()() = {
    _.count = 0
    // Construct the type-owned custom fallback instance.
  }
}
```

Candidate interpretation:

- `+++ final once` requests one custom fallback instance and constructs it;
- it does not add another ordinary zero-input constructor;
- it receives no constructor arguments;
- ordinary constructors do not need to branch on whether they are constructing
  fallback storage;
- type-qualified `once bound` calls and applicable vacant-pointer member routes
  use this instance;
- because it is a complete custom instance, member reads and writes follow its
  ordinary qualifications rather than default-fallback monitoring; and
- its persistent lifetime and publication must complete before any route can
  reach it.

The exact modifier ordering and even reuse of `once` remain proposals. The
source does not decide:

- one instance per canonical type identity, module instance, generic
  specialization, active execution environment, or process;
- eager versus lazy construction;
- dependency order;
- concurrent first access;
- construction panic, retry, or permanent failure;
- teardown order;
- dynamic-module behavior; or
- whether a custom fallback may be replaced.

Those lifecycle questions belong with indexed global/`once` work. This item must
still constrain them enough to avoid promising process-global identity when
generative modules or specializations require distinct state.

#### The write-check conflict must be resolved, not hidden in tooling

The refreshed notes propose that writing a compiler-provided default fallback is
a contract violation, optionally caught by debug checking, with no normal
runtime check required.

That rule is not coherent with the current safe-subset model when ordinary safe
source can reach the write dynamically:

```zax
pointer : MyCounter * = choosePointer()
pointer.count = 1
```

If `pointer` may be vacant, one of these must be true:

1. static proof of non-vacancy is required before the write;
2. ordinary access performs a required runtime check and panics on vacancy;
3. the selected type declares a custom writable fallback, making the write
   defined; or
4. source explicitly disables/asserts the check under narrow unsafe
   responsibility, with undefined consequences when false.

A build-mode-only diagnostic may supplement these rules but cannot choose among
them. Otherwise identical safe source changes from defined to undefined merely
because debugging instrumentation was omitted.

Candidate recommendation: default-fallback writes require proof or a
language-required check; disabling that check is an explicit unsafe contract.
Custom fallback writes follow the custom instance's ordinary qualification and
thread-safety contracts. This deliberately disagrees with the unchecked normal
runtime wording in the maintainer notes and needs direct maintainer review.

#### Trivial reads are narrower than zero-filled aggregate access

The notes' useful intent appears to be that fallback code can obtain neutral,
well-defined behavior without repeated guards:

```zax
readCount final once : (result : Integer)() = {
  return _.count
}
```

Returning `0` for a default `Integer` field may be coherent. Generalizing from
that example to arbitrary member reads is not:

- a member may lack default construction;
- a valid value may not have an all-zero representation;
- a function member's default value has failed-call behavior rather than
  arbitrary zero bits;
- an array has element lifetimes and may require element constructors;
- a pointer member's semantic vacancy may not use zero bits;
- an identity can reject an otherwise valid underlying representation; and
- a cross-member invariant may make individually default-looking fields invalid
  as one complete `T`.

Possible refinements:

- permit a member read only when that member type has a language-defined
  fallback projection independent of a containing `T` lifetime;
- synthesize the member's ordinary type-default value for the read, including
  its normal construction/destruction cost;
- require a complete custom fallback for every unguarded member access; or
- make default-fallback member access panic and reserve neutral behavior for
  explicit custom fallbacks.

The first option most closely preserves the notes but introduces a new
operation-generation contract that must define result lifetime and cost. The
last option is simplest and most consistent with current lifetimes but gives up
the requested trivial-read convenience. No option is yet aligned.

### Operation and source consequences

#### Presence and fallback are separate properties

A custom fallback may perform useful work and still be absent for `?`:

```zax
if !pointer
  reportVacant()

pointer.recordMiss()
// Candidate: may route to a custom fallback that records the miss.
```

This is coherent only if documentation states that presence answers “does this
pointer denote an ordinary target?” rather than “will every operation fail?” A
Null-Object-like fallback does not turn vacancy into ownership or allocation
success.

The same spelling may have different domain contracts:

- `?optional` means a boxed lifetime exists;
- `?pointer` means the pointer role is non-vacant, with role-specific strength;
- `?_` means an ordinary receiver was supplied, if that form is accepted; and
- future `?functionValue` would mean callable availability only if separately
  established.

Complement fallback between `?` and `!` can reuse the operator mechanism without
making these predicates one semantic type.

#### Reset already has a current source form

The maintainer notes consider `#`, a `nothing` literal, typed default assignment,
and a phrase. Current owners have since established:

```zax
reset pointer
pointer = (: MyValue *)
```

`reset pointer` performs role-specific release or disposition and then leaves
vacancy. Typed default assignment invokes ordinary destination behavior; for an
open-ended raw pointer it may discard only the address rather than disposition
the allocation.

Candidate disposition:

- retain protected `reset` as the positive lifecycle operation;
- retain typed default pointer construction where an ordinary default value is
  needed;
- do not overload `#`, whose current roles concern discard and bit operations;
- do not add `null`;
- do not add a universal `nothing` value merely as reset syntax; and
- reject the legacy `MyType._` exposure unless a separately reviewed fallback
  access facility needs it.

#### Qualification and transfer

Pointer-layer qualifications and ownership roles belong to the pointer value,
not to any fallback target. Reaching an ordinary pointee uses the pointee's
qualifications. A custom fallback must likewise expose exactly the
qualifications permitted by its declaration and route.

A compiler-provided fallback has no ordinary declaration from which to inherit
`mutable`, `immutable`, `writable`, `readonly`, `final`, or `varying`.
Assigning it ordinary `T` qualifications would imply an ordinary `T` place.
The special-fallback direction instead needs operation-specific authority:

- presence testing observes state;
- member-independent calls need no member authority;
- defined neutral reads return their declared result values;
- prohibited reads/writes diagnose or check;
- no operation gains replacement authority over shared fallback storage; and
- unsafe check removal grants only the stated access responsibility.

Transfer stance creates similar pressure. Passing `_` or a dereferenced vacant
pointer as `copy`, `deep`, `move`, or `last` would suggest a complete source
value and post-state. Candidate default: special fallback receivers do not
participate as ordinary transferable `T` values. A custom fallback instance can
participate only through a route whose ordinary lifetime and alias rules make
that transfer coherent; `move` or `last` must not consume shared persistent
fallback identity accidentally.

#### References and escape

Current references always bind to one live place and cannot contain vacancy.
Therefore general fallback dereference cannot silently return `T &`:

```zax
pointer : MyValue *
view : MyValue & = pointer.
```

If `pointer` is vacant at runtime:

- a default special fallback cannot satisfy the reference result without
  manufacturing an ordinary `MyValue` place;
- a custom real fallback could supply a live place, but then `view` no longer
  carries the pointer's semantic vacancy and `?view` is not meaningful; and
- allowing one source form to have those two radically different reference
  meanings according to whether a custom fallback exists would make APIs
  unstable.

Candidate direction: require non-vacancy proof for general postfix pointer
dereference and reference formation. Any fallback invocation facility should be
a member-call/member-access contract that does not expose an unrestricted
ordinary reference. Custom fallback access, if desired, should have an explicit
source and type contract.

The same restriction should be tested for `_` in a type-qualified `once` body.
A `self` reference result cannot simply return an unavailable receiver. A custom
fallback may make a real reference mechanically possible, but that would expose
persistent global identity and must be stated rather than inferred.

#### Construction and destruction

The retired legacy `+++(: Nothing)` constructor is superseded by the maintainer
notes and should not survive promotion. It incorrectly requires a concrete
`Nothing` type and makes an ordinary constructor inspect a magic argument.

If `+++ final once` or another dedicated declaration creates a custom fallback:

- it is a distinct lifecycle declaration, not an overload taking a magic value;
- it does not generate or suppress the ordinary zero-input constructor except
  by an explicitly defined declaration-conflict rule;
- its body must establish every member and invariant before publication;
- ordinary operations may not observe partial fallback construction;
- teardown must run its destructor exactly once if it is an ordinary resident
  instance; and
- construction failure, dependencies, retry, and concurrency must be delegated
  to the eventual global/`once` lifecycle contract.

A compiler-provided special fallback instead has no ordinary enclosing `T`
destructor. Any temporary value synthesized by a neutral read follows that
result type's own lifetime.

#### Storage, overlap, and identity

The language should promise semantic behavior, not a sentinel layout.

For special default fallbacks, implementations may:

- share bytes across compatible types;
- use no addressable bytes;
- use a tag in the pointer/function value;
- route member-independent functions without materializing a receiver; or
- choose target-specific representation.

Those choices are valid only because no ordinary typed `T` lifetime is promised
in that storage. Size, alignment, aliasing, and identity still constrain any
operation that exposes bytes or addresses.

For custom fallback instances, ordinary type identity and lifetime apply.
Distinct canonical types do not become overlapping live instances through
layout compatibility. A transparent alias reaches the same type-owned fallback;
a new identity needs its own declared behavior. Generic specializations and
generative module instances must not be merged merely because their layouts or
source inputs match.

Future pointer ABI may choose a stable sentinel or tagged representation, but
this work need only preserve:

- semantic presence independent of numeric address;
- safe conversion remaps vacancy when required;
- raw unsafe casting does not promise remapping;
- ownership never destroys a shared default fallback;
- custom state has an explicit lifetime owner; and
- reflection does not promote one implementation choice into canonical type
  identity.

### Diagnostics and costs

Representative diagnostics should distinguish:

- a legacy attempt to use `Nothing` as a type from an ordinary type-default
  expression;
- optional absence from pointer vacancy;
- a proved call of a default-unavailable function value;
- a type-qualified call to a non-`once` bound function;
- unguarded receiver-member access in a type-qualified `once` call;
- pointer dereference without non-vacancy proof;
- default-fallback access that requires a runtime check;
- a write to compiler-provided fallback storage;
- an attempt to form or escape a reference from an unavailable receiver;
- consuming transfer requested from persistent fallback state;
- custom fallback construction that leaves a member incomplete;
- a custom fallback initialization cycle or failed concurrent publication;
- an unsafe pointer cast whose represented fallback is not valid for the
  destination; and
- use of a build-only diagnostic as though it were a portable safety guarantee.

Programmers and tooling need to discover:

- whether an operation requires a presence check;
- whether a check is statically discharged, executed at runtime, or explicitly
  disabled under unsafe responsibility;
- whether fallback routing can invoke programmer code;
- whether a neutral value is stored or synthesized;
- whether a custom fallback introduces global construction, destruction, and
  synchronization;
- whether pointer/function vacancy needs a tag, sentinel, or wider
  representation on the selected target;
- whether a member-independent type call avoids receiver-state work; and
- whether a cast remaps semantic vacancy or preserves raw representation.

Exact diagnostic identifiers and representation costs remain future work.

### Evidence disposition candidates

These are proposed dispositions for review, not authorized owner changes.

#### Refreshed maintainer notes

- **Retire concrete `Nothing` type:** retain as the leading candidate.
- **Do not rely on CPU null faults:** retain as design pressure.
- **Default function value uses panic behavior:** retain semantically; do not
  require one thunk representation.
- **Type-qualified `once` can test `?_`:** retain, subject to exact protected
  receiver-state classification.
- **Trivial reads from default fallback:** unresolved; narrow the claim and
  decide how a valid result lifetime is produced.
- **Writes to default fallback are forbidden:** retain the restriction, but
  reject debug-only enforcement as sufficient for ordinary safe source.
- **Compatible default fallback overlap:** retain only if compiler-provided
  fallback storage is not claimed as simultaneous ordinary typed resident
  instances.
- **`final` functions and `once` values add no per-instance fallback storage:**
  retain.
- **Default function-valued members panic when called:** retain through the
  function-value contract.
- **Dedicated custom fallback storage:** retain as a candidate with ordinary
  global instance obligations.
- **`once` constructor marker:** retain as the leading source candidate, still
  illustrative.
- **Custom fallback permits mutation and ordinary behavior:** retain subject to
  qualifications, synchronization, and persistent transfer restrictions.
- **Pointer presence compares with the type's fallback:** retain only as
  semantic equivalence; do not require address comparison.
- **Safe casts preserve vacancy and unsafe casts preserve representation:**
  retain as the leading candidate.
- **`#` as pointer reset:** superseded by current protected `reset`.

#### Legacy `nothing.md`

- **`Nothing` constructor argument:** superseded by refreshed notes.
- **Constructor may return another compatible fallback pointer:** reject unless
  later identity and lifecycle review establishes an explicit bridge; binary
  compatibility alone is insufficient.
- **Fallback as useful Null Object:** preserve through custom fallback
  instances, not as the default behavior of arbitrary types.
- **Types without a custom fallback may have undefined calls:** superseded by
  current defined diagnostics/panic and safety boundaries.
- **Mutable singleton warning:** preserve as concrete synchronization pressure,
  not merely style advice.
- **Default pointer calls “do not crash or panic”:** reject as a universal rule;
  only a declared custom fallback can promise that behavior.
- **Direct unchecked mutation of default fallback fields:** reject for ordinary
  safe source.

#### Indexed raw Nothing-instance input

The raw file's default-monitored/custom split is substantially preserved by the
candidate special-default/real-custom split. Its overlap, pointer ownership,
generated-check, thread-safety, and reflection questions remain live. If this
work is promoted coherently, that raw file should retire after every unresolved
piece moves to the appropriate current or indexed future owner.

### Decisions needing maintainer review

The questions are ordered so the first answer constrains the rest.

1. **What is the compiler-provided fallback?** Is it an ordinary completely
   constructed `T`, a language-special fallback receiver that is not a resident
   `T`, or should types without a custom fallback have no target at all?
2. **What may unguarded default fallback access do?** Decide separately for
   member-independent calls, trivial/defaultable reads, nontrivial reads,
   writes, and general dereference/reference formation.
3. **Is runtime enforcement part of safe behavior?** If default-fallback access
   can be dynamically invalid, is the check required unless explicitly disabled
   under unsafe responsibility?
4. **What does a custom fallback establish?** Confirm whether it is a real
   persistent `T`, whether `+++ final once` is the desired declaration shape,
   and whether it may be mutable.
5. **Which routes use the custom instance?** Type-qualified `once` calls, vacant
   pointer member calls, member reads/writes, function-value defaults, and
   explicit source access need separate answers.
6. **Can fallback state escape?** Decide passing `_`, returning `self`, taking
   references/pointers, and transferring `copy`/`move`/`last`.
7. **Which domains expose presence/reset?** Pointer and optional behavior is
   current; function presence/reset and exact `?_` classification remain open.
8. **What is source-visible terminology?** Decide whether `Nothing` remains a
   capitalized conceptual term after its type is retired, or whether domain
   terms replace it.

The next useful conversational step is to align the answer to question 1 before
expanding syntax or per-operation matrices. The rest cannot be made coherent
until “fallback instance” either satisfies ordinary resident-instance rules or
is explicitly separated from them.

### Captured adjacent consequences and deferrals

These findings are material but do not need full design before the first
fallback decision.

- **Global and `once` lifecycle:** a real custom fallback needs a live owner for
  initialization order, concurrent first use, failure/retry, module-instance
  identity, and teardown. The indexed global/`once` lifetime input is the live
  destination. Current work must not promise process-global uniqueness or lazy
  initialization.
- **Variants and unions:** a vacancy tag does not make pointer/function state a
  general managed variant, and overlapping fallback bytes do not establish
  union resident lifetimes. The indexed variants/unions input is the live
  destination if active-alternative or overlay rules become necessary. Current
  work must preserve semantic states without selecting a general variant
  syntax.
- **Reflection:** future metadata must distinguish static type capability from
  one runtime value's state, default versus custom fallback, declared versus
  generated behavior, receiverless type calls, and explicit source choices.
  The indexed reflection input remains the live destination. Current work must
  not reserve illustrative reflection syntax.
- **Generic specializations:** each concrete specialization may need distinct
  fallback behavior and storage. Future generics work owns specialization
  identity and generation; current work must not merge specializations by
  representation.
- **Async and concurrency:** custom fallback mutation introduces ordinary data
  race and affinity concerns. Future concurrency work owns synchronization and
  async access; current work must not imply global storage is thread-safe.
- **ABI and interoperability:** sentinel addresses, tags, pointer width, callable
  thunks, and target variation belong to future contracts. Current work should
  guarantee semantic vacancy and failure behavior only.
- **Arrays and other aggregates:** current array construction requires live
  elements and applicable constructors. Future fallback access must not infer
  that zeroed aggregate storage contains live elements.
- **Panic controls:** exact check categories and syntax remain indexed
  analysis-control work. Current work must classify each failure as required
  panic, reporting state, static rejection, or explicit unsafe responsibility.
- **Custom default function behavior:** no-op/default-result policies remain
  future function work. Current work must preserve the default panic contract
  unless a function type explicitly selects another policy.
- **Explicit fallback access:** source such as `MyType._` remains rejected as a
  candidate because it would expose identity, reference, mutation, and lifetime
  behavior all at once. A later concrete use case can reopen a narrowly typed
  facility.

## Dispositions and promotion dry run

### Result: PASS

The aligned findings have a coherent lasting owner structure, teachable public
entry point, complete local integration plan, live destinations for every
deferral, and an exact promotion set. No unresolved design contradiction blocks
promotion.

PASS does not authorize promotion.

### Structure proposal

Add one cohesive programmer-facing owner:

```text
language/nothing-instances.md
```

No new directory or secondary index is needed. `index.md` remains the public
router. The existing domain owners retain pointer, function, invocation,
lifecycle, reference, safety, operator, optional, identity, array, and namespace
rules at the depth programmers need while linking to the cohesive Nothing model.

The obsolete root `nothing.md` is removed after its useful evidence is consumed.
Git history preserves provenance; no compatibility router remains.

The two raw Nothing inputs likewise remain marked consumed through promotion
and retire during closure. This preserves live links from the immutable fixed
initiating input until that input becomes historical.

### Human-facing teaching plan

The new owner must be written for a cold reader rather than copied from this
working record.

Its opening sequence is:

1. Show a vacant pointer, absent optional, unavailable function value, and
   receiverless type call.
2. Explain in ordinary language that Zax has no universal `Nothing` type.
3. Name the per-type Nothing instance and show what `?` means in each domain.
4. Demonstrate compiler-prepared member reads and a panicking member.
5. Introduce explicit `= default`, `= trap`, and custom `+++ final once`
   policies.
6. Teach receiverless type calls and their `copy` stance.
7. Explain unchecked pointer dereference, references, invalid writes, and
   diagnostics.
8. Finish with representation freedom, costs, advanced ownership behavior, and
   deferred ABI/global-lifetime details.

The document leads with valid ordinary forms. Invalid forms appear only where
they teach a likely misunderstanding: a legacy `Nothing` type, proved vacant
dereference, managed `vacate`, direct custom-instance access, and type-calling
an exact `last` receiver.

Tables may summarize policy differences only after examples establish the
mental model. Discovery chronology, the rejected “fallback” vocabulary, and
the initial resident-instance contradiction do not enter promoted teaching.

### Lasting ownership map

- `language/nothing-instances.md` owns the cohesive model: no universal
  `Nothing` type; per-type Nothing instances; compiler-default, trapping, and
  custom policies; compiler-prepared members; receiverless presence; shared
  backing freedom; intended access boundaries; policy costs; and the scoped
  directive's semantic effect.
- `language/pointers-and-arenas.md` owns pointer vacancy creation, presence by
  pointer role, same-type vacant equality, ownership/counting consequences,
  `reset`, raw-only `vacate`, allocation/ownership failure, casts, and pointer
  diagnostics.
- `language/declarations-and-bindings.md` owns default function values,
  `+++ final once` declaration integration, type-callable `once`, and
  declaration-facing directive/default interaction.
- `language/function-invocation.md` owns receiverless versus instance-qualified
  call selection, synthesized `copy` receiver stance, `_`/`_.` mapping,
  `self`, function presence/call failure, and function reset at the invocation
  boundary.
- `language/construction-and-destruction.md` owns the distinction between
  compiler preparation and ordinary `T` construction, custom Nothing
  initialization completion, function/capture reset lifecycle, and member
  preparation integration.
- `language/lifetimes-and-references.md` owns unchecked reference formation from
  pointer dereference, fixed binding to Nothing backing, non-ownership, and
  escape/lifetime consequences.
- `language/safety-and-analysis.md` owns proved-misuse diagnostics, incomplete
  analysis, unchecked write consequences, debug instrumentation, trap-policy
  guarantees, and the boundary that unsafe cannot legalize a guaranteed leak.
- `language/operators.md` and `language/operator-catalog.md` own exact protected
  `?`, `!`, `reset`, and `vacate` forms, domains, fixity, results, and
  reservation.
- `language/operator-phrases.md` owns type-qualified phrase teaching and must
  use `unbound` or ordinary `once` rather than a separate receiver category.
- `language/transfer-stances.md` owns the receiverless `copy` offer and why
  persistent Nothing state cannot supply `last`.
- `language/qualifiers.md` owns application of declared receiver qualifiers to
  compiler and custom Nothing routes without another qualifier system.
- `language/optional-values.md` owns absence and the explicit distinction among
  absent optional, present vacant pointer, present unavailable function, and
  nested absence.
- `language/source-structure.md` owns `[<...>]` directive attachment syntax.
- `language/namespaces-and-modules.md` owns module defaults, namespace-opening
  lexical inheritance, reopening, duplicate/conflicting directives, and
  restoration after a nested opening.
- `language/identity-types.md` owns prepared underlying defaults for
  representation-trivial identities and aggregate treatment for identities
  with resident data.
- `language/integers.md` owns type-qualified associated-type wording without a
  distinct type-receiver declaration category.
- `language/enums.md` owns enum default participation and receiverless enum
  `once` examples.
- `language/arrays-and-slices.md` owns the current absence of a readable
  prepared array representation and the resulting panic boundary.
- `language/composition.md` owns why automatic exposure cannot merge
  receiverless and instance-qualified `once` routes.
- `language/intent-acknowledgements.md` owns terminology changes where current
  examples say a pointer remains at `Nothing`; `vacate` misuse is safety or
  lifecycle invalidity, not intent acknowledgement.
- `language/terms.md` owns concise definitions of Nothing instance, vacant
  pointer, receiverless type call, prepared Nothing representation, trapping
  Nothing policy, custom Nothing instance, and vacate.

### Deferred ownership map

- `project/raw/global-and-once-lifetimes.md`: demand preparation, dependency
  order, concurrent publication, failure/retry, module-instance identity,
  custom teardown, and dynamic loading.
- `project/raw/type-parameters-and-generics.md`: one semantic policy per concrete
  specialization, generated declarations, and deduplication that preserves
  specialization identity.
- `project/raw/partial-types.md`: partial authority for type-qualified
  `unbound`, `once final`, and `once varying` declarations.
- `project/raw/compile-time-execution.md`: execution timing for type-qualified
  `unbound` and `once` operations.
- `project/raw/reflection.md`: effective and written policy, directive
  provenance, runtime presence, custom/default/trap distinction, and semantic
  versus physical representation.
- `project/raw/analysis-controls.md`: exact diagnostic and panic categories,
  independently selectable checks, debug instrumentation, and unsafe control
  syntax.
- `project/raw/function-composition-and-chaining.md`: callable capture
  representation and the resources disposed by function reset.
- `project/raw/interop.md`: foreign nullability, pointer/function representation,
  ABI, and state adapters.
- `project/raw/pointer-and-arena-mechanics.md`: pointer tags/sentinels,
  provenance, arithmetic, safe remapping, raw cast representation, and target
  mechanics.
- `project/raw/callable-selection.md`: future callable contracts that promise
  pointer presence or preserve receiverless/instance route information.
- `project/raw/safety.md`: cross-feature panic/unchecked-policy integration.
- `project/raw/cross-cutting-audit.md`: removal of the now-completed Nothing
  review from the remaining legacy-contradiction entry.
- `project/raw/cpu-provider-model.md`: read/write/execution trap capabilities and
  distinction from debugger detection.

General variants/unions do not own Nothing. Shared backing creates no managed
alternative or ordinary overlapping resident lifetime, so no new variant raw
entry is required.

### Exact promotion change set

Add:

- `language/nothing-instances.md`

Modify current public owners and routing:

- `index.md`
- `language/namespaces-and-modules.md`
- `language/terms.md`
- `language/declarations-and-bindings.md`
- `language/construction-and-destruction.md`
- `language/pointers-and-arenas.md`
- `language/lifetimes-and-references.md`
- `language/qualifiers.md`
- `language/transfer-stances.md`
- `language/function-invocation.md`
- `language/operators.md`
- `language/operator-phrases.md`
- `language/operator-catalog.md`
- `language/optional-values.md`
- `language/safety-and-analysis.md`
- `language/identity-types.md`
- `language/integers.md`
- `language/enums.md`
- `language/arrays-and-slices.md`
- `language/composition.md`
- `language/intent-acknowledgements.md`

Consume or correct reached legacy material:

- `nothing.md` — delete after consuming its useful evidence.
- `functions.md` — update `once` routing and receiverless terminology.
- `casting.md` — remove superseded mandatory pointer-to-reference panic claims
  and route unchecked Nothing-reference behavior.
- `flow-control.md` — remove the legacy `+++(: Nothing)` value-polymorphism
  section and route protected `?_`.
- `basics.md` — remove the concrete `Nothing` type listing and update pointer
  vacancy terminology.
- `compiler-directives.md` — route legacy pointer-to-Nothing panic categories
  and directive spellings to current Nothing/source behavior and indexed
  analysis-control work.
- `meta-types.md` — replace current routing that treats type identities as a
  distinct receiver category.
- `meta-functions.md` — remove legacy/current `operator type` routing.

Update live deferred owners and raw routing:

- `project/raw/README.md`
- `project/raw/analysis-controls.md`
- `project/raw/callable-selection.md`
- `project/raw/cross-cutting-audit.md`
- `project/raw/function-composition-and-chaining.md`
- `project/raw/global-and-once-lifetimes.md`
- `project/raw/interop.md`
- `project/raw/pointer-and-arena-mechanics.md`
- `project/raw/reflection.md`
- `project/raw/safety.md`
- `project/raw/type-parameters-and-generics.md`
- `project/raw/partial-types.md`
- `project/raw/compile-time-execution.md`
- `project/raw/cpu-provider-model.md`
- `project/raw/nothing-maintainer-notes.md` — mark consumed pending closure.
- `project/raw/nothing-instances.md` — mark consumed pending closure.

Update the active work record during promotion to record applied disposition and
validation:

- `project/work/026-nothing-values-states-and-instances.md`

No change is required to `README.md`, `project/documentation.md`,
`language/source-structure.md`, `language/execution-context.md`, general
variant/union input, operating-prompt sources, or implementation repositories.

#### Promotion-time dry-run revision

Promotion validation found that the `language/operator-phrases.md` example was
not merely using weak Nothing terminology: it relied on the unsupported
`operator type` declaration and a separate type-receiver category.

The aligned correction removes `operator type`. A concrete type identity may
anchor type-owned discovery:

- use `unbound` when the operation has no runtime receiver and `_` is
  unavailable; or
- use ordinary `once bound` when one declaration deliberately supports both
  type-qualified receiverless and instance-qualified routes.

The ownership map and exact change set now include every reached current, raw,
and legacy surface carrying the old category. The maintainer explicitly
authorized this expansion during promotion. The dry run remains PASS.

### Promotion validation plan

Validate:

- the new owner teaches the model without requiring this work record;
- every local owner links only where its reader task crosses the Nothing
  boundary;
- every current use of capitalized `Nothing` means the per-type instance or is
  replaced by domain terminology;
- no current page implies a universal `Nothing` type or source value;
- `reset`, `vacate`, function presence/reset, and `?_` source forms agree across
  operator, declaration, invocation, pointer, and lifecycle owners;
- directive attachment, namespace-opening inheritance, module conflict, and
  explicit type override agree;
- pointer-reference examples clearly identify unchecked formation and trap
  behavior;
- custom Nothing is not presented as a pointer-facing Null Object API;
- all invalid examples carry inline `// error` comments;
- raw deferrals preserve representative source where syntax matters;
- current links and heading anchors resolve;
- Markdown renders coherently; and
- staged and unstaged review boundaries remain unchanged except for the
  explicitly authorized promotion files.

### Promotion application and validation

Promotion was explicitly authorized after the PASS result and applied on
2026-09-18.

The promoted documentation:

- adds `language/nothing-instances.md` as the cohesive cold-reader owner;
- teaches the four distinct states before introducing per-type Nothing policy;
- integrates pointer vacancy, unchecked dereference, raw `vacate`, function
  presence/reset, receiverless `once`, construction, safety, qualification,
  transfer, optional, identity, enum, array, composition, directive, and
  namespace consequences into their local owners;
- removes the unsupported `operator type`/type-receiver category in favor of
  type-qualified `unbound` or ordinary `once` declarations;
- consumes reached legacy material without copying its chronology into current
  teaching; and
- moves every unresolved global, generic, reflection, analysis, capture,
  interop, pointer-mechanics, callable-contract, compile-time, partial-authority,
  and safety consequence to a live indexed raw owner.

Validation result: **PASS**.

- `git diff --check` passes.
- The new owner and every newly added heading anchor are reachable from current
  links.
- No live current owner links to the legacy root `nothing.md`.
- Current source no longer declares or routes through `operator type`.
- Invalid promoted examples identify their error inline.
- The public router includes the new owner and removes Nothing from the legacy
  topic list.
- The consumed raw compatibility routes state their pending-closure
  disposition.
- Staged and unstaged review boundaries were preserved throughout promotion;
  current repository status remains the source of truth for that review state.

No aligned finding remains only in this working record. Promotion does not
close or archive the item.

### Closure and continuation

Work item `026` closed after:

- every aligned finding was promoted into its current owner;
- every remaining global, generic, reflection, analysis, callable, interop,
  pointer, CPU-profile, and safety consequence received a live indexed raw
  destination;
- the obsolete root `nothing.md` and fully consumed Nothing raw inputs were
  retired;
- public and project routers were updated; and
- validation passed across the complete promoted change set.

The next aligned concern is lambda definitions and callable composition. Work
item `027` receives the maintainer-supplied lambda notes as unread primary input,
the existing function-composition raw material, and current callable/receiver/
capture/Nothing constraints. Creating and routing `027` does not authorize its
analysis.

Historical links inside this archived work may name paths that existed during
the review. No current owner depends on those paths.
