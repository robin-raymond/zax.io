# 033: What a `.=` expression produces

| Field | Value |
| --- | --- |
| Status | Historical working record / non-normative / audit-only |
| Work Item | `033` |
| Created | 2026-09-25 |
| Completed | 2026-09-25 |
| Owns | Historical evidence and dispositions from the completed bounded review |
| Does Not Own | Current Zax language design; see the promoted construction, invocation, operators, operator-catalog, integer-operator-catalog, declarations, optional-values, source-structure, and terms owners |

## Non-authority notice

This file is a historical audit record. It is non-authoritative and excluded
from ordinary onboarding. Current language meaning lives in the promoted
`language/` owners rather than this discussion history. Mentions of results
declared by replacement constructors describe a form that no longer exists.

## Fixed initiating input

This section records the aligned information known when work item `033` was
created. It is intentionally incomplete and must not be rewritten as work
develops.

### Initiating concern

Should every `.=` expression produce one predictable value, such as access to
the place it constructed? If so, how do the results declared by a replacement
constructor fit alongside that value?

Current design gives the same operator different values depending on which of
its four uses applies:

```zax
access := result .= [{ .name = "example" }]
// first construction: access to the new result

retained := owner .= [{ newFormat }]
// replacement: only the replacement constructor's declared results,
// possibly none; the destination is never implicitly returned

payload := optional .= value
// payload construction: access to the new payload

wrapper := variant .= [{ .text = "ready" }]
// variant packet: access to the wrapper
```

### Motivating pressure

- Work item 032 made `.=` the visible spelling for construction into an
  existing place: first construction of result slots and constructor members,
  replacement, payload construction, and statement-level routing groups.
  Readers now meet `.=` far more often.
- The construction owner currently teaches that the value of a `.=` expression
  "currently depends on its use" and lists unifying it as future work. That
  hedge is the weakest point of the newly promoted material.
- Which use applies can depend on tracked state. The same source line
  `x := place .= source` binds access when `place` is empty and replacement
  results when it is live, so a reader cannot predict the value without first
  applying the first-construction rule.
- Right-associative chains such as `a .= b .= c` depend on what the inner
  `.=` produces.

### Starting constraints

These are current design and are not reopened by this item:

- `.=` has exactly four uses: first construction of an empty explicitly
  controlled constructor member, first construction of an empty result slot,
  replacement of a live varying place, and optional or variant payload
  construction.
- `.=` may also end a statement-level routing group. A routing group is not an
  expression and produces no value.
- `.=` is compiler-owned and cannot be overloaded.
- A replacement constructor may declare zero or more ordinary results. A
  required result dropped by a `.=` routing group is an error; replacement
  results declared with `#` are discardable.
- The compiler does not currently return the reconstructed destination from a
  replacement, and a resultless replacement produces no value.
- As precedent, protected intrinsic `=` returns writable access to its
  destination, and generated same-type `copy` assignment returns readonly
  `copy` access to the receiver.

### Deliberately unresolved framing

- Whether `.=` should have one uniform value, a small set of predictable values,
  or keep the current per-use values with better teaching.
- How replacement-constructor results relate to access to the destination:
  replaced by it, delivered alongside it, or kept as the only value.
- The qualifications of any access produced, for example after first
  construction of a `final` or readonly place.

### Initial stopping guidance

Creating and routing this work item does not authorize analysis. A later
assignment begins it.

Do not promote findings, change current owners, archive this work item, or
create work item `034` without the separately required discussion, alignment,
and authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  authority, promotion, deferral, and closure.
- [Zax construction, replacement, and destruction](../../language/construction-and-destruction.md),
  the "Construct into an existing place with dot-equals", "Replacement
  results", and "Wrapper-owned contained reconstruction with dot-equals"
  sections - the current per-use values and the replacement-result contract.
