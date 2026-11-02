# 029: Except result-flow control

| Field | Value |
| --- | --- |
| Status | Historical working record / non-normative / audit-only |
| Work Item | `029` |
| Created | 2026-09-21 |
| Completed | 2026-09-22 |
| Owns | Historical evidence and dispositions from the completed bounded review |
| Does Not Own | Current Zax language design; see `language/except.md` and its integrated current owners |

## Non-authority notice

This file is a historical audit record. It is non-authoritative and excluded
from ordinary onboarding. Current language meaning lives in the promoted
`language/` owners rather than this discussion history.

## Fixed initiating input

This section records the aligned information known when work item `029` was
created. It is intentionally incomplete and must not be rewritten as work
develops.

### Initiating concern

Recover and refine Zax `except` as flow control related to function results.
These controls intentionally route execution through an exceptional result path.
They are not exceptions, stack unwinding, C++-style `throw`/`catch`, or an
alternate panic-recovery mechanism.

The maintainer-supplied
[except notes](../raw/except-maintainer-notes.md) are the latest primary input
for this work item. They may add to, replace, supersede, or reject conflicting
legacy evidence.

Legacy [`except`](../../except.md) contains useful ideas to mine and disposition
by value. It is not authoritative over the refreshed notes merely because it is
older, more detailed, or already checked in. The refreshed model is the
preferred direction unless concrete semantic, cross-feature, teachability, or
feasibility pressure exposes a flaw. Legacy precedence by itself is not such a
flaw.

Current `language/` owners remain current for the concerns they already own.
Any conflict between the refreshed notes and a current owner requires explicit
review.

### Motivating pressure

The work must give programmers one teachable model for:

- which function results can establish an exceptional result path;
- how a caller recognizes, routes, binds, forwards, handles, or deliberately
  ignores that path;
- how ordinary success and exceptional result flow relate to named and several
  results;
- which scopes, posts, disposal operations, and destructors run;
- how result transfer stance, ownership, reference origin, and lifetime remain
  valid;
- how `except` differs from `return`, panic, optional absence, enum/variant
  selection, and ordinary conditional control flow;
- how source exposes exceptional flow and its costs without implying exception
  stack machinery; and
- how adding or changing exceptional results affects source compatibility.

### Known starting boundaries

- C++-style exceptions, `throw`, `catch`, and exception-object hierarchies.
- Stack unwinding as an exception mechanism.
- General panic recovery or resumable panic handling.
- Compiler lowering, hidden control structures, runtime implementation, and ABI
  except where programmer-visible behavior constrains them.
- General algebraic effects, coroutines, async cancellation, or generalized
  pattern matching beyond concrete `except` pressure.

These boundaries do not erase consequences. Preserve every material pressure in
a current owner or indexed raw destination rather than leaving it only in the
eventual archive of this work item.

### Intentionally unresolved framing

- Exact `except` declaration and use-site syntax.
- How a function marks one or several results as exceptional.
- Whether exceptional routing is expression-level, statement-level,
  result-mapping behavior, callable-prototype behavior, or a combination.
- Binding, forwarding, acknowledgement, and completeness rules.
- Interaction with multiple results, defaulted results, optional values, enums,
  variants, references, ownership, and transfer stances.
- Exact scope-exit, post-operation, `using` disposal, and destruction ordering.
- Static diagnostics, source-compatibility policy, and any intent or safety
  categories.
- Lasting documentation ownership and terminology.

### Initial stopping guidance

Stop when the work has:

- established a cold-reader-friendly programmer model with representative
  success, exceptional-result, forwarding, handling, and cleanup examples;
- reconciled result declaration, invocation, routing, transfer, lifetime,
  scope-exit, disposal, panic, safety, and source behavior;
- clearly distinguished exceptional result flow from exception unwinding and
  catch/throw systems;
- dispositioned every useful maintainer-note and reached legacy finding;
- preserved all material deferrals in live owners; and
- completed the required documentation-fit dry run.

Do not promote findings, archive this work item, begin work item `030`, or
design C++-style exception machinery without the separately required
discussion, alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  decision maturity, promotion, deferral, and closure.
- [Except maintainer notes](../raw/except-maintainer-notes.md) - latest
  maintainer-supplied primary input; read and disposition completely after
  assignment.
- [Legacy except](../../except.md) - useful historical result-flow evidence to
  mine by value rather than authority.
- [Function invocation](../../language/function-invocation.md) - owns callable
  results, result slots, result routing, mapping order, completion, and visible
  prototypes.
- [Declarations and bindings](../../language/declarations-and-bindings.md) -
  owns result declaration contexts, labels, initialization, and binding scope.
- [Core flow control](../../language/core-flow-control.md) - owns effective
  bodies, `return`, transfers, posts, labels, and scope exit.
- [Construction, replacement, and destruction](../../language/construction-and-destruction.md) -
  owns result/member completeness, destruction order, abrupt scope exit, and the
  no-unwinding lifecycle boundary.
- [Transfer stances](../../language/transfer-stances.md) - owns result and
  source `copy`/`deep`/`move`/`last` behavior.
- [Lifetimes and references](../../language/lifetimes-and-references.md) - owns
  result/reference origin, call completion, temporary lifetime, and escape.
- [Safety and analysis](../../language/safety-and-analysis.md) - owns the panic
  boundary, proof, unsafe responsibility, and source-stability model.
- [Source structure](../../language/source-structure.md) - owns source
  continuation, bodies, mapping presentation, and contextual recognition.
- [Language-design terms](../../language/terms.md) - owns cross-cutting result,
  completion, panic, lifetime, and flow vocabulary.

### Consequence-driven

- [Zax `using`](../../language/using.md) when exceptional flow crosses resource
  enrollment, disposal, or disposal bypass.
- [Optional values](../../language/optional-values.md),
  [enums](../../language/enums.md), [variants](../../language/variants.md), and
  [switch](../../language/switch.md) when exceptional results use or resemble
  those state/selection mechanisms.
- [Qualifiers](../../language/qualifiers.md) when routing changes result,
  destination, or reference authority.
- [Intent acknowledgements](../../language/intent-acknowledgements.md) and
  [raw analysis controls](../raw/analysis-controls.md) when suspicious omission,
  unchecked routing, or failure controls become concrete.
- [Asynchronous execution](../raw/async.md) only when suspension, cancellation,
  or async result propagation creates a material constraint.
- Other legacy error, result, function, flow, or cleanup material only when a
  concrete question cannot be resolved from the refreshed notes, current
  owners, and `except.md`.

### Audit-only

- Archived work only when a concrete provenance or regression question cannot
  be answered from current owners and live raw input.

## Initial working posture

Begin with concrete source for:

1. a function producing an ordinary success result and one exceptional result;
2. a caller handling both paths locally;
3. a caller forwarding the exceptional path;
4. several results where only one path is exceptional;
5. a reference- or ownership-bearing exceptional result; and
6. cleanup showing exactly which locals, posts, disposal operations, and
   destructors run.

For each example, state which result slots are constructed, which body or route
executes, what transfers, what is destroyed, and why no exception stack
unwinding or catch search occurs.

Use the refreshed maintainer notes as the preferred primary model. Preserve
current owner constraints and treat legacy material as evidence rather than
authority.

## Working record

The current model below contains aligned findings for this work scope. They
remain non-authoritative until promotion. Illustrative source preserves current
surrounding Zax grammar, but exact `except` and `catch` grammar remains under
review.

The initial reconstruction treated an `except` result as an always-existing
result value whose `?` or `!` operation selected a handler. Review exposed a
more coherent result-flow model. This record replaces that reconstruction
rather than layering corrections over it.

