# 019: `using` and scoped resource lifetimes

| Field | Value |
| --- | --- |
| Status | Historical working record / non-normative / audit-only |
| Work Item | `019` |
| Created | 2026-09-09 |
| Closed | 2026-09-10 |
| Owns | Historical record of the bounded review defined by the fixed initiating input |
| Does Not Own | Exceptions or panic recovery, generalized cleanup hooks, async cancellation, complete owned composition, generic resource concepts, or compiler lowering |

## Non-authority notice

This file is a historical, audit-only record of collaborative design work.
Accepted findings were promoted into their lasting owners before closure. This
record remains non-authoritative and is not part of ordinary design reading.

## Fixed initiating input

This section records the information aligned when work item `019` was created.
It is intentionally incomplete and must not be rewritten as work develops.

### Initiating concern

Establish a coherent human-developer-facing model for scoped resource use and
decide the disposition of legacy `using`.

The central question is whether:

```zax
using resource := acquireResource() {
  use(resource)
}
```

provides any material behavior beyond an ordinary declaration in the current
explicit `scope` construct:

```zax
scope resource := acquireResource() {
  use(resource)
}
```

If no distinct semantic guarantee survives review, retire `using` rather than
preserving two spellings that imply different lifetime or cleanup behavior. If a
distinct construct remains justified, define its exact additional contract.

### Motivating pressure

Legacy flow control presents `using` as an always-true, scoped initialization
construct and also proposes `using value own`, which appears to expose or inject
member access. Current Zax already has:

- ordinary declaration and initialization;
- explicit `scope` with initializer, post, labels, and transfers;
- automatic reverse destruction on normal and abrupt scope exit;
- synchronous temporary lifetime rules;
- explicit transfer stances; and
- separate unresolved owned-composition and callable-capture pressure.

The review must distinguish a genuine resource-lifetime operation from redundant
flow sugar and unrelated member-exposure or capture syntax.

### Known assumptions

- Every local, body, and flow-header lifetime ends automatically when its scope
  exits.
- Normal and abrupt exits destroy applicable values in reverse construction
  order.
- A post operation is ordinary normal-completion work, not guaranteed cleanup,
  `finally`, or `defer`.
- Explicit `scope` accepts an initializer, optional post, one effective body, a
  flow label, and applicable `break`/`continue`/`next`/`goto` transfers.
- Direct body re-entry destroys the old body scope, retains active header
  lifetimes, and starts a fresh body scope.
- A declaration can name a temporary and give it the surrounding declaration
  lifetime under existing construction and reference rules.
- No compiler implementation exists in this repository.

### Known inclusions

- Ordinary scoped resource acquisition, initialization, use, and destruction.
- Whether `using` differs from explicit `scope`.
- Binding visibility and temporary lifetime.
- Normal completion and every applicable transfer or panic boundary.
- Post behavior and whether a resource construct needs one.
- Direct re-entry while an acquired header value remains alive.
- Several resources through ordinary composed initialization.
- Acquisition failure through existing construction, optional, result, or panic
  behavior rather than an assumed special mechanism.
- Cost visibility, diagnostics, source stability, and formatting.
- Complete disposition of legacy `using value own`.
- Lasting documentation ownership and legacy/raw disposition.

### Known starting boundaries

- Exceptions, panic recovery, or resumable failure.
- General `defer`, `finally`, cleanup callbacks, or scope guards.
- Async suspension, cancellation, and cross-thread teardown.
- Complete owned-composition or member-exposure syntax.
- General callable capture or closure representation.
- Generic resource concepts, constraints, or context-manager protocols.
- Compiler lowering or runtime implementation.

These boundaries do not erase consequences. Record every material pressure in a
live owner or indexed raw destination.

### Intentionally unresolved framing

- Whether `using` remains a language construct at all.
- Whether a retained `using` is semantic or only narrow source sugar.
- Whether it can express anything not expressible through explicit `scope`.
- Whether acquisition failure changes body entry.
- Whether a retained construct supports labels, posts, or direct re-entry.
- Whether `using value own` belongs to resource lifetime, owned composition,
  member projection, or callable capture.
- Whether resource-specific terminology is justified when ordinary values
  already have deterministic destruction.

### Initial stopping guidance

Stop when the work has:

- established the ordinary scoped-resource mental model;
- decided whether `using` remains, narrows to justified sugar, or retires;
- integrated initializer scope, temporary lifetime, transfers, posts,
  destruction, and completion;
- dispositioned `using value own` without silently designing owned composition;
- preserved exception, cleanup-hook, async, generic, capture, and implementation
  consequences in live destinations;
- identified exact current owners and legacy/raw changes; and
- completed the required documentation-fit dry run.

Do not promote findings, archive this work item, begin work item `020`, or design
general cleanup, owned composition, async cancellation, or generic resource
protocols without the separately required discussion, alignment, and
authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  numbered work, promotion, teaching, deferrals, and dry runs.
- Legacy `using` statement, consumed from `flow-control.md` during promotion -
  supplied the primary historical source and `using value own` pressure now
  preserved in this record and its disposition.
