# Zax qualifiers

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers reading, writing, or evaluating Zax |
| Applies To | Programmer-facing qualifier behavior; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | Place-replacement, value-mutability, and access qualifiers; type-side truth versus declaration-side replacement permission; qualifier attachment, defaults, inheritance, restatement, type-alias property overlay validation, ordering, ordinary promise strengthening, explicit unsafe weakening, deep immutability, semantic-indirection qualification boundaries, unsafe pliability, varying immutable places, reconstructive replacement at the depth required by qualifiers, receiver-operand constraints, and immediate construction, destruction, indirection, concurrency, and structural-typing boundaries |
| Does Not Own | Complete transfer behavior ([transfer stances](transfer-stances.md)); declaration/binding behavior ([declarations and bindings](declarations-and-bindings.md)); invocation/result preference ([function invocation](function-invocation.md)); lifecycle behavior ([construction and destruction](construction-and-destruction.md)); complete [optional behavior](optional-values.md), [variant behavior](variants.md), or [union behavior](unions.md); [reference lifetime](lifetimes-and-references.md); or [pointer ownership and arenas](pointers-and-arenas.md) |

## Mental model

Zax separates three questions that other languages often combine:

1. May this storage place receive another value lifetime?
2. May the current value lifetime's contained state change?
3. May this particular access path perform an otherwise available change?

Each question has its own qualifier axis:

| Axis | Stances | Governs |
| --- | --- | --- |
| Place replacement | `final` / `varying` | `final` promises that the place keeps the same value lifetime; `varying` permits another lifetime in that place, and the declaration side controls whether this path may initiate it |
| Value mutability | `mutable` / `immutable` | `immutable` promises that the current value's contents do not change through ordinary safe behavior; `mutable` makes no such promise |
| Access capability | `writable` / `readonly` | `readonly` promises that this path performs no change; `writable` may perform an otherwise permitted content mutation or place replacement |

These are called **place-replacement qualifiers**, **value-mutability
qualifiers**, and **access qualifiers**.

Transfer stance is not a fourth per-layer qualifier axis. `copy`, `deep`, `move`,
and `last` describe how one complete source or receiver is offered to a
consumer:

```zax
source : Buffer mutable readonly final & move
```

This is:

- a mutable current `Buffer` value;
- readonly access through this path;
- a final referenced place; and
- one `move` stance on the complete source.

`readonly` narrows only access. It does not erase mutable/immutable or
final/varying truth. Complete transfer meaning, fallback, and projection are
defined by [Zax transfer stances](transfer-stances.md).

Begin with omitted value and access defaults:

```zax
source final : Buffer = makeBuffer()

source.append(data)   // legal: mutable and writable are defaulted
source = makeBuffer() // error: source's place is final

reader : Buffer readonly final & = source
reader.append(data)   // error: this path is readonly
```

`final` governs replacement of `source`'s place. It does not prevent mutation of
the defaulted mutable value through the defaulted writable source path.
`readonly` removes change authority from `reader` without changing the value's
mutability or the place's final stance.

## Type-side truth versus declaration-side permission

`final` and `varying` occupy two positions that answer two different questions:

| Position | Meaning |
| --- | --- |
| Type-use side | Whether the underlying place is actually `final` or `varying` |
| Declaration-name side | Whether this declaration or access path may exercise replacement authority |

This is the same shape as the distinction between a mutable value and a readonly
access path. Teach it as **capability versus permission**:

| Kind of change | Underlying capability | Permission through this path |
| --- | --- | --- |
| Mutate the current value lifetime's contents | `mutable` | `writable` |
| Replace the entire value lifetime in place | type-side `varying` | `writable` plus declaration-side `varying` |

The restrictive counterparts are:

| Concern | Underlying restriction | Path restriction |
| --- | --- | --- |
| Content mutation | `immutable` | `readonly` |
| Whole-value replacement | type-side `final` | declaration-side `final` |

`readonly` and `writable` appear in the qualified type use but semantically
describe the current access path rather than the nominal base type. `readonly` is
broad: it removes both content-mutation and whole-value-replacement authority
through that path, so a second declaration-name `readonly` would only repeat the
same fact.

Declaration-side `final` is narrower. It removes only whole-value replacement
authority while preserving writable content mutation when the value is mutable:

```zax
source varying : MyType mutable varying

reader : MyType mutable readonly varying & = source
restricted final : MyType mutable writable varying & = source

reader.member = replacement     // error: readonly path
restricted.member = replacement // legal: mutable value and writable path
restricted .= makeMyType()      // error: declaration-side final
```

That is why `final`/`varying` needs both positions while `readonly`/`writable`
does not.

So:

```zax
myType final : MyType varying
```

means:

- the underlying type/place is varying;
- an authorized path may replace its value lifetime in place;
- this declaration promises not to exercise that replacement authority; and
- the declaration may still mutate contents when the value is mutable and the
  path is writable.

### Same-place aliases

A restricted same-place view preserves the underlying truth while narrowing
replacement authority through that declaration:

```zax
myType varying : MyType varying

myAliasType final : MyType varying & = myType
```

`myType` retains replacement access. `myAliasType` cannot initiate replacement,
but its type use still records truthfully that another path may replace the
referent. A same-place alias never misreports the referent's actual stance, and an
omitted type-side stance inherits that actual stance rather than changing it.

### Permitted and rejected combinations

```zax
name final : Type             // type-side omission resolves final for new storage
name final : Type varying     // legal: restrict replacement through this path
name varying : Type varying   // legal: retain replacement access
name final : Type final       // legal
name varying : Type final     // error: access cannot exceed underlying capability
```

Declaration-side `final` may narrow replacement access from varying to final. It
may never widen final storage to varying.

