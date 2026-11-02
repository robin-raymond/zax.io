# 007: Core flow control, clause headers, and exits

| Field | Value |
| --- | --- |
| Status | Historical working material / non-normative / audit-only |
| Work Item | `007` |
| Created | 2026-08-24 |
| Completed | 2026-08-26 |
| Owns | Historical record of the bounded review defined by the fixed initiating input |
| Does Not Own | Current language design, current project guidance, or ordinary reading paths |

## Non-authority notice

This archived file is a historical collaborative working record. Its promoted
findings are now owned by current language and project documents; its remaining
discussion, candidates, and chronology are non-authoritative. Read it only for a
targeted audit or provenance need.

## Fixed initiating input

This section records the information known and aligned when work item `007` was
created. It is intentionally incomplete rather than exhaustive and must not be
rewritten as the work develops.

### Initiating concern

Develop a coherent human-developer-facing model for core synchronous flow
control:

1. what a condition must produce and when it is evaluated;
2. how `if`, `else`, and related clauses select bodies;
3. how flow-header declarations, conditions, and post operations compose;
4. how pre-test, post-test, and unconditional loops execute;
5. how `break`, `continue`, and `return` choose targets and exit scopes;
6. how bindings, destruction, construction obligations, and result obligations
   behave on every path; and
7. how source structure, formatting, diagnostics, and visible cost make that
   behavior predictable.

Use current statement/body, declaration, invocation/result, qualifier, and
lifecycle owners as constraints. Establish the common clause, scope, ordering,
and exit model before expanding into iteration protocols, large selection
constructs, async flow, or specialized error propagation.

### Motivating pressure

Current owners now establish:

- statement-level newlines, effective bodies, semicolon composition, and
  mandatory indentation intent;
- declarations in flow headers and their clause-visible scope;
- strict call evaluation, result slots, complete return obligations, and
  resultless calls;
- qualifier-aware access and mutation;
- construction/destruction obligations and incomplete-lifetime tracking; and
- reusable principles for clear intent, trustworthy order, defensible
  preference, visible cost, and explicit shape changes.

The remaining root flow-control material proposes `if`, loops, header
initializers, `;;`, ternary expressions, `break`, `continue`, `return`, `switch`,
`each`, `using`, and value-polymorphic dispatch without one reviewed common
model.

Core flow control is now the earliest missing foundation for reading and writing
ordinary Zax programs. It should be reviewed before advanced function captures,
iteration protocols, pattern-like selection, error short-circuiting, or async
flow depend on unclear scope and exit behavior.

### Known assumptions

- [Zax language vision](../../../language/vision.md) is accepted foundational
  direction.
- [Zax language principles](../../../language/principles.md) owns reusable
  cross-cutting design and use principles.
- [Zax source structure](../../../language/source-structure.md) owns effective
  statements, bodies, semicolon composition, continuation, and mandatory layout.
- [Zax declarations and bindings](../../../language/declarations-and-bindings.md)
  owns declaration forms, visibility, shadowing, and the existing
  flow-initializer binding boundary.
- [Zax function invocation](../../../language/function-invocation.md) owns call
  evaluation, result slots, return completion, multiple-result capture, and
  synchronous call completion.
- [Zax qualifiers](../../../language/qualifiers.md) owns place, value, access,
  receiver, and indirection qualifications.
- [Zax construction, replacement, and destruction](../../../language/construction-and-destruction.md)
  owns value/member lifetime transitions and normal completion obligations.
- A flow-control clause consumes one effective body statement, which may be
  simple, composed, or braced.
- A flow-control clause establishes a scope around its complete body; braces do
  not make body-local names escape.
- A binding introduced by a flow initializer is visible to the condition,
  applicable clause bodies, and corresponding false or `else` clauses, then
  leaves scope after the complete flow statement.
- Resultful functions must complete every result on every normal exit. A bare
  return or fallthrough is valid only when all result slots are already complete.
- No source-order, indentation, or implementation guess may silently choose
  among plausible flow meanings.
- Exact operator declaration/ranking, casting, ownership, async, generic,
  iteration-protocol, and pattern-matching behavior remains later work unless an
  immediate core-flow contradiction requires a constraint.
- There is no language implementation in this repository.

### Known inclusions

- The condition contract for `if` and core loops.
- Exact versus converted `Boolean` conditions at the depth needed for coherent
  flow without designing the complete conversion system.
- Condition side effects and evaluation order.
- Short-circuit `&&`, `||`, and negation behavior at the depth required by
  conditions.
- `if`, `else`, and `else if`.
- Simple, composed, and braced clause bodies.
- Flow-header initialization, condition, and post-operation roles.
- The `;;` separator and its relationship to statement composition.
- Scope and visibility across conditions, true/false clauses, and loops.
- Pre-test `while` and `until`.
- Post-test `redo while` and `redo until`.
- Unconditional `forever`.
- Loop iteration order.
- `break` and `continue`, including target selection and whether labeled or named
  forms are needed.
- `return` as a flow exit, including complete result obligations.
- Normal fallthrough and early-exit behavior.
- Destruction and cleanup of applicable local scopes on normal completion,
  `break`, `continue`, and `return`.
- Construction and result completeness across branches and loop paths.
- Reachability and nontermination at the depth needed for programmer-facing
  diagnostics.
- Ternary/conditional expression behavior where it depends on the shared
  condition and branch model.
- Immediate callback and reentrancy consequences when condition or header
  evaluation invokes code.
- Programmer-visible ordering, costs, diagnostics, and canonical formatting.
- Source-stability pressure from changed conditions, added clauses, or changed
  exit targets.
- Candidate lasting owners and documentation boundaries.
- Preservation of useful but premature iteration, switch/pattern, error-flow,
  lifetime, analysis, generic, compile-time, or async findings in existing or
  newly justified indexed raw input.

### Known starting boundaries

- Complete `each` iteration protocols, ranges, arrays, metadata iteration, and
  iterator customization.
- Complete `switch`, `case`, pattern matching, exhaustiveness, and fallthrough.
- Complete `using` resource-management semantics.
- Runtime value-polymorphic function declarations using `if`.
- `except`, `catch`, and specialized error-result propagation.
- Function capture and closure representation.
- Complete pointer/reference lifetime strategies and ownership.
- Async suspension, cancellation, executors, and concurrent flow.
- Generics, concepts, specialization, and compile-time branch elimination.
- Arbitrary operator declaration, conversion, and ranking.
- Compiler directives controlling likelihood, reachability, or analysis.
- Formal definite-initialization, reachability, or termination algorithms.
- Foreign control transfer, ABI, stack unwinding, and compiler implementation.

These areas are not part of the initiating question and need not be explored to
begin. They do not prohibit following, recording, or dispositioning material
consequences.

### Intentionally unresolved framing

- Whether conditions require exactly `Boolean`, allow an explicit selected
  conversion, or use another narrowly defined truth-test contract.
- Whether logical short-circuit operators are intrinsic flow mechanisms,
  ordinary operators with protected ordering, or another explicit category.
- Exact syntax and meaning of `;;` in initializer/condition/post headers.
- Which core statements support initialization and post operations.
- Whether `until` remains a distinct statement or an inversion of `while`.
- Exact pre-test and post-test loop spelling.
- Whether `break` and `continue` target only the nearest loop or may name an
  enclosing loop or scope.
- Whether named scopes participate in core exit targeting.
- How loop-header and iteration-local bindings are scoped and destroyed.
- What cleanup is required before each exit transfers control.
- Whether a condition or body that cannot complete normally changes result,
  construction, or reachability obligations.
- How ternary/conditional expressions produce one value or result shape.
- Which findings belong in a core flow owner versus source structure,
  declarations, invocation, construction, lifetime, diagnostics, operators, or
  future iteration/selection owners.

### Initial stopping guidance

Stop when the work has:

- established one coherent condition model;
- established shared clause evaluation, body, and scope behavior;
- established core branch and loop forms;
- established header initialization, condition, post-operation, and `;;`
  behavior;
- established `break`, `continue`, `return`, fallthrough, and target selection;
- established programmer-visible cleanup, construction, and result obligations
  for every core exit;
- constrained short-circuit operators, conversions, callbacks, lifetimes,
  analysis, and nontermination without designing their complete future domains;
- identified costs, diagnostics, canonical formatting, and source-stability
  pressure;
- dispositioned useful legacy flow and scope material;
- preserved future iteration, selection, error-flow, async, and analysis
  findings in appropriate current or indexed raw owners;
- identified plausible lasting owners and their boundaries; and
- gathered enough information for a pre-promotion documentation fit dry run.

Do not promote findings, update language examples broadly, archive this work
item, or begin work item `008` without the separately required discussion,
alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../../documentation.md) - governs ownership,
  focused reading, numbered work, and promotion.
- [Language vision](../../../language/vision.md) - provides foundational direction
  and accepted high-level tradeoffs.
- [Zax language principles](../../../language/principles.md) - provides the
  cross-cutting principles against which flow behavior should be evaluated.
- [Zax source structure](../../../language/source-structure.md) - provides
  effective statements, bodies, composition, continuation, layout, and comments.
- [Zax declarations and bindings](../../../language/declarations-and-bindings.md) -
  provides flow-header declarations, clause-visible scope, shadowing, and
  binding visibility.
- [Zax function invocation](../../../language/function-invocation.md) - provides
  call evaluation, result slots, return completion, result capture, and
  synchronous completion.
- [Zax qualifiers](../../../language/qualifiers.md) - provides mutation, access,
  place, receiver, and indirection constraints used within flow.
- [Zax construction, replacement, and destruction](../../../language/construction-and-destruction.md) -
  provides lifecycle completeness, destruction, and incomplete-state pressure
  across control paths.
- [Zax language-design terms](../../../language/terms.md) - provides current
  cross-cutting vocabulary.
- [Legacy flow control](../../../flow-control.md) - preserves primary evidence for
  conditions, branch and loop forms, header initialization, `;;`, ternary,
  iteration, selection, exits, `using`, and value-polymorphic proposals.
- [Legacy scope](../../../scope.md) - preserves primary evidence for named and
  unnamed scopes, targeted `break`/`continue`, and scope capture proposals.

### Consequence-driven

- Read focused [operator](../../../operator.md) material when condition conversion,
  logical short-circuiting, negation, or operator/function consistency becomes
  necessary.
- Read focused [casting](../../../casting.md) material when a non-`Boolean`
  condition requires an explicit conversion boundary.
- Read focused [Nothing](../../../nothing.md) or [optional](../../../optional.md)
  material when condition truth, absence, or optional state becomes necessary.
- Read focused [pointer and reference](../../../pointers.md) material and the
  [raw lifetime-strategies input](../../raw/lifetimes.md) when scope exit,
  destruction, dangling references, captures, or transfer becomes necessary.
- Read the [raw analysis-controls input](../../raw/analysis-controls.md) when flow
  requires proof overrides, reachability assertions, definite completion, or
  diagnostic provenance.
- Read the [raw safety input](../../raw/safety.md) when flow behavior crosses a
  promised safe-subset boundary.
- Read focused [except](../../../except.md) material when ordinary exits cannot be
  established without constraining error-result short-circuiting.
- Read focused [arrays](../../../arrays.md), [variadic](../../../variadic.md), or
  remaining `each` material only when core loop behavior cannot be established
  without an iteration-protocol constraint.
- Read focused `switch`, `case`, or `using` sections from
  [legacy flow control](../../../flow-control.md) only when their future design
  constrains the shared clause or exit model.
- Read the [raw async input](../../raw/async.md) when flow completion, suspension,
  cancellation, or callback timing becomes materially async.
- Read focused [compiler-directive](../../../compiler-directives.md) or
  [meta-function](../../../meta-functions.md) material when runtime flow cannot be
  separated cleanly from build-time selection or likelihood directives.
- Expand beyond this list whenever discussion or a material finding establishes
  a concrete need. State why the additional reading is relevant and keep it
  focused.

Material not listed in this reading scope is neither required nor prohibited. It
remains available when discussion or a concrete consequence makes it relevant.

### Audit-only

- `project/archive/`, including work items `001` through `006`.

Do not read archived work item `006` while conducting ordinary work on `007`.
Its applicable findings have been promoted into current language and project
owners. Read it only when the language maintainer explicitly requests a targeted
audit or provenance investigation.

Other audit-only material likewise requires direct language-maintainer
instruction or a specific audit or recovery need.

## Working record

This working record began as raw input and candidate reconstruction. It now also
contains findings aligned in language-maintainer discussion. Consistent with the
non-authority notice above and the maturity states in the
[documentation architecture](../../documentation.md), those aligned working
findings remain non-authoritative until separately approved and promoted.
Current concept owners are cited as binding *constraints*. The root
[legacy flow control](../../../flow-control.md) and [legacy scope](../../../scope.md)
pages remain evidence rather than authority.

The record leads with the review state and programmer model, then layers
conditions, source intent, headers, branches, loops, labels, exits, conditional
expressions, analysis, repairs, deferrals, source stability, and likely owner
boundaries. Concrete examples and counterexamples sit beside the rules they
demonstrate.

### Evidence base and reading actually performed

Required reading completed: documentation architecture; vision; principles;
source structure; declarations and bindings; function invocation; qualifiers;
construction, replacement, and destruction; language-design terms; legacy flow
control; legacy scope.

Consequence-driven reading then expanded in bounded steps:

- focused [operator](../../../operator.md) and [Nothing](../../../nothing.md) material
  tested whether `if !_` and `if !resourceError` represent explicit operator
  results or condition-level truth conversion;
- focused [optional](../../../optional.md) material exposed the legacy bare-optional
  condition and dereference model;
- [raw analysis controls](../../raw/analysis-controls.md) established the existing
  cross-cutting destination for proof overrides, lint separation, provenance,
  and smarter-compiler behavior; and
- the [raw input index](../../raw/README.md) confirmed that no dedicated selection
  or cross-cutting final-audit input currently exists.

No archived work or operating-prompt source was read.

**Current-owner constraints already fixed (not re-litigated here).** These are
inputs, not findings of this item:

- Source structure owns effective statements and bodies: a flow-control clause
  consumes exactly one effective body statement (simple, composed with `;`, or a
  `{ }` block); `;` is a composition operator needing a complete statement on
  both sides; a clause opens one scope around its whole body; composed operands
  share that clause scope; braces do not let body-local names escape; indentation
  must truthfully present structure (mandatory layout validation). Source
  structure explicitly does **not** yet own `;;`, exact flow-header grammar, or
  scope-exit semantics.
- Declarations and bindings owns the flow-initializer boundary: a binding
  introduced in a flow initializer is visible to the condition, the applicable
  clause bodies, and the corresponding false/`else` clauses, then leaves scope
  after the complete flow statement; an ordinary binding is not visible during
  its own initialization; same-scope redeclaration is an error; nested shadowing
  requires `shadowable` permission on the hidden declaration; documentation
  before a flow statement attaches to the statement, not to a header declaration.
