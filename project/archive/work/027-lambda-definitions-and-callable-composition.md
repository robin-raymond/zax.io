# 027: Lambda definitions and callable composition

| Field | Value |
| --- | --- |
| Status | Historical working material / non-normative / audit-only |
| Work Item | `027` |
| Created | 2026-09-19 |
| Completed | 2026-09-20 |
| Owns | The bounded review defined by the fixed initiating input |
| Does Not Own | Complete generics, variadics, async execution, foreign calling conventions, compiler implementation, callable ABI, or runtime representation except where a concrete lambda/callable decision constrains them |

## Non-authority notice

This file is a collaborative working record. Existing statements, new
observations, candidate wording, and aligned findings remain non-authoritative
until a separately discussed, aligned, and explicitly authorized promotion
incorporates them into their lasting owners.

## Fixed initiating input

This section records the information aligned when work item `027` was created.
It is intentionally incomplete and must not be rewritten as work develops.

### Initiating concern

Establish a coherent programmer-facing model for lambda definitions and callable
composition. Current design distinguishes callable prototypes, fixed and varying
function storage, bound/unbound/`once` receiver behavior, captures, minted
implementations, unavailable function values, and composition candidates without
one reviewed owner teaching how a programmer defines, stores, transfers,
composes, resets, and invokes a lambda value.

The maintainer-supplied
[lambda maintainer notes](../raw/lambda-maintainer-notes.md) are the latest
primary input. Read and disposition their complete contents when this work item
is assigned. They may add to, replace, supersede, or reject older material.

The indexed
[function composition and chaining input](../raw/function-composition-and-chaining.md)
preserves capture, receiver application, generated callable, optional callable,
composition, chaining, reflection, and cost pressure not yet given lasting
owners.

### Motivating pressure

Several current owners already constrain the feature:

- function values have visible prototypes, fixed or varying storage, and
  callable-selection behavior;
- `bound`, `unbound`, and `once bound` declarations distinguish receiver slots
  and receiverless routes;
- a default or reset function value is unavailable and must disposition any
  owned capture representation;
- ordinary capture currently defaults to copying the captured value, while
  explicit reference capture borrows one fixed place;
- transfer stance, qualification, lifetime, and source state remain independent;
- a compatible visible prototype reuses one minted implementation without
  silently changing its body;
- contiguous `[[...]]` is reserved for lambda capture rather than compiler
  directives; and
- legacy composition and chaining syntax remains evidence rather than accepted
  design.

Without a cohesive model, lambda syntax can accidentally decide callable
identity, capture lifetime, transfer, receiver binding, composition, reset
cost, or generated implementation behavior in incompatible local ways.

### Known inclusions

- Lambda-definition syntax and the boundary among a declaration, expression,
  body, prototype, and callable value.
- Noncapturing and capturing lambdas.
- Generated callable identity and compatibility with visible prototypes.
- Capture discovery, explicit capture lists, naming, evaluation order, and
  construction.
- By-value and by-reference capture, including qualifications, transfer stance,
  lifetime, mutation, and escape.
- Repeated invocation after `move`/`last` use of captured state.
- Binding or applying an instance receiver to a bound prototype.
- Receiverless generated callables versus original bound, unbound, and `once`
  declarations.
- Function composition, chaining, result-to-input mapping, partial argument
  capture, and candidate source forms such as legacy `>>` and `|>`.
- Copying, moving, terminally transferring, assigning, resetting, and destroying
  callable values and their capture state.
- Callable presence, unavailable state, and interaction with Nothing behavior.
- Recursion, self-reference, and mutually dependent callable values where
  concrete evidence supports them.
- Invocation, overload selection, result routing, optional callable, safety,
  reflection, cost, diagnostics, and source-stability consequences.
- Complete disposition of maintainer notes, indexed raw input, and reached
  legacy/current evidence.

### Known starting boundaries

- Complete generic lambda syntax, constraints, specialization, and type erasure.
- Complete variadic callable behavior.
- Complete async suspension, cancellation, executors, and coroutine state.
- Foreign calling conventions, raw function-pointer ABI, dynamic linking, and
  interoperability representation.
- General operator redesign unrelated to the selected composition/chaining
  surface.
- Compiler data structures, lowering, closure layout, and optimization except
  where programmer-visible cost or behavior constrains them.

These boundaries do not erase consequences. Preserve every material pressure in
a current owner or indexed raw destination rather than leaving it only in the
eventual archive of this work item.

### Intentionally unresolved framing

- Whether every lambda produces one anonymous concrete type or may directly use
  another callable identity.
- Which lambda forms require an explicit prototype.
- Exact capture-list syntax and omission behavior.
- Whether capture defaults differ among values, references, receiver slots, and
  callable composition.
- Whether receiver application, partial argument capture, function composition,
  and chaining use one operation family or several deliberately distinct forms.
- How a generated callable exposes labels, defaults, results, stance, receiver
  origin, and availability.
- Which operations are generated for callable copy, move, `last`, reset, and
  destruction.
- How recursive and repeatedly invoked captures preserve source state.
- Which facts are conceptual language guarantees and which remain future
  representation, ABI, reflection, or implementation choices.

### Initial stopping guidance

Stop when the work has:

- established a teachable lambda/callable mental model and terminology;
- defined representative noncapturing, value-capturing, reference-capturing,
  receiver-binding, composition, chaining, reset, and invocation behavior;
- reconciled declarations, prototypes, capture construction, lifetime,
  qualification, transfer, invocation, result mapping, unavailable state,
  operators, source structure, safety, diagnostics, and costs;
- separated accepted callable behavior from future generic, variadic, async,
  FFI, ABI, and implementation mechanics;
- dispositioned all useful refreshed and reached legacy/raw material; and
- completed the required documentation-fit dry run.

Do not promote findings, archive this work item, begin work item `028`, or
redesign complete generic, variadic, async, FFI, ABI, reflection, or
implementation behavior without the separately required discussion, alignment,
and authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  decision maturity, promotion, deferral, and closure.
- [Lambda maintainer notes](../raw/lambda-maintainer-notes.md) - latest
  maintainer-supplied primary input; read and disposition it completely after
  assignment.
- [Function composition and chaining](../raw/function-composition-and-chaining.md) -
  preserves unresolved capture, receiver application, composition, chaining,
  optional callable, and reflection pressure.
- [Declarations and bindings](../../language/declarations-and-bindings.md) -
  owns function storage, prototypes, binding, initialization, aliases,
  receiver categories, and declaration-facing capture constraints.
- [Function invocation](../../language/function-invocation.md) - owns callable
  contracts, receiver and argument binding, results, selection, minted
  implementations, and call completion.
- [Lifetimes and references](../../language/lifetimes-and-references.md) - owns
  current capture defaults, fixed reference binding, escape, and callable
  capture life paths.
- [Transfer stances](../../language/transfer-stances.md) - owns `copy`, `deep`,
  `move`, `last`, receiver stance, projection, and repeated-use source state.
