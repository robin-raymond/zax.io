# 018: Switch, cases, and value selection

| Field | Value |
| --- | --- |
| Status | Historical working record / non-normative / audit-only |
| Work Item | `018` |
| Created | 2026-09-08 |
| Closed | 2026-09-09 |
| Owns | Historical record of the bounded review defined by the fixed initiating input |
| Does Not Own | Complete generalized pattern matching, arbitrary destructuring, compile-time selection, generics, reflection, variants and unions, or query/comprehension syntax |

## Non-authority notice

This file is a historical, audit-only record of collaborative design work.
Accepted findings were promoted into their lasting owners before closure. This
record remains non-authoritative and is not part of ordinary design reading.

## Fixed initiating input

This section records the information aligned when work item `018` was created.
It is intentionally incomplete and must not be rewritten as work develops.

### Initiating concern

Develop a coherent human-developer-facing model for runtime value selection
through `switch`, `case`, and `default` without prematurely designing a complete
generalized pattern-matching language.

The review should establish:

1. selector evaluation, initialization, binding scope, and body structure;
2. case ordering and comparison behavior;
3. first-match, multi-match, overlap, and unreachable-case behavior;
4. explicit fallthrough or the disposition of legacy `case continue`;
5. `break`, `next`, `continue`, labels, and transfer targets;
6. `default`, exhaustiveness, and incomplete selection;
7. runtime case expressions and user-defined comparison operations;
8. enum members, aliases, unnamed values, flags, and unsafe-admitted values;
9. bounded optional absence, presence, nested-layer, and payload-binding
   pressure;
10. scope, lifetime, cleanup, and result completeness;
11. costs, diagnostics, formatting, and source stability; and
12. lasting documentation ownership and legacy disposition.

### Motivating pressure

`switch`, `case`, and `default` remain a basic programmer-facing control-flow
hole after core flow and iteration promotion. Legacy flow material proposes
runtime cases, complex values, alternative comparison operators, header
sections, and `case continue`, but those forms have not been reconciled with
current flow, operator, enum, optional, and lifetime behavior.

Current core flow distinguishes `next` from `continue`. Selection must decide
whether the legacy fallthrough intent becomes `case next`, another explicit
form, or no fallthrough operation.

Enum declarations are not automatically exhaustive: strict enums may have
reachable unnamed values, relaxed enums admit all backing values, flags admit
unnamed combinations, aliases overlap, and `unsafe from` can establish any
backing representation. Selection must not infer complete coverage merely from
a list of declared member names.

### Known assumptions

- Conditions and guards that require truth produce exactly `Boolean`.
- A selected clause consumes one effective body statement and owns its body
  scope.
- Transfers unwind exited scopes in reverse construction order.
- `next` runs the target's post operation; `continue` skips it.
- Bare transfers do not silently skip a labeled eligible target.
- Comparison and operator selection use the current operator model.
- Optional layers retain independent presence and boxed lifetimes.
- No compiler implementation exists in this repository.

### Known inclusions

- `switch`, `case`, and `default` mental model and ordinary use.
- Selector evaluation count and visible scope.
- Runtime values and user-defined comparable values.
- Case ordering, overlap, reachability, and selection count.
- Alternative comparison operations when coherent with current operators.
- Fallthrough and the legacy `case continue` consequence.
- Labels, transfer targets, post behavior, and exits.
- Enum selection and non-exhaustive reachable values.
- Bounded optional-state and payload-binding pressure.
- Cleanup, construction completeness, costs, diagnostics, and formatting.
- Lasting ownership and complete legacy/raw disposition.

### Known starting boundaries

- Complete generalized pattern matching.
- Arbitrary destructuring or recursive pattern syntax.
- Compile-time `if`, compile-time dispatch, or compile-time execution.
- Generic constraints and generic specialization.
- General reflection or declaration metadata.
- Complete variants, unions, or algebraic data types.
- Query, comprehension, or functional-pipeline syntax.
- Compiler lowering or dispatch-table implementation.

These boundaries do not erase consequences. Record every material pressure in a
live owner or indexed raw destination.

### Intentionally unresolved framing

- Exact `switch`, `case`, and `default` source shape.
- Whether selection stops at the first match or may execute several cases.
- Whether fallthrough exists and how it is spelled.
- Whether a case is independently labelable or a transfer target.
- Whether `switch` itself accepts initializer and post sections.
- How case expressions select equality or another comparison operation.
- Whether overlap is legal, acknowledged, ordered, or rejected.
- What exhaustiveness means for open value domains and enum reachable values.
- Whether `default` is required, optional, or replaced by another catch-all.
- How optional presence and nested optional states are selected and bound.
- Whether selection is a statement only or later supports an expression form.
- Which pattern-like pressures belong here versus a later focused work item.

### Initial stopping guidance

Stop when the work has:

- established a usable runtime `switch`/`case`/`default` model;
- integrated selector evaluation, case ordering, body selection, labels,
  transfers, cleanup, and completion;
- dispositioned legacy fallthrough, alternative-comparison, complex-value, and
  header evidence;
- established enum and bounded optional selection behavior;
- preserved generalized pattern, variant, generic, reflection, and compile-time
  consequences without designing them prematurely;
- identified exact current owners and indexed deferrals; and
- completed the required documentation-fit dry run.

Do not design complete pattern matching, variants/unions, generics, reflection,
compile-time execution, promote findings, archive this work item, or begin work
item `019` without the separately required discussion, alignment, and
authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  numbered work, promotion, teaching, deferrals, and dry runs.
- [Raw generalized pattern-matching input](../raw/pattern-matching.md) - preserves
  the pattern, payload-binding, and guard remainder after current runtime
  selection was promoted.
- [Switch, case, and default](../../language/switch.md) - now owns
  the promoted `switch`, `case`, `default`, test, post, transfer, enum, and
  optional behavior. The legacy switch sections that supplied primary historical
  evidence were consumed from [flow control](../../flow-control.md) during
  promotion and remain recoverable through Git history.
