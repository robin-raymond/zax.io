# 017: Each iteration and compiler-known traversal

| Field | Value |
| --- | --- |
| Status | Completed historical record / non-normative / audit-only |
| Work Item | `017` |
| Created | 2026-09-07 |
| Completed | 2026-09-08 |
| Owns | Historical provenance for the bounded review defined by the fixed initiating input |
| Does Not Own | Current iteration behavior, first-class iterator concepts, complete generics, generators, coroutines, async or parallel iteration, or complete collection and range design |

## Non-authority notice

This file is a historical audit record. Its aligned findings were promoted into
their current owners before closure. This record remains non-authoritative and
must not serve as a current source of language meaning.

## Fixed initiating input

This section records the information aligned when work item `017` was created.
It is intentionally incomplete and must not be rewritten as work develops.

### Initiating concern

Develop a coherent human-developer-facing model for `each` and compiler-known
traversal without prematurely requiring first-class iterator values or a
concept/generic constraint system.

The review should establish:

1. the role and source shape of `each`;
2. the distinction, if retained, between `each in` and `each from`;
3. initialization, binding, condition, and body structure;
4. traversal order and termination;
5. element value, place, reference, qualification, and transfer behavior;
6. scope, lifetime, cleanup, and control-transfer behavior;
7. compiler-known traversal of enum declarations and case-insensitive matches;
8. the bounded interaction with arrays, ranges, fields, and multi-string flags
   conversion;
9. compile-time structural traversal versus runtime value traversal;
10. costs, diagnostics, and source stability; and
11. a precise handoff to later concept-dependent iterator protocols.

### Motivating pressure

Current enum design needs member and case-insensitive-match traversal in
declaration order. Flags conversion also needs a future way to consume several
strings atomically. Legacy flow material proposes `each in` and `each from`.

Zax cannot yet express a true general iterator protocol because concepts and
generic constraints are not current. The immediate task is therefore to design
direct `each` behavior over compiler-known sources without disguising a local
compiler facility as a completed user-extensible iterator abstraction.

### Known assumptions

- First-class iterator values and general iterable parameters remain deferred
  until concepts or an equivalent constraint system can express their
  requirements.
- A direct `each` construct may operate over language-known sources before that
  abstraction exists.
- Existing core flow rules for effective bodies, scopes, labels, transfer
  targets, unwinding, and completion remain constraints.
- Enum member traversal preserves declaration order and duplicate-valued
  aliases.
- Case-insensitive enum-match traversal visits each distinct matching value once
  in first matching declaration order.
- No compiler implementation exists in this repository.

### Known inclusions

- `each` statement mental model and ordinary use.
- `in` and `from` source roles.
- Header initialization and binding scope.
- Element binding type, qualification, and lifetime.
- Forward progress, exhaustion, and termination.
- `break`, `continue`, `next`, `return`, panic, and cleanup.
- Compiler-known enum member and match traversal.
- Concrete pressure from arrays and ranges where required to make `each`
  coherent.
- Compile-time reprocessing pressure for heterogeneous structural traversal.
- Multiple-string flags conversion pressure without assuming a general iterable
  parameter.
- Costs, diagnostics, formatting, and source stability.
- Lasting documentation ownership and legacy disposition.

### Known starting boundaries

- General concepts, traits, interfaces, or generic constraints.
- First-class iterator and iterable values.
- User-defined iteration protocols.
- Associated element-type contracts.
- Generator or coroutine functions.
- Async, concurrent, or parallel iteration.
- Complete arrays, slices, ranges, variadics, reflection, or collection APIs.
- Query comprehension or functional pipeline syntax.
- Compiler lowering.

These boundaries do not erase consequences. Record each material pressure in a
live owner or indexed raw destination.

### Intentionally unresolved framing

- Exact `each` header syntax.
- Whether `in` and `from` remain distinct.
- Which compiler-known sources are supported initially.
- Whether enum declaration traversal runs at compile time, causes body
  reprocessing, or exposes runtime metadata.
- Whether array/range traversal yields values, places, or references.
- Binding qualification and transfer stance.
- Mutation of a source during traversal.
- Whether `next` performs a traversal step while `continue` skips one, or
  whether `each` needs another transfer model.
- Cleanup and failure behavior when traversal stops early.
- How flags multi-string conversion uses a concrete source before iterable
  concepts exist.
- Which facts must be preserved for a later concepts-based iterator protocol.

### Initial stopping guidance

Stop when the work has:

- established a usable direct `each` model over a justified initial set of
  compiler-known sources;
- integrated header bindings, scopes, lifetimes, transfer, cleanup, and flow
  exits;
- established enum member and match traversal;
- dispositioned legacy `each in` and `each from` evidence;
- preserved concept-dependent iterator requirements without designing them;
- identified exact current owners and indexed deferrals; and
- completed the required documentation-fit dry run.

Do not design concepts, complete generics, first-class iterator protocols,
generators, coroutines, async or parallel iteration, promote findings, archive
this work item, or begin work item `018` without the separately required
discussion, alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  numbered work, promotion, teaching, deferrals, and dry runs.
- [Raw iteration and traversal input](../raw/iteration.md) - preserves legacy
  `each`, enum traversal, flags multi-string, and later concept-dependent
  iterator pressure.