- Function invocation owns result completion: every normal exit must complete the
  full declared result shape; a value-bearing `return` supplies the complete
  shape, while a bare `return` or fallthrough is valid only when every slot is
  already complete; partial value-bearing return lists are unavailable; the
  compiler tracks construction through control flow and rejects use-before-
  construction, double construction, and normal completion with an incomplete
  result. A call in a condition/header completes synchronously before the next
  step; callbacks observe completed earlier effects but never a partial binding.
- Construction/destruction owns lifecycle completeness: destructors run in
  reverse construction order; the compiler tracks member/result lifetimes across
  control flow; a panic is a fatal graceful crash with no exception-style
  rollback; lifecycle operations are synchronous.
- Qualifiers separate place/value/access: a condition that only *reads* needs
  only a readonly path, while a side-effecting condition needs writable/mutable
  authority on what it changes.
- Principles that bind flow design: reject ambiguous source rather than guess;
  keep visible order trustworthy; make preference defensible, not source-order;
  keep meaningful cost discoverable; keep similar meaning looking similar.

### Review status

The language maintainer and agent have discussed and aligned the findings in
this working record. They remain **aligned working findings**, not accepted
language design: approval of this review record, a documentation-fit dry run,
and separately authorized promotion are still required.

No known non-deferred contradiction remains in the model below. The review
should concentrate on whether this record faithfully preserves the aligned
meaning, examples, repair obligations, and explicit deferrals.

### Programmer model at a glance

A core flow statement has:

1. a complete flow introducer;
2. an optional flow label;
3. construct-specific initializer, condition, and post sections;
4. exactly one effective body statement; and
5. defined normal-completion and abrupt-transfer behavior.

```zax
if shadowable bad_stuff: \
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

The initializer runs once. The condition produces exactly `Boolean`. The
selected body runs. On normal completion, the post operation runs while header
bindings such as `resource` are still alive. A targeted `break bad_stuff:`
intentionally skips the post operation and exits the complete `if`/`else` chain.

Loops use the same visible phases while differing in their test and repetition
points:

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

`next` runs the target's post operation before proceeding. `continue` skips that
post operation. Every transfer destroys the scopes it leaves before arriving at
its target.

### Conditions and truth-producing operations

#### Exact `Boolean` condition contract

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

// if pointer       // error: pointer is not Boolean
// if optionalValue // error: MyType? is not Boolean
```

Each condition test is evaluated once at its defined phase and in visible source
order. Its completed side effects are observable before the selected body runs.
Core `&&` and `||` consume `Boolean` operands, produce `Boolean`, evaluate left
to right, and short-circuit, so a skipped right operand has no effects. Complete
operator declaration and ranking remains future operator work.

#### Pre-unary `?` and `!`

Pre-unary `?` and `!` are ordinary overloadable operators. A directly selected
overload may return any type:

```zax
myType : MyType = ?extractedType

// error if the selected ? overload returns MyType rather than Boolean
if ?extractType()
  useResult()
```

The condition context validates the already selected result; it does not silently
choose a result-only operator overload merely because `Boolean` is needed.

When the directly written operator is unavailable, the opposite operator may
supply a Boolean-only fallback:

- `?value` may become `!!value` only when no applicable direct `?` exists and
  the selected `!value` returns exactly `Boolean`;
- `!value` may become `!?value` only when no applicable direct `!` exists and
  the selected `?value` returns exactly `Boolean`; and
- if the directly written operator exists, its result is used even when that
  result later fails the condition's `Boolean` requirement.

A type may define both Boolean-returning forms to avoid the extra negation cost.
The requested direct operator runs. Independently maintaining both complement
operations is a possible lint concern rather than a language error; exact lint
and suppression syntax remains future analysis work.

#### Optional presence and dereference

Postfix optional dereference resolves before a pre-unary operator:

```zax
?optionalValue.
```

means:

```zax
?(optionalValue.)
```

It does not test optional presence before dereferencing. Dereferencing an
optional requires static proof that the optional contains a live value, or a
future narrow semantic assertion when correct code exceeds the compiler's proof.
The proof need not be an immediately adjacent check:

```zax
if ?optionalValue && ?optionalValue.
  use(optionalValue.)
```

This is one obvious passing pattern because the right operand is evaluated only
after the presence test succeeds. Construction, earlier control flow, and other
recognized proofs may also establish presence. An arbitrary user-defined
Boolean-returning `?` does not by itself prove optional initialization; the
analyzer needs a recognized optional-presence contract.

A later compiler that can prove what an older compiler could not may report an
obsolete semantic assertion as advisory or lint material under the applicable
language contract. Ordinary lint suppression cannot make an unproved
dereference valid.

### Effective bodies and source intent

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

The condition/header and body must have a clear boundary: an indented following
statement or a body-opening `{` on the final physical header line.

```zax
// error: body is attached without a newline or braces
if input < 0 return true
```

This source has two distinct failures:

```zax
if input < 0; return true
```

`input < 0; return true` is parsed as a composed statement in the condition
position, so it does not resolve to `Boolean`; the apparent `return` has also
been consumed into that composition, leaving no true body.

An unbraced body ends after its one effective statement. A correctly dedented
statement is independently executed without requiring a blank line:

```zax
if condition
  doConditionalWork()
doIndependentWork()
```

Requiring extra separation here would impose more routine friction than the
remaining protection justifies. Contradictory indentation remains an error:

```zax
if condition
  doConditionalWork()
  looksConditionalButIsNotComposed() // error
```

An empty body is legal:

```zax
if condition { }
```

An exact empty block used as an initializer or post header is an intent error
because it looks more like a missing or misplaced body than meaningful header
work:

```zax
while i := 0 ;; i < 100 ;; { } {
  body()
}
```

The rule is syntactic rather than an attempt to prove whether arbitrary nested
statements have effects. A nonempty but apparently effectless header operation
may become lint material.

### Whitespace and mandatory layout

The discussion-aligned source rules are:

- structural indentation uses ASCII spaces only;
- one structural level is exactly two spaces;
- physical tab characters are intent errors;
- continuation or expression alignment may be deeper only when the parsed
  source already establishes continuation;
- a multiline body-closing `}` aligns with its flow keyword;
- `{` used as a scope opener has whitespace on both sides, with newline counting
  as whitespace;
- a braced body begins on the final physical header line;
- `;` attaches to the complete statement on its left and requires whitespace on
  its right;
- `;;` and `??` require whitespace on both sides; and
- `\` after `;` or `;;` is redundant continuation and therefore an intent error.

```zax
foo(); bar()

foo();
bar()

// foo() ; bar() // error: whitespace before ;
// foo();bar()   // error: no whitespace after ;
```

Top-level operands and sections continued across a flow header use one common
two-space continuation level rather than aligning under a variable-length
keyword or label prefix:

```zax
if i := 0;
  j := 1;
  k := 2 ;;
  i + j + k == 7 ;;
  ++i; --j; ++k {
  body()
}
```

Progressively indented sibling sections contradict the header structure:

```zax
if i := 0 ;;
    i < 100 ;;
      ++i {
  body()
}
```

#### Explicit continuation as the alignment escape hatch

An alignment-sensitive programmer may move the whole header to the common
continuation level:

```zax
if \
  firstInitializer();
  secondInitializer();
  thirdInitializer() ;;
  condition ;;
  postOperation() {
  body()
}
```

The same form remains stable under a long label:

```zax
if shadowable extraordinarily_long_label: \
  firstInitializer();
  secondInitializer() ;;
  condition {
  body()
}
```

Here `\` is necessary and legal. A label does not itself continue the physical
line; requiring explicit continuation avoids construct-dependent behavior for
conditionless `forever` and `scope`. By contrast, `\` after `;;` would be
redundant and invalid.

Standalone `#` is not a no-op or alignment spacer. Current uses of `#`
acknowledge an actual value, result, destination, or completion position; the
alignment problem does not justify widening it into a general empty statement.

#### Blocks inside composed initializers

A block in an initializer retains its ordinary nested lexical scope:

```zax
if {
  temporary := inspectEnvironment()
  recordInspection(temporary)
} ;; condition {
  body()
}
```

`temporary` is destroyed at `}` and is not visible to the condition or body.
No flow-header position implicitly applies `[[descope]]`.

When a multiline block is one operand of vertically composed header
initialization, explicit continuation places the block and its sibling operands
at the common level:

```zax
if \
  {
    temporary := inspectEnvironment()
    recordInspection(temporary)
  };
  resource := getResource() ;;
  validated(resource) ;;
  discard(resource) {
  use(resource)
}
```

The block opens and closes at the two-space header-continuation level;
`resource` is its sibling, so `resource` belongs to the enclosing flow-header
scope.

The compact horizontal form is also legal:

```zax
if {
  temporary := inspectEnvironment()
  recordInspection(temporary)
}; resource := getResource() ;;
  validated(resource) ;;
  discard(resource) {
  use(resource)
}
```

This vertical form is a mandatory layout-intent error even though its tokens can
be parsed:

```zax
if {
  temporary := inspectEnvironment()
  recordInspection(temporary)
};
  resource := getResource() ;;
  validated(resource) ;;
  discard(resource) {
  use(resource)
}
```

The block closes at the flow keyword level while the `;` claims an indented
right operand as its structural sibling. Use explicit continuation to move both
operands to the header-continuation level or put the right operand horizontally
after `};`.

Moving `}` onto the last inner statement line is not a workaround; a multiline
block's closing delimiter returns to the level where that block statement began.

### Header schemas, ordering, and binding scope

`;;` separates construct-specific header sections. It is not doubled statement
composition and does not share `;` spacing.

Conditional constructs use:

```text
condition
initializer ;; condition
initializer ;; condition ;; post
;; condition
;; condition ;; post
```

Only the initializer position may be empty. One `;;` always means initializer
plus condition; therefore this is not condition plus post:

```zax
if condition ;; postOperation()
  body()
```

It treats `condition` as an initializer and requires `postOperation()` to produce
`Boolean`. A condition-plus-post form with no initializer is explicit:

```zax
if ;; condition ;; postOperation()
  body()
```

Conditionless `forever` and `scope` use:

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

The keyword determines the header schema. A conditional loop's second section
is a condition, while a conditionless construct has no condition to supply.
This is construct-specific meaning, not an unresolved contradiction.

The initializer accepts any effective statement and runs once. A binding it
introduces becomes visible after its own initializer completes and remains
visible to later initializer operands, the condition, true and false clauses,
the post operation, and the complete body. It is destroyed when the complete
flow statement exits. Bindings introduced inside a nested initializer block do
not escape that block.

Calls in initializer, condition, and post sections are ordinary synchronous call
sites. Earlier completed effects are visible to callbacks and reentrant code;
no code observes a partially established binding.

### `if`, `else`, and normal-completion post operations

`else` binds to the nearest open `if`. A label on `if` names the complete
`if`/`else if`/`else` chain; individual `else` clauses do not have independent
flow labels.

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
4. select the true body or corresponding false/`else` body;
5. run that body;
6. destroy clause-body locals on normal completion;
7. run the post operation exactly once while header bindings remain alive;
8. destroy header bindings; and
9. leave the complete `if`.

The post operation also runs when the condition is false and no `else` body
exists. It does not run when initializer or condition evaluation fails to
complete normally, or when `break`, `continue`, `next`, `return`, or panic leaves
the complete `if`.

An `if` post operation is ordinary normal-completion work, not a destructor,
`finally`, `defer`, or guaranteed cleanup hook. The ability to bypass it
deliberately is part of the labeled-exit model.

### Loops and explicit `scope`

`while` and `until` test before first entry. `redo while` and `redo until` differ
only by skipping that first test: the body executes once, then ordinary
post/test cycling begins. Whether a compiler internally rewrites `until` as
negated `while` is not language behavior unless future reflection or debugging
makes it observable.

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

| Construct | Initial entry | Normal fallthrough or `next` | `continue` |
| --- | --- | --- | --- |
| `while` / `until` | initializer, test, selected body | post, test, selected body | test, selected body |
| `redo while` / `redo until` | initializer, body without first test | post, test, selected body | test, selected body |
| `forever` | initializer, body | post, body | body |
| explicit `scope` | initializer, body | post, then exit normally; `next` instead re-enters | re-enter body |

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
goes to the next test, or directly to the next body entry when the construct has
no condition.

An explicit `scope` is re-enterable; an arbitrary `{ ... }` block is not.
Normal `scope` completion runs post and exits. `next` targeting the scope runs
post and re-enters. `continue` targeting it skips post and re-enters. `break`
skips post and exits. A `forever` body differs because normal completion
implicitly repeats after post.

It is useful in design reasoning to compare `forever` with an always-repeat
decision and `scope` with a never-repeat decision. Human-facing teaching should
not claim that either evaluates a hidden Boolean condition, operator, or
reflectable expression.

### Flow labels and transfer targets

#### Label spelling and placement

A flow label:

- starts with a lower-case ASCII letter;
- may then use lower-case ASCII letters, digits, and `_`, including consecutive
  interior underscores;
- may not begin or end with `_`; and
- is immediately followed by `:`.

The corresponding pattern is:

```text
[a-z](?:[a-z0-9_]*[a-z0-9])?:
```

Examples:

```zax
outer:
my___label:
loop2:
```

In a flow-label-capable position, adjacent `name:` claims label intent. A
label-shaped token that violates the category's spelling rule is an intent
error, not another construct. Parameter and result labels retain their own
category-specific name rules.

The label follows the complete flow introducer:

```zax
if shadowable check_resource: condition
while shadowable outer: condition
scope shadowable retry_operation: {
  body()
}
redo while shadowable outer: condition
redo until shadowable outer: condition
```

For `redo while` and `redo until`, the two keywords form one introducer. The
optional `shadowable` marker and label are considered only after that complete
introducer. Source that does not supply `while` or `until` after `redo` is
ordinary syntax rejection, not a label-specific intent check.

That last distinction belongs in this working record to prevent an accidental
compiler obligation. Promoted programmer-facing material should teach only the
valid positive forms above rather than mentioning arbitrary malformed token
sequences.

#### Label namespace and shadow permission

Flow labels are a separate, explicitly shaped name category from ordinary
bindings. The same spelling may be used without lookup ambiguity:

```zax
while outer: outer := 0 ;; outer < 100 ;; ++outer {
  ++outer
  break outer:
}
```

Expression `outer` names the binding; target `outer:` names the label. Label
shadowing is checked only against labels, and ordinary binding shadowing only
against ordinary bindings.

Overlapping duplicate labels are errors unless the outer label grants one-level
permission:

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