### Review entry point

#### Current programmer model

A callable completes in exactly one of these ways:

- **success:** every ordinary result is published; or
- **one named exceptional outcome:** exactly that declared `except` result is
  published.

```zax
readCount final : (
  count : Integer,
  failure except : MyReadError
)(
  source : String readonly &
) = {
  if !sourceIsReadable(source)
    except failure: makeReadError(source)

  return parseCount(source)
}
```

`return` selects the success outcome. `except failure: ...` constructs and
selects the `failure` outcome. A function with several `except` results still
selects only one outcome per completion.

The caller handles the named outcome locally:

```zax
showCount final : ()(
  source : String readonly &
) = {
  count := readCount(source) catch failure {
    report(failure)
    return
  }

  print(count)
}
```

On success, `count` is constructed and enters the caller's scope. On
`failure`, no `count` instance enters that scope; the named handler runs
instead. The handler's `return` is ordinary Zax flow control.

There is no `?` or `!` test. The function's selected completion outcome tells
the caller which path occurred. There is no thrown object, handler search,
stack unwinding, rollback, or panic recovery.

#### The deciding change from the refreshed notes

An `except` result is not an ordinary always-constructed result whose value is
tested for presence. It is one named alternative in the callable's completion
contract.

Consequently:

- an `except` result needs no default inactive value;
- its type needs no `?` or `!` operation;
- ordinary success leaves every `except` result unconstructed unless an
  unrelated prototype initializer deliberately constructed one provisionally;
- exceptional completion need not construct ordinary results;
- a directly stored error, non-default-constructible error, reference, optional,
  variant, or owning pointer can all be exceptional payloads under their
  ordinary type, transfer, and lifetime rules; and
- only one `except` result can be selected by one function completion.

The implementation may use a control edge, discriminator, flag, or another
representation to communicate the selected outcome. The language-level term in
this record is **completion outcome**. No particular lowering or ABI is
promised.

#### Immediate review focus

The central semantics are aligned. The next review should refine exact source
shape and the remaining lifecycle ordering without reopening these points:

- `except` no longer captures an arbitrary result;
- `except` at a call site mechanically forwards one named exceptional outcome;
- `catch` handles one named exceptional outcome locally;
- source-to-destination remapping remains explicit;
- exceptional outcomes cannot be omitted, discarded with `#`, or forwarded by
  an unwritten same-name rule;
- success destinations are published only on the success path; and
- nonselected live result slots are destroyed by their current provisional
  owner.

### Teach the ordinary use first

The promoted explanation should start with source like the preceding
`readCount` and `showCount` examples. A reader should learn four facts before
encountering elision, completion selectors, or cleanup ledgers:

1. the prototype names ordinary results and exceptional outcomes;
2. `return` chooses success;
3. producer `except` chooses one exceptional outcome; and
4. a caller must catch or forward every declared exceptional outcome.

The feature should initially read as a small extension of Zax result flow, not
as an exception subsystem and not as a compiler implementation mechanism.

### Producing success or one exceptional outcome

#### Success

An ordinary `return` completes every ordinary result:

```zax
parseRecord final : (
  record : MyRecord,
  consumed : IndexSize,
  failure except : MyParseError
)(
  source : Bytes readonly &
) = {
  // ...
  return parsedRecord, consumedCount
}
```

The success shape is `(record, consumed)`. `failure` is not a third success
value and does not require default construction.

This revises the current invocation invariant that every declared result slot
must be complete on every normal function exit. The refined invariant is:

> Every slot in the selected completion outcome must be complete, and no live
> slot outside that outcome may escape the invocation.

#### Exceptional completion

Producer `except` names the exceptional destination explicitly:

```zax
if malformed(source)
  except failure: makeParseError(source)
```

The value uses ordinary direct construction, qualification, transfer, and
selection rules. There is no constructor-graph search for a “best” exceptional
slot. If several exceptional results share the same type, the written result
label still decides the outcome.

A producer cannot activate several exceptional outcomes:

```zax
load final : (
  value : MyValue,
  missing except : MyMissing,
  denied except : MyDenied
)() = {
  if sourceIsMissing()
    except missing: makeMissing()

  if accessIsDenied()
    except denied: makeDenied()

  return makeValue()
}
```

Each reached `except` leaves the function. No path publishes both `missing` and
`denied`.

#### No Boolean activation protocol

The earlier candidate required:

```zax
?failure
```

or complement fallback through `!`. That protocol is superseded for
exceptional routing.

If the payload type itself is optional, pointer-like, variant-like, or otherwise
presence-bearing, its own `?` operation retains its ordinary meaning inside the
selected exceptional branch:

```zax
failure except : MyReadError?
```

Selecting `failure` says that the exceptional outcome occurred. It does not say
that the optional payload is present. A handler must still test optional
presence before postfix access when the API deliberately permits an absent
payload.

This separates two questions that the earlier notes combined:

- which callable outcome occurred; and
- what state the selected payload contains.

### Handling one named outcome with `catch`

`catch` attaches a local body to one named exceptional result:

```zax
scope read_attempt: {
  count := readCount(source) catch failure {
    report(failure)
    break read_attempt:
  }

  ++count
}
```

On success:

1. `readCount` constructs `count`;
2. the declaration completes;
3. `count` enters scope; and
4. `++count` is valid.

On `failure`:

1. no `count` instance is published;
2. the `failure` handler runs;
3. `break read_attempt:` leaves the containing scope; and
4. the path cannot reach `++count`.

This is explicit handling, not discard. The selected exceptional payload
remains alive through its handler use and is destroyed at its ordinary
call-completion boundary unless it is transferred elsewhere.

#### Success bindings are unavailable in the handler

The ordinary destination is not visible until its initializer succeeds:

```zax
count := readCount(source) catch failure {
  use(count) // error: count was not constructed on this path
}
```

This follows the existing declaration rule: a binding enters scope only after
initialization completes.

#### Handler fallthrough must preserve definite construction

For a value-producing declaration, a handler cannot fall through into source
that requires the missing success value:

```zax
count := readCount(source) catch failure {
  report(failure)
}

++count // error: the failure path did not construct count
```

The ordinary repair is a transfer:

```zax
count := readCount(source) catch failure {
  report(failure)
  return
}
```

A future handler form may explicitly provide replacement success results. That
mechanism is not established yet and must not be inferred from ordinary handler
fallthrough.

A zero-result operation has no missing success destination. A named handler may
therefore deliberately convert that one exceptional outcome into ordinary
continuation:

```zax
performNotification() catch unavailable { }
continueWork()
```

This is still explicit handling of `unavailable`, not silent omission.

#### Handler binding and remapping

The selected result can retain its source label:

```zax
value := operation() catch failure {
  report(failure)
  return
}
```

Source-to-destination order supports an explicit rename:

```zax
value := operation() catch failure: localFailure: {
  report(localFailure)
  return
}
```

A complete typed destination requests ordinary construction:

```zax
value := operation() catch failure: \
  report : MyFailureReport {
  submit(report)
  return
}
```

The exact multiline syntax is illustrative. The semantic order is not:

```text
source exceptional result -> handler destination
```

Typed mapping may copy, transfer, convert, allocate, invoke user code, or fail
under the ordinary selected operation. `catch` does not create an
exception-object conversion system.

### Forwarding one named outcome with `except`

A function that cannot handle an exceptional outcome declares its own
exceptional result and forwards explicitly:

