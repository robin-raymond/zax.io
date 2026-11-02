# 005: Construction, destruction, and replacement

| Field | Value |
| --- | --- |
| Status | Historical / non-normative / audit-only |
| Work Item | `005` |
| Created | 2026-08-22 |
| Closed | 2026-08-23 |
| Owns | The bounded review defined by the fixed initiating input |
| Does Not Own | Current construction semantics, formal grammar, compiler behavior, or any meaning required by current documentation |

## Non-authority notice

This file is a completed historical working record. It remains
non-authoritative and audit-only. Current documents do not depend on it for
meaning; promoted behavior belongs to its lasting owners.

## Fixed initiating input

This section records the information known and aligned when work item `005` was
created. It is intentionally incomplete rather than exhaustive and must not be
rewritten as the work develops.

### Initiating concern

Develop a coherent human-developer-facing model for how Zax value lifetimes:

1. begin through initialization and construction;
2. transition through reconstructive replacement; and
3. end through destruction.

Define ordinary constructors, replacement constructors, destructors,
construction authority, same-storage resource retention, generated behavior,
failure boundaries, and immediate qualifier interactions.

### Motivating pressure

Work item `004` established varying places containing successive immutable value
lifetimes, generated reconstructive `=`, contextual `replacement +++`, terminal
destruction authority, and a detailed set of unresolved replacement mechanics.

Those accepted qualifier constraints depend on construction and destruction
behavior that is not yet technically complete. The legacy constructor corpus
also contains substantial proposals for constructor selection, generated
constructors, arguments, member initialization, ordering, move/copy,
destruction, and failure behavior.

Reviewing these concerns next tests whether reconstructive replacement and
ordinary construction can form one coherent programmer model without silently
committing to a complete ownership or lifetime strategy.

### Known assumptions

- The current [language vision](../../../language/vision.md) is accepted
  foundational direction.
- [Zax declarations and bindings](../../../language/declarations-and-bindings.md)
  is accepted conceptual design.
- [Zax qualifiers](../../../language/qualifiers.md) is accepted conceptual design.
- Ordinary constructor declarations use `+++`.
- Destructor declarations use `---`.
- Replacement constructors use contextual `replacement +++`.
- `replacement` remains an ordinary identifier outside that narrow constructor
  declaration context.
- Generated reconstructive `=` requires an existing varying place and a
  writable current path.
- Reconstructive replacement uses the destination's existing storage.
- A replacement constructor receives `_` with the previous representation and
  resources and must establish a complete valid new lifetime before normal
  return.
- Replacement-constructor right-hand operands use ordinary declared parameter
  types, qualifications, and selection.
- Constructors and replacement constructors do not return results.
- Construction authority establishes final and immutable state without
  `unsafe pliable`.
- Destruction receives terminal mutable and writable authority.
- Panic is often terminal. Complete recoverable-panic behavior remains
  unresolved.
- Exact move/copy, `last`, alias, pointer, async, concurrency, ownership, and
  lifetime behavior remains later work unless an immediate construction
  contradiction requires a constraint.
- There is no language implementation in this repository.

### Known inclusions

- Default initialization, direct initialization, and constructor invocation
  boundaries.
- Ordinary constructor declarations and selection.
- Multiple constructor arguments and their mapping.
- Named constructor arguments and their interaction with declaration syntax.
- Default arguments and omission where construction requires them.
- Constructor overloading, viable candidates, and immediate ambiguity rules.
- Generated, defaulted, disabled, and programmer-declared constructors.
- Member initialization order and construction authority.
- Complete-instance activation of final and immutable guarantees.
- Contextual `replacement +++` recognition and selection.
- Same-storage replacement and the transitional receiver operand.
- Member retention, destruction, move, copy, and reconstruction during
  replacement.
- Right-hand operand evaluation and aliasing with the destination.
- Destructor declaration, selection, ordering, and terminal authority.
- Partial construction and partial destruction at the conceptual depth needed
  for a coherent model.
- Panic and non-recoverable failure boundaries.
- Raw and typed pointers into constructing, replacing, or destroying storage.
- Immediate interactions with `last`, move/copy, allocation, callbacks,
  reentrancy, async, and concurrency.
- Programmer-visible costs, diagnostics, and canonical formatting.
- Candidate lasting owners and documentation boundaries.
- Preservation of useful but premature argument, lifetime, operator, or safety
  proposals as indexed raw input when their lasting owner is not yet known.

### Known starting boundaries

- Complete ownership and selectable lifetime strategies.
- Complete pointer/reference grammar and rebinding.
- Complete move/copy overload selection and moved-from states.
- Complete function declaration, named/default argument, generic, and overload
  behavior outside construction pressure.
- Complete generated-operator ranking and arbitrary operator semantics.
- Complete allocation-strategy and allocator design.
- Complete panic recovery and general error handling.
- Complete callback, reentrancy, async, and concurrency design.
- Mutability-indexed type-family identity and representation.
- Structural identity, equivalence, layout, reflection, and anonymization.
- Formal grammar, compiler data structures, lowering, or implementation.

These areas are not part of the initiating question and need not be explored to
begin. They do not prohibit following, recording, or dispositioning material
consequences.

### Intentionally unresolved framing

- Whether ordinary and replacement constructors share all declaration and
  selection rules.
- How multiple and named arguments map to constructor parameters.
- Whether constructor default arguments use ordinary function defaults or a
  construction-specific mechanism.
- Which constructors are generated, defaulted, disabled, or reflected.
- Whether a replacement constructor must mention every member.
- Whether untouched member lifetimes continue across enclosing replacement.
- How self-replacement and right-hand aliases are handled.
- How raw interior pointers survive or fail across replacement.
- What fallback exists when no replacement constructor is declared.
- How generated reconstructive `=` ranks against domain-specific `=`.
- Which panic conditions are recoverable and what partial cleanup means.
- Whether replacement constructors may call back, reenter, suspend, or execute
  concurrently.
- Which detailed findings belong in current construction documentation versus
  future lifetime, operator, function, pointer, safety, async, or concurrency
  owners.

### Initial stopping guidance

Stop when the work has:

- established a coherent model for ordinary construction, replacement
  construction, and destruction;
- established constructor declaration, argument, and immediate selection
  behavior;
- established member-state and resource-retention principles during
  replacement;
- established construction completion and destruction boundaries;
- constrained generated behavior, move/copy, `last`, aliases, pointers, panic,
  callbacks, async, and concurrency without designing them completely;
- identified programmer-visible costs, diagnostics, and formatting;
- dispositioned the indexed replacement-construction input;
- preserved useful deferred argument or lifetime material in the appropriate
  indexed input;
- identified plausible lasting owners and their boundaries; and
- gathered enough information for a pre-promotion documentation fit dry run.

Do not promote findings, update language examples broadly, archive this work
item, or begin work item `006` without the separately required discussion,
alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../../documentation.md) - governs ownership,
  focused reading, numbered work, and promotion.
- [Language vision](../../../language/vision.md) - provides explicit-cost,
  progressive-complexity, safety, readability, and data-oriented principles.
- [Zax declarations and bindings](../../../language/declarations-and-bindings.md) -
  provides accepted initialization, assignment, generated replacement,
  incomplete-definition, and declaration constraints.
- [Zax qualifiers](../../../language/qualifiers.md) - provides accepted construction
  activation, reconstructive replacement, terminal destruction, place/access,
  and unsafe boundaries.
- [Legacy constructors and destructors](../../../ctor-dtor.md) - preserves the
  primary legacy evidence for constructor/destructor forms, selection,
  generation, arguments, ordering, and failure behavior.
- Replacement-construction input, formerly
  `project/raw/replacement-construction.md` - supplied the detailed future-work
  questions exposed by reconstructive replacement. Promotion retired that raw
  file after this working record dispositioned its material into current owners
  and indexed future inputs.

### Consequence-driven

- Read focused [type-definition](../../../type-definition.md) material when member
  initialization, generated constructors, or incomplete types become necessary.
- Read focused [function](../../../functions.md) material when constructor
  parameters, multiple/named/default arguments, overload selection, callbacks,
  or captures become necessary.
