# Raw input: pointer and arena mechanics

| Field | Value |
| --- | --- |
| Status | Raw future-work input / non-authoritative |
| Audience | Future work defining complete pointer provenance, arena interfaces, custom control blocks, collector algorithms, recovery internals, or affinity |
| Applies To | Mechanics deferred by current lifetime and pointer design |
| Owns | Preservation of unresolved arena interfaces, custom control-block implementation, unsafe interior ownership, pointer provenance, raw-memory byte views, allocator and control-block metadata queries, managed-pointer and `OpaqueOwner` layout, pointer-layer presence mechanics, cycle-tracing algorithms, recovery implementation, prompt-disposition generic pressure, and thread-affine release |
| Does Not Own | Current reference lifetime, pointer ownership semantics, `unsafe cast` and pointer/integer conversion ([conversions and casts](../../language/casting.md)), or the programmer-facing Nothing-instance model |
| Source / Provenance | Former raw lifetime input; legacy `pointers.md`, `memory-allocation.md`, `custom-allocators.md`, `strong-weak.md`, and `handle-hint.md`; legacy `basics.md` pointer operator list; work items `014`, `015`, and `031` |

## Current constraints

Current behavior is owned by:

- [Zax lifetimes and references](../../language/lifetimes-and-references.md);
- [Zax pointers and arenas](../../language/pointers-and-arenas.md);
- [Zax transfer stances](../../language/transfer-stances.md);
- [Zax conversions and casts](../../language/casting.md); and
- [Zax safety and analysis](../../language/safety-and-analysis.md).

Future work must preserve:

- permanent reference binding;
- raw, `unique`, `unique shareable`, `strong`, and `weak` pointer roles, with
  interior pointers as ordinary `strong` or `weak` pointers;
- pointer-layer `atomic` as shared lifetime accounting rather than pointee thread
  safety;
- arena-backed dynamic allocation;
- independent destruction timing, storage recovery, and cycle probing;
- inline or detached control blocks;
- declaration-bound `@`, `@!`, `@<`, and `@!<` allocation;
- protected pointer `reset`;
- universal allocation records distinct from shared-ownership control blocks;
- destination-directed ownership transitions;
- weak probing without acquisition;
- weak-to-strong `copy`;
- `inner` over direct member paths, and role-preserving `outer cast`;
- no interior-to-blockless-`unique` conversion; and
- vacancy-preserving `unsafe cast` that changes only the pointee type.

## Custom shareability implementation

A blockless unique owner cannot become strong until a control block is
allocated:

```zax
owner : MyValue * unique

prepared : MyValue * unique shareable =
  @!{
    controlArena: myControlArena
  } owner as last
```