```zax
loadCount final : (
  count : Integer,
  outerFailure except : MyReadError
)(
  source : String readonly &
) = {
  count := readCount(source) except failure: outerFailure:
  return count
}
```

The two labels have distinct roles:

```text
failure:      result selected from readCount
outerFailure: result selected from loadCount
```

If `readCount` succeeds, `count` initializes and execution reaches
`return count`. If it selects `failure`, the forwarding operation constructs or
maps `outerFailure`, completes `loadCount` through that exceptional outcome,
and does not execute the later return.

Even when both declarations use the same label, forwarding remains written:

```zax
count := readCount(source) except failure: failure:
```

No omitted syntax silently forwards by matching names. The repetition makes the
cross-call control transfer visible and keeps adding an exceptional result from
silently changing existing source.

Forwarding is not capture. This refreshed-note form is removed:

```zax
myResult := myFunc() except failure: myFailure:
// not a local result-capture operation
```

Ordinary result routing remains the way to capture several ordinary values.

#### Propagation follows immediate call boundaries

Without local recovery, an exceptional outcome passes through each callable
boundary:

```text
leaf selects one except outcome
-> its immediate caller forwards that named outcome
-> that caller performs its own cleanup and returns its named outcome
-> the next caller catches or forwards
-> propagation stops at the nearest handler
```

This is not a runtime search for an outer handler. Every callable prototype and
every call site exposes the applicable exceptional outcomes. A compiler may
optimize mechanically equivalent forwarding only when result construction,
destruction, disposal, transfer, and visible ordering remain unchanged.

### Every exceptional outcome must be accounted for

A caller must handle or forward each declared exceptional outcome that can
leave the call.

```zax
value := load() \
  catch missing {
    handleMissing(missing)
    return
  } \
  except denied: outerDenied:
```

This layout is illustrative. It shows the semantic requirement:

- `missing` is handled locally;
- `denied` is forwarded explicitly; and
- no exceptional outcome is omitted.

Adding an exceptional result to `load` makes this call incomplete until source
handles or forwards the new outcome.

#### No discard

An exceptional result cannot use declaration-side or call-site `#`.

```zax
failure except # : MyError // error
failure: #                 // error for an exceptional outcome
```

Discarding a payload would not decide where execution continues when ordinary
results were never constructed. Silent continuation would make later success
uses invalid, while silent forwarding would hide a cross-function control
effect.

A handler may choose not to inspect the payload while still making control
explicit:

```zax
scope operation_attempt: {
  value := operation() catch failure {
    break operation_attempt:
  }

  use(value)
}
```

The payload is destroyed normally. The outcome was handled, not discarded.

Ordinary `#` behavior for nonexceptional result routing remains a separate
invocation concern and is not changed by this finding.

#### No implicit catch-all

This form is not viable:

```zax
scope operation_attempt: {
  value := operation() break operation_attempt:
}
```

It would silently handle every exceptional outcome and let a later API addition
change behavior without a named source acknowledgement.

The source names the outcome:

```zax
scope operation_attempt: {
  value := operation() catch failure {
    break operation_attempt:
  }
}
```

No catch-all syntax is established. If future pressure justifies one, it must be
explicit and must preserve source-compatibility review when a callable adds an
outcome.

### Completion shapes and constructedness

#### Selected outcomes are fixed by the prototype

For:

```zax
makeValue final : (
  value : MyValue,
  count : Integer,
  failure except : MyFailure
)() = {
  // ...
}
```

the prototype declares two completion shapes:

```text
success -> value, count
failure -> failure
```

It does not declare a runtime-dependent subset such as “failure plus whichever
ordinary results the body happened to construct.” Incidental body state cannot
become caller-visible API.

If an API needs partial data together with an error, that data must be part of
the declared exceptional payload or a future explicitly declared multi-value
exceptional shape. It cannot leak from provisional ordinary slots.

#### General cleanup rule

The aligned rule is:

> Before completing with one outcome, every live result slot outside that
> outcome must be destroyed by its current provisional owner.

The rule is symmetric:

- exceptional completion destroys any live ordinary result not selected;
- success destroys any live exceptional result not selected;
- selecting one exceptional result destroys any live competing exceptional
  result; and
- an unconstructed nonselected slot needs no destruction.

The compiler statically tracks the result-slot lifecycle along each body path.
Opaque low-level construction may require the applicable narrow lifecycle
assertion, but uncertain constructedness cannot cross the callable boundary.

#### Prototype-preinitialized results

A result initializer in the visible prototype constructs that slot before body
entry:

```zax
prepare final : (
  value : MyValue = :,
  failure except : MyFailure
)() = {
  // ...
}
```

Caller-side invocation machinery establishes `value`, so it knows the slot is
live on every path. If `prepare` succeeds, the success outcome publishes it. If
`prepare` selects `failure`, caller-side call-completion machinery destroys the
nonselected `value`.

The callee does not destroy that caller-preinitialized slot merely because it
chooses an exceptional outcome. It may have mutated the live result under its
ordinary authority, but cleanup ownership remains with the invocation side that
preinitialized it.

A prototype may also preinitialize an exceptional result. That creates a live
provisional slot on every path:

```zax
failure except : MyFailure = :
```

If success is selected, invocation machinery destroys it. If `failure` is
selected, it may be published without reconstructing the already-live slot.
Exact producer syntax for selecting and preserving a preinitialized
exceptional slot remains open.

Prototype initialization controls body-entry state and visible cost. It does
not silently add that result to every completion outcome.

#### Body-constructed results

A result constructed in the implementation remains the callee's provisional
responsibility until the function commits an outcome:

```zax
prepare final : (
  value : MyValue,
  failure except : MyFailure
)() = {
  value.+++(makeInput())

  if laterCheckFails()
    except failure: makeFailure()

  return
}
```

If `laterCheckFails()` selects `failure`, the callee destroys the live
provisional `value` before completing exceptionally. If success is selected,
`value` is published and cleanup responsibility transfers outward with that
success.

This result cleanup is lifecycle work inside one function completion. It is not
stack unwinding.

### Elision uses conditional commitment

Deep result elision remains possible because storage existence and value
lifetime are distinct.

```zax
leaf final : (
  value : MyValue,
  failure except : MyFailure
)() = {
  // ...
}

middle final : (
  value : MyValue,
  failure except : MyFailure
)() = {
  value := leaf() except failure: failure:
  return value
}

outer final : ()() = {
  value := middle() catch failure {
    report(failure)
    return
  }

  use(value)
}
```

The outer caller may reserve final `MyValue` storage and make that storage
available through `middle` to `leaf`. Neither inner callable needs to know how
many call levels share the storage.

On success:

```text
outer reserves raw storage
-> middle forwards that storage
-> leaf constructs MyValue directly there
-> leaf commits success
-> middle commits success
-> outer publishes the value binding
```

On `failure`:

```text
outer reserves raw storage
-> middle forwards that storage
-> leaf selects failure without publishing MyValue
-> middle explicitly forwards failure
-> outer enters its failure handler
-> the value binding never enters scope
```

Raw reserved storage is not an instance and requires no destructor.

The elision rule is:

> Elision may unify a result slot with an outer destination's storage, but the
> destination lifetime becomes caller-visible only when the completion outcome
> publishes that result.

#### Provisional cleanup responsibility moves outward

If `leaf` constructs `value` and then selects `failure`, `leaf` destroys the
provisional instance before reporting the outcome.

If `leaf` commits success but `middle` later selects one of its own exceptional
outcomes, provisional cleanup responsibility has transferred to `middle`.
`middle` destroys the live value even though its bytes occupy `outer`'s reserved
storage.

