# 032: Construction with `.=`

| Field | Value |
| --- | --- |
| Status | Historical working record / non-normative / audit-only |
| Work Item | `032` |
| Created | 2026-09-24 |
| Completed | 2026-09-25 |
| Owns | Historical evidence and dispositions from the completed bounded review |
| Does Not Own | Current Zax language design; see the promoted declarations, construction, invocation, operator-catalog, structural, source-structure, qualifier, terms, and safety owners |

## Non-authority notice

This file is a historical audit record. It is non-authoritative and excluded
from ordinary onboarding. Current language meaning lives in the promoted
`language/` owners rather than this discussion history. Mentions of
`unsafe ???` and `replacement +++` describe forms that no longer exist.

## Fixed initiating input

This section records the aligned information known when work item `032` was
created. It is intentionally incomplete and must not be rewritten as work
develops.

### Initiating concern

Should `.=` mean **construction and replacement construction** at every
construction site, rather than only for function result slots?

Current teaching already constructs result slots with `.=`:

```zax
Celsius :: type {
  degrees : Binary64

  operator binary 'as' final : (
    result : DestinationType
  )(
    DestinationType : type
  ) readonly = {
    result .= [{
      .degrees = degrees * 9.0 / 5.0 + 32.0
    }]
  }
}
```

That is accepted, promoted design. It must be understood as real design
pressure, not a local curiosity. Promoted documentation now teaches readers
that `.=` constructs a result, so they will reasonably ask why other
construction sites use `=`:

```zax
// Illustrative only: would declarations construct with `.=`?
myType : MyType .= value
```

### Motivating pressure

- Ordinary `=` never constructs: `result.degrees = x` before construction is an
  error.
- A result initializer such as `= :` is part of the **prototype**. It obligates
  every caller and every other implementation of that prototype to
  pre-construct the result, so it is the wrong tool for a need local to one
  body.
- `result.+++()` is correct but verbose.
- `.=` already makes a place hold a newly constructed value. It replaces a live
  place, constructs an optional or variant payload, and now performs first
  construction of a result slot. It could become the single visible spelling
  for "construct here".

### Starting constraints

These are accepted today:

- ordinary `=` never constructs;
- first-construction `.=` reads exactly like a declaration initializer (`x .= src`
  means what `x : T = src` means, including `[{}]` constructing a present
  optional);
- `.=` replacement permissions apply only to live places, so first construction
  of a `final` result needs none;
- a path either completes a result itself or returns a value, never both;
- `.=` produces access to the newly constructed value.

### Required output: implications with representative examples

The maintainer will decide. The review must **not** deliver only its own
judgment. It must:

1. **Show the implications of moving construction from `=` to `.=`.** For each
   class of construction scenario that would change, give one representative
   before-and-after example: a representative class, not every example in the
   documentation. The classes include at least:
   - declarations and inferred declarations;
   - constructor members (`_.member .= [{ ... }]` as the readable form of
     `_.member.+++(...)`);
   - result initializers in prototypes;
   - multiple results and result mappings or routing;
   - construction packets;
   - optional and variant payloads;
   - allocation initializers;
   - captures;
   - any other construction site the review finds.
2. **Show what does not change**, where confusion is likely: ordinary
   assignment, in-lifetime mutation, and replacement.
3. **Trace the consequences** for teaching, diagnostics, source stability, and
   existing examples.
4. **Give the agent's opinion** with its reasoning, including narrower
   alternatives, such as constructor members only, or results and members but
   not declarations, and any other ideas that emerge.

The maintainer can then judge from both the opinion and the examples whether to
adopt a broad change, a narrower change, another idea, or no change, leaving
`.=` local to results and replacement.

### Initial stopping guidance

Creating and routing this work item does not authorize analysis. A later
assignment begins it.

Do not promote findings, change current owners, archive this work item, or
create work item `033` without the separately required discussion, alignment,
and authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  authority, promotion, deferral, and closure.
- [Zax function invocation](../../language/function-invocation.md), the result
  slots and opt-in result initialization sections - the current `.=`
  first-construction rule and the prototype obligation of `= :`.
- [Zax construction, replacement, and destruction](../../language/construction-and-destruction.md),
  the ordinary construction, construction packets, reconstructive replacement,
  and wrapper-owned `.=` sections - constructor-member construction and every
  current meaning of `.=`.
- [Zax declarations and bindings](../../language/declarations-and-bindings.md),
  the declaration and initialization sections - the largest class of `=`
  construction sites.

### Consequence-driven

Read only the smallest relevant sections when a scenario class reaches them:

- [Zax optional values](../../language/optional-values.md) and
  [Zax variants](../../language/variants.md) for payload construction.
- [Zax operator catalog](../../language/operator-catalog.md) for `=` and `.=`
  forms and precedence.
- [Zax pointers, allocation, and arenas](../../language/pointers-and-arenas.md)
  for allocation initializers.