Declaration-side replacement permission must survive aliases, argument mapping,
results, and captures. A declaration-final path cannot regain replacement
authority merely by being supplied to a callable whose referent type remains
varying. See
[Zax function invocation](function-invocation.md#declaration-side-replacement-permission).

### Immutable varying places seen through several paths

```zax
value varying : MyType immutable varying

observer final :
  MyType immutable readonly varying & = value

replacer varying :
  MyType immutable writable varying & = value

restricted final :
  MyType immutable writable varying & = value

replacer .= makeMyType()  // reconstructive replacement
restricted .= makeMyType() // error: this declaration lacks replacement authority
```

The underlying place can undergo compiler-recognized reconstructive replacement,
ending one immutable lifetime and beginning another. `observer` and `restricted`
must understand that `replacer` may do so. `restricted` remains writable for
operations that do not require whole-value replacement, but its declaration-side
`final` prevents it from initiating reconstructive replacement. An ordinary
`restricted = ...` expression may still select a viable in-lifetime operator,
but it receives no lifecycle authority.

Effective whole-value replacement through one path therefore requires:

- underlying type/place stance `varying`;
- declaration/access replacement permission `varying`;
- writable access;
- a viable replacement operation.

### Inspecting the stance

The protected `is final` query inspects the resolved type-use or referent-place
truth, not this declaration's replacement permission:

```zax
restricted final : MyType varying

restricted is final // false
```

Whether a particular declaration may exercise replacement belongs to future
access or declaration reflection.

### Pointer and reference bindings

Declaration-side `final` restricts whole-value replacement *through that
declaration*. It is not a statement about independently replacing or rebinding
the pointer binding itself. A pointer may be repointed according to its pointer
type and permissions. A reference binds permanently to one instance place and
never rebinds. Complete behavior is defined by
[lifetimes and references](lifetimes-and-references.md#references-never-rebind)
and [pointers and arenas](pointers-and-arenas.md#pointer-instances-and-pointees).

## What each axis promises

### `final` and `varying`

Type-side `final` says the qualified place is not ordinarily replaceable.
Type-side `varying` says it may receive another value lifetime when a writable
path with declaration-side replacement permission and a viable replacement
operation are available.

An ordinary alias to the same place preserves that actual type-side stance. A
varying referent may not be presented as final; declaration-side `final` or
`readonly` supplies the access restriction instead.

```zax
config final : Config mutable = makeConfig()

config .= makeOtherConfig() // error: config is final
config.refresh()           // legal when this access is writable
```

### `mutable` and `immutable`

`mutable` permits the current value lifetime's contained state to change. It
does not grant every access path permission to perform that change.

`immutable` guarantees recursively that the value and its contained values do
not mutate through ordinary safe behavior after construction completes. A
varying place may later end that immutable lifetime and begin another; that is
place reconstruction, not mutation of either instance.

```zax
value : Document mutable = makeDocument()
reader : Document mutable readonly & = value
```

The value is mutable, but `reader` cannot mutate it.

### `writable` and `readonly`

`writable` permits the current path to perform a change when the applicable place
or value axis and a viable operation also permit it.

`readonly` prevents the current path from mutating the value or reconstructing
the place. It does not freeze the underlying value, make a varying place final,
or suspend independent writable aliases:

```zax
source : Counter
reader : Counter readonly & = source

source.increment() // legal through the original writable path
reader.increment() // error: reader is readonly
```

`reader` remains readonly for its lifetime. Writable access is not recovered
from it when the view ends; `source` retained a separate writable path
throughout.

The axes combine when deciding whether a change is available:

| Operation | Required qualifications |
| --- | --- |
| Mutate the current value lifetime's contents | `mutable` + `writable` |
| Use complete `.=` reconstruction | Type-side `varying` + declaration-side `varying` + `writable`; the old value may be mutable or immutable |
| Observe a stable immutable place | `immutable` + `readonly` + `final` |
| Observe successive immutable lifetimes in one replaceable place | `immutable` + `readonly` + type-side `varying`, explicit or inherited |

## Varying places with immutable lifetimes

A varying place may hold successive immutable values:

```zax
message varying : Message immutable = makeMessage("first")

observer final :
  Message immutable readonly varying & = message

replacer varying :
  Message immutable writable varying & = message

display(observer)                 // "first"
replacer .= makeMessage("second") // reconstructive replacement
display(observer)                 // "second"
```

Neither immutable instance mutates. The first lifetime ends and another
immutable lifetime begins in the same storage. `replacer` needs
declaration-side `varying` in addition to writable access; a declaration-side
`final` alias of the same place could observe the transition but never initiate
it.

The reference remains bound to `message`'s place while that varying place
receives a successor resident instance. This is ordinary reference behavior,
not a separate tracking policy. An omitted type-side stance inherits the actual
referent-place truth:

```zax
implicit : Message immutable readonly & = message
display(implicit) // observes the completely established resident instance
```

A final referent view is unavailable:

```zax
stable : Message immutable readonly final & = message
// error: message's place is varying
```

The complete place, resident-instance, and member-renewal model is defined by
[lifetimes and references](lifetimes-and-references.md#mutation-and-replacement).

## Qualifier attachment

Qualification follows the entity being constrained:

- a declared binding has its own place qualification;
- a directly stored value has a value-mutability qualification;
- an alias or projection has an access qualification;
- a referenced or pointed-to place has a place qualification independent from
  the binding that stores the reference or pointer; and
- every further pointer or reference level may introduce another independently
  qualified place and access path.

The syntax must therefore distinguish a final pointer binding from a pointer to
a final place. A varying pointer may point to a final pointer that in turn points
to another final place. Replacing the outer pointer replaces neither inner
place.

The exact grammar for multiple pointer and reference levels remains later
indirection design. Whatever spelling is selected must preserve these distinct
layers.

## Defaults and resolution

The baseline omission defaults are:

- place replacement: `varying`;
- value mutability: `mutable`; and
- access capability: `writable`.

These defaults are the same everywhere. No directive or source context changes
them, so a declaration copied from one module into another means exactly the
same thing. A project that wants every qualifier spelled out may enforce that
with a lint, which never changes meaning.

Qualifiers resolve independently at every applicable layer:

1. preserve inherent or inherited qualifications supplied by the source,
   referent, or resolved type;
2. apply explicit qualifications at their stated layers;
3. apply the defaults only to still-unresolved axes; and
4. diagnose contradictions in the resulting combination.

A default never overrides a qualification carried by the source:

```zax
source final : Payload immutable = makePayload()

sameView : Payload immutable final & = source
reader : Payload readonly final & = source
writer : Payload writable final & = source // error
```

If `immutable` and `writable` are configured simultaneously as defaults, the
configuration itself is legal. A declaration to which both become applicable is
contradictory and fails at that declaration. This allows a project to force an
explicit choice in selected contexts.

A possible future mode with no fallback for an axis is deferred. Baseline Zax
provides sensible defaults.

### Relaxed abstract-role matching

Ordinary abstract composition roles use the same omission rule: an omitted
qualifier axis resolves to its applicable default. `abstract relaxed` is the
narrow exception for role matching:

```zax
ObserverContract :: type {
  exactObserve abstract : ()()
  observe abstract relaxed : ()()
}

Observer :: type {
  contract own private : ObserverContract

  exactObserve fulfill contract.exactObserve final : ()() = {
  }

  observeReadonly fulfill contract.observe final : ()() readonly = {
  }

  observeWritable fulfill contract.observe final : ()() mutable writable = {
  }
}
```

The exact role's omissions choose defaults. The relaxed role leaves only those
otherwise defaulted receiver axes open, allowing the two explicitly
qualification-specialized fulfillments shown above. It does not create a
general “no defaults” mode: qualifiers written on the role remain required, and
parameter, result, base-type, arity, indirection, transfer, label, and provenance
requirements remain exact. Value roles similarly relax only omitted axes on the
outer required value layer. Complete role and fulfillment behavior belongs to
[Zax composition](composition.md#abstract-roles-and-explicit-fulfillment).

Role optionality is independent of qualifier relaxation. An
`abstract optional relaxed` role may have zero or more distinct
qualification-specialized fulfillments; `optional` does not open another
qualifier axis or weaken any explicitly written requirement.

## Restatement, duplication, and conflict

Compatible qualification may reach one entity from distinct sources:

```zax
source final : Payload immutable = makePayload()
reader : Payload immutable final & = source
```

The explicit `immutable` on `reader` restates the qualification inherited from
`source`. This is legal and may be useful for aliases, generic substitution, or
deliberate clarity.

The same word may also appear at multiple pointer or reference levels because it
qualifies different entities.

Repeating one qualifier token at one syntactic point is an error:

```zax
value final final : Payload = makePayload() // error
```

Opposing stances applied to the same entity and layer are also errors. A
diagnostic must identify the entity and layer rather than treating identical
words at distinct indirection levels as duplicates.

### Type-alias property overlays

A concrete `alias type` may replace an inherited requested qualification on the
same axis:

```zax
ReadView :: alias type Payload readonly &
WriteView :: alias type ReadView writable
```

`WriteView` requests `Payload writable &`; it does not convert a readonly source
or claim that every `Payload` place is writable. Alias resolution happens before
an actual declaration binds its source:

1. inherit the original type and less-local alias properties;
2. replace axes explicitly stated by the more-local alias or use;
3. apply ordinary defaults to unresolved axes; and
4. validate the complete profile against the actual source, place, and access.

The final step preserves the ordinary rule that a source's real qualifications
cannot be overridden. An alias cannot turn immutable state mutable, readonly
access writable, or a final place varying merely by spelling another profile.
Canonical identity and the general overlay operation are defined by
[identity types](identity-types.md#alias-property-overlays-do-not-create-identity)
and
[declarations and bindings](declarations-and-bindings.md#exact-aliases-and-property-overlays).

## Canonical ordering

On the declaration-name side, canonical ordering is:

1. place replacement;
2. `unsafe pliable` or explicit `unpliable`; and
3. the declaration colon.

On the type-use side, read outward from the base type:

1. base type;
2. value mutability;
3. access capability;
4. referent-place replacement; and
5. optional, pointer, or reference marker;
6. then the independently qualified outer layer, when another layer follows.

```zax
source final : Payload immutable = makePayload()
view final unpliable : Payload immutable readonly final & = source
escape final unsafe pliable : Payload immutable readonly final & = source
```

A formatter may normalize ordering, spacing, and layout. It must not add or
remove explicit qualifiers. A linter may identify compatible restatement or an
explicit default as redundant without changing source.

## Ordinary promise strengthening

Ordinary views may narrow authority and strengthen the promise made by the new
path:

- `writable` to `readonly`;
- declaration-side `varying` to declaration-side `final`;
- a mutable value to a readonly path; and
- an immutable value to a readonly path.

The reverse directions weaken promises and are not ordinary conversions:

- `readonly` to `writable`;
- declaration-side `final` to declaration-side `varying`; and
- `immutable` to `mutable`.

Mutable or readonly state does not thereby become immutable. Readonly constrains
one path; immutable makes a guarantee about one value lifetime.

Type-side final/varying is not an ordinary same-place authority conversion. An
alias preserves the actual referent-place stance, even when its own
declaration-side permission is narrower. A new independent destination place
resolves its own stance:

```zax
source varying : Payload = makePayload()
copy final : Payload = source
```

`copy` is a new final place; `source` remains varying.

## Unsafe casts and new values

An explicit `unsafe cast` may change qualification in either direction. Its
written destination type states the complete result qualification, so it can
produce mutable or writable pointer or reference access from immutable or
readonly input, or add an `immutable` promise that a mutable value cannot make
through an ordinary view:

```zax
source : Payload immutable = makePayload()
writableView := source unsafe cast Payload mutable writable &
```

For a qualification change on the same type, prefer
[`unsafe pliable`](#unsafe-pliable), which keeps the recorded qualifications and
bypasses them locally. Complete `unsafe cast` behavior is defined by
[Zax conversions and casts](casting.md#qualification).

An ordinary mutable alias cannot safely become an immutable alias. Other mutable
aliases may still change the same value. Mutable-to-readonly access is safe
because it constrains only the new path.

A new immutable value may be produced safely from mutable input when the
operation establishes the complete deep-immutability guarantee. This may
involve:

- constructing an independent immutable value;
- making a sufficiently independent copy; or
- consuming mutable storage for which the compiler proves exclusive authority
  over all storage covered by the immutable guarantee.

A consuming transition ends the former mutable capability. A scoped
non-mutating view that later permits mutation again is readonly, not temporarily
immutable. Exact uniqueness proofs, freezing operations, `copy` selection, cost
visibility, and syntax remain future work.

## Construction and deep immutability

Immutability is recursively deep over direct structural containment. Semantic
indirection boundaries retain independently stated qualifications. Pointer,
reference, optional, and variant layers therefore do not silently rewrite the
qualifications of their pointee, referent, or boxed value:

```zax
stablePresence :
  Payload mutable writable? immutable readonly
```

The optional wrapper's presence is immutable and readonly through this path.
After proven access, the boxed `Payload` has its separately written
mutable/writable qualifications. The compiler may rely on stable wrapper state
but not stable payload bytes.

This independence does not remove lifetime ownership. An optional wrapper owns
its boxed lifetime and may end it through an otherwise authorized wrapper
operation. Complete behavior is defined by
[Zax optional values](optional-values.md#wrapper-and-boxed-qualifications).

Construction has authority to establish final and immutable state without an
unsafe bypass. The guarantees activate when the full instance, including all
contained parts, has completed construction and becomes ordinarily observable.

If construction does not complete, no fully constructed instance becomes live.
Member sequencing, completion, panic boundaries, premature publication, and
escaped construction-time aliases are defined or bounded by
[Zax construction, replacement, and destruction](construction-and-destruction.md).

## Reconstructive replacement

Protected `.=` requests complete reconstructive replacement. It is available
when the existing destination is type-side varying, this declaration has
declaration-side varying replacement permission, and the current path is
writable.

The old value may be mutable or immutable:

```zax
mutableValue .= replacement
immutableValue .= replacement
```

For a mutable value, ordinary `=` may instead select an in-lifetime assignment.
For an immutable value, ordinary `=` retains immutable receiver authority and
cannot change represented state. It never silently gains lifecycle authority.

Type-side `final`, declaration-side `final`, and readonly access each block
complete `.=` reconstruction at that layer. `writable` alone cannot overcome a
final place, and `varying` alone cannot overcome readonly access.

The protected operation ends one complete value lifetime and establishes
another in the same storage. Its lifecycle skeleton is compiler-owned and cannot
be replaced by an ordinary user-defined `=` or `.=` body. A type customizes the
transition with a
[replacement constructor](terms.md#replacement-constructor):

```zax
+++ replacement final : ()(
  rhs : Input readonly &
) = {
  // `_` initially contains the previous current-instance state.
}
```

`replacement` is contextual syntax only when it immediately follows `+++`
where a constructor declaration is legal, in the same post position as the
other constructor words such as `contextual` and `once`. Elsewhere it remains an ordinary
identifier:

```zax
replacement := makeValue()
value.replacement()

Example :: type {
  replacement : String
}
```

The replacement constructor:

- uses the destination's existing allocation;
- receives `_` with the old representation and resources;
- has transitional mutable and writable construction authority without
  `unsafe pliable`;
- may retain, move, copy, or otherwise recycle old resources while renewing and
  establishing every member resident instance;
- selects its declared right-hand operand through ordinary parameter and
  overload rules;
- may return additional declared results; and
- must establish a complete valid instance before returning normally.

An untouched resource may remain in the same field and allocation, but complete
replacement still renews that member resident instance. Reusing an address or
resource does not preserve the old member lifetime. Reference consequences are
defined by
[lifetimes and references](lifetimes-and-references.md#direct-member-references-cross-a-renewal-boundary).

This document owns why reconstructive replacement is required by the qualifier
model and its qualification boundary. Complete fallback, member transition,
resource retention, result, destruction, and alias behavior is defined by
[Zax construction, replacement, and destruction](construction-and-destruction.md#reconstructive-replacement).
Complete `move`/`copy`/`last` per-slot preference, callbacks, reentrancy, async,
and concurrency remain future focused work.

Panic does not produce a recoverable partial-replacement path. A matching helper
repairs the blocked condition and lets the same operation complete; otherwise
the process crashes gracefully without unwinding. See
[Zax safety and analysis](safety-and-analysis.md#panic-boundary).

## Type-family boundary

This qualifier model does not decide whether one named type may provide distinct
mutable and immutable implementations. Such variants could have different
layouts, stored state, operations, and construction strategies.

If a future type-family design adopts that capability:

- readonly access retains the selected variant's underlying mutability;
- a mutable readonly variant remains distinct from an immutable readonly
  variant;
- differently represented variants cannot be ordinary requalified views; and
- conversion requires construction, copying, or a consuming transformation that
  establishes the destination representation and guarantees.

Exact family identity, shared APIs, defaults, self-reference inside family
variants, conversion, reflection, structural compatibility, and syntax remain
future type-definition and structural-typing work.

## `unsafe pliable`

`unsafe pliable` creates an explicit access path that locally ignores final,
readonly, and immutable restrictions while retaining the underlying
qualifications.

```zax
source final : LegacyValue immutable = makeLegacyValue()
escape final unsafe pliable :
  LegacyValue immutable readonly final & = source
```

`unsafe pliable` also bypasses the declaring binding's own final stance for
operations through that path. The final, readonly, and immutable qualifications
remain recorded, but none is exempt from the explicit local bypass.

This escape supports narrow cases such as mutex use, hidden caches, lazy
bookkeeping, and legacy integration. It must be used with extreme caution:
other aliases may continue relying on the retained qualifications.

### `unpliable`

`unpliable` is ordinary behavior. An unpliable path respects all retained
qualifications. The word may be written explicitly but is normally omitted.

`unsafe pliable` cannot be selected through an omission default or a default
directive. Its purpose requires a local `unsafe` marker. Because `unpliable` is
the only ordinary behavior, no directive is needed to select it.

### Difference from an unsafe cast

An unsafe cast produces a differently qualified view. `unsafe pliable` retains
the original qualifications and ignores them locally:

```zax
source final : Payload readonly = makePayload()
escape unsafe pliable : Payload readonly final & = source

explicitView unpliable : Payload readonly final & = escape
ordinaryView : Payload readonly final & = escape
```

Both new views respect the retained readonly qualification.

### Non-propagation

Pliability is removed at every new boundary unless explicitly reintroduced:

- assigning or aliasing from an `unsafe pliable` source does not make the
  destination `unsafe pliable`;
- dereferencing does not carry pliability into another pointer or reference
  level;
- an operator may use an `unsafe pliable` operand's bypass for the current
  operation, but its result receives only the selected candidate's declared
  qualifications;
- function results cannot carry pliability as a returned property; and
- captures preserve the source qualifications but do not inherit source
  pliability.

An explicit capture may create a new `unsafe pliable` capture binding. The
capture clause is then the visible unsafe introduction site. Exact capture
syntax remains function design.

### Local aliases and members

A local alias can minimize the unsafe region:

```zax
useValue final : ()(input : LegacyValue immutable) = {
  local unsafe pliable : LegacyValue immutable & = input
  local.callMutableFunction()
}
```

A member may advertise an interior-mutation escape:

```zax
CacheOwner :: type {
  cache unsafe pliable : LegacyCache
}
```

`mutable` on a member describes that member value's mutability. It does not
authorize mutation through a readonly or immutable container path. Such a bypass
must be written `unsafe pliable`.

### Parameters

A parameter may introduce a local bypass:

```zax
useValue final : ()(
  input unsafe pliable : LegacyValue immutable
) = {
  input.callMutableFunction()
}
```

Candidate selection uses the parameter's declared `LegacyValue immutable`
requirement. After binding, the parameter path gains the explicit unsafe bypass.
The caller need not pass an `unsafe pliable` argument, and the bypass does not
cascade through every source-level caller.

A function may instead accept writable access, with its caller creating a
localized `unsafe pliable` alias before the call. A local alias minimizes the
unsafe region; an `unsafe pliable` parameter advertises a function-wide argument
escape at the declaration boundary.

Compiler and tooling analysis must preserve the possibility of mutation through
transitive aliases. It may track an internal effect without requiring every
source-level caller to repeat `unsafe pliable`.

### Safety and optimization

`unsafe pliable` has defined mechanical semantics: the compiler must preserve
the requested access and mutation. The operation is not itself undefined
behavior.

The programmer assumes responsibility for consequences. Broken invariants, data
races, invalid states, lifetime violations, or other misuse may produce
undefined behavior. Debug tooling may insert checks and panic when it detects a
problem, but such instrumentation is not a language guarantee.

An `unsafe pliable` escape is an optimization invalidation boundary. A compiler
may rely on immutable stability only where it proves that no relevant unsafe
escape may mutate the storage. Ordinary aliases remain typed immutable, but
their observed values cannot be assumed unchanged across a potentially aliasing
unsafe mutation.

Language documentation owns this programmer-visible requirement, not any
particular implementation or backend mapping.

## Parameters, results, and captures

Parameters resolve all three axes at the layers expressed by their prototypes.
Ordinary call matching may preserve or strengthen promises but may not silently
weaken an argument's qualifications.

Function results are governed by the resolved prototype:

- a returned pointer or reference carries the prototype's per-level
  qualifications;
- a returned by-value value carries its declared value mutability; and
- a completed result carries its declared transfer stance; and
- the caller's destination introduces an independently resolved place.

An inferred or generic result ultimately resolves to such a prototype. Complete
generic result resolution remains later function design.

Captures preserve or strengthen the captured source qualifications and never
inherit pliability implicitly. A reference capture behaves like an alias and
does not silently inherit a destructive `move` or `last` declaration stance.
The source stance used to construct a capture and the completed capture
declaration's ordinary future stance are independent. Complete examples belong
to
[Zax lambdas and callable composition](lambdas-and-callable-composition.md#source-stance-and-capture-stance-are-separate).

Declaration-side replacement permission survives parameter binding, result
routing, and capture. A declaration-final argument cannot regain replacement
authority merely because the parameter's referent type use remains varying:

```zax
replaceThrough final : ()(target varying : MyType immutable writable varying &)

restricted final : MyType immutable writable varying & = value
replaceThrough(restricted) // error: restricted may not delegate replacement authority
```

The invocation owner applies these qualification truths to parameter binding,
result routing, expected-result matching, compatible prototype adaptation, and
partial-order candidate preference. See
[Zax function invocation](function-invocation.md#declaration-side-replacement-permission).

`OpaqueObserver` and `OpaqueReferenceObserver` erase target identity without
erasing authority. Safe typed recovery may preserve or reduce qualifications
but cannot regain writable access, mutability, or replacement permission lost
before erasure. See
[Zax pointers, allocation, and arenas](pointers-and-arenas.md#type-erased-ownership-and-observation).

Changing the labels, defaults, or result-acknowledgement policy of a compatible
visible prototype does not permit its ordered implementation slots to gain
authority or require qualification-changing executable adaptation.

## Receiver operands

The [receiver operand](terms.md#receiver-operand) is the implicit operand
representing the instance on which a type-defined function or operator acts.
All three qualifier axes may constrain it and participate in candidate
selection. Receiver transfer stance is an independent callable slot whose
complete behavior is defined by
[Zax transfer stances](transfer-stances.md#receiver-stance).

A receiverless type call to a `once bound` declaration uses the same declared
receiver qualifications; it does not introduce another qualifier system. `_`
identifies compiler-prepared, trapping, or custom Nothing backing according to
the containing type's policy. Writable access to custom backing follows the
ordinary declaration, while writing shared compiler-provided backing remains
invalid and may be unchecked. Complete behavior belongs to
[Zax Nothing instances](nothing-instances.md#receiverless-and-instance-calls).

No operator, including `=` or `+=`, receives conventional qualifier behavior
merely because of traditional meaning. A domain-specific `=` may accept a final,
readonly, or immutable receiver when its declared behavior is compatible, but
it gains no lifecycle or replacement authority. Complete replacement is the
separate protected `.=` operation.

```zax
MyType :: type {
  x : Integer

  operator binary '=' final :
    (result : Boolean)(rhs : String) writable final = {
    _.x = 42
    return true
  }
}
```

`_` has the declared receiver-operand qualifications like any other qualified
place. `final` does not prohibit an operator named `=`. An operation involving
`_` selects only candidates compatible with its final stance. Likewise,
readonly prevents selection only of a candidate requiring writable access
through that operand; it does not prohibit arbitrary effects elsewhere.

An exact `move` or `last` receiver normally requires mutable value state through
writable access when it may extract represented resources. The selected
implementation receives that authority, but ordinary nested uses of `_` and its
members return to `copy` stance unless explicitly renewed. Qualifier authority
never increases merely because a transfer stance was accepted.

Complete `.=` reconstruction requires type-side varying, declaration-side
varying, and writable. It may replace a mutable or immutable resident. It is
unavailable through a readonly receiver or declaration-side final path, and a
type-side final place cannot receive a successor lifetime.

A temporary supplies the qualifications of its resolved result and
compiler-managed temporary place. An operation through a pointer or reference
uses the dereferenced instance's qualifications, not the pointer binding's place
qualification.

A directly constructed compiler-managed unnamed by-value temporary also offers
`last` transfer stance because its lifetime structurally ends after its one
complete consumer. This does not alter its mutable/immutable, final/varying, or
writable/readonly qualifications and does not make a pointee terminal.

Omitted receiver-operand qualifiers use the defaults applicable where the
operation is defined. Complete generated operator families, temporary destruction
beyond synchronous call completion, and default-source precedence remain future
work.

Ordinary member-call evaluation, fixed-arity viability, and ambiguity are
defined by [Zax function invocation](function-invocation.md). Operator-specific
lookup and selection are defined by [Zax operators](operators.md).

### Mixfix receiver anchors

A type-defined mixfix belongs to one receiver anchor. That receiver and every
operand hole carry ordinary place, value, access, and indirection
qualifications:

```zax
// Illustrative mixfix declaration syntax.
operator mixfix
  index 1
  binary '='
final : (
  result : MyResult
)(
  indexValue : MyIndex,
  rhs : MyValue
) writable = {
}
```

A writable declaration is nonviable through readonly access. Mutation still
requires mutable value state plus writable access. A final place may permit
content mutation but not ordinary place replacement.

Mixfix punctuation grants no authority. A user mixfix that consumes `=` does not
acquire the compiler-owned reconstructive-replacement lifecycle skeleton.
Protected `.=` is not a user-consumable mixfix component. Complete tree matching
and protected barriers are defined by
[mixfix operators](mixfix-operators.md).

## Optional qualification layers

`?` forms an outward qualification layer:

```zax
T q0 ? q1 * q2
```

This reads as a `q2` pointer to a `q1` optional wrapper containing a `q0` boxed
`T`. Qualifiers before `?` describe the boxed layer; qualifiers after `?`
describe the wrapper layer:

```zax
innerReadonly : Payload readonly?
wrapperReadonly : Payload? readonly
```

`innerReadonly` may change wrapper state through otherwise mutable/writable
wrapper defaults, but postfix access produces readonly `Payload` access.
`wrapperReadonly` cannot change state through this wrapper path, but the outer
readonly qualifier does not rewrite the boxed qualifications.

Postfix optional access crosses the wrapper boundary. Once presence is proven,
the result carries the boxed qualifications rather than the wrapper
qualifications:

```zax
if ?innerReadonly
  inspect(innerReadonly.) // readonly Payload access
```

### Wrapper axes

The three qualifier axes remain independent:

| Axis | Optional-wrapper consequence |
| --- | --- |
| `mutable` / `immutable` | Whether presence may change during this wrapper lifetime |
| `writable` / `readonly` | Whether this path may perform an otherwise available wrapper change |
| Type-side `varying` / `final` | Whether the place may receive another complete optional-wrapper lifetime |

Declaration-side `varying`/`final` separately says whether this declaration may
exercise complete-wrapper replacement.

A final mutable wrapper may reset and construct successive boxed lifetimes while
retaining one wrapper lifetime:

```zax
optional final : Payload? mutable final = value

reset optional
reset optional
optional .= [{}]
optional .= anotherOptional // error: complete wrapper place is final
```

An immutable varying wrapper cannot change presence within its current lifetime,
but an authorized writable varying path may replace that complete wrapper:

```zax
optional varying :
  Payload? immutable writable varying = first

reset optional    // error: current wrapper is immutable
optional .= [{}]  // error: current wrapper is immutable
optional .= second // reconstruct the complete wrapper lifetime
```

Boxed `readonly`, `immutable`, or `final` does not prevent the mutable/writable
wrapper owner from ending that conditional lifetime:

```zax
optional : Payload immutable readonly final? mutable writable

reset optional
optional .= [{}]
```

Those operations remove and create boxed places. They do not mutate or
independently replace the old payload through postfix access.

Wrapper-owned `.=` does not select `+++ replacement` at a final wrapper or
payload layer. It keeps the mutable wrapper lifetime and performs fresh payload
construction. To invoke the active payload's complete replacement, postfix
access must reach a payload place that is itself varying and writable:

```zax
wrapper final :
  Payload immutable writable varying? mutable writable final

wrapper .= replacement
// Legal wrapper-owned fresh payload construction; wrapper remains final.

if ?wrapper
  wrapper. .= replacement
  // Legal complete Payload reconstruction; the payload place is varying.
```

A readonly or final payload path rejects the second form. A wrapper that is
immutable rejects the first form because contained selection would mutate that
wrapper lifetime.

Complete optional operations, nested optional layers, and transfer effects are
defined by [Zax optional values](optional-values.md).

## Variant and union qualification

A variant wrapper has the same capability-versus-permission distinction for its
selection state:

- mutable + writable wrapper access may reset, use contained `.=` or perform
  ordinary same-type `=` while retaining one wrapper lifetime;
- an immutable wrapper keeps absence or the selected name stable during that
  lifetime;
- complete-wrapper `.=` additionally requires a type-side varying place,
  declaration-side varying permission, and writable access; and
- active payload access carries the alternative type's independently resolved
  qualifications.

```zax
choice final : MyChoice mutable final

choice.text .= "ready" // legal: mutate selection within this wrapper
choice = otherChoice   // legal: assign state within this wrapper lifetime
choice .= otherChoice  // error: complete wrapper place is final
```

Ending an immutable or readonly payload through authorized wrapper mutation is
not mutation through payload access; it ends the conditional path. Complete
behavior belongs to [Zax variants](variants.md#wrapper-and-payload-qualifications-are-separate).

A union lens instead derives qualifications from the path to the complete
union. It cannot increase mutability, writability, or replacement authority:

```zax
reader : MyBits readonly & = bits
reader.raw = 1 // error: readonly union path
```

Lenses are alternate views of one backing value, not independently qualified
member lifetimes. Complete admissibility and lens behavior belongs to
[Zax unions](unions.md#lens-access-qualification-and-references).

## Array and slice qualification

An immutable array may still occupy a writable varying place:

```zax
myArray varying :
  Integer[4 in 4..10] immutable writable varying = [ 1, 2, 3, 4 ]

myArray[0] = 10   // error: mutates the immutable array
myArray.resize(6) // error: mutates the immutable array
myArray .= replacement
// valid reconstructive replacement when the contracts match
```

`immutable` prevents changing elements, length, capacity, suggestion, or
storage during the current array lifetime. Type-side `varying`,
declaration-side `varying`, and `writable` together still permit ending that
lifetime and constructing a complete successor array in the same place.

A slice has two separately controlled things:

1. the elements reached through it; and
2. its own window description, such as start and length.

Source keeps those qualifier layers visible:

```text
T <element/type qualifiers> [] <slice qualifiers>
```

Words before `[]` govern element access. Words after `[]` govern the slice
window itself. A readonly window cannot be narrowed through that path, but an
independently writable element path may still change an element.

An owning array directly contains its elements, so making the array immutable
also constrains contained element state. A slice only borrows elements; its
window qualification does not replace their qualifications.

Creating or copying a slice can keep or reduce existing authority. It cannot
turn readonly elements writable, make immutable state mutable, permit
replacement of a final place, or extend an element's lifetime. Complete syntax,
window narrowing, storage-profile properties, and element access are explained
by
[Zax arrays and slices](arrays-and-slices.md#slice-qualification-and-narrowing).

## Indirection

Dereferencing does not change the qualifications of the dereferenced instance.
Converting between pointer and reference forms preserves pointee-level
qualifications.

An ordinary alias to the same referent preserves that place's type-side
final/varying stance, though it may narrow its own declaration-side replacement
permission. Changing indirection form cannot present a varying referent as final
or a final referent as varying.

The declaration-side permission of the pointer or reference binding constrains
replacement through that declaration. Replacing a pointer's pointer value is
separate from replacing its pointee. A reference never rebinds; assignment
through it acts on the referenced place.

Changing indirection form may not silently strip referent qualifications or
increase ordinary access authority. Complete reference lifetime is defined by
[lifetimes and references](lifetimes-and-references.md), and pointer ownership by
[pointers and arenas](pointers-and-arenas.md).

Pointer-layer ownership and accounting words qualify `*` rather than the
pointee:

```zax
sole : MyValue * unique
shared : MyValue * strong atomic
member : Member * weak
```

`unique`, `shareable`, `strong`, `weak`, and pointer-layer `atomic`
do not rewrite `MyValue` or `Member` qualifications. Their complete ownership
and ordering grammar is defined by the pointer owner.

## Members and nested access

A readonly path to a container ordinarily produces readonly paths to projected
members:

```zax
source final : Container
view : Container readonly final & = source

view.member = 5 // error
source.member = 5 // legal through the original writable path
```

A member requiring mutation through readonly or immutable access must introduce
explicit `unsafe pliable`.

Postfix optional access is not ordinary member projection. It crosses the
semantic `?` indirection boundary and therefore exposes the independently
qualified boxed layer as described above.

## Transfer and destruction

A final live place cannot be ordinarily replaced, but final does not freeze the
resources contained by its current mutable value. A selected `move` or `last`
consumer may transfer resources from a final place when:

- the current value is mutable;
- the access path is writable; and
- the exact consumer accepts that stance.

`move` leaves a live moved-from source. `last` leaves a live destruction-valid
terminal source. Neither stance itself ends the outer lifetime or replaces its
place.

Readonly or immutable source does not acquire ordinary resource-extraction
authority merely because `move` or `last` is offered. Unusual readonly contracts
remain possible only when their actual behavior respects those qualifications.

During destruction, the current instance receives terminal mutable and writable
authority without requiring `unsafe pliable`. Destruction may dismantle the
value and extract resources because its lifetime is ending. That authority
cannot create an alias usable after the instance's lifetime.

Destructor sequencing is defined by
[Zax construction, replacement, and destruction](construction-and-destruction.md#destruction).
Moved-from and terminal source meaning is defined by
[Zax transfer stances](transfer-stances.md#move).
Complete pointer ownership is defined by
[pointers and arenas](pointers-and-arenas.md). Reference lifetime and explicit
terminal forwarding are defined by
[lifetimes and references](lifetimes-and-references.md#move-last-and-reference-lifetime).

## Concurrency boundary

Immutable qualification addresses value change, not lifetime, shared backing
storage, synchronization, safe transfer, allocation, or reference-count safety.
It may make sharing easier but is not a complete thread-safety guarantee.

Lifetime management must independently ensure that an instance outlives every
reference to it. Mutable shared state requires synchronization. An
`unsafe pliable` path can additionally invalidate assumptions made by other
aliases and requires particular caution across threads.

## Structural compatibility

Qualifier-preserving conversion and structural identity are different
questions. Every direct or flattened leaf participating in safe structural
compatibility preserves its complete type-side qualifications. Ordinary
conversion may narrow path authority but never present immutable state as
mutable, readonly access as writable, or a final place as varying.

An anchored compatible region is a final subplace of the current containing
instance. It does not inherit the outer declaration's complete replacement
authority. A whole-root compatible reference may retain varying authority only
when complete lifetime partitions, qualifications, tracking, construction,
replacement, and destruction obligations match.

Safe and unsafe coercion never grant access authority. They may preserve
existing mutable, writable, or varying authority when the structural and
lifetime conditions permit it, but immutable, readonly, or final input cannot
become stronger.

Safe writable coercion proves that every destination write preserves a valid
source representation. `unsafe as coercive` is required when a permitted write
may invalidate the original scalar; the programmer must restore that invariant
before using the original type again. The `unsafe` marker accepts representation
risk rather than qualification weakening.

View-shaped `unsafe cast` and `unsafe pliable` remain the separate local
mechanisms that can bypass qualification promises under their stated unsafe
responsibility. Complete posture, anchor, shape/layout, coercion, and raw-cast
behavior is defined by
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md#coercive-conversion-and-raw-casting).

## Diagnostics and formatting

Diagnostics should:

- identify the entity and indirection level carrying each qualification;
- distinguish underlying capability from permission through the current
  declaration or path;
- distinguish unavailable authority from a conflicting qualification;
- reject duplicate qualifier tokens at one syntactic point;
- reject a declaration-side stance that would exceed the underlying capability;
- report contradictions where defaults become concrete;
- explain required and available receiver-operand qualifications when no
  candidate is viable; and
- distinguish an unsafe bypass from ordinary promise strengthening;
- show the qualification layer that makes an invocation or result transfer
  unavailable; and
- distinguish an incomparable transfer from a strictly worse match.

Canonical ordering is a formatting rule, not semantic precedence. Formatters
preserve explicit qualification. Linters may identify compatible restatement or
explicit defaults as redundant.

## Costs and risks

The qualifier model makes useful promises visible, but those promises have
costs:

- additional indirection levels require additional qualification decisions;
- deep immutable construction may require copying, exclusive authority, or
  specialized construction;
- readonly aliases do not prevent mutation through other paths;
- explicitly varying references may observe successive value lifetimes in one
  place;
- complete `.=` reconstruction introduces constructor, lifetime, and alias
  constraints for mutable or immutable values;
- `unsafe pliable` can invalidate invariants and optimization assumptions relied
  upon elsewhere; and
- qualifier-sensitive overload selection can make candidate sets more complex.

These costs are reasons to keep defaults sensible and unsafe escape paths local,
not reasons to combine the three axes.

## Boundaries and maturity

This document is accepted conceptual design, not a formal grammar,
implementation mapping, compatibility contract, or conformance specification.

Later work may refine syntax and adjacent mechanisms while preserving:

- the independence of place, value, and access qualification;
- the separation of type-side capability from declaration-side permission;
- per-layer attachment through indirection;
- defaults filling only unresolved axes;
- ordinary conversions never increasing authority;
- same-place aliases preserving the referent's actual type-side final/varying
  stance while being free to narrow their own replacement permission;
- mutation requiring mutable + writable;
- complete `.=` reconstruction requiring type-side varying +
  declaration-side varying + writable;
- deep immutability across direct structural containment while optional,
  pointer, and reference indirection layers retain independently written
  qualifications;
- optional wrapper mutability governing presence, independently from boxed
  qualification and complete-wrapper place replacement;
- explicit, non-propagating unsafe pliability;
- qualifier-aware receiver-operand selection;
- function invocation applying qualification truth without silently increasing
  authority; and
- the separation of mutability from lifetime and thread safety.

Pointer repointing remains distinct from pointee replacement. References never
rebind.
