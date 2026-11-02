# 014: Reference origins and lifetime strategies

| Field | Value |
| --- | --- |
| Status | Historical working material / non-normative / audit-only |
| Work Item | `014` |
| Created | 2026-09-05 |
| Completed | 2026-09-06 |
| Owns | The bounded review defined by the fixed initiating input |
| Does Not Own | Complete pointer grammar or ownership taxonomy, async suspension/cancellation, cross-thread preparation, complete generics, formal borrow checking, layout/ABI, or compiler implementation |

## Non-authority notice

This file is a historical project record. Its accepted findings were promoted
into their current language owners before archival. The record remains
non-authoritative and audit-only; current language documents own the meaning.

## Fixed initiating input

This section records the information aligned when work item `014` was created.
It is intentionally incomplete and must not be rewritten as work develops.

### Initiating concern

Develop a coherent human-developer-facing model for reference origins and
selectable lifetime strategies:

1. what exact lifetime a reference names;
2. reference origin from direct storage, members, temporaries, parameters,
   results, optionals, identities, and other eligible sources;
3. the distinction between a reference to one exact value lifetime and a
   reference that tracks a place across replacement;
4. reference validity through content mutation, whole-value replacement,
   reconstructive replacement, `move`, `last`, destruction, and reconstruction;
5. parameter binding and the synchronous call-completion boundary;
6. returned references and escape;
7. reference capture and storage;
8. interior references and aliases;
9. intentional lifetime-policy plurality, guarantees, costs, and failure modes;
10. safe proof versus narrow unsafe lifetime or alias assertion;
11. independent pointer/reference binding replacement or rebinding pressure;
12. pointer-object and pointee lifetime consequences at the depth required by
    references;
13. disposition of legacy `lease` and body-time terminal-capability evidence;
14. diagnostics, source stability, and lasting documentation ownership.

### Motivating pressure

Current design permits a reference to an existing value:

```zax
source : Document
view : Document readonly & = source
```

The programmer still needs to predict what `view` means if another path:

```zax
source = makeReplacement()
consume(source as move)
consume(source as last)
```

or simply lets `source` reach destruction.

The answer may depend on whether `view` names one exact `Document` lifetime,
tracks a place across successive lifetimes, or uses another selected lifetime
policy. Those choices carry different compile-time, runtime, memory,
synchronization, and usability costs.

Function invocation already extends a temporary reference through one
synchronous call:

```zax
inspect(makeDocument())
```

That does not by itself make a returned reference valid:

```zax
view := returnView(makeDocument())
```

Transfer work also establishes reference-shaped `move` and `last` parameters,
direct compiler-managed temporary `last` stance, by-value result terminal
opportunity, explicit destructive renewal, and exactly-once resource
disposition. Reference lifetime work must explain how those accepted behaviors
remain safe and understandable.

### Known assumptions

- A reference is an access path to existing storage rather than an independent
  by-value copy.
- Current conceptual design treats a direct reference as having a hidden handle,
  auto-following its referent, and not being independently rebound. Any revision
  requires concrete pointer/reference pressure and must not overload
  declaration-side `final` with another meaning.
- Mutable/immutable value truth, final/varying place truth, writable/readonly
  access, declaration-side replacement permission, and transfer stance remain
  independent.
- A same-place alias preserves the referent's actual type-side final/varying
  truth and may narrow only its own declaration-side replacement permission.
- Explicit `varying` on an immutable reference currently identifies unusual
  place-tracking behavior that may observe successive immutable lifetimes.
- Explicit arguments bind in caller source order. A reference-bound temporary
  survives through the complete synchronous call.
- A returned reference does not become valid merely because an input temporary
  survived the call.
- Named `move`/`last` references return to `copy` for ordinary nested use unless
  destructive stance is explicitly renewed.
- A direct compiler-managed unnamed by-value temporary may offer `last`; that
  does not make a reference result's referent terminal.
- A false unsafe lifetime, presence, or alias assertion may have undefined
  consequences without a required runtime check.
- Zax has no exception-style rollback model.
- There is no language implementation in this repository.

### Known inclusions

- Reference-origin mental model and terminology.
- Exact-lifetime versus place-tracking reference behavior.
- Direct storage, member, optional, identity, parameter, temporary, result, and
  capture origins.
- Mutation, replacement, move, terminal transfer, reconstruction, and
  destruction consequences.
- Synchronous parameter and temporary lifetime.
- Returned-reference origin and escape.
- Interior aliases and same-storage identity views.
- Lifetime-policy families, selection guidance, guarantees, costs, and failure.
- Static proof, diagnostics, and narrow unsafe responsibility.
- Reference binding replacement/rebinding pressure at the depth required to
  avoid conflating it with referent replacement.
- Pointer ownership/provenance, generic, async, and cross-thread consequences
  only far enough to constrain the reference model.
- Documentation fit, current owners, legacy disposition, and indexed deferrals.

### Known starting boundaries

- Complete pointer syntax, pointer representation, ownership taxonomy, and every
  pointer conversion.
- Final async suspension, cancellation, executor, and concurrency behavior.
- Cross-thread preparation stance/capability design.
- Complete generic, specialization, or qualifier-generic syntax.
- General structural typing and subtyping.
- Formal borrow-checking algorithms or proof calculus.
- Formal layout, ABI, grammar, compiler implementation, or lowering.

These boundaries do not erase consequences. Record each material pressure in a
live owner or indexed raw destination.

### Intentionally unresolved framing

- Whether ordinary `T &` names one exact value lifetime, uses a default lifetime
  policy, or needs another explicit distinction.
- Exact source syntax for place-tracking versus exact-lifetime references.
- The final set of lifetime strategies and their selection mechanism.
- Whether one policy temporarily prevents replacement while a stable reference
  exists and another tracks place transitions.
- Which policies require runtime state, synchronization, ownership, or
  allocation.
- Reference validity during and after whole-value or reconstructive replacement.
- Returned-reference origin through nested calls, result forwarding, and
  temporaries.
- Reference capture, storage, repeated invocation, and escape.
- Whether a terminal reference/capability may survive beyond one synchronous
  consumer body.
- Whether references remain categorically non-rebindable and how pointer or
  reference binding replacement is spelled.
- Exact unsafe assertion categories and diagnostics.
- Interaction with cross-thread lifetime transferability and thread-affine
  resources.

### Initial stopping guidance

Stop when the work has:

- established a usable programmer model for reference origin;
- distinguished exact-lifetime and place-tracking references;
- established mutation, replacement, move, last, reconstruction, and destruction
  effects on reference validity;
- established synchronous parameter binding, temporary extension, result
  origin, and escape behavior;
- established the useful lifetime-policy families and their visible guarantees,
  costs, and failure modes;
- reconciled optionals, identities, members, results, captures, and immediately
  necessary pointer behavior;
- dispositioned rebinding, unsafe proof, generic, async, cross-thread, and
  ownership pressure;
- identified exact current owners and legacy changes;
- and completed the required documentation-fit dry run.

Do not promote findings, archive this work item, or begin work item `015`
without the separately required discussion, alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  numbered work, promotion, teaching, deferrals, and dry runs.
- The former raw lifetime-strategies input supplied lifetime-policy plurality,
  reference origin, pointer/provenance, alias, terminal-capability, and
  cross-thread pressure. Its accepted material now lives in
  [lifetimes and references](../../language/lifetimes-and-references.md) and
  [pointers and arenas](../../language/pointers-and-arenas.md); unresolved
  mechanics live in
  [raw pointer and arena input](../raw/pointer-and-arena-mechanics.md).