Conceptually:

```text
constructing operation owns provisional cleanup
-> successful inner completion transfers responsibility outward
-> successful outer completion transfers it again
-> final caller publishes the binding
```

This is semantic lifecycle tracking, not a required runtime ownership object or
one initialized flag per result.

Elision remains optional. A wrapper may become an elision barrier when it:

- transforms rather than mechanically forwards a value;
- requires an independently observable intermediate lifetime;
- maps into an already-live destination through assignment;
- changes required construction or destruction order; or
- cannot preserve alias, transfer, origin, or qualification guarantees.

Those barriers affect optimization opportunity, not the source-level completion
model.

### Cleanup is ordinary flow, not stack unwinding

Producer `except` and call-site forwarding leave the current function as an
ordinary function-completion path.

```zax
loadValue final : (
  value : MyValue,
  outerFailure except : MyFailure
)() = {
  using (
    resource := acquireResource()
  ) {
    temporary := prepare(resource)

    value := readValue(resource) except failure: outerFailure:
    return value
  }
}
```

If `readValue` selects `failure`:

1. it returns that named outcome to `loadValue`;
2. the explicit forwarding route selects `outerFailure`;
3. body-local `temporary` is destroyed;
4. `using` disposes `resource`;
5. the owned resource is destroyed;
6. other exited local lifetimes end under ordinary scope rules; and
7. `loadValue` returns `outerFailure` to its immediate caller.

Like `return`, an exceptional function exit skips ordinary enclosing post
operations it crosses. It does not skip scope destruction or applicable
`using` disposal. The existing explicit `break label:` targeting a `using`
remains that construct's deliberate disposal bypass.

Each caller independently catches or forwards. No callee searches outward for a
handler, and no runtime walks the call stack looking for a matching type or
name.

An unresolved panic remains separate:

- panic does not select an `except` result;
- `catch` does not receive panic;
- panic does not unwind result slots, locals, or resources; and
- repair, when available, resumes the same blocked operation rather than
  continuing through an exceptional result branch.

### Transfer, ownership, and reference payloads

An exceptional result is an ordinary result payload after its outcome is
selected. Transfer stance and lifetime remain fully active.

#### Owning payload

```zax
openResource final : (
  resource : MyResource * unique last,
  failure except : MyOpenError * unique last
)(
  name : String readonly &
) = {
  if cannotOpen(name)
    except failure: makeOpenError(name) as last

  return makeResource(name) as last
}
```

The selected `failure` result owns its error allocation. A local handler may
borrow it:

```zax
resource := openResource(name) catch failure {
  error : MyOpenError readonly & = failure.
  report(error)
  return
}
```

Forwarding must transfer ownership under the declared stance. `except` does not
invent ownership, copy a move-only value, or weaken source-state obligations.

#### Reference payload

A reference result needs no optional wrapper merely to indicate whether the
exception occurred:

```zax
failure except : MyError readonly &
```

The selected outcome supplies the control distinction. Ordinary reference
origin must still prove that the target outlives the complete handler or
forwarding consumer. `catch` does not extend the referent life path, and
forwarding cannot erase its origin.

Use an optional reference only when the exceptional payload itself may
deliberately contain no reference:

```zax
failure except : MyError readonly & ?
```

That optional state is payload data, not the exceptional-outcome discriminator.

#### Variants and enums

A variant may be an exceptional payload, but variant selection remains inside
that payload. `catch` identifies the callable outcome, not the active variant
alternative.

An enum likewise needs no generated `?` operation. Its ordinary admission,
comparison, and switch rules remain unchanged.

### Diagnostics

Diagnostics should distinguish:

- an `except` marker outside a result declaration;
- producer `except` naming an unknown or ordinary result;
- producer `except` unable to construct or preserve the selected exceptional
  result;
- `return` with an incomplete ordinary success shape;
- exceptional completion with an incomplete selected payload;
- a live nonselected body-owned result not destroyed before completion;
- prototype-preinitialized nonselected cleanup that cannot be completed;
- two exceptional outcomes apparently selected on one path;
- a call site that neither catches nor forwards one declared exceptional
  outcome;
- attempted `#` omission or discard of an exceptional outcome;
- an unwritten same-name forwarding assumption;
- call-site `except` used as local result capture;
- `catch` naming an ordinary or unknown result;
- duplicate handling or forwarding of one exceptional outcome;
- handler fallthrough reaching a success-dependent continuation without
  completing its destinations;
- use of a success binding inside its own exceptional handler;
- a forwarding destination with incompatible type, qualification, transfer
  stance, ownership, or reference origin;
- elision that would publish a destination before its completion outcome
  commits;
- a nonselected elided instance whose current provisional owner fails to
  destroy it; and
- source presentation that obscures whether a clause catches or forwards.

A useful diagnostic should show:

- the callable's success and named exceptional outcomes;
- which outcome is unaccounted for;
- the source and destination result labels;
- the provisional instance whose lifecycle is incomplete;
- whether cleanup belongs to caller-side prototype initialization or
  callee-side/body construction; and
- positive repairs: add a named `catch`, add explicit
  `except source: destination:` forwarding, or restructure control so all paths
  complete.

### Costs and source compatibility

Programmers must be able to discover:

- the control branch needed to distinguish success from named exceptional
  outcomes;
- construction only for values on the selected outcome, except for explicit
  prototype preinitialization;
- destruction of live provisional values excluded by the selected outcome;
- copies, moves, allocations, conversions, or reference binding caused by catch
  or forwarding remapping;
- cleanup and disposal at each explicitly propagated function boundary;
- conditional commitment of elided destination lifetimes;
- loss of elision where a wrapper's observable behavior requires an
  intermediate boundary; and
- handler and result-slot destruction at call completion.

The following changes are intentionally source-visible:

- adding an exceptional result makes existing call sites incomplete;
- removing or renaming one breaks named catches and forwards;
- changing an ordinary result into an exceptional outcome, or the reverse,
  changes completion shapes;
- changing exceptional payload type, stance, qualification, ownership, or
  origin changes routing viability and cost;
- adding or removing a prototype result initializer changes body-entry
  constructedness and nonselected cleanup;
- changing a wrapper from mechanical forwarding to local handling changes
  control and possibly elision;
- changing a handler from transfer to fallthrough changes definite-construction
  requirements; and
- adding an implicit same-name forwarding rule would hide all of these changes
  and is therefore rejected.

An exceptional-result marker is part of the visible callable contract. A
compatible visible prototype cannot silently add, remove, rename, invert,
discard, or auto-forward outcomes while reusing a minted implementation.

### Candidate terminology

The teaching currently needs only a small vocabulary:

- **success outcome:** the completion that publishes all ordinary results;
- **exceptional outcome:** one named completion declared by an `except` result;
- **selected outcome:** the one success or exceptional completion chosen by one
  invocation;
- **provisional result:** a live result instance whose outcome has not yet been
  committed outward; and
- **forwarding:** explicit mapping of a selected exceptional result into one
  exceptional result of the current function.

These terms are candidates for review. In particular, “exceptional outcome”
must not imply C++ exceptions. The eventual owner should introduce each term
only after concrete source has established its meaning.

### Legacy and refreshed-note dispositions

These are aligned dispositions for the current work scope, not promoted
language design.

Retain by value:

- `except` represents first-class error/failure flow through function results;
- exceptional outcomes are declared in the callable prototype;
- a producer can end its function through one named exceptional result;
- a caller can handle locally or forward;
- source-to-destination mapping supports renaming and typed destinations;
- multiple named exceptional outcomes are useful;
- ordinary transfers remain available inside handlers;
- chaining and composition need an eventual forwarding model; and
- no C++ exception, throw/catch hierarchy, or stack-unwinding machinery is
  introduced.