- Focused [deferred enum traversal](../../language/enums.md#deferred-enum-traversal)
  and [several string inputs for flags](../../language/enums.md#several-string-inputs-for-flags) -
  supply the immediate accepted domain requirements without fixing traversal
  syntax.
- Focused [loops and explicit scope](../../language/core-flow-control.md#loops-and-explicit-scope),
  [flow labels and transfer targets](../../language/core-flow-control.md#flow-labels-and-transfer-targets),
  and [unwinding, destruction, and completion](../../language/core-flow-control.md#unwinding-destruction-and-completion) -
  constrain repeated execution, exits, and cleanup.
- Focused [header sections and separators](../../language/source-structure.md#header-sections-and-separators)
  and [braces and body boundaries](../../language/source-structure.md#braces-and-body-boundaries) -
  constrain `each` header and body source.
- Focused [flow-control initialization](../../language/declarations-and-bindings.md#flow-control-initialization) -
  constrains declaration visibility and repeated-body scope.
- Legacy `each` and focused array/range material formerly on `flow-control.md`
  supplied primary historical evidence. Promotion consumed that material; use
  Git history for the original source.

### Consequence-driven

- Read focused [array and collection lifetimes](../../language/lifetimes-and-references.md#arrays-and-collections)
  when traversal yields places or references.
- Read focused [transfer stances](../../language/transfer-stances.md) when
  traversal copies, moves, consumes, or exposes elements.
- Read [raw generic input](../raw/type-parameters-and-generics.md) only when a
  direct `each` rule creates a concrete concepts/constraint dependency that must
  be preserved rather than solved.
- Read [raw indexing and slicing input](../raw/indexing-and-slicing.md) when
  array, slice, range, proxy, or index traversal requires more than a local
  constraint.
- Read [raw reflection input](../raw/reflection.md) when heterogeneous member
  traversal requires metadata beyond the accepted enum facts.
- Read focused callable, construction, or optional material only when a concrete
  traversal rule crosses that owner's boundary.

### Audit-only

- `project/archive/`, including work items `001` through `016`.
- Deleted or superseded iterator implementation sketches recoverable through Git
  history.

Do not read archived work item `016` during ordinary work on `017`. Its accepted
findings are promoted into current owners and its future traversal pressure is
preserved in live raw input.

## Working record

### Aligned working model

Everything in this subsection is aligned for the current review scope but
remains non-authoritative until separately promoted into its lasting owners.

#### Review entry point

`each` is one flow-control family with two traversal mechanisms:

```zax
each value : in values {
  use(value)
}

each value : from values {
  use(value)
}
```

- `each in` performs compiler-defined traversal over a small set of
  language-recognized structures. It creates no first-class cursor.
- `each from` drives a first-class cursor supplied directly or produced by the
  `iterate` protocol.
- Both forms share initialization, post, body, label, transfer, scope,
  lifetime, and cleanup rules.
- Arrays may support both. `in` uses direct array knowledge; `from` uses the
  cursor protocol and may have different behavior or cost.
- Concrete cursor behavior is defined here. Later concepts or generic
  constraints need only express that an unknown type supplies this existing
  protocol; they do not get to redefine the protocol.

An active traversal creates a cursor-validity obligation, not a Rust-like
exclusive compiler lock over the source. Zax rejects established invalidity and
diagnoses recognizable hazards without forbidding arbitrary calls merely
because they might mutate a source through an alias.

#### Unified header and progression

The complete header family is:

```text
each [label:] traversal-clause body

each [label:] initializer ;;
     traversal-clause body

each [label:] initializer ;;
     traversal-clause ;;
     post body

each [label:] ;;
     traversal-clause ;;
     post body
```

The traversal clause is:

```text
binding-clause in source
binding-clause from source
```

The initializer, post, and body each accept one effective statement. The body
is required; initializer and post are independently optional. The empty first
section explicitly reaches a post without inventing an initializer:

```zax
each ;;
  value : in values ;;
  record(value) {
  use(value)
}
```

A label follows the complete `each` introducer:

```zax
each outer: value : in values {
  if done(value)
    break outer:
}
```

The declaration space distinguishes a binding from a label:

```zax
each outer: value : in values
//   label^       ^binding declaration
```

Legacy `value:` binding spelling is therefore invalid. `value :` is required.

A flow initializer executes once. Bindings introduced directly in it are
visible to later initializer operands, the traversal clause, post, and body.
Bindings inside an initializer block retain that block's narrower scope:

```zax
each count := 0;
     values := loadValues();
     { reset := findReset(values); applyReset(values, reset) } ;;
     value : in values ;;
     ++count {
  // count, values, and value are visible.
  // reset belonged to the completed initializer block.
}
```

For one current entry:

1. establish the per-entry binding;
2. execute the body;
3. on fallthrough or `next`, execute post while the binding remains live;
4. end the per-entry binding;
5. perform the traversal's progression transition; and
6. establish fresh bindings for the next entry or complete on exhaustion.

`continue` skips post but still performs progression. `break` skips post and
progression. Transfers unwind exited body-local scopes in reverse construction
order. Header bindings remain alive until the complete `each` exits.

Every direct `each in` source is finite. A cursor used by `each from` may be
finite or inexhaustible.

#### Positional and named bindings

The compact binding form is positional:

```zax
each value : in Box {
}

each value :, memberName : in Box {
}

each #, memberName : in Box {
}

each # in Box {
}
```

Each source family defines an ordered role list. A positional declaration
consumes the next role; `#` consumes and discards it.

Parentheses select a wholly named binding form:

```zax
each (name:) in Box {
  print(name)
}

each (
  name: memberName :,
  value: memberValue :
) in Box {
  print(memberName, memberValue)
}
```

Inside `(...)`:

- every entry selects a role by name;
- `name:` selects a role and introduces the same local spelling;
- `name: memberName :` selects `name` and introduces `memberName`;
- role order is arbitrary;
- an omitted role creates no binding;
- each role may be selected at most once; and
- positional bindings and `#` do not mix with the named form.

The enclosure prevents a named role from being confused with the flow label:

```zax
each outer: (name: memberName :) in Box {
}
```

Renaming `memberName` never changes which role it receives.

#### Direct array traversal

An array supplies one `value` role:

```zax
each element : in values {
  // Inferred reference to the current element place.
}

each element : Integer readonly & in values {
  // Explicit qualification-compatible reference.
}

each element : Integer in values {
  // Ordinary compatible value construction.
}
```

The source expression is evaluated once. Elements are visited in increasing
index order. Every pass establishes a fresh binding rather than rebinding one
reference.

An omitted element type infers a reference with the source element's available
qualifications. An explicit reference must be qualification-compatible. An
explicit non-reference type performs ordinary direct construction and may
copy, allocate, call programmer code, or be unavailable.

Fixed-array element places remain stable for the array's life path. A writable
binding permits element mutation only when the source grants that authority.
Replacing the complete source while traversal is active is invalid. Dynamic
collections, slices, proxies, and other sources require their own place
stability and invalidation contracts before direct `in` traversal can admit
them.

#### Enum declaration traversal

An enum supplies positional roles:

```text
value, name, [future reflection]
```

The current roles are `value` and `name`. Reflection retains the third
conceptual position for future work but has no current binding, type, or
diagnostic contract.

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

each value :, memberName : in Box {
  print(value as underlying value, memberName)
}

each (name: memberName :) in Box {
  print(memberName)
}
```

Traversal visits every declaration in source order and preserves
duplicate-valued aliases. The value binding has the enum value type. The name
binding defaults to a compiler-provided `String readonly immutable final &`;
an explicit `String` binding requests a copy:

```zax
each (name: memberName : String) in Box {
  keep(memberName)
}
```

There is no separate case-insensitive matching traversal. Generated enum
lookup remains the built-in lookup facility. A programmer needing several or
custom matches walks declarations and applies custom filtering and
deduplication.

This supersedes the initiating assumption that `each` must provide a dedicated
distinct-value case-insensitive match source.

#### Structural instance-member traversal

`each in` over a structured value visits:

> accessible stored instance member places of the source's static type, in
> declaration order.

```zax
MyType :: type {
  count : Integer
  ratio : Float
  name : String
}

instance : MyType

each value : in instance {
  print(value)
}
```

For each selected member, the compiler checks the binding, body, and post using
that member's concrete type. At runtime the visits execute in declaration
order. This is an observable typing and ordering rule, not a required literal
code-duplication strategy.

The source family supplies:

```text
value, name, [future reflection]
```

The heterogeneous value role permits these contextual shapes:

```zax
each value : in instance {
  // Default inferred reference.
}

each value : & in instance {
  // Explicit inferred reference.
}

each value : copy in instance {
  // Owned copy of each member's separately inferred concrete type.
}
```

No concrete type name is permitted for the heterogeneous value role.
`value :` is shorthand for `value : &`. The reference carries the current
member place's available qualifications. `value : copy` must be viable for
every selected member; otherwise the statement is invalid and the diagnostic
identifies the failing member specialization.

The name role follows enum name behavior:

```zax
each (
  value: memberValue : copy,
  name: memberName :
) in instance {
  use(memberName, memberValue)
}
```

The default selected set is precise:

- ordinary stored instance members are included;
- stored callback values are included because they occupy instance places;
- function declarations are excluded;
- operator declarations are excluded;
- `once` declarations are excluded because they are type-owned;
- inaccessible private members are excluded; and
- no filter can recover a member unavailable under ordinary access control.

This initial form has no `using` filter. Function, operator, generated, and
`once` declaration traversal is a different reflection-oriented operation,
not a broader interpretation of instance-place traversal.

Compiler knowledge, per-member body checking, and compile-time execution remain
distinct. Runtime operations in a per-member body execute at runtime unless an
independent compile-time execution context says otherwise.

#### Concrete cursor protocol

The exact cursor protocol phrases are:

- `iterate source`
- `value at cursor`
- `advance cursor`
- `erase and advance cursor`
- `erase cursor`

A type-defined phrase uses the ordinary receiver-owned operator mechanism.
Concrete cursor use does not require a generic constraint: the compiler can
validate the selected operations at the concrete `each from` site.

##### Acquisition

`iterate source` may return a cursor directly when a current entry always
exists:

```zax
operator pre unary 'iterate' final : (
  first : MyCursor
)() readonly = {
}
```

It may return an optional cursor when the source can be empty:

```zax
operator pre unary 'iterate' final : (
  first : MyCursor?
)() readonly = {
}
```

A present cursor always designates one real current entry. Absence means no
cursor exists and the body executes zero times. There are no before-first,
after-last, or erased-current cursor values exposed to programmers.

`each from` first uses a source expression directly when it supplies a viable
cursor protocol. Otherwise it invokes `iterate source` once. Acquisition does
not recursively search conversions.

##### Current value

When a value is requested, the cursor supplies exactly one value through:

```zax
operator pre unary 'value at' final : (
  result : MyValue readonly &
)() readonly = {
}
```

The actual result may be a value, reference, or proxy. Its qualifications and
cost belong to the cursor contract. Producing it may be substantially more
expensive than progression.

An ordinary value binding invokes `value at` once for every entered body:

```zax
each value : from values {
  use(value)
}
```

`#` suppresses that operation:

```zax
each # from values {
  doWorkWithoutProducingTheCurrentValue()
}
```

A progression-only cursor therefore needs no `value at` operation.

##### Advancement

A cursor may implement progression by mutating current cursor storage:

```zax
operator pre unary 'advance' final : (
  hasNext : Boolean
)() mutable writable = {
}
```

`true` means that storage now designates a real successor. `false` means there
is no successor; no cursor binding is exposed again.

A cursor may instead return replacement state:

```zax
operator pre unary 'advance' final : (
  next : MyCursor?
)() readonly = {
}
```

An inexhaustible form may return `MyCursor`. Replacement may use ordinary
receiver access or offer `move` or `last`; no terminal stance is required.

The choice is operator-driven, not inferred from a general declaration that
the cursor type is mutable:

1. collect progression declarations viable for the current cursor
   qualifications and stance;
2. accept either the in-place `Boolean` shape or the same-cursor replacement
   shape, optionally wrapped once for exhaustion;
3. use ordinary receiver and overload preference among viable declarations;
4. diagnose unresolved ambiguity; and
5. do not prefer a result shape merely because cursor mutation might be
   possible.

A cursor author chooses a strategy through the receiver variants and result
shapes it supplies. Mutable cursor types may use replacement; a cursor whose
own state is readonly may use replacement. The replacement result must retain
the same concrete cursor type.

Before progression, the current per-entry bindings end. A successful transition
establishes fresh bindings. An unsuccessful transition completes the `each`.

##### Erase capabilities

Continuing erasure admits the same two strategies:

```zax
operator pre unary 'erase and advance' final : (
  hasNext : Boolean
)() mutable writable = {
}

operator pre unary 'erase and advance' final : (
  next : MyCursor?
)() readonly = {
}
```

An always-successful replacement may return `MyCursor`. Receiver viability must
also provide authority to mutate the backing source. A replacement result does
not create erase authority; it only avoids mutating the old cursor
representation.

Erase-and-exit uses a separate no-result operation:

```zax
operator pre unary 'erase' final : (
)() mutable writable = {
}
```

Other viable receiver variants, including a terminal `last` form or a readonly
form with an independently writable source capability, may supply the same
operation. Erasure always requires real authority over the backing source.

There is no automatic fallback from erase-only to erase-and-advance because
constructing a successor solely to discard it can have visible cost.

#### Active-`each` access family

The body does not receive a cursor or position binding. Compiler-managed state
is accessed through one targetable family:

```text
from each [label:] value
from each [label:] underlying cursor
from each [label:] unsafe underlying cursor
from each [label:] erase
```

The target follows the complete `from each` introducer:

```zax
from each value
from each outer: value
```

A missing label selects the lexically innermost active `each`, even when that
statement itself has a label. An explicit label reaches that exact visible
`each`. Selection does not skip an incapable inner target to find a capable
outer one.

`from each value` returns the target traversal's current value with the same
shape and authority available to its ordinary value binding:

```zax
each index := 0 ;;
  # from values ;;
  ++index {
  if index % 15 == 0 {
    sampled : & = from each value
    mutate(sampled)
  }
}
```

No value is produced for the other entries. The family applies to direct
`each in` and cursor-driven `each from` sources when they provide a value.

`from each underlying cursor` applies only to `each from` and yields readonly
access to the actual current cursor. It supports inspection and
readonly-compatible cursor operations without exposing in-place progression.

`from each unsafe underlying cursor` yields the actual cursor with its existing
available qualifications under narrow unsafe responsibility. It does not
manufacture mutability, replacement, erase authority, or lifetime. The
programmer becomes responsible for progression, exhaustion, current-value
validity, later automatic transitions, and replacement results.

`from each erase` performs the target's compiler-coordinated
erase-and-advance transition. Its `Boolean` result is discardable:

```zax
from each erase
hasNext := from each erase

from each outer: erase
outerHasNext := from each outer: erase
```

The result is informational only. It reports whether a successor exists; it
does not control the loop. The compiler separately retains the successor or
absence as pending target state.

##### Postfix `_` keyword escape

A single postfix `_` forces one word through non-keyword grammar and is removed
from its semantic spelling:

```zax
erase outer:  // compiler-recognized active-each erase
erase_ cursor // ordinary phrase operator `erase` applied to cursor
```

The operator declaration remains:

```zax
operator pre unary 'erase' final : (
)() mutable writable = {
}
```

It is never named `'erase_'`.

The escape is legal even when the current compiler has no keyword
interpretation for the base word at that position. This lets older source
protect itself against future contextual keywords. A formatter preserves the
escape; it is not an unnecessary-form diagnostic.

`word` and `word_` have the same semantic identity and cannot declare separate
names. A second trailing underscore does not recursively escape another layer.
The narrow escape suppresses keyword interpretation for one word; future
`bare{...}` remains a separate candidate for a keyword-neutral complete source
enclosure.

#### Compiler-coordinated erasure

Common erase-and-transfer forms are:

```zax
continue with erase
next with erase
break with erase

continue outer: with erase
next outer: with erase
break outer: with erase
```

`with erase` applies to the transfer's ordinary target; it has no second target.
The target must be an `each from` with the required erase capability. Ordinary
bare and labeled transfer selection still applies. A bare transfer does not
skip an intervening loop merely to find an erasable target.

`continue with erase`:

1. skips post;
2. ends the current value binding and active-each access;
3. invokes `erase and advance`;
4. establishes the successor entry or exits on exhaustion.

`next with erase`:

1. runs post while current entry access remains valid;
2. ends current entry access;
3. invokes `erase and advance`;
4. establishes the successor entry or exits.

`break with erase`:

1. skips post;
2. ends current entry access;
3. invokes `erase` without advancement; and
4. exits.

Availability is exact:

| Source form | Required cursor operation |
| --- | --- |
| ordinary progression | viable `advance` |
| `from each erase` | viable `erase and advance` |
| `continue with erase` | viable `erase and advance` |
| `next with erase` | viable `erase and advance` |
| `break with erase` | viable `erase` |

An absent operation makes that form invalid for the selected target. The
compiler neither redirects to an outer cursor nor synthesizes an
erase-and-advance fallback for `break`.

##### Work after standalone erase

Standalone erase permits work that does not use the erased entry:

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

After `from each erase`, the target's current value binding and all
`from each` current-value or underlying-cursor access are unavailable. Other
body locals, initializer bindings, and outer state remain available.

The target has an erase transition pending:

- `continue` targeting it skips post and adopts the prepared successor without
  another `advance`;
- `next` targeting it runs post after erase, diagnoses any post access to the
  invalidated value or cursor, then adopts the prepared successor without
  another `advance`;
- `break` targeting it discards the prepared successor and exits;
- transfer beyond it, `return`, or panic exits normally; and
- normal fallthrough is invalid while the transition remains pending.

This creates an observable distinction:

```zax
next with erase
// Run post while the old entry is valid, then erase.

from each erase
next
// Erase first, then run only a post that does not use old entry state.
```

For a targeted erase:

```zax
from each outer: erase
```

every completing path must resume or exit `outer:` or transfer beyond it.
Re-entering a nested construct while leaving the outer erase transition pending
is invalid. A transfer targeting an inner construct does not satisfy the outer
obligation.

#### Mutation and cursor validity

Active traversal does not acquire a universal exclusive borrow or compile-time
mutation lock over its source.

The compiler rejects established contradictions, such as use after a
compiler-recognized erase, and diagnoses source operations whose contracts
establish cursor invalidation. It does not reject an arbitrary call merely
because that call might reach and mutate the source through an alias.

Each cursor/source family must state:

- which element mutations preserve traversal;
- which structural mutations preserve, reposition, or invalidate cursors;
- which operations invalidate yielded references;
- whether simultaneous cursors remain valid;
- whether opaque or reentrant mutation is stable, runtime-checked, or the
  programmer's responsibility; and
- the cost of any registration, generation, or runtime validation.

A safe cursor may use stable positions, restrict selected operations, or detect
invalidation at runtime. Zax does not impose one strategy or its cost on every
cursor. An opaque function that secretly violates a cursor contract cannot be
made statically visible at every call site.

Compiler-recognized erase is the preferred coordinated removal path, not a
prohibition against all other mutation. Direct access through
`from each unsafe underlying cursor` assumes narrow responsibility for
restoring a state compatible with the statement's later transition.

#### Costs and diagnostics

For a runtime source of `n` entries, `each` performs `n` body entries. Direct
`each in` creates no cursor allocation. `each from` does not imply allocation;
the selected `iterate` implementation owns that cost.

An ordinary cursor value binding invokes `value at` once per entered body.
`#` invokes it zero times, allowing cheap progression with selective explicit
materialization through `from each value`.

A reference binding performs no value construction. A copied binding performs
ordinary construction and destruction once per visited entry. Structural
member `copy` checks and performs that work separately for each selected member
type.

Representative diagnostics distinguish:

- unsupported direct `in` source;
- unavailable `iterate`;
- absent or ambiguous `advance`;
- value binding when the cursor has no viable `value at`;
- incompatible explicit reference or value binding;
- unavailable copy for one structural member specialization;
- unavailable erase or erase-and-advance capability;
- `with erase` on a non-`each from` transfer target;
- an incapable inner target rather than silently selecting an outer one;
- use of current value or underlying cursor after standalone erase;
- post access to invalidated state after `from each erase; next`;
- fallthrough or wrong-target re-entry with an erase transition pending;
- known source mutation that invalidates active traversal;
- unsafe underlying cursor access without narrow unsafe responsibility; and
- a dedicated enum matching source, which does not exist.

Diagnostics should name the selected `each` target, source family, required
binding role, and missing cursor operation. They must not suggest informal
method-name conventions or a general iterator concept when a concrete protocol
operation is absent.

#### Several strings for flags

The traversal model preserves the atomic conversion algorithm:

1. traverse one concrete string source in its defined order;
2. resolve each entry to exactly one enum value under exact or
   ASCII-case-insensitive lookup;
3. accumulate into private state;
4. on any unknown or ambiguous element, unwind traversal and return absence;
5. publish the flags value only after complete success.

No partial flags result escapes. Early failure uses ordinary `each` cleanup.

This does not itself provide one public parameter type meaning "every sequence
of `String`." A concrete collection overload can use the protocol now. A
general iterable parameter still needs generic constraint syntax capable of
requiring `iterate`, cursor progression, and a compatible string value.

#### Remaining future owners

The following boundaries are preserved without leaving the current cursor
semantics loose:

| Concern | Future owner or pressure | Constraint established here |
| --- | --- | --- |
| Generic iterable parameters | type-parameter and concept work | constraints describe the concrete protocol defined here rather than redefining it |
| Reflection payload | reflection work | enum and instance traversal preserve a future role after `value` and `name`; no current binding is exposed |
| Type-declaration traversal | reflection work | functions, operators, generated declarations, and `once` state are not instance-place traversal |
| Dynamic collection stability | collection and indexing work | each cursor/source family states mutation and invalidation behavior |
| General range and view catalog | range and collection work | ranges may supply direct or cursor-based traversal without changing existing `in`/`from` meaning |
| Compile-time execution | compile-time-execution work | compiler-known traversal and per-member checking do not themselves execute runtime effects during compilation |
| Flags sequence parameter | collection or generic owner | conversion remains atomic and consumes entries through the established traversal model |

#### Likely lasting ownership

A dedicated programmer-facing iteration owner should teach the shared `each`
mental model, `in`/`from` distinction, bindings, active-`each` family, concrete
cursor protocol, progression, erasure, invalidation responsibility, costs, and
diagnostics.

Focused integration remains necessary in:

- core flow control for labels, transfers, post ordering, pending erase state,
  unwinding, and completion;
- source structure for header grammar, named binding packets, keyword
  recognition, and postfix `_` escape;
- declarations and bindings for contextual binding shapes and scope;
- lifetimes and references for per-entry references and invalidation;
- qualifiers for cursor/source/element authority;
- transfer stances for receiver variants and replacement cursor state;
- enums for declaration traversal and removal of the previously anticipated
  case-insensitive match traversal;
- future reflection for the reserved role and declaration traversal;
- future generic constraints for expressing the concrete cursor protocol;
- legacy flow material for disposition of old binding, field, array, range,
  and `from` claims; and
- legacy basics material for replacement of the trailing-underscore naming
  interpretation with the keyword-neutral escape.

This ownership sketch is not the documentation-fit dry run and does not
authorize promotion.

### Pre-promotion documentation fit dry run

| Field | Result |
| --- | --- |
| Date | 2026-09-08 |
| Result | **PASS** |
| Meaning | The aligned findings have one coherent owner structure, a direct human reading path, explicit deferred destinations, and an exact promotion set |
| Authorization | This result does not authorize promotion |

The dry run passes because iteration is cohesive enough to require one
programmer-facing owner and because every cross-cutting consequence can be
integrated without creating another directory, a formal specification, or a
competing definition. No unresolved contradiction prevents teaching the
aligned model.

#### Structure proposal

Retain the current `language/` layout and add one owner:

```text
language/
  iteration.md
```

`language/iteration.md` becomes the complete programmer-facing owner for:

- the shared `each` mental model;
- `in` versus `from`;
- header forms, bindings, progression, and exhaustion;
- direct array, enum-declaration, and structural instance-member traversal;
- positional and named binding roles;
- the concrete cursor protocol;
- the active-`each` access family;
- coordinated erase transitions;
- cursor/source mutation responsibility;
- costs, diagnostics, and source stability; and
- the boundary to reflection, collections, and generic constraints.

No `language/README.md`, iterator subdirectory, protocol catalog, or
specification area is needed. Splitting direct traversal and cursors into
separate owner documents would force programmers to reconstruct one `each`
statement across two definitions. Folding the complete feature into core flow
would overload that owner with source families, cursor protocol, value
production, mutation, and collection-specific behavior.

The public reading path becomes:

```text
index.md
  -> language/iteration.md
       -> core flow control for shared transfer and unwinding
       -> source structure for token, layout, and keyword rules
       -> operator catalog for exact protocol forms
       -> applicable source owner for enum, lifetime, or collection behavior
```

Raw and project records remain outside the ordinary programmer route.

#### Finding-to-owner map

| Aligned finding | Lasting owner | Required local integration |
| --- | --- | --- |
| `each` is one flow family with direct `in` and cursor-driven `from` traversal | `language/iteration.md` | Core flow names `each` as a loop and hands complete iteration behavior to the new owner |
| Initializer, traversal, post, body, empty-initializer post form, and phase ordering | `language/iteration.md` | Core flow retains shared initializer/post/transfer semantics; source structure records the exact header and layout |
| Labels and `break`/`continue`/`next` behavior | `language/core-flow-control.md` | Iteration teaches local effects and links to the shared target/unwinding rules |
| Positional bindings, `#`, and wholly named `(...)` packets | `language/iteration.md` | Source structure records the enclosure and label/declaration spacing; declaration principles remain in their current owner |
| Array reference inference, explicit references, copies, order, and place stability | `language/iteration.md` | Existing lifetime owner retains the general fixed-array place rule; legacy array example is corrected and routed |
| Enum declaration order, duplicate-valued aliases, value/name roles, and future reflection position | `language/enums.md` for enum facts; `language/iteration.md` for traversal behavior | Enum owner gains concrete declarations and examples |
| No dedicated case-insensitive match traversal | `language/enums.md` | Remove the deferred match-traversal requirement; retain generated singular lookup and programmer-defined filtering |
| Structural traversal of accessible stored instance member places | `language/iteration.md` | Reflection raw input retains declaration traversal and future metadata |
| Heterogeneous `value :`, `value : &`, and `value : copy` | `language/iteration.md` | Declaration, qualifier, and transfer owners remain general dependencies rather than duplicate definitions |
| Concrete cursor acquisition and operations | `language/iteration.md` | Operator catalog lists exact phrase forms and routes their behavior to iteration |
| Mutable in-place and replacement cursor transitions | `language/iteration.md` | Existing qualifier and transfer owners define the reused axes and stances |
| `from each [label:] value`, underlying cursor, unsafe underlying cursor, and erase | `language/iteration.md` | Source structure owns recognition and target placement; safety owner supplies the existing narrow unsafe model |
| `continue`/`next`/`break` `with erase` and pending erase state | `language/iteration.md` for cursor effect; `language/core-flow-control.md` for transfer ordering and target behavior | Both owners cross-link rather than repeat the complete feature |
| Postfix `_` is a stripped one-word keyword escape | `language/source-structure.md` | Legacy basics is corrected; raw bare-source input narrows to complete enclosed neutralization |
| No universal Rust-like mutation lock | `language/iteration.md` applying the current safety model | Existing vision and safety owners already establish bounded proof and explicit responsibility |
| Cursor/source mutation and invalidation contracts | `language/iteration.md` | Existing lifetime owner retains general place-ending and reference-invalidating rules |
| Atomic flags conversion over several strings | `language/enums.md` | Generic input retains only the missing general parameter constraint |
| Generic ability to require the cursor protocol | `project/raw/type-parameters-and-generics.md` | It must reference the accepted concrete operations rather than preserve an undefined iterator |
| Range, view, slice, proxy, and dynamic-collection participation | `project/raw/indexing-and-slicing.md` | Preserve source-specific order, stability, and invalidation pressure |
| Reflection role and type-declaration traversal | `project/raw/reflection.md` | Preserve the role order and distinguish instance places from declarations |

This map leaves no aligned behavior owned only by numbered work.

#### Human-facing teaching plan

The new iteration owner should be written as teaching rather than as a
transcription of this record:

1. Open with one ordinary array `each in` example and one cursor-driven
   `each from` example.
2. Explain the shared loop mental model and visible costs.
3. Teach initializer, binding, post, body, labels, and transfers.
4. Teach positional bindings and wholly named packets.
5. Develop direct arrays, enum declarations, and structural instance members
   from simple to specialized forms.
6. Introduce `iterate`, cursor acquisition, `value at`, and both progression
   strategies.
7. Teach `#` and selective `from each value` materialization with a concrete
   expensive-value example.
8. Introduce readonly and unsafe underlying cursor access only after ordinary
   cursor use is understood.
9. Teach common `with erase` forms before standalone erase and pending
   transition analysis.
10. Close with mutation/invalidation responsibilities, costs, diagnostics, and
    future generic/reflection boundaries.

The enum owner should teach enum traversal locally after member declarations
and aliases are understood. It should not require an enum reader to learn the
complete cursor protocol merely to understand declaration order.

Examples must use current declaration spacing, flow-label placement, qualifier
ordering, and inline `// error` comments for known failures. Reflection source
must not be shown as currently usable.

#### Exact proposed promotion change set

Create:

- `language/iteration.md` - the cohesive owner and primary teaching path.

Modify current owners and routers:

- `index.md` - add iteration to Start here and Current conceptual design.
- `language/core-flow-control.md` - include `each` in the loop family; integrate
  label eligibility, `continue`/`next`/`break`, post ordering, pending erase
  transitions, and local handoff to the iteration owner.
- `language/source-structure.md` - add exact `each` header and named-binding
  packet presentation; add the `from each` family shape; define postfix `_` as
  a stripped, formatter-preserved, one-word keyword-role escape.
- `language/enums.md` - replace deferred traversal with concrete enum
  declaration traversal and examples; remove the dedicated case-insensitive
  match-traversal requirement; retain singular generated lookup and atomic
  multi-string conversion.
- `language/operator-catalog.md` - catalog `iterate`, `value at`, `advance`,
  `erase and advance`, and ordinary cursor `erase` as language-recognized
  receiver-owned protocol forms, with behavior routed to iteration.

Modify live legacy and raw evidence:

- `arrays.md` - correct the legacy array mutation example to use a writable
  element reference and route complete traversal behavior to the iteration
  owner.
- `flow-control.md` - remove the superseded legacy `each` sections and replace
  them with a concise route to current iteration; retain unrelated legacy flow
  evidence.
- `basics.md` - replace the obsolete claim that postfix `_` is part of a
  discouraged identifier with a route to the current keyword-escape rule.
- `project/raw/feature-catalog.md` - route `each in` and `each from` to the
  current iteration owner rather than raw future work.
- `project/raw/type-parameters-and-generics.md` - preserve only how a future
  generic constraint names the already-defined cursor operations, stable value
  shape, qualification, and lifetime relationship.
- `project/raw/indexing-and-slicing.md` - preserve future range, slice, proxy,
  and dynamic-collection participation and invalidation questions.
- `project/raw/reflection.md` - preserve the future reflection binding after
  value/name and the separate type-declaration traversal problem.
- `project/raw/bare-source.md` - record that postfix `_` now owns one-word
  keyword neutralization and narrow the remaining `bare{...}` candidate to a
  complete tree-transparent enclosure.
- `project/work/017-each-iteration-and-compiler-known-traversal.md` - record
  promotion execution, validation, and final dispositions without changing its
  fixed initiating input.

No promotion edit is required in:

- `language/declarations-and-bindings.md` because iteration-specific bindings
  belong to the new owner and existing flow-initializer scope already supplies
  the general rule;
- `language/lifetimes-and-references.md` because its fixed-array stability,
  reference-origin, and invalidation rules already supply the general contract;
- `language/qualifiers.md` or `language/transfer-stances.md` because iteration
  applies their existing axes and receiver variants without changing them;
- `language/operator-phrases.md` because it already owns phrase declaration,
  keyword-word, receiver, and selection mechanics while the catalog owns exact
  language-recognized forms;
- `language/safety-and-analysis.md` or `language/vision.md` because their
  bounded-safety and narrow-responsibility model already states the reusable
  principle applied by iteration;
- `language/optional-values.md` because cursor absence uses the existing
  optional model without changing it; or
- `README.md` because public concept routing belongs to `index.md`.

#### Raw retirement and closure effects

`project/raw/iteration.md` remains live through promotion because the active
work record still links it as initiating evidence. Promotion must move all of
its continuing value into:

- the new iteration owner;
- enum and other current integrations;
- raw generic input;
- raw indexing and collection input; and
- raw reflection input.

At work-item closure:

- delete `project/raw/iteration.md`;
- remove its row from `project/raw/README.md`;
- archive this work file under its unchanged filename;
- update `project/archive/README.md`;
- update the current-work pointer in `project/README.md`; and
- create work item `018` only after its initiating input and reading scope are
  separately discussed, aligned, and authorized.

The archived work may retain historical links that require Git history. No
current programmer-facing owner will cite this work item or the retired raw
file.

#### Validation plan

After an authorized promotion:

1. Verify every aligned finding above appears in exactly one complete owner or
   an explicit local handoff.
2. Read `language/iteration.md` cold from its opening examples through ordinary
   arrays and cursors before advanced structural traversal and erasure.
3. Verify every unmarked example uses current declaration, qualifier, label,
   separator, phrase, and keyword-escape spelling.
4. Verify enum declaration traversal preserves aliases and that no current
   text promises dedicated case-insensitive match traversal.
5. Trace fallthrough, `continue`, `next`, `break`, labeled transfers,
   `with erase`, standalone erase, post ordering, and cleanup through both the
   iteration and core-flow owners.
6. Verify every cursor protocol form has one exact operator-catalog entry and
   one behavior owner.
7. Verify value production is omitted for `#`, direct and replacement cursor
   strategies both work, and erase availability follows the exact supplied
   hooks.
8. Verify known-invalid post-erase access is diagnosed while arbitrary opaque
   calls are not treated as universal mutation locks.
9. Verify postfix `_` has one stripped semantic identity, remains legal before
   a keyword exists, and is distinct from the multi-token `bare{...}` candidate.
10. Verify current owners contain no links to numbered work or raw material.
11. Verify legacy `each` and underscore claims are removed or routed without
    losing unrelated evidence.
12. Verify all live Markdown links and anchors outside historical archive
    snapshots.
13. Run `git diff --check` and inspect staged, unstaged, and untracked state
    without changing the maintainer's review boundary.

#### Dry-run conclusion

**PASS.** The proposed owner structure can absorb the complete aligned design
without duplicate authority, an orphaned consequence, or a premature formal
specification. The exact promotion set is bounded and the remaining future work
has live indexed destinations. Promotion still requires separate discussion,
alignment on this dry run, and explicit authorization.

### Promotion execution

| Field | Result |
| --- | --- |
| Date | 2026-09-08 |
| Authorization | The language maintainer explicitly authorized promotion after reviewing the PASS dry run |
| Result | **Completed; validation passed** |
| Authority | Promoted language pages are current conceptual design, not a formal specification or implementation claim |

Promotion created `language/iteration.md` as the cohesive programmer-facing
owner. It teaches ordinary direct and cursor-driven traversal before layering
structural member specialization, active-`each` access, erasure, invalidation,
costs, diagnostics, and future boundaries.

Current-owner integration:

- `index.md` now routes human developers to iteration.
- `language/core-flow-control.md` integrates `each` into shared headers,
  progression, labels, transfers, unwinding, and pending erase state while
  leaving complete traversal behavior with the iteration owner.
- `language/source-structure.md` owns `each` header presentation, wholly named
  binding packets, active-`each` source shape, and the stripped postfix `_`
  keyword escape.
- `language/enums.md` now teaches declaration traversal and no longer promises
  dedicated case-insensitive match traversal.
- `language/operator-catalog.md` records the exact cursor protocol phrases and
  routes their behavior to iteration.

Legacy and deferred integration:

- the legacy array mutation example now uses a writable element reference;
- the consumed legacy `each` material was removed from the root flow page;
- the legacy underscore account now reflects keyword-neutral semantic identity;
- the raw feature catalog routes iteration to its current owner;
- raw generic, indexing/slicing, reflection, and bare-source inputs now retain
  only their continuing future pressure.

No promotion change was needed in declarations, lifetimes, qualifiers, transfer
stances, operator phrases, optional values, safety, or vision because their
existing general contracts already support the iteration owner without
duplicate local definitions.

Validation completed:

- the promoted concern has one cohesive owner;
- current local integrations link to that owner rather than numbered work;
- every changed live Markdown file resolves its local file and heading links;
- code fences and the retained historical details enclosure are balanced;
- `git diff --check` passes;
- the changed-file set matches the authorized dry-run promotion set; and
- the staged working-record boundary remains intact while promotion changes are
  unstaged.

`project/raw/iteration.md` remains live only until closure because this active
record still names it as initiating evidence. All of its continuing value has a
current owner or focused raw destination. Closure must retire it and its raw
index entry while archiving this work item.

### Closure

Work item `017` is complete.

- Current `each in`, `each from`, cursor protocol, active-`each` access,
  erasure, mutation responsibility, costs, and diagnostics are owned by
  `language/iteration.md`.
- Shared flow, source, enum, and operator consequences are integrated into
  their current owners.
- Continuing generic, collection, reflection, and multi-token keyword-neutral
  pressure is preserved in focused raw inputs.
- Dedicated case-insensitive enum-match traversal was discarded in favor of
  generated singular lookup and programmer-defined filtering over enum
  declaration traversal.
- Consumed legacy `each` material was removed or corrected.
- `project/raw/iteration.md` has no remaining unique live value and retires with
  this work item.
- Work item `018` begins the separately aligned review of `switch`, `case`,
  `default`, and runtime value selection.

This file is archived under its unchanged subject filename and is excluded from
ordinary onboarding and current design reading.

### Superseded initial reconstruction

<details>
<summary>Initial candidate preserved for review provenance</summary>

The reconstruction below predates maintainer discussion and is superseded by
the aligned model above. It remains non-authoritative evidence and must not be
used as the current working conclusion.

Everything in this section is raw reconstruction or candidate analysis for
maintainer review. It is not an aligned finding.

### Review entry point

The strongest candidate model is that `each` is a finite flow statement whose
progression is owned by the language:

```zax
values : String[3]
values[0] = "bird"
values[1] = "plane"
values[2] = "superman"

// Candidate source. The explicit reference shape is intentional.
each value : String readonly & in values {
  print(value)
}
```

`values` is evaluated once. The compiler establishes a traversal plan for that
known source, binds one current element, runs the body, advances, and stops at
exhaustion. This operation does not call an `iterator` function, does not create
an iterator value that the program can store, and does not infer a user-defined
protocol from matching method names.

The same statement category can support compiler-known enum queries without
pretending that enum metadata is a collection value:

```zax
// Illustrative semantics, not proposed source spelling:
// <case-insensitive matches of BoxKind for text> is a traversal-only source.
each match : BoxKind in
  <case-insensitive matches of BoxKind for text> {
  use(match)
}
```

For `"bluebox"`, this body runs once for each distinct matching `BoxKind` value,
in the order of that value's first matching declaration. It runs zero times
when there is no match. It may run more than once for an acknowledged
case-conflicting name. This is deliberately different from
`BoxKind.fromStringIgnoringCase(text)`, which returns absence rather than
selecting one value when several distinct values match.

The main decision is not whether Zax needs iteration; the accepted enum model
already creates that need. The main decision is whether the bounded direct form
below is a sound foundation:

1. initially admit only traversal sources whose progression, element shape,
   order, and exhaustion rules the language defines;
2. require explicit value-versus-reference binding where that choice has cost
   or mutation consequences;
3. make internal advancement unavoidable on every repeated path;
4. keep first-class iterators, user-extensible sources, and general iterable
   parameters deferred; and
5. avoid retaining `from` until it denotes a real semantic distinction rather
   than a guess about future ranges.

The most important uncertainty is binding syntax and category. Legacy
`value : in source` is useful evidence, but an uninitialized `value :` is an
error under current declaration rules. `each` either needs an explicitly
defined contextual binding form, or it needs syntax that makes the
compiler-supplied initializer and value/reference shape visible. Deciding that
by accident would also decide hidden copy cost, mutation authority, and
element-reference lifetime.

### Candidate direct-traversal model

`each` is a loop for flow-target purposes and a language-known traversal
operation for source-selection purposes.

- A direct traversal source is not required to have a first-class value type.
  An enum-declaration source, for example, may be legal only in an `each`
  traversal clause.
- The compiler must know the source family and its traversal contract at the
  `each` site. An unsupported source is a compile error; the compiler does not
  search for an informal `next`, `current`, `begin`, or `end` convention.
- The traversal contract defines the source evaluation phase, entry shape,
  order, exhaustion test, and whether an entry is a value or an addressable
  place.
- Progression state is hidden statement state. It cannot be named, copied,
  returned, captured, or passed to another function.
- Every initially supported source is finite. `each` is not the construct for a
  callback-driven stream, generator, or potentially unbounded user iterator.
- Adding a concepts-based protocol later may broaden the sources accepted by
  `each`; it must not silently reinterpret existing compiler-known sources,
  binding categories, order, or transfer behavior.

This model separates three facts that legacy wording sometimes combines:

1. **Compiler-known traversal planning** means the compiler recognizes the
   source and can establish its progression and entry shape.
2. **Compile-time body reprocessing** means the body may be checked or
   specialized separately for structurally different entries.
3. **Compile-time execution** means the body's operations actually execute
   during compilation.

The first fact does not imply the second or third. Traversing a runtime array
uses a compiler-known plan but executes the body at runtime. A future
heterogeneous field traversal may require body reprocessing per field type, but
runtime operations in that body still run at runtime unless an independent
compile-time-execution context says otherwise.

### Candidate header and execution phases

The legacy examples support one required traversal section and an optional
initializer:

```text
each traversal-binding in traversal-source body
each initializer ;; traversal-binding in traversal-source body
```

A candidate extension preserves the existing `next`/`continue` distinction by
allowing the ordinary post section:

```text
each ;; traversal-binding in traversal-source ;; post body
each initializer ;; traversal-binding in traversal-source ;; post body
```

For example:

```zax
each values := loadValues() ;;
  value : String readonly & in values ;;
  ++visited {
  consume(value)
}
```

This is candidate grammar, not an accepted spelling. In particular, review
still needs to decide whether `in` belongs before or after the complete
binding, and how an inferred traversal binding spells its compiler-supplied
initializer.

The traversal section occupies the construct-specific decision position. It is
not a Boolean condition. Exhaustion selects completion; an available entry
selects the body. The reconstruction finds no demonstrated need for a separate
programmer-written Boolean condition in an `each` header. A filter belongs in
the body, and an early stopping test can use `break`.

Candidate execution order is:

1. enter the complete `each` flow scope;
2. execute the initializer once, left to right;
3. evaluate or establish the traversal source once;
4. acquire the first available entry or complete if exhausted;
5. establish fresh per-entry binding or bindings;
6. execute one effective body;
7. on normal fallthrough or `next`, destroy body locals, run the optional post,
   end the per-entry bindings, advance, and return to step 4;
8. on `continue`, destroy body locals, skip the optional post, end the per-entry
   bindings, advance, and return to step 4; and
9. on exhaustion or an exit, destroy traversal state and then the surviving
   header bindings in reverse construction order.

Internal advancement is not the programmer-written post operation.
`continue` skips the latter but cannot skip the former. Otherwise a
`continue` would repeatedly re-enter on the same element and defeat the
language's forward-progress guarantee. When no post exists, `next` and
`continue` have the same next-entry destination but remain distinct transfer
operations for consistency with other loops and for stability if a post is
later added.

The body follows current effective-body rules: it may be one simple,
composed, or braced statement; its scope exists regardless of spelling; and an
opening brace remains on the final physical header line. Header sections use
`;;`, not statement-composition `;`, and multiline sections use the common
two-space continuation level.

### Binding, qualification, and lifetime

The direct model needs an explicit distinction between an entry **value** and
an entry **place**.

- An enum value or declaration ordinal can be supplied as an immutable value.
- A fixed-array entry has a stable element place for the array's life path.
- A value binding constructs one fresh owned value per visited entry and
  destroys it before the next entry. For an array element, that construction
  ordinarily offers `copy` and therefore may run programmer-defined code,
  allocate, share storage, or be unavailable.
- A reference binding aliases the current element place and performs no
  element copy. Its qualifications cannot exceed the source place.
- A writable reference permits element mutation only when the source and
  element place are mutable and writable. A readonly traversal cannot gain
  writable authority through inference.
- A same-place traversal alias has its own declaration stance. It does not
  inherit `move` or `last` merely because the containing source has that
  stance.
- No initial traversal implicitly moves from, terminally consumes, or destroys
  source elements. A request for `move` or `last` needs a later destructive
  traversal contract and should initially be diagnosed rather than guessed.

An explicit reference example makes the zero-copy behavior visible:

```zax
each value : String readonly & in values {
  inspect(value)
}
```

A by-value form must likewise make its construction visible. The current
declaration grammar suggests an explicit element type can carry that meaning:

```zax
// Candidate: one ordinary copy construction and destruction per entry.
each value : String in values {
  keep(value)
}
```

The compact legacy form remains unresolved:

```zax
each value : in values {
  inspect(value)
}
```

It could be defined as a contextual declaration whose source supplies an
initializer and complete shape, but the reconstruction does not recommend
choosing a value copy or reference implicitly. A safer direction is either to
require enough type shape to distinguish them or to introduce a
traversal-specific capture form after discussion.

Each pass creates a fresh binding life path; it does not repeatedly assign a
single long-lived owned variable. Body locals die first, the optional post may
still observe the current entry, and the entry binding then dies before
advancement. A reference cannot escape beyond either its source life path or
the use permitted by its per-entry binding. Existing lifetime and capture
analysis must reject an escaping borrow rather than extending an array element
or traversal temporary silently.

A temporary source is kept alive through completion or unwinding of the
complete `each` statement. This extension owns the source, not arbitrary
references returned from it. A fixed array keeps stable element places, so
replacement through the current writable element place can be permitted.
Replacing the complete source or performing an operation that can relocate,
remove, or invalidate element places while traversal is active must be rejected
unless that source family later defines a stronger mutation contract.

### Flow transfers, cleanup, and termination

An `each` statement accepts a flow label in the same position and namespace as
other loops:

```zax
each outer: value : String readonly & in values {
  if done(value)
    break outer:
}
```

Candidate transfer behavior is:

| Completion | Current body locals | Optional post | Current entry binding | Traversal |
| --- | --- | --- | --- | --- |
| body fallthrough | destroy | run | end | advance |
| `next` targeting this `each` | destroy | run | end | advance |
| `continue` targeting this `each` | destroy | skip | end | advance |
| `break` targeting this `each` | destroy | skip | end | exit and clean up |
| transfer to an outer target | destroy while unwinding | skip | end | exit and clean up |
| `return` | destroy while unwinding | skip | end | exit and clean up |
| panic | destroy according to panic unwinding | skip | end | clean up according to the same unwinding path |

The table describes the complete operation, not an exposed `advance` hook.
Every repeated path advances exactly once. Empty sources execute no body or
post. Finite source exhaustion completes normally. A body cannot suppress
advancement accidentally, and no source callback can report success without
progress because direct traversal has no such callback.

### Initial compiler-known sources

The smallest coherent initial family appears to be:

| Source | Phase | Order and entry | Candidate status |
| --- | --- | --- | --- |
| Fixed-shape array expression | runtime value traversal | increasing index order; each entry can expose its stable element place | strong initial candidate |
| Enum member declarations | compile-time-known declaration traversal with runtime body effects when present | every declaration in source order, including duplicate-valued aliases; name, value, and declaration ordinal available | required, but exact source and binding shape remain open |
| ASCII case-insensitive enum matches | compile-time-known candidate set plus runtime query | each distinct matching value once, in first matching declaration order | required, but exact traversal-only query spelling remains open |

For enum member traversal, the narrow facts required here do not justify a
general reflection object. Candidate source should expose the declared ASCII
name, enum value, and declaration ordinal without promising that arbitrary
declaration metadata is available at runtime. Two plausible source shapes need
review:

1. a source-specific multi-binding clause that captures those three facts
   directly; or
2. a narrow compiler-owned member-entry value with only those fields.

The first avoids inventing a metadata type but needs positional or named
binding grammar. The second is easier to pass to ordinary functions but begins
to define a first-class metadata value and its storage, lifetime, visibility,
and reflection relationship. The reconstruction therefore leans toward a
narrow multi-binding form but does not propose exact tokens yet.

Case-insensitive match traversal is query-specific rather than general member
iteration plus a user-written comparison. Its match set is established from
one evaluation of the input. Equal enum values are deduplicated even when
several matching declarations name them; the surviving order is the order of
their first matching declarations. This gives programmers the information that
unique lookup deliberately discards without changing the unique lookup result.

The compiler may unroll, table-drive, or otherwise optimize these operations
only when observable source evaluation, order, binding behavior, body effects,
and cleanup remain the same. The conceptual design does not promise runtime
metadata tables or a particular lowering.

### `in`, `from`, arrays, and ranges

Legacy material appears to use:

- `in` for declarations or elements contained by an enum, type, instance, or
  array; and
- `from` for values produced by a range or view expression.

That evidence does not yet establish a coherent language distinction. In
particular, the legacy `reverseView(values)` example already denotes the
desired traversal order. If that expression can serve as a traversal source,
then:

```zax
each value : in reverseView(values)
  print(value)
```

is compositionally sufficient. A second relation word adds value only if
`from` means something observable such as consuming a single-pass producer,
while `in` borrows stable contained places. Zax does not yet have the range,
ownership, or iterator contracts needed to make that promise.

The current candidate therefore keeps `in` for direct traversal and does not
reserve `from` as accepted syntax. Range traversal remains valid pressure:
future range work may show that `from` is necessary, or may show that ranges
are ordinary `in` sources whose expression determines order. That later choice
must preserve explicit source lifetime, element category, restartability,
mutation, and cleanup rather than rely on the English feel of the preposition.

Fixed arrays are a stronger initial source because their length, index order,
and element-place stability are already knowable. Dynamic arrays, slices,
proxies, variadics, and arbitrary collections remain excluded until their
owners define element-place invalidation and traversal shape.

### Structural field traversal

Legacy material says that `each value in MyType` traverses contained variables
and recompiles the body for each subtype. Its examples expose several
contradictions:

- prose names a type as the source while one example initializes a `myType`
  instance whose values it apparently expects to print;
- a type has field declarations, not runtime field values, unless an instance
  is also supplied;
- heterogeneous fields cannot share one ordinary inferred value type;
- the optional `metadata` capture has no current type or lifetime;
- private access and declaration-reflection visibility are not stated; and
- "re-compiled" does not say whether runtime body effects are duplicated,
  compile-time executed, or merely separately type-checked.

The initial direct model should not admit this source by implication. Preserve
it as reflection and structural-traversal pressure. Reactivate it when Zax
needs a concrete operation over heterogeneous instance fields or declaration
metadata. That work must decide type versus instance traversal, access control,
binding specialization, metadata shape, source order, and whether the body is
reprocessed per field type. Current `each` must not claim general reflection or
runtime metadata in the meantime.

### Several strings for flags

Direct `each` can describe the body of an atomic flags conversion, but it does
not by itself supply a public parameter type meaning "any sequence of
`String`." A generated operation still needs one concrete accepted source
shape or a later concepts-based iterable constraint.

The conversion rule remains clear:

1. evaluate the concrete string source once;
2. inspect entries in source traversal order;
3. resolve each entry to exactly one enum value under the selected exact or
   ASCII-folded rule;
4. accumulate into private state;
5. on an unknown or ambiguous entry, unwind traversal and return absence; and
6. publish the combined flags value only after complete successful traversal.

No partial flags result escapes on failure. Early failure must clean up the
current binding, traversal state, source temporary, and header state under the
ordinary `each` unwinding rules.

The unresolved API surface is material. A fixed-shape array traversal statement
does not automatically provide one callable signature accepting every array
length, and inventing a compiler-special generic signature would hide the
missing constraint model. Keep `fromStrings` and
`fromStringsIgnoringCase` deferred until a concrete collection parameter is
accepted or concepts can express the general contract.

### Costs and diagnostics that belong in the model

For a runtime source of `n` entries, direct traversal performs `n` body entries
and no iterator allocation. A reference binding adds no element construction.
A value binding performs the selected construction and destruction once per
visited entry. The source expression runs once, not once per entry.

Enum member expansion may increase generated code with the number of
declarations when runtime body work is specialized or unrolled. A folded-name
query may inspect multiple declared names and may run the body for several
distinct values. Implementations may improve lookup and code size without
changing order or distinct-value behavior.

Representative diagnostics need to distinguish:

- an unsupported traversal source from a missing ordinary overload;
- a malformed traversal binding from an ordinary uninitialized declaration;
- a requested reference binding when the source supplies only a value;
- a requested writable binding from a readonly source;
- unavailable `copy` construction for a by-value element binding;
- an attempted `move` or `last` without a destructive traversal contract;
- source replacement or mutation that can invalidate active element places;
- an escaping per-entry reference;
- wrong enum member-binding arity or role;
- use of enum-only traversal on a non-enum type; and
- `from` as legacy/unestablished syntax if the `in`-only model is selected.

Diagnostics should name the source family and the required entry category. They
should not suggest adding conventionally named iterator methods before such a
protocol exists.

### Legacy tensions requiring disposition

1. **`value :` conflicts with current declarations.** The traversal context can
   plausibly provide the missing initializer, but exact inference and
   value/reference meaning must be designed explicitly.
2. **Legacy "condition" is actually a traversal clause.** The examples show no
   independent Boolean test. Treating it as a condition would obscure source
   acquisition and exhaustion.
3. **`continue` cannot skip internal advancement.** Applying the ordinary
   "skip post" slogan to hidden progression would repeat the same entry
   forever. Internal advance and programmer post must remain distinct.
4. **`from` has no demonstrated contract.** Its examples suggest range-produced
   order, but that order can already belong to the source expression.
5. **Type-field examples conflate declarations and instance values.** They
   cannot establish a safe current source without reflection and heterogeneous
   binding design.
6. **Compile-time knowledge is not compile-time execution.** Legacy
   "re-compiled per subtype" wording needs a more exact specialization and
   runtime-effects model before structural field traversal can be accepted.
7. **Bulk flags conversion remains signature-blocked.** A statement capable of
   traversing one array does not establish a general iterable parameter.

### Decisions for maintainer review

The next discussion should start with the first three decisions because they
control most later detail:

1. Does the direct-flow model, with hidden compiler-owned progression and no
   first-class iterator value, match the intended role of initial `each`?
2. Should the header have a distinct traversal section plus optional ordinary
   post, with no separate Boolean condition?
3. Must a traversal binding spell value versus reference shape explicitly, and
   if so, can current partial type syntax express that without a new binding
   form?
4. Should initial source syntax use only `in`, leaving `from` unaccepted until a
   future range contract demonstrates a distinction?
5. Should enum members use narrow multi-binding rather than a first-class
   metadata entry, and what programmer task should decide the exact capture
   roles?
6. Should the initial supported runtime source stop at fixed-shape arrays, with
   ranges and dynamic collections deferred?
7. Is case-insensitive match traversal a dedicated enum query source, or should
   it be derived from member traversal without exposing accidental runtime
   metadata or repeated values?
8. Should heterogeneous field traversal remain deferred as proposed, or is it
   essential enough to require reflection and body-reprocessing design in this
   work item?

### Captured adjacent deferrals

| Deferred concern | Live destination | Reactivation pressure | Constraint on current work |
| --- | --- | --- | --- |
| First-class iterators, iterable parameters, and user-defined traversal | `project/raw/iteration.md`; consult generic input only when a concrete constraint model is needed | concepts or equivalent constraints can state element type, stepping, lifetime, and ownership | direct `each` must not invent a structural protocol or expose hidden traversal state |
| Range, reverse-view, slice, proxy, variadic, and dynamic-collection traversal | `project/raw/iteration.md`; `project/raw/indexing-and-slicing.md` when element access or proxy rules become concrete | an accepted range or collection contract needs `each` integration | do not assign `from`, restartability, place stability, or consuming behavior prematurely |
| Heterogeneous fields and declaration metadata | `project/raw/reflection.md` | a concrete type/instance field traversal or tool needs metadata | enum traversal exposes only its narrow required facts and does not reserve general reflection wording |
| Destructive or consuming traversal | `project/raw/iteration.md` and current transfer-stance rules | a source must yield move-only elements or promise single-pass consumption | initial traversal never infers `move` or `last` |
| General multi-string flags input | `project/raw/iteration.md` and the deferred enum section | a concrete collection parameter or iterable constraint becomes expressible | preserve all-or-absence conversion and complete cleanup; do not fake a generic signature |
| Compile-time execution during traversal | future compile-time-execution work; structural metadata remains in `project/raw/reflection.md` | a programmer needs traversal body effects during compilation rather than runtime specialization | compiler-known planning and body reprocessing must not imply compile-time execution |

### Likely lasting owner boundaries

If the model is later aligned, a dedicated human-facing iteration owner is
likely needed for the `each` mental model, source families, bindings,
progression, costs, and future protocol boundary. Existing owners would still
need focused integration:

- core flow control for progression, labels, transfer targets, completion, and
  unwinding;
- source structure for the final header schema, separators, continuation, and
  body boundary;
- declarations and bindings for the contextual per-entry declaration, scope,
  and inference;
- lifetimes and references for source extension, stable element places, and
  escaping references;
- transfer stances for by-value construction, same-place aliases, and the
  prohibition on implicit destructive traversal;
- enums for concrete member and case-insensitive-match traversal declarations
  and examples;
- the legacy root flow page for value-by-value disposition of `each in`,
  `each from`, arrays, ranges, fields, and metadata claims; and
- raw iteration and reflection input for concept-dependent or
  reflection-dependent material that remains deferred.

This ownership sketch is not a documentation-fit dry run and does not propose
promotion yet.

</details>