- [Nothing instances](../../language/nothing-instances.md) - owns unavailable
  function values, presence, reset, capture disposition pressure, and
  receiverless `once` behavior.
- [Construction, replacement, and destruction](../../language/construction-and-destruction.md) -
  owns callable/capture construction, reset lifecycle, destruction, and
  exactly-once resource disposition.
- [Source structure](../../language/source-structure.md) - owns `[[...]]`
  capture delimiters, declaration/expression boundaries, and physical source
  presentation.
- [Operators](../../language/operators.md) and the
  [operator catalog](../../language/operator-catalog.md) - own recognized
  composition/chaining source forms, receiver discovery, selection, precedence,
  and reservation.
- [Language-design terms](../../language/terms.md) - owns cross-cutting callable,
  capture, receiver, lifetime, and source-state vocabulary.
- Legacy function material - principal reached legacy callable, reassignment,
  `once`, capture, composition, and mutator evidence consumed and retired during
  promotion after disposition by value.

### Consequence-driven

- [Qualifiers](../../language/qualifiers.md) when capture mutability, access,
  replacement, or receiver qualification becomes concrete.
- [Safety and analysis](../../language/safety-and-analysis.md) and
  [raw analysis controls](../raw/analysis-controls.md) when capture escape,
  repeated destructive use, unavailable invocation, or unsafe callable
  assertions become concrete.
- [Raw callable selection](../raw/callable-selection.md) when generated callable
  preference, exact prototypes, pre/postconditions, receiver origins, or route
  preservation becomes concrete.
- [Optional values](../../language/optional-values.md) when optional callable
  construction, reset, nesting, or combinators become concrete.
- [Raw type parameters and generics](../raw/type-parameters-and-generics.md) when
  anonymous callable identity, generic lambda parameters, specialization, or
  type erasure becomes concrete.
- [Raw reflection](../raw/reflection.md) when callable identity, capture,
  generated declaration, source, or runtime metadata becomes concrete.
- [Raw asynchronous execution](../raw/async.md) when suspension would extend a
  capture, argument, result, receiver, or callable lifetime.
- Relevant legacy lambda, scope, operator, or variadic material only when a
  concrete question cannot be resolved from the primary notes, required
  callable input, current owners, and focused raw destinations.

### Audit-only

- Archived work only when a concrete provenance or regression question cannot
  be answered from current owners and live raw input.

## Initial working posture

Begin with programmer-visible examples of:

1. a noncapturing lambda;
2. a lambda capturing one value;
3. a lambda explicitly borrowing one reference;
4. a bound receiver converted into a receiverless callable;
5. two callables composed or chained; and
6. a varying callable reset to its unavailable state.

State what is constructed, owned, borrowed, callable, transferable, and
destroyed in each example before proposing one general lambda syntax or
composition operator family.

Use the maintainer notes as the latest primary evidence. Preserve current owner
constraints and treat legacy material as input rather than authority.

## Working record

### Review status

The findings under **Aligned candidate model** have been discussed and aligned
for this work item. They remain non-authoritative until separately authorized
promotion updates their lasting owners.

No known conceptual blocker remains in the core lambda/callable model. Remaining
work is focused integration, exact source placement where noted, deferred-owner
capture, wider consistency review, and the documentation-fit dry run.

### Aligned candidate model

#### Lambda expression and capture delimiters

A lambda expression contains a capture list, an optional complete callable
prototype, and a body:

```zax
doubleLater := [[]] (
  result : Integer
)(
  input : Integer
) {
  return input * 2
}

doubled := doubleLater(21)
```

`doubleLater :=` is the one declaration and initialization. The capture,
prototype, and body form its initializer expression; the lambda has no internal
declaration colon or initializer assignment.

Capture presentation expresses intent:

```zax
[[]]                 // Explicitly zero captures.
[[ value ]]          // One capture.
[[ value, other ]]   // Several captures.

[[
  value,
  other
]]
```

- `[[]]` is the canonical zero-capture form.
- A nonempty single-line capture has required interior spaces.
- A multiline capture uses the enclosing newlines and indentation.
- Compact nonempty capture source without interior separation is invalid.
- `[ [` and `] ]` remain nested-array presentation rather than capture.

The corresponding array distinction remains cardinal:

```zax
emptyMatrix : Integer[..][..] = []
oneEmptyRow : Integer[..][..] = [ [] ]
```

The first array has zero outer elements. The second has one outer element whose
inner array is empty. Compact `[]` is the explicit zero-entry array form; an
extra array layer cannot be added merely to emphasize emptiness.

Promotion fixes legacy/current malformed lambda examples directly. It does not
teach superseded lambda declaration syntax as a negative alternative.

#### Value capture

Ordinary capture copies the source into the generated lambda receiver:

```zax
factor := 3

scale := [[ factor ]] (
  result : Integer
)(
  input : Integer
) {
  return input * factor
}

factor = 4
scaled := scale(5) // 15
```

`factor` is captured when `scale` is constructed. Invocation does not recapture
it.

The overall lifecycle is:

```text
construct capture receiver and callable
-> invoke zero or more times
-> transfer, replace, reset, or destroy the callable
-> disposition the capture receiver exactly once
```

#### Renamed and typed capture

Capture mapping reads from source to destination:

```zax
sourceCount : Integer = 42

report := [[ sourceCount: capturedCount: ]] ()() {
  print(capturedCount)
}
```

A complete destination declaration performs direct capture construction:

```zax
report := [[ sourceCount: capturedCount : MyCount ]] ()() {
  print(capturedCount)
}
```

`MyCount` must be constructible from the offered `Integer`. Construction happens
when `report` is created and retains ordinary constructor, allocation, panic, and
destruction costs.

Leaving the destination name empty retains the source name:

```zax
report := [[ sourceCount: : MyCount ]] ()() {
  print(sourceCount)
}
```

Exact omission formatting belongs to final source-structure integration; the
source-to-destination meaning is aligned.

#### Explicit reference capture

```zax
total := 0

accumulate := [[ total: capturedTotal : & ]] ()(
  delta : Integer
) {
  capturedTotal += delta
}

accumulate(5)
print(total) // 5
```

The capture constructs a reference bound to `total`'s fixed place. It does not
copy or own `total`, and it does not extend `total`'s lifetime.

Ordinary capture of a reference name still copies its referent. Explicit
reference capture is required to retain aliasing.

A reference capture:

- never rebinds;
- preserves available qualification and declaration-side replacement
  permission;
- does not inherit `unsafe pliable`;
- does not silently inherit a source declaration's `move` or `last` stance; and
- requires its target to remain valid through every invocation.

A known escape beyond the target life path is an error. A future narrow unsafe
assertion may state an opaque but valid lifetime contract; unsafe source cannot
extend or revive a lifetime.

#### Capture source stance and capture declaration stance