Supersede:

- `as Boolean` activation;
- `?` activation and `!` polarity fallback for exceptional routing;
- default inactive values for every exceptional result;
- placing `except` on a type rather than the result declaration;
- using call-site `except` to capture any arbitrary result;
- expression-level capture that injects a binding into an outer expression;
- implicit constructor-graph “best match” forwarding;
- consuming an exceptional result as a special hidden mapping operation;
- several active exceptional results;
- grouped catch-as-Boolean-OR behavior;
- `catch !success`;
- declaration-side or call-site `#` discard;
- silent omission; and
- implicit same-name forwarding.

Retain as ordinary nonexception mechanisms:

- current named and several-result capture through ordinary invocation routing;
- optional, pointer, function, and variant `?` presence behavior;
- ordinary `#` disposition for nonexceptional results where its current owner
  permits it;
- `return #` preservation/default completion within the ordinary success shape;
- result transfer stances;
- reference-origin analysis; and
- intent acknowledgement, unsafe assertion, and lint mechanisms for their
  existing distinct jobs.

Legacy composition and pipeline examples are not accepted mechanically. A
composed callable must expose every success and exceptional outcome, preserve
labels or explicitly remap them, and avoid silently merging same-typed
exceptional payloads. This is concrete future pressure on
`language/lambdas-and-callable-composition.md`.

### Remaining design questions

The following questions remain open and should be reviewed without reopening the
aligned outcome model:

1. Exact grammar and formatting for:
   - exceptional result declarations;
   - producer `except`;
   - call-site forwarding;
   - one or several `catch` clauses;
   - mixed local handling and forwarding; and
   - multiline clause presentation under current body-boundary rules.
2. Exact producer syntax for selecting an already preinitialized exceptional
   result without reconstructing it.
3. Whether and how a handler may explicitly produce replacement success results
   so a declaration can complete after recovery.
4. The exact call-completion order among:
   - nonselected prototype-preinitialized cleanup;
   - nonselected body-owned cleanup;
   - selected payload mapping;
   - handler execution;
   - source result destruction;
   - parameter destruction; and
   - caller-side argument/result temporary destruction.
5. Whether a catch's same-name form creates a reference-shaped view of the
   source result or another result-mapped destination, and how explicit typed
   mapping changes that lifetime.
6. How transfer stance is inferred or required for mechanically forwarded
   owning exceptional results.
7. How compatible visible prototypes and exact function-value types represent
   completion outcomes, labels, and prototype preinitialization.
8. How immediate pipelines and composed callables combine, rename, or expose
   exceptional outcomes without hidden matching.
9. Reflection requirements for outcome names, payload types, construction
   state, and forwarding contracts.
10. Whether a future explicit catch-all is ever justified; none is established
    now.

No issue above requires returning to value-presence testing or permitting
discard.

### Promotion teaching plan

Promotion must reconstruct this material for a cold reader. Accuracy and
completeness are necessary but insufficient if the reader has to reverse
engineer the model from lifecycle machinery.

The likely concept-owner progression is:

1. **One success and one failure.** Open with a complete small producer and one
   caller. Explain the visible outcome before naming internal distinctions.
2. **Produce an exceptional outcome.** Teach declaration placement and
   producer `except`.
3. **Handle locally.** Show `catch`, handler binding, and a normal transfer such
   as `return` or labeled `break`.
4. **Forward deliberately.** Show `except source: destination:` and explain why
   same-name forwarding is still written.
5. **Several outcomes.** Establish that exactly one is selected and every one
   must be caught or forwarded.
6. **Construction and cleanup.** Introduce selected completion shapes,
   prototype-preinitialized versus body-constructed slots, and the general
   nonselected-destruction rule.
7. **Elision.** Only after the lifetime model is understood, explain reserved
   storage, conditional commitment, and deep mechanical forwarding.
8. **Resources and flow.** Trace body locals, posts, `using`, destructors, and
   immediate-caller propagation; explicitly deny stack unwinding.
9. **Ownership and references.** Add owning pointer, optional-payload, and
   reference-origin examples.
10. **Diagnostics, costs, and source stability.** End with the complete
    acknowledgement and compatibility obligations.

The owner should not open with:

- a result-state matrix;
- compiler flagging;
- ABI or lowering;
- legacy `?`/`!` alternatives;
- discarded syntax;
- constructor-graph matching;
- or a catalog of edge cases.

Those details either no longer belong or should follow the programmer's model.

Each critical failure should be marked in source. Each valid example must state
enough surrounding prototype and scope to show:

- which outcome was selected;
- whether an ordinary destination entered scope;
- what handler or forward receives the payload;
- which values are destroyed; and
- where execution continues.

The cold-reader test is:

> After the opening examples, can a competent programmer predict whether the
> statement after a call runs and whether its ordinary result binding exists?

The deeper completeness test is:

> After the lifecycle and elision sections, can the programmer predict who
> destroys every provisional value and why no call-stack search or unwind
> occurs?

### Likely lasting ownership

A cohesive programmer-facing `language/except.md` remains the likely primary
owner. Splitting the mental model across invocation, flow, lifecycle, and
operators would force readers to reconstruct one feature from several local
rules.

Local integration would still belong in:

- `language/function-invocation.md` for completion shapes, result routing,
  conditional destination publication, call completion, visible prototypes,
  and elision;
- `language/declarations-and-bindings.md` for result-marker placement, binding
  visibility, and handler destination scope;
- `language/core-flow-control.md` for producer/forwarding function exit and
  handler-body transfers;
- `language/construction-and-destruction.md` for provisional construction,
  nonselected cleanup, destruction order, and scope exit;
- `language/using.md` for disposal when exceptional propagation crosses a
  resource boundary;
- `language/transfer-stances.md` for payload and forwarding stance;
- `language/lifetimes-and-references.md` for result origin, conditional
  publication, references, and elided destination lifetime;
- `language/safety-and-analysis.md` for proof when body lifecycle state is
  opaque, without turning ordinary exceptional routing into unsafe behavior;
- `language/source-structure.md` for clause attachment, continuation, effective
  bodies, and canonical formatting;
- `language/terms.md` for the smallest useful cross-cutting vocabulary;
- `language/operators.md`, `language/optional-values.md`,
  `language/variants.md`, `language/enums.md`, and
  `language/pointers-and-arenas.md` only for removing false presence coupling or
  teaching local payload interactions; and
- `except.md` for retirement or a narrow migration route after every useful
  legacy finding is promoted, deferred, rejected, or superseded.

This ownership map is a planning candidate, not a documentation-fit dry run and
not promotion authorization.

### Adjacent findings deliberately deferred

- Generalized pattern matching is unnecessary for named exceptional outcomes.
  Revisit only if handlers later need structural payload patterns or guards.
- Async suspension and cancellation require a broader completion and cleanup
  model. Synchronous `except` must not imply that cancellation is another
  exceptional result.
- Panic recovery remains separate. Panic never enters `catch`, selects an
  exceptional outcome, or unwinds to a caller.
- General algebraic effects and resumable handlers are not implied. Handling
  does not resume the callee.
- ABI and lowering remain implementation concerns. Programmer-visible design
  constrains outcome identity, construction, transfer, cleanup, and cost without
  selecting a backend representation.
- A general callable result-origin contract may eventually help opaque
  reference-bearing exceptional results. Current origin analysis remains
  required.