- Read focused [operator](../../../operator.md) material when generated
  reconstructive `=` viability or ranking becomes necessary.
- Read focused [pointer and reference](../../../pointers.md) material and the
  [raw lifetime-strategies input](../../raw/lifetimes.md) when aliases, interior
  pointers, lifetime transitions, or `last` become necessary.
- Read focused allocation material when construction requires an immediate
  storage or allocator constraint.
- Read focused panic, safety, async, or concurrency material only when a
  construction finding crosses those boundaries.
- Read the [raw mutability-indexed type-family input](../../raw/mutability-indexed-type-families.md)
  only when variant construction or cross-variant replacement becomes material.
- Propose an update or new raw input when useful multiple/named/default argument,
  lifetime, operator, or safety material must be preserved for later work.
  Discussion, alignment, and explicit edit authorization still apply.
- Inspect focused Git history only when the origin or former purpose of an
  constructor choice becomes consequential.
- Expand beyond this list whenever discussion or a material finding establishes
  a concrete need. State why the additional reading is relevant and keep it
  focused.

Material not listed in this reading scope is neither required nor prohibited. It
remains available when discussion or a concrete consequence makes it relevant.

### Audit-only

- `project/archive/`, including work items `001` through `004`.

Audit-only material requires direct language-maintainer instruction or a
specific audit or recovery need.

## Working record

### Review state

The findings below are aligned for this working scope unless they are explicitly
marked as a candidate, open question, or deferral. They remain non-authoritative
working findings until a separately aligned and authorized promotion changes
their lasting owners.

The fixed initiating input remains immutable. Two of its starting assumptions
were refined during review:

- generated reconstructive replacement requires an `immutable`, `writable`,
  `varying` destination, not merely `writable` and `varying`; and
- ordinary constructors remain resultless, but a replacement constructor may
  declare results while still completing the replacement instance on every
  normal return.

The current qualifier owner omits the first restriction. That discrepancy is a
promotion concern, not authorization to edit the qualifier owner now.

### Evidence and legacy disposition

Focused consequence reading covered:

- legacy member initialization and type instantiation in `type-definition.md`;
- legacy multiple/default function arguments, overloads, and move/copy proposals
  in `functions.md`;
- legacy operator selection in `operator.md`;
- legacy pointer/reference and `last` proposals in `pointers.md`;
- the indexed lifetime, safety, and async inputs;
- legacy allocation-with-construction in `memory-allocation.md`; and
- manual raw-storage construction in `custom-allocators.md`.

Useful legacy intent includes:

- optional constructors and destructors;
- automatic member lifecycle management;
- explicit member construction and destruction;
- generated operations that avoid boilerplate;
- construction into separately obtained storage;
- same-storage replacement;
- programmer control over resource retention and cleanup; and
- low-level escape mechanisms for work the compiler cannot understand.

The following legacy mechanisms are superseded as candidate explanations:

- unreachable `[[never]]` calls used to trick lifecycle tracking;
- source or import order silently resolving equal overloads;
- one syntax falling back between named parameters and named members; and
- broad undefined behavior where static lifecycle tracking can identify a
  definite conflict.

The replacement is compiler lifecycle introspection with explicit, narrow unsafe
permissions or assertions where analysis cannot establish the programmer's
claim.

### Core lifetime model

Construction, replacement, and destruction distinguish:

1. **Storage:** a suitably sized and aligned place exists.
2. **Member lifetimes:** individual contained places may or may not currently
   contain live values.
3. **The complete enclosing lifetime:** ordinary code may use the complete
   instance after construction finishes and before replacement or destruction
   begins.

Allocation may obtain storage, but allocation alone does not construct a value.
Construction establishes a complete value in storage. Destruction ends the
complete value and its remaining member lifetimes. Deallocation is a separate
responsibility of the applicable storage or ownership policy.

Reconstructive replacement reuses the outer storage while recycling the
previous representation and resources into a new complete value lifetime.

### Ordinary construction

#### Automatic and explicit member construction

Before an ordinary constructor body runs, the compiler determines which members
are under explicit construction control.

- Members without an explicit `+++` call are initialized automatically before
  the enclosing constructor body.
- Automatic member initialization follows declaration order.
- A member with an explicit `_.member.+++()` call is not automatically
  initialized.
- Explicit member constructors execute wherever ordinary constructor-body
  control flow reaches them and may run in any order.
- After an explicit member `+++` completes, that member is live.
- A later `_.member = value` invokes ordinary operator selection, including
  reconstructive replacement when its requirements are met.
- Normal completion requires every required member to contain a valid live
  value.

```zax
MyType :: type {
    t : T
    u : U
    x : X
    y : Y
    z : Z

    +++ final : ()() = {
        _.t.+++()
        _.t = makeAT()

        _.u = makeAU()

        _.z.+++()
        _.x.+++()
    }
}
```

`u` and `y` are initialized automatically before the body, in declaration
order. `t`, `z`, and `x` are constructed explicitly in the body. The assignment
to `t` occurs after `t` is live; the assignment to `u` uses the already
automatically initialized member.

This model allows direct construction of a member whose type has no default
constructor:

```zax
Connection :: type {
    +++ final : ()(endpoint : Endpoint) = {
        // Establish a Connection.
    }
}

Session :: type {
    connection : Connection

    +++ final : ()(endpoint : Endpoint) = {
        _.connection.+++(endpoint)
    }
}
```

No separate declarative member-initializer list is required.

#### Static lifecycle tracking

The compiler tracks member lifecycle states through constructor control flow. It
should identify, at least where analysis is required by the applicable language
contract:

- use before construction;
- missing construction on a normal completion path;
- construction of one still-live member lifetime more than once;
- conflict between call-site member initialization and constructor-body
  construction; and
- normal return with an incomplete instance.

The tracking is static. Zax does not require per-instance runtime flags.
Compiler debug tooling may add runtime checks, but those checks are not language
guarantees.

Opaque operations and analysis limitations require narrow unsafe permissions or
state assertions rather than unreachable fake calls. Those mechanisms are
captured later in this working record.

#### Constructor declarations and authority

An ordinary constructor:

- is a type-owned `+++` declaration;
- has no result values;
- accepts zero or more ordinary input parameters;
- participates in ordinary parameter qualification and overload selection;
- receives `_` as the current instance under construction;
- may establish final and immutable state without `unsafe pliable`; and
- must establish a complete instance on every normal return.

Construction authority records the value's actual qualifications while allowing
the current instance to be established. It is not a general unsafe escape.

The spelling `+++ final` must retain the declaration and receiver-operand
attachment meanings established by the qualifier and declaration owners. It
must not imply that an already-live ordinary receiver operand exists before
construction.

The complete instance's final and immutable guarantees activate when
construction completes. A helper called during construction may receive
construction authority, but incomplete-member access and escape require the
separate constraints discussed below.

### Construction packets and arguments

#### Construction-packet syntax

The aligned conceptual syntax distinguishes three entry categories:

```zax
[{
    : expression,
    parameterName: expression,
    .memberName = expression
}]
```

- `: expression` supplies a positional constructor argument.
- `parameterName: expression` supplies a named constructor argument.
- `.memberName = expression` supplies direct call-site initialization for a
  stored member.

This syntax is the current construction model and should be used in
programmer-facing construction examples. It is not yet formal grammar and may
be revised if later concrete pressure justifies a change. It avoids fallback
between constructor parameters and stored members.

An entry expression may itself be a declaration expression:

```zax
[{
    : myValue : String,
    name: temporary : String,
    .member = makeMember()
}]
```

The outer `:` or `name:` categorizes the packet entry. The nested expression
introduces and evaluates the temporary. A formatter may add parentheses where
the nesting would otherwise be difficult to read.

#### Positional cursor

Argument mapping is tested separately for each constructor candidate:

1. At packet entry, the positional cursor points to the first parameter.
2. `: expression` binds the current positional parameter and advances.
3. `name: expression` binds that named parameter and resets the cursor to the
   parameter following it.
