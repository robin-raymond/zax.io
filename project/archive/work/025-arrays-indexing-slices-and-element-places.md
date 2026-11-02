# 025: Arrays, indexing, slices, and element places

| Field | Value |
| --- | --- |
| Status | Historical project record / non-normative / audit-only |
| Work Item | `025` |
| Created | 2026-09-17 |
| Owns | The bounded review defined by the fixed initiating input |
| Does Not Own | General user-defined generics, complete general collections, runtime string libraries, compiler vectorization, parallel algorithms, compile-time execution, reflection, or implementation architecture |

## Non-authority notice

This file is a collaborative working record. Existing statements, new
observations, candidate wording, and aligned findings remain non-authoritative
until a separately discussed, aligned, and explicitly authorized promotion
incorporates them into their lasting owners.

## Fixed initiating input

This section records the information aligned when work item `025` was created.
It is intentionally incomplete and must not be rewritten as work develops.

### Initiating concern

Establish a coherent programmer-facing model for intrinsic arrays, indexing,
slices, and element places: how element type, shape, length, dimensions,
construction, storage, bounds, mutation, views, lifetime, and traversal interact
without depending on unresolved user-defined generics.

The
[arrays and slices maintainer notes](../raw/arrays-and-slices-maintainer-notes.md)
are primary maintainer-supplied input. The language maintainer will populate
that file before work item `025` begins. Read and disposition its complete
contents as the latest design evidence. It may add to, replace, supersede, or
obsolete any portion of the legacy corpus; retain legacy pages as evidence only
for useful material the refreshed notes do not cover.

The indexed
[indexing and slicing input](../raw/indexing-and-slicing.md) preserves current
operator, multi-index, slicing, range, dynamic allocation, capacity, bit-place,
proxy, traversal, and element-stability pressure.

### Motivating pressure

Arrays are foundational to Zax's data-oriented purpose, but no current concept
owner defines their complete type, storage, construction, indexing, slicing, or
mutation model.

Several current owners already rely on bounded array behavior:

- fixed-array traversal visits stable element places in increasing index order;
- lifetime design requires every dynamic operation to say which element places
  continue, end, or relocate;
- pointer design treats one allocated array as an allocation root rather than
  each element as an independent allocation;
- strings depend on sequence capacity, array relationships, and eventual
  slicing/view behavior;
- operator design recognizes `operator index`, multi-index forms, and direct
  indexed mixfix mutation; and
- `IndexSize` and `TypeSize` distinguish logical positions/counts from storage
  extent and overflow.

These pressures should be resolved before runtime strings, general collections,
collection-aware generics, SIMD/parallel APIs, and compiler transformations
build on an unspecified sequence model.

### Known assumptions

- An intrinsic array family may use language-provided element/count
  parameterization without establishing general user-defined generics.
- A fixed-shape array owns stable element places for its life path.
- A dynamic collection operation must state which existing element places
  remain, relocate, or end.
- Index and call are postfix delimited forms, not arbitrary bracket
  punctuation.
- Direct indexed read/write/compound behavior may be one operation; no proxy is
  implied unless the selected result really provides one.
- A receiver, every index, and a direct indexed-mutation source evaluate once in
  visible source order.
- Valid element indexes and valid slice/splice endpoints have different upper
  bounds.
- `IndexSize` and `TypeSize` are distinct intent identities even when their
  ranges and representations agree.
- Dynamic allocation obtains all required storage before element constructors
  or initializers execute.
- Source, declaration, import, or traversal order does not silently resolve an
  ambiguity.

### Known inclusions

- Fixed-length array identity, element type, dimensions, length, and
  multidimensional shape/layout at programmer depth.
- Dynamic array identity, logical length, capacity, allocation ownership, and
  growth/reallocation contracts.
- Default, list, construction-packet, repeated, copied, moved, partially
  initialized, and explicitly unsafe element construction where each has a
  demonstrated need.
- Element destruction and partial-construction failure.
- Zero-based indexing, multiple indices, direct indexed read/write/compound
  operations, bounds checks, and result/place/proxy behavior.
- Slice/view identity, range/endpoint source, ownership versus borrowing,
  qualification, lifetime, mutation, overlap, and invalidation.
- Element-place stability under replacement, growth, insertion, removal,
  slicing, and traversal.
- `IndexSize`/`TypeSize` arithmetic, capacity, count-to-byte conversion, and
  overflow.
- Direct and cursor-driven `each` participation.
- Array source versus lambda-capture/source-enclosure ambiguity.
- Array-of-structures versus structure-of-arrays pressure where programmer
  layout, indexing, iteration, or transformation behavior materially differs.
- Diagnostics, costs, formatting, reflection pressure, and source stability.
- Complete disposition of the maintainer notes, focused raw input, and reached
  legacy array/index/slice material.

### Known starting boundaries

- General maps, sets, lists, trees, queues, and application/library collection
  APIs.
- General user-defined generic declarations, deduction, specialization,
  constraints, and generic collection concepts.
- Complete runtime string conversion, mutation, normalization, grapheme,
  presentation, locale, and code-page APIs.
- Complete compiler auto-vectorization, SIMD intrinsic catalogs, MIMD
  scheduling, parallel algorithms, and GPU execution.
- Complete compile-time execution, reflection metadata shape, and generated
  collection families.
- Complete pointer representation, allocator implementation, object-file
  layout, ABI, and lowering architecture.

These boundaries do not erase consequences. Preserve every material pressure in
a current owner or indexed raw destination rather than leaving it only in the
eventual archive of this work item.

### Intentionally unresolved framing

- Exact fixed/dynamic array and slice type syntax.
- Whether dimensions associate inside-out or outside-in and how source teaches
  that order.
- Whether array literals are expressions, construction-only source, or one
  destination-directed family.
- Whether length participates in canonical type identity for every fixed form.
- Exact dynamic-array ownership and whether one built-in dynamic form exists or
  several policy-selected forms.
- Bounds failure as panic, optional/reporting result, static diagnostic, unsafe
  unchecked operation, or a documented family.
- Whether index returns a value, reference, place, or proxy for each array/view
  category.
- Exact range and slice syntax, including inclusive/exclusive endpoints,
  omission, empty/reversed ranges, and multidimensional slicing.
- Whether a slice owns storage, borrows one contiguous region, anchors another
  owner, or has distinct forms for those relationships.
- Which mutations preserve element places and which invalidate views,
  references, pointers, and active traversal.
- How array-of-structures and structure-of-arrays transformations relate to
  current structural mapping without becoming hidden layout conversion.

### Initial stopping guidance

Stop when the work has:

- established a teachable fixed/dynamic array and slice/view mental model;
- defined type identity, dimensions, length, capacity, layout, and
  initialization at programmer depth;
- defined deterministic index/multi-index and direct mutation behavior;
- defined bounds and failure families without hidden fallback;
- defined element place, lifetime, invalidation, and traversal consequences;
- reconciled intrinsic array syntax with source, construction, pointer,
  integer-size, operator, mixfix, iteration, string, and structural constraints;
- assigned generic, runtime-string, reflection, vectorization, parallel,
  collection-library, and implementation consequences to current or indexed
  future owners;
- dispositioned all useful refreshed and reached legacy material; and
- completed the required documentation-fit dry run.

Do not promote findings, archive this work item, begin work item `026`, or
design general generics, collection libraries, runtime string services,
compile-time execution, reflection, vectorization, parallel execution, or
implementation architecture without the separately required discussion,
alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  decision maturity, promotion, deferral, and closure.
- [Arrays and slices maintainer notes](../raw/arrays-and-slices-maintainer-notes.md) -
  will contain the maintainer's refreshed primary input before this work item is
  assigned; read and disposition it completely.
- [Legacy arrays](../../arrays.md) - preserves the principal fixed,
  multidimensional, initialization, dynamic-allocation, slicing, and
  array-overhead use cases to disposition by value.
- [Raw indexing and slicing](../raw/indexing-and-slicing.md) - preserves direct
  index/mixfix, range, capacity, dynamic allocation, bit-place, proxy, traversal,
  and element-stability pressure.
- [Declarations and bindings](../../language/declarations-and-bindings.md) -
  supplies type/declaration, initialization, inference, qualifier, and
  assignment boundaries.
- [Construction, replacement, and destruction](../../language/construction-and-destruction.md) -
  owns construction packets, member/aggregate initialization, partial
  construction, replacement, and destruction.
- [Lifetimes and references](../../language/lifetimes-and-references.md) -
  owns current fixed-array element stability and dynamic collection
  continuation/relocation requirements.
- [Pointers, allocation, and arenas](../../language/pointers-and-arenas.md) -
  owns allocation roots, dynamic storage, pointer roles, arenas, and
  disposition.
- [Iteration](../../language/iteration.md) - owns fixed-array `each` traversal
  and the cursor protocol.
- [Operators](../../language/operators.md), the
  [operator catalog](../../language/operator-catalog.md), and
  [mixfix operators](../../language/mixfix-operators.md) - own index recognition,
  direct indexed mutation, candidate trees, result shapes, and evaluation.
- [Source structure](../../language/source-structure.md) - owns bracket,
  delimiter, comma-list, capture, continuation, and intent boundaries.
- [Integers](../../language/integers.md) - owns `IndexSize`, `TypeSize`,
  associated count/storage identities, conversion, and overflow constraints.

### Consequence-driven

- [Structural shapes and compatibility](../../language/structural-shapes-and-compatibility.md)
  when array shape, flattening, decomposition, recomposition, or
  array-of-structures/structure-of-arrays transformation becomes concrete.
- [Strings and characters](../../language/strings-and-characters.md) and
  [raw string runtime/encoding](../raw/string-runtime-and-encoding.md) when
  arrays, slices, capacity, contiguity, or mutation constrain current/future
  string behavior.
- [Safety and analysis](../../language/safety-and-analysis.md) when bounds,
  unchecked indexing, partial construction, overlap, or invalidation creates a
  safe/unsafe guarantee.
- [Transfer stances](../../language/transfer-stances.md) when element,
  aggregate, slice, or dynamic-storage copy/deep/move/last behavior becomes
  material.
- [Raw type parameters and generics](../raw/type-parameters-and-generics.md)
  when intrinsic array parameterization crosses into user generic declarations,
  constraints, or generated families.
- [Raw pointer and arena mechanics](../raw/pointer-and-arena-mechanics.md) when
  array allocation requires new arena interfaces, allocation records, pointer
  casts, provenance, or control-block mechanics.
- Relevant legacy allocation, construction, casting, string, flow, or operator
  material only when a concrete question cannot be resolved from the refreshed
  notes, required array evidence, and current owners.

### Audit-only

- Archived work only when a concrete provenance or regression question cannot
  be answered from current owners and live raw input.

## Initial working posture

Begin with programmer source that declares one fixed array, initializes its
elements, obtains one element place, takes one bounded slice/view, and traverses
the result. Explain type identity, storage ownership, bounds, and lifetime before
designing dynamic growth policies, generalized ranges, proxy families, or
parallel execution.

Treat the maintainer notes as the latest primary evidence once populated. Legacy
pages remain available for useful unrefreshed cases but do not override the
maintainer notes merely because they contain more text.

## Working record

### Current aligned working model

The findings in this section are aligned for work item `025`. They remain
non-authoritative until a separately authorized promotion incorporates them
into their lasting owners. The superseded initial reconstruction remains below
as historical candidate analysis and must not be read as the current model
where it conflicts with this section.

#### Review entry point

```zax
values : Integer[5] = [ 10, 20, 30, 40, 50 ]

third : Integer writable & = values[2]
middle := values[1..<4]

each element : in values {
  print(element)
}
```

- `values` is one owning fixed array with five live element places.
- `values[2]` designates the third element place. It neither copies merely by
  being indexed nor requires a proxy.
- `middle` is a borrowed view of the element places selected at creation:
  indexes `1`, `2`, and `3`.
- The fixed array's element places remain stable for its life path.
- The slice owns descriptor state but not the viewed elements or their life
  paths.
- Fixed arrays, resizable arrays, and slices support direct increasing-order
  traversal; cursor-driven traversal remains available for explicit progression
  and erasure behavior.

#### Intrinsic array roles and identity

Zax has three intrinsic sequence roles without thereby establishing general
user-defined generics:

1. A **fixed array** is an owning value whose element type and exact length are
   part of its type.
2. A **resizable array** is an owning value whose type records its element type
   and admitted logical-length range. Its current logical length belongs to the
   value.
3. A **slice** is a non-owning sequence view with a runtime length and a
   restricted descriptor over an existing array.

A completed safe array has one live element resident instance at every position
below its current logical length. Reserved storage at or above that length does
not contain an uninitialized or dormant element.

The array family is language-provided parameterization. It does not establish
syntax, specialization, constraints, or other behavior for user-defined generic
types.

#### Declaration ranges and slice ranges

Array declaration ranges range over possible **logical length values**:

```zax
first : Integer[0..3]
// Permitted lengths: 0, 1, 2, 3.

second : Integer[0..<3]
// Permitted lengths: 0, 1, 2.

third : Integer[1 in 0..3]
// Permitted lengths: 0 through 3; current length starts at 1.

fourth : Integer[2 in 0..3]
// Permitted lengths: 0 through 3; current length starts at 2.
```

`N in` supplies an exact initial logical length, not a storage capacity and not
a type-identity component. A compile-time-known invalid `N` is an error; a
runtime-invalid `N` uses the applicable registered panic category unless a
separate reporting operation says otherwise.

An initializer-free array starts at its required minimum length and
default-constructs exactly those elements. When no `N in` is written, an array
expression may determine the initial current length, including from runtime
slice contributions, provided the resulting length lies in the declaration's
range.

The ordinary mathematical consequence remains visible: `Integer[0..<0]`
admits no logical length, while exact `Integer[0]` is a valid zero-length array.

Slice ranges range over **element indexes and boundaries**:

```zax
inclusive := values[0..5]
// Indexes 0 through 5; normalized boundaries [0, 6).

halfOpen := values[0..<5]
// Indexes 0 through 4; normalized boundaries [0, 5).

one := values[0..0]
// One element; normalized boundaries [0, 1).

empty := values[0..<0]
// No elements; normalized boundaries [0, 0).
```

For an array of current length `L`, element indexes range from `0` through
`L - 1`, while slice endpoints may range from `0` through `L`.

Built-in slicing normalizes to half-open boundaries:

```text
[start..<end] -> [start, end)
[start..end]  -> [start, end + 1)
[..<end]      -> [0, end)
[..end]       -> [0, end + 1)
[start..]     -> [start, length)
[..]          -> [0, length)
```

`start..<` is malformed because the exclusion marker has no written endpoint.
An empty half-open range is valid. A statically known reversed or out-of-bounds
range is an error. A runtime-invalid required slice uses its registered panic
category and never silently clamps or produces an empty view.

#### Logical length, reserved capacity, and suggested capacity

The model distinguishes:

- `L`: current logical length;
- `C`: actual reserved element capacity; and
- `S`: an optional programmer-supplied suggested capacity.

They are not alternate names for one property.

```zax
actual : IndexSize = capacity of values
suggested : IndexSize? = suggested capacity of values
```

`capacity of` always produces a concrete `IndexSize`. An array with no backing
storage has capacity zero. Otherwise it reports how many elements can occupy
existing element storage. Physical allocation rounding beyond a bounded
array's usable maximum is not additional reported element capacity.

`suggested capacity of` produces an optional `IndexSize`. When no suggestion
has been supplied, the result is an **absent optional**. The absent optional is
not itself called `Nothing`; `Nothing` is its represented state.

