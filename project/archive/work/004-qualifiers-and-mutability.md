# 004: Qualifiers, mutability, and access capabilities

| Field | Value |
| --- | --- |
| Status | Historical / non-normative / audit-only |
| Work Item | `004` |
| Created | 2026-08-21 |
| Closed | 2026-08-22 |
| Owns | The bounded review defined by the fixed initiating input |
| Does Not Own | Current qualifier semantics, formal grammar, compiler behavior, or any meaning required by current documentation |

## Non-authority notice

This file is a completed historical working record. It remains
non-authoritative and audit-only. Current documents do not depend on it for
meaning; promoted behavior belongs to its lasting owners.

## Fixed initiating input

This section records the information known and aligned when work item `004` was
created. It is intentionally incomplete rather than exhaustive and must not be
rewritten as the work develops.

### Initiating concern

Develop a coherent human-developer-facing model for the independent qualifier
axes established by [Zax declarations and bindings](../../../language/declarations-and-bindings.md):

- place replacement through `final` and `varying`;
- underlying value change through `mutable` and `immutable`; and
- access capability through `writable` and `readonly`.

Determine how those axes combine, default, propagate, narrow, conflict, and
participate in declaration and call boundaries.

### Motivating pressure

Work item `003` established the qualifier axes and terminology while explicitly
deferring their complete semantics, syntax, conversions, propagation, and
interactions.

The terminology now appears throughout legacy documentation, but detailed
mutability and qualifier behavior remains unreviewed. Functions, references,
pointers, captures, operator selection, move and copy, concurrency, construction,
lifetime, and structural typing all require stable qualifier constraints.

Reviewing qualifiers next prevents mechanically migrated legacy examples from
being mistaken for accepted detailed behavior.

### Known assumptions

- The current [language vision](../../../language/vision.md) is accepted
  foundational direction.
- [Zax declarations and bindings](../../../language/declarations-and-bindings.md)
  is accepted conceptual design.
- The `final`/`varying`, `mutable`/`immutable`, and
  `writable`/`readonly` axes and names retain their accepted meanings.
- `final` qualifies a storage place and does not imply immutable contents.
- `readonly` constrains one access path and does not imply that no other access
  can mutate the value.
- `immutable` is a stronger underlying-value guarantee.
- Ordinary views may reduce capabilities but may not increase them.
- References and captures preserve or reduce source-place capabilities.
- Move and copy are contextual operations rather than persistent value
  qualifiers.
- Exact reference rebinding, ownership, move, and copy behavior remains later
  work unless an immediate contradiction requires a constraint.
- Legacy `pliable` and `unpliable` behavior is evidence rather than accepted
  design.

### Known inclusions

- Defaults and explicit spellings for all three axes.
- Qualifier attachment to variables, direct values, references, parameters,
  results, captures, and members at the depth needed for a coherent model.
- Compatible repetition and conflicting qualification.
- Safe capability-reduction rules.
- Whether and how writable access can be recovered after a readonly view ends.
- Function and receiver effects at the conceptual depth required by the
  qualifier model.
- Contained-member and nested-value implications.
- Reference and capture propagation constraints.
- Interaction between final places and movement out of a value.
- Disposition of `pliable` and `unpliable`.
- Canonical formatting and immediate diagnostics.
- Constraints on functions, pointers, operators, concurrency, construction,
  lifetime, and structural typing.
- Candidate lasting owner and documentation boundaries.

### Known starting boundaries

- Complete pointer, reference, and aliasing design.
- Complete lifetime and ownership strategies.
- Complete move/copy selection and moved-from states.
- Full function declaration, capture, overload, and invocation behavior.
- Complete operator ranking and generated candidates.
- Complete concurrency transfer guarantees.
- Structural identity, equivalence, layout, conversion, and reflection.
- Formal grammar, compiler data structures, lowering, or implementation.

These areas are not part of the initiating question and need not be explored to
begin. They do not prohibit following, recording, or dispositioning material
consequences.

### Intentionally unresolved framing

- Whether `writable` must be written explicitly or is normally represented by
  omission.
- Whether immutability is recursively deep by default.
- Whether a final place can be moved from when the moved-from value remains live.
- Whether readonly access permits hidden caches or bookkeeping.
- Whether `pliable` expresses a necessary capability or a legacy workaround.
- Whether every qualifier is part of a type-use expression, a place view, a
  declaration modifier, or some combination.
- Whether qualifier ordering is semantically meaningful or only canonical
  formatting.
- Which capability relations are implicit, explicit, unsafe, or impossible.

### Initial stopping guidance

Stop when the work has:

- established a coherent model for all three axes;
- established defaults and attachment;
- defined safe capability-reduction relationships;
- dispositioned `pliable` and `unpliable`;
- constrained references, captures, functions, members, operators, move/copy,
  and concurrency without designing them completely;
- identified programmer-visible diagnostics and formatting;
- identified structural-typing and lifetime consequences;
- identified a plausible lasting owner and its boundaries; and
- gathered enough information for a pre-promotion documentation fit dry run.

Do not promote findings, update language examples broadly, archive this work
item, or begin work item `005` without the separately required discussion,
alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../../documentation.md) - governs ownership,
  focused reading, numbered work, and promotion.
- [Language vision](../../../language/vision.md) - provides the accepted explicit
  policy, visible cost, safety, readability, and progressive-complexity
  principles.
- [Zax declarations and bindings](../../../language/declarations-and-bindings.md) -
  provides the accepted qualifier axes and declaration constraints.
- Former `mutable.md` legacy evidence - supplied the primary historical input
  for qualifier combinations, pliability, function effects, and distinct
  mutable/immutable implementations; the file was later removed after current
  behavior and future input were relocated.
- Focused qualifier-preservation and conversion material in
  [casting](../../../casting.md) - preserves legacy evidence for capability
  conversion.

### Consequence-driven

- Read focused [function](../../../functions.md) sections when receiver or function
  qualification becomes necessary.
- Read focused [pointer and reference](../../../pointers.md) material when
  qualifier propagation through indirection becomes necessary.
- Read focused [compiler directive](../../../compiler-directives.md) material when
  default qualifier policy must be tested.
- Read focused [concurrency](../../../concurrency.md) material when immutable or
  readonly guarantees cross threads.
- Read the [raw structural-typing input](../../raw/structural-typing.md) only when
  qualifier participation in structural identity becomes material.
- Read construction, lifetime, move/copy, operator, or safety material only when
  an immediate qualifier contradiction requires it.
- Propose an update or new raw input when a material finding must be preserved
  for future work. Discussion, alignment, and explicit edit authorization still
  apply.
- Inspect focused Git history only when the origin or former purpose of an
  unusual qualifier choice becomes consequential.
- Expand beyond this list whenever discussion or a material finding establishes
  a concrete need. State why the additional reading is relevant and keep it
  focused.

Material not listed in this reading scope is neither required nor prohibited. It
remains available when discussion or a concrete consequence makes it relevant.

### Audit-only

- `project/archive/`, including work items `001` through `003`.

Audit-only material requires direct language-maintainer instruction or a
specific audit or recovery need.

## Working record

### Review status

The language maintainer and agent aligned on the findings in this working record
during review. They remain non-authoritative aligned findings, not accepted
language design, until a separately authorized promotion incorporates them into
their lasting owners.

### Three independent qualifier axes

Zax uses three independent qualifier axes:

| Axis | Stances | Governs | Conceptual attachment |
| --- | --- | --- | --- |
| Place replacement | `final` / `varying` | Whether a storage place may receive a replacement value | The immediately qualified place |
| Value mutability | `mutable` / `immutable` | Whether the underlying value may ever change | The value or supported form of a type |
| Access capability | `writable` / `readonly` | Whether a particular access path may modify the value | The access path |

The terms **place-replacement qualifiers**, **value-mutability qualifiers**, and
**access qualifiers** distinguish the axes without presenting either stance as
negative.

The axes remain independent:

- a final place may hold a mutable value available through writable access;
- a varying place may expose a readonly view;
- an immutable value may occupy a varying place that can later receive a
  different immutable value; and
- readonly constrains one path without claiming that no other path can mutate
  the value.

`final` therefore does not imply `immutable`, and `readonly` does not imply that
the underlying value is immutable.

### Layered attachment

Qualification follows the entity being constrained rather than flattening all
words into one declaration-wide property:

1. A declared binding has its own place qualification.
2. A directly stored value has its value-mutability qualification.
3. An alias or member projection has an access qualification.
4. A referenced or pointed-to place has a place qualification independent from
   the binding that stores the reference or pointer.
5. Every further pointer or reference level may introduce another independently
   qualified place and access path.

This distinction is necessary for forms such as a varying pointer to a final
pointer to a final referent. Replacing the outer pointer does not replace either
inner place.

Dereferencing does not change the qualifications of the dereferenced instance.
Converting between pointer and reference forms preserves the pointee-level
qualifications. The place qualification of the pointer or reference binding
itself does not transfer to the newly declared binding: a final pointer may
still produce a separately varying reference binding, and a final reference
binding may produce a separately varying pointer binding, while the referent's
own final qualification remains intact.

Exact multi-level pointer and reference grammar, rebinding, lifetime, ownership,
and alias analysis remain later work. The required constraint is that changing
indirection form cannot silently strip qualifications from the referent or
increase ordinary access authority.