4. `.member = expression` does not establish a constructor position and
   invalidates the positional cursor.
5. A later named constructor argument may establish the cursor again.
6. A positional entry while the cursor is invalid is not viable.

```zax
[{
    .legs = 2,
    kind: "grizzly",
    : afterKind
}]
```

`afterKind` maps to the parameter after `kind` for each tested candidate.
Different candidates may map it differently. Ordinary viability and ranking
choose a clear winner; an unresolved tie is an error.

Overlapping overload contracts may still produce logic mistakes when later
changes make another candidate a better match. Zax diagnoses ambiguity but does
not prohibit advanced overload sets whose distinctions require care.

Constructors and replacement constructors use the eventual ordinary parameter
default and omission model. A packet may omit an input only when the tested
candidate supplies that input through those shared rules. Exact default-expression
syntax and evaluation remain future function work rather than a
construction-specific fallback.

#### Evaluation, binding, and construction order

Construction-packet entries evaluate strictly from left to right. The comma is a
non-overloadable sequencing operation for this purpose. Other expression
precedence rules still apply inside each entry and must also be defined by the
language.

For each entry in source order:

1. evaluate its expression;
2. immediately initialize or bind the selected input slot according to its
   copy, move, `last`, reference, pointer, or other parameter semantics; and
3. complete the observable effects of that binding before evaluating the next
   entry.

After all inputs are ready, member construction follows the selected
constructor's plan. Packet order does not reorder members.

- A copied input captures the source value when that entry is evaluated.
- A reference input binds at evaluation but observes later changes to its
  referent.
- A move or `last` input performs its eventual transfer semantics during that
  entry's evaluation.
- A call-site member initializer supplies the already evaluated input used when
  the callee's construction order reaches that member.

```zax
source : Integer = 1

value : Example = [{
    .snapshot = source,
    : changeSourceToTwo(source)
}]
```

If `snapshot` selects copy semantics, its input captures `1` before
`changeSourceToTwo` runs. If it selects reference semantics, the bound reference
later observes `2`.

The compiler may use a conceptual temporary to preserve this behavior and may
elide it only when observable copy, move, construction, and destruction behavior
does not change.

A temporary bound to a reference input survives through completion of the
selected lifecycle call. Other packet temporaries survive until their values
have been transferred and their specified destruction point is reached.
Complete temporary and full-expression rules remain shared function and
lifetime work, but they must preserve this ordering.

These evaluation and binding rules apply directly to future ordinary function
calling. Future function-call work should reuse constructor packets as evidence
for left-to-right argument evaluation, immediate parameter binding,
positional/named mapping, temporary lifetime, and overload ambiguity. Ordinary
function-call syntax need not use `[{ ... }]` and has no stored-member entries.

#### Mixed member initialization

A call-site `.member = expression` participates in lifecycle tracking:

- it suppresses that member's ordinary default initialization;
- its expression is evaluated in packet order;
- the member is initialized when the selected constructor plan reaches it;
- the enclosing constructor body begins after its pre-body member initialization;
  and
- an explicit `_.member.+++()` in the selected body conflicts because both
  operations attempt to construct the same member.

The compiler diagnoses that conflict. A narrow unsafe assertion may override the
diagnostic where opaque control flow or mutually exclusive behavior makes the
program valid.

#### Anonymous compatible structures

A future anonymous-structure expression is distinct from a construction packet:

```zax
animal : Animal = {{
    value: first,
    kind: "grizzly",
    legs: 2
}}
```

The exact syntax is only illustrative. The important distinction is that this
form produces one anonymous structural value that may later convert into a
constructor input. It does not provide several named constructor arguments.
Complete structural identity, conversion, and literal syntax remain future
structural-typing work.

### Generated and declared lifecycle operations

#### Five declaration states

The aligned working model distinguishes:

| Form | Meaning |
| --- | --- |
| Declaration with a body | Programmer-defined implementation |
| Bodyless declaration | Unavailable declared requirement that blocks weaker selection and that composition or later completion may fulfill |
| `= default` | Require the compiler to supply the language-defined default implementation |
| `= existing` | Explicitly satisfy this shape using a compatible existing operation |
| `= forbidden` | Permanently prohibit this operation shape |

`forbidden` is a hard prohibition, not a feature toggle. Generation or
compatibility fallback cannot silently restore that operation.

A bodyless declaration claims the signature and suppresses automatic generation
for that shape, but it does not mean `forbidden`. It participates in match-quality
comparison while remaining unavailable for invocation.

- If the bodyless declaration is the best match, selection fails because the
  type does not currently support that operation. The compiler does not fall
  through to a weaker candidate.
- If a latent generated candidate would match better, the compiler requires an
  explicit declaration for that better shape rather than generating it
  silently.
- Composition or later completion may supply the missing implementation.

`= default` selects the language-defined default implementation for that exact
declaration shape. The programmer does not supply the body; the compiler must
supply it or diagnose that the declaration category has no defined default.

```zax
+++ final : ()() = default
```

An arbitrary function does not acquire invented behavior merely because
`= default` parses:

```zax
myFunc : ()() = default // error: no language-defined default implementation
```

#### Demand-driven generation

The programmer need declare only operations intended for use. Zax does not
require an exhaustive list of every theoretical constructor or operator
variation.

When a requested operation could use either a compatible existing declaration
or a newly generated specialization with meaningfully different behavior, the
compiler does not guess. The programmer resolves the demanded shape by:

- providing a body;
- writing `= existing`;
- writing `= default`; or
- writing `= forbidden`.

No error is required for an unused latent ambiguity.

Callable compatibility alone does not establish lifecycle-contract
compatibility. A copy-taking constructor may be callable with a move-shaped
source while failing to provide the intended transfer, source-state, or cost
contract. `= existing` makes such reuse deliberate.

```zax
+++ final : ()(value : Value copy) = {
    // Programmer-defined copy behavior.
}

+++ final : ()(value : Value move) = existing
```

Complete target selection, delegation-cycle prevention, reflection, and
copy/move contracts remain later work.

#### Generated default construction

A generated empty constructor exists only when every required member has a
viable default or declared initialization path.

Declaring a constructor that supplies the default-construction shape suppresses
generation of that shape. Other generated shapes are independent unless their
contracts say otherwise.

For containing types, a generated operation selects member operations that
satisfy the generated operation's contract. It does not silently substitute any
callable member operation merely because one is available. Whether a move-shaped
container operation may copy an unmovable member remains move/copy work and must
preserve visible cost and source-state promises.

Reflection must eventually distinguish declared, generated, explicitly
defaulted, delegated-to-existing, bodyless, and forbidden operations.

### Reconstructive replacement

#### Qualifier requirement

Generated reconstructive replacement is the whole-value transition needed for
an `immutable`, `writable`, `varying` destination.

- `immutable` prevents mutation within one complete value lifetime.
- `varying` permits the place to receive another complete value lifetime.
- `writable` permits the current access path to initiate that transition.

```zax
value : Message immutable writable varying = makeMessage("first")
value = makeMessage("second")
```

Neither immutable instance mutates. The old lifetime shuts down and a new
immutable lifetime is established in the same outer storage.

A `mutable`, `writable`, `varying` destination uses ordinary assignment
selection. It does not receive this generated immutable-value replacement
facility merely because its place is varying.

#### Generated fallback

When no selected custom replacement constructor handles the transition, the
generated fallback:

1. invokes the old instance's ordinary `---`;
2. retains the already allocated outer storage;
3. invokes the selected ordinary `+++`; and
4. performs the ordinary new member initialization process.

The fallback assumes no old member resource is intentionally retained through
the transition.

#### Custom replacement

A selected custom replacement constructor:

- suppresses the enclosing ordinary `---`;
- suppresses the enclosing ordinary `+++`;
- receives the prior representation and resources through the current instance
  `_`;
- has transitional construction authority;
- may retain, assign, move, copy, destroy, reconstruct, or otherwise recycle
  member state;
- uses the existing outer allocation; and
- must establish a complete valid replacement instance on every normal return.