A source expression may restate one transfer stance before its capture mapping:

```zax
callback := [[ value as last: myValue ]] ()() bound strong {
  use(myValue)
}

movingCallback := [[ value as move ]] ()() bound strong {
  use(value)
}
```

The source stance controls construction of the capture. It is a one-use offer at
lambda construction and never causes later recapture.

The completed destination declaration may independently state its ordinary
future stance:

```zax
callback := [[
  value as last: myValue : MyType move
]] ()() bound strong {
  consume(myValue)
}
```

Here:

- `value as last` controls transfer from the outer source into the capture; and
- `myValue : MyType move` controls ordinary later offers from the completed
  capture declaration.

Reference capture remains distinct because binding a reference does not itself
transfer the referent value. Source-relative `anchor` may likewise appear in a
capture source expression under its ordinary structural rules.

#### Capturing producer results

```zax
report := [[
  count: capturedCount:,
  text: capturedText: = makeReportInputs()
]] ()() {
  print(capturedCount, capturedText)
}
```

`makeReportInputs()` executes once during lambda construction. Its results map
into capture declarations using the ordinary source-to-destination direction.

Capture expressions and producer groups evaluate strictly in source order and
bind immediately. Each completed capture is available to later capture
construction. The body becomes callable only after the complete generated
receiver is valid.

#### Four independent callable axes

Callable design answers four separate questions:

1. The explicit call prototype defines results, inputs, labels, defaults,
   qualifications, and transfer stances.
2. `bound` means the implementation has a receiver available through `_`;
   `unbound` means it does not.
3. `final` fixes one implementation; `varying` supplies replaceable callable
   storage.
4. A bound storage profile may additionally support borrowed, unique, strong,
   atomic strong, or weak receiver lifetime relationships.

Receiver-lifetime qualifiers require receiver-capable `bound` storage. There is
no `unbound unique`, `unbound strong`, or `unbound weak`: an unbound
implementation has no receiver lifetime to retain.

#### Fixed and varying declarations

```zax
MyType :: type {
  value : Integer

  implicitBound final : ()() = {
    ++_.value
  }

  explicitBound final : ()() bound = {
    ++_.value
  }

  receiverless final : ()() unbound = {
    // `_` is unavailable.
  }

  typeAndInstance final once : ()() bound = {
    if ?_
      ++_.value
  }

  replaceableReceiverless : ()() unbound = {
    // May be replaced by a compatible unbound implementation.
  }

  replaceableBound : ()() bound = {
    ++_.value
    // May be replaced by a compatible implementation and receiver.
  }

  retainingBound : ()() bound strong = {
    ++_.value
    // May retain strong receiver ownership when the installed target uses it.
  }
}
```

`implicitBound` and `explicitBound` are both declared `final`. They have one
fixed implementation and no replaceable per-instance function slot.

`receiverless` is owned and qualified by `MyType`, but type ownership does not
create a receiver.

`typeAndInstance` supports an instance call and the receiverless type call using
the type's Nothing instance. `once final unbound` remains invalid because
`final unbound` already has one fixed receiverless implementation and `once`
adds no route.

`replaceableReceiverless` stores an implementation without a receiver.

`replaceableBound` stores an implementation and, when needed, its receiver.
Its initial implementation uses the containing `MyType` instance, but a
replacement may use another receiver type:

```zax
left : MyType
right : OtherType

left.replaceableBound = right.otherFunc
left.replaceableBound()
// `_` inside OtherType.otherFunc is right, not left.
```

The containing `left` finds the slot; it does not replace the installed
receiver. The receiver's concrete type belongs to the installed minted
implementation and is erased from the compatible visible call prototype.

A lifetime-capable slot may still contain an unbound implementation or an
ordinary borrowed bound implementation. Capacity does not force every installed
target to use ownership:

- `unbound` storage accepts unbound targets;
- plain `bound` storage accepts unbound or borrowed-bound targets;
- `bound unique` additionally accepts uniquely owned receivers;
- `bound strong` and `bound strong atomic` additionally accept the corresponding
  shared receivers; and
- `bound weak` and `bound weak atomic` additionally accept weak observations.

Installing a strong target into weak storage establishes a weak observation; it
does not retain strong ownership. Installing an unbound or borrowed target does
not pretend that target became weak.

#### `final` and `varying` affect callable type and storage

For new direct storage, declaration-side `final` resolves an omitted type-side
place stance to final:

```zax
fixed final : ()() bound = implementation
replaceable : ()() bound = implementation
```

The resolved storage profiles are respectively `bound final` and
`bound varying`. This is a semantic type distinction:

- the final declaration has one fixed implementation;
- the varying declaration has a replaceable slot; and
- `type of` retains the distinction.

Exact byte layout is target and representation work, but source cannot assume
equal size or replacement behavior.

The broader qualifier rule still permits a declaration-side `final` alias to
restrict access to an underlying varying slot. That alias case does not alter
the direct-storage resolution above.

#### Lambda receivers and storage forms

A capturing lambda constructs an anonymous receiver containing its captures.
The lambda body is minted against that receiver, and captured declarations are
available as ordinary body names.

```text
construct captures
-> construct anonymous receiver
-> pair implementation with receiver
-> expose the callable under its visible prototype
```

The anonymous receiver identity belongs to the lambda definition and concrete
specialization. Equal capture layouts do not merge different lambda bodies.

Representative storage forms:

```zax
plain := [[]] ()() unbound {
  performWork()
}

recursive := [[]] ()(
  value : Integer
) bound {
  if value > 0
    _.(value - 1)
}

localCapture := [[ value ]] ()() {
  use(value)
}

borrowed : ()() bound = localCapture

uniqueDefault := [[ value ]] ()() bound unique {
  use(value)
}

uniqueInArena := [[ value ]] ()() bound unique @{ myArena } {
  use(value)
}

shared := [[ value ]] ()() bound strong {
  use(value)
}

atomicShared := [[ value ]] ()() bound strong atomic @{ myArena } {
  use(value)
}

observer : ()() bound weak = shared
```

- A noncapturing lambda may be unbound.
- A noncapturing lambda may deliberately be bound when it needs its self receiver,
  including recursion through `_.(...)`.
- Capturing implies a generated bound receiver.
- Plain bound erasure borrows `localCapture` and must not outlive it.
- Unique/strong forms continue the receiver under their ownership relationship.
- A weak callable derives from applicable shared storage rather than directly
  owning a new lambda.
- `atomic` changes ownership accounting, not thread safety of the receiver.
- A custom arena applies only when receiver storage is allocated. Exact policy
  enclosure placement must remain consistent with general allocation syntax.

#### Complete prototype inheritance

Prototype inheritance is all-or-nothing:

```zax
callback := [[ value ]] ()() bound strong {
  use(value)
}
```

or:

```zax
MyCallable :: alias type ()() bound strong

callback : MyCallable = [[ value ]] {
  use(value)
}
```

