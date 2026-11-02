# Zax iteration

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers traversing arrays, enums, structured values, ranges, views, or generated sequences |
| Applies To | Programmer-facing `each in` and `each from` behavior; not a formal grammar, collection contract, or implementation specification |
| Implementation State | Not established by this repository |
| Owns | The `each` mental model; direct and cursor-driven traversal; header phases and entry bindings; positional and named roles; compiler-known array, enum, and structural traversal; cursor acquisition, value production, progression, erasure, direct re-entry to a live current entry, and active-`each` access; traversal costs, diagnostics, mutation responsibilities, and source stability |
| Does Not Own | Shared flow-transfer and unwinding rules ([core flow control](core-flow-control.md)); general token and layout rules ([source structure](source-structure.md)); collection-specific place stability ([lifetimes and references](lifetimes-and-references.md)); complete reflection or type-declaration traversal; generic constraint syntax; or compiler lowering |
| Source / Provenance | Legacy [flow control](../flow-control.md) together with current array, enum, flow, declaration, qualifier, lifetime, safety, operator, and transfer designs |

## Start with one entry at a time

`each` runs one body for every entry supplied by a traversal source:

```zax
values : String[3]
values[0] = "bird"
values[1] = "plane"
values[2] = "superman"

each value : in values {
  print(value)
}
```

This visits the array elements in increasing index order. The omitted binding
type gives `value` a reference to each element place; it does not silently copy
every string.

Zax has two traversal mechanisms:

```zax
each value : in values {
  // Direct traversal recognized by the language.
}

each value : from values {
  // Traversal through a first-class cursor.
}
```

- `in` knows the source's structure directly. Its source families are intrinsic
  arrays and slices, enum declarations, and stored instance members.
- `from` obtains a cursor and uses its declared progression and value
  operations.

Arrays may support both. The similar loop spelling does not make their
mechanisms or costs identical.

## Header and body

The traversal clause is required. An initializer and post operation are
independently optional:

```text
each traversal-clause body

each initializer ;;
     traversal-clause body

each initializer ;;
     traversal-clause ;;
     post body

each ;;
     traversal-clause ;;
     post body
```

