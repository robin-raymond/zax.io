# 013: Transfer stances, source state, and lifetime completion

| Field | Value |
| --- | --- |
| Status | Historical project record / non-normative / audit-only |
| Work Item | `013` |
| Created | 2026-09-02 |
| Archived | 2026-09-05 |
| Outcome | Promoted transfer stances, intent acknowledgements, integrated owner behavior, teaching-debt guidance, and indexed future pressure |
| Owns | Historical evidence and project provenance for the completed bounded review |
| Does Not Own | Current language meaning, active project work, complete pointer/reference grammar, final lifetime-policy selection, async/concurrency, complete generics, optional combinators, or formal layout/ABI rules |

## Non-authority notice

This file is a historical audit record. It is excluded from ordinary onboarding
and current design reading. Promoted language meaning is owned by current
`language/` documents, and unresolved future pressure is owned by indexed raw
inputs.

## Fixed initiating input

This section records the information aligned when work item `013` was created.
It is intentionally incomplete and must not be rewritten as work develops.

### Initiating concern

Develop a coherent human-developer-facing model for transfer stances, source
state, and lifetime completion:

1. the distinct meaning of `copy`, `deep`, `move`, and `last`;
2. whether each stance permits, requests, or requires a specialized transfer;
3. ordinary/default transfer when no stance is written;
4. candidate availability and preference among value, reference, pointer, copy,
   move, and terminal forms;
5. when a consumer accepts a stance and when source effects occur;
6. argument, construction-packet, assignment, result, and return timing;
7. source lifetime and valid state after successful copy, move, or terminal
   transfer;
8. source-shell destruction and exactly-once resource disposition;
9. outermost stance propagation through optional, pointer, reference, member,
   and other structural layers;
10. generated versus declared transfer operations;
11. destination/result qualification and prohibition on silently increasing
    authority;
12. inferred declarations such as `result := move source` at the depth required
    by transfer;
13. self-aliasing, interior aliases, and operation-specific same-object behavior;
14. costs, diagnostics, source stability, and lasting documentation ownership.

### Motivating pressure

Current conceptual design now uses transfer stances across several owners:

```zax
source : MyType?
anotherSource : MyType?

// ...

copied : MyType? = source
moved : MyType? = move anotherSource
terminallyTransferred : MyType? = last source
```

Optional behavior fixes the state consequences after a consumer accepts a
stance, but general Zax still needs to decide source syntax, preference, timing,
qualification, propagation, and the valid source lifetime afterward.

Construction packets and function inputs bind in source order. Qualifiers
distinguish value capability, access permission, and place replacement.
Pointers, references, optionals, and structural values may have several layers
through which an outer transfer stance must remain understandable.

### Known assumptions

- A transfer-qualified source expression has no state effect until a selected
  consumer accepts it.
- Caller source order and immediate binding remain observable.
- `copy`, `deep`, `move`, and `last` are distinct intents.
- Combined stances such as `move copy` and `last deep` do not exist.
- A stance on the outermost composite source is expected to carry inward through
  generated transfer:

  ```zax
  MyType readonly ? writable * immutable * varying deep
  ```

- Wrapper, pointer, referent, and boxed qualifications remain independently
  attached.
- Optional-specific source consequences already documented in
  [Zax optional values](../../../language/optional-values.md) must be preserved.
- A false unsafe lifetime assertion may have undefined consequences without a
  required runtime check.
- Zax has no exception-style rollback model.
- There is no language implementation in this repository.

### Known inclusions

- Mental model and terminology for all four transfer stances.
- Default transfer and explicit source intent.
- Transfer-qualified source views and their result type.
- Candidate viability, partial-order preference, and ambiguity.
- Immediate binding and consumer timing.
- Source lifetime, moved-from state, terminal consumption, and destruction.
- Outermost-to-inner propagation through generated operations.
- Custom interception and containing-type generation.
- Optional, pointer, reference, member, parameter, result, and capture
  consequences at the depth required by transfer.
- Inferred declaration pressure at the depth required by transfer.
- Self-aliasing, resource disposition, costs, diagnostics, and source stability.
- Documentation fit and lasting ownership.

### Known starting boundaries

- Complete pointer/reference grammar and independent binding rebinding.
- Final lifetime-policy selection or a complete borrow/ownership system.
- Async suspension, cancellation, executors, and concurrency.
- Complete generics, specialization, and generic inference.
- Optional combinators and general functional composition.
- Formal layout, ABI, grammar, compiler implementation, or lowering.

These boundaries do not erase consequences. Record each material pressure in a
live owner or indexed raw destination.

### Intentionally unresolved framing

- Whether a stance permits or mandates its specialized operation.
- Exact preference among copy, deep copy, move, terminal transfer, and reference
  binding.
- Canonical pre-unary, post-unary, parameter, and declaration syntax.
- Whether `as copy`, `as deep`, `as move`, and `as last` are accepted exact
  phrases.
- Whether accepted transfer phrases can remain unfenced.
- Exact moved-from validity guarantees.
- Whether a `last` consumer must steal resources or may still copy.
- Parameter binding versus a terminal reference/capability consumed later in a
  body.
- `:=` value/reference/pointer inference.
- Generated containing-type transfer and custom interception.
- Exact self-assignment behavior.
- Interaction with pointer ownership and lifetime strategies beyond immediate
  transfer consequences.

### Initial stopping guidance

Stop when the work has:

- established a usable programmer model for `copy`, `deep`, `move`, and `last`;
- established default and explicit stance source;
- established candidate availability and defensible preference;
- established when each consumer produces source effects;
- established source lifetime, valid state, and destruction after transfer;
- established outermost stance propagation and custom interception;
- reconciled parameters, packets, assignment, results, captures, optionals, and
  immediately necessary pointer/reference behavior;
- dispositioned inference, alias, safety, async, generic, and ownership pressure;
- identified exact current owners and promotion changes; and
- completed the required documentation-fit dry run.

Do not promote findings, archive this work item, or begin work item `014`
without the separately required discussion, alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../../documentation.md) - governs focused reading,
  numbered work, reader-first promotion, disposition, and dry runs.