A suggestion operation accepts `IndexSize?`:

```zax
values.suggestCapacity(128)
// The IndexSize constructs a present optional and sets the suggestion.

unset : IndexSize?
values.suggestCapacity(unset)
// The absent optional clears the suggestion.
```

Static min/max length facts may guide an implementation's internal allocation
heuristics but do not manufacture a programmer-visible suggested capacity.
Suggested capacity is not a promise or upper bound and has no required ordering
relationship with actual capacity.

Reservation is different:

```zax
values.reserveCapacity(128)
```

After successful reservation, `capacity of values` is at least `128` until an
operation explicitly allowed to reduce capacity or complete array replacement
changes that fact. Reservation accepts `IndexSize`, not an absent optional.
`trimCapacity()` or another separately aligned operation owns deliberate
capacity reduction.

For finite maximum `M`:

```text
minimum <= L <= C <= M
successful reserveCapacity(N) => C >= N
```

Extra capacity constructs no elements. Capacity change alone performs no
logical insertion or eviction, although obtaining different backing storage may
relocate every continuing element.

An implementation may choose an initial capacity when no reservation was
written. Source cannot rely on that private choice for reference validity. An
operation whose contract does not prove that capacity is sufficient remains
potentially relocating.

#### Array expressions and lambda capture

`[ ... ]` is an array expression. Lambda capture instead uses a contiguous
double-bracket enclosure:

```zax
declared : Integer[5][4]

matrix := [ [ 1, 2 ], [ 3, 4 ] ]

matrix[3][4] = 4

callback := [[ matrix ]] : ()() = {
  // ...
}
```

The intent rules are:

- contiguous `[[` opens a lambda capture;
- `[ [` begins an array containing an array;
- a compact nested-array spelling such as `[[1, 2], [3, 4]]` is a
  confusable-form error rather than an array;
- contiguous `]]` closes a capture only in the applicable capture context;
- nested indexing keeps adjacent `][`; and
- formatting preserves `[ [` and `] ]` for nested array source and
  `[[ ... ]]` for capture.

This avoids consuming `array` as a high-value contextual word and avoids a
C++-style semantic parser hint whose requirements vary with name lookup.

The outer legacy `[{...}]` aggregate wrapper is not required for arrays:

```zax
numbers : Integer[3] = [ 1, 2, 3 ]

items : MyType[3] = [
  [{ "first", 3 }],
  [{ "second", 7 }],
  [{}]
]
```

The outer brackets form an array expression. Each inner `[{...}]` is an
ordinary construction packet for one element, using the array's expected
element type. A packet always requires an expected element type; a packet-only
array expression cannot infer one.

Array inference determines one common concrete element identity from all
entries without source-order preference:

- when every numeric element remains uncommitted, the applicable suggested
  default is used, ordinarily `Integer`;
- one committed concrete identity causes every compatible uncommitted value to
  commit to that identity;
- several equal committed identities establish that identity;
- different committed identities are an error; and
- no implicit union, `Any`, widening, or arbitrary converting constructor is
  introduced to manufacture a common type.

```zax
ordinary := [ 1, 2, 3 ]
// Integer[3] adaptive

bytes : U8[3] flat = [ 1, 2, 3 ]
// The destination commits every element to U8.

small := [ (: U8 = 1), 2 ]
// U8[2]; the uncommitted 2 fits the one committed identity.

bad := [ myU8, myU16 ]
// error: the committed identities differ

empty := []
// error: no element type can be inferred

typedEmpty : Integer[0] = []
```

Omitted array layout resolves to `adaptive`; `flat` remains explicit.

Nested array expressions infer recursively when their exact inner types agree:

```zax
matrix := [ [ 1, 2 ], [ 3, 4 ] ]
// Integer[2][2] adaptive
```

Unequal inferred row shapes do not silently invent a resizable common row type:

```zax
jagged := [ [ 1, 2 ], [ 3 ] ]
// error: jagged inference requires an explicit type

jagged : Integer[2][1..2] = [
  [ 1, 2 ],
  [ 3 ]
]
```

The explicit declaration proves that resizable inner rows are intended. A
readonly path would only restrict one access path; it would neither make the
different fixed row identities equal nor establish a runtime-fixed row family.
Runtime-fixed owning arrays remain a deferred concept, not an inferred
substitute.

Every ordinary array-expression entry contributes one value. An unsliced array
therefore remains one array element and never decomposes implicitly:

```zax
firstRow : Integer[3]
secondRow : Integer[3]

rows := [ firstRow, secondRow ]
// Integer[2][3]

flatValues := [ firstRow[..], secondRow[..] ]
// Integer[6]; each written slice contributes its selected elements.

invalid : Integer[6] = [ firstRow, secondRow ]
// error: two Integer[3] values cannot initialize six Integer elements
```

A written slice expression is the explicit sequence-contribution form. A named
slice can remain one slice element; slicing it again marks expansion:

```zax
view := firstRow[..]
views := [ view ]
expanded := [ view[..] ]
```

When every contribution count is statically known, the inferred array has an
exact fixed length. A runtime-sized slice contribution explicitly requests a
runtime-sized owning array and infers the narrowest evident open lower bound:

```zax
runtimeValues := [ makeRuntimeSlice()[..] ]
// T[0..] adaptive

withOne := [ makeRuntimeSlice()[..], makeValue() ]
// T[1..] adaptive
```

This runtime expansion supplies sufficient intent for resizable inference. It
does not create a runtime-dependent fixed type.

#### Exact initializer cardinality and runtime source sizing

An exact current length requires an exact total contribution:

```zax
source : Integer[3] = [ 1, 2, 3 ]

fixed : Integer[2] = [ source[..] ]
// error: three contributed elements cannot initialize exactly two
```

There is no implicit prefix truncation:

```zax
alsoFixed : Integer[3] = [ source[..], 77 ]
// error: four contributed elements cannot initialize exactly three
```

For a declaration without an exact current length, the contribution total
becomes the current length when it satisfies the declared range:

```zax
bounded : Integer[0..5] = [ source[..] ]
// Current length is 3.
```

When runtime slices contribute elements:

```zax
destination : Item[0..100] = [
  makeRuntimeSlice()[..]
]
```

array construction:

1. evaluates every array-source expression once from left to right;
2. retains each produced value or slice descriptor;
3. calculates and checks the complete contribution count;
4. requests all required destination backing storage;
5. constructs destination elements in increasing index order; and
6. publishes only the complete array.

If the runtime total is outside `0..100`, source evaluation has occurred but no
destination element has become live. The registered cardinality panic blocks
the operation unless it is repaired; otherwise the process crashes gracefully.

An explicit runtime `N in` remains useful but is not required:

```zax
source := makeRuntimeSlice()

destination :
  Item[source.length in 0..100] = [ source[..] ]
```

Here the source must contribute exactly `source.length`. Storage still exists
before destination element construction; only source expressions needed to
discover the count run before the backing request.

Array expressions may also consume arbitrary iterable sources explicitly:

```zax
values : Integer[10] = [
  from makeMeRepeatIterable(0, 10)
]
```

Contextual `from` in an array-expression entry means to consume the source
through the current `each from` cursor protocol. The explicit marker is required
because a bare iterable, including a bare array, remains one ordinary element.
Written slices retain their compact `source[..]` expansion.

An iterable type may optionally declare an exact non-consuming count:

```zax
operator post unary 'count' final : (
  result : IndexSize
)() readonly = {
}
```

“Optional” describes availability of the operation; its result is a concrete
`IndexSize`, not an optional value. When present, `source count` promises the
exact number of values produced by the corresponding traversal. Array
construction invokes it once and may reserve the complete required storage
before traversal. A mismatch between the promised and produced count enters a
registered iterable-count panic. A source that knows only a possible or
estimated count does not provide this exact operation; a weaker
suggested-count facility remains deferred.

Without exact count:

- a fixed destination already knows its required storage and verifies exact
  exhaustion;
- a bounded destination grows while consuming entries and checks its maximum;
- an inferred result receives the applicable open-ended adaptive type; and
- explicit reservation or suggested capacity can avoid repeated growth.

Array construction evaluates and retains all ordinary entry and iterable-source
expressions once in source order, queries available exact counts, obtains
storage when the complete count is known, and then consumes sequence sources in
array-entry order.

Final bare `..` remains a default-construction remainder marker:

```zax
values : Item[5] = [
  makeFirst(),
  makeSecond(),
  ..
]
```

It is available only as the final entry when an exact target count exists and
independently default-constructs every remaining element. It cannot infer a
larger length. An iterable before `..` contributes until exhaustion; excess
values fail cardinality, while early exhaustion leaves the remainder to
default construction.

Repeated values use ordinary iterable facilities rather than array-specific
`repeated` syntax. A repeat iterable defines whether it evaluates or captures
its source once and exposes exact `count` when applicable.

#### Whole-array `unsafe ???` and delayed construction

Ordinary arrays never contain live-but-uninitialized elements. Reserved capacity
is not initialized using `???`, and `unsafe ???` is not an aggregate tail
filler.

```zax
rawArray : Item[5] = unsafe ???
```

This bypasses initialization of the complete array value and all its storage
under the general unsafe construction contract. The programmer assumes
responsibility for reaching a complete destruction-valid state.

Delayed construction uses an ordinary array value and an ordinary constructor
call:

```zax
rawArray.+++([
  makeFirst(),
  makeSecond(),
  makeThird(),
  makeFourth(),
  makeFifth()
])
```

The bracket expression first constructs an `Item[5]` temporary. The array's
ordinary same-array construction then consumes it, potentially through the
temporary's inherent `last` stance. This is not a variadic special array
constructor form.

```zax
rawArray.+++()
```

is available only when every element required by the array can be
default-constructed.

#### Multidimensional arrays and flat layout

Dimension declaration order matches chained index order:

```zax
matrix : Integer[4][3]
element := matrix[row][column]
```

This is four rows of three integers. `matrix[row]` is one `Integer[3]` row.
Chained indexing with adjacent `][` is preferred:

```zax
element := matrix[row][column]
```

Each bracket contains one index. `matrix[row, column]` is not supported.
Custom multidimensional behavior likewise chains index operations or defines a
mixfix over several adjacent `index 1` components.

`flat` is a static storage guarantee. A one-dimensional flat array stores its
current elements in one contiguous region in increasing index order. A flat
multidimensional array is rectangular, contiguous, row-major, and varies the
rightmost dimension fastest, matching built-in C/C++ array layout:

```zax
rows : Integer[0..100][3] flat
// Resizable outer row count, exact inner width.

ragged : Integer[4][1..3] flat
// error: independently resizable inner rows cannot satisfy flat rectangular
// layout
```

Every inner dimension of a flat multidimensional array has an exact length.
The outermost dimension may resize.

`flat` does not prohibit resizing. In-capacity outer growth appends complete
fixed-size rows. Growth requiring a larger contiguous region relocates
continuing elements. The explicit mirror is `adaptive`: it makes no static
contiguity promise and does not require the actual representation to be
segmented.

Adaptive multidimensional arrays may be explicitly jagged:

```zax
rows : Integer[4][1..10] adaptive
```

Each row independently owns its current length, capacity, suggested capacity,
and backing strategy. Outer indexing first selects one row, and indexing or
slicing that row then applies its own current bounds:

```zax
selectedRows := rows[1..<3]
selectedColumns := rows[row][1..<3]
```

Initial intrinsic multidimensional slicing handles one dimension per bracket.
`matrix[rowRange, columnRange]` does not yet create a rectangular or strided
block view; that operation needs a distinct multidimensional-view identity,
layout, overlap, lifetime, and cost model and is explicitly deferred.

#### Qualifier layers and slice identity

Array and slice syntax must preserve separate inner and outer qualification:

```text
T <element/type qualifiers> [] <slice qualifiers>
```

The qualifiers before `[]` describe the viewed element layer. The qualifiers
after `[]` describe the slice descriptor. Creating a slice preserves or narrows
source authority and never adds element mutability, writability, replacement
permission, or lifetime.

A slice:

- borrows its source element places;
- owns mutable or immutable descriptor state according to its slice-layer
  qualifications;
- never extends the source array's life path;
- can be sliced again to create an equal or narrower view;
- can never expand beyond its currently authorized view;
- can be copied without copying elements; and
- can be referenced so descriptor narrowing affects the original descriptor
  rather than only a copy.

An already obtained element reference remains bound to its selected element
place even if a separately mutable slice descriptor is narrowed afterward.

Slices denote the element places selected when the slice is created, not
“whatever later occupies the same numeric indexes.” They do not silently adjust
to structural mutation:

- an operation whose contract may reallocate backing invalidates every slice;
- an insertion or removal invalidates slices intersecting or following its
  first affected position;
- a slice entirely before an insertion or removal may survive when the
  operation proves that no backing relocation occurs; and
- an insertion after a slice preserves it when relocation is impossible.

An invalidated slice does not become empty, clamp itself, retarget current
indexes, or revive after later growth. A statically known use is an error. A
dynamically detected use enters the registered slice-invalidation panic.
Disabling that category makes a violated validity promise undefined.

#### Splice overloads and direct splice assignment

Bracket source containing a range selects a distinct splice form rather than an
ordinary element-index form. A custom declaration uses `operator splice 1`;
each bracket contributes one sliced dimension.

One-dimensional semantic inputs preserve omitted endpoints and normalize a
written endpoint to an exclusive end:

```zax
operator splice 1 final : (
  result : MyCustomView
)(
  start : IndexSize?,
  endExclusive : IndexSize?
) readonly = {
}
```

The mapping is:

```text
[..5]   -> absent start, present 6
[..<5]  -> absent start, present 5
[0..<0] -> present 0, present 0
[0..]   -> present 0, absent end
[..]    -> absent start, absent end
```

The words “absent start” and “absent end” mean absent optional arguments. A
custom splice may return any declared type; it need not return `MyType[]`.
Intrinsic array splice returns the applicable borrowed slice.

A slice expression is passive:

```zax
slice := myArray[2..4]
```

Direct splice assignment is a separate complete mixfix:

```zax
myArray[3..<3] = [ 5, 10, 15 ] // insert
myArray[2..4] = []             // remove
myArray[2..4] = [ 20, 30 ]     // replace and change length
```

The direct mixfix consumes the array receiver, range, `=`, and replacement. It
does not create a temporary slice or proxy. Therefore:

```zax
slice := myArray[3..<3]
slice = [ 5, 10, 15 ]
// error: this does not insert into myArray
```

A callable may accept a still-current same-array slice as a structural
mutation designator:

```zax
selected := myArray[2..4]
myArray.remove(selected)
```

The operation verifies origin and current validity, captures the represented
interval, performs the removal, and invalidates affected views.

The slice must originate from the receiver's exact underlying array place:

```zax
selected := firstArray[2..<5]
secondArray.remove(selected)
// error when the distinct origins are statically known
```

A runtime or opaque foreign-origin mismatch enters a registered panic before
mutation. Disabling that category makes a false same-origin promise undefined.
The operation never removes equal values or reuses a foreign slice's numeric
range against the receiver. A slice of a slice retains its original array
origin and remains eligible while valid. An empty valid same-array slice removes
nothing.

Self-slices may supply joining or insertion:

```zax
myArray <+>= myArray[2..4]
myArray[3..<3] = myArray[5..6]
```