- Formal analysis-control categories for opaque result construction belong to
  the existing safety and analysis-control owners, not to `except`.

### Next review

Review should now proceed in teaching order rather than by legacy syntax:

1. confirm the simple producer, local handler, and explicit forwarding examples;
2. settle clause grammar and handler/forwarding mapping;
3. settle preinitialized-result selection and exact cleanup order;
4. test ownership, reference, `using`, and elision examples against those
   choices; and
5. only then prepare the documentation-fit dry run.

That working-record refresh did not itself authorize promotion, owner-document
edits, legacy-page retirement, structural change, or a dry run. The maintainer's
later 2026-09-22 authorization initiated the dry run below and conditionally
authorized promotion only if it passed.

## Dispositions and promotion dry run

### Result

**FAIL — 2026-09-22**

The aligned success-versus-one-exceptional-outcome model has a coherent primary
owner, teaching path, and repository structure. Promotion cannot yet produce
accurate programmer-facing source or complete cross-owner behavior because
several remaining questions change binding lifetime, cleanup order, transfer,
and which source forms are valid.

This result blocks promotion. It does not reopen the aligned outcome model and
does not require maintainer review of the complete ownership inventory before
the focused blockers are discussed.

### What passed

- One cohesive `language/except.md` can own the programmer model without
  duplicating invocation, flow, lifecycle, or transfer owners.
- The existing flat `language/` structure can absorb the concept; no new
  directory or index family is needed.
- The teaching progression recorded above gives a cold reader valid ordinary
  use before lifecycle, elision, diagnostics, and reference detail.
- `index.md` can route directly to the new owner from both Start here and the
  current conceptual-design catalog.
- Legacy `except.md` and the maintainer raw notes can retire after their useful
  material and rejected alternatives are dispositioned in this work record and
  accepted behavior is promoted.
- Presence-bearing types, operators, enums, variants, optionals, and pointers
  need no new exceptional-routing rule. Their local behavior remains payload
  behavior, so most of those owners do not need promotion edits.
- Panic remains owned by `language/safety-and-analysis.md`; the new owner can
  link to that boundary without redefining it.

### Blocking design questions

#### Call-site source shape and attachment

The examples consistently distinguish:

```zax
value := operation() catch failure {
  return
}

value := operation() except failure: outerFailure:
```

but current source ownership does not yet establish:

- whether `catch` and forwarding `except` attach to one invocation, one result
  routing group, or a wider expression;
- how several clauses continue across physical lines;
- whether a clause body uses the ordinary effective-body boundary;
- how local handling and forwarding interleave in one call;
- how the forms appear in a `return`, call argument, construction packet,
  `using` resource entry, pipeline, or composed callable; and
- which parentheses change a bare result sequence into expression mode before
  exceptional routing can attach.

These choices change parsing, binding scope, and whether earlier mapping effects
occur before a later producer selects an exceptional outcome. Illustrative
syntax cannot be promoted as accepted source.

#### Handler binding and lifetime

The following intended forms are aligned at the mapping level:

```zax
catch failure
catch failure: localFailure:
catch failure: report : MyFailureReport
```

The exact value behavior remains unsettled:

- whether the same-name and renamed forms create reference-shaped access to the
  producer result slot or construct independent handler destinations;
- which declaration stance and replacement permission the handler name has;
- whether a typed destination consumes, copies, moves, or merely views the
  source under ordinary mapping;
- when the producer result slot is destroyed relative to the handler body; and
- whether a handler may safely retain or forward a reference derived from that
  slot.

This is material for move-only payloads, owning pointers, references, handler
cost, and destruction. Promotion cannot teach the forms until one behavior is
aligned.

#### Exact call-completion and cleanup order

The ownership split is aligned:

- caller-side invocation machinery owns prototype-preinitialized results; and
- the callee owns body-constructed provisional results until it commits them
  outward.

The exact order still needs alignment among:

1. destruction of nonselected body-owned provisional results;
2. destruction of nonselected prototype-preinitialized results;
3. selected exceptional-payload mapping;
4. handler execution;
5. destruction of selected source result slots;
6. parameter destruction;
7. caller argument and nested-result temporary destruction; and
8. continuation of a handler transfer.

Current invocation keeps parameters and caller temporaries alive through result
mapping. A `catch` body may become part of that completion boundary or may run
after some call-owned state has ended. That decision changes valid references,
observable destructor order, and resource cost.

#### Selecting a preinitialized exceptional result

The prototype may make an exceptional slot live before body entry:

```zax
failure except : MyFailure = :
```

The model says success destroys that nonselected slot and exceptional completion
may publish it without reconstructing it. Exact producer behavior is missing:

- how source selects the already-live slot without supplying another value;
- whether a value-bearing producer `except` assigns, reconstructs, or conflicts;
- how the compiler distinguishes preserving the slot from constructing an
  initially unconstructed exceptional result; and
- whether `return #`-like preservation is intentionally unavailable here
  despite ordinary result precedent.

This cannot be left to compiler inference because construction, assignment, and
replacement have different effects.

#### Forwarding transfer and conditional elision

Explicit source-to-destination labels are aligned, but forwarding still needs a
precise transfer contract:

- which stance the producer exceptional result offers by default;
- whether mechanically forwarding an owned temporary receives a structural
  terminal opportunity;
- when explicit `as move` or `as last` is required;
- whether the destination is constructed directly in its outer elided storage;
- when provisional cleanup responsibility transfers to the forwarding function;
  and
- how a failed typed remapping dispositions the source payload.

Without this, examples for unique ownership, scheduled raw results, and
reference origin can state labels but cannot predict source state or cleanup.

#### Callable contracts and contexts

Exceptional outcomes are part of a callable's visible contract, but current
owners do not yet say:

- whether compatible visible prototypes must preserve the exact exceptional
  outcome set and labels;
- how function-value type identity includes exceptional outcomes;
- whether overload selection can compare callables with differing outcome sets;
- whether current `>>` composition and `|>` chaining reject exceptional
  callables, preserve their outcomes, or require explicit mapping; and
- how a `using` resource list handles an acquisition call with one or more
  exceptional outcomes before any resource destination becomes live.

These can be explicitly restricted or designed, but either choice changes
currently documented callable and resource behavior and therefore needs
alignment before promotion.

### Nonblocking explicit deferrals

The following can remain future work without making the synchronous current
model incoherent:

- handlers that recover by producing replacement success results; the current
  rule can require a transfer whenever a success-dependent destination would
  otherwise remain unconstructed;
- a catch-all; no catch-all exists in the current model;
- generalized pattern matching and guarded handlers;
- async suspension and cancellation;
- detailed reflection schema, provided the current callable contract still
  preserves outcome names and payload types conceptually;
- ABI and lowering; and
- generalized effects or resumable handlers.

### Proposed lasting ownership map