- Focused [effective bodies and header boundaries](../../language/core-flow-control.md#effective-bodies-and-header-boundaries),
  [header schemas, ordering, and binding scope](../../language/core-flow-control.md#header-schemas-ordering-and-binding-scope),
  [flow labels and transfer targets](../../language/core-flow-control.md#flow-labels-and-transfer-targets),
  and [unwinding, destruction, and completion](../../language/core-flow-control.md#unwinding-destruction-and-completion) -
  constrain clauses, headers, exits, and cleanup.
- Focused [viability, preference, and availability](../../language/operators.md#viability-preference-and-availability),
  [evaluation order](../../language/operators.md#evaluation-order), and
  [assignment and mutation boundaries](../../language/operators.md#assignment-and-mutation-boundaries) -
  constrain comparison selection, evaluation count, and case operations.
- Focused [enum comparisons and operation selection](../../language/enums.md#comparisons-and-operation-selection)
  and [selection with enum values](../../language/enums.md#selection-with-enum-values) -
  constrain enum cases, aliases, unnamed values, flags, and exhaustiveness.
- Focused [presence proof and postfix access](../../language/optional-values.md#presence-proof-and-postfix-access)
  and [nested optionals](../../language/optional-values.md#nested-optionals) -
  constrain absence/presence selection and payload lifetimes.
- Focused [header sections and separators](../../language/source-structure.md#header-sections-and-separators),
  [braces and body boundaries](../../language/source-structure.md#braces-and-body-boundaries),
  and [`else` attachment and layout](../../language/source-structure.md#else-attachment-and-layout) -
  constrain selection source, clauses, continuation, and bodies.

### Consequence-driven

- Read [raw variants and unions](../raw/variants-and-unions.md) only when a
  concrete selected-value shape requires variant coverage or payload binding.
- Read [raw function composition and chaining](../raw/function-composition-and-chaining.md)
  only when callback or combinator selection becomes material.
- Read focused [construction completeness](../../language/construction-and-destruction.md)
  only when case bindings or selected results create a concrete lifecycle
  dependency.
- Read [intent acknowledgements](../../language/intent-acknowledgements.md) when
  defined but suspicious overlap or ordering requires an acknowledgement
  category.
- Read raw compile-time, generic, reflection, or callable-selection input only
  when a concrete selection rule creates a dependency that must be preserved
  rather than solved.

### Audit-only

- `project/archive/`, including work items `001` through `017`.
- Deleted or superseded selection implementation sketches recoverable through
  Git history.

Do not read archived work item `017` during ordinary work on `018`. Its accepted
findings are promoted into current owners, and selection pressure is preserved
in live raw input.

## Working record

Everything below records aligned findings unless a subsection explicitly marks
material as rejected, deferred, or still requiring later documentation
disposition. The findings remain non-authoritative until separately authorized
promotion incorporates them into their lasting owners.

### Aligned programmer model

`switch` is ordered runtime value selection. It evaluates and retains one
selector, tests case alternatives in visible source order, and runs the body of
the first successful clause.

```zax
switch selected := readValue() ;; selected ;; recordSelection(selected) {
  case outside: < lowerBound, > upperBound ;; recordOutsideRange() {
    reportOutsideRange(selected)
  }

  case expected: expectedValue
    handleExpected(selected)

  default fallback:
    handleFallback(selected)
}
```

The example demonstrates the central model:

- `readValue()` runs once.
- `selected` remains visible throughout the switch.
- Alternatives in `outside:` are tested left to right.
- Clauses are tested top to bottom.
- The first true test selects exactly one effective body statement.
- A selected clause's post runs after its body completes normally.
- The switch post runs after the selected clause and its post complete normally.
- `default` runs only when no preceding alternative succeeds.

The compiler need not implement literal linear dispatch. Any transformation must
preserve the same observable outcome, including reached case-expression
evaluation, selected operations, side effects, panic behavior, lifetimes, posts,
and selected body.

### Switch header, selector capture, and scope

`switch` uses the established conditional header schemas:

```text
selector
initializer ;; selector
initializer ;; selector ;; post
;; selector ;; post
```

The flow label follows the complete `switch` introducer:

```zax
switch choose_value: ;; makeValue() ;; recordCompletion() {
  // ...
}
```

The initializer runs once. Its completed bindings are visible to the selector,
all tests, all clause bodies and posts, and the switch post. They are destroyed
when the complete switch exits.

The selector expression evaluates exactly once:

- A selector naming an existing place is retained through a reference to that
  place.
- A selector producing a temporary materializes that temporary once, retains it
  for the complete switch lifetime, and tests through a reference to it.
- There is no hidden selector copy.
- The selector is the receiver for selector-relative binary, post-unary, and
  pre-unary tests.
- Receiver `copy`, `deep`, `move`, or `last` stance participates in operation
  selection without itself transferring the selector as a right-hand parameter.
- The selected operation may have ordinary effects, including mutation when its
  qualifications permit it.
- No test ends the retained selector lifetime before the complete switch exits.

A temporary selector can retain its genuine `last` stance because its lifetime
does end with the switch. The stance is offered to receiver selection, not used
as an implicit value transfer to a case operand.

An unnamed temporary is available implicitly to tests but has no invented source
name. When the body or post needs a name, the programmer uses the ordinary switch
initializer:

```zax
switch selected := makeValue() ;; selected {
  case expectedValue
    use(selected)
}
```

`continue switch_label:` restarts testing from the first clause with this same
retained selector. It does not rerun the initializer or reevaluate the selector.
Mutations performed before that transfer are visible to the repeated tests.

### Clause source, bodies, and posts

A tested case has:

1. optional `shadowable` and a flow label;
2. one or more comma-separated test alternatives;
3. an optional post after `;;`; and
4. one required effective body statement.

A transfer-only case has:

1. a required flow label;
2. no test alternatives;
3. an optional post after `;;`; and
4. one required effective body statement.

It is skipped by ordinary testing and exists as an explicit body-entry target.

Conceptual source shapes are:

```text
case [shadowable label:] test [, test ...] [;; post] effective-body
case [shadowable] label: [;; post] effective-body
default [shadowable label:] [;; post] effective-body
```

This is conceptual source, not a formal grammar.

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

The switch region is braced because it contains a clause sequence rather than
one ordinary body statement. `case` and `default` align as siblings within that
region. Transfer-only cases may appear before, between, or after tested cases and
defaults without participating in ordinary test order.

Each clause consumes one simple, `;`-composed, or braced effective body statement
and owns a scope around it:

```zax
case 1
  myValue := 1;
  use(myValue)
case 5, 6, 7 {
  use(myValue) // error: myValue is not defined in this clause
}
```

The declaration and call after `case 1` are one composed effective statement.
The declaration is visible to the later operand of that statement but does not
escape the clause.

Every tested case requires a body. Consecutive bodyless cases do not share a
later body:

```zax
case -1 // error: tested case has no body
case > 2 {
  handleLarge()
}
```

Use comma alternatives for one shared body:

```zax
case -1, > 2 {
  handleSelected()
}
```

Use an explicit empty block to filter a value intentionally:

```zax
case -1 { }
```

`case 0 ;` is invalid because `;` lacks a right statement operand. `case 2 {}`
is invalid because an empty block uses the established `{ }` spelling.

The body either begins on the following indented line or opens a `{` on the final
physical clause-header line. Commas may continue the test list across lines under
the established comma-list continuation rules:

```zax
case firstCandidate,
  secondCandidate,
  thirdCandidate {
  handleCandidate()
}
```

A clause post is normal-completion work. Body-local values are destroyed before
the post runs. The post can see switch-header bindings and surrounding scope but
cannot see body locals.

A clause has no initializer. Adding one would:

- make the selector-relative test forms difficult or impossible to retain;
- overload the meaning of `;;` between initializer/condition and test/post;
- create initialization and visibility problems for direct body entry;
- require another way to name an otherwise hidden selector temporary; and
- add complexity whose optional-payload use case is clearer as nested selection.

Consistency alone does not justify those costs. Clause initialization is
rejected for the current model.

Zero or more positional `default` clauses may appear. Each has no tests but may
have a label, post, and effective body. It is the unconditional fallback for the
particular ordered search that reaches it. Its body must explicitly `continue`
when testing should resume at a later segment.

### Case-test resolution

Every alternative must ultimately form exactly one operation whose result is
exactly `Boolean`. An alternative uses the following aligned resolution order.

#### 1. Direct selector-relative interpretation

First attempt to use the retained selector as the omitted left or receiver
operand:

```zax
case < upperBound
// selected < upperBound

case similar expected
// selected similar expected

case 'is hot'
// selected is hot
```

This stage accepts selector-relative binary or post-unary source. Symbolic and
phrase operations participate through the ordinary operator model.

If one exact-`Boolean` direct interpretation succeeds, it wins. Direct ambiguity
or a uniquely best unavailable exact-`Boolean` operation is diagnosed rather
than hidden by fallback.

If no applicable direct interpretation exists, or the direct operation does not
produce exactly `Boolean`, resolution proceeds to implicit equality.

#### 2. Implicit equality

The complete slot is treated as an ordinary right-hand expression:

```zax
case expectedValue
// selected == expectedValue

case similar expected
// selected == (similar expected), when direct interpretation did not succeed

case -amount
// selected == (-amount)
```

The retained selector is the left/receiver operand of `==`. Ordinary discovery,
viability, preference, availability, complement fallback, qualifications, and
evaluation behavior apply. The selected equality result must be exactly
`Boolean`.

Grouping commits to this interpretation and prevents the grouped expression from
being treated as a direct selector-relative operation:

```zax
case (similar expected)
case (-amount)
```

An explicitly written equality also states the operation directly:

```zax
case == (similar expected)
```

#### 3. Omitted-operand pre-unary interpretation

If direct use and implicit equality fail, a slot consisting of an exact
pre-unary operator component with no supplied operand may receive the retained
selector as its sole receiver:

```zax
case ?
// ?selected

case 'bad'
// bad selected, when `bad` is an applicable pre-unary phrase
```

This stage does not try arbitrary right-to-left binary trees. It supplies only
the missing sole operand of an exact pre-unary component, and the result must be
exactly `Boolean`.

Post-unary direct use therefore wins before pre-unary use of the same natural
words. An exact phrase fence can establish the intended component, while
grouping continues to commit to equality against an ordinary expression.

#### Source-stability consequence

Direct-before-equality resolution is a deliberate construct-specific fallback.
Adding a newly applicable exact-`Boolean` direct operator can change source that
previously reached implicit equality:

```zax
case similar expected
```

The interpretation can change from:

```zax
selected == (similar expected)
```

to:

```zax
selected similar expected
```

Programmers can lock the equality interpretation with grouping or explicit
`==`. This is an aligned source-stability consequence that lasting teaching and
tooling must make visible; it must not be presented as ordinary phrase-tree
ambiguity or accidental compiler preference.

### Evaluation, ordering, overlap, and reachability

After selector evaluation:

1. visit clauses in source order;
2. within a case, visit alternatives left to right;
3. evaluate a reached alternative's runtime operands once;
4. select and invoke its operation;
5. stop testing immediately on the first `true` result;
6. enter that clause's body scope and execute its body once; and
7. follow the body, clause-post, switch-post, and transfer rules below.

Alternatives in one case behave as an ordered short-circuit selection of one
shared body:

```zax
case > lowerBound, < upperBound, expectedValue
  handleMatch()
```

Later alternatives do not evaluate after an earlier one succeeds. Test
expressions and user-defined operations may have observable effects, so this
order is semantic.

Ordered overlap is legal:

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

The second range overlaps the first mathematically but receives only the
remaining values because the first matching clause wins.

General overlap cannot be decided statically. Runtime values, effects, mutation,
and user-defined comparison behavior prevent the language from assuming purity,
algebraic laws, or stable repeated results.

All alternatives and bodies remain statically validated even when runtime order
will skip them. Runtime short circuit does not make an ambiguous or unavailable
operation, invalid body, or incomplete construction acceptable.

A clause body is reachable when any reachable control-flow edge can enter it:

- a successful ordinary test;
- a `default` reached by an ordered search;
- `continue` beginning or resuming a search that can select it; or
- `goto` entering its body directly.

A direct edge can therefore make a body reachable even when its ordinary test is
shadowed:

```zax
switch mode {
  case MyMode.Primary
    if usePrimaryFails()
      goto alias_version:

  case alias_version: MyMode.PrimaryAlias
    useAlias()
}
```

The alias body is reachable through `goto alias_version:` and does not require
an unreachable-clause acknowledgement. A self-edge inside an otherwise
unreachable body does not bootstrap reachability; some reachable incoming path
must exist.

A clause body still proven unreachable under the complete flow graph is an
acknowledgement-required intent error rather than silently accepted dead source.
`intent<unreachable-selection-clause>{...}` encloses that one complete `case` or
`default` clause. Uncertain or runtime-dependent overlap remains legal.

Generated enum equality can, for example, prove duplicate-valued member clauses
overlap. Replaced user-defined equality cannot be assumed to retain that result
without analysis of the selected operation.

### Transfer and phase model

Selection has three distinct re-entry operations:

- `next` performs the target's normal-completion post phase;
- `continue` skips post and resumes the target's progression or testing phase;
- `goto` skips post and testing and enters an eligible body directly.

These differences are first-class programmer teaching:

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

Representative source:

```zax
switch choose_value: ;; value ;; recordSelection() {
  case first: > 15 ;; recordFirst() {
    if shouldRetestSecond()
      continue second: // run second:'s tests

    if shouldEnterSecondDirectly()
      goto second: // bypass second:'s tests

    if shouldFinishFirst()
      next first: // run recordFirst(), then recordSelection(), then exit

    if shouldFinishSwitch()
      next choose_value: // skip recordFirst(), run recordSelection(), then exit
  }

  case second: < 100 ;; recordSecond() {
    handleSecond()
  }

  default fallback:
    handleFallback()
}
```

This table and example are first-class teaching material, not merely an internal
disposition ledger.

#### Normal completion and no match

When a selected clause body completes normally:

1. destroy its body-local values;
2. run its clause post once;
3. run the switch post once;
4. destroy switch-header state; and
5. exit the switch.

When no case matches:

- run the first `default` reached by that ordered search, then follow its body,
  post, and transfer behavior; or
- run no clause and run only the switch post when `default` is absent.

An empty switch still evaluates its header and runs its post, but it is a defined
and suspicious missing-clause form requiring intent acknowledgement. A block is
normally clearer when only sequential effects are intended.

#### `continue`

Bare `continue` in a clause resumes testing at the following clause. In the final
clause it is an error; it does not silently select an outer flow construct. An
outer target remains available through an explicit label.

`continue case_label:` begins with the named case's first test. If none of its
alternatives succeeds, testing proceeds through later clauses in source order.
The target may precede or follow the source, so repeated testing and intentional
cycles are legal.

Transfer-only cases are skipped during an ordered search. `continue` cannot name
one because it has no test entry. `continue default_label:` enters the labeled
default body because `default` is an unconditional fallback rather than a
transfer-only clause.

`continue switch_label:` restarts testing from the first case with the retained
selector. It does not rerun switch initialization or selector evaluation.

Every form skips the source clause post and switch post while selection remains
active.

#### `goto`

Bare `goto` follows the ordinary bare-target rule. In a switch clause it enters
the immediately following unlabeled clause body without evaluating tests. A
labeled following clause blocks the bare transfer and must be named. Bare `goto`
in the final clause is an error rather than permission to select an outer
construct silently.

Outside selection, bare `goto` restarts the nearest eligible unlabeled active
loop, `forever`, `each`, or explicit `scope` body directly. A labeled nearest
eligible target must be named.

`goto case_label:` enters the named tested case, transfer-only case, or default
body without evaluating tests. It may transfer forward or backward and may
intentionally form an unbounded state-machine cycle. There is no special
`goto default` form; nonadjacent default entry requires an explicit declared
label.

Before entry it destroys every clause or nested scope left by the transfer in
reverse construction order. The old body scope ends completely, including
reverse destruction of every body-local lifetime. The target then gets a fresh
body scope entered from its beginning. Header lifetimes belonging to the active
target remain alive. If the new body later completes normally, its own clause
post and the switch post run normally.

`goto` never enters the middle of an effective statement and cannot bypass a
clause initializer because clauses have no initializer.

`goto ... with erase` is invalid. Direct body entry requires the current target
state to remain live, while erasure ends an `each` entry. Erase the explicitly
selected active traversal first, then transfer beyond it:

```zax
from each current_iteration: erase
goto outer:
```

A transfer-only case is taught with an actual incoming edge:

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

Without a reachable incoming `goto`, the transfer-only body is unreachable and
requires `intent<unreachable-selection-clause>{...}`.

#### `next`, `break`, and outward transfer

`next` can target:

- the currently active case, to run its post and then complete the switch; or
- the switch, to skip the case post, run the switch post, and exit.

It cannot target a sibling case. A bare `next` selects the nearest eligible
unlabeled target under the shared target rules; an eligible labeled target must
be named.

`break` exits the complete switch, not merely the current clause. It skips the
case and switch posts while still unwinding all exited scopes.

#### Redundant tail `break`

A `break` targeting the current switch is a non-acknowledgeable intent error when
replacing it with normal clause completion would execute no additional body work
or post operation before leaving that same switch.

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

The rule is semantic tail position rather than textual position. It applies when
every continuation from the `break` site to the switch's normal exit contains no
additional body operation or post phase. Ordinary scope destruction does not
make the transfer meaningful: tail fallthrough and tail `break` destroy the same
exited lifetimes in the same order.

A written post makes the transfer meaningful even when the `break` is last:

```zax
switch ;; value ;; recordSelection() {
  case 1 ;; recordCaseCompletion() {
    doSomething()
    break // valid: skips both written posts
  }
}
```

Effect analysis does not remove that distinction. A written post is a semantic
phase even when optimization could erase its work.

An explicitly selected outer target is also meaningful:

```zax
while outer: moreWork() {
  switch value {
    case 1 {
      doSomething()
      break outer: // valid: exits the loop
    }
  }
}
```

The error is deliberately specific to redundant switch-targeting `break`.
`continue`, `goto`, and `next` express distinct testing, body-entry, or post
behavior and do not inherit a hard diagnostic merely for symmetry. A redundant
use of another transfer may become lint material under separate evidence.

An outward `next`, `continue`, `break`, or `return` skips the posts belonging to
the switch it exits. The selected outer target then performs its own established
behavior. Panic likewise does not run normal-completion posts.

### Labels, visibility, and target eligibility

Tested-case and default labels are known throughout their containing switch for
`continue` and `goto`, permitting forward and backward transfer. They identify
clause test and body entry points according to the transfer keyword; they do not
make a clause a `break` target.

A transfer-only case label has no test entry. It is eligible for `goto`, and for
`next` while its body is active, but `continue transfer_only_label:` is an error.

The current active case is additionally a `next` target because it has a
normal-completion post phase. A sibling case is not a `next` target.

`shadowable` uses the established position:

```zax
switch shadowable choose_value: value {
  case shadowable retry: 1 {
    // ...
  }
}
```

- An enclosing same-named label must say `shadowable` before a case can reuse its
  spelling.
- `case shadowable label:` lets an eligible nested declaration in that case body
  reuse the spelling.
- Same-scope duplicate case labels remain errors rather than sibling shadowing.
- A case label shadows an outer same-named label for operations that can target
  that case from the source position.

Target eligibility also depends on the transfer keyword and source position:

```zax
while shadowable retry: condition {
  switch value {
    case retry: 1 {
      next retry: // current case is eligible: run its post and complete switch
    }

    case 2 {
      next retry: // acknowledgement-required intent error
    }
  }
}
```

In the second clause, sibling `retry:` cannot be the `next` target, while the
outer loop can. Reaching through the nearer same-spelled but ineligible case label
to the outer target is a defined but suspicious interpretation requiring intent
acknowledgement through
`intent<outer-target-through-ineligible-label>{...}` around the transfer
statement. The diagnostic should identify both declarations and the eligible
outer target.

For `continue` and `goto`, the sibling case is eligible and hides the outer
same-named target normally. The same keyword-specific reasoning applies to
`break` and other target kinds.

This is a cross-cutting refinement of the current simpler label-hiding account.
Promotion must update the shared flow-label owner rather than teaching a
selection-only lookup exception.

### General `goto` consequence

Selection exposes a broader missing operation: direct entry into an already
active flow body while bypassing its test, progression, and post.

The aligned general meaning is:

```text
next     = post, then the target's ordinary progression or completion
continue = skip post, then the target's ordinary progression or testing
goto     = skip post and progression/testing, then enter the target body
```

For a `while`:

```text
next loop:      post -> condition -> selected body
continue loop:  condition -> selected body
goto loop:      body directly
```

For `each`:

```text
next loop:      post -> progression -> next entry
continue loop:  progression -> next entry
goto loop:      current entry body directly
```

The `each` form requires the current entry lifetime to remain active. Erase or
another lifetime-ending transition cannot be bypassed to recreate an ended
entry.

General `goto` is not an unrestricted arbitrary statement jump:

- a bare form selects the nearest eligible unlabeled active body under the
  construct-specific target rules;
- an explicit form may name an eligible visible flow label;
- the target construct is already active;
- it enters only that construct's complete body boundary;
- it cannot enter an inactive loop or scope from outside and bypass header
  initialization;
- it unwinds exited scopes before entry;
- it cannot target `if`;
- static analysis validates every fact required by the target body; and
- switch cases are the deliberate sibling exception because one active switch
  header encloses all of their entry points.

The `goto` spelling usefully signals a low-level control-flow escape hatch
without making social stigma its safety mechanism. Lifetime, initialization,
scope, and proof rules provide the semantic safety.

This finding is aligned because it is necessary to give case direct entry a
coherent place in the language. It is not permission to edit core flow outside
the eventual authorized promotion change set.

### Optional presence and nested layers

The three-stage case-test model supports optional presence without optional-only
post-unary operations or generalized patterns:

```zax
switch myOptional {
  case ! {
    handleAbsent()
  }

  case ? {
    use(myOptional.)
  }
}
```

`case !` can form `!myOptional`; `case ?` can form `?myOptional`. The selected
operation must be exactly `Boolean`. The recognized optional-presence contract,
not an arbitrary user-defined Boolean operation, establishes boxed-lifetime
proof.

Failure of a recognized absence test can establish presence on the ordinary
path into later clauses or `default`:

```zax
switch myOptional {
  case !
    handleAbsent()
  default
    use(myOptional.) // the recognized absence test failed
}
```

That proof still intersects with any `continue` or `goto` entry that can reach
the same body without executing the absence test.

Nested optionals are selected one layer at a time:

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

This preserves the three states of `MyValue? ?`:

1. outer absent;
2. outer present and inner absent; and
3. outer present and inner present.

No case header binds the contained payload. The selected body uses ordinary
postfix access after recognized proof. A hidden temporary selector must be named
through the switch initializer when the body needs to access it.

Proof at a shared clause body is the intersection of every successful
alternative:

```zax
case ?, emergencyMode {
  use(myOptional.) // error if emergencyMode can succeed while absent
}
```

The body may also have incoming `continue` or `goto` edges. Direct body entry does
not inherit the bypassed test's proof:

```zax
switch myOptional {
  case present: ? {
    use(myOptional.) // error if the goto path below lacks presence proof
  }

  default retry: {
    goto present:
  }
}
```

Static analysis evaluates facts from all incoming paths rather than banning
direct transfer wholesale. The same requirement applies to pointer validity,
conditional storage, value ranges, aliases, and future recognized contracts.

Postfix `.` is access rather than a Boolean test and cannot become:

```zax
// case . > 15 // no such test form
```

Payload selection composes ordinary constructs instead:

```zax
switch myOptional {
  case ! {
    handleAbsent()
  }

  default {
    value : MyValue & = myOptional.

    switch value {
      case > 15
        handleLarge(value)
      default
        handleOther(value)
    }
  }
}
```

This is clearer than introducing a case initializer or making optional access
look like a comparison operator.

### `default`, incomplete selection, and exhaustiveness

`default` is optional and positional. It is the unconditional fallback for the
particular ordered search that reaches it, not one global declaration unique to
the switch.

A search can begin through:

- initial switch entry;
- `continue switch_label:`, restarting at the first clause;
- `continue case_label:`, beginning with that tested case; or
- bare `continue`, beginning after the current clause.

During a search:

1. transfer-only cases are skipped;
2. tested cases evaluate in source order;
3. the first successful test selects its body;
4. the first reached `default` selects unconditionally; and
5. reaching the end without either completes the switch normally.

Several defaults can therefore delimit several test segments:

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

Normal initial search reaches `normalize:` after `< 0` fails. Its `continue`
destroys that body scope and resumes testing after it. The later default is then
the fallback for the later segment.

A default reachable only through `goto` and through no ordered search is a
non-acknowledgeable intent error. It should be written as a transfer-only case
because it is not functioning as a fallback.

Body-local calculations do not survive a continuing default. State needed by a
later segment must live in the switch header or surrounding scope, or the later
selection should be nested inside the default body.

When an ordered search reaches no matching case or default, no clause runs. That
is an ordinary normal-completion path that runs the switch post and exits.

That path participates in static completion. Required construction, assignment,
result, and lifetime state after the switch must hold on:

- every selected case body that completes normally;
- every reached `default` body that completes normally; and
- every search path that reaches the end without a selected clause.

Enum selection performs two distinct audits:

1. **Declared-member coverage:** every distinct declared member value is
   explicitly handled on the applicable search entry.
2. **Reachable-domain coverage:** every other reachable backing value can select
   a body or is deliberately left as an incomplete no-match path.

A `default` satisfies reachable-domain coverage for the segment that reaches it.
It does not count as explicit coverage of omitted declared members. Merely
listing enum member names also does not cover the full reachable domain because:

- strict owner operations may produce unnamed values;
- relaxed enums admit every backing value;
- flags admit unnamed combinations;
- `unsafe from` may establish any backing representation;
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

The source above explicitly handles both declared members while `default`
handles unnamed or unsafe-admitted values. If `Permission.Execute` is later
declared, the switch becomes member-incomplete even though `default` remains.

A deliberately partial enum switch uses
`intent<partial-enum-selection>{...}` around the complete switch. Duplicate-
valued aliases count as one distinct enum value. Flags and relaxed enums still
need a fallback or another proof for unnamed values.

Coverage is evaluated for every reachable search entry. A later tested segment
does not retroactively make the initial segment member-complete. Arbitrary
user-defined predicates count toward coverage only when the language can prove
their domain.

Only reachable tests count as explicit member coverage:

| Entry path | Body reachable | Test reachable | Counts as explicit member coverage |
| --- | --- | --- | --- |
| Ordinary successful test | Yes | Yes | Yes |
| `continue member_case:` followed by a successful test | Yes | Yes | Yes, for that search entry |
| `goto member_case:` | Yes | No | No |
| No reachable incoming edge | No | No | No |

A case after a positional default does not count for a search that cannot reach
its test. `goto` can make that case body reachable but still bypasses the test and
therefore contributes no declared-member coverage.

Closed language-defined state spaces such as optional presence may support
recognized coverage, but proof remains layer-specific and path-sensitive.

The current construct is statement-only. It does not produce a common value,
support `break` with a result, or imply a future expression form.

### Lifetime, completion, and costs

The ordinary lifetime order is:

1. run and complete the optional switch initializer;
2. evaluate and retain the selector;
3. evaluate each reached test's runtime operands;
4. destroy ordinary test temporaries after their test completes;
5. enter only the selected or directly targeted clause scope;
6. destroy clause-body locals before its post or any outward arrival;
7. run applicable clause and switch posts under the transfer table; and
8. destroy retained selector and switch-header bindings when the switch exits.

No code observes a partially established selector. A failed, ambiguous, or
unavailable operation is a compile-time error rather than a runtime non-match.
Every normal path must leave required instance and result state complete.

Backward `continue` and `goto` can create intentional cycles. Each direct body
entry constructs a fresh clause scope, and each transfer destroys the previous
one. The switch selector and header state remain alive until an actual switch
exit.

Every re-entry is a complete scope transition rather than an instruction-pointer
jump that preserves body locals:

```zax
while resource := obtainResource() ;; resource.valid() {
  grant := resource.obtainGrant()

  if needToRedo()
    goto // grant is destroyed before a fresh body constructs another grant
}
```

For `next`, `continue`, and `goto`, the old body scope is destroyed first.
`next` then runs post and ordinary test/progression, `continue` skips post and
runs ordinary test/progression, and `goto` skips both. Any resulting entry begins
the body from its start with new body-local lifetimes while the active target's
header lifetimes remain alive.

Baseline cost is visible:

- one initializer execution when written;
- one selector evaluation and no hidden copy;
- one evaluation and operation for every reached test until a match;
- one selected body, or none on incomplete selection;
- posts selected by normal completion or `next`;
- ordinary destruction for every exited scope; and
- repeated tests or bodies when `continue` or `goto` cycles.

There is no constant-time dispatch promise. Dense constant cases may use another
dispatch mechanism only when it preserves observable source order and behavior.

### Diagnostics and intent acknowledgements

The design requires precise diagnostics for:

- no viable direct or equality interpretation of a case test;
- direct or equality ambiguity;
- a uniquely best unavailable operation;
- a final selected operation whose result is not exactly `Boolean`;
- missing or malformed clause-body boundaries;
- invalid comma-list continuation;
- a tested case with no body;
- an unlabeled testless case;
- `;` without a right effective-statement operand or compact `{}` where the
  established empty block is `{ }`;
- semantically proven unreachable clause bodies;
- a `default` reachable only through direct body entry and through no ordered
  search, which must be a transfer-only case instead;
- enum declared-member coverage omitted without
  `intent<partial-enum-selection>{...}`;
- an empty switch missing its required intent acknowledgement;
- `continue` with no following clause;
- `continue` naming a transfer-only case with no test entry;
- `next` naming a sibling case;
- bare `goto` with no following switch clause or stopped by a labeled eligible
  target;
- `goto` naming an ineligible or inactive target;
- any combined `goto ... with erase` form;
- a switch-targeting `break` in semantic tail position when no body work or post
  distinguishes it from normal completion;
- direct entry without required presence, pointer, initialization, or lifetime
  proof;
- use of a selector or clause-local value after its lifetime ends;
- incomplete construction or result state on a no-match path; and
- legacy `case continue` or another unsupported combined spelling.

Four defined but suspicious situations use exact intent categories:

| Category | Meaning and enclosure |
| --- | --- |
| `unreachable-selection-clause` | Deliberately retain one semantically proven unreachable `case` or `default`; enclose that complete clause |
| `empty-selection` | Deliberately write a switch containing no clauses; enclose the complete `switch` |
| `outer-target-through-ineligible-label` | Deliberately select an eligible outer target through a nearer same-named label that is ineligible for the written transfer keyword; enclose the transfer statement |
| `partial-enum-selection` | Deliberately omit one or more distinct declared enum member values from explicit coverage; enclose the complete `switch` |

```zax
switch mode {
  case MyMode.Primary
    usePrimary()

  // PrimaryAlias has the same value as Primary under the selected generated
  // equality operation.
  intent<unreachable-selection-clause>{
    case MyMode.PrimaryAlias
      useAlias()
  }
}
```

```zax
intent<empty-selection>{
  switch value { }
}
```

```zax
while shadowable retry: condition {
  switch value {
    case retry: 1
      handleOne()
    case 2 {
      intent<outer-target-through-ineligible-label>{
        next retry: // deliberately target the eligible outer loop
      }
    }
  }
}
```

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

An intent payload must form the complete contextual source unit required by its
category. `unreachable-selection-clause` accepts one complete clause, including
its required body; it cannot enclose only a test while borrowing the body from
outside. `empty-selection` and `partial-enum-selection` enclose a complete
switch, while `outer-target-through-ineligible-label` encloses one complete
transfer statement. Acknowledgement does not make invalid lifetime,
initialization, or target entry valid.

Redundant tail `break` is not another acknowledgement category. It has no
distinct useful interpretation to preserve and must be removed. The diagnostic
should explain that normal selected-clause completion already exits the switch
and identify any post or outer-target form that would make a transfer meaningful.

### Legacy evidence disposition

Useful legacy intent is retained with the following refinements:

| Legacy evidence | Aligned disposition |
| --- | --- |
| Ordered runtime `switch`, `case`, and `default` | Retain as ordered first-match statement selection |
| Stacked bodyless tested `case` headers | Replace with comma-separated alternatives owning one body; retain a distinct required-label transfer-only case form |
| No implicit fallthrough | Retain |
| `case continue` direct fallthrough | Retire spelling; `continue` retests, while bare or explicit `goto` enters a body directly |
| Runtime case values and complex types | Retain through ordinary reached expression evaluation and operation selection |
| Alternative binary operators | Retain through direct selector-relative test resolution |
| User-defined Boolean operations | Retain, including phrase and unary forms under the three-stage model |
| Switch initializer through `;;` | Retain under the complete conditional header schema |
| Switch post | Add consistently with current flow phases |
| Case post | Add as the missing local normal-completion phase |
| Case initializer | Reject for the current model |
| Break from a case | Clarify as exit from the complete switch; reject a redundant switch-targeting tail `break` unless it skips a post or selects an outer target |

Legacy root teaching should be consumed during promotion rather than retained as
a competing definition.

### Deliberate deferrals and rejected alternatives

#### Generalized patterns and payload bindings

Case tests do not declare bindings, destructure values, or recursively match
payloads. Optional selection demonstrates that recognized proof plus ordinary
nested selection is sufficient for the current bounded concern.

Variant payload extraction, recursive patterns, guards that resume later tests,
and flat payload-binding clauses remain future pattern/variant work. Before this
work item is archived, unresolved generalized matching pressure needs an indexed
raw destination rather than surviving only here. Activate it when a selected
value shape cannot be expressed clearly through ordinary operations, recognized
proof, and nested selection.

#### Case initialization

Case initialization is rejected for the current model, not left as an assumed
missing section. The optional-payload motivation is better served by nested
selection, and consistency does not justify the grammar, hidden-selector,
visibility, and direct-entry problems it creates.

New concrete pressure could reopen a separately focused design, but current
selection syntax must not reserve or imply a case initializer.

#### Value-producing selection

A future selection expression would need result convergence, direct destination
construction, exhaustive paths, branch-specific operation selection, and its own
transfer restrictions. Statement switch does not imply that design.

#### Variants, generics, reflection, and compile-time selection

Existing raw owners remain responsible for variants/unions, generic
specialization, reflection, and compile-time execution. Runtime source order and
case fallback do not establish compile-time dispatch contracts.

#### Callback selection

Present/absent callbacks and combinators remain with function-composition input.
The aligned switch model invokes no implicit callbacks and does not activate
that concern.

### Likely lasting ownership

The cohesive programmer-facing model is substantial enough for a dedicated
current selection owner. Promotion must nevertheless update every affected
shared owner rather than isolating consequences in that new document.

| Concern | Likely lasting owner or disposition |
| --- | --- |
| `switch`, `case`, `default`, test resolution, ordering, clause posts, transfers, incomplete selection, costs, and diagnostics | Dedicated current selection concept owner |
| Shared `next`/`continue`/`goto` phase meanings, active-body entry, `if` exclusion, unwinding, and target eligibility | Core flow control |
| Keyword-specific label lookup, `shadowable`, and suspicious reach-through to an outer target | Core flow control plus intent acknowledgements |
| Case-list continuation, header separators, labels, braces, bodies, and formatting | Source structure |
| Direct-before-equality and pre-unary fallback interaction with operator selection and source stability | Selection owner for construct behavior; operators for shared selection constraints |
| Enum aliases, unnamed values, flags, unsafe admission, and local coverage warning | Enum owner, linked to complete selection behavior |
| Optional presence proof, per-layer access, and nested-state example | Optional owner for lifetime facts; selection owner for case use |
| Scope destruction and direct-entry lifetime validation | Construction/destruction and safety/analysis owners |
| New acknowledgement categories | Intent-acknowledgement registry and the domain owner |
| Legacy root `switch` teaching | Consume useful evidence and remove or replace competing legacy teaching |
| Raw selection input | Retire after every accepted finding is promoted and each deferred remainder has a live indexed destination |

This map is provisional documentation architecture for a later dry run. It does
not authorize promotion, new files, owner edits, raw-input changes, or archival.

## Dispositions and promotion dry run

### Dry-run result: PASS (follow-up review)

The pre-promotion documentation fit dry run was performed on 2026-09-09.

The initial dry run passed after its first acknowledgement blocker was resolved,
and the aligned design was promoted. Subsequent maintainer review found material
semantic and integration omissions, temporarily revising the result to FAIL.

The maintainer has now aligned those findings, and the follow-up documentation
fit dry run passes. The existing owner structure and human reading path can
absorb bare `goto`, transfer-only cases, positional defaults, clause-local
intent, complete-flow reachability, and enum-coverage behavior coherently. The
exact ten-file follow-up promotion set is recorded below.

### Aligned acknowledgement resolution

| Situation | Category | Enclosure |
| --- | --- | --- |
| Deliberately retain a clause whose body is semantically proven unreachable | `unreachable-selection-clause` | Enclose the complete contextual `case` or `default` clause |
| Deliberately write a switch containing no clauses | `empty-selection` | Enclose the complete `switch` |
| Deliberately select an eligible outer target through a nearer same-named ineligible label | `outer-target-through-ineligible-label` | Enclose the transfer statement |
| Deliberately omit one or more distinct declared enum member values from explicit coverage | `partial-enum-selection` | Enclose the complete `switch` |

The first name applies to `case` and `default` rather than implying only equality
cases. The second can remain meaningful if another statement-form selection
construct later exists. The third describes the reusable label-resolution
situation rather than naming `switch`, `case`, or one transfer keyword. The
fourth separates deliberate partial interest in an enum's declared members from
coverage of unnamed or unsafe-admitted values.

The redundant tail-`break` diagnostic is deliberately absent from this table. It
is non-acknowledgeable and has no source category.

### Aligned-finding ownership map

| Aligned finding | Complete owner | Required supporting owners |
| --- | --- | --- |
| Runtime `switch` mental model, ordered first match, exactly one selected body, and optional `default` | New `language/switch.md` | `index.md` for routing |
| Switch initializer, retained selector, post, scope, no hidden copy, and temporary lifetime | Runtime value selection | Core flow for shared phases; construction/destruction for lifetime order; transfer stances for existing receiver-stance meaning |
| Required effective bodies, comma alternatives, transfer-only labeled cases, clause posts, positional defaults, and no case initializer | Runtime value selection | Source structure for layout, contextual clause payloads, separators, continuation, and body boundaries |
| Direct binary/post-unary, implicit equality, and final pre-unary case-test resolution | Runtime value selection | Operators for shared candidate selection and the construct-specific fallback boundary; operator phrases for phrase interpretation and fencing |
| Ordered side effects, overlap, reachability, and optimization equivalence | Runtime value selection | Operators for evaluation; analysis controls for future proof precision |
| `next`, `continue`, `goto`, `break`, clause posts, switch post, and the transfer table | Runtime value selection for switch behavior | Core flow for reusable phase meanings and target rules |
| General active-body `goto`, bare/explicit target selection, fresh body scope, no arbitrary statement entry, and no `if` target | Core flow control | Construction/destruction for unwinding; source structure for contextual-keyword source |
| `goto` to a live current `each` entry, rejection after entry-ending erase, and rejection of combined `goto ... with erase` | Iteration | Core flow for shared `goto` meaning |
| Case-wide forward/backward labels, `shadowable`, keyword/source-position eligibility, and suspicious outer reach-through | Core flow control | Runtime value selection for case use; declarations for namespace boundary; terms for the cross-cutting definition; intent acknowledgements for the required category |
| Static fact intersection across matched, continued, and direct-entry paths | Runtime value selection for case consequences | Construction/destruction for live-value proof; safety/analysis for the reusable proof model; raw analysis controls for future proof algorithms |
| Optional presence/absence, nested layers, and ordinary postfix payload access | Optional values for layer and proof facts | Runtime value selection for case use |
| Enum declared-member coverage, reachable-domain coverage, aliases, unnamed values, flags, unsafe admission, and partial-selection acknowledgement | Enums for domain facts | Runtime value selection for per-search-entry coverage behavior |
| Tail switch `break` as a non-acknowledgeable intent error unless it skips a post or exits an outer target | Runtime value selection | Core flow for target/post behavior; intent acknowledgements for the non-acknowledgeable distinction |
| Empty selection, clause-local unreachable acknowledgement, partial enum selection, and outer-target reach-through | Runtime value selection, enums, or core flow as applicable | Intent-acknowledgement registry and source structure |
| Costs, diagnostics, and source stability | Runtime value selection | Applicable flow, operator, source, iteration, and intent owners |

No new reusable language principle is required. These are a cohesive construct
model plus applications of existing evaluation, lifetime, proof, and source
principles.

### Structure proposal

Add one current concept owner:

```text
language/switch.md
```

The keyword-facing filename makes the owner directly discoverable while its body
distinguishes runtime value selection from callable candidate selection,
operator selection, compile-time selection, and future generic specialization.
No new directory or index family is needed.

The owner should teach in this order:

1. ordinary equality cases and `default`;
2. selector evaluation once and ordered first match;
3. comma-separated alternatives and effective bodies;
4. direct operators and the three-stage test interpretation;
5. switch and case posts;
6. the transfer table, then focused `continue`, `goto`, `next`, and `break`
   examples;
7. labels, direct entry, and static proof;
8. incomplete selection and enum coverage;
9. optional presence and nested-layer examples;
10. costs, diagnostics, source stability, and future boundaries.

This is a teaching reconstruction, not the order of the working record.

`index.md` remains the public router. `README.md` remains unchanged because it
already routes public readers through `index.md`. `project/README.md` remains
unchanged during promotion because work item `018` remains active until separate
closure and archival authorization.

### Teaching-fit checks

- **Cold-reader:** PASS for the proposed owner structure. Ordinary equality,
  first-match behavior, and the absence of required tail `break` can be taught
  before posts, labels, or `goto`.
- **Vocabulary earns precision:** PASS. Begin with selector, test, and body in
  ordinary language; introduce direct entry, progression entry, and
  keyword-specific target eligibility only with their transfer examples.
- **Concrete consequence:** PASS. The transfer table, comma alternatives,
  selector-capture example, enum catch-all, nested optional example, and tail
  `break` counterexample provide source-level consequences.
- **Teaching before reference:** PASS. Put exhaustive test-resolution rules,
  target matrices, costs, and diagnostics after ordinary source.
- **Positive-first:** PASS. Lead with valid equality/default source. Retain
  invalid source only for likely migration mistakes, proof failures, and
  intentional diagnostics.
- **Direct entry:** PASS. The owner can explain representative source and local
  outcomes without requiring the working record; links lead to the complete
  shared operator, flow, optional, enum, and lifetime rules.
- **No working-record transcription:** PASS. The proposed outline removes
  discovery chronology and rejected intermediate fallthrough models.

These checks establish that a cohesive dedicated owner is warranted. Scattering
the model among core flow, operators, enums, and optionals would force readers to
reconstruct one feature from several documents.

### Raw and deferred disposition

`project/raw/selection.md` should not remain after promotion under its current
name because its accepted runtime-selection input will have been consumed.

Move and rewrite it as:

```text
project/raw/pattern-matching.md
```

The rewritten raw input should preserve only:

- generalized and recursive pattern forms;
- variant/union active-alternative tests and payload binding;
- flat optional payload-binding pressure beyond ordinary proven access;
- pattern guards and their resume/proof behavior;
- binding-shape agreement across alternatives;
- interaction between direct body entry and pattern-established bindings; and
- representative illustrative syntax clearly marked unaccepted.

It should state that current `case` tests are Boolean operations and introduce no
bindings. Future pattern work must use a distinct coherent source form rather
than silently reinterpret current cases.

Other deferred destinations:

| Deferred concern | Live destination after promotion | Activation pressure |
| --- | --- | --- |
| Variant/union alternatives and payload lifetimes | `project/raw/variants-and-unions.md` plus the proposed pattern-matching input | A concrete managed alternative or overlay design |
| Value-producing runtime selection | Future boundary in `language/switch.md` | A use case requiring direct result construction and arm convergence |
| Compile-time selection | `project/raw/compile-time-execution.md` | Compile-time dispatch or execution design |
| Generic specialization and reflection | Existing generic and reflection raw owners | Selection over generic or reflected declarations |
| Optional callbacks/combinators | `project/raw/function-composition-and-chaining.md` | Expression-form callback selection |
| Formal path-merge, reachability, and termination proof | `project/raw/analysis-controls.md` | A formal analyzer contract or proof override |
| Mixfix or multi-hole case tests | Future boundary in the runtime-selection owner and current mixfix owner | A concrete case test requiring more than binary or unary omitted-selector forms |

`project/raw/README.md` must replace the selection row with the new generalized
pattern-matching row. `project/raw/feature-catalog.md` must route
`switch`/`case`/`default` to the current owner and add `goto` to current core
flow. `project/raw/cross-cutting-audit.md` must replace its stale `case next`
example. `project/raw/variants-and-unions.md` should link the current selection
owner and proposed pattern input where it names future exhaustive alternative
selection.

### Exact proposed promotion change set

1. **Add `language/switch.md`.**
   Teach the complete aligned programmer model, including source, selector
   capture, test resolution, ordering, case and switch posts, the transfer table,
   labels, direct-entry proof, default/exhaustiveness, enum and optional
   interaction, costs, diagnostics, source stability, and future boundaries.
2. **Update `index.md`.**
   Add the new owner to the start route and current-concept list; annotate the
   legacy flow-control entry as retaining only unresolved remainder.
3. **Update `flow-control.md`.**
   Route current `switch` behavior to the new owner, add `goto` to the current
   core-flow summary, and remove the fully consumed legacy `switch` through
   `case continue` sections while retaining unrelated `using` and value-
   polymorphism evidence.
4. **Update `language/core-flow-control.md`.**
   Add shared `goto` meaning and target eligibility, switch/case phase
   integration, case/switch label behavior, keyword/source-position target
   lookup, unwinding, costs, diagnostics, formatting, and source-stability
   consequences; replace the future-selection boundary with a current-owner
   handoff and preserve generalized patterns as future work.
5. **Update `language/iteration.md`.**
   Define `goto each_label:` as direct re-entry to the still-live current entry
   body, add its cost and diagnostics, and reject it after erase or another
   entry-ending transition.
6. **Update `language/construction-and-destruction.md`.**
   Add `goto` to abrupt scope-exit destruction and state that direct entry must
   satisfy live-value and construction proof on every incoming path.
7. **Update `language/source-structure.md`.**
   Teach switch/case/default structural layout, comma continuation, `;;` post
   separation, label placement, body boundaries, and contextual `goto`
   recognition; keep semantic test and transfer behavior in their owners.
8. **Update `language/operators.md`.**
   Explain that runtime selection creates a construct-specific
   direct-before-equality/pre-unary interpretation and then uses ordinary
   operator selection inside the chosen interpretation; record the deliberate
   source-stability consequence.
9. **Update `language/operator-phrases.md`.**
   Add a concise handoff showing natural and fenced phrase components in case
   tests and distinguish case interpretation order from ordinary all-tree phrase
   ambiguity.
10. **Update `language/enums.md`.**
    Replace future selection pressure with positive current enum-selection
    guidance and a handoff to the new owner; retain aliases and unnamed-value
    coverage facts.
11. **Update `language/optional-values.md`.**
    Add positive one-layer and nested switch examples, recognized presence proof,
    proof intersection across alternatives and transfer entries, and the boundary
    against payload-binding cases.
12. **Update `language/declarations-and-bindings.md`.**
    Preserve the separate label namespace while accounting for switch-wide case
    labels, `shadowable`, and keyword/source-position eligibility owned by core
    flow.
13. **Update `language/terms.md`.**
    Broaden `flow label` beyond only complete flow statements, add `goto` to
    abrupt-transfer terminology, and update `post operation` to state that
    `goto` skips post.
14. **Update `language/intent-acknowledgements.md`.**
    Register the three aligned exact categories with positive examples and add
    redundant tail switch `break` as a non-acknowledgeable domain example.
15. **Update `project/raw/analysis-controls.md`.**
    Preserve future proof-algorithm pressure for facts merged across matched,
    continued, and direct-entry paths, plus unreachable-clause and cyclic
    selection analysis.
16. **Move and rewrite `project/raw/selection.md` to
    `project/raw/pattern-matching.md`.**
    Consume accepted runtime selection and retain only the explicitly deferred
    generalized-pattern material and representative illustrative source.
17. **Update `project/raw/README.md`.**
    Replace the retired selection entry with the proposed pattern-matching input.
18. **Update `project/raw/feature-catalog.md`.**
    Route `switch`/`case`/`default` to the new current owner and
    `break`/`continue`/`next`/`goto` to core flow.
19. **Update `project/raw/cross-cutting-audit.md`.**
    Replace the consumed `case next` representative example with a live deferred
    consequence.
20. **Update `project/raw/variants-and-unions.md`.**
    Route future active-alternative selection to the current runtime-selection
    constraints and proposed pattern-matching input.
21. **Update this active work file.**
    Record final category alignment and the applied promotion disposition. Keep
    the item active until separate closure, archival, and continuation work.

No promotion change is proposed for:

- `README.md`, because it already routes through `index.md`;
- `project/README.md`, until separately authorized closure;
- `language/safety-and-analysis.md`, whose general required-proof model already
  covers direct-entry facts;
- `language/lifetimes-and-references.md`, whose life-path rules already cover
  every retained and exited scope;
- `language/qualifiers.md` or `language/transfer-stances.md`, whose current
  receiver and reference rules already supply selector behavior;
- `language/operator-catalog.md`, because selection adds no operator spelling;
  or
- implementation documentation, because no implementation exists here.

### Dry-run conclusion

**PASS.** The initial promotion remains useful, the post-promotion findings are
aligned, every finding has one owner, and the exact follow-up integration set is
complete. This PASS establishes readiness but does not authorize the follow-up
promotion.

### Applied promotion

The language maintainer explicitly authorized promotion after reviewing the PASS
result. Promotion was applied on 2026-09-09.

- `language/switch.md` now owns the complete current
  programmer-facing `switch`, `case`, and `default` model.
- Core flow, iteration, construction, source structure, operators, operator
  phrases, declarations, terminology, enums, optionals, and intent
  acknowledgements now own their respective shared or local consequences.
- `index.md` routes public readers to the new owner.
- The legacy switch sections were consumed from `flow-control.md`; unrelated
  `using` and value-polymorphism evidence remains.
- The raw selection file moved to `project/raw/pattern-matching.md` and now
  preserves only generalized patterns, payload binding, guards, and related
  future pressure.
- Raw indexes, feature routing, cross-cutting audit input, variant input, and
  analysis-control input now point to the current or deferred owners.
- `unreachable-selection-clause`, `empty-selection`, and
  `outer-target-through-ineligible-label` are registered intent categories.
- Redundant switch-targeting tail `break` is recorded as a
  non-acknowledgeable intent error.

Review corrections after the initial promotion:

- the dedicated owner uses the discoverable `language/switch.md` filename and
  “Zax switch, case, and default” title; runtime value selection remains its
  conceptual subject rather than its navigation name; and
- the construction/destruction integration is limited to lifetime ordering,
  fresh body scope, and path-sensitive explicit `+++`, `---`, and `@`
  consequences instead of repeating general flow or selection teaching.

The promotion establishes conceptual design, not implementation, a formal
specification, or a compatibility contract. Work item `018` remains active until
separately authorized closure, archival, and continuation.

Promotion validation:

- the 22 changed paths match the 21 authorized change-set operations, with the
  raw-file move counted as its source and destination paths;
- every local link and heading anchor in the changed Markdown resolves;
- Markdown code fences are balanced;
- staged and unstaged diffs pass whitespace validation;
- no current language owner links to numbered work or raw project input;
- no live reference still targets `project/raw/selection.md`; and
- the unrelated legacy `using` and value-polymorphism remainder in
  `flow-control.md` is byte-for-byte unchanged.

### Follow-up promotion required by review

The applied promotion remains useful but is not the complete current aligned
model. The following review findings require a separately aligned and authorized
follow-up promotion:

- every `next`, `continue`, and `goto` re-entry destroys the old body scope and
  enters a fresh body scope from its beginning;
- bare `goto` uses ordinary nearest-unlabeled targeting, with switch-local
  following-clause behavior and no silent outer fallback;
- all combined `goto ... with erase` forms are invalid; explicit erase followed
  by transfer beyond the traversal expresses the coherent operation;
- transfer-only labeled cases may appear anywhere, are skipped by ordered
  searches, and provide body entry for `goto` without a `continue` test entry;
- every tested case and every default requires one effective body, with `{ }` as
  the explicit empty body;
- zero or more positional defaults delimit ordered test segments;
- complete-flow reachability includes successful tests, defaults, `continue`,
  and `goto`;
- `unreachable-selection-clause` encloses one complete contextual clause;
- enum selection separately audits declared-member coverage and complete
  reachable-domain coverage; and
- `partial-enum-selection` acknowledges deliberate omission of declared member
  values while still allowing `default` to handle unnamed or unsafe-admitted
  values.

Exact follow-up change set:

1. `language/switch.md` - reconstruct ordinary teaching and advanced transfer,
   default-segment, transfer-only-case, reachability, body, and enum rules.
2. `language/core-flow-control.md` - define bare `goto` targeting and make fresh
   body destruction/re-entry explicit for every re-entry transfer.
3. `language/construction-and-destruction.md` - state the fresh-scope lifecycle
   consequence without duplicating flow teaching.
4. `language/iteration.md` - define bare `goto` for a live current entry and
   reject every combined `goto ... with erase` form.
5. `language/source-structure.md` - define transfer-only clause shape, mandatory
   clause bodies, positional defaults, and category-specific contextual intent
   payload completeness.
6. `language/intent-acknowledgements.md` - revise unreachable-clause enclosure,
   register `partial-enum-selection`, and preserve non-acknowledgeable repairs.
7. `language/enums.md` - teach declared-member and reachable-domain coverage as
   separate checks.
8. `project/raw/analysis-controls.md` - preserve complete-flow reachability,
   cyclic-entry, per-search-segment, and enum-coverage proof pressure.
9. `language/terms.md` - distinguish tested-case labels from transfer-only labels
   when defining test, body, and completion entry points.
10. This active work file - record follow-up alignment, dry-run status,
   application, and validation.

No other current owner, router, legacy page, or raw destination needs to change
for this follow-up. This recorded PASS requires separate explicit promotion
authorization before the ten-file set is applied.

### Applied follow-up promotion

The language maintainer explicitly authorized the ten-file follow-up after
reviewing its PASS. The follow-up was applied on 2026-09-09.

- `language/switch.md` now teaches transfer-only cases, positional defaults,
  multiple search segments, bare and explicit `goto`, fresh body scope,
  complete-flow reachability, clause-local acknowledgement, and two-dimensional
  enum coverage.
- `language/core-flow-control.md` defines bare active-body targeting and makes
  destruction-before-re-entry explicit.
- `language/construction-and-destruction.md` records the fresh-scope lifetime
  consequence without duplicating the flow model.
- `language/iteration.md` defines bare `goto` for a live current entry, rejects
  every combined `goto ... with erase`, and teaches explicit erase followed by
  transfer beyond the traversal.
- `language/source-structure.md` owns transfer-only clause presentation,
  mandatory bodies, positional defaults, explicit `{ }`, and complete contextual
  intent payloads.
- `language/intent-acknowledgements.md` gives
  `unreachable-selection-clause` one complete clause payload and registers
  `partial-enum-selection`.
- `language/enums.md` separates declared-member coverage from reachable-domain
  coverage.
- `language/terms.md` distinguishes tested-case and transfer-only label entry
  points.
- `project/raw/analysis-controls.md` preserves future proof precision for flow
  edges, search segments, cycles, and enum coverage.
- Introductory examples no longer present an unreferenced transfer-only case as
  ordinary valid source; transfer-only teaching begins with a reachable incoming
  `goto`.
- Enum teaching distinguishes body reachability from test reachability so a
  textual member case or `goto`-reachable body cannot satisfy member coverage
  when its test is unreachable.

Follow-up validation:

- the follow-up changed exactly the nine current/raw owners above plus this active
  work file, while preserving any staged/unstaged boundary established during
  review;
- local links and heading anchors resolve;
- Markdown fences are balanced;
- staged and unstaged whitespace checks pass; and
- no router, legacy page, or unrelated owner changed in the follow-up.