An explicit callable destination can supply the complete results, inputs,
labels, defaults, qualifications, `bound`/`unbound` category, and lifetime
capacity. Source does not omit only selected pieces of that contract.

An inferred declaration writes the complete prototype. A `>>` target may supply
the complete prototype when selection is unambiguous. A nondefault arena remains
expression-specific even when the destination supplies the callable type:

```zax
callback : MyCallable = [[ value ]] @{ myArena } {
  use(value)
}
```

#### Presence is not weak liveness

Recognized `?` and `!` inspect immediate presence or vacancy:

- a raw pointer is present when an address is stored;
- a unique or strong owner is present when ownership is stored;
- a weak owner is present when a weak relationship is stored, even after strong
  ownership closes;
- a callable is present when an implementation is assigned;
- an `OpaqueOwner` is present when an ownership relationship is stored; and
- an optional remains present only when its boxed value exists.

This gives weak pointers, weak opaque owners, and weak callables the same
presence meaning. Presence does not promise that weak promotion can succeed.

The protected `liveness probe` operation returns exactly `Boolean` and never
acquires ownership:

```zax
assigned := ?callback
liveNow := liveness probe callback
```

For callable targets:

```text
unavailable          -> false
unbound              -> true
bound borrowed       -> true
bound unique         -> true
bound strong         -> true
bound strong atomic  -> true
bound weak           -> strong ownership open at this instant
bound weak atomic    -> strong ownership open at this instant
```

For a weak pointer or `OpaqueOwner weak`, it probes whether strong ownership is
open at that instant. The result may become stale immediately and grants no
lifetime proof. Actual weak-to-strong construction is the operation that pins
the allocation.

#### Installed callable binding kind

One query reports the active target mode rather than reserving a predicate for
each case:

```zax
kind := binding kind of callback
```

The conceptual result enum is:

```zax
Callables.BindingKind :: enum {
  Unavailable
  Unbound
  Bound
  BoundUnique
  BoundStrong
  BoundStrongAtomic
  BoundWeak
  BoundWeakAtomic
}
```

`Bound` means a receiver is installed without unique/strong/weak ownership.
The query reports the installed target, not merely the maximum capacity of the
slot's static type.

`Callables` is a candidate namespace, not an accepted library placement. The
enum must remain publicly nameable, but its canonical namespace and any root
alias belong to the deferred language/library-surface review described below.

#### Weak callable invocation

`bound weak` is an explicit conditional-call mode and permits only zero-result
prototypes.

Invocation performs ordinary caller-side setup first, including explicit
argument evaluation, defaults, parameter construction, and temporary lifetime.
It then:

1. invokes an installed unbound or borrowed target normally;
2. attempts temporary strong promotion for an installed weak target;
3. pins and invokes the weak receiver on success; or
4. performs no body work when the slot is unavailable or promotion fails.

It cannot synthesize results, which is why resultful weak callable prototypes
are unavailable.

The static `bound weak` mode guarantees that absence is a no-op rather than a
panic, so programmers do not need to prefill or guard a callback merely to make
invocation safe. Caller-side setup cost remains visible even when no body runs.
`binding kind of` distinguishes the installed mode; `liveness probe` remains a
total Boolean across all modes. Resetting a weak-installed slot releases the
weak observation.

#### Bound member capture needs no receiver reattachment

A member expression already identifies its receiver:

```zax
counter : Counter

readCounter := [[]] >> counter.read
current := readCounter()
```

`counter.read` is the bound callable captured by the composition. `[[]]` says
that no additional values or ordinary inputs are captured. The target supplies
the prototype.

The legacy model in which a bound prototype existed as a runtime callable with
an unfilled receiver, then used a lambda as the receiver-rebinding point, is
retired. The new bound callable storage model addresses its motivating use:

- a qualified declaration path may still support lookup and exact selection;
- `type of` may describe callable type information without evaluating an
  instance;
- `counter.read` produces the actual bound callable value; and
- an `unbound` implementation cannot acquire `_` after minting.

No receiver-reattachment source form remains unless a future concrete use case
reopens the concern.

#### Partial argument capture

`>>` binds selected ordinary inputs without invoking the target:

```zax
printPair final : ()(
  number : Integer,
  text : String
) unbound = {
  print(number, text)
}

number := 42

printNumberWith := [[ number: number: ]] ()(
  remainingText : String
) >> printPair

printNumberWith("apples")
```

Construction captures `number` once and binds it to `printPair.number`. The
written prototype presents the remaining input as `remainingText`.

When no relabeling or other visible-contract change is needed, the target
supplies the complete remaining prototype:

```zax
printNumberWith := [[ number: number: ]] >> printPair
```

Target defaults remain delayed until invocation unless composition explicitly
captures a value now. Capturing and omission are observably different timing
choices.

#### Direct composition

```zax
parse final : (
  result : Parsed
)(
  source : String
) unbound = {
}

render final : (
  result : String
)(
  parsed : Parsed
) unbound = {
}

parseThenRender := parse >> render
text := parseThenRender(sourceText)
```

Construction invokes neither function. Each later invocation calls `parse`,
maps its result positionally into `render`, calls `render`, and returns the final
result. Several results remain several result slots; composition creates no
implicit tuple.

#### Reshape composition mapping

`reshape` explicitly remaps producer results into consumer inputs without a
runtime reshape stage or intermediate aggregate:

```zax
parse final : (
  tree : Parsed,
  notes : Diagnostics
)(
  source : String
) unbound = {
}

render final : (
  text : String
)(
  document : Parsed,
  diagnostics : Diagnostics
) unbound = {
}

ParseToRender :: reshape {
  tree: document:
  notes: diagnostics:
}

parseThenRender := parse >> ParseToRender >> render
```

The complete three-part sequence is one recognized composition operation:

1. Apply explicit reshape entries and consume their source/destination slots.
2. Match remaining exact equal labels.
3. Map remaining slots positionally in declaration order.
4. Apply destination defaults and source-result omission policy.
5. Never backtrack.

An equal-label pair that is type-incompatible reports that mismatch rather than
silently falling through to positional remapping. An explicit reshape entry can
state the intended alternative.

Nested reshape paths may later decompose one structured result only when
ordinary transformation, transfer, and lifetime rules make every path valid.

#### Immediate chaining

`|>` performs calls now rather than constructing a retained callable:

```zax
result := 5 |> double() |> square() |> half()
```

It evaluates the carried source once, maps it to the next call, completes that
call before advancing, and exposes the final result shape.

Because arbitrary left value types cannot all own one universal chaining
operator, `|>` is language-recognized chaining behavior. Named remapping,
several-result stages, defaults, and zero-result stages use the same explicit
routing discipline as composition.

#### Callable transfer never recaptures

Capture expressions run exactly once when the capture receiver is constructed.
Later callable transfer changes the implementation/receiver relationship; it
never reevaluates or reconstructs captures.