The operation uses source values from before structural mutation. Duplicating
elements that remain in the array requires `copy`; relocation of continuing old
elements may independently use `last`. A noncopyable element cannot be
duplicated merely because source and destination are the same array.

#### Element indexing, proxies, and direct mutation

Successful intrinsic array or slice indexing designates an element place with
qualifications and lifetime derived from the base access path:

```zax
value := array[index]
view : Item readonly & = array[index]
array[index] = replacement
array[index] ^= mask
```

The index operation itself need not copy and creates no proxy. A value context
may copy from the selected place; a reference context binds to it.

A custom `operator index` may return any declared value, reference, or explicit
proxy. Direct custom indexed mutation requires a matching mixfix when indexed
read does not return an assignable place or real proxy. Punctuation grants no
special mutation authority.

Integer bit indexing remains a distinct case. It may support direct read,
write, and compound operations without making a bit an addressable
`Boolean &`. A lasting proxy, if later introduced, needs its own identity,
lifetime, qualification, and cost contract.

#### Direct-mixfix evaluation and bounds

For:

```zax
array[chooseIndex()] = makeReplacement()
```

the predictable runtime order is:

1. evaluate and bind the array receiver;
2. evaluate and bind each index in visible source order;
3. evaluate and bind `makeReplacement()`;
4. invoke the selected direct mixfix;
5. perform its runtime bounds check; and
6. mutate or enter the registered bounds panic.

The right-hand side therefore runs before a runtime bounds panic. A statically
known invalid index is a compile-time error, so no runtime expression executes.

For chained multidimensional indexing, each inner index operation necessarily
completes to produce the receiver for the next bracket:

```zax
matrix[row][column] = replacement
```

The row selection precedes evaluation of `column`. The final direct mutation
still follows the applicable mixfix-hole order.

A matching panic helper resumes only when it can repair the blocked condition.
Some resizable-array bounds failures may be repairable; an impossible fixed
array index ordinarily is not. There is no unwinding or alternate result.

#### Joining, structural mutation, and relocation

`<+>` is the nonmutating array join family, and `<+>=` is its direct mutating
compound:

```zax
combined := left <+> right
left <+>= right
left <+>= [ value ]
```

The right operand is a sequence. `[value]` is a one-element sequence, which
keeps arrays of arrays unambiguous. Intrinsic arrays receive no built-in `+`
concatenation. `<+>=` must be added to the recognized operator catalog during
promotion.

Exact `<+>` result identity, static bound derivation, transfer behavior,
allocation failure, and joining between fixed, bounded, dynamic, and slice
operands follow the whole-array transfer model below.

The aligned mutation roles are:

- reserve or trim actual capacity without changing logical length;
- set or clear suggested capacity without a behavioral guarantee;
- grow, shrink, or resize logical length;
- append a sequence with `<+>=`;
- insert, remove, or replace through direct splice assignment; and
- remove through a current same-array slice when the callable form is useful.

The required callable surface is resultless:

```zax
values.reserveCapacity(count)
values.suggestCapacity(optionalCount)
values.trimCapacity()
values.grow(length)
values.shrink(length)
values.resize(length)
values.remove(slice)
```

- `suggestCapacity` changes optional metadata and performs no immediate
  allocation or relocation.
- `reserveCapacity` guarantees capacity on normal completion and may relocate.
- `trimCapacity` may relocate but never changes logical length.
- `grow` default-constructs a suffix and is a no-op when the requested length
  is not larger.
- `shrink` destroys a suffix and is a no-op when the requested length is not
  smaller.
- `resize` selects the applicable grow or shrink behavior.
- `remove` consumes a valid same-array interval designator and removes those
  elements.

An unavailable required constructor or transfer makes the operation
unavailable. A statically invalid request is an error; a runtime-invalid
required request uses its registered panic. Optional/reporting variants are
separate operations rather than alternate result shapes of these required
callables.

Relocation into fresh storage is construction, not assignment. Each old
continuing element is offered as `last` to construction of its fresh successor;
normal `last -> move -> copy` fallback applies. The old terminal element remains
destruction-valid and is destroyed exactly once.

Shifting into an already-live slot instead selects assignment or reconstructive
replacement with the applicable stance. If the required transfer or replacement
is unavailable, the structural operation is unavailable.

The initial stability contract is:

- in-place content mutation preserves the element place;
- complete element replacement may renew the resident instance at that place;
- in-capacity suffix growth preserves existing element places and constructs
  new paths in increasing order;
- backing reallocation relocates every element and invalidates ordinary
  references, slices, raw pointers, and cursors to old places;
- suffix shrink destroys removed elements in reverse positional order and
  preserves the unaffected prefix;
- insertion moves or renews the affected suffix without logically evicting its
  continuing values;
- removal evicts selected elements and may move or renew the suffix; and
- capacity change alone evicts no logical elements but may relocate all of them.

Flat storage must use the contiguous behavior above. Adaptive storage may
provide stronger stability only when its static operation contract exposes that
guarantee; implementation choice cannot silently change source validity.

#### Whole-array transfer and join result identity

An owning-array `copy` constructs independent backing storage and independent
element places. Each destination element uses the corresponding source
element's ordinary `copy`. This is structurally independent array ownership even
when an element's own `copy` legitimately shares an internal resource.

`deep` likewise creates independent array storage and requires every element's
exact `deep` operation. It has no fallback to ordinary `copy`.

A fixed array cannot transfer its inline outer storage. Its `move` and `last`
forms transfer elements individually:

- `move` leaves each source element in its declared moved-from state and leaves
  the complete source array live; and
- `last` may terminally transfer each source element while leaving the source
  array destruction-valid.

A dynamically backed owning array may transfer its complete backing allocation
when source and destination storage contracts permit:

- `move` may steal backing only when it can leave the source as a valid live
  moved-from array under the source's minimum-length and element contracts;
- otherwise it moves elements individually or falls back to `copy`; and
- `last` may transfer backing while leaving only the source descriptor state
  required for one correct destruction.

Slice transfer applies to the slice descriptor itself. Copying, moving, or
terminally transferring a slice never implicitly transfers its borrowed
elements. An owning-array consumer of a slice separately states how it
constructs destination elements.

Array `<+>` defaults to preserving both operands through `copy`:

```zax
combined := left <+> right
reused := left as last <+> right
```

An explicit receiver or right-operand stance may select a stronger transfer
variant. A last-stanced dynamically backed left operand may donate suitable
backing capacity to the result. A right self-slice whose elements remain in the
left prefix still requires element `copy`; it cannot terminally transfer those
same continuing source elements.

The result's current length is the checked sum of operand current lengths.
Static result bounds are the checked sum of operand bounds:

```zax
Integer[3] <+> Integer[2]
// Integer[5]

Integer[1..4] <+> Integer[2..<5]
// Integer[3..8]
```

An open input maximum produces an open result maximum. If the minimum sum itself
cannot fit `IndexSize`, the operation is statically unavailable. A runtime
current-length sum that does not fit enters the registered extent/count panic.

The left operand supplies the result's element and storage family:

- flat left produces a flat result;
- adaptive left produces an adaptive result; and
- a slice left produces a new owning array with the slice's statically exposed
  flat/adaptive guarantee.

The right source layout need not match because joining constructs destination
elements. Suggested capacity does not propagate into the new result. Actual
result capacity is at least its current result length.

`<+>=` preserves the left array's existing type and bounds:

```zax
resizable <+>= source
fixed <+>= source
// unavailable when source can contribute any element
```

The new current length must fit the left bounds. Exact fixed arrays do not gain
a vacuous mutating join family merely for a provably empty source.

#### Direct and cursor traversal

All intrinsic fixed arrays, resizable arrays, and slices support direct
`each in`:

```zax
each element : in values {
  use(element)
}
```

The source evaluates once, entries are visited in increasing index order, and
an omitted binding type creates a fresh reference to each current element place.
An explicit value binding performs ordinary element construction.

Element mutation during direct traversal is available when qualifications allow
it and the current element place remains. Structural mutation that may change
length, positions, backing, or viewed places invalidates direct traversal
rather than silently changing which entries it visits.

Intrinsic arrays and slices may also participate in `each from` through the
current cursor protocol. Cursor-driven traversal owns controlled erasure,
specialized progression, reverse or filtered order, and any stronger
invalidation behavior. Supporting `each in` does not make the cursor or its
cost implicit.

#### Bounded array references

A bounded array reference may adapt an array with different underlying length
capabilities:

```zax
change final : ()(
  values : Integer[0..10] &
) = {
  values.resize(10)
}

fixed : Integer[3]
change(fixed)
```

At binding:

- the source's current length must satisfy the parameter's admitted range;
- the reference retains the underlying array's actual fixed or resizable
  bounds and layout capabilities; and
- the parameter's written range supplies the operation envelope visible in the
  body.

Every length-changing operation checks both the parameter envelope and the
underlying array capability. In the example, `10` fits the parameter but not
the fixed source, so the resize enters its registered panic. Disabling that
panic category makes violation undefined.

A locally evident contradiction is a mandatory compile-time error:

```zax
fixed.resize(10) // error
```

Through an accepted widened parameter, the portable contract retains its
runtime check unless the selected language-analysis contract specifically
requires an interprocedural proof. One compiler's private optimization must not
make source validity vary.

The adaptation may carry bounds or capability metadata and therefore has a
programmer-visible cost compared with an exact ordinary reference.

A by-value `T[]` parameter copies only a slice descriptor. A `T[] &` parameter
refers to descriptor state and may receive a call-duration temporary descriptor
made from an array.

A by-value bounded owning-array parameter constructs one independent local
array:

```zax
consume final : ()(
  values : Integer[2..10]
) = {
}
```

- The source's current length must fit `2..10`.
- A statically impossible mapping is an error.
- Runtime-dependent fit uses the registered cardinality panic.
- The source's offered whole-array transfer stance constructs the parameter.
- The completed local parameter may resize within its own declared bounds.
- A flat by-value destination may construct from adaptive source storage,
  paying the required allocation and element-transfer cost.
- A flat reference parameter instead requires an actually flat source because
  no destination storage is constructed.

#### Universal panic registry and mandatory array checks

Every defined panic condition participates in a universal registry rather than
using feature-local ad hoc controls:

- each condition has one stable category;
- language and programmer/library categories use collision-free ownership;
- each category can be independently enabled or disabled;
- disabling authorizes the compiler to assume the condition never occurs and
  omit its runtime check;
- violating that promise has undefined behavior;
- a statically proved violation remains a compile-time error even when its
  runtime panic category is disabled; and
- optional/reporting operations retain their defined checks and result shapes.

Array work requires distinct registered pressure for:

- element index outside bounds;
- slice endpoint or order outside bounds;
- requested logical length outside its declared range;
- initializer cardinality mismatch;
- invalidated slice use;
- foreign slice origin supplied to an array mutation;
- iterable exact-count promise mismatch;
- storage-extent overflow; and
- backing allocation failure where a reporting form was not selected.

Exact category identifiers remain panic-design work. During promotion, inspect
and update the indexed raw panic design-pressure document so the universal
registry and these array entries exist there. This is an explicit promotion
obligation, not authorization to update that raw document now.

Intrinsic arrays define a closed mandatory static-check list. It includes:

- a constant element index outside known length;
- a constant slice endpoint outside known bounds;
- a reversed constant range;
- fixed initializer cardinality mismatch;
- a constant requested length outside declared bounds;
- impossible fixed-array resize;
- capacity request beyond a finite maximum;
- a slice whose distinct foreign array origin is statically known; and
- statically overflowing multidimensional extent.

Custom types receive no inferred array semantics merely because they overload
index or splice. Their declarations and bodies explicitly select any validation
and panic categories they provide.

#### Size, allocation, and lifecycle constraints

Logical lengths, capacities, indexes, and endpoints use the applicable memory
domain's `IndexSize`. Backing allocation extent uses `TypeSize`. Their declared
equal-range bridge does not make their identities interchangeable.

Count multiplication, multidimensional extent, alignment, metadata addition,
and conversion to byte size are checked before an arena request. No failure
silently widens to another near, ordinary, or far domain.

A resizable owning array may own one backing allocation root. No element is an
independently dispositionable allocation root. Omitted backing arenas come from
the current `___` execution context at the operation requesting storage.
Allocating a pointer to an array with `@` remains a distinct declaration-bound
outer allocation.

When the destination length is exact or every sequence source supplies an exact
count, all required final destination backing storage exists before destination
element construction begins. An uncounted iterable necessarily permits
incremental growth: each backing expansion completes before construction of the
next element, and relocation of earlier elements follows the ordinary `last`
transfer contract.

Neither path publishes a partially live array. Ordinary construction proceeds
in increasing element order; array destruction and suffix eviction proceed in
reverse positional order. A panic during unknown-count traversal blocks the
still-unpublished construction rather than returning the constructed prefix.

Exact reporting behavior for extent overflow and backing allocation failure
uses operations distinct from required panic-producing construction. `@!`
continues to report only its own allocation request and does not automatically
change array-value construction into an optional result.

#### Reporting and unchecked operation families

Required source remains compact:

```zax
element := values[index]
view := values[start..<end]
```

Separately named optional/reporting operations retain one static result shape
regardless of whether a particular value is compile-time known. Representative
callable shapes are:

```zax
element : Item & ? = values.elementAt(index)
view : Item[]? = values.viewWithin(start, end)
```

Exact final names belong to promotion/source integration, but the operation
families remain distinct from required indexing and splicing. Reporting
capacity, extent, resize, and allocation forms likewise expose their failure
through their declared result rather than changing according to compiler
knowledge.

Unchecked required behavior normally comes from a local or wider explicit
disablement of the corresponding registered panic category. It does not need a
second bracket punctuation family. The compiler may then omit the check and
assume its condition impossible; violation is undefined. Optional/reporting
operations still perform the check needed to produce their result even when the
required-operation panic category is disabled.

#### Structural and reflection consequences

Arrays are atomic structural leaves by default. Ordinary direct or flattened
shape processing does not expand array elements, make runtime length part of a
record shape, or turn a large fixed array into hundreds of implicit structural
members.

Array-of-structures to structure-of-arrays conversion is an explicit
transformation. It may reorder or gather storage, allocate, construct
destination elements, invoke transfer operations, and require overlap handling.
It is not an implicit `as shape`, `as layout`, or compiler optimization.

Future reflection must distinguish type facts from value facts.

Type-level facts include:

- element type and complete element qualifications;
- rank and each dimension's exact or ranged bounds;
- fixed or resizable role;
- flat or adaptive storage guarantee;
- slice descriptor and element-layer qualifications; and
- source-explicit versus defaulted properties where stability requires that
  provenance.

Value-level facts include:

- current logical length for every applicable dimension;
- actual capacity;
- absent or present suggested capacity; and
- current operation availability or layout state when a reflected API can state
  it without turning implementation detail into a language promise.

Reflection does not expose a physical pointer merely because storage is flat.
Raw contiguous access remains a separate lifetime- and qualification-bearing
operation.

### Storage-strategy refinement and final source clarifications

This section records later aligned findings and supersedes earlier
`flat`/`adaptive`, automatic slice-expansion, capacity, storage-profile,
reference-representation, and related reflection wording in the current aligned
model above.

#### Conceptual common element type

Array inference does not invent an implicit union or a conceptual “any” type.
No source spelling such as `Any`, `Variant`, or `Union` is established by this
finding.

#### Every sequence expansion writes `from`

Every ordinary array-expression entry contributes one value, including an
array or slice value. Contextual `from` is required whenever the entries of an
array, slice, or arbitrary iterable should fill the surrounding array:

```zax
firstRow : Integer[3]
secondRow : Integer[3]

rows := [ firstRow, secondRow ]
// Integer[2][3]

slices := [ firstRow[..], secondRow[..] ]
// Two slice values.

values := [ from firstRow, from secondRow ]
// Integer[6]

alsoValues := [ from firstRow[..], from secondRow[..] ]
// Integer[6]; the full slices are redundant but explicit.
```

`from` means sequence expansion through intrinsic direct traversal or the cursor
protocol. It does not replace structural `>-` decomposition.

#### Open and empty declaration ranges

`T[0..]` and `T[..]` permit the same lengths from zero upward. Both resolve to
the same array identity. Current teaching prefers `T[0..]` because it exposes
the minimum.

```zax
onlyEmpty : Integer[0..0]
// Current and only permitted length is zero.

impossible : Integer[0..<0]
// error: the half-open range contains no permitted length
```

`T[0..<0]` is not a zero-length array type. A value declaration using an empty
length set is invalid. Exact `T[0]` or inclusive `T[0..0]` expresses a
zero-element array.

#### Counting before construction

Array construction evaluates and retains every written entry expression first,
then queries every available exact iterable count before constructing
destination elements:

```zax
myValues : Integer[0..] = [
  mutateThenReturnCurrentCount(myIterable1),
  from myIterable1,
  mutateThenReturnCurrentCount(myIterable2),
  from myIterable2
]
```

If `myIterable1` supplies exact `count` and `myIterable2` does not, construction:

1. evaluates both mutation expressions and captures both iterable sources in
   source order;
2. asks `myIterable1` for its post-mutation exact count;
3. reserves the known minimum from the two ordinary element entries plus that
   exact count;
4. constructs and consumes entries in written order; and
5. grows incrementally while consuming the uncounted second iterable.

One uncounted source prevents exact final reservation but does not discard known
minimum information.

#### Length and type-bound queries

Arrays and slices use a callable for current length because property getters are
not established:

```zax
current := myValues.length()
```

The declared type bounds are separately observable. Exact source syntax remains
type-reflection integration, but the semantic results are:

- minimum length is concrete `IndexSize`;
- maximum length is `IndexSize?`;
- exact `T[N]` returns minimum `N` and present maximum `N`;
- a bounded range returns its declared minimum and present maximum; and
- an open range returns its declared minimum and an absent maximum.

`size of T` remains unrelated to these logical length queries.

#### Usable capacity and physical backing

Array-level capacity is the number of elements the array could hold in its
current backing without obtaining different element storage while respecting
its declared maximum. It is **usable capacity**, not the full physical byte
extent owned by the storage provider.

A chunked provider may physically reserve 128 element-sized slots for an array
whose maximum is 100. `capacity of` reports 100. Provider-specific reflection
may report the larger physical backing, but ordinary array capacity does not.

`reserveCapacity(N)` requests usable capacity. A known `N` above the declared
maximum is an error; a runtime value above it enters the registered panic. A
provider may internally allocate more without violating the array contract.

Likewise, suggested capacity describes a likely logical array size. A known
suggestion above the maximum is an error and a runtime value panics. The
provider may expose a separate physical-backing hint if it has such a concept;
the array suggestion is not silently clamped or reinterpreted.

Shrinking changes logical length only. It does not trim backing. A complete
`last` transfer may carry physical backing larger than the destination's usable
maximum when the storage contracts are compatible. The destination reports its
own capped usable capacity, and a later compatible transfer to a wider array
may expose the retained physical room again. `trimCapacity()` is unnecessary
before such a transfer unless physical release is intended.

#### Storage providers and unique array handles

Array storage is a strategy/provider relationship, not a built-in
`flat`/`adaptive` choice.

One storage provider may be shared by many arrays. Each array retains:

- strong participation keeping its provider instance alive; and
- one unique handle identifying that array's independently owned raw backing
  region.

The provider owns:

- raw bytes;
- slot/chunk organization;
- gross size and alignment satisfaction;
- logical-slot-to-physical-byte mapping through the array's unique handle;
- physical and usable backing extent;
- raw backing acquisition/release; and
- storage stability tokens and versions.

The array owns:

- element type and logical index bounds;
- which logical slots contain live elements;
- construction, replacement, transfer, and destruction;
- interpretation of raw slots as typed element places;
- qualification and source operation behavior; and
- the programmer-visible stability and invalidation contract.

For indexing, the array validates the logical index and asks the storage handle
to locate a suitably sized/aligned raw slot or chunk. The storage does not know
the Zax element identity or whether that slot contains a live value.

For insertion, growth, and relocation, storage supplies fresh slots, chunks, or
a relocation plan. The array performs element lifecycle operations; storage
does not blindly move bytes containing unknown live values.

#### Slice stability cooperation

Array and storage jointly determine whether an operation invalidates a slice.
Future storage-contract work should preserve the aligned stability
kind/token-plus-version direction:

- a slice retains its required stability kind or token and observed version;
- the storage reports which physical guarantees changed;
- the array maps those changes to its language-level place guarantees and
  advances the applicable versions; and
- checked slice use compares retained and current stability information,
  entering the slice-invalidation panic on mismatch.

The raw design must not commit prematurely to exactly two machine fields. A
slice spanning independently stable chunks may need one composite token,
several tokens, range generations, or conservative whole-array invalidation.
The storage may provide finer preservation, but it cannot claim that an ended
or moved element place remained.

#### Storage declaration profile

Illustrative storage clauses preserve two independent requests:

```zax
provider : MyStorage * strong

byInstance :
  Integer[5 in 3..1000] storage provider

byType :
  Integer[50] storage type MyStorage

byInstanceAndType :
  Integer[50] storage provider type MyStorage
```

Supplying an instance requires that exact provider. Its concrete type is
inferred, so the explicit storage type is optional compatible restatement.

Supplying only a storage type obtains or creates a suitable provider according
to that type's default/context contract. This does not require one newly
allocated provider per array.

Storage does not change logical array identity, but it is part of the resolved
storage profile and may change the owning value's self-contained
representation, operation capabilities, costs, and layout.

#### Storage-erased array references

An unconstrained array reference accepts every compatible storage profile:

```zax
func final : ()(
  myValues : Integer[5] &
) = {
  myValues[2] = 55
}

a : Integer[5]
b : Integer[5] storage provider
c : Integer[5] storage provider type MyStorage

func(a)
func(b)
func(c)
```

All calls invoke the same concrete `func` implementation. The parameter is a
fixed storage-erased array-reference contract carrying enough information to
reach the original array, invoke its storage operations, preserve bounds and
qualification, and locate element slots.

The owning layouts need not be byte-for-byte identical. The caller supplies an
operation-compatible reference view. Its implementation may use a tag,
operation table, generated thunk, or another mechanism; none is a language
representation promise.

Binding this reference copies no elements and allocates no second array, but the
reference may be wider and its operations may be indirect. An exact
storage-qualified reference may permit more specialized provider operations.

An unconstrained reference guarantees ordinary array behavior, not contiguous
physical storage or provider-specific APIs.

#### By-value storage selection

A by-value parameter constructs an independent local array. When it names a
provider instance, that local array uses the specified provider regardless of
the source storage. When it names only a storage type, the local uses a provider
of that type.

With no explicit destination provider, ordinary construction may reuse the
source provider:

- `copy` may retain the strong provider and request another unique handle;
- `deep` may share the provider only when distinct backing and element deep
  behavior preserve independence;
- `move` may transfer a handle only when the source remains a valid moved-from
  array; and
- `last` may transfer the complete handle when the destination accepts it.

A reference parameter never obtains replacement storage from `___`; it aliases
the caller's existing array and preserves or checks that source provider.

#### Full-array and range transfer

A complete-array `last` may transfer only the strong provider participation and
unique backing handle when source and destination storage contracts are
compatible. No element relocation is then required.

A passive slice owns neither the elements nor the unique backing handle.
`slice as last` transfers only its window description. Storage-assisted transfer
of a range requires a consuming operation rooted in the owning source array.
Such a future extraction/splice operation may detach complete chunks, remove
the elements from the source, update indexing and stability versions, or fall
back to elementwise transfer.

#### `size of` values and references

`size of T` reports only the bytes required by one self-contained `T` value. It
does not include external provider backing, shared storage, or transitively
owned allocations. Zax has no current `full storage size of` operation.

```zax
size of Integer[5]
size of Integer[5] &
```

These are different:

- the first reports the resolved self-contained owning representation for
  creating an `Integer[5]` value;
- the second reports the representation of a reference to such an array.

If reflection strips the reference layer, the resulting logical `Integer[5]`
type reports the size of a newly created value under that resolved type
profile. It cannot recover the actual owning representation erased by an
already supplied unconstrained reference. Reflection must distinguish logical
array type, resolved owner storage profile, erased reference profile, and
self-contained size.

#### Default provider factories and `once`

A storage type may own a receiverless factory:

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

`unbound` makes the factory callable through `MyStorage` without an instance.
`once` on a function would not memoize the result. Persistent shared provider
state instead belongs to a future type-owned `once` value, `___`, a global
owner, or another explicit lifetime contract.

The factory/storage contract must promise whether repeated calls return the
same provider. Merely returning `MyStorage * strong` does not establish shared
identity.

#### Provider and handle access

When the exact storage type is present, the array may safely expose its provider
and unique handle under their declared types and qualifications.

When the provider type is erased, the common storage contract may expose an
unsafe raw pointer to the provider object. A programmer may use ordinary
pointer `unsafe cast` to assert a concrete provider type. The raw pointer owns no
provider lifetime, exposes no element pointer, and provides no contiguity
guarantee.

Unsafe authority cannot manufacture one contiguous element span from linked,
tree, bucketed, or otherwise discontiguous backing.

#### Changing storage

A future explicit storage-change operation:

1. verifies the target provider supports the array's shape and required
   capabilities;
2. obtains target raw slots;
3. constructs or transfers elements in index order;
4. ends old element places correctly;
5. releases the old unique backing handle; and
6. publishes the new provider and handle only after completion.

It invalidates existing element references, slices, raw pointers, and cursors
unless a stronger explicit stability contract proves continuity.

#### Default selection through `___`

The current execution context may provide a default storage type and provider
instance. A request includes slot size/alignment, dimensions and bounds, initial
count, required capabilities, explicit provider type, and explicit provider
instance where supplied.

An ordinary fixed array may resolve to inline storage; another profile may use
a shared provider and unique handle. The resolved owning representation must
still have a stable `size of` result.

#### Future raw storage-contract owner

Current array design owns the programmer-visible selection, ownership, costs,
capabilities, operations, lifetime, and failure guarantees above.

A new indexed raw array-storage-strategy record will own:

- provider and unique-handle contracts;
- raw slot/chunk requests and gross alignment;
- logical-slot lookup;
- physical versus usable capacity;
- relocation plans and lifecycle callbacks/boundaries;
- stability kinds/tokens and versions;
- provider sharing and default factories;
- `___` selection hooks;
- consuming range/chunk transfer;
- typed and erased provider/handle access;
- static capability requirements;
- storage-erased reference representation pressure; and
- explicit storage changes.

Pointer/arena, generic, reflection, global/`once`, analysis-control, structural,
invocation, and runtime-string raw owners retain their reached consequences.

### Alignment status and explicit deferrals

The known conceptual questions raised by the initial reconstruction and
maintainer review are aligned at the current human-readable design depth. The
focused completeness and contradiction pass and documentation-fit dry run are
recorded at the end of this working record.

The aligned design deliberately defers:

- runtime-fixed owning arrays and therefore inferred fixed-but-jagged rows;
- rectangular or strided multidimensional block views;
- weaker optional or suggested iterable-count capabilities;
- owning or allocation-anchored slices;
- exact optional/reporting array callable names;
- exact generic constraints for iterable and array families;
- complete runtime string APIs;
- exact reflection API syntax;
- SIMD, MIMD, parallel traversal, and compiler vectorization;
- formal panic-category identifiers and registry syntax;
- implementation representation, ABI, and lowering.

These deferrals do not weaken the current constraints. Runtime-sized expansion
infers an open resizable adaptive array; jagged inference requires an explicit
type; array sources and iterables expand only through their explicit forms;
ordinary slices remain borrowed; flat multidimensional arrays remain
rectangular; and every operation retains its aligned evaluation, lifetime,
invalidation, failure, and cost behavior.

### Superseded initial reconstruction

The remainder of this working record is the original candidate reconstruction.
It is retained temporarily as discovery evidence. The current aligned working
model above supersedes it wherever they differ; none of this historical
candidate material is authoritative.

### Review entry point

The smallest useful programmer model appears to be:

```zax
// Candidate array construction and slice source.
values : Integer[5] = [{ 10, 20, 30, 40, 50 }]

third : Integer writable & = values[2]
middle := values[1..<4]

// Candidate extension of direct traversal to a slice.
each element : in middle {
  print(element)
}
```

Under this candidate reading:

- `values` is one owning fixed array with exactly five live element places.
- `values[2]` designates the third element place rather than first copying its
  value or manufacturing a proxy.
- `middle` is a non-owning bounded view of the same three element places at
  indexes `1`, `2`, and `3`; it owns a view descriptor, not those elements.
- Mutation through an authorized path to either the array or the view is
  observed through the other path.
- Element indexing accepts only positions below the current logical length.
- Half-open slice endpoints may equal the logical length.
- Traversal visits increasing logical index order.
- Destruction of the array ends its live element paths in reverse positional
  order.

The slice and slice-traversal spellings remain illustrative. The model is the
review target.

The first decision is more basic than those details: the refreshed notes use
**capacity** for at least two different quantities and use range endpoints as
both counts and positions. Before array syntax can be reliable, Zax needs to
separate:

1. the type-level set of logical lengths a value may have;
2. the value's current logical length;
3. the currently reserved number of storage slots; and
4. whether the live elements have one contiguous physical layout.

Conflating these makes type identity, allocation cost, bounds, parameter
compatibility, and place stability impossible to state consistently.

### Candidate programmer model

#### Three intrinsic sequence roles

The evidence supports three roles without requiring general user-defined
generics:

1. A **fixed array** is an owning value whose element type and exact logical
   length are part of its type. Its element places remain stable for the
   array's life path.
2. A **resizable array** is an owning value whose type records its element type
   and static length restrictions. Its value records a current logical length,
   and its storage policy records a reserved slot count and possible maximum.
3. A **slice** is a non-owning sequence view with a runtime logical length. It
   forwards access to source element places and does not extend their life
   paths.

`T[N]` is strong evidence for the first role. The refreshed `T[min..max]`
family is evidence for the second, and `T[]` is evidence for the third. These
are language-provided parameterized identities; accepting them does not decide
how programmers declare generic types.

Every array dimension has an element type. A multidimensional array is an array
whose elements are themselves arrays. A completed safe array has one live
element resident instance at every position below its current length and no
element resident instance in reserved storage at or above that length.

A zero-length array or slice is a valid sequence. It owns or views no element
places, can be traversed zero times, and still has a complete array or slice
value.

#### Length bounds are not reserved capacity

The refreshed forms provide useful pressure:

```zax
fixed : Integer[5]
minimum : Integer[5..]
maximum : Integer[..5]
bounded : Integer[2..<5]
explicitCurrent : Integer[3 in 2..<5]
unbounded : Integer[1 in ..]
```

But the comments assign two incompatible readings to the range:

- the lower endpoint is a minimum **element count**; while
- an inclusive upper endpoint is treated as a final valid **element index**, so
  `[..5]` is said to permit six elements.

That is not one range over one quantity. It also makes `2..<5` look like the
ordinary count interval `2, 3, 4` while the prose describes storage for five
elements.

The more coherent candidate is that the brackets constrain the `length` value
itself:

```text
T[2..5]   permits lengths 2 through 5
T[2..<5]  permits lengths 2 through 4
T[..5]    permits lengths 0 through 5
T[..<5]   permits lengths 0 through 4
```

This gives `..` and `..<` their ordinary inclusive and half-open meanings. It
would require correcting refreshed comments that interpret upper endpoints as
indexes. If the actual intent is instead “minimum live count plus maximum
indexable slot,” that pair should not masquerade as a mathematical range and
needs a different programmer-facing explanation or syntax.

Under either reading:

- `N in` is evidence for an explicit initial logical length, not a storage
  capacity.
- A compile-time-known `N` outside the type's admitted length set is an error.
- A runtime `N` outside that set uses the selected runtime failure policy.
- An initializer-free resizable array starts at its required minimum and
  default-constructs those elements.
- An explicit initializer may determine a larger initial length when `N in` is
  absent, subject to the static length restriction. The exact rule for
  runtime-sized initializer sources remains open.

The physical reserved slot count needs a separate property. It is always at
least `length`, can be larger without creating elements, and can change without
changing logical length. A static maximum length may permit reserving all
possible slots, but it should not silently require that cost unless the array
type's storage contract says so.

#### Ownership, allocation, and storage

An owning array owns its live element paths. Reserved but inactive slots are
storage, not dormant or uninitialized `T` values.

A fixed array can store its elements as part of its own representation when
that representation satisfies size and alignment. This does not by itself
promise contiguity unless fixed arrays are later defined to be implicitly flat.

A resizable array may own a separate backing allocation. That allocation is one
allocation root for the complete backing region; no element is independently
resettable or adoptable as an allocation root. The array value owns the
allocation relationship, and each live element has a nested element path.

Omitted backing arenas come from the current `___` execution context at the
array operation that requests storage. This is consistent with current
allocation design, but it does not make `___` an allocation expression.
`@` remains the declaration-bound operation for allocating a separately
pointed-to array object. Constructing or growing an owning array may perform its
own visible backing-storage request under the array contract.

The refreshed notes correctly reject the idea that unused capacity consists of
elements initialized with `???`. Safe array state contains either a live
element or no element at that position.

Current general construction design still permits:

```zax
rawArray : Item[5] = unsafe ???
```

as a whole-value unsafe bypass unless array work explicitly changes that
cross-cutting rule. If retained, this is not a normal partially filled array and
does not license `unsafe ???` as a tail entry. It places responsibility for the
complete array representation, every element lifetime, and eventual destruction
on the programmer. The preferable ordinary rule is that no completed safe array
contains an uninitialized live element.

#### Flat storage is an independent static guarantee

The refreshed notes explicitly withdraw the legacy promise that every array is
flat and ask for an opt-in guarantee. That direction is coherent if `flat`
means a statically visible layout property rather than a runtime accident:

```zax
// Illustrative placement of `flat`; exact grammar remains open.
samples : Sample[500] flat

consumeSamples final : ()(
  input : Sample[] flat readonly &
) = {
}
```

A useful one-dimensional guarantee would be:

> The current logical elements occupy one contiguous region in increasing
> index order, with each element represented according to its own type.

The exact source position of `flat`, whether fixed arrays default to it, and
whether it participates in canonical type identity remain decisions. A
parameter that relies on contiguity must nevertheless be able to require it
statically. Runtime discovery that a non-flat value happens to be contiguous is
a different operation.

`flat` need not prohibit resizing:

- growth within reserved contiguous storage can preserve all existing element
  places;
- growth that needs a larger contiguous allocation relocates all elements and
  invalidates their old places;
- reserving a larger region without changing length may itself relocate
  elements; and
- a fixed flat array has no growth relocation.

Making every flat array non-resizable would combine layout and lifetime policy
without necessity. If a stronger pinned or nonrelocating guarantee is needed,
it should be a separate contract.

A slice of flat storage can offer a flat slice. A logical slice of a segmented
or otherwise non-flat array may still be indexable and iterable, but it cannot
pretend to be one base-address-plus-length region. This is also the boundary
needed by future string contiguity and foreign-view APIs.

#### Qualifier layers

The refreshed qualifier example appears to require independently qualifying an
element layer and an array layer:

```zax
// Refreshed evidence; exact interpretation needs confirmation.
values : MyType writable mutable varying [100]
  immutable readonly final
```

This resembles the current optional and indirection model: qualifications before
the array marker describe the inner element access or value layer, while
qualifications after it describe the array value or view descriptor layer.
Exact grammar is not yet established.

The semantic distinction is necessary:

- an owning array is direct containment, so deep immutability of the array
  constrains its resident elements;
- readonly access to an owning array projects readonly access to its elements;
- a slice is semantic indirection, so qualification of the slice descriptor
  must remain distinct from qualification of the borrowed elements;
- a readonly reference to a slice descriptor may prevent changing its start or
  length without necessarily turning a separately authorized element path
  readonly; and
- creating a slice can preserve or narrow source authority but cannot increase
  element mutability, writability, replacement permission, or lifetime.

The array owner should define those projection rules. The qualifier owner should
continue to own the three axes and their ordering.

#### Construction and aggregate source

Ordinary fixed-array default construction establishes elements in increasing
index order. Resizable default construction establishes its initial logical
length the same way. Destruction ends live elements in decreasing index order.
Shrinking or evicting a suffix also destroys that suffix in reverse positional
order.

The refreshed aggregate examples preserve several useful needs:

```zax
destination : Integer[6] = [{
  source[..],
  55,
  otherSource[..]
}]

items : MyType[3] = [{
  [{ "first", 3 }],
  [{ "second", 7 }],
  [{}]
}]
```

They require destination-directed array construction from:

- one element expression;
- one construction packet for an element;
- a sequence source such as an array or slice;
- a repeated/default remainder; and
- eventually an explicit copied, deep-copied, moved, or terminally consumed
  source.

There is a real source-structure conflict to resolve. `[{}]` and `[{...}]`
already denote construction packets, and a packet is not an independently
typed expression. The outer `[{...}]` in the examples can coherently be an
intrinsic array construction packet whose entries are interpreted by the
selected array destination, but this must be stated explicitly. A nested
`[{...}]` then supplies one element constructor packet.

Plain bracket source also remains unresolved:

```zax
typed : Integer[4] = [ 1, 2, 3, 4 ]
inferred := [ 1, 2, 3, 4 ]
```

The first can be destination-directed without making the bracketed source an
independent expression. The second needs an actual inference rule that chooses
element identity, length identity, storage policy, and transfer stance. It
cannot be accepted merely because the tokens are familiar. The legacy claim
that `:= [ ... ]` is confused specifically with lambda capture should be
retested against current source structure rather than carried forward as fact.

An existing destination can also provide type context:

```zax
values : Integer[4]
values = [ 5, 6, 7, 8 ]
```

If array assignment declares a destination-directed sequence input, this does
not require `[ ... ]` to have a standalone inferred type. Rejecting it solely
because inferred standalone source is unsettled would be an unnecessary
restriction.

The refreshed cardinality behavior needs direct maintainer review. It currently
allows a slice source to be silently truncated:

```zax
source : Integer[3] = [{ 1, 2, 3 }]
destination : Integer[2] = [{ source[..] }]
// Refreshed notes say this takes 1 and 2 without failure.
```

but diagnoses or panics when a later explicitly written element appears after a
previous source filled the destination:

```zax
destination : Integer[3] = [{ source[..], 77 }]
// Refreshed notes say this fails because 77 cannot be admitted.
```

That distinction depends on grouping rather than on explicit truncation intent
and can silently lose values. The stronger candidate is exact cardinality by
default, with an explicit prefix/take/truncation operation when discarding a
source tail is intended. If implicit fill-and-truncate is retained, source,
diagnostics, and tooling need to expose that it is a lossy operation.

The candidate static/runtime division is:

- a mismatch proved from fixed source lengths is a compile-time error;
- a mismatch depending on runtime slice lengths is a defined runtime failure;
- no element construction starts until the operation has determined that its
  complete cardinality contract can be met; and
- source expressions and element transfers still occur once in documented
  source order.

The last two bullets expose another hole. If the destination length is inferred
from runtime slice descriptors, storage size is not known until those source
expressions are evaluated. The existing allocation rule can remain coherent if
it is stated as:

> Every required destination backing-storage request completes before any
> destination element construction or transfer begins.

That permits a preflight phase which evaluates and captures extent-bearing
source descriptors once, computes the checked total, obtains storage, and then
constructs elements. It is stricter and more achievable than claiming storage
exists before every expression contributing to initialization is evaluated.
The effects, lifetime, and alias behavior of preflight descriptors still need
review.

`..` as a final aggregate entry is useful evidence for “default-construct the
remaining required elements.” It should be legal only in the final entry,
require a known target length, and require viable default construction. If no
elements remain it is a no-op. Exact repeated-value construction has no settled
source form and remains a known hole.

#### Multidimensional shape

The refreshed examples establish source-order pressure:

```zax
matrix : Integer[4][3] = [{
  [ 15, 16, 17 ],
  [ 19, 21, 27 ],
  [ 100, 101, 102 ],
  [ 193, 231, -27 ]
}]
```

`matrix[0]` is said to contain three integers. Therefore the first written
dimension is the first indexed, outer dimension, and `Integer[4][3]` reads as
four rows of three integers. That is teachable because declaration dimension
order matches access order, but it is not the ordinary left-associated meaning
of repeated postfix type constructors. The array grammar must state the
association rather than leave it to intuition.

Resizable inner dimensions produce ragged arrays:

```zax
rows : Integer[4][1..<3]
```

Each of the four row values may have its own current inner length. Such a value
cannot automatically promise one rectangular, contiguous leaf matrix. A flat
multidimensional contract must decide whether:

- every nested array remains independently flat;
- all leaves occupy one rectangular contiguous region;
- ragged rows use offsets into one backing region; or
- ragged dimensions are unavailable under that flat form.

Nested indexing and a multi-index form should preserve one source order:

```zax
first := matrix[row][column]
second := matrix[row, column]
```

For intrinsic rectangular arrays, both may designate the same deepest element
place. For ragged arrays, the second index is checked against the selected
row's current length. A direct two-index operation remains independently
overloadable for custom receivers and need not decompose through a row proxy.
Whether both intrinsic spellings are provided and exactly when each dimension
check occurs remain open.

Arrays should remain atomic structural leaves by default. Flattening every array
element through ordinary structural compatibility would make dynamic length,
element lifecycle, and large fixed counts part of record-shape matching.
Array-of-structures to structure-of-arrays conversion reorders or gathers
storage and should therefore be an explicit transformation with visible
construction, transfer, allocation, and overlap cost, not a hidden `as shape`
or `as layout` recast.

#### Indexing produces element places for intrinsic arrays

For an intrinsic owning array or ordinary slice, the strongest candidate is:

> Successful element indexing designates the selected existing element place
> with qualifications and lifetime derived from the base access path.

A value context may copy from that place, and a reference context may bind to
it. The index operation itself need not copy and need not create a proxy.

```zax
value := array[index]                 // destination may copy from the place
view : Item readonly & = array[index] // binds to the place
array[index] = replacement            // acts on the place
array[index] ^= mask                  // direct read-modify-write operation
```

This does not constrain custom `operator index`: a custom receiver may return a
value, reference, or explicit proxy type. Direct indexed assignment and compound
forms remain mixfix operations and win before decomposition. If no direct
mixfix applies, decomposition is available only when indexing genuinely returns
an assignable place or real proxy.

Integer bit indexing is a related but distinct case. A bit may support direct
read, write, and compound mutation without being an addressable `Boolean &`.
No persistent proxy should be implied. If a proxy is later introduced, it needs
an explicit type, lifetime, qualification, capture, and cost contract.

For a collection of logical length `L`:

- an element index is valid when `0 <= index < L`;
- a half-open slice endpoint is valid when `0 <= endpoint <= L`; and
- each count, position, and endpoint uses the applicable array memory domain's
  `IndexSize` identity unless a later associated index type is deliberately
  introduced.

Compile-time-known invalid indexes are diagnostics. Required runtime indexing
panics on an invalid index under the current panic model. Optional/reporting and
unchecked families may be added explicitly; compile-time versus runtime
knowledge must not silently change one operation's static result shape.

One evaluation-order question remains. Current operator and mixfix design says
the receiver and every written hole evaluate and bind once in source order
before invocation. Therefore:

```zax
array[chooseIndex()] = makeReplacement()
```

naturally evaluates the array receiver, `chooseIndex()`, and
`makeReplacement()` before the direct indexed-mutation operation performs its
bounds-dependent body. Checking the index before evaluating the right side
would be a special protected evaluation rule. The array design must choose and
document one behavior; “visible source order” alone does not answer whether the
right side runs before a bounds panic.

#### Half-open slicing must preserve empty ranges

The refreshed notes establish `..` as inclusive and `..<` as half-open. That
supersedes the raw `..=` candidate as the latest evidence, but it does not yet
make either spelling accepted.

The proposed normalization in the notes subtracts one from a half-open end,
forbids an end of zero, and rejects `0..<0`. That loses a foundational slice
value:

```zax
emptyAtStart := array[0..<0]
emptyAtMiddle := array[3..<3]
emptyAtEnd := array[array.length..<array.length]
```

Empty ranges are needed for zero-length arrays, partition boundaries,
incremental parsers, insertion positions, and algorithms whose valid result can
contain no elements. Their absence would force pervasive special cases.

The stronger candidate is to normalize every built-in slice to half-open
boundaries:

```text
array[start..<end]  -> [start, end)
array[start..end]   -> [start, end + 1)
array[..<end]       -> [0, end)
array[..end]        -> [0, end + 1)
array[start..]      -> [start, length)
array[..]           -> [0, length)
```

Half-open validity is `0 <= start <= end <= length`; equality produces an empty
slice. Inclusive validity is `0 <= start <= end < length`. Converting a valid
inclusive end to `end + 1` cannot exceed `length`, so it does not require an
index identity wider than `IndexSize`.

`start..<` can remain malformed because the exclusion marker has no written end
to exclude. `start..` already expresses the open-ended form.

A reversed range known at compile time is an error. A required intrinsic slice
with runtime `start > end` should have one defined failure, most likely panic;
it cannot be left to an implementation. A custom splice operation may define a
domain-specific result or failure only through its visible declared contract,
not through undocumented implementation choice.

The refreshed `operator splice N` proposal is useful evidence for a distinct
custom operation, but current operator owners recognize only call and index.
Work item `025` must decide whether slicing is:

- `operator index` receiving a range value;
- a distinct `operator splice N` postfix form;
- direct syntax mapped to another declared operation; or
- a combination with explicit rules that prevent ambiguous discovery.

The normalized pair supplied to custom code must preserve empty intervals.
Passing “inclusive end after subtracting one” cannot do that.

#### Slice value, descriptor reference, and borrowed elements

The parameter examples imply that `T[]` is a slice descriptor:

```zax
inspect final : ()(input : Integer[]) = {
}

adjustView final : ()(input : Integer[] &) = {
}
```