### Defaults, inheritance, and explicit qualification

The aligned baseline defaults are:

- place replacement: `varying`;
- value mutability: `mutable`; and
- access capability: `writable`.

Every qualifier stance that may be omitted may have its omission default changed
for the applicable source context. Sensible baseline defaults remain part of the
language. A future mode in which an axis has no fallback default and omission is
an error is explicitly deferred as a possible later extension.

Qualification resolves independently at every applicable layer:

1. preserve inherent or inherited qualifications supplied by the source,
   referent, or resolved type;
2. apply explicit qualifications at their stated layers;
3. apply the applicable contextual defaults only to still-unresolved axes; and
4. diagnose contradictions in the resulting combination.

Defaults never override qualifications carried by the source:

```zax
source : Foo immutable

sameView : immutable & = source
reader : readonly & = source
writer : writable & = source // error: immutable source cannot provide writable access
```

If `immutable` and `writable` are configured simultaneously as omission
defaults, that configuration itself is legal. A declaration to which both
defaults apply is contradictory and fails at that declaration. This permits a
project to require an explicit qualifier choice without making the default
configuration invalid in isolation.

Each declaration context uses its applicable defaults, including local and
member places, type uses, parameters, results, receiver operands, and each
pointer or reference layer. Exact directive spelling, lexical scope, stacking,
and precedence between future default sources remain later default-policy work.

### Repetition and conflicts

Repeating the same qualifier token at the same syntactic point is not useful
compatible combination:

```zax
foo final final : Foo = makeFoo() // error: duplicate qualification
```

Compatible restatement is allowed when the same entity receives the same stance
from distinct sources, including:

- source inheritance and an explicit destination spelling;
- declaration-name and type-use spellings that meet at one direct-storage
  boundary;
- a default and an explicit spelling;
- alias or generic substitution; and
- independent pointer or reference layers that happen to use the same word.

```zax
source : Foo immutable
reader : immutable & = source // legal explicit restatement
```

Conflicting stances applied to the same entity and layer are errors. The
diagnostic must distinguish a real conflict from identical words that qualify
different indirection levels.

### Canonical ordering and source preservation

Canonical ordering follows conceptual attachment. On the declaration-name side:

1. place replacement (`final` / `varying`);
2. access-bypass behavior (`unsafe pliable` / `unpliable`); and
3. the declaration colon.

On the type-use side, reading outward from the base type:

1. base type;
2. value mutability (`mutable` / `immutable`);
3. access capability (`writable` / `readonly`);
4. referent-place replacement (`final` / `varying`); and
5. pointer or reference marker.

For example:

```zax
source final : Foo immutable = makeImmutableFoo()
view final unpliable : Foo immutable readonly final & = source
escape final unsafe pliable : Foo immutable readonly final & = source
```

`unsafe pliable` is an indivisible pair. Explicit `unpliable` remains legal even
though unpliable behavior is the default.

A formatter may normalize ordering, spacing, and layout. It may not add or
remove explicit qualifiers. Compatible restatement can be deliberate and
omission may depend on contextual defaults. A linter may identify redundant
explicit qualification without changing source.

### Ordinary promise strengthening

Ordinary views may narrow authority and thereby strengthen the promise made by
the new path:

- `writable` to `readonly`;
- `varying` to `final`;
- a mutable value to a readonly path; and
- an immutable value to a readonly path.

The reverse directions weaken promises and are not ordinary conversions:

- `readonly` to `writable`;
- `final` to `varying`; and
- `immutable` to `mutable`.

Mutable or readonly state does not thereby become immutable. Readonly promises
only that the current path will not mutate; immutable promises that the
underlying value will not change through ordinary safe behavior.

Creating a readonly view does not hide or suspend another path:

```zax
source : Foo
reader : Foo readonly & = source

source.change() // legal through the original writable path
```

`reader` remains readonly. Writable access is not recovered from it when the
view ends; the independent source path retained its original qualification
throughout. Any future exclusive-borrow mechanism would be separate lifetime
and aliasing design.

### Casts and value-producing mutability changes

An explicit unsafe cast may weaken qualifications, including producing a
mutable or writable pointer/reference view from immutable or readonly input.
The unsafe operation must be explicit. The complete cast lattice and exact cast
syntax remain later work.

An unsafe cast produces a differently qualified view. `unsafe pliable` instead
retains the original qualifications and locally ignores their restrictions. A
new ordinary or explicitly unpliable alias therefore observes the retained
qualifications again.

An ordinary alias conversion from mutable to immutable is not safe. It cannot
promise that no other mutable alias will change the same value. Mutable to
readonly is safe because it constrains only the new path.

A new immutable value may be safely produced from mutable input when the
operation establishes the full deep-immutability guarantee. Possible cases
include:

- constructing an independent immutable value;
- making a sufficiently independent copy; or
- consuming mutable storage for which the compiler proves exclusive authority
  over all storage covered by the immutable guarantee.

The consuming transition ends the former mutable capability. A temporary
immutable view that later becomes mutable again is not this operation; ordinary
temporary non-mutating access is readonly. Exact uniqueness analysis, freezing,
copy selection, cost visibility, and syntax are future work.

### Deep immutability and construction

Immutability is recursively deep over contained values. An immutable value's
ordinary safe behavior does not permit any contained value to change. Pointer
and reference levels retain their own qualifications; deep immutability does not
silently rewrite every externally referenced pointee.

Construction has authority to establish final and immutable state without
`unsafe pliable`. The guarantees activate when the full instance, including all
contained parts, has completed construction and becomes ordinarily observable.
If construction does not complete, no fully constructed instance becomes live.

Zax constructors do not report failure through constructor results, and Zax has
no exception mechanism. Partial cleanup, panic behavior, premature publication,
and escaped construction-time aliases remain later construction and lifetime
work.

### `unsafe pliable` and `unpliable`

Legacy `pliable` is refined rather than retained unchanged:

- pliability must be written `unsafe pliable`;
- `unpliable` is the ordinary default and may be written explicitly;
- pliability is an access-path bypass modifier, not a fourth qualifier axis; and
- it locally ignores final, readonly, and immutable restrictions while leaving
  those underlying qualifications intact.

`unsafe pliable` cannot be selected through an omission default or a default
directive. Its purpose requires a local, explicit unsafe marker. Legacy
`[[variables=pliable]]` is therefore retired. `unpliable` is the only ordinary
behavior, so no directive is needed to select it.

The bypass applies to the target qualifications reached through that access
path. It does not silently make the pliable binding's own final place varying:

```zax
source final : Foo immutable = makeImmutableFoo()
escape final unsafe pliable : Foo immutable readonly final & = source
```

`escape` cannot be rebound because its binding is final. Operations through it
may explicitly bypass the source's final, readonly, and immutable promises.

This escape supports narrow cases such as mutex use, hidden caches, lazy
bookkeeping, legacy integration, and other operations whose local safety cannot
be expressed by the ordinary axes. It must be used with extreme caution because
other aliases may continue relying on the retained qualifications.

#### Non-propagation

Pliability is removed at every new boundary unless explicitly reintroduced:

- assigning or aliasing from a pliable source does not make the destination
  pliable;
- dereferencing does not carry pliability into another pointer or reference
  level;
- operands may use their explicit bypass for the current operation, but an
  operator result receives only the result qualifications declared by the
  selected candidate;
- function results cannot inherit or declare pliability as a returned property;
  the destination binding is unpliable unless it explicitly reintroduces the
  bypass; and
- captures preserve the source qualifications but do not preserve source
  pliability.

```zax
source : Foo readonly
escape unsafe pliable : Foo readonly & = source

explicitView unpliable : Foo readonly & = escape
ordinaryView : Foo readonly & = escape
```

Both new views respect the retained readonly qualification.

An explicit closure capture may create a new `unsafe pliable` capture binding.
The capture clause is then the visible unsafe introduction site. Exact capture
syntax remains function and capture design.

#### Local aliases, members, and parameters

A local alias may introduce the narrowest bypass:

```zax
useValue final : ()(input : MyType immutable) = {
    local unsafe pliable : MyType immutable & = input
    local.callMutableFunction()
}
```

A member may advertise that it is an unsafe interior-mutation escape:

```zax
MyType :: type {
    cache unsafe pliable : LegacyCache
}
```

The legacy proposal that a member spelled only `mutable` could pierce readonly
or immutable access is not retained. `mutable` belongs to the value-mutability
axis; violating another qualifier promise requires explicit `unsafe pliable`.

A parameter may also introduce a local bypass:

```zax
useValue final : ()(input unsafe pliable : MyType immutable) = {
    input.callMutableFunction()
}
```

Candidate selection still uses the parameter's declared `MyType immutable`
requirements. After the argument is bound, the parameter access path gains the
explicit unsafe bypass. The caller need not pass a pliable argument, and the
bypass does not cascade through every source-level caller. The parameter
spelling advertises at the function boundary that its implementation may violate
the retained argument promises.

A function may instead accept ordinary writable access. A caller or intermediate
function can then create a local `unsafe pliable` path before the call. Both
forms are legitimate: a local alias minimizes the unsafe region, while a
pliable parameter makes a function-wide argument escape visible in its
declaration.

