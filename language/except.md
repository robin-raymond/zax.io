# Zax exceptional result flow

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers declaring, producing, handling, or forwarding exceptional function outcomes |
| Applies To | Programmer-facing synchronous exceptional result flow; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | Exceptional result declarations; success and named exceptional completion outcomes; producer `except`; local `catch`; explicit forwarding; outcome reshape; exhaustive outcome acknowledgement; handler binding; exceptional propagation, cleanup, elision, costs, diagnostics, and source stability |
| Does Not Own | Ordinary argument/result routing and callable selection ([function invocation](function-invocation.md)); general declaration behavior ([declarations and bindings](declarations-and-bindings.md)); scope transfers and posts ([core flow control](core-flow-control.md)); general lifecycle ordering ([construction, replacement, and destruction](construction-and-destruction.md)); transfer stances ([transfer stances](transfer-stances.md)); reference origin ([lifetimes and references](lifetimes-and-references.md)); resource disposal ([Zax `using`](using.md)); callable composition ([lambdas and callable composition](lambdas-and-callable-composition.md)); or panic and unsafe proof ([safety and analysis](safety-and-analysis.md)) |

## Start with success or one named failure

A function may publish its ordinary results or one named exceptional result:

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

`return` completes the success outcome and publishes `count`.

`except failure: ...` completes the named `failure` outcome instead. On that
path, no `count` value is published.

A caller handles the exceptional outcome where the call is made:

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

If `readCount` succeeds, `count` is constructed and enters scope. If it selects
`failure`, the handler runs and `count` never enters scope. The handler's
`return` is ordinary flow control.

This is not stack exception handling:

- no object is thrown;
- no runtime searches outward for a handler;
- no call stack is unwound;
- no completed operation is rolled back; and
- panic does not enter `catch`.

Each function returns one declared completion outcome to its immediate caller.

## Callable completion outcomes

A callable has:

- one **success outcome** containing all ordinary results; and
- zero or more named **exceptional outcomes**, each containing one declared
  `except` result.

One invocation selects exactly one outcome.

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

The possible completion shapes are:

```text
success -> value
missing -> missing
denied  -> denied
```

No path publishes several exceptional results or an incidental subset of the
ordinary results.

Exceptional selection comes from the producer's control path. The exceptional
payload does not need a Boolean presence operation, a default inactive state,
or a particular error base type.

### Exceptional results are constructed when selected

An exceptional result begins unconstructed. It is constructed exactly once by:

- producer `except`; or
- explicit forwarding from another call.

Prepare complex state in an ordinary local, then select the outcome:

```zax
preparedFailure := makeFailure()

if operationFailed()
  except failure: preparedFailure
```

Exceptional result declarations have no initializer, and ordinary body source
does not construct their slots early. This keeps constructedness and outcome
selection at one visible operation.

The supplied value follows ordinary direct-construction, qualification,
transfer, and selection rules. Naming the destination avoids any search for an
exceptional result by payload type.

### Success still completes its ordinary shape

A value-bearing `return` supplies all ordinary results:

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

A bare return or fallthrough remains valid only when every ordinary success
result is already complete. Exceptional results do not participate in that
success list.

## Handle an outcome locally with `catch`

`catch` names one exceptional outcome and supplies one braced handler body:

```zax
scope read_attempt: {
  count := readCount(source) catch failure {
    report(failure)
    break read_attempt:
  }

  ++count
}
```

The call has two continuations:

- success constructs `count` and reaches `++count`;
- `failure` constructs the handler destination and reaches
  `break read_attempt:`.

The exceptional continuation cannot reach a use that requires the missing
success destination.

### Handler destinations use result mapping

The short form introduces a same-named inferred handler destination:

```zax
value := operation() catch failure {
  report(failure)
  return
}
```

Source-to-destination order permits a rename:

```zax
value := operation() catch failure: localFailure: {
  report(localFailure)
  return
}
```

A typed destination requests ordinary construction:

```zax
value := operation() catch failure: \
  report : MyFailureReport {
  submit(report)
  return
}
```

A discarded destination handles the outcome and introduces no payload name:

```zax
value := operation() catch failure: # {
  return
}
```

