# Raw input: array storage strategies

| Field | Value |
| --- | --- |
| Status | Raw future-work input / non-authoritative |
| Audience | Future work defining the contract between intrinsic arrays and programmer-provided storage strategies |
| Applies To | Raw backing providers, unique array handles, slot/chunk lookup, alignment, stability tracking, provider sharing, storage-erased references, provider-specific access, strategy changes, and array/context integration |
| Owns | Preserved programmer-visible constraints and protocol pressure whose exact storage-provider contract and syntax remain unresolved |
| Does Not Own | Current intrinsic array behavior ([arrays and slices](../../language/arrays-and-slices.md)); general pointer ownership and arenas ([pointers, allocation, and arenas](../../language/pointers-and-arenas.md)); general execution context ([execution context](../../language/execution-context.md)); general transfer stances ([transfer stances](../../language/transfer-stances.md)); or accepted generic/reflection/analysis-control syntax |
| Source / Provenance | Array and slice review after the initial flat/adaptive model exposed the need for replaceable programmer-provided backing strategies |

## Why this input exists

Current arrays need one stable programmer model while allowing very different
physical organizations:

- inline fixed storage;
- one contiguous dynamic block;
- chunked or bucketed storage;
- linked slots;
- trees or other indexed organizations;
- shared storage providers with independent per-array regions; and
- programmer-defined providers.

The array must continue to own element identity, bounds, lifetime, transfer, and
failure behavior. The storage strategy must remain a raw-byte service and must
not silently become another implementation of Zax element semantics.

This input preserves the contract pressure. It does not establish a provider
interface, exact source grammar, ABI, or lowering.

## Provider and unique-handle ownership

One provider may be shared by several arrays:

```text
shared provider instance
|- unique handle for array A's raw region
|- unique handle for array B's raw region
`- unique handle for array C's raw region
```

Each array retains:

- strong participation keeping the provider alive; and
- one unique handle identifying its independently owned backing region.

Provider sharing does not share array elements. Two arrays have distinct element
places unless another explicit array operation establishes shared element
semantics.

The provider pointer and region handle have different roles:

- provider ownership keeps the raw storage service alive;
- the unique handle owns one array's region/disposition relationship; and
- slices borrow element places and own neither role.

## Raw storage boundary

The provider receives raw requirements such as:

- slot byte extent;
- gross alignment;
- current and requested slot count;
- dimensions and shape capabilities;
- requested contiguous or segmented behavior where constrained;
- existing unique handle; and
- operation kind.

It may supply:

- one raw slot;
- one chunk containing several equal-stride slots;
- several chunks;
- a linked-node location;
- a bucket/tree location; or
- a relocation plan and fresh destinations.

The provider does not know:

- the Zax element identity;
- whether one slot contains a live value;
- which constructor or destructor applies;
- element transfer stance;
- element qualification; or
- whether source code is performing construction, assignment, or replacement.

It must not blindly copy or move bytes containing unknown live values. It
supplies raw destinations and transition information; the array layer performs
the required Zax lifecycle operations.

## Logical index and physical lookup

The array owns logical bounds. For `array[index]`, it first validates the index
against current logical length, then asks the provider/handle to locate the raw
slot or chunk containing that logical position.

The provider owns physical mapping. It may map logical positions through:

- direct offset calculation;
- chunk and in-chunk offset;
- node traversal;
- bucket lookup;
- tree lookup; or
- another declared strategy.

The returned region must satisfy the supplied extent/alignment contract. The
array then interprets the slot as the element place whose lifetime it owns.

Future work must define:

- lookup result shape;
- whether a chunk reports count and stride;
- mutable versus readonly raw access;
- failure behavior;
- repeated lookup/caching;
- thread/concurrency capability;
- how provider-specific cost is exposed; and
- how an erased array reference invokes lookup.

## Physical backing and usable capacity

Array-level capacity means usable element capacity while respecting the array's
declared maximum.

A provider may physically own more:

```text
array maximum:        100 elements
provider chunk:       128 element-sized slots
array capacity query: 100 elements
```

The extra physical space is not directly usable under the current array
contract. Provider-specific inspection may report physical slots or bytes.

Future protocol work must distinguish:

- logical length;
- usable array capacity;
- provider physical slot extent;
- provider physical byte extent;
- programmer-supplied suggested logical capacity;
- provider-specific physical allocation hints; and
- retained physical capacity hidden by a narrower destination contract.

`reserveCapacity(N)` requests usable capacity. A provider may overallocate
physically. `trimCapacity()` requests physical release/reorganization without
changing logical length.

## Stability kinds, tokens, and versions

Array and storage jointly determine slice and element-place invalidation.

The aligned conceptual direction is:

```text
slice:
  required stability kind/token
  observed version
  array origin
  logical interval

array/provider:
  current versions for applicable stability guarantees