Passing an array to `inspect` creates and copies a temporary descriptor; it does
not copy the elements. Passing an array to `adjustView` may create a temporary
descriptor and bind the parameter reference to that descriptor for the call.
Changing its start or length then changes only the temporary descriptor, not
the caller's array or a caller-owned descriptor.

Passing a caller-owned slice by reference is different: descriptor mutation is
visible to that caller-owned slice value. In every case, element mutation
depends on the separately preserved element access qualifications.

Copying a slice descriptor creates another private view state over the same
source places. It does not create an owning copy of the elements. A slice's
descriptor lifetime may be independent, but every element access remains
bounded by the source element path. Destruction, shrinking, removal, relocation,
or replacement that ends a viewed place invalidates the corresponding access.

An already obtained element reference remains bound to its original element
place even if a mutable slice descriptor later changes which place its own
index zero selects.

Overlapping readonly slices are ordinary. Mutation through overlapping writable
slices changes shared source places. A bulk splice/copy whose source and
destination may overlap needs deterministic behavior. The strongest general
candidate is “as if all source values needed by later writes were captured
before the first conflicting write,” matching structural transformation. A
specialized directional operation could expose a cheaper contract. Silent
direction-dependent corruption is not acceptable.

The baseline slice should remain borrowed. An owning or allocation-anchored view
would be a distinct, explicit ownership form with pointer-like cost and
invalidation behavior; it should not be inferred merely because a slice escapes.

#### Resize operations and element-place stability

The refreshed `shrink`, `grow`, and `resize` phrases are useful behavior
candidates:

- `array shrink targetLength` destroys a suffix when the target is smaller and
  is a no-op when it is not smaller.
- `array grow targetLength` constructs a suffix when the target is larger and
  is a no-op when it is not larger.
- `array resize targetLength` reaches exactly that logical length by one of
  those paths.
- A target outside the static length restriction is a compile-time error when
  known and a defined runtime failure otherwise.
- A grow path requires a construction source for every new element. A
  zero-input/default form is available only when the element supports it.

Exact phrase declarations and whether these are protected intrinsic operations
remain open. `resize` requiring default construction even on a runtime path that
might shrink is predictable but may be unnecessarily restrictive; separate
shrink/grow overloads or explicit element construction sources can preserve
clarity.

The initial intrinsic stability candidate is:

- Replacing or mutating one existing element does not relocate any element
  place. Replacement may renew the resident instance at the selected place.
- Growing within reserved capacity preserves all existing element places and
  begins new paths in increasing index order.
- Growing after backing reallocation ends or relocates every old element path,
  establishes successor elements in the new backing region, and invalidates
  ordinary references, slices, raw pointers, and cursors to the old places.
- Shrinking from `L` to `N` ends positions `N` through `L - 1` in reverse
  order; the prefix positions remain.
- Reserving storage without changing length preserves every place only when the
  backing region does not move.
- Removal always ends the removed element path.
- Insertion and removal within the sequence preserve an unaffected prefix at
  most. Positions at and after the operation need an explicit renewal or
  invalidation rule; unchanged numeric index is not evidence that one element
  lifetime continued.

Whether non-flat or segmented arrays promise stronger local stability is a
storage-policy decision that must be reflected in their static contract. It
cannot vary invisibly by implementation.

Fixed arrays already participate in direct `each in`; the source evaluates once,
elements are visited in increasing index order, and a default binding creates a
fresh reference to each stable element place. Dynamic arrays and slices still
need an explicit decision:

- adding them to the language-recognized direct `each in` source family; or
- providing `each from` cursors with exact progression and invalidation
  contracts; or
- supporting both while keeping their costs distinct.

Element mutation that preserves the current place can remain valid during
traversal. Structural mutation must follow the same place/cursor invalidation
rules as direct references. Compiler-coordinated erase remains the explicit
continuing-removal path when a cursor supplies it.

#### Array parameters and checked length contracts

The refreshed call examples distinguish by-value arrays, array references, and
slices, but their rule needs refinement.

A coherent candidate is:

- A by-value bounded-array parameter constructs a new owning array while
  preserving the source's current logical length. Static interval facts reject
  impossible mappings; a source whose current length might fit requires a
  runtime length check before destination element construction.
- A by-reference bounded-array parameter creates no array copy and should avoid
  a hidden runtime narrowing check. The source's statically possible current
  lengths must therefore fit within the parameter's promised interval.
- A by-value `T[]` parameter copies only a slice descriptor.
- A `T[] &` parameter refers to a descriptor, which may be a call-duration
  temporary made from an array.

For example, a source whose type permits lengths `2` through `6` may map by
value to a destination requiring `3` through `9`, with runtime failure when its
current length is `2`. It cannot bind directly to a reference contract that
promises every source state is at least `3`.

This still leaves a capability question. If a bounded-array reference lets the
callee call `grow` or `shrink`, interval containment alone may grant operations
the source concrete array cannot perform. The parameter type must distinguish:

- a read-only current-length promise;
- writable element access;
- descriptor or length mutation;
- backing-storage mutation; and
- ownership of reallocation and element relocation.

The refreshed examples accept a fixed row through a bounded array reference,
which suggests that length bounds in a parameter are observation/admission
requirements rather than an automatic resize capability. That interpretation
needs explicit confirmation.

The examples use both `Integer` and `Integers`; absent an independently declared
`Integers` identity, the plural form appears to be a source typo and should not
become language evidence.

#### Counts, storage extent, overflow, and failure

Logical lengths, capacities, indexes, and slice endpoints use `IndexSize` for
the applicable memory domain. Allocation byte extent uses `TypeSize`.

Computing backing storage requires checked arithmetic:

```text
element count * element storage extent
plus array metadata and alignment
```

The exact `IndexSize`/`TypeSize` bridge is permitted because their ranges and
representations agree, but it does not erase their intent identities. Every
intermediate count, product, multidimensional extent, offset, and metadata
addition must be checked before an arena request. No failure silently widens
the array to another near, ordinary, or far domain.

Compile-time-known overflow is a diagnostic. Runtime overflow needs a defined
policy. Current `@!` reports allocation-request failure only; it does not
automatically translate count arithmetic, constructor panic, or nested
allocation panic into `Nothing`. Array construction must decide whether a
reporting array-allocation form includes extent overflow or whether extent
overflow remains a distinct required-operation panic.

Required bounds and length failures use the current panic boundary: the
operation is blocked until a matching helper repairs the condition or the
process crashes gracefully. Panic does not skip an index, return an empty slice,
leave a partially published array, or unwind already constructed elements.
Ordinary code must never observe the array while a construction, growth, or
replacement operation has incomplete element state.

An unchecked bounds form, if added, is not merely a faster spelling of required
indexing. It supplies a narrow safety assertion or permission and has undefined
consequences when its promised bounds are false. Optional/reporting forms have
defined static result shapes independent of whether a particular index is known
at compile time.

### Decisions requiring maintainer review

The current candidate cannot become one coherent proposal until these questions
are answered. They are ordered by how many later rules depend on them.

1. **What exactly does the bracket range measure?** Decide whether both
   endpoints are logical lengths, or whether the syntax intentionally combines
   a minimum count with a maximum index/storage endpoint. Also decide whether a
   static upper bound mandates physical reservation.
2. **Is initializer cardinality exact by default?** The refreshed silent
   truncation of a slice tail conflicts with explicit intent and differs from a
   later scalar overflow. Decide whether truncation needs explicit source.
3. **Are empty half-open slices valid?** The reconstruction recommends yes and
   rejects normalization by subtracting one from the exclusive end.
4. **What source construct owns array aggregate entries?** Reconcile outer
   `[{...}]`, element construction packets, plain `[ ... ]`, unsliced array
   expansion, nested-array elements, and standalone inference.
5. **When does bounds failure occur relative to a direct mutation RHS?** Decide
   whether all mixfix holes bind before the check or indexing introduces an
   explicit earlier failure boundary.
6. **What does `T[]` own and how are its descriptor and element layers
   qualified?** The reconstruction recommends a borrowed descriptor with
   independent descriptor and element authority.
7. **What exact static guarantee does `flat` make?** Decide its type-identity
   role, dimension behavior, and parameter spelling without coupling it
   unnecessarily to resizing.
8. **Which intrinsic dynamic operations preserve, renew, relocate, or end
   element places?** Insertion, removal, reserve, and segmented growth need
   particular attention.
9. **How does runtime-sized initialization preflight cardinality while
   preserving source-order effects and storage-before-element-construction?**
10. **What does a bounded array reference promise and permit?** Separate current
    length admission from resize capability and descriptor mutation.
11. **Does dimension declaration order deliberately match index order?** The
    refreshed `Integer[4][3]` examples say yes; grammar association and flat
    multidimensional layout must agree.
12. **Is slicing a distinct `operator splice N`, an index operation over a
    range, or another protected form?** Add only the recognized forms whose
    discovery, declaration, normalization, and direct-mixfix behavior can be
    taught coherently.

### Provisional evidence disposition

This disposition is only an initial audit of evidence. It does not accept the
candidate outcomes.

From the refreshed maintainer notes:

- Retain `..` as the latest inclusive-range spelling and `..<` as the latest
  half-open spelling for review; treat the raw `..=` form as superseded evidence
  if this spelling is confirmed.
- Retain separate minimum, maximum, and explicit-current-length use cases, but
  reject the current terminology collapse until length bounds and storage
  capacity are separated.
- Retain mixed element/slice construction, runtime cardinality pressure,
  default remainder, same-element array sources, and compile-time diagnostics;
  put implicit truncation under direct review.
- Retain by-value array, array-reference, slice-value, and slice-reference
  parameter use cases; replace the unexplained “requirements satisfy” rule with
  explicit length and capability relationships.
- Retain nested and ragged multidimensional use cases, compile-time-known bound
  errors, and per-dimension runtime checks.
- Retain arbitrary custom index result types, direct indexed mutation without a
  proxy, and the need for direct splice mutation.
- Retain the need for custom slicing, but reject empty-range loss and
  implementation-selected reversed-range behavior as currently stated.
- Retain per-element construction packets and zero-input construction. Reconcile
  them with the current canonical packet grammar rather than reviving a generic
  “multi-value operator.”
- Retain reverse positional destruction and eviction.
- Retain `shrink`, `grow`, and `resize` behavior as phrase candidates, pending
  recognized-form and construction-source review.
- Retain independent element/array qualification pressure, pending exact layer
  interpretation.
- Retain the prohibition on ordinary uninitialized element slots. Do not use
  legacy `unsafe ???` as a capacity filler.
- Retain standalone inferred-array uncertainty as an open inference question;
  do not let it block destination-directed typed initialization or assignment.
- Accept as refreshed evidence that the old `.slice` function is obsolete.
- Retain opt-in flat-storage pressure and the willingness to require a flat
  argument contract.
- Retain default-arena-context pressure for dynamic backing storage while
  preserving current allocation-token ownership.

From legacy `arrays.md`:

- Retain fixed arrays, arbitrary element types, default initialization,
  element-reference traversal, multidimensional source, constructor-driven
  element construction, and array pointer/reference use cases.
- Retire the claim that arrays categorically cannot resize.
- Retire `.slice(...)` as a candidate current API after its useful bounded-view
  use cases are preserved in direct slicing.
- Retire type-side legacy `@` and `#` dynamic-array allocation syntax.
- Retire uninitialized aggregate tails in ordinary array construction.
- Do not retain the claimed universal flat matrix and per-dimension overhead
  representation. Preserve only the programmer need for explicit flat data and
  inspectable costs.
- Re-evaluate legacy `[ ... ]` and `[{...}]` explanations under current
  construction-packet and source rules instead of preserving their old
  “slice” and “multi-value operator” terminology.
- Preserve inferred-array/capture ambiguity only as a source case to test; the
  old compiler-confusion explanation is not authority.

From indexed raw input:

- Retain direct index/mixfix behavior, multi-index pressure, once-only operand
  evaluation, no implied proxy, direct splice assignment, index/endpoint
  capacity, checked count-to-byte conversion, dynamic allocation use cases,
  bit-place behavior, element stability, and cursor participation.
- Replace the raw range-punctuation candidates with the refreshed spelling only
  after the inclusive/half-open review confirms it.
- Preserve the rule that positions use `0` through `L - 1` while endpoints use
  `0` through `L`.
- Preserve the rule that capacity and unchanged numeric index do not alone prove
  an element place continued.

From current owners:

- Preserve direct initialization rather than default construction followed by
  assignment.
- Preserve storage, member lifetime, and complete-instance distinctions during
  array construction and resizing.
- Preserve fixed reference binding and the rule that source lifetime is not
  extended by a slice.
- Preserve the array allocation root rather than treating elements as separate
  allocations.
- Preserve fixed-array increasing direct traversal and the cursor protocol.
- Preserve direct-mixfix selection before decomposition and all-hole once-only
  source evaluation.
- Preserve qualifier capability versus access permission at every array and
  slice layer.
- Preserve `IndexSize`/`TypeSize` identity and exact bridges without implicit
  widening.
- Preserve strict structural identity, contiguous-view requirements, and
  explicit shape-changing transformation.
- Preserve the safe/proved, unproved, defined-unsafe, and impossible analysis
  outcomes.
- Preserve string identities' logical unit types without making ordinary
  strings flat; array flatness and slices should provide the future explicit
  contiguity mechanism rather than silently strengthening strings.

### Likely lasting owners and deliberate deferrals

The concept is cohesive enough to need one programmer-facing arrays and slices
owner. Splitting fixed arrays, dynamic arrays, slicing, and element places across
existing local owners would force readers to reconstruct the feature. That
owner would teach the common sequence model first, then fixed ownership,
resizing/storage, indexing, slicing, multidimensional behavior, construction,
place stability, traversal, costs, and diagnostics.

Promotion would also require focused integration:

- construction owns packet mechanics, element lifecycle ordering, partial
  construction, whole-array `unsafe ???`, and destruction;
- lifetimes owns place/reference consequences while the array owner supplies
  each operation's stability contract;
- pointers owns backing allocation roots, arena/disposition interaction, and
  array-pointer boundaries;
- iteration owns direct source-family and cursor-protocol participation;
- operators, the catalog, and mixfix own recognized index/splice/resize forms,
  declarations, direct mutation, and evaluation;
- source structure owns bracket/list/packet formation and formatting;
- integers owns count, position, endpoint, storage-size conversion, and overflow
  identities;
- qualifiers owns the axes while arrays owns layer projection;
- structural compatibility owns whether arrays are atomic leaves and the
  explicit AoS/SoA transformation boundary;
- safety owns reusable checked/unchecked proof categories while arrays owns
  bounds and invalidation facts; and
- strings owns string invariants while its indexed runtime input retains
  coalescing, raw access, and string-specific mutation consequences.

General user-defined sequence generics remain in
`project/raw/type-parameters-and-generics.md`. Runtime string APIs remain in
`project/raw/string-runtime-and-encoding.md`. Array metadata reflection remains
in `project/raw/reflection.md`. New pointer mechanics, if actually required,
belong in `project/raw/pointer-and-arena-mechanics.md`.

Compiler vectorization, parallel traversal, and generalized collection APIs are
deliberately not designed here. Current array work constrains them:

- they may rely on `flat` only when the static contract supplies it;
- they must preserve source evaluation, index order, effects, and lifecycle;
- they cannot treat AoS/SoA transformation as free or implicit;
- they must state alias, overlap, and element-stability requirements; and
- they cannot silently add synchronization, allocation, or relocation.

Before archival, that pressure needs an indexed live raw destination if no
current domain owner has been established. It must not survive only in this
working record.