- [Zax lambdas and callable composition](../../language/lambdas-and-callable-composition.md)
  for captures.
- [Zax structural shapes and compatibility](../../language/structural-shapes-and-compatibility.md)
  for result mapping and transformation construction.
- [Zax qualifiers](../../language/qualifiers.md) for replacement permission.
- [Zax conversions and casts](../../language/casting.md) as the first taught use
  of result `.=`.

### Audit-only

- [Archived `031`](../archive/work/031-casting.md), for how the result-slot
  rule was reached, only when a provenance question cannot be answered from
  current owners.

## Working record

### How to read this record

The language maintainer and agent reviewed this concern on 2026-09-24. The
sections below are **aligned findings**: accepted for this review scope but
not yet promoted, and therefore still non-authoritative. They are ordered the
way a programmer should learn them: the model first, then the rules that
follow from it, then boundaries and traps. Rejected ideas, deferrals, promotion
consequences, and the evidence behind the rejected broad change follow.

Unmarked source uses the aligned forms, including the renamed
`+++ replacement`. Illustrative syntax is marked inline.

Reading performed beyond the required set: result routing and elision,
structural result mapping, decomposition and transformation, lambda captures,
allocation initializers, the operator catalog `.=` rows and mapping-bound
forms, source structure's `.=` tokenization, the `terms.md` replacement entry,
the arrays owner's delayed-construction section, the casting example, and raw
safety inputs that mention `unsafe ???`.

### Finding 1: declaring a place constructs it; `.=` constructs into a place that already exists

This is the answer to the initiating question, "why do declarations use `=`
when results use `.=`?"

```zax
item : Item = source
// Declaring `item` is what constructs it. The `=` belongs to the declaration.

item = other
// `item` already exists and is live: ordinary assignment.

make final : (
  result : Item
)() = {
  result .= source
  // `result` was declared by the prototype but starts empty.
  // `.=` constructs a value into that existing place.
}
```

Plain-language model to teach:

> Writing `:` introduces a place, and the declaration's initializer constructs
> it. Once a place exists, the assignment operator `=` changes its live value,
> and `.=` constructs a new value into it: first construction when the place is
> still empty, replacement when it is live.

A declaration never needs `.=`, because introducing the place is the act of
construction. `.=` is needed only where a place was introduced somewhere else,
by a prototype or a type definition, and the code now has to construct into it.

The broad alternative, moving every construction site to `.=`, was rejected.
See "Rejected and superseded" and the retained evidence.

### Finding 2: `=` has three roles, and only one is the assignment operator

Teaching must name these roles separately, because readers see the same
character in each:

```zax
item : Item = source
// 1. Declaration initializer: constructs the declared place.

a: myFoo : MyFoo, b: bar: = makeValues()
// 2. Routing-group mapping: routes each producer result to its entry.
//    New declarations such as myFoo and bar are constructed.
//    An existing destination in an `=` group receives ordinary assignment.

existing = source
// 3. Assignment operator: requires a live destination and never constructs it.
```

The accurate teaching sentence is:

> The assignment operator `=` never constructs its destination.

"Never constructs" alone is inaccurate. Evaluating the right side of an
assignment may construct values: a temporary, a producer's result slot, or the
pointee of a new allocation:

```zax
scheduled : MyValue * = @
reset scheduled
scheduled = @{ anotherArena }
// The pointer place is live and receives assignment.
// The new MyValue pointee is constructed on the right side.
```

### Finding 3: `.=` has exactly four uses

| Use | The place before `.=` | Example |
| --- | --- | --- |
| Replacement | A live varying place | `message .= makeMessage("second")` |
| Payload construction | A live optional or variant wrapper | `optional .= [{ inputs }]` |
| First construction of a result slot | Empty result slot | `result .= source` |
| First construction of a constructor member | Empty direct member under explicit control | `_.connection .= [{ endpoint }]` |

**Replacement** always happens when `.=` meets a live place, whether or not the
type declares a hook. When a viable `+++ replacement` exists, it runs and may
recycle resources. Otherwise the compiler-owned fallback destroys the old value
in place and constructs the new one. A programmer with no custom hook still
writes `value .= makeValue()`.

**Payload construction** keeps the wrapper's lifetime and constructs its
contained state. It was already current design and is unchanged by this work.

**First construction** selects an ordinary constructor, exactly as a declaration
initializer would, and needs no replacement permission.

Only result slots and direct members under explicit construction control can
be empty after their declaration. No other place in safe or unsafe code starts
empty (see Finding 9).

### Finding 4: constructor members use `.=` for delayed construction

In a constructor body, `_.member .= ...` is the readable form of
`_.member.+++(...)`. Both remain valid and mean the same thing.