- `copy` copies the callable receiver/lifetime relationship.
- Copying borrowed storage creates another borrow of the same receiver.
- Copying strong storage adds participation in the same receiver lifetime.
- Copying weak storage adds another weak observation.
- Unique ownership cannot be copied.
- `move` and `last` transfer applicable receiver/lifetime state and leave the
  source callable initialized but unavailable, as though reset had established
  its final state without releasing the relationship that moved away.
- `deep` callable transfer is undefined.

Capture storage remains in its established receiver place. A storage transition
may relocate or adopt the receiver only through an operation whose contract
preserves lifetime, aliases, and exactly-once destruction.

#### Destructive capture use and repeated invocation

The callable remains alive after an invocation. An accepted `move` leaves the
capture live in moved-from state; an accepted `last` leaves it live in terminal
source state.

Static analysis diagnoses later operations that are invalid for those states.
The programmer is responsible for writing a repeatedly invoked body whose
capture-state transitions remain valid.

Zax does not add a language-level one-shot or self-resetting lambda:

- automatic reset would need to identify one owning slot even when several
  handles reach the same receiver;
- a wrapper that fires once, retains a filled callable slot, and defines later
  panic/no-op behavior can be a future generic/reflection-based abstraction; and
- ordinary source can invoke and then explicitly reset the applicable slot.

Capture declarations may ordinarily offer `last`; doing so does not make the
callable self-reset or make repeated destructive use valid.

#### Lambda self-recursion

A bound lambda's receiver is the lambda itself:

```zax
countDown := [[]] ()(
  value : Integer
) bound {
  if value > 0
    _.(value - 1)
}
```

`_.(...)` invokes the same receiver and minted implementation. Replacing a
varying slot through which this lambda was originally reached does not redirect
its self-recursion. Calling the slot name explicitly would deliberately dispatch
through the slot's current installed value.

An unbound lambda has no `_` and cannot use self-receiver recursion.

#### Replacement and reset

Replacing a varying callable slot:

1. evaluates and completely constructs the new callable;
2. preserves any relationship needed when source and destination alias;
3. dispositions the old installed target according to its active binding kind;
4. installs one complete new callable state; and
5. never exposes a half-replaced slot.

Old-target disposition is:

- unbound: clear the implementation;
- borrowed bound: release only the borrow;
- unique: release/destroy the owned receiver;
- strong: release one strong participation;
- strong atomic: release atomically;
- weak: release the weak observation, never the target.

Reset applies the same disposition and establishes unavailable state. Resetting
an already unavailable slot is an idempotent no-op. A final slot cannot be reset,
and a readonly path cannot reset a varying slot.

If disposition panics, replacement/reset remains the blocked operation. It does
not publish the new callable, unwind, or expose a half-reset state.

Exact unique self-move must avoid destroying its own source; it may be defined as
a no-op or diagnosed according to the selected ownership operation. Borrowed,
unbound, and strong copies establish the needed new relationship before
releasing the old one.

### Type-erased allocation ownership consequence

#### Why `OpaqueOwner` is required

Without a dedicated type-erased owner, programmers can misuse bound callables
solely to keep an unknown strong or unique receiver alive. `OpaqueOwner` provides
the honest operation:

> Own or observe one allocation root without exposing typed access to its
> resident instance.

It is available only from actual allocation ownership. An ordinary value,
reference, borrowed raw pointer, or callable without active ownership cannot
manufacture an `OpaqueOwner`.

#### Ownership roles

```zax
exclusive : OpaqueOwner unique
prepared : OpaqueOwner unique shareable
shared : OpaqueOwner strong
observer : OpaqueOwner weak
atomicShared : OpaqueOwner strong atomic
atomicObserver : OpaqueOwner weak atomic
```

Erasure preserves the ownership transition:

```zax
typedShared : MyType * strong = @
opaqueShared : OpaqueOwner strong = typedShared
// Both participate in the same strong ownership.
```

```zax
typedUnique : MyType * unique = @
opaqueUnique : OpaqueOwner unique = typedUnique as last
// typedUnique is vacant; opaqueUnique uniquely owns the allocation.
```

The owner retains the allocation root, allocation record, destructor,
arena/disposition relationship, control-block participation, collection
metadata, and private exact type/capability witness needed for safe recovery.

It exposes no pointee, address, member access, dereference, or structural
compatibility. It is not a substitute for `void` or an empty structural type.

`copy`, `move`, `last`, vacancy, reset, weak acquisition, and atomic accounting
follow the corresponding managed ownership rules. `deep` is unavailable.

```zax
if ?opaqueShared
  reportAssignedOwner()

reset opaqueShared
```

For `OpaqueOwner weak`, `?` reports a stored weak relationship.
`liveness probe` observes whether strong ownership is open at that instant.
Ordinary construction into `OpaqueOwner strong` performs the actual pinning
acquisition and may produce a vacant destination.

#### Safe exact typed recovery

Every `OpaqueOwner` preserves a private exact allocation-root type witness. No
`outer tracked` marking is required: that existing capability tracks an
immediate member/container placement relationship, not erased root identity.

Safe recovery checks:

- exact canonical allocation-root type;
- compatible ownership transition;
- local versus atomic ownership family;
- retained qualification/access authority; and
- allocation-root rather than erased interior-target provenance.

```zax
myType : MyType * unique = myOpaqueOwner as last
```

Failure produces a vacant typed destination rather than panic. The opaque source
retains its ownership when the checked transition fails. The complete
conditional-transfer integration must preserve the established source-state rule
for an offered `last`; code intending to try several destination types can
preflight exact identity.

```zax
if myType : MyType * unique = myOpaqueOwner as last ;; ?myType {
  use(myType.)
}
```

The exact type probe performs no transfer:

```zax
if myOpaqueOwner is type MyType {
  myType : MyType * unique = myOpaqueOwner as last
}
```

Transparent aliases compare as their canonical target. Identity types remain
distinct. For a weak opaque owner, `is type` may remain true after ownership
closes; it does not prove liveness or acquire strong ownership.

#### Unsafe typed recovery

```zax
myType : MyType * unique =
  unsafe transfer (myOpaqueOwner as last)
```

`unsafe transfer` bypasses the type-witness check while performing the requested
ownership transition. It is not `unsafe cast`: it reconstructs typed ownership
from allocation metadata rather than reinterpreting pointer bits.

A statically proved mismatch remains an error. A false opaque assertion has
undefined consequences.

#### Anchored and common-lifetime boundaries

Erasing an anchored interior owner retains the enclosing allocation root, not
the interior target:

```text
anchored target ownership
-> OpaqueOwner
-> erased allocation-root ownership
```

Typed recovery therefore recovers only a compatible allocation-root pointer.
Preserving an erased interior path would be a different type-erased anchored
pointer feature.

No `OpaqueOwnerCommonLifetime` built-in is needed. A programmer-defined generic
may pair one `OpaqueOwner` with a typed reference or pointer whose origin is
proved to remain within the owned allocation lifetime. The wrapper must
establish that relationship; merely storing unrelated values together proves
nothing.