No implementation mapping is needed or authorized. Feasibility pressure is
captured through programmer-visible storage, overflow, evaluation, lifetime,
and cost contracts.

## Dispositions and promotion dry run

### Completeness and contradiction pass

The pass rechecked the complete maintainer notes, legacy array input, raw
indexing input, and every current owner in the required reading scope against
the aligned working model. It also followed concrete consequences into current
string teaching and the live raw destinations for panic controls, pointer
mechanics, runtime strings, generics, and reflection.

No remaining contradiction invalidates the aligned model. Promotion must account
for these integration findings:

- Current string documentation sometimes writes `U8[]` where it means an owning
  decoded-byte result. Under the aligned model, `T[]` is a borrowed slice. A
  compile-time decoder with known output count instead produces an owning fixed
  `U8[N]`; a runtime-sized owning byte result uses the appropriate ranged array
  type.
- Other `U8[]`, `Legacy.Char[]`, and `Legacy.WChar[]` string examples may remain
  slice inputs when they deliberately consume a complete borrowed sequence.
  Each use must be reviewed by value rather than changed mechanically.
- Runtime slice invalidation requires visible origin and validity-check cost.
  The language need not promise one representation, but the arrays owner must
  explain when static proof removes a check, when checked use can panic, and
  when disabled checking transfers undefined-behavior responsibility.
- The iterable `count` example uses the applicable ordinary `IndexSize`.
  Near/far and unknown generic iterable count association remains generic
  pressure; promotion must not imply an unreviewed cross-domain conversion.
- Fixed-array splice assignment may replace only with equal cardinality.
  Insertion, removal, and unequal replacement require a resizable receiver whose
  resulting length remains within its declared range.
- A present suggested capacity outside a finite admitted maximum is invalid even
  though suggestion and actual capacity otherwise have no ordering relation.
- Narrowing a mutable slice descriptor can use ordinary assignment from an
  equal-or-narrower same-origin sub-slice. It cannot assign a foreign, broader,
  invalidated, or temporary-escaping view into validity.
- Unknown-count iterable construction is the intentional exception to
  final-storage-before-first-element construction. Each incremental backing
  expansion still completes before the next element construction, and no
  partial array is published.

These are consequences or clarifications of aligned findings rather than new
language choices.

### Dry-run result: FAIL (revised during promotion validation)

The initial pre-promotion result was **PASS**: the aligned findings had a
cohesive programmer-facing owner, every supporting rule had one lasting owner,
and every explicit deferral had a live current or indexed raw destination.

Promotion validation revised the result to **FAIL** after finding a lifecycle
conflict in the exact change set. Deleting root `arrays.md`,
`project/raw/arrays-and-slices-maintainer-notes.md`, and
`project/raw/indexing-and-slicing.md` leaves broken links inside this active
file's immutable fixed initiating input and reading scope. Those sections may
not be rewritten, and the archive-only historical-link exception does not apply
until this file is archived.

The exact promotion set therefore requires new alignment before validation can
pass. The recommended correction is to retain minimal non-authoritative routing
stubs at the three paths solely until closure archives this work item, then
delete the stubs during the same authorized closure operation. Keeping the full
superseded inputs would preserve links but retain unnecessary duplicate
material; archiving immediately would combine promotion with separately gated
closure and continuation work.

### Proposed ownership map

#### Cohesive array owner

Create `language/arrays-and-slices.md` as the complete programmer-facing owner
for:

- fixed, resizable, flat, adaptive, multidimensional, and slice mental models;
- element type, dimension order, exact/ranged length identity, current length,
  actual capacity, and optional suggested capacity;
- array expressions, nested inference, explicit slice and iterable expansion,
  exact cardinality, and runtime-sized construction;
- default, packet-based, iterable, delayed unsafe, copied, deep, moved, and
  terminal construction;
- element-place indexing, bounds, required and reporting access, and bit-place
  handoff;
- inclusive and half-open slicing, optional endpoints, slice descriptor
  qualification, narrowing, provenance, overlap, lifetime, and invalidation;
- direct splice assignment, insertion, removal, replacement, reservation,
  trimming, growth, shrinking, resizing, and joining;
- `<+>` and `<+>=` array-specific behavior;
- whole-array and slice transfer behavior;
- flat row-major and explicit adaptive/jagged behavior;
- bounded value/reference parameter behavior;
- direct and cursor traversal handoffs;
- array-specific costs, static errors, panic conditions, unchecked
  consequences, reflection facts, source stability, and explicit future
  boundaries.

The document will lead with fixed array source, one element place, one slice,
and direct traversal. It will then layer dynamic length/capacity, construction,
layout, mutation, lifetime, advanced transfer, diagnostics, costs, and reference
material. It will not reproduce discovery chronology or cite this numbered
work item or raw inputs.

#### Existing current owners

- `language/declarations-and-bindings.md` owns declaration-facing fixed/ranged
  forms, `N in`, common-element inference boundaries, typed versus inferred
  array expressions, assignment versus declaration, and whole-array
  `unsafe ???` integration. It links to the array owner for complete behavior.
- `language/construction-and-destruction.md` owns general construction-packet,
  delayed-construction, partial-construction, panic, element lifecycle, and
  reverse-destruction mechanics. It adds array-specific handoffs without
  duplicating array-expression or resize APIs.
- `language/lifetimes-and-references.md` owns element-place and borrowed-slice
  lifetime consequences. Its deferred dynamic-array section becomes a concise
  current summary and link to the array operation contracts.
- `language/pointers-and-arenas.md` owns array backing allocation roots, arena
  relationships, pointer-to-array allocation, and the rule that an element is
  not an independent allocation root.
- `language/iteration.md` owns direct `each in` participation for fixed arrays,
  resizable arrays, and slices; cursor-driven `each from`; the optional exact
  `count` protocol operation; traversal bindings; progression; erase; and
  invalidation handoffs.
- `language/operators.md` owns shared index/splice discovery, evaluation,
  result, and direct-mixfix behavior, plus general runtime `<+>` and compound
  integration.
- `language/operator-catalog.md` owns exact `..`, `..<`, `operator splice N`,
  `count`, `capacity of`, `suggested capacity of`, and `<+>=` recognition,
  fixity, precedence, reservation, and routing to the arrays owner.
- `language/mixfix-operators.md` owns `splice N` components, direct splice
  assignment, indexed/spliced mutation without a proxy, and the aligned
  receiver/index/RHS evaluation boundary.
- `language/source-structure.md` owns `[ ... ]` array-expression delimiters,
  contiguous `[[ ... ]]` capture delimiters, `[ [` nested-array intent,
  adjacent `][` indexing, array-entry contextual `from`, final bare `..`,
  comma/list continuation, packet nesting, and formatting.
- `language/integers.md` retains `IndexSize`/`TypeSize`, position/endpoint
  capacity, exact bridges, memory-domain distinctions, and checked
  count-to-byte conversion. It links to arrays for container behavior.
- `language/qualifiers.md` owns the qualifier axes and ordering around array and
  slice layers. Arrays owns how those qualifiers project to elements and
  descriptor mutation.
- `language/function-invocation.md` owns call-boundary mapping and immediate
  binding for owning array values, adapting bounded references, slice
  descriptors, array expressions, and construction packets.
- `language/transfer-stances.md` owns general stance meaning and fallback.
  Arrays owns storage stealing, elementwise transfer, slice-descriptor
  transfer, and join-specific consequences.
- `language/structural-shapes-and-compatibility.md` owns arrays as atomic
  structural leaves and explicit AoS/SoA transformation rather than implicit
  shape/layout conversion.
- `language/safety-and-analysis.md` owns the reusable static-proof,
  required-check, panic-category disabling, and undefined-behavior model.
  Arrays owns the exact array conditions to which it applies.
- `language/strings-and-characters.md` owns string invariants and string join
  admission. It uses current array/slice identities correctly and links to the
  array owner for storage, views, and capacity.
- `language/terms.md` gains concise definitions for fixed array, resizable
  array, slice, splice, array expression, flat/adaptive layout, logical length,
  capacity, suggested capacity, and element place where a cross-cutting term is
  actually reused.

#### Routers and legacy input

- `index.md` adds the arrays-and-slices owner to the start route and current
  conceptual-design list, and removes `arrays.md` from legacy language notes.
- Root `arrays.md` retires after every useful example and concern is promoted,
  superseded, rejected, or transferred to a live deferred owner. No compatibility
  stub remains.
- Root `README.md` needs no change; its existing route through `index.md`
  remains sufficient.
- `project/README.md` needs no promotion change. Its active-work pointer changes
  only during separately authorized closure.

#### Raw input

- `project/raw/arrays-and-slices-maintainer-notes.md` retires after its complete
  value is consumed.
- `project/raw/indexing-and-slicing.md` retires after index, splice, bit-place,
  dynamic allocation, stability, traversal, and capacity pressure reaches
  current owners or the narrower live destinations below.
- `project/raw/README.md` removes those two retired rows and retains all other
  live raw routes.
- `project/raw/analysis-controls.md` receives the universal panic-registry
  pressure and the provisional array conditions required by this work:
  index bounds, splice bounds/order, length range, cardinality, invalidated
  slice, foreign slice origin, iterable count mismatch, extent overflow, and
  required backing allocation.
- `project/raw/pointer-and-arena-mechanics.md` replaces its link to retired raw
  indexing input with the current array owner and retains owning/anchored slice
  and relocated-element pointer pressure.
- `project/raw/string-runtime-and-encoding.md` consumes current general array
  behavior and retains only string-specific conversion, mutation, contiguity,
  coalescing, invariant, builder, and foreign-view questions.
- `project/raw/type-parameters-and-generics.md` records that concrete intrinsic
  array/iterable parameterization does not establish general generics and
  retains generic iterable value/count association, memory-domain count types,
  and generated array-family constraints.
- `project/raw/reflection.md` receives deferred exact API shape for array type
  bounds, dimensions, layout guarantee, current length, capacity, suggestion,
  source provenance, and operation availability.
- `project/raw/feature-catalog.md` routes arrays and current indexing/slicing to
  the new owner, routes bit-place mechanics to arrays and integer/operator
  owners, and removes its dependency on the retired raw indexing file.
- `project/raw/safety.md`, `project/raw/cross-cutting-audit.md`, and other raw
  inputs need no promotion edit. Their existing broader concerns are neither
  consumed nor changed by this work.

### Structure proposal

Retain the current flat `language/` directory. Add exactly one cohesive owner:

```text
language/
  arrays-and-slices.md
```

Do not add an array subdirectory, formal specification area, implementation
documentation, compatibility stub, or new raw file. Existing `index.md` remains
the public router. Existing domain-oriented current owners retain their local
rules and link to the new owner only where a concrete dependency or handoff
exists.

Deferred runtime-fixed arrays, multidimensional block views, owning slices,
reporting names, and array reflection syntax remain visible in the new owner's
future-boundary section. Generic, pointer, panic-control, string-runtime, and
reflection mechanics additionally remain discoverable through their existing
indexed raw owners. No deferred meaning depends only on the eventual archive of
this work item.

### Exact proposed promotion change set

Promotion will make exactly these repository changes:

1. Add `language/arrays-and-slices.md`.
2. Update `index.md`.
3. Delete root `arrays.md`.
4. Update `language/declarations-and-bindings.md`.
5. Update `language/construction-and-destruction.md`.
6. Update `language/lifetimes-and-references.md`.
7. Update `language/pointers-and-arenas.md`.
8. Update `language/iteration.md`.
9. Update `language/operators.md`.
10. Update `language/operator-catalog.md`.
11. Update `language/mixfix-operators.md`.
12. Update `language/source-structure.md`.
13. Update `language/integers.md`.
14. Update `language/qualifiers.md`.
15. Update `language/function-invocation.md`.
16. Update `language/transfer-stances.md`.
17. Update `language/structural-shapes-and-compatibility.md`.
18. Update `language/safety-and-analysis.md`.
19. Update `language/strings-and-characters.md`.
20. Update `language/terms.md`.
21. Update `project/raw/README.md`.
22. Update `project/raw/analysis-controls.md`.
23. Update `project/raw/feature-catalog.md`.
24. Update `project/raw/pointer-and-arena-mechanics.md`.
25. Update `project/raw/reflection.md`.
26. Update `project/raw/string-runtime-and-encoding.md`.
27. Update `project/raw/type-parameters-and-generics.md`.
28. Delete `project/raw/arrays-and-slices-maintainer-notes.md`.
29. Delete `project/raw/indexing-and-slicing.md`.
30. Update this active work file with the applied promotion disposition and
    validation result.

No other file, directory, branch, index, archive, prompt source, compiler
repository, website deployment, or version-control state is part of the
proposed promotion.

### Promotion teaching and validation obligations

The new owner must:

- teach valid fixed-array source before dynamic policies;
- distinguish array expressions, element packets, slices, and iterable
  expansion in source examples;
- distinguish logical length, actual capacity, and optional suggested capacity;
- show valid empty slicing and direct insertion/removal;
- show bounds, cardinality, relocation, and invalidation failures inline;
- expose allocation, copy/deep/move/last, traversal, runtime-check, and
  contiguity costs;
- distinguish ordinary slices from owning arrays and raw contiguous access;
- explain multidimensional row-major layout before adaptive jagged behavior;
- keep custom index/splice results and proxy behavior separate from intrinsic
  element places; and
- preserve every explicit deferral without making it sound current.

After authorized promotion, validation must:

- verify every changed current concern has one owner;
- verify the public index reaches the new owner and no live route reaches
  deleted `arrays.md`;
- verify no live file references either retired raw array input;
- verify all non-archive relative links and heading anchors;
- verify Markdown rendering and focused example ordering;
- verify unmarked source uses the current `[[ ... ]]`, array-expression,
  range, qualifier, operator, and callable forms;
- verify every known invalid example carries an inline error marker;
- verify current string documentation no longer uses slice syntax for an owning
  byte result;
- verify no current owner cites this numbered work item or raw input for
  meaning;
- verify the exact changed-file set above; and
- preserve the staged and unstaged review boundary throughout.

### Promotion application

Promotion was applied on 2026-09-17 under explicit maintainer authorization.
Validation remains recorded separately below.

- `language/arrays-and-slices.md` now owns the cohesive programmer-facing
  concept.
- Every current supporting owner in the dry-run set received its local rule,
  dependency, or handoff.
- `index.md` routes readers to the new owner and no longer routes arrays as
  legacy input.
- Root `arrays.md` retired after its useful material was consumed.
- The maintainer notes and raw indexing/slicing input retired after accepted
  behavior moved to current owners and deferred pressure moved to narrower live
  destinations.
- Existing raw analysis-control, pointer, string-runtime, generic, reflection,
  and feature-catalog owners now retain their assigned future pressure.
- No implementation source, branch, archive, operating-prompt source, or
  external website was changed.

### Promotion validation: FAIL

The promoted conceptual content and integration checks passed:

- the new owner contains no numbered-work or raw-input dependency;
- every new owner link and heading anchor resolves;
- `index.md` routes to the new owner and no longer routes arrays as legacy;
- current owners no longer describe intrinsic array, slicing, traversal, or
  runtime array joining as unresolved future work;
- current string teaching no longer uses `U8[]` for an owning decoded-byte
  result;
- no live current language owner links to a retired raw array input;
- the changed-file set matches the recorded promotion scope;
- no branch, stage, commit, archive, prompt source, or external resource was
  changed; and
- `git diff --check` passes.