- Focused [qualifier capability versus permission](../../language/qualifiers.md#type-side-truth-versus-declaration-side-permission),
  [optional qualification layers](../../language/qualifiers.md#optional-qualification-layers),
  [indirection](../../language/qualifiers.md#indirection), and
  [transfer and destruction](../../language/qualifiers.md#transfer-and-destruction) -
  supply the concrete value, place, access, alias, and replacement model.
- Focused [declaration-side replacement permission](../../language/function-invocation.md#declaration-side-replacement-permission),
  [evaluation and immediate binding](../../language/function-invocation.md#evaluation-and-immediate-binding), and
  [temporary lifetime and call completion](../../language/function-invocation.md#temporary-lifetime-and-call-completion) -
  supply synchronous binding, result, temporary, and escape pressure.
- Focused [by value and by reference](../../language/transfer-stances.md#by-value-and-by-reference),
  [projection and aliases](../../language/transfer-stances.md#projection-and-aliases),
  [receiver stance](../../language/transfer-stances.md#receiver-stance), and
  [terminal opportunity when mapping a result](../../language/transfer-stances.md#terminal-opportunity-when-mapping-a-result) -
  supply accepted transfer/reference consequences.
- Focused [reconstructive replacement](../../language/construction-and-destruction.md#reconstructive-replacement),
  [self-aliasing and interior aliases](../../language/construction-and-destruction.md#self-aliasing-and-interior-aliases),
  and [destruction](../../language/construction-and-destruction.md#destruction) -
  supply lifetime-ending, member-origin, and exactly-once disposition pressure.
- Focused [references, pointers, and other boxed types](../../language/optional-values.md#references-pointers-and-other-boxed-types)
  and [optional references](../../language/optional-values.md#optional-references) -
  supply conditional reference-handle and referent-lifetime behavior.
- Focused [identity construction and transfer](../../language/identity-types.md#construction-and-transfer) -
  supplies same-storage identity-reference and origin pressure.

### Consequence-driven

- Read [raw safety input](../raw/safety.md) and
  [raw analysis controls](../raw/analysis-controls.md) when mandatory proof,
  unsafe lifetime/alias assertion, panic, or diagnostic severity becomes
  concrete.
- Read [pointers and arenas](../../language/pointers-and-arenas.md) for current
  ownership behavior and [raw pointer and arena input](../raw/pointer-and-arena-mechanics.md)
  when exact syntax, provenance, or deferred mechanics are required.
- Read [raw async input](../raw/async.md) when a synchronous conclusion
  materially constrains suspension, cancellation, or cross-thread preparation.
- Read [raw generic input](../raw/type-parameters-and-generics.md),
  [raw callable-selection input](../raw/callable-selection.md), or
  [raw `own` input](../raw/owned-composition.md) when reference origin must
  survive specialization, exact-prototype selection, or exposed-surface
  transformation.
- Read other pointer, allocation, capture, structural, or interop material only
  when an immediate reference-origin consequence cannot be dispositioned
  coherently without it.

### Audit-only

- `project/archive/`, including work items `001` through `013`.
- Superseded implementation sketches and compiler-internal explanations.

Do not read archived work item `013` during ordinary work on `014`. Its accepted
findings are promoted into current owners and its future pressures are preserved
in live raw inputs.

## Working record

The findings explicitly marked aligned below are accepted for this review scope
but remain non-authoritative until separately promoted into their lasting
owners. Open questions and candidate dispositions remain unaligned.

### Current aligned foundation

#### Review entry point

The fundamental teaching model is:

> Every instance is created, lives, and is destroyed within a life path. An
> instance never outlives its life path. A reference is permanently bound to one
> instance place within such a path; it never owns the path and never rebinds.

This model must lead later programmer-facing lifetime teaching. Without it,
references, pointers, optionals, allocation, replacement, transfer, and
destruction look like unrelated special cases.

The most important remaining design pressure is now narrower than the initiating
question suggested. References have one permanent binding rule. The unresolved
work is to define when nested instance places continue through lifecycle
transitions and when static analysis can prove a complete resident instance is
available through them.

#### Foundational terms

**Aligned finding: life path.** A life path bounds the existence of its
instances. Outside an unsafe contract violation:

- every instance is constructed once within its life path;
- every instance is destroyed once within its life path;
- no instance survives beyond that path;
- ordinary construction follows the path's defined creation order;
- ordinary destruction follows the corresponding reverse order; and
- lifecycle-specific facilities may control some exact construction or
  destruction timing without allowing an instance to escape the path.

A life path may contain several instances and may create nested life paths. Some
life paths are lexical or structural. Others are dynamically owned and may be
transferred or shared among owning handles. A dynamically owned path ends when
its last owner is destroyed, evicted, or transfers away ownership without
leaving another owner.

Use **owning handle** when discussing ownership generally so that an owning
pointer is not confused with Zax's non-owning `&` reference.

**Aligned finding: instance place.** An **instance place** is the stable, typed
position within a life path through which a live instance is reached. After the
first explanation, documentation may shorten this to **place**.

A final instance place hosts one instance lifetime. A varying instance place may
host successive instance lifetimes without the place itself ending. Ordinary
access requires a complete resident instance in the place.

**Aligned finding: resident instance.** The resident instance is the completely
constructed value presently available through an instance place. Construction,
destruction, or reconstructive replacement may temporarily leave a continuing
place without an ordinarily accessible complete instance.

**Aligned finding: allocation is separate.** Allocation provides physical
storage for one or more life paths and instance places. It does not directly
create an instance. Allocation may precede construction and remain after
destruction. Stable address or retained bytes do not by themselves establish
that a life path, place, or instance remains valid.

The core relationship is:

```text
allocation or enclosing storage
-> life path
-> instance place
-> resident instance
```

This is a teaching relationship, not a required runtime representation. An
instance place need not add a wrapper object, allocation, hidden state, or
indirection in generated code.

#### Where life paths arise

The mental model applies uniformly:

| Source of a life path | Aligned lifetime meaning |
| --- | --- |
| Global execution | Global instances live within the process life path. Global source executes in a defined order as if one conceptual function enclosed the global space. Zax has no intrinsic `main`; platform libraries expose arguments, environment, and similar startup information. |
| Namespace | A namespace supplies a path within the global path. Its path is tied to global lifetime and is destroyed before the global unnamed-namespace path. |
| Flow scope | Blocks, branches, loops, and other flow constructs create bounded paths. No contained instance survives the path, though an independently owned nested path may be transferred to a longer-lived owner. |
| Type instance | Composition creates contained instance places and nested paths. Members ordinarily construct in declaration order and destruct in reverse order, subject to the controlled lifecycle facilities of constructors and destructors. |
| Function invocation | Parameters and result places live within the complete invocation path, including the caller-visible result-mapping boundary. |
| Lambda capture | A capturing lambda owns a capture path tied to the lambda instance. Captured instances construct in capture order and destruct when that path ends. |
| Expression temporary | An expression may create a temporary path to fill a value hole. Its instances live until the complete use or transfer that required them finishes. |
| Optional | An optional instance can create and end a nested single-instance path. Presence means that nested path has a live payload instance; absence means it does not. |
| Array or collection | An array owns a multi-instance path. A dynamic array may create or end element paths as it expands, contracts, removes, or relocates elements. |
| Allocation or arena | An allocated envelope owns storage in which one or many paths and instances may live. Every contained instance must end before the enclosing allocation path ends. |
| Owning or shared pointer | The pointer instance lives in its own path while its ownership contract may preserve another dynamically owned path. Transfer or sharing changes who keeps that path alive. |
| Raw pointer | The pointer instance still has an ordinary lifetime, but it does not preserve or dispose the path of its pointee. |
| Reference | The reference is an instance in its own path. It borrows one target instance place and neither creates nor destroys the referenced path. |

The global, namespace, broad collection, and complete allocation details may
ultimately belong outside the lasting reference owner. They remain necessary
here because they establish the one mental model from which reference validity
must be taught.

### Permanent reference binding

**Aligned hard rule:** a reference is bound to exactly one instance place when
the reference is constructed. That binding never changes during the reference's
lifetime. This is a permanent language rule, not an initial default or a
strategy that later work may relax.

```text
reference target instance place = permanently fixed
resident instance in that place = may change when the place is varying
```

No safe operation, unsafe assertion, transfer stance, cast, pointer conversion,
capture, parameter mapping, or result mapping may rebind an existing reference.
Unsafe source may assert an unproved validity fact; it cannot change what a
reference means.

Constructing another reference to the same target is not rebinding:

```zax
source : MyType
first : MyType & = source
second : MyType & = first
```

`first` and `second` are separate reference instances bound to the same instance
place. Passing, returning, or capturing a reference may construct another
reference carrying that same fixed target and origin.

An optional or collection may destroy one contained reference and later
construct another bound to a different target. Those are distinct reference
lifetimes, not rebinding one reference. Pointer-to-reference and other
addressable handle compositions must preserve the same rule.

Assignment through a reference operates on the referenced place:

```zax
view : MyType writable & = source
view = replacement
```

It does not assign a new target into `view`. Pointers are different: changing a
pointer's value may repoint that pointer instance to another place or to
`Nothing`.

### Varying places and reconstructive replacement

**Aligned finding:** a reference to a varying declaration denotes that
declaration's stable outer instance place, not only the current in-place
incarnation.

```zax
value varying :
  MyType immutable writable varying = makeMyType("first")

view final :
  MyType immutable readonly varying & = value

value = makeMyType("second")
inspect(view) // observes the complete second instance
```

`view` was not rebound. It remains attached to `value`'s instance place while
that place receives a successor instance. This is ordinary varying-place
behavior, not a separate place-tracking reference policy.

Type-side `varying` records that the referenced place may host successive
instances. Declaration-side `varying` separately says whether a particular path
may initiate replacement. The reference's fixed binding does not change either
truth.

A varying place and its resident instance must not be conflated:

- replacement may end the resident instance without ending the place;
- ordinary access is unavailable while the place has no complete current
  instance;
- successful replacement establishes a complete successor before ordinary
  access resumes; and
- ending the place's own life path invalidates every reference bound to it.

### Direct members, nested places, and pointees

**Aligned finding:** a direct member has its own instance place. A reference to
that member is permanently bound to the member place, not to the enclosing
object as a whole.

```zax
record : MyRecord
countView : Integer readonly & = record.count

record.count = 5
inspect(countView)
```

Content change alone is not a safety violation. `readonly` prevents change
through `countView`; it does not promise that another authorized path will never
change what the reference observes.

Safe use after an enclosing replacement depends on actual lifecycle facts:

1. the referenced member place still exists;
2. a complete current member instance has been established there;
3. no access occurs while that member is in transition; and
4. the reference retains sufficient access permission.

**Aligned finding:** complete replacement renews every direct member instance.
This applies whether replacement uses a compiler-provided operation or a custom
replacement constructor. A custom replacement may reuse storage or transfer and
retain resources, but it does not preserve the old member instance lifetime.

An existing member reference therefore crosses a known logical renewal
boundary:

```zax
record varying : MyRecord immutable writable varying
countView : Integer readonly & = record.count

record = makeReplacement()
inspect(countView) // error: the referenced member was renewed
```

This remains a safe-language error even when the member has a trivial type, the
address is unchanged, and low-level access would be technically viable. A narrow
unsafe permission may allow the programmer to use the fixed member-place
reference to reach the completely established successor instance. That unsafe
operation accepts the logical renewal boundary; it does not claim that the old
member instance survived.

Static analysis may recognize whether storage, representation, completion, and
access requirements make such an unsafe use technically viable. Zax does not
currently promise one universal shortcut for every type classified as trivial.
Improved analysis may reduce the proof burden without making cross-replacement
member access ordinary safe behavior.

A reference to a pointer member and a reference through that pointer have
different origins:

```zax
pointerView : MyValue * readonly & = owner.pointer
pointeeView : MyValue readonly & = owner.pointer.
```

- `pointerView` is bound to the pointer member's instance place. It may observe
  the pointer value being repointed while remaining bound to the same pointer
  member.
- `pointeeView` is bound to the pointee's instance place. Repointing
  `owner.pointer` does not retarget `pointeeView`; its old pointee origin still
  needs an independent lifetime proof.

Pointer-value transfer likewise does not imply transfer of the pointee or its
life path. An owning pointer may transfer that path only according to its
explicit ownership contract.

#### Container-specific continuity

An optional wrapper place continues while its boxed path begins and ends.
Transition from present to absent ends the boxed instance and its life path.
Later presence creates a new boxed path; a reference to the old payload does not
automatically follow it. A reference to the optional wrapper itself may remain
valid.

An immutable or fixed-shape array keeps its element places stable for the array's
life path. A dynamic array's operation contract determines which element places
continue:

- an in-capacity operation may preserve existing elements;
- contiguous growth may allocate new storage, `last`-transfer elements, and
  destroy every original element;
- a segmented implementation may relocate only affected segments; and
- removal or contraction may end selected element paths without ending the
  array wrapper.

These stability guarantees are programmer-visible behavior, not an
implementation detail. Two array types may deliberately provide different
reference-stability and cost contracts.

A managed active-alternative type behaves like an optional generalized to
several alternatives: one alternative path is present and the others are
absent. Changing alternatives ends one path and begins another. **Candidate
naming:** use `variant` for this managed form and reserve `union` for an
unmanaged overlay that views the same storage through different type lenses.
The unmanaged form would be restricted to suitable trivial types or explicit
manual `+++`/`---` lifecycle operations under unsafe responsibility. Final
syntax and terminology remain open.

Every optional, array, variant, union, or other contained path ends no later than
its container path.

An arena is different: it supplies allocation backing in which independently
constructed life paths may exist. The arena storage must outlive every instance
using it. Arena teardown either performs the destruction required by the
selected allocation disposition or detects outstanding dependencies and
panics; releasing or repurposing storage while an instance still needs it would
otherwise have undefined consequences.

### Replacement transition and reentrancy

**Aligned finding:** half-complete states are not ordinarily observable outside
the lifecycle operation establishing or dismantling them.

Construction, destruction, and reconstructive replacement place the affected
instance places under exclusive lifecycle transition authority. Constructor,
destructor, and replacement code may access the portions permitted by their
tracked lifecycle state. Ordinary aliases may not observe an incomplete current
instance.

Reentrancy creates the concrete pressure:

```zax
replacement +++ final : ()(
  source : Source
) = {
  _.first = source.first
  notifyObservers()
  _.second = source.second
}
```

If `notifyObservers()` may reenter through an alias to `_`, it could attempt to
observe the containing instance before `second` is established. Safe analysis
must prove that the call cannot expose an invalid partial state or reject the
operation.

When analysis cannot prove a genuinely valid case, a narrow unsafe assertion
must remain available. Relevant claims may include:

- a helper accesses only already-live members;
- an opaque call does not retain or publish the partial instance;
- a callback cannot reenter through an alias;
- a replacement preserves a referenced place; or
- a complete successor is established before the asserted use.

These are distinct claims and should not collapse into broad unsafe mode. If the
compiler knows that a use reaches a place with no live complete instance, an
unsafe assertion cannot make that known violation valid.

Unsafe control must also distinguish an **assertion** from a **permission**:

- an assertion states that a required fact is true even though analysis cannot
  prove it; and
- a permission deliberately crosses a safe-language boundary that has a defined
  lower-level interpretation, such as accessing a completely established
  successor through a member-place reference retained across replacement.

Neither form may authorize access while the place has no complete current
instance. Exact categories and source syntax should be informed by later
compiler experiments rather than inventing a complete proof calculus here.

Zax has no exception-style rollback model. If a future nonfatal panic model
permits execution to continue after a partial transition, it must separately
define how completion obligations are restored before ordinary access resumes.

### Parameter and result lifecycle

#### Parameters and defaults

**Aligned finding:** the implementation body depends on every parameter being
constructed before body entry, not on one universal chronology for all
parameter construction.

The caller:

1. evaluates explicit value-producing inputs in caller source order;
2. immediately constructs or binds their mapped parameters;
3. evaluates omitted defaults afterward in parameter declaration order; and
4. enters the body only after every parameter is complete.

A parameter default may depend on earlier parameters:

```zax
func final : ()(
  a : Foo,
  b : Foo = a,
  c : Foo = b,
  d : Foo = a
) = {
}
```

The default order respects those dependencies. Explicitly supplied arguments do
not need artificial temporaries merely to reproduce parameter declaration
order. A prototype may redefine defaults while reusing an implementation body
because the body receives the completed parameter state promised by each
compatible prototype.

#### Result constructedness is part of the prototype signature

**Aligned finding:** a prototype establishes the lifecycle state in which each
return-result place enters the implementation body.

A result default means that the result is constructed as part of
prototype-directed invocation setup:

```zax
foo final : (
  result : Foo = input
)(
  input : Foo
) = {
  // `result` is already constructed on body entry.
}
```

The caller allocates result storage before invocation. After parameters are
established, the caller performs every result construction required by the
prototype before entering the body. The body may assume those result instances
are live.

Another result may deliberately enter the body unconstructed so that the body
controls construction through explicit `+++`, `return`, or another accepted
result-completion path.

The per-result distinction:

```text
preconstructed before body entry
versus
implementation body must construct
```

is part of prototype compatibility. A body that expects one state is not
compatible with a prototype that supplies the other, even when the result types
otherwise match. Different prototypes may provide different default
expressions while sharing a body only when they establish the lifecycle state
that body expects.

Every result must still be constructed exactly once on every normal completion
path and destroyed exactly once at the end of its invocation/result-mapping life
path.

#### Result holes and construction forwarding

An enclosing expression may allocate one result place before a nested call
chain:

```zax
result := outer(inner(evenMoreInner()))
```

Construction responsibility for the eventual result may be forwarded deeper
through result holes and compiler elision. `outer`, `inner`, `evenMoreInner`, or
another call used by one of their return paths may ultimately construct the one
destination instance.

This is not adequately modeled as every call necessarily constructing an
independent temporary followed by moves. The language-level obligations are:

- one suitable destination place is available;
- one operation in the accepted chain constructs its resident instance;
- every intermediate callable respects its result contract;
- the complete consumer receives the constructed value; and
- the temporary path, when one exists, ends after its complete use or transfer.

#### Completion and destruction order

**Aligned starting rule:** after the implementation body has completed every
result and result mapping has finished:

1. source result slots are destroyed in reverse result declaration order;
2. parameter instances are destroyed in reverse parameter declaration order;
3. caller-side argument, receiver, and nested-result temporaries are destroyed
   in reverse construction order when their complete consumer no longer needs
   them.

Body-local instances still destruct in reverse construction order as their
scopes exit.

This keeps result slots alive through complete mapping, parameters alive while
that mapping may still depend on their origins, and caller temporaries alive
through every invocation object that borrows them. An inner result temporary
bound to an outer reference parameter survives through the outer synchronous
call and is destroyed only after that complete consumer finishes.

Later concrete pressure may refine this starting rule, but cleanup order must
remain deterministic and programmer-visible.

### Reference origin, forwarding, and escape

**Aligned finding:** reference origin identifies the fixed target instance place
and the life path that must outlive every use. It is semantic provenance, not
necessarily runtime metadata.

Ordinary local references may be compiled as raw addresses after successful
proof. The compiler still reasons about:

- the root life path;
- the projected instance place;
- lifecycle transitions affecting that place;
- access and replacement permission;
- capture or storage duration; and
- every boundary through which the reference is forwarded.

Reference parameters construct local reference instances bound to caller
places. The callee receives no new ownership:

```zax
inspect final : ()(
  input : Document readonly &
) = {
}
```

The caller must preserve `input`'s target through the complete synchronous call.
An argument temporary bound to a reference survives through body execution and
result mapping:

```zax
inspect(makeDocument())
```

That extension does not automatically validate an escaping result:

```zax
view := returnView(makeDocument())
```

A returned reference carries the same fixed target place as its source
reference. Compiler origin analysis must prove that the target life path
outlives every later use. A local or temporary result origin cannot become safe
merely because a reference result slot was mapped successfully.

Nested immediate use may remain safe when analysis can preserve the origin
through the complete outer synchronous consumer:

```zax
inspect(returnView(makeDocument()))
```

**Aligned direction:** Zax does not currently need programmer-written
Rust-style lifetime parameters for returned references. The compiler statically
tracks the target place and projected life path through the concrete body.
Returning `_`, an input-origin place, or a global place is commonly provable.
Returning a local or temporary place is rejected when it would escape.

Separate or opaque prototypes may eventually require inferred semantic metadata
or a conservative origin contract so body/prototype compatibility and callers
remain sound. That pressure does not currently justify exposing lifetime syntax.
An unproved but valid result origin may use the same narrow unsafe responsibility
as other lifetime analysis.

**Aligned finding:** lambda capture defaults to `copy`, including when the
captured name is a reference. Capturing a reference name by default copies the
underlying referenced value into the lambda's capture path; it does not capture
the reference handle:

```zax
source : Document
view : Document readonly & = source

callback := [view] {
  inspect(view) // observes the captured Document copy
}
```

If the referent is not copyable, default capture is unavailable. The programmer
may select another viable transfer stance or explicitly request reference
capture.

Explicit reference capture constructs another reference with the same fixed
target. It does not extend or own the target path. The capture is safe only
while static analysis proves that an independent guarantee keeps the target
place alive and complete for every invocation. Exact reference-capture syntax
and its callable-type expression remain open.

An optional reference owns a conditional reference instance, not the referenced
path:

```zax
view : MyValue readonly & ?
```

Reset ends the contained reference lifetime. It does not destroy the target.
Later construction may place a different reference in the optional, but that is
a new reference lifetime rather than rebinding the old one.

A same-storage identity reference similarly creates no independent identity
instance lifetime or destruction. It forwards the underlying target place and
must preserve its origin and permissions.

Generic functions require no separate lifetime model. Each concrete generic
realization follows the same origin, reference, lifecycle, transfer, and safety
rules as the equivalent nongeneric function.

### Lifetime ownership and strategy plurality

**Aligned finding:** multiple lifetime strategies remain intentional because
life paths may require different ownership, sharing, runtime, memory,
synchronization, and compile-time costs. This plurality does not create several
meanings for reference binding.

An ordinary reference is the cheap non-owning mechanism:

- non-null while live;
- permanently bound to one instance place;
- no ownership transfer;
- no independent extension of the target life path; and
- validity established through static proof or a narrow unsafe assertion.

#### Arena-backed dynamic allocation

**Aligned finding:** every dynamic allocation receives storage from an arena.
The ordinary process heap is one arena; programs may provide other arenas. The
allocation selects an object arena and may separately select an arena for its
ownership control block.

Allocation disposition has three independent decisions:

1. destroy the resident instance promptly when ordinary ownership closes, or
   delay destruction until arena teardown;
2. return the backing block promptly for arena reuse, or retain it until arena
   teardown/reset; and
3. disable cycle probing, or emit collectable metadata and permit explicit
   collection of unreachable strong-reference cycles.

Delayed destruction requires delayed storage recovery. Storage cannot be reused
while its resident instance remains live.

| Allocation behavior | Destruction | Storage recovery | Cycle probing |
| --- | --- | --- | --- |
| Normal | Prompt at ordinary ownership closure | Prompt arena reuse | Disabled |
| Deferred recovery | Prompt at ordinary ownership closure | Arena teardown/reset | Disabled |
| Attached lifespan | Arena teardown | Arena teardown/reset | Disabled |
| Collected normal | Prompt at ordinary closure or cycle eviction | Prompt arena reuse | Enabled |
| Collected deferred recovery | Prompt at ordinary closure or cycle eviction | Arena teardown/reset | Enabled |
| Collected attached | According to the attached policy after closure or eviction | Arena teardown/reset | Enabled |

Cycle collection augments strong reference counting rather than replacing
ordinary ownership. When collection identifies an unreachable cycle, eviction
applies the allocation's selected destruction and storage-recovery policy.
Compiler-generated root/edge metadata is required only when reachable build
output uses collectable allocations. Memory pressure is a program-visible
library/arena event; the programmer chooses if and when collection runs.

**Deferred-recovery allocation** is the aligned concept, not an accepted
`discard` keyword. It preserves the useful legacy behavior of prompt instance
destruction with arena-wide storage recovery and may be combined with `unique`
or shared ownership.

The allocation disposition is chosen visibly at allocation but does not
currently become another pointer qualifier. A common owning-pointer contract
therefore cannot promise that final release always causes immediate destruction.
APIs that require prompt resource release may eventually need a stronger
allocation capability or an explicit resource-closing operation.

An arena is not inherently thread-safe. It declares whether allocation, release,
and teardown are thread-confined, synchronized, movable, or queueable to an
owning thread or executor. Separate instance affinity rules constrain operation
and final destruction.

The language manages allocation records, lifecycle registration, control blocks,
retained lists, collection metadata, and teardown validation over the arena's
basic backing-storage interface.

The underlying arena request may fail. A panicking allocation operator panics,
a non-panicking form produces `Nothing`, and an unchecked form makes a false
success guarantee undefined. Automatic allocation during stack/global/container
construction follows the selected operator's policy.

Arena teardown:

1. destroys attached-lifespan instances in the arena's defined order;
2. performs any required collector and retained-storage disposition;
3. detects externally owned allocations or other dependencies that would
   outlive the arena;
4. panics rather than releasing backing storage while such dependencies remain;
   and
5. releases storage only after contained destruction is complete.

Ending or repurposing arena storage while a dependent live instance remains
would otherwise have undefined consequences.

#### Pointer ownership vocabulary

The aligned pointer axes are ownership role and shared-accounting atomicity.
The aligned source forms are:

```zax
raw : Foo *
sole : Foo * unique
prepared : Foo * unique shareable
shared : Foo * strong
sharedAcrossThreads : Foo * strong atomic
observer : Foo * weak
observerAcrossThreads : Foo * weak atomic
```

`shareable` is the aligned spelling. It means that the unique owner has a
reserved control block and may enter either local or atomic shared ownership
without allocating that block.

- `unique` has one owning pointer and no control-block overhead.
- `unique shareable` still has one owner but reserves an exclusively controlled,
  dormant control block.
- `strong` participates in local shared ownership.
- `weak` observes local shared ownership without preserving it.
- pointer-layer `atomic` makes strong/weak lifetime accounting and acquisition
  cross-thread capable.
- raw pointers own nothing and preserve no life path.

Atomicity has no meaning during the unique phase because no shared accounting is
active. There is therefore no separate `unique shareable atomic` type. When a
shareable unique owner enters a `strong` or `strong atomic` destination, that
destination initializes the dormant block in the selected mode.

Pointer-layer `atomic` does not make the pointee's operations thread-safe and
does not by itself define an atomic cell for concurrent mutation of one pointer
variable.

#### Control-block placement and arenas

A shareable allocation may place its control block inline with the instance or
in a separately selected control-block arena.

An inline block permits one combined allocation but keeps that allocation
reserved while weak observers remain. Destroying the object arena while an
external weak pointer still requires the inline block causes a detectable panic.

A detached block permits the object instance and its arena to end while weak
pointers continue to observe permanent termination. The control-block arena must
then outlive every such weak pointer; destroying it too early causes the
corresponding panic.

When ordinary strong ownership reaches zero:

- the ownership lifetime closes permanently;
- every weak acquisition thereafter fails;
- normal and garbage-collected acyclic allocation destroy immediately;
- attached-lifespan allocation may retain the physical instance until arena
  teardown; and
- delayed physical destruction never permits weak ownership to resurrect the
  instance.

The control block must support:

- entry into local strong/weak accounting;
- entry into atomic strong/weak accounting;
- return to exclusive dormant state after uniqueness is established;
- and permanent target termination.

Custom control-block implementations may satisfy those operations differently.
Exact allocation and customization syntax remains later pointer/arena work.

#### Anchored interior owning pointers

**Aligned finding:** `anchored` is a pointer-layer qualification for an owning
pointer whose direct-member target differs from the allocation root owned by its
control block.

```zax
container : Container * strong = ...

member : Item * strong anchored =
  container.item anchored by container
```

`anchored by` is the aligned binary operator phrase. It:

1. requires the target to be a statically recognized direct contained member of
   the right operand's allocation-root resident instance;
2. keeps the target member place as the pointer's access target;
3. copies participation in the right operand's control block; and
4. increments that same strong count.

The operation cannot cross an optional payload, pointer, reference, variant
alternative, dynamic array element, unmanaged overlay, or separately allocated
member. Exact eligibility for a deeper uninterrupted chain of direct composition
remains deferred.

The explanatory terms are **anchored strong pointer**, **anchored weak pointer**,
and **ownership anchor**:

```zax
observer : Item * weak anchored = member
restored : Item * strong anchored = observer
```

Weak acquisition retains the same member target and ownership anchor. Atomicity
remains orthogonal:

```zax
sharedMember : Item * strong anchored atomic
weakMember : Item * weak anchored atomic
```

An anchored pointer can keep the container allocation and place alive while
complete replacement renews the target member's resident instance. When the
target or an enclosing direct place can undergo such replacement, creating the
anchored pointer is a defined but surprising operation and causes an intent
error. The acknowledgement confirms that later access may observe a successor
member:

```zax
intent<anchored-pointer-tracks-replacement>{
  member : Item * strong anchored =
    container.item anchored by container
}
```

The category identifier is provisional. Replacement itself remains available,
and ordinary aliases cannot observe a half-complete successor during the
transition.

An anchored pointer cannot become `unique`:

```zax
owner : Item * unique = member as last
// error: the target is not the control block's allocation root
```

This is statically unavailable rather than a conditional empty-pointer result.
The control block still records the container allocation root, destructor,
arenas, and disposition. Releasing the final anchored strong pointer therefore
destroys or schedules disposition of the complete container according to that
allocation contract.

An unsafe ownership-anchor operation may remain future work. It must not weaken
the ordinary direct-containment restriction or manufacture validity across a
conditional or relocated target path.

#### Destination-directed ownership transitions

Pointer ownership conversion uses the destination type and ordinary transfer
stances rather than a separate family of conversion keywords.

Claiming unique ownership from shared ownership is conditional:

```zax
plain : Foo * unique = myStrongPointer as last
prepared : Foo * unique shareable = anotherStrongPointer as last
```

The claim succeeds only when exactly one strong owner and no weak observers
remain. An atomic source claims that state atomically.

On success:

- the source strong pointer becomes vacant;
- the destination receives unique ownership;
- a `unique` destination sheds the control block; and
- a `unique shareable` destination retains it in dormant exclusive state.

On failure:

- the destination is a valid empty unique pointer that points to `Nothing`;
- the source retains its original strong ownership and control block;
- the source remains in terminal state because it was used `as last`; and
- ordinary source destruction later releases that retained strong ownership.

A detached block can be physically released when it is shed. An inline block is
logically retired but its coallocated bytes generally remain until the instance
allocation ends. Recovering those bytes would require a separate visible
relocation.

A shareable unique owner enters either shared mode by ordinary transfer:

```zax
localShared : Foo * strong = prepared as last
atomicShared : Foo * strong atomic = anotherPrepared as last
```

The source becomes vacant and the reserved block enters the destination's
accounting mode. A shareable unique owner may also shed its block and become a
plain unique owner.

A blockless `unique` cannot directly initialize `strong` because the required
capability is absent. It first performs an explicitly allocation-bearing
operation that creates a control block, optionally in a selected control-block
arena, and produces `unique shareable`. Exact source syntax remains open.

#### Weak probing and acquisition

Every pointer supports a `?` presence operation, but the guarantee depends on
the pointer kind:

| Pointer kind | Meaning of `?pointer` |
| --- | --- |
| `unique` | This pointer currently owns a target |
| `strong` | This pointer currently owns a target |
| `weak` | At the instant of the non-owning probe, strong ownership remained open |
| Raw | The stored address is not `Nothing`; no lifetime or validity proof |

`if ?weakPointer` does not create strong ownership, extend lifetime, or make the
weak pointer dereferenceable. A successful atomic weak probe may become stale
immediately. A failed probe is permanent for that ownership lifetime because
weak ownership cannot resurrect a target after strong ownership closes.

Actual weak acquisition uses destination-directed `copy`:

```zax
owner : Foo * strong atomic = weakPointer
```

The operation leaves the weak source unchanged, atomically acquires compatible
strong ownership when still possible, and constructs an empty strong pointer on
failure. Local weak acquires local strong; atomic weak acquires atomic strong.
Crossing between local and atomic shared families requires the exclusive
`unique shareable` bridge.

Creating a dynamic path known only through a raw pointer remains inherently
unsafe because losing or repointing that pointer can leave no owner able to end
the path. A raw-pointer operation is nevertheless safe when analysis proves its
presence, pointee life path, resident instance, provenance, alignment, and access
requirements. A containing type's declared lifecycle may own an automatically
allocated nested path while a raw member merely accesses it; that is a
representative provable case and does not make the raw pointer an owner. Escape
may invalidate the proof. Raw non-`Nothing` state alone proves neither a live
pointee nor valid provenance.

#### Thread boundary

Ordinary references do not safely cross thread boundaries. Raw pointers may
cross when pointee, arena, provenance, and affinity requirements are proved;
otherwise the boundary requires programmer-owned unsafe responsibility.

A unique pointer may cross by transferring its sole ownership when the instance
is permitted to move between threads. Local shared ownership crosses by first
claiming `unique shareable`, transferring that unique owner, and then entering
`strong atomic` on the destination thread. Exactly one strong owner and no weak
observers are required before the bridge.

Atomic lifetime machinery does not imply pointee data-race safety. A future
thread-oriented copy or preparation stance, instance affinity, destructor
affinity, and arena recovery synchronization remain deferred.

### Transfer and terminal authority

`move` and `last` change resource authority and source state; they do not by
themselves end the outer instance place or rebind a reference.

A reference-shaped `move` or `last` parameter binds caller-owned storage for one
synchronous consumer. Named destructive reference use returns to `copy` unless
the body explicitly renews the stance:

```zax
consume final : ()(
  input : MyValue mutable writable & last
) = {
  inspect(input)
  transfer(input.resource as last)
}
```

The reference remains permanently bound to `input`'s caller place. It does not
carry terminal authority into a successor instance established later in that
place.

**Aligned finding:** terminal authority never escapes implicitly, but explicit
renewal may store or forward it:

```zax
consume final : ()(
  input : Resource mutable writable & last
) = {
  stored : Resource mutable writable & last = input
  // error: named `input` ordinarily offers `copy`

  explicit :
    Resource mutable writable & last = (input as last)
  // viable when independent reference-lifetime analysis also succeeds
}
```

The explicit declaration constructs another reference bound to the same
instance place; it does not rebind `input`. Its target must outlive the stored
reference, and every later destructive use must explicitly renew `as last`
again because named `move`/`last` references ordinarily offer `copy`.

`last` grants maximum terminal authority. It does not promise that the consumer
physically removes every resource. A failed lifetime proof remains a lifetime
error independently of the explicit stance.

Moving a pointer value does not move its pointee unless the pointer's ownership
contract says the owned life path transfers. A temporary reference-result slot
does not make its target terminal.

**Aligned disposition:** legacy `lease` no longer has a distinct language role.
Ordinary non-owning references, fixed reference binding, synchronous parameter
lifetime, pointer ownership axes, copy-default named use, and explicit
destructive renewal supersede its useful behavior. Focused legacy review should
preserve evidence but must not retain `lease` as a current concept.

### Safety proof, unsafe responsibility, and lints

**Aligned finding:** lifetime safety uses four categories.

1. **Proven safe.** The selected contract's required facts are established; no
   unsafe assertion is needed.
2. **Not proved but not known invalid.** A narrow unsafe assertion may allow the
   programmer to accept responsibility for a genuinely valid case the compiler
   cannot prove.
3. **Defined unsafe permission.** The compiler understands the lower-level
   operation but the safe language deliberately excludes it, such as using a
   member-place reference across complete enclosing replacement after proving a
   suitable successor is established.
4. **No valid interpretation.** The compiler knows that no live suitable place or
   complete instance exists, or the operation contradicts a hard semantic rule.
   Unsafe source cannot manufacture a valid meaning.

The threshold for the fourth category is intentionally high. Conservative
analysis belongs in an assertable or permitted unsafe category rather than being
presented as universal invalidity.

Unsafe responsibility belongs at the narrow operation, use, or contiguous
region whose proof is missing. It does not:

- create ownership;
- extend a life path;
- rebind a reference;
- revive an ended place;
- create a missing resident instance; or
- turn known partial-state observation into valid ordinary access.

A false unsafe lifetime, presence, alias, or transition assertion may have
undefined consequences without a required runtime check. Debug instrumentation
may detect and panic on misuse, but that is tooling rather than a language
guarantee.

A surprising observation can be safe:

```zax
inspect(countView) // may observe a value changed through another legal path
```

When the target place and resident instance remain valid but the observation is
likely unintended, a lint may ask the programmer to confirm intent. That is not
an unsafe-lifetime diagnostic.

#### Safety analysis and source evolution

**Aligned finding:** compiler improvement and language-contract advancement are
different.

- A newer compiler may prove more under an existing contract.
- An unsafe assertion made redundant only by that compiler improvement is at
  most advisory under a contract that did not require the proof.
- Removing it may make source depend on that compiler's stronger analysis.
- A later explicitly selected language contract may require every conforming
  compiler to recognize the case as safe.
- Under that later contract, retaining the obsolete assertion may be an error.
- A compiler that demands an assertion for a case the selected contract requires
  it to prove is nonconforming.

This rule applies to every mandatory safety analysis, not only references or
lifecycle transitions.

The pre-promotion documentation fit dry run must verify that
`project/raw/safety.md` and `project/raw/analysis-controls.md` preserve and
adequately explain:

- compiler improvement versus contract advancement;
- advisory redundant assertions versus contract-level errors;
- explicit adoption of a newer contract;
- portability consequences of removing an implementation-redundant assertion;
  and
- the general application of this rule across all mandatory safety analysis.

### Explicit deferrals

The following questions do not invalidate the aligned reference model. Each has
a live destination, activation pressure, and current constraint.

| Deferred concern | Live destination | Reactivation pressure | Constraint preserved now |
| --- | --- | --- | --- |
| Per-operation dynamic-array and segmented-collection stability | `project/raw/indexing-and-slicing.md` | Array resizing, insertion, removal, segmentation, slicing, or iterator/reference guarantees are reviewed | Every operation exposes which element places survive and which are relocated or ended |
| Managed alternatives and unsafe overlay storage | `project/raw/variants-and-unions.md` | Variant, union, active-member, or overlay-memory design begins | Managed alternatives have one live path; unsafe overlays cannot manufacture lifecycle validity |
| Exact unsafe assertion and permission categories | `project/raw/safety.md` and `project/raw/analysis-controls.md` | Compiler experiments establish useful proof boundaries or safety contracts are formalized | Safe code never observes incomplete state; unproved valid and defined unsafe cases remain narrow and explicit |
| Opaque returned-reference origin metadata | `project/raw/callable-selection.md` | Separate compilation, opaque bodies, FFI, function values, or exact prototype matching needs origin contracts | Concrete bodies use static origin analysis; no origin is invented when proof is unavailable |
| Explicit reference-capture syntax and callable representation | `project/raw/function-composition-and-chaining.md` | Lambda, closure, capture, callable-layout, or repeated-invocation design begins | Capture defaults to referent `copy`; explicit reference capture preserves the original target and must be proved safe |
| Pointer allocation, failure operators, and control-block syntax | `project/raw/pointer-and-arena-mechanics.md` | Allocation grammar, panicking/non-panicking/unchecked operators, custom control blocks, allocator APIs, or pointer construction is designed | Ownership role, `shareable`, atomicity, disposition, and block placement remain separate visible choices |
| Deeper/unsafe ownership anchors and allocation provenance | `project/raw/pointer-and-arena-mechanics.md` | Nested direct projection, unsafe `anchored by`, allocation-relative aliases, or provenance conversion is designed | Ordinary anchoring crosses only a statically recognized direct contained-member boundary |
| Dynamic-array relocation provenance | `project/raw/pointer-and-arena-mechanics.md` plus `project/raw/indexing-and-slicing.md` | Array relocation, stable iterators, or owning element views are designed | An ownership control block cannot silently preserve a relocated or ended element place |
| `Nothing` resident instances and pointer representation | `project/raw/nothing-instances.md` | Empty-pointer dereference, monitored/custom `Nothing`, generated `_` checks, compatible overlap, or nullable representation is reviewed | `?` proves only the guarantee assigned to the selected pointer layer; raw non-`Nothing` does not prove validity |
| Arena teardown and memory-recovery syntax | `project/raw/pointer-and-arena-mechanics.md` | Arena interfaces, block recovery, retained storage, or manual deallocation is designed | Instance destruction and storage recovery remain separate; deferred recovery means prompt destruction with delayed arena recovery |
| Cycle collection mechanics | `project/raw/pointer-and-arena-mechanics.md` | Collectable allocation is implemented or specified | Metadata is emitted only when reachable use requires it; programmer-triggered collection may break unreachable strong cycles |
| Thread affinity and pointer release | `project/raw/async.md` plus `project/raw/pointer-and-arena-mechanics.md` | Cross-thread ownership, affinity, executor-bound destruction, or allocator recovery is designed | Arenas declare concurrency capability; references do not safely cross threads; atomic ownership does not imply pointee thread safety |
| Async reference and result lifetime | `project/raw/async.md` | Suspension, cancellation, delayed result mapping, or executor movement is designed | Current temporary extension and reference guarantees end at synchronous completion |
| APIs requiring prompt disposition | `project/raw/pointer-and-arena-mechanics.md` | A resource API depends on immediate destruction rather than eventual allocation-policy disposition | Ordinary `strong` cannot promise prompt destruction across all allocation modes |

Reference rebinding is not an open question. Existing references never rebind.
Generic realizations use ordinary concrete analysis. Temporary destruction has
an aligned starting order. Explicit `last` may carry terminal stance into a new
reference when lifetime analysis permits. Legacy `lease` is superseded.

### Promotion and teaching obligations

The life-path and instance-place model is a fundamental programmer teaching
requirement, not merely internal reasoning for this work item.

Later promotion must:

- explain life paths, instance places, resident instances, and allocation before
  relying on them to teach references;
- use concrete examples spanning lexical paths, dynamic ownership, optionals,
  replacement, members, pointers, temporaries, and invocation;
- distinguish reference binding from change of the resident instance in a
  varying place;
- explain locally at every linked entry point what an instance place is, rather
  than requiring readers to infer a newly invented term;
- avoid implying that an instance place requires runtime wrapper overhead;
- distinguish technically safe but surprising observation from actual lifetime
  invalidity;
- preserve the hard no-rebinding rule at every pointer/reference/optional layer;
- explain default referent-copy capture before explicit reference capture;
- teach weak probing separately from strong acquisition;
- expose allocation disposition and control-block costs without multiplying
  pointer ownership families;
- distinguish pointer-layer atomic accounting from pointee thread safety; and
- expose guarantees, failure, unsafe responsibility, and ownership cost rather
  than teaching only successful syntax.

A cohesive lifecycle/reference owner may be necessary because scattering the
mental model among qualifiers, construction, invocation, optionals, and pointers
would force programmers to reconstruct the foundation themselves. The exact
owner and structure remain for the required documentation-fit dry run.

Expected affected current owners include:

- `language/qualifiers.md` for place truth and replacement permission;
- `language/construction-and-destruction.md` for lifecycle transitions, member
  continuity, and partial state;
- `language/function-invocation.md` for parameter setup, result constructedness,
  temporary extension, and returned origin;
- `language/transfer-stances.md` for reference-shaped authority and terminal
  boundaries;
- `language/optional-values.md` for conditional paths and optional references;
- `language/identity-types.md` for same-storage views;
- future pointer/lifetime teaching for ownership strategies and provenance; and
- raw safety and analysis-control input for general proof-contract evolution.

Pointer and arena promotion must preserve the orthogonal axes established here:
ownership role, shared-accounting atomicity, allocation disposition,
control-block placement, and instance thread affinity. It must not revive one
named pointer family for every cross-product combination.

The legacy `lease` material should retire after its remaining evidence is traced
to fixed reference binding, pointer ownership, synchronous lifetime, or explicit
transfer stance.

### Discarded initial reconstruction

The remainder of this working record is the original candidate reconstruction.
It is retained as evidence and a checklist of pressures, but its reference-policy
model, recommendations, and review questions are discarded or superseded. No
finding promotes from this block unless it has been restated in the current
aligned foundation or explicit deferrals above.

#### Former review entry point

##### Former candidate model

A reference should carry a semantic **origin**, not merely an address. The origin
identifies:

- the storage place from which access began;
- the particular live value within that place, or an explicit choice to follow
  the place across successive values;
- any member, optional payload, identity, or other projection used to reach it;
- the access and replacement permission that the source path could delegate; and
- the lifetime strategy that makes continued access valid.

This suggests two independent decisions rather than one flat list of reference
kinds:

1. **What does the reference follow?**
   - An **exact-lifetime reference** names one particular value lifetime. It
     continues through content mutation but never starts naming a successor
     merely because that successor occupies the same address.
   - A **place-tracking reference** names one stable place and may observe
     successive complete value lifetimes established there. It does not follow a
     value copied or transferred to a different place.
2. **How is validity guaranteed?**
   - static proof and source restrictions;
   - retention or ownership that keeps an exact lifetime alive;
   - checked runtime state;
   - or a narrow unsafe assertion when the program knows a fact the required
     analysis cannot prove.

These axes must remain separate from mutable/immutable value truth,
final/varying place truth, writable/readonly access, declaration-side
replacement permission, and `copy`/`deep`/`move`/`last` transfer stance.

The current candidate recommendation is:

- ordinary `T &` should mean an exact-lifetime reference under the ordinary
  safe, statically proved strategy;
- following successor values at a place should require an explicit
  place-tracking choice whose final spelling is still open;
- a direct reference should remain non-null and non-rebindable;
- an optional reference, pointer, owning handle, or another explicit wrapper
  should represent a replaceable association; and
- alternate lifetime machinery should be visible wherever it changes cost,
  failure, allocation, synchronization, or cross-thread capability.

The exact-lifetime default makes the common local and parameter case cheap and
predictable. It also avoids making replacement elsewhere silently change which
value an existing reference denotes. A compiler may allow an old exact reference
to remain declared after its referent ends, but safe source may not use or escape
it afterward.

##### Former most important contradiction

Current reconstructive-replacement teaching says this ordinary reference
observes the successor value:

```zax
message varying : Message immutable writable varying = makeMessage("first")

observer final :
  Message immutable readonly varying & = message

message = makeMessage("second")
display(observer) // currently taught as observing "second"
```

That is place-tracking behavior. Elsewhere, a reference is described as stuck to
its original data, and the lifetime input requires a distinct exact-pointee
case. Type-side `varying` cannot resolve the contradiction: it reports the
truth that the underlying place may be replaced, and both an exact-lifetime
reference and a place-tracking reference must preserve that truth.

Under the candidate default, the unmarked `observer` above would instead be
unusable after replacement:

```zax
message varying : Message immutable writable varying = makeMessage("first")

observer final :
  Message immutable readonly varying & = message

display(observer)                 // observes the first lifetime
message = makeMessage("second")   // legal if observer has no later use
display(observer)                 // error: the exact referenced lifetime ended
```

An explicitly selected place-tracking form could observe `"second"`. Its syntax
must not be inferred from type-side `varying`, and it must define how access is
prevented while replacement has ended the old lifetime but not yet established
the new one.

##### Former decisions needing maintainer review first

1. Is the two-axis model—target identity separately from validity
   machinery—the right basis for the work?
2. Should ordinary `T &` use the exact-lifetime, statically proved default, with
   place tracking explicit?
3. Should the existing reconstructive-replacement observer example become an
   explicit place-tracking example rather than define all varying references as
   trackers?
4. Should direct references remain non-rebindable, leaving replaceable
   associations to pointers, optional references, and other wrappers?
5. Must every externally visible reference result expose an origin relationship
   in its callable contract rather than relying only on body inference?
6. Should `move` and `last` authority on a reference remain bounded to the
   selected synchronous consumer, with no implicit stored or returned terminal
   capability?

#### Places, value lifetimes, and origin

A **place** is storage capable of containing a value. A varying place may contain
a sequence of complete value lifetimes. Each construction or lifecycle-ending
replacement starts a new value lifetime even if the address and static type do
not change.

An origin is semantic provenance. It need not imply a fat runtime reference.
For a statically proved local reference, the compiler may erase all origin
metadata after validation. Other strategies may need a stable cell, generation,
owner, reference count, arena relationship, or another runtime representation.

The useful conceptual origin shape is:

```text
root place
-> projection path
-> exact incarnation or place lineage
-> delegated access and replacement permission
-> selected validity strategy
```

The projection path matters because an enclosing object and one of its members
do not necessarily have the same lifetime. A custom replacement may retain one
member's exact lifetime while ending another's. Reusing the same bytes without
retaining the language-level member lifetime does not preserve the old origin.

No ordinary conversion should launder origin:

- readonly access cannot become writable;
- declaration-side `final` cannot regain replacement permission through a
  parameter, result, or capture;
- a reference into a member cannot become a reference to the enclosing object;
- an exact-lifetime reference cannot silently start following a place;
- a reference into a copied or moved value does not follow resources into a new
  destination; and
- converting among eligible pointer and reference forms does not invent pointee
  validity or ownership.

#### What each reference target means

| Event | Exact-lifetime reference | Place-tracking reference |
| --- | --- | --- |
| Mutate contents within the current value lifetime | Still names that value and observes permitted mutations | Still names the current value at that place |
| Replace resources through accepted `move` | Outer lifetime remains; reference remains lifetime-valid but sees moved-from state and its operation restrictions | Same while that incarnation remains current |
| Consume through accepted `last` | Outer lifetime remains until destruction; reference does not gain or renew terminal authority implicitly | The tracking handle remains attached to the place, but terminal authority applies only to the exact consumed incarnation |
| Lifecycle-ending whole-value replacement | Old reference may not be used afterward | Begins observing the successor only after it is completely established |
| Reconstructive replacement | Same as any other lifetime-ending replacement | Same as any other lifetime-ending replacement |
| Explicit destruction without reconstruction | Invalid for use | Place currently has no live value; an ordinary nonoptional access cannot succeed |
| Later reconstruction at the same address | Does not revive the old reference | May observe the new value if the place and policy permit an empty interval |
| Destruction of the root place | Invalid | Invalid; place tracking does not outlive the place itself |
| Copy or transfer to another place | Continues to concern the source incarnation | Continues to concern the source place |

This table deliberately classifies operations by lifecycle effect rather than
punctuation. Current material does not yet establish whether every selected
whole-value `=` on a mutable destination ends one lifetime and begins another,
or whether some assignments mutate one continuing lifetime. The eventual
operation contract must say which event occurred. Reference rules should then
follow that event rather than guess from the token.

`move` and `last` do not themselves end the outer value lifetime. They change
resource authority and source state. A reference may therefore remain
lifetime-valid while some later operations are unavailable, require an intent
acknowledgement, or observe resources already transferred away. Lifetime
validity must not be mistaken for permission to perform every operation.

Likewise, place tracking is not rebinding. The handle keeps the same place
identity while the place receives a successor value. Assigning a direct
reference to a different root place would be rebinding and remains unavailable
under the current candidate.

#### Replacement and transition visibility

For an exact-lifetime reference, safe static analysis can permit replacement
after the reference's final use:

```zax
source varying : Document immutable writable varying = makeDocument("old")
view final : Document immutable readonly varying & = source

inspect(view)
source = makeDocument("new") // legal if no later use or escape of view exists
```

It should reject replacement when a later exact use remains:

```zax
source varying : Document immutable writable varying = makeDocument("old")
view final : Document immutable readonly varying & = source

source = makeDocument("new")
inspect(view) // error: view names the ended "old" lifetime
```

A conservative implementation may report the conflict at the replacement, at
the later use, or at both, but diagnostics should identify the origin, the
lifetime-ending event, and the conflicting use.

A place-tracking reference raises a different problem. Reconstructive
replacement may invoke user code after the old lifetime has begun ending and
before the successor is complete. Reentrant callbacks must not observe a
partially destroyed or partially constructed value through the tracker. A safe
policy therefore needs one of:

- static exclusion of every tracking access during the transition;
- runtime transition state with defined checked failure;
- synchronization that delays access until a complete successor exists; or
- rejection of replacement when the selected policy cannot provide the
  guarantee.

No candidate currently permits a normal `T &` access to silently observe an
absent or incomplete value.

Interior aliasing is decided at the projected lifetime:

```zax
document varying : Document immutable writable varying
textView : String readonly & = document.text

document = makeReplacement()
inspect(textView)
```

The final line is valid for an exact reference only if the selected replacement
retained `document.text`'s exact language-level lifetime. Leaving bytes at the
same address is insufficient. A place-tracking member reference could instead
follow the `text` member place into its successor, but only if the member place
has stable identity across that replacement and no access occurs during its
transition.

For right-hand aliases such as:

```zax
value = value
value = viewOf(value)
value = makeReplacementUsing(value)
```

evaluation-before-replacement does not prove independence. A safe operation must
copy what it needs, consume the alias before ending its exact source lifetime,
retain that exact lifetime, implement documented self-alias handling, or prove
that the selected alias/lifetime contract permits the transition. A narrow
unsafe assertion may accept an unproved valid case; it cannot make use after a
known-ended lifetime valid.

#### Origin by source form

##### Direct storage

A reference to a local, global, member-owned root, arena entry, or other direct
storage starts from that place. The validity strategy must prove or arrange that
the selected exact incarnation or place outlives every use.

A storage duration alone is not sufficient to choose target identity. Long-lived
global storage may still be a varying place whose individual values are
replaced.

##### Members and projections

A member reference derives from both the containing origin and the member's own
lifetime. Ending the container necessarily ends every non-retained member
lifetime. Reconstructing one member invalidates an exact reference to that
member without necessarily ending the container lifetime.

A projection cannot silently inherit destructive declaration stance. It follows
the current transfer precedence for owned storage, explicit member stance, and
use-site restatement. A same-place alias receives its own declaration stance and
defaults ordinary nested use to `copy`.

##### Parameters

A reference parameter borrows or otherwise forwards the caller's origin. The
callee does not acquire a fresh lifetime merely because the parameter has a
local name:

```zax
inspect final : ()(
  input : Document readonly &
) = {
  // input carries the caller's origin.
}
```

Explicit arguments bind in caller source order. Later argument evaluation may
mutate the current referent, so an earlier reference can observe that mutation.
If later evaluation ends the exact referent lifetime, the candidate call is
valid only when the binding's strategy defines the transition or the earlier
reference is never used afterward.

Declaration-side replacement permission is part of the forwarded path. A
parameter cannot turn a declaration-final argument into a path that may replace
the referent.

##### Temporaries and nested calls

The synchronous call-completion boundary is a validity boundary, not an
ownership inference:

```zax
inspect(makeDocument())
```

An input temporary bound to `Document readonly &` remains live through the
complete selected synchronous call, including body execution and result mapping.
The temporary is then destroyed once at its ordinary completion boundary.

A returned reference must continue to carry the temporary's origin:

```zax
returnView final : (
  result : Document readonly &
)(
  input : Document readonly &
) = {
  return input
}

view := returnView(makeDocument())
// error under the ordinary exact strategy: view would escape the input
// temporary's completion boundary
```

Nested immediate consumption may be valid:

```zax
inspect(returnView(makeDocument()))
```

The candidate rule is that origin forwarding can extend the originating
temporary through the complete outer synchronous consumer when the compiler can
prove the chain and no intermediate contract loses the origin. It does not
extend the lifetime into `view` in the previous example. Exact destruction order
among several caller temporaries remains to be established, but it must not
change whether the complete nested consumption is valid.

##### Results

A reference result owns only its access-path value; result-slot destruction
does not make the referent terminal and does not extend it.

Every visible reference-result contract needs enough information to distinguish
at least:

- origin forwarded from a named reference input;
- origin projected from the receiver;
- origin selected from one of several inputs;
- origin in stable external or global storage;
- origin retained by a declared lifetime strategy; and
- an invalid attempt to return a local, by-value parameter, or temporary.

For example, the prototype of `returnView` above must eventually state that
`result` forwards `input`'s origin. The spelling is unresolved. Inferring that
fact only from one available body is insufficient for separate declarations,
function values, interface-like structural matching, overload selection,
generic code, or long-term source stability.

If a result may select between `left` and `right`, the contract must expose that
set or another relationship strong enough for the caller to prove every later
use. A caller cannot soundly assume the longer-lived input was chosen merely
because that would make its code valid.

A reference into a by-value result slot or by-value parameter is not made safe
by the slot's mapping machinery. Either the resulting complete value is
established directly in storage whose origin the contract can name, or the
reference still points into temporary/local storage and may not escape.

##### Optional references and optional payloads

These are different lifetime compositions:

```zax
storedView : MyValue readonly & ? // optional wrapper owns a conditional handle
wrapperView : MyValue? readonly & // reference to the optional wrapper
```

Resetting `storedView` ends the stored reference-handle lifetime, not the
referred-to `MyValue`. While present, the handle retains the original referent
origin and may not outlive it.

A reference obtained through postfix access to `MyValue?` names the current
payload incarnation. Resetting or reconstructing the payload ends an exact
reference to it. A place-tracking payload reference needs an explicit absence
model because reset can leave no payload:

- reject reset while a nonoptional tracker may be used;
- make access through the tracker checked or optional;
- or use a different conditional tracking handle.

Silently retaining a normal nonoptional `MyValue &` across absence is not a
coherent option.

Each nested optional, pointer, reference handle, and pointee layer has its own
lifetime. Ending an outer wrapper or handle does not by itself end a separately
owned pointee, while ending a pointee invalidates every nonowning handle to it.

##### Same-storage identity views

A same-storage identity admission creates no new by-value object and schedules
no independent destruction. Its reference must forward the underlying storage's
origin, target mode, and validity strategy while applying the identity's access
and admission rules.

Underlying and identity views may coexist only when their alias permissions are
compatible. Replacement, move, or destruction affects both according to the one
underlying lifetime; the identity spelling cannot hide that event.

##### Captures and stored references

Storing a reference in a field, optional, closure capture, callback, or result
does not reset its origin. The destination must be proved not to outlive the
origin or must select machinery that retains or checks it.

Conceptually:

```zax
source : Document

callback := [source] {
  inspect(source)
}
```

The capture form must eventually distinguish an independent by-value capture
from a reference capture forwarding `source`'s origin. A reference-capturing
closure may be called repeatedly only while that origin remains valid. Copying
the closure cannot silently copy, extend, or strengthen the referent.

A closure invoked only inside the same synchronous call may use a call-bounded
reference. A closure stored by the callee may not, unless its reference strategy
supports that escape and the callable contract says capture is possible.

#### Lifetime strategy families

The following are candidate families, not a final required list.

| Family | Useful guarantee | Typical visible consequence or cost | Failure boundary |
| --- | --- | --- | --- |
| Statically bounded exact reference | One exact value remains live for all proved uses | No required runtime state; may delay or reject replacement, destruction, escape, or conflicting use | Compile-time diagnostic |
| Region- or owner-bounded exact reference | An enclosing arena/owner is proved to preserve the exact value | Region/owner relationship constrains storage and escape | Compile-time diagnostic or owner-specific failure |
| Retained exact reference | The exact value remains alive independently of one source binding | Ownership/control block, allocation or stable storage, release work, possibly atomic synchronization | Construction/conversion may be unavailable; allocation policy may fail |
| Checked exact reference | Use succeeds only while the named incarnation remains live | Generation/state storage and a check on access; synchronization if shared | Defined panic or explicit fallible access, still undecided |
| Statically controlled place tracker | A stable place may receive successors, and access never overlaps an incomplete transition | Replacement/access restrictions and stronger origin tracking | Compile-time diagnostic |
| Runtime place tracker | A stable cell mediates successor publication and access | Indirection, transition state, possible synchronization and allocation | Defined checked failure or waiting policy, still undecided |
| Narrow unsafe assertion over a nonowning origin | No additional runtime machinery; programmer supplies an unproved fact | Auditable unsafe marker and undefined consequences if the assertion is false | Compile-time rejection for known violations; otherwise programmer responsibility |

Retained exact lifetime is not a free upgrade for arbitrary inline storage. If a
value must remain alive after its containing place is reused or destroyed, the
representation must already support detaching/retaining that value, or source
must explicitly construct a separate owned value. A reference conversion must
not silently relocate an object, allocate, or change identity.

Place tracking likewise requires stable semantic place identity, not merely an
address that happens to be reused. A stack slot reused for an unrelated local is
not one place lineage. A member place may be stable across same-type
replacement only if the language defines that projection as the same continuing
place.

Meaningful conversions should follow guarantees rather than syntax convenience:

- place tracking to exact lifetime may snapshot the currently live incarnation
  when an exact validity strategy can be established;
- exact lifetime to place tracking changes what future values are observed and
  therefore must not happen implicitly;
- retained to shorter statically bounded access may be a safe narrowing;
- statically bounded access cannot become retained unless the source storage
  supports retention;
- checked access cannot lose its check except through a narrow unsafe claim or
  another proof; and
- transfer stance, access qualification, and lifetime-strategy conversion remain
  separate operations.

The selected strategy need not always be written on each `&`. It might be
established by a storage owner, region, generic contract, or API. However, a
caller's visible contract must still expose guarantees and costs that affect
validity, failure, allocation, or synchronization. Switching project defaults
must not silently change which value an existing source reference follows.

#### `move`, `last`, and terminal capability

A reference-shaped `move` or `last` parameter binds caller-owned storage for one
synchronous consumer. The reference origin identifies the source incarnation;
the stance grants that selected body resource authority over it.

Named nested use returns to `copy`:

```zax
consume final : ()(
  input : MyValue mutable writable & last
) = {
  inspect(input)                 // ordinary copy/default use
  transfer(input.resource as last)
}
```

The initial candidate preserves that rule and treats terminal authority as
call-bounded:

- `last` does not make the reference result slot, pointer object, or pointee
  independently terminal;
- storing or returning the parameter does not silently store or return terminal
  authority;
- capture by an escaping closure does not extend the authority;
- renewing `as last` inside the body is valid only against the same exact source
  incarnation and current terminal-state rules; and
- a place tracker cannot carry terminal authority forward into a successor
  value.

This dispositions the useful part of legacy `lease` evidence without reviving
`lease` as an overlapping transfer stance. Ordinary reference use already
provides bounded non-owning access; named destructive references already return
to `copy`; and destructive forwarding already requires explicit `move` or
`last` renewal.

If Zax later needs a terminal capability that can be stored, returned, divided,
or invoked repeatedly, that should be a distinct linear or ownership-bearing
concept with explicit capability lifetime. It should not emerge accidentally
from an ordinary `T & last` parameter.

#### Reference binding, pointer binding, and pointee lifetime

A direct reference's hidden handle exists so the language can model optional
references, pointers to reference handles, and other layer composition. That
does not require direct reference rebinding.

Under the current candidate:

```zax
reference : MyValue & = first
reference = second
```

the second line operates on the referent according to ordinary `=` selection and
permissions; it does not retarget `reference`.

A pointer is different:

```zax
pointer : MyValue * = first
pointer = second
```

The second line may replace the pointer object's value so that it names
`second`; it does not replace `first`. Exact pointer grammar and assignment
selection remain to be reconciled with this distinction.

Every indirect layer has two potentially separate concerns:

1. the lifetime and replaceability of the pointer/reference/optional handle
   object; and
2. the origin, lifetime, and ownership of the pointee.

Moving or destroying a raw pointer value does not move or destroy its pointee.
An owning pointer may dispose the pointee according to its own contract.
Reference-counted or arena handles may participate in a retained strategy. The
pointer's numeric capacity says nothing about provenance, permission, ownership,
or lifetime validity.

Legacy material says references are valid “by convention” while compilers do
not enforce continued validity. That is insufficient for the candidate safe
subset. A raw pointer can preserve this low-level responsibility; an ordinary
safe reference needs proof, retention, or a defined checked strategy.

#### Safe proof, unsafe assertion, and diagnostics

The candidate adopts the existing prove-or-narrowly-assert direction:

> The selected reference/lifetime contract determines what must be proved. A
> valid operation the compiler cannot prove may use a narrow assertion. A known
> violation remains invalid.

Relevant provisional categories already exist in raw analysis input:

- lifetime escape;
- replacement alias;
- construction/destruction path and at-most-once claims;
- partial instance access or escape; and
- terminal reconstruction.

Their names and syntax are not accepted. The important boundary is semantic:

- an assertion applies at the narrow operation or contiguous region;
- it names the exact property being asserted;
- it does not create broad unsafe mode;
- it has no required runtime check;
- a false lifetime, presence, or alias assertion may have undefined
  consequences; and
- it cannot make a known-ended lifetime live again.

A checked lifetime strategy is not an unsafe assertion. A stale checked access
must have defined behavior—such as panic or an explicit absent/error result—while
unsafe falsehood may remain undefined. Debug instrumentation of unsafe claims
does not become a language guarantee.

Diagnostics should distinguish at least:

- exact referent ended before this use;
- root place ended;
- replacement conflicts with a later exact use;
- place tracker accessed during an incomplete transition;
- payload absent for a conditional tracked place;
- stored reference or capture may outlive its origin;
- returned reference has no expressible or sufficient origin contract;
- attempted origin or replacement-permission laundering;
- attempted direct-reference rebinding versus referent replacement;
- terminal capability would escape its synchronous consumer;
- selected runtime strategy is unavailable for this storage;
- strategy state cannot cross the requested thread boundary; and
- unsafe assertion contradicts a known fact rather than filling a proof gap.

For source stability, diagnostics should cite the language-contract obligation,
origin path, lifetime-ending event, and use that failed. Improved analysis may
prove more source under the selected contract, but it must not silently change
an exact reference into a tracker, add retention, select another transfer
stance, or remove a required check.

#### Cross-thread and async constraints

The synchronous rule does not generalize by making a temporary survive until an
unspecified future callback. Suspension introduces storage, cancellation,
executor, and partial-completion questions. A reference across suspension needs
a strategy whose origin remains valid for the entire async operation, including
all defined cancellation cleanup.

Cross-thread transfer is another independent capability:

- lifetime bookkeeping must remain valid on the destination thread;
- release/deallocation must not depend unsafely on the origin thread;
- thread-local arenas, callbacks, handles, and registrations must be adapted or
  rejected;
- shared lifetime state may require atomic synchronization; and
- none of this alone grants synchronized pointee mutation or data-race safety.

`copy`, `deep`, `move`, and `last` do not imply that capability. A retained
strategy based on non-atomic counts may be valid on one thread and invalid
across threads. An exact static reference into an async caller frame may be valid
only if the frame's storage and cancellation contract preserve it.

The current work needs to preserve these constraints and avoid a synchronous
model that makes them impossible. Final suspension, cancellation, executor, and
cross-thread preparation syntax remains deferred to the live async input.

#### Recovered tensions and holes

1. **Ordinary `&` currently carries incompatible implications.** Current
   replacement teaching uses place tracking, while legacy wording and the raw
   lifetime distinctions preserve original-lifetime behavior.
2. **Type-side `varying` is already occupied.** It truthfully describes the
   referent place and cannot double as the tracking-policy selector.
3. **Result prototypes lack an origin relationship.** Body inference alone
   cannot support separate declarations, function values, structural matching,
   or stable caller reasoning.
4. **“Assignment” is not yet a sufficient lifetime event.** The language needs
   to say which whole-value operations end an incarnation and which mutate one
   continuing value.
5. **Place identity is underspecified.** Same address, same static type, and same
   member name are each insufficient by themselves to prove one continuing
   place lineage.
6. **Transition reentrancy is observable pressure.** A tracking handle may be
   reachable while custom replacement calls user code; no current owner defines
   access during that gap.
7. **Conditional places need conditional tracking.** An ordinary nonoptional
   reference cannot represent an optional payload's absent interval.
8. **Retention may change representation.** Exact-lifetime extension cannot be
   promised independently of storage, allocation, ownership, and release
   behavior.
9. **Lifetime and alias safety are related but not identical.** A reference can
   be live while a mutable alias conflict, replacement conflict, or data race
   remains invalid under another contract.
10. **Terminal state does not end lifetime.** An alias may remain non-dangling
    yet be unable to perform an operation on moved-from or terminal state.
11. **Capture mode is not yet explicit enough for origin.** Closure lifetime,
    closure copying, and repeated invocation need to preserve whether capture
    copied a value or forwarded a reference.
12. **Checked failure is undecided.** Panic, optional access, and explicit result
    each teach materially different programmer models and cannot be selected as
    an implementation detail.
13. **Direct-reference non-rebinding needs pressure testing.** Optional
    references and pointers appear to cover replaceable associations, but stored
    callback and data-structure use cases may expose a missing ergonomic case.
14. **Destruction order remains incomplete.** Current synchronous boundaries
    establish validity through complete calls but not exact ordering among every
    caller argument and result temporary.

#### Immediate consequence ledger

These are candidate dispositions for review, not aligned findings.

| Consequence | Candidate disposition | Constraint on current work |
| --- | --- | --- |
| Exact versus place-following meaning | Resolve in this item; likely owned by a dedicated reference/lifetime concept owner | Qualifiers must not overload type-side `varying` |
| Ordinary reference default | Resolve in this item; candidate is statically proved exact lifetime | Existing replacement observer teaching would need revision |
| Place-tracking syntax and transition behavior | Resolve enough in this item to make examples unambiguous; exact grammar may remain illustrative until syntax pressure is tested | No unmarked `T &` example may silently require tracking |
| Reference result origin | Resolve the contract model in this item; callable syntax may require focused follow-up | Synchronous temporary escape cannot be taught soundly without it |
| Capture/storage escape | Resolve origin propagation and validity here; leave complete closure grammar elsewhere | Captures must not launder or extend origin |
| Direct reference rebinding | Candidate rejection here; pointers/optional references retain replaceable-association pressure | Declaration-side `final` remains referent-replacement permission |
| Pointer ownership and provenance | Constrain here but defer the complete pointer taxonomy and conversion catalog | Pointer-value transfer must not imply pointee authority |
| Terminal capability beyond a body | Candidate rejection for ordinary reference stance; preserve distinct future capability pressure if a use case appears | `last` cannot hop to a successor or escape by capture/result |
| Unsafe proof controls | Reuse the raw prove-or-narrowly-assert direction; final syntax stays with analysis-control/safety work | Unsafe cannot revive a known-ended lifetime |
| Optional payload tracking | Resolve the semantic absence requirement here; exact surface form may stay with optional/lifetime owners | Nonoptional access cannot silently cross absence |
| Same-storage identity views | Forward origin here; retain admission and nominal behavior in identity types | No independent lifetime or destructor is created |
| Async and cancellation | Explicitly defer to `project/raw/async.md` | Synchronous extension must not imply async extension |
| Cross-thread preparation | Explicitly defer final capability/stance design to `project/raw/async.md` and lifetime ownership work | Policies must declare whether their bookkeeping can cross threads |
| Generic expression of origin and policy | Activate focused generic/callable input only when a concrete generic contract is proposed | Do not bake body-only inference into the base model |
| Formal borrow calculus, layout, ABI, and lowering | Defer; future specification or implementation concern | Human model must remain implementable without prescribing an algorithm |

#### Likely lasting owner boundaries

A pre-promotion dry run must test these rather than assuming them:

- A dedicated reference-and-lifetime concept owner appears necessary for origin,
  exact-lifetime versus place tracking, strategy selection, escape, storage,
  capture, unsafe boundary, costs, and diagnostics. Splitting those rules among
  local owners would force readers to reconstruct the feature.
- `language/qualifiers.md` should retain the independent qualifier axes,
  same-place truth, and local indirection summary. Its reconstructive observer
  example must use whichever reference target semantics this item accepts.
- `language/function-invocation.md` should retain caller-order binding,
  synchronous completion, temporary extension, and the local explanation of
  result-origin contracts.
- `language/transfer-stances.md` should retain reference-shaped consumer
  authority, copy-default nested use, result-slot nonterminal behavior, and the
  local handoff to lifetime validity.
- `language/construction-and-destruction.md` should retain the exact events that
  begin and end lifetimes, retained-member behavior, transition completeness,
  and local interior-alias consequences.
- `language/optional-values.md` should retain optional-handle versus referent
  lifetime and the local payload absence interaction.
- `language/identity-types.md` should retain the no-copy/no-independent-lifetime
  effect of same-storage identity views.
- Complete pointer grammar, pointer ownership families, numeric/address
  conversion, and provenance likely need a later dedicated pointer owner. The
  legacy `pointers.md` page should be consumed by value rather than promoted as
  one block.
- `project/raw/safety.md`, `project/raw/analysis-controls.md`, and
  `project/raw/async.md` remain live destinations for the explicitly deferred
  safety-control and async/cross-thread decisions.

The raw lifetime placeholder should retire only after every useful reference,
pointer, rebinding, ownership, provenance, terminal-capability, and cross-thread
pressure in it has either been promoted, rejected, or moved to a live indexed
destination.

## Dispositions and promotion dry run

### Dry-run result: PASS

The documentation fit dry run was performed on 2026-09-06. The aligned design
now has a coherent proposed owner structure, reading path, deferred destinations,
legacy disposition, and exact promotion change set.

The three issues found by the initial dry-run pass are resolved:

1. **Terminology:** **resident instance** means the complete live occupant of an
   instance place. **Current instance** retains its established meaning for the
   instance whose type-defined body is executing through `_`.
2. **Interior owning pointers:** `strong anchored` and `weak anchored` are
   accepted pointer forms. `anchored by` creates a direct-member pointer whose
   target place differs from the allocation-root ownership anchor. It shares the
   anchor's control block, cannot become `unique`, and requires intent
   acknowledgement when replacement may renew the target resident instance.
3. **Deferred recovery:** prompt instance destruction with arena-delayed storage
   recovery is an allocation concept, not a pointer ownership family.
   Destruction timing, storage recovery, and cycle probing are orthogonal
   allocation choices. Legacy `discard` spelling does not establish a current
   allocation keyword.

PASS confirms documentation fit only. It does not authorize promotion,
relocation, deletion, archival, staging, or any other repository change.

### Proposed minimal lasting structure

No directory change is needed. The smallest cohesive structure adds three
current concept owners under `language/`:

| Proposed path | Role |
| --- | --- |
| `language/lifetimes-and-references.md` | Own life paths, instance places, live occupants, fixed reference binding, reference origin, synchronous borrowing, replacement/member-reference boundaries, capture lifetime, escape, and lifetime diagnostics |
| `language/pointers-and-arenas.md` | Own raw/`unique`/`shareable`/`strong`/`weak` pointer behavior, pointer-layer `atomic`, arena-backed allocation dispositions, control blocks, weak probing/acquisition, ownership transitions, and programmer-visible costs |
| `language/safety-and-analysis.md` | Own the reusable safe-subset model, proven/unproved/defined-unsafe/invalid distinctions, narrow unsafe responsibility, compiler improvement versus language-contract advancement, and lint separation |

These owners are independently useful:

- construction and destruction describe how lifetimes begin and end;
- lifetimes and references describe where instances live and how non-owning
  access remains valid;
- pointers and arenas describe how dynamic life paths are owned and backed; and
- safety and analysis describe the general proof and unsafe contract used by
  lifetime and other language domains.

Combining all three would overload one document and make pointer allocation or
general safety readers traverse reference teaching. Splitting the lifetime
mental model among existing local owners would instead force programmers to
reconstruct the central concept from qualifiers, construction, invocation, and
optionals. Three owners are therefore the minimal coherent structure found by
the dry run.

`index.md` remains the public router. No `language/README.md` or new directory is
needed.

### Terminology proposal

`language/terms.md` remains the cross-cutting terminology owner.

- Extend the existing **place** entry so **instance place** is introduced as the
  explicit teaching phrase for a stable typed place within a life path. This is
  an elaboration of the existing term rather than a competing definition.
- Add **resident instance** for the complete live instance occupying an instance
  place.
- Add **life path**, **reference binding**, **allocation disposition**,
  **control block**, **strong pointer**, **weak pointer**, and **shareable unique
  pointer** as concise terms with links to their complete owners.
- Preserve the existing **current instance** meaning for `_`.
- Add **anchored strong pointer**, **anchored weak pointer**, and **ownership
  anchor** while keeping `anchored` and `anchored by` recognizable as source
  spellings rather than conflating every lifetime relationship with anchoring.
- Keep source keywords and descriptive terms distinct: `shareable` is aligned
  pointer syntax, while “life path” and “instance place” are conceptual terms.

### Aligned-finding ownership map

| Aligned finding | Lasting owner | Required local integration |
| --- | --- | --- |
| Every instance exists within and never outlives a life path | New `language/lifetimes-and-references.md` | Construction, core flow, global source, namespace, optional, array, and capture sections link or summarize locally |
| Allocation, life path, instance place, and live occupant are distinct | New lifetime owner plus `language/terms.md` | `language/construction-and-destruction.md` retains construction/storage detail |
| References bind permanently to one instance place and never rebind | New lifetime owner | `language/qualifiers.md`, optional references, and pointer/reference examples preserve the distinction |
| A varying outer place may expose successive complete instances | New lifetime owner | Qualifier and replacement owners retain their local examples |
| Complete replacement renews every member instance | `language/construction-and-destruction.md` | Lifetime owner teaches reference consequences; `language/qualifiers.md` removes retained-member wording |
| Interior reference use across complete replacement requires unsafe permission | New lifetime and safety owners | Construction owner shows the replacement boundary; analysis raw material retains exact future controls |
| Half-complete state is not ordinarily observable | Construction owner plus new safety owner | Lifetime owner explains alias and reentrancy consequences |
| Optional, array, variant, and arena paths have distinct continuity | New lifetime owner | Optional owner keeps complete optional behavior; array and variant details remain explicitly deferred |
| Explicit arguments, defaults, result preconstruction, and deterministic completion order | `language/function-invocation.md` | `language/terms.md` updates call-completion and result-slot summaries |
| Result preconstruction state participates in prototype/body compatibility | `language/function-invocation.md` | Compatible-prototype section and declarations summary remain consistent |
| Returned references use static origin analysis without current explicit lifetime syntax | New lifetime owner and invocation owner | Opaque callable metadata remains deferred |
| Lambda capture defaults to copying the referent; explicit reference capture borrows | New lifetime owner for lifetime behavior and `language/transfer-stances.md` for transfer | Exact capture syntax remains in callable raw input |
| Multiple ownership strategies do not change reference binding | New pointer/arena owner | `language/vision.md` updates its lifetime-strategy summary |
| All dynamic storage is arena-backed with independent destruction, recovery, and cycle-probing choices | New pointer/arena owner | Construction retains allocation-versus-construction boundary; deferred recovery remains prompt destruction with delayed arena reuse |
| Pointer ownership uses raw, `unique`, `unique shareable`, `strong`, `weak`, anchored strong/weak, and pointer-layer `atomic` | New pointer/arena owner | Qualifier and optional owners summarize layer behavior |
| Inline and detached control blocks impose different arena obligations | New pointer/arena owner | Residual implementation/syntax details move to indexed raw input |
| `anchored by` creates a direct-member owning projection sharing its allocation-root control block | New pointer/arena owner | Replacement intent, no-`unique` conversion, weak acquisition, and direct-containment restrictions remain explicit |
| Destination type plus `copy`/`move`/`last` selects pointer ownership transition | New pointer/arena owner and `language/transfer-stances.md` | Pointer examples use the shared stance model rather than legacy casts |
| `?weak` is a non-owning probe; weak-to-strong `copy` is the ownership acquisition | New pointer/arena owner | Optional owner distinguishes optional presence from pointer-layer presence |
| Raw-pointer operations may be statically safe when presence, origin, lifetime, provenance, alignment, and access are proved | New pointer/arena and safety owners | Escape or loss of proof requires narrow unsafe responsibility; non-`Nothing` alone is insufficient |
| Arenas declare concurrency and affinity capabilities rather than being universally thread-safe | New pointer/arena owner | Cross-thread release must be supported, queued, rejected, or diagnosed; every arena outlives its dependencies |
| Collectable support is emitted only for reachable use and collection is programmer-triggered from visible pressure | New pointer/arena owner | Mark state may use ordinary control blocks; generated traversal/root metadata remains visible cost |
| Allocation failure behavior comes from the selected operator | New pointer/arena and safety owners | Panicking, non-panicking `Nothing`, and unchecked undefined-consequence forms remain distinct |
| References do not safely cross threads; unique or atomic managed ownership may cross subject to affinity | New pointer/arena owner | Async raw input retains suspension, affinity, and preparation detail |
| Explicit `as last` may store or forward terminal reference stance when lifetime proof succeeds | `language/transfer-stances.md` and new lifetime owner | Invocation keeps synchronous reference guarantees |
| Generic realizations use ordinary concrete lifetime analysis | New lifetime owner local boundary | Generic raw input needs no new lifetime mechanism |
| `lease` is superseded | New lifetime owner and pointer owner | Remove it from legacy teaching after preserving provenance |
| Safety analysis follows the selected contract, not one compiler's current cleverness | New safety owner | `project/raw/analysis-controls.md` retains unresolved syntax and exact categories |

### Current-owner edits required by promotion

| Existing file | Exact promotion responsibility |
| --- | --- |
| `index.md` | Add the three new concept owners to Start Here and Current conceptual design; update or remove affected legacy routes |
| `language/vision.md` | Replace the wholly-future lifetime-strategy summary with the accepted high-level plurality and links while retaining unresolved implementation and syntax boundaries |
| `language/terms.md` | Preserve `current instance` for `_`; add `resident instance`, life-path/place/reference/pointer/ownership-anchor terminology; update call completion and result-slot summaries |
| `language/declarations-and-bindings.md` | Remove the obsolete explicit-`varying` reference rule and state that references never rebind |
| `language/qualifiers.md` | Teach references as fixed bindings to instance places; preserve varying-place truth; remove claims that custom replacement may retain old member instances |
| `language/construction-and-destruction.md` | Add the life-path handoff; make complete replacement renew every member instance; revise custom replacement, self-alias, raw-pointer, and retained-member passages |
| `language/function-invocation.md` | Add result-constructedness compatibility, aligned destruction order, returned-origin tracking, and the complete temporary boundary |
| `language/transfer-stances.md` | Add destination-directed pointer transitions, weak acquisition by `copy`, explicit terminal-reference forwarding/storage, and pointer-source post-states |
| `language/optional-values.md` | Distinguish wrapper, boxed path, optional reference, pointer emptiness, weak probing, and per-layer presence |
| `language/identity-types.md` | State that same-storage identity references forward one fixed target place and create no independent lifetime |
| `language/core-flow-control.md` | Connect lexical/header scope destruction to the general life-path model without duplicating lifecycle rules |
| `language/intent-acknowledgements.md` | Broaden the unsafe distinction from proof assertion alone to exact unsafe assertions and permissions |

### Raw-input disposition

| Raw input | Proposed disposition |
| --- | --- |
| Former `project/raw/lifetimes.md` | Consumed and deleted after moving accepted material into current owners and residual mechanics into `project/raw/pointer-and-arena-mechanics.md` |
| `project/raw/safety.md` | Move accepted general guarantees into the new safety owner; retain unresolved guarantee categories and future safe-subset detail |
| `project/raw/analysis-controls.md` | Retain exact unsafe syntax/category work; add the portability consequence of removing an assertion made redundant only by one compiler |
| `project/raw/async.md` | Retain suspension, cancellation, thread affinity, cross-thread preparation, and destructor scheduling |
| `project/raw/callable-selection.md` | Retain opaque/exact-prototype origin metadata and callable compatibility pressure |
| `project/raw/function-composition-and-chaining.md` | Record aligned default referent-copy capture and retain explicit reference-capture syntax, closure representation, and repeated-invocation detail |
| `project/raw/indexing-and-slicing.md` | Add per-operation element-place stability and relocation guarantees |
| Proposed `project/raw/global-and-once-lifetimes.md` | Preserve exact global dependency ordering, circular initialization, `once`, teardown, and concurrency pressure |
| Proposed `project/raw/variants-and-unions.md` | Preserve managed active-alternative and unsafe overlay questions; note that no reliable final source form exists yet |
| Proposed `project/raw/nothing-instances.md` | Preserve real monitored and custom global `Nothing`, generated `_` checks, compatible sentinel overlap, panic/check policy, and thread-safety pressure |
| Proposed `project/raw/pointer-and-arena-mechanics.md` | Preserve control-block allocation syntax, deeper/unsafe ownership anchoring, pointer provenance and casts, pointer-layer presence mechanics, arena recovery syntax, cycle tracing, custom arenas, and thread-affine release |
| `project/raw/README.md` | Update the index for moved, new, retained, and retired raw inputs |

### Legacy disposition

Promotion must consume legacy material by value rather than leaving duplicate
public teaching.

- `pointers.md`, `memory-allocation.md`, `strong-weak.md`, `handle-hint.md`, and
  `custom-allocators.md`: move unique unresolved pointer, cast, allocator,
  deeper/unsafe anchoring, and manual-memory evidence to the residual
  pointer/arena raw input; promote accepted ownership anchoring, `discard`
  allocation, and other current behavior into the two new owners; then retire
  these pages and remove their public legacy links.
- `functions.md`: promote the capture-by-copy and explicit reference-capture
  evidence, remove superseded capture teaching, and retain unrelated legacy
  function material.
- `scope.md`: move its remaining callable-like capture evidence to
  `project/raw/function-composition-and-chaining.md`; current core flow already
  owns ordinary scope behavior, so retire the page.
- `arrays.md`: retain as legacy array input, add the current lifetime-owner
  handoff, and remove or clearly supersede claims that conflict with future
  dynamic-array stability work.
- `concurrency.md`: remove superseded allocator/thread-pointer teaching after
  preserving async and affinity pressure in current/raw owners; retain unrelated
  async legacy material.
- `ctor-dtor.md`: move global construction/destruction teaching to the lifetime
  owner and remove the duplicate section; retain unresolved `once` evidence.
- `basics.md`: replace the legacy `main` framing with the accepted global
  execution/life-path route while retaining unrelated legacy foundations.
- `namespacing.md`: add the namespace-life-path handoff while retaining module,
  import, export, visibility, and lookup input.
- `nothing.md`: move monitored/custom global `Nothing`, pointer representation,
  dereference, compatible overlap, and thread-safety evidence to dedicated raw
  input; retain the page while it remains unique substantive legacy type input.
- `context.md`: mark concrete allocation syntax as legacy and route arena-backed
  allocation to the current pointer owner.
- `discard.md` and `flow-control.md`: update their local handoffs for allocation
  disposition and retired callable-like scope capture.

No lasting current owner may cite item 014 or its eventual archive path.

### Teaching and direct-entry test

The proposed structure can satisfy the cold-reader test:

1. `language/lifetimes-and-references.md` opens with a concrete lexical example,
   then explains life path, instance place, fixed reference binding, replacement,
   nested paths, escape, and diagnostics.
2. `language/pointers-and-arenas.md` opens with `unique`, `strong`, and `weak`
   allocation/use examples, then layers allocation disposition, control blocks,
   conversions, atomicity, arenas, costs, and deferred mechanics.
3. `language/safety-and-analysis.md` opens with one proven use, one unproved valid
   use, one defined unsafe permission, and one impossible use before introducing
   contract-version evolution.
4. Existing local owners explain why each linked rule matters before handing off
   to the complete owner.

The test passes with **resident instance** as the place-occupant term and
**current instance** retained for `_`. Anchored-pointer teaching must introduce
the target place and ownership anchor before showing replacement intent or
weak-pointer details.

### Exact proposed promotion change set

Promotion should be one coherent change set:

1. Create:
   - `language/lifetimes-and-references.md`;
   - `language/pointers-and-arenas.md`;
   - `language/safety-and-analysis.md`;
   - `project/raw/variants-and-unions.md`;
   - `project/raw/pointer-and-arena-mechanics.md`;
   - `project/raw/global-and-once-lifetimes.md`;
   - `project/raw/nothing-instances.md`.
2. Delete `project/raw/lifetimes.md` after moving unresolved evidence to the new
   pointer/arena input. Historical records recover their former links through
   Git history.
3. Update:
   - `index.md`;
   - `language/vision.md`;
   - `language/terms.md`;
   - `language/declarations-and-bindings.md`;
   - `language/qualifiers.md`;
   - `language/construction-and-destruction.md`;
   - `language/function-invocation.md`;
   - `language/transfer-stances.md`;
   - `language/optional-values.md`;
   - `language/identity-types.md`;
   - `language/core-flow-control.md`;
   - `language/intent-acknowledgements.md`;
   - `project/documentation.md`;
   - `project/raw/README.md`;
   - `project/raw/safety.md`;
   - `project/raw/analysis-controls.md`;
   - `project/raw/async.md`;
   - `project/raw/callable-selection.md`;
   - `project/raw/function-composition-and-chaining.md`;
   - `project/raw/indexing-and-slicing.md`;
   - `arrays.md`;
   - `basics.md`;
   - `concurrency.md`;
   - `context.md`;
   - `ctor-dtor.md`;
   - `discard.md`;
   - `flow-control.md`;
   - `functions.md`;
   - `namespacing.md`;
   - `nothing.md`;
   - `enums.md` for the live-link anchor repair found by validation.
4. Extract unique unresolved evidence and delete these historical-only pages:
   - `pointers.md`;
   - `memory-allocation.md`;
   - `custom-allocators.md`;
   - `strong-weak.md`;
   - `handle-hint.md`;
   - `scope.md`.
5. Verify all current examples use aligned terminology and source forms, all
   invalid examples carry inline error comments, all raw syntax is labeled
   illustrative, and all links and anchors resolve.

This set was explicitly authorized for promotion on 2026-09-06. Material
integration findings discovered during promotion added
`language/declarations-and-bindings.md`, `context.md`, `discard.md`,
`flow-control.md`, and the indexed global/`once` raw destination before
validation; those additions preserve the same owner structure and do not change
the PASS result.

A later authorized refinement removed historical-only compatibility stubs,
introduced proof-sensitive raw-pointer safety, made arena concurrency a declared
capability, replaced source-looking `discard` wording with deferred recovery,
added explicit collection instrumentation/pressure and allocation-failure
behavior, captured language-managed arena responsibilities, and created the
dedicated `Nothing` raw owner. The live-tree link policy and exact change set
were updated before this second dry-run validation.

### Follow-up dry-run result: PASS

The follow-up documentation fit dry run passes:

- proof-sensitive raw-pointer behavior fits the existing lifetime and safety
  owners without creating another ownership family;
- arena concurrency, allocation failure, explicit collection, and
  language-managed metadata remain cohesive in the pointer/arena owner;
- monitored and custom `Nothing` behavior has one indexed raw destination;
- variants remain present in the life-path model with one indexed raw
  destination;
- every historical-only stub was removed after its unique input moved;
- all live non-archive Markdown links and anchors resolve;
- archives remain historical records whose former paths are recoverable through
  Git history; and
- the exact owner and promotion structure remains unchanged.

### Authorized promotion result

The initial promotion was completed on 2026-09-06 and later staged by the
language maintainer without edits. The authorized follow-up refinement remains
unstaged for review.

The lasting owner structure now consists of:

- `language/lifetimes-and-references.md`;
- `language/pointers-and-arenas.md`; and
- `language/safety-and-analysis.md`.

Existing qualifier, construction, invocation, transfer, optional, identity,
flow, declaration, intent, terminology, vision, and navigation owners now carry
their required local rules and handoffs.

Residual future input is indexed in:

- `project/raw/pointer-and-arena-mechanics.md`;
- `project/raw/variants-and-unions.md`;
- `project/raw/global-and-once-lifetimes.md`;
- `project/raw/nothing-instances.md`;
- the existing async, callable-selection, capture/composition,
  indexing/slicing, safety, and analysis-control inputs.

The former raw lifetime, public pointer, allocation, strong/weak, handle/hint,
custom allocator, and scope-capture pages were deleted after unique unresolved
evidence moved. Historical records recover their former context through Git
history rather than live compatibility routes.

Promotion review found and corrected:

- stale declaration rules requiring explicit `varying` and implying future
  reference rebinding;
- duplicate safety authority;
- an unresolved explanation of resource carry across member renewal;
- missing global/`once` and capture evidence;
- old pointer-family and allocation-context routes;
- blockless unique arena-provenance pressure;
- ambiguous reference-inference examples; and
- residual current-owner statements that treated the promoted model as future.

Validation after those corrections established:

- all local links and heading anchors in the live non-archive tree resolve;
- current language owners contain no links to numbered work or raw project
  inputs;
- no stale current rule permits reference rebinding, requires explicit
  `varying` for an inherited varying referent, or preserves member lifetimes
  across complete replacement;
- historical-only compatibility routes are deleted and substantive legacy pages
  remain only while they preserve unique input;
- `git diff --check` passes for staged and unstaged changes; and
- the maintainer-staged initial promotion remains unchanged, while this
  authorized follow-up refinement is unstaged and its new `Nothing` raw input is
  untracked pending separate staging permission.

### Archive effect

Item 014 remains active after this PASS. Promotion is complete, but PASS does not
authorize archival or a current-work index change. After the maintainer reviews
this follow-up and separately authorizes closure, the item may be archived and
item 015 prepared under the ordinary closure process.