The initializer, post, and body each accept one
[effective statement](core-flow-control.md#effective-bodies-and-header-boundaries).

```zax
each count := 0;
     values := loadValues() ;;
     value : in values ;;
     ++count {
  consume(value)
}
```

`count` and `values` are established once. The traversal binding is established
separately for each entry. Both the initializer bindings and current traversal
binding are visible to the post and body.

An initializer block retains its own nested scope:

```zax
each count := 0;
     values := loadValues();
     { reset := findReset(values); applyReset(values, reset) } ;;
     value : in values ;;
     ++count {
  use(count, values, value)
  // reset = replacement // error: reset belonged to the completed block
}
```

The body is required. As with other flow statements, it may be one simple,
composed, or braced effective statement.

## Progression and transfers

For one current entry:

1. establish fresh entry bindings;
2. execute the body;
3. on fallthrough or `next`, execute post while those bindings remain live;
4. end the entry bindings;
5. perform the source's progression transition; and
6. establish bindings for the successor or complete on exhaustion.

`continue` skips post but still progresses. `goto` skips both and re-enters the
current entry body directly. `break` skips post and exits.

```zax
each value : in values ;; record(value) {
  if shouldSkipPost(value)
    continue

  if shouldStop(value)
    break

  if bodyWorkIsDone(value)
    next
}
```

All four transfers use the shared target, label, and unwinding rules from
[core flow control](core-flow-control.md#flow-labels-and-transfer-targets).

```zax
each outer: value : in values {
  while needsWork(value) {
    if skipOuterEntry(value)
      continue outer:
  }
}
```

### `goto` and the current entry

Bare `goto` targets the nearest eligible unlabeled active `each`. An explicit
`goto each_label:` selects a labeled active `each`. Either form enters the body
again without running post, advancing the source, or establishing another entry:

```zax
each repeat_entry: value : in values ;; record(value) {
  use(value)

  if needsAnotherPass(value)
    goto repeat_entry:
}
```

The current entry binding and cursor access must still be live. Body locals from
the source entry are destroyed before the target body begins with a fresh body
scope. The traversal binding remains the same current entry.

This direct entry may intentionally repeat forever. It does not request another
cursor value and does not imply progression. Shared active-body `goto` behavior
is defined by
[core flow control](core-flow-control.md#direct-body-entry-with-goto).

The label attaches to the `each` statement as `outer:`. A traversal declaration
uses a spaced colon as `value :`; `value:` would instead present label intent
and is invalid as a binding.

Body-local values are destroyed before post or transfer. Entry bindings end
before progression. Header bindings and statement-owned cursor state are
destroyed when the complete `each` exits.

## Binding entries

### Positional bindings

Each source family defines an ordered role list:

```zax
each value :, memberName : in MyEnum {
  print(value, memberName)
}
```

A declaration consumes the next role. `#` consumes and discards one role:

```zax
each #, memberName : in MyEnum {
  print(memberName)
}

each # in MyEnum {
  recordOneDeclaration()
}
```

### Named bindings

Parentheses select a wholly named binding form:

```zax
each (name:) in MyEnum {
  print(name)
}

each (
  name: memberName :,
  value: memberValue :
) in MyEnum {
  print(memberName, memberValue)
}
```

Inside the parentheses:

- every entry selects a role by name;
- `name:` selects `name` and introduces the same local spelling;
- `name: memberName :` selects `name` and introduces `memberName`;
- roles may appear in any order;
- an omitted role creates no binding;
- a role may be selected at most once; and
- positional declarations and `#` do not mix with the named form.

A flow label remains outside the binding packet:

```zax
each outer: (name: memberName :) in MyEnum {
}
```

## Direct `in` traversal

`each in` recognizes a closed set of language-defined source families. It
creates no first-class cursor and does not search for methods whose names happen
to resemble a traversal protocol.

Every direct source is finite. Its contract defines entry order, binding roles,
value or place behavior, and exhaustion.

### Intrinsic arrays and slices

A fixed array, resizable array, or slice gives the loop one element on each
pass. By default the loop accesses that element directly:

```zax
each element : in values {
  inspect(element)
}
```

The programmer may state a compatible reference explicitly:

```zax
each element : Integer readonly & in values {
  inspect(element)
}
```

An explicit non-reference type constructs a separate value:

```zax
each element : Integer in values {
  keep(element)
}
```

That construction follows ordinary transfer and construction rules. It may call
programmer code, share storage, allocate, or be unavailable.

The source expression is evaluated once. Elements are visited in increasing
index order. Each pass establishes a fresh binding; a reference is not rebound
from one element place to another.

A fixed array's element places remain stable for its life path. A resizable
array or slice uses the place-stability and invalidation contract defined by
[Zax arrays and slices](arrays-and-slices.md#element-place-stability).
Writable element access is available only when the source grants it.

Element mutation that preserves the current place may remain valid. Structural
mutation invalidates direct traversal rather than silently changing the
remaining entry set. Use cursor-driven `each from` when controlled erasure or
specialized progression is required.

### Enum declarations

An enum declaration supplies:

```text
value, name, [future reflection]
```

The current `value` and `name` roles visit every declaration in source order,
including duplicate-valued aliases:

```zax
Box :: enum U8 {
  RedBox = 1
  Redbox = 2
  GreenBox = 5
  Greenbox = 5
  BlueBox = 6
  Bluebox = 7
}

each value : in Box {
  print(value as underlying value)
}
// 1, 2, 5, 5, 6, 7
```

Names are available without requiring general reflection:

```zax
each value :, memberName : in Box {
  print(value as underlying value, memberName)
}

each (name: memberName :) in Box {
  print(memberName)
}
```

An inferred name is a compiler-provided
`String readonly immutable final &`. An explicit `String` requests a copy:

```zax
each (name: memberName : String) in Box {
  keep(memberName)
}
```

The future reflection role follows `value` and `name`, but no reflection
binding, type, or source is currently defined.

`each` provides no special case-insensitive match traversal. Use the generated
singular enum lookup for ordinary lookup, or walk declarations and apply custom
matching and deduplication when an application needs several results.

### Stored instance members

Traversal over a structured value visits:

> accessible stored instance member places of the source's static type, in
> declaration order.

```zax
MyRecord :: type {
  count : Integer
  ratio : Float
  name : String
}

record : MyRecord

each value : in record {
  print(value)
}
```

The compiler checks the binding, body, and post separately using every selected
member's concrete type. Runtime body executions occur in declaration order.
This is an observable typing rule, not a required code-generation strategy.

The value role is heterogeneous and does not accept a concrete type name:

```zax
each value : in record {
  // Default inferred reference.
}

each value : & in record {
  // Explicit inferred reference.
}

each value : copy in record {
  // Owned copy with a separately inferred type for every member.
}
```

`value : copy` must be viable for every selected member. If one member cannot be
copied, the statement is invalid and the diagnostic identifies that member
specialization.

Stored callback values participate because they occupy instance places.
Function declarations, operator declarations, and type-owned `once`
declarations do not. Private members participate only from an access context
that may ordinarily reach them.

This form has no structural filter. Traversal of functions, operators,
generated declarations, `once` state, or other declaration metadata is a
separate reflection concern rather than a wider interpretation of instance
places.

Compiler-known member selection and per-member checking do not imply
compile-time execution. Runtime effects in the body remain runtime effects
unless an independent compile-time execution context says otherwise.

## Cursor-driven `from` traversal

`each from` drives a first-class cursor. Its protocol uses five exact
receiver-owned phrases:

- `iterate`, used as `iterate <source>`;
- `value at`, used as `value at <cursor>`;
- `advance`, used as `advance <cursor>`;
- `erase and advance`, used as `erase and advance <cursor>`; and
- `erase`, used as `erase <cursor>`.

The first three support ordinary traversal. The erase operations are optional
capabilities.

A concrete cursor needs no generic concept at a concrete `each` site. The
compiler checks the selected type's exact operations directly. Future generic
constraints will express this already-defined protocol for an unknown type.

### Optional exact count

An iterable source may expose an exact non-consuming count:

```zax
operator post unary 'count' final : (
  result : IndexSize
)() readonly = {
}
```

An iterable need not provide `count`. When it does, the returned number must
exactly match the values produced by the next corresponding traversal, and
asking for it must not consume those values. Array construction can use that
number to reserve storage once before traversal.

If the iterable later produces a different number of values, the array
operation panics because the iterable broke its promise. A source that knows
only an estimate does not provide this exact operation. Complete array-entry
`from`, reservation, element-count checking, and memory-domain behavior is
explained by
[Zax arrays and slices](arrays-and-slices.md#iterable-contributions).

### Acquiring the first cursor

An `iterate` operation returns a cursor when a first entry always exists:

```zax
operator pre unary 'iterate' final : (
  first : MyCursor
)() readonly = {
  // ...
}
```

A possibly empty source returns an optional cursor:

```zax
operator pre unary 'iterate' final : (
  first : MyCursor?
)() readonly = {
  // ...
}
```

A present cursor always designates one real current entry. Absence means that no
cursor exists and the body runs zero times. Zax exposes no before-first,
after-last, or erased-current cursor value.

If the `from` expression already supplies a viable cursor protocol, it may
initialize the statement's cursor state directly. Otherwise `each` invokes
`iterate source` once. Acquisition never recursively searches for more
conversions.

The statement owns its current cursor state. Ordinary construction and transfer
rules decide whether a source cursor is copied, moved, or produced as a
temporary; `each` does not silently invent another transfer policy.

### Producing the current value

The cursor supplies one current value:

```zax
operator pre unary 'value at' final : (
  result : MyValue readonly &
)() readonly = {
  // ...
}
```

The result may instead be an owned value or proxy. Producing it may be
substantially more expensive than finding the next valid cursor.

An ordinary binding requests the value once before each body entry:

```zax
each value : from values {
  consume(value)
}
```

An explicit compatible type may bind a reference or construct a value:

```zax
each value : MyValue readonly & from values {
  inspect(value)
}

each value : MyValue from values {
  keep(value)
}
```

Both discard spellings avoid requesting the value:

```zax
each # from values {
  recordOnePosition()
}

each # : from values {
  recordAnotherPosition()
}
```

`#` is the compact positional discard. `# :` is its explicit
declaration-shaped spelling. Neither creates a binding, requires `value at`, or
produces a value. A progression-only cursor may therefore use either form.

A named binding must retain its declaration colon:

```zax
each value : from values {
  consume(value)
}

each value from values { // error: `value` is not a declaration
}
```

### Advancing

A cursor may mutate its statement-owned storage and report whether it now
designates a successor:

```zax
operator pre unary 'advance' final : (
  hasNext : Boolean
)() mutable writable = {
  // ...
}
```

Or it may return replacement cursor state:

```zax
operator pre unary 'advance' final : (
  next : MyCursor?
)() readonly = {
  // ...
}
```

An inexhaustible replacement form may return `MyCursor`. Replacement forms may
also use viable `move` or `last` receiver variants, but no terminal stance is
required.

The choice is operation-driven rather than inferred from whether the cursor
type is generally mutable. The compiler:

1. forms progression declarations viable for the current cursor
   qualifications and stance;
2. accepts an in-place `Boolean` shape or a same-cursor replacement shape,
   optionally wrapped once for exhaustion;
3. applies ordinary receiver and overload preference;
4. diagnoses unresolved ambiguity; and
5. does not prefer one result shape merely because cursor mutation is possible.

Before transition, current entry bindings end. A successful transition
establishes fresh bindings. Failure or absence completes the statement.

## Accessing the active `each`

The body does not receive a cursor or position binding. It uses one targetable
family when ordinary value binding is insufficient:

```text
from each [label:] value
from each [label:] underlying cursor
from each [label:] unsafe underlying cursor
from each [label:] erase
```

The label follows the complete `from each` introducer:

```zax
from each value
from each outer: value
```

Without a label, the family selects the lexically innermost active `each`, even
when that statement has a label. An explicit label selects that exact visible
`each`. The compiler does not skip an incapable inner target to find a capable
outer one.

### Selective value production

`from each value` requests the same current value shape and authority available
to an ordinary binding:

```zax
each index := 0 ;;
  # from expensiveSequence ;;
  ++index {
  if index % 15 == 0 {
    sampled : & = from each value
    mutate(sampled)
  }
}
```

The other positions advance without producing their expensive values.

The operation applies to `each in` and `each from` when the selected source
provides a value. Repeated requests may repeat value-production cost unless the
source contract promises caching.

### Underlying cursor

`from each underlying cursor` applies only to `each from` and returns readonly
access to the actual current cursor:

```zax
cursorView := from each underlying cursor
```

It permits observation and readonly-compatible cursor operations. It does not
expose in-place progression.

Direct access with the cursor's existing qualifications requires narrow unsafe
responsibility:

```zax
rawCursor := from each unsafe underlying cursor
```

Unsafe access does not manufacture mutable, writable, transfer, erase, or
lifetime authority. The programmer accepts responsibility for any effect on
progression, exhaustion, current-value validity, replacement results, and the
statement's later automatic transition. It cannot restore access after the
current cursor life has ended.

## Erasing during traversal

Erasure is available only when the target cursor supplies the exact required
operation. The compiler does not synthesize removal or redirect to another
active traversal.

### Erase and advance

Continuing erasure may mutate cursor state:

```zax
operator pre unary 'erase and advance' final : (
  hasNext : Boolean
)() mutable writable = {
  // ...
}
```

Or it may return replacement state:

```zax
operator pre unary 'erase and advance' final : (
  next : MyCursor?
)() readonly = {
  // ...
}
```

An always-successful replacement may return `MyCursor`. Either form also needs
real authority to mutate the backing source. Returning a new cursor does not
create that authority.

### Erase and exit

Exiting erasure uses a no-result operation:

```zax
operator pre unary 'erase' final : (
)() mutable writable = {
  // ...
}
```

Other receiver variants may provide the same operation when they retain source
mutation authority. There is no automatic fallback to `erase and advance`;
finding or constructing a successor solely to discard it can have visible cost.

### Common erase transfers

The common forms combine erasure with an immediate transfer:

```zax
continue with erase
next with erase
break with erase

continue outer: with erase
next outer: with erase
break outer: with erase
```

`with erase` modifies the transfer's ordinary target. It has no independent
target and never causes target search to skip an intervening construct.

- `continue with erase` skips post, ends current entry access, invokes
  `erase and advance`, and enters the successor or exits.
- `next with erase` runs post while current entry access remains valid, ends
  that access, invokes `erase and advance`, and enters the successor or exits.
- `break with erase` skips post, ends current entry access, invokes `erase`,
  and exits without finding a successor.

Every combined `goto ... with erase` form is invalid. `goto` directly re-enters
the current body, while erasure ends the entry that body requires.

### Erase, perform work, then transfer

The active-`each` family permits work after erasure:

```zax
each value : from values {
  if shouldErase(value) {
    summary := summarize(value)
    hasNext := from each erase

    updateStatistics(summary)

    if !hasNext
      beginSecondaryPhase()

    continue
  }

  use(value)
}
```

`hasNext` is a discardable `Boolean` convenience result. It reports whether the
erase transition produced a successor; it does not control the loop. The
compiler separately retains the successor or absence.

After `from each erase`:

- the target's current value and underlying cursor access are unavailable;
- other body locals, header bindings, and outer state remain available;
- `continue` targeting that `each` skips post and adopts the prepared successor
  without another advance;
- `next` targeting it runs post after erase, diagnoses any post use of
  invalidated entry state, and then adopts the prepared successor without
  another advance;
- `break` targeting it discards the prepared successor and exits;
- `goto` targeting that `each` is invalid because the current entry lifetime has
  ended and direct body entry cannot recreate it;
- a transfer beyond it, `return`, or panic exits normally; and
- normal fallthrough is invalid while the erase transition remains pending.

This ordering is intentional:

```zax
next with erase
// Run post while current entry access is valid, then erase.

from each erase
next
// Erase first, then run only a post independent of old entry access.
```

Direct transfer beyond the erased traversal names both operations separately:

```zax
from each current_iteration: erase
goto outer:
```

`goto outer: with erase` is invalid because its one target label cannot also name
which crossed traversal owns the erase operation.

For targeted erase:

```zax
from each outer: erase
```

every completing path must resume or exit `outer:` or transfer beyond it. A
transfer targeting an inner construct does not resolve the outer pending state.

### Availability

| Source form | Required cursor operation |
| --- | --- |
| ordinary progression | viable `advance` |
| `from each erase` | viable `erase and advance` |
| `continue with erase` | viable `erase and advance` |
| `next with erase` | viable `erase and advance` |
| `break with erase` | viable `erase` |

Missing capability is a compile error on the selected target. `break with
erase` does not fall back to `erase and advance`.

## Cursor validity and source mutation

Active traversal creates a cursor-validity obligation, not an exclusive
compiler lock over the source.

The compiler rejects established contradictions and diagnoses source operations
whose contracts establish invalidation:

```zax
each value : from values {
  from each erase
  use(value) // error: the current value ended
}
```

It does not reject an arbitrary call merely because hidden code might mutate
the source:

```zax
each value : from values {
  opaqueCallback()
}
```

Cursor and collection contracts must state:

- which element mutations preserve traversal;
- which structural mutations preserve, reposition, or invalidate cursors;
- which operations invalidate yielded references;
- whether simultaneous cursors remain valid;
- whether opaque or reentrant mutation is stable, runtime-checked, or the
  programmer's responsibility; and
- the cost of any runtime validation.

A cursor may use stable positions, restrict selected operations, or detect
invalidation at runtime. Zax does not impose one strategy or its cost on every
cursor. A hidden mutation that violates the cursor's documented requirements
remains a program or implementation error rather than something `each` can
universally discover.

Compiler-coordinated erase is the preferred removal path, not a prohibition
against all other mutation.

## Costs

For a runtime source of `n` entries, `each` enters the body `n` times unless
control transfer exits early.

- Direct `each in` creates no cursor allocation.
- `each from` does not imply allocation; `iterate` owns cursor-construction
  cost.
- An ordinary cursor value binding invokes `value at` once per entered body.
- `#` and `# :` invoke it zero times.
- A reference binding performs no value construction.
- A copied binding performs ordinary construction and destruction once per
  visited entry.
- `goto` can repeat the current body and its per-entry work without paying post,
  value production, or progression cost.
- Structural `copy` performs and checks that work separately for each selected
  member type.
- Runtime invalidation tracking is present only when a cursor or collection
  contract chooses it.
- Replacement progression may construct and destroy cursor values; an
  applicable receiver variant may reuse storage without changing the observable
  transition.

The compiler may unroll, table-drive, specialize, or otherwise optimize
compiler-known traversal only when source evaluation, order, binding behavior,
effects, and cleanup remain unchanged.

## Diagnostics

Representative diagnostics distinguish:

- unsupported direct `in` source;
- unavailable or ambiguous `iterate`;
- unavailable or ambiguous `advance`;
- a value binding when the cursor supplies no viable `value at`;
- incompatible explicit reference or value binding;
- unavailable `copy` for one structural member specialization;
- unavailable `erase` or `erase and advance`;
- `with erase` on a transfer target other than `each from`;
- any combined `goto ... with erase` form;
- an incapable inner target rather than silently selecting an outer one;
- use of current value or underlying cursor after erase;
- post access to invalidated entry state after `from each erase; next`;
- `goto` to an `each` whose current entry has ended or whose progression
  transition is pending;
- fallthrough or wrong-target re-entry with an erase transition pending;
- known source mutation that invalidates active traversal;
- unsafe underlying cursor access without explicit responsibility;
- named and positional binding forms mixed in one clause; and
- a named traversal binding that omits its declaration colon.

Diagnostics name the selected `each` target, source family, binding role, or
missing cursor operation. They do not suggest informal method-name conventions
or a generic concept when a concrete protocol operation is absent.

## Formatting

- `each`, its optional label, and the first header section begin on one physical
  line unless explicit continuation moves the complete header.
- A label uses attached `label:` spelling; a declaration uses `name :`.
- A named traversal binding always retains its spaced declaration colon; only
  discard has the compact `#` form.
- `;;` has whitespace on both sides.
- Continued header sections use the common two-space continuation level.
- Parentheses around bindings mean the wholly named form.
- A multiline body-opening `{` remains on the final physical header line.
- `with erase` follows the complete transfer target:

  ```zax
  continue outer: with erase
  ```

- The active-`each` target follows the `from each` introducer:

  ```zax
  from each outer: value
  ```

## Source stability

The language preserves these distinctions:

- `in` is direct compiler-known traversal;
- `from` is cursor-driven traversal;
- adding a generic constraint later does not reinterpret existing concrete
  cursors;
- `#` and `# :` guarantee that cursor value production is not requested;
- a present cursor always designates a real entry;
- transition absence is not an exposed end cursor;
- adding an erase capability makes new source valid but does not change
  ordinary progression;
- `goto` retains the current live entry and never silently progresses;
- named role selection is explicit and independent of local identifier spelling;
- the future reflection role follows enum and structural `value`/`name`
  positions without making reflection current; and
- postfix `_` changes only keyword interpretation, not semantic identity.

## Boundaries and maturity

This document defines current conceptual iteration behavior. It is not a formal
grammar, ABI, conformance contract, or compiler lowering.

Still future:

- generic syntax for constraining an unknown iterable or cursor type;
- the reflection payload and type-declaration traversal;
- generic collection, proxy, range-value, and non-array view catalogs;
- generator and coroutine production;
- asynchronous, concurrent, and parallel traversal;
- compile-time execution of traversal bodies;
- formal cursor invalidation and effect contracts; and
- implementation-specific optimization and lowering.

Those future concerns must preserve the programmer-visible source, ordering,
binding, progression, erasure, lifetime, cost, and diagnostic boundaries
established here.