```zax
Registration :: type {
    id : RegistrationId
    settings : Settings

    +++ final : ()(settings : Settings) = {
        _.id = registry.register(settings)
        _.settings = settings
    }

    --- final : ()() = {
        registry.unregister(_.id)
    }

    replacement +++ final : ()(
        settings : Settings
    ) = {
        registry.reconfigure(_.id, settings)
        _.settings = settings
    }
}
```

The custom replacement retains `id`. Running the ordinary destructor first
would unregister it and defeat recycling, so only the selected
`replacement +++` runs.

An untouched member retains its current language-level lifetime and resource
unless another transition operation ends or transfers it. Leaving bits at an
address after ending the member lifetime is not retention.

The compiler tracks the evolving member states. Normal return requires one live
valid value for every required member and exactly one disposition for every
resource the transition ceased to retain.

#### Results

Unlike an ordinary constructor, a replacement constructor may declare zero or
more results:

```zax
BufferOwner :: type {
    buffer : Buffer
    format : Format

    replacement +++ final :
        (retainedCapacity : Boolean)(
            nextFormat : Format
        ) = {
        capacityWasRetained := _.buffer.canReuseFor(nextFormat)

        // Establish the complete replacement instance.

        return capacityWasRetained
    }
}

retained := owner = newFormat
```

The generated reconstructive `=` expression forwards the literal results
returned by the selected replacement constructor. The compiler does not
synthesize, adapt, or infer an arbitrary result value.

A resultless replacement declaration means no result:

```zax
replacement +++ final : ()(rhs : Source) = {
    // Complete replacement.
}
```

The compiler does not implicitly return the reconstructed destination. A
resultless generated fallback likewise supplies no expression result.

Every normal return must complete both:

- the replacement instance; and
- every declared result.

A result may report resource reuse or another domain outcome. It cannot mean
that the destination is half-constructed. The compiler cannot infer the
programmer's semantic intent from a Boolean or name; documentation and API
design must make that distinction understandable.

Result references and pointers remain subject to ordinary lifetime and alias
rules. Expected-result context may participate in selection only as allowed by
the eventual general callable-selection model.

#### Generated and declared `=` selection

Generation does not automatically make a candidate win or lose. A generated
reconstructive `=` and declared domain-specific `=` candidates participate in
ordinary selection according to their declaration shapes and qualifications.
An unresolved equal match is an error.

When a declared compatible operation and a latent generated form would create a
meaningful choice at an actual use, the programmer resolves the demanded shape
with a body, `= existing`, `= default`, or `= forbidden`.

Construction and replacement may be controlled independently:

```zax
+++ final : ()(source : Source) = default

replacement +++ final :
    ()(source : Source) = forbidden
```

This type permits constructing a new value from `Source` while prohibiting
replacement from `Source`.

A domain-specific operator may return an arbitrary declared result. That is
separate from a replacement constructor's declared results.

#### Right-hand evaluation and aliasing

Completing a right-hand expression before replacement begins does not guarantee
independence. A reference result may still point into the destination:

```zax
Document :: type {
    text : String

    replacement +++ final : ()(
        source : String readonly &
    ) = {
        _.text = source
    }

    stringViewFrom final :
        (result : String readonly &)() = {
        return _.text
    }
}

document : Document immutable writable varying
document = document.stringViewFrom()
```

`source` may alias `_.text`. Delegating to `String`'s selected `=` lets `String`
own exact self-alias handling, but it does not erase the general problem.

The same pressure appears in:

```zax
value = value
value = viewOf(value)
value = makeReplacementUsing(value)
```

Possible safe cases include:

- a by-value snapshot established before transition;
- a reference whose exact pointee lifetime is retained until its final use;
- an operation designed to handle exact self-aliasing; and
- a lifetime policy that proves nonconflicting access.

The compiler should diagnose a known or potential conflict where the applicable
language contract requires that analysis. A narrow unsafe permission applies at
the risky replacement use, not at every `&` declaration.

Reference-origin, borrow, provenance, and lifetime-policy mechanisms remain
future pointer and lifetime work. The construction-facing constraint is that a
right-hand reference or pointer may alias the current instance or any contained
member.

#### Pointer preservation

Same-storage replacement does not categorically invalidate every raw pointer.

- A pointer into retained storage may continue to name the same location.
- A pointer to a retained exact member lifetime may remain valid.
- A pointer to a value whose lifetime ended no longer identifies that old live
  value, even when another value later occupies the address.
- Reusing an address does not by itself decide whether the intended pointee
  survives.

Raw pointers are unsafe and may be useful precisely where the programmer accepts
responsibility for these distinctions. Future pointer and lifetime policies may
provide stronger checked guarantees.

### Destruction

#### Ordinary sequence

A concrete type has at most one destructor. It accepts no ordinary input
arguments and returns no results.

Ordinary destruction proceeds as follows:

1. enter terminal destruction authority;
2. run the enclosing `---` body while every not-yet-destroyed member remains
   live;
3. run automatic member destruction after the body; and
4. end the enclosing lifetime.

Automatic member destruction follows reverse declaration/default-construction
order. It does not attempt to reconstruct the arbitrary runtime order chosen by
explicit constructor-body `+++` calls.

Explicit `_.member.---()` calls may run in any destructor-body control-flow
order and suppress the ordinary generated destructor call for that member
lifetime. The compiler tracks whether each member remains live at every normal
destructor exit.

Destruction receives terminal mutable and writable authority. That authority
may dismantle resources but cannot create an access path valid beyond the
enclosing lifetime.

#### Reconstruction during destruction

A destructor may reconstruct a member. This is an advanced terminal operation
that may require a narrow unsafe permission when the compiler cannot prove its
lifecycle behavior.

```zax
--- final : ()() = {
    _.member.---()
    _.member.+++()
    use(_.member)
    _.member.---()
}
```

The two member destructor calls end two different member lifetimes. A mature
tracker should distinguish them rather than report double destruction of one
lifetime.

Generated destruction suppression therefore cannot depend only on seeing one
`---` token. It depends on the member's tracked state at each normal exit.

Whether a statically visible use after explicit destruction is a mandatory
error, warning, or optional tool diagnostic remains future safety and diagnostic
work. Runtime debug checks may help but are not language guarantees.

### Manual and delayed lifecycle operations

Explicit `+++` and `---` are lifecycle operations, not ordinary calls that the
compiler merely notices textually. The compiler tracks the storage/member state
to the extent required by the selected language contract.

`unsafe ???` permits delayed explicit construction:

```zax
value : MyType = unsafe ???
value.+++()
```

The declaration establishes the binding and storage while bypassing ordinary
initialization. The later `+++` performs the default member initialization,
allocation, explicit member construction, and constructor body that would
ordinarily have happened during declaration initialization.

Before successful `+++`, ordinary use of the representation is unsafe. Calling
`+++` again on the same still-live value may duplicate initialization or leak
resources. Static tracking should diagnose cases it can identify.

The eventual declaration-owner wording must distinguish:

- a live binding with storage;
- a valid constructed value lifetime;
- a scheduled destruction obligation; and
- the programmer's unsafe responsibility for representation validity.

Raw or foreign construction that analysis cannot observe uses explicit unsafe
state assertions. Unreachable fake calls are not a lasting mechanism.

#### Stored members initialized with `unsafe ???`

A stored-member initializer of `unsafe ???` is itself the programmer's explicit
unsafe disposition for that member:

```zax
MyType :: type {
    s : S = unsafe ???
    t : T
    u : U
    x : X
    y : Y
    z : Z

    +++ final : ()() = {
        _.t.+++()
        _.t = makeAT()

        _.u = makeAU()

        _.z.+++()
        _.x.+++()
    }
}
```

For `s`:

- storage exists;
- ordinary default initialization and `S.+++()` are bypassed;
- destruction remains scheduled;
- the representation remains indeterminate unless another operation establishes
  it;
- the containing `MyType` constructor may complete without calling
  `_.s.+++()`; and
- the programmer assumes responsibility for every later use and for making
  destruction valid.