An inner label must itself say `shadowable` to permit another nested reuse. While
the inner label is active, references to that spelling cannot reach the hidden
outer label.

#### Bare and explicit target selection

For a particular transfer keyword:

1. scan outward through constructs ineligible for that keyword;
2. find the nearest eligible target;
3. if it is unlabeled, a bare transfer targets it;
4. if it is labeled, a bare transfer is an intent error and may not silently skip
   past it; and
5. an explicit `keyword label:` may cross nearer constructs and selects that
   visible eligible target.

| Construct | Bare `break` | Bare `continue` / `next` | Explicit `break label:` | Explicit `continue label:` / `next label:` |
| --- | --- | --- | --- | --- |
| Loop | Yes when nearest and unlabeled | Yes when nearest and unlabeled | Yes | Yes |
| Explicit `scope` | Yes when nearest and unlabeled | Yes when nearest and unlabeled | Yes | Yes |
| Labeled `if` | No implicit target | No | Yes | No |
| Unlabeled `if` | Transparent | Transparent | Not nameable | Not nameable |
| Ordinary block | Transparent | Transparent | No | No |

`continue if_label:` and `next if_label:` are errors. Re-evaluating an `if` would
turn selection into iteration; treating them as exits would make them misleading
synonyms for `break if_label:`.

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
```

Omitting the colon in a target position is an intent error. `continue()` and
`next()` remain ordinary call expressions: the keywords are recognized only as
standalone transfer statements or transfer statements followed by a flow label.

#### Unwinding and lifetime

Before a transfer arrives at its target, every body or nested scope it leaves is
destroyed in reverse construction order. Target-header bindings remain alive
when `next` or `continue` re-enters that target and are destroyed only when the
complete target flow statement exits.

On normal body completion:

1. destroy body-local bindings;
2. run post when the construct requires it;
3. test, re-enter, or exit according to the construct; and
4. destroy header bindings when the complete construct exits.

`break` and `return` skip ordinary post operations but still perform applicable
scope destruction. Construction and result completeness must hold on every
normal path produced by branches, loops, and transfers.

### Return and result completion

`return` exits the complete function rather than selecting a flow label. It
unwinds every applicable scope and must satisfy the complete declared result
shape.

A value-bearing return supplies every result position. A bare return or
fallthrough supplies no expressions and is valid only when every result slot is
already complete. `#` in a value-bearing return occupies one result position and
means that no new explicit value is supplied for that slot:

1. preserve an already completed result;
2. preserve a result initialized by its declaration;
3. otherwise request type-default construction; and
4. diagnose the return if that type cannot be default-constructed.

```zax
foo final : (
  first : Integer = 5,
  second : String
)() = {
  return #, "apple" // preserve 5
}

bar final : (
  first : Integer,
  second : String
)() = {
  first.+++(42)
  return #, "apple" // preserve 42
}

foobar final : (
  first : Integer,
  second : String
)() = {
  return #, "apple" // type-default first
}
```

This keeps the broad meaning of `#` as an explicit "do not supply or retain a
value here" acknowledgement while giving it position-specific behavior:

- result declarations use it to permit caller omission;
- caller mapping uses it to discard a produced result; and
- return lists use it to preserve or default-complete the corresponding slot.

`return #` differs materially from bare `return`: the former may default-complete
an unconstructed slot, while the latter requires prior completion. This extends
the current function-invocation owner and must be incorporated there during
promotion.

### Conditional expression and branch convergence

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

The direct-construction model can be illustrated with delayed unsafe
construction, but the actual conditional expression never exposes an
unconstructed destination:

```zax
e : Type = unsafe ???
if a > b
  e.+++(c)
else
  e.+++(d)
```

This is explanatory equivalence, not a required lowering. The destination is
always established by the one selected path, without default-constructing and
then assigning over it.

#### Branch-dependent resolution

Surrounding operators and calls may resolve separately for each selected arm:

```zax
e := f + (condition ?? c ;; d)
```

This may select `f + c` or `f + d`, including different overload bodies. The
behavior preserves source evaluation:

1. evaluate `f` exactly once in its original position;
2. evaluate `condition` once;
3. evaluate only `c` or `d`; and
4. invoke the operation selected for that path.

The explanatory distribution must never imply duplicated evaluation of `f`.

Branch-dependent resolution may propagate through the enclosing evaluation tree
of one complete source operation. Every runtime path must converge to one
statically usable result shape before that operation completes:

```zax
whatAmI : KnownType = (a < b ?? c ;; d) // each path constructs KnownType
```

An inferred binding may not retain several possible types:

```zax
whatAmI := (a < b ?? c ;; d) // error if c and d remain different types
```

Different overloads or constructors may provide convergence when they ultimately
produce or construct the same type. Boolean-producing paths may converge inside
a condition:

```zax
if ?(a < b ?? c ;; d)
  doSomething()
```

Statement composition does not extend convergence into a later statement:

```zax
if condition
  e : Type = f + (condition ?? c ;; d);
  doSomething(e)
```

The declaration must converge before `;`; the later call cannot help resolve it.
Allowing an ordinary stored binding to retain branch-dependent runtime types
would cross into deferred value-polymorphic representation, lifetime, and
dispatch design.

`??` itself is sufficient source acknowledgement. No additional marker is
required merely because different branch paths select different overloads.
Diagnostics and tooling should show branch-specific selections and costs; a
future lint may flag different overload declarations selected before convergence.

### Static analysis, completion, and callbacks

Flow analysis is cross-cutting rather than owned solely by loops or lifecycle:

- optional dereference requires a proof of presence;
- every normal path must complete required result and instance state;
- one lifetime may not be constructed or destroyed twice;
- use before construction and use after destruction are errors;
- obvious unreachable code and accidental nontermination are diagnosable; and
- conservative proof failures may require future narrow semantic assertions.

`while i : Integer ;; i < 100 { }` is an obvious candidate for an
accidental-nontermination diagnostic because its empty body never changes `i`.
Complete termination proof is not required here. The future analysis owner must
separate mandatory language validity, conservative proof overrides, advisory
diagnostics, and ordinary lints.

Initializers, conditions, post operations, truth-producing operators, and
conditional arms may invoke arbitrary synchronous code. Each call completes
before the next visible phase. Reentrant code may observe completed earlier
effects but never a partially established binding. Future async suspension and
cancellation remain separate design.

### Programmer-visible cost and diagnostics

#### Cost

- Header initialization pays ordinary construction and call cost once.
- Conditions pay their visible evaluation cost at each defined test.
- `redo` always pays one body execution before its first test.
- `next` pays post cost; `continue` deliberately skips it.
- `break`, `continue`, `next`, and `return` pay destruction cost for every scope
  they unwind.
- A normal-completion post operation may perform substantial ordinary work and is
  not hidden cleanup.
- A conditional expression evaluates one arm and directly constructs its result.
- Branch-dependent ternary resolution may select different callable bodies and
  code paths.
- A synthesized `?` or `!` fallback adds a Boolean negation that a direct
  overload may avoid.

#### Rejection and diagnostic categories

The working model distinguishes ordinary syntax rejection, semantic errors, and
deliberate intent/layout checks. This classification prevents programmer-facing
rules from accidentally requiring special recognition of every malformed token
sequence.

##### Ordinary syntax rejection

Ordinary syntax rejection means the source does not match a legal production.
The parser reports the legal token or construct expected at that point; the
language does not enumerate every unrelated thing a programmer could write.

Examples include:

- a token other than `while` or `until` where the `redo` introducer requires one;
- a condition/header that never supplies a legal body boundary;
- a missing, doubled, or trailing `;` operand; and
- a missing required condition or post section after `;;`.

These failures may receive helpful diagnostics, but no language rule requires
the parser to reinterpret arbitrary source as a misplaced label, operator,
formula, or unrelated keyword.

##### Semantic errors

Semantic errors parse successfully but violate a type, target, name-resolution,
lifetime, or completion rule. They include:

- a condition that does not resolve to exactly `Boolean`;
- a directly selected `?` or `!` result that is non-Boolean in a condition;
- an optional dereference without sufficient proof or semantic assertion;
- a composed statement occupying a condition and therefore producing no
  `Boolean`, even when its token sequence is otherwise parseable;
- a nested block declaration used after its block ends;
- branch-dependent expression paths that fail to converge;
- a transfer label naming a construct ineligible for that keyword;
- label shadowing without permission;
- an incomplete result or instance on normal completion; and
- use before construction, double construction, or invalid destruction.

##### Deliberate intent and layout errors

Intent/layout checks reject a finite, deliberately selected set of near-miss
patterns whose tokens may otherwise parse or resemble another valid form while
presenting contradictory programmer intent. They include:

- whitespace before `;` or missing whitespace after it;
- missing whitespace around `;;` or `??`;
- redundant `\` after `;` or `;;`;
- physical tab characters;
- statement-start indentation that does not use the exact two-space structural
  level;
- sibling header operands or sections at conflicting continuation levels;
- a multiline closing brace at the wrong structural level;
- a body-opening `{` separated onto the next physical line;
- scope-opening `{` without whitespace on both sides;
- exact `{ }` used as an initializer or post header;
- the parseable multiline initializer-block composition whose layout places its
  operands at conflicting levels;
- a ternary in a `;;` header without required disambiguating parentheses;
- a label-shaped token with invalid spelling in a flow-label-capable position;
- a target reference without trailing `:`;
- a bare transfer stopped by a labeled eligible target; and
- any other layout contradiction that a formatter would otherwise have to
  reinterpret.

The category is intentionally bounded. It does not authorize speculative checks
for every malformed spelling a compiler might hypothetically explain.

### Legacy repair obligations

The following are concrete promotion repairs, not new language questions:

- Change declaration-like loop headers such as `while i: ;; ...` to a real label
  or to a complete declaration such as `i := 0` or `i : Integer`.
- Change named-scope forms to `scope process_work:` and target forms to
  `break process_work:`, `continue process_work:`, or `next process_work:`.
- Replace the legacy `else if food = "durian"` test with `==` unless an example
  deliberately teaches the exact-`Boolean` diagnostic.
- Remove stale `for` entries from legacy target lists; no reviewed `for`
  statement exists.
- Replace illegal horizontal examples such as
  `forever { if done break; ++i }` with a valid condition/body boundary.
- Remove copied `assert(starting < ending)` statements that precede a
  header-local `starting` declaration in initialized `redo until` examples.
- Correct the initialized `redo until` sample that uses `starting < ending`
  where the demonstrated until-test is `starting == ending`.
- Correct adjacent declaration spelling such as `starting: Integer` to
  `starting : Integer` where a declaration, not a label, is intended.
- Replace legacy bare optional conditions with explicit Boolean-producing
  presence operations.
- Update result documentation for the aligned `return #` completion behavior.
- Preserve legacy `case continue` as a selection-design consequence to review as
  `case next`.
- Convert promoted and newly touched examples to two-space structural
  indentation, spaces rather than physical tabs, aligned header continuation,
  and the aligned brace and separator whitespace.

Legacy snippets that are already provably safe under optional-state analysis do
not need redundant nearby checks. Repairs should address demonstrated errors,
not mechanically decorate every dereference.

### Live consequences and explicit deferrals

Deferring design does not defer capture. A consequence is fixed now when needed
for coherence; otherwise it receives a live owner or indexed raw destination,
activation pressure, and any constraint it places on current work.

#### Cross-cutting analysis controls

[Raw analysis controls](../../raw/analysis-controls.md) already owns semantic proof
overrides, lint separation, contract provenance, and smarter-compiler behavior.
Flow-derived input still needs to be added there during a separately authorized
change:

- optional-presence and dereference proofs;
- reachability and accidental nontermination;
- loop-sensitive result completion;
- analysis of branch-dependent construction; and
- obsolete proof assertions after analysis improves.

#### Optional behavior

[Legacy optional material](../../../optional.md) currently claims bare optional
conditions and unchecked dereference behavior that conflict with this model.
Promotion must correct the condition claim and state the static proof obligation.
Complete optional construction, reset, unwrapping, and operator design may remain
future work only after the surviving evidence has a live destination.

#### Selection and `case next`

Complete `switch`, `case`, patterns, exhaustiveness, and fallthrough remain
future selection work. The concrete finding that legacy `case continue` should
be reviewed as `case next` must be captured in a dedicated indexed selection
input or in an actually aligned next work item before 007 is archived. No
selection-specific raw owner currently exists.

#### Persistent cross-cutting audit input

A new indexed raw input such as `project/raw/cross-cutting-audit.md` is needed
for concerns that cross several owners and must survive numbered-work archival.
Each entry should preserve:

- a concrete concern and representative example;
- why local review may miss it;
- affected or likely owners;
- what would count as resolution;
- activation pressure;
- current constraints; and
- eventual disposition.

Initial entries should cover:

- an audit of valid ternary-convergence forms;
- branch-dependent overload selection and visible cost;
- repository-wide source-layout and legacy-example repair;
- confirmation that every deferred consequence has a live non-archive owner; and
- legacy claims contradicted by promoted condition, optional, or exit behavior.

This raw input must not become a miscellaneous design dump or competing
authority. Current work still resolves every immediately necessary question.

#### Reusable process lesson

The reusable process finding is:

> A consequence may be deferred for design, but never deferred for capture.
> Record it, assign it an owner or indexed raw destination, identify what
> reactivates it, and state any constraint it places on current work.

[Documentation architecture](../../documentation.md) already establishes much of
this rule. A future authorized project-guidance review should decide whether the
wording needs clarification there and whether the synchronized operating prompts
need a short teaching reminder. This item does not authorize reading or editing
those prompt sources.

#### Promotion teaching rule

Programmer-facing documentation should not manufacture a concept merely to deny
it. The aligned promotion rule is:

> Teach valid forms and meaningful boundaries. Mention an invalid form only when
> it prevents a likely misunderstanding, demonstrates an intentional diagnostic,
> or distinguishes two genuinely plausible source meanings.

Counterexamples such as `foo() ; bar()`, a missing target `:`, or the parseable
but misaligned initializer-block composition earn their place because they teach
deliberate source-intent boundaries. Arbitrary malformed source after `redo`
does not: mentioning it would invite questions about a relationship the language
never establishes.

Working records may preserve rejected hypotheses and diagnostic classification
when later promotion must avoid inventing an obligation. Promoted owners should
remove that discovery history and lead with the valid programmer model.

#### Other bounded deferrals

- **Iteration protocols:** `each`, `in`, `from`, ranges, arrays, metadata
  iteration, and customization remain with a future iteration owner. Core flow
  no longer depends on their details.
- **Selection and matching:** complete `switch`/`case` and patterns remain with a
  future selection owner, subject to the preserved `case next` consequence.
- **Resource management:** complete `using` and `own` behavior remains future
  resource/lifetime work. No claim here makes `using` a core transfer target.
- **Value polymorphism:** stored values whose runtime type depends on a ternary
  path, declaration-level runtime dispatch, and `[[likely]]`/`[[unlikely]]`
  remain future value-polymorphism and directive work.
- **Scope capture:** callable-like named-scope capture and captured results remain
  future closure/scope design. Core `scope` here is an explicit flow target.
- **Lifetime strategies:** dangling references, captured results, transfers, and
  complete pointer/reference lifetime behavior remain with
  [raw lifetimes](../../raw/lifetimes.md).
- **Safety:** branch-local `unsafe ???`, incomplete-state escape, and proof
  permissions remain with [raw safety](../../raw/safety.md) and analysis controls.
- **Error propagation:** `except`, `catch`, and specialized error-result
  short-circuiting remain with [legacy except material](../../../except.md). They
  must preserve ordinary exit and result-completion rules.
- **Async:** suspension, cancellation, and async conditions or post operations
  remain with [raw async](../../raw/async.md).
- **Formal grammar and algorithms:** exact grammar productions, proof algorithms,
  diagnostic identifiers, and compiler lowering remain future specification or
  implementation work unless they change programmer-visible behavior.

### Programmer-visible source stability

The aligned rules deliberately protect against:

- changing a value's type silently changing condition truthiness;
- adding or removing `;;` silently turning a body into a post operation;
- a formatter rescuing contradictory source structure;
- renaming a long label forcing continuation reindentation;
- inserting a labeled eligible target silently redirecting a bare transfer;
- adding a local binding changing label resolution, or vice versa;
- changing one ternary arm silently materializing a runtime-dependent stored
  type; and
- smarter compiler analysis invalidating source under an unchanged language
  contract merely because an old assertion became redundant.

Some changes remain intentionally source-visible:

- renaming a flow label breaks explicit target references;
- adding or changing an overload may alter a ternary branch's selected operation;
- adding `else` or a post operation changes normal flow work;
- adding a nested unlabeled loop or explicit `scope` may change a bare target;
  and
- changing construction or result shape changes path-completion obligations.

### Plausible lasting owners and promotion boundaries

- A new **core flow-control** concept owner should teach conditions, clause
  selection, header schemas, `if`/`else`, loops, explicit `scope`, labels,
  `break`/`continue`/`next`/`return`, post behavior, and the conditional
  expression's core branch model.
- **Source structure** must absorb exact two-space indentation, physical-tab
  rejection, `;`/`;;`/`??` whitespace, header continuation, brace placement,
  body boundaries, and block-composition intent errors.
- **Declarations and bindings** retains flow-header binding visibility, nested
  block scope, separate label-category interaction with the ordinary namespace,
  and label shadow permission at the declaration boundary.
- **Function invocation** retains result-slot completion and must absorb
  `return #` behavior.
- **Construction and destruction** retains lifetime completeness and destruction
  across exits; future lifetime owners absorb reference validity and capture.
- A current **operators** owner is needed because direct/fallback `?` and `!`,
  arbitrary operator results, and Boolean short-circuit behavior apply outside
  flow statements. The flow owner should state only what a condition consumes.
- Optional-presence syntax belongs with operators, while proof that optional
  storage is live belongs with construction/lifetime analysis. Complete optional
  construction, reset, and unwrapping behavior may remain legacy input.
- Cross-cutting terminology should distinguish ordinary syntax rejection,
  semantic errors, deliberate intent/layout errors, and normal completion.
- Future detailed **analysis/diagnostics** work must absorb proof controls, lints,
  provenance, reachability, and termination depth.
- Future iteration, selection, resource, error, async, and value-polymorphism
  owners receive the explicit deferrals above.

The pre-promotion documentation-fit dry run must identify every exact affected
owner, legacy correction, raw/index addition, terminology change, and source
example repair. It must not assume that approval of this working record
authorizes those edits.

### Aligned-record checkpoint before the dry run

The language maintainer reviewed and approved this working record as the input
to a pre-promotion documentation-fit dry run. That approval established the
meaning to test; it did not promote the findings. The dry run below verifies:

- the examples express the aligned behavior without relying on hidden context;
- no aligned finding was weakened into a vague summary;
- no deferred concern was mistaken for resolved design;
- each deferred concern has a durable proposed destination and activation
  pressure;
- current owner constraints are preserved; and
- the eventual promotion will teach one coherent programmer model rather than
  the chronology of this discussion.

Approval of this record authorized the dry-run analysis and recording its result
here. It did not authorize promotion, raw/index edits outside this record, legacy
repairs, archival, or work item 008.

## Dispositions and promotion dry run

| Field | Value |
| --- | --- |
| Status | Completed read-only documentation-fit analysis / non-authoritative promotion plan |
| Completed | 2026-08-26 |
| Input | The aligned working findings above |
| Outcome | Successful: every aligned finding has a coherent owner or explicit live deferral |
| New language-design alignment concerns | None discovered |
| Promotion authorization | Not granted by this dry run |

### Dry-run conclusion

The aligned model fits the repository without a new specification area, a new
directory family, or splitting core flow into several competing owner documents.
One cohesive new core-flow owner can teach the programmer model.

The dry run confirms one additional current owner is structurally necessary:
general operator behavior aligned during 007 cannot be hidden inside flow because
`?value` and `!value` may be used as ordinary expressions with arbitrary result
types. A bounded current operators owner can absorb that behavior without
prematurely accepting complete operator declaration, lookup, ranking, literal,
or word-operator design.

All other aligned findings fit existing current owners, existing legacy
remainder pages, or explicit indexed raw destinations. No newly discovered
semantic contradiction requires reopening alignment before planning promotion.

### Proposed minimal structure

```text
language/
  core-flow-control.md       # new current concept owner
  operators.md               # new bounded current concept owner
  construction-and-destruction.md
  declarations-and-bindings.md
  function-invocation.md
  qualifiers.md
  source-structure.md
  terms.md
  ...

project/raw/
  cross-cutting-audit.md     # new indexed live audit input
  selection.md               # new indexed future selection input
  analysis-controls.md
  ...
```

No current file needs to move. Root legacy pages remain at their public paths
with reduced or clarified legacy responsibilities. No compatibility stub,
specification directory, diagnostics directory, or operator subdirectory is
justified.

### Ownership map

| Aligned finding or consequence | Lasting owner | Supporting updates and boundary |
| --- | --- | --- |
| Exact-`Boolean` condition contract; clause selection; `if`/`else`; core loop and `scope` behavior | New `language/core-flow-control.md` | Operators owns how expressions produce values; flow owns what conditions consume and when tests occur |
| Conditional header schemas, phase order, post behavior, and `;;` roles | New `language/core-flow-control.md` | Source structure owns token spacing, continuation, layout, and body boundaries; declarations owns binding visibility |
| `while`, `until`, `redo while`, `redo until`, `forever`, normal completion, `next`, and `continue` order | New `language/core-flow-control.md` | Construction/destruction owns lifetime ending; implementation rewriting remains non-authoritative |
| Flow-label spelling, complete-introducer placement, target eligibility, barriers, and explicit `:` references | New `language/core-flow-control.md` | Declarations owns interaction with ordinary names and the shared `shadowable` permission model |
| Exact two-space structural indentation, physical-tab rejection, brace layout, `;`/`;;`/`??` whitespace, and explicit-continuation teaching | `language/source-structure.md` | Core flow shows local examples without redefining source validity |
| Effective bodies, compound conditions, multiline initializer-block composition, and syntax/semantic/intent diagnostic classification as applied to source | `language/source-structure.md` | `language/terms.md` supplies concise cross-cutting diagnostic vocabulary |
| Flow-header declaration visibility through conditions, clauses, and post; nested initializer-block scope | `language/declarations-and-bindings.md` | Core flow owns phase execution, not declaration mechanics |
| Flow labels as a separate category from the ordinary identifier namespace; label `shadowable` interaction | Core behavior in `language/core-flow-control.md`; namespace boundary in `language/declarations-and-bindings.md` | Avoid duplicate definitions: declarations states the category boundary and links to flow for label semantics |
| Arbitrary direct `?`/`!` results, Boolean-only opposite-operator fallback, direct-pair lint pressure, and Boolean `&&`/`\|\|` short-circuiting | New `language/operators.md` | Complete operator declaration, lookup, and ranking remain legacy/future work; function invocation continues to own shared callable selection |
| Optional presence operation used by `?optionalValue` | New `language/operators.md` | Complete optional representation and state transitions remain on the legacy optional path until focused review |
| Proof that optional storage is live before postfix dereference | `language/construction-and-destruction.md` for the programmer-visible lifetime obligation | `project/raw/analysis-controls.md` retains proof-override, contract-version, and lint details |
| Scope/body/header destruction on normal and abrupt exits | `language/construction-and-destruction.md` | Core flow states which scopes are crossed and when post is skipped; destruction owner states automatic local/header lifetime order |
| Whole-function `return`, complete result shape, and position-specific `return #` behavior | `language/function-invocation.md` | Core flow summarizes return as an exit and links to the complete result contract |
| Conditional-expression evaluation, selected-arm direct construction, one-operation convergence, and statement boundary | New `language/core-flow-control.md` | Function invocation owns branch-specific callable selection; construction owns destination lifetime |
| Branch-specific overload selection without expected-result propagation from a later statement | `language/function-invocation.md` | Must clarify compatibility with its narrow expected-result rule rather than invent a second inference model |
| Ordinary syntax rejection, semantic error, deliberate intent/layout error, normal completion, flow label, and post operation vocabulary | `language/terms.md` | Behavior remains in the applicable concept owner |
| Consequence capture and positive-only programmer teaching | `project/documentation.md` | Detailed project guidance belongs here; no operating-prompt source change is needed |
| Reachability, nontermination, optional proof controls, redundant assertions, and lint separation | `project/raw/analysis-controls.md` | Remains non-authoritative until dedicated analysis/diagnostics work |
| `case next` and complete `switch`/selection pressure | New `project/raw/selection.md` | Root legacy flow material remains provenance until future selection work consumes it |
| Cross-owner convergence and repository-wide repair sweeps | New `project/raw/cross-cutting-audit.md` | Must stay indexed and live rather than disappear with archived numbered work |

### Owner fit and architecture findings

#### New core flow owner

`language/core-flow-control.md` is cohesive even though it will be substantial.
Conditions, header phases, clauses, loops, explicit `scope`, labels, transfers,
post operations, and the conditional expression share one programmer mental
model and repeatedly depend on one another. Splitting labels, loops, or exits
into separate current documents would force ordinary readers across competing
definitions and would not provide independent lifecycle or reuse.

The owner should lead with ordinary `if` and loop examples, then layer:

1. exact-`Boolean` conditions;
2. effective bodies and header schemas;
3. `if`/`else` and normal-completion post;
4. pre-test, post-test, unconditional, and explicit-scope progression;
5. `break`, `continue`, `next`, labels, and target selection;
6. destruction/result interactions;
7. conditional expressions and convergence;
8. diagnostics, costs, source stability, and advanced reference material.

It should not reproduce the working chronology, rejected alternatives, dry-run
ownership analysis, or arbitrary malformed syntax.

#### New bounded operators owner

`language/operators.md` should be current only for the programmer-visible
operator behavior actually aligned or already constrained by current owners:

- operator results may be arbitrary types;
- direct `?` and `!` selection precedes the Boolean fallback;
- the opposite-operator fallback is available only with a `Boolean` result;
- defining both Boolean forms is legal and may create lint pressure;
- core `&&` and `||` require Boolean operands and short-circuit left to right;
- shared fixed-arity callable viability and narrow result-context behavior remain
  owned by function invocation.

It should explicitly leave complete declaration syntax, operator catalogs,
lookup domains, priority/ranking, literals, word operators, conversion, and
generated operators to later focused work. This boundary is narrow enough to
avoid accepting the entire root legacy `operator.md` page.

#### Existing owners can absorb their findings

- `language/source-structure.md` already owns every affected layout and
  statement concern. It must replace its statement that `;;` is unaccepted and
  extend its mandatory-layout model rather than create another syntax owner.
- `language/declarations-and-bindings.md` already has the flow-initializer
  section, ordinary namespace boundary, and `shadowable`; the new findings fit
  there without reorganizing the document.
- `language/function-invocation.md` already owns return completion, result
  acknowledgement, callable selection, diagnostics, costs, and source stability.
- `language/construction-and-destruction.md` already owns live/incomplete state,
  automatic destruction, and proof pressure. It needs a local/header lifetime
  subsection but not a separate scope-lifetime owner.
- `language/terms.md` can add concise terminology without becoming a diagnostics
  or flow behavior owner.

No existing current owner is overloaded enough to require a split for this
promotion.

### Current-owner updates

#### `language/source-structure.md`

- Expand metadata ownership to exact structural indentation, physical tabs,
  `;;`/`??` separator whitespace, flow-header continuation, and brace placement.
- Teach the exact two-space structural grid while preserving deeper expression
  alignment only after continuation is established.
- Extend the one-continuation-reason rule to `;` and `;;`; reject redundant `\`.
- Distinguish `;` attachment from `;;`/`??` surrounding whitespace.
- Add header-level vertical composition, explicit `if \` alignment, block
  initializer scope presentation, and the parseable-but-misaligned counterexample.
- Add body-opening and closing-brace rules and exact empty-header-block intent.
- Split programmer diagnostics into ordinary syntax, semantic, and deliberate
  intent/layout categories only at the source concerns owned there.
- Remove the boundary claim that `;;` is unaccepted.
- Reformat every current example in this owner to valid two-space structural
  indentation.

#### `language/declarations-and-bindings.md`

- Extend flow-header visibility through post operations.
- Replace the old block-initializer example with aligned two-space legal forms
  and explain which declarations escape the nested block.
- State that flow labels are an explicitly shaped category separate from the
  ordinary identifier namespace.
- Reuse the outer-declaration, one-level `shadowable` model while linking exact
  flow-label placement and targeting to core flow.
- Update diagnostics, formatting, and boundaries accordingly.
- Reformat every current example to two-space structural indentation.

#### `language/function-invocation.md`

- Extend return completion with `#` preserving an already completed/defaulted
  result or type-defaulting an otherwise incomplete slot.
- Contrast bare `return` with `return #`.
- Preserve the complete-result-shape rule because `#` occupies a result position.
- Add branch-specific callable selection under a conditional expression while
  preserving once-only source evaluation and the narrow expected-result rule.
- Update diagnostics, cost, formatting, source-stability, and boundary sections.
- Reformat every current example to two-space structural indentation.

#### `language/construction-and-destruction.md`

- Add automatic local, body, and flow-header lifetime ending on normal and abrupt
  scope exit.
- State destruction ordering for crossed scopes and distinguish body locals,
  post execution, and surviving target-header bindings.
- State the proof obligation before optional stored value dereference without
  accepting complete optional syntax.
- Route conservative proof controls to raw analysis input.
- Update diagnostics, costs, and boundaries.
- Reformat every current example to two-space structural indentation.

#### `language/qualifiers.md`

No flow semantics change is required. Reformat its current Zax examples to the
new exact two-space structural indentation so the current conceptual set does
not demonstrate invalid source after source structure is promoted.

#### `language/terms.md`

Add concise linked entries for:

- deliberate intent/layout error;
- flow label;
- normal completion;
- ordinary syntax rejection;
- post operation; and
- semantic error.

The terms page should not define grammar, target selection, or diagnostic
algorithms.

### Legacy-page dispositions

#### `flow-control.md`

Retain the path as a legacy remainder page, but remove the superseded core
sections for:

- `if`/`else` and header initialization;
- conditional/ternary expression;
- `while` and `until`;
- `redo while` and `redo until`; and
- `forever`.

Replace them with a short current-owner route. Retain continuing legacy evidence
for `each`, `switch`, `using`, and runtime value polymorphism under an explicit
legacy-remainder heading. The new selection raw input must identify the retained
`switch` evidence and preserve the aligned `case next` pressure.

This value-based pruning disposes the known declaration-like `i:`, illegal
horizontal body, `redo until`, and other core sample defects by replacing their
useful meaning with the reviewed owner rather than repairing superseded teaching.

#### `scope.md`

Remove superseded unnamed/named/anonymous core exit sections and route them to
the new core-flow owner. Retain only the unreviewed callable-like scope-capture
material under an explicit legacy-remainder notice. Removing the superseded core
sections also removes stale `for`, old target spelling, and old label syntax.

#### `operator.md`

Add a route to the new current operators owner for aligned result, `?`/`!`, and
Boolean short-circuit behavior. Retain the remaining page as legacy input for
declaration forms, lookup domains, ranking, literals, and word operators.

#### `optional.md`

Add routes to core flow for the exact-Boolean condition boundary, operators for
the optional presence operation, and construction/destruction for the
live-storage proof obligation. Replace the two bare optional conditions with
explicit presence operations and revise unchecked-dereference prose so it no
longer contradicts mandatory static proof. Retain complete optional
construction/reset behavior as legacy input.

#### `except.md`

Add the current condition/operator routes and replace the known bare
`if capturedError` forms with explicit Boolean-producing error-presence
operations. Retain complete `except`/`catch` short-circuit behavior as legacy
input.

#### `discard.md`

Expand its existing function-invocation route to include the current
position-specific `return #` behavior. Retain local/type/memory-policy uses as
legacy input.

#### `nothing.md`

Add the current operators route for examples such as `if !_`. Retain complete
`Nothing` representation, construction, and fallback behavior as legacy input.

Other root legacy pages remain non-authoritative and are not rewritten during
this promotion merely because they use older indentation. The cross-cutting
audit input must keep the eventual legacy example sweep live. Any known semantic
contradiction discovered while applying the authorized change set still requires
disposition rather than silent retention.

### Raw and project-guidance updates

#### New `project/raw/selection.md`

Preserve:

- the future selection concern and root legacy provenance;
- `switch`, `case`, `default`, runtime and complex-type matching pressure;
- the aligned consequence that legacy `case continue` should be reviewed as
  `case next`;
- what core flow already constrains;
- activation pressure; and
- retirement criteria.

It must not accept selection syntax or behavior.

#### New `project/raw/cross-cutting-audit.md`

Use the entry shape aligned above and seed it with:

- ternary convergence forms;
- branch-specific overload/cost audits;
- current-versus-legacy two-space source repair;
- live-owner checks for deferred consequences; and
- contradictory legacy condition, optional, and exit claims.

This file remains live across numbered-work archival and retires only after every
entry is dispositioned.

#### `project/raw/analysis-controls.md`

Add flow-derived proof pressure for optional presence, reachability,
nontermination, loop/result completion, branch-dependent construction, and
redundant assertions after analysis improves. Preserve the distinction between
semantic assertions and lint suppression.

#### `project/raw/feature-catalog.md`

Disposition the core `if`/`else`, loop, and scope-control claims to the new
current owner while retaining or routing `each` and selection claims to their
future evidence. Do not leave promoted features looking like unreviewed catalog
claims.

#### `project/raw/README.md`

Index the new selection and cross-cutting audit inputs with their activation and
retirement posture.

#### `project/documentation.md`

Add the two aligned reusable documentation rules:

1. design may be deferred, but capture may not; and
2. promoted teaching leads with valid forms and mentions invalid forms only for
   likely misunderstandings, intentional diagnostics, or genuinely plausible
   alternative meanings.

The current operating prompts already contain the necessary consequence and
promotion boundaries. Their sources are not part of the proposed promotion
change set.

### Navigation and reading paths

Update `index.md`:

- add `language/core-flow-control.md` and `language/operators.md` to **Start
  here** and **Current conceptual design**;
- keep root `flow-control.md`, `scope.md`, `operator.md`, `optional.md`,
  `except.md`, `discard.md`, and `nothing.md` in their legacy groups while they
  retain continuing evidence; and
- ensure no current route asks a programmer to read a raw or archived file.

`README.md` remains a correct repository-level router and needs no promotion
change. `project/README.md` continues to point at active work 007 until separate
closure/archival authorization. No archive index or work 008 change belongs in
promotion.

### Exact proposed promotion change set

#### Create

1. `language/core-flow-control.md`
2. `language/operators.md`
3. `project/raw/selection.md`
4. `project/raw/cross-cutting-audit.md`

#### Modify current owners and terminology

5. `language/source-structure.md`
6. `language/declarations-and-bindings.md`
7. `language/function-invocation.md`
8. `language/construction-and-destruction.md`
9. `language/qualifiers.md` (two-space example repair only)
10. `language/terms.md`

#### Modify navigation and project/raw guidance

11. `index.md`
12. `project/documentation.md`
13. `project/raw/README.md`
14. `project/raw/analysis-controls.md`
15. `project/raw/feature-catalog.md`

#### Disposition affected legacy pages

16. `flow-control.md`
17. `scope.md`
18. `operator.md`
19. `optional.md`
20. `except.md`
21. `discard.md`
22. `nothing.md`

#### Update the active record during promotion

23. `project/work/007-core-flow-control.md` to record the authorized promotion
    result and final dispositions before any separate closure operation.

No other file is currently required. Newly discovered affected material during
promotion must be discussed and authorized rather than silently added to this
change set.

### Proposed promotion sequence

The exact change set should be authorized and applied as one coherent promotion,
not as independent semantic fragments:

1. Create the new current owners and raw destinations.
2. Update existing current owners and terminology against those destinations.
3. Prune and route legacy core material while preserving future evidence.
4. Update raw inputs, project documentation, and public navigation.
5. Reformat every current conceptual Zax example to exact two-space structural
   indentation.
6. Validate links, headings, examples, authority language, and source rules.
7. Record actual promotion dispositions in 007.

Staging, committing, pushing, archival, and work 008 remain separate actions.

### Promotion validation plan

The authorized promotion should verify:

- every current concept has one owner and every cross-link states a dependency
  rather than creating duplicate authority;
- all current conceptual Zax examples use spaces, exact two-space structural
  indentation, aligned continuation, and valid brace/separator whitespace;
- `language/source-structure.md` no longer says `;;` is unaccepted;
- current condition examples produce exactly `Boolean`;
- no current example uses declaration-like `i:` where a declaration was intended;
- labels, targets, and `shadowable` use the aligned spelling and placement;
- `continue` and `next` ordering agrees in prose, tables, and examples;
- `redo` examples skip only the first test;
- post operations run and are skipped on the aligned paths;
- `return #` agrees across invocation and legacy discard routing;
- conditional-expression examples converge within one complete operation;
- optional and error legacy examples no longer use bare non-Boolean conditions;
- retained legacy pages clearly route superseded concerns and keep only useful
  future evidence;
- new raw files are indexed and have activation/retirement criteria;
- project and public routers reach every new current owner;
- no current or raw document depends on archived work for meaning;
- Markdown links and heading anchors resolve;
- no physical tabs or trailing whitespace are introduced; and
- the final changed-file set matches the authorized promotion scope.

Documentation-only validation does not require compiler tests because this
repository has no implementation. Existing Markdown/site validation should be
used if the repository exposes it when promotion is authorized.

### Dry-run decisions required before promotion authorization

The dry run introduces no new language-design question. Promotion still requires
language-maintainer alignment on the proposed documentation structure and exact
change set:

1. approve one new `language/core-flow-control.md` owner;
2. approve one bounded new `language/operators.md` owner rather than deferring
   aligned general operator behavior;
3. approve value-based pruning of superseded core sections from `flow-control.md`
   and `scope.md` while retaining their future legacy remainders;
4. approve the two new indexed raw inputs; and
5. approve the current-owner two-space example sweep as part of promotion while
   retaining the repository-wide legacy sweep in the cross-cutting audit input.

These are expected dry-run architecture and change-set decisions, not newly
discovered semantic alignment concerns.

## Promotion result

| Field | Value |
| --- | --- |
| Status | Authorized promotion applied / working record still active and non-normative |
| Applied | 2026-08-26 |
| Authorization | Explicit maintainer authorization for the exact dry-run change set |
| Scope | The 23-file change set below, applied as one coherent promotion |
| Not performed | Staging, committing, pushing, archival, closure, and work item `008` |

The exact proposed promotion change set was authorized and applied as one
promotion. This section records the actual dispositions. It does not archive this
work item, mark it historical, or open work item `008`; those remain separate
authorized actions. The prior staged checkpoint of this file is preserved, and
these promotion edits are unstaged.

### Created

1. `language/core-flow-control.md` — new current owner teaching exact-`Boolean`
   conditions, effective bodies, header schemas, `if`/`else` and post, the
   `while`/`until`/`redo`/`forever`/`scope` progression, flow labels and target
   selection, unwinding and completion, the conditional expression and
   convergence, analysis, costs, diagnostics, formatting, and source stability.
   It leads with usable examples and links current owners rather than reproducing
   the working chronology or arbitrary malformed syntax.
2. `language/operators.md` — new bounded current owner for arbitrary operator
   results, direct and fallback pre-unary `?`/`!`, paired Boolean forms and lint
   pressure, Boolean `&&`/`||` short-circuiting, and the optional-presence
   operation, explicitly deferring complete declaration, lookup, ranking,
   literal, word-operator, and conversion design.
3. `project/raw/selection.md` — new indexed future selection input preserving the
   `switch`/`case`/`default` concern, legacy provenance, the `case next`
   consequence, current-flow constraints, activation, and retirement criteria.
4. `project/raw/cross-cutting-audit.md` — new indexed live audit input seeded with
   ternary-convergence, branch-cost, two-space repair, live-owner, and
   contradicted-legacy entries; it stays live across archival.

### Modified current owners and terminology

5. `language/source-structure.md` — absorbed exact two-space structural
   indentation and physical-tab rejection, `;` attachment and `;;`/`??`
   surrounding whitespace, header continuation with the explicit `\` alignment
   escape hatch and redundant-continuation rule, brace layout and body
   boundaries, the empty-header-block intent error, multiline initializer-block
   composition (compact/legal/invalid), and the source-level diagnostic
   categories. Removed the claim that `;;` is unaccepted. All current examples
   reformatted to two-space structural indentation.
6. `language/declarations-and-bindings.md` — extended flow-header binding
   visibility through the post operation, clarified nested initializer-block
   scope, stated flow labels as a separate name category, reused the one-level
   `shadowable` model while linking label semantics to core flow. Examples
   reformatted to two-space.
7. `language/function-invocation.md` — added position-specific `return #`
   preservation and default-completion within the complete-result-shape rule,
   contrasted it with bare return, and added branch-specific callable selection
   under a conditional expression without breaking the narrow expected-result
   rule. Diagnostics, costs, formatting, source stability, and boundaries
   updated; examples reformatted to two-space.
8. `language/construction-and-destruction.md` — added automatic local, body, and
   flow-header lifetime ending and destruction order across normal and abrupt
   exits, target-header survival on `next`/`continue`, and the optional
   live-storage proof obligation with the analysis-control boundary. Diagnostics,
   costs, and boundaries updated; examples reformatted to two-space.
9. `language/qualifiers.md` — current examples reformatted to two-space structural
   indentation; no qualifier redesign.
10. `language/terms.md` — added concise cross-linked entries for deliberate intent
    or layout error, flow label, normal completion, ordinary syntax rejection,
    post operation, and semantic error.

### Modified navigation and project/raw guidance

11. `index.md` — added the two new current owners to **Start here** and **Current
    conceptual design**; legacy routes retained; no public route to raw, project,
    or archive.
12. `project/documentation.md` — captured the two aligned rules: design may be
    deferred but capture may not; and promoted teaching leads with valid forms and
    mentions invalid forms only for likely misunderstanding, intentional
    diagnostics, or genuinely plausible alternate meanings.
13. `project/raw/README.md` — indexed the selection and cross-cutting-audit inputs
    with activation and retirement posture.
14. `project/raw/analysis-controls.md` — added flow-derived proof pressure
    (optional presence, reachability, nontermination, loop/result completion,
    branch-dependent construction, obsolete assertions) and updated provenance.
15. `project/raw/feature-catalog.md` — dispositioned the core `if`/`else`, loop,
    scope-control, `switch`, `each`, operator, and value-polymorphism catalog
    entries to their owners or future evidence without losing provenance.

### Dispositioned legacy pages

16. `flow-control.md` — value-based pruned the superseded core `if`, ternary,
    `while`, `until`, `redo while`, `redo until`, and `forever` sections and
    routed them to the new owner, retaining `each`, `switch`, `using`, and value
    polymorphism under an explicit legacy-remainder heading.
17. `scope.md` — pruned the superseded unnamed, named, and anonymous exit
    sections and routed them to the core owner, retaining `scope` variable
    capture as legacy remainder.
18. `operator.md` — routed the aligned result, `?`/`!`, and Boolean short-circuit
    behavior to the new owner, retaining declaration, lookup, ranking, literal,
    and word-operator material as legacy.
19. `optional.md` — routed the condition, operator, and lifetime boundaries;
    replaced the bare optional conditions with explicit `?` presence checks; and
    revised unchecked-dereference prose to require mandatory static proof.
20. `except.md` — routed the condition and operator boundaries and replaced the
    bare captured-error conditions with Boolean-producing `?` presence operations,
    retaining complete error flow as legacy.
21. `discard.md` — extended the function-invocation route to include the current
    `return #` behavior, retaining local, type, and memory-policy uses as legacy.
22. `nothing.md` — routed the current operator behavior for examples such as
    `if !_`, retaining broader `Nothing` behavior as legacy.

### Active record

23. `project/work/007-core-flow-control.md` — updated with this promotion result
    and final dispositions. Closure, archival, and any work item `008` remain
    separate authorized actions and are not claimed here.

### Validation performed

- The staged/unstaged boundary was preserved: only the prior checkpoint of this
  file remains staged, and every promotion edit, including the created files, is
  unstaged. The changed-file set matches the 23-file authorized scope exactly.
- `git diff --check` and `git diff --cached --check` reported no whitespace
  errors.
- No physical tabs were introduced in changed files; current conceptual examples
  use exact two-space structural indentation; Markdown fences remain balanced.
- No current owner still says `;;` is unaccepted; the new raw inputs are indexed;
  the new current owners appear in the public index; legacy pages clearly route
  superseded material; and no current or raw document depends on archived work for
  meaning.
- No known bare optional or captured-error conditions remain in the corrected
  legacy sections.
- Final cross-owner review confirmed that direct `?`/`!` fallback remains
  available whenever the directly written operator is unavailable and the
  opposite operator returns `Boolean`; it is not limited to an already
  Boolean-demanding context.
- Current and public legacy pages do not route ordinary readers into project raw
  inputs. All local Markdown links and heading anchors in the 23-file promotion
  set resolve.
- Cross-cutting terminology distinguishes abrupt completion of an enclosing flow
  body from an explicit `return` that is still a normal function exit for result
  obligations.

## Post-promotion staged-review findings

| Field | Value |
| --- | --- |
| Status | Aligned corrections captured after review of the staged promotion / non-authoritative |
| Review boundary | The 23-file promotion is fully staged; staging is not acceptance |
| Repository edits authorized in this round | This working record only |
| Required next process | Review this capture, rerun the documentation-fit dry run, then separately authorize a corrective promotion |

### Review outcome and process reset

Review of the staged promotion exposed substantive operator corrections and
several owner, teaching, analysis, and example-placement refinements. The staged
promotion must not be treated as accepted until those findings are incorporated
through a new aligned dry run and corrective promotion.

The correct process is:

1. capture the new aligned findings here;
2. review this capture for fidelity;
3. rerun the documentation-fit dry run because the ownership and change surface
   have changed;
4. discuss and align the new dry-run structure and exact change set;
5. wait for separate corrective-promotion authorization; and
6. preserve staging, committing, closure, archival, and work item `008` as
   separate actions.

This section completes step 1 only.

### Conditionally live storage, not optional ownership

The promoted construction owner contains a section titled "Optional live-storage
proof." Its rule is valuable, but the optional-specific heading and metadata make
the construction owner appear to own optional semantics.

The lasting construction/lifetime concern should instead be generalized:

> Access through storage whose value lifetime may not be active requires proof
> that a live value exists on that path.

Optional dereference is one application:

```zax
if ?optionalValue
  use(optionalValue.)
```

The construction/destruction owner should teach:

- conditionally live versus unconstructed storage;
- use-before-construction;
- proof that a value lifetime is active before access; and
- optional stored-value dereference as one example.

It should not define complete optional representation, construction, reset,
presence syntax, or operator behavior. Detailed proof algorithms, assertion
syntax, contract provenance, and lint behavior remain future analysis work.

### Operator owner is cumulative, not a flow remainder

The promoted operators mental model describes itself as owning behavior needed
outside flow statements and the narrow parts core flow needs. That incorrectly
frames the owner as a remainder created by work item 007.

The lasting model should be:

> This is the current conceptual owner for accepted programmer-visible operator
> behavior. It begins with the semantics accepted so far and grows when future
> reviewed operator behavior belongs cohesively here.

Complete declaration syntax, operator catalogs, lookup domains, ranking,
literals, word operators, conversion, and general generation remain deferred
because they are unreviewed, not because they fall outside flow.

### Direct `?`/`!` selection and Boolean-only fallback

Direct operator selection happens before any fallback:

1. resolve the directly written operator under ordinary selection;
2. a condition does not provide an expected `Boolean` result to that selection;
3. one applicable direct overload is used even if its result later fails a
   `Boolean` requirement;
4. ambiguous direct selection is an error and does not activate fallback;
5. only no applicable direct overload activates opposite-operator fallback;
6. resolve the opposite operator normally, without filtering its candidates to
   Boolean results;
7. fallback is available only when that opposite selection is unambiguous and
   returns exactly `Boolean`; and
8. an explicit typed declaration may supply result context that an ordinary
   condition does not.

The following representative declarations use legacy candidate operator syntax
only to make the overload sets concrete; complete declaration grammar remains
future work:

```zax
A :: type {
  operator pre unary '?' final : (result : Boolean)() = { ... }
}

B :: type {
  operator pre unary '?' final : (result : Boolean)() = { ... }
  operator pre unary '!' final : (result : Boolean)() = { ... }
}

C :: type {
  operator pre unary '?' final : (result : Boolean)() = { ... }
  operator pre unary '?' final : (result : WhateverType)() = { ... }
  operator pre unary '!' final : (result : Boolean)() = { ... }
}

D :: type {
  operator pre unary '?' final : (result : Boolean)() = { ... }
  operator pre unary '?' final : (result : WhateverType)() = { ... }
}

E :: type {
  operator pre unary '!' final : (result : Boolean)() = { ... }
}
```

```zax
a : A
b : B
c : C
d : D
e : E

if ?a               // direct Boolean ?
  print(a)

if !a               // fallback through the unique Boolean ?: !?a
  print(a)

if ?b               // direct Boolean ?
  print(b)

if !b               // direct Boolean !
  print(b)

if ?c               // error: direct ? is ambiguous; condition cannot choose Boolean
  print(c)

if : Boolean = ?c   // explicit result selection chooses the Boolean ?
  print(c)

if !c               // direct Boolean !
  print(c)

if ?d               // error: direct ? is ambiguous
  print(d)

if : Boolean = ?d   // explicit result selection chooses the Boolean ?
  print(d)

if !d               // error: fallback cannot filter ambiguous ? candidates
  print(d)

if ?e               // fallback through the unique Boolean !: !!e
  print(e)

if !e               // direct Boolean !
  print(e)
```

These examples are required because prose alone does not expose the
ambiguous-opposite case.

### Overloadable `&&`/`||` and protected Boolean short-circuiting

`&&` and `||` are not globally Boolean-only. Operand shapes other than exact
`Boolean`/`Boolean` use ordinary overload selection and may return any type:

```zax
A :: type {
  operator binary '||' final : (result : Boolean)(rhs : A) = { ... }
  operator binary '&&' final : (result : Boolean)(rhs : A) = { ... }
}

B :: type {
  operator binary '||' final : (result : FooBarType)(rhs : B) = { ... }
  operator binary '&&' final : (result : FooBarType)(rhs : B) = { ... }
}
```

```zax
result1 := a1 || a2 || a3 // A || A, then Boolean || A; each node must resolve
result3 := b1 || b2       // eager ordinary overload; result is FooBarType
result4 := b1 && b2       // eager ordinary overload; result is FooBarType
```

The exact language-provided `Boolean || Boolean` and `Boolean && Boolean`
operations are protected:

- they evaluate left to right;
- they short-circuit runtime evaluation of the right operand;
- source must still resolve and validate the right operand at compile time; and
- a user declaration attempting to replace either exact operation is a compile
  error.

```zax
// error: the protected Boolean || Boolean operation cannot be replaced
operator binary '||' final :
  (result : Boolean)(lhs : Boolean, rhs : Boolean) = {
  return asmCompare(lhs, rhs)
}
```

For other operand shapes, both operands are evaluated left to right before the
ordinary operator call.

#### Branch-dependent short-circuiting

Conditional-expression paths may cause one `&&`/`||` node to have different
resolved behavior per path:

1. statically resolve every possible conditional arm and operator path;
2. classify each resolved operator node on each path;
3. exact Boolean/Boolean uses protected short-circuiting;
4. any other valid operand pair uses ordinary eager overload behavior;
5. preserve left-to-right runtime order and selected-arm-only ternary evaluation;
6. runtime skips a right operand only on a path classified as Boolean
   short-circuit;
7. every possible path remains subject to compile-time validation; and
8. every path must converge by the complete operation boundary.

Short-circuiting skips runtime evaluation, not compile-time resolution.
Determining the types of later expressions never requires evaluating them early
at runtime.

```zax
result2 := (?a1) || (?a2) // both sides resolve Boolean; right may short-circuit
```

```zax
result5 :=
  (?a1) ||
  (c < d ?? (?a2) ;; e) ||
  (?a3) ||
  (g < h ?? (?a3) ;; f)
```

The latter expression must be resolved as a branch graph. Some paths may use
Boolean short-circuit nodes while others may require ordinary custom overloads.
No path may escape the complete expression with an unresolved result shape.
Chained examples rely on the aligned left-to-right progression demonstrated
above; complete operator precedence remains future operator work.

### Optional proof cannot assume arbitrary `&&`

This is not a universally safe proof example:

```zax
if ?optionalValue && ?optionalValue.
  use(optionalValue.)
```

It short-circuits only when the right expression resolves unambiguously to
`Boolean`. If `?optionalValue.` resolves to another type, the node is either an
ordinary eager `Boolean && WhateverType` overload or an error. If direct
selection is ambiguous, compilation fails. In no non-short-circuit case may the
left presence test prove that the dereference is skipped.

The general presence-proof example is:

```zax
if ?optionalValue
  use(optionalValue.)
```

When the underlying stored value's `?` operation is also needed, source can make
the Boolean result explicit:

```zax
if ?optionalValue && (: Boolean = ?optionalValue.)
  use(optionalValue.)
```

The typed right operand makes Boolean short-circuit eligibility and result
selection explicit.

### Required language support versus an optional system library

Zax does not require one conventional system or standard library, but an
implementation cannot omit behavior required by the language.

Distinguish:

- optional library APIs such as collections, I/O, networking, and frameworks;
- required language support for fundamental types, protected primitive
  operations, and runtime/lifetime behavior used by source.

A required operation may be lowered to instructions, constant-folded, emitted
as target code, treated as an intrinsic, or supplied through an automatically
linked helper. Those are implementation choices. The programmer-visible
guarantee is that the operation and semantics are available.

A compiler should reject a conflicting user declaration. Failure to provide a
required helper is a broken or incomplete toolchain, not the intended source
diagnostic. This finding affects operator boundaries, foundational
compiler/library separation, and future build/toolchain input.

### Source structure corrections and required examples

#### Comment trivia during composition

Comments may occur while `;` is waiting for its right operand:

```zax
if condition
  doA(); // `;` establishes that another operand follows.
  // Comment trivia may occur before that operand.
  doB()
```

No `\` is needed. The source processor must preserve comment and physical-line
trivia long enough to validate composition, blank-line boundaries,
documentation attachment, and layout intent. It need not expose comments as
ordinary semantic tokens, but it cannot erase the relevant positions before
those checks.

#### Remove the orphaned `;;` statement

The sentence "`;;` is a distinct header token rather than doubled composition"
became an orphan after its counterexample was removed from the semicolon section.
The later header-separator section already teaches `;;` positively with examples;
the orphan sentence should be removed.

#### Structural operands versus expression continuation

New statement operands remain at one exact structural level:

```zax
if true
  a();
  b();
  c()
```

Progressively indented sibling operands are errors:

```zax
if true
  a();
    b(); // error: composed operands must share one structural level
      c()
```

A body beginning four spaces deeper is also an error:

```zax
if true
    a(); // error: the body must begin exactly two spaces deeper
    b();
    c()
```

Deeper alignment remains legal only for tokens continuing the same expression
after continuation is established:

```zax
if true
  a := 1 + 2 + 3 + 4 \
    + 5 + 6;
  b := 1 + 2 + \
       3 + 4;
  calc(a, b)
```

The lasting rule is:

> Parsed continuation may use deeper alignment for tokens that continue the same
> expression. It does not relax exact structural indentation for a new body
> statement, composed operand, header section, or sibling declaration.

### `else` attachment, whitespace, and layout

#### Unbraced clauses

```zax
if condition
  trueBody()
else
  falseBody()
```

- `else` aligns exactly with its owning `if`;
- it follows the complete true body without a physically blank line; and
- its unbraced body begins exactly two spaces deeper.

#### Braced clauses

```zax
if condition {
  trueBody()
} else {
  falseBody()
}
```

- `else` shares the physical line with the preceding `}`;
- whitespace separates `}` and `else`;
- a braced `else` body opens on the `else` line; and
- the closing brace aligns with the owning `if`.

A newline between `}` and `else` is a deliberate attachment-intent error:

```zax
if condition {
  trueBody()
}
else { // error: else is visually detached from the completed if
  falseBody()
}
```

#### Chained clauses

```zax
if firstCondition
  firstBody()