```zax
Session :: type {
  connection : Connection

  +++ final : ()(endpoint : Endpoint) = {
    _.connection .= [{ endpoint }]
    // Same as _.connection.+++(endpoint)
  }
}
```

#### Which members are constructed automatically

Before the body runs, the compiler decides which members the selected
constructor controls explicitly. A member is under explicit control when that
constructor contains any of:

- `_.member .= ...`, including `_.member` as a destination in a `.=` routing
  group (Finding 6);
- `_.member.+++(...)`;
- an `unsafe<opaque-construction>{ ... }` enclosure that names the member
  (Finding 9); or
- a direct allocation `_.member = @...`, which, as today, suppresses the
  member's declared automatic pointee allocation. The pointer member itself is
  initialized vacant; the allocation fills it.

Every other member is initialized automatically before the body, in
declaration order, from its own declaration: its initializer when it has one,
otherwise default construction. What matters is whether an explicit
construction step appears, not whether the member is referenced:

```zax
MyType :: type {
  a : A
  b : B = makeB()
  c : C
  d : D
  e : E

  +++ final : ()() = {
    _.c .= makeC()                                     // c is constructed here
    unsafe<opaque-construction>{ lowLevelFill(_.d) }   // d is established here
    _.e = makeE()                                      // e was already initialized: assignment
    use(_.a)                                           // a was already initialized
  }
}
```

- `a` and `e` are default-constructed before the body; `b` is constructed from
  `makeB()`.
- `c` and `d` are constructed where the body reaches them.

**Trap to teach explicitly.** `_.e = makeE()` is two operations: automatic
default construction, then assignment. If `E` has no default constructor, the
line is an error. The one-operation form is `_.e .= makeE()`.

A member whose type cannot be default-constructed, with no initializer and no
explicit step in the selected constructor, is an error. A call-site packet
entry such as `[{ .b = otherB }]` also replaces automatic initialization for
that member.

#### Qualifiers: first construction versus a second `.=`

The first `.=` constructs and needs no permission. A second `.=` on the same
member is replacement and follows ordinary replacement rules:

```zax
MyType :: type {
  label : Label immutable writable varying
  view : Label readonly varying &
  fixed final : Label

  +++ final : ()(first : Label, second : Label) = {
    _.label .= first
    _.label .= second  // replacement: the old immutable value ends; allowed

    _.view .= first
    _.view .= second   // error: replacement through a readonly path

    _.fixed .= first
    _.fixed .= second  // error: replacement of a final place
  }
}
```

`immutable` describes the value within one lifetime, so it does not block a
replacement that ends that lifetime. `readonly` describes the access path and
`final` describes the place; each independently blocks replacement. This
matches result slots.

#### Boundaries

- **Only direct members.** Construction does not flatten. `_.member.sub .= x`
  can only replace a live `sub` after `member` exists.
- **Variant and optional members.** The payload form requires a live wrapper.
  Construct the wrapper as a whole instead. The same rule applies to an empty
  variant or optional result slot.

  ```zax
  +++ final : ()() = {
    _.choice.text .= "ready"          // error: the wrapper is not constructed yet
    _.choice .= [{ .text = "ready" }] // constructs the wrapper with that alternative
  }
  ```

- **Packet conflict.** A call-site `.member = ...` entry and a body
  `_.member .= ...` both construct the same member. This is diagnosed like the
  existing `+++` conflict.
- **`+++ replacement` bodies.** Members hold the old representation and are
  live, so `_.member .= x` is member replacement. After the body explicitly ends
  a member, that member is empty, and the next `.=` is first construction.
- **Outside constructors.** In an ordinary method, `_.member .= x` is always
  replacement, because every member is live.

### Finding 5: the compiler decides "first time" with a conservative rule

`.=` means first construction or replacement depending on whether the place is
empty or live. The rule for deciding must give every compiler the same answer,
so it performs no reasoning about values.

- Each tracked place (a result slot or a direct member under explicit control)
  is **empty** or **live** at every point in the body.
- The state changes only at visible operations: `.=`, `+++`, a value-bearing
  `return`, a naming `opaque-construction` enclosure, or a routing entry make
  a place live; an explicit end makes it empty.
- Where control-flow paths join, including the back edge of a loop, differing
  states become **unknown**.
- Conditions are never evaluated. There is no exemption for constant
  conditions such as `while true`.
- At each `.=`, the state must be known: empty means first construction, live
  means replacement, unknown is an error asking the programmer to restructure.
- At normal completion, every result slot and every required member must be
  live.

Branches that agree are accepted:

```zax
make final : (result : Item)(fast : Boolean) = {
  if fast
    result .= makeQuick()
  else
    result .= makeCareful()
}
```

A loop is rejected even when it would construct only once:

```zax
make final : (result : Item)(candidates : Item[]) = {
  while true {
    if candidate.matches() {
      result .= candidate // error: construction state is unknown inside the loop
      break
    }
  }
}
```