### Type-erased nonowning observation consequence

#### Why observers are separate from owners

Programmers also need to erase the type of a pointer or reference without
constructing an arbitrary “any” value and without asserting an unrelated type
through `unsafe cast`.

Two nonowning forms preserve that distinction:

- `OpaqueObserver` erases one pointer target and may be vacant.
- `OpaqueReferenceObserver` erases one fixed reference target and cannot be
  vacant.

Neither owns or extends the target lifetime. They retain exact hidden target
type, origin, place, qualification, and access facts needed for safe recovery
and static lifetime analysis.

#### `OpaqueObserver`

```zax
typed : MyType * = obtainPointer()
opaque : OpaqueObserver = typed

if ?opaque
  inspectAddressAvailable()
```

`OpaqueObserver` is a type-erased raw-pointer-like observation:

- it can be vacant and supports `?`/`!` as vacancy tests;
- it owns no allocation and keeps no target alive;
- it may preserve an allocation-root or interior target;
- copying it copies only the observation;
- ordinary raw-pointer lifetime and provenance rules continue to apply; and
- `liveness probe` is unavailable because no ownership relationship can test
  whether an arbitrary raw target remains live.

A default `OpaqueObserver` is vacant and has no hidden target type. Erasing a
vacant typed pointer produces a vacant observer that still remembers that
pointee type.

Safe recovery produces a typed raw pointer:

```zax
typedAgain : MyType * = opaque

if ?typedAgain
  use(typedAgain.)
```

Type mismatch produces a vacant destination rather than panic. `is type`
distinguishes mismatch from source vacancy:

```zax
if opaque is type MyType {
  // The erased target type is MyType even when the pointer is vacant.
}
```

`vacate opaque` clears a nonowning address without disposition and preserves the
hidden target type, just as vacating an ordinary typed raw pointer preserves its
static pointee type. Reset remains an ownership/disposition operation and is not
the nonowning observer operation.

#### `OpaqueReferenceObserver`

```zax
typed : MyType & = value
opaque : OpaqueReferenceObserver = typed
```

`OpaqueReferenceObserver` is a type-erased reference:

- it always binds one fixed place;
- it cannot be vacant and has no `?`/`!`;
- it owns nothing and extends no lifetime;
- it never rebinds;
- copying constructs another observer bound to the same place; and
- it has no reset, vacate, or liveness probe.

Direct typed recovery returns a reference and therefore cannot report mismatch
through vacancy:

```zax
typedAgain : MyType & = opaque
// Panics when the hidden type does not match.
```

The nonexecuting preflight is:

```zax
if opaque is type MyType {
  typedAgain : MyType & = opaque
  use(typedAgain)
}
```

The successful test establishes a flow fact, allowing the body recovery without
another runtime type-token comparison. The reference target must independently
outlive every use before and after erasure.

#### Shared exact-type probe

The protected exact form applies to all three opaque categories:

```zax
opaqueOwner is type MyType
opaquePointer is type MyType
opaqueReference is type MyType
```

`is type`:

- performs no ownership transfer;
- creates no pointer or reference;
- compares the retained exact canonical type witness;
- treats transparent aliases as their canonical target;
- preserves identity boundaries; and
- grants no liveness, ownership, qualification, or access proof beyond the
  exact tested type fact.

Safe recovery never launders authority. It may preserve or reduce the source
qualification/access profile but cannot turn erased readonly access into
writable access, final-place truth into varying truth, or a borrowed observation
into ownership.

#### Anchored owner and interior observer are complementary

Erasing an anchored pointer into `OpaqueOwner` keeps the allocation root and
forgets the interior target. Erasing the same typed target into
`OpaqueObserver` keeps the interior observation and no ownership.

A programmer-defined generic may retain both when it needs type-erased lifetime
ownership plus a type-erased interior target. No additional common-lifetime
built-in is needed; the wrapper must prove that the observed target remains
inside or otherwise bounded by the owned life path.

### Library-surface placement pressure

There is no current general raw library-surface owner. Before archival, an
indexed live raw destination must capture canonical namespace/root placement for:

- `OpaqueOwner`, `OpaqueObserver`, and `OpaqueReferenceObserver`;
- `Callables.BindingKind` and other callable metadata;
- `String`, `AsciiString`, `Ucs2String`, `Utf8String`, `Utf16String`, and
  `Utf32String`;
- `Rune`, `AsciiChar`, and `Ucs2`;
- `MbcsString<Encoding>`;
- `Legacy.CharString`, optional `Legacy.WideString`, and the terminated legacy
  string policy families;
- the canonical anchor for `Legacy`; and
- the general distinction between protected root declarations, exact root
  aliases, and library namespaces.

Intrinsic `U8`, `U16`, and `U32` remain under their current scalar ownership and
do not move into that future library surface.

This work item does not decide whether `OpaqueOwner` is canonical at root or
exposed there by exact alias, whether `Callables` is the final namespace name, or
what common namespace owns text identities. It records the required future
decision and prevents accidental permanent root placement through promotion.

### Diagnostics and costs

Diagnostics should identify:

- malformed capture spacing or an invalid zero-capture form;
- duplicate, unconstructible, or multiply mapped captures;
- unavailable default capture `copy`;
- reference-capture lifetime escape;
- source stance versus capture declaration stance;
- a capture or `_` used in an unbound lambda;
- receiver-lifetime qualifiers applied to unbound storage;
- bound/unbound or ownership-capacity mismatch;
- a final callable used as replaceable;
- positional composition mismatch;
- reshape duplicate, missing, reversed, or incompatible mappings;
- resultful weak callable storage;
- weak callable assignment presence versus liveness;
- repeated invalid use of moved-from or terminal capture state;
- invocation of an unavailable function;
- reset through an ineligible path;
- `OpaqueOwner` construction from a nonowner;
- `OpaqueObserver` or `OpaqueReferenceObserver` lifetime escape;
- unavailable liveness probing on a nonowning observer;
- reference-observer type mismatch requiring panic;
- observer recovery that would strengthen qualifications or access;
- exact type-recovery failure versus ownership-transition failure;
- unsafe transfer with a proved mismatch; and
- attempted typed recovery of an erased interior target.

Programmers and tools must expose:

- capture evaluation/construction/destruction order;
- direct versus indirect invocation;
- receiver storage and erased destructor cost;
- allocation and selected arena;
- unique/strong/weak and local/atomic accounting;
- liveness-probe cost and its non-pinning result;
- binding-kind query cost;
- composition call sequence and mapping;
- reset/replacement disposition;
- private type-witness comparison for opaque owners and observers;
- observer representation and origin-tracking cost;
- static capture, origin, transfer, and repeated-state analysis; and
- source-stability effects of labels, reshape entries, prototypes, and storage
  capabilities.