else if secondCondition
  secondBody()
else
  finalBody()
```

`else if` is an `else` whose effective body is another `if`. `else` and `if` are
separated by whitespace, every clause aligns with the owning `if`, and one flow
label names the complete chain rather than an individual `else`.

Comment trivia may be preserved by the generalized lexical mechanism; final
teaching must include representative comment placement without weakening the
no-blank-line attachment rule.

### Core-flow teaching corrections

#### Opening example prerequisites

The complex label/initializer/post/target example appears before those concepts
are taught and is repeated later in the appropriate section. The opening mental
model should use a smaller example whose untaught pieces can be safely treated
as placeholders. The complex example belongs only after labels and post
operations are introduced.

#### Invalid or surprising examples must say so inline

This source is not inherently condition-plus-post:

```zax
if condition ;; performCleanup() // error if performCleanup does not return Boolean
  body()
```

One `;;` means initializer plus condition. Even if `performCleanup()` returned
Boolean and made the source valid, it would still not mean condition plus post.
The intended no-initializer condition-plus-post form is:

```zax
if ;; condition ;; postOperation()
  body()
```

Examples with a known compile error must mark the error in code rather than rely
only on prose below.

#### Post operation wording

The positive rule is sufficient:

> A post operation runs after the selected body completes normally.

Vague wording about initializer or condition evaluation "failing to complete
normally" should be removed unless a concrete legal transfer requires teaching.
Panic is fatal rather than an ordinary alternative flow path.

#### Plain labels before `shadowable`

Label-placement examples should first show ordinary labels:

```zax
if check_resource: condition
while outer: condition
redo while retry: condition
scope process_work: {
  body()
}
```

Introduce `shadowable` only in the nested label-reuse section so examples do not
make the optional permission look mandatory.

### Conditional expression producing an optional

The conditional expression may converge through a typed optional destination:

```zax
myType : MyType? = (a < b ?? : MyType = a ;; : MyType?)