The compiler neither initializes `s` automatically nor requires an explicit
`_.s.+++()` call. An opaque operation, assembly, foreign code, or another
low-level mechanism may establish the representation. Leaving `s` unreferenced
is not a language error; it may exist for layout, foreign compatibility,
reflection, or later raw access.

Calling `_.s.+++()` remains legal delayed construction. It transitions the
member from explicitly bypassed initialization into an ordinarily constructed
state. It is optional, not a completion requirement imposed by the containing
constructor.

Lifecycle tracking must therefore distinguish at least:

- ordinarily constructed;
- explicitly bypassed through `unsafe ???`;
- ordinarily constructed after bypass; and
- lifetime ended.

The bypassed state satisfies the enclosing constructor's completion requirement
through explicit unsafe programmer responsibility rather than through proof of
a valid `S` representation. If the scheduled destructor later receives invalid
state, that is a consequence of the original unsafe promise; the compiler does
not require repeated unsafe acknowledgments for the same bypass.

### Unsafe semantic permissions and assertions

#### Required shape

Some valid low-level programs exceed compiler lifecycle or lifetime analysis.
The future mechanism must:

- use the literal `unsafe`;
- identify the exact unsafe category;
- name the resolved member, binding, expression, or operation where applicable;
- apply at the narrowest useful source location;
- affect only the stated analysis or generated behavior;
- permit attached `///` documentation explaining why the claim is valid; and
- have no required runtime check.

Compiler debug tooling may instrument the claim and panic on detected misuse,
but such instrumentation is optional tooling.

Two related forms are needed:

1. an unsafe permission around an operation or source region; and
2. an unsafe lifecycle-state assertion at a precise program point.

Illustrative placeholder:

```zax
/// The callback consumes the view synchronously and stores nothing.
unsafe <lifetime-escape> {
    myCaptureBeyondLifetime(myType.view())
}
```

An unsafe grouping block should not silently create a lexical scope. It either
uses a non-scoping source construct or the established `[[descope]]` mechanism
if ordinary block syntax would otherwise introduce scope.

Illustrative state assertion:

```zax
opaqueInitialize(_.prop2)

/// opaqueInitialize establishes prop2 on every normal path.
unsafe <construction-path-complete> _.prop2
```

These examples capture required semantics, not final syntax.

#### Provisional unsafe-point identifiers

| Identifier | Claim or permission |
| --- | --- |
| `manual-member-construction` | Suppress generated member construction because another operation establishes the member lifetime. |
| `manual-member-destruction` | Suppress generated member destruction because another operation ends or transfers the member lifetime. |
| `construction-path-complete` | Treat a member as constructed on every applicable normal path despite incomplete proof. |
| `destruction-path-complete` | Treat a member as destroyed or dispositioned on every applicable path despite incomplete proof. |
| `construction-at-most-once` | Trust that apparently overlapping construction paths cannot construct one member lifetime twice. |
| `destruction-at-most-once` | Trust that apparently overlapping destruction paths cannot end one member lifetime twice. |
| `partial-instance-access` | Permit a helper to receive or inspect a current instance that is not fully constructed. |
| `partial-instance-escape` | Permit an incomplete current instance or access path to escape or become externally observable. |
| `lifetime-escape` | Permit an access path to cross a boundary where analysis cannot prove its lifetime remains bounded. |
| `replacement-alias` | Permit replacement despite unresolved possible aliasing between destination and a right-hand operand. |
| `terminal-reconstruction` | Permit reconstruction of a member during enclosing destruction. |

These are documentation identifiers, not accepted keywords.

An unsafe assertion may override an incomplete proof or assert the result of an
opaque operation. It cannot make a known-ended value lifetime live again.
Explicit `unsafe ???` already supplies the unsafe acknowledgment for delayed
construction and does not require another unsafe category merely to call `+++`
later.

#### Partial-instance helpers and escape

A synchronous helper may operate on construction state:

```zax
+++ final : ()() = {
    _.first.+++()

    unsafe <partial-instance-access> {
        initializeRemaining(_)
    }
}
```

The current instance retains its actual immutable qualification while
construction authority permits its establishment. An immutable-compatible
helper is preferred; construction authority may permit a mutable-compatible
helper when no immutable form is viable.

A sufficiently capable compiler may prove that the helper touches only live
members and remove the need for unsafe permission under a future language
contract.

Publishing the instance is a stronger operation:

```zax
unsafe <partial-instance-escape> {
    registerGlobally(_)
}
```

The publication may retain a reference, notify subscribers, or reenter through
another access path. Permission for bounded helper access does not imply
permission for escape.

#### Language contract and analysis provenance

Future source may select a language-contract version whose required static
analysis and diagnostics define portable validity. Selection should be optional
for small snippets and may come from:

1. source;
2. project configuration;
3. build invocation or toolchain configuration; or
4. the compiler's stable default.

Omission means the source accepts the active environment's contract, not that it
is compatible with every historical and future version.

Analysis identifiers require visible provenance:

- language-contract identifiers;
- compiler or vendor experimental identifiers; and
- community extension identifiers not yet standardized.

An unknown standard identifier under a supported contract is an error. An
unknown lint extension may be ignored or reported according to policy. An
unknown unsafe semantic extension may be accepted only when the compiler
independently proves the code valid without it; otherwise the compiler reports
that the required semantic extension is unsupported.

When a newer contract mandates analysis that proves an older unsafe marker
unnecessary, source adopting that contract may receive a hard error for the
obsolete marker. A smarter compiler operating under the older contract reports
at most advisory redundancy. Compiler experimentation does not silently change
portable source validity.

#### Lint distinction

Lint suppression is related in source-locality and documentation needs but does
not change language semantics or generated lifecycle behavior. It should not
require `unsafe`.

Future lint work should support:

- narrow rule-specific suppression;
- attached documentation;
- compiler, language-contract, and community provenance; and
- intentional layout or naming exceptions.

```zax
MyType :: type {
    bar : Foo
    foo : Bar // A naming lint may be intentionally suppressed here.
}
```

Layout-efficiency linting must permit intentional ABI layout, readable member
grouping, or insignificant small-type overhead without treating the choice as
unsafe.

### Panic, allocation, async, and concurrency boundaries

#### Panic and allocation

Unresolved panic is fatal graceful crashing. A panic may resolve or be
intentionally suppressed through its own eventual mechanism; otherwise the
process ends.

If a panic is resolved and execution continues inside a constructor or
replacement constructor, normal completion obligations still apply. Zax does
not currently require exception-style rollback to an old value or recoverable
partial-construction unwinding.

Automatic allocation occurs before construction of the allocated value.
Ordinary allocation exhaustion is commonly fatal. Manual allocator APIs may
support explicit checked failure through ordinary control flow where a program
has a real recovery strategy.

A compiler may know which member steps completed and use that knowledge for
debugging or a future cleanup model, but recoverable partial-construction cleanup
is not a current language promise.

#### Async lifecycle operations

Constructors, replacement constructors, and destructors are synchronous in the
current model.

True suspending lifecycle operations are explicitly deferred:

```zax
+++ final async : ()() = {
    _.first.+++()
    await acquireResource()
    _.second.+++()
}
```

Future work would need to define partial-state storage, cancellation, cleanup,
escaped access, scheduling, and final/immutable activation. An async factory can
perform asynchronous work before invoking ordinary synchronous construction.

#### Concurrency

Synchronous replacement is not automatically atomic for other tasks or threads.
Concurrent access, synchronization, place-tracking references, and visibility
across replacement remain future lifetime and concurrency work.

An unsafe partial-instance escape or raw pointer can additionally invalidate
assumptions made by independent code. The construction model records that
pressure without defining a complete concurrency policy.

### Move, copy, `last`, and ownership boundary

Move, copy, and `last` details remain future ownership and lifetime work.
Construction and replacement impose these constraints:

- a by-value copy or move begins an independent destination lifetime;
- transfer must identify who remains responsible for every resource;
- a moved or partially consumed source cannot later destroy resources it no
  longer owns;
- replacement may retain old destination resources independently of how it
  receives the right-hand operand;
- explicit move intent must not silently become an expensive copy unless the
  accepted move contract permits that fallback; and
