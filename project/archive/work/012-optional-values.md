# 012: Optional values, construction, absence, and transfer

| Field | Value |
| --- | --- |
| Status | Historical / non-normative / audit-only |
| Work Item | `012` |
| Created | 2026-09-01 |
| Completed | 2026-09-02 |
| Owns | The bounded review defined by the fixed initiating input |
| Does Not Own | General result/error types, pointer validity, pattern matching, async cancellation, or formal layout/ABI rules |

## Non-authority notice

This completed working record is retained for targeted audits and provenance.
Its findings have been dispositioned into current language owners or indexed raw
future-work inputs. It is not part of ordinary onboarding and is not a current
source of language meaning.

## Fixed initiating input

This section records the information aligned when work item `012` was created.
It is intentionally incomplete and must not be rewritten as work develops.

### Initiating concern

Develop a coherent human-developer-facing model for optional values,
construction, absence, and transfer:

1. what `T?` stores and means when absent or present;
2. default absent initialization;
3. construction of one present contained `T`;
4. forwarding an initializer into contained construction;
5. why failed contained construction is an error rather than absence;
6. the difference between optional construction and optional conversion;
7. copy, move, consuming/`last`, and source-state behavior;
8. contained lifetime start, destruction, replacement, and reset;
9. presence testing and proven dereference;
10. conditional convergence involving optional values;
11. nested optionals and whether `T??` remains meaningfully distinct;
12. optional parameters and results where they materially affect transfer;
13. costs, layout pressure, diagnostics, and source stability; and
14. lasting documentation owners and boundaries.

Recover Zax-specific intent before adopting another language's optional rules.

### Motivating pressure

Current conceptual design already relies on ordinary optional source:

```zax
myValue : FooBar
myOptional := (: FooBar? = myValue)

if ?myOptional
  use(myOptional.)
```

Programmers need to predict when an optional is absent, when a contained value
is constructed, what happens to the source, and when dereference is valid.

Optional construction and optional conversion must remain distinct:

```zax
myWide : I16 = 355
myConverted := myWide as U8? // absent

myConstruction := (: U8? = 355)
// error: constructing the contained U8 fails; this does not mean absence
```

### Known assumptions

- `T?` is one concrete static type distinct from `T`.
- An optional contains either no live `T` or one live `T`.
- Type-default optional initialization is absent.
- Constructing a present optional must successfully establish a valid contained
  `T`.
- Failed contained construction does not mean absence.
- Optional conversion is an explicitly selected operation that may return
  absence.
- Presence testing produces exactly `Boolean`.
- Dereference requires proof that the contained lifetime is active.
- Conditional paths converge to one concrete optional/result shape.
- Optional construction does not introduce implicit narrowing or a conversion
  chain.
- There is no language implementation in this repository.

### Known inclusions

- Absent and present mental model.
- Default initialization.
- Present-value construction and forwarded initialization.
- Copy, move, consuming/`last`, and source state.
- Contained lifetime, destruction, replacement, reset, and failure cleanup.
- Presence testing, dereference, and access proof.
- Conditional convergence.
- Optional parameters and results at the depth required by transfer.
- Optional conversion and validating admission as distinct producers.
- Nested optional behavior.
- Costs, layout pressure, diagnostics, formatting, and source stability.
- Documentation fit and lasting ownership.

### Known starting boundaries

- General result and error types.
- Exception handling or propagation.
- General algebraic sum and variant types.
- Pointer validity and complete `Nothing` semantics.
- Complete pattern matching.
- Async suspension and cancellation.
- General lifetime strategies beyond immediate optional consequences.
- Formal layout, ABI, grammar, compiler implementation, or lowering.

These boundaries do not erase consequences. Record each material pressure in a
live owner or indexed raw destination.

### Intentionally unresolved framing

- Exact generated constructor set for `T?`.
- Whether present construction forwards arbitrary initializer packets or only
  selected source shapes.
- Default copy versus explicit move/`last`.
- Source state after successful or failed transfer.
- In-place construction and temporary-elision guarantees.
- Reset and replacement spelling.
- Destruction ordering and failure cleanup.
- Nested optional behavior.
- Optional references, pointers, functions, and zero-storage values.
- Layout, discriminant, niche optimization, and ABI pressure.
- Whether optional operations are protected, generated, or declared by a
  generic mechanism.
- Exact diagnostics and formatting.

### Initial stopping guidance

Stop when the work has:

- established the ordinary absent/present mental model;
- established present construction and failure;
- separated construction, optional conversion, and default absence;
- established copy/move/`last` and source-state behavior at programmer depth;
- established contained lifetime, destruction, reset, and proven access;
- reconciled conditional convergence, parameters, results, and identities;
- preserved layout/ABI/implementation pressure without designing machinery;
- assigned every deferred consequence a live destination;
- identified exact current owners and promotion changes; and
- completed the required documentation-fit dry run.

Do not promote findings, archive this work item, or begin work item `013`
without the separately required discussion, alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../../documentation.md) - governs focused reading,
  numbered work, reader-first promotion, disposition, and dry runs.
- Retired root `optional.md` input - supplied the detailed candidate
  construction, reset, presence, dereference, transfer, and lifetime behavior
  consumed during this work.