The source-level bypass need not cascade through every wrapper signature.
Compiler and tooling analysis must nevertheless preserve the possibility of
mutation through transitive aliases; it may track an internal effect without
requiring every caller to repeat `unsafe pliable`.

### Unsafe behavior and optimization

`unsafe pliable` has defined mechanical semantics: it permits the specified
access despite retained qualifications. The operation is not itself undefined
behavior, because the compiler must preserve the requested access and mutation.

The programmer assumes responsibility for consequences. Broken invariants, data
races, invalid states, lifetime violations, or other misuse may produce
undefined behavior. Debug tooling may insert checks and panic when it detects a
violation, but such instrumentation is not a language guarantee and need not
exist in a release build.

An `unsafe pliable` escape is an optimization invalidation boundary. A compiler
may rely on immutable stability only where it proves that no relevant unsafe
escape may mutate the storage. Ordinary aliases remain typed immutable, but the
compiler cannot assume their observed value remains unchanged across a
potentially aliasing unsafe mutation.

Future implementations must preserve this programmer-visible behavior in their
chosen backend representation. Language documentation owns the behavior, not a
mapping to C++, LLVM, or another implementation mechanism.

### Parameters, results, and captures

Parameters resolve all three axes at the layers expressed by their prototypes.
At a call boundary, ordinary matching may preserve or strengthen promises but
may not silently weaken an argument's qualifications. Unsafe casts and explicit
parameter pliability are the distinct escape mechanisms described above.

Function results are governed by the resolved prototype:

- a returned pointer or reference carries the prototype's per-level
  qualifications;
- a returned by-value value carries its declared value mutability; and
- the caller's destination introduces its own independently resolved place
  qualification.

An inferred or generic result ultimately resolves to such a prototype.
Complete generic result resolution is deferred.

Captures preserve or strengthen the captured source qualifications. They never
inherit pliability implicitly. A capture that needs the bypass must introduce
`unsafe pliable` explicitly at the capture site.

### Receiver operands and type-defined operations

The **receiver operand** is the implicit operand representing the instance on
which a type-defined function or operator acts. Use the complete term
"receiver operand" where context permits; do not use "receiver object." Zax is
not object-oriented.

Outside invocation or operator-selection discussion, **current instance**
describes the instance whose function body is executing. A named type uses its
own name for type-level self-reference.

All three qualifier axes may constrain a receiver operand and participate in
candidate selection. No operator, including `=` or `+=`, receives a special
language rule merely because its conventional use often replaces or mutates a
left operand. A domain-specific operator may accept a final, readonly, or
immutable receiver operand when its declared behavior is compatible.

For example:

```zax
MyType :: type {
    x : Integer

    operator binary '=' final :
        (result : Boolean)(lhs : Integer, rhs : String) writable final = {
        _.x = 42 // legal: the receiver operand is writable
        return true
    }
}
```

`_` receives the declared receiver-operand qualifications like any other
qualified place. `final` does not prohibit operators named `=`; it constrains
which candidate can be selected for an operation involving `_`. If the body
evaluates `_ = value`, it performs ordinary operator selection using the final
qualification of `_`; a conventional replacement candidate requiring varying
is unavailable, but a final-compatible domain-specific candidate may be viable.
Likewise, readonly prevents selection only of a candidate requiring writable
access through that operand. It does not prohibit an operator from producing
arbitrary domain-specific effects elsewhere.

A temporary receiver operand supplies the qualifications of its resolved result
and compiler-managed temporary place. An operator applied through a pointer or
reference uses the dereferenced instance's qualifications, not the pointer
binding's place qualification. Complete temporary lifetime, operator
generation, viability, ranking, and "most closely aligned" selection remain
future work.

Omitted receiver-operand qualifiers use the defaults applicable to the context
where the operation is defined. No separate type-wide default layer is required
without a concrete use case.

### Members and nested access

A readonly path to a container ordinarily produces readonly paths to projected
members:

```zax
source : MyType
container : MyType readonly & = source

container.member = 5 // error: this member path is readonly
```

The original `source` path remains independently writable. A member requiring
interior mutation through readonly or immutable access must use explicit
`unsafe pliable`, not an unmarked mutable-member exception.

### Final places, move-out, and destruction

A final live place cannot be ordinarily replaced. Move and copy remain
contextual operations rather than persistent qualifier axes.

A final place may permit destructive move-out when the operation truly consumes
the source as part of ending its lifetime. This supports reclaiming or recycling
expensive resources during destruction. It does not permit transferring
contents from a still-live immutable or readonly source into mutable or writable
state without an explicit unsafe mechanism.

During destruction, the current instance receives terminal mutable and writable
authority without requiring `unsafe pliable`. Destruction may dismantle the
value and extract resources because its lifetime is ending. That elevated
authority cannot produce an alias that remains usable after the destroyed
instance's lifetime. Exact destructor sequencing, moved-from states, partial
destruction, and escape analysis remain lifetime and ownership work.

### Concurrency boundary

Immutable qualification addresses value change, not lifetime, shared backing
storage, synchronization, safe transfer, allocator behavior, or reference-count
safety. It may make sharing easier but is not a complete thread-safety
guarantee.

Lifetime management must independently ensure that an instance outlives every
reference to it. Mutable shared state requires synchronization beyond these
qualifier axes. `unsafe pliable` can additionally invalidate assumptions made by
other aliases and therefore requires particular caution across threads.

### Structural-typing consequence

Qualifier-preserving conversion and structural identity are distinct questions.
This work constrains conversions not to increase ordinary authority, but defers
whether qualifiers participate in structural identity, shape equivalence,
layout equivalence, reflection, or other compatibility relations.

Future structural-typing work must distinguish at least:

- nominal identity;
- name-and-type shape compatibility;
- qualifier-aware layout compatibility;
- qualifier-erased layout compatibility; and
- safe substitutability under the qualifications available at the use site.

Zax should eventually provide an explicit anonymization operation that can
express intentional use of structurally compatible differently named types
without making unrelated named types implicitly interchangeable. Anonymization
must not become a qualifier-elevation mechanism. Exact syntax and equivalence
rules remain deferred.

### Mutability-indexed type-family consequence

Promotion review recovered a distinct legacy concern that the initial qualifier
review had not dispositioned: one named type could provide separate mutable and
immutable implementations with different members, API surfaces, layouts, and
construction strategies.

The concept was not rejected. Its detailed legacy behavior crosses type
definition, identity, layout, structural compatibility, defaults, conversion,
reflection, recursive family-name resolution, generic, and composition concerns
that work item `004` cannot decide responsibly.

The candidate framing is a mutability-indexed type family:

- `MyType mutable` and `MyType immutable` would be distinct concrete variants
  belonging to one named family;
- a type could still use one shared implementation when specialization has no
  value;
- readonly access would retain the selected variant's underlying mutability;
- differently represented variants would require construction, copying, or
  consuming conversion rather than ordinary requalification; and
- separately named types plus `own` composition remain a viable alternative.

The evidence, representative syntax, accepted qualifier constraints, open
questions, activation pressure, and retirement conditions are preserved in
`project/raw/mutability-indexed-type-families.md`. The public qualifier owner
records only the boundary needed not to foreclose the feature.

### Diagnostics and formatting

Programmer-visible diagnostics should:

- identify the entity and indirection layer carrying each relevant
  qualification;
- distinguish unavailable authority from a conflicting qualification;
- reject duplicate qualifier tokens at one syntactic point;
- report contradictions when defaults are applied at the declaration where the
  contradiction becomes concrete;
- explain the required and available receiver-operand qualifications when no
  candidate is viable; and
- keep unsafe bypasses distinguishable from ordinary promise strengthening.

Canonical qualifier ordering is a formatting rule rather than semantic
precedence. Formatters preserve explicit qualifiers. Linters may identify
compatible restatement or redundant explicit defaults.

### Cascading unsafe-marking consequence

The accepted declarations owner currently treats bare `???` as syntax that is
itself an explicit unsafe escape from initialization:

```zax
item : Item = ???
```

The qualifier review exposes a broader consistency question: safety-contract
violations should use a local, searchable `unsafe` marker wherever practical.
The candidate future spelling is:

```zax
item : Item = unsafe ???
```

This work does not revise the accepted declaration form. The implication is
explicitly deferred for later review and must update its accepted owner only
through a later documentation fit dry run, discussion, alignment, and
authorized promotion.

### Explicit deferrals

The aligned model constrains but does not complete:

- pointer and reference grammar, rebinding, ownership, and alias analysis;
- lifetime, borrow, destructor, moved-from, and exclusive-authority rules;
- the complete safe and unsafe cast lattice;
- uniqueness proofs and operations that safely freeze mutable storage;
- constructor cleanup, panic, and premature-publication behavior;
- complete parameter, result, generic, and capture syntax;
- operator generation, viability, ranking, and overload diagnostics;
- exact default-directive syntax, scope, stacking, and precedence;
- a possible future no-fallback default mode;
- concurrency transfer and synchronization guarantees;
- structural identity, equivalence, layout, reflection, and anonymization;
- mutability-indexed type-family identity, representation, shared API, defaults,
  conversion, and reflection; and
- formal grammar, compiler internals, lowering, or implementation mappings.