if ?myType
  use(myType.)
```

On the true path, the anonymous `MyType` is constructed from `a` and then
constructs the destination optional. On the false path, the anonymous
type-default `MyType?` supplies an empty optional. Both paths converge to the
single destination type `MyType?`.

The spelling uses the current anonymous typed-declaration form and adjacent
optional type use `MyType?`. Complete optional construction/reset design remains
future optional work. Preserve this example in any raw deferral because code
disambiguates the intended behavior more reliably than prose alone.

### Static-analysis contract and safety philosophy

For every property mandatory under the selected language contract, Zax attempts
to prove the operation valid. If proof cannot be established, the operation is
rejected unless a narrow semantic assertion explicitly accepts responsibility
for an unproved but valid boundary. An assertion cannot make a known violation
valid.

Redundant-control validity follows the selected contract:

1. if the contract does not require proof of that case, implementation improvement
   may make an assertion redundant but at most advisory;
2. if the selected contract requires that case to be recognized as safe, the
   redundant assertion may be a hard error;
3. a compiler that requires an assertion for a contract-mandated passing case is
   nonconforming; and
4. source adopting a later contract that newly mandates the proof may need to
   remove a control valid under an earlier contract.

Source validity follows the selected contract, not merely the executable
compiler's current cleverness. This generalized correction applies to all static
analysis, not only optional presence or one lifecycle feature.

### Cross-cutting audit purpose and workflow

The cross-cutting audit is a durable queue of repository-wide verification
obligations that cannot be completed responsibly within one local concept
review. It exists so those obligations survive numbered-work archival and are
eventually tested against every affected owner.

A future agent using it should:

1. read only entries activated by the assigned task;
2. inspect the owners and evidence named by the entry;
3. determine whether the concern remains valid under current design;
4. identify every current or legacy surface affected;
5. discuss and obtain authorization for edits;
6. apply corrections to the real owners rather than treating raw input as
   authority;
7. record evidence that the concern was resolved, narrowed, superseded, or
   remains open; and
8. remove a resolved entry.

Add an entry only when a concrete concern crosses owners, local review cannot
prove repository-wide consistency, representative examples exist, and resolution
and activation pressure can be stated. Do not use the file for ordinary local
TODOs, speculation, or unresolved design that belongs in numbered work.

Add an **example prerequisite and placement audit** entry:

- verify each current owner top to bottom as a new programmer would encounter it;
- confirm examples are understandable where placed;
- ensure untaught concepts can be safely treated as placeholders;
- ensure explanations do not depend on concepts introduced only later; and
- require critical diagnostics and costs to appear in representative examples.

### Examples and raw syntax as documentation obligations

Examples should be as short as possible, but no shorter than needed to preserve
the behavior being taught. Because readers often scan examples before prose,
critical teaching must appear in source and comments as well as surrounding
explanation.

When a compile error is known, the sample must contain an inline `// error` or
equally clear comment. Prose may expand the diagnosis but must not be the only
indication that the source is invalid.