```

During mutation:

1. the provider reports which chunks, slots, or physical guarantees changed;
2. the array maps those changes to language-level element-place guarantees;
3. applicable stability versions advance; and
4. later checked slice use compares retained/current information.

A mismatch enters the slice-invalidation panic category.

The exact representation remains open. Viable implementations may need:

- one whole-array backing generation;
- separate logical-position and physical-backing generations;
- per-chunk tokens;
- composite slice tokens;
- range-generation structures;
- several retained versions; or
- conservative invalidation where no stronger guarantee is promised.

Future work must preserve:

- no claim that an ended/moved place survived;
- explicit runtime-check cost;
- static proof eliminating a check when required;
- source-stable guarantees independent from accidental provider behavior; and
- independently disableable invalidation checks with undefined consequences
  for a false promise.

## Storage declaration pressure

Current storage-clause syntax:

```zax
provider : MyStorage * strong

byInstance :
  Integer[5 in 3..1000] storage provider

byType :
  Integer[50] storage type MyStorage

byInstanceAndType :
  Integer[50] storage provider type MyStorage
```

The source must distinguish:

- a required provider instance;
- a required provider type;
- compatible explicit restatement of the inferred provider type;
- no storage requirement on an accepting parameter;
- an omitted value-declaration strategy resolved from defaults/context; and
- future storage capabilities such as contiguous access.

Exactly one storage clause follows the complete array dimension list and applies
to the entire multidimensional array. Per-dimension providers are unavailable;
they would require several storage implementations to coordinate one logical
shape, nested element lifetimes, movement, capacity, and invalidation.

A type alias may retain a storage type requirement. It cannot capture a runtime
provider instance because an alias creates no value or provider ownership.

Supplying a provider instance requires that exact instance. Supplying a type
without an instance obtains or constructs an applicable provider under the
storage type's default/context contract.

The current single storage clause follows the complete dimension list.
`storage` introduces an instance expression; `storage type` introduces a
provider type; and an optional type after an instance is compatible explicit
restatement. Future work may add capability requirements without introducing
per-dimension providers.

## Logical identity and resolved storage profile

Storage strategy does not change the logical array identity consisting of
element type, dimensions, and length contract.

It can change the complete owning representation and therefore affects:

- self-contained `size of`;
- layout and structural compatibility;
- operation availability;
- cost;
- provider-specific access;
- contiguous access;
- element-place stability; and
- parameter/reference mapping.

Future type work must distinguish:

- logical array identity;
- resolved owning storage profile;
- storage-relaxed parameter requirement;
- exact storage type requirement;
- storage-erased array-reference profile; and
- provider-specific handle type.

## Storage-erased references

One concrete function must accept compatible arrays using different storage:

```zax
func final : ()(
  values : Integer[5] &
) = {
  values[2] = 55
}

a : Integer[5]
b : Integer[5] storage provider
c : Integer[5] storage provider type MyStorage

