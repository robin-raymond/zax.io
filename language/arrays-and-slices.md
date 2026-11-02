# Zax arrays and slices

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers storing, reading, changing, viewing, or traversing ordered data |
| Applies To | Programmer-facing intrinsic arrays, array expressions, indexing, slicing, capacity, layout, and element lifetime; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | Fixed and resizable array identities; logical length and usable capacity; array expressions and inference; intrinsic index and splice behavior; borrowed slices; direct splice mutation; element-place stability; programmer-visible storage selection and capabilities; array transfer and joining; storage-erased array references; array parameter behavior; array-specific costs, diagnostics, reflection facts, and future boundaries |
| Does Not Own | General construction packets and lifecycle machinery ([construction, replacement, and destruction](construction-and-destruction.md)); reusable reference lifetimes ([lifetimes and references](lifetimes-and-references.md)); pointer ownership and arenas ([pointers, allocation, and arenas](pointers-and-arenas.md)); exact array storage-provider/handle protocols; shared operator and mixfix selection ([operators](operators.md), [mixfix operators](mixfix-operators.md)); general traversal protocol behavior ([iteration](iteration.md)); qualifier meaning ([qualifiers](qualifiers.md)); transfer-stance meaning ([transfer stances](transfer-stances.md)); general integer roles ([integers](integers.md)); general structural transformation ([structural shapes and compatibility](structural-shapes-and-compatibility.md)); runtime string APIs; user-defined generics; or implementation layout and lowering |

## Start with an ordinary array

Use `[ ... ]` to provide the values of an array:

```zax
values : Integer[5] = [ 10, 20, 30, 40, 50 ]

third := values[2]
third = 300
// values[2] is still 30; third is an independent Integer.

thirdInArray : Integer writable & = values[2]
thirdInArray = 300
// values[2] is now 300.
```

`Integer[5]` says that `values` always contains exactly five integers. Array
indexes start at zero, so `values[2]` selects the third integer.

What happens after selection depends on how the result is used:

- `third := values[2]` constructs a separate `Integer` from the selected
  element.
- `thirdInArray : Integer writable & = values[2]` keeps access to that element
  inside `values`.

Direct traversal visits every element in increasing index order:

```zax
each element : in values {
  print(element)
}
```

The inferred traversal binding accesses each array element directly. An
explicit value binding would instead construct a separate value for each pass.

## Fixed arrays, resizable arrays, and slices

Zax provides three related sequence forms.

### Fixed arrays

```zax
values : Integer[5]
```

A fixed array always contains its declared number of elements. Its length is
part of its type. It can change individual elements when their types and the
current access allow, but it cannot insert, remove, or resize.

### Resizable arrays

```zax
scores : Integer[0..100]

scores <+>= [ 10, 20 ]
// scores now contains two elements.
```

`Integer[0..100]` allows the array to contain anywhere from zero through one
hundred integers. The array begins with the minimum required count—zero here—and
size-changing operations must keep it inside those limits.

The range is a contract on the array. It does not mean that storage for one
hundred elements is immediately reserved.

### Slices

```zax
values : Integer[5] = [ 10, 20, 30, 40, 50 ]
middle := values[1..<4]

middle[0] = 200
// values[1] is now 200.
```

A slice is a window into part of an existing array. `middle` views the three
elements at indexes `1`, `2`, and `3`; it does not create another copy of them.

The slice can remember a narrower window, but it cannot expand beyond the
elements it was originally allowed to view. The array must remain alive, and
operations that move or remove viewed elements can invalidate the slice.

`T[N]`, ranged arrays, and `T[]` slices are built into Zax. Their syntax does
not establish how programmers declare general generic types.

## Array expressions

An array expression constructs one owning array:

```zax
numbers := [ 1, 2, 3 ]
// Integer[3] using the resolved default storage profile

bytes : U8[3] = [ 1, 2, 3 ]
```

When the destination supplies a type, each element is constructed as that type.
Without a destination type, all entries must establish one common element type.

### Inferring the element type

Number literals that have not yet selected an integer type remain uncommitted
while the array is considered:

```zax
ordinary := [ 1, 2, 3 ]
// Every number uses the ordinary suggested default: Integer.

small := [ (: U8 = 1), 2 ]
// The committed U8 causes 2 to become U8 as well.
```

All committed elements must have the same concrete type:

```zax
myU8 : U8 = 1
myU16 : U16 = 2

bad := [ myU8, myU16 ]
// error: the committed element types differ
```

Inference does not choose the first entry, widen one integer to another, or
invent an implicit union or a conceptual “any” type. Zax has not established
`Any`, `Variant`, or `Union` as the name of such a type.

An empty expression cannot supply an element type:

```zax
empty := [] // error: no element type can be inferred
typedEmpty : Integer[0] = []
openEmpty : Integer[0..] = []
```

`[]` is the explicit zero-entry array expression. `[ ]` is not an alternate
empty spelling. In nested source, `[ [] ]` contains one empty inner array:

```zax
zeroRows : Integer[0..][0..] = []
oneEmptyRow : Integer[0..][0..] = [ [] ]
```

Omitted storage resolves through the declaration's applicable default storage
profile. A concrete provider type or instance can be stated when its storage
behavior is required.

### Construction packets as elements

A construction packet gets its destination type from the array element:

```zax
items : MyItem[3] = [
  [{ "first", 3 }],
  [{ "second", 7 }],
  [{}]
]
```

The outer `[ ... ]` constructs the array. Each inner `[{ ... }]` supplies the
constructor inputs for one `MyItem`.

```zax
unknown := [ [{ "first", 3 }] ]
// error: the packet has no expected element type
```

Packets do not independently infer their destination type.