These deferrals may not invalidate the aligned constraints recorded here.

### Consequence-driven reading performed

- **casting.md** (focused "`type` casting using `as`" section): Read because
  qualifier preservation during conversion constrains ordinary promise
  strengthening and unsafe weakening. Its legacy mutability rules conflict in
  places and are not accepted wholesale.
- **compiler-directives.md** (focused variable, type, and function default
  sections): Read because omission defaults may be changed by source context.
  Its exact directive mechanisms remain legacy input.
- **functions.md** (focused readonly function and pliable-member sections): Read
  because receiver-operand access and interior mutation became necessary. It
  supplies legacy evidence but does not establish the refined model.
- **pointers.md** (focused qualifier-bearing reference/pointer material): Read
  because indirection must preserve per-level qualification. It has no complete
  model for the three axes and entangles them with later `lease`/`last` design.
- **concurrency.md** (focused immutable sharing and deep-copy material): Read
  because immutable guarantees were tested across threads. It shows that
  aliasing, backing storage, transfer, and allocation remain independent safety
  concerns.
- **project/raw/structural-typing.md** (focused preserved declaration-model
  constraint): Read because qualifier participation in structural compatibility
  became material. It explicitly leaves identity, equivalence, layout,
  conversion, and reflection participation for future decision.

### Candidate lasting owners

The pre-promotion documentation fit dry run must decide whether the detailed
model belongs in an expanded `language/declarations-and-bindings.md` or a focused
qualifier owner such as `language/qualifiers.md`. The declarations owner already
owns the three axes and should at minimum retain the routing mental model.

A future terms document should own concise human-language definitions for
cross-cutting concepts such as storage place, access path, receiver operand, and
current instance. It is not a keyword list and must not duplicate behavioral
rules owned by concept documents.

The dry run must also disposition legacy `mutable.md`, affected function,
pointer, casting, compiler-directive, concurrency, and structural-typing
material, and the separately deferred `unsafe ???` owner revision. No lasting
owner or repository structure is selected merely by this working record.

## Dispositions and promotion dry run

### Dry-run status

This is a read-only pre-promotion proposal. It does not promote the aligned
findings, authorize the proposed edits, select a lasting structure, archive this
work item, or create work item `005`.

The dry run found no documentation-architecture blocker, but promotion requires
a coherent multi-file consistency pass rather than only adding a qualifier
owner. Leaving legacy unsafe spellings, contradictory qualifier behavior, or
misordered examples in ordinary reading paths would allow superseded behavior to
propagate into later work.

### Proposed structure

Retain the existing `language/` directory and add two current conceptual owners:

- `language/qualifiers.md` - detailed human-developer-facing owner for the three
  qualifier axes and their interactions; and
- `language/terms.md` - concise owner for cross-cutting human-language
  terminology, not a keyword list or a duplicate behavior owner.

Retain `language/declarations-and-bindings.md` as the declaration-facing owner.
It should summarize qualifier attachment where declarations require it and
route complete qualifier behavior to `language/qualifiers.md`.

Replace the contents of legacy `mutable.md` with a short supersession notice
that routes readers to `language/qualifiers.md`. Retaining the path preserves an
existing public URL; retaining its contradictory examples would not.

Update `index.md` to list qualifiers and terms under current conceptual design
and remove mutability from ordinary legacy navigation. No new directory family
or specification area is needed.

### Proposed ownership map

| Concern | Proposed lasting owner or disposition |
| --- | --- |
| Three axes, independence, layering, defaults, inheritance, repetition, and ordering | `language/qualifiers.md` |
| Ordinary promise strengthening and explicit unsafe weakening | `language/qualifiers.md` |
| Deep immutability, construction activation, and exclusive mutable-to-immutable transition | `language/qualifiers.md` |
| `unsafe pliable`, `unpliable`, non-propagation, parameters, captures, results, and optimization constraints | `language/qualifiers.md` |
| Receiver-operand qualification and operator-selection constraints | `language/qualifiers.md` |
| Final-place move-out and terminal destruction constraints | `language/qualifiers.md` |
| Pointer/reference per-level preservation constraints | `language/qualifiers.md`; complete indirection remains later work |
| Concurrency and structural-typing consequences | `language/qualifiers.md`; complete adjacent designs remain deferred |
| Declaration spelling, direct-storage attachment, and declaration diagnostics | `language/declarations-and-bindings.md` |
| `unsafe ???` initialization behavior | `language/declarations-and-bindings.md` |
| Storage place, access path, receiver operand, current instance, and similar cross-cutting terms | `language/terms.md` |
| Exact default-directive syntax and scope | Legacy `compiler-directives.md` pending its own complete review |
| Structural identity, equivalence, layout, reflection, and anonymization | Explicitly deferred; existing raw structural-typing input remains unchanged |
| Compiler, transpiler, backend, and lowering mechanisms | Future implementation repository |

Current owners must not depend on this working record or its later archived form
for meaning.

### Exact proposed promotion change set

#### New current owners

1. Add `language/qualifiers.md` with the detailed aligned model, authority
   metadata, programmer-facing examples, explicit costs and risks, and bounded
   deferrals.
2. Add `language/terms.md` with concise definitions and links to behavior owners.

#### Current owner and navigation changes

3. Update `language/declarations-and-bindings.md`:
   - retain a declaration-facing summary of the three axes and their attachment;
   - route complete qualifier semantics to `language/qualifiers.md`;
   - align default resolution, compatible restatement, duplicate spelling,
     diagnostics, and formatter constraints; and
   - change the accepted explicitly uninitialized form from bare `???` to
     `unsafe ???` throughout prose, examples, tables, and boundaries.
4. Update `index.md`:
   - add qualifiers and terms to current conceptual design;
   - add the smallest useful reading routes; and
   - remove mutability from ordinary legacy navigation.
5. Replace legacy `mutable.md` with a supersession notice linking
   `language/qualifiers.md`. Remove all contradictory legacy examples from the
   public page rather than preserving them beside current design.

#### Focused legacy corrections

6. Update `compiler-directives.md`:
   - remove `pliable` and `unpliable` variable-default options and their example
     block;
   - route qualifier default behavior to `language/qualifiers.md`;
   - state that defaults fill only unresolved axes and do not override inherited
     qualifications;
   - allow contradictory configured defaults while diagnosing the declaration
     where both become applicable; and
   - describe function defaults as receiver-operand defaults.
7. Update `functions.md`:
   - route qualifier behavior to `language/qualifiers.md`;
   - describe readonly functions through receiver-operand access;
   - change the mutex escape to `unsafe pliable`; and
   - avoid claiming no underlying change is possible when an explicit unsafe
     escape exists.
8. Update `operator.md`:
   - use receiver-operand and current-instance terminology;
   - establish that all three axes participate in candidate viability; and
   - remove any implication that operators receive special mutability behavior
     from their spelling.
9. Update `casting.md`:
   - replace conflicting qualifier conversion claims with ordinary promise
     strengthening, explicit unsafe weakening, and value-producing immutable
     construction constraints; and
   - mark structural identity and compatibility rules as unresolved rather than
     presenting legacy layout rules as current design.
10. Update `pointers.md`:
    - add per-level qualifier-preservation constraints;
    - state that changing indirection form does not carry the pointer or
      reference binding's own final stance to a new binding; and
    - replace categorical `last`/readonly/immutable claims with the aligned
      terminal-consumption constraint and an explicit lifetime deferral.
11. Update `concurrency.md`:
    - link immutable behavior to `language/qualifiers.md`;
    - distinguish immutability from lifetime, shared backing storage,
      synchronization, allocation, and transfer safety; and
    - account for explicit unsafe pliability.
12. Update `ctor-dtor.md`:
    - change its accepted initialization reference to `unsafe ???`;
    - record construction activation and terminal destruction constraints; and
    - normalize `MyType & deep readonly` examples to the best-supported
      canonical form `MyType readonly & deep`.
13. Update `warnings-errors.md`:
    - stop presenting duplicate qualifier spelling as a configurable warning;
    - treat same-point duplication and conflicting stances as source errors; and
    - avoid prematurely stabilizing exact diagnostic identifiers.

#### Mechanical consistency corrections

14. Update `arrays.md`:
    - spell aggregate uninitialized operands `unsafe ???`; and
    - distinguish a final function binding from readonly receiver-operand access
      in the `slice` explanation.
15. Update `basics.md`:
    - list `unsafe ???` and `unsafe pliable` consistently; and
    - retain the intentionally unusual but memorable `pliable` and `unpliable`
      vocabulary.
16. Update `flow-control.md` so the ternary expansion declares
    `e : Integer = unsafe ???`.
17. Update `type-definition.md` so uninitialized-member prose and examples use
    `unsafe ???`.

No change was initially proposed for `README.md`, `language/vision.md`,
`language/source-structure.md`, project guidance, raw input, or archived work as
part of qualifier promotion. The separately authorized post-dry-run adjustment
below adds one indexed raw input without changing ordinary project guidance.

### Corpus consistency audit

The dry run searched all current non-archived Markdown while excluding the
operating-prompt sources and audit-only archive.

#### Unsafe initialization

Active bare `???` material occurs in:

- `arrays.md`;
- `basics.md`;
- `ctor-dtor.md`;
- `flow-control.md`;
- `language/declarations-and-bindings.md`; and
- `type-definition.md`.

Every behavior spelling and example should become `unsafe ???`. Prose may still
identify the `???` token when explaining the combined unsafe form, but it must
not present bare `???` as a usable current operation.

Inside aggregate initialization, `unsafe ???` is the best current spelling:

```zax
values : Integer[100] = [{ source, unsafe ??? }]
```

Exact aggregate grammar remains future work. The example should be labeled
conceptual if that future boundary would otherwise be mistaken for settled
formal syntax.

#### Pliability

Active pliability material occurs in:

- `basics.md`;
- `compiler-directives.md`;
- `functions.md`; and
- legacy `mutable.md`.

Behavioral use of pliability must be spelled `unsafe pliable`. `unpliable`
remains the sole ordinary behavior and may still be written explicitly.

Legacy `[[variables=pliable]]` is incompatible with a local, explicit unsafe
marker and must retire. `[[variables=unpliable]]` is also unnecessary because
there is no alternative safe default to select. No pliability default directive
should remain.

Legacy name-side `mutable` used as an interior-mutation escape must not be
mechanically retained. The aligned escape is `unsafe pliable`; `mutable` remains
the value-mutability stance.

#### Ordering and duplication

The audit found one clear noncanonical qualifier order outside the superseded
mutability page: `MyType & deep readonly` in `ctor-dtor.md`. The best-supported
correction is `MyType readonly & deep`, matching other legacy examples while
keeping complete `deep` semantics deferred.

Repeating one qualifier token at a single syntactic point is an error.
Compatible restatement from distinct sources or qualification at distinct
indirection levels remains legal. Formatters may normalize order and spacing but
must not add or remove explicit qualification.

#### References and missing referents

Standalone indirection declarations were checked for the previously identified
reference-example problem. Pointer declarations that intentionally default to
Nothing, stored pointer members, parameters, and result declarations do not
require invented referents. Current affected qualifier examples that create
reference views must name and initialize from an existing source.

### Future-dependent corrections

#### `last`, readonly, immutable, and terminal consumption

Legacy `pointers.md` says `last` is categorically incompatible with readonly and
immutable values. Work item `004` establishes instead that final, readonly, or
immutable contents may be extracted when an operation truly consumes the source
as part of ending its lifetime.

`last` alone does not yet prove that terminal condition. Promotion should remove
the categorical legacy claim, state the terminal-consumption constraint, and
defer whether and how `last` proves it to complete lifetime and ownership work.
Existing `last` examples remain legacy candidates rather than accepted qualifier
behavior.

#### Structural casts

Legacy `casting.md` presents structural layout and qualifier rules more
confidently than current design supports. Promotion should preserve only:

- ordinary conversions may strengthen promises but not increase authority;
- unsafe conversions may explicitly weaken qualification;
- a new value may have different mutability only when its construction or
  consuming transition establishes the required guarantee; and
- anonymization and structural conversion may not silently elevate qualifier
  capabilities.

Member-name relevance, qualifier-aware and qualifier-erased equivalence, layout
compatibility, reflection, and exact anonymization remain future structural
typing work.

### Promotion validation plan

After applying an authorized promotion:

- every active unsafe-initialization operation is spelled `unsafe ???`;
- every active behavioral pliability escape is spelled `unsafe pliable`;
- no pliability default directive remains;
- `unpliable` remains available as an explicit spelling but is never required by
  a default directive;
- qualifier ordering follows the aligned canonical order;
- formatters preserve explicit qualifiers;
- duplicate same-point qualifier tokens and conflicting stances are errors;
- ordinary examples that create reference views identify their referents;
- current owners state conceptual design without claiming formal grammar,
  implementation, conformance, or unreviewed adjacent behavior;
- current owners do not depend on this working record or archive;
- the public index reaches both new owners;
- the superseded `mutable.md` path contains no stale behavior;
- local links and heading anchors resolve;
- Markdown fences are balanced and render coherently;
- the changed-file set exactly matches the authorized promotion scope; and
- the staged and unstaged review boundary is preserved.

### Closure boundary

Promotion does not itself archive this work item, update `project/README.md`, or
create work item `005`. After promotion validation, closure must separately:

1. record the final disposition of every finding;
2. mark this file historical, non-normative, and audit-only;
3. archive it without changing its filename;
4. discuss and align the initiating input and reading scope for `005`;
5. obtain explicit authorization for the closure and continuation change set;
   and
6. stop before analyzing `005`.

### Post-dry-run type-family preservation adjustment

After the dry run, promotion review identified that replacing legacy
`mutable.md` would remove the unresolved distinct-implementation concept
described above. The language maintainer and agent aligned that the concept has
realistic merit but is not ready for acceptance.

A separately authorized preservation change therefore:

1. adds `project/raw/mutability-indexed-type-families.md`;
2. indexes it in `project/raw/README.md`;
3. adds a concise non-decision boundary to `language/qualifiers.md`; and
4. records this disposition in work item `004`.

This adjustment preserves the candidate without restoring stale behavior to the
superseded public mutability path or expanding the accepted qualifier owner into
type-family design.

## Post-promotion review alignment

### Status and precedence

The language maintainer reviewed the staged promotion and identified
qualifier, replacement, reference, terminology, and recursive-name consequences
that materially refine the earlier working record.

The aligned findings in this section supersede contradictory statements in the
earlier working record and first dry run. The earlier material remains as a
chronological review record, not as the current conclusion.

In particular, this section supersedes earlier claims that:

- a varying referent may ordinarily be presented as final;
- `unsafe pliable` leaves the pliable binding's own final qualification
  effective;
- no operator spelling has any compiler-recognized special scenario;
- immutable-place replacement can be taught without its generated lifetime
  behavior; and
- a named type requires `forward` merely to refer to its own name inside its
  body.

The first dry run is no longer sufficient for promotion planning. A second
read-only dry run is required after the language maintainer reviews this
section. Do not perform that dry run, edit promotion targets again, or begin
closure without separate authorization.

### Legacy mutability-path disposition

The supersession notice proposed for `mutable.md` is not needed. Once useful
future input has been relocated and internal references are corrected, the file
should be deleted rather than retained as a defunct compatibility placeholder.

Repository history and the indexed raw type-family input preserve provenance.
The second dry run must identify and repair any remaining current link before
proposing deletion.

### Teaching correction

The initial three-axis example is too dense for a reader who has not yet learned
the distinction between a place, a value lifetime, and an access path.

A clearer progression begins with omitted defaults and demonstrates direct
behavior:

```zax
source final : Buffer = makeBuffer()

source.append(data)   // legal: omitted access defaults writable
source = makeBuffer() // error: source's place is final

reader : Buffer readonly final & = source
reader.append(data)   // error: this path is readonly
```

Explicit `mutable`, `immutable`, `writable`, and `readonly` restatements should
appear after the reader has a working model for defaults and place replacement.
Complete tutorial expansion may wait for broader language maturity, but examples
must not teach a misleading implication.

### Refined roles of the three axes

The three axes remain independent but interact when deciding whether an
operation is available:

| Axis | Stances | Refined role |
| --- | --- | --- |
| Place replacement | `final` / `varying` | Truth about whether the immediately qualified place may ordinarily receive another value lifetime |
| Value mutability | `mutable` / `immutable` | Truth about whether the current value lifetime's contained state may change through ordinary behavior |
| Access capability | `writable` / `readonly` | Permission for the current path to perform an otherwise available change |

`varying` alone does not authorize the current path to replace a place.
`writable` is also required. `writable` alone cannot replace a final place or
mutate an immutable value.

The required combinations are:

| Operation | Required qualifications |
| --- | --- |
| Mutate the current value lifetime's contents | `mutable` + `writable` |
| Reconstruct a place with another value lifetime | `varying` + `writable` |
| Observe a stable immutable place | `immutable` + `readonly` + `final` |
| Observe a replaceable place containing successive immutable lifetimes | `immutable` + `readonly` + explicit `varying` |

A domain-specific operator candidate may still accept readonly, final, or
immutable operands when it does not perform the prohibited change through that
operand.

### Place qualification across aliases

An ordinary alias to the same place must preserve that place's actual final or
varying stance. Readonly removes change authority from the new path; changing
the place qualifier would misrepresent the referent.

```zax
source varying : Message immutable = makeMessage("first")

incorrect : Message immutable readonly final & = source
// error: the referent place is varying

observer final :
    Message immutable readonly varying & = source
// legal: the observer binding is final; the referent place is varying
```

The name-side `final` applies to the reference binding. The type-side `varying`
describes the referenced place.

An alias to a varying immutable place must spell `varying` explicitly. The
explicit spelling acknowledges that the path may observe successive immutable
lifetimes in the same storage. Omission must not silently create that unusual
place-tracking reference.

The observer above cannot initiate replacement because its path is readonly.
Another writable path may:

```zax
replacer final :
    Message immutable writable varying & = source

replacer = makeMessage("second") // reconstructive replacement
display(observer)                // observes "second"
```

Neither immutable instance mutates. The first lifetime ends and another
immutable lifetime begins in the same place.

The earlier ordinary conversion `varying` -> `final` therefore does not apply to
an alias of the same referent place. It remains legal when an operation creates a
new independent place:

```zax
source varying : Message = makeMessage("first")
copy final : Message = source
```

`copy` has its own final place; it does not claim that `source` became final.
By-value parameters, constructed results, copies, moves into new destinations,
and other operations that create independent storage resolve the destination's
place qualification separately.

### No baseline stable-borrow conversion

A future lifetime strategy could temporarily prevent replacement while a final
reference to a varying place remains live. This need not allocate or copy, but
it requires compiler tracking of aliases and borrow completion, including
captures, callbacks, async suspension, and concurrency.

Baseline qualifier behavior does not require that Rust-like stable-borrow
analysis. Without such a separately selected lifetime mechanism, a varying
immutable place cannot provide a final reference.

### Reconstructive replacement

A direct varying place may hold successive immutable values:

```zax
message varying : Message immutable = makeMessage("first")
message = makeMessage("second")
```

This is not ordinary user-defined assignment. The compiler recognizes a
specific **reconstructive replacement** scenario for `=`:

- the destination is an existing varying place;
- the current path is writable;
- a viable replacement construction exists for the right-hand operand; and
- lifetime, alias, construction, and destruction constraints permit the
  transition.

The generated reconstructive candidate has a compiler-owned lifetime skeleton.
A type cannot override that skeleton with an ordinary `=` body. It customizes
the transition through a replacement constructor.

This is an acceptable kind of special behavior: the compiler recognizes one
specific lifetime scenario. It does not impose conventional assignment meaning
on every `=` candidate. Other domain-specific `=` candidates remain ordinary
selectable operations, including candidates for final or readonly receiver
operands.

`+=` and other assignment-like tokens do not automatically receive
reconstructive lifetime behavior.

Complete generated-candidate viability, priority, ambiguity, result shape, and
interaction with domain-specific `=` candidates remain operator work.

### Contextual `replacement` keyword

The aligned spelling for a replacement constructor uses the contextual keyword
`replacement` with `+++`:

```zax
replacement +++ final : ()(
    rhs : WhateverType readonly &
) = {
    // `_` initially contains the previous receiver state.
}
```

`replacement` is special only when:

1. the parser is in a context where a constructor declaration is legal; and
2. `replacement` is immediately followed by `+++`.

Everywhere else, `replacement` remains an ordinary identifier:

```zax
replacement := makeValue()
value.replacement()

MyType :: type {
    replacement : String
}
```

This narrow contextual rule avoids globally reserving a common identifier.

### Replacement-constructor behavior

A replacement constructor is a constructor because:

- it establishes a new valid instance lifetime;
- it cannot return results;
- it receives construction authority without `unsafe pliable`; and
- its completion establishes the new instance invariants.

It is also a compiler-recognized lifetime transition rather than an ordinary
constructor call.

The receiver operand `_` uses the original allocation and initially contains the
old representation and resources. No copied or separately allocated `old`
parameter exists.

Conceptually:

1. evaluate the right-hand operand;
2. select the generated reconstructive `=` scenario and the best matching
   replacement constructor;
3. enter a compiler-recognized transition on the destination storage;
4. grant `_` transitional mutable and writable construction authority;
5. let the replacement constructor retain, destroy, replace, move, copy, or
   initialize members in place using the declared right-hand operand;
6. require a complete valid instance when the constructor returns; and
7. commit the new enclosing value lifetime in the same place.

The right-hand operand may have any declared type and qualifications. Normal
parameter and overload selection applies. Its implementation may copy, move,
borrow, partially consume, use as configuration, or ignore the operand as its
declared semantics allow.

An untouched resource such as a database connection handle may remain in its
existing field and allocation. Reusing the same address does not automatically
preserve the validity of raw interior pointers when the pointee's lifetime is
ended or reconstructed.

At minimum, future replacement-construction work must define:

- how the compiler tracks retained, moved, destroyed, and reconstructed members;
- whether untouched member lifetimes continue across the enclosing transition;
- when member reads become invalid during transition;
- self-replacement and right-hand operands aliasing the destination;
- destructor and constructor ordering;
- fallback behavior when no replacement constructor exists;
- interaction with `last`, move/copy policy, and disabled operations;
- raw pointers and references into replaced members;
- callback, reentrancy, async, and concurrency behavior; and
- diagnostics when the constructor does not establish a complete valid result.

If no replacement constructor exists, future work may allow ordinary
destroy-and-move/copy construction or may make the generated replacement
candidate unavailable when a safe transition cannot be formed.

### Panic during replacement

Panic is often terminal. A terminal panic need not make partially transitioned
storage available to resumed ordinary execution.

If future panic behavior permits recovery from a replacement constructor, the
language must separately define partial cleanup, which destructors remain
applicable, and how a valid state is restored or abandoned. Work item `004` does
not require recoverable replacement construction.

### `unsafe pliable` reaches the declaring binding

`unsafe pliable` bypasses final, readonly, and immutable restrictions applicable
to operations through the declared path, including the binding's own final
stance. The underlying qualifications remain recorded.

```zax
source final : Integer immutable = 42
escape final unsafe pliable :
    Integer immutable readonly final & = source

escape = 5 // permitted through the explicit unsafe bypass
```

A new ordinary or explicitly unpliable alias does not inherit this bypass and
again respects the retained qualifications. Dereferencing into another pointer
or reference level likewise removes pliability unless explicitly reintroduced.

Exact reference rebinding versus referent replacement syntax remains later
reference design; this finding establishes only that the declaring binding's
final stance is not exempt from its own explicit unsafe bypass.

### Member wording correction

`mutable` on a member describes that member value's mutability. It does not
authorize mutation through a readonly or immutable container path. Such a bypass
must be written `unsafe pliable`.

The previous wording about "spelling a member only mutable" was unclear and
should not enter lasting documentation.

### Terminology corrections

The terms owner should define **pointee** independently:

> A pointee is the place or value targeted by a pointer. The binding storing the
> pointer and its pointee are distinct qualification layers. A pointer may target
> Nothing; a reference must have a valid referent.

The terms owner should contain only human-language descriptive concepts, not a
catalog of source keywords.

### Named type self-reference

The rule that an ordinary value binding enters scope only after initialization
does not apply to a named type definition.

A named type enters scope as an incomplete type before its body is resolved:

```zax
Node :: type {
    next : Node *
}
```

The self-name resolves. The pointer has finite known representation, so the
member is legal.

Direct recursive storage remains an infinite-layout error:

```zax
Node :: type {
    next : Node // error: no finite layout
}
```

This is a layout failure, not a name-resolution failure.

`forward` remains necessary when another name has not yet been introduced,
including mutual recursion and separately completed definitions:

```zax
SomeOtherNode :: forward type

Node :: type {
    next : SomeOtherNode.SubType *
}

SomeOtherNode :: type {
    SubType :: type {
        // ...
    }
}
```

`SomeOtherNode` resolves as incomplete. Its `.SubType` suffix remains pending
until the definition completes; `SubType` does not require its own forward
declaration.

The same incomplete-definition principle may later be evaluated for enums,
aliases, namespaces, and other non-value definition forms. Cyclic aliases and
operations requiring completed layout still require diagnostics.

Named types use their own incomplete names. Anonymous recursive type syntax is
not established by current design.

### Second dry-run requirements

The second dry run must begin from the fully reviewed aligned findings, not
mechanically adjust the first change set. At minimum it must:

1. remap the refined place/access model into lasting owners;
2. identify every current statement that permits a varying referent to become a
   final alias;
3. update generated replacement requirements to `varying` + `writable`;
4. disposition reconstructive `=` and contextual `replacement +++` between
   qualifier, operator, constructor/destructor, lifetime, and terms owners;
5. correct `unsafe pliable` binding behavior and examples;
6. simplify the introductory qualifier examples;
7. add pointee terminology and remove keyword material from the terms owner;
8. update named-type visibility, `forward`, recursive naming, and affected
   examples;
9. propose deletion of `mutable.md` and repair all resulting references;
10. preserve unresolved replacement, alias, panic, async, concurrency, and
    anonymous-recursion questions as explicit deferrals or indexed raw input;
11. repeat the complete unsafe-spelling, ordering, example, link, Markdown, and
    owner-independence audits; and
12. produce a new exact change set and structure proposal for discussion.

The second dry run must not assume that the first promotion change set remains
minimal or sufficient.

## Second dispositions and promotion dry run

### Second dry-run status