The loop body may run more than once, so the compiler cannot tell whether this
`.=` constructs or replaces. Make the construction point clear in code, for
example by finding the match first and constructing `result` after the loop.

This formalizes the tracking that delayed member `+++` construction already
relied on. It is a conservative definite-state analysis, comparable to the
"definitely unassigned" rule some languages use for `final` variables.

### Finding 6: a `.=` routing group constructs or replaces every existing destination

A multiple-result producer can construct existing places directly, which a
single `.=` or a construction packet cannot do:

```zax
MyType :: type {
  a : A
  b : B

  +++ final : ()() = {
    first: _.a, second: mySecond : MySecond .= makeTwo()
    _.b .= mySecond.makeB()
  }
}
```

- `_.a` is under explicit control and empty, so `first` constructs it directly.
  The compiler may elide the producer's result slot into the member.
- `mySecond : MySecond` is a new declaration and is constructed once, directly
  from `second`.
- `_.b` is constructed from `mySecond.makeB()`.
- `mySecond` is destroyed at the end of the body.

The group's token decides what happens to every **existing** destination, and
it applies to all of them:

```zax
number:, existingText = produce()
// `=` group: new declarations are constructed; existing destinations are assigned.

first: _.a, second: mySecond .= makeTwo()
// `.=` group: each existing destination receives `.=`.
// A `.=` group never assigns.
```

When an existing destination is already live, its entry is replacement:

```zax
+++ final : ()() = {
  mySecond : MySecond = prepareMeTheSecondPlace()
  first: _.a, second: mySecond .= makeTwo()
  // _.a: first construction, with elision possible.
  // mySecond: replacement from `second`.
  _.b .= mySecond.makeB()
}
```

Replacement cannot elide: a live place cannot become the producer's result
slot, so `second` is an input to the replacement and its temporary is
destroyed afterwards. If `MySecond` declares a `+++ replacement` that accepts
that input, it can recycle resources prepared by
`prepareMeTheSecondPlace()`. Without such a hook, preparing the place first only
adds a construction and a destruction.

Result slots use the same form without completing the function:

```zax
make final : (a : A, b : B)() = {
  first: a, second: b .= makeTwo()
  finishSetup(a, b)
}
```

Rules:

- Every existing destination receives `.=` as if written `place .= result`:
  first construction when empty, replacement when live. The first-time rule
  applies to each entry separately.
- New declarations may appear in a `.=` group and are constructed.
- In a constructor, a member used as a `.=` group destination is under explicit
  control.
- Entries are processed in order and are not transactional. A panic in a later
  entry does not undo earlier entries, matching `=` groups.
- Replacement entries follow ordinary replacement rules: inputs are evaluated
  before the old lifetime ends, and alias hazards are unchanged.
- A discarded `#` source result is still constructed by the producer. It is a
  separate result slot, destroyed when the mapping completes.
- A replacement hook's **required** result has nowhere to go in a group, so
  that entry is an error. A discardable (`#`) result is dropped. Guidance:
  declare replacement results with `#` when callers may reasonably ignore them.
  When the result is needed, write that replacement as its own statement:
  `retained := owner .= [{ nextFormat }]`.
- A `.=` group is allowed only as a statement. The other routing contexts have
  no existing places to construct into:

  | Context | Destinations | `.=` group |
  | --- | --- | --- |
  | Call routing | Parameters created by the call | Error; use `=` |
  | `using` lists | New resource bindings | Error |
  | Capture producer groups | New captures | Error |
  | `return` routing | Result slots that `return` already constructs | Error; use `return … =` or a statement `.=` group |

- Grammar: a `.=` group ends with `.=` and one producer, just as an `=` group
  ends with `=` and one producer. `#, resultB: kept .= makeResults()` is one
  group, not `kept .= makeResults()` nested inside an entry.

Call routing keeps `=` because binding a parameter is construction of a new
slot, the same as a new declaration in an `=` group.

### Finding 7: structural mapping forms

**Transformation already supports both tokens.** Its contextual construction
form takes a destination context:

```zax
renderPoint = -<>- DeviceToRender -<>- devicePoint
// renderPoint is live: construct a complete temporary, then assign it.

_.renderPoint .= -<>- DeviceToRender -<>- devicePoint
// First construction of the member, with elision possible;
// replacement if the place is live.
```

The update form `renderPoint -<>- DeviceToRender -<>- devicePoint` assigns into
a live destination, so it is an error on an empty place.

**Decomposition `>-` has no `.=` form.** It exists mainly to spread a structure
into call arguments, not to construct results or members. Into existing
destinations it assigns. To construct members from a decomposed value, write
one `.=` per member:

```zax
+++ final : ()(point : Point) = {
  _.x .= point.x        // one operation per member: first construction
  _.y .= point.y
}

+++ final : ()(point : Point) = {
  x: _.x, y: _.y >- point
  // Two operations per member: automatic default construction, then assignment.
  // Valid only when the member types can be default-constructed.
}
```