func(a)
func(b)
func(c)
```

The unconstrained reference therefore needs one fixed operational shape able
to:

- reach the original array place;
- invoke the applicable provider operations;
- retain logical bounds and qualification;
- locate raw element slots; and
- preserve origin/lifetime.

It may be wider or more indirect than a reference to one exact storage profile.
Binding must not copy elements or allocate another array.

Possible implementation pressure includes a tag, operation table, generated
thunk, opaque handle, or another erased operation mechanism. None is an accepted
representation promise.

An exact storage-qualified reference may permit specialized provider access.
An unconstrained reference guarantees ordinary array operations, not one
contiguous block or provider-specific API.

## By-value construction and provider reuse

When a destination explicitly names a provider instance, by-value construction
uses that provider even when the source uses another.

When no destination provider is fixed:

- `copy` may retain the strong provider and request another unique handle;
- `deep` may retain the provider only when independent backing plus element
  deep behavior satisfy the contract;
- `move` may transfer a handle only when the source remains a valid live
  moved-from array; and
- `last` may transfer the complete handle when the destination accepts it.

A reference parameter cannot select replacement storage from `___`; it aliases
the caller's existing array and must preserve/check that source provider.

Future work must define provider preference, fallback, failure, overload
comparison, and source/destination storage requirement matching.

## Complete-array and range transfer

A complete-array `last` may transfer provider participation and the unique
backing handle without moving elements.

A passive slice owns neither elements nor the unique handle. `slice as last`
therefore transfers only slice descriptor/window state.

Storage-assisted range transfer requires a consuming operation rooted in the
owning source array. Future design may support:

- detaching whole chunks;
- removing the transferred range from the source;
- transferring chunk/region handles;
- updating index mappings and stability versions;
- elementwise fallback when chunk transfer is impossible; and
- explicit failure when source/destination providers cannot cooperate.

Representative source is not established. Any future spelling must make source
element consumption visible rather than disguising it as passive slice transfer.

## Provider factories and shared defaults

A storage type may own a receiverless default-provider factory:

```zax
MyStorage :: type {
  defaultStorage final : (
    result : MyStorage * strong
  )() unbound = {
    return MyStorage.sharedStorage
  }

  // Illustrative future once-value syntax.
  sharedStorage once : MyStorage * strong
}
```

`unbound` makes the factory callable through the storage type. `once` on a
function does not memoize its result. Persistent shared provider state instead
requires a future type-owned `once` value, execution-context owner, global
owner, or another explicit lifetime.

Future work must define:

- how a storage type declares shared versus dedicated default behavior;
- whether repeated calls return the same provider;
- provider initialization/failure/retry;
- concurrency and teardown;
- context overrides;
- provider result stance; and
- source-stability effects of adding/changing the factory.

## Default selection through `___`

The declaration must resolve a storage representation profile before owning
layout and `size of` become fixed. For a provider-backed profile, the execution
context may select a compatible provider instance from a request containing:

- slot size and gross alignment;
- dimensions and allowed bounds;
- initial count;
- required storage capabilities;
- explicit provider type; and
- explicit provider instance.

An ordinary fixed array may resolve to inline storage. A provider-backed profile
may use a shared provider and unique handle. Runtime context replacement cannot
change one already resolved owning representation from inline to
provider-backed or otherwise alter its self-contained size.

Exact context hook names, override precedence, caching, error behavior, and
compiler-host/target behavior remain future work.

## Provider and handle access

When the exact storage type is part of the resolved profile, array APIs may
expose the provider and unique handle under their declared types and
qualifications.

When provider type is erased, the common storage contract may expose an unsafe
raw pointer to the provider object. An ordinary pointer `unsafe cast` may assert
a concrete provider type.

This pointer:

- owns no provider lifetime;
- points to the provider object, not element contents;
- carries no contiguity guarantee;
- must preserve provider-root provenance; and
- cannot outlive the provider without another owner.

Unsafe authority cannot manufacture one contiguous element span from
discontiguous backing.

## Changing storage strategy

An explicit future operation may change one array's provider:

```zax
// Illustrative only.
values.changeToStorage(newStorage)
```

The operation must:

1. verify provider shape/capability compatibility;
2. obtain destination raw slots;
3. construct or transfer elements in index order;
4. end old element places correctly;
5. release the old unique handle; and
6. publish the new provider/handle only after completion.

It invalidates element references, slices, raw pointers, and cursors unless a
stronger explicit contract proves continuity.

Failure before or during transfer follows current array construction and panic
rules; no partial new array becomes ordinary visible state.

## Shape and capability pressure

Concrete providers may support different shapes and operations:

- dense contiguous rectangular storage;
- padded rectangular backing;
- jagged dimensions;
- stable-prefix insertion;
- cheap append;
- cheap arbitrary removal;
- random access;
- sequential access only;
- cross-thread access; or
- provider-specific physical inspection.

Every valid array provider must support the core array contract selected by its
declaration. Optional capabilities need static requirements or defined
reporting/panic behavior when selection remains runtime.

One concrete `Flat` provider may guarantee dense contiguous storage. A future
capability requirement should permit other providers to guarantee compatible
contiguous access without sharing the exact `Flat` type.

Actual operation cost may vary. Safe source validity may not vary merely because
one provider happened not to move bytes. The general array operation states its
conservative invalidation; a statically visible provider capability may promise
more.

## Cross-owner consequences

Future review must coordinate:

- pointers and arenas for provider/handle ownership, adoption, and disposition;
- execution context for default selection;
- global/`once` lifetimes for shared providers;
- function invocation for earlier-parameter provider dependencies and
  storage-relaxed versus exact parameters;
- qualifiers and lifetimes for erased reference authority and stability;
- transfer stances for handle reuse and consuming range transfer;
- structural compatibility for storage-profile layout;
- generics for provider/capability constraints and associated handles;
- reflection for logical type versus resolved storage profile;
- analysis controls for version checks and disabled panic categories;
- strings/runtime collections for provider reuse; and
- source structure for storage-clause grammar.

## What this input does not decide

This file does not establish:

- provider or handle protocol declarations;
- one array ABI;
- operation-table or dispatch representation;
- stability token representation;
- exact default provider factory names;
- context hook names;
- capability constraint syntax;
- consuming range-transfer syntax;
- provider-specific reflection APIs;
- concurrency contracts; or
- implementation algorithms.

## Activation and retirement

Activate this input when a numbered work item defines array-storage providers,
storage clauses, provider/handle protocols, storage-erased references, strategy
capabilities, stability versions, provider sharing, default selection, or
storage changes.

That work must:

1. begin from current array semantics;
2. preserve the raw-byte versus element-lifetime ownership split;
3. define shared provider and unique array-handle ownership;
4. define static and erased parameter behavior;
5. keep `size of` limited to self-contained storage;
6. expose operation and validity costs;
7. disposition all cross-owner consequences above;
8. move accepted behavior into current array, pointer, context, invocation,
   lifetime, transfer, structural, generic, reflection, safety, and source
   owners; and
9. remove this file from the raw index and retire it when no unresolved storage
   contract remains.
