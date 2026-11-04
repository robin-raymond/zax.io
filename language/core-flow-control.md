# Zax core flow control

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers reading, writing, or evaluating Zax |
| Applies To | Programmer-facing synchronous flow control; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | The exact-`Boolean` condition contract; clause selection; effective-body execution; conditional and loop header schemas, phase order, and `;;` section roles; `if`/`else` clause forms, chaining, and normal-completion post operations; branch hint and path-promise slots and the unattached unreachable marker; `while`, `until`, `redo while`, `redo until`, `forever`, `each`, and explicit `scope` as flow constructs; `break`, `continue`, `next`, `goto`, `return`, and producer/forwarding `except` as flow transfers, target eligibility, and barriers; flow-label spelling, placement, lookup, and reference; the conditional expression's shared condition, selected-arm, and convergence model; flow-facing costs, diagnostics, formatting, and source stability |
| Does Not Own | Complete runtime `switch`/`case` behavior ([switch, case, and default](switch.md)); complete `each` source families, bindings, cursor protocol, or erasure behavior ([iteration](iteration.md)); `using` resource enrollment and disposal ([Zax `using`](using.md)); expression/operator selection ([operators](operators.md)); complete [optional behavior](optional-values.md); source token/layout behavior ([source structure](source-structure.md)); lifecycle/access proof ([construction and destruction](construction-and-destruction.md)); whole-function result completion ([function invocation](function-invocation.md)); or the cohesive [exceptional result-flow model](except.md) |
| Source / Provenance | Legacy [flow control](../flow-control.md) and retired scope evidence |

## Mental model

A core flow statement selects and runs a body under a predictable set of visible
phases. It has:

1. a complete flow introducer such as `if`, `while`, `redo until`, or `each`;
2. an optional flow label;
3. construct-specific initializer, condition, and post sections;
4. exactly one effective body statement; and
5. defined normal-completion and abrupt-transfer behavior.

Ordinary `if` reads as expected:

```zax
if input < 0
  return true
```

Ordinary `if`/`else` reads the same way:

```zax
if input < 0
  handleNegative()
else
  handleOther()
```

Ordinary loops also read as expected:

```zax
while starting <= 100 {
  ++starting
}

each value : in values {
  use(value)
}
```

Header sections make the phases explicit. The initializer runs once, the
condition produces exactly `Boolean`, the selected body runs, and a post
operation runs after normal completion while header bindings are still alive:

```zax
while i := 0 ;; i < 100 ;; ++i {
  useIndex(i)
}
```

Loops add a repetition point, and transfers choose whether the post operation
runs:

```zax
while outer: i := 0 ;; i < 100 ;; ++i {
  if i == 5
    next outer:

  if i == 50 {
    i += 10
    continue outer:
  }
}
```

`next` runs the target's post operation before proceeding. `continue` skips it
and resumes the target's progression or test. `goto` skips both and enters an
eligible active body directly. Every transfer destroys the scopes it leaves
before arriving at its target.

The remainder of this document layers that model: conditions, effective bodies
and header schemas, `if`/`else` and post, loops and explicit `scope`, labels and
transfer targets, unwinding and completion, the conditional expression, then
analysis, costs, diagnostics, and source stability.

## Conditions

### The exact `Boolean` contract

Every core condition must resolve to exactly `Boolean`. Zax does not apply a
condition-specific truthiness conversion to pointers, optionals, errors, or
arbitrary values.

```zax
if booleanValue
  useBoolean()

if ?pointer
  use(pointer)

if !pointer
  handleNothing()
```

A value that is not already `Boolean` is a condition error rather than an
implicit conversion:

```zax
// if pointer       // error: pointer is not Boolean
// if optionalValue // error: MyType? is not Boolean
```

Each condition test is evaluated once at its defined phase and in visible source
order. Its completed side effects are observable before the selected body runs.

### Truth-producing operations

Conditions consume `Boolean` values; [operators](operators.md) owns how those
values are produced. The parts a condition depends on are:

- `?` and `!` are ordinary pre-unary operators resolved under ordinary selection.
  A condition supplies no expected `Boolean` result to that selection: it
  validates the already selected result and errors if it is not `Boolean`.
- An ambiguous direct `?` or `!` selection is an error. It does not activate the
  opposite-operator fallback, and the wanted `Boolean` never narrows the
  candidate set.
- Only when no direct overload applies may the opposite operator supply a
  fallback (`?value` via `!(!value)`, `!value` via `!(?value)`), and only when
  that opposite selection is unambiguous and returns exactly `Boolean`.
- An anonymous typed declaration such as `if : Boolean = ?value` supplies result
  context that an ordinary condition does not.