- Focused [loops and explicit `scope`](../../language/core-flow-control.md#loops-and-explicit-scope),
  [header schemas and binding scope](../../language/core-flow-control.md#header-schemas-ordering-and-binding-scope),
  and [unwinding and completion](../../language/core-flow-control.md#unwinding-destruction-and-completion) -
  establish the current competing flow and transfer model.
- Focused [flow-control initialization](../../language/declarations-and-bindings.md#flow-control-initialization) -
  establishes header declaration visibility.
- Focused [scope-exit destruction](../../language/construction-and-destruction.md#scope-exit-destruction-and-flow-transfers) -
  establishes automatic lifetime ending and explicit lifecycle pressure.
- Focused [common life paths](../../language/lifetimes-and-references.md#common-life-paths)
  and [synchronous temporary extension](../../language/lifetimes-and-references.md#temporary-extension-is-synchronous) -
  constrain resource and temporary lifetime.
- Focused [header sections and separators](../../language/source-structure.md#header-sections-and-separators)
  and [braces and body boundaries](../../language/source-structure.md#braces-and-body-boundaries) -
  constrain any retained source form.

### Consequence-driven

- Read [raw owned composition](../raw/owned-composition.md) only when
  dispositioning legacy `using value own` or a concrete member-exposure claim.
- Read [raw function composition and chaining](../raw/function-composition-and-chaining.md)
  only when callable capture or generated callback behavior becomes material.
- Read [optional values](../../language/optional-values.md) only when a concrete
  fallible acquisition uses absence.
- Read [pointers, allocation, and arenas](../../language/pointers-and-arenas.md)
  only when a concrete resource example depends on allocation ownership or arena
  lifetime.
- Read raw async, safety, analysis-control, generic, or interop input only when a
  concrete scoped-resource rule creates a dependency that must be preserved
  rather than solved.

### Audit-only

- `project/archive/`, including work items `001` through `018`.
- Deleted or superseded resource-management implementation sketches recoverable
  through Git history.

Do not read archived work item `018` during ordinary work on `019`. Its accepted
flow and transfer findings are promoted into current owners.

## Working record

This record preserves the initial reconstruction and the later maintainer
discussion that materially changed its conclusion. The aligned direction below
remains non-authoritative until promotion.

### Current review entry point

**Aligned direction:** retain `using`, but do not retain its legacy
"always-true `if`" model. Redesign it as a non-reentrant scoped-resource
operation with:

- a mandatory parenthesized, mapping-capable resource-entry list rather than a
  flow initializer;
- named values declared for the body, unnamed temporaries whose lifetimes are
  extended through the body, and existing places borrowed by reference;
- an end-of-use phase that invokes a compatible pre-unary `dispose` phrase on
  enrolled receivers while they remain alive;
- destruction of `using`-owned entries only after the disposal phase completes;
- an optional ordinary flow label; and
- an explicitly targeted `break label:` as the only ordinary-flow way to bypass
  that `using` statement's disposal phase.

```zax
using resources: (
  resource := fetchResource(),
  grant := fetchGrant(),
  fetchContract(),
  altResource
) {
  use(resource, grant, altResource)
}
```

This operation is materially different from explicit `scope`. A `scope` post is
normal-completion work that transfers may skip, and the `scope` body may be
re-entered while its header remains alive. `using` instead establishes one
non-reentrant end-of-use obligation that follows every ordinary exit unless the
programmer names that exact `using` as the target of `break`.

`using` deliberately has no `;;` initializer or post section. Its resource list
and disposal phase are the complete operation. This difference from other flow
headers is justified by its distinct mapping, lifetime, and exit behavior.

### Why disposal is distinct from destruction

Destruction is the terminal end of one resident instance. Disposal is an
ordinary operation on an instance that remains alive and is destroyed later.
That distinction supports behavior a narrower block or destructor cannot always
provide:

- breaking a circular `strong` relationship before reference-counted
  destruction can begin;
- completing or shutting down a resource while its strong reference and other
  collaborating resources remain alive;
- making an acknowledged orderly shutdown protocol distinct from terminal
  dismantling; and
- coordinating several resources before any `using`-owned entry is destroyed.

For example, two strongly connected objects may never reach their destructors
until an operation severs the cycle:

```zax
using (session := establishSession()) {
  run(session)
}
// `dispose session` may break strong cycles while session is still alive.
// Destruction follows after the disposal phase.
```

`using` is the complete scoped-resource operation. `dispose` is the optional
per-type hook selected during its end-of-use phase. Neither one changes the
meaning of `---`, and `dispose` does not by itself end the receiver's lifetime.

### Resource entries, result mapping, and lifetime

The mandatory parentheses delimit a mapping-capable resource-entry list:

```zax
using resources: (
  resource := fetchResource(),
  grant := fetchGrant(),
  fetchContract(),
  altResource
) {
  use(resource, grant, altResource)
}
```

The complete header shape is:

```text
using [label:] (resource-entry-list) body
```

The parentheses are construct-specific list delimiters like a call's argument
list, not expression-grouping parentheses. They are required even for one entry:

```zax
using (resource := fetchResource()) {
  use(resource)
}
```

The entry categories are:

| Entry | Behavior |
| --- | --- |
| `resource := fetchResource()` | Introduce a named header binding, make it available in the body, enroll its receiver for possible disposal, and destroy its owned value after disposal |
| `fetchContract()` | Evaluate once and enroll every result; an unnamed by-value result remains alive through the body and disposal phase and is then destroyed |
| `altResource` | Borrow the existing instance place by reference, enroll the receiver resident in that place at disposal time, and do not destroy the borrowed value |
| A reference-valued expression | Keep the returned reference alive, enroll its borrowed target, and do not destroy that target |

Entries evaluate once in visible source order. A declared entry becomes visible
after its initializer completes and remains available to later entries and the
body. A bare producer with several results enrolls each result in declared
result order; it does not pack them into a structural value:

```zax
using (fetchPair()) {
  // Every result is enrolled but unnamed.
}
```

The outer parentheses still denote the `using` list. Another pair forces the
producer into one-expression mode under existing result-mapping rules:

```zax
using ((fetchPair())) {
  // Valid only when fetchPair() can produce one expression value.
}
```

Named multiple-result capture must select source results by label:

```zax
using resources: (
  resource:,
  grant: = fetchPair(),
  externalResource
) {
  use(resource, grant, externalResource)
}
```

`resource:` and `grant:` select those exact source-facing result labels and
introduce same-named inferred bindings. A label absent from the selected
producer is a hard semantic error. Results may be selected in another order,
and each may be selected at most once.

Post-promotion review exposed a broader result-routing limitation. The aligned
direction is now **source result first, destination second** across `using`,
calls, returns, and other result-routing contexts:

```zax
using (
  resource: myResource:,
  grant: myGrant: = fetchPair()
) {
  use(myResource, myGrant)
}
```

Here `resource:` and `grant:` select producer results. `myResource:` and
`myGrant:` name their new body-visible destinations. A source label may instead
map into a complete typed destination declaration:

```zax
using (
  resource:,
  grant: myDisposalGrant : MyCarryingGrant = fetchPair()
) {
  use(resource, myDisposalGrant)
}
```

The selected `grant` result directly initializes `myDisposalGrant`; the new
destination type and operations determine what `using` later disposes and
destroys. Introducing several named results positionally remains invalid:

```zax
using (
  resource : Resource,
  grant : Grant = fetchPair()
) {
} // error: named using results require source-result labels
```

This source-first ordering is a general result-routing correction, not a
`using`-specific reversal. The source selector may be followed by a function
parameter label, return-result label, new inferred binding, complete typed
declaration, existing destination, or discard according to the surrounding
mapping context.

An anonymous destination and `#` have different effects in a result-routing
context. For a call, bare `:` supplies the current positional destination,
whereas `#` consumes the selected source without supplying that destination:

```zax
consume(
  text: : = produce()
) // produce.text supplies the current positional input

consume(
  text: # = produce()
) // produce.text is discarded; the input must be completed another way
```

The second form is valid only when the remaining input can be supplied later,
omitted, or defaulted. A typed anonymous destination is a caller-owned
intermediate that supplies the current positional input:

```zax
consume(
  text: : MyTextConsumer = produce()
)
```

Return-result routing has the same destination distinction: bare `:` supplies
the current outer result slot, while `#` leaves it to its existing initializer
or another valid completion path.

Outside result mapping and `using`, ordinary anonymous declarations may use
either an absent name or the explicit discard name:

```zax
{
  : Resource = acquire()
  work()
}

{
  # : Resource = acquire()
  work()
}
```

These are alternative spellings. Neither declaration exposes a name. With the
same declared type and surrounding lifetime, they have the same construction,
elision, and destruction behavior; `#` only makes the discard intent explicit.

Caller-side `#` suppresses body access and disposal but does not shorten the
result's natural lifetime in the complete `using` consumer:

```zax
using (
  resource:,
  # = fetchPair()
) {
  use(resource)
}
```

The discarded result remains an unnamed `using`-owned value. It participates in
the same destruction ordering it would have had if retained, but no compatible
`dispose` is selected for it.

`using` gives the anonymous declaration and `#` the same entry lifetime but
different disposal participation:

```zax
using (resource: : = acquire()) {
  work()
} // dispose and destroy the anonymous resource

using (resource: # = acquire()) {
  work()
} // skip dispose, then destroy the anonymous resource
```

Both entries are constructed at the same point, occupy the same caller-visible
entry position, permit the same source/destination elision, and are destroyed at
the same point. Bare `:` enrolls its value for compatible structural disposal;
`#` explicitly suppresses that disposal. `resource: : MyResource` remains
meaningful because it constructs a typed anonymous destination before
enrollment.

A structured pair is one result and therefore one enrolled receiver. A
zero-result operation cannot form a resource entry, and an empty list is
invalid:

```zax
using () { // error: use an ordinary block or scope
}
```

Explicit qualification and transfer syntax retains its ordinary meaning.
`resource as move`, for example, offers the `move` stance to the applicable
consumer; `using` does not reinterpret that source as proof that a transfer or
new ownership occurred.

Borrowing an existing value means borrowing its fixed place under the current
reference model. It does not freeze the resident instance that happened to
occupy a varying place at entry:

```zax
resource varying : Resource = fetchFirst()

using cleanup: (resource) {
  resource = fetchSecond()
}
// The enrolled reference still names resource's place.
// Disposal observes the completely established fetchSecond() successor.
```

Ordinary reference and lifecycle proof still applies. The place must exist and
contain a complete resident instance when the implicit disposal call occurs.
Mutation or replacement through other paths remains observable according to
the enrolled reference's qualifications. Zax does not allow an ordinary local
`value.---()` operation: explicit destructor-body member destruction remains a
separate, already constrained lifecycle facility.

Existing names retain ordinary lexical visibility in the body. The resource
list enrolls values for lifetime extension and disposal; it is not the legacy
callable-like restricted-capture list. Callable capture remains separate future
work.

An optional entry conditionally enrolls its immediate boxed value for disposal:

```zax
using (resource := tryObtainResource()) {
  if ?resource
    use(resource.)
}
```

At the disposal phase, an absent optional performs no call. A present optional
selects `dispose resource.` through ordinary rules. Compatibility is checked
statically for the possible present path even when a runtime instance may be
absent. The wrapper remains owned or borrowed according to the resource entry
and undergoes ordinary later destruction.

Optional participation crosses exactly one wrapper layer per enrollment. A
nested optional does not flatten recursively; source must explicitly select
another proven-present layer when that deeper value should participate.

Two acknowledgement categories keep distinct suspicious meanings separate.

`duplicate-resource-enrollment` applies when required origin analysis
establishes that a later entry enrolls a place already enrolled by an earlier
entry:

```zax
using (
  resource,
  intent<duplicate-resource-enrollment>{ resource }
) {
}
```

`repeated-resource-expression` applies when a later entry repeats the same
resource-producing expression even though each evaluation may produce a
distinct result:

```zax
using (
  grantMeAResource(),
  intent<repeated-resource-expression>{ grantMeAResource() }
) {
}
```

Each category encloses the later complete resource entry or producer group that
creates the repetition. When both descriptions could apply, known duplicate
place enrollment uses `duplicate-resource-enrollment`. Acknowledgement preserves
every written evaluation, entry, and corresponding disposal call; the compiler
does not merge repeated expressions or aliases. When distinct source cannot be
established as the same place and does not repeat the same expression, ordinary
programmer responsibility applies. Diagnostics do not depend on optional
optimizer inference.

A surrounding `intent<category>{...}` may acknowledge every occurrence of the
same category within its complete payload:

```zax
intent<repeated-resource-expression>{
  using (
    grantMeAResource(),
    grantMeAResource(),
    grantMeAResource()
  ) {
  }
}
```

Per-occurrence acknowledgement remains valid. A grouped acknowledgement must
contain at least one applicable occurrence; unrelated categories and every
non-acknowledgeable error remain active.

### The structural `dispose` hook

A type opts into automatic disposal by providing an exact pre-unary operator
phrase whose receiver is the enrolled value:

```zax
Resource :: type {
  operator pre unary 'dispose' final : ()() writable = {
    // Complete or shut down `_` while the instance remains alive.
  }
}
```

`dispose` is an ordinary, non-protected, receiver-owned phrase. `using` gives it
no special `writable` or other qualification requirement. The hook is compatible
when normal operation selection can invoke `dispose receiver` for the effective
receiver qualifications and transfer state with no result the caller is
required to acknowledge. Zero results is the ordinary form, but every result may
instead permit omission:

```zax
Resource :: type {
  operator pre unary 'dispose' final : (
    diagnostic # : String
  )() readonly = {
    // Readonly disposal is valid when no mutation occurs through `_`.
  }
}
```

The second form demonstrates two independent facts: `readonly` is compatible
when normal selection permits it, and a bare invocation may legally omit
`diagnostic`. The result is still produced and follows ordinary result,
temporary, and destruction rules; omission does not erase its cost or effects.
This form is incompatible because its result is required:

```zax
Resource :: type {
  operator pre unary 'dispose' final : (
    success : Boolean
  )() writable = {
  }
}
```

The required `success` result demands a caller decision that implicit disposal
cannot make.

The aligned discovery and diagnostic rule is:

- when the receiver exposes no applicable exact pre-unary `dispose` phrase, the
  entry has no disposal call;
- when it exposes a uniquely selected compatible `dispose`, `using` invokes it;
  and
- when `dispose` is present but cannot satisfy the implicit-call contract,
  compilation reports a non-acknowledgeable intent error on that entry.

Incompatibility includes a required result, receiver qualification or transfer
mismatch, ambiguity, an unavailable selected operation, or another condition
that prevents the implicit bare call. `intent<...>{ ... }` cannot legalize or
suppress the error. The programmer must not use `using` for that value, must
invoke and handle the operation explicitly, or must wrap the value in a type
that provides a compatible `dispose`.

Silent omission is deliberately rejected. It would make source request resource
disposal while quietly performing no shutdown despite the type exposing the
named hook. Adding or changing `dispose` on a type may therefore change or
invalidate existing `using` source and is a source-compatibility event.

The implicit call uses ordinary operator-phrase selection and receiver
qualifications; it does not search for `close`, `shutdown`, destructor bodies,
or similarly named functions. Its behavior is as if `dispose receiver` were
called, but the language selects the operation directly rather than constructing
and reparsing hidden source. The one explicit integration is an optional entry,
whose present immediate payload is conditionally offered as the receiver.

### Disposal and destruction sequence

On an exit that performs disposal, the established sequence is:

1. complete the body;
2. destroy body-local values in reverse construction order;
3. invoke compatible enrolled `dispose` hooks in reverse flattened entry order;
4. finish the complete disposal phase; and
5. destroy `using`-owned named values and unnamed temporaries in reverse
   caller-visible entry order.

No `using`-owned entry is destroyed between disposal calls. Borrowed entries are
not destroyed by `using`; their containing lifetime remains responsible for
them. Body-local destruction happens before disposal because the body is the
nested scope and its destructors may still depend on live, undisposed entries.
Every enrolled disposal call nevertheless completes before any `using`-owned
entry is destroyed. Independently evaluated entry declarations normally make
caller-visible entry order and destination construction order coincide. Result
routing does not require those orders to coincide with result construction
before mapping, so the caller-visible entry order is the controlling `using`
destruction rule.

```zax
using resources: (
  first := acquireFirst(),
  second := acquireSecond(first)
) {
  use(second)
}
// dispose second, dispose first, destroy second, destroy first
```

A several-result producer contributes its results to the flattened entry order
in declared result order unless explicit label routing selects another visible
order. Reverse disposal applies to that flattened order.

Result construction before mapping has two owners. The selected visible
prototype determines which result slots are initialized before body entry and
runs those initializers in result declaration order. This includes allocation
forms such as `@`; the caller establishes the call boundary from that prototype.
The implementation controls explicit body construction, while value-bearing
return expressions evaluate left to right. A rebound body and visible prototype
must agree on which slots are already constructed at body entry. Mapping selects
which source establishes which destination; it does not retroactively reorder
any of these construction effects.

Source result slots also retain their own completion rule: after mapping, their
remaining logical slots complete in reverse producer result declaration order.
That source-slot order does not become the destruction order of new
caller-owned destinations.

New destination lifetimes follow the visible order of their owning caller
context. Explicitly reordering source-label mappings can therefore change later
destruction order for newly introduced `using` entries even though the producer
constructs its results identically:

```zax
using (
  resultA: firstA:,
  resultB: secondB: = makeResults()
) {
  use(firstA, secondB)
}
// dispose and destroy secondB before firstA

using (
  resultB: firstB:,
  resultA: secondA: = makeResults()
) {
  use(firstB, secondA)
}
// Prototype- and implementation-defined construction is unchanged.
// Dispose and destroy secondA before firstB.
```

Destinations that already existed retain their existing lifetime order.
Function parameters and outer results retain the order established by their own
prototype declarations rather than adopting source-selection order.

Elision may unify a source result slot with its destination. The unified value
assumes the destination's lifetime and destruction position, and the source slot
has no separate destruction. Elision can therefore intentionally change the
value's destruction order relative to the producer's remaining result slots. It
does not reorder visible-prototype pre-body construction or the implementation's
explicitly sequenced body and return construction. Source slots that remain
distinct still complete in reverse producer result declaration order, while the
unified value is destroyed when its destination owner requires.

Within a `using` result group, `#` retains an anonymous owned value through the
complete consumer and excludes it only from body access and disposal:

```zax
using (
  resultA:,
  # = makeResults()
) {
  use(resultA)
}
// skip disposal of the second result
// destroy the second result, then resultA
```

`#` therefore does not disturb dependencies protected by reverse result and
entry destruction order.

Omission-permitting results from each `dispose` call are still produced. They
complete and are destroyed under ordinary invocation and temporary-lifetime
rules before the next disposal call. The guarantee that no entry is destroyed
between disposal calls applies to enrolled resource entries, not to incidental
temporaries produced by disposal itself.

The current panic model remains a constraint. An unresolved panic is fatal, not
an ordinary unwinding path, so the aligned model does not promise that remaining
disposal calls or destruction complete after a disposal hook or body operation
panics. Async disposal and cancellation remain separate future design.

### Labels, transfers, and the disposal-bypass barrier

`using` may carry an ordinary flow label in the established position:

```zax
using cleanup: (resource) {
  use(resource)
}
```

It is not a loop and is not re-enterable. `next`, `continue`, and `goto` cannot
target it. The mandatory list parentheses make the boundary between
`cleanup:` and any result labels unambiguous.

Normal completion and every ordinary transfer that crosses the `using` boundary
perform disposal before continuing outward:

```zax
using cleanup: (resource) {
  if shouldReturn()
    return // dispose resource, destroy using-owned entries, then return

  if shouldContinue()
    continue outer: // dispose before reaching the outer target
}
```

Only `break cleanup:` explicitly targeting that exact `using` bypasses its
disposal phase:

```zax
using cleanup: (resource) {
  if transferResponsibility(resource)
    break cleanup: // deliberately skip this using's dispose phase
}
// using-owned entries still undergo their ordinary destruction sequence
```

A bare `break` cannot make that decision. When `using` is the nearest enclosing
break boundary, bare `break` is a non-acknowledgeable intent error rather than
silently searching for an outer target:

```zax
while keepWorking() {
  using cleanup: (resource) {
    if done()
      break // intent error: name cleanup: to bypass disposal,
            // or name an outer target to perform disposal before leaving
  }
}
```

`intent<...>{ ... }` cannot acknowledge this error. The programmer must name the
intended control-flow consequence.

An ordinary nearer break target still works:

```zax
using cleanup: (resource) {
  while pending() {
    break // exits the inner while; cleanup: remains active
  }
}
```

Nested `using` statements apply the same crossing rule:

```zax
using outer: (first) {
  using inner: (second) {
    break outer:
    // dispose second because inner: is crossed;
    // bypass dispose first because outer: is explicitly targeted
  }
}
```

The targeted `using` still destroys values it owns after bypassing disposal.
Every crossed non-target `using` completes disposal and destruction before the
transfer arrives at its destination.

### No initializer or post sections

`using` accepts no `;;` header section:

```zax
using (prepare() ;; resource) {
} // error: using has no separate initializer section

using (resource) ;; recordCompletion(resource) {
} // error: using has no post section
```

The resource-entry list already performs ordered acquisition and initialization.
A zero-result setup operation belongs before `using`; a value needed by later
entries can be declared as an enrolled entry. Ordinary normal-completion work
belongs at the end of the body.

If such work must run after other body-local values are destroyed but before
disposal, an inner block states that boundary:

```zax
using (resource) {
  {
    temporary := prepare(resource)
    use(temporary)
  }

  recordCompletion(resource)
}
```

Work that must occur as part of disposal belongs in `dispose` or in a wrapper
that provides the required compatible operation. Adding initializer or post
sections would blur `using` with explicit `scope`, make a skippable post look
like guaranteed disposal, and obscure the construct's distinct resource-list
and exit semantics.

### Relationship to `scope`

Explicit `scope` remains appropriate for an initialized conditionless flow
region with optional post work and deliberate body re-entry. Its post is not
cleanup:

```zax
scope connection := connect() ;; connection.close() {
  if shouldStop()
    return // close is skipped under established post rules
}
```

Redesigned `using` instead makes the possible disposal cost and obligation
visible at the construct:

```zax
using cleanup: (connection := connect()) {
  if shouldStop()
    return // compatible dispose runs before the return
}
```

It has no repeated entry and no normal transfer that accidentally skips
disposal. A nested block or `scope` remains the right tool when a programmer
only wants bounded destruction and no structural `dispose` phase.

### Disposition of legacy `using value own`

Legacy:

```zax
using value own := makeValue() {
  print(value1)
  print(value2)
}
```

appears to inject or expose members of `value` as unqualified names. That
behavior is unrelated to scoped disposal and is not part of redesigned `using`.
The resource behavior uses explicit member access:

```zax
using (value := makeValue()) {
  print(value.value1)
  print(value.value2)
}
```

The aligned disposition is:

- retire the `using value own` spelling;
- do not reinterpret `own` as cleanup responsibility;
- preserve the member-exposure pressure in
  [owned composition and exposed identity surfaces](../raw/owned-composition.md),
  subject to later review of whether lexical exposure needs a distinct owner;
  and
- keep callable capture separate in
  [function composition and chaining](../raw/function-composition-and-chaining.md).

This legacy `own` disposition belongs in the project record and its future raw
owner. Promoted `using` documentation should not mention `own`, even as a
rejected alternative, because it has no applicability to the retained
resource-lifetime operation.

### Aligned design status and remaining boundaries

The programmer-visible `using` model is aligned for the current conceptual
scope:

- mandatory resource-list parentheses;
- no `;;` initializer or post;
- source-order evaluation and source-result-first mapping;
- label-based inferred or typed destination naming for several producer results;
- visible-prototype-controlled pre-body result construction,
  implementation-controlled body and return construction, reverse-declaration
  completion of distinct source result slots, caller-visible destination-owner
  destruction order, and elision that moves a unified value into its destination
  lifetime;
- `#` retaining an unnamed owned lifetime while suppressing body access and
  disposal;
- one-layer conditional disposal participation for optional payloads;
- ordinary value, reference, qualification, and transfer behavior;
- body-local destruction before reverse disposal;
- destruction of owned entries only after every disposal call;
- ordinary non-protected `dispose` selection and result omission;
- a non-acknowledgeable intent error for an incompatible present `dispose`;
- `duplicate-resource-enrollment` for a known repeated place and
  `repeated-resource-expression` for a repeated producing expression;
- individual or enclosing acknowledgement of one or several occurrences of the
  same category;
- one non-reentrant body;
- and explicit labeled `break` as the sole ordinary-flow disposal bypass.

Exact formal grammar productions and diagnostic identifiers remain future
specification work. They do not leave a competing conceptual interpretation.
Async cancellation, exception-like recovery, and generalized cleanup hooks
remain separate concerns; the current fatal-panic boundary continues to apply.

### Adjacent boundaries and likely ownership

- [`language/core-flow-control.md`](../../language/core-flow-control.md) likely
  owns crossing transfers, explicit `break` targeting, the bare-target barrier,
  and `using`'s non-reentrant control behavior.
- `language/using.md` should be the cohesive owner because one language
  construct owns the complete concern. The initially promoted
  `language/scoped-resource-use.md` name is needlessly indirect and should be
  replaced during the corrective promotion.
- [`language/operator-phrases.md`](../../language/operator-phrases.md) and
  [`language/operators.md`](../../language/operators.md) own ordinary
  receiver-side `dispose` declaration, discovery, selection, qualification, and
  result compatibility at their shared depths.
- [`language/function-invocation.md`](../../language/function-invocation.md) owns
  source-first result routing, producer result-slot lifetime, destination
  mapping, elision constraints, and required versus omission-permitting result
  acknowledgement.
- [`language/declarations-and-bindings.md`](../../language/declarations-and-bindings.md)
  owns named entry declaration and visibility behavior.
- [`language/construction-and-destruction.md`](../../language/construction-and-destruction.md)
  owns terminal destruction; arbitrary explicit local destruction is not part
  of the `using` model.
- [`language/lifetimes-and-references.md`](../../language/lifetimes-and-references.md)
  owns borrowed fixed-place references, successor observation, and temporary
  lifetime.
- [`language/optional-values.md`](../../language/optional-values.md) owns
  one-layer presence, boxed access, wrapper ownership, and boxed destruction;
  `using` owns conditional disposal participation at that boundary.
- [`language/source-structure.md`](../../language/source-structure.md) will need
  to own mandatory resource-list parentheses, comma-list continuation, the
  label/list boundary, and body presentation.
- [`flow-control.md`](../../flow-control.md) should eventually lose or be
  rewritten from the legacy always-true `using` account after its useful
  evidence is dispositioned.
- Async cancellation, exception-like recovery, and generalized cleanup hooks
  remain outside this operation. Any concrete future pressure must receive a
  live indexed destination before archival.

This ownership map is refined by the documentation-fit dry run below.

## Dispositions and promotion dry run

### Result: PASS

The dry run was revised on 2026-09-09 after post-promotion review exposed
cross-cutting result-routing, result-lifetime, optional, acknowledgement, and
owner-naming consequences. Every revised finding has one lasting owner, the
complete operation has a cohesive human reading path, legacy and raw evidence
have explicit dispositions, and the exact corrective promotion set is
identified below.

The two acknowledgement-required categories are:

- `duplicate-resource-enrollment` for a later entry known to enroll a place
  already enrolled by an earlier entry; and
- `repeated-resource-expression` for a later repeated resource-producing
  expression whose separate evaluations may produce distinct results.

Each category encloses the later complete resource entry or producer group that
creates the repetition. A known duplicate place uses
`duplicate-resource-enrollment` when both categories could describe the source.
The acknowledgement retains every evaluation, enrollment, disposal call, and
ordinary destruction consequence.

One surrounding `intent<category>{...}` may acknowledge several matching
occurrences in its complete payload; individual acknowledgement remains valid.

PASS confirms the revised documentation fit only. It does not authorize the
corrective promotion.

### Proposed structure and reading path

Use one cohesive owner:

```text
language/using.md
```

The document should teach the complete `using` operation under the title
**Zax `using`**. The construct-based filename is more direct and discoverable
than the initially promoted `language/scoped-resource-use.md`. Subject-based
filenames remain appropriate when no single language construct owns a concern.
A dedicated owner is justified because a programmer would otherwise have to
reconstruct `using` from flow targeting, result mapping, references, optional
values, operator phrases, destruction, source structure, and intent diagnostics.

The human reading path should be:

```text
index.md
-> language/using.md
-> focused links to existing owners for ordinary result mapping,
   references, optional presence, operator selection, destruction,
   and flow targeting
```

The new owner should begin with ordinary source and the end-of-use mental model,
then layer entry forms, `dispose` compatibility, ordering, transfers and bypass,
diagnostics, costs, formatting, source stability, and deferred boundaries. It
must not reproduce the discovery chronology or mention legacy `own`.

This working record is dense design evidence, not source text for promotion.
Promotion must rewrite the material as progressive human teaching: establish one
mental model at a time, use valid examples before edge-case matrices, explain why
visible caller order matters, and route supporting detail to its existing owner.
Completeness and factual precision remain required, but they must not be achieved
by copying compressed working-record prose into programmer-facing pages.

No new directory or secondary index is needed. `index.md` remains the public
router. `project/README.md` remains the numbered-work router and changes only
during later closure.

### Finding-to-owner map

| Aligned finding | Lasting owner and integration |
| --- | --- |
| `using [label:] (resource-entry-list) body` is the complete source shape | `language/using.md`; `language/source-structure.md` owns token, list-continuation, delimiter, label-boundary, and body presentation |
| Parentheses are always required and are mapping-list rather than expression-grouping delimiters | New owner teaches meaning; `language/source-structure.md` owns presentation; `language/function-invocation.md` owns the reused expression-versus-result-sequence distinction |
| `using` has no initializer or post `;;` section | New owner owns the construct rule; `language/core-flow-control.md` and `language/source-structure.md` contrast their existing `;;` schemas without treating `using` as one |
| Entries evaluate once in source order and may introduce named values, unnamed value results, or borrowed places/reference results | New owner applies the rules; `language/declarations-and-bindings.md`, `language/function-invocation.md`, and `language/lifetimes-and-references.md` retain their general authority |
| Every bare producer result is enrolled; structured values remain one result; `#` suppresses naming and disposal while retaining its natural complete-consumer lifetime and destruction position | New owner owns enrollment; `language/function-invocation.md` owns result shapes, complete-consumer lifetime, mapping, labels, and discard |
| Explicit result routing is source first and destination second in every context; a destination may be an inferred name, typed declaration, existing place, parameter/result label, or discard | `language/function-invocation.md` owns the general routing direction and cursors; new owner and `language/declarations-and-bindings.md` own their local destination forms |
| Bare `:` supplies a destination and `#` does not during function result routing; in `using`, both establish the same anonymous entry lifetime but only bare `:` participates in disposal; outside mapping, equivalent anonymous declarations may use either spelling without changing value behavior | `language/function-invocation.md` owns function routing and complete-consumer behavior; new owner owns `using` disposal participation; `language/declarations-and-bindings.md` owns ordinary anonymous declarations |
| The selected visible prototype controls pre-body result construction, while the implementation controls explicit body and return construction; distinct source slots complete in reverse result declaration order; each destination owner establishes later destruction from its own visible order; elision unifies a source slot with a destination and moves destruction to the destination lifetime | `language/function-invocation.md` owns result construction, mapping, source-slot completion, destination order, and elision; construction and lifetime owners retain their general rules |
| A borrowed entry follows one fixed place and observes a successor in a varying place | `language/lifetimes-and-references.md`; new owner gives the resource-specific consequence |
| One optional layer participates conditionally: absence skips disposal, presence offers the immediate boxed value, and wrapper ownership remains unchanged | New owner owns conditional resource participation; `language/optional-values.md` owns presence, boxed access, depth, and destruction |
| Body locals are destroyed first, then compatible hooks run in reverse flattened entry order, then owned entries are destroyed in reverse construction order | New owner owns the complete operation; `language/construction-and-destruction.md` owns ordinary destruction and records the integration boundary |
| Omitted `dispose` results follow ordinary call and temporary destruction rules between disposal calls | `language/function-invocation.md` and `language/lifetimes-and-references.md`; new owner states the sequencing consequence |
| `dispose` is an ordinary non-protected receiver-owned pre-unary phrase selected as if `dispose receiver` were called | `language/operator-phrases.md` and `language/operators.md` retain general declaration and selection authority; new owner owns structural recognition by `using` |
| `using` imposes no `readonly` or `writable` requirement | `language/qualifiers.md` retains qualification meaning; new owner shows both representative forms and applies ordinary selection |
| No applicable `dispose` means no call; a present incompatible `dispose` is a non-acknowledgeable intent error | New owner owns the resource diagnostic; `language/intent-acknowledgements.md` owns the non-acknowledgeable classification |
| Recognizable repeated enrollment is acknowledgement-required and remains semantically repeated after acknowledgement | New owner owns the resource consequence; `language/intent-acknowledgements.md` owns the categories, per-occurrence enclosure, and grouped same-category acknowledgement |
| `using` is non-reentrant; `next`, `continue`, and `goto` cannot target it | New owner owns construct eligibility; `language/core-flow-control.md` integrates it into shared targeting |
| Normal completion and outward transfers run disposal; only `break label:` explicitly targeting that exact `using` bypasses it | New owner owns the bypass rule; `language/core-flow-control.md` owns target lookup and crossing transfers |
| A bare `break` stopped by a nearest `using` is a non-acknowledgeable intent error rather than an outward retarget | `language/core-flow-control.md` owns bare-target search and barrier behavior; new owner teaches the local consequence |
| Fatal panic is not an unwinding path; async disposal and generalized cleanup remain separate | New owner states the boundary; existing panic, indexed async, and legacy `defer` raw input remain their current destinations |
| Legacy `using value own` has no place in the retained resource operation | No mention in promoted `using` teaching; preserve only the independently useful member-exposure evidence in `project/raw/owned-composition.md` |

### Exact proposed promotion change set

The minimal coherent corrective promotion is:

1. **Replace `language/scoped-resource-use.md` with `language/using.md`.**
   - Preserve the complete human-facing model under the direct construct name.
   - Correct source-first result routing, typed destinations, retained `#`
     lifetime, optional participation, and result construction/destruction
     distinctions.
   - Contrast anonymous `:` enrollment with `#`: both use the same entry
     lifetime and elision model, but only `:` participates in disposal.
   - State conceptual status and no implementation.
   - Own `using` entry enrollment, structural `dispose` recognition, the complete
     disposal sequence, and using-specific transfer behavior.
   - Hand ordinary mapping, reference, qualification, operator-selection,
     destruction, source, and intent mechanics to their existing owners.
2. **Update `index.md`.**
   - Add the new owner to **Start here** and **Current conceptual design**.
   - Route scoped resource use directly to it.
   - Change the legacy flow-control annotation so it names only the remaining
     value-polymorphism input.
3. **Update `flow-control.md`.**
   - Add a current-owner route for scoped resource use.
   - Remove the complete legacy `using` section after its resource intent is
     consumed and its independent `own` evidence is moved.
   - Revise the legacy-remainder introduction to describe only value-polymorphic
     function input.
4. **Update `language/core-flow-control.md`.**
   - Add `using` to label placement and transfer-target teaching.
   - Add its bare-`break` barrier, explicit labeled bypass, crossing-transfer
     disposal, and ineligible re-entry behavior.
   - Update unwinding, costs, diagnostics, source stability, metadata, and the
     future-work boundary without duplicating the complete resource model.
5. **Update `language/source-structure.md`.**
   - Add mandatory `using (...)` list delimiters, comma continuation, label/list
     separation, expression-grouping contrast, and body presentation.
   - State explicitly that `using` accepts no `;;` sections.
   - Add applicable malformed-list and separator diagnostics and metadata
     handoff.
6. **Update `language/function-invocation.md`.**
   - Change explicit routing pairs consistently from destination/source to
     source/destination order across calls, captures, returns, and examples.
   - Define typed destination declarations after source-result selectors.
   - Distinguish bare `:` from `#` in result routing, including positional
     destination fulfillment, anonymous typed intermediates, and discard.
   - Separate visible-prototype-controlled pre-body result construction,
     implementation-controlled body and return construction,
     reverse-declaration completion of distinct source result slots,
     caller-visible destination ordering, destination-owner destruction, and
     elision-driven lifetime unification.
   - Include valid contrasting examples in which the same producer results map
     to new caller destinations in different visible orders and therefore
     produce different destination destruction orders without changing
     prototype- or implementation-defined construction.
   - Show that existing destinations retain their established lifetimes and that
     parameter and outer-result destinations use their owning prototype
     declaration order rather than call-site source-selection order.
   - Teach an elision case in which one result is unified with a longer-lived
     outer destination: the unified result has no separate source-slot
     destruction, another distinct source slot may therefore be destroyed first,
     and the unified result is destroyed later at its destination position.
   - Preserve the visible prototype's pre-body construction and the
     implementation's explicitly sequenced body and return construction while
     showing that elision can intentionally change overall destruction order.
   - Clarify that `#` suppresses access or outward mapping without independently
     shortening the result's complete-consumer lifetime or changing its ordinary
     source-slot destruction position; distinguish the anonymous caller-ordered
     entry created by `#` in a `using` result group.
   - Identify the `using` list as a mapping-capable result consumer.
   - Route enrollment and using-specific destination-name behavior to the new
     owner.
7. **Update `language/declarations-and-bindings.md`.**
   - Add `using`-entry declaration visibility and label-based several-result
     binding.
   - Apply source-result-first inferred and typed destination forms.
   - Distinguish direct one-result declaration from prohibited unlabeled
     positional naming of several producer results.
   - Show that ordinary anonymous declarations may use either a missing name or
     `#` without changing construction, lifetime, elision, or destruction.
   - Route complete enrollment semantics to the new owner.
8. **Update `language/optional-values.md`.**
   - Define one-layer conditional `using` participation for an optional payload.
   - Preserve current presence, nested-depth, wrapper ownership, reset,
     replacement, and destruction behavior.
9. **Update `language/lifetimes-and-references.md`.**
   - Add the owned-entry, unnamed-temporary, and borrowed-place life paths.
   - Apply fixed-place successor observation and reference-result lifetime to
     `using`.
   - Clarify complete-consumer lifetime for inaccessible `#` results and
     the destination-lifetime consequences of result elision.
   - Route disposal and transfer behavior to the new owner.
10. **Update `language/construction-and-destruction.md`.**
   - Distinguish ordinary `dispose` from terminal destruction.
   - Record body-local destruction, complete reverse disposal, and later reverse
     destruction of owned entries at the shared lifecycle boundary.
   - Do not introduce arbitrary explicit local destructor calls.
11. **Update `language/operator-phrases.md`.**
    - Add a concise handoff explaining that resource `dispose` remains an
      ordinary non-protected receiver-owned phrase even though `using`
      recognizes its exact form structurally.
    - Leave general phrase declaration and selection unchanged.
12. **Update `language/intent-acknowledgements.md`.**
    - Register `duplicate-resource-enrollment` and
      `repeated-resource-expression`, including their later-entry or
      producer-group enclosure and precedence when both could apply.
    - Establish that one enclosing acknowledgement covers every matching
      occurrence of its category in the payload while unrelated diagnostics
      remain active.
    - Add the incompatible-`dispose` and bare-`break` cases to
      non-acknowledgeable teaching, with complete behavior linked to the new
      resource owner and core flow.
13. **Update `project/raw/owned-composition.md`.**
    - Preserve the smallest useful legacy `using value own` member-exposure
      example and state that it has no resource-lifetime meaning.
    - Keep it out of ordinary programmer-facing reading.
14. **Update `discard.md`.**
    - Route ordinary anonymous declaration behavior to
      `language/declarations-and-bindings.md`.
    - Preserve unrelated legacy local-use and type-level discard evidence.

No corrective promotion change is proposed for `README.md`,
`project/README.md`, `project/raw/README.md`, `language/operators.md`,
`language/qualifiers.md`, `language/terms.md`,
`language/transfer-stances.md`, or
`project/raw/function-composition-and-chaining.md`. Their existing ownership or
routing remains sufficient.

### Deferred and retired material

- The legacy always-true-`if` explanation and unparenthesized resource syntax are
  superseded and should be removed rather than preserved in a public legacy
  path.
- Legacy `using value own` resource syntax retires. Only its independent
  member-exposure pressure moves to indexed owned-composition input.
- General end-of-scope `defer` remains preserved in
  `project/raw/feature-catalog.md`; redesigned `using` neither accepts nor
  rejects that future general facility.
- Async cancellation and suspension remain in indexed async input.
- Exception-like recovery and generalized panic unwinding remain outside the
  current fatal-panic model.
- Generic resource concepts beyond exact structural `dispose` recognition are
  not introduced.
- Compiler lowering, ABI, and implementation strategy remain outside this
  repository.

### Promotion, validation, and closure effects

Promotion should validate:

- one complete owner for scoped resource use;
- the owner path is `language/using.md`, with no remaining
  `language/scoped-resource-use.md` path;
- no current owner or public legacy page teaching the superseded `using` model;
- no promoted `using` documentation mentioning `own`;
- every unmarked example using mandatory parentheses and source-result-first
  routing;
- `#` preserving its complete-consumer lifetime and destruction position while
  remaining absent from disposal;
- one-layer optional participation agreeing with the optional wrapper owner;
- visible-prototype pre-body construction, implementation body/return
  construction, source-slot completion, caller-visible destination destruction,
  and elision described without conflation;
- bare `:` and `#` distinguished in function routing and `using`, while their
  equivalent ordinary anonymous-declaration behavior remains explicit;
- valid examples demonstrating that reordered new destinations change
  destruction order, while existing destinations, parameters, and outer results
  retain the order established by their own lifetime owner;
- elision examples showing the unified result move to its destination
  destruction position, the resulting change relative to remaining source
  slots, and unchanged prototype- or implementation-defined construction order;
- enclosing intent acknowledgement covering all matching occurrences without
  suppressing other categories or non-acknowledgeable errors;
- a progressive explanation whose examples establish the mental model before
  edge cases, rather than a compressed transcription of this working record;
- consistent reverse disposal and destruction ordering;
- working links and heading anchors;
- preserved staged and unstaged review boundaries; and
- the final changed-file set matching the authorized promotion scope.

During promotion, this work item remains active and `project/README.md` does not
change. Only after successful promotion, validation, and separate closure
authorization should `019` move to the archive, the current-work index advance,
and work item `020` be created.

### Initial promotion and required corrective pass

The language maintainer authorized an initial promotion on 2026-09-09. That pass
created `language/scoped-resource-use.md`, integrated the then-aligned local
owner boundaries, routed public readers, consumed the superseded legacy `using`
section, and preserved independent member-exposure evidence. Its focused
validation passed for that change set.

Post-promotion review then established material corrections:

- result routing is source first and destination second across the language;
- a selected source result may directly initialize a differently typed
  destination;
- visible-prototype pre-body construction, implementation body/return
  construction, distinct source-slot completion, destination-owner destruction
  order, and elision-driven lifetime unification are separate;
- `#` suppresses access and disposal without shortening the value's natural
  complete-consumer lifetime;
- an optional contributes its immediate boxed value conditionally;
- one `intent<category>` enclosure may cover several matching occurrences; and
- the direct owner filename is `language/using.md`.

The initial promoted wording therefore requires the exact corrective promotion
listed above before closure. The revised dry run remains PASS because each
correction has one coherent owner and a complete integration path; PASS does not
authorize those edits.

### Corrective promotion execution

The language maintainer authorized the corrective promotion, which was executed
on 2026-09-09 as an unstaged overlay on the staged initial-promotion baseline.
The pass:

- replaced the direct owner with `language/using.md` and removed
  `language/scoped-resource-use.md` from the working tree;
- applied source-result-first routing and typed destinations across calls,
  declarations, returns, and `using`;
- separated result construction, distinct source-slot completion, caller-owned
  destination order, and elision-driven lifetime unification;
- taught `#` as an inaccessible anonymous `using` entry with an ordinary
  complete-consumer lifetime but no disposal;
- integrated one-layer optional participation and grouped same-category intent
  acknowledgement; and
- rewrote the direct owner progressively around the mental model, entry kinds,
  mapping, ordering, flow, diagnostics, and boundaries.

Focused validation passed for the complete corrective overlay:

- every changed-document link and heading anchor resolves;
- fenced code and trailing whitespace checks pass;
- `git diff --check HEAD` passes;
- no current owner retains the old owner path, known destination-first examples,
  or promoted `using value own` teaching;
- the working tree contains `language/using.md` and not
  `language/scoped-resource-use.md`; and
- a separate read-only conceptual and teaching review found no blocking issue.

The staged initial-promotion baseline remains unchanged. Its staged addition of
`language/scoped-resource-use.md` is paired with an unstaged deletion and the
new `language/using.md` remains untracked, preserving the maintainer's requested
before/after review boundary.

The language maintainer subsequently staged the complete corrective promotion
and authorized a further clarification pass. That pass:

- distinguishes bare `:` from `#` in function input and output routing;
- gives anonymous `:` enrollment and disposal-suppressing `#` enrollment the
  same `using` lifetime, destruction position, and elision opportunities;
- establishes equivalent missing-name and explicit-`#` anonymous declarations
  outside mapping-specific contexts;
- distinguishes anonymous typed destinations because their type can introduce
  construction and transfer behavior;
- attributes pre-body result construction to the selected visible prototype and
  body/return construction to the implementation;
- shows existing destinations before routing into them and uses newly mapped
  names inside `using` examples; and
- clarifies that body-local destruction precedes disposal while every enrolled
  disposal call still completes before any enrolled entry is destroyed.

Focused checks for the clarification pass validate its links, heading anchors,
code fences, whitespace, terminology, and staged/unstaged boundary. The
maintainer's complete staged promotion remains unchanged, and the clarification
is an unstaged overlay.

No promotion or clarification pass closes this work item or authorizes archival,
staging, committing, creation of work item `020`, or changes to
`project/README.md`.

### Superseded initial review entry point

The initial reconstruction below is retained as superseded evidence. It
recommended retirement before the distinct structural disposal use case was
supplied and incorrectly generalized destructor-body member destruction into an
ordinary `value.---()` operation. Neither point is part of the aligned model
above.

**Candidate conclusion:** retire legacy `using` as a resource-lifetime
statement. The legacy text gives it only an always-entered initializer scope.
Current Zax already expresses that behavior with an ordinary nested block or an
explicit `scope`, and current lifetime rules already guarantee deterministic
destruction when that lifetime actually exits. The legacy text identifies no
`dispose` operation, resource protocol, cleanup ordering, or additional exit
guarantee that would distinguish `using`.

The strongest remaining uncertainty is what "dispose before destruction" is
intended to mean:

1. If it means ending the resource value's usable lifetime before the enclosing
   function or block ends, a narrower block, explicit `scope`, or explicit
   `resource.---()` already expresses it.
2. If it means invoking a separate resource-state transition while the Zax
   instance remains alive and is later destroyed, Zax currently has no generic
   disposal contract. A type may expose an ordinary operation such as `close()`,
   but a guaranteed implicit call would require new resource-protocol semantics,
   not merely another spelling for `scope`.

For a declared `using` value, the second operation would be followed immediately
by destruction when the statement exits. There is no ordinary source interval
in which the disposed-but-still-live value can be used. A type that must release
an external resource before its members are destroyed can therefore do that at
the start of its destructor body. A separate implicit pre-destructor hook has no
demonstrated programmer-visible benefit unless Zax first establishes a genuinely
distinct disposal protocol.

The immediate maintainer decision is whether that distinction recovers the
original intent. If it does, the candidate direction is to retire `using`. If
there is a use case in which an implicit operation must occur while the instance
remains live and ordinary destruction cannot own it, that use case needs to be
made concrete before `using` can be justified.

#### Recovered evidence

The legacy `using` statement consumed from `flow-control.md` said that `using`
was like an `if` whose condition was always true. It gave an initializer binding
scope but did not say that the language:

- selects or invokes a `dispose`, `close`, or other resource operation;
- treats particular types as resources;
- changes destruction order;
- guarantees an operation on transfers or panic;
- skips the body after fallible acquisition; or
- transfers cleanup responsibility.

Its ordinary examples therefore support scoped naming, not a distinct resource
contract:

```zax
using resource := acquireResource() {
  use(resource)
}
```

Current explicit `scope` already accepts an initializer, an optional post, and
one effective body. Initializer bindings remain live through the complete flow
statement and are destroyed when it exits:

```zax
scope resource := acquireResource() {
  use(resource)
}
```

For straight-line code that needs none of `scope`'s flow-target behavior, an
ordinary nested block is the smaller semantic tool:

```zax
{
  resource := acquireResource()
  use(resource)
}

continueAfterResourceDestruction()
```

This is early destruction relative to the containing function without a
resource-specific statement.

Current lifecycle material also demonstrates explicit destruction:

```zax
resource := acquireResource()
use(resource)
resource.---()

continueAfterResourceDestruction()
inspect(resource) // error: no resident instance exists
```

The explicit destructor call ends the instance; it is not a `Dispose`-like
operation after which the same instance remains ordinarily usable. The current
construction owner tracks an ended lifetime, but it states suppression of later
generated destruction most directly for explicitly destroyed members inside a
destructor. Before promotion, the owner should confirm or clarify the
corresponding automatic scope-exit behavior for an explicitly destroyed local.
The candidate model assumes an ended local is not destroyed a second time.

#### Candidate ordinary-resource model

A resource is not a separate lifetime category merely because its destructor
releases a file, socket, allocation, lock, transaction, or other external state.
It is an ordinary value whose type defines the terminal work performed by
`---`.

For a resource in a `scope` header:

1. acquisition and initialization complete before the binding becomes visible;
2. the header binding remains alive while the body executes;
3. body-local values are destroyed before a normal post or header destruction;
4. applicable transfers destroy every body and nested scope they leave;
5. transfers that exit the complete `scope` destroy the header resource;
6. transfers that re-enter the same `scope` retain the header resource and
   create a fresh body scope; and
7. destruction of several successfully constructed values follows reverse
   construction order.

These are general initialization, flow, and destruction rules. No
resource-specific mechanism is needed:

```zax
scope connection := connect() {
  request := receive(connection)

  if shouldRetry(request)
    continue // request is destroyed; connection stays alive; body re-enters

  process(request)
}
// connection is destroyed only when the complete scope exits
```

This re-entry rule is important when comparing `using` with `scope`. Mapping
legacy `using` mechanically to `scope` is behaviorally harmless when no transfer
targets the new construct, but `scope` is deliberately re-enterable and does not
reacquire its header value. A retained `using` would have to decide whether it
inherits that behavior. Making it a second re-enterable spelling adds no value;
making it non-re-enterable duplicates the ordinary nested block.

Several resources can use ordinary composed initialization:

```zax
scope first := acquireFirst();
  second := acquireSecond() {
  use(first, second)
}
// second is destroyed before first
```

Acquisition itself has no implicit `using` success test. A normally returned
optional or result remains an ordinary returned value that the body must handle
through its own type's operations or surrounding control flow. A panic prevents
normal acquisition completion. The current panic model is fatal rather than an
exception-like unwinding path, so neither `scope` nor a retired `using` promises
cleanup during an unresolved panic.

#### Disposal while an instance remains alive

A type-specific early-release operation can be an ordinary call:

```zax
scope connection := connect() {
  use(connection)
  connection.close()
}
```

If `close()` leaves a valid but closed `connection`, its state, repeated-call
behavior, later destructor behavior, and permitted operations belong to that
type's contract. Calling it explicitly does not create a language-wide resource
concept.

An explicit `scope` post cannot be used as guaranteed disposal:

```zax
scope connection := connect() ;; connection.close() {
  if shouldStop()
    return // close is skipped; ordinary scope destruction still applies
}
```

Posts are normal-completion work. `break`, `continue`, `goto`, `return`, and
panic may skip them according to the current flow rules. Treating a post as
`finally`, `defer`, or guaranteed cleanup would contradict its established
meaning.

If `close()` is mandatory on every actual lifetime exit, the type's destructor
or an owning wrapper can perform it. If future Zax needs a distinct guaranteed
operation that runs before destruction while the instance remains live, that
feature must define at least:

- how the operation is selected and which types satisfy the contract;
- whether it runs on normal exit, each transfer, or fatal panic;
- its order relative to body-local destruction and the resource destructor;
- whether re-entry disposes and reacquires or retains the resource;
- behavior when the operation fails or panics;
- repeated disposal and destructor behavior;
- transfer of cleanup responsibility through `move` or `last`;
- partial acquisition and reverse ordering for several resources; and
- synchronous-only versus future async cleanup.

Those questions cross the stated generic-resource, cleanup-hook, panic, transfer,
and async boundaries. No legacy evidence answers them. Unless a concrete use
case requires this contract, introducing it solely to preserve `using` would
create a large implicit mechanism with no demonstrated advantage over
deterministic destruction.

#### Exit behavior that `using` would not improve

| Event in an explicit `scope` | Body-local values | Header resource | Post |
| --- | --- | --- | --- |
| Normal body completion | Destroyed | Destroyed when the complete scope exits | Runs before header destruction |
| `break` targeting the scope | Destroyed | Destroyed as the complete scope exits | Skipped |
| `return` or outward transfer | Every exited scope is destroyed | Destroyed when crossed | Skipped |
| `next` targeting the scope | Destroyed, then recreated on entry | Retained | Runs |
| `continue` or `goto` targeting the scope | Destroyed, then recreated on entry | Retained | Skipped |
| Unresolved panic | Not an ordinary unwind path in the current model | No scoped cleanup guarantee | Skipped |

A retained `using` that merely repeats these outcomes has no distinct semantic
contract. A retained `using` that changes one of them is a new flow or cleanup
feature and must justify that change explicitly.

#### Candidate disposition of `using value own`

Legacy:

```zax
using value own := makeValue() {
  print(value1)
  print(value2)
}
```

appears to expose members of `value` as unqualified names inside the body. That
is not resource acquisition, disposal, or destruction. The same example's
resource-lifetime behavior is already expressible without injected names:

```zax
scope value := makeValue() {
  print(value.value1)
  print(value.value2)
}
```

The candidate disposition is:

- retire the `using value own` spelling with `using`;
- do not reinterpret `own` as cleanup responsibility;
- preserve its member-exposure intent as future input for
  [owned composition and exposed identity surfaces](../raw/owned-composition.md),
  while recognizing that lexical member injection may ultimately need a
  different owner from type-level owned composition; and
- keep callable capture separate in
  [function composition and chaining](../raw/function-composition-and-chaining.md).

Any future lexical exposure design must address name collisions, eligibility,
qualification, mutation authority, method receiver binding, identity, lifetime,
reflection, and whether exposure is only shorthand for explicit member access.
Those questions do not block retirement of a resource statement because the
qualified form already preserves the shown program's behavior.

#### Candidate source and diagnostic disposition

If `using` retires:

- new teaching should present an ordinary block for a simple bounded lifetime
  and explicit `scope` when its initializer, post, label, or transfer target is
  useful;
- legacy `using value := expression body` has a direct structural migration to
  `scope value := expression body` when no newly available re-entry transfer is
  introduced, or to a nested declaration block when no flow target is wanted;
- legacy `using value own := expression body` has no direct member-injection
  replacement; use explicit `value.member` access;
- diagnostics should name the two migrations rather than imply that a missing
  disposal protocol exists; and
- formatting follows the established `scope` header, continuation, and body
  rules rather than preserving a second format.

No implementation exists, so this is documentation and future source-stability
pressure rather than an implemented compatibility transition. Whether `using`
remains reserved as a word is a separate lexical decision and need not be
decided merely to retire this construct.

#### Decisions requiring maintainer review

1. Does the ordinary lifetime model recover the intended deterministic-resource
   use, including release earlier than an enclosing lifetime?
2. Is there a concrete case where a generated pre-destructor disposal operation
   must occur while the instance remains live and cannot instead be part of its
   destructor, an explicit method call, or an owning wrapper?
3. Should straight-line resource teaching prefer a nested block, reserving
   explicit `scope` for code that needs its header or transfer behavior?
4. Is the current no-unwind fatal-panic boundary acceptable for resource
   cleanup, rather than importing C# exception/`finally` expectations?
5. Should `using value own` be rejected as lexical member injection and its
   remaining intent moved to the existing owned-composition raw input?

#### Known holes and adjacent dispositions

- **Explicitly destroyed locals:** current material clearly makes subsequent use
  invalid, but should state directly how automatic scope-exit destruction treats
  that ended local before this work is promoted.
- **Generic disposal or cleanup hooks:** no such design is assumed. If review
  produces a real use case, it requires an indexed live raw destination and a
  later work item; it cannot remain only in archived `019`.
- **Panic cleanup:** current panic behavior already constrains this candidate.
  Exception-like unwinding or recovery remains outside this item.
- **Async cleanup and cancellation:** no retained synchronous `using` behavior
  should imply an async guarantee. Concrete future pressure requires its own live
  destination.
- **Owned composition and lexical exposure:** the existing owned-composition raw
  input is the likely destination for the legacy `own` evidence, but future work
  must decide whether local unqualified projection is actually the same feature.
- **Callable capture:** already preserved separately in the function-composition
  raw input and should not be pulled back into resource lifetime.

#### Likely lasting ownership if the candidate is accepted

- [`language/core-flow-control.md`](../../language/core-flow-control.md) remains
  the owner of explicit `scope`, posts, transfer targeting, re-entry, and
  completion.
- [`language/declarations-and-bindings.md`](../../language/declarations-and-bindings.md)
  remains the owner of initializer binding visibility.
- [`language/construction-and-destruction.md`](../../language/construction-and-destruction.md)
  remains the owner of automatic and explicit destruction, including the local
  ended-lifetime clarification.
- [`language/lifetimes-and-references.md`](../../language/lifetimes-and-references.md)
  remains the owner of bounded life paths and synchronous temporary lifetime.
- [`language/source-structure.md`](../../language/source-structure.md) remains
  the owner of `scope` header and body formatting.
- [`flow-control.md`](../../flow-control.md) should lose the legacy `using`
  section after all useful `using value own` evidence has moved.
- [`project/raw/owned-composition.md`](../raw/owned-composition.md) should receive
  the smallest useful lexical-exposure evidence if that intent is retained.
- [`project/raw/function-composition-and-chaining.md`](../raw/function-composition-and-chaining.md)
  already owns the separately preserved callable-capture pressure.

This ownership map is preliminary. It is not the required pre-promotion
documentation-fit dry run.