- generated containing operations select member operations according to their
  lifecycle contracts, not merely call compatibility.

Automatic `deep`, move, copy, or `last` constructor families must not be
promoted before those contracts are accepted.

### Programmer-visible costs, diagnostics, and formatting

#### Costs

Programmers must be able to discover:

- automatic versus explicit member construction and destruction;
- temporaries retained while a construction packet is evaluated;
- copy, move, `last`, and reference binding performed per packet entry;
- generated fallback replacement as ordinary `---` followed by `+++`;
- resources retained or reconstructed by custom replacement;
- copies or snapshots required to avoid aliasing;
- allocator retention and deallocation behavior;
- static-analysis and unsafe-override boundaries; and
- synchronization or future async machinery when those facilities are used.

#### Diagnostics

Applicable diagnostics include:

- no viable constructor or replacement constructor;
- missing, duplicate, unknown, or ambiguously mapped packet entries;
- a positional entry with no current positional cursor;
- equal viable overloads;
- unresolved existing-versus-generated behavior at an actual use;
- use of an operation declared `forbidden`;
- a member that cannot be initialized automatically;
- conflict between call-site member initialization and explicit body
  construction;
- use before construction or after destruction where required analysis proves
  it;
- missing or duplicate lifecycle transitions on normal paths;
- normal completion with an incomplete instance or result;
- possible self or interior alias conflict during replacement;
- partial-instance access or escape without required permission;
- unsupported unsafe semantic extensions; and
- an unsafe assertion whose stated member or operation is inapplicable.

Exact severity, identifiers, wording, and mandatory-versus-tooling boundaries
remain diagnostic and safety work.

#### Formatting

Canonical formatting should preserve:

- contextual adjacency in `replacement +++`;
- visible packet entry categories;
- left-to-right packet entry order;
- ordinary qualifier and parameter ordering;
- explicit `= default`, `= existing`, and `= forbidden`;
- narrow unsafe categories and attached documentation; and
- source grouping without accidentally adding lexical scope.

Formatters must not reorder packet entries because evaluation order is
observable.

### Disposition of replacement-construction input

The indexed raw replacement-construction concerns now have these working
dispositions:

- **Member transitions:** compiler-tracked member lifetimes; custom replacement
  may retain, assign, destroy, or reconstruct; normal return requires complete
  state.
- **Resource retention:** exact untouched member lifetimes may continue; bits or
  address reuse alone is not retention.
- **Raw and typed pointers:** same address does not decide pointee lifetime;
  complete checked behavior remains pointer/lifetime work.
- **Self-replacement and aliases:** direct and interior aliases are real hazards;
  compiler analysis and narrow use-site unsafe permission are required, while
  complete reference-origin rules remain deferred.
- **Generated candidate selection:** generated and declared candidates use
  ordinary selection; actual existing-versus-generated uncertainty requires an
  explicit body, `existing`, `default`, or `forbidden`.
- **Move, copy, and `last`:** constrained by exactly-once resource disposition
  and visible cost; complete semantics remain deferred.
- **Panic:** unresolved panic is fatal; no recoverable rollback is currently
  promised.
- **Reentrancy:** bounded incomplete access and externally observable escape are
  distinct unsafe categories; complete callback and effect behavior is deferred.
- **Async and concurrency:** explicitly deferred with concrete activation
  pressures.

The raw file should retire only after promotion places these findings and
deferrals in their lasting owners.

### Future raw-input needs

Future authorized raw material should preserve examples, not prose-only issue
labels.

#### Function invocation

Future function-call work should consume the constructor packet model for:

- strict left-to-right argument evaluation;
- immediate parameter binding;
- named and positional cursor mapping;
- declarations used as argument expressions;
- temporary lifetime;
- copy/move/reference effects; and
- overload ambiguity.

Ordinary calls need appropriate syntax and do not include stored-member
initializers.

#### Unsafe semantic analysis

Future unsafe-boundary work should consume every provisional unsafe identifier,
with:

1. an ordinary safe example;
2. a compiler-concern example;
3. a clearly provisional unsafe override;
4. the exact claim;
5. the consequence when the claim is false; and
6. the pressure that should standardize or retire the marker.

#### Tooling and linting

Future tooling input should preserve:

- rule-specific documented lint suppression;
- intentional layout and naming exceptions;
- language/compiler/community identifier provenance; and
- the separation between advisory lint suppression and semantic unsafe
  permission.

#### Structural values

Future structural-typing work should preserve the distinction between one
anonymous compatible value and a construction packet containing several
arguments and member initializers.

#### Async lifecycle

Future async work should preserve the partial-state, cancellation, cleanup,
escape, scheduling, and activation concerns shown by a suspending constructor.

No raw file is edited or created by this working-record update.

### Remaining explicit deferrals

The following are constrained but not designed here:

- complete function-call grammar, defaults, overload ranking, and result-context
  selection;
- exact move/copy/`last` contracts and generated families;
- complete reference, pointer, provenance, borrow, and ownership strategies;
- formal unsafe assertion and permission syntax;
- formal language-contract and extension syntax;
- complete lint-control syntax;
- anonymous structural values and conversions;
- union and overlapping-storage transitions;
- mutability-indexed representation changes;
- global and `once` initialization and teardown;
- recoverable panic use cases;
- async lifecycle operations;
- concurrent replacement and synchronization;
- formal diagnostics and reflection; and
- compiler, transpiler, and backend implementation.

### Likely documentation-owner boundaries

If promoted after a documentation fit dry run:

- a construction/destruction owner should define lifecycle phases, ordinary and
  replacement constructors, destructors, member tracking, packets, generation
  states, results, and lifecycle diagnostics;
- declarations and bindings should retain declaration visibility and the
  default/direct/`unsafe ???` boundaries, refined for delayed construction;
- qualifiers should require `immutable + writable + varying` for generated
  reconstructive replacement and retain construction/destruction authority;
- function documentation should own general argument syntax, parameter binding,
  defaults, temporary lifetime, and shared overload selection;
- operator documentation should own generated `=` candidate formation, ranking,
  ambiguity, and expression result behavior;
- pointer and lifetime documentation should own reference origin, alias
  guarantees, pointee lifetime, provenance, move/copy/`last`, and ownership;
- allocation documentation should own storage acquisition, allocator
  provenance, checked manual failure, and deallocation;
- safety documentation should own unsafe semantic permissions, assertions,
  contract-version behavior, and mandatory diagnostic guarantees;
- tooling documentation should own lint provenance and suppression;
- structural-typing documentation should own anonymous compatible values; and
- async and concurrency documentation should own suspension, cancellation,
  synchronization, and cross-task observation.

This ownership map was the input to the documentation fit dry run below.

## Dispositions and promotion dry run

### Dry-run status

The pre-promotion documentation fit dry run succeeded.

The aligned construction, replacement, and destruction findings can be promoted
without creating duplicate current authority, a specification area, or an
implementation-documentation surface. One new cohesive language concept owner
is sufficient. Existing declaration and qualifier owners retain their current
boundaries with focused corrections and links.

No unresolved contradiction blocks promotion. Exact function-call syntax,
operator ranking, pointer/lifetime proof mechanisms, unsafe-control syntax,
structural values, async lifecycle operations, and concurrency remain explicit
deferrals with preservation paths.

This dry run is a proposal for review. It does not authorize promotion.

### Structure proposal

Retain the current repository layout and add:

```text
language/
    construction-and-destruction.md

project/raw/
    analysis-controls.md
    function-invocation.md
```

`language/construction-and-destruction.md` would have the human-facing title
**Zax construction, replacement, and destruction**. The shorter filename keeps
the established lower-case concept-owner style while replacement remains a
first-class section and owned concern.

No new directory, `language/README.md`, specification area, contract area, or
implementation area is needed.

The two proposed raw files have distinct future pressures:

- `analysis-controls.md` preserves unsafe semantic permissions and assertions,
  language/compiler/community provenance, contract-version interaction,
  redundant-unsafe handling, and the boundary between semantic controls and
  lint suppression.