Complete validation nevertheless **FAILS** because this active work file still
contains immutable links to the three deleted evidence paths. Archived work may
rely on Git history for such links; active work may not. Promotion remains
applied in the working tree, but it is not ready for acceptance or closure until
the temporary-link disposition is discussed, aligned, authorized, applied, and
revalidated.

### Teaching revision after maintainer review

The maintainer stopped the first promotion review because the initial
`language/arrays-and-slices.md` closely preserved working-record density without
building a cold reader's model. Accurate phrases such as “element place,”
“proxy,” “descriptor,” and “cardinality panic” appeared before the immediate
programmer question made them useful.

The promoted teaching was rewritten under explicit maintainer authorization:

- the owner now opens with ordinary fixed-array construction, copied element
  access, direct reference access, and traversal;
- fixed, resizable, and slice forms are explained through programmer outcomes
  before their detailed contracts;
- proxy behavior moved to the advanced custom-indexing section and is defined
  there;
- element-place terminology appears only after ordinary element selection and
  slice use are established;
- length, capacity, suggestions, slicing, mutation, invalidation, transfer,
  parameters, diagnostics, and costs are layered from common use toward
  reference detail; and
- every promoted local `language/` integration was reviewed for compressed
  working-record terminology and rewritten with the local programmer task
  first.

This teaching revision changes presentation, examples, and explanation rather
than the aligned array semantics. The active-link validation blocker above
remains unresolved.

### Revised storage-strategy documentation-fit dry run: PASS

The storage-strategy refinement, required `from` expansion, usable-versus-
physical capacity distinction, storage-erased references, `size of`
clarification, provider factory boundary, and temporary-link disposition
materially changed the earlier promotion plan. This revised dry run supersedes
the earlier dry-run result, exact change set, and failed promotion validation
recorded above.

**PASS.**

The revised design has one cohesive current array owner, one indexed raw owner
for unresolved storage-provider mechanics, coherent current-owner integration,
live destinations for every deferral, and a complete direct reading path.

#### Revised owner structure

Retain the flat current `language/` directory with:

```text
language/
  arrays-and-slices.md
```

The current array owner teaches programmer-visible array behavior, storage
selection, shared provider/unique handle ownership, storage-erased references,
capacity, stability, cost, and failure. It does not define the provider/handle
protocol.

Add one indexed raw future owner:

```text
project/raw/
  array-storage-strategies.md
```

That raw owner preserves exact provider/handle, slot/chunk, alignment,
stability-token, provider-factory, context-hook, consuming-range-transfer,
erasure, capability, and storage-change pressure.

Retain minimal temporary stubs at:

```text
arrays.md
project/raw/arrays-and-slices-maintainer-notes.md
project/raw/indexing-and-slicing.md
```

The stubs own no behavior, remain outside public/current and raw navigation,
exist solely so this active work file's immutable links resolve, and must be
deleted when this work item is archived.

No new implementation, specification, array subdirectory, or compatibility
layer is required.

#### Revised ownership consequences

- `language/arrays-and-slices.md` owns logical arrays, required `from`
  expansion, usable capacity, programmer-visible storage selection,
  provider/handle ownership, erased references, `size of` consequences,
  lifecycle, slicing, joining, and stability behavior.
- `language/execution-context.md` owns the programmer-visible rule that an
  owning representation profile resolves before layout while `___` may supply
  a compatible provider instance.
- `language/pointers-and-arenas.md` owns strong provider ownership, unique array
  handle provenance/disposition, and the distinction between one array object
  root, provider physical allocations, and non-root elements.
- `language/function-invocation.md` owns by-value provider selection and
  storage-erased versus exact storage-reference mapping.
- `language/lifetimes-and-references.md` owns the fixed source-array origin of
  erased references and element/slice invalidation.
- `language/transfer-stances.md` owns general stance meaning while arrays owns
  provider reuse, complete-handle transfer, and the passive-slice boundary.
- `language/structural-shapes-and-compatibility.md` owns resolved storage
  profile as owning representation shape without making storage part of logical
  array identity.
- `language/source-structure.md` owns required array-entry `from`, storage
  clauses, range forms, array/capture delimiters, and formatting.
- `language/operator-catalog.md` owns current length/bound/capacity query forms.
- `language/terms.md` owns the reused storage-provider/profile and
  storage-erased-reference terms.
- Existing construction, iteration, operator, mixfix, integer, qualifier,
  safety, string, and reflection-facing current-owner dispositions remain as
  recorded by the earlier dry run except where the revised storage model
  updates their terminology.
- `project/raw/array-storage-strategies.md` owns the unresolved storage contract.
- `project/raw/global-and-once-lifetimes.md` owns persistent shared-provider
  initialization, concurrency, and teardown pressure; an `unbound` factory does
  not make `once` memoize its result.
- Existing raw pointer, generic, reflection, analysis-control,
  string-runtime, and feature-catalog owners retain their revised reached
  consequences.

#### Revised exact promotion change set

The complete revised promotion consists of exactly these 33 paths:

1. Replace `arrays.md` with a temporary historical routing stub.
2. Update `index.md`.
3. Add `language/arrays-and-slices.md`.
4. Update `language/construction-and-destruction.md`.
5. Update `language/declarations-and-bindings.md`.
6. Update `language/execution-context.md`.
7. Update `language/function-invocation.md`.
8. Update `language/integers.md`.
9. Update `language/iteration.md`.
10. Update `language/lifetimes-and-references.md`.
11. Update `language/mixfix-operators.md`.
12. Update `language/operator-catalog.md`.
13. Update `language/operators.md`.
14. Update `language/pointers-and-arenas.md`.
15. Update `language/qualifiers.md`.
16. Update `language/safety-and-analysis.md`.
17. Update `language/source-structure.md`.
18. Update `language/strings-and-characters.md`.
19. Update `language/structural-shapes-and-compatibility.md`.
20. Update `language/terms.md`.
21. Update `language/transfer-stances.md`.
22. Update `project/raw/README.md`.
23. Update `project/raw/analysis-controls.md`.
24. Add `project/raw/array-storage-strategies.md`.
25. Replace `project/raw/arrays-and-slices-maintainer-notes.md` with a temporary
    historical routing stub.
26. Update `project/raw/feature-catalog.md`.
27. Update `project/raw/global-and-once-lifetimes.md`.
28. Replace `project/raw/indexing-and-slicing.md` with a temporary historical
    routing stub.
29. Update `project/raw/pointer-and-arena-mechanics.md`.
30. Update `project/raw/reflection.md`.
31. Update `project/raw/string-runtime-and-encoding.md`.
32. Update `project/raw/type-parameters-and-generics.md`.
33. Update this active work file with the revised dry run, application, and
    validation record.

No other current, raw, router, legacy, project, archive, prompt, implementation,
or external path is part of this revised promotion.

### Revised promotion application and validation: PASS

The maintainer authorized promotion after alignment of the revised storage
model. The complete 33-path set above has been applied.

Validation passes:

- the new current owner begins with ordinary array use and layers advanced
  storage, proxy, lifecycle, diagnostics, and reference details later;
- every ordinary array-expression entry contributes one value and every
  sequence expansion writes contextual `from`;
- `T[0..]`/`T[..]`, `T[0..0]`, and invalid `T[0..<0]` are taught consistently;
- current length uses `.length()`, while type minimum/maximum and usable
  capacity remain distinct;
- physical provider backing is not reported as usable array capacity;
- storage provider versus unique array handle ownership is consistent across
  array, pointer, context, invocation, lifetime, transfer, structural, terms,
  and raw owners;
- `size of T` and `size of T &` remain distinct and exclude external backing;
- one unconstrained concrete array-reference implementation can accept inline,
  instance-selected, and type-selected storage through the erased contract;
- passive slice `last` does not transfer element/backing ownership;
- exact provider access and erased unsafe provider-object access do not imply
  element contiguity;
- the type-owned default-provider factory is `unbound`; persistent shared state
  remains separate `once`/global/context pressure;
- all new current-owner relative links and heading anchors resolve;
- no current language owner cites this work item or raw input for meaning;
- `index.md` routes to the current owner and no longer routes legacy arrays;
- the new raw storage owner is indexed;
- every immutable active-work evidence link resolves through a temporary
  non-authoritative stub;
- the three stubs are absent from public/current and raw navigation and are
  explicitly scheduled for deletion during archival;
- every known invalid promoted example is marked inline;
- current string teaching does not use slice syntax for an owning decoded-byte
  result;
- the working tree contains exactly the 33 revised promotion paths;
- the staged/unstaged comparison boundary remains untouched; and
- `git diff --check` passes.

The promotion is ready for maintainer review. It is not authorization to stage,
commit, archive this work item, create the next work item, or alter the current
review boundary.

### Final maintainer-review refinements

The maintainer aligned these final corrections after the revised storage
promotion:

- No historical routing stubs are retained. Root `arrays.md` and both consumed
  raw array inputs are deleted. Archived project work uses Git history to
  recover former evidence paths.
- `writable immutable varying` array access permits compatible whole-array
  reconstructive replacement while preventing element, length, capacity,
  suggestion, and storage mutation during the current immutable array lifetime.
- One storage clause follows the complete multidimensional array type and
  applies to every dimension. Per-dimension storage providers are unavailable.
- A type alias may retain `storage type MyStorage` but cannot capture a runtime
  provider instance.
- Multidimensional indexing uses chained brackets only:
  `matrix[row][column]`. Comma-based `matrix[row, column]` is unsupported.
- Slicing the outer dimension of `Integer[5][6]` produces `Integer[][6]`, a
  slice of fixed six-element rows. `Integer[][]` would be an outer slice of
  inner slices.
- Bounded array references accept a source when its current length fits the
  parameter range. Writable operations check both the parameter range and
  source array's actual range at runtime; readonly references perform no
  size-changing operation.
- Contiguous `[[ ... ]]` belongs exclusively to lambda capture. Compiler
  directives use `[< ... >]`.

These findings supersede every contrary earlier candidate or promotion passage
in this working record.

### Final revised documentation-fit dry run: PASS

**PASS.**

The final refinement preserves one cohesive current array owner, one indexed
raw storage-strategy owner, unique current ownership for source/directive rules,
complete cross-owner consequences, and explicit future destinations.

#### Final structure and disposition

- `language/arrays-and-slices.md` remains the cohesive current owner.
- `project/raw/array-storage-strategies.md` remains the indexed future protocol
  owner.
- Root `arrays.md`,
  `project/raw/arrays-and-slices-maintainer-notes.md`, and
  `project/raw/indexing-and-slicing.md` are deleted without stubs.
- `language/source-structure.md` owns `[< ... >]` directive enclosure and
  `[[ ... ]]` capture separation.
- Legacy `compiler-directives.md` retains historical directive content but
  routes readers to the current enclosure and warns against copying `[[...]]`
  directive spelling.
- Raw compile-time and export/visibility inputs use the current directive
  enclosure while retaining unresolved directive meanings.
- Existing immutable project-record links to deleted evidence paths are
  historical and intentionally rely on Git history under the maintainer's
  explicit disposition. No public/current or raw router depends on those
  paths.

#### Final exact promotion change set

The final promotion consists of these 39 paths:

1. Delete `arrays.md`.
2. Update `compiler-directives.md`.
3. Update `index.md`.
4. Add `language/arrays-and-slices.md`.
5. Update `language/construction-and-destruction.md`.
6. Update `language/core-flow-control.md`.
7. Update `language/declarations-and-bindings.md`.
8. Update `language/execution-context.md`.
9. Update `language/function-invocation.md`.
10. Update `language/integers.md`.
11. Update `language/iteration.md`.
12. Update `language/lifetimes-and-references.md`.
13. Update `language/mixfix-operators.md`.
14. Update `language/namespaces-and-modules.md`.
15. Update `language/operator-catalog.md`.
16. Update `language/operators.md`.
17. Update `language/pointers-and-arenas.md`.
18. Update `language/qualifiers.md`.
19. Update `language/safety-and-analysis.md`.
20. Update `language/source-structure.md`.
21. Update `language/strings-and-characters.md`.
22. Update `language/structural-shapes-and-compatibility.md`.
23. Update `language/terms.md`.
24. Update `language/transfer-stances.md`.
25. Update `meta-functions.md` only to route legacy directive spellings to the
    current enclosure; independently staged maintainer edits in that file
    remain outside this promotion.
26. Update `project/raw/README.md`.
27. Update `project/raw/analysis-controls.md`.
28. Add `project/raw/array-storage-strategies.md`.
29. Delete `project/raw/arrays-and-slices-maintainer-notes.md`.
30. Update `project/raw/compile-time-execution.md`.
31. Update `project/raw/export-and-visibility-directives.md`.
32. Update `project/raw/feature-catalog.md`.
33. Update `project/raw/global-and-once-lifetimes.md`.
34. Delete `project/raw/indexing-and-slicing.md`.
35. Update `project/raw/pointer-and-arena-mechanics.md`.
36. Update `project/raw/reflection.md`.
37. Update `project/raw/string-runtime-and-encoding.md`.
38. Update `project/raw/type-parameters-and-generics.md`.
39. Update this active work file with the final findings, dry run, application,
    and validation.

Maintainer edits to `functions.md` and `type-definition.md` remain outside this
promotion and are preserved unchanged.

### Final promotion application and validation: PASS

The maintainer authorized promotion of the final aligned model. The 39-path set
above has been applied.

Validation passes:

- the array owner teaches ordinary fixed/resizable/slice use before storage,
  custom operator, lifecycle, and diagnostic detail;
- sequence expansion always uses array-entry `from`;
- one storage clause applies after all dimensions and aliases capture only
  provider types, never instances;
- array qualification distinguishes immutable in-lifetime state from writable
  varying whole-value replacement;
- multidimensional access uses only chained brackets;
- outer-dimension slice types preserve inner fixed dimensions;
- bounded readonly/writable reference behavior is explicit;
- current directive source uses `[< ... >]` and current lambda source uses
  `[[ ... ]]`;
- legacy directive material is clearly marked and routed;
- the raw storage strategy record owns provider/handle protocol, stability,
  erased-reference, context, transfer, and capability pressure;
- all current-owner relative links and heading anchors resolve;
- no current language owner cites this work item or raw input for meaning;
- `index.md` routes only to the current array owner;
- the raw index reaches every live future owner and omits consumed inputs;
- every known invalid promoted example is marked inline;
- current string documentation does not use slice syntax for an owning decoded
  byte result;
- no compatibility/reference stub remains;
- the promotion touches exactly the 39 paths above while preserving the two
  independent maintainer-edited paths;
- the staged/unstaged review boundary remains untouched; and
- `git diff --check` passes.

The promotion is ready for maintainer review. It does not authorize staging,
commit, archival, continuation, or changes to independently edited lambda
source.

### Closure

The language maintainer accepted the completed `025` promotion and explicitly
authorized archival, creation of the next work item, staging, commit, and push
on 2026-09-18.

- All aligned array, index, slice, capacity, storage-strategy, traversal,
  transfer, source, directive-collision, and cross-owner findings have current
  or indexed future owners.
- The final documentation-fit dry run and promotion validation passed.
- Root legacy array input and consumed raw inputs retired without stubs;
  historical references in this record rely on Git history.
- `project/raw/nothing-maintainer-notes.md` was indexed without being read.
- Work item `026` was created for Nothing values, states, and instances, using
  the maintainer notes and existing Nothing input as its fixed initiating
  evidence.
- No analysis of work item `026` was performed during closure.