Constructing the members first with `_.x .= [{}]` before decomposing is valid
but still two operations per member, and it needs the same zero-input
constructor that automatic initialization would use. It only makes the
construction points visible.

### Finding 8: `replacement +++` becomes `+++ replacement`

Every other constructor variant places its words after `+++`:

```zax
+++ final : ()(source : Source) = { … }
+++ contextual final : ()(rhs : Integer) = { … }
+++ final once : ()() = { … }

+++ replacement final : ()(settings : Settings) = { … }
```

The replacement hook follows the same post position. The relative order of
several post words stays with the existing future decision on declaration
keyword position. At the time of review the prefix form appeared 26 times in 8
live files; promotion renames every occurrence.

### Finding 9: `???` is removed; opaque construction is asserted where it happens

Zax no longer has `unsafe ???` in any declaration context, and no safe `???`
form is added. No place outside result slots and explicitly controlled members
starts empty.

**Members** need no bypass form. Explicit construction control already stops
automatic initialization. When an operation the compiler cannot see
establishes a member, such as assembly or foreign code, the constructor asserts
it at that operation:

```zax
MyType :: type {
  s : S
  t : T

  +++ final : ()() = {
    _.t .= [{}]
    unsafe<opaque-construction>{ lowLevelAssemblyCall(_.s) }
  }
}
```

- The enclosure places `s` under explicit control and makes it live for the
  first-time rule. A later `_.s .= ...` is replacement.
- Passing `_.s` to an ordinary call without the enclosure is use before
  construction. The compiler cannot tell an establishing call from a reading
  one, so construction must be explicit.
- The enclosure must identify the places it establishes. A call receiving two
  empty places must not silently establish both. The syntax for naming places
  is deferred.
- The category name follows the existing domain-and-fact pattern, such as
  `replacement-alias` and `optional-presence`. It is accepted for now and may be
  revised later.

**Locals** are always initialized when declared and have no delayed
construction. Code that needs an uninitialized buffer, for example to avoid
initialization cost before a device fills it, wraps it in a type whose
constructor asserts the opaque construction:

```zax
DeviceBuffer :: type {
  bytes : U8[4096]

  +++ final : ()(device : Device &) = {
    unsafe<opaque-construction>{ device.readInto(_.bytes) }
  }
}

buffer : DeviceBuffer = [{ device }]
```

The wrapper adds no runtime cost. The unsafe responsibility sits on the one
operation that needs it, following the existing principle that a permission
belongs at the risky operation rather than on a declaration.

Arrays need no special rule. An array member is established the same way as
any other member.

### Finding 10: correct the elision example in function invocation

The current example routes into a result slot without `return`:

```zax
keepSecond final : (
  kept : ResultB
)() = {
  #,
  resultB: kept: = makeResults()
}
```

`kept:` there reads as a new binding shadowing the result slot. The corrected
form is a `.=` group:

```zax
keepSecond final : (
  kept : ResultB
)() = {
  #,
  resultB: kept .= makeResults()
}
```

`kept` is empty, so `resultB` constructs it, with elision possible. The `#`
result `resultA` is constructed by the producer and destroyed when the mapping
completes, so `resultA` is destroyed before `resultB`. That was the point of
the original example, and it is preserved.

### Teaching obligations for promotion

These points are where readers are most likely to go wrong. Promotion should
teach each with a representative example:

1. **Why declarations use `=`.** Declaring constructs; `.=` constructs into a
   place declared elsewhere (Finding 1).
2. **The assignment operator never constructs its destination**, while its
   right side may construct values (Finding 2).
3. **`_.e = makeE()` in a constructor is two operations** unless the member is
   under explicit control (Finding 4).
4. **`immutable` does not block replacement; `readonly` and `final` do**
   (Finding 4).
5. **A `.=` group never assigns; an `=` group never constructs an existing
   destination** (Finding 6).
6. **Decomposition assigns; construct members with one `.=` each**
   (Finding 7).
7. **Result-slot wording** in function invocation should be revised to present
   the same model as members, rather than as a special case for results.

### Rejected and superseded

These were considered during review. Promoted documentation removes them and
does not present them as alternatives.

- **Moving every construction site to `.=`.** Rejected. It makes common code
  noisier (`count : .= 0` replaces `count := 0`, because `.=` is a spaced token)
  and leaves routing groups that mix construction and assignment with no
  coherent token. Evidence is retained below.
- **A safe local `???` for delayed construction.** Rejected. Locals are always
  initialized; use a wrapper type.
- **`unsafe ???`.** Removed entirely (Finding 9).
- **The array pattern `rawArray.+++([ ... ])` after `unsafe ???`.** Rejected
  and removed as if never presented. It built a complete temporary array and
  then constructed from it, which costs more than direct initialization and
  gains nothing.