- The exact language-provided Boolean logical family is protected and cannot be
  user-replaced. Its members differ only in the logical operation and whether
  that operation can decide its result before evaluating the right operand:

  | Exact Boolean form | Evaluation |
  | --- | --- |
  | `&&` | Short-circuit when the left operand is false |
  | `\|\|` | Short-circuit when the left operand is true |
  | `^^` | Eager |
  | `logical nand`, `logical and not` | Preserve the corresponding AND short-circuit decision |
  | `logical nor`, `logical or not` | Preserve the corresponding OR short-circuit decision |
  | `logical xnor` | Eager |

  Every exact form evaluates required operands from left to right and still
  requires every possible operand path to be valid at compile time. Their exact
  forms and precedence are listed in the
  [operator catalog](operator-catalog.md#boolean-forms).
- For operand shapes other than an exact protected Boolean signature, the
  symbolic and phrase forms are ordinary eager overloadable operations, may
  return any type, and evaluate both operands left to right.

Short-circuiting skips runtime evaluation, not compile-time resolution. A
condition therefore may rely on protected short-circuit order for proof, but only
where the node really is exact `Boolean`/`Boolean`.

Operator discovery and selection are defined by [operators](operators.md);
exact forms and precedence by the
[operator catalog](operator-catalog.md#boolean-forms).

### Optional presence and dereference in conditions

Postfix optional dereference resolves before a pre-unary operator, so `?value.`
means `?(value.)` and does not test presence before dereferencing. Dereferencing
an optional requires static proof that it holds a live value. The general form
tests presence and dereferences in the proven body:

```zax
if ?optionalValue
  use(optionalValue.)
```

A compound proof is safe only when its right expression is unambiguously exactly
`Boolean`, because only then is the node a protected short-circuit operation:

```zax
if ?optionalValue && (: Boolean = ?optionalValue.)
  use(optionalValue.)
```

Without that typed right operand, an underlying `?` returning some other type
makes the node an ordinary eager overload that evaluates the dereference anyway,
and an ambiguous direct selection is an error:

```zax
// if ?optionalValue && ?optionalValue. // error unless ?optionalValue. is exactly Boolean
```

The proof need not be immediately adjacent. Construction, earlier control flow,
and other recognized presence contracts may also establish it. The
programmer-visible proof obligation is owned by
[construction, replacement, and destruction](construction-and-destruction.md#conditionally-live-storage-and-access-proof);
the presence operation itself is owned by [operators](operators.md). Complete
optional proof invalidation and wrapper/boxed behavior is taught by
[Zax optional values](optional-values.md#presence-proof-and-postfix-access).

## Effective bodies and header boundaries

Each clause consumes one effective body statement: simple, `;`-composed, or
braced. The clause owns a scope around that body regardless of its spelling.

```zax
if input < 0
  return true

if input < 0 { return true }

if input < 0
  ++input;
  value := input * 3;
  return true
```

The condition or header and the body must have a clear boundary: an indented
following statement, or a body-opening `{` on the final physical header line. A
body attached with neither is an error:

```zax
// if input < 0 return true // error: no body boundary
```

An unbraced body ends after its one effective statement. A correctly dedented
statement runs independently without requiring a blank line:

```zax
if condition
  doConditionalWork()
doIndependentWork()
```

An empty braced body is legal:

```zax
if condition { }
```

Complete effective-statement, composition, brace-layout, and body-boundary rules
belong to [source structure](source-structure.md). This document depends on them
but does not redefine them.

## Header schemas, ordering, and binding scope

`;;` separates construct-specific header sections. It is a header-section
separator, not doubled statement composition, and it requires whitespace on both
sides.

Conditional constructs (`if`, `while`, `until`, `redo while`, `redo until`) use:

```text
condition
initializer ;; condition
initializer ;; condition ;; post
;; condition
;; condition ;; post
```

Only the initializer position may be empty. One `;;` always means initializer
plus condition, so this source treats `condition` as the initializer and requires
`postOperation()` to produce `Boolean`:

```zax
if condition ;; postOperation()
  body() // error unless postOperation() returns Boolean
```

Even when `postOperation()` does return `Boolean` and the source is therefore
valid, it still does not mean condition plus post: the second section is the
condition. Write the no-initializer condition-plus-post form explicitly:

```zax
if ;; condition ;; postOperation()
  body()
```

Conditionless `forever` and `scope` have no condition to supply and use:

```text
body
initializer body
;; post body
initializer ;; post body
```

```zax
forever i := 0 {
  body()
}

forever ;; ++i {
  body()
}

scope i := 0 ;; ++i {
  body()
}
```

Iteration uses a required traversal clause rather than a Boolean condition:

```text
traversal
initializer ;; traversal
;; traversal ;; post
initializer ;; traversal ;; post
```

```zax
each values := loadValues() ;;
  value : in values ;;
  record(value) {
  use(value)
}
```

The complete `in`/`from` source, entry-binding, progression, and erase model is
owned by [Zax iteration](iteration.md).

Runtime value selection uses a selector rather than a Boolean condition:

```text
selector
initializer ;; selector
initializer ;; selector ;; post
;; selector ;; post
```

The selector evaluates once and remains retained through the complete switch.
Complete case tests, clause posts, and selection behavior are owned by
[Zax switch, case, and default](switch.md).

The keyword determines the header schema. A conditional loop's second section is
a condition; a conditionless construct's second section is a post operation.
This is construct-specific meaning, not an ambiguity.

The initializer accepts any effective statement and runs once. A binding it
introduces becomes visible after its own initializer completes and remains
visible to later initializer operands, the condition, the true and false
clauses, the post operation, and the complete body. It is destroyed when the
complete flow statement exits. Bindings introduced inside a nested initializer
block do not escape that block. Binding visibility and the nested-block boundary
are owned by [declarations and bindings](declarations-and-bindings.md).

Calls in initializer, condition, and post sections are ordinary synchronous call
sites. Earlier completed effects are visible to callbacks and reentrant code; no
code observes a partially established binding.

## `if`, `else`, and normal-completion post operations

`else` binds to the nearest open `if`. A label on `if` names the complete
`if`/`else if`/`else` chain; individual `else` clauses do not have independent
flow labels.

### `else` clause forms

An unbraced `else` follows the complete true body and takes one effective body
statement of its own:

```zax
if condition
  trueBody()
else
  falseBody()
```

A braced chain keeps `else` on the physical line that closes the preceding body:

```zax
if condition {
  trueBody()
} else {
  falseBody()
}
```

`else if` is an `else` whose effective body is another `if`, so a chain reads as
one selection:

```zax
if firstCondition
  firstBody()
else if secondCondition
  secondBody()
else
  finalBody()
```

Exactly one clause of a chain runs. A chain with no matching clause and no final
`else` runs no body, and the `if`'s post operation still runs. One flow label on
the leading `if` names the complete chain:

```zax
if pick_one: firstCondition
  firstBody()
else if secondCondition
  break pick_one:
else
  finalBody()
```

`else` must remain visually attached to the `if` it completes: it aligns with its
owning `if`, follows the complete true body with no physically blank line
between them, and shares a physical line with a preceding `}`. Those source
rules and their counterexamples are owned by
[source structure](source-structure.md#else-attachment-and-layout).

### Execution and post operations

```zax
if bad_stuff: \
  resource := getResource();
  inspectMetadata(resource) ;;
  validated(resource) ;;
  discard(resource) {
  if !resourceInspectionPassed(resource)
    break bad_stuff:

  use(resource)
} else {
  tryToFix(resource)

  if stillBad(resource)
    break bad_stuff:
}
```

Execution is:

1. run the initializer once;
2. establish its bindings;
3. evaluate the condition once;
4. select the true body or the corresponding false or `else` body;
5. run that body;
6. destroy clause-body locals on normal completion;
7. run the post operation exactly once while header bindings remain alive;
8. destroy header bindings; and
9. leave the complete `if`.

A post operation runs after the selected body completes normally. It also runs
when the condition is false and no `else` body exists. It does not run when
`break`, `continue`, `next`, `goto`, or `return` leaves the complete `if`; a
targeted `break bad_stuff:` above deliberately skips it. An unresolved panic is
not another exit path: the operation remains blocked until repaired or the
process crashes gracefully, with no scope unwinding or post execution.

An `if` post operation is ordinary normal-completion work, not a destructor,
`finally`, `defer`, or guaranteed cleanup hook. Deliberately bypassing it is
part of the labeled-exit model.

### Declarations that outlive a branch

A declaration inside a branch ends with that branch. A declaration marked
`outward` instead belongs to the scope around the whole `if` chain, provided
every path that continues past the chain declares it exactly once:

```zax
if useLarge {
  limit outward final : Integer = 1000
} else {
  limit outward final : Integer = 10
}

process(limit)
```

The complete rules are described by
[declarations and bindings](declarations-and-bindings.md#outward-declarations).
To choose a branch while compiling, so that only one branch becomes part of the
program, write `if compile`; see
[compile-time code](compile-time-code.md#choosing-code-at-compile-time).

## Branch hints and path promises

### Telling the compiler which branch is common

A compiler lays out branches and predicts jumps better when it knows which path
usually runs. `[<likely>]` and `[<unlikely>]` mark one branch as the common or
the rare path. They are
hints: they never change what the program does, and a compiler may ignore them.

The directive goes in the branch's slot, at the end of its header, just before
its body:

```zax
if parsed [<likely>] {
  useResult()
} else if retryable [<likely>] {
  retry()
} else {
  reportError()
}

if cacheHit {
  serve()
} else [<unlikely>] {
  rebuild()
}
```

For an `else`, the slot follows the word `else`. An `if` with a label,
initializer, or post keeps the slot after the complete header:

```zax
if cached: entry := lookup(key) ;; entry.valid ;; touch(entry) [<likely>] {
  use(entry)
}
```

Several branches may be marked likely. Marking every branch the same way says
nothing, so a lint may point it out.

### Promising that a path is always or never taken

`[<always>]` and `[<never>]` are promises rather than hints. `[<always>]` on a
branch promises that its condition always selects it. `[<never>]` promises that
the branch is never selected:

```zax
if bufferReady [<always>] {    // promise: bufferReady is always true here
  flush()
}

if corrupted [<never>] {       // promise: this branch never runs
  recover()
}
```

The compiler may optimize on the promise. By default the promise is also
checked: breaking it panics in the `always-path-missed` or `never-path-reached`
category. Disabling that category removes the check, and then breaking the
promise is undefined behavior. See
[controlling panic checks](safety-and-analysis.md#controlling-panic-checks).

An unattached `[<never>]` on its own line promises that execution never reaches
that point:

```zax
forever {
  value := random()
  if value >= 0 {
    continue
  }
  if value < 0 {
    continue
  }

  [<never>]                    // every value took one of the branches above

}
```

A hint is a probability; a promise is a fact the compiler may rely on. So
`[<always>]` together with `[<likely>]` on one branch is redundant, which a lint
may report, and `[<always>]` together with `[<unlikely>]` contradicts itself and
is an error.

The same directives on `case` and `default` are described by
[switch, case, and default](switch.md#case-hints-and-promises).

## Loops and explicit `scope`

`while` and `until` test before first entry. `redo while` and `redo until`
differ only by skipping that first test: the body executes once, then ordinary
post-then-test cycling begins.

```zax
while starting <= 100 {
  ++starting
}

until starting > 100 {
  ++starting
}

redo while starting < ending {
  doSomething()
}

redo until starting == ending {
  doSomething()
}

forever {
  if done
    break
  ++i
}
```

The common progression model is:

| Construct | Initial entry | Normal fallthrough or `next` | `continue` | `goto` |
| --- | --- | --- | --- | --- |
| `while` / `until` | initializer, test, selected body | post, test, selected body | test, selected body | current active body directly |
| `redo while` / `redo until` | initializer, body without first test | post, test, selected body | test, selected body | current active body directly |
| `forever` | initializer, body | post, body | body | current active body directly |
| `each` | initializer, first entry or exhaustion | post, progression, next entry or exhaustion | progression, next entry or exhaustion | current live entry body directly |
| explicit `scope` | initializer, body | post, then exit normally; `next` instead re-enters | re-enter body | re-enter body |

For `while`, continuation selects the body when the condition is true. For
`until`, continuation selects the body while the condition is false.

`next` and `continue` are deliberately different:

```zax
while i := 0 ;; i < 100 ;; ++i {
  if i == 5
    next // post increments i, then the loop tests 6

  if i == 50 {
    i += 10
    continue // skips post, then the loop tests 60
  }
}
```

Normal loop fallthrough behaves like `next`: it runs post before the next test
or unconditional entry. `break` skips post and exits. `continue` skips post and
goes to the next test, progression, or unconditional entry. `goto` also skips
post but bypasses that test or progression and enters the already active target
body directly.

For `each`, progression belongs to the traversal rather than to the
programmer-written post. `continue` skips post but still progresses; otherwise
it could repeat the same entry indefinitely. `with erase` and standalone erase
may replace ordinary progression with a cursor-provided erase transition. Their
complete availability and cursor effects are defined by
[Zax iteration](iteration.md#erasing-during-traversal).

An explicit `scope` is re-enterable; an arbitrary `{ ... }` block is not. Normal
`scope` completion runs post and exits. `next` targeting the scope runs post and
re-enters. `continue` targeting it skips post and re-enters. `break` skips post
and exits. `goto` targeting it also re-enters directly; it is distinct because
the same transfer has observable test or progression differences on other
constructs. A `forever` body differs because normal completion implicitly
repeats after post.

Comparing `forever` with an always-repeat decision and `scope` with a
never-repeat decision is useful in design reasoning, but neither construct
evaluates a hidden condition, operator, or reflectable expression.

## Flow labels and transfer targets

### Label spelling and placement

A flow label:

- starts with a lower-case ASCII letter;
- may then use lower-case ASCII letters, digits, and `_`, including consecutive
  interior underscores;
- may not begin or end with `_`; and
- is immediately followed by `:`.

```zax
outer:
my___label:
loop2:
```

The label follows the complete flow introducer:

```zax
if check_resource: condition
  body()

while outer: condition
  body()

redo while retry: condition
  body()

scope process_work: {
  body()
}

using cleanup: (resource) {
  body()
}
```

For `redo while` and `redo until`, the two keywords form one introducer, and a
label follows that complete introducer. The optional `shadowable` marker, when
present, sits between the introducer and the label; see
[Label namespace and shadow permission](#label-namespace-and-shadow-permission).

Runtime selection uses the same placement for a switch and for its clauses:

```zax
switch choose: value {
  case retry: 1
    handleOne()
  default fallback:
    handleFallback()
}
```

Case labels have switch-wide visibility for selection transfers rather than the
ordinary enclosing-only visibility of a complete flow statement. Their complete
test-entry and body-entry behavior belongs to
[switch, case, and default](switch.md#labels-and-target-visibility).

### Label namespace and shadow permission

Flow labels are a separate, explicitly shaped name category from ordinary
bindings, so the same spelling may name both without lookup ambiguity:

```zax
while outer: outer := 0 ;; outer < 100 ;; ++outer {
  ++outer
  break outer:
}
```

Expression `outer` names the binding; target `outer:` names the label. Label
shadowing is checked only against labels, and ordinary binding shadowing only
against ordinary bindings. The namespace boundary is owned by
[declarations and bindings](declarations-and-bindings.md#flow-labels-and-the-ordinary-namespace).

Overlapping duplicate labels are errors unless the outer label grants one level
of `shadowable` permission. `shadowable` follows the complete flow introducer and
precedes the label it applies to:

```zax
forever shadowable young: {
  forever young: {
    next young: // inner target

    // forever young: { } // error: middle label is not shadowable
  }

  next young: // outer target is visible again
}
```

`shadowable`:

- follows the complete flow introducer;
- precedes a required label;
- has whitespace on both sides;
- belongs to the outer label willing to be hidden;
- grants permission only to the next overlapping declaration; and
- is not inherited by that inner declaration.

An inner label must itself say `shadowable` to permit another nested reuse.

Target lookup considers the transfer keyword and source position as well as
spelling. An eligible inner target hides an outer same-named target normally. A
nearer same-named declaration that is ineligible for the written keyword makes
reaching an eligible outer target a defined but suspicious interpretation:

```zax
while shadowable retry: condition {
  switch value {
    case retry: 1
      handleOne()
    case 2 {
      intent<outer-target-through-ineligible-label>{
        next retry: // deliberately target the outer loop
      }
    }
  }
}
```

Without the acknowledgement, the final transfer is an intent error. The
acknowledgement does not make an actually ineligible target valid. Complete
category behavior is registered by
[intent acknowledgements](intent-acknowledgements.md#outer-target-through-an-ineligible-label).

### Bare and explicit target selection

For a particular transfer keyword:

1. scan outward through constructs ineligible for that keyword;
2. find the nearest eligible target;
3. if it is unlabeled, a bare transfer targets it;
4. if it is labeled, a bare transfer is an error and may not silently skip past
   it; and
5. an explicit `keyword label:` may cross nearer constructs and selects that
   visible eligible target.

| Construct | Bare `break` | Bare `continue` / `next` | Explicit `break label:` | Explicit `continue label:` / `next label:` |
| --- | --- | --- | --- | --- |
| Loop | Yes when nearest and unlabeled | Yes when nearest and unlabeled | Yes | Yes |
| Explicit `scope` | Yes when nearest and unlabeled | Yes when nearest and unlabeled | Yes | Yes |
| `using` | No; nearest `using` is a non-acknowledgeable barrier | No | Yes, and explicitly bypasses that target's disposal | No |
| Labeled `if` | No implicit target | No | Yes | No |
| Unlabeled `if` | Transparent | Transparent | Not nameable | Not nameable |
| Ordinary block | Transparent | Transparent | No | No |

`using` is deliberately unlike an ordinary unlabeled target. A bare `break`
stopped by a nearest `using` is an error rather than permission to bypass
disposal or search for an outer target. `break cleanup:` may target a labeled
`using` explicitly; every transfer that merely crosses a `using` performs its
disposal phase. Complete resource behavior is defined by
[Zax `using`](using.md#labels-and-exits).

`continue if_label:` and `next if_label:` are errors: re-evaluating an `if` would
turn selection into iteration, and treating them as exits would make them
misleading synonyms for `break if_label:`.

Runtime selection adds construct-specific targets:

- `break` can target the complete switch;
- `next` can target the active case or complete switch;
- `continue` can resume at another case's tests or at the first switch test; and
- `goto` can enter another case body directly.

Complete eligibility, bare continuation, and case-label rules are defined by
[switch, case, and default](switch.md#transfer-summary).

### Direct body entry with `goto`

`goto` enters the complete body boundary of an eligible already-active target
without running that target's post, condition, traversal progression, or other
entry test:

```zax
while resource := obtainResource() ;; resource.valid() {
  grant := resource.obtainGrant()

  if needToRedo()
    goto // destroy grant, then restart with a fresh body scope
}
```

This is not an arbitrary statement label:

- a bare `goto` selects the nearest eligible unlabeled active body;
- if that nearest eligible target is labeled, bare `goto` is an error and the
  label must be named;
- `goto label:` may explicitly select an eligible visible target;
- the target must already be active;
- entry begins at the target's complete body boundary;
- it cannot enter an inactive construct from outside;
- it cannot target `if`;
- it cannot enter the middle of a block or composed statement; and
- every lifetime, initialization, and proof requirement must hold on the new
  incoming path.

Every re-entry fully leaves the old body scope first. Its body-local values are
destroyed in reverse construction order. Entry then begins at the target body's
first statement with a fresh body scope; no body-local value survives. The
target's active header lifetimes remain alive.

For `each`, direct entry requires its current entry to remain live; complete
traversal interaction belongs to [Zax iteration](iteration.md#goto-and-the-current-entry).
Switch cases are the deliberate sibling-target exception because one active
switch header encloses every case entry; see
[switch, case, and default](switch.md#goto-enter-a-body-directly).

Inside a switch clause, bare `goto` uses the following-clause rule defined by the
selection owner. Failure to find a following clause is an error rather than
permission to target an outer construct silently. A call such as `goto()` remains
an ordinary expression when that spelling is callable.

```zax
scope process_work: i := 0 ;; ++i {
  while moreToDo() {
    if lessImportantStuffDone()
      next // targets the inner unlabeled while
  }
}
```

With no inner loop, the labeled scope is the nearest eligible target and requires
explicit naming:

```zax
scope process_work: i := 0 ;; ++i {
  if lessImportantStuffDone()
    next // error: process_work: is labeled
}
```

An unlabeled explicit scope is an ordinary bare target:

```zax
while i := 0 ;; i < 100 ;; ++i {
  scope j := 0 ;; ++j {
    if lessImportantStuffDone()
      next // targets the explicit scope
  }
}
```

Target references retain `:`:

```zax
break outer:
continue outer:
next outer:
goto outer:
```

Omitting the colon in a target position is an error. `continue()` and `next()`
remain ordinary call expressions, as does `goto()`: a spelling has keyword
status only where its keyword construct is grammatically permitted. The general
rule is owned by
[source structure](source-structure.md#contextual-keyword-recognition).

## Unwinding, destruction, and completion

Before a transfer arrives at its target, every body or nested scope it leaves is
destroyed in reverse construction order. Target-header bindings remain alive
when `next`, `continue`, or `goto` re-enters that target and are destroyed only
when the complete target flow statement exits.

For every re-entry transfer, destruction of the old body finishes before post,
test, progression, or direct body entry begins. `next` then runs post and
ordinary progression, `continue` skips post and performs ordinary progression,
and `goto` skips both. Any resulting body entry creates fresh body-local
lifetimes from the beginning of the body.

For `each`, current entry bindings end after post and before progression.
Standalone erase ends current entry access immediately and leaves a
target-specific pending transition. A following `next` may run only a post that
does not access the ended entry; `continue` skips post; neither performs a
second progression. Complete erase behavior is owned by
[Zax iteration](iteration.md#erase-perform-work-then-transfer).

On normal body completion:

1. destroy body-local bindings;
2. run post when the construct requires it;
3. test, re-enter, or exit according to the construct; and
4. destroy header bindings when the complete construct exits.

`break`, `goto`, and `return` skip ordinary post operations they cross but still
perform applicable scope destruction. `continue` likewise skips its target post;
`next` runs it. Construction and result completeness must hold on every normal
path produced by branches, loops, and transfers. The automatic local, body, and
header lifetime ordering is owned by
[construction, replacement, and destruction](construction-and-destruction.md#scope-exit-destruction-and-flow-transfers).
Each scope and surviving header region forms a
[life path](lifetimes-and-references.md#start-with-a-life-path); no contained
instance or reference target outlives the path a transfer exits.

A transfer crossing `using` performs that construct's body-local destruction,
reverse disposal, and owned-entry destruction before continuing outward. The
only ordinary-flow exception is `break label:` targeting that exact `using`,
which deliberately bypasses its disposal phase but not destruction. `using`
cannot be re-entered. Complete enrollment, disposal ordering, and bypass
behavior are owned by
[`using`](using.md#labels-and-exits).

### `return` as a flow exit

`return` exits the complete function rather than selecting a flow label. It
leaves every applicable scope and must satisfy the complete ordinary success
shape. A value-bearing return supplies every ordinary result position; a bare
return or fallthrough is valid only when every ordinary result slot is already
complete; and `return #` occupies one ordinary result position while preserving
or default-completing that slot. Complete return and result behavior, including
`return #`, is owned by
[function invocation](function-invocation.md#return-and-completion). Core flow
treats `return` as an exit that leaves scopes and never runs enclosing post
operations.

### `except` as a flow exit

Producer `except` and forwarding `except` likewise exit the complete function,
but select one declared exceptional completion outcome:

```zax
if failed()
  except failure: makeFailure()
```

They skip enclosing post operations and perform the same ordinary scope exit,
destruction, and `using` crossing behavior as `return`. They do not search for
an outer handler or initiate exception stack unwinding. The immediate caller
handles or forwards the selected named outcome under
[Zax exceptional result flow](except.md).

## Conditional expression and branch convergence

The conditional expression uses the shared exact-`Boolean` condition contract,
evaluates its condition once, evaluates only the selected arm, and directly
constructs or supplies its selected result:

```zax
e := a > b ?? c ;; d
```

`??` requires whitespace on both sides. A conditional expression inside a
`;;`-sectioned header requires parentheses for human intent even when a parser
could pair the tokens:

```zax
while i := 0 ;; (a < b ?? c ;; d) ;; ++i {
  body()
}
```

The selected path establishes the destination directly, without
default-constructing and then assigning over it. Surrounding operators and calls
may resolve separately for each selected arm:

```zax
e := f + (condition ?? c ;; d)
```

This may select `f + c` or `f + d`, including different overload bodies, while
preserving source evaluation:

1. evaluate `f` exactly once in its original position;
2. evaluate `condition` once;
3. evaluate only `c` or `d`; and
4. invoke the operation selected for that path.

The distribution never duplicates evaluation of `f`. Branch-specific callable
selection under a conditional expression is owned by
[function invocation](function-invocation.md#branch-specific-selection-under-a-conditional-expression);
this document owns only the shared condition, single-arm evaluation, and
convergence requirement.

A path may also select a direct mixfix while another path uses ordinary
component operations:

```zax
result := (condition ?? a * b ;; fallback) + c
```

The true path may select a `*`, `+` mixfix and the false path ordinary `+`.
The condition and common operands still evaluate once, only the selected arm
runs, every path is validated, and the results must converge. Complete tree
matching is defined by
[mixfix operators](mixfix-operators.md#branch-specific-mixfix-selection).

Every runtime path must converge to one statically usable result shape before
the complete operation completes:

```zax
whatAmI : KnownType = (a < b ?? c ;; d) // each path constructs KnownType
```

Number literals at the ends of conditional arms can use the type that the
conditional must produce:

```zax
myByte : U8 = condition ?? 1 ;; 2
```

Both `1` and `2` are checked and used as `U8` before their runtime paths leave
the conditional. If one arm already has an integer type, a number literal in the
other arm takes that same type:

```zax
myByte := condition ?? (: U8 = 1) ;; 2
```

Two concrete arms use ordinary convergence and do not reopen:

```zax
myValue := condition ?? (: U8 = 1) ;; (: Byte = 2) // error: distinct identities
```

The convergence type reaches only each arm's final result. It cannot choose a
width or signedness interpretation needed by an invalid inner operation:

```zax
myValue : U8 = condition ?? ~1 ;; 1 // error: complement has no invariant result
```

The first arm cannot resolve independently:

```zax
signed := ~(: I8 = 1)       // -2
unsigned8 := ~(: U8 = 1)    // 254
unsigned16 := ~(: U16 = 1)  // 65534
```

Bare `1` has not chosen signed or unsigned intent, and those valid concrete
interpretations produce different numbers. The outer `U8` convergence type can
receive a completed arm result; it cannot reach inside the arm and choose which
complement operation the programmer meant.

Commit the operand when `U8` complement is intended:

```zax
myValue : U8 = condition ?? ~(: U8 = 1) ;; 1
```

No uncommitted value reaches runtime. The complete integer-specific rules are in
[Zax integer literals and realization](integer-literals.md#conditional-convergence).

An inferred binding may not retain several possible types:

```zax
whatAmI := (a < b ?? c ;; d) // error if c and d remain different types
```

Different overloads or constructors may provide convergence when they ultimately
produce the same type. Boolean-producing paths may converge inside a condition:

```zax
if ?(a < b ?? c ;; d)
  doSomething()
```

A typed optional destination is another convergence form:

```zax
myType : MyType? =
  a < b ?? (: MyType = a) ;; (: MyType?)

if ?myType
  use(myType.)
```

On the true path the anonymous `MyType` is constructed from `a` and then
constructs the destination optional. On the false path the anonymous
type-default `MyType?` supplies absence. Both paths converge to the single
destination type `MyType?`.

Adding a layer around an already-optional arm requires explicit typed packet
construction:

```zax
inner : MyType?

nested : MyType? ? =
  condition ??
    (: MyType? ? = [{ inner }]) ;;
    (: MyType? ?)
```

`condition ?? [{ inner }] ;; (: MyType? ?)` is an error because a construction
packet is not independently an expression and has no destination in that arm.
The anonymous typed declaration supplies the construction boundary. Anonymous
declaration syntax is owned by
[function invocation](function-invocation.md#anonymous-typed-declarations);
complete optional convergence and depth intent is taught by
[Zax optional values](optional-values.md#conditional-convergence).

Statement composition does not extend convergence into a later statement:

```zax
if condition
  e : Type = f + (condition ?? c ;; d);
  doSomething(e)
```

The declaration must converge before `;`; the later call cannot help resolve it.
Allowing an ordinary stored binding to retain branch-dependent runtime types
would cross into deferred value-polymorphic representation and dispatch, which is
future work. `??` is itself sufficient source acknowledgement; no extra marker is
required merely because branch paths select different overloads.

## Static analysis, completion, and callbacks

Flow analysis is cross-cutting rather than owned solely by loops or lifecycle:

- access through conditionally live storage, such as an optional dereference,
  requires proof that a live value exists on that path;
- every normal path must complete required result and instance state;
- one lifetime may not be constructed or destroyed twice;
- use before construction and use after destruction are errors;
- obvious unreachable code and accidental nontermination are diagnosable; and
- conservative proof failures may require a future narrow semantic assertion.

```zax
// while i : Integer ;; i < 100 { } // candidate accidental-nontermination diagnostic
```

The empty body above never changes `i`. Complete termination proof is not
required here; a future analysis owner separates mandatory language validity,
conservative proof overrides, advisory diagnostics, and ordinary lints. Which of
these an implementation must prove follows the selected static-analysis contract
rather than one compiler's current cleverness, and the contract model itself
remains future analysis and diagnostics work.

Every branch path of a conditional expression is statically validated even though
only the selected arm evaluates, and a protected `Boolean` short-circuit skips
runtime evaluation without skipping compile-time resolution. Compile-time
resolution never implies early runtime evaluation.

Initializers, conditions, post operations, truth-producing operators, and
conditional arms may invoke arbitrary synchronous code. Each call completes
before the next visible phase. Reentrant code may observe completed earlier
effects but never a partially established binding. Async suspension and
cancellation remain separate future design.

## Costs

- Header initialization pays ordinary construction and call cost once.
- Conditions pay their visible evaluation cost at each defined test.
- `redo` always pays one body execution before its first test.
- `next` pays post cost; `continue` deliberately skips it.
- `goto` skips post and test/progression cost but pays the target body's work.
- `break`, `continue`, `next`, `goto`, and `return` pay destruction cost for
  every scope they leave.
- producer or forwarding `except` pays the same crossed-scope destruction and
  resource-disposal costs as `return`;
- A transfer crossing `using` also pays its selected disposal-call costs; an
  explicit `break` targeting that `using` is the visible bypass.
- A normal-completion post operation may perform substantial ordinary work and is
  not hidden cleanup.
- A conditional expression evaluates one arm and directly constructs its result.
- Branch-dependent ternary resolution may select different callable bodies and
  code paths.
- One branch may select one mixfix operation while another invokes ordinary
  component operators.
- A synthesized `?` or `!` Boolean fallback adds a negation that a direct
  overload may avoid.

## Diagnostics

Flow diagnostics fall into the shared categories defined by
[language-design terms](terms.md): ordinary syntax rejection, semantic errors,
and deliberate intent or layout errors.

Representative semantic errors include:

- a condition that does not resolve to exactly `Boolean`;
- a directly selected `?` or `!` result that is non-`Boolean` in a condition;
- an ambiguous direct `?` or `!` selection in a condition, which a condition may
  not resolve by preferring a `Boolean` candidate;
- an access through conditionally live storage without sufficient proof or
  semantic assertion;
- a composed statement occupying a condition and therefore producing no
  `Boolean`;
- branch-dependent expression paths that fail to converge;
- a transfer label naming a construct ineligible for that keyword;
- `next`, `continue`, or `goto` targeting a `using`;
- bare `goto` with no eligible unlabeled target;
- `goto` naming an inactive construct, an `if`, or a location other than a
  complete eligible body;
- direct body entry without the lifetime, initialization, or other facts required
  by that body;
- label shadowing without permission; and
- an incomplete result or instance on a normal completion path.

Representative deliberate intent or layout errors include:

- a bare transfer stopped by a labeled eligible target;
- a bare `break` stopped by a nearest `using`, which may not be acknowledged or
  retargeted outward;
- an eligible outer target reached through a nearer same-named label that is
  ineligible for the written keyword and lacks
  `intent<outer-target-through-ineligible-label>{...}`;
- a target reference without trailing `:`;
- an `else` detached from the `if` it completes; and
- a ternary in a `;;` header without disambiguating parentheses.

The intent-and-layout category is intentionally bounded. It does not authorize
speculative checks for every malformed spelling a compiler might hypothetically
explain. Source spacing, continuation, and brace-layout diagnostics are owned by
[source structure](source-structure.md); exact diagnostic identifiers remain
future diagnostics design.

## Formatting

Canonical flow formatting:

- writes structural indentation as exactly two spaces per level;
- keeps a body-opening `{` on the final physical header line;
- aligns a multiline body-closing `}` with its flow keyword;
- keeps `;;` and `??` surrounded by whitespace;
- keeps `else` aligned with its owning `if`, with no blank line before it and no
  newline between a preceding `}` and `else`;
- uses explicit `\` continuation to move a whole header to the common
  continuation level rather than aligning under a variable-length label; and
- preserves explicit target `:` references and labels, including `goto label:`.

Exact source spacing and continuation rules are owned by
[source structure](source-structure.md).

## Source stability

The aligned rules deliberately protect against:

- changing a value's type silently changing condition truthiness;
- adding or removing `;;` silently turning a body into a post operation;
- inserting a labeled eligible target silently redirecting a bare transfer;
- inserting a `using` boundary changing an outward transfer's disposal work;
- adding a local binding changing label resolution, or vice versa;
- adding a same-named label changing whether an explicit outer transfer requires
  intent acknowledgement;
- adding a `goto` edge changing the facts available at a body entry; and
- changing one ternary arm silently materializing a runtime-dependent stored
  type.

Some changes remain intentionally source-visible:

- renaming a flow label breaks explicit target references;
- adding or changing an overload may alter a ternary branch's selected operation;
- adding `else` or a post operation changes normal flow work;
- adding a nested unlabeled loop or explicit `scope` may change a bare target;
- adding a post operation changes the difference among `next`, `continue`, and
  `goto`; and
- changing construction or result shape changes path-completion obligations.

## Boundaries and maturity

This document is current conceptual design, not a formal grammar, implementation
mapping, compatibility contract, or conformance specification. It teaches the
programmer model and depends on the current owners linked above rather than
duplicating their rules.

The following remain explicit future work and are not established here:

- complete `each` iteration protocols, ranges, arrays, and iterator
  customization;
- generalized patterns, payload binding, guards, and value-producing selection;
  current `switch`, `case`, `default`, and direct case transfer are owned by
  [Zax switch, case, and default](switch.md);
- generalized cleanup hooks beyond current
  [Zax `using`](using.md); core `scope` here remains an
  explicit flow target, not a resource construct;
- runtime value-polymorphic declarations and stored branch-dependent types;
- specialized exceptional result production, handling, and forwarding, which
  preserves these ordinary exit rules and is owned by
  [Zax exceptional result flow](except.md);
- complete function capture and closure representation, including callable-like
  scope capture;
- async suspension, cancellation, executors, and concurrent flow;
- formal definitions of the reachability that `[<never>]` and `[<always>]`
  promise; and
- formal reachability, definite-initialization, and termination algorithms,
  diagnostic identifiers, and compiler lowering.

For Zax's accepted foundational direction, see the
[language vision](vision.md).
