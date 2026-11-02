# 030: Legacy lifecycle and discard audit

| Field | Value |
| --- | --- |
| Status | Historical working record / non-normative / audit-only |
| Work Item | `030` |
| Created | 2026-09-22 |
| Completed | 2026-09-23 |
| Owns | Historical evidence and dispositions from the completed bounded review |
| Does Not Own | Current Zax language design; see the promoted declaration, invocation, exceptional-flow, lambda, source-structure, and pointer owners |

## Non-authority notice

This file is a historical audit record. It is non-authoritative and excluded
from ordinary onboarding. Current language meaning lives in the promoted
`language/` owners rather than this discussion history.

## Fixed initiating input

This section records the aligned information known when work item `030` was
created. It is intentionally incomplete and must not be rewritten as work
develops.

### Initiating concern

Mine legacy `ctor-dtor.md` and `discard.md` by value so both pages can receive
a complete disposition.

For each distinct concept, rule, source form, example, or design pressure:

- determine whether current documentation already teaches its useful meaning;
- identify worthwhile material that current owners do not yet preserve;
- identify conflict with, or supersession by, current conceptual design;
- explain ambiguity that prevents a responsible disposition;
- recommend the lasting owner or explicit deferral for material worth keeping;
  and
- recommend whether the legacy page can be deleted after all useful input is
  promoted, deferred, rejected, or superseded.

The review must do more than recommend deletion. Any concept worth preserving
must be made understandable in plain language, supported by representative Zax
source where source can clarify it, and shaped like material that could later be
promoted for human developers.

### Review posture

- Treat both root pages as legacy evidence, not accepted design.
- Treat current `language/` owners as current for the concerns they own.
- Do not preserve legacy syntax merely because it exists.
- Do not promote rejected or superseded concepts merely to deny them in current
  teaching.
- When useful intent survives but its old syntax or mechanism does not,
  separate the intent from that obsolete vehicle.
- Distinguish duplicate current teaching from genuinely missing language
  design.
- Follow material consequences far enough to expose conflicts, costs, cleanup,
  lifetime, transfer, or source-stability effects without redesigning unrelated
  language areas.
- If no unique useful material remains after the audit, recommend deleting the
  applicable legacy page.

### Required output

The working record must provide:

1. a reviewable inventory of the useful claims and examples in each legacy
   page;
2. the current owner, if any, that already preserves each useful idea;
3. teachable candidate material for every uncovered idea worth retaining;
4. concrete conflicts with current design and a recommended disposition;
5. explicit deferrals with a live destination and activation pressure;
6. a whole-file recommendation for `ctor-dtor.md`;
7. a whole-file recommendation for `discard.md`; and
8. the exact promotion/deletion set if the work reaches a documentation-fit dry
   run.

The inventory supports review but must not replace understandable findings.

### Initial stopping guidance

Stop when:

- every materially useful passage and representative example from both legacy
  pages has one disposition;
- any uncovered candidate is explained well enough for maintainer review;
- every conflict identifies the current rule or owner that decides it;
- no future-relevant concept survives only in this eventual archive;
- page-retirement recommendations are evidence-based; and
- any aligned promotion has completed the required documentation-fit dry run.

Do not promote findings, delete either initiating legacy page, archive this work
item, or create work item `031` without the separately required discussion,
alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  authority, legacy consumption, promotion, deferral, and closure.
- `ctor-dtor.md` and `discard.md` were the source corpora. Both files have been
  removed. Git history retains them. They are not a current reading path.
- [Construction, replacement, and destruction](../../language/construction-and-destruction.md) -
  current owner for constructors, destructors, lifecycle state, replacement,
  and scope-exit destruction.
- [Declarations and bindings](../../language/declarations-and-bindings.md) -
  current owner for anonymous declarations, explicit discard names,
  initialization, and declaration/result contexts.
- [Function invocation](../../language/function-invocation.md) - current owner
  for result acknowledgement, source/destination discard, `return #`, mapping,
  completion, and elision.
- [Zax `using`](../../language/using.md) - current owner for anonymous resource
  enrollment, disposal suppression through `#`, destruction, and resource-exit
  ordering.

### Consequence-driven

- [Exceptional result flow](../../language/except.md) when legacy discard or
  lifecycle material implies omission, forwarding, or failure outcomes.
