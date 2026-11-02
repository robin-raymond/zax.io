# 015: Allocation operations and arena boundaries

| Field | Value |
| --- | --- |
| Status | Historical working record / non-normative / audit-only |
| Work Item | `015` |
| Created | 2026-09-06 |
| Completed | 2026-09-07 |
| Owns | Historical evidence of the bounded review defined by the fixed initiating input |
| Does Not Own | Current language meaning; arena API surface, arena registration or recognition, allocator implementation, complete pointer casts/provenance, collector graph algorithms, exact `Nothing` representation, variants/unions, complete arrays, async/concurrency, layout/ABI, or compiler lowering |

## Non-authority notice

This file is a historical collaborative working record retained for targeted
audit only. Its promoted findings are owned by current language documentation;
remaining future pressure is owned by live indexed raw input. Nothing here is a
current source of language meaning.

## Fixed initiating input

This section records the information aligned when work item `015` was created.
It is intentionally incomplete and must not be rewritten as work develops.

### Initiating concern

Develop a coherent human-developer-facing model and source contract for
allocation operations against already available arena instances:

1. requesting dynamic storage and then constructing a resident instance;
2. selecting an existing object arena;
3. optionally selecting an existing control-block arena;
4. constructing raw, blockless `unique`, `unique shareable`, or `strong`
   pointer results;
5. requesting inline or detached control-block storage where applicable;
6. selecting prompt or attached resident-instance destruction;
7. selecting prompt or deferred storage recovery;
8. selecting collectable metadata and explicit programmer-triggered cycle
   collection;
9. panicking, non-panicking, and unchecked allocation behavior;
10. automatic nested allocation during stack, global, contained-instance, and
    dynamic construction;
11. propagation and override of arena, disposition, and failure choices;
12. cleanup and exactly-once disposition after partial allocation or
    construction;
13. destination-directed ownership and control-block transitions;
14. programmer-visible costs, diagnostics, source stability, and reflection
    pressure; and
15. disposition of legacy allocation operators and policy words.

### Motivating pressure

Current design explains pointer ownership and arena-backed storage but does not
yet give programmers a settled source form for creating those allocations:

```zax
owner : MyValue * unique
shared : MyValue * strong
```

The language must explain how source requests storage, selects an arena and
allocation behavior, constructs `MyValue`, and establishes the requested
ownership.

Failure must also be predictable:

```text
panicking allocation
-> non-Nothing pointer or panic

non-panicking allocation
-> non-Nothing pointer or Nothing

unchecked allocation
-> non-Nothing pointer under programmer guarantee
-> undefined consequences when that guarantee is false
```

Automatic allocation inside another constructor applies the same selected
failure contract. A panicking form prevents normal construction completion. A
non-panicking form may leave a valid pointer member containing `Nothing`.

### Arena-interface deferral

This work presumes that arena instances already exist and that a future process
has made them recognizable as legitimate arenas.

It must **not** define:

- the API surface an arena type exposes;
- how an arena registers with the language;
- how the compiler or runtime recognizes a legitimate arena;
- the backing-storage protocol;
- custom arena implementation internals; or
- the library mechanism that creates standard or custom arenas.

Source in this work may select or refer to an existing arena. The arena's
existence, legitimacy, and language integration are supplied by an explicitly
deferred **future process**.

Current conclusions may constrain that future process—for example, an arena
must expose or carry sufficient capabilities for storage, alignment, failure,
concurrency, affinity, and teardown—but this item does not design the interface
that provides them.

### Known assumptions

- Every dynamic allocation receives storage from an arena.
- The ordinary process heap is an arena.
- Arena instances are presumed available and legitimate through the deferred
  future process.
- Allocation and construction are distinct: storage may exist without a
  resident instance.
- Pointer ownership and allocation disposition are independent.
- `unique` is blockless; `unique shareable`, `strong`, and `weak` use a control
  block.
- An object arena and control-block arena may differ.
- Destruction timing, storage recovery, and cycle probing are independent except
  that delayed destruction requires delayed recovery.
- Deferred recovery means prompt destruction with arena-delayed reuse; no
  allocation keyword spelling is accepted merely by naming the concept.
- Collectable support is emitted only when reachable build output uses it, and
  collection is programmer-triggered rather than an automatic memory-pressure
  pause.
- An arena is not inherently thread-safe. Its concurrency and affinity
  capabilities constrain allocation, release, and teardown.
- The language manages Zax lifecycle and ownership metadata over arena-provided
  storage.
- Raw-pointer operations may be safe when their required origin and lifetime
  facts are proved.
- Zax has no exception-style rollback model.
- There is no language implementation in this repository.

### Known inclusions

- Allocation expression and declaration mental model.
- Object-arena selection.
- Optional control-block-arena selection.
- Pointer ownership result selection.
- Control-block request and placement at the source-contract level.
- Destruction, recovery, and collection choices.
- Panicking, non-panicking, and unchecked allocation.
- Automatic allocation inside construction.
- Propagation and local override of allocation choices.
- Nested allocation ordering and failure.
- Interaction with constructor selection and construction packets.
- Partial allocation/construction cleanup and exactly-once disposition.
- Empty pointer results and the boundary to future `Nothing` design.
- Ownership transitions that require control-block allocation.
- Arena capability requirements visible to allocation source.
- Costs, diagnostics, unsafe responsibility, and source stability.
- Lasting documentation ownership and legacy disposition.

### Known starting boundaries

- Arena API methods, protocols, registration, discovery, and legitimacy checks.
- Standard-library or platform arena implementations.
- Allocator/compiler/runtime internal architecture.
- Complete raw-pointer casts, arithmetic, and provenance.
- Deeper or unsafe ownership anchoring.
- Collector root/edge algorithms and concurrent collection.
- Exact `Nothing` resident-instance representation and behavior.
- Managed variants and unmanaged unions.
- Complete dynamic-array and collection allocation contracts.
- Complete thread-affinity, executor, async suspension, and cancellation design.
- Generic allocation-policy syntax beyond immediate constraints.
- FFI allocator adoption.
- Formal layout, ABI, grammar, or lowering.

These boundaries do not erase consequences. Record each material pressure in a
live owner or indexed raw destination.

### Intentionally unresolved framing

- Exact source syntax for allocation.
- Whether allocation is symbolic, phrase-based, declaration-integrated, packet
  based, or a coordinated family of forms.
- How source distinguishes panicking, non-panicking, and unchecked allocation.
- Default object arena selection.
- Control-block arena selection.
- Inline versus detached control-block request.
- Whether ownership result is determined only by destination type or may also be
  stated at the allocation site.
- How automatic nested allocation inherits or overrides arena and disposition.
- Failure sequencing when several nested allocations have already succeeded.
- Whether a direct allocation first produces storage, a construction
  destination, a pointer result, or one complete coordinated operation.
- How collection eligibility is selected without implying automatic collection.
- How allocation-policy requirements participate in callable and generic
  compatibility.

### Initial stopping guidance

Stop when the work has:

- established a usable programmer model for allocation;
- established source forms for selecting existing arenas and allocation
  behavior;
- established raw, `unique`, `unique shareable`, and `strong` allocation
  results;
- established control-block allocation and placement requests;
- established panicking, non-panicking, and unchecked failure behavior;
- established automatic nested allocation, propagation, override, and cleanup;
- established constructor and allocation ordering;
- established visible arena capability requirements without defining arena APIs
  or registration;
- reconciled destination ownership, `Nothing`, safety, and transfer consequences
  at the depth required by allocation;
- dispositioned legacy allocation source and policy evidence;
- identified exact current owners and indexed deferrals; and
- completed the required documentation-fit dry run.