When deferred raw material involves source syntax or programmer-visible
behavior, preserve representative valid examples, invalid counterexamples, and
comments explaining the deciding distinction. Prose alone is insufficient when
code can disambiguate the intended meaning. A raw input may omit examples only
when no useful source form exists yet, and should say so explicitly.

### Contextual keyword recognition

The aligned source rule is:

> A spelling has keyword status only where that keyword's construct is
> grammatically permitted. Outside those positions, that spelling is not a
> keyword.

```zax
next          // transfer keyword
next outer:   // transfer keyword with a target
next()        // next has no keyword status here

continue          // transfer keyword
continue outer:   // transfer keyword with a target
continue()        // continue has no keyword status here
```

The general rule belongs in current source structure. Local concept owners may
show a likely-confusion example but should link rather than redefine it.

### Current documents must not cite numbered work

Promoted programmer-facing owners must not cite numbered work or eventual
archives for provenance or meaning. They should cite current conceptual
predecessors or live legacy evidence. Numbered-work provenance belongs in project
records.

The staged promotion violates this in both new owners, and older current owners
contain similar work-item references. The corrective dry run must identify every
current occurrence and either remove it in scope or preserve it as a live
cross-cutting consistency repair. Raw and project records may retain numbered
provenance.

### Consequences for the next dry run

The next documentation-fit dry run must remap at least:

- construction/destruction ownership around conditionally live storage;
- cumulative operator-owner scope;
- direct/fallback `?`/`!` examples and diagnostics;
- arbitrary `&&`/`||` overloads, protected Boolean operations, branch-specific
  short-circuiting, associativity pressure, and optional proof examples;
- required language support versus optional library/runtime facilities;
- source continuation, comments, `else`, contextual keywords, and new
  counterexamples;
- static-analysis contract rules;
- example and raw-syntax documentation principles;
- cross-cutting audit purpose and entries;
- removal of numbered-work references from current owners;
- opening-example prerequisite order; and
- the optional-producing ternary example.

No corrective promotion may begin until that new dry run is discussed, aligned,
and explicitly authorized.

## Second documentation-fit dry run

| Field | Value |
| --- | --- |
| Status | Completed read-only corrective documentation-fit analysis / non-authoritative plan |
| Completed | 2026-08-26 |
| Input | The aligned post-promotion staged-review findings above |
| Outcome | Passed: every correction fits an existing current owner, legacy route, or indexed raw input |
| Failure criterion | A new unaligned semantic or behavioral question |
| New unaligned issues discovered | None |
| Corrective-promotion authorization | Not granted by this dry run |

### Second dry-run conclusion

The new findings do not require another current owner, another raw file, a file
move, or a new documentation directory. They refine the boundaries of the
already-created core-flow and operators owners and extend existing source,
lifetime, terminology, foundational, analysis, safety, build, documentation, and
audit surfaces.

The corrective promotion is broader than the first promotion because several
findings are reusable language or project principles:

- protected Boolean operations expose required language/toolchain support;
- contextual keyword recognition is a general source rule;
- static proof/assertion behavior is contract-wide;
- current owners must not cite numbered work; and
- examples and raw syntax have repository-wide teaching obligations.

No new language-design decision is needed before discussing the corrective
change set.

### Revised ownership map

| Finding | Lasting owner | Supporting boundary |
| --- | --- | --- |
| Conditionally live storage and proof before access | `language/construction-and-destruction.md` | Optional dereference is one application; operator and optional syntax remain elsewhere |
| Direct `?`/`!` selection, ambiguous direct sets, opposite-operator fallback, and explicit typed result selection | `language/operators.md` | `language/function-invocation.md` retains shared expected-result and overload-selection mechanics |
| Arbitrary eager `&&`/`\|\|` overloads and protected Boolean/Boolean short-circuit operations | `language/operators.md` | `language/core-flow-control.md` states condition-facing effects and proof ordering |
| Branch-path-specific eager versus short-circuit behavior and convergence | `language/operators.md` for operator-node behavior; `language/core-flow-control.md` for selected-arm order and convergence | `language/function-invocation.md` owns branch-specific callable selection |
| Optional presence proof examples | `language/core-flow-control.md` and `language/construction-and-destruction.md` | Do not rely on arbitrary underlying-value `?` results |
| Required language support versus optional library APIs | `language/vision.md` for foundational promise; `language/operators.md` for protected operations | `project/raw/build-and-dependencies.md` preserves toolchain/helper/linkage pressure |
| Comment trivia retained through composition/layout validation | `language/source-structure.md` | Exact lexer/parser representation remains implementation work |
| Structural operand alignment, expression-continuation exception, and requested counterexamples | `language/source-structure.md` | No flow-semantic duplication |
| `else` attachment, indentation, braces, whitespace, and chain layout | Behavior in `language/core-flow-control.md`; source validity in `language/source-structure.md` | Comment-trivia examples must preserve the no-blank-line rule |
| Contextual keyword recognition | `language/source-structure.md` with concise terminology in `language/terms.md` | Root `basics.md` becomes legacy evidence and route |
| Optional-producing conditional expression | `language/core-flow-control.md` | `optional.md` links the accepted convergence example while retaining broader legacy optional behavior |
| Contract-dependent proof assertions and redundant-control severity | `project/raw/analysis-controls.md` | `project/raw/safety.md` preserves the broader prove-or-assert philosophy |
| Example brevity, prerequisite order, inline error comments, and raw syntax examples | `project/documentation.md` | `project/raw/cross-cutting-audit.md` owns live repository-wide verification entries |
| Cross-cutting audit purpose and future-agent workflow | `project/raw/cross-cutting-audit.md` | Raw index remains only a router |
| No numbered-work citations in current programmer owners | `project/documentation.md` | Current-owner metadata is repaired; project/raw records retain provenance |

### Existing structure remains sufficient

#### `language/operators.md`

Retain one bounded but cumulative operators owner. Its scope is accepted operator
semantics, not merely material needed outside flow. Expand it with:

- ordinary direct selection and ambiguity;
- opposite-operator fallback without Boolean result filtering;
- the representative A–E selection examples;
- arbitrary eager `&&`/`||` overloads;
- protected Boolean/Boolean operations that users cannot replace;
- branch-path-specific eager versus short-circuit behavior;
- compile-time validation versus runtime skipping;
- required language support and cost/source-stability consequences; and
- explicit boundaries around still-unreviewed declaration, lookup, ranking,
  precedence, and generation.

No separate Boolean-operator, short-circuit, or built-in-support owner is needed.

#### `language/construction-and-destruction.md`

Rename and generalize the optional-specific proof section to conditionally live
storage and access proof. Construction/destruction remains the correct owner
because the concern is whether a value lifetime exists, not how optional syntax
is spelled. Remove optional-specific ownership from metadata while retaining an
optional example and links to operators/core flow.

No new optional current owner is justified by this correction.

#### Source, core flow, and terms

`language/source-structure.md` can absorb contextual keywords, comment-trivia
retention, the continuation examples, `else` source layout, and removal of the
orphaned `;;` sentence without becoming a parser specification.

`language/core-flow-control.md` can absorb `else` clause behavior, protected
Boolean proof ordering, safer optional examples, the optional-producing ternary,
and teaching-order corrections without splitting.

`language/terms.md` can define contextual keyword and keep behavior in source
structure.

### Corrective updates by file

#### Current language owners

1. **`language/operators.md`**
   - Rewrite the mental model as a cumulative current operator owner.
   - Replace Boolean-only `&&`/`||` claims with ordinary arbitrary overloads plus
     protected exact Boolean/Boolean operations.
   - Add direct/ambiguous/fallback result-selection rules and A–E examples.
   - Add branch-path-specific short-circuit behavior and protected-operation
     diagnostics.
   - State that conflicting Boolean/Boolean declarations are compile errors.
   - Remove numbered-work provenance and references.

2. **`language/core-flow-control.md`**
   - Replace the too-advanced opening example with a smaller prerequisite-safe
     example and keep the complex example only in its later section.
   - Replace the generic optional `&&` proof with simple presence proof and,
     where needed, explicitly typed Boolean right operands.
   - Teach ordinary labels before optional `shadowable`.
   - Add comprehensive `else` behavior and link source layout.
   - Mark the one-`;;` likely-intent example inline and explain that it can still
     be technically valid when the second section returns Boolean.
   - Remove vague initializer/condition non-completion wording.
   - Add the optional-producing conditional-expression example.
   - Summarize arbitrary/eager versus protected/short-circuit operator behavior.
   - Remove numbered-work provenance.

3. **`language/source-structure.md`**
   - Remove the orphaned `;;` sentence from semicolon composition.
   - State that comments/trivia and physical-line boundaries must survive long
     enough for composition and layout validation.
   - Add the aligned legal, progressively indented error, four-space body error,
     and expression-continuation-with-composition examples.
   - Refine continuation wording to distinguish expression tokens from new
     statement/header siblings.
   - Add complete `else` attachment/layout rules and counterexamples.
   - Add contextual keyword recognition and link terminology.

4. **`language/construction-and-destruction.md`**
   - Generalize metadata and heading from optional-specific proof to conditionally
     live storage and access proof.
   - Retain optional dereference as one application.
   - Replace any unsafe generic `&&` proof example with direct presence proof.

5. **`language/function-invocation.md`**
   - Remove numbered-work provenance.
   - Clarify, where cross-linking requires it, that explicit anonymous typed
     declarations can provide operator-result context while ordinary conditions
     cannot.
   - Link branch-specific operator-node behavior without duplicating it.

6. **`language/terms.md`**
   - Add a concise contextual-keyword entry.
   - Preserve the relative normal-completion clarification already promoted.

7. **`language/vision.md`**
   - Clarify that rejecting one mandatory conventional system library does not
     remove required language support for fundamental types, protected
     operations, or used runtime/lifetime features.
   - Keep exact lowering and helper linkage outside the language promise.

8. **`language/principles.md`**
   - Remove numbered-work provenance from current programmer-facing metadata.
   - Do not promote the static-analysis philosophy here before focused
     safety/analysis work decides whether it is a reusable accepted principle.

#### Current navigation and legacy routes

9. **`index.md`**
   - Update the operators route to describe arbitrary overloads and protected
     Boolean short-circuit behavior accurately.

10. **`operator.md`**
    - Update its current-owner route for arbitrary `&&`/`||` overloads,
      protected Boolean operations, and direct/fallback selection.
    - Retain broader declaration/lookup material as legacy.

11. **`optional.md`**
    - Update the generalized live-storage-proof anchor.
    - Link the accepted optional-producing conditional-expression example
      without making the legacy optional page authoritative for conditional
      expressions.

12. **`basics.md`**
    - Route legacy keyword-disambiguation evidence to the current contextual
      keyword rule.
    - Retain unrelated legacy parsing/keyword material as non-authoritative.

#### Project guidance and raw inputs

13. **`project/documentation.md`**
    - Strengthen example guidance: shortest sufficient examples, prerequisite
      placement, critical comments, and inline `// error` when failure is known.
    - Require syntax-bearing raw inputs to preserve representative valid and
      invalid examples when useful source forms exist.
    - State that current programmer owners do not cite numbered work or archived
      project records for meaning or provenance.

14. **`project/raw/analysis-controls.md`**
    - Replace the absolute unchanged-contract advisory rule with the aligned
      contract-dependent matrix.
    - State the prove-or-narrowly-assert mental model for analyses mandated by the
      selected contract.

15. **`project/raw/safety.md`**
    - Preserve the generalized contract-qualified philosophy: prove required safe
      behavior or require a narrow semantic assertion; assertions cannot make
      known violations valid.

16. **`project/raw/build-and-dependencies.md`**
    - Add required language/toolchain support, protected primitive operations,
      automatic helpers/linkage, and incomplete-toolchain pressure.
    - Keep exact compiler/runtime architecture undecided.

17. **`project/raw/cross-cutting-audit.md`**
    - Add the durable-queue purpose, future-agent workflow, add/do-not-add rules,
      and success/retirement model.
    - Add example prerequisite/placement, current-owner numbered-provenance, and
      protected-operator/toolchain consistency entries.
    - Enrich existing operator/convergence entries with path-specific
      short-circuit behavior and concrete examples.

