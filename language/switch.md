# Zax switch, case, and default

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers selecting runtime behavior from one value |
| Applies To | Programmer-facing `switch`, `case`, and `default` behavior; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | Runtime selector capture; ordinary ordered case testing; direct, equality, and pre-unary case interpretation; variant-specific alternative routing and payload binding; comma-separated alternatives; case and switch posts; switch and case labels; switch-specific `break`, `continue`, `next`, and `goto` behavior; `default`, incomplete selection, exhaustiveness, overlap, reachability; case hint and promise slots; `outward` declarations in cases; costs, diagnostics, formatting, and source stability |
| Does Not Own | Shared flow-transfer and unwinding rules ([core flow control](core-flow-control.md)); ordinary operator selection ([operators](operators.md)); complete optional behavior ([optional values](optional-values.md)); complete managed-alternative lifecycle ([variants](variants.md)); enum reachable domains ([enums](enums.md)); general source layout ([source structure](source-structure.md)); or generalized pattern matching |
| Source / Provenance | Current flow, operator, enum, optional, lifetime, source, and intent designs, incorporating reviewed legacy switch evidence |
| Supersedes | Legacy switch sections formerly preserved on [flow control](../flow-control.md) |

## Start with one selected body

`switch` evaluates one value and chooses the first matching `case`:

```zax
switch status {
  case Status.Ready
    startWork()
  case Status.Waiting
    showWaiting()
  default
    reportUnknownStatus()
}
```

Cases are tested from top to bottom. The first successful case runs exactly one
body, and normal completion of that body exits the switch. Zax does not
implicitly flow into the following case, so a trailing `break` is not required.

`default` runs only when every preceding case test fails. It is optional. A
switch with no match and no `default` runs no body and completes normally.

Several alternatives can select one body:

```zax
switch responseCode {
  case 200, 201, 204
    handleSuccess()
  case 400, 404
    handleClientFailure()
  default
    handleOtherResponse()
}
```

Alternatives are tested left to right. Once one succeeds, later alternatives and
later cases are not evaluated.

## Evaluate and retain the selector once

The selector expression evaluates exactly once:

```zax
switch selected := readResponse() ;; selected {
  case expectedResponse
    use(selected)
  default
    reportUnexpected(selected)
}
```

The initializer runs once, then the selector evaluates once. The `selected`
binding remains visible to every case test and body until the complete switch
exits.

A named selector is observed through a reference to its existing place. A
temporary selector is materialized once, retained for the complete switch
lifetime, and tested through a reference to that temporary. Selection does not
silently copy either form.

```zax
switch makeResponse() {
  case expectedResponse
    handleExpected()
  default
    handleUnexpected()
}
```

The temporary has no invented source name. Use an initializer when a body or post
needs to name it.

The selector is the receiver for every selector-relative test. Its receiver
qualification and transfer stance participate in ordinary operation selection.
An existing place can therefore be mutated when the selected operation permits
it. A retained temporary can offer `last` because its lifetime really ends with
the switch, but receiver stance does not itself transfer the selector as a
right-hand argument. No test ends the retained selector lifetime before the
switch exits.

Complete receiver stance and qualification behavior belongs to
[transfer stances](transfer-stances.md) and [qualifiers](qualifiers.md).

## Switch headers

`switch` uses the shared conditional header shapes:

```text
selector
initializer ;; selector
initializer ;; selector ;; post
;; selector ;; post
```

The initializer accepts one effective statement. Completed initializer bindings
are visible to the selector, every reached test, all clause bodies and posts, and
the switch post. The switch post runs once on normal completion while those
bindings and the retained selector remain alive.

A flow label follows the complete introducer:

```zax
switch choose_response: ;; readResponse() ;; recordCompletion() {
  default
    handleResponse()
}
```