- Focused [construction and destruction](../../../language/construction-and-destruction.md#optional-construction-must-establish-the-contained-value) -
  provides present-construction failure and contained-lifetime boundaries.
- Focused [conditionally live storage and access proof](../../../language/construction-and-destruction.md#access-proof) -
  provides the current dereference proof obligation.
- Focused [optional presence operation](../../../language/operators.md#optional-presence-operation) -
  provides protected presence behavior.
- Focused [conditional expression and branch convergence](../../../language/core-flow-control.md#conditional-expression-and-branch-convergence) -
  provides the current optional convergence boundary.
- Focused [optional construction from numeric source](../../../language/integer-literals.md#optional-construction-still-has-to-build-a-value) -
  provides the distinction between contained construction failure and absence.
- Focused [exact intrinsic optional conversion](../../../language/integer-operator-catalog.md#exact-intrinsic-conversion) -
  provides `as Destination?` as an explicit optional-value producer.
- Focused [identity admission](../../../language/identity-types.md#admission) -
  provides validating `optional from` as a distinct producer.

### Consequence-driven

- Read focused function invocation and qualifier material when a concrete
  parameter, result, copy, move, `last`, or access-capability question requires
  it.
- Read [raw lifetime input](../../raw/lifetimes.md) only when current contained
  lifetime rules cannot resolve an immediate optional case.
- Read pointer or `Nothing` material only when optional behavior would otherwise
  imply equivalence.
- Read [raw selection input](../../raw/selection.md) only when optional matching
  creates a concrete requirement.
- Read raw analysis or safety input only when a proof, failure, or unsafe
  boundary cannot be described through current owners.

### Audit-only

- `project/archive/`, including work items `001` through `011`.
- Superseded implementation sketches and compiler-internal explanations.

Do not read archived work item `011` during ordinary work on `012`. Its current
findings are promoted into language owners and its future pressures are
preserved in live raw or legacy inputs.

## Working record

This working record now contains findings aligned in discussion with the
language maintainer. They remain non-authoritative until a separately authorized
promotion incorporates them into their lasting owners.

### Review entry point

#### Aligned programmer model

An optional value is a live wrapper with one of two semantic states:

- **absent:** no boxed `T` lifetime exists; or
- **present:** exactly one boxed `T` lifetime exists.

Absence is a valid optional state, not "uninitialized memory." The representation
may use a tag, a niche, or another mechanism, but no live `T` exists to access or
destroy while the wrapper is absent.

```zax
empty : MyType?
present : MyType? = myValue
explicitPresent : MyType? = [{}]

if ?present
  use(present.)
```

Type-default construction of `MyType?` produces absence without constructing
`MyType`. Present construction succeeds only after it establishes one complete
valid `MyType`. `?present` returns exactly `Boolean`; `present.` crosses the
optional boundary and produces access under the boxed value's own qualifications
only where that lifetime is proven active.

The wrapper and boxed value have independent qualifications and lifetimes.
Qualification does not propagate across the `?` indirection boundary, but
lifetime ownership does: wrapper operations may end the boxed lifetime and
invalidate every access tied to it.

#### Core operation family

These three operations form the central lifecycle and transfer model:

| Completed operation | Boxed source lifetime afterward | Source wrapper state afterward | Destination/result |
| --- | --- | --- | --- |
| `reset value` | Ended when present | Absent | Reference to the same wrapper |
| A consumer accepts `last value` | Ended when a present payload is terminally transferred | Absent after a consuming transfer | Receives the optional state under compatible `last` semantics |
| A consumer accepts `move value` | Remains live in its defined moved-from state when present | Preserves source presence | Receives the optional state under compatible `move` semantics |

`reset` is an immediate protected optional operation. `last value` and
`move value` are transfer-qualified source expressions of the same optional type;
they do not copy, move, destroy, unwrap, or change presence until a selected
consumer accepts the stance.

```zax
source : MyType?
anotherSource : MyType?

// ...

destination : MyType? = last source
anotherDestination : MyType? = move anotherSource
```

The typed destinations above consume the qualified expressions. Direct extraction
of `T` from `T?` would require a separately available operation; expected result
context must not invent unwrapping. Whether `:=` infers the same value shape is a
separate general inference question recorded below.

#### Construction, replacement, and assignment at a glance

```zax
optional : MyType?
value : MyType
otherOptional : MyType?

optional = [{ value }] // construct a fresh boxed MyType
optional = value       // error: value-shaped wrapper assignment is an intent error

if ?optional
  optional. = value    // operate on the proven-live boxed MyType

optional = otherOptional // replace the complete MyType? wrapper lifetime
reset optional         // retain the wrapper lifetime and become absent
```

The construction packet is the explicit acknowledgement that an existing
wrapper will end any old boxed lifetime and construct a fresh payload. Direct
same-type optional `=` instead replaces the complete wrapper lifetime and
therefore requires a varying wrapper place, declaration-side replacement
permission, and writable access. Assignment through `optional.` selects an
operation on `MyType` and does not change presence.

No unresolved contradiction remains in the optional model. Exact general
`copy`/`move`/`last` candidate preference, transfer-view syntax, inference,
pointer/reference mechanics, and representation remain explicitly assigned
future pressures rather than hidden optional decisions.

### Recovered evidence and legacy disposition

The aligned model preserves these current constraints:

- `T?` is one concrete static type distinct from `T`.
- Type-default construction of `T?` is absent.
- Present construction failure is an error rather than absence.
- Optional conversion and validating optional admission are explicit
  absence-producing operations distinct from construction.
- Presence testing is protected and returns exactly `Boolean`.
- Dereference requires proof of a live boxed value.
- Conditional paths and function results converge to one concrete type and
  establish their destinations directly.
- Argument evaluation, binding, and any selected transfer effects preserve
  caller source order.

The legacy optional page remains useful evidence but these parts must not survive
unchanged:

- "Contains uninitialized memory" confuses a semantic state with representation.
- `foo = value` hides construction/destruction intent and is rejected for a
  `T` source.
- `= :`, `= #`, and `= {}` compete as reset spellings; aligned source uses
  `reset value`.
- `[{}]` is the canonical empty construction packet; `[{ }]` is an intent error.
- Claims that construction "will optimize" to a particular implementation
  overstate the contract. The semantic promise is direct destination
  establishment without a required observable default-then-assign sequence.
- Presence proof does not permit implicit member access; source still uses
  postfix `.` to cross the optional boundary.
- Passing an optional does not by itself decide `copy`, `move`, `last`,
  reference, or other transfer behavior.

### Wrapper state, boxed lifetime, and construction

The optional wrapper has an ordinary value lifetime. Its state controls whether
one nested boxed lifetime is active:

| Wrapper state | Live boxed values | Dereference | Boxed destruction at wrapper exit |
| --- | ---: | --- | --- |
| Absent | 0 | Invalid | None |
| Present | 1 | Requires proof | Destroy the one live `T` exactly once |

Presence becomes true only after construction of the complete boxed `T`
succeeds. A partially constructed `T` is never a present payload. If construction
panics, Zax performs its graceful-crash behavior; optional construction provides
no rollback or exception-recovery guarantee.

Ending a boxed lifetime and later constructing another creates two distinct
lifetimes even if both reuse the same address. An address does not preserve a
reference, pointer, proof, or value identity across that transition.

#### Empty and explicit construction packets

An ordinary declaration and an explicit empty packet select the same zero-input
construction when applied to a non-optional `T`:

```zax
ordinary : MyType
explicit : MyType = [{}]
```

For an optional they intentionally differ:

```zax
empty : MyType?                // absent
present : MyType? = [{}]       // present, zero-input-constructed MyType
presentFromValue : MyType? = myValue
presentFromPacket : MyType? = [{ myValue }]
```

`[{}]` is the one canonical zero-entry construction packet. `[{ }]` is rejected
because its internal spacing makes it resemble a packet containing an empty
braced scope rather than one contiguous zero-entry construction packet.
Packets support the ordinary positional, named, omitted, multiple-result, and
stored-member input forms accepted by construction. Optional construction does
not create a parallel parameter-mapping system.

#### Construction into an existing wrapper

A construction packet on an existing mutable wrapper always requests fresh
boxed construction:

```zax
optional = [{
  endpoint,
  mode: selectedMode
}]
```

It never changes to boxed assignment or boxed replacement because the old state
happens to be present:

1. evaluate and immediately bind packet inputs in source order;
2. verify that their lifetimes remain compatible with the transition;
3. destroy the old boxed value if present;
4. invoke the selected boxed constructor;
5. mark the wrapper present only after that constructor completes.

An implementation may establish the boxed value directly in optional storage.
This is a programmer-visible direct-construction and ordering rule, not a
specific lowering promise.

Evaluation before destruction permits an independent copied snapshot. It does
not make a reference into the old payload independent:

```zax
if ?optional
  optional = [{ optional. }]
  // error when the selected binding would read the reference after the old
  // boxed lifetime ends
```

The selected operation or lifetime policy must establish an independent value,
prove that the exact source lifetime remains valid through its final use, handle
the alias explicitly, or reject the operation. A future narrow unsafe assertion
may accept responsibility only for an unproved valid case, never a known-ended
lifetime.

#### Construction, conversion, and admission remain distinct

| Producer | Result | Rejection or failure |
| --- | --- | --- |
| Type-default `T?` construction | Absent `T?` | No boxed constructor runs |
| Present `T?` construction | Present containing one constructed `T` | Construction error or panic; never absence |
| Exact `source as T?` conversion | Present when the declared exact conversion succeeds | Absent |
| `Identity optional from source` | Present identity when validation admits | Absent |
| Copy construction of `T?` | Source-matching absent or present state | Copy failure; never silently changed to absence |

```zax
myWide : I16 = 355

myConverted := myWide as U8?          // absent
myConstructed := (: U8? = 355)        // error: contained U8 construction fails
```

Present construction does not fall through to optional conversion, validating
admission, narrowing, another width, or default absence after failure.

The existence of a nested optional type does not generate nested `as` or
admission operations. If a declared operator returns `U8? ?`, that exact operation
owns whether it produces outer absence, outer-present/inner-absent, or another
defined state. Inference merely receives its declared result:

```zax
nested := source as U8? ?
// Valid only if the selected source type actually declares this result shape.
```

Numeric availability and policy belong to future numeric conversion review;
optional design preserves the states without inventing the operator.

### Wrapper state operations and complete-wrapper replacement

#### Reset

`reset value` is the protected optional operation for ending the boxed lifetime
without ending the wrapper lifetime:

```zax
reset optional
reset optional // no-op: the same live wrapper is already absent
```

When present, reset destroys the boxed value exactly once and records the wrapper
absent. When absent, it performs no boxed work. It returns a reference to the
same wrapper, never to the old boxed value:

```zax
(reset optional) = [{ replacementInput }]
```

The returned path preserves the source wrapper's qualifications and cannot gain
replacement or access authority. Reset requires a mutable wrapper through a
writable path, but wrapper-place `final` does not prohibit it because the same
wrapper lifetime continues.

Direct boxed destruction is always rejected:

```zax
optional.---() // error
```

Postfix `.` grants access to the boxed value, not authority to update its
wrapper's presence state. Allowing a destructor through that path would leave
the wrapper able to destroy the same boxed lifetime again. Even an unsafe
assertion cannot repair that missing state transition; a narrow assertion may
instead acknowledge an unproved alias boundary around the legal owner-aware
`reset` operation.

#### Three distinct meanings around `=`

```zax
optional = [{ value }] // mutate this wrapper by constructing a fresh payload
optional = otherOptional // replace the complete optional wrapper lifetime
optional. = value      // operate on the already-live boxed value
```

Packet construction retains the wrapper lifetime, destroys any old payload, and
constructs a new one. Dereferenced assignment requires presence proof, selects
the boxed type's own assignment or replacement behavior, and leaves the wrapper
present.

Same-type optional `=` ends the complete destination wrapper lifetime and
constructs another in the same place. Its state matrix is:

| Old destination | Source optional | New wrapper |
| --- | --- | --- |
| Absent | Absent | Absent |
| Present | Absent | Destroy old payload and construct an absent wrapper |
| Absent | Present | Construct a present wrapper and payload |
| Present | Present | Destroy old payload and construct a present wrapper and fresh payload |

This operation requires type-side `varying`, declaration-side replacement
permission, writable access, and a compatible source transfer. A final wrapper
may still reset and reconstruct its payload when mutable, but cannot use
same-type `=`:

```zax
optional final : MyType? mutable final = value

reset optional            // legal
optional = [{}]           // legal
optional = otherOptional  // error: complete wrapper place is final
```

The rule makes these existing-wrapper sources intent errors:

```zax
optional = value
optional = (: MyType)
```

They are too easily confused with `optional. = value`, yet the plausible
meanings have materially different construction, destruction, replacement, and
presence effects. Use a construction packet to acknowledge fresh payload
construction. A declaration remains direct construction rather than assignment:

```zax
newOptional : MyType? = value
```

Exact self-assignment and self-aliasing remain general future pressure:

```zax
optional = optional
```

A blanket same-object diagnostic would be wrong because a custom operator phrase
may intentionally receive the same object twice. Protected optional replacement
must eventually define or select alias-safe behavior; lifecycle self-aliasing
belongs in raw lifetime work, while operation-aware suspicious-self diagnostics
belong in analysis/lint work.

### Transfer-qualified optional sources

`last source` and `move source` produce the same optional type under distinct
transfer stances. They do nothing until a constructor, assignment, parameter
binding, or other consumer accepts the expression:

```zax
source : MyType?
anotherSource : MyType?

// ...

copied : MyType? = source
terminallyTransferred : MyType? = last source
moved : MyType? = move anotherSource
```

For a same-type optional consumer:

| Accepted source stance | Destination | Source after the consumer |
| --- | --- | --- |
| Ordinary copy | Receives matching absent/present state and copies a present payload | Unchanged |
| `last` | Receives matching state using compatible terminal payload transfer | Wrapper becomes absent; transferred payload lifetime ends |
| `move` | Receives matching state using compatible nonterminal payload transfer | Absence stays absent; presence remains with a live moved-from payload |

The source stance does not automatically unwrap `T?` into `T`, and an inferred
destination does not invent that result type. A direct optional-to-`T` transfer
would require a separately available operation.

An unused transfer-qualified result is handled by the general required-result
rule rather than a special `last` diagnostic. Exact
`copy`/`move`/`last` candidate preference and whether a stance requires or merely
permits its specialized transfer remain future transfer work.

Future transfer-semantics and qualifier work owns the decision whether to accept
these candidate post-unary phrase forms as one mutually exclusive stance family:

```zax
source as copy
source as deep
source as move
source as last
```

They are distinct, mutually exclusive intent signals; combined forms such as
`as move copy` or `as last deep` do not exist. The design goal is for any
accepted language-defined forms to work unfenced. Future transfer and
source-structure review must verify that their keyword components can be
recognized unambiguously; if no coherent unfenced grammar exists, an exact
phrase fence may be required. A fence may select an existing exact phrase but
cannot invent one.

These views preserve source identity and add transfer stance rather than
performing a conversion. The optional-specific pre-unary `last source` and
`move source` forms are aligned here. General qualifier/transfer work decides
whether the post-unary family also exists for optionals and other types, and how
any accepted forms interact. Optional design requires the source-state effects
when a selected consumer actually copies, moves, or terminally transfers an
optional.

Parameter binding exposes the same unresolved general boundary. A by-value input
may consume during immediate binding, while a future terminal reference or
capability may bind first and permit a later body operation to consume. The
eventual design must preserve source order, make later-use restrictions visible,
and revisit these optional state effects if it changes the meaning of `move` or
`last`.

### Parameters and results

Optional parameters do not need a special invocation model. A by-value `T?`
parameter uses the selected ordinary `copy`, `move`, `last`, or other binding
operation. A reference parameter refers to the wrapper under its own
qualification and lifetime rules. Access through that reference still requires
proof before dereferencing its boxed value.

An optional result slot is established directly as absent or present:

```zax
findMyValue final : (
  result : MyType?
)() = {
  if found()
    return myValue

  return (: MyType?)
}
```

The present return directly constructs the declared result; the absent return
directly constructs its absent state. Neither path default-constructs a result
slot and assigns over it.

An absence-producing conversion remains explicit:

```zax
tryByte final : (
  result : U8?
)(
  source : I16
) = {
  return source as U8?
}
```

Returning `source` directly would request present construction of `U8?` and
would be an error when exact construction cannot be established; it would not
mean "return absent when out of range."

Optional depth uses the same intent gate in arguments and results. A parameter
or result expecting `T? ?` does not silently wrap a `T?` source:

```zax
consume((: MyType? ? = [{ innerOptional }]))
```

A bare packet has no destination and is not an expression value. Anonymous typed
construction supplies the required boundary.

#### Inferred declarations remain future general work

Examples using `:=` assume value construction rather than inferred aliasing:

```zax
source : Integer = 0

copied := source
explicitCopy : Integer = source

reference : & = source
moved := move source
```

The likely general direction is that `:=` infers a value result by default,
while `: & =` explicitly requests reference formation. A reference expression
may auto-follow for value construction, while a pointer remains a value whose
pointer type may be inferred. Exact type shape, qualification,
`copy`/`move`/`last` selection, and source-reference behavior are not accepted
here. They belong to declarations and bindings with invocation, qualifier, and
lifetime consequences. Optional examples must not become accidental authority
for that rule.

### Presence proof and invalidation

The established ordinary pattern remains:

```zax
if ?myOptional
  use(myOptional.)
```

Static analysis must establish that the exact boxed lifetime is present before
accepting postfix `.`. A preceding `?` check is the ordinary proof, but direct
construction, earlier control flow, or another recognized presence contract may
also prove it:

```zax
myOptional : MyType? = [{}]
use(myOptional.) // construction proves this boxed lifetime present
```

When an operation is valid but analysis cannot establish definitive proof, a
future narrow `unsafe<...>{...}` assertion is required. An assertion cannot
override known absence.

The proof is about the exact current boxed lifetime. Any reset, packet
construction, complete-wrapper replacement, terminal transfer, or other
operation ending that lifetime invalidates its proofs and references:

```zax
if ?myOptional {
  boxedReference : MyType & = myOptional.
  reset myOptional
  use(boxedReference) // error: the referenced boxed lifetime ended
}
```

Reset may follow the proven final use of a non-escaping reference. If analysis
cannot prove a valid alias boundary, a future narrow assertion may accept
responsibility. It cannot make a known-ended lifetime live:

```zax
if ?myOptional {
  oldValue : MyType & = myOptional.
  opaqueObserve(oldValue)

  unsafe<replacement-alias>{
    reset myOptional // asserts that opaqueObserve retained no alias
  }
}
```

An unproved access places the assertion around that access:

```zax
unsafe<lifetime-escape>{
  use(possiblyValidReference)
}
```

The syntax records the aligned contiguous, non-scoping enclosure shape and the
possibility of several exact analysis categories, with whitespace after each
comma. Category names and semantics remain future analysis-control work. A false
presence, alias, or lifetime assertion has undefined consequences and no
required runtime check; debug instrumentation may detect it and panic. A known
absence or unconditionally known-ended lifetime remains invalid.

Postfix dereference binds more tightly than pre-unary `?`, so
`?myOptional.` means `?(myOptional.)`; it is not a combined presence-and-access
operation. A user-defined Boolean-returning `?` cannot grant payload access.

Potential future conveniences such as presence-binding conditions, guarded
optional chaining, or pattern matching must preserve the same lifetime fact:
access is valid only where the selected language contract proves the exact
contained lifetime active.

Postfix `.` remains preferable to a named `unwrap` operation. Rust-style
`unwrap()` suggests a runtime-checked operation that may panic, while ordinary
Zax access is accepted only after static proof and produces a reference under
the boxed qualifications. Prefix nesting also becomes unreadable across pointer
and optional layers:

```zax
// Not the preferred model:
(unwrap ((unwrap pointer).content)).result

// Layered postfix access:
pointer.content.result
```

Parser and semantic tooling can identify optional and pointer dereference nodes
without relying on textual grep for a word. A future explicitly panicking
`expect`-like operation would be a separate contract, not a replacement for
proven postfix access.

### Conditional convergence

A typed optional destination can converge a boxed value and explicit absence:

```zax
myResult : MyType? =
  condition ?? myValue ;; (: MyType?)
```

The selected arm establishes the destination directly. Inference may likewise
converge `T` with an already concrete `T?` through the same first-layer direct
construction when no competing convergence is equally valid.

Adding a layer around an already-optional arm requires explicit typed packet
construction:

```zax
inner : MyType?

nestedResult : MyType? ? =
  condition ??
    (: MyType? ? = [{ inner }]) ;;
    (: MyType? ?)
```

These forms are rejected:

```zax
nestedResult : MyType? ? =
  condition ?? inner ;; (: MyType? ?)
// error: the true path requires acknowledged optional-depth construction

nestedResult : MyType? ? =
  condition ?? [{ inner }] ;; (: MyType? ?)
// error: a construction packet has no destination here
```

The explicit anonymous typed declaration is required anywhere an expression
hole must turn an already-optional source into one additional optional layer.
This includes conditional arms, arguments, return expressions, operator
operands, aliases of aliases, and generic substitutions.

No runtime binding may retain "either `T` or `T?`" as a branch-dependent type.
Every path must converge to one concrete static result before the conditional
completes.

### Nested optionals

Nested optionals are ordinary explicit wrapper composition:

```zax
single : MyType?
nested : MyType? ?
```

Adjacent `MyType??` is rejected because `??` is a different token and does not
acknowledge two optional layers. The spaced `? ?` form has three states:

1. outer absent;
2. outer present containing an absent `MyType?`; and
3. outer present containing a present `MyType?`, which contains one `MyType`.

The two absent-looking states can carry different meaning in generic code,
patches, caches, staged computation, and protocols. No operation flattens them.

Construction states are explicit:

```zax
outerAbsent : MyType? ?
outerPresentInnerAbsent : MyType? ? = [{}]

innerPresent : MyType? = myValue
outerPresentInnerPresent : MyType? ? = [{ innerPresent }]
```

Direct shorthand may add the first optional layer:

```zax
first : MyType? = myValue
```

Adding a layer around an already-optional value requires a packet:

```zax
inner : MyType?

outer : MyType? ? = inner       // error: optional-depth intent
outer : MyType? ? = [{ inner }] // legal
```

Same-type construction and replacement remain ordinary:

```zax
source : MyType? ?
copy : MyType? ? = source

destination varying : MyType? ? varying = source
destination = source
```

The intent gate follows resolved type structure, including aliases and generic
substitution:

```zax
MyInner :: alias type MyType?
MyOuter :: alias type MyInner?

innerAlias : MyInner
outerAlias : MyOuter = innerAlias
// error: resolved construction adds a layer around an optional

outerAliasFromPacket : MyOuter = [{ innerAlias }]
```

Each `?`, `reset`, packet construction, presence test, and dereference acts on
one layer. Access to the final value proves and crosses both:

```zax
if ?nested {
  inner : MyType? & = nested.

  if ?inner
    use(inner.)
}
```

A declared operation returning `T? ?` owns its exact result state. Type
inference does not alter that result, and optional type formation does not imply
that a source type provides a nested conversion or admission operation.

### Qualifications, special contained types, and identity

`?`, `*`, and `&` form outward type layers. Qualifiers apply to the completed
layer immediately to their left before the next marker wraps it:

```zax
T q0 ? q1 * q2
```

means a `q2` pointer to a `q1` optional containing `q0 T`. Optional wrapper
qualifications and boxed qualifications are independent:

```zax
innerReadonly : MyType readonly?
wrapperReadonly : MyType? readonly
```

The first permits wrapper-state operations through otherwise mutable/writable
wrapper defaults but exposes readonly boxed access. The second freezes change
through the wrapper path but does not rewrite the boxed type's qualifications.
After proven postfix `.`, wrapper qualifications are gone from view; the result
has the boxed qualifications.

This establishes `?` as semantic indirection despite inline storage:

> Qualification does not propagate across the optional boundary, but lifetime
> ownership does.

Outer optional immutability may stabilize presence without making a separately
qualified mutable boxed value immutable. A compiler may rely on the wrapper
state but not the boxed bytes. To stabilize both, both layers say so:

```zax
stable : MyType immutable? immutable
```

This refines the current broad deep-immutability and readonly-projection rules
and requires explicit qualifier-owner integration during promotion.

#### Wrapper qualification meanings

| Qualifier position | Meaning for an optional |
| --- | --- |
| Wrapper `mutable` | Presence may change within this wrapper lifetime through an authorized operation |
| Wrapper `immutable` | Presence remains stable for this wrapper lifetime |
| Wrapper path `writable` | This path may perform an otherwise permitted state change or complete replacement |
| Wrapper path `readonly` | This path may observe but not reset, packet-construct, or otherwise change wrapper state |
| Wrapper type-side `varying` | The place may receive another complete `T?` wrapper lifetime |
| Wrapper type-side `final` | The place may not receive another complete wrapper lifetime |
| Declaration-side `varying` | This declaration/path may exercise available complete-wrapper replacement |
| Declaration-side `final` | This declaration/path may not exercise complete-wrapper replacement |

Wrapper `final` does not freeze mutable presence. These retain one wrapper
lifetime:

```zax
optional final : MyType? mutable final = value

reset optional
reset optional
optional = [{}]
```

This replaces the complete wrapper lifetime and is rejected:

```zax
optional = otherOptional
```

An immutable varying wrapper has the opposite boundary:

```zax
optional varying :
  MyType? immutable writable varying = first

reset optional          // error: would mutate current immutable wrapper
optional = [{}]         // error: would mutate current immutable wrapper
optional = second       // legal complete-wrapper replacement
```

For new direct storage, declaration-side `final` with omitted type-side stance
resolves a final underlying place. An alias may instead be declaration-final
while truthfully referring to a varying place.

#### Boxed qualification and owner destruction

Boxed `mutable`/`immutable`, `writable`/`readonly`, and `final`/`varying` govern
the boxed value and access produced by `.`. They do not prevent the mutable,
writable wrapper owner from ending the boxed lifetime:

```zax
optional : MyType immutable readonly final? mutable writable

reset optional
optional = [{}]
```

Those operations structurally remove and create conditional payload places.
They do not mutate or independently replace the old value through
`optional.`. Boxed `final` prevents independent boxed-place reconstruction;
boxed `readonly` prevents mutation through the exposed path; boxed `immutable`
prevents mutation during that boxed lifetime.

Destroying a boxed reference ends only the stored reference-handle lifetime, not
its referent. Destroying a boxed pointer follows that pointer type's ownership
contract: a raw pointer may do no pointee work, while a reference-counted or
owning pointer may release or destroy another value.

#### Operation qualification matrix

`T` below means the qualifier combination permits the operation whenever its
ordinary constructor or transfer candidate exists. `N` means the stated
combination prohibits it. `S` means proof, selected transfer stance, or another
operation-specific condition decides it. `-` means that column does not decide.

| Operation | Current wrapper requirements | Wrapper-place requirements | Boxed requirements | Allow | Effect on wrapper | Effect on boxed value |
| --- | --- | --- | --- | --- | --- | --- |
| `?value` | Live readable wrapper | `-` | `-` | T | None | None |
| `value.` | Live readable wrapper | `-` | Preserve declared boxed qualifications | S | None | Produce boxed access only with presence proof |
| `reset value` | `mutable` + `writable` | `final` or `varying` | `-` | T | Same wrapper becomes/remains absent; return wrapper reference | Destroy once when present |
| `value = [{...}]` | `mutable` + `writable` | `final` or `varying` | Applicable boxed constructor | T | Same wrapper becomes present | End old lifetime if present; construct fresh payload |
| `value = otherOptional` | Writable destination | Type-side and declaration-side `varying` | Compatible optional source transfer | T | End old wrapper lifetime; construct new wrapper | Old payload ends with old wrapper; new state comes from source |
| `value = valueOfT` | `-` | `-` | `-` | N | `-` | Intent error; use a packet |
| `value. = source` | Wrapper readable; presence proven | `-` | Selected boxed assignment or replacement must accept effective qualifications | S | Presence remains true | Operate on existing boxed value |
| `value.---()` | `-` | `-` | `-` | N | Would fail to update presence | Always rejected |

Wrapper access does not narrow boxed access after `.`. The boxed declaration
itself decides whether `value. = source` may mutate or reconstruct. Ending that
boxed lifetime invalidates earlier references even though the wrapper may remain
present with a new payload.

#### Transfer-stance matrix

The stance expression alone has no state effect. These rows describe a consumer
that accepts the qualified source:

| Source stance accepted by consumer | Source wrapper requirements | Boxed requirements | Allow | Source wrapper after consumption | Boxed source after consumption |
| --- | --- | --- | --- | --- | --- |
| Ordinary `copy` | `-` | Compatible copy | S | Unchanged | Unchanged |
| `deep` | `-` | Compatible recursively independent transfer | S | Unchanged | Unchanged |
| `last value` | Wrapper state must be mutable through writable access if the consumer terminally transfers a present payload | Compatible `last` transfer | S | Absent after terminal payload transfer; already-absent state remains absent | Present payload lifetime ends |
| `move value` | Wrapper qualifiers do not propagate to the boxed value; presence is unchanged | Compatible nonterminal move under boxed qualifications | S | Preserves absence/presence | Present payload remains live in defined moved-from state |

Whether an absent qualified optional can satisfy a consumer follows that
consumer's result contract. Same-type optional transfer can propagate absence;
a future unwrapping consumer may panic or be unavailable. Merely writing
`last value` or `move value` neither tests presence nor changes state.

#### References, pointers, and arbitrary layer composition

A reference has a hidden handle location, auto-follows its referent, cannot be
rebound, and accepts no qualifiers on the reference layer. It may nevertheless
be contained in an optional or pointed to when that handle location is
addressable:

```zax
MyType readonly & ?          // optional containing a readonly reference
MyType? readonly &           // reference to a readonly optional wrapper
MyType readonly & * writable // writable pointer to a readonly reference handle
```

Direct reference-to-reference formation is rejected:

```zax
MyType & & // error
```

Pointers, references, and optionals otherwise compose without an artificial
depth limit when each adjacent layer is eligible:

```zax
MyType & * & * &
MyType & ? * & ?
```

The second type reads outward as an optional containing a reference to a pointer
to an optional containing a reference to `MyType`. Each optional has independent
presence and qualifications. Reference layers accept no intervening qualifier
group, while pointer and optional layers do.

An optional reference owns only the conditional reference handle:

```zax
maybeView : MyType readonly & ?
```

Reset ends the stored reference lifetime, not the referent. The applicable
lifetime strategy must prove that a present stored reference never outlives its
referent.

Optional absence also remains distinct from a boxed type's own empty-like state:

- optional pointer: outer absent versus present pointer-to-nothing versus another
  present pointer state;
- optional function: outer absent versus present function `Nothing` versus a
  present callable;
- nested optional: outer absent versus outer-present/inner-absent versus both
  present.

Zero-storage boxed values still require the semantic presence distinction.
Eligibility, addressability, pointer ownership, function `Nothing`, and exact
representation remain type-family and lifetime concerns; optional composition
does not collapse their states.

An identity's validating `optional from` operation remains an
absence-producing admission policy, not construction syntax. A present result
contains a real identity value with its ordinary lifetime and qualifications.

### Costs, representation pressure, and observable behavior

The model requires enough information to preserve every distinct wrapper state,
but no particular tag or layout:

- a separate tag may increase `TypeSize`, alignment, and padding;
- a valid niche may represent absence only if every boxed value and nested state
  remains distinguishable;
- `T? ?` requires at least three semantic states;
- optional pointers/functions retain their inner empty-like states;
- optional zero-storage values still need a state distinction;
- presence checks read represented state;
- reset, copy, transfer, destruction, and replacement perform boxed work only
  when applicable to a present path;
- direct construction does not require observable default construction followed
  by assignment.

Exact layout, discriminant location, niche selection, ABI, reflection encoding,
and cross-module representation stability remain future formal-contract work.
Formatting and debugging must distinguish absence, present payloads, and nested
states; exact text remains later tooling/design work.

### Diagnostics and source stability

An intent error must identify the plausible competing readings and the explicit
source that distinguishes them:

| Rejected source | Why intent is unclear | Required acknowledgement |
| --- | --- | --- |
| `optional = value` | It is one dot away from boxed assignment but could instead mean construction or wrapper replacement with different lifetime effects | `optional = [{ value }]` for fresh construction or `optional. = value` for boxed assignment |
| `[{ }]` | Its blank body looks like the programmer opened a nonempty packet but forgot the intended arguments | `[{}]` explicitly acknowledges zero inputs |
| `T??` | `??` is the conditional-expression token rather than two visibly separate wrapper layers | `T? ?` |
| `outer : T? ? = innerOptional` | It resembles same-type optional construction while actually adding another optional layer | `outer : T? ? = [{ innerOptional }]` |
| Bare `[{ innerOptional }]` in an expression hole | A packet supplies constructor inputs but is neither a value nor an anonymous structure and has no destination | `(: T? ? = [{ innerOptional }])` |

Programmer-facing diagnostics and teaching must explain these confusions rather
than use "intent error" as a self-justifying label.

The eventual owner should teach diagnostics for:

- value-shaped assignment to an existing optional when a packet is required;
- `[{ }]` where canonical `[{}]` is required;
- a bare construction packet without a destination;
- optional-depth shorthand around an already-optional source;
- compact `T??` rather than spaced `T? ?`;
- unproved or known-absent dereference;
- attempted direct boxed destruction;
- wrapper mutation through readonly or immutable access;
- complete-wrapper replacement without varying place authority;
- boxed assignment incompatible with boxed qualifications;
- use of an invalidated payload reference;
- unsupported or incompatible `copy`/`move`/`last` transfer;
- non-converging conditional arms;
- a requested nested conversion/admission operation that does not exist.

Diagnostics may suggest a packet, explicit anonymous typed construction,
presence proof, or optional conversion when that matches likely intent. They
must not silently reinterpret source.

Formatters must preserve:

- the space between nested `?` markers;
- contiguous `[{}]`;
- construction packets and their entry order;
- postfix `.` placement;
- wrapper-versus-boxed qualifier attachment;
- transfer stance and any exact phrase fence;
- contiguous future `unsafe<categories>{...}` enclosure syntax.

### Aligned deferrals and required live captures

These findings are material but do not block the optional model:

- **General transfer and lifetime:** `copy`/`move`/`last` preference, whether a
  stance permits or requires specialized transfer, result/source qualifications,
  moved-from validity, source-shell destruction, terminal references, parameter
  binding versus later consumption, and exact transfer-view syntax belong in
  [raw lifetime input](../../raw/lifetimes.md). Future changes must revisit the
  optional source-state matrix.
- **Transfer phrase family:** `as copy`, `as deep`, `as move`, and `as last` are
  candidate exact mutually exclusive post-unary phrases. Future transfer-semantics
  and qualifier work must decide their syntax, selection force, and relation to
  optional pre-unary forms; the operator catalog records exact forms only after
  that decision. Ordinary unfenced source is a goal requiring source-structure
  validation, not an established guarantee.
- **Outermost stance and inward propagation:** transfer semantics attach to the
  outermost completed composite source and are expected to carry inward as a
  generated operation unwraps or decomposes each layer:

  ```zax
  MyType readonly ? writable * immutable * varying deep
  ```

  A present optional forwards `copy`, `deep`, `move`, or `last` to its boxed
  transfer; an absent optional performs no boxed transfer. Nested wrappers carry
  the stance through each present layer. Future transfer work must define custom
  interception, pointer ownership behavior, reference authority, result
  qualification, and whether a stance permits or requires the specialized
  operation. This propagation requirement does not imply matching pre-unary
  `copy` and `deep` optional operators.
- **Inference:** `:=` examples currently assume value construction rather than
  inferred aliasing; partial `: & =` explicitly requests a reference. Exact
  value/reference/pointer inference and transfer-qualified initialization belong
  to declarations and bindings, invocation, qualifiers, and
  [raw lifetime input](../../raw/lifetimes.md).
- **Generic substitution and aliases:** resolved type structure controls the
  optional-depth intent gate even when aliases or type parameters hide a layer.
  Complete constraints and substitution behavior belong in
  [raw generic input](../../raw/type-parameters-and-generics.md) until future generic
  work integrates them.
- **Self-aliasing:** lifecycle-sensitive self-assignment belongs in raw lifetime
  input. General same-object warnings for arbitrary symbolic and phrase operators
  belong in [raw analysis controls](../../raw/analysis-controls.md) and cannot be a
  universal semantic rejection.
- **Unsafe analysis controls:** the aligned source shape is contiguous,
  non-scoping `unsafe<category, ...>{...}` with whitespace after each comma.
  Assertions may cover only named unproved-valid boundaries and never known
  violations.
- **Unsafe lifetime failure:** an accepted unsafe presence/lifetime assertion
  adds no required runtime handle or check. A false claim has undefined
  consequences; debug instrumentation may detect it and panic. Unconditionally
  known absence or an ended lifetime remains invalid.
- **General panic-check contracts:** future analysis/safety work must evaluate a
  shared default-check/explicit-unchecked contract for cheaply checkable runtime
  failures without implying runtime tracking for references or other static
  lifetime proof.
- **Numeric nested conversion:** optional type formation does not generate
  `as U8? ?` or another nested policy. If future numeric review declares one, its
  exact state contract belongs in
  [raw numeric type-family input](../../raw/numeric-type-families.md) and the numeric
  operator owner.
- **Pointer/reference lifetime and addressability:** optional references,
  pointers to references, deep layer composition, reference-handle storage,
  ownership, and invalidation feed future pointer/lifetime work. Direct
  reference-to-reference remains rejected.
- **Optional callable construction and combinators:** construction packets
  containing named or future inline callable values, and present/absent
  `fold`/`on some`/`on none` composition, belong in
  [raw function-composition input](../../raw/function-composition-and-chaining.md)
  together with selection pressure. `optionalValue.onSome(...)` conflicts with
  postfix `.` crossing into the boxed value and is not neutral candidate syntax.
- **Pattern matching and presence binding:** any future selection surface must
  preserve per-layer presence and lifetime proof; capture remains in
  [raw selection input](../../raw/selection.md).
- **Async:** suspension/cancellation must not observe a wrapper as present before
  boxed construction completes and must disposition partially transferred
  resources under the eventual contract preserved in
  [raw async input](../../raw/async.md).
- **Formal representation:** layout, ABI, niches, reflection, and exact
  formatting remain deferred until named consumers require contracts. Repeated
  wrapper layers and per-layer qualifications feed
  [raw reflection input](../../raw/reflection.md); exact foreign layout and ABI feed
  [raw interop input](../../raw/interop.md). Exact human-facing formatting remains a
  future boundary in the optional owner.
- **Symbolic trailing continuation:** recognized spaced symbolic infix
  operators/components at physical line end establish continuation; attached
  symbols and phrases do not, and explicit `\` is redundant where the symbolic
  form already proves intent. Current and raw examples require a non-archive
  sweep; legacy-only findings remain for their focused reviews.

### Promotion teaching obligation

This working record is intentionally information-dense decision and integration
material. It must not be copied or lightly rearranged into programmer-facing
documentation.

Promotion must reconstruct the optional owner for a cold reader:

1. begin with ordinary absent/present source and the wrapper/boxed mental model;
2. teach default and packet construction before replacement matrices;
3. establish presence proof and postfix access before advanced alias cases;
4. introduce the concise `reset`/`last`/`move` distinction before transfer
   machinery;
5. teach nested optionals through their three concrete states and explicit
   depth acknowledgement;
6. layer wrapper versus boxed qualification after ordinary operations are
   understandable;
7. place exhaustive matrices, deferrals, and owner boundaries later as reference
   material.

Every retained intent error must show the plausible confusion and the source
that resolves it. Every critical cost, diagnostic, lifetime invalidation, and
unsafe boundary needs a representative example near the rule it teaches.
Completeness and accuracy do not compensate for forcing a programmer to
reconstruct the model from analytical chronology.

### Candidate lasting ownership map

This is a proposal for the pre-promotion documentation-fit dry run, not
authorization to edit or create an owner:

- A dedicated optional-values owner should teach the wrapper/boxed model,
  operations, construction packets, complete-wrapper replacement, nested
  optionals, qualification, proof, transfer effects, costs, and diagnostics as
  one cohesive human-facing concept.
- Construction, replacement, and destruction should retain general constructor
  packets, present-construction validity, failure/lifetime rules, access proof,
  and reusable replacement machinery. It must integrate `[{}]`, optional packet
  state mutation, and optional complete-wrapper replacement without making
  ordinary `=` state-dependent.
- Declarations and bindings should own direct initialization, empty/default
  declaration behavior, anonymous typed packet construction, and eventual `:=`
  value/reference inference.
- Qualifiers should own the outward layer algebra, declaration-side versus
  type-side replacement, and the refinement that semantic `?` indirection stops
  qualification propagation while preserving lifetime ownership.
- Operators and the operator catalog should retain protected optional presence,
  postfix dereference parsing, protected `reset`, and any exact transfer phrase
  family accepted by its future transfer/qualifier decision owner.
- Core flow control should retain optional conditional convergence, including
  the nested-depth packet acknowledgement and the rule that a bare packet is not
  an arm expression.
- Function invocation should own argument/result construction boundaries,
  immediate binding order, required-result handling, and the future transfer
  stance preference/capability model.
- Integer literals and the integer operator catalog should retain
  construction-versus-optional-conversion behavior. Numeric raw input should
  receive the non-generated nested-conversion pressure.
- Identity types should retain validating `optional from` as a distinct
  absence-producing admission.
- Pointer/reference and lifetime work should receive reference addressability,
  optional/reference/pointer layer composition, aliases across wrapper and
  payload transitions, and `copy`/`move`/`last` propagation and effects.
- Analysis-control raw input should receive the corrected unsafe enclosure,
  multiple exact categories, and operation-aware self/alias diagnostic pressure.
- Safety raw input should receive general default panic-check versus explicit
  unchecked-contract pressure and the undefined behavior of false unsafe
  lifetime claims.
- Function-composition raw input should receive optional callable packet and
  present/absent combinator pressure; selection raw input should receive the
  selected-callback and convergence side.
- Generic raw input should receive resolved alias/substitution optional-depth
  intent and non-flattening requirements.
- Async raw input should receive optional construction/transfer completion and
  cancellation pressure.
- Reflection and interop raw inputs should receive repeated-layer,
  per-qualification metadata and exact layout/ABI pressure respectively.
- Source structure and formatting owners must preserve `[{}]`, spaced nested
  optional markers, layer attachment, and non-value packet boundaries.
- The legacy optional page should be consumed by value: promote useful evidence,
  move every unresolved concern to a live destination, and retire conflicting or
  superseded teaching rather than preserving duplicate authority.

Before promotion, the dry run must verify every finding and legacy passage
against this map, identify the exact changed-file set, and ensure every deferred
pressure is written into its live destination. No current owner may depend on
this numbered work item for meaning.

## Dispositions and promotion dry run

### Initial documentation-fit dry run: PASS

Performed after the aligned working record and final reviewability refinements
were captured.

This initial dry run proposed retaining a historical `optional.md` path. The
maintainer's post-promotion review found that the path had no remaining value.
The corrective dry run below supersedes that disposition and exact file set.

**PASS** means the optional findings have one coherent human-facing owner,
every local interaction has an existing owner able to absorb it, every deferred
consequence has an indexed live destination, the legacy input can be consumed
without losing useful evidence, and the exact promotion change set is known. It
does not authorize promotion.

No formal specification, implementation mapping, compiler repository content,
new project directory, or new raw placeholder is required.

### Proposed structure and reading path

Add one cohesive current owner:

```text
language/optional-values.md
```

Retain the current directory layout. Do not create an optional subdirectory or a
specification area.

The ordinary reader path becomes:

```text
README.md
-> index.md
-> language/optional-values.md
```

Local current owners link to the optional owner only where optional behavior
changes the reader's immediate task. Raw destinations remain outside ordinary
programmer reading.

The root `optional.md` path becomes a concise historical, non-normative,
audit-only disposition page, following the established `operator.md` pattern.
Keeping that path preserves historical project and external references without
retaining duplicate teaching. `index.md` must route ordinary readers directly to
the new owner rather than through the historical page.

`project/README.md` remains unchanged during promotion because work item `012`
stays active until separately authorized closure. The operating-prompt sources
also remain unchanged.

### Human-facing teaching plan

The new owner must be reconstructed rather than copied from this working record:

1. open with ordinary absent/present declarations, presence testing, and proven
   postfix access;
2. establish the live-wrapper/conditional-boxed-lifetime mental model;
3. teach default absence, direct present construction, and `[{}]`/packet
   construction;
4. introduce `reset` and the concise `copy`/`move`/`last` source-state table;
5. distinguish packet construction, complete-wrapper `=`, and boxed `. =`;
6. teach nested optionals through three concrete states and explicit depth
   acknowledgement;
7. layer wrapper-versus-boxed qualifications and lifetime invalidation;
8. place complete matrices, special contained types, costs, diagnostics, and
   future boundaries later.

Every intent error locally shows its plausible competing readings and the source
that resolves them. The owner uses ordinary valid source first, marks every
known error inline, and does not present working chronology, rejected spellings,
or owner-disposition mechanics as programmer teaching.

### Aligned finding ownership

| Finding | Lasting owner |
| --- | --- |
| Absent/present wrapper model; default absence; boxed lifetime; optional operations; nested optionals; wrapper/boxed distinction; programmer-facing costs and diagnostics | New `language/optional-values.md` |
| General construction packet, canonical `[{}]`, packet evaluation/binding order, present-construction validity, lifecycle cleanup, complete-wrapper replacement integration, direct boxed-destruction prohibition | `language/construction-and-destruction.md` |
| Direct/default declaration construction, optional-depth intent at declaration boundaries, ordinary empty packet on non-optionals | `language/declarations-and-bindings.md` |
| Outward qualifier layers, wrapper versus boxed qualifications, `?` as a semantic indirection boundary, type-side versus declaration-side `final`/`varying` | `language/qualifiers.md` |
| Protected presence, reset, optional `last`/`move` source forms, postfix optional access handoff, no ordinary `unwrap` replacement | `language/operators.md` |
| Exact recognized optional forms, fixity, precedence, protected/reserved status, spaced nested marker and empty-packet source constraints | `language/operator-catalog.md` and `language/source-structure.md` |
| Optional conditional convergence, first-layer construction, nested-depth acknowledgement, proof consequences in flow | `language/core-flow-control.md` |
| Anonymous typed packet construction, parameter/result boundaries, required-result handling, immediate binding and future transfer stance interaction | `language/function-invocation.md` |
| Exact/optional/narrowing integer summary and construction-versus-optional-conversion examples | `language/integers.md`, `language/integer-literals.md`, and `language/integer-operator-catalog.md` |
| Validating `optional from` as a distinct absence-producing admission | `language/identity-types.md` |
| Shared vocabulary for optional wrapper, boxed optional value, construction packet, and transfer stance where independently reusable | `language/terms.md` |
| Public current route | `index.md` |
| Historical legacy disposition and preserved path | `optional.md` |

No finding requires a new reusable principle in `language/principles.md`.
Qualification non-propagation is integrated first in the qualifier and optional
owners rather than promoted into a broader umbrella principle without separate
review.

### Deferred material and live destinations

| Deferred pressure | Live destination and required addition |
| --- | --- |
| `copy`/`deep`/`move`/`last` preference, outermost stance propagation, source/result qualifications, moved-from validity, terminal references, source-shell destruction, parameter binding versus later consumption, pre/post-unary transfer syntax | `project/raw/lifetimes.md`; add one optional/layer-derived transfer section with the representative composite type and optional source-state table |
| Optional presence proof overrides, multiple unsafe categories, contiguous non-scoping `unsafe<...>{...}`, replacement aliasing, operation-aware same-object diagnostics | `project/raw/analysis-controls.md`; repair the existing spaced example and add optional proof/alias pressure |
| Possible nested numeric conversion result such as `U8? ?`, without generated availability or inferred policy | `project/raw/numeric-type-families.md`; add explicit non-generation and exact-state-contract pressure |
| Optional pattern matching, per-layer presence, binding, exhaustiveness, and proof lifetime | `project/raw/selection.md`; add an optional matching section and activation pressure |
| Alias/type-parameter substitution that produces nested optionals and must preserve the resolved optional-depth intent gate | `project/raw/type-parameters-and-generics.md`; add representative alias and generic examples |
| Suspension/cancellation during optional construction or transfer | `project/raw/async.md`; add wrapper-presence publication and partial-resource disposition constraints |
| Reflection of repeated wrapper layers, per-layer qualifications, and source distinctions such as `T? ?` | `project/raw/reflection.md`; add optional type/source metadata pressure |
| Exact optional layout, discriminants, niches, foreign layout, and ABI | `project/raw/interop.md`; add optional representation/interoperation pressure while leaving exact layout unaccepted |
| Repository-wide legacy optional contradiction tracker | `project/raw/cross-cutting-audit.md`; mark the optional part resolved to the new owner/historical disposition while leaving unrelated error-handling concerns open |

All listed raw files are already indexed by `project/raw/README.md`, and their
current activation descriptions are broad enough to include these additions.
No raw-index edit or new raw file is needed.

Exact optional debug/printed formatting remains a visible future boundary in the
new optional owner rather than being orphaned in this work item.

### Legacy disposition

Consume `optional.md` by value:

- promote the useful absent/present, reset, construction, dereference, parameter,
  and replacement distinctions into current owners;
- replace uninitialized-memory wording with boxed-lifetime semantics;
- replace `foo = value`, `= #`, `= :`, `{}`, `[{ }]`, and implicit member access
  examples with aligned source or explicit historical disposition;
- preserve general transfer, pointer/reference, analysis, async, generic,
  reflection, and ABI pressure in the live raw destinations above;
- leave only the concise historical path explaining where every useful concern
  moved.

Update all current links that currently send readers to legacy optional input.
Historical provenance may retain the root path, but no current rule may depend
on that page for meaning.

### Exact proposed promotion change set

Create:

1. `language/optional-values.md`

Rewrite as historical disposition:

2. `optional.md`

Update the public route:

3. `index.md`

Update current conceptual owners:

4. `language/terms.md`
5. `language/source-structure.md`
6. `language/declarations-and-bindings.md`
7. `language/qualifiers.md`
8. `language/construction-and-destruction.md`
9. `language/operators.md`
10. `language/operator-catalog.md`
11. `language/core-flow-control.md`
12. `language/function-invocation.md`
13. `language/integers.md`
14. `language/integer-literals.md`
15. `language/integer-operator-catalog.md`
16. `language/identity-types.md`

Update existing indexed raw destinations:

17. `project/raw/lifetimes.md`
18. `project/raw/analysis-controls.md`
19. `project/raw/numeric-type-families.md`
20. `project/raw/selection.md`
21. `project/raw/type-parameters-and-generics.md`
22. `project/raw/async.md`
23. `project/raw/reflection.md`
24. `project/raw/interop.md`
25. `project/raw/cross-cutting-audit.md`

Update this work item during promotion only as needed to record completed
dispositions and validation:

26. `project/work/012-optional-values.md`

No other file is part of the proposed promotion. In particular:

- no archive file is read or edited;
- `README.md`, `project/README.md`, `project/raw/README.md`,
  `language/principles.md`, `pointers.md`, `nothing.md`, and operating-prompt
  sources remain unchanged;
- work item archival, work item `013`, and the current-work pointer are separate
  closure actions after promotion.

### Promotion validation required

The authorized promotion must verify:

- the new owner reads coherently for a cold programmer without this work record;
- every intent error explains its plausible confusion and correction;
- every compile error is marked inline;
- no current page cites this work item for meaning;
- optional rules have one owner and local summaries do not duplicate authority;
- all legacy optional links route to the current owner or justified historical
  provenance;
- all deferred findings appear in the exact live raw destinations above;
- `copy`/`move`/`last` typography distinguishes literal stances from prose
  concepts throughout every touched file;
- nested `? ?`, `[{}]`, qualification attachment, transfer stance, and
  `unsafe<...>{...}` presentation remain source-stable;
- local links and anchors resolve;
- Markdown renders coherently;
- the final changed-file set is exactly the authorized promotion set;
- staged and unstaged review boundaries remain preserved.

### Initial promotion application: PASS

The language maintainer explicitly authorized the PASS change set after staging
the completed working record. The promotion was applied as one unstaged
repository-wide change set. This records the initial promotion application; the
subsequent staged review and corrective promotion below supersede its
historical-path disposition and final validation set.

The initial change set:

- `language/optional-values.md` now owns cohesive programmer-facing optional
  design;
- `index.md` routes ordinary readers directly to that owner;
- `optional.md` is a historical, non-normative disposition path with no
  competing teaching;
- every listed current conceptual owner contains only the local optional
  boundary its readers need and routes complete behavior to the optional owner;
- all nine indexed raw destinations contain their assigned deferred pressure and
  updated work-012 provenance;
- the cross-cutting legacy tracker no longer treats optional contradictions as
  unresolved;
- no unlisted current, legacy, raw, router, archive, operating-prompt, or project
  file was edited.

The promoted owner was reconstructed for teachability rather than copied from
this record. It leads with ordinary use and the concise state/transfer model,
then layers construction, replacement, proof, nesting, qualification, reference
details, costs, diagnostics, and future boundaries. Intent errors explain their
plausible confusion and correction.

Promotion validation passed for:

- unique optional ownership and local handoffs;
- current-owner authority and implementation-state wording;
- explicit disposition of every aligned finding and deferral;
- required raw files remaining indexed;
- legacy route and provenance preservation;
- inline invalid-example diagnostics;
- `copy`/`move`/`last` terminology in touched current documentation;
- nested optional, construction-packet, qualifier-layer, transfer-stance, and
  unsafe-enclosure source stability;
- Markdown fence balance;
- relative file links and heading anchors;
- whitespace and diff integrity;
- the exact authorized file set;
- the fully staged pre-promotion boundary plus unstaged promotion changes.

This PASS records completed promotion and validation only. Work item `012`
remains active. Archival, creation of work item `013`, current-work routing, and
any version-control staging or commit remain separately authorized actions.

### Corrective documentation-fit dry run: PASS

The maintainer staged the complete initial promotion, made no edits, and aligned
a focused corrective review. The review changes presentation, source structure,
unsafe boundaries, and deferred capture without reopening the accepted optional
model.

The revised structure retains `language/optional-values.md` as the one current
owner and deletes `optional.md`; no historical stub remains because the
maintainer found no continuing compatibility, provenance, or audit value in that
path. Current provenance wording and the active reading record no longer link to
the deleted file.

The correction needs no new language owner or raw document:

- inline callable construction and optional folding fit existing function
  composition and selection raw inputs;
- generic panic-check and unsafe lifetime consequences fit existing analysis and
  safety raw inputs;
- legacy-only continuation findings fit the existing cross-cutting audit.

The trailing-symbolic continuation refinement is coherent with the current
source owner:

- a recognized symbolic infix operator/component that is the last non-comment
  code token on a line continues its required right operand or section;
- the preceding whitespace and following newline present infix use;
- initializer/assignment `=`, inferred declaration `:=`, `??`, and `;;` follow
  the same rule;
- attached symbolic forms and operator phrases do not establish continuation;
- explicit `\` is redundant when another accepted rule already continues;
- comma separators require no preceding whitespace and whitespace after them,
  with newline satisfying the latter.

A sweep of every current language owner and live raw/work input found and fixed
the affected unmarked examples. Four old explicit-continuation forms remain only
in `meta-functions.md` and `except.md`, both explicitly legacy input; their
future focused correction is preserved in the cross-cutting audit rather than
partially modernizing unrelated legacy examples.

The corrective set also:

- explains `[{}]` as explicit zero-input acknowledgement and `[{ }]` as an
  apparently unfinished argument packet;
- adds complete direct/same-type/depth-changing declaration contrasts;
- clarifies that anonymous typed declaration means an unnamed destination, not
  an anonymous type;
- removes unlikely nested-conversion denials from positive integer/identity
  teaching while retaining future numeric pressure;
- defines false unsafe presence/lifetime claims as undefined with no required
  runtime check and optional debug panic instrumentation;
- separates unsafe alias permission at reset from unsafe unproved access;
- restores `deep` to the complete transfer-stance family;
- captures optional callable packet and present/absent combinator pressure in
  existing indexed raw owners.

Exact corrective change set:

1. `language/construction-and-destruction.md`
2. `language/core-flow-control.md`
3. `language/declarations-and-bindings.md`
4. `language/function-invocation.md`
5. `language/identity-types.md`
6. `language/integer-operator-catalog.md`
7. `language/integers.md`
8. `language/operators.md`
9. `language/optional-values.md`
10. `language/source-structure.md`
11. delete `optional.md`
12. `project/raw/README.md`
13. `project/raw/analysis-controls.md`
14. `project/raw/cross-cutting-audit.md`
15. `project/raw/function-composition-and-chaining.md`
16. `project/raw/lifetimes.md`
17. `project/raw/safety.md`
18. `project/raw/selection.md`
19. `project/work/012-optional-values.md`

No archive, operating-prompt, current-work router, implementation, or other
legacy page belongs to the corrective set.

### Corrective promotion application: PASS

The language maintainer authorized the corrective PASS set after staging the
complete initial promotion and made no edits. All 19 corrective files above were
updated as one unstaged change set.

Validation passed for:

- deletion of `optional.md` with no remaining live links;
- unique current ownership in `language/optional-values.md`;
- corrected optional names, declaration contrasts, anonymous-declaration
  teaching, packet intent rationale, and positive numeric/identity presentation;
- mandatory static presence proof and explicit undefined consequences for false
  unsafe presence/lifetime claims;
- comma whitespace and valid operator-phrase source;
- complete `copy`/`deep`/`move`/`last` raw transfer coverage;
- optional callable construction and present/absent combinator capture in
  existing indexed raw owners;
- generic default-check/unchecked-panic pressure in analysis and safety inputs;
- trailing-symbolic continuation and redundant-`\` diagnostics;
- a sweep of every current language owner and live raw/work example;
- durable capture of the four intentionally untouched legacy continuation
  examples;
- Markdown fence balance, relative links, heading anchors, and whitespace;
- the exact 19-file corrective set;
- preservation of the fully staged initial promotion and unstaged correction.

The corrected promotion remains current conceptual documentation, not a formal
specification or implementation claim. Work item `012` remains active; closure,
archival, work item `013`, staging, and commit remain separately authorized.