Do not design arena interfaces, promote findings, archive this work item, or
begin work item `016` without the separately required discussion, alignment, and
authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  numbered work, promotion, teaching, deferrals, and dry runs.
- Focused [pointer ownership](../../language/pointers-and-arenas.md#pointer-ownership-roles),
  [ownership transitions](../../language/pointers-and-arenas.md#ownership-transitions),
  [arenas](../../language/pointers-and-arenas.md#arenas),
  [allocation disposition](../../language/pointers-and-arenas.md#allocation-disposition),
  [allocation failure](../../language/pointers-and-arenas.md#allocation-failure),
  and [language-managed arena behavior](../../language/pointers-and-arenas.md#language-managed-arena-behavior) -
  supply the accepted ownership, storage, failure, and cost boundaries.
- Focused [construction mental model](../../language/construction-and-destruction.md#mental-model),
  [ordinary construction](../../language/construction-and-destruction.md#ordinary-construction),
  [construction packets](../../language/construction-and-destruction.md#construction-packets),
  and [panic and allocation boundaries](../../language/construction-and-destruction.md#panic-and-allocation-boundaries) -
  supply storage-versus-instance, ordering, completion, and partial-failure
  pressure.
- Focused [direct initialization](../../language/declarations-and-bindings.md#direct-initialization)
  and [declaration expressions as inputs](../../language/construction-and-destruction.md#declaration-expressions-as-inputs) -
  supply declaration and destination integration.
- Focused [pointer ownership transitions](../../language/transfer-stances.md#pointer-ownership-transitions) -
  supplies destination-directed `copy`/`move`/`last` behavior.
- Focused [runtime failure and unsafe failure](../../language/safety-and-analysis.md#runtime-failure-and-unsafe-failure)
  and [unsafe boundaries](../../language/safety-and-analysis.md#unsafe-is-narrow) -
  supply panicking, alternative-result, and unchecked responsibility.
- Focused [life paths](../../language/lifetimes-and-references.md#start-with-a-life-path)
  and [costs](../../language/lifetimes-and-references.md#costs-and-guarantees) -
  supply the lifetime foundation and non-owning boundary.
- [Raw pointer and arena mechanics](../raw/pointer-and-arena-mechanics.md) -
  preserves allocation syntax, control-block, failure, recovery, collection, and
  affinity pressure while respecting the arena-interface deferral.
- Focused legacy [context](../../context.md) - preserves allocator-selection and
  propagation evidence without making its old `@` syntax current.

### Consequence-driven

- Read [raw `Nothing` input](../raw/nothing-instances.md) when non-panicking
  allocation, automatic pointer-member failure, or pointer presence requires
  exact future constraints.
- Read [raw async input](../raw/async.md) when arena affinity, queued release,
  cross-thread ownership, or cancellation becomes concrete.
- Read current operator owners when a proposed allocation form uses symbolic,
  phrase, or mixfix syntax.
- Read [raw generic input](../raw/type-parameters-and-generics.md) when arena or
  allocation policy becomes a generic parameter or constraint.
- Read [raw interop input](../raw/interop.md) when external storage or foreign
  allocator adoption becomes unavoidable.
- Read [raw indexing and slicing input](../raw/indexing-and-slicing.md) only when
  bulk or variable-length allocation creates an immediate array-specific
  consequence.
- Read other allocation, build, reflection, or platform material only when a
  concrete allocation source or contract cannot be dispositioned coherently
  without it.

### Audit-only

- `project/archive/`, including work items `001` through `014`.
- Deleted legacy allocation and pointer pages recoverable through Git history.
- Superseded implementation sketches and compiler-internal explanations.

Do not read archived work item `014` during ordinary work on `015`. Its accepted
findings are promoted into current owners and its future pressures are preserved
in live raw inputs.

## Working record

This working record contains both aligned findings and explicitly identified
open questions. Aligned findings remain non-authoritative until separately
authorized promotion incorporates them into their lasting owners.

### Review entry point

The aligned model is one **declaration-bound coordinated allocation operation**:

```text
an explicit destination declaration supplies the pointee type and pointer role
-> select an existing arena and allocation behavior
-> reserve all required raw storage
-> construct one resident instance using ordinary construction
-> establish the requested pointer ownership
-> publish the pointer result
```

Allocation and construction remain distinct stages, but programmers request the
common operation through a declaration initializer. The operation owns
uncommitted storage until the resident instance and required ownership metadata
are complete. This avoids exposing an ordinary pointer to raw storage between
the two stages.

Every direct source allocation occurs in a declaration:

```zax
owner : MyValue * unique = @ [{ endpoint }]
empty : MyValue * unique

view : MyValue *
anotherView : MyValue * = view
// No allocation occurs here; the raw address is copied.

existing = @
// error: assignment does not establish an allocation destination

existing = (: MyValue * unique = @ [{ endpoint }]) as last
// A declaration expression supplies the allocation destination.
```

The destination type supplies both `MyValue` and the requested `unique` role.
`@` is not an independently usable expression and does not rely on expected
result context to invent its destination. The construction packet retains its
current meaning: it supplies constructor inputs and stored-member initializers
after storage has been obtained.

Panic does not create a recoverable partial-construction path. A panicking
operation blocks at the condition. A narrowly applicable panic helper may repair
that condition and allow the same operation to succeed as though it had not
failed. Otherwise the program crashes gracefully. Execution never skips the
failed operation and continues with inconsistent allocation, construction, or
destruction state, and Zax provides no exception-style unwind.

Each panic category can be enabled or disabled independently. Disabling an
allocation-failure panic through a future narrow safety contract promises that
the failure condition cannot occur and may permit the compiler to omit that
check. If the condition nevertheless occurs, behavior is undefined. This is a
general category-specific panic-control rule, not a third allocation mode.

A pointer type alone does not allocate its pointee:

```zax
Node :: type {
  next : Node * unique
  // next is Nothing after ordinary default initialization.

  allocatedNext : Node * unique = @
  // This declaration explicitly requests allocation and construction.
}
```

This explicit marker prevents pointer types from creating unbounded recursive
default allocation and keeps every direct allocation source-visible.

The aligned rules below distinguish declaration-attached raw allocation,
open-ended raw allocation, and managed ownership. Exact proof coverage may grow
with implementation experience, but the safe, unsafe-assertable, and
known-invalid semantic boundaries are established here.

### Evidence baseline

Current owners establish the following constraints:

- Allocation supplies storage; construction establishes a resident instance;
  storage may precede construction and outlast destruction.
- Every dynamic allocation uses an already legitimate arena. The ordinary
  process heap is one arena.
- Pointer ownership and allocation disposition are independent.
- `unique` is blockless. `unique shareable`, `strong`, and `weak` use a control
  block, although a newly allocated result cannot be `weak`.
- The object arena and detached control-block arena may differ and must each
  outlive every dependent object, block, and pointer.
- Destruction timing, storage recovery, and cycle probing are separate choices.
  Delayed destruction requires delayed recovery.
- Panicking allocation produces a non-`Nothing` pointer or enters panic.
  Non-panicking allocation produces a pointer to `Nothing` on exhaustion.
  There is no independent unchecked allocation mode.
- Direct initialization does not default-initialize and then assign. Allocation
  must therefore produce the declaration's initial pointer value directly.
- Construction-packet expressions evaluate and bind left to right. Automatic
  member construction then follows member declaration order, while explicit
  member construction runs where the constructor body reaches it.
- A raw pointer is not inherently unsafe, but it owns no life path. A dynamically
  created path reached only by a raw pointer needs a separate lifecycle owner or
  narrow unsafe responsibility.
- Pointer `Nothing` is not optional absence. A present optional may contain an
  empty pointer, and no physical sentinel representation is settled.
- Arena affinity, instance affinity, and atomic shared-lifetime accounting are
  separate. Pointer-layer `atomic` does not make the pointee or arena generally
  thread-safe.

Legacy [context input](../../context.md) and maintainer clarification establish:

- `___` is the replaceable context for one thread's complete execution path;
- the application has one complete context shape assembled from a core shape and
  permitted partial additions;
- every replacement context instance has that same resolved shape;
- core context behavior supplies an ordinary object arena and control-block
  arena, conceptually through `___.getDefaultArena()` and
  `___.getDefaultControlBlockArena()`;
- a site may override the selected allocator;
- thread-local allocation can be materially cheaper than synchronized
  allocation; and
- a missing allocator must not become silent success.

The exact context APIs, type declaration, partial-shape assembly, concurrency,
and async swapping remain future work. This item relies only on the semantic
rule that an allocation with no explicit arena reads the applicable default from
the current thread's `___`.

### Aligned programmer model

#### One operation, several explicit semantic slots

The allocation operation has these semantic inputs:

| Slot | Meaning |
| --- | --- |
| Resident type | The type whose storage and resident instance are created |
| Pointer result role | Raw, `unique`, `unique shareable`, or `strong`, normally supplied by the destination type |
| Object arena | The existing arena that supplies resident storage |
| Control-block placement | None, inline, or detached |
| Control-block arena | The existing arena used for a detached block |
| Destruction | Prompt or attached to arena lifespan |
| Recovery | Prompt or deferred until arena teardown/reset |
| Collection | Disabled or explicit-cycle-probing metadata enabled |
| Failure | Panic or return an empty pointer |
| Raw disposition attachment | Declaration-attached or open-ended |
| Construction packet | Ordinary constructor and stored-member inputs |

These are slots of one operation, not one pointer type per policy combination.
The pointer type continues to describe ownership. The operation records the
allocation-specific behavior needed when ownership closes or arena teardown
occurs.

The reserved token family carries failure and raw disposition attachment:

| Form | Allocation failure | Raw result |
| --- | --- | --- |
| `@` | Panic | Destination declaration owns automatic disposition |
| `@!` | Return `Nothing` | Destination declaration owns automatic disposition on success |
| `@<` | Panic | Open-ended; no automatic disposition owner is established |
| `@!<` | Return `Nothing` | Open-ended on success |

These are exact, indivisible reserved tokens under longest-symbol recognition,
not compositions of independently invoked `@`, `!`, and `<` operations. `@!`
reports storage-request failure through the pointer's ordinary `Nothing` state;
it does not mean that plain `@` omits a check. `@<` and `@!<` select an
open-ended raw-allocation relationship rather than an unchecked arena request.
They are unavailable or contradictory for managed destinations whose pointer
role already owns disposition.

Unchecked allocation is not a Zax allocation mode. A future safety contract may
disable one particular panic category while leaving other panic categories
enabled. Disabling the allocation-failure panic promises that the condition will
not occur, permits omission of that panic check, and makes an actual failure
undefined. It does not change `@!`, whose defined `Nothing` result requires
detecting request failure.

The `@{ ... }` enclosure carries arena and allocation-policy inputs. Its opening
brace must attach directly to the allocation token:

```zax
custom : MyValue * unique = @{ myArena } [{ endpoint }]
spaced : MyValue * unique = @ { myArena } // intent error
```

The space before `[{}]` is intentional because the construction packet is a
separate source component with its existing constructor-input meaning.

The enclosure permits at most one positional input, which is the object arena:

```zax
defaultPlacement : MyValue * unique shareable = @
customObjectArena : MyValue * unique shareable = @{ myArena }

detached : MyValue * unique shareable =
  @{ myArena, controlArena: myControlArena }
```

A second arena must be named because two positional arena-like operands are easy
to reverse and would make later policy evolution less stable:

```zax
invalid : MyValue * unique shareable =
  @{ myArena, myControlArena }
// non-acknowledgeable intent error: name controlArena
```

All inputs may instead be named. Ordinary Zax enclosure and multiline-layout
rules apply.

Empty policy enclosures are rejected in both compact and spaced forms:

```zax
emptyCompact : MyValue * unique = @{}
emptySpaced : MyValue * unique = @{ }
// non-acknowledgeable intent errors: use @
```

An **acknowledgement-required intent error** has one defined but suspicious
meaning and may use `intent<category>{...}`. A **non-acknowledgeable intent
error** is a recognized bad source form that must be rewritten; acknowledgement
cannot invent a valid form. For allocation:

- explicitly stating detached placement together with `controlArena:` is
  acknowledgeable redundant policy;
- an empty enclosure, two positional arenas, or a duplicate named slot is
  non-acknowledgeable; and
- contradictory values are semantic errors.

The ordinary defaults are:

| Slot | Default |
| --- | --- |
| Object arena | Current `___.getDefaultArena()` |
| Block placement for `unique shareable` and `strong` | Inline |
| Detached block arena when requested without an explicit arena | Current `___.getDefaultControlBlockArena()` |
| Disposition | Prompt destruction and prompt recovery |
| Collection | Disabled |
| Construction | Implicit zero-input construction |

The aligned policy value names are:

```text
AllocationDisposition.Prompt
AllocationDisposition.DeferredRecovery
AllocationDisposition.AttachedLifespan

AllocationCollection.Disabled
AllocationCollection.Collectable

ControlBlockPlacement.Inline
ControlBlockPlacement.Detached
```

The three disposition values encode all valid destruction/recovery
combinations. Collection remains independent because it combines with each
disposition. `Disposition.Collectable` is therefore not a suitable complete
policy name.

Named policy inputs are required after the optional positional object arena:

```zax
shared : MyValue * strong =
  @{
    arena: myObjectArena,
    controlArena: myControlArena,
    disposition: AllocationDisposition.DeferredRecovery,
    collection: AllocationCollection.Collectable
  } [{
    endpoint
  }]
```

Supplying `controlArena:` implies detached placement. An explicit `control:`
remains useful when detached placement should use the context's default
control-block arena. Stating both detached placement and `controlArena:` is
redundant and requires explicit intent acknowledgement; specifying inline
placement with `controlArena:` is contradictory.

#### Staged behavior

The aligned operation behaves conceptually as follows:

1. Resolve the resident type, destination ownership role, policies, and required
   arena capabilities.
2. Evaluate each written arena or policy operand once in visible source order.
3. Resolve omitted arenas from the current thread's `___`.
4. Request object storage.
5. Request detached control-block storage when needed.
6. For `@!` or `@!<`, release any earlier uncommitted reservation exactly once
   if either request fails, publish `Nothing`, and do not evaluate constructor
   inputs.
7. For `@` or `@<`, block at a failed request. A matching panic helper may repair
   the condition and allow that same request to succeed; otherwise the program
   crashes gracefully.
8. After all required storage exists, evaluate and bind construction-packet
   entries left to right under the existing packet rules.
9. Construct automatic members in declaration order, then run the selected
   constructor body and any explicit member construction it controls.
10. Initialize and connect required ownership or declaration-attachment metadata
    without exposing a half-established owning pointer.
11. Publish the complete pointer result only after the resident instance and its
    disposition contract are complete.

This is a programmer-visible semantic order, not a required compiler lowering.
An implementation may coalesce or elide work only when allocation calls, operand
evaluation, construction, destruction, ownership, and failure behavior remain
unchanged.

All storage requests deliberately occur before packet-expression evaluation. A
non-panicking failure to obtain required storage does not evaluate constructor
arguments and does not run the constructor. That rule prevents argument side
effects from occurring for an object that could never be constructed.

`@!` controls allocation-request failure only. It does not convert a panic from
a constructor, nested operation, or destructor into `Nothing`.

Capability rejection is not automatically arena exhaustion. A statically known
incompatible arena or illegal policy combination is a diagnostic. A dynamic
request failure may use the selected panic-or-`Nothing` contract. Affinity
violations, closed-arena use, and dependency violations need their own defined
diagnostics or panics rather than silently producing `Nothing` unless the future
arena contract explicitly classifies them as request failure.

#### Ownership results

| Result | Required metadata | Creation boundary |
| --- | --- | --- |
| `T *` with `@` or `@!` | No ownership block; declaration-attached disposition | The destination declaration's life path owns cleanup after successful allocation |
| `T *` with `@<` or `@!<` | No ownership block or attached disposition | Requires another proved owner/manual protocol or narrow unsafe responsibility |
| `T * unique` | Allocation-root release provenance, but no shared block | Publishes one unique owner |
| `T * unique shareable` | Dormant local/atomic-capable control block | Publishes one unique owner prepared for later sharing |
| `T * strong` | Active control block | Publishes the first strong owner |
| `T * weak` | Existing control block and live or terminated ownership relationship | Not a direct allocation result |

A declaration-attached raw allocation keeps the raw pointer role non-owning while
the declaration's life path independently owns disposition:

```zax
owner : MyValue * = @
alias : MyValue * = owner
```

`alias` receives only the raw address. It does not copy the cleanup schedule, and
lifetime analysis must keep its uses within the allocation path owned by
`owner`. Other safe raw cases may use:

- a containing type's declared lifecycle owns an automatically allocated child
  path;
- attached-lifespan allocation makes the arena own the resident path and lifetime
  analysis proves the arena remains available.

An open-ended raw allocation is not made safe merely by retaining its address.
The compiler may prove another complete disposition protocol; otherwise creating
or escaping that path requires narrow unsafe responsibility.

The destination declaration always supplies the pointer result role:

```zax
owner := @ // error: no explicit pointee or pointer role
```

`@` does not overload `as copy`, `as move`, or another established transfer
phrase to mean allocation. Stances matter later when an existing allocation and
its disposition authority move between declarations.

#### Declaration-attached and open-ended raw allocations

For a raw destination, `@` or `@!` attaches successful allocation disposition to
the destination declaration's life path:

```zax
scoped : MyValue * = @
maybeScoped : MyValue * = @!
```

The raw pointer value remains non-owning. The declaration independently owns the
responsibility to apply the allocation's selected disposition when its path ends
or when the slot replaces that allocation. Copying the address does not copy
that responsibility:

```zax
alias : MyValue * = scoped
```

`alias` is bounded by the scheduled allocation's life path. It cannot safely
escape or remain usable after `scoped` dispositions the allocation.

`@<` and `@!<` create open-ended raw allocations:

```zax
manual : MyValue * = @<
maybeManual : MyValue * = @!<
```

Retaining the address does not establish cleanup. These forms are safe only when
analysis proves another valid lifetime/disposition owner or a complete manual
protocol. Otherwise they require narrow unsafe responsibility. The `<` marker
does not suppress allocation-failure checking.

An attached-lifespan arena may provide the independent lifetime owner for an
open-ended raw pointer when analysis proves that the arena remains valid through
every use. Other manual protocols must preserve allocation-root provenance,
destructor, size, alignment, disposition, arena identity, and exactly-once
cleanup.

##### Scheduled raw-slot replacement

Assignment to a declaration-attached raw slot replaces its scheduled allocation
rather than merely repointing:

```zax
slot : MyValue * = @
slot = anotherPointer
```

The operation:

1. applies the old allocation's disposition;
2. invalidates proofs tied to that old resident path;
3. adopts the new allocation and its disposition authority; and
4. schedules the new allocation against `slot`'s remaining life path.

The source must supply legitimate disposition authority. These transfers may be
safe:

```zax
slot = uniqueOwner as last
slot = otherScheduledRaw as last
```

The first transfers `unique` ownership into the declaration-attached schedule.
The second transfers another declaration's disposition responsibility, sets that
source pointer to `Nothing`, and leaves its existing scheduled cleanup as a
no-op.

An ordinary borrowed raw source does not by itself prove that it names an
allocation root or that no competing owner will later clean it:

```zax
slot = borrowedPointer
// unsafe unless analysis proves valid disposition adoption
```

A statically proved source is safe. Otherwise the operation requires narrow
unsafe responsibility; blindly adopting it could double-dispose an allocation,
destroy a subobject as though it were an allocation root, or use unavailable
arena provenance.

Resetting a scheduled slot dispositions its current allocation before
installing `Nothing`:

```zax
slot = (: MyValue *)
```

The same assignment on an open-ended raw slot only discards the address and does
not clean the allocation:

```zax
manual : MyValue * = @<
manual = (: MyValue *)
// The open-ended allocation was not dispositioned.
```

Ordinary unscheduled raw-pointer assignment remains address repointing. The
destination declaration's scheduled state, not `T *` alone, selects the
replacement behavior and must remain visible to analysis, tooling, and
reflection.

##### Protected `reset`

Protected pre-unary `reset` is the complete pointer operation:

```zax
reset pointer
```

It ends the allocation, ownership, observation, or scheduled-disposition
relationship for which the pointer or declaration has authority, then leaves the
pointer containing `Nothing`.

| Pointer state | `reset pointer` |
| --- | --- |
| Scheduled raw | Apply the recorded allocation disposition; set the pointer to `Nothing`; later scheduled cleanup is a no-op |
| Open-ended raw | Explicitly disposition the allocation and set the pointer to `Nothing`; requires allocation-root and authority proof or narrow unsafe responsibility |
| Borrowed raw | Invalid unless analysis proves that this use legitimately assumes complete disposition authority |
| `unique` | Release unique ownership, apply disposition, and set the pointer to `Nothing` |
| `unique shareable` | Release unique ownership and its dormant block, apply disposition, and set the pointer to `Nothing` |
| `strong` | Release this strong participation; destroy only if strong ownership closes |
| `weak` | Release this weak observation; release the block if it is the final remaining relationship |
| Any pointer already containing `Nothing` | Harmless no-op |

For an open-ended raw allocation, `reset` is the explicit complete manual
disposition operation:

```zax
manual : MyValue * = @<
reset manual
```

The operation applies the disposition recorded at allocation:

- `Prompt` destroys the resident instance and recovers storage promptly;
- `DeferredRecovery` destroys promptly while the arena retains storage; and
- `AttachedLifespan` closes the pointer's local responsibility while resident
  destruction and storage recovery remain attached to arena teardown.

There is no new disposition choice at reset time. Prompt remains the allocation
default.

`reset` operates on the pointer state rather than calling a function through the
pointee, so resetting `Nothing` is a no-op. Calling an ordinary member through a
pointer to `Nothing` remains a separate access-proof and panic question.

Assignment to an empty pointer remains different for an open-ended raw slot:

```zax
manual = (: MyValue *)
// Discard the address only; do not disposition the open allocation.
```

For scheduled and managed destinations, assigning `Nothing` releases their
existing relationship under the destination contract. A separate protected
`clear` operation would merely duplicate that assignment distinction and is not
part of the current model.

Raw reset requires proof that the pointer identifies the allocation root and
that the operation holds valid disposition authority:

1. proved root and authority is safe;
2. potentially valid but unproved provenance may use narrow unsafe
   responsibility; and
3. a proved interior pointer, stack/global place, ended allocation, or competing
   disposition owner is known-invalid and cannot be made valid by unrelated
   unsafe permission.

This allocation-root requirement is independent of panic-versus-`Nothing`
allocation failure. Treating an interior member address as the allocation root
could invoke the wrong destructor or recover storage from the middle of a
containing block.

Exactly-once disposition is lifecycle analysis, not the `once` declaration
category. Resetting the same slot twice is safe because the second reset observes
`Nothing`. Disposition through a stale alias is rejected when analysis proves
that the allocation already ended; where alias identity or provenance remains
unproved, the operation requires narrow unsafe responsibility and a false claim
has undefined consequences.

##### Ownership transitions

`last` may transfer declaration-attached responsibility into managed ownership:

```zax
scheduled : MyValue * = @
owner : MyValue * unique = scheduled as last
```

This is safe when analysis proves that `scheduled` identifies the allocation
root and owns the sole disposition responsibility. `scheduled` becomes
`Nothing`; its declaration-attached cleanup remains scheduled but later observes
`Nothing` and performs no disposition. `owner` becomes responsible for the
allocation. Existing raw aliases remain non-owning and must stay within the
resulting owned path.

The reverse direction is safe when a scheduled raw destination adopts the
managed owner's responsibility:

```zax
scheduled : MyValue * = @
scheduled = uniqueOwner as last
```

Transferring scheduled raw or `unique` ownership into an ordinary raw
destination sheds automatic disposition and makes the allocation open-ended:

```zax
rawResult : MyValue * = uniqueOwner as last
// unsafe unless another valid disposition owner is proved
```

The same open-ended boundary applies when a scheduled raw local escapes through
an ordinary raw result. The raw result type does not secretly retain the source
declaration's schedule.

| Source | Destination | Disposition result |
| --- | --- | --- |
| Scheduled raw `as last` | `unique` | Managed owner adopts the allocation |
| Scheduled raw `as last` | Scheduled raw | Destination adopts the schedule |
| Scheduled raw `as last` | Ordinary raw | Allocation becomes open-ended |
| `unique as last` | Scheduled raw | Destination adopts the allocation schedule |
| `unique as last` | Ordinary raw | Allocation becomes open-ended |
| Borrowed raw | Scheduled raw | Requires proof of allocation-root and sole-disposition authority |
| Ordinary raw | Ordinary raw | Address transfer only |

##### Result slots and recommended `last`

A result declaration may request allocation before body entry:

```zax
makeValue final : (
  result : MyValue * last = @
)() = {
  // result is a live scheduled raw allocation on body entry.
}
```

`= @` establishes pre-body allocation and declaration-attached disposition.
`last` states that the completed result offers terminal transfer of that
responsibility to its caller. This is the recommended raw scheduled-result
contract.

The allocation initializer is part of the visible callable prototype. Every
compatible body must expect the same preconstructed scheduled result state, and
prototype adaptation must not erase materially different arena, disposition,
failure, or open-ended behavior merely because the value type remains
`MyValue *`.

A caller declaration may adopt the schedule:

```zax
value : MyValue * = makeValue()
```

If the result is discarded, its result-slot schedule dispositions a successful
allocation. A reporting result transfers either a successful schedule or
`Nothing`:

```zax
tryMakeValue final : (
  result : MyValue * last = @!
)() = {
}
```

An implicit or explicit `copy` generally copies only the raw address. The
producer result slot would then disposition the allocation after mapping and
leave an invalid caller pointer:

```zax
makeInvalid final : (
  result : MyValue * copy = @
)() = {
}
// error: scheduled raw result requires terminal transfer
```

Analysis distinguishes an unproved escape, which may require narrow unsafe
responsibility when another owner could make it valid, from a provably invalid
escape, which is rejected rather than made valid by `unsafe`. Diagnostics should
recommend `last` or a managed result such as `MyValue * unique`.

Allocation currently produces a pointer result. A hypothetical non-pointer
prototype such as `result : MyValue = @` would request a different
allocation-backed result-storage feature and is not implied by this work.

#### Allocation records and ownership control blocks

Every dynamic allocation has recoverable allocation-record information for:

- the allocation root;
- object arena;
- destructor;
- size and alignment;
- disposition;
- and any link to further metadata.

An implementation may place a record or pointer before the resident storage,
recover it through arena metadata, or use another representation. The physical
layout is not a language promise unless future ABI work establishes one.

An **ownership control block** is the additional structure used for dormant
shareability, strong and weak counts, local versus atomic accounting,
termination state, and collection metadata:

```text
raw or unique
-> allocation record
-> no shared-ownership control block

unique shareable, strong, or weak
-> allocation record
-> inline or detached ownership control block
```

`unique` therefore remains blockless at the ownership type level even though its
allocation retains enough metadata for correct destruction and recovery.

#### Control-block placement

`unique shareable` and `strong` require a control block from the beginning.

- **Inline** means the object arena supplies one coallocation containing resident
  storage and block storage.
- **Detached** means a block arena supplies an independent allocation. Omitting a
  separate block arena may still mean a detached allocation from the object
  arena if that placement is selected.
- A detached block arena must outlive all strong and weak pointers that use it.
- An inline block keeps the combined object allocation reserved while weak
  pointers remain, even after the resident instance has been destroyed.

The allocation operation must acquire all required storage before constructing
the resident instance. If detached block acquisition fails after object-storage
acquisition, the raw object reservation is returned without invoking a resident
destructor because no resident instance exists.

A blockless `unique` cannot generally gain an inline block later without moving
the resident instance. The resulting constraint is:

- an allocation that reserves an inline dormant block produces `unique
  shareable` from the start; and
- preparing an existing blockless `unique` for sharing can allocate only a
  detached block unless its original allocation explicitly reserved suitable
  dormant storage.

That preparation is a separate explicit allocation operation, not a hidden
effect of ordinary `last` transfer:

```zax
prepared : MyValue * unique shareable =
  @!{
    controlArena: myControlArena
  } owner as last
```

Supplying `controlArena:` implies detached placement. `@` and `@!` both apply:

- `@` blocks on allocation failure until a matching helper repairs the condition
  or the program crashes;
- `@!` returns an empty `prepared` destination when block allocation fails.

The operation is transactional. On success, `owner` becomes `Nothing`,
`prepared` owns the same resident instance, and its detached control block is
dormant. On `@!` failure, `prepared` is `Nothing`, `owner` remains an unchanged
usable unique owner, and any partial block reservation is released. The `last`
stance offers terminal transfer but does not mutate the source until the
consumer successfully accepts it.

A `unique shareable` allocation already has a dormant inline or detached block.
It activates that block whenever transfer to `strong` or `strong atomic` needs
it; no allocation occurs at that transition. The dormant block supports both
local and atomic accounting, but compiler-enforced transfer through exclusive
`unique shareable` permits only one model to be active at a time.

#### Disposition and collection

Allocation independently selects one valid destruction/recovery disposition and
one collection policy:

```text
disposition: Prompt | DeferredRecovery | AttachedLifespan
collection:  Disabled | Collectable
```

`Prompt` means prompt destruction and prompt storage recovery.
`DeferredRecovery` means prompt destruction with arena-delayed storage reuse.
`AttachedLifespan` means destruction and recovery at arena teardown/reset. These
three values prevent the invalid combination of attached destruction with prompt
storage recovery while reflection may still expose the two underlying effects.

Deferred recovery means prompt destruction followed by arena-delayed storage
reuse. It does not mean deferred destruction and does not require a distinct
pointer role. Attached lifespan retains the resident instance until arena
teardown even after ordinary pointer ownership closes.

Collection eligibility adds metadata to a block-bearing allocation. It does not
schedule automatic memory-pressure pauses. An arena may report memory pressure,
but that signal is global pressure information rather than a request to collect
one arena.

The programmer explicitly triggers one process-wide collection pass. All
currently collectable strong ownership participates regardless of the object or
control-block arenas that store individual allocations, so cycles spanning
several arenas remain in one graph. Exact trigger spelling, root discovery,
cross-thread coordination, and traversal algorithms remain future collector and
concurrency work.

Ordinary acyclic final release still follows the selected destruction and
recovery behavior. Each cycle evicted by a global pass likewise follows its own
recorded disposition.

`unique` ownership does not participate in cycle collection because no strong
ownership graph exists. A `unique shareable` allocation may nevertheless select
`AllocationCollection.Collectable`; its eligibility and metadata remain dormant
until the allocation becomes `strong` or `strong atomic`. A blockless `unique`
may select collectability while explicitly allocating its detached shareable
block.

### Automatic nested allocation

#### Explicit sites and context defaults

The aligned model distinguishes:

1. **ordinary pointer initialization**, which creates an empty pointer; and
2. **an allocation initializer**, which explicitly asks its destination
   declaration to allocate and construct the pointee.

```zax
Node :: type {
  next : Node * unique
  // Ordinary pointer member: initially Nothing.
}

Tree :: type {
  root : Node * unique = @
  // This member explicitly requests allocation and construction.
}
```

Allocation cannot be inferred merely from a pointer type because every pointer
role can validly contain `Nothing`, recursive pointer structures are ordinary,
and non-panicking allocation intentionally produces an empty pointer.

An obvious unconditional cycle of automatic allocations should be diagnosed:

```zax
Node :: type {
  next : Node * unique = @
  // error: default construction has no terminating path
}
```

Conditional or constructor-directed recursion may still be valid when source
supplies a terminating construction path.

An allocation with no explicit arena uses the current thread context when that
particular `@` operation executes. It does not inherit the arena selected for an
enclosing dynamic allocation:

```zax
Parent :: type {
  child : Child * unique = @
}

parent : Parent * unique =
  @{ arena: scratchArena } [{}]
```

`parent` uses `scratchArena`; `child` uses the then-current
`___.getDefaultArena()`. Discovering the arena of the enclosing allocation would
require extra provenance, couple nested allocation to the outer storage choice,
and fail to generalize cleanly to stack or global containing instances.

When a child must use a particular arena, pass that arena into construction and
state the nested allocation explicitly:

```zax
Parent :: type {
  child : Child * unique

  +++ final : ()(
    childArena : Arena &
  ) = {
    _.child =
      (: Child * unique = @{ childArena }) as last
  }
}
```

`Arena` and its exact reference shape are illustrative because arena interfaces
remain deferred. A construction packet may likewise supply a preallocated child.
No hidden outer-allocation discovery is required.

Called functions that use an omitted-arena `@` also observe the current thread's
`___`. That ambient default is intentional. The enclosing allocation's explicit
arena, failure token, disposition, collection policy, and control placement do
not silently cascade into arbitrary nested declarations or callees.

#### Nested order and failure

Automatic allocation follows the existing member construction plan:

1. automatic members are processed in member declaration order;
2. a member explicitly controlled by the constructor body is processed where
   control flow reaches its `+++`;
3. each automatic pointer allocation completes its own storage, construction,
   and pointer publication before the next member begins; and
4. the enclosing constructor body begins only after its automatic members have
   completed.

The selected failure mode belongs to each allocation site:

| Nested mode | Effect on the pointer member | Effect on enclosing construction |
| --- | --- | --- |
| Panic | No empty result is published | Normal completion is prevented unless the panic is resolved under the existing panic contract |
| Non-panicking | The member receives a valid pointer value containing `Nothing` | Construction may continue and complete |

A non-panicking child failure does not implicitly fail the parent allocation.
The parent constructor may inspect the empty pointer and choose its own defined
behavior. Turning several child failures into an aggregate construction result
would require a separate fallible-construction model; ordinary constructors
currently have no such result.

### Failure, cleanup, and publication

#### Defined allocation-failure paths

Before resident construction begins, allocation may have acquired:

- no storage;
- object storage only;
- both reservations.

The operation retains sole authority over every uncommitted reservation. No
resident destructor runs because no resident instance has been established.

For `@!` or `@!<`, a failed later reservation releases every earlier
uncommitted reservation exactly once before the empty pointer result is
published. For `@` or `@<`, a failed request blocks at that operation. A panic
helper may repair the condition and allow the same request to complete; otherwise
the program crashes gracefully. Fatal panic does not create an ordinary cleanup
or unwind path.

Packet expressions and constructor execution begin only after all required
reservations succeed. This removes a large partial-allocation case and prevents a
detached-block failure from needing to destroy a completed resident instance.

#### Panic never publishes partial state

A panic does not return a substitute value, skip the failed operation, or expose
partially established state to ordinary continuation:

- a matching helper may repair the condition while the operation remains
  blocked;
- the blocked operation then completes as though that failure had not occurred;
  or
- the program crashes gracefully.

The same rule applies when a panic condition occurs during construction or
destruction. Zax does not unwind completed members, roll back a constructor,
continue after an incomplete destructor, or translate a constructor panic into
the `Nothing` result of `@!`.

Each panic category may be independently enabled or disabled. A future narrow
no-panic guarantee for one category may remove its check. If that category's
condition actually occurs, behavior is undefined. Other panic categories remain
enabled, and the guarantee does not create an unchecked allocation operator.

This work requires exactly-once disposition on ordinary continuing paths:

- ordinary successful destruction;
- ownership closure under the selected disposition;
- arena teardown;
- `@!` or `@!<` reservation failure before construction; and
- explicit non-panicking nested-allocation failure.

It imposes no exception-style partial-construction cleanup contract on a fatal
panic path.

#### Publication and exactly-once authority

Before publication, the allocation operation is the sole disposition authority
for raw reservations. After publication, the ordinary release authority moves
to:

- the `unique` owner;
- the shared control block;
- or the declaration life path that owns a scheduled raw allocation.

An attached-lifespan registration separately assigns eventual resident
destruction to arena teardown after ordinary ownership or declaration attachment
closes. Deferred-recovery registration likewise leaves storage reuse with the
arena after prompt destruction.

No normal path may leave two peers each believing they are the sole authority
for the same disposition step. Diagnostics and debug instrumentation should
identify duplicate registration, missing transfer of disposition authority, or
a resident instance published before its control block is usable.

### Arena capability boundary

This work does not define arena APIs, but source behavior requires an existing
arena to provide or carry enough semantic capability for:

- size and alignment of each requested region;
- object or detached-control-block storage as selected;
- prompt release when prompt recovery is requested;
- deferred retention and bulk teardown/reset when arena recovery is requested;
- attached-instance and deferred-recovery registration;
- collection metadata and traversal participation when collection is enabled;
- allocation, release, destruction, and teardown on permitted threads;
- a lifetime long enough for every resident, inline block, detached block, and
  pointer dependency; and
- a request-failure signal distinguishable from successful storage.

The future arena-recognition process must make those capabilities available to
analysis without requiring this work to invent method names or registration
protocols.

Where capabilities are statically known, incompatible source should fail during
analysis:

```zax
// Illustrative only.
owner : MyValue * strong =
  @{
    arena: threadLocalArena,
    controlArena: shortLivedBlockArena
  }
// error if the block arena cannot outlive the resulting weak/strong dependency
```

Where a capability is dynamic, the future arena contract must classify the
runtime outcome explicitly. Allocation must not silently fall back to the process
heap, change prompt recovery to deferred recovery, remove collection metadata, or
move destruction to an incompatible thread.

### Source-form pressure

The reserved `@` family and declaration-bound source category are aligned.
Policy value names, positional/named enclosure rules, control-block-transition
source, and protected pointer `reset` are also aligned conceptual syntax. Formal
grammar remains future specification work.

#### Reserved declaration-bound form

An ordinary operator phrase adds spelling but uses ordinary receiver-owned
dispatch. A multi-clause word form with several operands is a mixfix operation,
whose receiver anchor controls discovery and whose addition can replace prior
expression decomposition.

Allocation does not fit that model cleanly:

- the resident type is not an existing value receiver;
- the arena is not permitted to redefine Zax lifecycle and ownership semantics;
- construction requires language authority over uninitialized storage;
- failure must control whether later constructor inputs are evaluated;
- object and control-block requests form one commit boundary; and
- every policy combination must not require another open phrase or mixfix
  declaration.

`@`, `@!`, `@<`, and `@!<` are therefore language-reserved allocation
initializers rather than ordinary open operator overloads. Their grammar and
evaluation are owned by allocation rather than inherited accidentally from eager
operator invocation. Arenas provide storage capabilities but do not become
receivers that redefine Zax lifecycle semantics.

All direct source allocations require a destination declaration:

```zax
owner : MyValue * unique = @
ownerWithInput : MyValue * unique = @ [{ endpoint }]
ownerInArena : MyValue * unique = @{ myArena } [{ endpoint }]

existing = @ // error: no allocation destination declaration
existing = (: MyValue * unique = @{ myArena }) as last
```

This constraint covers explicit source creation of an allocation root.
Operations such as a collection growth operation may internally allocate under
their own visible cost and failure contracts without exposing a standalone `@`
expression.

#### Keep allocation policy separate from construction packets

Current construction packets already distinguish:

- positional constructor inputs;
- named constructor inputs;
- explicit omission/default requests; and
- `.member = expression` stored-member initialization.

Reusing `.arena`, `.failure`, or similar entries in that same packet would
conflate allocation policy with stored members and create collisions when the
resident type has members of those names. Allocation source therefore has a
distinct allocation-policy region and retains the existing construction
packet unchanged.

Likewise, allocation behavior should not become part of pointer type identity.
Two `MyValue * strong` values may have different prompt, deferred, attached, or
collectable histories. Generic code that requires prompt behavior needs a
separate visible contract rather than a different pointer role.

#### Common and explicit forms

The aligned common forms are:

```zax
plain : MyValue * unique = @
constructed : MyValue * unique = @ [{ endpoint }]
custom : MyValue * unique = @{ myArena } [{ endpoint }]
maybe : MyValue * unique = @!{ myArena }
scopedRaw : MyValue * = @
manualRaw : MyValue * = @<

owner : MyValue * strong =
  @{
    arena: myObjectArena,
    controlArena: myControlArena,
    disposition: AllocationDisposition.DeferredRecovery,
    collection: AllocationCollection.Collectable
  } [{ endpoint }]
```

The following source details remain to be tested:

- category-specific panic-control syntax;
- reflection of explicit, contextual, and defaulted choices;
- source compatibility when a new policy is added; and
- whether a policy value can be stored or passed without prematurely defining
  generic policy syntax.

### Costs and diagnostics

Tooling should expose the effective allocation plan at each site:

- resident type and result pointer role;
- object arena and detached block arena;
- number of storage requests;
- inline or detached control placement;
- control-block and collector metadata;
- prompt or attached destruction;
- prompt or arena recovery;
- panic or empty-pointer failure;
- applicable category-specific panic guarantees;
- context-selected defaults and local overrides;
- constructor selected and packet evaluation order; and
- final disposition authority.

Representative diagnostics include:

- no ownership or lifecycle authority for a raw allocated path;
- destination role missing at a fully inferred site;
- `weak` requested as a new allocation result;
- control placement requested for blockless raw or `unique`;
- block-bearing ownership requested without viable control storage;
- collection requested without compatible ownership metadata;
- attached destruction combined with prompt recovery;
- object or block arena lifetime too short;
- arena lacks size, alignment, release, collection, concurrency, or affinity
  capability required by the plan;
- cross-thread release or destruction cannot run or be scheduled legally;
- empty policy enclosure, second positional arena, or duplicate policy slot;
- redundant detached placement with `controlArena:` lacks intent
  acknowledgement;
- contradictory control placement and control arena;
- automatic allocation requested through an unconditional recursive cycle;
- declaration-attached raw allocation copied where its schedule must transfer;
- ordinary raw source adopted by a scheduled slot without proved disposition
  authority;
- scheduled or `unique` ownership opened into raw escape without sufficient
  proof or unsafe responsibility;
- result `copy` would outlive its scheduled raw result slot;
- open-ended allocation loses its last address without proved disposition;
- raw `reset` target is a proved interior or non-allocation place;
- disposition attempted through a proved stale alias;
- pointer result published before construction or block establishment completes;
- duplicate cleanup or missing exactly-once disposition authority; and
- disabled panic category reaches the promised-impossible condition.

Reflection should distinguish choices that were:

- explicit at the site;
- supplied by the current thread context;
- supplied by the ordinary allocation default;
- attached to a declaration or made open-ended; or
- selected because the destination ownership role requires them.

That distinction is important for cost inspection and future source-stability
analysis. Adding a new policy should not silently change existing effective
plans unless the language deliberately changes a documented default.

### Alignment status and remaining decisions

The following findings are aligned for this work item:

- every direct allocation is a coordinated declaration initializer;
- `@`, `@!`, `@<`, and `@!<` are exact reserved allocation forms;
- the destination declaration supplies the pointee type and pointer role;
- `@` panics on request failure and `@!` returns `Nothing`;
- there is no separate unchecked allocation mode;
- future panic controls enable or disable individual panic categories rather than
  all panics as one unit;
- `@{ ... }` permits at most one positional object arena and otherwise uses
  named policy inputs;
- empty policy enclosures, two positional arenas, and duplicate inputs are
  non-acknowledgeable intent errors;
- redundant detached placement with `controlArena:` is an
  acknowledgement-required intent error;
- `AllocationDisposition.Prompt`, `DeferredRecovery`, and `AttachedLifespan`,
  `AllocationCollection.Disabled` and `Collectable`, and
  `ControlBlockPlacement.Inline` and `Detached` are the aligned policy values;
- ordinary `unique shareable` and `strong` allocation coallocates an inline
  control block and remains non-collectable unless source says otherwise;
- raw `@`/`@!` allocations attach disposition to their destination declaration;
- raw `@<`/`@!<` allocations are open-ended;
- scheduled raw replacement dispositions the old allocation and requires valid
  authority before adopting the new one;
- `last` transfers scheduled raw responsibility into another scheduled slot or a
  managed owner;
- terminal transfer leaves the source pointer at `Nothing`; its declaration
  cleanup remains scheduled and later performs no disposition;
- transfer into an ordinary raw destination opens the allocation and requires
  proof or narrow unsafe responsibility;
- protected pre-unary `reset` applies the pointer's valid disposition,
  ownership, or observation release and leaves it at `Nothing`;
- raw `reset` is the complete manual disposition operation for an open-ended
  allocation, subject to allocation-root and authority proof;
- allocation records are universal allocation metadata while ownership control
  blocks remain specific to shareable/shared ownership;
- scheduled raw result prototypes should declare `last`;
- adding a detached control block to blockless `unique` is declaration-bound and
  transactional: success vacates the source, while `@!` failure leaves the
  source unchanged and the destination `Nothing`;
- dormant shareable blocks support local and atomic accounting with only one
  mode active at a time;
- collectable `unique shareable` metadata remains dormant until strong ownership
  begins;
- cycle collection is explicitly triggered process-wide rather than for one
  arena, although an arena may emit a global memory-pressure signal;
- omitted arenas come from the current thread's `___`, not the enclosing
  allocation;
- explicit outer-arena reuse is passed through construction;
- all required storage is acquired before constructor-packet evaluation; and
- panic resolution repairs the blocked condition or the program crashes, without
  partial-state continuation or exception-style unwinding.

No unresolved allocation-semantic question is currently identified. Completion
work remains before promotion:

1. Disposition legacy `@@`, the old synchronous meaning of `@!`, and legacy
   allocation policy words.
2. Trace every aligned finding into its exact lasting owner and finalize the
   reflection and diagnostic teaching required there.
3. Confirm that deferred proof catalogs, panic-control syntax, collection
   algorithms, and arena/context interfaces remain discoverable in their live raw
   destinations.
4. Perform the required pre-promotion documentation-fit dry run and record its
   exact proposed promotion change set.

Exact static proof scenarios may grow with implementation experience without
changing the aligned three-way contract: proved safe, unproved but assertable
under narrow unsafe responsibility, and proved invalid. Category-specific
panic-control syntax, arena APIs, context declaration and replacement syntax,
generic allocation-policy constraints, global collection implementation and
trigger spelling, async scheduling, and exact `Nothing` representation remain in
their separate future owners.

### Captured deferrals and constraints

#### Panic categories and suppression

**Live destination:** [raw safety input](../raw/safety.md).

**Deferred:** panic-category declaration and selection, helper discovery and
eligibility, the exact blocked-operation resumption contract, compile-time panic
handling, and syntax for promising that one category cannot occur.

**Reactivation pressure:** panic behavior is reviewed generally, a source form
needs to disable one category, or an operation needs a helper that can repair its
failure condition.

**Constraint from this work:** panic categories are independently enabled or
disabled rather than controlled by one all-or-nothing panic mode. Allocation
exposes only panicking `@` and reporting `@!`; disabling the
allocation-failure panic is a general safety guarantee, not an allocation
operator. A matching helper repairs the blocked condition and lets the same
operation complete, or the program crashes gracefully. No path continues with
partial allocation, construction, or destruction state.

#### Arena recognition and APIs

**Live destination:** [raw pointer and arena mechanics](../raw/pointer-and-arena-mechanics.md).

**Deferred:** method names, protocols, registration, discovery, backing-store
implementation, standard arenas, dynamic capability representation, and the
library surface that creates arenas.

**Reactivation pressure:** the allocation source contract needs to validate or
invoke a capability that cannot be described semantically.

**Constraint from this work:** the future process must expose enough capability
for size, alignment, object/control storage, failure, disposition, collection,
affinity, and dependency lifetime without allowing an arena to replace
language-owned lifecycle semantics.

#### `Nothing` representation and dereference

**Live destination:** [raw `Nothing` input](../raw/nothing-instances.md).

**Deferred:** sentinel representation, custom `Nothing`, monitored member
access, compatible overlap, ABI, and exact pointer encoding.

**Reactivation pressure:** allocation begins to define dereference, physical
empty representation, custom fallback behavior, or monitored sentinel access.

**Constraint from this work:** non-panicking allocation produces the destination
pointer role's ordinary semantic `Nothing`; it does not produce optional absence,
does not create a destructible sentinel owner, and does not make an arena failure
“present.”

#### Async, affinity, and queued release

**Live destination:** [raw async input](../raw/async.md).

**Deferred:** executors, cancellation, suspending lifecycle operations,
cross-thread preparation, scheduling failure, and the mechanism for queued
release.

**Reactivation pressure:** allocation, final release, destructor execution, or
arena teardown must cross a thread/executor boundary or survive suspension.

**Constraint from this work:** allocation source must respect arena and resident
affinity; pointer-layer `atomic` covers shared lifetime accounting only; no
operation may silently destroy or release on an incompatible thread.

#### Generic allocation-policy requirements

**Live destination:** [raw type-parameter and generic input](../raw/type-parameters-and-generics.md),
with prompt-disposition pressure retained in
[raw pointer and arena mechanics](../raw/pointer-and-arena-mechanics.md#prompt-disposition-requirements).

**Deferred:** generic policy parameters, constraints, inference, specialization,
and compatibility ordering.

**Reactivation pressure:** a callable or generic must require prompt destruction,
prompt recovery, a particular arena capability, or a transferable allocation
plan.

**Constraint from this work:** `T * strong` alone cannot imply prompt destruction
or recovery. Any such requirement must be visible in callable compatibility and
must not depend on hidden allocation history.

#### Arrays, interop, and collector algorithms

Variable-length/bulk allocation, foreign allocator adoption, exact collector
root/edge algorithms, concurrent graph mutation, and array element relocation
remain deliberately unactivated. They should be read when a concrete source form
attempts to allocate a variable-size region, adopt external storage, or define
collection traversal. Current work must not assume contiguous elements, FFI
allocator equivalence, or automatic collection pauses.

Future collector work is constrained to one explicitly triggered process-wide
pass rather than arena-local collection. Arenas may publish a global
memory-pressure signal, but the programmer decides whether to initiate the
process-wide pass. Cross-arena traversal, synchronization, roots, eviction order,
and exact trigger spelling remain deferred.

### Likely lasting documentation ownership

This is only an initial ownership hypothesis for later dry-run review:

- A cohesive allocation-operation owner may be needed because programmers should
  not have to reconstruct one operation across pointer, construction,
  declaration, safety, and operator documents.
- [Pointers and arenas](../../language/pointers-and-arenas.md) should continue to
  own pointer roles, allocation records versus ownership control blocks,
  protected pointer `reset`, arena relationships, disposition, process-wide
  collection meaning, and allocation-failure categories.
- [Construction and destruction](../../language/construction-and-destruction.md)
  should continue to own resident construction, packet ordering, member order,
  normal completion, and the panic boundary, with a local handoff to allocation.
- [Declarations and bindings](../../language/declarations-and-bindings.md) should
  explain direct initialization from an allocation result and ordinary empty
  pointer initialization.
- [Function invocation](../../language/function-invocation.md) should own
  pre-body allocated result slots, prototype compatibility, and mapping of a
  scheduled raw `last` result into its caller destination.
- [Transfer stances](../../language/transfer-stances.md) should own later
  scheduled-raw/managed transitions while making clear that initial allocation
  is not a transfer from an existing source.
- [Safety and analysis](../../language/safety-and-analysis.md) should own
  category-specific no-panic guarantees, unsafe open-ended/adoption
  responsibility, and precise failure diagnostics.
- [Intent acknowledgements](../../language/intent-acknowledgements.md) should own
  the acknowledgeable versus non-acknowledgeable intent-error distinction.
- [Operator catalog](../../language/operator-catalog.md) and
  [source structure](../../language/source-structure.md) should own exact
  `@`-family and protected pointer-`reset` recognition, longest-token behavior,
  and attached-enclosure presentation without treating allocation as an open
  overloadable operator.
- Arena interfaces, `Nothing` representation, async scheduling, and generic
  policy contracts remain in their indexed raw destinations until activated.

The legacy [context page](../../context.md) appears consumable by value: retain
the thread execution context, fixed application-wide context shape, default
arena access, explicit override, failure, and thread-local cost evidence. The
aligned `@` family and `___` model stand on current review rather than legacy
compatibility. Promotion moves that meaning into current owners while retaining
the root path temporarily for this work item's immutable reading-scope link.

## Dispositions and promotion dry run

### Dry-run result: PASS

Date: 2026-09-06

The aligned findings have a coherent current-owner structure, direct
human-developer reading path, live destinations for every deferred consequence,
and an exact promotion change set. No unresolved allocation-semantic question
blocks promotion.

PASS does not authorize promotion, staging, archival, or closure.

### Structure proposal

Retain the flat `language/` directory and the existing
`language/pointers-and-arenas.md` path. Materially reorganize that document as
the one cohesive programmer-facing owner for pointer roles, allocation source,
allocation records, arenas, disposition, control blocks, reset, and
process-wide collection.

A separate `language/allocation.md` would divide the ordinary allocation task
between two heavily interdependent owners and require readers to reconstruct
pointer-result behavior across both. The existing owner can absorb the material
coherently when it leads with allocation examples and layers pointer roles,
policy, transitions, costs, and advanced arena behavior afterward.

Create `language/execution-context.md` as the independently reusable current
owner for:

- the per-thread `___` execution context;
- one application-wide resolved context shape;
- core shape plus permitted partial additions;
- replacement only by another instance of that same shape; and
- context-provided default object and control-block arenas.

That owner states boundaries rather than inventing unsettled context APIs,
partial-extension syntax, or async swapping behavior. Remove the root
`context.md` from the public legacy route and reduce it to a temporary
non-authoritative bridge because this work item's immutable reading scope still
links to that path. Retire the bridge when item `015` is archived.

No other current-language file move or directory addition is required.

### Lasting ownership map

| Aligned finding | Lasting owner |
| --- | --- |
| Declaration-bound coordinated allocation and the `@`/`@!`/`@<`/`@!<` family | `language/pointers-and-arenas.md` |
| Policy enclosure, one positional object arena, named remaining inputs, defaults, and policy enum values | `language/pointers-and-arenas.md` |
| Raw scheduled/open-ended allocation, scheduled replacement, allocation-root requirements, and protected pointer `reset` | `language/pointers-and-arenas.md` |
| Raw, `unique`, `unique shareable`, `strong`, and `weak` result behavior | `language/pointers-and-arenas.md` |
| Universal allocation records versus shared-ownership control blocks | `language/pointers-and-arenas.md` and terminology summary in `language/terms.md` |
| Inline/detached block placement and transactional block allocation for existing `unique` | `language/pointers-and-arenas.md` |
| Prompt, deferred-recovery, and attached-lifespan disposition | `language/pointers-and-arenas.md`; concise term in `language/terms.md` |
| Dormant collection eligibility and explicitly triggered process-wide collection | `language/pointers-and-arenas.md` |
| Per-thread `___`, fixed application context shape, replacement, and default-arena provision | `language/execution-context.md` |
| Direct initialization, pointer default to `Nothing`, declaration-expression allocation, and no allocation through assignment alone | `language/declarations-and-bindings.md` |
| Construction packets, allocation-before-packet order, automatic member allocation, and construction publication | `language/construction-and-destruction.md` |
| Pre-body `result : T * last = @`, result compatibility, and caller mapping | `language/function-invocation.md` |
| Scheduled-raw/managed `last` transitions and transactional source state | `language/transfer-stances.md` |
| Declaration-owned allocation paths, raw aliases, escape, and reset invalidation | `language/lifetimes-and-references.md` |
| Category-specific panic suppression, unsafe raw adoption/reset, and known-invalid boundaries | `language/safety-and-analysis.md` |
| Reserved allocation-initializer and protected pointer-reset operator categories | `language/operators.md` and `language/operator-catalog.md` |
| Longest-token recognition, `@{` attachment, enclosure layout, and hard source forms | `language/source-structure.md` |
| Acknowledgeable versus non-acknowledgeable intent errors and redundant control placement | `language/intent-acknowledgements.md` and `language/terms.md` |
| Exact future reflection API for explicit, contextual, defaulted, scheduled, and open allocation facts | `project/raw/reflection.md` |
| Exact panic-category source controls | `project/raw/analysis-controls.md` and `project/raw/safety.md` |
| Arena interfaces, provenance, collector algorithms, and thread-affine release | `project/raw/pointer-and-arena-mechanics.md` |
| Context partial-extension mechanics | `project/raw/partial-types.md` |
| Context switching and process-wide collection coordination across threads | `project/raw/async.md` |
| Generic arena/disposition requirements | `project/raw/type-parameters-and-generics.md` |
| Pointer-`Nothing` representation and monitored access | `project/raw/nothing-instances.md` |
| Dynamic-array allocation shape and variable-length consequences | `project/raw/indexing-and-slicing.md` |

### Human-facing teaching plan

The rewritten pointer/allocation owner should teach in this order:

1. Allocate ordinary `unique`, raw, and `strong` values with short valid examples.
2. Explain that allocation is a declaration initializer and construction remains
   a distinct internal stage.
3. Show default behavior before customization: context object arena, inline
   block where required, prompt disposition, non-collectable, panicking failure,
   and zero-input construction.
4. Introduce `@!`, `@<`, and `@!<` through concrete failure and cleanup needs.
5. Explain the attached policy enclosure and construction packet as separate
   source regions.
6. Explain pointer roles, allocation records, control blocks, and object/control
   arena relationships.
7. Teach scheduled raw replacement, protected `reset`, `last` transitions, and
   result-slot allocation.
8. Layer disposition, dormant collectability, process-wide collection, panic
   behavior, arena capability/affinity, costs, diagnostics, reflection pressure,
   and deferred boundaries.

This order gives a cold reader usable allocation source before policy tables and
cross-feature detail. Invalid forms appear only where they distinguish a likely
mistake or required diagnostic.

### Exact current-owner promotion set

Promotion should apply one coherent change set to:

- `language/pointers-and-arenas.md` - materially reorganize and teach the complete
  aligned allocation model while retaining pointer and arena ownership.
- `language/execution-context.md` - create the focused current owner described
  above.
- `language/declarations-and-bindings.md` - integrate declaration-only
  allocation, pointer default initialization, declaration expressions, and
  scheduled declaration state.
- `language/construction-and-destruction.md` - integrate allocation ordering,
  automatic pointer-member allocation, and repair-or-crash panic behavior.
- `language/function-invocation.md` - integrate allocated pre-body result slots,
  required/recommended `last`, and compatibility/mapping consequences.
- `language/transfer-stances.md` - integrate scheduled raw, open raw, and managed
  transitions, including source-`Nothing` and no-op scheduled cleanup.
- `language/lifetimes-and-references.md` - integrate declaration-owned allocation
  paths, alias escape, reset invalidation, and allocation-root lifetime proof.
- `language/safety-and-analysis.md` - remove unchecked allocation as an
  independent mode and integrate category-specific no-panic guarantees,
  unsafe-assertable provenance, and known-invalid reset/adoption.
- `language/operators.md` - identify allocation initializers and pointer `reset`
  as protected language operations outside ordinary open overload dispatch.
- `language/operator-catalog.md` - recognize exact allocation tokens and
  protected pointer `reset`; remove `@`, `@@`, and `@!` from deferred allocation
  evidence.
- `language/source-structure.md` - define longest-token treatment, `@{`
  attachment, policy enclosure presentation, and non-acknowledgeable malformed
  forms.
- `language/intent-acknowledgements.md` - define acknowledgeable versus
  non-acknowledgeable intent errors and the redundant-control-placement case.
- `language/terms.md` - add allocation record and scheduled/open raw terminology,
  distinguish control blocks, remove collection from allocation-disposition
  meaning, and record the intent-error distinction.
- `index.md` - route readers to the expanded pointer/allocation owner and the new
  execution-context owner, and remove the retired legacy context route.

### Exact legacy disposition set

- `basics.md` - replace the deferred `@`/`@@`/`@!` allocation list with a route
  to current allocation syntax; explicitly reject or supersede legacy parallel
  `@@` and synchronous `@!` meanings.
- `context.md` - extract its continuing context evidence into
  `language/execution-context.md`, remove its public legacy route, and reduce it
  to a temporary bridge for item `015`'s immutable reading-scope link; retire it
  during item `015` closure.
- `ctor-dtor.md` - remove the legacy manual-allocation section after its useful
  construction/allocation distinctions and examples are represented by current
  owners; retain unrelated global and `once` evidence.
- `discard.md` - state that legacy allocation `discard` is superseded by
  `AllocationDisposition.DeferredRecovery`; retain unrelated result/type
  discard evidence.
- `flow-control.md` - translate the isolated legacy type-side `@` example into a
  declaration initializer or remove it if it no longer serves the deferred
  value-polymorphism example.
- `arrays.md` - move representative dynamic-array allocation source and questions
  into indexed raw array input, then remove the obsolete type-side `@` examples
  without deciding array semantics.
- `warnings-errors.md` - retire the legacy
  `allocation-into-raw-pointer` diagnostic because declaration-attached raw
  allocation is valid; preserve any still-useful unsafe-open-allocation pressure
  under current diagnostic categories.
- `compiler-directives.md` - route legacy per-category panic-disable syntax and
  panic-registry evidence to current safety documentation and raw
  analysis-control input without adopting the legacy directive spelling.
- `concurrency.md` - replace the legacy thread/fiber context claim with a route
  to the current execution-context owner and retain async transfer details in raw
  async input.

No deleted historical allocation page or archived work item enters the
promotion reading path.

### Exact raw-input disposition set

- `project/raw/pointer-and-arena-mechanics.md` - remove promoted allocation
  grammar, failure, reset, shareability-transition, disposition, and
  process-wide collection contracts; retain arena interfaces, deeper/unsafe
  anchoring, provenance/casts, collector algorithms, recovery implementation,
  and affinity details with updated activation boundaries.
- `project/raw/safety.md` - replace the open resumable-panic alternatives with the
  aligned repair-the-blocked-condition-or-crash rule; retain exact panic-helper
  and safety-contract syntax as future work.
- `project/raw/analysis-controls.md` - add allocation-failure panic as one
  independently selectable category under the general check-removal contract.
- `project/raw/reflection.md` - preserve exact future reflection shape for
  allocation source, defaults, context selection, policy, scheduled/open state,
  storage requests, construction, and disposition authority.
- `project/raw/partial-types.md` - preserve the constraint that partial additions
  contribute to one application-wide context shape that is fixed before runtime
  context replacement.
- `project/raw/async.md` - preserve correct `___` switching and process-wide
  collection coordination across threads without changing current synchronous
  lifecycle rules.
- `project/raw/type-parameters-and-generics.md` - preserve generic requirements
  for arenas, prompt disposition, collection eligibility, and allocation policy.
- `project/raw/nothing-instances.md` - preserve that `@!` produces the pointer
  role's semantic `Nothing`, not optional absence or a representation promise.
- `project/raw/indexing-and-slicing.md` - add representative legacy
  variable-length/dynamic-array allocation examples and state that future array
  work must express them through declaration-bound allocation.
- `project/raw/feature-catalog.md` - mark allocation versus initialization and
  allocation-policy `discard` as dispositioned into current owners instead of
  routing them back to future allocation work.
- `project/raw/README.md` - update the narrowed pointer/arena entry and any raw
  descriptions changed by these dispositions.

No new raw file is required. Future execution-context boundaries can remain in
the new current owner and the already indexed partial, async, and reflection
inputs.

### Validation required after promotion

The authorized promotion, if later granted, must verify:

- every current example uses declaration-bound `@` syntax;
- no current owner describes independent unchecked allocation;
- `@!` means reporting allocation everywhere;
- `@@` has no live current meaning;
- raw scheduled cleanup remains attached after transfer and observes source
  `Nothing`;
- `reset` behavior is consistent across pointer, lifetime, operator, and safety
  owners;
- default control placement and policy values agree in every owner;
- collection is process-wide and programmer-triggered everywhere;
- current documents do not cite item 015 or raw files for meaning;
- the public index routes execution-context readers to the current owner and the
  temporary root bridge remains only for item `015`'s immutable link;
- dynamic-array evidence remains available through the raw index;
- all local links and headings resolve;
- Markdown renders coherently; and
- the final changed-file set exactly matches the separately authorized promotion
  set.

### PASS rationale

The proposed structure gives the complete allocation operation one current
owner, gives execution context one independently reusable owner, preserves
cross-feature rules in their established owners, and leaves every unimplemented
or not-yet-designed mechanism in a live indexed destination. The legacy corpus
can be consumed without losing unique allocation, context, array, panic, or
diagnostic evidence. No current owner would depend on this working record or an
archived file for meaning.

### Promotion result

Status: Applied and validated on 2026-09-07.

The authorized promotion:

- reorganized `language/pointers-and-arenas.md` as the cohesive allocation,
  pointer, arena, disposition, reset, and collection owner;
- created `language/execution-context.md`;
- integrated declaration, construction, result, transfer, lifetime, safety,
  operator, source, intent, and terminology consequences into their current
  owners;
- consumed conflicting legacy allocation syntax, policy words, diagnostics, and
  context routing;
- preserved deferred arena, pointer, panic-control, reflection, partial-context,
  async, generic, `Nothing`, dynamic-array, and collector work in indexed raw
  destinations; and
- retained the root `context.md` path only as the temporary bridge required by
  this active work item's immutable reading scope.

Validation passed:

- `git diff --check`;
- all links, heading anchors, and fenced code blocks in 89 live Markdown files;
- no current language owner depends on numbered work, archives, or raw input;
- no positive current claim of an independent unchecked allocation mode;
- no obsolete unmarked root allocation syntax;
- the complete promotion content matches the 35-path authorized change set; and
- the pre-existing closure/index paths remain outside the promotion scope.

The agent did not stage, unstage, archive, commit, or push promotion changes.
Promotion does not itself authorize item `015` closure or creation of item
`016`.

### Post-promotion aligned refinements

Review after the first promotion exposed additional allocation and callable
boundaries. These findings are aligned and remain part of item `015`.

#### Allocation-capable destinations

A pointer declaration whose own initializer uses the `@` family is an
allocation-capable destination. It may later use direct allocation assignment:

```zax
scheduled : Foo * = @
reset scheduled
scheduled = @{ anotherArena }
```

This is a documented direct operation using the existing declaration's pointee
type, pointer role, and disposition contract. It is not implicit sugar for a
separate declaration-expression temporary.

An ordinary pointer declaration with no allocation initializer cannot use the
shortcut:

```zax
ordinary : Foo *
ordinary = @ // error
```

For an open-ended allocation-capable slot, assignment never performs an implicit
reset:

```zax
open : Foo * = @<
open = @<{ anotherArena }
```

The second allocation is valid only when analysis proves `open` is already
`Nothing` or its previous allocation was dispositioned. A proved live overwrite
is a non-acknowledgeable lifecycle error. Opaque prior disposition requires
narrow unsafe responsibility rather than intent acknowledgement.

`@<` and `@!<` on managed pointer destinations are non-acknowledgeable intent
errors because open-ended disposition contradicts `unique` or shared ownership.

#### Existing-allocation source form

A trailing source expression changes `@` from new-object construction to
existing-allocation adoption and missing-metadata allocation:

```zax
prepared : Foo * unique shareable =
  @ originalValue as last
```

No construction packet is involved because the existing resident instance is
preserved. The destination role determines whether metadata is required:

- proved raw or scheduled raw to blockless `unique` needs no `@`;
- raw or blockless `unique` to `unique shareable` allocates a detached dormant
  block;
- raw or blockless `unique` to `strong` may allocate and activate a detached
  block directly; and
- redundant `@` where the destination requires no allocation or metadata is a
  non-acknowledgeable intent error.

A function result may supply the source:

```zax
prepared : Foo * unique shareable =
  @ makeScheduledRawFoo()
```

The producer's declared stance and disposition authority, not its name, decide
whether adoption is valid. A temporary declaration may likewise transfer its
authority with `as last`.

When the source is runtime `Nothing`, both `@` and `@!` produce `Nothing` without
requesting a control block or panicking. Preparing a source statically proved
`Nothing` is a non-acknowledgeable intent error because no work can occur.

#### Allocation root

An **allocation root** is the top-level life path and typed instance place
created directly by one dynamic allocation operation. Its allocation record
governs the complete allocated storage and final disposition.

An interior member or array element may occupy bytes inside that allocation but
is not an independent allocation root. This distinction controls destructor
selection, arena recovery, raw reset, `unique` adoption, control-block ownership,
and anchored pointers.

#### Constructor-controlled member allocation

A member declared with `= @` ordinarily allocates before constructor-body entry.
A direct current-instance member allocation in a selected constructor suppresses
that automatic pointee allocation for that constructor:

```zax
MyType :: type {
  foo : Foo * = @
  bar : Bar * = @

  +++ final : ()(
    arena : MyArena &
  ) = {
    _.foo = @{ arena }

    if condition
      _.bar = @{ arena }
  }
}
```

The pointer member itself is initialized to `Nothing` before body entry and its
declaration-attached schedule exists. The first reached `_.member = @...`
allocates into that empty slot; later reached operations are allocation
replacement.

Every normal path that reaches an explicit member allocation establishes a
valid scheduled pointer. A constructor that may leave a declaration written
`= @` at `Nothing` requires an acknowledgeable
`conditionally-unallocated-member` intent category. The wrapper acknowledges
suppression of the declaration's strong automatic-allocation signal; it does not
make an uninitialized pointer representation valid.

#### Minted implementations and compatible prototypes

A callable declaration with a body is checked and **minted** once against its
own implementation prototype:

```text
implementation prototype + body
-> establish body-entry facts
-> select internal operations
-> establish internal parameter/result behavior
-> mint one implementation
```

A compatible visible prototype reuses that minted implementation. It does not
reprocess, reselect, specialize, or remint operations inside the body. Generic
specialization is different: future generics may reprocess a generic body for
each demanded concrete type, qualification, or other generic argument, and each
result becomes its own minted implementation.

A compatible visible prototype may adapt call-boundary setup and outward
presentation only when:

- its supplied entry values satisfy the minted body's requirements; and
- the minted implementation's produced results satisfy the visible caller
  contract.

For allocated results, `@` versus `@!` is incompatible in either direction:

- changing `@` to `@!` may give a minted body `Nothing` where it expects presence;
- changing `@!` to `@` may promise callers presence that the implementation does
  not produce.

Scheduled versus open-ended presentation may differ:

```zax
implementation : (result : Foo * last = @)() = { ... }
openView : (result : Foo * last = @<)() = implementation
```

The minted body retains its scheduled internal replacement behavior. The
compatible boundary exposes the completed result as open-ended to callers.
Conversely, an implementation minted for manual open-ended cleanup may be
presented through a scheduled result boundary.

Changing an outward result stance likewise does not remint the body. It is
compatible when the already-minted result can satisfy the new mapping. A
scheduled raw result exposed as `copy` retains producer cleanup and therefore
permits only lifetime-safe borrowed uses unless a caller explicitly restates
`last` or proves another owner.

An implicit `copy` stance on a scheduled raw result is an
acknowledgement-required `implicit-stance-at-terminal-use` error. `last` is the
ordinary repair. Explicit `copy` or `move` deliberately retains a borrowed
outward contract and remains subject to caller lifetime proof. `deep` is
available only when an exact deep-capable consumer exists; raw pointers acquire
no implicit pointee-cloning behavior.

Non-reporting `@`/`@<` results guarantee presence on normal exit. The body may
temporarily reset such a result but must restore presence. Opaque proof may use a
narrow unsafe assertion; a proved `Nothing` exit requires changing the prototype
to `@!`/`@!<`.

#### Parameter default ownership

For an input parameter, `= @` is an omission default rather than an
allocation-capable parameter-slot contract:

```zax
useFoo final : ()(
  foo : Foo * = @
) = {
  // foo is always borrowed raw inside the minted body.
}
```

When omitted, caller-side invocation machinery owns the scheduled allocation
temporary and retains it through call completion. The callee receives no cleanup
obligation. An explicit caller-supplied pointer retains its existing owner.

The same rule applies to a general default expression. Its producer's actual
ownership and stance contract, not its name, controls temporary lifetime. No
`@ functionCall()` wrapper is needed merely to make a parameter default safe.

#### Revised dry-run result: PASS

These refinements fit the promoted structure without a new current owner or
directory change.

Incremental current-owner set:

- `language/pointers-and-arenas.md` - allocation-capable replacement,
  existing-allocation adoption, empty-source bypass, managed `<` rejection, and
  allocation-root definition.
- `language/construction-and-destruction.md` - constructor-controlled member
  allocation and conditional-nonallocation intent.
- `language/declarations-and-bindings.md` - allocation-capable destinations and
  parameter-default distinction.
- `language/function-invocation.md` - minted implementation model, compatible
  boundary adaptation, result presence, result stance, and invocation-owned
  defaults.
- `language/transfer-stances.md` - fixed minted stance versus outward alias
  stance and existing-allocation adoption.
- `language/safety-and-analysis.md` - open-allocation overwrite proof and
  result-presence assertions.
- `language/operator-catalog.md` and `language/source-structure.md` - direct
  allocation assignment and trailing-source forms.
- `language/intent-acknowledgements.md` - conditional member allocation and
  implicit scheduled-result stance.
- `language/terms.md` - allocation root and minted implementation terminology.

Incremental raw-owner set:

- `project/raw/type-parameters-and-generics.md` - generic reprocessing versus
  concrete minted implementations.
- `project/raw/callable-selection.md` - compatible visible prototype versus
  minted-body boundary.
- `project/raw/reflection.md` - implementation prototype, visible aliases, and
  minted specialization identity.
- `project/raw/README.md` - route future callable/reflection work through the
  minted-implementation distinction.

The revised result remains **PASS**. The existing current-owner structure absorbs
every finding coherently, no accepted rule depends on archived work, and all
future generic/reflection mechanics have live indexed destinations.

#### Refinement promotion result

Status: Applied and validated on 2026-09-07.

The incremental promotion captured:

- allocation-capable direct assignment and open-allocation overwrite proof;
- existing-allocation adoption through a trailing source;
- runtime-`Nothing` bypass and managed open-ended rejection;
- allocation-root terminology;
- constructor-controlled member allocation;
- minted implementation versus compatible visible prototype behavior;
- result presence, outward scheduling, and explicit stance contracts;
- caller-owned raw parameter-default temporaries; and
- generic reprocessing as distinct from reuse of a concrete minted body.

Validation passed:

- `git diff --check`;
- links, anchors, and fenced code blocks in all 89 live Markdown files;
- no broken former allocation anchors;
- no current-owner dependency on project work, archives, or raw input;
- no stale compatibility wording that implies compatible aliases remint bodies;
  and
- the incremental unstaged set exactly matches the 15-path revised promotion
  set.

The agent did not alter the staged baseline. Item `015` remains active pending
separate closure authorization.

#### General direct-allocation refinement

Further review clarified that bare allocation assignment is not limited to a
declaration whose initializer already used `@`.

Any typed pointer destination supplies enough context for:

```zax
ordinary : Foo *
ordinary = @
```

The destination role determines the resulting relationship:

- scheduled raw destinations implicitly reset and install a scheduled
  replacement;
- open-ended and ordinary undecorated raw destinations do not implicitly reset
  or acquire a schedule;
- managed destinations release old ownership and install the new managed
  allocation.

Therefore assignment into an undecorated raw pointer creates an open-ended
allocation and enters ordinary manual-disposition analysis. It is the direct
form of allocating through a typed declaration expression and terminally mapping
the result into that raw destination.

For an open-ended destination:

```zax
open : Foo * = @<
open = @<{ anotherArena }
```

the second operation never resets the old allocation automatically. It is safe
only when `open` is proved `Nothing` or its prior allocation was dispositioned.
The ordinary repair is:

```zax
reset open
open = @<{ anotherArena }
```

A proved live overwrite is non-acknowledgeable. Opaque prior disposition requires
narrow unsafe responsibility rather than intent acknowledgement.

Inside a constructor, direct `_.member = @...` uses the same general allocation
assignment. Its only constructor-specific behavior is syntactic suppression of
that member's automatic pre-body pointee allocation for the selected
constructor.

#### Open-ended parameter defaults

When an omitted raw parameter default produces a terminally transferred
open-ended allocation, caller-side invocation machinery may adopt that authority
into an invocation-owned cleanup obligation:

```zax
makeOpen final : (
  result : Foo * last = @<
)() = {
}

useFoo final : ()(
  foo : Foo * = makeOpen()
) = {
  // foo is borrowed raw.
}
```

The caller retains the open allocation through call completion and resets it
afterward. The callee receives no ownership or cleanup obligation. This is valid
only when the default result carries allocation-root and disposition authority;
an ordinary borrowed raw result does not cause cleanup to be invented.

#### Delayed guaranteed-presence pressure

Current result forms cannot express all three of:

1. no pre-body resident pointer allocation;
2. owning, raw, or optional result shape; and
3. guaranteed presence on every normal exit.

`result : Foo * last = @` guarantees presence but performs pre-body allocation.
An uninitialized `Foo * unique`, raw pointer, or optional result permits delayed
construction but does not promise non-`Nothing`/present state to callers.
References express non-`Nothing` borrowed access but do not preserve pointer
ownership, rebinding, or absence behavior.

This is future callable precondition/postcondition pressure, conceptually:

```zax
makeFoo final : (
  result : Foo * unique
)() ensures ?result = {
}

useFoo final : ()(
  foo : Foo *
) requires ?foo = {
}
```

Exact syntax is not accepted. Future contracts should generalize beyond pointer
presence to optional presence, ranges, collection size, ownership state, and
other value predicates. Known violations remain errors; opaque proof may use
narrow unsafe responsibility.

#### Targeted dry-run result: PASS

This refinement requires no new current owner or directory change.

Incremental current-owner set:

- `language/pointers-and-arenas.md`;
- `language/construction-and-destruction.md`;
- `language/declarations-and-bindings.md`;
- `language/function-invocation.md`;
- `language/safety-and-analysis.md`;
- `language/operator-catalog.md`;
- `language/source-structure.md`; and
- `language/terms.md`.

Incremental raw-owner set:

- `project/raw/callable-selection.md` for future pre/post callable contracts;
- `project/raw/safety.md` for contract proof and unsafe assertion pressure; and
- `project/raw/README.md` for discoverability.

The existing owner structure absorbs the findings coherently. The targeted
dry-run result is **PASS**.

#### General direct-allocation promotion result

Status: Applied and validated on 2026-09-07.

The incremental promotion:

- made direct `destination = @...` allocation valid for every typed pointer
  destination;
- defined undecorated raw assignment as open-ended and retained explicit reset
  requirements for open replacement;
- narrowed constructor-specific behavior to suppression of automatic member
  pointee allocation;
- defined caller-owned cleanup for terminally transferred open parameter
  defaults; and
- preserved delayed guaranteed-presence requirements as future general callable
  precondition/postcondition work.

Validation passed:

- `git diff --check`;
- links, heading anchors, and fenced code blocks in all 89 live Markdown files;
- no stale current use of the superseded allocation-capable-destination concept;
  and
- the incremental unstaged set exactly matches the 12-path targeted promotion
  set.

The staged baseline remains unchanged. Item `015` remains active pending separate
closure authorization.