- [Optional values](../../../language/optional-values.md#reset-move-and-last) -
  supplies current concrete source-state requirements and inward-propagation
  pressure.
- [Raw lifetime strategies](../../raw/lifetimes.md#accepted-transfer-constraints-on-lifetime-work) -
  is the main live input for transfer preference, source state, terminal
  references, reference addressability, and resource disposition.
- Focused [evaluation and immediate binding](../../../language/function-invocation.md#evaluation-and-immediate-binding),
  [partial-order preference](../../../language/function-invocation.md#partial-order-preference),
  and [temporary lifetime](../../../language/function-invocation.md#temporary-lifetime-and-call-completion) -
  provide call timing, consumer selection, result transfer, and completion
  boundaries.
- Focused [construction packet binding order](../../../language/construction-and-destruction.md#evaluation-and-binding-order),
  [generated lifecycle operations](../../../language/construction-and-destruction.md#declared-and-generated-lifecycle-operations),
  [optional wrapper replacement](../../../language/construction-and-destruction.md#optional-complete-wrapper-replacement),
  and [self-aliasing](../../../language/construction-and-destruction.md#self-aliasing-and-interior-aliases) -
  provide lifecycle generation, ordering, destruction, and alias constraints.
- Focused [qualifier capability and permission](../../../language/qualifiers.md#type-side-truth-versus-declaration-side-permission),
  [optional qualification layers](../../../language/qualifiers.md#optional-qualification-layers),
  and [transfer and destruction](../../../language/qualifiers.md#transfer-and-destruction) -
  provide the qualification and terminal-authority model.
- Focused [direct initialization](../../../language/declarations-and-bindings.md#direct-initialization)
  and [declaration boundaries](../../../language/declarations-and-bindings.md#boundaries-and-maturity) -
  provide value construction and unresolved `:=` inference pressure.
- Focused [optional transfer-source operations](../../../language/operators.md#optional-reset-and-protected-transfer-source-operations),
  [operator phrases](../../../language/operator-phrases.md), and
  [optional forms](../../../language/operator-catalog.md#optional-forms) - provide
  current source forms, phrase recognition, fencing, result holes, and optional
  pre-unary behavior.
- [Transfer stance terminology](../../../language/terms.md#transfer-stance) -
  provides the current shared term and maturity boundary.
- Legacy default copy/deep/last constructor evidence was consumed during
  promotion and is no longer a live required-reading path.

### Consequence-driven

- Read focused legacy pointer `last`/`lease` material when a concrete reference,
  pointer, terminal-capability, or consumer-timing question requires it.
- Read [raw analysis controls](../../raw/analysis-controls.md) and
  [raw safety input](../../raw/safety.md) when an unsafe, proof, panic-check, or
  diagnostic question becomes concrete.
- Read [raw callable-composition input](../../raw/function-composition-and-chaining.md)
  when transfer through capture, callable construction, or composition becomes
  necessary.
- Read [raw generic input](../../raw/type-parameters-and-generics.md) when
  substitution changes stance, generated operation, or inference behavior.
- Read pointer ownership, async, or structural material only when an immediate
  transfer consequence cannot be dispositioned coherently without it.

### Audit-only

- `project/archive/`, including work items `001` through `012`.
- Superseded implementation sketches and compiler-internal explanations.

Do not read archived work item `012` during ordinary work on `013`. Its current
findings are promoted into language owners and its future pressures are
preserved in live raw inputs.

## Working record

This working record contains aligned findings and a superseded initial
reconstruction. The aligned findings remain non-authoritative until a separately
discussed and authorized promotion incorporates them into lasting owners.

### Aligned findings

#### Programmer model

A **transfer stance** says how one complete value is ordinarily offered to a
consumer. It is one property of the complete source or receiver, not a qualifier
repeated independently at optional, pointer, reference, member, or other
structural layers.

Four stages keep the model precise:

1. A declaration supplies the value's ordinary stance. Omission resolves to
   `copy`.
2. A use-site `as copy`, `as deep`, `as move`, or `as last` may restate the
   offered stance for one consumer.
3. Selection chooses the accepted stance under the fallback rules.
4. Parameter binding determines which object receives the accepted effects: the
   caller's referent or an independently constructed by-value parameter.

```zax
ordinary : Foo
alwaysDeep : Foo deep
moveSource : Foo move

copyA := ordinary
deepA := ordinary as deep
moveA := moveSource
copyB := moveSource as copy
```

An explicit declaration stance controls later ordinary source and receiver uses
of the completed value. It does not control the stance used to initialize that
value:

```zax
make final : (
  result : Foo move
)() = {
  // ...
}

received : Foo copy = make()
```

`make()` offers a move-stanced result while constructing `received`. After
construction, `received` ordinarily offers copy.

The generic `as <stance>` forms are language-provided source restatements. They
do not execute user code, perform transfer by themselves, or require every type
to declare an `as` operator. They preserve the complete underlying source shape
and affect selection for the immediate consumer.

Transfer stance has one declaration position after the complete type use:

```zax
received : Foo copy
```

It is not another declaration-name-side axis analogous to replacement
permission.

#### Stance meanings and fallback

The offered stance admits these consumer families in preference order:

| Offered stance | Accepted consumer preference |
| --- | --- |
| `copy` | `copy` |
| `deep` | `deep`; otherwise error |
| `move` | `move`, then `copy` |
| `last` | `last`, then `move`, then `copy` |

The accepted consumer, not merely the written offer, determines source effects:

| Accepted stance | Source consequence |
| --- | --- |
| `copy` | Source remains unchanged |
| `deep` | Source remains unchanged and the consumer supplies the type's deep-copy contract |
| `move` | Source remains live under its moved-from contract |
| `last` | Source remains destruction-valid under its terminal contract |

`copy`, `move`, and `last` form an increasing permission ladder. A copy
implementation may satisfy a move or last offer because it exercises less
authority. A move implementation may satisfy a last offer because it preserves
more source usability than terminal transfer requires.

`deep` is separate. It has no copy fallback. A deep implementation is a
programmer-facing semantic promise even though the compiler may not prove every
recursive property of arbitrary user code.

Move and last are opportunities, not mandates to steal a resource. A selected
implementation may copy, transfer selected resources, or leave the source in an
ordinary state when it still satisfies the accepted contract. It must preserve
destination completeness, destruction validity, exclusive ownership, and
exactly-once resource disposition.

Fallback also requires stance variants of one callable or lifecycle family to
preserve the same logical domain operation and destination outcome. Copy, move,
and last variants may differ in:

- whether and how source resources are retained or extracted;
- source post-state;
- allocation, sharing, and copying;
- destruction work that remains;
- and cost.

They must not use stance variation to implement unrelated business behavior.
The compiler may not be able to prove that arbitrary user bodies honor this
semantic contract, but the programmer-facing promise is what makes fallback and
stance-directed selection coherent.

#### Ordinary parameter contracts

The recommended ordinary declarations are reference-shaped:

```zax
+++ final : ()(
  source : Foo readonly & copy
) = {
  // ...
}

+++ final : ()(
  source : Foo readonly & deep
) = {
  // ...
}

+++ final : ()(
  source : Foo mutable writable & move
) = {
  // ...
}

+++ final : ()(
  source : Foo mutable writable & last
) = {
  // ...
}
```

Copy and deep normally need readable access. Move and last normally need mutable
state through writable access because they may extract resources. Unusual
readonly move or last contracts remain legal when their actual behavior is
coherent, for example when transfer operates on external state without changing
the represented source. Legality does not guarantee natural selection.

These examples use omitted mutability and place qualifiers as baseline
shorthand. Each omission resolves to one concrete default shape; it does not
make the declaration accept every mutable/immutable or final/varying
combination. A reusable family needs explicit qualifier-complete declarations or
the future generic mechanism described below.

##### By-value parameters

By-value stance parameters are legal and have distinct meaning:

| Parameter | Binding and body behavior |
| --- | --- |
| `Foo copy` | Copy-construct the independent parameter; retain copy in the body |
| `Foo deep` | Deep-construct the independent parameter; retain deep in the body |
| `Foo move` | Copy in an independent parameter; retain move in the body |
| `Foo last` | Copy in an independent parameter; retain last in the body |

By-value move and last provide a **copy-in barrier followed by owned destructive
processing**:

```zax
process final : ()(
  input : Foo move
) = {
  consume(input.resource)
  // input.resource ordinarily offers move.
}
```

The caller's original `Foo` remains unchanged by processing of `input`. A
move-only source cannot use this form when the preliminary copy is unavailable.
Elision may remove storage or operations only when it preserves which object may
lose resources.

By-value deep first performs the required deep construction. The resulting local
retains deep:

```zax
operator binary '=' final : ()(
  input : Foo deep
) = {
  _.restricted =
    managedCopyWithBRestriction(input.restricted, input.policy)

  _.first = input.first as move
  _.policy = input.policy as move
}
```

This form may perform more deep work than intended. A readonly-reference deep
parameter is the recommended ordinary form because it lets the implementation
choose the appropriate operation for each destination member without first
constructing a complete deep temporary.

##### Reference versus value selection

By-value and reference-shaped move or last declarations may coexist. They are
different contracts, not duplicate spellings.

For an ordinary writable source and otherwise equal candidates, the normal
mutable/writable reference form is better than the by-value form:

```zax
process(foo as move)
```

A safe readonly restatement can make the writable-reference form nonviable and
select copy-in processing:

```zax
process((foo as readonly) as move)
```

If a readonly-reference move variant also exists, the readonly reference and
by-value forms may remain incomparable. The call is ambiguous unless another
dimension chooses a winner.

There is no universal "reference beats value" rule. Exact stance, value/reference
binding, type, qualification, and result quality remain independent partial-order
dimensions. If no viable candidate dominates every other viable candidate,
selection reports ambiguity.

#### Owned projection, references, aliases, and receivers

An owned value declaration carries its stance into ordinary projections unless
the projected declaration states another stance:

```zax
container : Container move

consume(container.first)
// first offers move when its declaration has no explicit stance.
```

A by-value parameter is owned local storage and follows the same rule.

An explicit contained declaration stance wins over inherited owned stance:

```zax
Container :: type {
  first : Resource copy
}

container : Container move
consume(container.first) // copy
```

A use-site restatement has the highest priority:

```zax
consume(container.first as last)
```

The precedence is:

1. use-site `as <stance>`;
2. explicit contained declaration stance;
3. stance inherited from owned containing storage;
4. implicit copy.

A same-place alias has its own declaration stance. It does not silently inherit
the destructive default of the declaration it aliases:

```zax
owner : Foo move
alias : Foo mutable writable & = owner

consume(alias)         // copy-default through alias
consume(alias as move) // explicit destructive authority
```

An explicitly stance-qualified alias may deliberately establish another
default, subject to its qualifications and future lifetime rules.

##### Named destructive references

A move- or last-stanced reference grants authority to the current selected
implementation. Ordinary named uses inside the body reset to copy:

```zax
relay final : ()(
  input : Foo mutable writable & move
) = {
  inspect(input)         // copy/default
  consume(input as move) // explicitly renew move
}
```

The same rule applies to member projections from that reference. Move and last
do not cascade automatically through a custom implementation.

Copy remains copy. Deep may carry through unstanced readable projections because
it is non-destructive and recursive propagation is part of its useful mental
model.

##### Receiver stance

Receiver stance is an independent callable slot. It affects selection of the
current implementation but does not change argument or result stances.

```zax
Foo :: type {
  operation final : ()() deep = {
    _.member.anotherOperation()
    // member receives deep when it has no explicit stance.
  }
}
```

An explicit receiver stance admits only that exact stance. Receiver selection
does not use the source fallback ladder: a move receiver declaration does not
service last, nor does last service move.

An unstanced receiver declaration is the fallback for every receiver stance. Its
body normalizes `_` to copy regardless of the incoming receiver stance:

```zax
Foo :: type {
  operation final : ()() = {
    _.member.anotherOperation()
    // Copy/default body context.
  }
}
```

An explicit copy receiver declaration and an unstanced receiver declaration are
not identical:

```zax
Foo :: type {
  operation final : ()() = {
    // Fallback for every receiver stance.
  }

  operation final : ()() copy = {
    // Exact copy-receiver specialization.
  }
}
```

Implicit or explicit copy selects the exact copy variant. Deep, move, or last
may use the unstanced fallback.

Inside exact receiver implementations:

| Selected receiver declaration | Ordinary nested receiver use |
| --- | --- |
| `copy` | Copy |
| `deep` | Deep through unstanced projections |
| `move` | Copy unless explicitly renewed |
| `last` | Copy unless explicitly renewed |
| Unstanced fallback | Copy |

The current move or last implementation remains fixed and retains authority to
perform its declared work. Each nested call is a new consumer selection, so
destructive stance must be restated:

```zax
Foo :: type {
  operation final : ()() last = {
    _.inspect()
    (_ as last).finish()
  }
}
```

This matches the named destructive-reference rule and keeps actual transfer
sites visible.

#### Qualification is concrete

Every ordinary callable prototype resolves complete mutability, place, access,
and transfer qualifications. Omission applies contextual defaults; it does not
create qualifier erasure, hidden runtime branching, or implicit generic
specialization.

For example:

```zax
func final : ()(
  input : Foo readonly &
) = {
}
```

resolves under the baseline defaults to a mutable, readonly, varying reference.
It is the same concrete prototype as spelling those resolved qualifiers
explicitly.

`readonly` narrows only the current access path. It does not erase:

- whether the value is mutable or immutable; or
- whether the referenced place is final or varying.

Those axes remain part of viability, preference, result shape, reflection, and
prototype identity.

A final reference and a varying reference are different exact shapes:

```zax
process final : ()(
  input : Foo mutable readonly final &
) = {
}

process final : ()(
  input : Foo mutable readonly varying &
) = {
}
```

An ordinary non-generic function cannot defer that choice until a caller and
then fork or branch its body. It declares one exact shape.

##### Qualifier families and generics

Concrete language semantics therefore use qualifier-complete declaration
families. A flexible author may write the valid combinations explicitly or use a
future explicit generic mechanism to generate exact specializations.

Illustrative future syntax:

```zax
func$(Mutability, PlaceStance) final : (
  result : Foo Mutability readonly PlaceStance &
)(
  input : Foo Mutability readonly PlaceStance &
) = {
  // ...
}
```

The spelling is not accepted. The aligned requirement is:

- qualifier abstraction must be explicit;
- every selected specialization has one exact concrete prototype;
- dependent result qualification can repeat inferred input metadata;
- no ordinary omitted qualifier becomes generic;
- invalid specializations require constraints or demand-time diagnostics;
- only demanded specializations need executable code;
- implementation deduplication must not erase language-level signature
  identity.

This is Option D for concrete semantics with Option B for authoring reuse.
Qualifier erasure and removal of mutability/place axes are not selected.

Future `own` work must promote a finite set of exact prototypes or concrete
generic specializations. It must not reserve an unbounded runtime surface merely
because a compile-time generic family exists.

#### Selection, ambiguity, and unavailable declarations

Callable selection keeps these stages separate:

1. declaration legality;
2. discovery and mapping;
3. viability;
4. partial-order preference;
5. availability.

Distinct declarations are legal even when no natural call can select one without
ambiguity. The compiler need not prove at declaration time that every callable
will eventually be naturally selectable.

Exact canonical duplicate signatures are redeclaration errors. Otherwise:

- latent ambiguity is harmless until demanded;
- several nondominated viable candidates produce a use-site ambiguity;
- a uniquely best unavailable candidate produces an unavailable-best error;
- selection does not fall through to a weaker available declaration;
- declaration, import, source, or discovery order never breaks a tie.

An exact typed function alias or resolved function value may select one complete
prototype:

```zax
myNonAmbiguousFunc : ExactPrototype = polymorphicFunc
myNonAmbiguousFunc(argument)
```

This is intentional contract selection, not a workaround for an implementation
failure. Future `own` work has a complete prototype and must use the same exact
signature discipline when deciding which declarations are promoted.

Detailed cross-axis preference remains future callable-selection work. Later
revision may improve rules but must preserve the semantic distinctions among
value/reference binding, qualification, stance, and result shape.

#### Terminal opportunity is explicit

The compiler does not silently upgrade implicit copy or explicit move to last.
Selecting another last overload is language semantics, not an optimization.

When all of the following hold:

1. the source declaration stance is implicit;
2. analysis proves this use is terminal under the applicable contract;
3. a last or fallback move consumer is viable; and
4. terminal offering could materially change the accepted contract;

the compiler reports an intent error:

```zax
forward(foo)
// error: terminal opportunity requires explicit stance intent
```

The programmer stabilizes the choice:

```zax
forward(foo as last)
forward(foo as move)
forward(foo as copy)
forward(foo as deep)
```

or deliberately retains the implicit behavior:

```zax
intent<implicit-stance-at-terminal-use>{
  forward(foo)
}
```

No error is useful when last would select the same accepted contract and source
post-state.

An explicitly declared stance remains authoritative:

```zax
source : Foo move
forward(source)
```

The compiler does not upgrade it to last. A use-site `as last` is required.

Improved analysis may discover more terminal opportunities and produce new
intent errors. It must not silently select another user body. Destruction does
not count as ordinary later use. Branches may contain separate path-terminal
uses. Loop recognition may improve without requiring the first language version
to prove every terminal path.

Ordinary bounded references cannot escape their source lifetime. Pointers,
ownership handles, and explicit lifetime mechanisms may create different
terminal-proof obligations.

#### Move and terminal source state

A selected move consumer leaves the affected object live under its moved-from
contract. A selected last consumer leaves it at least destruction-valid under
its terminal contract. Generic `as last` does not end the outer lifetime or
reschedule its destructor.

The selected binding form identifies the affected object:

- reference-shaped move or last affects the caller's referent;
- by-value move or last affects only the independently constructed local
  parameter.

Ordinary source use after an accepted last transfer is an intent error:

```zax
process(foo as last)
printStats(foo) // error: terminal-source reuse
```

A defined but suspicious terminal-state operation may be acknowledged:

```zax
process(foo as last)

intent<terminal-source-reuse>{
  earlyCleanHandle(foo)
}
```

The acknowledgement does not make an invalid operation valid. The selected
operation and type must support that terminal-state use.

The source's eventual destructor remains mandatory and may call helpers. A last
implementation must leave all state required by its destructor valid.

The compiler trusts declared stance contracts across `= existing` reuse.
Failure to visibly steal a resource is not a violation. Provable violations
belong to the implementation, including duplicate exclusive ownership, resource
loss, double disposition, incomplete destination state, or a source unsafe to
destroy.

#### Binding, exercise, and completion

These moments are distinct:

1. the source expression is evaluated and its stance is accepted;
2. the parameter or other consumer slot binds in source order;
3. the selected operation may exercise move or last authority;
4. the complete consumer operation establishes its postconditions;
5. later surrounding evaluation continues.

A by-value parameter is constructed during binding. A stance-qualified
reference binds authority to existing storage; its body may exercise that
authority later during the call.

This distinction replaces the earlier blanket assumption that every move or last
resource effect occurs during immediate parameter binding. Caller source order
still fixes evaluation and binding. The selected function or lifecycle body owns
when it exercises the accepted authority within its completion boundary.

Return expressions evaluate left to right and establish their result slots in
order. A moved-from local receives its later destructor once. A terminal local
receives exactly the remaining shell and member disposition required by its
terminal contract.

#### Optional transfer adapters

Generic source restatement and protected optional operations are distinct:

```zax
source as last // generic stance only
last source    // protected optional adapter when source is optional
```

Protected `last optionalSource`:

1. offers last to the optional payload through the selected consumer;
2. marks the original optional terminal for source analysis immediately when
   accepted;
3. keeps the wrapper and required payload state physically live for the complete
   singular consumer operation;
4. rejects unrelated later source use as terminal-source reuse during that
   operation;
5. dispositions any remaining payload state at operation completion; and
6. leaves the original wrapper absent afterward.

Reference- and by-value consumers use the same cleanup boundary. The original
wrapper becomes absent after the complete call, construction, assignment, or
other singular consumer finishes, not at a different time based on parameter
shape.

```zax
observe(
  last source,
  wasPresent: ?source // error: terminal-source reuse
)
```

Physical presence retained for the accepted consumer does not make unrelated
later use legal. An acknowledged presence test may observe represented presence
before cleanup but cannot restore ordinary payload validity.

Protected `move optionalSource` forwards move inward without scheduled wrapper
cleanup. Absence remains absent; presence remains present with one live
moved-from payload.

Optional promotion must also teach that `<<>>` swap is defined for optional
values and integrate that operation with the applicable operator owner.

#### Generated transfer operations

Generated copy is the conservative baseline. Zax does not generally generate
deep, move, or last operations:

- deep requires an exact deep contract;
- move and last fall back through the aligned ladder;
- built-in or library-owned types may declare additional operations.

Declaring an unrelated polymorphic operation does not suppress generation.
Generation is suppressed or replaced only by the exact generated shape:

- a matching body supplies the implementation;
- a matching bodyless declaration leaves the shape unavailable;
- matching `= default` requests the language-defined implementation;
- matching `= existing` deliberately reuses a compatible declaration;
- matching `= forbidden` prohibits the shape.

An incorrect attempted replacement may coexist with generated behavior and cause
use-site ambiguity. That is acceptable. Diagnostics should show the exact
generated signature and the mismatch.

##### Qualifier-complete generated family

Generated copy is one compiler-owned generic schema exposed as exact,
qualifier-complete declarations. It is not one qualifier-erased prototype.

For each supported concrete self shape, copy construction needs exact source
place variants. Schematically:

```zax
+++ final : ()(
  : Foo mutable readonly final & copy
) = default

+++ final : ()(
  : Foo mutable readonly varying & copy
) = default
```

The source parameter is compiler-anonymous. Copy construction has no receiver
stance. Corresponding immutable variants may be needed for immutable self
shapes. Future immutable work must revisit the assumption that the source has
the same mutability shape as the constructed value.

Generated whole-value copy assignment uses an unstanced transfer receiver,
requires a varying place, and returns compiler-anonymous readonly copy access to
the assigned receiver so association remains possible. Schematically:

```zax
operator binary '=' final : (
  : Foo mutable readonly varying & copy
)(
  : Foo mutable readonly final & copy
) mutable writable varying = default

operator binary '=' final : (
  : Foo mutable readonly varying & copy
)(
  : Foo mutable readonly varying & copy
) mutable writable varying = default
```

The language-defined body returns `_`. The final-source and varying-source forms
are distinct exact signatures. A final receiver receives no canonical generated
whole-value assignment, though a domain-specific `=` may explicitly accept a
final receiver and perform coherent in-lifetime behavior.

For an immutable varying receiver, the applicable generated `=` uses the
compiler-owned reconstructive lifecycle skeleton rather than mutable
in-lifetime assignment. Its exact immutable self family must preserve:

- immutable receiver truth;
- type-side varying capability;
- declaration-side varying replacement permission;
- writable access;
- exact source qualification;
- result access to the new current receiver lifetime;
- and exactly-once old-resource disposition.

A concrete resolved self shape is mutable or immutable, never both. There is no
runtime mutability branch inside one generated operation.

Future generics may let programmers author one qualifier-parameterized family
whose demanded exact specializations match these signatures. `= existing` alone
does not transform incompatible prototypes or create dependent result
qualification.

#### Results and forwarding

A result declaration stance becomes active when its slot is constructed and
remains the produced result stance after return:

```zax
make final : (
  result : Foo move
)() = {
  result.+++(source as copy)
  inspect(result)
  // result now ordinarily offers move.
}
```

The source used to fill the result slot has an independent stance:

```zax
make final : (
  result : Foo move
)() = {
  return source as copy
}
```

The copy offer controls construction of `result`; the completed result then
offers move.

Forwarding across another result declaration crosses two contracts:

- the producer result stance controls transfer into the forwarding result slot;
- the forwarding result declaration controls what its caller receives.

An inferred declaration may adopt both the concrete value type and result
stance. Complete `:=` reference/pointer-shape inference remains later
declaration work.

Reference capture behaves like an alias and must not silently inherit a
destructive declaration stance. Exact lambda capture syntax, by-value stance,
repeated invocation, and lifetime behavior remain future lambda work.

#### Aliasing and exactly-once disposition

There is no blanket same-object prohibition:

```zax
value = value
value = value as move
value = value as last
value = makeValueMove(value)
```

Exact same-object behavior belongs to the selected operation contract. Numeric
or domain operations may define coherent self behavior. Other operations may
have conflicting destination and source postconditions.

An interior alias can become invalid when replacement ends the old member
lifetime:

```zax
owner = owner.payload as move
```

A reconstructive fallback cannot destroy `owner.payload` and then use a
reference to that ended lifetime. A custom replacement may transfer the member
before dispositioning the remaining old state.

The compiler should diagnose conflicts it can prove. Indirect alias and effect
analysis may require:

- reference-origin and return-origin contracts;
- operation-specific self-safe behavior;
- lifetime and effect summaries;
- narrow unsafe `replacement-alias` assertions for valid but unproved cases;
- and optional debug instrumentation.

An unsafe assertion cannot legalize a known-ended lifetime or known qualifier
violation.

Every successful operation must disposition each source resource, old
destination resource, result slot, member lifetime, and temporary exactly once.

#### Intent acknowledgements

Intent errors are hard errors for defined source that is likely mistaken.
`intent<...>{...}` confirms deliberate meaning. It is distinct from:

- `unsafe`, which accepts responsibility for a required valid property the
  compiler cannot prove; and
- lint suppression, which changes only advisory reporting.

Neither intent nor unsafe can make a known-invalid operation valid.

The initial aligned category registry is:

| Category | Meaning acknowledged |
| --- | --- |
| `implicit-stance-at-terminal-use` | Deliberately retain implicit transfer stance at a proven material terminal opportunity |
| `terminal-source-reuse` | Deliberately perform a defined operation after accepted terminal transfer |
| `asymmetric-saturating-magnitude` | Deliberately parse `\|\|value\|` as asymmetric saturating magnitude rather than malformed norm |

The saturating-magnitude form should use:

```zax
intent<asymmetric-saturating-magnitude>{ ||value| }
```

`bare{...}` remains only a keyword-neutralizing enclosure. Existing promoted and
raw examples that use `bare{ ||value| }` require correction during the applicable
authorized promotion.

#### Costs, diagnostics, and source stability

Transfer stance exposes permission and preference, not a universal cost bound.
Programmer-facing teaching must preserve these costs:

- ordinary copy may copy inline state, retain shared backing, update reference
  counts, allocate, invoke user code, or use copy-on-write behavior;
- deep transfer may traverse and allocate recursively under the type's deep
  contract;
- a by-value parameter may construct a complete independent value before the
  body performs more copy, deep, move, or last work;
- reference-shaped move or last may change caller-owned source state directly;
- move and last may fall back to copy, retaining copy's cost;
- generated structural copy performs the selected member operations;
- assignment or replacement must also disposition the old destination;
- optional terminal cleanup performs payload work at consumer completion;
- capture may allocate or enlarge callable state;
- and lifetime, alias, terminal-use, and generic-specialization analysis add
  compile-time and tooling cost.

No stance promises constant time, zero allocation, or resource theft. A type's
declared operation family and documentation own its concrete cost model.

Diagnostics must identify the deciding contract rather than report only a
generic mismatch. Representative distinctions include:

- unsupported or unavailable `deep` with no fallback;
- the accepted move or last fallback and resulting source post-state;
- value/reference ambiguity;
- qualifier mismatch at the exact mutability, place, access, or stance layer;
- terminal opportunity requiring explicit source intent;
- ordinary use after accepted terminal transfer;
- a source or result consumed more than once;
- an exact generated signature that a bodyless, default, existing, or forbidden
  declaration failed to match;
- a generated containing operation blocked by a specific member path;
- same-object behavior unsupported by the selected operation;
- a possible interior alias whose lifetime would end;
- an invalid intent or unsafe acknowledgement;
- incomplete result, member, or replacement state;
- and zero or multiple disposition of one resource.

A nested generated failure should show the requested outer contract, resolved
qualifier path, and first member whose exact operation is missing, ambiguous, or
unavailable.

Source-stability constraints include:

- an explicit declaration or use-site stance is never silently upgraded;
- improved terminal analysis produces intent errors rather than selecting a
  different user body;
- adding another stance variant may create a loud ambiguity but never wins by
  declaration, import, source, or discovery order;
- generated operation availability is exact-shape and demand-driven;
- formatter or source-preserving tools must not add, remove, or change a stance
  or intent acknowledgement;
- and optimization or implementation deduplication must preserve selected
  contracts, source effects, callbacks, result shape, and destruction order.

#### Promotion teaching contract

The aligned findings above are an analytical completeness map, not a
programmer-facing presentation order. Promotion must reconstruct the material
for a competent cold reader rather than transplanting this working record.

Teaching begins with the ordinary-looking problem:

```zax
a := b
c = d
```

The reader must first understand why those forms alone do not reveal:

- whether resources are shared or independently duplicated;
- whether exclusive ownership may transfer;
- whether the source remains ordinarily usable;
- whether the source is at its final meaningful use;
- or which allocation, copying, and destruction costs follow.

Only then should the documentation introduce transfer stance as the shared
solution across construction, assignment, calls, results, and receivers.

The central teaching statement must be prominent:

> `source as move` changes the stance offered for consumer selection. It does
> not move anything by itself. Any transfer or source-state change comes from
> the selected consumer.

The same principle applies to every `as <stance>` form.

Each stance needs its own motivating use case before the complete fallback table:

1. `copy` preserves the source and accepts the type's ordinary sharing or
   duplication policy.
2. `deep` requests the type's independent-copy contract when ordinary sharing is
   unsuitable.
3. `move` permits resource transfer while the source remains a live moved-from
   value.
4. `last` permits terminal resource reuse when ordinary source preservation is no
   longer required, while the original lifetime remains destruction-valid.

By-value and reference-shaped contracts need a direct paired explanation:

- by value constructs an independent parameter before body execution;
- by-value move or last therefore processes that private copy rather than the
  caller's object;
- by reference binds authority to caller-owned storage;
- and nested destructive use through a reference must be explicitly renewed.

Receiver teaching must show where stance comes from before presenting
receiver-qualified declaration syntax:

```zax
document : Document deep
document.publish()

(document as copy).publish()
```

The explanation should then connect declaration stance and use-site restatement
to exact receiver selection, unstanced fallback, body normalization, and nested
propagation or renewal.

Before qualifier-complete transfer families, promotion must briefly reestablish
the promises made by the qualifier axes:

- `mutable`: the current value lifetime may change;
- `immutable`: the current value lifetime does not change through ordinary safe
  behavior;
- `varying`: the place may receive another value lifetime;
- `final`: the place does not receive another value lifetime;
- `writable`: this access path may perform an otherwise permitted change;
- `readonly`: this access path may only observe.

Tables consolidate behavior after the reader understands what varies. They do
not replace the initial problem, mental model, or motivating examples.

Promotion removes discovery chronology and rejected alternatives whose only
purpose was to help this review. It does not teach:

- invented multiplicative operator families;
- the superseded exact-stance candidate;
- discarded silent automatic-last behavior;
- incorrect comparative-language reasoning;
- or option-by-option working discussion.

Promoted examples use meaningful domain names rather than `operation`, which is
too easy to confuse with `operator`. Zax example indentation uses 2 spaces.
Positive valid examples precede diagnostics, and critical source effects and
costs remain visible in source comments where readers may scan before prose.

### Deliberate deferrals and live destinations

The following material is captured but not designed here.

#### Callable selection

Future callable-selection work must own:

- legal declarations that may be naturally unselectable;
- demand-time ambiguity rather than global declaration-time proof;
- exact-prototype aliases and function values;
- cross-axis value/reference/qualification/stance/result preference;
- generic-specialization preference;
- latent generated-versus-declared ambiguity;
- and source-compatibility consequences of later preference revision.

The current raw `selection` input owns flow and pattern selection, not callable
selection. Documentation fit should propose an indexed callable-selection raw
owner rather than overloading the existing file. Until then, this active work
record is the live capture.

#### Qualifier generics, metadata, reflection, and `own`

The indexed [raw generic input](../../raw/type-parameters-and-generics.md) must
define qualifier parameters, constraints, inference, dependent result
qualification, demand-driven specialization, diagnostics for invalid
specializations, and interaction with generated families.

The indexed [raw reflection input](../../raw/reflection.md) must preserve the
distinction between a generic declaration family and each concrete
specialization without erasing exact prototype identity.

The indexed [raw `own` input](../../raw/owned-composition.md) must require future
`own` work to select and promote finite exact prototypes or concrete
specializations. It must preserve qualifications and transfer behavior and must
not create an unbounded runtime surface for an open generic family.

#### Immutable self shapes

Future immutable work must revisit:

- whether copy construction requires the same source mutability as destination;
- exact generated immutable copy-constructor variants;
- exact immutable reconstructive assignment signatures;
- receiver varying and declaration-side replacement permission;
- result qualification after reconstruction;
- and compatibility or implementation reuse across mutable and immutable
  families.

The current generated signatures are aligned conceptual schemas, not formal
grammar or permanent compatibility contracts.

#### Pointers, references, and lifetime policies

Future lifetime work must define:

- reference origin and place tracking;
- pointer-object versus pointee transfer;
- ownership handles and lifetime-policy plurality;
- pointer/reference binding replacement or rebinding;
- terminal capabilities that survive into a body;
- moved-from and terminal operation eligibility;
- and the proof boundary for terminal opportunity across aliases.

No transfer stance may invent pointee authority merely because a pointer value
moves.

#### Lambda and callable construction

Future lambda/capture work must preserve:

- by-value capture ownership and stance;
- reference capture resetting destructive default like an alias;
- explicit destructive restatement;
- repeated-invocation behavior;
- capture lifetime and allocation;
- and forwarding through generated callable types.

The indexed
[raw callable-composition input](../../raw/function-composition-and-chaining.md)
is the live destination. Documentation fit must retain the material there or
propose a dedicated lambda owner before this work is archived.

#### Safety and analysis controls

Existing [raw safety](../../raw/safety.md),
[analysis-control](../../raw/analysis-controls.md), and
[lifetime](../../raw/lifetimes.md) inputs remain the live destinations for:

- use after move or last;
- source/result consumption at most once;
- replacement aliasing;
- lifetime escape;
- returned reference validity;
- opaque resource disposition;
- and contract-version pressure as analysis improves.

False unsafe lifetime or alias assertions may have undefined consequences
without a required runtime check. Debug tooling may detect and panic without
creating a language guarantee.

#### Async and concurrency

All completion rules here are synchronous. The indexed
[raw async input](../../raw/async.md) retains suspension, cancellation, thread
transfer, executor-lifetime, completion, and disposition pressure.

### Likely promotion ownership

The cross-cutting mental model is cohesive enough to require a dedicated
human-facing transfer owner rather than forcing readers to reconstruct it from
optional, invocation, qualifiers, construction, and operators.

Likely local ownership after promotion:

| Owner | Local responsibility |
| --- | --- |
| Dedicated transfer owner | Four-stage model, stance meanings, fallback, source state, propagation, timing, costs, diagnostics, and source stability |
| Optional values | Protected move/last adapters, presence and payload state, cleanup timing, nesting, and optional `<<>>` swap |
| Function invocation | Parameter binding forms, partial-order integration, result stance, forwarding, completion, and ambiguity |
| Construction and destruction | Exact generated families, lifecycle declarations, replacement, resource disposition, and destructor interaction |
| Qualifiers | Concrete qualifier axes, destructive authority, receiver requirements, and prohibition on silent authority increase |
| Declarations and bindings | Declaration stance, initialization independence, result declarations, and inference boundaries |
| Operators and catalog | Protected `as <stance>` source forms, optional protected forms, precedence, and generated assignment result |
| Terms | Transfer stance, offered/accepted stance, moved-from state, terminal state, and related shared vocabulary |
| Intent owner | Intent acknowledgement mechanism and searchable category registry |
| Indexed raw owners | Callable selection, qualifier generics, lambda capture, lifetime, safety, analysis controls, `own`, reflection, immutable self shapes, and async pressure |

The exact file and directory structure remains a documentation-fit question. No
lasting owner is authorized by this working-record edit.

### Superseded initial reconstruction

Everything from this heading through the end of the file is the initial
evidence-based reconstruction written before maintainer review. It remains raw
provenance but is superseded wherever it conflicts with the aligned findings
above. It must not be used as the current candidate model.

### Initial review entry point

#### Candidate programmer model

The current candidate is:

> A transfer stance states the source-state contract under which one selected
> consumer may obtain a value. The stance expression is inert until that consumer
> accepts it. Acceptance constructs or binds the destination immediately, then
> establishes the stance's source post-state before source-order evaluation
> continues.

The four stances are semantically distinct:

| Stance | Destination promise | Successful source post-state |
| --- | --- | --- |
| `copy` | An ordinary copied value, with sharing and aliasing only where the type's copy contract permits it | Source remains unchanged |
| `deep` | A recursively independent value under the type's deep-transfer contract | Source remains unchanged |
| `move` | A valid value obtained under the type's nonterminal move contract | Source remains live in its defined moved-from state |
| `last` | A valid value obtained under the type's terminal-transfer contract | Transferable source contents are terminally dispositioned; the original owner retains a destructible source shell until its ordinary lifetime completion |

An explicit stance would require the corresponding semantic contract. It would
not require a particular implementation technique: a `last` operation could
steal resources, copy and then dispose them, or combine those strategies.
However, another stance would not silently stand in for the requested one.
Intentional implementation reuse would be declared as an exact operation, for
example with `= existing`, so the type still promises the requested source
post-state and cost remains attributable to its public operation.

Illustrative candidate source uses one protected pre-unary family:

```zax
ordinarySource : MyBuffer
copySource : MyBuffer
deepSource : MyBuffer
moveSource : MyBuffer
lastSource : MyBuffer

ordinary : MyBuffer = ordinarySource // ordinary by-value transfer is copy
copied : MyBuffer = copy copySource
independent : MyBuffer = deep deepSource
moved : MyBuffer = move moveSource
terminal : MyBuffer = last lastSource
```

`copy` and `deep` in this example are proposed forms, not current accepted
syntax. Current owners establish pre-unary `move source` and `last source`,
while legacy material and the optional owner also preserve post-unary
`source as copy`, `source as deep`, `source as move`, and `source as last` as
candidates.

The stance does not become part of the destination type:

```zax
source : MyBuffer

result := move source
// Candidate: result is MyBuffer, directly initialized under move semantics.
// It is not a stored "MyBuffer move" source view.

reference : & = source
// The explicit reference shape remains distinct from value inference.
```

The declaration is the accepting consumer. `:=` therefore infers the ordinary
value result after transfer, not a reference, pointer, or persistent
stance-qualified wrapper. Complete partial-type inference remains later
declaration work.

#### Most important disagreement with legacy behavior

Legacy pointer material would automatically infer `last` from dataflow when a
named value appears not to be used again. This candidate does not.

```zax
first := consume(source)
observe(source)
```

Removing `observe(source)` must not silently change the first call from copy or
reference binding to terminal transfer. Such a change could alter overload
selection, source state, destructor behavior, allocation, callbacks, and cost.
For named source, terminal intent should therefore be written:

```zax
first := consume(last source)
```

Compiler-created storage may still be elided when all selected operations,
source effects, destruction effects, and observable ordering are preserved.
Elision is not permission to reclassify a copy as `last`.

This deliberately prefers source stability and explicit cost over the legacy
last-use optimization. Whether intrinsically ephemeral producer-result slots
should receive a separate, stable default is still open; it must not revive
flow-sensitive reinterpretation of named source.

#### Most important unresolved semantic hole

The evidence fixes moved and terminal *effects* more clearly than it fixes the
post-transfer usability of the source shell.

- A moved-from source is live and must at least support destruction and
  authorized whole-value replacement. It is not yet decided whether every
  ordinary operation may accept that state, whether the type publishes a wider
  moved-from contract, or whether the compiler tracks a restricted moved-from
  state.
- A terminal source retains enough valid state for exactly-once shell
  destruction. It is not yet decided which operations, if any, may inspect or
  replace that shell before its ordinary lifetime boundary.
- `last optional` is an established special pressure: a present payload ends and
  the still-live optional wrapper becomes absent. A coherent general rule must
  explain whether this is a normal type-defined terminal shell, an
  optional-specific interception, or evidence that `last` cannot imply a
  universal whole-source state.

The candidate below uses a **type-defined moved-from state** and a
**destructible terminal shell** as the working model. That preserves optional
behavior and exactly-once disposition, but it introduces state-sensitive
operation eligibility that needs maintainer review before it can be aligned.

#### Decisions requested from maintainer review

Review should begin with these decisions, in order:

1. Is a written stance an exact semantic requirement, rather than permission to
   fall back silently to another stance?
2. Is ordinary unmarked by-value transfer `copy`, with no flow-sensitive
   automatic `last` for named values?
3. Should the source family be uniformly pre-unary
   `copy`/`deep`/`move`/`last`, retaining the two current forms and replacing
   the legacy `as ...` candidates?
4. Does `last` leave a destructible terminal shell owned by the source's
   original lifetime, or does it end the whole outer lifetime immediately?
5. What is the minimum safe operation set for moved-from and terminal source
   states?
6. Are reference binding, copy, move, and terminal transfer intentionally
   incomparable when they differ observably, forcing explicit source intent
   rather than a universal preference?

The remaining sections develop the consequences of that candidate and identify
where evidence disagrees.

### Recovered intent and pressure

Several parts of the corpus agree on the following core:

- A stance expression by itself has no state effect.
- The selected constructor, assignment, parameter binding, result binding, or
  other consumer owns the effect.
- Caller inputs and construction-packet entries evaluate and bind immediately
  in source order.
- Copy, move, and terminal transfer have observably different source
  post-states.
- An outer stance must remain visible while a generated operation crosses
  structural layers.
- Transfer cannot silently increase authority or discard independently attached
  qualifications.
- Destruction must give every lifetime and resource exactly one final
  disposition.

The corpus does not yet agree on the abstraction behind those points:

- Legacy constructors model `deep` and `last` as automatically generated copy
  constructor variations and use `source as deep` or `source as last`.
- Legacy pointers model `last` as a reference/pointer qualifier indicating the
  last owner, permit a callee body to extract contents later, and infer `last`
  for apparent final uses.
- Current optional and operator owners model `move source` and `last source` as
  inert pre-unary source expressions of the same optional type.
- Current qualifiers model copy and move as contextual operations, not
  persistent qualifier axes, and reserve terminal mutation authority for a
  lifetime that is ending.
- Current invocation establishes immediate binding, which conflicts with a
  terminal reference whose callee may or may not consume the source later.

The candidate treats the legacy constructor variation, current source view, and
consumer declaration as three surfaces of one operation:

1. source syntax states or defaults the offered stance;
2. the consumer contract states which stance it accepts; and
3. the selected transfer operation implements the stance and its source
   post-state.

`last` is therefore not an ownership qualifier attached indefinitely to an
ordinary reference. If Zax later needs a capability that grants terminal access
to a callee body, that capability needs its own lifetime and completion contract
rather than borrowing transfer-stance syntax and delaying immediate binding.

### The four stance contracts

#### `copy`

`copy` constructs a destination while leaving the source value and its
programmer-visible state unchanged.

It is the ordinary default for an unmarked by-value source:

```zax
source : MyValue
destination : MyValue = source
```

An ordinary copy is not necessarily recursively independent. A type may use
shared backing, reference counting, copy-on-write state, handles, or copied
references when its public copy and qualification contracts permit them. Any
shared authority and lifetime effects remain part of the type's copy behavior.

Explicit `copy source` is useful when an unmarked source could also bind by
reference or participate in another observably different candidate family. It
requires materialization under the copy contract; it does not directly bind the
original object to a reference parameter.

#### `deep`

`deep` leaves the source unchanged and produces a destination that is
recursively independent under the selected type contract.

Independence should be stated by observable consequence rather than by a
required allocation strategy:

- later mutation through one value cannot change the other's observable value;
- ending either value cannot invalidate resources required by the other;
- recursively owned mutable state is not shared;
- immutable interned or global state may be shared only when the sharing cannot
  violate those promises.

This is stronger than ordinary copy, not merely a preference for a more
expensive overload. A copy operation may implement deep transfer only when the
type explicitly exposes an exact deep contract, such as a deep declaration
delegated with `= existing`.

#### `move`

`move` performs a nonterminal destructive transfer:

```zax
source : MyValue
destination : MyValue = move source
```

On successful acceptance:

- the destination is a complete valid value;
- the source remains one live `MyValue` lifetime;
- every source resource is transferred, retained in the source, or disposed
  exactly once;
- the source satisfies the type's moved-from invariant; and
- the original source owner remains responsible for later shell destruction.

The minimum moved-from contract is unresolved. The conservative candidate is:

- destruction is always valid;
- authorized complete replacement may establish a fresh ordinary state;
- another move or `last` may be valid when the type declares it;
- ordinary reads and mutations require the type to promise that operation for
  moved-from state.

This is more explicit than "valid but unspecified." It also requires a way for
operation declarations, generic constraints, reflection, and diagnostics to
describe moved-from eligibility. If that machinery is too costly, the
alternative is to require every move to leave a fully ordinary usable value,
which may make moves more expensive or impossible for some types.

#### `last`

`last` grants terminal transfer authority:

```zax
source : MyValue
destination : MyValue = last source
```

On successful acceptance:

- the destination is a complete valid value;
- no transferred resource remains accidentally owned by both source and
  destination;
- every untransferred source resource is retained for one later disposition or
  is disposed during the transfer;
- the source enters its defined terminal post-state before evaluation continues;
  and
- its original owner completes source-shell destruction exactly once.

The candidate does **not** make stealing mandatory. A type may implement the
terminal contract by copying and then dispositioning the source. The important
difference from `copy` is the source post-state and terminal authority, not a
backend operation count.

The terminal shell model reconciles legacy and current optional evidence:

```zax
source : MyValue?
destination : MyValue? = last source
```

For a present source, optional transfer carries `last` into the payload. The
payload's terminal transfer completes, the source wrapper becomes absent, and
the absent wrapper remains a valid shell for its later destruction. For an
absent source, no payload transfer occurs.

The unresolved part is whether ordinary code may continue to use that wrapper,
or whether only aliases and lifecycle machinery may observe its absent terminal
state. Current optional wording describes the state afterward but does not yet
supply a general post-`last` operation-eligibility rule.

### Source forms and result type

#### Candidate syntax

The uniform source proposal is:

```zax
copy expression
deep expression
move expression
last expression
```

Each is a protected pre-unary form. Grouping determines a larger source:

```zax
move (makeLeft() + makeRight())
```

The source view:

- preserves the expression's resolved value type;
- preserves every value, wrapper, pointer, reference, and access
  qualification;
- adds ephemeral stance intent used only while selecting and completing the
  immediate consumer;
- cannot be stored as a lasting value or returned as a persistent capability;
  and
- performs no transfer merely because it was formed.

This extends the current protected `move` and `last` forms to one regular family.
It avoids overloading `as`, whose current ordinary role is a binary conversion
phrase with a complete destination type argument:

```zax
converted := source as DestinationType
```

Treating `source as deep` as a post-unary exact phrase is grammatically possible,
but it creates two unrelated `as` models, adds phrase-boundary pressure, and
would require deciding whether natural source or an exact phrase fence is
necessary. The pre-unary family is therefore the current recommendation.

Parameter and lifecycle declarations may continue to place the accepted stance
after the complete parameter type:

```zax
+++ final : ()(source : MyValue copy)
+++ final : ()(source : MyValue deep)
+++ final : ()(source : MyValue move)
+++ final : ()(source : MyValue last)
```

This syntax is existing candidate evidence, not yet a final grammar decision.
In a layered type, the stance attaches after the outermost completed type:

```zax
MyType readonly ? writable * immutable * varying deep
```

It does not become another independently qualified pointer, optional, or
referent layer.

#### Combined stances remain unavailable

These forms remain unavailable:

```zax
move copy source // no combined stance
last deep source // no combined stance
```

A single selected operation owns one source post-state. If an API needs an
independent copy followed by terminal disposition, its `last` operation may
implement exactly that behavior without exposing a compound stance.

### Default source intent and candidate selection

#### Ordinary source

For a named value source with no written stance:

- by-value transfer offers ordinary `copy`;
- a compatible reference parameter may bind a reference;
- an already formed pointer value may satisfy a compatible pointer parameter;
- `deep`, `move`, and `last` are not inferred from apparent liveness; and
- pointer or reference layers are not invented merely to make another candidate
  viable.

An unqualified by-value parameter or construction source defaults to `copy`.
Explicit `copy source` narrows the source to that transfer family.

#### Explicit stance

An explicit stance makes only its semantic family viable:

| Source | Directly viable consumer family |
| --- | --- |
| `copy source` | Copy |
| `deep source` | Deep |
| `move source` | Nonterminal move |
| `last source` | Terminal transfer |

A type may expose an exact stance operation by implementing it directly,
requiring the compiler's language-defined default, or explicitly delegating to a
compatible existing operation. It may not make a different stance silently win
because the requested one is absent.

This answers "requires a specialized transfer" at the semantic rather than code
generation level:

- the exact semantic contract is required;
- a distinct specialized body is not;
- reuse must still be visible in the type's operation set; and
- a missing exact contract is a diagnostic.

#### Partial-order preference

Within one stance family, ordinary type, qualification, conversion, parameter,
and result comparisons participate in the shared partial order.

Across families with different effects, there is no universal "cheapest wins"
ordering:

- reference binding may alias while copy snapshots;
- copy may share backing while deep promises independence;
- move leaves a live moved-from source while `last` establishes terminal state;
- pointer transfer may move a handle without transferring the pointee.

The candidate therefore treats such pairs as incomparable unless source syntax
or an exact language rule removes one family.

```zax
inspect final : ()(input : MyValue readonly &)
inspect final : ()(input : MyValue copy)

inspect(source)
// Candidate diagnostic when both are otherwise equally viable:
// aliasing and copy are observably different and neither dominates.

inspect(copy source) // selects only the copy family
```

An exact call-site reference-selection form remains pointer/reference grammar
work. The inability to spell one yet is a real usability hole, not a reason to
make reference silently dominate copy.

Unavailable-best behavior remains intact. A bodyless or forbidden exact stance
declaration can block a weaker match; selection does not fall through to a
different transfer family.

### When acceptance and effects occur

Selection is conceptually complete before execution. Testing a candidate for
viability does not accept the stance and cannot modify source state.

At runtime:

1. evaluate the source expression;
2. begin the already selected consumer's direct binding or construction;
3. run the selected transfer operation;
4. establish the destination and required source post-state;
5. complete any operation-local destruction; and
6. only then continue to the next source-ordered evaluation.

If the transfer does not complete normally, Zax does not invent exception-style
rollback. Current panic input treats unresolved panic as fatal graceful
crashing. Normal-path completeness and exactly-once disposition remain required
where execution continues.

#### Function arguments

```zax
observe(
  move source,
  action: changeSource(source)
)
```

The first argument evaluates and its mapped parameter immediately accepts the
move before `changeSource(source)` begins. Later use must satisfy the moved-from
contract; otherwise it is diagnosed.

A by-value parameter receives its own complete destination value during
binding. A legacy `T & last` parameter that postpones actual consumption until
the callee body would violate this immediate-effect model. If such terminal
capabilities remain useful, they need a separate contract answering:

- when caller use becomes invalid;
- whether the callee must consume;
- what happens on every return path;
- whether the capability may be forwarded or stored; and
- who completes source-shell destruction.

That capability is deferred rather than silently identified with ordinary
`last` parameter binding.

#### Construction packets

Packet entries follow the same rule:

```zax
value : MyContainer = [{
  .first = last firstSource,
  mutate(secondSource),
  .second = move secondSource
}]
```

The first terminal transfer completes before `mutate(secondSource)`. The move of
`secondSource` completes when its own entry binds. Packet order does not change
member construction order, but each bound source effect is already observable
to later entries.

#### Direct initialization

```zax
destination : MyValue = move source
```

This directly constructs `destination`. It does not default-construct and assign
over it. The source's moved-from post-state exists once direct initialization
completes.

#### Assignment and replacement

```zax
destination = last source
```

The right-hand source is evaluated before replacement begins, but aliases may
still point into `destination`. The selected assignment or reconstructive
replacement owns two independent disposition obligations:

1. every old destination resource is retained, transferred, or destroyed once;
2. every source resource reaches the state required by the accepted stance.

A custom replacement constructor may recycle old destination resources. A
generated fallback destroys the old destination lifetime and constructs the new
one. Neither path may double-dispose a source resource merely because the source
aliases the destination.

#### Return expressions and result slots

```zax
make final : (
  first : MyValue,
  second : MyOtherValue
)() = {
  return last firstSource, move secondSource
}
```

Return expressions evaluate left to right. Each immediately constructs its
corresponding result slot and establishes its source post-state before the next
return expression begins.

After every result slot is complete, returning destroys remaining function-local
lifetimes in reverse construction order. A moved-from local still receives its
one later destructor call. A terminally transferred local retains only the
source-shell disposition required by its terminal contract and must not dispose
transferred contents again.

`return result` remains unavailable as a way to transfer an already completed
result slot into itself. Bare return or fallthrough completes that path.

#### Producer results and forwarding

```zax
consume(produce())
```

`produce` first completes its result slots. Mapping into `consume` then treats
those slots as sources for immediate binding. Each producer-result source
survives through its transfer. At synchronous call completion:

- a copied result source is destroyed normally;
- a moved result source is destroyed once in moved-from state; and
- a terminally transferred result source receives only its remaining shell
  disposition.

Whether an unmarked ephemeral result defaults to copy or has a stable
terminal-transfer rule remains open. It cannot be decided merely by observing
that no source name exists; destructor and overload behavior are still
programmer-visible.

#### Captures

Creating a callable with captured state is itself a consumer. Invocation is not
the transfer point:

```zax
// Illustrative only; final capture syntax is deferred.
callback := capture(move source) {
  useCapturedValue()
}
```

The move would complete when `callback` is constructed. Later invocation uses
the captured destination state. Copy, deep, move, and terminal capture must
expose their allocation, lifetime, and indirect-call costs. Exact capture
syntax, generated callable identity, and composition remain owned by indexed
raw callable-composition input.

### Source lifetime and exactly-once disposition

#### Source shell

The **source shell** is the original value lifetime and storage that remain under
the source owner's lifecycle after transferable resources have moved or been
terminally extracted. It is not a second hidden value and does not imply a
particular object layout.

For `copy` and `deep`, the shell remains the unchanged ordinary source.

For `move`, the shell remains a live moved-from value. Its destructor runs once
at the original lifetime boundary unless an authorized replacement first ends
that lifetime and begins another.

For `last`, the candidate preserves the original owner as shell owner:

- transfer does not silently transfer stack storage, a declaration binding, or
  enclosing member storage to the destination;
- the terminal operation leaves tracked state sufficient for later shell
  completion;
- the source owner runs or suppresses generated destruction according to that
  tracked state; and
- a resource extracted, destroyed, or transferred during `last` is not disposed
  again by shell destruction.

This fits existing destructor generation: a custom terminal operation may
explicitly disposition members, and generated destruction runs only for
remaining live member lifetimes.

An alternative whole-lifetime model would end the outer source lifetime during
`last` and suppress its later destructor entirely. That is simpler to state but
does not directly explain the current optional requirement that the same wrapper
becomes absent and remains available for ordinary wrapper destruction. The
maintainer should decide this boundary before detailed terminal syntax.

#### Resource ledger

Every successful transfer must support the following conceptual audit:

| Resource starts in | Allowed final disposition |
| --- | --- |
| Copy/deep source | Still owned by source; independently represented in destination; or shared under an explicit safe copy contract |
| Move source | Transferred to destination; retained in valid moved-from source; or destroyed exactly once |
| Terminal source | Transferred to destination; retained only as part of the defined terminal shell; or destroyed exactly once |
| Old assignment destination | Retained by replacement; transferred into the replacement; or destroyed exactly once |
| Temporary/result shell | Destroyed once at its completion boundary after transfer-specific member state is honored |

Generated and custom operations may achieve these outcomes differently, but
normal completion may not leak an undispositioned resource or leave two owners
believing they have exclusive disposition responsibility.

### Outermost stance propagation

A stance applies to the complete source expression. Generated transfer carries
that one stance inward while decomposing the value:

```zax
source : MyRecord?
destination : MyRecord? = deep source
```

- an absent optional performs no payload transfer;
- a present optional forwards `deep` to its payload;
- a structural payload forwards `deep` to each transferred member;
- every destination layer is established under its own declared qualifications;
  and
- each source layer reaches the post-state owned by that layer's exact transfer
  contract.

Propagation is not blind syntax copying. Each semantic layer may intercept the
stance according to its ownership:

- an optional owns presence and conditional payload lifetime;
- an owning pointer may transfer ownership represented by the pointer;
- a shared pointer may copy or move its ownership handle under a different cost;
- a raw pointer transfer does not by itself transfer or destroy the pointee;
- a reference binding does not become permission to consume the referent; and
- a containing type may declare a custom operation that owns member behavior.

Therefore "carry inward" means that generated decomposition preserves the
outer intent until an owning semantic layer defines its exact effect. It does
not mean that `last` applied to any pointer recursively destroys whatever
address the pointer contains.

#### Custom interception

A declared exact transfer operation intercepts generation at that type:

```zax
+++ final : ()(source : MyRecord move) = {
  // This body owns the complete MyRecord move contract.
}
```

The compiler does not additionally move every member around the custom body.
The body must satisfy complete destination construction, source post-state, and
resource disposition. If it delegates a member transfer, the member's selected
operation owns the next layer.

Generated containing-type operations are demand-driven and stance-specific:

- generated copy selects member copy contracts;
- generated deep selects member deep contracts;
- generated move selects member move contracts;
- generated terminal transfer selects member terminal contracts;
- a missing or ambiguous required member operation makes the containing
  operation unavailable; and
- another stance is not silently substituted.

For a type whose existing operation genuinely satisfies another stance,
`= existing` can make that reuse explicit. A bodyless declaration blocks latent
generation for its exact shape, while `forbidden` permanently prohibits it.
Reflection eventually needs to distinguish declared, generated, defaulted,
delegated, bodyless, and forbidden transfer operations.

### Qualification and authority

A stance changes neither the source type's truths nor the current path's
permissions.

- `copy` and `deep` require the read access declared by their source contract.
- `move` may change a still-live source and therefore needs the corresponding
  source mutability and writable access.
- `last` receives terminal authority only after the selected lifetime policy
  proves that terminal use is permitted; the word itself does not turn an
  arbitrary readonly alias into an owner.
- Type-side `final`/`varying`, declaration-side replacement permission,
  `mutable`/`immutable`, and `writable`/`readonly` remain attached to their own
  layers.
- A stance cannot cause a parameter, result, alias, or capture to regain
  declaration-side replacement permission that the source path lacks.

The destination is governed by the selected consumer's declared result:

- a by-value result receives its declared value qualifications and an
  independently resolved destination place;
- a returned pointer or reference preserves per-level qualifications;
- shared state produced by ordinary copy cannot expose more authority than the
  copy contract safely provides; and
- deep independence may permit a separately mutable destination only when the
  deep contract actually establishes that independence.

Terminal authority and result authority are separate. `last` may dismantle an
ending immutable source when its lifetime contract permits it; that fact alone
does not produce a mutable or writable destination.

### Aliasing and same-object behavior

Evaluation before transfer does not prove independence:

```zax
value = value
value = move value
value = last value
value = viewOf(value)
value = makeReplacementUsing(value)
```

Exact same-object behavior belongs to the selected operation contract.

- Copy self-assignment may be a defined no-op or may invoke a declared operation
  with observable behavior; the language must not assume one universally.
- Generated move self-assignment has conflicting destination and moved-from
  post-states and should be rejected unless the selected operation explicitly
  defines same-object behavior.
- Generated terminal self-transfer has conflicting destination and terminal
  source obligations and should likewise be rejected unless an exact operation
  defines one coherent result.
- An arbitrary user operation may intentionally accept the same object twice;
  there is no blanket same-object error.

Interior aliases are a separate problem. A source reference may point into a
destination member whose lifetime the selected replacement ends. Safe execution
requires one of:

- an independent snapshot established before transition;
- retention of the exact referred-to lifetime through final use;
- an operation contract designed for the alias;
- a lifetime policy proving nonconflicting access; or
- a narrow unsafe alias assertion for a valid case the compiler cannot prove.

An unsafe assertion cannot make a known-ended lifetime usable, legalize known
readonly or immutable mutation, or bypass a final place. A false lifetime or
alias assertion may have undefined consequences without a required runtime
check; debug tooling may detect and panic without creating a language guarantee.
Exact syntax and category names remain with indexed safety and analysis-control
input.

### Costs, diagnostics, and source stability

#### Costs that must remain visible

- ordinary copy may copy inline state, retain shared state, update reference
  counts, allocate, or invoke user code;
- deep transfer may recursively allocate and traverse the entire owned graph;
- move writes or otherwise changes source state and adds later moved-from
  destruction obligations;
- terminal transfer performs source disposition work even when it cannot steal a
  resource;
- generated structural transfer performs member operations in a defined order;
- assignment also dispositions the old destination;
- capture may allocate or enlarge callable state; and
- lifetime and alias proof may add compile-time cost, while selected pointer or
  ownership strategies may add runtime state.

No stance guarantees constant time or zero allocation. A type's declared
operation and documentation own those costs.

#### Required diagnostic distinctions

Diagnostics should distinguish at least:

- unknown or unsupported source stance;
- exact stance operation absent;
- exact stance operation bodyless or forbidden;
- several viable implementations within one stance;
- incomparable reference/copy/deep/move/terminal families;
- source access or qualification insufficient for the requested stance;
- attempted authority increase in a destination, result, parameter, or capture;
- use not permitted by a moved-from contract;
- use not permitted by a terminal-shell contract;
- a source or result consumed more than once;
- generated containing transfer blocked by a specific member;
- same-object transfer unsupported by the selected operation;
- possible interior alias whose lifetime would end;
- a known-invalid unsafe assertion;
- result or member lifetime left incomplete; and
- resource or shell disposition that would occur zero or multiple times.

For a nested generated failure, the diagnostic should show the structural path,
requested outer stance, and first unavailable or conflicting member contract.

#### Source-stability constraints

- Adding a different stance overload must not silently reinterpret an explicitly
  qualified source.
- Adding reference and copy overloads must not resolve an otherwise incomparable
  call by declaration, import, or source order.
- Later uses of a named value must not retroactively change an earlier call to
  `last`.
- A formatter may normalize spacing but may not add, remove, or change a stance.
- Generated operation availability is demand-driven, but an unavailable demanded
  shape is diagnosed rather than replaced by weaker behavior.
- Compiler elision must preserve selected operation, source post-state, callback
  effects, resource disposition, and destruction order.

### Known holes requiring expansion

1. **Moved-from operation eligibility.** The conservative tracked-state model
   needs declaration syntax, generic constraints, reflection, and a usable
   programmer story. The fully ordinary-state alternative needs stronger
   generation requirements and may forfeit cheap moves.
2. **Terminal-shell usability.** Optional absence is concrete, but general
   values, aliases, member sources, and replacement after `last` need one
   coherent rule.
3. **Temporary default.** Copy-only default is predictable but may impose an
   avoidable transfer from producer result slots. Any terminal default must be
   based on a stable source category, not whole-program liveness.
4. **Reference selection syntax.** If reference and copy are incomparable,
   programmers need an exact source form for choosing reference binding without
   inventing pointer/reference grammar here.
5. **Terminal capabilities.** The legacy `T & last` body-time model may encode a
   useful capability, but it is not compatible with ordinary immediate
   by-value binding without a separate completion obligation.
6. **Deep independence.** Cycles, identity-bearing nodes, shared immutable state,
   external resources, and interior references need a precise observable
   contract.
7. **Transfer order inside generated structures.** Member order likely follows
   declaration/construction order, but failure and fatal-panic behavior need
   review against exactly-once disposition.
8. **Result and return stance declaration.** Source effects are clear, but exact
   result-slot syntax and whether a prototype constrains outgoing stance need
   focused grammar work.
9. **Pointer ownership.** Carrying a stance through pointer layers requires the
   future pointer owner to separate pointer-object transfer, ownership transfer,
   pointee lifetime, provenance, and rebinding.
10. **Generic substitution.** A substituted type may add optional, pointer, or
    reference layers or remove a generated operation. Diagnostics and constraints
    must expose the resolved structural path and exact missing stance.
11. **Unsafe contracts.** Exact assertion syntax, mandatory proof by selected
    safety contract, portability across compiler versions, and redundant-control
    severity remain unresolved.

### Immediate consequences and deliberate deferrals

#### Pointer and reference lifetimes

Captured finding: transfer stance cannot also serve as a permanent
reference/pointer ownership qualifier. Pointer-object transfer, referent
authority, `lease`, terminal capability, provenance, and binding replacement
remain distinct questions.

Why it can wait: the current candidate can reject implicit crossing from a
reference handle into referent consumption and require every owning layer to
intercept explicitly.

Activation pressure: a consumer that must retain terminal access into its body,
an owning pointer transfer, pointer rebinding, or an exact reference-selection
form.

Live destination: indexed raw lifetime input, followed by a dedicated pointer
and lifetime owner. Constraint on current work: no stance may invent referent
authority or silently transfer a pointee merely because a pointer value moved.

#### Captures and callable composition

Captured finding: callable construction, not later invocation, is the consumer
of captured source. Outermost stance must reach captured optional or structural
state, and generated callable cost depends on copy/move/terminal behavior.

Why it can wait: no exact capture or composition syntax is accepted, and the
immediate transfer rule can be preserved without selecting it here.

Activation pressure: lambdas, partial application, `>>` composition, callable
reflection, or optional combinators.

Live destination: indexed raw function-composition-and-chaining input.
Constraint on current work: future capture syntax must not defer an already
written `move` or `last` until invocation.

#### Generics

Captured finding: substitution must preserve every resolved structural layer,
qualification, source stance, and exact operation requirement. A generic cannot
silently flatten optional layers or weaken `deep` to copy because a substituted
member lacks deep transfer.

Why it can wait: complete generic parameter, constraint, specialization, and
reflection models are not available.

Activation pressure: a generic body performs transfer, constrains a type by
available stance, or generates a containing lifecycle operation.

Live destination: indexed raw type-parameter and generic input. Constraint on
current work: generated transfer failure must be explainable using the resolved
type and structural member path.

#### Safety and analysis controls

Captured finding: safe transfer needs proof against use after moved/terminal
state, duplicate source/result consumption, invalid returned aliases, and
replacement aliases. A narrow assertion may accept an unproved valid boundary
but cannot legalize a known violation.

Why it can wait: exact safety contracts, assertion spelling, analysis provenance,
and runtime-debug instrumentation are separate reusable concerns.

Activation pressure: an alias-safe operation the compiler cannot prove, opaque
lifecycle code, a returned reference crossing a transfer boundary, or
contract-version portability.

Live destinations: indexed raw safety and analysis-control inputs. Constraint
on current work: no required runtime check is implied; false unsafe lifetime or
alias claims may have undefined consequences, while compile-time-known
violations remain errors.

#### Async and concurrency

Captured finding: the synchronous acceptance and call-completion boundary cannot
be reused unchanged across suspension. A captured parameter or result may
outlive the caller frame, and cancellation needs a disposition point for every
partially transferred resource.

Why it can wait: no accepted async call, suspension, cancellation, or executor
model exists.

Activation pressure: any callable can suspend after accepting a transfer, or a
result/capture can cross a thread or cancellation boundary.

Live destination: indexed raw lifetime input until a dedicated async/concurrency
concern exists. Constraint on current work: claims here are synchronous only.

### Likely lasting owner boundaries

The cross-cutting mental model is too cohesive to leave readers to reconstruct
from optional, invocation, qualifiers, construction, operators, and pointers.
The likely fit is one dedicated human-facing transfer/lifetime-completion owner
that teaches:

- the four stance contracts;
- ordinary and explicit source intent;
- source views and source post-states;
- cross-family viability and preference;
- generated propagation and custom interception;
- source-shell completion and resource disposition;
- common timing across consumer kinds;
- costs, diagnostics, and source stability.

Existing owners would retain local application:

| Owner | Local responsibility after eventual promotion |
| --- | --- |
| Optional values | Absent/present wrapper effects, payload propagation, nested layers, and optional diagnostics |
| Function invocation | Immediate argument binding, result-slot transfer, forwarding, call completion, and callable preference integration |
| Construction, replacement, and destruction | Transfer-operation declarations and generation, packet/assignment timing, old-destination disposition, member order, and destructor suppression |
| Qualifiers | Capability versus permission, per-layer qualification, terminal authority, and prohibition on silent authority increase |
| Declarations and bindings | Direct initialization, `:=` value inference, and explicit partial reference/pointer shape |
| Operators and operator catalog | Protected source forms, precedence, result shape, and diagnostics |
| Operator phrases | Only phrase interaction if any `as ...` form survives; otherwise no transfer definition |
| Terms | Concise shared definitions for transfer stance, moved-from state, terminal state, and source shell |
| Future pointer/lifetime owner | Ownership strategies, reference origin, pointer-object versus pointee transfer, terminal capabilities, and rebinding |

The exact new owner name and structure remain dry-run questions, not an
authorized structural proposal. Legacy `ctor-dtor.md` and `pointers.md` retain
useful evidence until every generated-constructor, temporary-`last`, `lease`,
and terminal-reference concern is promoted, deferred to a live owner, rejected,
or superseded.

## Dispositions and promotion dry run

### Result

**PASS — revalidated 2026-09-04 after result/temporary realignment**

The owner structure, exact 41-file set, legacy disposition, and teaching-debt
lifecycle remain coherent. The newly aligned temporary-receiver, result-mapping,
optional-result, move/last, and teaching-debt-effort findings all have exact
owners within that set and require no new structural decision.

PASS does not authorize promotion. The latest findings remain to be incorporated
under separately authorized promotion.

### Documentation-fit conclusion

Transfer stance is one cohesive cross-cutting concept. Leaving its mental model
distributed among invocation, construction, qualifiers, optional values, and
operators would force a cold reader to reconstruct:

- why the feature exists;
- what `as <stance>` does and does not do;
- the copy/deep/move/last fallback model;
- which object receives source effects;
- how declaration, reference, receiver, result, and generated-operation behavior
  relate;
- and when source state and destruction obligations change.

A dedicated current owner is therefore required. Local owners continue to teach
how transfer appears in their own task without redefining the complete feature.

Intent acknowledgement is independently reusable across terminal transfer,
asymmetric saturating magnitude, and future defined-but-suspicious source. It
needs a second focused current owner rather than being embedded in transfer or
unsafe analysis.

Callable-selection deferrals do not fit the existing raw selection input, which
owns `switch`/`case` and pattern-like selection. They need one new indexed raw
destination.

No new directory family, specification area, implementation document, or
general language reorganization is needed.

### Proposed minimal structure

```text
language/
  transfer-stances.md             # new current conceptual owner
  intent-acknowledgements.md      # new current conceptual owner

project/raw/
  callable-selection.md           # new indexed future-work input
```

All other paths remain in place. `index.md` remains the public router,
`project/raw/README.md` remains the raw router, and `project/README.md` remains
the numbered-work router. No `language/README.md` or new subdirectory is needed.

Current language owners do not link to this work item or to raw inputs. Raw
files may cite this work for provenance.

### Finding-to-owner map

| Aligned finding | Lasting owner | Local integration |
| --- | --- | --- |
| The motivating ambiguity of ordinary copy-looking source | New `language/transfer-stances.md` | `language/principles.md` links the general explicit-intent principle |
| `as <stance>` changes offered selection intent and performs no operation itself | `language/transfer-stances.md` | Exact form and precedence in operators, operator phrases, and operator catalog |
| Declaration stance, use-site offered stance, accepted fallback, and affected object | `language/transfer-stances.md` | Declaration syntax in declarations; binding consequences in invocation |
| Copy, deep, move, and last meanings | `language/transfer-stances.md` | Optional and identity owners teach their local source states |
| `copy`; `deep`; `move -> copy`; `last -> move -> copy` | `language/transfer-stances.md` | Invocation applies the partial-order comparison |
| Stance variants share one logical domain operation and destination outcome | `language/transfer-stances.md` | Construction and invocation diagnose incompatible reuse |
| Persistent declaration stance and one-use restatement | `language/transfer-stances.md` | `language/declarations-and-bindings.md` owns declaration behavior |
| Recommended copy/deep references and writable move/last references | `language/transfer-stances.md` | Qualifier viability in qualifiers; parameter binding in invocation |
| By-value copy, deep, move, and last behavior | `language/transfer-stances.md` | Invocation owns copy-in/deep construction and completion timing |
| Value/reference preference and ambiguity | `language/function-invocation.md` | Transfer owner teaches the programmer-facing distinction |
| Owned projection, explicit member override, and alias reset | `language/transfer-stances.md` | Qualifiers owns authority preservation |
| Receiver stance, exact receiver variants, unstanced fallback, and nested renewal | `language/transfer-stances.md` | Invocation owns receiver-slot selection; qualifiers owns receiver capability |
| Qualifier-complete concrete prototypes with no implicit erasure | `language/qualifiers.md` | Declarations owns omission defaults; transfer owner teaches why it matters |
| Explicit qualifier generics produce exact specializations | `project/raw/type-parameters-and-generics.md` | Reflection and `own` raw inputs retain their consequences |
| Legal but naturally unselectable declarations and demand-time ambiguity | New `project/raw/callable-selection.md` | Invocation retains only currently aligned partial-order behavior |
| Terminal opportunity is an intent error rather than silent automatic last | `language/transfer-stances.md` | Intent owner defines acknowledgement; invocation owns proof/application boundary |
| Moved-from and terminal source state and later destruction | `language/transfer-stances.md` | Construction owns lifecycle completion; qualifiers owns authority |
| Binding, exercise, and synchronous consumer completion are distinct | `language/function-invocation.md` | Transfer owner supplies the cross-consumer mental model |
| Protected optional move and last adapters | `language/optional-values.md` | Operator owners list exact protected forms |
| Optional last marks terminal immediately and cleans after complete consumer operation | `language/optional-values.md` | Invocation owns call completion |
| Optional `<<>>` availability | `language/optional-values.md` | Operator catalog owns the general swap form |
| Generated copy is a qualifier-complete exact family | `language/construction-and-destruction.md` | Qualifiers owns exact axes; invocation owns selection |
| Deep, move, and last are not generally generated | `language/construction-and-destruction.md` | Transfer owner teaches fallback consequences |
| Exact body/default/existing/bodyless/forbidden matching | `language/construction-and-destruction.md` | Reflection raw input retains declaration-state requirements |
| Generated assignment returns anonymous readonly copy access to `_` | `language/construction-and-destruction.md` | Operator catalog owns assignment association and exact form |
| Result stance before and after return; forwarding crosses declarations | `language/function-invocation.md` | Declarations owns result declaration and inference |
| Same-object behavior is operation-specific; interior aliases may end | `language/transfer-stances.md` | Construction owns replacement order; lifetime/safety raw inputs own future proof |
| Exactly-once resource disposition | `language/construction-and-destruction.md` | Transfer owner teaches the common obligation |
| Intent acknowledgement mechanism and category registry | New `language/intent-acknowledgements.md` | Source structure owns enclosure mechanics; domain owners name categories |
| `implicit-stance-at-terminal-use` and `terminal-source-reuse` | New intent owner | Transfer owner teaches the triggering cases |
| `asymmetric-saturating-magnitude` | New intent owner | Integer operator catalog teaches the operation |
| Transfer costs, diagnostics, and source stability | `language/transfer-stances.md` | Local owners show task-specific costs and failures |
| Programmer-first promotion order and example obligations | New transfer owner | Every edited current owner follows the local direct-entry requirement |

### Cold-reader teaching plan

The new transfer owner must not follow working-record order. Its opening
progression is:

1. show `a := b` and `c = d`;
2. explain why resource sharing, independence, ownership transfer, terminal
   reuse, and cost remain hidden;
3. state that `as <stance>` only changes consumer selection intent;
4. introduce copy, deep, move, and last one at a time through concrete use cases;
5. show how consumer declarations accept those stances;
6. contrast by-value copy-in with direct reference transfer;
7. introduce receiver stance from a declaration and one-use restatement;
8. explain source state and later destruction;
9. consolidate fallback and selection in tables;
10. briefly reteach qualifier promises before exact signature families;
11. layer optional adapters, generated families, aliases, costs, diagnostics, and
    advanced reference material afterward.

Each direct-entry local section explains why transfer matters to that reader's
task, shows representative source, and routes the complete rule to the transfer
owner. Links do not replace local teaching.

Promoted examples use 2-space indentation and meaningful names. They do not use
`operation` as a placeholder function name. Rejected operator families,
superseded automatic-last behavior, comparative-language mistakes, and other
discovery chronology remain only in this project record.

### Exact proposed promotion change set

The promotion is one coherent change set. Omitting one listed file would leave
duplicate authority, stale source, a broken reading path, or an orphaned
deferral.

#### New current owners and raw destination

1. **Create `language/transfer-stances.md`.**
   - Add current conceptual-design metadata and the cold-reader teaching sequence
     above.
   - Own stance motivation, source restatement, stance contracts, fallback,
     declaration and projection behavior, receiver stance, source post-state,
     terminal intent, common completion model, alias overview, costs,
     diagnostics, and source stability.
   - Route construction, invocation, qualification, optional, intent, and future
     lifetime details to their owners without citing this work item.

2. **Create `language/intent-acknowledgements.md`.**
   - Teach `intent<category>{...}` as acknowledgement of defined but suspicious
     source.
   - Distinguish intent from unsafe responsibility and lint suppression.
   - Own the searchable category registry:
     `implicit-stance-at-terminal-use`, `terminal-source-reuse`, and
     `asymmetric-saturating-magnitude`.
   - State that intent cannot make known-invalid source valid.
   - Leave formal grammar, diagnostic identifiers, and future category extension
     contracts as explicit boundaries.

3. **Create `project/raw/callable-selection.md`.**
   - Preserve legal-but-naturally-unselectable declarations, demand-time
     ambiguity, exact-prototype aliases, cross-axis comparison, inferred generic
     specialization, generated-versus-declared ambiguity, source compatibility,
     and future revision pressure.
   - State that it does not own `switch`/`case` or accepted current invocation
     behavior.
   - Include activation and retirement criteria.

#### Routers and reusable principles

4. **Update `index.md`.**
   - Add both new current owners to `Start here` and `Current conceptual design`.
   - Describe transfer as the route for copy/deep/move/last intent and source
     state.
   - Describe intent acknowledgements as the route for defined
     suspicious-looking source.
   - Keep project work and raw inputs out of the public language route.

5. **Update `project/raw/README.md`.**
   - Index callable selection.
   - Narrow bare-source pressure to keyword-neutral source.
   - Update generic, lifetime, callable-composition, `own`, reflection, safety,
     analysis, mutability-family, and async descriptions where transfer
     promotion changes their activation pressure.

6. **Update `language/principles.md`.**
   - Include deep in explicit transfer intent.
   - Link the concrete transfer model to the new owner.
   - Clarify that optimization may implement a selected transfer more cheaply but
     never infer another stance or user body silently.
   - Route defined suspicious source to the intent owner.

7. **Update `language/terms.md`.**
   - Replace the provisional transfer-stance definition with the accepted
     offered/accepted stance model.
   - Add or refine concise terms for moved-from state, terminal source state,
     receiver stance, and intent acknowledgement where cross-owner reuse
     requires them.
   - Update confusable-form intent error to use the accepted intent mechanism
     rather than a future escape.

#### Core local owners

8. **Update `language/declarations-and-bindings.md`.**
   - Replace the statement that move/copy cannot persist on declarations.
   - Teach declaration stance, omission as copy, initialization stance
     independence, result declaration stance, and the `:=` inference boundary.
   - Preserve qualifier-complete concrete prototypes and defer explicit
     qualifier generics.
   - Reconcile assignment-result examples with anonymous readonly copy access.

9. **Update `language/qualifiers.md`.**
   - Teach that transfer stance is one complete-value selection property, not a
     fourth per-layer qualifier axis.
   - Apply mutable/writable requirements to ordinary move/last references.
   - Preserve exact final/varying and mutable/immutable truth under readonly
     access.
   - Add receiver and alias authority boundaries, explicit renewal, and
     qualifier-family pressure.
   - Update move-out/destruction and generated replacement boundaries to the
     aligned moved-from and terminal model.

10. **Update `language/function-invocation.md`.**
    - Separate offered stance, accepted fallback, value/reference binding, and
      affected object.
    - Replace the blanket claim that every move/last effect occurs during
      immediate argument binding.
    - Add by-value copy/deep/move/last behavior, reference/value preference,
      receiver-slot comparison, demand-time ambiguity, results, forwarding,
      terminal opportunity, and synchronous completion.
    - Route complete transfer meaning and intent acknowledgement to their owners.

11. **Update `language/construction-and-destruction.md`.**
    - Define generated copy as a qualifier-complete exact family.
    - Add the copy-constructor and assignment schemas, compiler-anonymous
      parameters/results, returned `_`, and assignment association.
    - State that deep/move/last are not generally generated and use aligned
      fallback.
    - Apply exact shape matching to body/default/existing/bodyless/forbidden
      declarations.
    - Reconcile packet timing, custom interception, reconstructive replacement,
      source/old-destination disposition, self-aliasing, and destructor
      completion.

12. **Update `language/operators.md`.**
    - Add the generic stance-restatement forms as protected source behavior.
    - State that the form itself performs no transfer.
    - Distinguish them from protected optional move/last adapters.
    - Route exact forms and precedence to the catalog and complete semantics to
      the transfer owner.

13. **Update `language/operator-phrases.md`.**
    - Teach exact post-unary `as copy`, `as deep`, `as move`, and `as last`
      recognition alongside binary `source as DestinationType`.
    - Explain the complete-tree distinction without requiring a phrase fence.
    - Mark the forms reserved or protected as established by the catalog.

14. **Update `language/operator-catalog.md`.**
    - Add exact generic stance forms, fixity, precedence, receiver/result role,
      and protected/reserved status.
    - Preserve `as default` as a separately unsettled reserved form; do not infer
      a stance-reset contract not aligned here.
    - Update optional forms, assignment association/result, and
      `asymmetric-saturating-magnitude` to the intent acknowledgement mechanism.
    - Keep complete general swap mechanics as an explicit boundary while routing
      optional support locally.

15. **Update `language/optional-values.md`.**
    - Replace provisional general-transfer text with links to the transfer owner.
    - Teach optional-specific copy/deep/move/last state effects after accepted
      fallback.
    - Distinguish generic `optional as last` from protected `last optional`.
    - Add immediate terminal analysis, delayed physical cleanup at complete
      consumer completion, same timing for value/reference consumers, and
      terminal-source reuse diagnostics.
    - Replace blanket inward propagation with optional-layer interception and
      forwarding.
    - Add a valid optional `<<>>` example and state that optional values support
      the protected same-type swap form without inventing unresolved general swap
      rules.

16. **Update `language/source-structure.md`.**
    - Add the source-level `intent<category>{...}` enclosure boundary at the
      depth needed for tokenization, layout, one complete payload, and direct
      links to the intent owner.
    - Keep contextual keyword recognition general.
    - Retain `bare{...}` only as future keyword-neutral source.

17. **Update `language/integer-operator-catalog.md`.**
    - Replace `bare{ ||myValue| }` with
      `intent<asymmetric-saturating-magnitude>{ ||myValue| }`.
    - Preserve the natural-form intent error and route the category definition to
      the intent owner.

18. **Update `language/integer-literals.md`.**
    - Split the current optional transfer reference: route general stance and
      fallback to the transfer owner and optional wrapper effects to the optional
      owner.

19. **Update `language/identity-types.md`.**
    - Include deep and aligned fallback in by-value identity construction.
    - Route general transfer behavior to the transfer owner.
    - Preserve same-storage reference and ownership/lifetime questions as future
      `own` and lifetime work.

20. **Removed from the proposed promotion set: `language/endianness.md`.**
    - Its limited supported-operation list already says enough.
    - A generic transfer link there adds noise without helping an endian reader's
      immediate task.

#### Raw future-work inputs

21. **Update `project/raw/lifetimes.md`.**
    - Remove general transfer rules now promoted.
    - Retain lifetime-policy plurality, reference origin, place tracking,
      rebinding, pointer ownership/provenance, and alias pressure.
    - Extract the useful legacy `lease` and body-time terminal-capability evidence
      from `pointers.md`, with representative source marked as unresolved.
    - State how future lifetime work may extend but not contradict accepted
      transfer source state and synchronous completion.

22. **Update `project/raw/analysis-controls.md`.**
    - Distinguish accepted intent acknowledgement from unsafe proof assertions
      and lint suppression.
    - Retain replacement-alias, lifetime-escape, and opaque lifecycle proof
      categories.
    - Remove terminal-source reuse from unsafe responsibility when the operation
      is defined but suspicious.

23. **Update `project/raw/safety.md`.**
    - Preserve contract blame for provable transfer implementation violations.
    - Distinguish defined terminal-source reuse intent from unproved lifetime or
      alias safety.
    - Keep false unsafe lifetime/alias assertions and safe-subset guarantees as
      future work.

24. **Update `project/raw/bare-source.md`.**
    - Remove confusable-form acknowledgement from `bare{...}`.
    - Hand asymmetric saturating magnitude to the current intent owner.
    - Retain only keyword-neutral, tree-transparent enclosure pressure and its
      representative source.

25. **Update `project/raw/async.md`.**
    - Preserve the fact that current transfer completion is synchronous.
    - Add suspension, cancellation, partial transfer, and terminal cleanup
      pressure.
    - Preserve the legacy question of whether async by-value inputs require a
      deep or another explicit transfer contract before removing that diagnostic
      from legacy warning material.

26. **Update `project/raw/function-composition-and-chaining.md`.**
    - Add aligned by-value capture stance, reference-capture alias reset, explicit
      destructive renewal, repeated invocation, and forwarding pressure.

27. **Update `project/raw/type-parameters-and-generics.md`.**
    - Add explicit qualifier parameters, constraints, inferred qualifier
      arguments, dependent result qualification, exact demanded
      specializations, generated-family interaction, and diagnostics.
    - State that ordinary omission never creates qualifier genericity.

28. **Update `project/raw/owned-composition.md`.**
    - Require `own` to transform and expose finite exact qualified transfer
      prototypes or selected concrete generic specializations.
    - Prohibit an implicit unbounded runtime surface for an open generic family.

29. **Update `project/raw/reflection.md`.**
    - Distinguish generic declaration families, exact specializations, generated
      transfer declarations, explicit defaults, `existing`, bodyless shapes, and
      forbidden shapes.

30. **Update `project/raw/mutability-indexed-type-families.md`.**
    - Add generated copy self-shape pressure across concrete mutable/immutable
      variants.
    - Preserve the need to decide representation and operation compatibility
      rather than assuming one implementation.

#### Legacy disposition

31. **Update `functions.md`.**
    - Add a current transfer-owner route near the existing declaration,
      qualifier, and invocation routes.
    - Remove the legacy by-value move, automatic move propagation, explicit copy,
      and output-move sections after all continuing meaning is promoted or
      deferred.
    - Retain unrelated capture, composition, reassignment, `mutator`, `once`, and
      split/combine legacy input.

32. **Update `pointers.md`.**
    - Route general transfer to the current transfer owner.
    - Move useful `lease`, pointer/reference ownership, and body-time terminal
      capability evidence into raw lifetime input.
    - Remove the legacy automatic-last and `lease`/`last` transfer teaching once
      that extraction is complete.
    - Retain unrelated pointer grammar, representation, casting, ownership, and
      rebinding evidence.

33. **Update `ctor-dtor.md`.**
    - Route generated copy and stance-specific construction to current owners.
    - Remove the legacy default copy/deep/last constructor sections after the
      exact generated family, declaration states, and fallback behavior are
      promoted.
    - Retain unrelated allocation, global, and `once` evidence.

34. **Update `casting.md`.**
    - Route generic `as <stance>` to the transfer owner and remove duplicate
      legacy stance-restatement teaching.
    - Reduce `as default` transfer reset to the smallest clearly marked unresolved
      legacy concern with representative source, because its exact stance behavior
      remains outside this work.
    - Remove obsolete `shallow`/`lease` equivalence claims.

35. **Update `warnings-errors.md`.**
    - Route current transfer and intent diagnostics to their owners.
    - Remove superseded `copy-or-move`, `lease-or-last`, and explicit legacy
      transfer diagnostic claims.
    - Preserve the async-deep question in raw async input before removing the
      legacy registry entry.
    - Retain unrelated diagnostics evidence.

#### Active record

36. **Update `project/work/013-transfer-stances-and-source-state.md`.**
    - Record promotion dispositions and validation results.
    - Revise this PASS and exact set if promotion review exposes a material
      design, ownership, or integration issue.
    - Do not make current owners depend on this file.

### Files deliberately unchanged during promotion

- `README.md` remains the repository-level three-way router.
- `language/vision.md` remains the foundational direction owner.
- `project/README.md` remains unchanged until separately authorized closure and
  continuation work.
- Handoff and rehydration prompt sources remain unchanged.
- Archive files remain audit-only and unread.
- Unrelated legacy pages, current owners, and raw inputs remain untouched.

### Deferred material and constraints

| Deferred concern | Live destination | Constraint on promoted transfer design |
| --- | --- | --- |
| Detailed callable preference and generic-specialization comparison | New raw callable-selection input | Preserve partial-order dominance and loud ambiguity |
| Qualifier generic syntax and constraints | Raw generic input | Every selected specialization remains an exact prototype |
| Runtime/reflection identity of generic families | Raw reflection input | Do not erase concrete specialization identity |
| `own` promotion of generic behavior | Raw owned-composition input | Promote only finite exact surfaces |
| Mutable/immutable representation-indexed families | Raw mutability-family input | Generated copy must use the concrete self shape |
| Pointer ownership, `lease`, terminal capabilities, rebinding, and provenance | Raw lifetime input | Transfer stance does not invent pointee authority |
| Unsafe proof, replacement aliasing, and lifetime escape | Raw analysis-control and safety inputs | Intent acknowledgement never substitutes for unsafe proof |
| Lambda capture and generated callable types | Raw callable-composition input | Reference capture resets destructive default like an alias |
| Async suspension and cancellation | Raw async input | Current completion and cleanup rules are synchronous only |
| General `as default` transfer reset | Reduced legacy casting input and operator-catalog boundary | Do not infer reset semantics from `as copy` |
| Complete general swap mechanics | Operator-catalog boundary | Optional support may be taught without inventing wider behavior |
| Formal grammar, conformance, diagnostic identifiers, layout, ABI, and implementation | Future specification or implementation owners | Promotion remains conceptual and programmer-facing |

### Legacy-value audit

The promotion consumes legacy material by value:

- useful transfer motivation, by-value/reference distinctions, source
  restatement, result stance, generated constructor controls, and terminal
  resource reuse move into current owners;
- pointer-only `lease`, terminal capability, and provenance questions move to
  indexed raw lifetime input;
- async deep-input pressure moves to raw async input;
- unresolved `as default` reset remains as the smallest explicit legacy
  boundary;
- superseded automatic move/last selection, implicit destructive propagation,
  stale diagnostic names, `shallow` equivalence, and incompatible ownership
  claims retire;
- unrelated useful legacy material remains in its existing pages.

No current owner cites a legacy page for meaning after promotion.

### Promotion validation plan

After an authorized promotion:

1. Verify the public router reaches both new owners and no current owner links to
   numbered work or raw input.
2. Verify every raw file is indexed and every deferral names activation and
   retirement pressure.
3. Verify `as copy`, `as deep`, `as move`, and `as last` use one exact current
   source model and are never described as performing transfer themselves.
4. Verify generic `as last` and protected `last optional` remain distinct.
5. Verify optional terminal analysis is immediate and physical cleanup occurs at
   complete consumer completion.
6. Verify the stance fallback table agrees across transfer, invocation,
   construction, optional, operator, and terminology owners.
7. Verify concrete qualifier families never imply erasure or implicit generic
   forking.
8. Verify generated signatures, anonymous results, returned `_`, receiver
   requirements, and exact suppression agree across construction, qualifiers,
   declarations, invocation, and operators.
9. Verify every Zax example uses 2-space indentation, has meaningful names, and
   marks known errors inline.
10. Verify promoted teaching begins with motivation and valid source before
    reference tables and diagnostics.
11. Search current and legacy public paths for stale automatic-last,
    copy-or-move, lease-or-last, `bare{ ||...| }`, and obsolete
    `deep`/`move`/`last` constructor claims.
12. Verify `||value|` is escaped inside Markdown tables.
13. Verify links and heading anchors, render Markdown coherently, and run any
    existing documentation checks.
14. Verify only the authorized promotion files changed and preserve any staged
    review boundary.

### Original PASS rationale (superseded)

The original dry run passed because:

- the feature has one cohesive teaching owner;
- source syntax, invocation, construction, qualification, optional, intent, and
  terminology each have one noncompeting local owner;
- two small new current documents solve real independent teaching and reuse
  needs;
- one new raw file prevents callable selection from being misplaced under
  pattern selection;
- every other deferral already has an indexed live destination;
- the current flat `language/` and indexed `project/raw/` structure can absorb
  the material without a wider reorganization;
- all useful legacy transfer evidence can be promoted, explicitly deferred, or
  retired;
- and the cold-reader teaching sequence can present motivation before the
  qualifier-complete reference material.

A material issue found during promotion review changes this result to FAIL until
the dry run and exact change set are revised.

### Promotion-review findings

The first promotion attempt confirmed that complete ownership and complete
teaching are separate tests. The new transfer owner was written from the
programmer's motivating problem and read comparatively well. Several local
owner edits instead inserted compressed conclusions into existing reference
sections. They were technically related but did not establish enough meaning for
a direct-entry reader.

The aligned language design remains intact. The required revision is primarily
teaching, terminology, and local integration, with two additional legacy
consequences.

#### Why the transfer owner taught better

The transfer owner began with:

1. familiar source;
2. a concrete hidden-cost/source-state problem;
3. a plain-language explanation;
4. one stance and use case at a time;
5. exact terminology only after its consequence was understandable;
6. and consolidated tables and reference details later.

The local edits were driven instead by the finding ledger and existing headings.
That allowed working-record vocabulary such as "authority," "terminal shell,"
"compiler-anonymous," and "unavailable-best" to appear before a reader had a
reason to understand it.

The lesson is not that every owner must follow one rigid template. A fixed
template can interfere with a document whose reader task needs another order.
The reusable reader outcome is:

> A competent programmer entering the edited section can understand why the
> behavior matters, what representative source does, and what rule or cost
> follows before encountering dense reference terminology.

Useful techniques include an owner-specific teaching brief, a teaching pass
followed by a completeness pass, and a separate cold-reader audit. They remain
techniques chosen to achieve the outcome, not mandatory content slots.

An owner-specific teaching brief may record:

```text
Reader task
Opening valid example
Recognizable problem
Plain-language outcome
Vocabulary introduced after that outcome
Common path
Visible cost or failure
Later reference material
Foreign-owner handoff
```

The motivation for this brief is to prevent a complete finding ledger from
becoming the document outline. If another process produces clearer teaching
while preserving completeness, it remains valid.

#### Literal and terminology corrections

Promotion must:

- quote literal stance words as `` `copy` ``, `` `deep` ``, `` `move` ``, and
  `` `last` `` wherever the text names Zax syntax;
- leave them unquoted only when they are ordinary English concepts;
- reserve **receiver** for the implicit current-instance operand rather than an
  explicit parameter or result;
- prefer concrete wording such as "the consumer may take ownership from the
  caller's value" over unexplained "binds authority";
- introduce formal terms such as **incomparable**, **protected**, and
  **unavailable** only where their distinction matters and after plain-language
  meaning;
- keep compiler-anonymous parameter/result detail in the generated-prototype
  owner instead of repeating it in ordinary operator lookup;
- avoid "steal" in current teaching in favor of assume ownership, take
  ownership, transfer ownership, or reuse a source resource;
- and explain why each rule exists rather than stating only its mechanical
  result.

Private declarations outside their permitted context are ineligible before
preference. **Unavailable** in callable selection means an eligible visible
shape that cannot be invoked, such as a bodyless, forbidden, or unsupported
generated/default declaration. Private ineligibility must not produce an
unavailable-best error.

#### Qualifier teaching corrections

Promotion must distinguish the qualifier promises in plain language:

- `final`: the place continues to hold the same value lifetime; ordinary
  whole-value replacement is unavailable;
- `varying`: the place may end one value lifetime and receive another;
- `immutable`: the contents of the current value lifetime do not change through
  ordinary safe behavior;
- `mutable`: no immutable-content promise is made; an actual change still needs
  an eligible operation and writable path;
- `readonly`: this path does not perform a change;
- `writable`: this path may perform an otherwise permitted change.

The phrase "a final value does not change" is too easily confused with
immutability. Teaching should say that the same value lifetime remains in the
place.

For generated behavior:

- mutable self shapes may have ordinary generated in-lifetime assignment;
- immutable self shapes do not receive an ordinary mutating assignment;
- an immutable value in a varying place may participate in the compiler-owned
  reconstructive `=` skeleton, which constructs a new immutable value and ends
  the old lifetime;
- a final immutable place cannot be reconstructed;
- and independently represented mutable/immutable shapes may each receive
  appropriate generated constructors without pretending their assignment
  behavior is the same.

The construction owner should teach this positive split rather than say only
that a final receiver receives no generated assignment.

#### Stance-restatement corrections

Repeated complete restatement is legal:

```zax
result := source as move as copy
```

The post-unary phrases apply left to right; the final `as copy` controls the
stance offered to the consumer. The programmer changed intent before any
consumer accepted it.

A malformed combined phrase remains unavailable:

```zax
result := source as move copy // error
```

Generic `as copy`, `as deep`, `as move`, and `as last` are built-in post-unary
phrases at ordinary phrase precedence. Optional `move optionalValue` and
`last optionalValue` are ordinary pre-unary phrases at the same phrase level.
Optional behavior is type-specific, but its precedence is not exceptional.

#### Move and last teaching corrections

`move` permits a selected consumer to take ownership of source resources while
leaving a live moved-from value. It does not require resource transfer.

`last` offers one final opportunity for the selected consumer to assume
ownership of useful resources before the source's already-existing lifetime is
expected to end. Generic `last` does not schedule or cause that outer lifetime
to end.

A selected last consumer must leave enough valid source state for the original
destructor to run once without acting as owner of resources transferred
elsewhere. A resource whose ownership moved to the destination must not also be
released by the old source.

The motivating example should occur inside a visible scope:

```zax
postDatabaseCommand final : ()() = {
  command : DatabaseCommand
  command.preparePrivateDatabase()

  submit(command as last)
}
```

The reader can see that `command` reaches ordinary destruction at function exit
and understand why one last ownership-transfer opportunity is useful.

Use after accepted last is suspicious because resources that ordinary methods
expect may have moved elsewhere:

```zax
submit(command as last)
printStatistics(command) // error: may require transferred resources
```

The value still exists; the intent error asks the programmer to acknowledge a
defined terminal-state use rather than claiming all access is inherently
invalid.

A type representing one exclusive resource may prohibit copy construction and
copy assignment. Reference-shaped move and last remain meaningful because
ownership moves from one value to another without producing two owners.
By-value move may be unavailable when its preliminary copy-in cannot be formed.

#### By-value and reference teaching corrections

A by-value `move` parameter must be explained as a sequence:

1. construct an independent parameter from the caller's offered source;
2. preserve the caller's original according to that construction contract;
3. give the completed local parameter `move` declaration stance;
4. prefer moving resources while processing that private value rather than
   repeatedly copying them;
5. and destroy the remaining local state at call completion.

A reference-shaped `move` or `last` parameter refers directly to caller-owned
storage. It permits the selected function to take resources from that value.

Named reference uses return to `copy` because making every nested call inherit
destructive stance could transfer resources earlier or more often than the body
intended:

```zax
route final : ()(
  input : Message mutable writable & move
) = {
  log(input)                 // preserve input
  send(input.payload as move) // transfer deliberately here
}
```

If `log(input)` inherited `move`, it could select a move-aware logger and deplete
state before `send`. Explicit renewal identifies the actual transfer site.

#### Fallback teaching corrections

Fallback needs positive contrasting examples.

With copy only:

```zax
Archive :: type {
  +++ final : ()(
    source : Archive readonly & copy
  ) = {
    // ...
  }
}

first : Archive = source as move
// Copy is accepted as fallback; source remains unchanged.
```

After adding an exact move variant:

```zax
+++ final : ()(
  source : Archive mutable writable & move
) = {
  // ...
}

second : Archive = source as move
// Move is preferred; source becomes moved-from.
```

Terminal-opportunity analysis should not require decoration when it cannot
change selection or source state:

```zax
inspect final : ()(
  value : Record readonly & copy
) = {
}

inspect(record)
// No move/last path exists, so terminality does not change the outcome.
```

#### Declaration and result teaching corrections

An initializer **may** state a transfer stance. When omitted, `copy` still
exists as the declaration/source default.

A same-place alias needs a concrete example:

```zax
owner : Buffer move
alias : Buffer mutable writable & = owner

consume(alias)         // alias defaults to copy
consume(alias as move) // explicit move through this alias
```

The alias refers to the same place but has its own declaration stance.

Inference should be taught with source:

```zax
make final : (
  result : Buffer move
)() = {
  // ...
}

buffer := make()
```

`buffer` may infer `Buffer` and the produced move stance. Stance does not imply
reference or pointer shape; those shapes remain explicit or require future
dedicated inference rules.

Result teaching separates construction from later offering:

```zax
make final : (
  result : Message move
)() = {
  return source as copy
}
```

`source as copy` controls construction of the result slot. Once the result
exists, its declared `move` stance controls later uses inside the body and what
the caller receives.

Generated assignment prose should say "the assignment receiver `_`" or
"destination receiver," not "assigned receiver." Parameters, arguments,
results, and receiver operands remain distinct vocabulary.

#### Receiver teaching corrections

Receiver stance exists because a type-defined function has an implicit
current-instance input even when no explicit parameter carries the stance:

```zax
document : Document deep
document.publish()

(document as copy).publish()
```

The declaration or one-use restatement supplies the receiver stance, which
selects among receiver-qualified functions. Parameter and result stances occupy
separate callable slots.

The exact/fallback distinction needs positive source:

```zax
Document :: type {
  publish final : ()() deep = {
    // Exact deep receiver.
  }

  publish final : ()() = {
    // Receiver fallback; body uses copy stance.
  }
}
```

A deep receiver selects the exact deep version when present. Without it, the
unstanced receiver fallback may be selected and its body uses `copy`. This
receiver fallback is distinct from parameter/result fallback: a `deep`
parameter or result never falls back to `copy`.

#### Optional, operator, and source corrections

Ordinary optional teaching should say:

> When a consumer accepts `last optionalValue`, the optional immediately enters
> terminal source state for analysis. Its boxed state remains available to that
> consumer until the complete call or lifecycle operation finishes. The adapter
> then removes any remaining boxed value and leaves `optionalValue` absent.

The technical protected classification belongs where declaration conflicts or
operator ownership are taught, not in every ordinary explanation.

Operator lookup should state ordinary phrase precedence for `as <stance>` and
optional pre-unary phrases. Compiler-anonymous parameter/result details belong
only with generated prototypes in the construction owner.

The intent acknowledgement enclosure creates no scope:

```zax
intent<category>{
  value : Integer
}

value = 5
```

Its braces create no declaration scope, lifetime boundary, or final
expression-tree node.

The additional transfer sentence added to `language/endianness.md` is
unnecessary and must be removed. Endianness already lists its supported
operation surface.

### Teaching-debt lifecycle finding

Incremental concept promotion inevitably leaves some current `language/`
sections technically accurate but materially difficult to learn from. Trying to
perfect every touched page during every promotion would create uncontrolled
rewriting and premature polish. Ignoring the debt until a final audit is too
late.

Teaching debt is not evidence that an agent failed or that the documentation
process failed. It is a natural consequence of incremental cross-owner work:

- an agent understands the active concept most deeply and may know an adjacent
  owner's reader task less well;
- a local integration may be semantically necessary before the wider concept is
  familiar enough to teach naturally in that location;
- an explanation that was adequate under an earlier design may become poorly
  ordered after later concepts depend on it;
- several individually reasonable edits may accumulate terminology and
  prerequisites that no one edit made visibly difficult;
- and a reader entering from another task may expose friction that the original
  author, already familiar with the design, could not experience.

The purpose of teaching-debt capture is therefore not to assign blame or demand
perfect foresight. It gives the project a normal way to revisit current teaching
after enough context and concrete reader evidence exist to improve it
responsibly.

The aligned response separates three concerns:

1. `project/documentation.md` explains how an agent may recognize teaching debt
   while already reading current owners and how observations flow through
   promotion.
2. A new `project/teaching-debt.md` suggests how to perform a separately assigned,
   bounded teaching refresh.
3. A new `project/teaching-debt-log.md` preserves only concrete current-owner
   teaching problems that remain after promotion.

This is separate from the cross-cutting audit. An audit may discover a missed
teaching problem, but it remains a late correctness/consistency safety net rather
than the normal teaching-debt workflow.

#### What teaching debt is

Teaching debt is material reader friction in a current `language/` owner whose
semantics may be correct but whose explanation does not let a competent
programmer understand the behavior, motivation, cost, or consequence without
unnecessary reconstruction.

Representative evidence includes:

- a rule appears before the problem or use case that gives it meaning;
- unexplained terminology must be decoded before behavior is visible;
- a table or matrix replaces rather than consolidates a mental model;
- an example shows syntax without the programmer's intent, source effect, or
  material cost;
- a link substitutes for the local explanation a direct-entry reader needs;
- repeated maintainer questions expose the same unclear distinction;
- or a foundational, frequently routed section blocks understanding of later
  current owners.

Teaching debt is not:

- a semantic contradiction or unresolved design question;
- a stylistic preference or opportunity to make adequate prose prettier;
- one awkward sentence in an otherwise usable explanation;
- raw material, whose role is evidence preservation;
- legacy material, whose role is non-authoritative design input;
- archive material;
- or project-process prose outside `language/`.

Semantic contradictions remain correctness/design work. They cannot be deferred
as teaching debt.

#### Opportunistic discovery only

An ordinary concept agent does not search for teaching debt, scan `language/`
for it, or read the debt log to discover what it is expected to notice.

The agent may record a problem only when it experiences concrete reader friction
while following the required focused reading for its assigned concern. If the
material remains understandable under the recognition guidance, the agent does
not invent a debt observation merely because prose could be improved.

The debt log is not:

- ordinary onboarding;
- a checklist for concept work;
- a test answer key;
- a way to prime an agent to repeat earlier judgments;
- or permission to expand an active concern into documentation cleanup.

An optional active-work heading such as:

```markdown
### Teaching concerns noticed while reading
```

is created only when actual material friction was observed. No empty heading or
routine teaching review is required.

A useful provisional observation identifies:

- the current `language/` owner and section;
- the reader task that was blocked;
- the concrete wording, ordering, example, or missing consequence causing
  friction;
- why the problem is material rather than stylistic;
- and possible activation pressure for a later refresh.

That entry records firsthand evidence. It does not need to prescribe a solution.

#### Promotion disposition

Provisional observations remain in the active work record until promotion
because promotion may:

- rewrite the affected section naturally;
- clarify the design that made the wording confusing;
- consolidate several observations into one concern;
- remove the affected passage;
- or show that the observation was mistaken.

During promotion, every provisional observation is dispositioned:

1. **Resolved by promotion.** No debt-log entry is created.
2. **Superseded or mistaken.** The observation is discarded with its reason.
3. **Still valid in a current owner outside the authorized promotion.** Add it to
   the durable teaching-debt log.
4. **Still valid in a section materially changed by this promotion.** Treat it as
   a promotion blocker rather than knowingly publishing poor teaching and
   deferring it to the log.

Before the numbered work item is archived, no teaching observation may survive
only in that future historical record.

The promotion agent reads the debt log only as needed to update or deduplicate a
specific independently noticed concern. It does not browse the whole log before
reviewing current owners.

#### Durable teaching-debt log

`project/teaching-debt-log.md` is a live, non-authoritative queue of unresolved
observations after promotion. It owns no language meaning and does not explain
how to fix the debt.

The log:

- includes only current files under `language/`;
- groups entries primarily by owner path and section;
- preserves the reader task, observed friction, concrete evidence, importance,
  activation pressure, and provenance;
- may include a possible refresh direction when already clear;
- and exposes cross-owner patterns only after several concrete entries
  demonstrate them.

Broad entries such as "this document is difficult" are not actionable. A useful
entry resembles:

```markdown
## `language/function-invocation.md`

### Transfer-aware value and reference parameters

- **Reader task:** Determine whether a call copies an argument or may change
  caller-owned storage.
- **Observed friction:** The section introduces an internal capability term
  before explaining by-value construction versus reference binding.
- **Why it matters:** A reader can know the selection rule without knowing which
  object receives the source effect.
- **Activation pressure:** Refresh when invocation teaching is next revised or
  when related parameter/result/receiver entries form one bounded task.
```

Resolved entries are removed. Partially resolved entries are narrowed. The log
must not become a chronological archive of already fixed prose.

#### Teaching-debt refresh guidance

`project/teaching-debt.md` is read when the maintainer assigns a teaching refresh
or prepares a work item from selected log entries. It explains why debt
accumulates as a normal consequence of uneven familiarity and incremental
cross-owner editing, makes clear that its existence does not establish agent or
process failure, and suggests ways to improve a bounded cluster without semantic
drift.

Useful techniques include:

1. select a small cluster from concrete log evidence;
2. read only the affected current owners and prerequisites;
3. recover accepted meaning before rewriting presentation;
4. identify the programmer's reader task;
5. lead the common path with motivation, valid source, and plain outcome;
6. introduce precise vocabulary after the consequence is understandable;
7. retain costs, diagnostics, corner cases, and reference tables in later
   layers;
8. stop and return to design discussion if the rewrite exposes semantic
   uncertainty;
9. validate direct entry and foreign-owner handoffs;
10. resolve only the selected entries.

These are suggested techniques, not a rigid document template. They exist to
help achieve reader understanding without sacrificing completeness. Another
approach remains valid when it reaches that outcome more effectively.

The maintainer chooses the "worst of the worst" bounded cluster by considering
concrete repeated confusion, high-traffic owners, foundational concepts,
upcoming-work blockers, and patterns across several entries. Slightly awkward
but understandable prose is not enough reason for a refresh.

The assigned agent reads only the selected log entries or sections supplied by
the work item. It does not browse unrelated debt for additional work.

#### Current `013` teaching observations

Every teaching problem noticed during the current transfer promotion concerns a
file already being materially changed by this promotion:

- transfer-aware value/reference and result teaching in function invocation;
- generated copy and reconstructive-replacement teaching in construction;
- initialization, inference, result stance, and alias examples in declarations;
- receiver motivation, fallback, move/last rationale, and source-state teaching
  in the transfer owner;
- qualifier-promise wording needed by those transfer explanations;
- technical classification noise in operator lookup;
- and scope/lifetime explanation for intent acknowledgement.

These are promotion blockers under the revised FAIL result, not durable
teaching-debt-log entries. Corrected promotion must resolve them first. Only a
specific concern that remains afterward may enter the new log.

No unrelated current `language/` teaching problem has been observed with enough
concrete firsthand evidence during this work to justify another entry.

### Revised exact promotion-set delta

The original 36-file set is revised as follows:

- remove `language/endianness.md`;
- add `variadic.md`;
- add `concurrency.md`;
- add `project/documentation.md`;
- add `project/teaching-debt.md`;
- add `project/teaching-debt-log.md`;
- add a route in `project/README.md`;
- retain every other original file unless another promotion review finding
  changes the fit;
- and update this work record with the final reviewed set before PASS.

`variadic.md` must remove automatic-last forwarding claims and preserve
unresolved variadic-pack stance propagation under the current explicit stance
model.

`concurrency.md` must remove automatic-last result claims, route current transfer
semantics to the transfer owner, and preserve unresolved deep/async/thread
transfer behavior in `project/raw/async.md` without retaining stale current
teaching.

`project/documentation.md` must add concise teaching-debt recognition,
opportunistic-only discovery, promotion disposition, and archival handoff
without making the debt guide or log required onboarding.

`project/teaching-debt.md` must own the motivated suggested refresh techniques.
`project/teaching-debt-log.md` must own only the durable unresolved observation
queue and must prohibit proactive ordinary-agent reading. `project/README.md`
must route an explicitly assigned teaching refresh or maintainer log review
without adding either file to the normal concept-work reading path.

### Requirements for corrected promotion

Corrected promotion must:

1. Rework materially changed current sections from the reader's task rather than
   editing only for ledger coverage.
2. Apply the literal-stance backtick audit across every changed current and
   legacy file.
3. Replace unexplained internal vocabulary with concrete source consequences or
   introduce the term after its meaning.
4. Add the aligned motivating examples and reasons above.
5. Remove the unnecessary endianness edit.
6. Integrate variadic and concurrency consequences.
7. Recheck private ineligibility versus unavailable-best behavior.
8. Recheck ordinary destruction wording and optional cleanup as the explicit
   special adapter.
9. Repeat cold-reader review before completeness/reference review.
10. Add the teaching-debt guidance, refresh guide, empty or evidence-backed live
    log, and targeted project route.
11. Disposition every current `013` teaching observation as resolved,
    superseded, logged, or still blocking.
12. Update the exact file set and rerun all dry-run and promotion validation.

Failure to satisfy these checks revises this result back to FAIL before closure.

### Corrective dry-run re-evaluation

The promotion-review findings do not reopen the accepted transfer model. They
change how current owners teach it and expand the exact repository integration
needed to avoid stale public behavior and lost teaching observations.

The dry run now passes because:

- every semantic finding still has one current owner;
- every unresolved design consequence has one indexed raw destination;
- teaching-debt recognition, refresh guidance, and durable logging have distinct
  current project owners and lifecycles;
- ordinary agents are not primed by the debt log or asked to search for
  teaching problems;
- known teaching failures in materially changed files remain promotion work
  rather than deferred log entries;
- variadic and concurrency legacy contradictions have exact dispositions;
- the unnecessary endianness edit is removed;
- every terminology and teaching concern raised in promotion review has an
  actionable local correction;
- raw files receive only design-disposition updates, not a prose-quality sweep;
- and the final promotion set is exact.

### Final exact promotion file set

The corrected promotion contains exactly these 41 files:

#### Current language owners and public route

1. `index.md`
2. `language/transfer-stances.md` — new
3. `language/intent-acknowledgements.md` — new
4. `language/principles.md`
5. `language/terms.md`
6. `language/declarations-and-bindings.md`
7. `language/qualifiers.md`
8. `language/function-invocation.md`
9. `language/construction-and-destruction.md`
10. `language/operators.md`
11. `language/operator-phrases.md`
12. `language/operator-catalog.md`
13. `language/optional-values.md`
14. `language/source-structure.md`
15. `language/integer-operator-catalog.md`
16. `language/integer-literals.md`
17. `language/identity-types.md`

#### Project guidance and current work

18. `project/documentation.md`
19. `project/teaching-debt.md` — new
20. `project/teaching-debt-log.md` — new
21. `project/README.md`
22. `project/work/013-transfer-stances-and-source-state.md`

#### Indexed raw destinations

23. `project/raw/README.md`
24. `project/raw/callable-selection.md` — new
25. `project/raw/lifetimes.md`
26. `project/raw/analysis-controls.md`
27. `project/raw/safety.md`
28. `project/raw/bare-source.md`
29. `project/raw/async.md`
30. `project/raw/function-composition-and-chaining.md`
31. `project/raw/type-parameters-and-generics.md`
32. `project/raw/owned-composition.md`
33. `project/raw/reflection.md`
34. `project/raw/mutability-indexed-type-families.md`

#### Legacy disposition

35. `functions.md`
36. `pointers.md`
37. `ctor-dtor.md`
38. `casting.md`
39. `warnings-errors.md`
40. `variadic.md`
41. `concurrency.md`

`language/endianness.md` is explicitly unchanged. Its supported-operation list
already provides the local information an endian reader needs.

Repository and public routers not listed above remain unchanged. Handoff and
rehydration prompt sources remain unchanged because detailed documentation
practice continues to live in `project/documentation.md`.

### Corrected promotion teaching check

Before declaring the promotion complete, review materially changed current
sections in this order:

1. Confirm the direct-entry reader task and motivating source are visible.
2. Confirm the plain-language effect and reason appear before internal
   vocabulary.
3. Confirm literal stance words use backticks in prose.
4. Confirm examples explain why `as <stance>` only restates selection intent.
5. Confirm by-value/reference, receiver, result, fallback, and terminal examples
   show the affected object and source consequence.
6. Confirm `final`/`varying`, `mutable`/`immutable`, and
   `writable`/`readonly` promises are not conflated.
7. Confirm formal terms such as protected, incomparable, unavailable, and
   compiler-anonymous appear only where their distinction matters.
8. Confirm tables consolidate an already established model.
9. Confirm links hand off complete foreign rules without replacing local
   teaching.
10. Run the completeness, ownership, legacy, raw-destination, link, Markdown,
    and staged-boundary validation already listed above.

These checks describe reader outcomes and useful review order, not a mandatory
document template.

### Promotion application and validation — 2026-09-04

The separately authorized corrected promotion was applied across the final
41-file set.

#### Current owner results

- `language/transfer-stances.md` now teaches the motivating copy-looking problem,
  one stance per complete value, `as <stance>` as selection intent rather than an
  operation, each stance's use case, fallback, by-value/reference behavior,
  declaration and result stance, projection, receiver stance, terminal
  opportunity, optional handoff, qualifiers, generated behavior, costs,
  diagnostics, and source stability.
- `language/intent-acknowledgements.md` now owns the defined-but-suspicious source
  model, no-scope enclosure meaning, intent/unsafe/lint distinctions, and the
  initial category registry.
- Declarations, qualifiers, invocation, construction, operators, operator
  phrases/catalog, optionals, source structure, terms, integer, and identity
  owners now teach only their local transfer consequences and route the complete
  model to the transfer owner.
- Generated copy construction and assignment now use exact
  qualifier-complete schemas. Mutable assignment, immutable reconstructive
  replacement, final-place behavior, anonymous generated slots, returned `_`,
  and exact-shape suppression are distinguished.
- Optional values now distinguish generic stance restatement from the
  `last optionalValue` cleanup adapter and teach same-type `<<>>` swap.

#### Teaching-debt process results

- `project/documentation.md` now explains teaching debt, its blame-free cause,
  opportunistic-only recognition, promotion disposition, and archival handoff.
- `project/teaching-debt.md` now owns motivated suggested refresh techniques
  without imposing one rigid document template.
- `project/teaching-debt-log.md` now owns the targeted unresolved observation
  queue and explicitly prohibits ordinary-agent browsing or problem hunting.
- `project/README.md` routes only an assigned teaching refresh or maintainer log
  review.

Every concrete teaching problem noticed during `013` affected a section
materially changed by this promotion and was treated as a promotion blocker.
The corrected teaching pass resolved those observations, so the new log contains
no unresolved entries. No unrelated teaching concern was invented through a
proactive sweep.

#### Raw and legacy disposition results

- Callable selection now has one indexed raw destination for detailed
  cross-axis, generic-specialization, generated-candidate, and exact-prototype
  work.
- Lifetime, analysis-control, safety, async, callable-composition, generic,
  `own`, reflection, mutability-family, and bare-source inputs now retain only
  their unresolved consequences under the current transfer model.
- Raw files received disposition updates, not a teaching-quality sweep.
- Legacy function, pointer, constructor, casting, and diagnostic transfer
  sections were consumed by value and removed after their useful meaning moved
  to current or raw owners.
- `variadic.md` no longer teaches automatic `last`; it preserves unresolved pack
  transfer under the explicit stance model.
- `concurrency.md` no longer teaches automatic `deep` propagation or automatic
  `last` results; unresolved thread/suspension transfer is preserved in raw async
  input.
- `language/endianness.md` has no promotion change.

#### Validation result

**FAIL — previous validation passed mechanically, but newer aligned findings are
not yet promoted**

Validation confirmed:

- the net changed-file set is exactly the 41 files recorded above;
- `language/endianness.md` is absent from that set;
- every changed Markdown link and heading anchor resolves;
- every changed file has balanced code fences;
- new owner examples use 2-space indentation;
- literal `copy`, `deep`, `move`, and `last` stance names use backticks in
  promoted prose, with ordinary English verbs left unquoted;
- `||value|` is escaped inside Markdown tables;
- no current or public legacy path retains `copy-or-move`, `lease-or-last`,
  `bare{ ||...| }`, or semantic automatic-`last` behavior;
- generic post-unary stance forms and optional pre-unary adapters remain
  distinct;
- transfer fallback agrees across current owners;
- generated-copy anchors and teaching-debt routes resolve;
- the teaching-debt log contains no unresolved entry;
- and `git diff --check` passes.

This promotion remains conceptual language documentation. It creates no formal
specification, implementation, conformance claim, commit, or closure
authorization.

### Post-promotion result and temporary findings

The following findings were aligned after reviewing the promoted optional,
receiver, result, and teaching-debt material. They remain within the same
41-file promotion set but require another current-owner update and dry-run
review.

#### Optional phrase result remains optional

`last optionalValue` and `move optionalValue` preserve the complete optional
type. They do not cross the optional boundary or directly return the boxed
value.

Conceptually:

```zax
last optionalValue
```

presents the complete optional similarly to:

```zax
optionalValue as last
```

while additionally scheduling optional-specific cleanup after the complete
consumer.

Likewise:

```zax
move optionalValue
```

presents the complete optional under `move` stance without scheduling wrapper
absence.

The selected consumer therefore receives `MyValue?`, not `MyValue`. It may
observe absence and must prove presence before postfix access. An absent wrapper
performs no boxed transfer. A present wrapper may forward the accepted stance to
its payload under the optional contract.

This prevents phrase use from becoming implicit unsafe unwrapping. A consumer
requiring bare `MyValue` is not made viable merely by writing
`last optionalValue` or `move optionalValue`.

Optional values, operators, and the operator catalog must teach this result role
explicitly.

#### Repeated stance restatement is legal but normally redundant

```zax
result := source as move as copy
```

is legal. The phrases apply from the left and the final `as copy` is the stance
observed by the consumer.

No consumer observes the intermediate `move`, so it has no effect in ordinary
source. Teaching should retain the example to explain grouping while noting that
the intermediate restatement is normally pointless.

A malformed attempt to combine two stance words in one phrase remains invalid:

```zax
result := source as move copy // error
```

#### By-value move requires available copy-in

A by-value `move` parameter means:

1. use the offered source to select the by-value `move` contract;
2. perform `copy` construction to establish the independent parameter;
3. preserve the caller's original value;
4. apply `move` as the completed parameter's declaration stance;
5. prefer moving resources while processing that private value;
6. destroy the remaining local state at call completion.

For a type that prohibits copy because one resource may have only one owner:

```zax
SingleOwnedResource :: type {
  // Exact copy construction and assignment shapes are forbidden.
}
```

this by-value contract is unavailable:

```zax
process final : ()(
  input : SingleOwnedResource move
) = {
}

resource : SingleOwnedResource move
process(resource as move)
// error: the independent parameter needs unavailable copy construction
```

A reference-shaped `move` or `last` parameter can instead transfer ownership
directly from caller-owned storage. Exclusive ownership does not by itself
require disabling `move` or `last`.

#### Direct compiler-managed temporaries offer last

A directly constructed compiler-managed unnamed by-value temporary has a
structurally known end: its lifetime completes after its one complete consumer.
The compiler gives that temporary inherent `last` stance. The programmer does
not need to state `as last`.

Illustrative receiver use:

```zax
Source :: type {
  get final : (
    result : Payload copy
  )() copy = {
    // Preserve a named receiver and produce a copy-stanced result.
  }

  get final : (
    result : Payload last
  )() last = {
    // Reuse resources from a receiver whose temporary lifetime is ending.
  }
}

fromTemporary := (: Source).get()

source : Source
fromNamed := source.get()
```

The anonymous `(: Source)` receiver inherently offers `last` and may select the
`last` receiver variant. The named `source` uses its declaration stance,
implicitly `copy` here.

This is stable source-category behavior, not analysis-dependent upgrading of a
named value. A programmer may restate another stance:

```zax
fromTemporaryCopy := ((: Source) as copy).get()
```

The receiver stance matters because the selected `get` implementation can
choose how to produce its result. A terminal receiver variant may transfer
resources into a `last`- or `move`-stanced result, while a `copy` receiver
variant preserves the receiver and returns a `copy` result.

Future integration must test this structural temporary rule against direct
argument consumption, lifetime extension, captures, multiple results, and
optional cleanup without silently extending it to named liveness inference.

#### By-value result terminal opportunity is checked at mapping

A function result has the stance declared by the producer's prototype. Omission
continues to mean implicit `copy`; the caller does not silently rewrite that
public result contract to `last`.

The producer cannot know which downstream consumer will receive its result. The
caller-side result-mapping boundary can know that:

- one owned by-value result slot is temporary source storage;
- mapping into its one consumer is structurally final for that slot;
- and offering `last` could materially change which consumer is accepted.

For example:

```zax
consume final : ()(
  input : Payload readonly & copy
) = {
}

consume final : ()(
  input : Payload mutable writable & last
) = {
}

makePayload final : (
  result : Payload
)() = {
  // Implicit copy result stance.
}

consume(makePayload())
// intent error: this by-value result could select the last consumer,
// but the producer result left its stance implicit
```

The intent error applies when all of these hold:

1. the result is an owned by-value result slot;
2. its declaration stance is implicit;
3. this is its structurally final mapping boundary;
4. offering `last` could make another consumer viable or select a materially
   different contract;
5. accepted implementation or source outcome would differ.

The producer may declare the API-wide result stance:

```zax
makePayload final : (
  result : Payload last
)() = {
}
```

The caller may restate one use:

```zax
consume(makePayload() as last)
```

The producer may explicitly preserve copy:

```zax
makePayload final : (
  result : Payload copy
)() = {
}
```

Or the programmer may deliberately retain implicit behavior:

```zax
intent<implicit-stance-at-terminal-use>{
  consume(makePayload())
}
```

No result intent error is needed when:

- the result stance is explicit;
- no `move` or `last` consumer could be selected;
- `last` would accept the same `copy` consumer and produce the same source
  outcome;
- or the result is discarded without another transfer consumer.

Each slot of a multiple-result producer is considered independently.

#### Reference results are excluded

A reference result owns only an access path:

```zax
findCurrent final : (
  result : Payload readonly &
)() = {
}
```

Destruction of the reference result slot does not prove that the referred-to
`Payload` is terminal. The by-value result terminal-opportunity rule therefore
does not apply to reference results.

Pointer values and ownership-bearing handles require future pointer/lifetime
design. Transfer of a pointer value does not automatically make its pointee
terminal.

#### Why move and last are distinct

`move` must preserve the type's moved-from contract. `last` only needs to
preserve destruction and explicitly defined terminal-state behavior.

Many types use the same implementation:

- an empty buffer;
- a null handle;
- or zero elements

may already satisfy both moved-from and terminal requirements. In those cases a
separate `last` implementation has no benefit and `last` falls back to `move`.

A specialized `last` implementation matters when the weaker terminal
requirement permits more resource reuse:

- **Private database connection.** Every ordinarily live instance requires a
  connection. `move` retains or recreates one for the moved-from source; `last`
  may transfer it because only destruction remains.
- **Reusable backing allocation.** A moved-from buffer promises immediate reuse
  and retains capacity; `last` may transfer payload and backing allocation.
- **Registration or identity token.** A live moved-from service must remain
  registered; `last` may transfer or retire the registration.
- **Self-referential representation.** `move` may rebuild internal pointers so
  ordinary methods remain valid; `last` may avoid that repair cost.

A type may provide `move` and rely on fallback for `last` when moved-from state
is already minimal. It may provide a specialized `last` but no `move` when a
continuing moved-from invariant would be too expensive: `move` offers then fall
back to `copy`, while `last` can use terminal resource transfer.

Current transfer teaching must include brief use-case discussion of where the
distinction creates value and where it does not.

#### Teaching debt is residual risk, not an effort exemption

Teaching debt is unavoidable residual risk, not an acceptable output target.
The blame-free model means perfect foresight is impossible; it does not reduce
the effort expected in every current `language/` section materially changed by
an agent.

Agents still:

- make a conscientious reader-focused effort with the context available;
- explain the local reader task and motivation;
- prefer a concise honest handoff when adjacent behavior is not understood well
  enough to reteach;
- seek discussion rather than invent dense pseudo-precision;
- and use the debt log only for residual current-owner problems outside the
  authorized change or remaining despite a real effort.

Known poor teaching in a materially changed section remains a promotion blocker,
not a debt-log entry.

Project documentation and teaching-debt guidance must state this expectation
alongside the blame-free explanation.

### Integration status after these findings

The exact 41-file set remains sufficient. No new file is required.

Before PASS:

1. update optional, operator, and catalog result-role teaching;
2. mark repeated restatement as legal but normally redundant;
3. expand by-value `move` and copy-prohibited ownership teaching;
4. replace teaching-debt "live log" and "activation pressure" shorthand with
   explicit teaching-debt-log and revisit-trigger wording;
5. add the structural temporary `last` rule and receiver motivation;
6. add the by-value result-to-hole intent rule and reference exclusion;
7. add `move`/`last` distinction examples;
8. add the teaching-debt effort expectation;
9. rerun the cold-reader, semantic, exact-set, link, terminology, and legacy
   validation.

The preceding mechanical validation remains useful evidence but cannot restore
PASS until these semantic and teaching updates are applied.

### Result/temporary corrective dry-run re-evaluation

**PASS**

The latest findings fit the existing promotion architecture:

| Finding | Lasting owner | Required local integration |
| --- | --- | --- |
| Optional `move`/`last` phrase result remains the complete optional | `language/optional-values.md` | Operators and catalog state same optional result role |
| Repeated `as <stance>` is legal but normally redundant | `language/transfer-stances.md` | Operator catalog states left grouping and redundancy |
| By-value `move` needs available copy-in | `language/transfer-stances.md` | Invocation teaches binding failure for copy-prohibited types |
| Direct compiler-created by-value temporary inherently offers `last` | `language/transfer-stances.md` | Invocation applies structural temporary receiver/source selection |
| Omitted owned by-value result gets a terminal-opportunity intent check at mapping | `language/function-invocation.md` | Transfer, declarations, and intent owners teach producer/caller choices |
| Reference results are excluded | `language/function-invocation.md` | Lifetime raw input retains pointer/ownership extension pressure |
| Each multiple-result slot is considered independently | `language/function-invocation.md` | Declarations retains per-result stance |
| `move` versus `last` distinction and fallback rationale | `language/transfer-stances.md` | Construction and invocation link rather than duplicate |
| Teaching debt is residual risk, not an effort exemption | `project/teaching-debt.md` | Documentation architecture states the promotion blocker rule |
| Name the teaching-debt log and replace activation jargon | `project/documentation.md` | Teaching-debt log entry shape uses plain revisit wording |

#### Temporary and result model fit

The two temporary cases remain distinct:

1. A directly constructed compiler-managed unnamed by-value temporary has
   structurally known destruction after its complete consumer and therefore
   receives inherent `last` stance.
2. A function result exposes the stance declared by the producer's prototype.
   An omitted owned by-value result remains implicit `copy`; the downstream
   mapping boundary reports an intent error only when offering `last` could
   materially change the accepted consumer or source outcome.

This preserves:

- explicit API result contracts;
- no analysis-dependent upgrade of named values;
- stable terminal behavior for compiler-created anonymous storage;
- per-result-slot selection;
- and exclusion of reference results that do not own their referents.

Pointer values and owning handles remain future lifetime work without blocking
the by-value result rule.

#### Exact promotion set

The final promotion set remains the same 41 files recorded above. No new owner,
raw input, router, or legacy page is required.

`language/endianness.md` remains unchanged.

#### Required integration before promotion validation

Promotion must:

1. state that optional phrase results preserve `T?` and never implicitly unwrap;
2. mark repeated stance restatement as legal but normally pointless;
3. add the copy-prohibited by-value `move` failure example;
4. teach structurally temporary `last` receiver/source behavior;
5. add result-to-hole terminal intent, producer/caller remedies, reference
   exclusion, and per-result-slot behavior;
6. add concise `move`/`last` use cases and explain when fallback makes a separate
   `last` implementation unnecessary;
7. state that teaching debt does not excuse weak effort;
8. use the explicit teaching-debt-log name and plain revisit-trigger wording;
9. rerun exact-set, cold-reader, link, terminology, syntax, legacy, and staged
   boundary validation.

No material design, ownership, or structure question remains before that
authorized integration.

### Result/temporary promotion application and validation — 2026-09-04

The separately authorized latest promotion was applied within the unchanged
41-file set.

#### Applied current behavior

- Optional `last optionalValue` and `move optionalValue` now explicitly produce
  the complete optional type. Neither form crosses the optional boundary or
  exposes a bare boxed payload.
- Operators and the catalog use the same optional result role.
- Repeated `as <stance>` remains legal and left-grouped; current teaching now
  notes that an unobserved intermediate restatement is normally pointless.
- By-value `move` teaching now explains the required `copy` construction and why
  a copy-prohibited exclusive resource cannot bind that contract.
- A directly constructed compiler-managed unnamed by-value temporary now
  inherently offers `last`, with receiver examples distinguishing it from named
  declaration stance.
- Omitted owned by-value results remain implicit `copy`; a materially useful
  terminal opportunity is diagnosed at result mapping.
- Producer-declared stance, caller restatement, explicit `copy`, and intent
  acknowledgement are documented remedies.
- Reference results are excluded, multiple-result slots are independent, and
  pointer/pointee extension remains future lifetime work.
- Transfer teaching now explains when `move` and `last` share implementation,
  when terminal knowledge permits more resource reuse, and why fallback is
  useful.

#### Applied teaching-debt guidance

- Teaching debt is explicitly unavoidable residual risk rather than an
  acceptable output target.
- Agents remain expected to make a conscientious reader-focused effort in every
  current section they materially change.
- Known poor teaching in a materially changed section remains a promotion
  blocker.
- Documentation now names the teaching-debt log explicitly and describes
  concrete future circumstances for revisiting an entry rather than using
  unexplained activation jargon.

No unresolved teaching-debt entry remains after this promotion.

#### Validation result

**PASS**

Validation confirmed:

- the net promotion scope remains exactly the recorded 41 files;
- no endianness change exists;
- all changed Markdown links and anchors resolve;
- code fences are balanced;
- new-owner examples retain 2-space indentation;
- optional phrase result, direct temporary `last`, by-value result mapping,
  reference exclusion, and teaching-debt effort text are present in their
  owners;
- current transfer fallback, receiver behavior, and generated-family anchors
  remain consistent;
- stale automatic-transfer claims remain absent from current design;
- the variadic mention of old automatic `last` is explicitly historical and
  states that the behavior is superseded;
- and `git diff --check` passes.

The promotion remains conceptual documentation and does not authorize commit,
closure, archival, or creation of work item `014`.

### Cross-thread preparation pressure and teaching-effort clarification

Transfer review exposed one additional future concern without changing the
accepted four-stance model.

#### Candidate cross-thread preparation contract

A future consumer may need to produce a value whose lifetime and ownership
machinery can cross to another thread without depending unsafely on the
originating thread.

Illustrative future source:

```zax
worker.submit(source as cross thread)
// Illustrative future stance/contract name; not accepted syntax.
```

The intended guarantee is narrower than general thread safety:

- lifetime and ownership bookkeeping remains valid across the thread boundary;
- thread-local or executor-bound resources are adapted, replaced, or rejected;
- and the destination may move to another thread under its declared contract.

It does not itself guarantee synchronized access, absence of data races,
immutability, lock-free behavior, or permission to invoke every operation from
any thread.

Possible implementations include:

- ordinary `copy` when lifetime machinery is already cross-thread capable;
- atomic reference-counted sharing, which protects lifetime but not concurrent
  mutation;
- `deep` when independence is sufficient and no thread-affine resource remains;
- specialized rehoming, freezing, callback replacement, or resource cloning;
- accepted `last` transfer to avoid duplication when the source is terminal;
- or rejection when no valid representation exists.

Neither `copy`, `deep`, nor `last` is universally sufficient:

- ordinary copy may retain non-atomic or thread-local state;
- deep copy may preserve thread affinity;
- last may transfer a resource that is still invalid on another thread.

Any fallback must therefore be conditional:

```text
exact cross-thread preparation
-> compatible deep implementation known to satisfy the contract
-> compatible copy implementation known to satisfy the contract
-> otherwise unavailable
```

Future work must decide whether compatibility uses exact declaration,
`= existing`, reflection, a generic constraint, a type capability, or another
contract mechanism.

The concept may be:

- a fifth transfer stance;
- a destination/result capability;
- a generic constraint;
- a callable or capture contract;
- or a composition of existing transfer stance with another guarantee.

If it becomes a stance, it likely propagates through unstanced projections more
like `deep` than `move`, while permitting each member to use copying, deep
copying, atomic sharing, specialized preparation, or rejection.

This remains raw input. It does not add another accepted stance.

#### Disposition

- `project/raw/async.md` owns the candidate contract, example, conditional
  fallback, propagation, and stance-versus-capability questions.
- `project/raw/lifetimes.md` owns allocator, atomic lifetime, alias confinement,
  pointer/pointee, provenance, thread-affinity, and rehoming pressure.
- `project/raw/README.md` routes future cross-thread preparation work.
- `language/transfer-stances.md` mentions only the future boundary and does not
  teach another current stance.

The existing 41-file promotion set already contains every affected file.

#### Teaching effort clarification

Ordinary agents do not normally read the full teaching-debt guide. The
documentation architecture now states concisely that unavoidable teaching debt
does not permit weaker effort in unfamiliar adjacent areas.

Agents still make a conscientious reader-focused effort in every current
`language/` section they materially change. The debt process captures only
residual problems despite that effort or outside the authorized scope. Known
poor teaching in a materially changed section remains a promotion blocker.

#### Validation

**PASS**

- The candidate is preserved only in indexed raw owners.
- Current transfer documentation retains exactly four accepted stances.
- Cross-thread lifetime transferability is distinguished from synchronized
  access and data-race safety.
- `last` is recognized as a possible optimization rather than a sufficient
  cross-thread guarantee.
- Conditional reuse of `deep` or `copy` is explicit.
- The teaching-effort expectation appears in ordinary documentation guidance.
- The exact promotion set remains unchanged.