- `function-invocation.md` preserves constructor-derived evaluation, immediate
  parameter binding, positional/named cursor, declaration-expression,
  temporary-lifetime, and overload pressures for future ordinary calls.

Both remain outside ordinary reading paths.

### Lasting ownership map

| Finding cluster | Lasting owner or disposition | Boundary |
| --- | --- | --- |
| Storage, member lifetimes, and complete enclosing lifetimes | New `language/construction-and-destruction.md` | Allocation policy and deallocation remain future allocation work |
| Automatic and explicit member construction | New construction owner | Declaration syntax remains in declarations and bindings |
| Construction packets, positional/named/member entries, cursor behavior, and strict evaluation/binding order | New construction owner | General call syntax and complete default/overload rules move to future function work |
| Constructor declarations, construction authority, completion, and ordinary result prohibition | New construction owner | Qualifier owner retains why construction may establish immutable/final state |
| Body, bodyless, `default`, `existing`, and `forbidden` lifecycle declaration states | New construction owner | General declaration completion and composition remain future declaration/composition work |
| Generated default lifecycle behavior and demand-driven generation | New construction owner | Complete move/copy contracts and arbitrary operator generation remain future owners |
| Immutable + writable + varying eligibility for generated reconstructive replacement | `language/qualifiers.md` | New construction owner defines the lifecycle transition after eligibility is established |
| Generated fallback, custom recycling, member retention, replacement completion, and replacement results | New construction owner | Operator owner later defines complete ranking among arbitrary `=` candidates |
| Assignment-versus-declaration boundary and entry into generated reconstructive selection | `language/declarations-and-bindings.md` | It links to qualifier eligibility and construction mechanics |
| Self-alias and interior-alias hazards during replacement | New construction owner for the construction-facing constraint | Proof, provenance, borrow, and policy mechanisms remain temporary raw lifetime input |
| Raw-pointer preservation across retained or ended member lifetimes | New construction owner for replacement consequences | Complete pointer guarantees remain temporary raw lifetime input |
| Destructor declaration, sequencing, explicit member destruction, terminal reconstruction, and completion | New construction owner | Complete safety guarantees and optional diagnostics remain future safety work |
| `unsafe ???` declaration form and scheduled destruction | `language/declarations-and-bindings.md` | New construction owner defines member and delayed-construction interaction |
| Construction-facing unsafe points and partial-instance access/escape | New construction owner states where a control is required | Proposed raw analysis-control input preserves final syntax, provenance, and contract behavior |
| Fatal-panic construction boundary | New construction owner | Future nonfatal panic use cases remain explicitly deferred |
| Allocation before construction and storage/value separation | New construction owner at lifecycle depth | Allocator APIs, provenance, checked failure, and deallocation remain future allocation work |
| Replacement constructor terminology | `language/terms.md` | Behavior links to the new construction owner |
| Move/copy/`last`, async, concurrency, structural values, type-family representation, and lint controls | Indexed raw inputs | Each future domain owner must consume its preserved examples and constraints |

Each aligned behavior has one lasting conceptual owner. Existing owners may
summarize their local boundary and link to the construction owner without
redefining lifecycle mechanics.

### Proposed construction owner

`language/construction-and-destruction.md` can absorb the findings coherently
with this outline:

1. authority metadata and mental model;
2. storage, member, and enclosing lifetime distinctions;
3. ordinary construction and member tracking;
4. construction-packet syntax, mapping, evaluation, binding, and temporaries;
5. constructor declarations and construction authority;
6. declared, generated, existing, default, bodyless, and forbidden operations;
7. immutable reconstructive-replacement eligibility boundary;
8. generated fallback and custom replacement;
9. replacement member/resource transitions, alias pressure, and results;
10. destruction sequencing and terminal authority;
11. `unsafe ???`, manual lifecycle operations, partial-instance access, and
    unsafe-boundary requirements;
12. panic, allocation, pointer, async, and concurrency boundaries;
13. costs, diagnostics, formatting, and maturity.

The owner remains human-developer-facing conceptual design. It does not define
formal grammar, diagnostic identifiers, compiler state machines, lowering, C++
mapping, ABI, or conformance.

### Current owner updates

#### `language/declarations-and-bindings.md`

Focused promotion changes should:

- link constructor selection and lifecycle behavior to the new construction
  owner from metadata and the direct/default initialization sections;
- retain ownership of `unsafe ???` as a declaration form;
- clarify that `unsafe ???` is an explicit initialization bypass that can
  satisfy a containing constructor through unsafe responsibility and can later
  receive explicit `+++`;
- distinguish ordinary resultless constructors/destructors from resultful
  replacement constructors;
- require `immutable + writable + varying` for the generated reconstructive
  assignment scenario;
- link the compiler-owned replacement skeleton and replacement results to the
  new construction owner; and
- leave complete operator ranking deferred.

#### `language/qualifiers.md`

Focused promotion changes should:

- retain ownership of the three qualifier axes and complete qualification
  attachment;
- revise the operation table and reconstructive section so the generated
  lifecycle candidate requires `immutable + writable + varying`;
- state that `mutable + writable + varying` uses ordinary assignment selection;
- link construction activation, replacement mechanics, and terminal destruction
  sequencing to the new construction owner;
- remove the obsolete claim that replacement constructors cannot return
  results;
- retain only the qualifier-facing explanation of why successive immutable
  lifetimes are coherent; and
- update receiver-operand and maturity summaries to include the immutable
  requirement.

#### `language/terms.md`

Update the replacement-constructor entry to link complete behavior to the new
construction owner. Add concise entries for construction packet, lifecycle
operation, reconstructive replacement, and value lifetime. The terms page
should distinguish the compiler-owned reconstructive-replacement operation from
its optional type-defined replacement-constructor customization, define concise
vocabulary, and link its owner without duplicating packet, generation,
member-state, or result rules.

### Public navigation and legacy routes

#### `index.md`

Add **Construction, replacement, and destruction** to:

- the `Start here` task list; and
- the `Current conceptual design` list.

Keep `ctor-dtor.md` in the legacy memory/lifetime group because it still contains
unreviewed global, `once`, allocator, deep-copy, and other legacy material.

#### `ctor-dtor.md`

Replace the current scoped qualifier/declaration notes and duplicated
replacement-constructor subsection with one concise route to:

- declarations and bindings for declaration initialization;
- qualifiers for eligibility and authority; and
- the new construction owner for constructors, packets, generation,
  replacement, and destruction.

Leave the remaining page explicitly legacy. Its global/`once`, allocation,
move/copy/`last`, and other unreviewed proposals remain evidence rather than
current behavior.

#### `type-definition.md`

Update the introductory routing note so current member construction,
construction packets, generated lifecycle operations, and `unsafe ???` member
interaction route to the new owner while complete type-definition, union, and
function-type behavior remains legacy.

#### `operator.md`

Replace the current generated-replacement paragraph with the promoted
immutable-only eligibility and a link to the new construction owner. Retain
complete arbitrary operator declaration, candidate ranking, module lookup, and
conversion as legacy/future operator work.

#### `memory-allocation.md` and `custom-allocators.md`

Add concise top-of-page routes for the accepted storage-versus-construction,
manual lifecycle, and replacement boundaries. Leave complete allocator syntax,
ownership policies, arenas, and deallocation behavior as legacy input. Do not
perform unrelated example cleanup.

### Raw-input disposition

#### Retire `project/raw/replacement-construction.md`

The replacement input can retire after promotion because every material concern
has one of these destinations:

- promoted construction behavior in the new owner;
- qualifier eligibility in `language/qualifiers.md`;
- declaration entry behavior in `language/declarations-and-bindings.md`;
- deferred pointer/lifetime material in `project/raw/lifetimes.md`;
- deferred safety and panic material in `project/raw/safety.md`;
- deferred unsafe/diagnostic-control material in proposed
  `project/raw/analysis-controls.md`;
- deferred function-call material in proposed
  `project/raw/function-invocation.md`;
- deferred async material in `project/raw/async.md`;
- deferred structural-value material in `project/raw/structural-typing.md`; or
- deferred representation-family material in
  `project/raw/mutability-indexed-type-families.md`.