### Remaining integration and deferrals

No core semantic disagreement is currently known. The following remain explicit
integration or deferred-owner work:

- Exact grammar placement for lambda allocation policy enclosures must remain
  consistent with general `@{...}` allocation syntax.
- Exact protected source registration for `liveness probe`,
  `binding kind of`, `is type`, and `unsafe transfer` requires operator/source
  integration without reserving unrelated phrase families.
- Exact construction/recovery syntax and qualification-profile representation
  for `OpaqueObserver` and `OpaqueReferenceObserver` must preserve the aligned
  nonowning behavior without creating a general arbitrary-value `any`.
- Failed checked `OpaqueOwner` recovery must integrate precisely with the common
  runtime-conditional `last` source-state rule.
- The library-surface raw destination described above requires separate
  discussion and edit authorization before archival.
- Generic lambda parameters, constraints, and specialization remain indexed
  generic work.
- Reflection must eventually expose callable static capacity separately from
  installed binding kind and private erased receiver/type facts.
- Async capture remains async work; synchronous reference proof does not imply
  validity across suspension.
- FFI/raw function-pointer ABI and dynamic linking remain interop work.
- Complete variadic composition remains variadic work.
- Optional callback combinator naming remains indexed composition input.
- Legacy restricted `scope` capture remains preserved for later disposition.
- A one-shot callable remains a possible generic/reflection-based abstraction,
  not a language lambda mode.
- Closure layout, control-block layout, small-buffer optimization, type-token
  representation, and lowering remain implementation concerns unless they
  change programmer-visible behavior or cost.

Every retained deferral requires an indexed live destination before archival.

### Provisional evidence disposition

These dispositions reflect the aligned candidate model but are not promoted
language design:

- The refreshed maintainer notes supply the primary lambda expression,
  source-to-destination capture, callable storage, receiver lifetime,
  composition, and weak-call behavior.
- Current examples using superseded lambda declaration syntax must be repaired
  directly during promotion without teaching the old form.
- Current owner wording that every bound value is an unfilled known receiver
  prototype must be revised to teach fixed declarations, receiver-capable
  varying storage, and installed erased receiver kinds coherently.
- Legacy unsafe pointer/reference casts used only to erase type identity should
  be replaced by the applicable opaque observer without teaching an arbitrary
  value-erasure mechanism.
- The legacy receiver-reattachment proposal retires because direct bound values
  and replaceable bound storage solve its motivating use.
- Legacy raw-function-pointer spelling conflicts with current pointer grammar.
  Preserve the compact noncapturing unbound callable requirement, not that
  spelling.
- Legacy composition/chaining examples remain behavioral evidence, with
  positional composition, explicit reshape mapping, and immediate `|>` chaining
  replacing implicit name-first guessing.
- Legacy immediate anonymous invocation remains useful: a complete lambda
  expression is directly callable.
- Legacy `mutator` remains an independent computed-property/accessor concern. If
  useful evidence survives, it needs an indexed raw destination.
- `[<...>]` compiler directives remain current. Lambda work retains contiguous
  capture delimiters and does not reopen legacy directive spelling.

## Dispositions and promotion dry run

### Documentation-fit result: PASS

The aligned findings have one coherent programmer-facing home, every cross-owner
rule has a lasting owner, unresolved namespace/library and computed-property
material has an indexed raw destination, and the complete promotion set can
teach the feature without depending on this working record.

PASS does not make this file authoritative. It records that the aligned model is
ready for the separately authorized promotion.

### Structure proposal

Retain the current repository layout and add one cohesive concept owner:

- `language/lambdas-and-callable-composition.md` becomes the programmer-facing
  owner for lambda expressions, capture construction, callable binding/storage,
  receiver lifetime capacity, weak callable behavior, callable transfer/reset,
  recursion, partial application, composition, reshape mapping, chaining,
  diagnostics, and costs.
- Existing current owners retain their local rules and teach only the part a
  reader needs at that boundary.
- `language/pointers-and-arenas.md` owns `OpaqueOwner`, `OpaqueObserver`, and
  `OpaqueReferenceObserver`, because they are ownership/observation mechanisms
  rather than lambda types.
- `project/raw/library-surface-and-namespaces.md` holds unresolved canonical
  placement for opaque facilities, callable metadata, text identities, and the
  `Legacy` anchor.
- `project/raw/computed-properties-and-mutator-callables.md` preserves the
  remaining useful `mutator`/computed-property evidence extracted from the
  legacy function page.
- `index.md` routes ordinary readers to the new current owner. Raw files remain
  absent from ordinary reading paths.

No new directory, specification area, or implementation documentation is
needed.

### Ownership map

#### Cohesive lambda/callable owner

`language/lambdas-and-callable-composition.md` owns:

- `[[]]` zero capture and the complete lambda expression mental model;
- by-value, typed, renamed, reference, source-stance, anchored, and producer
  result capture;
- capture evaluation, construction, body visibility, and destruction order;
- complete prototype inheritance and anonymous receiver identity;
- `bound`/`unbound`, `final`/`varying`, and installed receiver-lifetime modes;
- `Callables.BindingKind`, callable presence, `liveness probe`, and weak no-op
  invocation;
- member-bound capture without receiver reattachment;
- callable `copy`/`move`/`last`, unavailable source state, reset, replacement,
  repeated destructive capture analysis, and self-recursion;
- partial capture, positional composition, reshape composition, and immediate
  chaining; and
- callable-specific diagnostics, costs, and source-stability effects.

#### Local current owners

- `language/source-structure.md` owns capture/array/directive token boundaries,
  mandatory interior spacing, compact zero forms, and final physical source
  presentation.
- `language/arrays-and-slices.md` owns `[]` as an explicit empty array, nested
  `[ [] ]` cardinality, and corrected lambda examples used to distinguish array
  source.
- `language/declarations-and-bindings.md` owns fixed/varying function
  declarations, receiver-capable storage declarations, prototype inheritance
  at a typed destination, aliases, and unavailable default initialization.
- `language/function-invocation.md` owns invocation phases, selected visible
  prototypes, callable-value discovery, weak call completion, and result/input
  routing used by composition.
- `language/lifetimes-and-references.md` owns capture/observer life paths,
  reference capture, borrowed callable/observer escape, and origin preservation.
- `language/qualifiers.md` owns qualification and declaration-side permission
  preservation through captures and opaque observers.
- `language/transfer-stances.md` owns callable relationship transfer source
  states, capture source stance, and the absence of callable `deep`.
- `language/nothing-instances.md` owns unavailable callable state, uniform
  immediate `?`/`!` presence, weak presence versus liveness, and reset state.
- `language/pointers-and-arenas.md` owns weak presence/acquisition,
  `OpaqueOwner`, `OpaqueObserver`, `OpaqueReferenceObserver`, exact typed
  recovery, vacancy/panic behavior, reset/vacate, anchored-root effects, arena
  relationships, and costs.