18. **`project/raw/feature-catalog.md`**
    - Correct the operator disposition so it no longer implies globally
      Boolean-only `&&`/`||`.
    - Route library/compiler separation and required support pressure to the
      current vision plus build raw input.

19. **`project/raw/README.md`**
    - Expand the build/dependencies activation description to include required
      language support and automatic toolchain/helper linkage.

#### Active record

20. **`project/work/007-core-flow-control.md`**
    - Record the authorized corrective-promotion result and actual dispositions.
    - Keep closure, archival, and work item `008` separate.

### Exact corrective-promotion change set

The second dry run proposes modifying exactly these 20 existing files:

1. `basics.md`
2. `index.md`
3. `language/construction-and-destruction.md`
4. `language/core-flow-control.md`
5. `language/function-invocation.md`
6. `language/operators.md`
7. `language/principles.md`
8. `language/source-structure.md`
9. `language/terms.md`
10. `language/vision.md`
11. `operator.md`
12. `optional.md`
13. `project/documentation.md`
14. `project/raw/README.md`
15. `project/raw/analysis-controls.md`
16. `project/raw/build-and-dependencies.md`
17. `project/raw/cross-cutting-audit.md`
18. `project/raw/feature-catalog.md`
19. `project/raw/safety.md`
20. `project/work/007-core-flow-control.md`

No file is created, moved, retired, archived, or deleted by the corrective
promotion. Other staged promotion files remain unchanged unless applying the
authorized correction exposes a direct contradiction, which would require new
discussion and authorization.

### Corrective-promotion validation plan

Validate that:

- operator examples cover direct selection, direct ambiguity, typed result
  selection, valid fallback, and ambiguous opposite fallback;
- `&&`/`||` are arbitrary ordinary overloads except protected exact
  Boolean/Boolean operations;
- protected Boolean operations cannot be user-replaced and short-circuit
  left-to-right;
- every branch path is statically valid while runtime skips only operands on a
  short-circuiting selected path;
- optional proof examples do not depend on an arbitrary non-Boolean underlying
  `?`;
- construction/destruction owns the general conditional-lifetime proof rather
  than optional syntax;
- comments and blank physical lines behave consistently during vertical
  composition and `else` attachment;
- structural operands remain exactly aligned while expression continuation may
  align deeper;
- requested legal/error examples carry inline comments;
- `else` examples cover unbraced, braced, chained, detached, and misaligned
  forms;
- contextual keyword wording says the spelling is not a keyword outside legal
  keyword positions;
- required language support is distinguished from an optional system library
  without promising a lowering;
- contract-selected proof and redundant-control severity agree across analysis
  and safety raw inputs;
- cross-cutting audit purpose and future-agent workflow are actionable;
- syntax-bearing raw concerns preserve concrete examples;
- no current programmer owner cites a numbered work item or archive;
- the optional-producing conditional expression converges to `MyType?`;
- current/public owners do not route ordinary readers into project raw material;
- all local links and heading anchors resolve;
- staged and unstaged review boundaries are preserved; and
- the final changed-file set matches the authorized 20-file corrective scope.

### Second dry-run pass/fail result

**Passed.** The dry run discovered no new unaligned semantic or behavioral issue.
Every second-round finding has a coherent destination and a bounded corrective
change. The broader file set reflects already-aligned cross-cutting consequences,
not new language design.

Corrective promotion still requires discussion and explicit authorization of
this exact 20-file plan. This dry run does not authorize edits outside this
working record.

## Corrective promotion result

| Field | Value |
| --- | --- |
| Status | Authorized corrective promotion applied / working record still active and non-normative |
| Applied | 2026-08-26 |
| Authorization | Explicit maintainer authorization for the exact 20-file second dry-run change set |
| Scope | Exactly the 20 existing files listed in the second dry run |
| Files created, moved, retired, or deleted | None |
| Review boundary | The prior 23-file promotion remains staged and unchanged; every corrective edit is unstaged |
| Not performed | Staging, committing, pushing, branch changes, closure, archival, and work item `008` |

The exact 20-file corrective plan was authorized and applied as one corrective
promotion. This section records the actual dispositions. It does not close this
work item, archive it, mark it historical, or open work item `008`; those remain
separate authorized actions.

### Applied corrections by file

#### Current language owners

1. `language/operators.md` — reframed the mental model as a cumulative current
   operator owner rather than a flow remainder, and stated that deferred material
   is deferred because it is unreviewed. Replaced the direct/fallback section with
   the eight ordinary-selection rules, the representative A–E overload sets, the
   full selection example including `if : Boolean = ?c` typed selection, ambiguous
   direct sets as errors, and ambiguous-opposite fallback failure. Replaced the
   Boolean-only `&&`/`||` claim with ordinary eager arbitrary-result overloads
   plus the protected exact `Boolean`/`Boolean` operations, their left-to-right
   short-circuit, the compile-time validity of a skipped right operand, and the
   compile error for a replacing declaration. Added branch-dependent
   classification with the eight-step model and both chained examples. Rewrote the
   optional-presence section around `if ?optionalValue` with the typed
   `(: Boolean = ?optionalValue.)` right operand. Added required language support
   versus an optional system library without promising a lowering. Extended costs,
   diagnostics, source stability, and boundaries accordingly, and removed all
   numbered-work provenance and references.

2. `language/core-flow-control.md` — replaced the opening complex labeled example
   with prerequisite-safe `if`, `if`/`else`, `while`, and header-section examples,
   keeping the complex example only in the `if`/`else` section that owns it.
   Rewrote truth-producing operations for direct selection, direct ambiguity,
   restricted fallback, typed result context, eager `&&`/`||`, and protected
   short-circuiting. Replaced the generic `&&` optional proof with
   `if ?optionalValue` plus the explicitly typed compound form and an inline error
   counterexample. Marked the one-`;;` example inline and explained that a
   `Boolean`-returning second section is still a condition, not a post operation.
   Removed the vague initializer/condition non-completion wording in favour of the
   positive post-operation rule and fatal panic. Added comprehensive `else` clause
   forms, chain behavior, and a link to source layout. Showed plain labels before
   `shadowable`. Added the optional-producing conditional-expression example.
   Generalized analysis and diagnostics to conditionally live storage, contract
   selection, and per-path validity, and removed numbered-work provenance.

3. `language/source-structure.md` — removed the orphaned `;;` sentence from
   semicolon composition. Stated that comment and physical-line trivia must
   survive long enough for composition, blank-line, attachment, and layout
   validation. Added the aligned legal example, the progressively indented sibling
   error, the four-space body error, and the legal
   expression-continuation-with-composition example, each with inline comments,
   plus the lasting continuation rule distinguishing expression tokens from new
   statement, operand, header, or sibling starts. Added a complete `else`
   attachment and layout section covering unbraced, braced same-line `} else {`,
   chained, detached-newline, blank-line, misaligned, and comment-trivia forms.
   Added contextual keyword recognition with the `next`/`continue` examples.
   Updated metadata and the layout diagnostic list.

4. `language/construction-and-destruction.md` — renamed and generalized the
   optional-specific proof section to conditionally live storage and access proof,
   with the general rule stated first and optional dereference retained as one
   application. Kept `if ?optionalValue` as the proof example, warned that a `&&`
   proof requires an unambiguously exact `Boolean` right operand, and pointed at
   the operators owner. Generalized the metadata, the diagnostics entry, and the
   preserved-behavior list, and moved contract-dependent severity language to the
   analysis owner.

5. `language/function-invocation.md` — removed numbered-work provenance. Stated
   that a flow condition is not a narrow expected-result context and that an
   anonymous typed declaration supplies the context a condition does not, with the
   `if : Boolean = ?value` example and a link to operator selection. Linked
   per-path operator-node classification without duplicating it.

6. `language/terms.md` — added a concise contextual-keyword entry pointing at
   source structure. The relative normal-completion clarification is unchanged.

7. `language/vision.md` — added a required-language-support subsection under the
   non-goals clarifying that rejecting one mandatory conventional system library
   does not remove required support for fundamental types, protected operations,
   or used runtime/lifetime behavior, and that no lowering or helper linkage is
   promised. Qualified the build-model separability goal accordingly.

8. `language/principles.md` — removed numbered-work provenance from current
   programmer-facing metadata. The static-analysis philosophy was deliberately not
   promoted here.

#### Current navigation and legacy routes

9. `index.md` — corrected the operators route to describe arbitrary results,
   direct `?`/`!` selection with opposite-operator fallback, ordinary eager
   `&&`/`||` overloads, and the protected `Boolean` short-circuit operations.

10. `operator.md` — updated its current-owner route for direct selection,
    ambiguity, restricted fallback, typed result context, eager `&&`/`||`
    overloads, and protected `Boolean` operations, retaining broader declaration,
    lookup, ranking, precedence, literal, and word-operator material as legacy.

11. `optional.md` — updated both references to the generalized live-storage-proof
    anchor and linked the accepted optional-producing conditional-expression
    example while stating that this page is not authoritative for conditional
    expressions.

12. `basics.md` — routed legacy keyword-disambiguation evidence to the current
    contextual keyword rule and term, retaining the underscore convention and
    unrelated parsing/keyword material as non-authoritative legacy input.

#### Project guidance and raw inputs

13. `project/documentation.md` — added example obligations (shortest sufficient
    examples, prerequisite placement, inline `// error` comments, critical
    diagnostics and costs in code, complex examples in their owning section) and a
    matching obligation for syntax-bearing raw material. Added the rule that
    current programmer owners do not cite numbered work or archived records for
    provenance or meaning and do not route ordinary readers into `project/raw/`.

14. `project/raw/analysis-controls.md` — replaced the absolute unchanged-contract
    advisory rule with the contract-dependent matrix, added the
    prove-or-narrowly-assert model for contract-mandated analyses, stated that an
    assertion cannot make a known violation valid, generalized the rule beyond
    optional presence, and preserved concrete example shapes. Updated the
    flow-derived presence entry for conditionally live storage and `&&` proof
    eligibility.

15. `project/raw/safety.md` — preserved the generalized contract-qualified
    prove-or-narrowly-assert philosophy, its two lasting consequences, and the
    redundant-control severity summary, linking the detailed matrix in the
    analysis input.

16. `project/raw/build-and-dependencies.md` — added required language support and
    toolchain completeness, protected primitive operations with the replacing
    declaration counterexample, automatic helper linkage as an implementation
    choice, incomplete-toolchain pressure, and the future questions this raises,
    while explicitly leaving compiler and runtime architecture undecided.

17. `project/raw/cross-cutting-audit.md` — added the durable-queue purpose, the
    eight-step future-agent workflow, add and do-not-add criteria, and the
    retirement model. Enriched the ternary-convergence entry with the optional
    destination form and the operators owner, and the branch-cost entry with
    per-path protected-versus-eager classification and a concrete example. Added
    example-prerequisite-and-placement, current-owner numbered-provenance, and
    protected-operator/required-support entries. Noted the globally-Boolean
    `&&`/`||` claim in the contradicted-legacy entry.

18. `project/raw/feature-catalog.md` — corrected the operator disposition so it no
    longer implies globally Boolean-only `&&`/`||`, and added a library/compiler
    separation disposition routing required-support pressure to the current vision,
    the operators owner, and the build raw input.

19. `project/raw/README.md` — expanded the build/dependencies activation
    description to include required language support and automatic
    toolchain/helper linkage.

#### Active record

20. `project/work/007-core-flow-control.md` — this section.

### Corrective-promotion validation

Validated after applying the corrective promotion:

- exactly the authorized 20 files have unstaged changes, and the previously
  staged 23-file set is unchanged;
- no file was created, moved, retired, archived, or deleted;
- `git diff --check` and `git diff --cached --check` report nothing;
- no changed file introduces a tab character or a malformed fence;
- every local Markdown link and heading anchor in the changed files resolves,
  including the renamed conditionally-live-storage anchor;
- no current programmer-facing owner cites a numbered work item or an archive;
- no current or public owner routes ordinary readers into `project/raw/`;
- operator prose and examples cover direct selection, direct ambiguity, typed
  result selection, valid fallback, ambiguous-opposite fallback failure, eager
  `&&`/`||`, and protected `Boolean` operations consistently;
- required language support is distinguished from an optional system library
  without promising any lowering;
- source examples carry inline error comments and use exact two-space structural
  levels except where an example is a deliberate invalid counterexample; and
- `else` examples cover unbraced, braced, chained, detached, blank-line,
  misaligned, and comment-trivia forms.

### Still deliberately outside this corrective promotion

This work item remains active. The following are separate actions requiring
separate authorization and are not performed here:

- staging, committing, pushing, or any index or history change;
- closing this work item or marking it historical;
- archiving this file to `project/archive/work/`; and
- opening work item `008`.

## Operator-owner scope correction

| Field | Value |
| --- | --- |
| Status | Authorized ownership correction applied / working record still active and non-normative |
| Applied | 2026-08-26 |
| Authorization | Explicit maintainer authorization after review of the staged corrective promotion |
| Scope | `language/operators.md` and this working record |
| Not performed | Staging, committing, pushing, closure, archival, or work item `008` |

The staged operators owner correctly states that exact `Boolean && Boolean` and
`Boolean || Boolean` operations are language-provided and cannot be replaced,
but its "Required language support and an optional system library" section
explained the broader compiler/library/toolchain boundary in unnecessary detail.

The correction:

- removes general required-language-support ownership from the operators metadata;
- renames and narrows the section to **Language-provided protected operations**;
- retains only the local guarantee that exact Boolean operations are protected
  and available without an optional library API;
- links the broader distinction to the language vision;
- leaves compiler, linker, runtime, helper, and lowering behavior outside operator
  semantics;
- removes the broad helper/lowering cost bullet; and
- narrows source stability to the fact that another source declaration cannot
  replace the protected operation.

The broader explanation remains with the language vision, and future
toolchain/linkage questions remain project raw input. This is an ownership and
teaching correction, not a new semantic finding.

## Closure result

| Field | Value |
| --- | --- |
| Status | Work item 007 closed and archived |
| Closed | 2026-08-26 |
| Promotion | Core flow control, bounded operator semantics, affected current owners, project guidance, raw inputs, navigation, and legacy dispositions promoted and validated |
| Continuing evidence | Indexed raw selection, analysis, safety, build/toolchain, lifetime, and cross-cutting-audit inputs plus retained legacy remainders |
| Successor | `project/work/008-operator-catalog-and-resolution.md` |

Every material 007 finding received a current owner, live raw destination,
explicit deferral, correction, or rejection before closure. Current documents do
not depend on this archive for meaning.

Work item 008 was created from separately aligned initiating input and reading
scope. Its creating agent stopped before analysis, as required.