- [Zax function invocation](../../language/function-invocation.md), the
  [opt-in result initialization](../../language/function-invocation.md#opt-in-result-initialization)
  and [required and discardable results](../../language/function-invocation.md#required-and-discardable-results)
  sections - the access produced by first construction and the rules for
  results a caller may drop.
- [Zax operator catalog](../../language/operator-catalog.md), the
  [replacement, optional, pointer, and function lifecycle forms](../../language/operator-catalog.md#replacement-optional-pointer-and-function-lifecycle-forms)
  table and [assignment and swap forms](../../language/operator-catalog.md#assignment-and-swap-forms)
  - the catalogued result role of each `.=` form and its associativity.

### Consequence-driven

Read only the smallest relevant sections when the concern reaches them:

- [Zax optional values](../../language/optional-values.md#contained-reconstruction-on-an-existing-wrapper)
  and [Zax variants](../../language/variants.md#-reconstructs-contained-state)
  for payload and wrapper access results.
- [Zax declarations and bindings](../../language/declarations-and-bindings.md#assignment-and-overload-selection)
  for the assignment-result precedent.
- [Zax qualifiers](../../language/qualifiers.md) when the qualifications of
  produced access matter.
- [Zax transfer stances](../../language/transfer-stances.md) when a produced
  value's stance matters.

### Audit-only

- [Archived `032`](../archive/work/032-dot-equals-construction.md), for how the
  current `.=` model and this deferral were reached, only when a provenance
  question cannot be answered from current owners.

## Working record

### How to read this record

The language maintainer and agent reviewed this concern on 2026-09-25 in the
same session that completed 032. The findings below are **aligned** for this
review scope but not yet promoted, and remain non-authoritative. They are
ordered for teaching: the rule first, then what it replaces, then boundaries.
Rejected options, evidence, and promotion consequences follow. No deferrals
remain.

Reading performed beyond the required set: the optional-value and variant
result statements, the variant `reset` result, the source and destination
discard section of function invocation, and a search of the operator owners
for result-acknowledgement rules (none found).

### Finding 1: every `.=` produces access to the place it constructed

One rule replaces the per-use values:

```zax
+++ final : ()(first : Buffer, second : Buffer) = {
  a := _.buffer .= first    // first construction: access to the buffer
  b := _.buffer .= second   // replacement: access to the same buffer
}

payload := optional .= value              // access to the new payload
wrapper := variant .= [{ .text = "ready" }]  // access to the wrapper
whole := destinationOptional .= sourceOptional  // access to the wrapper
```

| Use | Access produced |
| --- | --- |
| First construction of a member or result slot | The member or result slot |
| Replacement | The replaced place |
| Optional or named-variant payload construction | The new payload |
| Complete variant packet | The wrapper, because a zero-entry packet constructs absence and has no payload |
| Complete optional wrapper replacement | The wrapper |

The value no longer depends on whether the place was empty or live, so a reader
can predict what `x := place .= source` binds without applying the
first-construction rule.

Chains become consistent. `.=` is right-associative:

```zax
a .= b .= c
// `b .= c` produces access to `b` whether `b` was empty or live,
// so `a` is always built from `b`.
```

Previously, a live `b` with a resultful replacement constructor would have
built `a` from that constructor's result, such as a `Boolean`.

### Finding 2: `+++ replacement` is resultless, like `+++`

A replacement constructor declares no results, exactly as an ordinary
constructor does:

```zax
BufferOwner :: type {
  buffer : Buffer
  format : Format

  +++ replacement final : ()(nextFormat : Format) = {
    // Establish the complete replacement instance.
  }
}
```

Code that needs information about the transition asks before replacing:

```zax
willReuse := owner.buffer.canReuseFor(newFormat)
owner .= [{ newFormat }]
```

The previous example returned exactly that `Boolean` from the hook, computed
from `_.buffer.canReuseFor(nextFormat)`; a caller can ask the same question
first.

Consequences:

- `retained := owner .= [{ newFormat }]` now binds access to `owner`.
- A replacement constructor that declares results is an error.
- Two rules added in 032 disappear: a required hook result dropped by a `.=`
  routing group, and the guidance to declare replacement results with `#`.
- Replacement and construction now match: neither lifecycle hook produces a
  result, and `.=` supplies the expression value.

Accepted cost: a result that can only be known during the transition, such as
an allocation outcome inside the hook, has no channel back to the caller. The
maintainer judged the uniform rule more valuable than that flexibility.

### Finding 3: the access carries the path's own qualifications, never more

The access produced by `.=` has the qualifications of the path used to reach
the place. `.=` never grants more authority than that path had. This follows
the existing `reset` rule, which returns wrapper access "without increasing
authority".

```zax
make final : (
  result : Item readonly
)() = {
  view := result .= source
  // `view` is readonly access, matching the result's path.
}
```

First construction needs no replacement permission, but the access it produces
does not gain any: access to a `final` place still cannot be used to replace it.

### Finding 4: access returned by `.=` and similar protected forms is discardable

The general rule says an unmarked result must be captured, routed, or
explicitly discarded. Read literally, that would make ordinary statements
errors:

```zax
a = b              // intrinsic `=` returns writable access to `a`
first = second     // generated copy `=` returns readonly copy access
reset message      // returns access to the wrapper
place .= source    // returns access to `place`
```

The access returned by these protected forms is discardable, as if declared
with `#`. Each line above is a complete statement. This makes existing practice
explicit; no owner previously stated it.

The rule covers `.=`, intrinsic `=`, generated copy assignment, and `reset`.
Other protected forms that return access should be checked during the dry run
and added if they follow the same pattern.

### Teaching obligations for promotion

1. **"`.=` produces access to what it constructed"** is taught once, in the
   construction owner's `.=` overview, with the table from Finding 1.
2. **`+++ replacement` has no results.** Teach the ask-first pattern with the
   buffer example, not the removed resultful form.
3. **Chains** are shown with one example and the sentence that `a` is always
   built from `b`.
4. **Discardable access** is stated where assignment results are taught, so a
   reader never wonders why `a = b` is a valid statement.
5. **Operator result advice** (Finding 5) sits beside the discardable-access
   rule in the operators owner, so authors see the protected behavior and
   their own choice together.

### Rejected

- **Keep the per-use values and teach them better.** Rejected: it leaves the
  state-dependent value and the chain hazard.
- **Access plus trailing replacement results.** Rejected: more flexible, but
  it keeps multiple-result `.=` expressions, needs label routing to recover
  the hook results, and leaves the required-result rules in place. The
  maintainer's reasoning: flexible is not always better.
- **`.=` produces no value.** Rejected: it contradicts current result-slot
  access and forbids chains.

### Finding 5: user-defined operators already have the tool they need

No language change. A user-defined operator's author chooses, with the existing
`#` result marker, whether its result may be ignored:

```zax
operator binary '+=' final : (
  total # : MyValue writable &
)(
  rhs : MyValue
) = { … }

total += amount    // fine: the result is discardable

operator binary '+' final : (
  sum : MyValue
)(
  rhs : MyValue
) readonly = { … }

a + b              // error: the sum is required and unused
```

General advice for authors, to be taught next to Finding 4:

- **Mutating operators** such as `=` and `+=`: declare the result with `#`. The
  effect is the point, and the result is only a convenience for chaining.
- **Operators whose result is meant to be used**, such as `+` returning a
  temporary: leave the result required, so an unused result is diagnosed as
  the likely mistake it is.

This was first recorded as a deferral about a missing single-expression
discard form. The maintainer judged it an advisory concern only: authors
already have the marker, so no discard form is needed.

### Evidence retained

Values before this review:

| Use | Value before |
| --- | --- |
| First construction | Access to the new value |
| Replacement | Only the replacement constructor's declared results; nothing for a resultless hook or generated fallback |
| Optional or named-variant payload | Access to the new payload |
| Complete variant packet | Access to the wrapper |
| Complete optional wrapper replacement | Not stated |

Precedent: intrinsic `=` returns writable access to its destination; generated
copy assignment returns readonly `copy` access to its receiver; `reset` returns
wrapper access without increasing authority.

### Promotion consequences

The dry run will sweep for every mention. Known affected places:

| File | Change |
| --- | --- |
| `language/construction-and-destruction.md` | `.=` overview: replace the "currently depends on its use" paragraph with Finding 1; rewrite "Replacement results" as resultless replacement with the ask-first example; remove the routing-group hook-result paragraph and its diagnostic; boundaries: replace the resultful/resultless replacement bullets and the deferral bullet |
| `language/function-invocation.md` | Remove the `.=` group rule that points to replacement results; state discardable access where result acknowledgement is taught, or link to it |
| `language/operator-catalog.md` | Result-role column for every `.=` row; complete optional wrapper row |
| `language/declarations-and-bindings.md` | Remove "A replacement constructor may return additional results"; note discardable access in the assignment section |
| `language/optional-values.md` | Complete-wrapper `.=` produces wrapper access |
| `language/variants.md`, `language/qualifiers.md`, `language/terms.md` | Check result statements and the replacement-constructor entry |
| `language/operators.md` | Discardable access from protected forms, with the operator result advice from Finding 5 |

## Dispositions and promotion dry run

### Result: PASS

Dry run performed 2026-09-25 after the maintainer authorized a dry run and, on
PASS, immediate promotion. Every finding has one lasting owner in the current
layout. No file, directory, or index changes are needed, and no deferral
remains.

### Structure proposal

Retain the current layout. `project/README.md` and the raw index need no
change for promotion.

### Ownership map

| Finding | Lasting owner | Other files that summarize or link |
| --- | --- | --- |
| 1. `.=` produces access to what it constructed | Construction owner, `.=` overview | Operator catalog rows; function invocation result slots; optional values (complete wrapper) |
| 2. `+++ replacement` is resultless | Construction owner, replacement section (retitled) | Declarations (remove the "additional results" sentence); function invocation (remove the group rule); `terms.md` |
| 3. Path qualifications, never more | Construction owner, `.=` overview | Function invocation result slots |
| 4. Discardable access from protected assignment forms | `operators.md`, assignment and mutation boundaries | Declarations assignment section, operator catalog assignment forms, integer operator catalog, function invocation required and discardable results |
| 5. Operator result advice | `operators.md`, same subsection as Finding 4 | None |

The sweep found two more protected forms that return writable access:
ordinary intrinsic compound assignment and pre-increment/decrement. Finding 4
covers them under its "same pattern" clause.

### Adjacent finding: post forms and reporting forms (resolved)

Two protected integer forms return something other than access, so Finding 4
does not cover them:

```zax
myValue++           // post form: returns the previous value by copy
total +!%= delta    // reporting compound: returns its report
```

The dry run captured this for maintainer decision. The maintainer decided after
promotion, and the decision was promoted as a follow-up:

- **The post form's copy is required.** A post form exists to capture the
  previous value; the pre form is the statement form and returns discardable
  access. The post form's common use as a statement was likely inherited
  C-family habit rather than need.
- **Reports are required.** Choosing a reporting form states that the code will
  inspect the report. Every policy also has a non-reporting spelling (`+=`
  panics, `+%=` wraps, `+|=` saturates) for code that wants no report.
- These are the intrinsic choices. A type that declares its own `++` or
  reporting operators may choose differently; documentation samples show the
  common pattern.

The agent's first suggestion, a discardable post-form copy, was rejected. The
agent's first reporting example, `+?=`, was the optional form rather than a
reporting form and is corrected above.

### Teaching plan

Rejected forms are not taught. The only reference to results on replacement
constructors is the reason for resultless replacement: it lets every `.=`
produce the same value. The ask-first example shows how to obtain transition
information. The resultful `BufferOwner` example, the routing-group
hook-result rule, and the `#` guidance for replacement results are removed
rather than presented as former forms.

### Exact promotion change set

1. `language/construction-and-destruction.md`: `.=` overview value paragraph;
   replacement-results section rewritten as resultless replacement; the
   operator-result sentence in candidate selection; the generated-fallback
   result sentence; diagnostics; boundaries.
2. `language/function-invocation.md`: result-slot access qualifications;
   remove the `.=` group hook-result rule; handoff in required and
   discardable results.
3. `language/operators.md`: new subsection on discardable access and operator
   result advice.
4. `language/operator-catalog.md`: `.=` value row; assignment-forms
   discardable sentence.
5. `language/declarations-and-bindings.md`: remove the additional-results
   sentence; discardable-access sentence in the assignment section.
6. `language/optional-values.md`: complete-wrapper `.=` produces wrapper access.
7. `language/integer-operator-catalog.md`: discardable note for compounds and
   pre forms.
8. `language/terms.md`: replacement-constructor entry states no results.

### Promotion log

Promoted 2026-09-25 under the maintainer's authorization to promote on PASS.
Nothing was staged, committed, or archived.

The change set matched the dry run. Where each finding landed:

- **Finding 1 and Finding 3:** the construction owner's `.=` overview now
  teaches access to what was constructed, with qualifications, chains, and the
  complete-wrapper cases. Function invocation's result slots state the
  qualification rule; the operator catalog row and the optional-values
  complete-wrapper paragraph state the produced access.
- **Finding 2:** "Replacement results" became "Replacement constructors have no
  results", with the resultless `BufferOwner` example and the ask-first
  pattern. The only mention of results is the reason resultless replacement
  was chosen. The routing-group hook-result paragraph and its function
  invocation rule were removed without describing them as former forms. The
  declarations owner, `terms.md`, diagnostics, and boundaries were updated.
- **Finding 4 and Finding 5:** `operators.md` gained "Discardable access from
  assignment forms", covering `=`, generated copy assignment, intrinsic
  compounds, pre-increment, `reset`, and `.=`, followed by the author advice.
  Short handoffs were added in function invocation, the declarations
  assignment section, the operator catalog, and the integer operator catalog.

Validation: all local links and anchors resolve; no current owner teaches
replacement-constructor results or the per-use `.=` values.

**Follow-up promotion (2026-09-25), authorized after maintainer decision on the
adjacent finding:**

- `language/integer-operator-catalog.md`: post forms return a required copy,
  with an example steering statements to the pre form; reporting compounds and
  reporting increment/decrement return a required report, with an example
  naming the non-reporting alternative.
- `language/operators.md`: one paragraph saying the intrinsic integer forms
  follow the advice, and that a type's own operators choose their results the
  same way.
- `language/operator-catalog.md` "Fixity and adjacency" and
  `language/source-structure.md` "Symbolic operators, whitespace, and
  adjacency": bare expressions were changed to captures or statement forms, so
  the samples show common usage rather than forms that would fail as
  statements. The phrase example uses the established
  `alias type type of` declaration.

No open items remain for closure.

### Closure

Closed and archived 2026-09-25 with the maintainer's authorization. Every
finding is promoted or rejected; no deferral remains. The maintainer ruled that
catalog fragments listing forms, such as a bare `myLeft == myRight`, need not
be complete statements, so no teaching-debt entry was recorded for them. Work
item 034 was not created at closure.