Header separator spacing and continuation are defined by
[source structure](source-structure.md#header-sections-and-separators). Shared
initializer, post, and binding-scope behavior is defined by
[core flow control](core-flow-control.md#header-schemas-ordering-and-binding-scope).

## Cases, bodies, and posts

A tested case has one or more comma-separated tests, an optional post operation,
and one required effective body statement:

```zax
switch value {
  case small: 1, 2, 3 ;; recordSmall() {
    handleSmall(value)
  }

  case 5
    handleFive(value)

  default fallback: ;; recordFallback()
    handleFallback(value)
}
```

The common conceptual source shapes are:

```text
case [shadowable label:] test [, test ...] [;; post] effective-body
default [shadowable label:] [;; post] effective-body
```

The optional label follows `case` or `default`. The optional post follows the
test list after `;;`; a `default` begins its post with `;;`. Transfer-only cases
are introduced later with the `goto` behavior that makes them reachable.

One effective body can be simple, composed with `;`, or braced:

```zax
case 1
  handleOne()

case 2
  prepare();
  handlePrepared()

case 3 {
  prepare()
  handlePrepared()
}
```

Every clause owns a scope around its complete effective body. Operands of a
composed body share that scope, but body bindings do not escape to another
clause:

```zax
case 1
  myValue := makeValue();
  use(myValue)
case 2 {
  use(myValue) // error: myValue belonged to the preceding clause
}
```

Every tested case and every default requires a body. Consecutive bodyless tests
never share a later body:

```zax
case -1 // error: tested case has no body
case > 2 {
  handleLarge()
}
```

Use comma alternatives for a shared body:

```zax
case -1, > 2 {
  handleSelected()
}
```

Use `{ }` to select and intentionally do nothing:

```zax
case -1 { }
```

`case 0 ;` is invalid because `;` lacks a right statement operand. `case 2 {}`
is invalid because the established empty-block spelling is `{ }`.

Body-local values are destroyed before the clause post runs. The post can use
switch-header bindings and surrounding state but cannot use body locals.

Cases have no initializer section. In particular, the following is not a case
header shape:

```zax
// case local := makeValue() ;; local > 15 // error: cases have no initializer
```

Use ordinary body declarations and nested selection when later work needs a
named value.

Zero or more positional `default` clauses may appear. Each is the unconditional
fallback for the ordered search that reaches it. Its body must explicitly
`continue` when selection should proceed into a later test segment.

## How one case test is interpreted

For an ordinary non-variant selector, every comma-separated alternative must
ultimately produce exactly `Boolean`. The case supplies the retained selector as
an omitted operand through three ordered interpretations.

A variant selector instead uses the dedicated
[alternative-routing mode](#variant-alternative-selection); its case names are
not expressions passed through these interpretations.

### 1. Try a direct selector-relative operation

First use the selector as the omitted left or receiver operand:

```zax
switch value {
  case < upperBound
    handleBelow()
  case 'is hot'
    cool(value)
}
```

The tests mean:

```zax
value < upperBound
value is hot
```

This stage accepts binary and post-unary symbolic or phrase operations. Ordinary
operator discovery, operand mapping, viability, preference, qualification, and
availability apply.

If one direct interpretation selects an exact-`Boolean` operation, it wins.
Direct ambiguity and a uniquely best unavailable exact-`Boolean` operation are
errors rather than permission to try another interpretation.

If no direct interpretation applies, or the direct result is not exactly
`Boolean`, resolution tries implicit equality.

### 2. Try equality with the written expression

The complete case alternative becomes the right operand of `==`:

```zax
switch value {
  case expectedValue
    handleExpected()
  case -amount
    handleNegativeAmount()
}
```

The tests mean:

```zax
value == expectedValue
value == (-amount)
```

The selected equality must return exactly `Boolean`. It uses the ordinary
operator model, including direct/complement fallback and unavailable-best
behavior.

Grouping commits to equality interpretation:

```zax
case (similar expected)
// value == (similar expected)
```

An explicit operator states the same intent directly:

```zax
case == (similar expected)
```

### 3. Try an omitted-operand pre-unary operation

Finally, a case alternative containing an exact pre-unary operator component and
no supplied operand may apply that operation to the selector:

```zax
switch optionalValue {
  case ?
    use(optionalValue.)
  case !
    handleAbsent()
}
```

These tests mean:

```zax
?optionalValue
!optionalValue
```

A phrase can use the same rule:

```zax
case 'bad'
// bad value, when `bad` is an applicable pre-unary phrase
```

This stage never searches arbitrary right-to-left binary trees. It supplies only
the missing sole operand of a recognized pre-unary component.

An exact phrase fence fixes the phrase component. It does not override ordinary
operation selection. Complete phrase behavior belongs to
[operator phrases](operator-phrases.md).

### Direct-before-equality source stability

The ordered interpretations are part of the switch contract. Adding a newly
applicable exact-`Boolean` direct operation can change a case that previously
used equality:

```zax
case similar expected
```

It may change from:

```zax
value == (similar expected)
```

to:

```zax
value similar expected
```

Use grouping or explicit `==` when the equality interpretation must remain
fixed. Tooling should show which interpretation and operation each case selected.

## Ordering, effects, and overlap

Selection proceeds in visible order:

1. visit clauses from top to bottom;
2. visit one case's alternatives from left to right;
3. evaluate each reached alternative's runtime operands once;
4. invoke its selected operation;
5. stop testing on the first `true` result; and
6. execute that clause body once.

Case expressions and user-defined operations may have effects:

```zax
switch value {
  case nextCandidate()
    useFirstMatch()
  case fallbackCandidate()
    useSecondMatch()
}
```

`fallbackCandidate()` does not run when the first test succeeds. Optimization
may use a table or another dispatch mechanism only when evaluation, selected
operations, effects, panic behavior, lifetimes, and the chosen body remain
unchanged.

Overlap is legal and frequently useful:

```zax
switch temperature {
  case < freezingPoint
    reportFrozen()
  case < boilingPoint
    reportLiquid()
  default
    reportAtOrAboveBoiling()
}
```

The second predicate receives only values not selected by the first. Zax does not
assume purity, algebraic laws, or repeatability for arbitrary user-defined
operations.

A clause body is reachable through any reachable successful test, positional
`default`, `continue` search, or direct `goto` body entry:

```zax
switch mode {
  case MyMode.Primary
    if usePrimaryFails()
      goto alias_version:

  case alias_version: MyMode.PrimaryAlias
    useAlias()
}
```

Although generated equality prevents ordinary selection of the alias body after
`Primary` matched, `goto alias_version:` makes it reachable. A self-edge inside
an otherwise unreachable body does not bootstrap reachability.

A clause still proven unreachable under the complete flow graph requires a
clause-local acknowledgement:

```zax
switch mode {
  case MyMode.Primary
    usePrimary()

  // PrimaryAlias has the same value as Primary under generated equality.
  intent<unreachable-selection-clause>{
    case MyMode.PrimaryAlias
      useAlias()
  }
}
```

The enclosure occupies one complete contextual clause position and includes the
required body. It cannot enclose only a test while borrowing a body from outside.
Uncertain or runtime-dependent overlap requires no acknowledgement.

## Case hints and promises

The [branch hints and path promises](core-flow-control.md#branch-hints-and-path-promises)
used on `if` also apply to cases. The directive goes in the slot after the
`case` tests or after `default`, and applies to that clause's test, not to its
body:

```zax
switch value {
  case 1 [<likely>]
    handleCommon()
  case 2
    handleOther()
  default [<unlikely>]
    handleUnexpected()
}
```

Hints never change which clause is selected or which test effects happen. The
ordering rules above still hold: a compiler may try a likely clause's test
first only when that cannot change the selected clause, the operations that
run, or their effects. With overlapping tests, or tests that call functions,
the visible order stays.

`[<never>]` on a case promises that the case is never selected.
`[<always>]` promises that it is always the selected case:

```zax
switch mode {
  case MyMode.Legacy [<never>]      // promise: never selected
    legacyPath()
  case MyMode.Modern [<always>]     // promise: always the selected case
    modernPath()
}
```

These are checked by default, like the `if` forms: breaking one panics in the
`never-path-reached` or `always-path-missed` category, and disabling that
category makes a broken promise undefined behavior.

## Outward declarations in cases

An [`outward`](declarations-and-bindings.md#outward-declarations) declaration in
a case body belongs to the scope around the `switch`. Every path that continues
past the `switch` must declare it exactly once, with the same type and
qualifiers. A case that leaves the `switch` another way needs no declaration:

```zax
switch mode {
  case 1
    limit outward final : Integer = 10
  case 2
    limit outward final : Integer = 1000
  case 3
    return                            // leaves: needs no limit
  default
    limit outward final : Integer = 100
}

process(limit)
```

When one body enters another with `goto`, both bodies run on that path, so only
one of them may declare a given outward name.

To select a case while compiling, so that only the selected body becomes part of
the program, write `switch compile`; see
[compile-time code](compile-time-code.md#if-compile-and-switch-compile).

## Normal completion and posts

After a selected body completes normally:

1. destroy its body-local values;
2. run its clause post;
3. run the switch post;
4. destroy the retained selector and switch-header bindings; and
5. exit the switch.

When no case matches, `default` runs when present. Without `default`, the switch
runs no clause. Either normal path still runs the switch post.

A post is ordinary work, not guaranteed cleanup. Transfers select which posts
run, while every exited scope is still destroyed. Shared post and unwinding
behavior belongs to [core flow control](core-flow-control.md).

Every re-entry is a complete scope transition. `next`, `continue`, and `goto`
first destroy the old body scope in reverse construction order. Any resulting
entry begins again at the target body's first statement with fresh body-local
lifetimes. `next` runs post and ordinary test/progression first; `continue` skips
post but performs ordinary test/progression; `goto` skips both. Active
target-header lifetimes remain alive throughout re-entry.

## Transfer summary

`next`, `continue`, and `goto` select three different phases:

- `next` performs the target's normal-completion post phase;
- `continue` skips post and resumes the target's testing or progression;
- `goto` skips post and testing and enters an eligible body directly.

| Transfer | Target point | Target test | Target post | Later behavior |
| --- | --- | --- | --- | --- |
| Normal clause completion | Current clause completion | Already succeeded | Run case post | Run switch post, exit |
| `next current_case:` | Current clause completion | Do not repeat | Run case post | Run switch post, exit |
| `next switch_label:` | Switch completion | Do not repeat | Run switch post | Exit |
| `continue case_label:` | Target clause's test entry | Run target tests | Skip source case post | If false, continue with following clauses |
| `continue switch_label:` | First switch test | Restart testing | Skip source case and switch posts | Retain the existing selector |
| Bare `goto` in a switch clause | Following unlabeled clause's body entry | Bypass | Skip source case post | Target post runs if its body later completes normally |
| `goto case_label:` | Target clause's body entry | Bypass | Skip source case post | Target post runs if its body later completes normally |
| `break` / `break switch_label:` | Switch exit | Bypass | Skip case and switch posts | Exit |

```zax
switch choose_value: ;; value ;; recordSelection() {
  case first: > 15 ;; recordFirst() {
    if shouldRetestSecond()
      continue second: // run second:'s tests

    if shouldEnterSecondDirectly()
      goto second: // bypass second:'s tests

    if shouldFinishFirst()
      next first: // recordFirst(), recordSelection(), then exit

    if shouldFinishSwitch()
      next choose_value: // skip recordFirst(), recordSelection(), then exit
  }

  case second: < 100 ;; recordSecond() {
    handleSecond()
  }

  default fallback:
    handleFallback()
}
```

This table summarizes complete operations. Post execution and test/body entry
are not separate hooks that can be combined arbitrarily.

## `continue`: resume testing

Bare `continue` resumes testing after the current clause:

```zax
switch value {
  case 1 {
    updateValue()
    continue // begin testing at case 2
  }
  case 2
    handleTwo()
}
```

A bare `continue` skips intervening transfer-only cases while looking for the
next tested case or positional `default`. In the final searchable segment it is
an error. It does not silently select an outer construct; use an explicit outer
label.

`continue case_label:` begins with the named case's first alternative. When all
its alternatives fail, testing continues with later clauses:

```zax
switch value {
  case retry_small: < 10
    handleSmall()

  case retry_large: > 100 {
    adjust(value)
    continue retry_small:
  }
}
```

The target may precede or follow the source. Backward continuation can form an
intentional cycle.

`continue` cannot name a transfer-only case because that label has no test entry.
`continue default_label:` enters that default body because `default` is an
unconditional search fallback. `continue switch_label:` restarts at the first
case without rerunning the switch initializer or selector expression.

Every switch-local `continue` destroys the source body scope and skips its case
post. The switch remains active, so its post does not run yet.

## `goto`: enter a body directly

Bare `goto` enters the immediately following unlabeled clause body without
running its tests:

```zax
switch value {
  case 0 {
    prepare()
    goto // enter case 1's body directly
  }

  case 1
    usePreparedState()
}
```

A labeled following clause blocks bare transfer:

```zax
case 0 {
  goto // error: ready: must be named
}

case ready: 1
  usePreparedState()
```

Bare `goto` in the final clause is an error and never silently selects an outer
construct. Outside a switch, bare `goto` directly restarts the nearest eligible
unlabeled active loop, `forever`, `each`, or explicit `scope` body.

`goto case_label:` bypasses the target tests:

```zax
switch state {
  case read: State.Read {
    readNext()
    goto process:
  }

  case process: State.Process {
    processCurrent()

    if needsMoreWork()
      goto read:
  }
}
```

Forward and backward targets are legal. This can express a compact state machine
and can intentionally run forever.

Before entry, `goto` destroys every exited clause or nested scope in reverse
construction order. The old body scope ends completely. The target then receives
a fresh body scope entered from its beginning; no body-local value survives the
transfer. Active target-header lifetimes remain alive. When the new body later
completes normally, its own case post and the switch post run.

`goto` does not carry facts established by bypassed tests:

```zax
switch optionalValue {
  case present: ? {
    use(optionalValue.) // error if the goto path below lacks presence proof
  }

  default {
    goto present:
  }
}
```

The body must be valid under the facts common to every incoming path.

Case `goto` is one application of the shared active-body transfer defined by
[core flow control](core-flow-control.md#direct-body-entry-with-goto). It is not
an arbitrary jump into the middle of source.

There is no special `goto default` form. Nonadjacent direct entry to a default
requires an explicit declared label such as `goto fallback:`.

Every combined `goto ... with erase` form is invalid. Erasure ends the current
`each` entry while direct body re-entry requires that entry to remain live.
Perform an explicit targeted erase first, then transfer beyond that traversal.

### Transfer-only cases

A transfer-only case has a required label, no tests, an optional post, and one
required body:

```text
case [shadowable] label: [;; post] effective-body
```

It may appear before, between, or after tested cases and defaults. Ordinary
searches skip it without running effects or its post.

```zax
switch state {
  case failure: {
    handleFailure()
  }

  case State.Ready {
    if failed()
      goto failure:

    run()
  }
}
```

`failure:` is reachable through the backward `goto`. Without a reachable incoming
edge, its body is unreachable and needs
`intent<unreachable-selection-clause>{...}`.

A transfer-only label is eligible for `goto`, and for `next` while its body is
active. It is not eligible for `continue` because it has no test entry.

## `next`, `break`, and outward transfer

`next` can target the active case or the switch:

```zax
switch choose: ;; value ;; recordSwitch() {
  case chosen: 1 ;; recordCase() {
    if finishCase()
      next chosen: // run recordCase(), then recordSwitch(), then exit

    if skipCasePost()
      next choose: // run recordSwitch(), then exit
  }
}
```

It cannot target a sibling case. A bare `next` selects the nearest eligible
unlabeled target under the shared target rules.

`break` exits the complete switch, skips both posts, and still destroys every
scope it exits. `return` and transfers to outer constructs likewise skip the
posts belonging to the switch they leave.

### Redundant tail `break`

Normal completion already exits the switch, so a trailing switch-targeting
`break` usually teaches the wrong fallthrough model:

```zax
switch value {
  case 1
    doSomething();
    break // error: normal case completion already exits the switch

  case 2 {
    if condition()
      break // valid: skips doSomethingElse()

    doSomethingElse()
  }

  case 3 {
    doSomethingAgain()

    if condition()
      break // error: both paths now exit the switch identically
  }
}
```

A switch-targeting `break` is a non-acknowledgeable intent error when replacing
it with normal clause completion would execute no additional body work or post
before leaving the same switch. This is semantic tail position, not merely the
last textual token.

Scope destruction does not distinguish the two paths: both destroy the same
exited lifetimes in the same order.

A post or outer target makes the transfer meaningful:

```zax
switch ;; value ;; recordSwitch() {
  case 1 ;; recordCase() {
    doSomething()
    break // valid: skips both posts
  }
}

while outer: moreWork() {
  switch value {
    case 1
      break outer: // valid: exits the loop
  }
}
```

Effect analysis does not erase the source-semantic distinction created by a
written post.

## Labels and target visibility

Tested-case and default labels are known throughout their containing switch for
`continue` and `goto`. They therefore permit forward and backward transfer.

A transfer-only case label has no test entry. It is eligible for `goto`, and for
`next` while its body is active, but not for `continue`.

The currently executing case is also a `next` target because it has a
normal-completion post phase. A sibling case is not a `next` target, and no case
is a `break` target.

`shadowable` follows the established label placement:

```zax
switch shadowable choose: value {
  case shadowable retry: 1 {
    // A nested eligible target may reuse retry: with permission.
  }
}
```

Target lookup considers the written transfer keyword and the source position.
When a nearer same-named label is ineligible but an outer label is eligible,
selecting the outer target is defined but suspicious:

```zax
while shadowable retry: condition {
  switch value {
    case retry: 1 {
      next retry: // current case is eligible
    }

    case 2 {
      intent<outer-target-through-ineligible-label>{
        next retry: // deliberately target the outer loop
      }
    }
  }
}
```

Without the acknowledgement, the second `next retry:` is an intent error. For
`continue` and `goto`, sibling `retry:` is eligible and hides the outer target
normally.

Complete shared label spelling, shadowing, eligibility, and active-target rules
belong to [core flow control](core-flow-control.md#flow-labels-and-transfer-targets).

## `default`, incomplete selection, and exhaustiveness

`default` is optional and positional. It is the unconditional fallback for the
ordered search that reaches it. A switch may contain several defaults when
explicit continuation creates several test segments:

```zax
switch normalized := value ;; normalized {
  case < 0
    handleNegative()

  default normalize: {
    normalized = normalizeValue(normalized)
    continue
  }

  case < 10
    handleSmallNormalized()

  case < 100
    handleMediumNormalized()

  default out_of_range:
    handleLargeNormalized()
}
```

Initial search reaches `normalize:` when `< 0` fails. Its `continue` destroys
that body scope and resumes after the default. `out_of_range:` is then the
fallback for the later segment.

A search can begin from initial switch entry, `continue switch_label:`,
`continue case_label:`, or bare `continue`. During that search:

1. transfer-only cases are skipped;
2. tested cases run in source order;
3. the first successful test selects its body;
4. the first reached `default` selects unconditionally; and
5. reaching the end selects no clause and completes normally.

A default reachable only through `goto` and through no ordered search is a
non-acknowledgeable intent error. Write it as a transfer-only case instead.

Body-local calculations do not survive `continue`. State needed by a later
segment must live in the switch header or surrounding scope, or the later
selection should be nested inside the default body.

Every no-match path participates in construction and result-completion analysis.
Required state must be complete after each normally completing case or default
and after each search that reaches the end without selecting a clause.

### Enum coverage has two dimensions

Enum selection audits two different questions:

1. **Declared-member coverage:** did the source explicitly handle every distinct
   declared member value for the applicable search entry?
2. **Reachable-domain coverage:** can every other reachable backing value select
   a body?

`default` satisfies the second question for the segment that reaches it. It does
not count as explicit handling of omitted declared members.

Member names also do not necessarily cover every enum value:

- a strict owner operation may produce an unnamed value;
- relaxed enums admit every backing value;
- flags admit unnamed combinations;
- `unsafe from` can establish any backing representation;
- duplicate-valued names overlap; and
- an empty strict enum may hold its unnamed backing default.

```zax
switch permissions {
  case Permission.Read
    allowRead()
  case Permission.Write
    allowWrite()
  default
    handleOtherPermissionValue()
}
```

This source handles both declared members explicitly and uses `default` for
unnamed or unsafe-admitted values. Adding a new declared member makes it
member-incomplete even though `default` remains.

A deliberately partial enum switch requires:

```zax
intent<partial-enum-selection>{
  switch status {
    case Status.Ready
      startWork()
    default
      handleOtherDeclaredOrUnnamedStatus()
  }
}
```

Duplicate-valued aliases count as one distinct enum value. Flags and relaxed
enums still need a fallback or another domain proof for unnamed values. Coverage
is assessed for every reachable search entry; a later segment does not
retroactively complete the initial one. Arbitrary user-defined predicates count
only when the language can prove their covered domain.

Only reachable tests count as explicit member coverage:

| Entry path | Body reachable | Test reachable | Counts as explicit member coverage |
| --- | --- | --- | --- |
| Ordinary successful test | Yes | Yes | Yes |
| `continue member_case:` followed by a successful test | Yes | Yes | Yes, for that search entry |
| `goto member_case:` | Yes | No | No |
| No reachable incoming edge | No | No | No |

A case physically following a positional default does not count merely because
its member name appears in source. `goto` may make its body reachable while still
bypassing its test. Only a reachable ordered search through that test can
contribute coverage.

Complete enum admission and operation behavior belongs to
[enums](enums.md#selection-with-enum-values).

## Optional presence and nested layers

The recognized optional-presence operation works through the pre-unary case
interpretation:

```zax
switch optionalValue {
  case ?
    use(optionalValue.)
  default
    handleAbsent()
}
```

The successful `?` test proves the exact boxed lifetime present in its ordinary
body entry.

Failure of a recognized absence test can establish presence for later ordinary
selection:

```zax
switch optionalValue {
  case !
    handleAbsent()
  default
    use(optionalValue.) // recognized absence test failed
}
```

Proof at a shared body is the intersection of all alternatives that can select
it:

```zax
case ?, emergencyMode {
  use(optionalValue.) // error if emergencyMode can succeed while absent
}
```

`continue` retests and can establish the target test's facts. `goto` enters the
body directly and cannot supply facts from bypassed tests. Analysis considers
every incoming path.

Nested optionals cross one wrapper at a time:

```zax
switch nested {
  case ? {
    switch nested. {
      case ?
        use(nested..)
      default
        handleInnerAbsent()
    }
  }

  default
    handleOuterAbsent()
}
```

Cases do not bind optional payloads. Postfix `.` remains proven access, not a case
test:

```zax
// case . > 15 // error: postfix access is not a Boolean case test
```

Use ordinary access and nested selection:

```zax
switch optionalValue {
  case !
    handleAbsent()
  default {
    value : MyValue & = optionalValue.

    switch value {
      case > 15
        handleLarge(value)
      default
        handleOther(value)
    }
  }
}
```

Complete wrapper state, per-layer proof, and boxed lifetime behavior belongs to
[optional values](optional-values.md).

## Variant alternative selection

When the retained selector has [variant](variants.md) type, `case` enters a
dedicated alternative-routing mode. Case entries name alternatives rather than
ordinary expressions or Boolean operations:

```zax
switch message {
  case text {
    print(text)
  }

  case number, alternateNumber bind selectedNumber {
    print(selectedNumber)
  }

  case ! {
    handleAbsent()
  }
}
```

### Names route and bind payloads

`case text` resolves `text` only in the selector variant's alternative
namespace. A successful route introduces clause-local reference-shaped payload
access under that same name.

Several alternatives may share one body. `bind` gives them one common local:

```zax
case number, alternateNumber bind selectedNumber {
  print(selectedNumber)
}
```

The body is checked independently for every alternative that can route to it.
It is valid only when every specialization succeeds. A diagnostic identifies
the failing alternative, its payload type, and the unavailable operation.

A several-name entry without `bind` only routes control and introduces no
payload local. This lets several states share behavior that does not inspect the
payload.

The binding performs no copy. It preserves payload origin, qualifications, and
the transfer stance projected from the retained selector. An accepted later
consumer performs any move or terminal transfer.

Ordinary local shadowing rules apply. Rename a same-name alternative when
needed:

```zax
case text bind selectedText {
  print(selectedText)
}
```

### Presence and absence entries

Variant mode reserves:

```zax
case ? bind selected
  handlePresent(selected)

case !
  handleAbsent()
```

`case ?` selects any present alternative that reaches it. With `bind`, its body
is specialized for every such payload type. Bare `case ?` introduces no payload
binding. `case !` selects absence and introduces none.

Ordering remains visible. Specific names can appear before the present catch-all:

```zax
switch message {
  case text
    handleText(text)
  case ? bind other
    handleOtherPresent(other)
  case !
    handleAbsent()
}
```

`default` retains its ordinary positional fallback meaning. It can receive
absence and any present alternative not selected earlier. It does not
intrinsically mean absence.

### Coverage has two dimensions

Variant selection audits:

1. **Alternative coverage:** every declared name is handled by a named case or
   `case ?`.
2. **State coverage:** absence reaches `case !` or `default`.

`default` satisfies fallback state coverage but does not silently count as
explicit handling of omitted alternative names. Deliberately partial source
uses:

```zax
intent<partial-variant-selection>{
  switch message {
    case text
      handleText(text)
    default
      handleOtherState()
  }
}
```

The acknowledgement permits omitted names without changing order, fallback, or
payload binding. Adding an alternative makes a named-complete switch incomplete.
`case ?` deliberately accepts later alternatives when its new body
specialization succeeds.

Coverage is assessed for every reachable ordered-search entry, just as enum
coverage is. A direct body entry bypasses the route and contributes no
alternative coverage.

### Transfers and binding establishment

A payload-bound clause body cannot be a `goto` target: direct entry would
bypass routing and could not construct its local binding.

`continue case_label:` may target a routed variant clause because it starts at
that clause's route entry. It enters the body only after matching the current
selection and establishing the binding; if it does not match, search continues.

Bare `case ?`, `case !`, `default`, and transfer-only clauses create no payload
binding. An otherwise eligible `goto` may enter such a body, but that path
inherits no presence or absence fact from the bypassed route. The body remains
valid only under facts common to every incoming path.

`next`, `break`, clause and switch posts, scope destruction, and outward
transfer retain their ordinary behavior.

This finite variant mode is not generalized pattern matching. It provides no
recursive destructuring, structural pattern, guard, or optional-payload
flattening.

## Empty selection

A switch containing no clauses still evaluates its initializer and selector and
runs its post on normal completion. It nevertheless strongly resembles
unfinished source and requires explicit acknowledgement:

```zax
intent<empty-selection>{
  switch initialize() ;; selectValue() ;; recordCompletion() { }
}
```

An ordinary block is clearer when only sequential work is intended.

## Costs

The visible baseline cost is:

- one initializer execution when written;
- one selector evaluation and no hidden copy;
- one evaluation and selected operation for every reached case alternative;
- one selected body, or none on incomplete selection;
- applicable case and switch posts;
- destruction for every exited scope; and
- repeated tests or bodies when `continue` or `goto` cycles.

There is no constant-time dispatch promise. User-defined operations, runtime
case expressions, mixed operators, mutation, and observable effects may require
ordered testing.

Variant mode tests the represented active name or absence rather than invoking
ordinary case operators. A polymorphic `bind` body adds compile-time checking
and potentially generated code for each routed payload type; ordinary
optimization may merge equivalent specializations without changing behavior.

## Diagnostics

Diagnostics should distinguish:

- no viable direct or equality interpretation;
- direct or equality ambiguity;
- a uniquely best unavailable operation;
- a selected result that is not exactly `Boolean`;
- malformed case alternatives or comma continuation;
- a missing or malformed effective body;
- an unlabeled testless case;
- `;` without a right effective statement or compact `{}` where `{ }` is
  required;
- a default reachable only through `goto`, which should be a transfer-only case;
- proven unreachable selection requiring
  `intent<unreachable-selection-clause>{...}`;
- omitted declared enum members requiring
  `intent<partial-enum-selection>{...}`;
- an unknown variant alternative name or a lexical expression used where
  variant mode requires an alternative;
- a polymorphic variant body that fails for one routed alternative;
- omitted variant alternatives requiring
  `intent<partial-variant-selection>{...}`;
- a variant `goto` target whose payload binding cannot be established;
- empty selection requiring `intent<empty-selection>{...}`;
- outer-target reach-through requiring
  `intent<outer-target-through-ineligible-label>{...}`;
- bare `continue` with no following clause;
- `continue` naming a transfer-only case with no test entry;
- bare `goto` with no following clause or stopped by a labeled eligible target;
- `next` naming a sibling case;
- `goto` naming an ineligible or inactive target;
- any combined `goto ... with erase` form;
- missing proof on one matched, continued, or direct-entry path;
- use after the selector or a clause-local lifetime ends;
- incomplete state on a no-match path;
- redundant switch-targeting tail `break`; and
- legacy `case continue` or another unsupported combined spelling.

`unreachable-selection-clause`, `partial-enum-selection`,
`partial-variant-selection`, `empty-selection`, and
`outer-target-through-ineligible-label` are acknowledgement-required intent
errors with the enclosure shapes shown above.

Redundant tail `break` is non-acknowledgeable. It must be removed unless it
actually skips a case/switch post or targets an outer construct.

A default reachable through no ordered search is likewise non-acknowledgeable:
rewrite it as a transfer-only case rather than preserving misleading fallback
spelling.

## Formatting

- `switch`, its optional `shadowable` label, and the first header section begin
  on one physical line unless explicit continuation moves the complete header.
- The outer switch clause region is braced.
- `case` and `default` align one level inside the switch.
- A clause label immediately follows `case` or `default`.
- A transfer-only case has a required label followed directly by its optional
  post or body.
- A comma has no preceding whitespace and requires following whitespace.
- A comma continues the test list across a following indented line.
- In variant mode, `bind` follows the complete alternative list and precedes
  its body or post.
- `;;` has whitespace on both sides.
- A simple or composed body begins on the following line one level deeper.
- A braced body opens on the final physical clause-header line.
- A multiline body-closing `}` aligns with its clause keyword.
- An explicit transfer target retains its trailing `:`.

Complete token, continuation, indentation, separator, and brace rules belong to
[source structure](source-structure.md).

## Source stability

- Case order and comma-alternative order are semantic.
- Adding a direct exact-`Boolean` operation may replace implicit equality for
  otherwise unchanged ungrouped case source.
- Grouping or explicit `==` preserves equality intent.
- Adding a case can change which body first matches.
- Adding a positional default changes where an ordered search stops unless its
  body explicitly continues.
- Adding a post changes normal completion and whether a terminal `break` is
  redundant.
- Adding or moving a label can change target lookup or require intent
  acknowledgement, but cannot silently redirect a transfer.
- Adding an enum member makes a previously member-complete switch incomplete
  unless it already acknowledges partial enum selection; `default` continues to
  cover the applicable unnamed domain rather than the omitted declaration.
- Adding a variant alternative makes a named-complete switch incomplete.
  `case ?` deliberately accepts the new alternative only when its additional
  body specialization succeeds.
- Improved analysis may identify a language-defined unreachable clause and
  require its existing acknowledgement; it cannot reorder observable tests.
- Formatting and source-preserving tools retain phrase fences, grouping,
  alternative order, labels, posts, and intent acknowledgements.

## Boundaries and maturity

This document defines current conceptual runtime value selection. It is not a
formal grammar, implementation mapping, compatibility contract, or conformance
specification.

Still future:

- generalized or recursive patterns;
- payload destructuring beyond the accepted variant binding;
- pattern guards;
- unmanaged-union selection, which has no active-lens state;
- value-producing selection and result convergence;
- mixfix or other multi-hole case tests;
- compile-time dispatch strategies beyond `switch compile`;
- generic specialization and reflection;
- formal reachability, path-merge, and termination algorithms; and
- compiler lowering or dispatch-table strategy.

Future pattern syntax must remain distinct from current Boolean case tests and
must account for direct body entry that bypasses test-established proof.