This second dry run was performed after the language maintainer reviewed the
fully staged first promotion and aligned the superseding findings in
[Post-promotion review alignment](#post-promotion-review-alignment).

The dry run is read-only except for this authorized working-record addition. It
does not edit promotion targets, accept the proposed structure, authorize the
second promotion, unstage the first promotion, archive work item `004`, or begin
work item `005`.

The first promotion is not ready to remain as staged without revision. No
documentation-architecture blocker prevents a coherent second promotion, but
the place/access model, reconstructive replacement, contextual constructor
syntax, pliability, terminology, and recursive-name rules affect more owners
than the first dry run identified.

### Second structure proposal

Retain the current `language/` structure and the three current conceptual
owners:

- `language/declarations-and-bindings.md`;
- `language/qualifiers.md`; and
- `language/terms.md`.

Do not create a complete construction/destruction owner yet. Replacement
construction is aligned only at the depth required by qualifier-driven place
replacement. `language/qualifiers.md` can own that current boundary while the
complete field-transition, lifetime, panic, alias, and generated-candidate
questions remain indexed raw input.

Add:

- `project/raw/replacement-construction.md` - non-authoritative future input for
  the unresolved mechanics and cross-owner questions of reconstructive
  replacement.

Delete:

- `mutable.md` - the supersession placeholder has no remaining ordinary route
  or independent content after provenance and future input are preserved.

Update `project/raw/README.md` to index replacement construction. No new
directory, public compatibility stub, or specification area is needed.

### Second proposed ownership map

| Concern | Proposed lasting owner or disposition |
| --- | --- |
| Final/varying as truth about one place | `language/qualifiers.md` |
| Mutable/immutable as truth about one value lifetime | `language/qualifiers.md` |
| Writable/readonly as current-path authority over mutation and replacement | `language/qualifiers.md` |
| Same-place alias preservation and explicit varying immutable references | `language/qualifiers.md`, summarized for declarations in `language/declarations-and-bindings.md` |
| New independent destination place qualification | `language/declarations-and-bindings.md`, constrained by `language/qualifiers.md` |
| Generated reconstructive `=` scenario | `language/qualifiers.md`, with assignment selection summarized in `language/declarations-and-bindings.md` |
| Contextual `replacement +++` and replacement-constructor boundary | `language/qualifiers.md`; complete constructor mechanics remain future work |
| Pointee and replacement-constructor descriptive terms | `language/terms.md` |
| Contextual source spelling inventory | Legacy `basics.md`, corrected to match the current owner |
| Complete replacement field transitions, raw pointers, aliasing, panic, async/concurrency, move/copy, and candidate ranking | New indexed raw `project/raw/replacement-construction.md` |
| Named type visibility inside its own body | `language/declarations-and-bindings.md` |
| `forward`, pending suffix, and direct recursive-layout boundaries | `language/declarations-and-bindings.md` |
| Anonymous recursive type syntax | Explicitly deferred type/name-resolution input; not a human-language term |
| Former dual mutable/immutable implementation proposal | Existing indexed `project/raw/mutability-indexed-type-families.md` |
| Complete operator, pointer, function, and constructor designs | Their future concept owners; affected legacy pages receive focused consistency corrections only |

Current owners must not depend on this working record, either raw placeholder,
or the future archive for their meaning.

### Exact second proposed promotion change set

The second promotion proposal contains 16 files.

#### Current conceptual owners

1. Revise `language/qualifiers.md`:
   - replace the dense introductory three-axis example with a progressive
     final-place, writable-default, and readonly-view example;
   - describe final/varying as the actual stance of a place;
   - describe writable/readonly as authority to perform an otherwise permitted
     content mutation or place replacement through the current path;
   - require `mutable` + `writable` for content mutation;
   - require `varying` + `writable` for reconstructive replacement;
   - add a dedicated explanation of successive immutable lifetimes in one
     varying place;
   - require explicit `varying` when an immutable reference tracks such a place;
   - reject a same-place alias that presents a varying referent as final;
   - retain independent place qualification for new by-value destinations;
   - remove `varying` -> `final` from ordinary same-place view conversion;
   - add the compiler-recognized reconstructive `=` boundary;
   - add contextual `replacement +++` and same-storage transitional receiver
     behavior at the accepted conceptual depth;
   - state that readonly varying observers may witness replacement but cannot
     initiate it;
   - correct `unsafe pliable` so it bypasses the declaring binding's own final
     stance;
   - replace the unclear member wording with the aligned value-mutability versus
     unsafe-bypass explanation; and
   - preserve complete replacement mechanics and stable borrowing as explicit
     deferrals.
2. Revise `language/declarations-and-bindings.md`:
   - preserve a referent place's actual final/varying stance across ordinary
     aliases;
   - distinguish that preservation from a new independently qualified
     destination place;
   - remove the varying-place-to-final-view example and conversion bullet;
   - revise access terminology so readonly blocks both mutation and
     reconstructive replacement through the path;
   - require varying + writable for the generated normal replacement scenario;
   - distinguish the compiler-recognized replacement candidate from arbitrary
     domain-specific `=` candidates;
   - route replacement construction to the qualifier owner;
   - introduce a named type as incomplete before resolving its body;
   - allow the named type to resolve its own name inside that body;
   - retain direct recursive storage as a layout error;
   - retain `forward` for names used before introduction, mutual recursion, and
     separately completed declarations;
   - preserve pending suffix resolution through a forwarded prefix; and
   - leave anonymous recursive type syntax outside the established baseline.
3. Revise `language/terms.md`:
   - add **pointee** as the place or value targeted by a pointer;
   - distinguish the pointer binding from its pointee qualification layers;
   - add **replacement constructor** as the compiler-recognized constructor that
     transitions one value lifetime to another in existing storage;
   - link complete behavior to the qualifier owner; and
   - remove source-keyword material.

#### Focused legacy consistency corrections

4. Revise `operator.md`:
   - qualify the statement that operators receive no special behavior from
     traditional spelling;
   - identify reconstructive `=` as a compiler-recognized lifetime scenario,
     not conventional meaning imposed on every `=` candidate;
   - require varying + writable for that generated scenario; and
   - preserve domain-specific final/readonly/immutable `=` candidates.
5. Revise `ctor-dtor.md`:
   - route accepted replacement-constructor boundaries to
     `language/qualifiers.md`;
   - show contextual `replacement +++` as the aligned conceptual spelling;
   - explain that `_` begins with the old receiver representation in the same
     storage;
   - identify constructor authority, no result, and completion invariants; and
   - keep complete member-transition, panic, and cleanup rules visibly legacy or
     deferred.
6. Revise `casting.md`:
   - remove the statement that varying may ordinarily become final;
   - preserve the final/varying stance for aliases of the same place; and
   - state that a newly constructed by-value destination chooses its own place
     stance.
7. Revise `functions.md`:
   - state that readonly access cannot initiate mutation or reconstructive
     replacement through the receiver operand;
   - preserve the possibility that an explicitly varying readonly path observes
     replacement through another writable path; and
   - keep unsafe pliability as the explicit bypass.
8. Revise `pointers.md`:
   - state that pointer/reference conversion and dereference preserve the
     referent place's final/varying truth;
   - distinguish that referent from the new pointer/reference binding's own
     independently resolved place; and
   - reject same-place final/varying rewriting through indirection.
9. Revise `type-definition.md`:
   - route named self-reference and incomplete-definition behavior to the
     declarations owner;
   - remove outdated self-reference syntax assumptions; and
   - retain complete type-definition behavior as legacy.
10. Revise `forward.md`:
    - state that a named type resolves its own incomplete name inside its body;
    - retain `forward` for names needed before their declarations, mutual
      recursion, and separately completed definitions;
    - retain pending suffix resolution through an incomplete forwarded prefix;
      and
    - remove outdated self-reference syntax assumptions.
11. Revise `basics.md`:
    - list contextual `replacement +++` where compound source forms are
      cataloged;
    - state that `replacement` remains an ordinary identifier elsewhere; and
    - avoid listing it as a globally reserved keyword.

#### Indexed future input and cleanup

12. Add `project/raw/replacement-construction.md` with:
    - aligned motivation and same-storage transition constraints;
    - the contextual replacement-constructor spelling as accepted input;
    - unresolved member-state tracking and retained-member lifetime questions;
    - self-replacement and right-hand aliasing;
    - raw interior pointer validity;
    - destructor, move/copy, `last`, and disabled-operation interaction;
    - generated candidate priority and domain-specific `=` ambiguity;
    - panic and partial-transition behavior;
    - callback, reentrancy, async, and concurrency consequences;
    - activation pressure; and
    - owner-by-owner retirement criteria.
13. Update `project/raw/README.md` to index replacement construction for future
    constructor, lifetime, move/copy, alias, operator, panic, async, or
    concurrency work.
14. Revise `project/raw/mutability-indexed-type-families.md`:
    - give reference examples concrete mutable and immutable source places;
    - preserve explicit final/varying referent qualification;
    - avoid standalone uninitialized reference examples; and
    - distinguish variant mutability from the place stance selected for each
      example.
15. Delete `mutable.md`. No ordinary route links to it. Its remaining active
    work-item link must be replaced with a plain provenance reference before
    deletion.
16. Revise `project/work/004-qualifiers-and-mutability.md` during an authorized
    second promotion:
    - replace the required-reading hyperlink to `mutable.md` with a plain
      historical provenance statement;
    - record the disposition of the second promotion;
    - preserve the first and second dry runs as chronological non-authoritative
      records; and
    - avoid becoming a semantic dependency of current owners.

No second-promotion change is proposed for `index.md`, `README.md`,
`language/vision.md`, `language/source-structure.md`,
`compiler-directives.md`, `concurrency.md`, `warnings-errors.md`, archived work,
or project operating-prompt sources.

### Second corpus audit findings

#### Same-place varying-to-final claims

Current non-archived material still presents same-place varying-to-final
conversion as ordinary behavior in:

- `language/qualifiers.md`;
- `language/declarations-and-bindings.md`; and
- `casting.md`.

Those claims must be removed or replaced. Occurrences that describe a new
binding's own independently chosen stance, a new by-value destination, or
different pointer/reference binding layers remain valid and must not be changed
mechanically.

The validation distinction is:

> An ordinary alias preserves the actual stance of the same referent place. An
> independently created destination place resolves its own final/varying stance.

#### Reconstructive replacement

No current owner yet contains the aligned contextual `replacement +++`
constructor or the compiler-recognized same-storage lifetime transition.

The current declaration owner says a generated replacement requires varying but
does not also require writable. The operator legacy page says operators receive
no special behavior from spelling without identifying the acceptable
compiler-recognized replacement scenario. Both require focused correction.

The second promotion must keep two kinds of special behavior distinct:

- acceptable scenario recognition: the compiler detects an existing varying,
  writable destination and invokes the fixed reconstructive lifetime skeleton;
  and
- rejected conventional-token privilege: the language does not force every `=`
  candidate to have ordinary assignment meaning.

#### Access and observable replacement

Readonly varying access is a coherent combination:

```zax
source varying : Message immutable = makeMessage("first")
observer final :
    Message immutable readonly varying & = source
```

`observer` cannot initiate replacement, but it may observe another immutable
lifetime after a writable path reconstructs `source`. This must be explained
without claiming that either immutable instance mutated.

A writable varying path may initiate replacement:

```zax
replacer final :
    Message immutable writable varying & = source

replacer = makeMessage("second")
```

The generated scenario is unavailable through readonly even when the place is
varying.

#### `unsafe pliable`

The current qualifier owner says a final unsafe-pliable binding cannot be
rebound. That conflicts with the superseding aligned model.

The corrected rule is:

- `unsafe pliable` bypasses final, readonly, and immutable restrictions
  applicable to operations through the declaring path, including its own
  binding stance;
- retained qualifications remain visible to ordinary or explicitly unpliable
  aliases; and
- pliability still does not propagate across alias, result, capture, or
  dereference boundaries.

Exact reference rebinding versus referent replacement syntax remains deferred.
Examples must not imply that unresolved distinction is already formal grammar.

#### Terminology cleanup

The current terms owner mentions pointee only inside the referent definition and
contains source-keyword material outside its descriptive purpose.

The second promotion should:

- define pointee independently;
- add replacement constructor as a human-language concept;
- remove source-keyword material from the terms owner.

The declarations owner, `forward.md`, and `type-definition.md` currently use an
outdated self-reference model and say a named unforwarded type does not
self-resolve. Those statements must be revised together.

#### Named recursive definitions

The corrected name-resolution boundary is:

- an ordinary value binding remains unavailable during its executable
  initializer;
- a named type becomes visible as an incomplete type before resolving its body;
- `Node *` inside `Node` is legal because the name resolves and pointer
  representation is finite;
- a directly stored `Node` member remains an infinite-layout error;
- `forward` remains necessary when another name is used before introduction or
  for mutual/separately completed declarations; and
- a suffix below a forwarded incomplete prefix may remain pending until that
  prefix completes.

The audit found many legacy named-type bodies that already refer to their own
type name. Those examples become consistent once the declarations owner is
corrected; they do not each require mechanical rewriting.

#### Mutability placeholder deletion

No current language owner, public index, or corrected legacy page links to
`mutable.md`. The only live hyperlink is the active work item's historical
required-reading entry.

The second promotion can delete `mutable.md` after changing that entry to plain
provenance text such as "former `mutable.md` legacy evidence." The indexed raw
type-family input preserves the separately recovered future concern.

#### Raw reference examples

`project/raw/mutability-indexed-type-families.md` currently declares standalone
reference examples without referents and omits their place stance. These should
receive concrete source declarations and explicit final/varying referent
qualification so future work does not inherit the same ambiguity corrected
elsewhere.

### Explicit second-run deferrals

The second promotion can establish the current conceptual boundary without
completing:

- field-by-field replacement transition analysis;
- whether untouched contained lifetimes continue across enclosing replacement;
- self-replacement and aliased right-hand operands;
- raw and typed pointers into transitioned members;
- replacement-constructor fallback generation;
- move/copy/`last` and disabled-operation interaction;
- generated replacement priority against domain-specific `=` candidates;
- recoverable panic and partial cleanup;
- reentrancy, callbacks, async suspension, and concurrency;
- stable-borrow lifetime strategies;
- exact reference rebinding versus referent replacement syntax;
- anonymous recursive types.

An unresolved item must not be expressed as confident accepted detail in a
current owner. The new raw replacement input preserves the questions that lack a
current behavior owner.

### Second promotion validation plan

After an authorized second promotion:

- no current owner or ordinary legacy route claims that a same-place varying
  referent may become final;
- new independently created places still resolve their own final/varying stance;
- every generated reconstructive replacement statement requires both varying
  and writable;
- readonly varying observers cannot initiate replacement;
- contextual `replacement` is special only immediately before `+++` where a
  constructor declaration is legal;
- ordinary identifiers named `replacement` remain legal;
- qualifier examples teach defaults and place/value/path distinctions
  progressively;
- `unsafe pliable` examples include the declaring binding's final stance in the
  bypass while preserving non-propagation;
- pointee and replacement constructor are present as descriptive terms;
- the terms owner contains no source-keyword catalog entries;
- named type self-reference resolves through an incomplete definition;
- direct recursive storage remains a layout error;
- `forward` and pending suffix examples remain coherent;
- `mutable.md` is absent and no live local link targets it;
- raw future-work input is indexed but absent from ordinary language routes;
- all reference-view examples identify their sources unless a parameter or
  result context inherently supplies one;
- unsafe spellings and qualifier ordering remain consistent with the first
  audit;
- local links and heading anchors resolve;
- Markdown fences are balanced;
- current owners do not depend on work item `004` or raw files for meaning;
- the final changed-file set exactly matches the separately authorized second
  promotion; and
- staged and unstaged review boundaries remain intact.

### Second closure boundary

The second dry run does not authorize its proposed change set.

After review, the language maintainer must explicitly authorize any second
promotion. That promotion must be validated before work item `004` can close.
Archival, project-index continuation, and creation of work item `005` remain
separate actions requiring their own discussion, alignment, and authorization.

## Second promotion application record

The language maintainer reviewed the second dry run, aligned that replacement
construction belongs both in qualifier context and in future constructor work
without strict repetition, and explicitly authorized the proposed second
promotion.

The applied change set:

- revised `language/qualifiers.md`,
  `language/declarations-and-bindings.md`, and `language/terms.md`;
- corrected focused qualifier, replacement, and recursive-name material in
  `operator.md`, `ctor-dtor.md`, `casting.md`, `functions.md`, `pointers.md`,
  `type-definition.md`, `forward.md`, and `basics.md`;
- added and indexed `project/raw/replacement-construction.md`;
- corrected reference examples in
  `project/raw/mutability-indexed-type-families.md`;
- deleted the defunct `mutable.md` placeholder; and
- replaced the active work item's former mutability-page link with historical
  provenance text.

The qualifier owner explains the programmer-visible place/access reason and
accepted replacement-constructor boundary. The indexed raw input preserves the
detailed field-transition, lifetime, alias, panic, move/copy, pointer, operator,
async, and concurrency questions for a future constructor-centered owner.

This application record does not mark work item `004` closed, authorize
archival, select work item `005`, or authorize staging, committing, or pushing
the second-promotion changes.

## Closure disposition

Work item `004` closed after the language maintainer reviewed and authorized both
promotion passes and the final removal of the obsolete predefined self-type
model.

### Promoted current design

Aligned qualifier behavior was incorporated into:

- `language/qualifiers.md` as the detailed conceptual qualifier owner;
- `language/declarations-and-bindings.md` for declaration-facing attachment,
  alias preservation, generated replacement, and named incomplete type
  visibility; and
- `language/terms.md` for cross-cutting descriptive terminology.

Affected legacy pages were corrected without becoming competing owners.
Unsafe initialization now uses `unsafe ???`; unsafe pliability uses
`unsafe pliable`; the global pliability default was retired; qualifier order and
formatter preservation were established; the defunct `mutable.md` path was
deleted; and named types now use their own incomplete names for self-reference.

### Preserved future work

Unresolved material was preserved in indexed raw input:

- `project/raw/replacement-construction.md` for member transitions, resource
  retention, aliases, pointers, generated candidates, move/copy, panic, async,
  and concurrency;
- `project/raw/mutability-indexed-type-families.md` for distinct mutable and
  immutable implementations under one family; and
- `project/raw/structural-typing.md` for qualifier-aware identity,
  compatibility, layout, and recursive-name questions.

Complete ownership/lifetime strategies, reference rebinding, generated operator
ranking, recoverable panic, concurrent replacement, type-family identity,
structural equivalence, and anonymous recursive type syntax remain explicitly
deferred.

### Validation and continuation

Both promotion passes were validated for authorized file scope, staged review
boundaries, unsafe spelling, qualifier ordering, same-place alias preservation,
contextual replacement syntax, terminology, links, anchors, and Markdown
structure.

Work item `005` continues with construction, destruction, and replacement. This
archive is not required reading for that work; all necessary current behavior
and future pressure are available through current owners and indexed raw input.