### Nested arrays and lambda capture

Equal nested shapes infer fixed dimensions:

```zax
matrix := [ [ 1, 2 ], [ 3, 4 ] ]
// Integer[2][2] using the resolved default storage profile
```

The spaces in `[ [` and `] ]` are meaningful. Contiguous `[[]]` is an empty
lambda capture and nonempty capture requires interior spacing:

```zax
callback := [[ matrix ]] ()() {
  print(matrix[0][0])
}
```

Compact `[[matrix]]` is not a legal capture, and compact
`[[1, 2], [3, 4]]` is not an array spelling. Complete capture behavior belongs
to [Zax lambdas and callable composition](lambdas-and-callable-composition.md).

Rows of different sizes need an explicit type:

```zax
invalidJagged := [ [ 1, 2 ], [ 3 ] ]
// error: jagged inference requires an explicit type

jagged : Integer[2][1..2] = [
  [ 1, 2 ],
  [ 3 ]
]
```

The explicit declaration confirms that each inner row is allowed to resize
between one and two elements. Zax does not infer those future permissions from
the two initial row sizes.

## One element or several elements

Each ordinary entry contributes one value. A bare array therefore becomes one
element of an outer array:

```zax
firstRow : Integer[3]
secondRow : Integer[3]

rows := [ firstRow, secondRow ]
// Integer[2][3]
```

It does not automatically unpack into an array of six integers:

```zax
invalid : Integer[6] = [ firstRow, secondRow ]
// error: two Integer[3] values are not six Integer values
```

Writing a slice still contributes one slice value:

```zax
slices := [ firstRow[..], secondRow[..] ]
// Two Integer[] slice elements.
```

Write contextual `from` when the surrounding array should receive the sequence
elements:

```zax
values := [ from firstRow, from secondRow ]
// Integer[6]

alsoValues := [ from firstRow[..], from secondRow[..] ]
// Integer[6]; the full slices are redundant but explicit.
```

The same rule applies to a named slice:

```zax
view := firstRow[..]
views := [ view ]
expanded := [ from view ]
```

`from` means sequence expansion through direct array/slice traversal or the
cursor protocol. Structural `>-` decomposition remains a different operation.

### Exact contribution count

An exact-sized destination must receive exactly that many elements:

```zax
source : Integer[3] = [ 1, 2, 3 ]
destination : Integer[2] = [ from source ]
// error: three elements cannot initialize exactly two
```

Zax does not silently discard the extra element. When the count depends on a
runtime slice, the same mismatch is checked while the program runs.

If the destination allows several lengths and does not state an initial count,
the contributed count becomes its current length:

```zax
destination : Integer[0..10] = [ from source ]
// destination currently contains three elements.
```

### Runtime-sized slice contributions

An explicit runtime-sized slice produces a resizable result:

```zax
runtimeValues := [ from makeRuntimeSlice() ]
// MyItem[0..] using the resolved default storage profile

withOne := [ from makeRuntimeSlice(), makeItem() ]
// MyItem[1..] using the resolved default storage profile
```

The first result can contain any count supplied by its runtime slice. The second
always has at least the separately written `makeItem()` element.

These are resizable arrays, not runtime-dependent fixed array types.

## Iterable contributions

Use contextual `from` to fill an array from any cursor-driven iterable:

```zax
values : Integer[10] = [
  from makeMeRepeatIterable(0, 10)
]
```

The explicit `from` matters because every bare array, slice, or other iterable
is otherwise one ordinary array element:

```zax
fromArray := [ from sourceArray ]
fromSlice := [ from sourceArray[1..<4] ]
```

### When an iterable knows its count

Some iterables know exactly how many values they will produce. They may provide
the post-unary `count` operation:

```zax
MyIterable :: type {
  operator post unary 'count' final : (
    result : IndexSize
  )() readonly = {
    // Return exactly how many values the next traversal will produce.
  }
}
```

The operation is optional, but its returned count is exact. Array construction
may ask for that count once and reserve enough storage before traversal.

If the iterable later produces another number of values, the array operation
panics because the iterable broke its promise.

An iterable without `count` is still valid. A resizable destination grows while
values arrive; a fixed destination already knows its required storage. Explicit
capacity reservation or suggestion can avoid repeated growth.

### Evaluation and construction order

Array construction:

1. evaluates each ordinary entry and iterable-source expression once from left
   to right;
2. keeps the resulting values and iterable sources alive;
3. asks count-capable sources for their exact counts in entry order;
4. reserves final storage when the complete count is known, or at least the
   known minimum when one or more sources have no count;
5. consumes slices and iterables in entry order; and
6. constructs array elements in increasing index order.

For an uncounted iterable, each required storage expansion finishes before the
next element is constructed. The incomplete array is not available to ordinary
code.

For example:

```zax
values : Integer[0..] = [
  mutateThenReturnCurrentCount(firstIterable),
  from firstIterable,
  mutateThenReturnCurrentCount(secondIterable),
  from secondIterable
]
```

If only `firstIterable` supplies `count`, both mutation expressions and both
source captures run first. Construction then asks for the first iterable's
post-mutation count and reserves the two ordinary elements plus that known
count. It fills entries in written order and grows incrementally while consuming
the uncounted second iterable.

### Defaulting the remainder

Final bare `..` default-constructs the exact number of still-missing elements:

```zax
values : MyItem[5] = [
  makeFirst(),
  makeSecond(),
  ..
]
```

Here `..` default-constructs three more `MyItem` values. It requires an exact
target count and a default constructor. It cannot infer an arbitrary larger
length.

An iterable before `..` contributes until it ends. Producing too many values is
a count mismatch; ending early leaves the remaining elements for `..`.