- [Lifetimes and references](../../language/lifetimes-and-references.md) when a
  claim changes instance paths, reference validity, escape, or destruction.
- [Transfer stances](../../language/transfer-stances.md) when copy, move,
  terminal use, or source post-state becomes material.
- [Pointers, allocation, and arenas](../../language/pointers-and-arenas.md) when
  legacy ownership, allocation, reset, or deallocation behavior is concrete.
- [Optional values](../../language/optional-values.md) and
  [variants](../../language/variants.md) when conditional contained lifetime or
  reset behavior is implicated.
- [Qualifiers](../../language/qualifiers.md) when construction/destruction or
  discard claims change mutation, access, or replacement authority.
- [Core flow control](../../language/core-flow-control.md) when scope exit,
  transfer, or post behavior matters.
- [Source structure](../../language/source-structure.md) when punctuation,
  attachment, continuation, or declaration-versus-routing intent needs
  disposition.
- [Safety and analysis](../../language/safety-and-analysis.md) when legacy
  material requires proof, unsafe responsibility, panic, or known-invalid
  classification.

Read only the smallest relevant sections after a concrete legacy claim crosses
one of these boundaries.

Review crossed these additional boundaries, so they were read at the named
pressure:

- [Exceptional result flow](../../language/except.md) for `catch` payload
  discard.
- [Lambdas and callable composition](../../language/lambdas-and-callable-composition.md)
  for capture discard, inherited parameters, and receiver spelling.
- [Integer operator catalog](../../language/integer-operator-catalog.md) for
  attached `#myBits`.
- [Nothing instances](../../language/nothing-instances.md) for pointer-shaped
  `_` and explicit `_.`.
- [Raw global and once lifetimes](../raw/global-and-once-lifetimes.md) for the
  deferred `once` value.
- [Compiler warnings and errors](../../warnings-errors.md) for the legacy
  unread-binding diagnostic id.

### Audit-only

- Archived numbered work only when a concrete provenance or regression question
  cannot be answered from current owners and the two initiating legacy pages.

## Working record

Aligned findings. The language maintainer aligned these decisions in review.
Promotion below incorporates them into their lasting owners. This file remains
non-normative.

### Unread names

A named binding the local scope may leave unread carries `#` between the name
and its type:

```zax
lock # : MyLock = mutex
ignore final : ()(input # : Integer) = { }
```

The caller still passes `input`. On a result, the same marker position excuses
the caller instead: `diagnostic # : String` may be omitted. A mapping discard
such as `diagnostic: #` acknowledges a result and introduces no name.

An exceptional outcome still requires `catch` or `except`. The handler may
decline the payload name:

```zax
myResult := function() catch failure: # {
  return
}
```

The payload is constructed for the handler and destroyed when the handler
exits. A value-producing call still leaves through `return` or another
transfer.

A capture uses the same mapping discard. An empty destination name keeps the
source name; `#` stores the value and introduces no body name:

```zax
keeper := [[ source: # : StoredResource, visible: kept: ]] ()() {
  use(kept)
}
```

A statement `# name` is not valid. Attached `#myBits` remains population count.
A name this body did not declare, including an inherited lambda parameter, is
acknowledged with a trailing marker:

```zax
callback : MyCallback = [[]] {
  value #
}
```

An unread named binding that was not acknowledged is an intent error. That is
not an `intent<...>{}` category: there is no present operation to wrap.
Anonymous `: & = input` is an ordinary declaration, not the acknowledgement.

Type-level `MyLock # :: type` is rejected.

### Lambda receiver spelling

The capture list inserts a body binding for a receiver member. Both spellings
name that member:

```zax
print(captured)
print(_.captured)
```

Either spelling uses the capture. A `#` destination has no member name.

Self-recursion calls the receiver with no dot: `_(value - 1)`. `_.captured` is
member access. `myValue(...)` is a call. `myValue.(...)` is not the call form.

### Construction legacy

Path-sensitive member lifecycle in the construction owner supersedes textual
scans, dead-branch suppression, and the undefined-behavior account of a missed
or repeated member construction. Ordinary constructor and destructor teaching
on the legacy page is already owned there. No new construction model is
promoted.

### Lazy `once` values

A function-local lazy unique value is the intended mechanism and stays
deferred. Its representative example and claims live in
[raw global and once lifetimes](../raw/global-and-once-lifetimes.md). Current
`once` functions are not this feature. Exact ordering, panic, retry, and
module-instance teardown stay unresolved in that raw file.