Current allocation source and transactional failure behavior are defined by
[Zax pointers, allocation, and arenas](../../language/pointers-and-arenas.md#shareable-unique-ownership).
Future implementation work must decide:

- how allocation records locate detached blocks;
- and how custom block implementations expose local and atomic shared modes.

Shedding a detached block may recover its allocation. Shedding an inline block
retires its semantic role but normally cannot recover its bytes without
relocating the instance.

## Unsafe interior ownership

Current `inner` accepts a direct member path, including an uninterrupted chain of
direct members, and rejects paths that cross a pointer, reference, optional
payload, variant alternative, dynamic array element, unmanaged overlay, or
separate allocation:

```zax
wheel : Wheel * strong = inner car.axle.wheel
```

Legacy `unsafe lifetime of` preserved arbitrary adoption of another pointer's
ownership control block. If an unsafe successor exists, it must state the exact
relationship being claimed:

- the target lies within the root pointer's allocation;
- the target instance place remains valid for every use;
- replacement or relocation behavior is intentional;
- representation and alignment are suitable; and
- the interior target cannot become a blockless `unique` allocation root.

Illustrative future source:

```zax
// Illustrative only; the spelling is not accepted.
/// myTarget is a stable subplace of owner despite opaque projection.
target : Target * strong = unsafe inner owner with myTarget
```

## Managed-pointer and `OpaqueOwner` layout

Current design requires `strong`, `weak`, and `unique shareable` pointers to
record their target and control block separately, and requires `OpaqueOwner` to
remember an interior target and its type so erasure round-trips exactly.
Blockless `unique` stays a single address.

Future work must define the physical layout. `OpaqueOwner` may need more storage
than a root-only owner; that is acceptable if no better representation is found.
Activate when pointer representation, ABI, or opaque-owner storage is designed.

## Blockless `unique` disposal information

A blockless `unique` must target its allocation root, and current
[conversions and casts](../../language/casting.md#pointer-ownership-roles)
permits `unsafe cast` to change its pointee type under programmer
responsibility. Because the target is always the root, the root allocation could
record basic destructor information for the constructed type, so that a retyped
blockless `unique` still disposes the real type. Future work must decide whether
that record exists and therefore which type a retyped blockless `unique`
destroys. This joins the open question below of how a blockless `unique`
retains its object arena, disposition, destructor, size, and alignment.

## Allocator and control-block queries

Legacy `basics.md` proposed operators that observe allocation metadata. Their
surviving direction:

```zax
// Illustrative only; exact words and result types are not accepted.
arena := allocator of myValue           // requires proof of a known allocation
arena := unsafe allocator of myValue    // programmer asserts a known allocation
block := overhead as myShared           // protected access to a control-block description
bytes := overhead size of MyValue * strong
```

- `allocator of` returns the arena by reference, because both forms are
  non-failing. Until an arena interface exists, a placeholder such as
  `OpaqueReferenceObserver`, recovered with `is type`, stands in for the result
  type. A blockless `unique` qualifies only if it retains its arena.
- `overhead as` would be protected and return a pointer to a yet-undefined
  control-block description type.
- `overhead size of` is design pressure only. The size varies by pointer role and
  by compiler host versus target (see
  [compile-time environments](../../language/compile-time-code.md#which-definitions-apply-while-compiling)). Custom control blocks
  are considered unlikely and are not its motivation.
- A `weak count probe` beside the current `strong count probe` may be added if
  weak-count observation is needed.

Activate when arena interfaces, control-block representation, or allocation
reflection is designed.

## Pointer provenance and conversion

Pointer-representation integer capacities are owned by
[Zax integers](../../language/integers.md#pointer-representation-integer-roles).
Conversion between pointers and `UPointer` is owned by
[Zax conversions and casts](../../language/casting.md#pointers-and-integers).
Numeric fit does not establish pointer validity.

Future pointer work must define:

- raw pointer creation and address-of behavior;
- how a blockless unique pointer retains or recovers its object arena,
  disposition, destructor, size, and alignment without a shared control block;
- provenance carried, lost, or asserted across pointer/integer conversion;
- provenance preservation;
- alignment;
- segment or address-space identity;
- comparability and ordering;
- pointer subtraction and `PointerDelta`;
- one-past and range boundaries;
- ownership-preserving versus ownership-losing conversions beyond the current
  role transitions;
- and FFI adoption.

A helper that views raw memory as an array or iterator of bytes is also
pressure here. Raw pointers already reach bytes, but a bounded view would make
serialization and FFI code clearer. Its shape touches
[arrays and slices](../../language/arrays-and-slices.md). Viewing a pointer
object through `unsafe cast UPointer &` is not such a view: it yields one integer,
not bytes.

Near, ordinary, and far representations may share machine layouts while
retaining distinct source identities and intent.

`pointerA delta pointerB` must not overflow for a semantically valid difference,
but that does not make arbitrary pointers comparable.

## Vacant-pointer and Nothing-instance mechanics

Current semantics are defined by
[Zax Nothing instances](../../language/nothing-instances.md). A vacant pointer
semantically targets its pointee type's Nothing instance, and `?pointer` reports
the guarantee appropriate to the pointer role. `@!` and `@!<` produce the
destination pointer role's vacancy when a storage request fails. This is not
optional absence and creates no ownership of Nothing backing.

Future pointer mechanics must define:

- exact tags, sentinel addresses, protected target storage, or other
  representations by pointer role and target;
- whether compatible compiler-provided Nothing backing has an address at all;
- provenance and alignment attributed to a vacant representation;
- arithmetic checks when an ordinary target is required;
- safe type-aware conversion that remaps vacancy to the destination role;
- the vacancy test performed by `unsafe cast`, which remaps a vacant source to
  the destination's vacancy, and when it can be elided;
- the raw representation produced when a vacant pointer converts to `UPointer`;
- same-type vacant equality and any permitted byte-level observation; and
- interaction with foreign nullability, target protection, and debug
  instrumentation.

Pointer mechanics must not assume a universal software vacancy check. Hardware
write protection is used when available; trapping reads likewise depend on
target capability. When required hardware support is absent, debugger detection
does not create an equivalent panic guarantee and an access relying on that trap
has undefined behavior.

No representation may weaken the rule that raw pointer presence proves no
pointee lifetime, provenance, alignment, or authority. A managed vacant pointer
owns no target and never retains, releases, counts, or destroys a Nothing
instance.

## Arena interfaces

Every dynamic allocation uses an arena, but complete interfaces remain open:

- allocation request shape;
- size and alignment types;
- backing block acquisition, release, and bulk reset;
- concurrency and thread-affinity declarations;
- capacity and memory-pressure reporting;
- object and control-block arena relationships;
- failure and panic behavior;
- nested arenas;
- arena ownership;
- and queued release for thread-confined arenas.

The arena supplies basic storage operations. Language-managed machinery owns
object-by-object construction/destruction registration, control blocks,
attached-lifespan and deferred-recovery lists, collector metadata, and teardown
validation.

Arena implementation may recover memory immediately, lazily, in batches, or at
teardown. Current allocation disposition controls the programmer-visible
destruction and recovery contract; an implementation must not silently change
it.

## Allocation failure implementation

Current source exposes panicking `@` and reporting `@!`. There is no independent
unchecked allocation form. A future category-specific panic control may omit the
allocation-failure check under a programmer guarantee; exact syntax remains in
[analysis-control input](analysis-controls.md).

Arena interfaces must report request success or failure in a form that lets the
language implement `@!`, enter the allocation-failure panic for `@`, and retry a
blocked request after a matching helper repairs its condition.

## Prompt-disposition requirements

Ordinary `strong` ownership may use prompt, deferred-recovery, attached, or
collected allocation behavior. A generic API that accepts only `T * strong`
therefore cannot assume that final release immediately runs the destructor or
recovers storage.

Future work must decide how an API requests:

- prompt destruction;
- prompt storage recovery;
- an explicit resource-closing operation independent of lifetime;
- rejection of attached or collected disposition;
- or a generic allocation-disposition constraint.

The requirement must remain visible in source and callable compatibility rather
than depending on hidden allocation history.

## Teardown and recovery

Future arena work must define exact ordering among:

- closing the arena to new allocations;
- cycle probing;
- attached-lifespan destruction;
- prompt-destroyed deferred-recovery blocks;
- externally owned allocations;
- inline control blocks retained by weak pointers;
- detached control blocks in another arena;
- destructor-created work;
- panic during teardown; and
- final backing-storage release.

The current contract requires panic rather than successful teardown when a
dependency would outlive required backing storage.

## Process-wide cycle collection

Cycle probing is optional allocation behavior layered on strong ownership. The
programmer explicitly triggers one process-wide pass; an arena may report global
memory pressure but does not initiate an arena-local collection.

Future work must define:

- compiler emission of collector support only when reachable build output uses
  collectable allocations;
- root registration or stack/global metadata;
- process-wide coordination across object and control-block arenas;
- how external roots are distinguished from strong edges inside a candidate
  cycle;
- how interior pointers participate;
- whether custom types expose outgoing strong edges through reflection or
  generated traversal;
- collector interaction with local versus atomic control blocks;
- concurrent graph mutation;
- eviction ordering;
- destructor reentrancy;
- weak termination publication;
- attached-lifespan behavior after cycle eviction; and
- library-provided memory-pressure events and explicit programmer-triggered
  collection APIs.

An unreachable cycle is not merely “strong count greater than zero.” Collection
requires proving that no ownership path from outside the candidate graph
remains.

Mark state may live in the existing control block, so collectable pointers need
not be wider. The compiler still needs generated traversal or equivalent type
metadata to discover outgoing strong edges.

## Thread affinity and release

Arenas are not inherently thread-safe. An arena may be thread-confined,
synchronized, movable under exclusive ownership, or able to queue work to an
owning thread while resident instances remain separately thread-affine.

Future work must define:

- whether ownership may move to another thread;
- where final destruction may execute;
- whether the arena itself may receive allocation, release, or teardown on that
  thread;
- executor or thread scheduling for affine destruction;
- what happens when scheduling fails;
- whether control-block arenas may be released from another thread;
- interaction with cancellation;
- and which guarantees an atomic pointer actually exposes.

Pointer-layer `atomic` protects shared lifetime accounting only.

## Arrays and relocated elements

Current [arrays and slices](../../language/arrays-and-slices.md) permits shared
raw storage providers with one unique backing handle per array. Providers own
raw chunks/slots and physical lookup; arrays own element lifetimes and
programmer-visible invalidation. Backing relocation invalidates ordinary element
pointers, references, slices, and cursors; keeping the provider alive does not
keep one relocated element place valid.

Future pointer work must define:

- strong ownership of the provider versus unique ownership of one array handle;
- transferring or adopting the unique handle during `move`/`last`;
- provider/handle lifetime when an array is inline;
- storage-erased versus exact provider references;
- unsafe raw pointers to an erased provider object;
- why such a pointer exposes no element contiguity;
- provider-root provenance and safe typed access when storage type is known;
- provider physical allocations hidden behind one array handle; and
- failure/disposition when changing providers.

The shared provider, unique handle, slot/chunk lookup, stability, factory, and
storage-change protocol pressure is preserved by
[raw array storage strategies](array-storage-strategies.md). This file owns the
pointer provenance and ownership consequences of that future contract.

Ordinary slices remain non-owning. If future design provides an owning or
allocation-anchored slice, it must retain the exact array root while separately
proving that every targeted element place remains current. Pointer provenance
work must consume array operation guarantees rather than inventing hidden
stability or treating one element as an allocation root.

## Structural slicing and copied pointers

[Zax structural shapes and compatibility](../../language/structural-shapes-and-compatibility.md)
treats a pointer as one atomic leaf and leaves pointer-copy validity to the
pointer and lifetime contract.

Structural slicing can separate a copied pointer from storage it references:

```zax
Packet :: type {
  payload : Buffer
  cursor : Byte *
}

CursorOnly :: type {
  cursor : Byte *
}
```

Future pointer work must decide whether copying `cursor` while excluding
`payload` is:

- safe because another proved owner preserves the target;
- valid through a managed or interior-pointer relationship;
- unavailable because lifetime cannot be proved; or
- permitted only through local unsafe responsibility.

Destination copy behavior may repair, retain, or reject the relationship.
Structural conversion does not prohibit pointer-bearing slices and does not
invent another pointer model.

## Activation and retirement

Activate this input when arena interfaces, control-block customization, unsafe
interior ownership, provenance, raw-memory views, allocation metadata queries,
pointer representation, pointer-layer presence mechanics,
process-wide cycle-collection algorithms, memory recovery implementation, or
thread-affine release is reviewed. Current allocation and pointer source is
owned by
[Zax pointers, allocation, and arenas](../../language/pointers-and-arenas.md).
Programmer-facing Nothing-instance and dereference behavior belongs to
[Zax Nothing instances](../../language/nothing-instances.md).

Move accepted behavior into domain-oriented current owners, preserve remaining
future concerns in narrower indexed inputs, then retire this file.