- **Superseded interim alignments.** "`unsafe ???` members must be constructed
  during construction," the reading of "explicit, not implicit," and the rule
  for bypassed locals were aligned during review and then superseded by
  removing `???`. `.=` groups were first proposed without new declarations;
  that restriction was dropped.
- **Constant-condition exemption** for the first-time rule. Rejected.
- **Letting an `=` routing entry construct an empty existing place** by tracked
  state. Rejected in favor of `.=` groups, so that an existing place is
  constructed only where `.=` or `+++` is visible.
- **A `.=` form of decomposition**, such as an illustrative
  `x: _.x, y: _.y .= >- point`. Rejected; no special grammar.
- **Splitting construction and replacement into separate tokens.** Considered
  and not pursued.

### Deferred

**What a `.=` expression returns.** Current forms disagree:

```zax
access := result .= [{ .name = "example" }]
// first construction: access to the new result

retained := owner .= [{ newFormat }]
// replacement: the hook's declared results, possibly none;
// the destination is never implicitly returned

payload := optional .= value
// payload construction: access to the new payload

wrapper := variant .= [{ .text = "ready" }]
// variant packet: access to the wrapper
```

- Why it matters: a reader cannot predict the value of a `.=` expression without
  knowing which use applies.
- Why it can wait: the aligned rules do not depend on it, and `.=` groups
  cannot be used as expressions.
- Reopen when code needs a `.=` expression's value, such as chaining or passing
  it along.
- Constraint on promotion: do not teach a uniform result for `.=`. Member first
  construction follows the result-slot rule and returns access.
- Owner: the construction owner's replacement-results section, with function
  invocation's result-slot section.

**How `opaque-construction` names its places.**

- Why it matters: the enclosure triggers explicit control and changes tracked
  state, so the compiler must know exactly which places it establishes.
- Why it can wait: the requirement is aligned; only the syntax is open.
- Reopen with the unsafe-category syntax work.
- Constraint: the enclosure must not establish places implicitly.
- Owner: the analysis-control raw input
  (`project/raw/analysis-controls.md` and its registry) until a current
  safety owner exists.

### Promotion consequences

The dry run will sweep for every mention. Known affected files:

| File | Change |
| --- | --- |
| `language/declarations-and-bindings.md` | Mental-model sentence on declaring versus `.=`; the three roles of `=`; remove the `unsafe ???` row, "Explicitly uninitialized storage", and the stored-member `unsafe ???` paragraph; `.=` groups beside routing; replacement naming |
| `language/construction-and-destruction.md` | Member `.=`, explicit-control triggers, the `_.e` trap, qualifiers, boundaries; the first-time rule; rewrite "Manual and delayed construction" around `opaque-construction` and the wrapper workaround; remove the `rawArray` example; `+++ replacement`; diagnostics |
| `language/function-invocation.md` | Result-slot wording under the shared model; "never constructs its destination"; `.=` groups in routing; correct `keepSecond` |
| `language/operator-catalog.md` | `.=` rows for member construction and groups; replace the `unsafe ???` note; replacement naming |
| `language/arrays-and-slices.md` | Remove "Delayed construction after `unsafe ???`" |
| `language/structural-shapes-and-compatibility.md` | `.=` with contextual transformation; decomposition assigns, with the per-member workaround |
| `language/source-structure.md` | Recognize a routing group ending in `.=` |
| `language/terms.md` | `.=` and replacement-constructor entries |
| Other live files using `replacement +++` | Rename |
| `project/raw/safety.md`, `project/raw/analysis-controls.md`, `project/raw/analysis-control-registry.md`, `project/raw/feature-catalog.md` | Remove `unsafe ???` assumptions; add `opaque-construction` and its deferred naming requirement |

### Evidence retained from the initial reconstruction

#### Design intent found in the corpus

- Declarations make name introduction visible, and declaration initialization
  is reserved: the `:` introduces the name and user code cannot overload that
  act (declarations, mental model and assignment sections).
- An initialized typed declaration performs direct initialization, never
  default construction followed by assignment.
- Ordinary `=` requires a live destination and never changes into construction
  according to tracked state.
- Protected `.=` is compiler-owned and cannot be overloaded.
- The result-slot rule already treats first-construction `.=` as meaning
  "exactly what the same source would mean as a declaration initializer."
- Explicit member construction was already determined syntactically, by the
  presence of `_.member.+++()` or a direct `_.member = @...` allocation.

The corpus implied the declared-place model but never stated it, so readers had
to infer it. Finding 1 states it.

#### Implications of the rejected broad change

These examples show what moving every construction site to `.=` would have
changed. All `.=` declaration forms below are illustrative and rejected.

**Declarations and inferred declarations.** `:=` appeared on about 640 lines in
40 live language files. Because `.=` is a spaced binary token, the compact
inferred form would disappear:

```zax
item : Item = source
count := 0

// Rejected broad form
item : Item .= source
count : .= 0
```

Declarations also share their `=` with bodies and lifecycle states, such as
`make final : (result : Item)() = { … }` and `= default`, which would split one
declaration grammar into two spellings.

**Routing groups.** One `=` serves entries that construct and entries that
assign:

```zax
existingText : String = "previous"
number:, existingText = produce()
```

Moving to `.=` would turn `existingText` into replacement; keeping `=` would
leave the declaration constructing through `=`. The same token appears in
`using` lists, call routing, and capture producer groups.

**Prototype initializers and parameter defaults.** `result : Item = :` and
`attempts : Integer = 3` would both have to move, or one prototype would mix
spellings.

**Construction packets.** `.member = expression` entries are stored-member
initialization in an entry grammar, alongside `name: expression`, not
assignment. A broad change would produce `[{ .data .= makeMouseData() }]`.

**Unchanged in every model.** Captures construct without an `=` token
(`[[ sourceCount: capturedCount : MyCount ]]`); `return` constructs result
slots; producer `except failure: makeFailure()` and
`catch failure: localFailure:` construct through labels; and ordinary
assignment, compound mutation, replacement, and payload construction keep
their meaning:

```zax
mutableMessage = makeMessage("assigned") // in-lifetime assignment
counter += 1                              // compound mutation
message .= makeMessage("second")          // replacement of a live varying place
optional .= value                         // payload construction
```

## Dispositions and promotion dry run

### Result: PASS

Dry run performed 2026-09-24 after the maintainer authorized a dry run and,
on PASS, promotion without a separate review. Every aligned finding has one
lasting owner in the current layout, no directory or new document is needed,
and the two deferrals have live destinations.

### Structure proposal

Retain the current layout. No files move, no files are created, and no
directories or indexes change. `project/README.md`, `index.md`, and
`project/raw/README.md` need no routing changes: no raw file is created or
retired, and every affected owner is already routed.

### Ownership map

| Finding | Lasting owner | Other files that summarize or link |
| --- | --- | --- |
| 1. Declaring constructs; `.=` constructs into an existing place | Declarations and bindings, mental model | Construction owner mental model (short handoff) |
| 2. Three roles of `=`; the assignment operator never constructs its destination | Declarations and bindings, assignment section | Function invocation result slots |
| 3. Four uses of `.=` | Construction owner, new overview subsection under the mental model | Operator catalog `.=` rows; `terms.md` |
| 4. Constructor members | Construction owner, automatic and explicit member construction | `terms.md` |
| 5. First-construction rule | Construction owner, new subsection after member construction | Function invocation result-slot tracking list |
| 6. `.=` routing groups | Function invocation, result-routing groups | Declarations multiple results (summary), source structure (grammar), operator catalog (row) |
| 7. Structural forms | Structural shapes and compatibility | None |
| 8. `+++ replacement` | Construction owner (custom replacement) | Every live occurrence renamed, including `qualifiers.md`, `terms.md`, `optional-values.md`, `variants.md`, `source-structure.md`, `operator-catalog.md`, `declarations-and-bindings.md`, and legacy `basics.md` |
| 9. `???` removed; `opaque-construction` | Construction owner, manual and delayed construction | Declarations (removals), arrays (removal), operator catalog (note), `safety-and-analysis.md` (control list), raw analysis-control input and registry, raw safety, raw feature catalog |
| 10. `keepSecond` correction | Function invocation, elision example | None |
| Deferred: `.=` expression results | Construction owner, boundaries and maturity | Operator catalog rows keep per-form results |
| Deferred: naming places in `opaque-construction` | `project/raw/analysis-controls.md` and registry | Construction owner states the requirement |

### Raw-input dispositions

- `project/raw/analysis-control-registry.md`: add `opaque-construction`
  (Provisional; name accepted for now, syntax for naming places open). Mark
  `manual-member-construction` superseded: suppressing automatic member
  construction is now implied by every explicit construction step, and
  opaque establishment is `opaque-construction`. Remove `unsafe ???` from the
  domain-source-form list. `construction-path-complete` and
  `construction-at-most-once` are also superseded (revised during promotion;
  see the promotion log).
- `project/raw/analysis-controls.md`: same table change; replace the
  `unsafe ???` paragraph and update the lifecycle-state assertion example.
- `project/raw/safety.md`: replace the `unsafe ???` paragraph.
- `project/raw/feature-catalog.md`: repoint the uninitialized-memory row.

### Teaching plan

Promotion applies two passes. First, each changed owner section is rebuilt so
a cold reader meets source, the plain-language outcome, and then the rule:

- the declarations mental model answers "why `=` here and `.=` there" before
  any table;
- the construction owner introduces the four uses of `.=` with one example
  each before the member rules, then teaches members with the automatic
  versus explicit example and the `_.e` trap inline;