The payload is still constructed for the handler and destroyed when the handler
exits. The body cannot read it. A value-producing call still cannot fall
through into the success continuation; `return` or another transfer leaves the
handler. `#` does not omit the outcome. Omitting the outcome remains an error,
as [every exposed outcome](#every-exposed-outcome-is-handled-or-forwarded)
requires `catch` or `except`.

The producer result stance controls transfer into the handler destination.
Mapping may copy, move, convert, allocate, bind a reference, or be elided under
the ordinary result-routing rules. A typed destination must be viable for every
source path that can select that handler.

The destination lives for the handler body. It is destroyed when that body
exits unless its value is transferred elsewhere.

### The success binding does not exist in the handler

A destination declaration enters scope only after its initializer completes.
The handler therefore cannot use the success binding whose construction it
replaced.

A value-producing call's handler must leave the continuation through ordinary
flow control unless a future explicit recovery form supplies replacement
success results. A zero-result call has no missing success destination and may
use a normally completing named handler:

```zax
performNotification() catch unavailable { }
continueWork()
```

This explicitly handles `unavailable`; it does not silently omit an outcome.

## Forward an outcome with `except`

A function that cannot handle an outcome declares a compatible exceptional
result and forwards explicitly:

```zax
loadCount final : (
  count : Integer,
  failure except : MyReadError
)(
  source : String readonly &
) = {
  count := readCount(source) except failure
  return count
}
```

`except failure` means:

```text
readCount.failure -> loadCount.failure
```

The keyword and source label make same-name forwarding visible. If the current
function has no compatible same-named exceptional result, the forwarding clause
is an error.

Use a source-to-destination pair to rename:

```zax
count := readCount(source) except failure: outerFailure:
```

Forwarding maps the payload into the current function's exceptional result,
completes the inner call, performs the current function's ordinary exit cleanup,
and returns that named outcome to the immediate caller. Source after the
forwarding operation does not execute.

Forwarding never targets a later `catch`; it exits the current function.

### Every exposed outcome is handled or forwarded

One call may handle some outcomes and forward others:

```zax
value := load() catch missing {
  report(missing)
  return
} except denied: outerDenied:
```

Every exposed exceptional outcome must have one disposition. An exceptional
outcome cannot be omitted or discarded in place of `catch` or `except`, because
its branch has no success values with which ordinary execution could continue.
A handler that does run may still map its payload to `#` and give the body no
name for that payload.

Adding an exceptional result to a callable therefore requires review of every
call site.

## Nested calls expose their outcomes

A handler on an invocation covers the complete evaluation required for that
invocation: its receiver, arguments, nested calls, and selected outer callable.

```zax
result := transform(makeValue()) catch failure {
  report(failure)
  return
}
```

If `makeValue` selects `failure`, `transform` never starts. If `makeValue`
succeeds and `transform` selects its own `failure`, the same handler runs for
that path.

When the two payload types differ, the handler is checked separately for each
source path. Its inferred `failure` destination and body are branch-specialized;
no erased common error object is created.

```zax
result := transform(makeValue()) catch failure: \
  report : MyFailureReport {
  submit(report)
  return
}
```

Here the typed destination instead requires every `failure` specialization to
construct `MyFailureReport`.

### Reshape an exposed outcome label

Callable `reshape` applies to ordinary success-result labels as well as
exceptional outcome labels. Use it when nested producers need different handler
names:

```zax
result := transform(
  makeValue() reshape failure: makeFailure:
) catch failure {
  reportTransformFailure(failure)
  return
} catch makeFailure {
  reportMakeFailure(makeFailure)
  return
}
```

The mapping reads from source to destination:

```text
failure: makeFailure:
```

Outcome reshape:

- changes only exposed result labels and paths;
- preserves the exceptional category, payload, transfer stance,
  qualifications, origin, and constructedness;
- performs no handling or forwarding;
- creates no runtime value or storage; and
- cannot map an ordinary result into an exceptional outcome or the reverse.

One use-site reshape accepts either one inline mapping or one reshape
declaration. Several mappings belong in a named or local anonymous declaration:

```zax
MakeFailureNames :: reshape {
  value: producedValue:
  failure: makeFailure:
}

result := transform(
  makeValue() reshape MakeFailureNames
) catch failure {
  return
} catch makeFailure {
  return
}
```

```zax
result := makeValue() reshape {
  value: producedValue:
  failure: makeFailure:
}
```

Reshape entries are separate declarations and never use commas, including in a
local anonymous reshape. Several inline `source: destination:` pairs and
comma-separated reshape entries are invalid.

Ordinary mappings remain in the success shape; exceptional mappings remain
exceptional outcomes. Reshape cannot change either category or merge them.

Explicit entries consume their source and destination labels before remaining
equal-label matching. Duplicate destination outcome labels are an error.
Complete no-storage mapping behavior is shared with
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md#reusable-mapping-with-reshape).

## Result construction and cleanup

### Selected shapes, not maybe-live API

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

the caller sees either:

```text
success -> value, count
failure -> failure
```

The body may provisionally construct ordinary results before it discovers a
failure. That intermediate state does not change the callable's public outcome
shape.

Before completing with one outcome, every live result outside that outcome is
destroyed by its current provisional owner.

### Prototype-preinitialized ordinary results

An ordinary result initializer still constructs that slot before body entry:

```zax
prepare final : (
  value : MyValue = :,
  failure except : MyFailure
)() = {
  // ...
}
```

Caller-side invocation machinery establishes `value`. Success publishes it. If
`failure` is selected, caller-side call-completion machinery destroys the
nonselected `value`.

Prototype initialization controls body-entry state and visible cost; it does
not add the result to every completion outcome.

### Body-constructed provisional results

An ordinary result constructed in the body remains the callee's provisional
responsibility:

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

If the later check fails, the callee destroys provisional `value` before
completing exceptionally. On success, cleanup responsibility transfers outward
with the published result.

### Exact exceptional completion order

When one exceptional outcome is selected:

1. complete the selected exceptional source result;
2. leave the producer body, destroying body locals and performing crossed
   `using` disposal and destruction while skipping crossed post operations as
   `return` does;
3. destroy nonselected body-owned provisional results in reverse result
   declaration order;
4. destroy nonselected prototype-preinitialized results through caller-side
   invocation machinery;
5. map the selected source result into the catch or forwarding destination;
6. run a catch handler while producer parameters and required caller argument
   temporaries remain alive;
7. destroy handler locals when the handler exits;
8. destroy any distinct producer exceptional source slot;
9. destroy producer parameters and then caller-side argument and nested-result
   temporaries under ordinary call-completion order; and
10. continue the handler's transfer.

For forwarding, selected-result mapping first completes the inner call. The
current function then performs its own ordinary function-exit cleanup while
returning its outer exceptional outcome.

If a nested argument call selects an outcome before the enclosing callable
starts, that outer callable has no parameters or body locals. Already evaluated
sibling arguments and caller-side temporaries remain subject to the enclosing
invocation's completion cleanup.

## Conditional result elision

Reserved storage does not contain an instance until construction begins. A
caller may therefore make final destination storage available through several
forwarding calls:

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
  value := leaf() except failure
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

On success, `leaf` may construct `MyValue` directly in `outer`'s reserved
storage. Each successful boundary transfers provisional cleanup responsibility
outward, and `outer` finally publishes the binding.

On `failure`, the value binding never enters scope. Raw reserved storage needs
no destructor. If an inner function had already constructed a provisional
ordinary result, its current provisional owner destroys that instance before
propagating the exceptional outcome.

Elision may unify a result slot with an outer destination's storage, but the
destination lifetime becomes caller-visible only when the selected outcome
publishes that result.

Elision remains optional and cannot change visible construction, destruction,
alias, transfer, qualification, or callback behavior.

## Flow, resources, and panic

### Ordinary scope exit

Producer `except` and forwarding are function exits. Like `return`, they:

- skip enclosing post operations they cross;
- destroy exited body-local values;
- perform applicable `using` disposal;
- destroy owned resources and other exited lifetimes; and
- return one outcome to the immediate caller.

Each caller then catches or forwards explicitly. This is ordinary source-level
flow through callable boundaries, not exception stack unwinding.

### Acquisition inside `using`

A resource becomes enrolled only after its success outcome commits:

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

If grant acquisition fails:

- no `grant` instance is enrolled, disposed, or destroyed;
- previously enrolled `connection` receives ordinary reverse disposal and
  destruction; and
- the handler transfer continues only after that cleanup.

A handler cannot fall through into a `using` body that requires an
unconstructed resource.

### Panic is separate

An unresolved panic blocks its operation and ends in graceful process crash
unless an applicable helper repairs that same operation. Panic does not select
an exceptional result, enter `catch`, forward through `except`, or unwind
resources.

Complete panic behavior is defined by
[Zax safety and analysis](safety-and-analysis.md#panic-boundary).

## Transfer, ownership, and references

Exceptional payload mapping uses ordinary transfer stances. No implicit
`move` or `last` is added.

- The producer result stance controls transfer into a handler or forwarding
  destination.
- The forwarding result declaration controls what its caller receives.
- A move-only payload requires a viable declared stance.
- Terminal-opportunity diagnostics remain ordinary.
- Provisional cleanup responsibility transfers only after successful mapping.

### Owning payload

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

The selected `failure` result owns its allocation. A handler destination may
receive it under the declared `last` stance:

```zax
resource := openResource(name) catch failure {
  error : MyOpenError readonly & = failure.
  report(error)
  return
}
```

### Reference payload

A reference can be an exceptional payload directly:

```zax
failure except : MyError readonly &
```

The selected outcome supplies the control distinction. Ordinary origin analysis
must still prove that the referent survives the handler or forwarding consumer.
`catch` extends no target life path, and forwarding erases no origin.

An optional reference is used only when the payload itself may contain no
reference:

```zax
failure except : MyError readonly & ?
```

Optional absence is payload state, not the exceptional-outcome discriminator.

## Lambdas and callbacks

### Lambdas use the same result contract

A lambda may declare and produce exceptional outcomes like a named function:

```zax
reader := [[ source ]] (
  value : MyValue,
  failure except : MyFailure
)() {
  if cannotRead(source)
    except failure: makeFailure(source)

  return readValue(source)
}
```

The outcomes are part of the lambda's minted callable prototype. Calling,
catching, forwarding, reshaping, storing, and composing the lambda use the
ordinary rules in this document.

Selecting an exceptional outcome ends one invocation; it does not destroy or
reset the lambda or its capture receiver. Repeated invocation remains available
under the callable's ordinary captured-state and transfer contracts.

A reference exceptional payload projected from captured state retains that
capture origin. It may escape only while the callable receiver relationship
keeps the target path valid.

### A callback is a callable role

A callback parameter or stored callback value exposes one static callable
prototype, including every exceptional outcome:

- every installed implementation must satisfy that prototype;
- the invoker catches or forwards outcomes at the callback call site;
- an implementation with additional outcomes cannot enter a callback slot that
  omits them; and
- a wrapper lambda may handle, reshape, or forward outcomes to present another
  explicit callback contract.

Dynamic callback selection introduces no runtime handler discovery. The visible
prototype already determines every possible outcome.

`weak` callable storage is unavailable for any resultful prototype. An
exceptional result is a result, so a weak callable has neither ordinary nor
exceptional results:

```zax
callback : ()() bound weak
```

The existing unavailable-weak no-op behavior therefore applies only to a truly
empty result contract. A callback with exceptional outcomes uses another
binding kind or a zero-result wrapper that handles every outcome internally.

Complete capture, storage, receiver lifetime, and weak behavior is defined by
[Zax lambdas and callable composition](lambdas-and-callable-composition.md).

## Pipelines and callable composition

### Immediate `|>` chaining

An exceptional outcome from a reached stage skips every later stage. A trailing
handler sees the exposed outcomes of those stages:

```zax
result := source |>
  parse() reshape failure: parseFailure: |>
  transform() catch parseFailure {
    return
  } catch failure {
    return
  }
```

Without reshape, equal-label outcomes may share one branch-polymorphic handler.

### Retained `>>` composition

`>>` constructs a callable without running either stage. The composed callable:

- invokes a later stage only after prior success;
- exposes the union of stage exceptional outcomes; and
- requires unique exceptional labels in its reusable visible prototype.

Colliding labels are reshaped before composition:

```zax
ParseFailureNames :: reshape {
  failure: parseFailure:
}

renamedParse := parse reshape ParseFailureNames
pipeline := renamedParse >> transform
```

Outcome reshape adapts the visible callable contract without invoking the
callable or constructing a runtime aggregate.

Callable values and compatible visible prototypes preserve the number,
ordinary-versus-exceptional category, payload contracts, and body-entry
constructedness of outcomes. A visible prototype may explicitly relabel a
corresponding exceptional outcome, but it cannot silently add, remove, merge,
split, discard, or change its category.

Outcome sets do not create overload preference. Selection first chooses the
callable under ordinary rules; source then accounts for the selected callable's
outcomes.

## Costs

Exceptional result flow may require:

- a branch or equivalent completion selector;
- construction of one selected payload;
- destruction of provisional nonselected results;
- handler-destination mapping;
- copies, moves, allocation, conversion, or reference binding selected by that
  mapping;
- cleanup at every explicitly forwarded function boundary;
- several compile-time handler specializations for one equal-label nested
  outcome;
- conditional destination-lifetime tracking for elision; and
- wider retained callable contracts when several stages expose outcomes.

Outcome reshape itself creates no runtime value, storage, payload conversion, or
control transfer.

No representation, calling convention, flag layout, or lowering strategy is
promised.

## Diagnostics

Diagnostics should identify:

- the callable and unaccounted exceptional outcome;
- an unknown, ordinary, duplicate, or already consumed outcome label;
- an exceptional result initialized or manually constructed before selection;
- incomplete success or selected exceptional payload construction;
- a live nonselected provisional result with no valid cleanup;
- handler fallthrough that reaches a success-dependent continuation;
- use of a success binding in its exceptional handler;
- incompatible typed handler or forwarding mapping;
- a stance, ownership, qualification, or reference-origin mismatch;
- an outcome reshape whose source is missing, destination collides, or category
  changes;
- an invalid same-name forward with no compatible current result;
- a retained composition whose exceptional labels still collide;
- an unconstructed `using` resource that a body would require; and
- elision that would publish a destination before its outcome commits.

When one same-name handler is branch-polymorphic, a diagnostic should identify
the exact producer path and payload specialization for which its body or
destination mapping fails.

Exact diagnostic identifiers and presentation remain future diagnostics work.

## Source stability

The following changes are intentionally visible:

- adding an exceptional result makes every affected call incomplete until
  source catches, forwards, or reshapes it;
- removing or renaming one breaks its mappings;
- changing an ordinary result to exceptional, or the reverse, changes completion
  shapes;
- changing payload type, stance, qualification, ownership, or origin changes
  handler and forwarding viability;
- adding a nested equal-label outcome adds another handler specialization unless
  source reshapes it;
- changing prototype ordinary-result initialization changes body-entry state
  and nonselected cleanup;
- changing local handling to forwarding changes function exit and elision;
- changing one `>>` stage changes the retained callable's visible outcome set;
  and
- changing resource acquisition outcomes changes `using` header completion.

Source, declaration, import, discovery, or outcome order never silently resolves
an incompatible mapping.

## Lifecycle-callable boundary

Ordinary constructors, replacement constructors, and destructors cannot declare
or produce exceptional outcomes. Their lifecycle contracts require completion
of the instance or terminal transition rather than another caller-visible
result path.

A lifecycle body may invoke an exception-producing function only when every
outcome is handled locally and every resulting path still satisfies the
lifecycle operation's completion obligations. Forwarding `except` is
unavailable because the lifecycle callable has no exceptional result.

Use an ordinary factory function when construction needs a caller-visible
exceptional outcome.

## Boundaries and maturity

This document is current conceptual design, not a formal grammar,
implementation mapping, ABI contract, compatibility promise, or conformance
specification.

Still deferred:

- exceptional outcomes on non-call operator result shapes; current `except`
  declarations belong to ordinary function and call-syntax callable
  prototypes;
- handlers that recover by producing replacement success results;
- an explicit catch-all;
- generalized patterns and guards;
- async suspension and cancellation;
- detailed outcome reflection schema;
- ABI and lowering;
- generalized effects and resumable handlers; and
- formal grammar and diagnostic identifiers.

Those deferrals do not weaken the current synchronous rule: every invocation
publishes either its complete success shape or exactly one named exceptional
payload, and every exposed exceptional outcome is handled or forwarded
explicitly.