### Legacy inventory

| Legacy claim | Disposition |
| --- | --- |
| `+++` / `---` overload and optional destructor | Already taught by construction |
| Resultless constructors and destructors | Already taught; panic does not publish a partial instance |
| Synchronous lifecycle | Already a boundary; suspension stays in raw async input |
| Field defaults and direct initialization | Already taught |
| Brace-list multi-argument construction | Superseded by construction packets |
| Textual scan, dead-branch suppression, missed-path undefined behavior | Superseded by path-sensitive diagnostics |
| Manual member destruction order | Already taught |
| `value once` lazy unique instance | Deferred in the global/`once` raw input |
| Anonymous `# :=` and result `#` | Already taught |
| `using` and iteration `#` | Already taught |
| Allocation `discard` | Superseded by `AllocationDisposition.DeferredRecovery` |
| `#myBits` family | Current attached forms `#myBits`, `#<myBits`, `#^myBits` |
| Statement `# input` | Rejected |
| `name # : Type` and statement `name #` | Promoted with declarations |
| `catch` and capture destination `#` | Promoted with exceptional flow and lambdas |
| Type-level `#` | Rejected |
| Unread binding diagnostic | Intent error, taught with declarations |

## Dispositions and promotion dry run

**Result: PASS.**

Owners can absorb the findings as local teaching. No new concept document is
required. Deferred `once` values already have a live raw destination. Rejected
vehicles are not promoted as counterexample chapters.

### Ownership

| Finding | Owner |
| --- | --- |
| Unread `name # : Type`, statement `name #`, intent error, rejection of type-level `#` | Declarations and bindings |
| Statement shape of `name #`; `catch failure: #` layout | Source structure, meaning stays with the concept owners |
| Result `#` versus body `#`; exceptional outcome still requires catch or except | Function invocation, with the handler form taught by exceptional flow |
| `catch failure: #` payload lifetime | Exceptional result flow |
| Capture `#`, `_.capture`, `_(...)` recursion | Lambdas and callable composition |
| Path-sensitive construction supersession | Already taught; legacy page becomes a route |
| Lazy `once` value example and claims | Raw global and once lifetimes |
| Legacy unread-binding registry entry | Warnings and errors registry, marked superseded |
| Public legacy index entries | `index.md` |
| Feature-catalog discard row | `project/raw/feature-catalog.md` |

### Teaching check

Promotion leads with the valid source. Declarations teaches the unread binding
before the inherited-statement form. Lambdas teaches the existing empty-name
capture before `#`, then shows `_.capture` as the same member. Exceptional
flow teaches `catch failure: #` beside the other handler destinations. Result
omission stays in function invocation, with one boundary sentence so the shared
marker is not given a second definition. Population count stays an integer
operation; declarations names it only to keep `#myBits` from being read as the
unused-name marker. Type-level discard and the dead-branch constructor trick
are not given denial chapters.

### Change set

- `language/declarations-and-bindings.md`
- `language/function-invocation.md`
- `language/except.md`
- `language/lambdas-and-callable-composition.md`
- `language/source-structure.md`
- `project/raw/global-and-once-lifetimes.md`
- `project/raw/feature-catalog.md`
- `warnings-errors.md`
- `index.md`
- `ctor-dtor.md` and `discard.md` deleted. Git history retains their former
  text. No route pointers remain.
- This work file.

No `031` is created by the promotion pass itself.

## Closure

Work item `030` is complete.

- Unread-name acknowledgement, handler and capture discard, capture member
  access, and lambda self-call spelling are promoted.
- Path-sensitive construction remains the current model. The legacy textual
  scan and undefined-behavior account were not promoted.
- The lazy `once` value example and its claims live in
  `project/raw/global-and-once-lifetimes.md`.
- Root `ctor-dtor.md` and `discard.md` were consumed and deleted. Git history
  retains them. The fixed initiating input above names those paths without
  linking to them.
- No accepted meaning depends on this record after archival.

The next bounded concern is casting, including pointer casts and `as`. Its
fixed initiating input and reading scope are created separately as work item
`031`. This record performs no analysis of that material. The maintainer will
write `project/raw/casting-maintainer-notes.md` before that review begins.