- the first-construction rule leads with an accepted branch and a rejected
  loop marked `// error`;
- `.=` groups lead with a constructor example and contrast `=` groups
  side by side.

Second, every finding and teaching obligation is traced into those sections.
Discovery history, rejected alternatives, and interim alignments stay in this
record only. Promoted text does not mention `???`, the array pattern, or
`replacement +++` as former forms.

### Checks

- **Cold reader:** each rewritten section opens with source and its outcome.
- **Vocabulary:** "first construction", "explicit construction control", and
  "`.=` group" are introduced in plain words before use.
- **Concrete consequence:** every rule has an example with inline `// error`
  where source is invalid.
- **Teaching before reference:** the operator catalog and `terms.md` summarize
  and link; they do not become the teaching surface.
- **No numbered-work citations** in current owners.

### Exact promotion change set

1. `language/declarations-and-bindings.md`: Owns metadata; mental model;
   core value forms table; array paragraph; replace "Explicitly uninitialized
   storage" with a short statement that locals always initialize, linking the
   construction owner's opaque-construction guidance; assignment section
   (roles of `=`, `.=` summary); multiple results (`.=` group summary);
   diagnostics; boundaries.
2. `language/construction-and-destruction.md`: mental model handoff and new
   four-uses overview; member construction rewrite; new first-construction
   subsection; remove `rawArray` example; rename; rewrite manual and delayed
   construction; future unsafe controls list; costs; diagnostics; formatting;
   boundaries, including the deferred result-shape note.
3. `language/function-invocation.md`: result-slot wording; tracking list;
   new `.=` group subsection; `keepSecond`; diagnostics.
4. `language/operator-catalog.md`: `.=` rows, group row, protected-form note,
   rename, diagnostics.
5. `language/arrays-and-slices.md`: remove the delayed-construction section.
6. `language/structural-shapes-and-compatibility.md`: `.=` with contextual
   transformation; decomposition assigns into existing destinations.
7. `language/source-structure.md`: `.=` group grammar; rename.
8. `language/terms.md`: `.=`, lifecycle-operation, and replacement-constructor
   entries.
9. `language/qualifiers.md`, `language/optional-values.md`,
   `language/variants.md`, `basics.md`: rename.
10. `language/safety-and-analysis.md`: control list wording.
11. Raw files listed above.

### Promotion log

Promoted 2026-09-24 under the maintainer's authorization to promote on PASS.
Nothing was staged, committed, or archived.

**Dry-run revision made during promotion.** The dry run first kept
`construction-path-complete` and `construction-at-most-once` as provisional
future pressure. That contradicts the aligned first-construction rule: when the
compiler cannot tell whether a place is constructed, the programmer
restructures the code, and no unsafe assertion overrides it. Both are now
marked superseded in the raw registry and the raw analysis-control input. The
construction owner's list of future unsafe controls was narrowed to
destruction accordingly, with a sentence stating that construction has no
override. The registry also gained a "Superseded" maturity label, and
`opaque-construction` is recorded there as an accepted domain entry, since it
is now taught by current owners.

**Changes beyond the listed change set, all within aligned findings:**

- `terms.md` gained a "First construction" entry, so the new term has a
  terminology home.
- The construction owner's `Registration` example constructed members with
  `_.id = ...`, which the aligned model makes a two-operation trap; it now uses
  `.=`.
- The construction owner's diagnostics gained member-specific entries, and the
  function invocation and operator catalog diagnostics gained `.=` group
  entries.

**Where each teaching obligation landed:**

1. Why declarations use `=`: declarations mental model.
2. Assignment never constructs its destination: declarations "Three roles of
   `=`", function invocation result slots.
3. `_.e = makeE()` is two operations: construction owner, automatic and
   explicit member construction; also listed under costs.
4. `immutable` versus `readonly` and `final`: construction owner, "A second
   `.=` on a member is replacement".
5. `.=` groups never assign: function invocation, "Construct existing places
   with a dot-equals group", summarized in declarations.
6. Decomposition assigns: structural shapes, decomposition section.
7. Result-slot wording under the shared model: function invocation, opt-in
   result initialization.

**Validation:**

- No `unsafe ???` or `replacement +++` remains in live files outside
  provenance notes in raw input.
- All local links and heading anchors in the live tree resolve.
- No current owner cites numbered work.
- Loop examples use Zax's `each value : in values` form.

### Closure

Closed and archived 2026-09-25 with the maintainer's authorization, after the
maintainer reviewed the promotion. Every finding is promoted, rejected,
superseded, or deferred to a live owner:

- the `.=` expression-result deferral lives in the construction owner's
  boundaries and maturity;
- the `opaque-construction` place-naming deferral lives in
  `project/raw/analysis-controls.md` and its registry.

Work item 033 was proposed in chat for alignment and was not created at
closure.