| Concern | Lasting owner |
| --- | --- |
| Cohesive mental model, producer `except`, local `catch`, explicit forwarding, exhaustive acknowledgement, no discard, costs, diagnostics, and cross-feature teaching | New `language/except.md` |
| Completion outcomes, result-slot shapes, call-site routing, call completion, compatible prototypes, and conditional result elision | `language/function-invocation.md` |
| Result-marker placement, handler destination declaration, binding visibility, and declaration-facing mapping | `language/declarations-and-bindings.md` |
| Function-exit behavior, posts, handler transfers, and local scope exit | `language/core-flow-control.md` |
| Provisional result construction, nonselected destruction, and exact lifecycle order | `language/construction-and-destruction.md` |
| Conditional destination life paths, returned references, and elided storage/lifetime commitment | `language/lifetimes-and-references.md` |
| Exceptional payload and forwarding stance, ownership transfer, and source post-state | `language/transfer-stances.md` |
| Exceptional propagation across acquisition, disposal, and resource-list mapping | `language/using.md` |
| Clause attachment, effective bodies, continuation, contextual recognition, and formatting | `language/source-structure.md` |
| Function values, `>>`, `|>`, and callable-composition restrictions or mappings | `language/lambdas-and-callable-composition.md` |
| Concise cross-cutting outcome and provisional-result vocabulary | `language/terms.md` |
| Panic boundary and unsafe proof model | Existing `language/safety-and-analysis.md`; linked, not redefined |
| Public navigation | `index.md` |
| Historical evidence and rejected legacy behavior | This work record, then archive after closure |

### Structure proposal

Retain the current repository layout:

```text
language/
  except.md
```

No `language/README.md`, feature subdirectory, formal specification area, or
compatibility stub is needed.

Focused reading reaches the owner from `index.md`. The owner links only to
concrete dependencies such as invocation, core flow, lifecycle, transfer,
lifetimes, `using`, and safety. Ordinary readers do not route into numbered work
or raw notes.

### Exact proposed promotion file set

Promotion should use this one coherent file set after the blockers are aligned:

**Add**

- `language/except.md`

**Modify**

- `index.md`
- `language/function-invocation.md`
- `language/declarations-and-bindings.md`
- `language/core-flow-control.md`
- `language/construction-and-destruction.md`
- `language/lifetimes-and-references.md`
- `language/transfer-stances.md`
- `language/using.md`
- `language/source-structure.md`
- `language/lambdas-and-callable-composition.md`
- `language/terms.md`
- `project/raw/README.md`
- `project/raw/cross-cutting-audit.md`
- `project/work/029-except.md`

**Delete after complete disposition**

- `except.md`
- `project/raw/except-maintainer-notes.md`

No edit is currently required in:

- `README.md`;
- `project/README.md`;
- operator, optional, enum, variant, pointer, qualifier, or safety owners; or
- handoff, rehydration, teaching-debt, archive, implementation, or formal
  specification surfaces.

The first exception-specific cross-cutting-audit entry can close when the legacy
page retires. The continuation-syntax audit remains live only for its remaining
meta-function example after its `except.md` references are removed.

### Teaching fit

The proposed dedicated owner passes the cold-reader and cohesive-teaching tests.
It can lead with one success/failure example, then local handling and explicit
forwarding, before introducing completion shapes, provisional cleanup, elision,
resource exit, ownership, references, and diagnostics.

The current open questions fail the accuracy side of the same test: a reader
could understand the high-level model but could not yet predict whether a catch
binding copies or borrows, when a destructor runs, or how valid syntax attaches
inside common call contexts. Filling those holes during prose drafting would
silently make language decisions, so promotion must wait.

### Promotion gate

The dry run becomes eligible for PASS after focused alignment establishes:

1. call-site attachment and clause grammar;
2. catch-binding value/lifetime behavior;
3. exact cleanup and call-completion order;
4. preinitialized exceptional-result selection;
5. forwarding transfer and elision behavior; and
6. current restrictions or mappings for visible prototypes, function values,
   composition/chaining, and `using` acquisition.

After those decisions are recorded, rerun the fit against the exact file set
above. PASS would authorize the already conditionally approved promotion; this
FAIL does not.

### Alignment after the failed dry run

The maintainer and agent subsequently aligned every blocking concern. These
findings retain working-record maturity until promotion.

#### Invocation clauses expose nested outcomes

A `catch` or forwarding clause on an invocation applies to the complete
evaluation needed for that invocation: receiver, arguments, nested calls, and
the selected outer callable.

```zax
result := transform(makeValue()) catch failure {
  report(failure)
  return
}
```

If both `makeValue` and `transform` expose `failure`, the one handler is checked
separately for each source path and payload type. `transform` never starts when
`makeValue` selects its exceptional outcome. The handler is branch-polymorphic;
no erased common error value is introduced.

Two positional same-name handlers do not distinguish nested producers. Source
that needs separate behavior first reshapes one exposed outcome label.

#### Outcome reshape

Existing no-storage directional `reshape` expands to callable exceptional
outcome labels:

```zax
result := transform(
  makeValue() reshape failure: myFailure:
) catch failure {
  report(failure)
  return
} catch myFailure {
  report(myFailure)
  return
}
```

An inline outcome reshape:

- uses source-to-destination label order;
- preserves the exceptional category, payload, stance, qualifications, origin,
  and constructedness;
- performs no handling or forwarding;
- adds no required runtime operation; and
- cannot map ordinary success results to exceptional outcomes or the reverse.

A named reusable reshape is also valid:

```zax
MyFailureNames :: reshape {
  failure: myFailure:
}

result := transform(
  makeValue() reshape MyFailureNames
) catch failure {
  return
} catch myFailure {
  return
}
```

This is concrete pressure that revises the current structural-owner statement
that no use-site `reshape` keyword exists. Structural transformation syntax
remains unchanged; this is a callable-outcome application of the same
no-storage mapping declaration.

#### Catch and forwarding destinations use ordinary result mapping

These forms use ordinary source-to-destination result mapping:

```zax
catch failure
catch failure: localFailure:
catch failure: report : MyFailureReport
```

`catch failure` is same-name inferred handler mapping. A handler destination
lives for its handler body. Source stance controls transfer into it. Ordinary
result elision may unify the producer result slot with that destination; when
they remain distinct, each follows ordinary source-slot and handler-local
destruction.

Forwarding has the same mapping behavior:

```zax
call() except failure
call() except failure: outerFailure:
```

The first form explicitly forwards to a compatible same-named exceptional
result of the current function. It is not hidden name matching because the
forwarding keyword and source outcome are written. The second form renames.
Forwarding exits the current function; it never forwards to a later catch.

A branch-polymorphic same-name catch maps each source specialization
independently. An explicit typed handler destination supplies convergence and
must be constructible from every source specialization.

#### Exact cleanup order

When one exceptional outcome is selected:

1. complete the selected exceptional source result;
2. leave the producer body, destroying body locals and performing crossed
   `using` disposal/destruction while skipping crossed post operations as
   `return` does;
3. destroy live nonselected body-owned provisional results in reverse result
   declaration order;
4. destroy live nonselected caller-preinitialized results through caller-side
   invocation machinery;
5. map the selected source result into the catch or forwarding destination;
6. run a catch handler while producer parameters and required caller argument
   temporaries remain alive;
7. destroy handler locals when the handler exits;
8. destroy any distinct producer exceptional source slot;
9. destroy producer parameters and then caller-side argument and nested-result
   temporaries under ordinary call-completion order; and
10. continue the handler transfer.

For forwarding, mapping completes the inner call first. The current function
then performs its own ordinary function-exit cleanup while returning the
selected outer exceptional outcome.

If a nested argument call fails before its enclosing callable starts, that
outer callable has no parameters or body locals. Already evaluated sibling
arguments and caller-side temporaries remain governed by the enclosing
invocation's completion cleanup.

#### Exceptional slots have one construction point

Exceptional result declarations cannot have prototype initializers:

```zax
failure except : MyFailure = : // error
```

They also cannot be manually constructed before selection:

```zax
failure.+++(input) // error
```

Prepare an ordinary local instead:

```zax
preparedFailure := makeFailure()
except failure: preparedFailure
```