Repeated values use an ordinary iterable such as
`makeMeRepeatIterable(value, count)`. Arrays need no separate repetition
syntax.

### Nothing preparation does not create elements

Compiler-provided Nothing backing does not ordinary-construct array elements.
An array member reached through a Nothing receiver is therefore invalid when the
operation requires an element:

```zax
MyArrayOwner :: type {
  values : Integer[5]

  first final once : (result : Integer)() = {
    return _.values[0] // invalid; trap/panic when target support permits
  }
}
```

Zeroed or reserved storage is not an array with live elements. A type may
instead choose trapping Nothing behavior or prepare a custom Nothing instance.
Complete policy behavior belongs to
[Zax Nothing instances](nothing-instances.md#what-compiler-preparation-provides).
A future narrower prepared-array form must preserve the element-lifetime rules
in this document. Targets without suitable trap support do not receive a
software vacancy check before every element access; relying on the missing trap
has undefined behavior.

## Logical length ranges

Array declaration ranges describe the counts that one array is allowed to have:

```zax
first : Integer[0..3]
// May contain 0, 1, 2, or 3 elements.

second : Integer[0..<3]
// May contain 0, 1, or 2 elements.

third : Integer[1 in 0..3]
// May contain 0 through 3 elements and initially contains 1.

fourth : Integer[2 in 0..3]
// May contain 0 through 3 elements and initially contains 2.

unboundedA : Integer[0..]
unboundedB : Integer[..]
// Both may contain any IndexSize-supported count from zero upward.

onlyEmpty : Integer[0..0]
// Current and only permitted length is zero.

impossible : Integer[0..<0]
// error: this half-open range contains no permitted length
```

`..` includes its upper count. `..<` excludes it.

`N in` states the exact initial count. It does not state capacity and is not
part of the array's type identity. A known invalid `N` is a compile-time error;
a runtime-invalid count panics unless a reporting operation was selected.

Without an initializer, a resizable array begins at its minimum required count
and default-constructs those elements.

`Integer[0..]` and `Integer[..]` resolve to the same array identity. Current
teaching usually writes the explicit zero minimum.

Exact `Integer[0]` and inclusive `Integer[0..0]` can represent an empty array.
The mathematical range `Integer[0..<0]` contains no allowed count and therefore
cannot be used for a value declaration.

## Length, capacity, and suggestion are different

Three numbers answer different questions:

- **Length:** How many elements exist now?
- **Capacity:** How many elements fit in already reserved storage?
- **Suggested capacity:** How much storage has the programmer suggested may be
  useful?

Every array and slice exposes its current logical length:

```zax
current : IndexSize = values.length()
```

A slice's length is the number of elements in its current window.

Type bounds are separate from current length:

```zax
ValuesType :: alias type type of values

minimum : IndexSize = minimum length of ValuesType
maximum : IndexSize? = maximum length of ValuesType
```

Minimum is always concrete. Maximum is absent for an open range. An exact
`T[N]` reports `N` for both.

For an array with finite maximum `M`:

```text
minimum length <= current length <= capacity <= M
```

Capacity beyond current length is only storage. No elements exist there yet.

### Querying capacity

```zax
actual : IndexSize = capacity of values
suggested : IndexSize? = suggested capacity of values
```

`capacity of` always returns the **usable capacity**: how many elements this
array could hold in its current backing without obtaining different element
storage and without exceeding its declared maximum. An array with no backing
storage has zero capacity.

The storage provider may physically own more. For example, a 128-slot chunk
backing an array whose maximum is 100 still produces array capacity 100.
Provider-specific inspection may expose physical storage separately.

`suggested capacity of` returns an optional count. It is an **absent optional**
until a suggestion is supplied.

### Reserving storage

```zax
values.reserveCapacity(128)
```

After normal completion, at least 128 elements fit without obtaining more
element storage. Length and existing values are unchanged. Reserving may move
all existing elements into a larger region, so references and slices can be
affected.

```zax
values.trimCapacity()
```

Trimming may release unnecessary physical storage but never reduces usable
capacity below current length. It may also move elements.

A request beyond the array's finite maximum is a compile-time error when known
and a panic otherwise.

### Setting and clearing a suggestion

```zax
values.suggestCapacity(256)

unset : IndexSize?
values.suggestCapacity(unset)
```

`suggestCapacity` accepts `IndexSize?`. A concrete `IndexSize` becomes a present
optional and sets the suggestion. An absent optional clears it.

Changing the suggestion records guidance only. It performs no immediate
allocation and moves no elements. Usable capacity may remain below or already
exceed the suggestion.

A known suggestion beyond a finite maximum is an error. A runtime value beyond
that maximum panics. A provider-specific physical-backing hint is a different
operation and may legitimately request storage the current array cannot expose
as usable capacity.

The compiler or runtime may use static bounds for private allocation choices,
but such a choice does not become a visible programmer suggestion.

## Slicing

Slice ranges describe element indexes, not permitted array lengths:

```zax
values : Integer[6] = [ 10, 20, 30, 40, 50, 60 ]

inclusive := values[0..5]
// Indexes 0 through 5: six elements.

halfOpen := values[0..<5]
// Indexes 0 through 4: five elements.

one := values[0..0]
// One element.

empty := values[0..<0]
// A valid empty slice at boundary 0.
```

A half-open slice includes its start and excludes its end. Both boundaries may
be equal, which creates an empty slice. The end may equal `values.length()`.

An inclusive slice includes both indexes. Internally, the same interval can be
described with an exclusive end one greater than the written inclusive end.

Omitted endpoints mean:

```text
values[start..<end] -> start through end - 1
values[start..end]  -> start through end
values[..<end]      -> beginning through end - 1
values[..end]       -> beginning through end
values[start..]     -> start through the last element
values[..]          -> the complete array
```

`start..<` is malformed because there is no end to exclude.

A known reversed or out-of-bounds range is a compile-time error. A runtime
required slice checks its boundaries and panics when they are invalid. It never
silently shortens the requested range or substitutes an empty slice.

## Qualifying an owning array

Array qualification separates mutation of the current array value from
replacement of the whole array lifetime:

```zax
myArray varying :
  Integer[4 in 4..10] immutable writable varying = [ 1, 2, 3, 4 ]
```

The current array is immutable. Element assignment, insertion, removal,
resizing, capacity changes, suggestion changes, and storage changes cannot
mutate that lifetime.

The place itself is varying, this declaration retains replacement permission,
and the access path is writable. Compatible whole-array `.=` reconstruction may
therefore end the old immutable array and construct another immutable array in
the same place:

```zax
myArray[0] = 10      // error: mutates the current immutable array
myArray.resize(6)    // error: mutates the current immutable array
myArray .= replacement
// valid when replacement satisfies the array and storage contracts
```

`writable` is meaningful here because it permits whole-value replacement; it
does not make immutable elements mutable.

## Slice qualification and narrowing

Array and slice syntax can qualify the viewed elements separately from the
slice value:

```text
T <element/type qualifiers> [] <slice qualifiers>
```

Words before `[]` describe access to the elements. Words after `[]` describe
the slice descriptor—its current start, length, origin, and validity state.

Creating a slice may preserve or reduce the source authority. It cannot make a
readonly element writable, make immutable state mutable, or extend an element's
lifetime.

Copying a slice copies only its descriptor:

```zax
original := values[1..<5]
copy := original
copy = copy[1..<3]
// original is unchanged.
```

A reference to the descriptor changes the original slice:

```zax
alias : Integer[] & = original
alias = alias[1..<3]
// original now describes this narrower window.
```

The replacement must be an equal or narrower view with the same array origin.
It cannot widen the window, adopt a foreign array, revive an invalid slice, or
retain a view into an expiring temporary.

An element reference obtained before descriptor narrowing remains bound to its
original selected element.

## Slice lifetime and invalidation

A slice remembers the elements selected when it was created. It does not mean
“whatever values later occupy these numeric indexes.”

```zax
values : Integer[0..] = [ 10, 20, 30, 40, 50 ]
view := values[0..3]

values[4..<4] = [ 99 ]
// view survives only when this insertion cannot move existing storage.

values[2..<2] = [ 77 ]
// view is invalidated because insertion changes part of its viewed region.
```

The general rules are:

- An operation that may move the backing storage invalidates every slice unless
  its contract proves preservation.
- Insertion or removal invalidates slices that intersect or follow the first
  changed position.
- A slice entirely before that position may survive when storage cannot move.
- Insertion after a slice preserves it when storage cannot move.

An invalidated slice does not become empty, retarget new values, or become valid
again after later growth.

When invalidation is known from source, later use is a compile-time error.
Otherwise checked use may need origin and validity information and can panic.
Tooling must make that check cost visible. Disabling the corresponding panic
category transfers responsibility to the programmer; a false validity promise
has undefined consequences.

## Indexing element places

For current length `L`, valid indexes are `0` through `L - 1`.

```zax
copied := values[index]
direct : Integer writable & = values[index]
values[index] = replacement
values[index] ^= mask
```

The stable position inside an array that can contain an element is called an
**element place**. Intrinsic indexing selects that place. A value destination
constructs from it; a reference destination keeps access to it.

A constant invalid index is a compile-time error. A runtime required index
checks the current length and panics when invalid.

### Evaluation during indexed mutation

For:

```zax
values[chooseIndex()] = makeReplacement()
```

Zax evaluates and binds:

1. `values`;
2. `chooseIndex()`; and
3. `makeReplacement()`.

It then invokes the direct indexed-mutation operation, checks the runtime bounds,
and changes the element or enters panic. The replacement expression therefore
runs before a runtime bounds panic. A statically invalid operation executes
nothing.

Chained multidimensional indexing resolves the inner array first:

```zax
matrix[row][column] = replacement
```

The row selection completes before `column` is evaluated.

## Direct splice mutation

Using a slice on the right of `:=` or `=` normally produces a passive view:

```zax
view := values[2..4]
```

When a range appears on the left of assignment, the complete source form is one
direct structural operation:

```zax
values : Integer[0..] = [ 10, 20, 30, 40, 50 ]

values[3..<3] = [ 5, 10, 15 ] // insert at boundary 3
values[2..4] = []             // remove indexes 2 through 4
values[2..4] = [ 20, 30 ]     // replace three elements with two
```

The operation uses the array, range, assignment component, and replacement
together. It does not first create a slice and then assign through it.

Assignment to a saved slice therefore does not insert into the array:

```zax
insertionPoint := values[3..<3]
insertionPoint = [ 5, 10, 15 ]
// error: an array value cannot replace this slice descriptor
```

A fixed array can replace only with exactly as many elements as the range
contains. Insertion, removal, and unequal replacement require a resizable array,
and the resulting length must remain within its declared limits.

### Removing a saved slice

```zax
selected := values[2..4]
values.remove(selected)
```

`remove` confirms that the slice is still valid and came from this exact array,
then removes the represented interval.

```zax
firstArray : Integer[0..] = [ 1, 2, 3, 4, 5 ]
secondArray : Integer[0..] = [ 6, 7, 8, 9, 10 ]

selected := firstArray[2..<5]
secondArray.remove(selected)
// error: selected belongs to a different array
```

When the different origin is known, this is a compile-time error. Otherwise the
operation checks before mutation and panics on mismatch. It never searches for
equal values or applies the foreign numeric indexes to `secondArray`.

A slice of a slice retains the original array origin. Removing an empty
same-array slice does nothing.

### Overlapping and self-slice input

```zax
values <+>= values[2..4]
values[3..<3] = values[5..6]
```

Both operations use the source values as they existed before structural
mutation. Appending a view of the same array duplicates those values, so their
element type must support `copy`.

In general, an overlapping bulk mutation behaves as if source values needed by
later writes were saved before the first conflicting write. A separate
directional operation may offer a cheaper explicit contract.

## Growing, shrinking, and resizing

These required callables return no result:

```zax
values.grow(newLength)
values.shrink(newLength)
values.resize(newLength)
```

- `grow` does nothing when `newLength` is not larger. Otherwise it
  default-constructs the new suffix.
- `shrink` does nothing when `newLength` is not smaller. Otherwise it destroys
  the removed suffix in reverse order.
- `resize` applies the corresponding behavior.

The new length must satisfy both the current access path and the underlying
array's actual size limits. If new elements cannot be default-constructed,
`grow` and the growing path of `resize` are unavailable.

Splice insertion supplies nondefault new elements:

```zax
values[values.length()..<values.length()] = incoming
```

## Element-place stability

References and slices remain useful only while their selected element places
continue to exist.

The ordinary rules are:

- Changing an element's contents preserves its place.
- Replacing one complete element may begin a new element lifetime in the same
  place.
- Growing at the end within existing capacity preserves old places and creates
  new ones.
- Moving to new backing storage relocates every element and invalidates access
  to the old places.
- Shrinking ends the removed suffix and preserves the earlier prefix.
- Insertion preserves an unaffected prefix at most and moves or replaces later
  elements.
- Removal ends selected elements and may move or replace later elements.
- Changing capacity removes no logical element but may relocate every element.

An unchanged index is not enough to prove that the old element still exists
there.

The array and storage provider determine invalidation together. A checked slice
may retain a stability kind/token and the version observed when it was created.
When storage reports that a relevant chunk or mapping changed, the array
advances the corresponding version. Later slice use compares that information
and panics after invalidation.

The exact token representation remains future storage-contract work. A slice
covering several chunks may need a composite token or conservative whole-array
invalidity. The programmer-visible requirement is that storage cannot claim an
ended or moved element place remained valid, and stronger stability must come
from an exposed provider capability.

Moving an element into unused fresh storage uses construction. The old element
is offered as `last`, allowing `last -> move -> copy` fallback. Shifting into an
already live destination uses ordinary assignment or protected `.=` complete
reconstruction as required by the selected element contract.

If an element type cannot perform the required construction, transfer, or
replacement, the array operation is unavailable.

## Storage strategies

An array keeps the same logical behavior while different storage providers
organize its raw backing:

```zax
provider : MyStorage * strong

byDefault : Integer[50]
byInstance : Integer[50] storage provider
byType : Integer[50] storage type MyStorage
```

`byDefault` uses the storage profile selected by its declaration context.
`byInstance` must use that exact provider instance. `byType` obtains or creates
a provider satisfying `MyStorage`'s default/context contract.

Storage does not change the logical identity of an array, but it may change the
owning value's self-contained representation, available operations, cost, and
physical layout.

One storage clause follows the complete dimension list and governs the entire
multidimensional array. A type alias may retain a provider type requirement:

```zax
MyFlatArray :: alias type Integer[4][3] storage type Flat

MySpecificArray :: alias type
  Integer[4][3] storage provider type MyStorage
// error: a type alias cannot capture a runtime provider instance
```

Selecting storage independently for each dimension is unavailable. A single
provider/handle maps the complete array shape and coordinates all row and
element storage.

The storage clause follows the complete dimension list. `storage` introduces a
provider instance expression; `storage type` introduces a provider type.

### Who owns the storage

One provider may serve many arrays. Each array keeps:

- strong participation that keeps the provider alive; and
- one unique handle for that array's independently owned backing region.

The provider owns raw bytes, slot/chunk organization, gross alignment,
physical lookup, physical extent, and storage stability information. It does
not know the Zax element type or whether one slot contains a live element.

The array owns logical bounds and knows which slots hold live values. It runs
element construction, transfer, replacement, and destruction.

For indexing, the array checks the logical position and then asks the provider
handle to locate a suitably sized/aligned raw slot or chunk. For relocation,
the provider supplies fresh destinations or a relocation plan; the array moves
the actual elements. A provider never blindly copies unknown live values as
raw bytes.

The exact provider/handle protocol is deferred to dedicated future storage work.

### Shared and dedicated providers

Supplying a provider instance allows several arrays to share the storage
service while retaining separate unique backing handles.

A storage type may provide a receiverless default-provider factory:

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

`unbound` lets the factory be called through `MyStorage` without an instance.
`once` on a function would not memoize its result. Persistent shared state
belongs to the future `once` value, the execution context, or another explicit
lifetime owner.

If no shared-provider contract exists, a storage type may obtain or construct a
dedicated provider. The applicable cost must remain visible.

### Provider-specific and erased access

When `storage type MyStorage` is present, code may use safe provider-specific
operations under the provider and handle's declared qualifications.

When the provider type has been erased, ordinary array operations remain
available through the common storage contract. The array may expose an unsafe
raw pointer to the provider object for a programmer who asserts its concrete
type. That pointer:

- does not point to array elements;
- owns no provider lifetime;
- gives no contiguity guarantee; and
- uses ordinary pointer
  [`unsafe cast`](casting.md#unchecked-reinterpretation-with-unsafe-cast)
  responsibility.

A discontiguous provider has no universal raw element pointer. Unsafe source
cannot manufacture one.

### Contiguous storage

`Flat` can be one concrete storage provider:

```zax
values : Sample[500] storage type Flat
```

Its contract can guarantee one contiguous increasing-index element region. A
resizable flat provider may use existing reserved slots or relocate every
element into a larger region.

Other provider types may offer the same contiguous capability. A future
storage-capability requirement should let an API request contiguity without
requiring the exact `Flat` type.

### Multidimensional storage

Dimension order still matches chained indexing:

```zax
matrix : Integer[4][3] storage type Flat
element := matrix[row][column]
```

When `Flat` accepts this shape, its dense multidimensional contract is
rectangular and row-major: neighboring column values are adjacent and the
rightmost dimension changes fastest.

Another provider may support jagged rows:

```zax
rows : Integer[4][1..10] storage type MyJaggedStorage
selectedColumns := rows[row][1..<3]
```

A provider may reject a shape it cannot represent. A padded rectangular
provider may support varying row lengths without claiming that all live values
form one dense flat sequence.

Every bracket supplies one index or one sliced dimension. Multidimensional
access therefore chains brackets as `matrix[row][column]`. Rectangular and
strided block views remain future work.

Slicing the outer dimension preserves the inner row type:

```zax
myValues : Integer[5][6]

rowsView := myValues[..]
// Integer[][6]: a slice of fixed six-element rows.

rowView := myValues[0][..]
// Integer[]: a slice of Integer elements in one row.
```

`Integer[][]` would be an outer slice whose elements are themselves slices.
`myValues[..]` does not have that type because its row elements remain fixed
`Integer[6]` arrays.

### Changing storage

A future explicit storage-change operation obtains suitable target slots,
transfers elements in index order, ends old element places, releases the old
unique handle, and publishes the new provider only after completion.

The transition invalidates element references, slices, pointers, and cursors
unless a stronger stability contract proves continuity.

## Copy, deep, move, and last

Copying an owning array creates another array with independent element places
and backing storage. Each element uses its ordinary `copy`, which may still
share internal resources according to that element type's contract.

`deep` also creates independent array storage and requires every element to
support its exact deep-copy contract. It never falls back to ordinary copy.

An array using inline storage cannot donate that enclosing storage:

- `move` transfers each element while leaving a valid moved-from element in the
  source array.
- `last` may take more resources from each element because the source only needs
  to remain safe to destroy.

A provider-backed array—including a fixed-length one—may transfer its complete
provider participation and unique backing handle when the destination accepts
them. `move` may do so only if the source remains a valid live moved-from array.
`last` only needs to leave enough local state for one correct destruction.

Copying or moving a slice affects its window description only. It never
implicitly copies or moves borrowed elements and cannot transfer the unique
backing handle.

Storage-assisted transfer of an element range therefore needs a consuming
operation rooted in the owning array. Future storage work may detach complete
chunks or fall back to elementwise transfer, but a passive `slice as last`
cannot authorize either operation.

## Joining arrays

`<+>` constructs a new owning array:

```zax
first : Integer[3] = [ 1, 2, 3 ]
second : Integer[2] = [ 4, 5 ]

joined := first <+> second
// Integer[5]
```

`<+>=` appends a sequence to an existing resizable array:

```zax
values : Integer[0..] = [ 1, 2 ]
incoming : Integer[2] = [ 3, 4 ]
oneMore : Integer = 5

values <+>= incoming
values <+>= [ oneMore ]
```

Arrays do not provide intrinsic `+` concatenation.

Ordinary `<+>` preserves both inputs by copying their elements. An explicit
stance may permit reuse:

```zax
reused := left as last <+> right
```

A compatible provider-backed left array may donate its unique backing handle.
An ordinary copy may retain the same shared provider while requesting another
unique handle. Suggested capacity does not propagate to the new array.

The current result length is the checked sum of input lengths. Static bounds add
in the same way:

```text
Integer[3] <+> Integer[2]       -> Integer[5]
Integer[1..4] <+> Integer[2..<5] -> Integer[3..8]
```

An open input maximum produces an open result maximum. If the current sum
cannot fit `IndexSize`, the operation panics.

The left input supplies the result element type. With no explicit destination
provider, storage selection may reuse the left provider or use the applicable
default according to the selected `copy`, `move`, or `last` contract. A slice
left has no unique backing handle to donate and therefore constructs a new
owning result.

`<+>=` retains the left array's type, so its new length must fit the existing
limits. A fixed array has no mutating join when the source might contribute an
element.

## Array and slice parameters

### Passing an owning array by value

```zax
consume final : ()(
  values : Integer[2..10]
) = {
}
```

The call creates an independent local array. The source must currently contain
between two and ten elements. A known impossible size is a compile-time error;
otherwise the call checks the current length and panics when it does not fit.

The source's `copy`, `deep`, `move`, or `last` stance controls how the local
array is constructed. With no explicit storage requirement, construction may
reuse the source provider or choose the destination context's default. Once
constructed, the local array may resize within its own declared limits.

A by-value parameter naming a provider instance constructs into that provider.
A parameter naming only `storage type MyStorage` obtains a provider of that
type. Either can accept a source array using another storage profile because a
new destination is being constructed.

### Passing an array by reference

An array reference without a storage requirement is storage-erased:

```zax
useAnyStorage final : ()(
  values : Integer[5] &
) = {
  values[2] = 55
}

provider : MyStorage * strong

inlineValues : Integer[5]
providerValues : Integer[5] storage provider
typedProviderValues : Integer[5] storage provider type MyStorage

useAnyStorage(inlineValues)
useAnyStorage(providerValues)
useAnyStorage(typedProviderValues)
```

All three calls use the same concrete implementation. The reference carries
enough common operation information to reach the source array, ask its storage
to locate element slots, and preserve logical bounds and qualifications.

Binding copies no elements and creates no second array. The erased reference
may be wider and its operations more indirect than a reference constrained to
one exact storage type. It guarantees ordinary array operations, not
provider-specific APIs or contiguous backing.

A storage-qualified reference requires the caller's existing array to use that
provider type or exact instance. A reference cannot obtain replacement storage
from the destination context.

Bounded references additionally retain source size capabilities:

```zax
change final : ()(
  values : Integer[0..10] &
) = {
  values.resize(10)
}

fixed : Integer[3]
change(fixed) // runtime panic when change attempts to resize to 10
```

The reference remembers both the parameter's allowed range and the source
array's actual capabilities.

At the call, `fixed` currently contains three elements, which fits `0..10`.
Inside `change`, attempting to resize it to ten still fails because the original
array is fixed at three. That runtime failure panics. Writing
`fixed.resize(10)` directly is a compile-time error.

This adapting reference may need both storage operations and size-capability
metadata.

Different parameter ranges can borrow the same resizable source:

```zax
func1 final : ()(values : Integer[100] &) = {
}

func2 final : ()(values : Integer[..100] &) = {
}

func3 final : ()(values : Integer[50..200] &) = {
}

func4 final : ()(values : Integer[..] &) = {
}

myValues : Integer[100..]
// Initial length is 100.

func1(myValues)
func2(myValues)
func3(myValues)
func4(myValues)
```

All four calls pass while `myValues.length()` is 100. If it later grows to 150,
only `func3` and `func4` pass their entry checks.

A writable parameter operation must satisfy both the parameter's visible range
and the source array's actual range. For example, `func3` can request a length
from 50 through 200, but this source still panics on a request below its own
minimum of 100.

A readonly parameter cannot resize the source, so after its entry-length check
it needs no size-changing capability. Ordinary writable references retain the
runtime checks; a future no-panic capability contract may impose stronger
static requirements.

### Passing a slice

```zax
inspect final : ()(
  values : Integer[]
) = {
}

narrow final : ()(
  values : Integer[] &
) = {
}
```

Passing a slice by value copies its window description, not its elements.
Passing it by reference lets the callee narrow the caller's slice descriptor.

Passing an array where a slice is expected may create a temporary slice for the
call. Narrowing that temporary does not change a separately named caller slice.

### Size of an array value and reference

These queries do not report the same number:

```zax
size of Integer[5]
size of Integer[5] &
```

The first reports the self-contained owning representation selected for creating
an `Integer[5]` value. Inline storage includes the elements; provider-backed
storage includes only the array's local provider ownership, unique handle,
bounds, and other metadata. External backing bytes are not counted.

The second reports the representation of the array reference itself. A
storage-erased reference may contain more than one machine pointer.

If reflection strips the reference layer, the resulting logical array type can
report the size of a newly created value under its resolved storage profile. It
cannot recover the actual owning representation erased by an already supplied
unconstrained reference.

Zax has no current `full storage size of` operation. Provider-specific APIs may
report external physical use separately.

## Traversal

Fixed arrays, resizable arrays, and slices support direct `each in`:

```zax
each element : in values {
  inspect(element)
}
```

The source is evaluated once and elements are visited in increasing index
order. The default binding accesses each element directly. An explicitly
non-reference binding constructs a separate value for every pass.

Changing an element is allowed when its qualifications permit. An operation
that might change array length, positions, or backing invalidates direct
traversal instead of changing its remaining work.

`each from` remains available through cursors for controlled erasure, reverse or
filtered order, and specialized progression. Direct traversal does not secretly
create a cursor.

## Construction and destruction

Array elements construct in increasing index order. They are destroyed in
reverse positional order. Shrinking an array likewise destroys its removed
suffix from the highest index downward.

When all source counts are known, final backing storage is obtained before the
first destination element is constructed. An uncounted iterable may require
incremental growth; each storage expansion and relocation finishes before the
next element is constructed.

A panic blocks the incomplete operation. It does not return a partially
constructed array or unwind completed elements. A matching helper may repair
the condition and resume the same operation; otherwise the process crashes.

## Custom indexing and slicing

Intrinsic arrays select real element storage directly. A custom type can make
the same bracket shapes mean another operation and return another declared
type.

```zax
MySequence :: type {
  operator index final : (
    result : MyResult
  )(
    index : IndexSize
  ) readonly = {
  }
}
```

A custom index may return a value, a reference, or a helper object that later
performs access. Such a helper is called a **proxy**. Proxies are useful when no
ordinary addressable element exists—for example, one packed bit—but they require
their own type, lifetime, qualification, and cost rules.

Ordinary intrinsic arrays need no proxy for element mutation. A custom type can
instead define one direct indexed-mutation mixfix so:

```zax
custom[row][column] = replacement
```

The first index result must itself support the second index, or one direct
mixfix can consume both adjacent index components. Either route performs the
declared operation without requiring a captured proxy.

### Custom splice results

A bracket containing a range selects `operator splice 1`, separate from
`operator index`. Each bracket contributes one sliced dimension:

```zax
MySequence :: type {
  operator splice 1 final : (
    result : MyView
  )(
    start : IndexSize?,
    endExclusive : IndexSize?
  ) readonly = {
  }
}
```

Omitted endpoints arrive as absent optional inputs. Written inclusive endpoints
are converted to their equivalent exclusive end:

```text
[..5]   -> absent start, present 6
[..<5]  -> absent start, present 5
[0..<0] -> present 0, present 0
[0..]   -> present 0, absent end
[..]    -> absent start, absent end
```

The custom result need not be `MyType[]`. Its declaration owns the result and
domain-specific validation.

## Counts, storage extent, and allocation

Lengths, capacities, indexes, endpoints, and ordinary iterable counts use the
applicable memory domain's `IndexSize`. Backing byte extent uses `TypeSize`.

The two identities have equal ranges and representations within one memory
domain but remain different because one counts logical positions while the
other measures storage.

Before requesting backing storage, Zax checks:

- element count multiplication;
- multidimensional extent;
- alignment;
- metadata addition; and
- conversion to byte extent.

No failure silently chooses another near, ordinary, or far memory domain.

The array's unique storage handle owns disposition of every raw region used for
that array. A provider may implement the region with one allocation, several
chunks, or individual nodes. No array element becomes an independently
resettable allocation merely because the provider uses several physical blocks.

When no storage is written, the declaration resolves a storage representation
profile before its owning layout is fixed. For a provider-backed profile, the
current `___` execution context may supply a compatible provider instance. The
provider separately chooses its backing arenas. Runtime context replacement
cannot change the already resolved self-contained array size.

Allocating a pointer to the array object itself with `@` remains another outer
allocation.

## Required, reporting, and unchecked behavior

Required operations use compact source:

```zax
element := values[index]
view := values[start..<end]
values.resize(newLength)
```

They check runtime conditions and panic on violation. Separately named reporting
operations instead return a fixed optional or reporting result. Their exact
callable names remain future source integration.

Disabling a registered panic category tells the compiler to assume that
condition never occurs and permits it to remove the check. If the promise is
false, behavior is undefined.

Disabling a category never:

- suppresses a compile-time-known error;
- changes required source into an optional result; or
- removes a check needed by an explicitly reporting operation.

The reusable proof and category model belongs to
[safety and analysis](safety-and-analysis.md).

## Diagnostics

Source presentation rejects spaced `[ ]` where the zero-entry form is `[]` and
compact nested-array spelling where contiguous `[[` begins lambda capture.

Intrinsic array source rejects a known:

- element index outside the current or fixed bounds;
- slice endpoint outside bounds;
- reversed range;
- exact initializer count mismatch;
- requested length outside its declared limits;
- fixed-array resize;
- capacity request beyond a finite maximum;
- foreign slice used to mutate another array;
- incompatible nested or committed element type; or
- overflowing multidimensional or byte extent.

Runtime panic conditions distinguish:

- index bounds;
- splice bounds or order;
- requested length;
- initializer count;
- invalidated slice;
- foreign slice origin;
- broken iterable count promise;
- extent overflow; and
- required backing allocation failure.

Custom bracket operations receive no inferred array checks merely because they
use index or splice syntax. Their own declarations define their validation.

## Costs programmers must be able to see

Depending on the operation and type, array code may perform:

- default, copy, deep, move, or last element construction;
- reverse element destruction;
- backing allocation, reservation, trimming, and recovery;
- full relocation or affected-suffix movement;
- temporary capture for overlapping mutation;
- slice descriptor copying;
- slice origin and validity checks;
- bounds, range, count, and storage-overflow checks;
- repeated growth for an uncounted iterable;
- cursor acquisition and progression; and
- storage-provider lookup, dispatch, and capability checks.

The compiler may remove work only when the selected behavior, effects, order,
lifetimes, failure, and source-visible results remain unchanged.

## Structural and reflection boundaries

An array is one structural leaf. Structural shape processing does not expand
every element or turn runtime length into a record-member list.

Changing between array-of-structures and structure-of-arrays layout is an
explicit transformation. It may allocate, visit elements, transfer values, and
handle overlap; it is not an implicit shape/layout conversion.

Future type reflection needs:

- element type and qualifications;
- rank and per-dimension limits;
- fixed or resizable role;
- resolved storage provider/profile and statically required capabilities;
- storage-erased versus exact reference profile; and
- explicit versus defaulted source properties where that difference affects
  compatibility.

Future value reflection needs:

- current lengths;
- usable capacities;
- absent or present capacity suggestions; and
- operation, provider, physical-backing, or layout state exposed by a declared
  provider API.

A statically known provider type may expose safe provider/handle access. An
erased provider may expose an unsafe raw pointer to the provider object, not to
element contents. Contiguous element access remains a separate
lifetime- and qualification-bearing capability.

## Source stability and future boundaries

Changing element identity, size limits, storage profile/capabilities, dimension
order, expansion, capacity behavior, place stability, transfer availability,
failure policy, or operator/callable surface can change source validity,
behavior, cost, and lifetime.

Source, declaration, import, allocation, or traversal order never silently
resolves ambiguity.

This document is current conceptual design, not formal grammar, an ABI,
implementation representation, generic constraint system, or conformance
specification.

Still future:

- runtime-fixed owning arrays and inferred fixed-but-jagged rows;
- rectangular and strided multidimensional block views;
- owning or allocation-anchored slices;
- exact array storage-provider and unique-handle protocols;
- storage-clause and storage-capability syntax;
- stability token/version representation;
- shared/default provider factories and context hooks;
- consuming range/chunk transfer;
- provider-specific and erased handle APIs;
- explicit storage-change source;
- exact optional/reporting callable names;
- weaker suggested iterable-count capabilities;
- generic iterable and array constraints;
- complete runtime string mutation and builder APIs;
- exact reflection API syntax;
- SIMD, MIMD, parallel traversal, and vectorization contracts;
- formal panic-registry syntax and identifiers; and
- ABI, representation, lowering, and optimization algorithms.