- `language/construction-and-destruction.md` owns capture receiver and callable
  replacement/reset lifecycle ordering.
- `language/safety-and-analysis.md` owns checked opaque recovery, `is type`,
  unsafe transfer responsibility, lifetime assertions, and known-invalid
  boundaries.
- `language/operators.md` owns protected presence, liveness, binding-kind,
  opaque-type probe/transfer, composition, reshape composition, and chaining
  behavior at the shared operator depth.
- `language/operator-catalog.md` owns exact forms, fixity, precedence,
  reservation, and the removal of `|>` from deferred status.
- `language/structural-shapes-and-compatibility.md` owns no-storage reshape
  mapping across callable result/input slot shapes.
- `language/optional-values.md` owns the distinction among outer absence,
  present-unavailable callable, and present-callable states.
- `language/terms.md` owns concise cross-cutting vocabulary for lambdas,
  generated receivers, callable binding/storage, opaque ownership/observation,
  liveness probes, and composition.

#### Routers, raw inputs, and legacy surfaces

- `index.md` adds the current owner to both the start route and current-design
  list and removes legacy function composition as an ordinary destination.
- `project/raw/README.md` indexes the new library-surface and computed-property
  raw inputs.
- `project/raw/function-composition-and-chaining.md` retains only unresolved
  optional combinator, restricted-scope, advanced call/mixfix, generic, and
  reflection pressure after promoted lambda/composition behavior is removed.
- `project/raw/callable-selection.md` and `project/raw/reflection.md` are corrected
  to use receiver-capable callable storage rather than the retired
  unfilled-receiver runtime model.
- `project/raw/feature-catalog.md` routes lambdas to the new current owner.
- The defunct root `functions.md` page is removed after useful callable material
  is promoted and mutator evidence is preserved in indexed raw input.
- `type-definition.md` routes function/lambda readers to the new owner.
- The defunct root `operator.md` page is removed after its useful material has
  current owners.
- Legacy directive-bearing pages retain their legacy concepts but use the
  current `[<...>]` enclosure so directive source is not confused with lambda
  capture.

### Teaching plan

The new owner is written for a cold reader rather than copied from this record:

1. open with one small noncapturing lambda and one copied capture;
2. contrast copied and reference capture with visible outcomes;
3. introduce the generated receiver before naming bound storage;
4. teach fixed/varying and bound/unbound with one declaration family;
5. layer borrowed, unique, strong, atomic, and weak storage with costs;
6. teach partial capture, composition, reshape mapping, and chaining through
   executable source progression;
7. place transfer, reset, recursion, diagnostics, and advanced state after
   ordinary use; and
8. hand lifetime-only erasure to the pointer owner instead of interrupting the
   lambda narrative.

Promotion does not teach superseded approaches merely to deny them. It corrects
old examples directly. Counterexamples remain only where a likely programmer
mistake distinguishes a valid current boundary, such as `[]` versus `[ [] ]`,
presence versus liveness, or reference recovery mismatch.

### Exact promotion change set

#### Add

- `language/lambdas-and-callable-composition.md`
- `project/raw/library-surface-and-namespaces.md`
- `project/raw/computed-properties-and-mutator-callables.md`

#### Modify current owners and routers

- `index.md`
- `language/source-structure.md`
- `language/arrays-and-slices.md`
- `language/declarations-and-bindings.md`
- `language/function-invocation.md`
- `language/lifetimes-and-references.md`
- `language/qualifiers.md`
- `language/transfer-stances.md`
- `language/nothing-instances.md`
- `language/pointers-and-arenas.md`
- `language/construction-and-destruction.md`
- `language/safety-and-analysis.md`
- `language/operators.md`
- `language/operator-catalog.md`
- `language/structural-shapes-and-compatibility.md`
- `language/optional-values.md`
- `language/terms.md`
- `project/raw/README.md`
- `project/raw/function-composition-and-chaining.md`
- `project/raw/callable-selection.md`
- `project/raw/reflection.md`
- `project/raw/feature-catalog.md`
- `type-definition.md`

#### Delete

- `functions.md`
- `operator.md`

#### Correct directive enclosure mechanically and contextually

- `compiler-directives.md`
- `meta-functions.md`
- `meta-types.md`
- `flow-control.md`
- `basics.md`
- `concurrency.md`
- `warnings-errors.md`
- `ctor-dtor.md`
- `lazy.md`
- `project/raw/export-and-visibility-directives.md`

Only directive occurrences change enclosure. Lambda capture in
`type-definition.md`, current owners, and maintainer notes is not included in
the mechanical replacement.

#### Update the active record after promotion

- `project/work/027-lambda-definitions-and-callable-composition.md` records the
  applied promotion, validation result, and any revised disposition.

No file is moved, archived, staged, deleted, or committed by this promotion.
The maintainer's staged review boundary remains untouched.

### Promotion applied

The authorized promotion was applied after the PASS result.

The resulting current reading path:

1. routes developers from `index.md` to
   `language/lambdas-and-callable-composition.md`;
2. teaches the cohesive feature there through ordinary source progression;
3. keeps invocation, lifetime, transfer, Nothing, pointer, lifecycle,
   structural, safety, and source rules in their existing local owners;
4. routes canonical library placement and computed-property evidence to new
   indexed raw destinations; and
5. removes defunct `functions.md` after extracting every useful concern.

Legacy directive-bearing pages now use `[<...>]` enclosures. Lambda captures
remain `[[ ... ]]`, with `[[]]` for zero captures. Empty arrays use `[]`, while
`[ [] ]` retains its distinct one-empty-inner-array cardinality.

Validation performed:

- `git diff --check` passes for the complete unstaged promotion.
- The staged baseline remains unchanged; promotion edits are unstaged and new
  owner/raw files remain untracked.
- Live non-archive searches find no old directive enclosure code outside the
  preserved maintainer-note evidence, and no current lambda expression using the
  superseded declaration boundary.
- Current weak-pointer teaching consistently separates immediate `?` presence,
  nonacquiring `liveness probe`, and actual strong acquisition.
- Current bound-callable teaching contains no runtime unfilled-receiver or
  receiver-reattachment route.
- New relative links and referenced headings were checked against their live
  destinations.
- The current lambda owner opens with usable source, introduces terminology only
  after concrete effects, layers advanced ownership/composition behavior later,
  and does not transcribe discovery chronology or teach superseded forms.

No promotion change requires implementation source, formal ABI, or a
specification area.

### Closure

Work item `027` is complete. Its aligned lambda, callable-storage, composition,
weak-presence, opaque-owner/observer, source-spacing, and legacy-correction
findings were promoted to their current owners. Remaining optional-combinator,
generic, reflection, library-placement, and computed-property concerns have
indexed raw destinations.

The consumed lambda maintainer notes retired from `project/raw/`; this archived
record and Git history preserve their provenance. Work continues with
`028: Types, unions, and variants`.