An exceptional result slot begins unconstructed and is constructed exactly once
by producer `except` or explicit forwarding. Success constructs no exceptional
slot. This removes preinitialized exceptional-result selection from the
language model.

#### Transfer and conditional elision remain ordinary

Exceptional mapping adds no implicit `move` or `last`:

- the source result's declared stance controls transfer into the handler or
  forwarding destination;
- the forwarding result declaration controls what its caller receives;
- terminal-opportunity diagnostics remain ordinary;
- move-only payloads require a viable declared stance; and
- conditional elision may construct directly in eventual handler or outer
  exceptional-result storage.

Provisional cleanup responsibility transfers outward only after successful
mapping. If a later enclosing function selects another outcome, its current
provisional owner destroys the live value.

#### Pipelines, composition, and `using`

Immediate `|>` chaining exposes each reached stage's exceptional outcomes to a
trailing handler. A stage after an exceptional outcome does not run. Duplicate
labels may use a branch-polymorphic catch or outcome reshape.

Stored `>>` composition exposes the union of stage outcomes. A later stage runs
only after prior success. Reusable composed prototypes require unique
exceptional labels; colliding labels must be reshaped before composition rather
than creating an implicit path-polymorphic result declaration.

```zax
ParseFailureNames :: reshape {
  failure: parseFailure:
}

renamedParse := parse reshape ParseFailureNames
pipeline := renamedParse >> transform
```

For `using`, an acquisition result becomes enrolled only after its success
outcome commits:

```zax
using (
  connection := connect(),
  grant := acquireGrant(connection) catch failure {
    return
  }
) {
  use(connection, grant)
}
```

If grant acquisition fails, `grant` was never acquired and receives no disposal
or destruction. Earlier enrolled `connection` still receives ordinary reverse
disposal and destruction before the handler transfer continues. A handler
cannot fall through into a body that requires an unconstructed resource.

### Documentation-fit dry-run rerun

**PASS — 2026-09-22**

The aligned model now has:

- one cohesive programmer-facing owner;
- exact ordinary and exceptional completion shapes;
- concrete producer, catch, forwarding, and outcome-reshape source;
- deterministic binding, transfer, cleanup, and elision behavior;
- explicit callable-composition, pipeline, and resource-acquisition
  integration;
- a cold-reader teaching progression;
- complete legacy and raw-input disposition;
- no duplicate authority or orphaned deferred meaning; and
- an exact promotion change set.

The flat structure proposal remains valid. The exact promotion set is the prior
set with one necessary addition:

- modify `language/structural-shapes-and-compatibility.md` to own outcome
  reshaping and revise the former no-use-site-`reshape` boundary.

All other listed additions, modifications, deletions, non-edits, raw
dispositions, router changes, and teaching obligations remain unchanged.

The remaining deferrals are nonblocking: recovery that produces replacement
success values, an explicit catch-all, patterns, async/cancellation, detailed
reflection schema, ABI/lowering, and generalized effects.

This PASS does not itself establish accepted language design. The maintainer
explicitly authorized promotion on PASS in the same discussion that authorized
this rerun.

### Promotion application and provenance correction

The PASS promotion was applied on 2026-09-22.

The new programmer-facing owner teaches only the aligned positive model:

- success or one named exceptional outcome;
- local `catch`;
- explicit same-name or renamed forwarding;
- nested branch-polymorphic handling;
- outcome `reshape`;
- deterministic construction, cleanup, and conditional elision;
- ordinary transfer stance;
- pipeline, composition, and `using` integration; and
- no exception stack unwinding or panic recovery.

Rejected discovery alternatives were not promoted or anti-taught. Their
disposition remains only in this working record.

The immutable fixed initiating input links to consumed legacy `except.md`.
During promotion integration that link was initially treated as a reason to
retain a temporary routed page. The maintainer rejected that retention:
consumed legacy pages are deleted, and Git history is sufficient provenance.
Root `except.md` was therefore removed despite the historical link in the
active fixed input.

The maintainer raw notes remain unchanged and their raw-index entry marks them
consumed but temporarily retained because this promotion did not separately
direct their deletion. They retire during work-item closure unless the
maintainer directs earlier removal.

Neither retained raw evidence nor the broken historical legacy link supplies
current language authority.

The applied current-owner set is:

- new `language/except.md`;
- `index.md`;
- invocation, declaration, core-flow, lifecycle, lifetime, transfer, `using`,
  source-structure, structural-reshape, callable-composition, and terms owners;
- the live raw index and cross-cutting audit; and
- this working record.

Validation confirmed the staged review boundary remains intact: the
maintainer-approved pre-dry-run `029` content is staged, while the dry runs,
aligned resolutions, promotion record, and wider promotion remain unstaged.

### Post-promotion review alignment

The maintainer's review of the promoted model established these additional
findings.

#### Consumed legacy page deletion

Root `except.md` must be deleted rather than retained as a router or consumed
stub. Git history is sufficient provenance even though the immutable fixed input
above retains its historical link. The page was removed from the working tree.

#### Lifecycle callables do not expose exceptional outcomes

Constructors, replacement constructors, and destructors cannot declare or
produce exceptional outcomes. A lifecycle body may call an exception-producing
function only when it handles every outcome locally and still completes its
lifecycle obligation. It cannot forward through `except`.

An ordinary factory function remains the callable form for construction that
needs a caller-visible exceptional outcome.

#### Reshape cardinality and result categories

Callable reshape applies to ordinary success results and exceptional outcomes.
It preserves each result's category:

- ordinary results remain in the success shape; and
- exceptional results remain exceptional outcomes.

One application accepts either:

- one inline `source: destination:` mapping; or
- one named or local anonymous reshape declaration containing several entries.

Reshape entries never use commas. Several inline pairs and comma-separated
named or anonymous entries are invalid.

#### Lambdas and callbacks

A lambda may declare exceptional outcomes under the same function-call model.
Those outcomes belong to its minted callable prototype. Selecting one ends only
that invocation; it does not destroy or reset the lambda or its capture
receiver. Reference payloads projected from captures preserve capture origin.

A callback is a callable role, not another language category. Its static
prototype includes all ordinary and exceptional outcomes. Every installed
implementation must satisfy that contract, and the invoker catches or forwards
at the callback call site. Runtime callback selection performs no handler
search.

#### Weak callables have no results

`weak` and `weak atomic` callable storage require a completely empty result
contract. They permit neither ordinary nor exceptional results. The
unavailable-weak no-op behavior applies only to that genuinely resultless
contract.

A callback with exceptional outcomes uses another binding kind or a zero-result
wrapper that handles every outcome internally.

These findings were promoted directly into the current exceptional-flow,
invocation, declaration, lifecycle, structural-reshape, source-structure, and
callable-composition owners under the maintainer's explicit authorization.

## Closure

Work item `029` is complete.

- The aligned model is promoted to `language/except.md` and integrated with
  invocation, declarations, core flow, lifecycle, lifetimes, transfer,
  structural reshape, source structure, callable composition, `using`,
  Nothing/weak callable behavior, terminology, and navigation.
- Rejected and superseded alternatives remain only in this historical record;
  current owners teach the positive model without anti-teaching them.
- Root legacy `except.md` was consumed and deleted.
- `project/raw/except-maintainer-notes.md` was fully dispositioned and may be
  removed with its raw-index entry.
- The exception-specific cross-cutting audit concern was consumed.
- No accepted meaning depends on this record after archival.

The next bounded concern is the by-value mining and disposition of legacy
constructor/destructor and discard material. Its fixed initiating input and
reading scope are created separately as work item `030`; this record performs
no analysis of that material.