Remove its row from `project/raw/README.md` only after all destination updates
land in the same promotion change set.

#### Update `project/raw/lifetimes.md`

Preserve concrete examples for:

```zax
value = value
value = viewOf(value)
value = makeReplacementUsing(value)
```

Record:

- direct and interior reference origins;
- exact retained pointee lifetime versus same-address reconstruction;
- raw-pointer responsibility;
- move/copy/`last` transfer and exactly-once resource disposition; and
- the pressure for lifetime policies that can prove or reject replacement while
  aliases exist.

#### Update `project/raw/safety.md`

Preserve:

- construction-state and destruction-state guarantee questions;
- safe-versus-unsafe manual lifecycle manipulation;
- incomplete current-instance access and publication;
- fatal panic as the baseline and use-case-driven activation for nonfatal panic;
- the requirement that known-invalid lifetime use cannot be made valid merely
  by an unsafe assertion; and
- concrete ordinary, diagnosed, and unsafe-override examples.

#### Add `project/raw/analysis-controls.md`

Preserve:

- unsafe permission regions and lifecycle-state assertions;
- every provisional construction unsafe-point identifier;
- non-scoping or explicitly descoped unsafe grouping;
- attached `///` rationale;
- language-contract, compiler, and community provenance;
- handling of unknown semantic versus lint extensions;
- redundant unsafe behavior across contract versions; and
- the boundary between semantic unsafe controls and advisory lint suppression.

Each item should include a safe example, compiler concern, provisional override,
exact claim, failure consequence, and retirement pressure.

#### Add `project/raw/function-invocation.md`

Preserve construction-derived examples and constraints for:

- strict left-to-right argument evaluation;
- immediate copy/move/reference/`last` parameter binding;
- named and positional cursor behavior;
- declarations as argument expressions;
- temporary lifetime;
- overload ambiguity and later contract changes that alter a previously selected
  best match; and
- the requirement that ordinary function-call syntax not inherit stored-member
  entries from construction packets.

#### Update `project/raw/async.md`

Add the suspending-lifecycle example and preserve partial-state storage,
cancellation, cleanup, escape, scheduling, and activation questions. Keep
ordinary lifecycle operations synchronous until a concrete future work item
changes that design.

#### Update `project/raw/structural-typing.md`

Preserve the distinction between:

- a construction packet containing several constructor arguments and member
  initializers; and
- one anonymous compatible structural value passed as a single construction
  input.

Include both concrete source examples and leave anonymous-value syntax
explicitly candidate.

#### Update `project/raw/mutability-indexed-type-families.md`

Add the replacement constraint that differently represented variants cannot use
ordinary same-storage replacement unless the existing place satisfies the
selected representation's size, alignment, and layout. Cross-variant
transformation otherwise requires separately suitable storage and construction.

#### Update `project/raw/feature-catalog.md`

Record dispositions for:

- constructors and destructors to the new current owner;
- default initialization to declarations plus the construction owner;
- allocation-versus-initialization separation to the construction boundary
  while complete allocation remains future; and
- warning-suppression and analysis-control pressure to the proposed raw
  analysis-control input.

The preserved catalog remains until all other entries receive dispositions.

#### Update `project/raw/README.md`

- remove the retired replacement-construction row;
- add indexed rows for analysis controls and function invocation;
- retain and, where useful, narrow activation pressure for lifetime, safety,
  async, structural-typing, type-family, and feature-catalog inputs.

### Files reviewed with no promotion edit

The following files contain related legacy material but need no promotion edit:

- `README.md` already routes public readers through `index.md`;
- `language/vision.md` owns direction rather than an exhaustive concept index;
- `language/source-structure.md` does not own lifecycle behavior;
- `functions.md` remains legacy input, while new function-call pressure moves to
  indexed raw input;
- `pointers.md` remains legacy input for complete pointer and lifetime behavior,
  while current construction-facing constraints live in the new owner;
- `warnings-errors.md` remains legacy because exact diagnostic identifiers and
  severities are deferred;
- `concurrency.md` remains legacy because concurrent replacement is deferred;
- `arrays.md`, `basics.md`, `casting.md`, `except.md`, `optional.md`,
  `partial.md`, `nothing.md`, `handle-hint.md`, `strong-weak.md`, and
  `meta-types.md` contain incidental constructor/destructor language but no
  current authority that promotion must change; and
- `project/README.md` continues to point to active work item `005` until closure.

This no-edit disposition avoids a corpus-wide legacy cleanup while leaving all
material available as evidence.

### Focused reading after promotion

The public path becomes:

1. `index.md`;
2. `language/construction-and-destruction.md`;
3. linked declarations or qualifiers only when the reader needs those
   prerequisites.

The new construction owner should link:

- declarations and bindings for declaration forms and `unsafe ???`;
- qualifiers for place, value, and access promises;
- terms for cross-cutting vocabulary; and
- no raw or archived file for required meaning.

Raw inputs remain discoverable only through `project/raw/README.md` and future
work activation. Legacy `ctor-dtor.md` remains discoverable under the legacy
index, not as a current prerequisite.

### Exact proposed promotion change set

The coherent promotion should make these changes together:

1. Add `language/construction-and-destruction.md`.
2. Update `index.md`.
3. Update `language/declarations-and-bindings.md`.
4. Update `language/qualifiers.md`.
5. Update `language/terms.md`.
6. Update `ctor-dtor.md`.
7. Update `type-definition.md`.
8. Update `operator.md`.
9. Update `memory-allocation.md`.
10. Update `custom-allocators.md`.
11. Add `project/raw/analysis-controls.md`.
12. Add `project/raw/function-invocation.md`.
13. Update `project/raw/lifetimes.md`.
14. Update `project/raw/safety.md`.
15. Update `project/raw/async.md`.
16. Update `project/raw/structural-typing.md`.
17. Update `project/raw/mutability-indexed-type-families.md`.
18. Update `project/raw/feature-catalog.md`.
19. Update `project/raw/README.md`.
20. Delete `project/raw/replacement-construction.md` after its destinations are
    present.

Do not partially promote only the new owner or qualifier correction. The raw
dispositions and scoped legacy routes prevent orphaned input and duplicate
authority.

### Promotion validation plan

After an authorized promotion:

- verify every accepted construction rule has one current owner;
- verify generated reconstructive replacement consistently requires
  `immutable + writable + varying`;
- verify ordinary constructors remain resultless and replacement results are
  forwarded exactly without an implicit destination result;
- verify packet syntax and strict evaluation/binding order agree across all
  promoted examples;
- verify `unsafe ???` member and delayed-construction behavior agrees between
  declarations and construction;
- verify lifecycle declaration states use body, bodyless, `default`, `existing`,
  and `forbidden` consistently;
- verify the new owner contains no implementation mapping or formal-specification
  claims;
- verify public navigation reaches the new owner;
- verify current owners do not depend on legacy, raw, or archived files;
- verify every raw file is indexed and the replacement raw input has no
  undispositioned material;
- verify all relative links and heading anchors;
- verify Markdown and direct repository rendering; and
- preserve the staged/unstaged review boundary until separately authorized.

### Closure and archive effects

Promotion does not itself close work item `005`.

After promotion validation, closure still requires separate discussion,
alignment, and authorization to:

1. mark this working file historical and audit-only;
2. move the historical file to
   `project/archive/work/005-construction-destruction-and-replacement.md`
   without changing its filename;
3. align the fixed initiating input and focused reading scope for `006`;
4. create the new active work item; and
5. update `project/README.md`.

Current documents must not link to archived `005` for meaning.

### Dry-run conclusion

The documentation architecture can absorb the aligned findings coherently. The
proposed structure adds one current concept owner and two justified raw inputs,
retires the consumed replacement input, preserves focused reading, and avoids
duplicate authority.

No additional language-design issue or documentation-architecture blocker was
discovered during the dry run. Promotion can proceed after the language
maintainer reviews and aligns this exact proposal and separately authorizes the
twenty-file promotion change set.
