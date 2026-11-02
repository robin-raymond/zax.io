# Raw registry: unsafe and panic analysis controls

| Field | Value |
| --- | --- |
| Status | Raw provisional registry / non-authoritative |
| Audience | Language maintainers and future work defining unsafe assertions, unsafe permissions, runtime-check controls, or panic categories |
| Applies To | Discovery and maturity of proposed `unsafe` and panic category identifiers |
| Owns | The centralized provisional inventory of category names, unnamed category pressure, source occurrences, and disposition state |
| Does Not Own | Accepted unsafe syntax, accepted panic-control syntax, domain behavior, intent acknowledgements, diagnostic identifiers, or compiler implementation |
| Source / Provenance | Current conceptual owners, `project/raw/analysis-controls.md`, reached raw safety input, legacy compiler-directive evidence, and work item `028` |

## Purpose and reading posture

This file prevents analysis-control names and unnamed registry obligations from
surviving only in scattered examples. It is a discovery and disposition ledger,
not an accepted language registry.

Each behavior remains owned by its domain document. The future analysis-control
owner decides exact syntax, category kinds, extension provenance, selected
language-contract requirements, and promotion into an accepted registry.

The current accepted **intent acknowledgement** registry is separate and owned
by
[Zax intent acknowledgements](../../language/intent-acknowledgements.md#current-category-registry).
`intent<...>` confirms defined but suspicious behavior; it supplies no unsafe
fact and disables no runtime check. Do not duplicate its categories here.

The root legacy compiler-directive page formerly contained historical panic
names; they and their legacy meanings are preserved below under
[legacy panic identifiers](#legacy-panic-identifiers-requiring-disposition).
They are evidence to disposition by value, not the current registry and not
authority for syntax or behavior.

## Registry maturity

Entries use these local maturity labels:

- **Aligned candidate**: aligned in active numbered work but not promoted.
- **Accepted domain entry**: promoted behavior whose centralized
  analysis-control registry placement remains future work.
- **Provisional**: preserved evidence or a candidate name requiring focused
  review.
- **Illustrative**: appears in current conceptual prose only to demonstrate a
  mechanism; its spelling is not accepted.
- **Unassigned**: a domain requires a category, but no identifier is yet
  proposed.
- **Legacy**: historical evidence requiring value-based disposition.
- **Superseded**: replaced by a named mechanism; retained only so its evidence
  and replacement remain discoverable.

An occurrence in source does not raise its maturity.

## Unsafe-category inventory

Unsafe categories identify an exact assertion or permission. They do not disable
a defined panic check merely by sharing a failure condition.

| Identifier | Maturity | Claim or permission | Behavior owner and current evidence |
| --- | --- | --- | --- |
| `union-lens-validity` | Accepted domain entry | Assert that the current backing representation of a `union unsafe` is valid for the requested lens where the selected contract does not prove it | [Zax unions](../../language/unions.md#unsafe-union) and [safety and analysis](../../language/safety-and-analysis.md#unsafe-union-lens-validity) |
| `opaque-construction` | Accepted domain entry (name may be revised; place-naming syntax open) | Assert that the enclosed operation constructs the named empty member or result slot; the member receives no automatic construction | [Zax construction, replacement, and destruction](../../language/construction-and-destruction.md#construction-by-an-opaque-operation), [declarations and bindings](../../language/declarations-and-bindings.md#low-level-initialization-belongs-in-a-constructor), and [raw analysis controls](analysis-controls.md#lifecycle-state-assertions) |
| `manual-member-construction` | Superseded (032) | Replaced by explicit construction control plus `opaque-construction` | [Raw analysis controls](analysis-controls.md#known-construction-derived-category-pressure) |
| `manual-member-destruction` | Provisional | Suppress generated member destruction because another operation ends or transfers the member lifetime | [Raw analysis controls](analysis-controls.md#known-construction-derived-category-pressure); construction owner |
| `construction-path-complete` | Superseded (032) | Unknown construction state is an error to restructure, not a fact to assert | [Raw analysis controls](analysis-controls.md#known-construction-derived-category-pressure) |
| `destruction-path-complete` | Provisional | Treat a member as destroyed or dispositioned on every applicable path despite incomplete proof | [Raw analysis controls](analysis-controls.md#known-construction-derived-category-pressure); construction owner |
| `construction-at-most-once` | Superseded (032) | Unknown construction state is an error to restructure, not a fact to assert | [Raw analysis controls](analysis-controls.md#known-construction-derived-category-pressure) |
| `destruction-at-most-once` | Provisional | Trust that apparently overlapping paths cannot end one member lifetime twice | [Raw analysis controls](analysis-controls.md#known-construction-derived-category-pressure); construction owner |
| `partial-instance-access` | Provisional | Permit bounded access to a current instance that is not fully constructed | [Raw analysis controls](analysis-controls.md#known-construction-derived-category-pressure); construction and safety owners |
| `partial-instance-escape` | Provisional | Permit an incomplete current instance or access path to escape or become externally observable | [Raw analysis controls](analysis-controls.md#known-construction-derived-category-pressure); construction and safety owners |
| `lifetime-escape` | Provisional | Assert that an access path remains valid across a boundary whose lifetime proof is incomplete | Used illustratively by [safety and analysis](../../language/safety-and-analysis.md#unproved-but-valid), [optional values](../../language/optional-values.md#presence-proof-and-postfix-access), and [raw analysis controls](analysis-controls.md#permission-regions) |
| `replacement-alias` | Provisional | Permit replacement despite unresolved possible aliasing between the destination and a right-hand operand | Used by [optional values](../../language/optional-values.md#presence-proof-and-postfix-access), [intent acknowledgements](../../language/intent-acknowledgements.md#intent-is-not-unsafe), and [raw analysis controls](analysis-controls.md#known-construction-derived-category-pressure) |
| `terminal-reconstruction` | Provisional | Permit reconstruction of a member during enclosing destruction where required analysis cannot prove the lifecycle sequence | [Raw analysis controls](analysis-controls.md#known-construction-derived-category-pressure); construction owner |
| `optional-presence` | Provisional | Assert that the exact optional boxed lifetime required by an access is present despite incomplete proof | Used illustratively by [optional values](../../language/optional-values.md#presence-proof-and-postfix-access) and [raw analysis controls](analysis-controls.md#permission-regions) |
| `replacement-member-successor` | Illustrative | Deliberately reach the completely established successor resident instance through a continuing member place after replacement | Used illustratively by [safety and analysis](../../language/safety-and-analysis.md#defined-unsafe-permission); lifetime and replacement owners |
| `possible-nothing-write` | Provisional | Accept a pointer handoff or operation whose downstream write may reach compiler-provided Nothing backing without asserting pointer presence | [Raw analysis controls](analysis-controls.md#proven-errors-versus-suspected-flow) and [Nothing instances](../../language/nothing-instances.md#possible-write-flow-needs-a-future-unsafe-boundary) |
| `possible-nothing-read-trap` | Provisional | Accept a pointer handoff or operation whose downstream read may reach trapping Nothing backing | [Raw analysis controls](analysis-controls.md#proven-errors-versus-suspected-flow) and [Nothing instances](../../language/nothing-instances.md#possible-write-flow-needs-a-future-unsafe-boundary) |

### Illustrative spellings that are not suitable registry entries

`unsafe<lifetime>{...}` appears once in
[safety and analysis](../../language/safety-and-analysis.md#false-unsafe-claims)
to show that unsafe cannot revive a known-ended lifetime. `lifetime` is too broad
to identify one responsibility and is not a candidate registry identifier.

`unsafe pliable`, `unsafe cast`, `unsafe from`,
`unsafe outer cast`, `unsafe via`, and `unsafe vacate` are domain source forms,
not category identifiers in the candidate `unsafe<category>{...}` registry.

### Unassigned unsafe pressure

The following reached concerns still need focused review and exact identifiers
or an explicit decision that another mechanism owns them:

- source-result and destination-slot single consumption;
- operations on moved-from or terminal values when validity is unproved rather
  than merely suspicious;
- returned-reference escape from temporaries or opaque origins;
- non-overlap assertions for structural transformation;
- opaque raw-pointer allocation-root, provenance, alignment, sole-disposition,
  reset, overwrite, and vacate facts;
- unsafe recovery of type-erased owners or observers;
- union-reference escape when later overlay writes may invalidate the requested
  lens; and
- optional or debug-only unchecked variant access if future work wants a local
  source form distinct from disabling its registered panic check.

Complete construction and at-most-once establishment of result slots and
explicitly controlled members no longer need an unsafe category: the
construction owner's first-construction rule makes unknown state an error to
restructure.

Do not assign these identifiers merely to complete the table. Their future owner
must distinguish an assertion from a defined unsafe permission and from a
known-invalid operation.

## Panic-category inventory

A panic category names one defined runtime failure whose required check can be
controlled independently. Unless its domain says otherwise:

- the default contract performs the check and panics on violation;
- static proof may eliminate the check;
- a statically proved violation remains an error;
- disabling the category promises that the condition cannot occur and permits
  check removal; and
- violation while disabled has undefined consequences.

Reporting or optional operations retain checks needed to produce their declared
results. Disabling a panic category does not turn required source into a
reporting form.

| Identifier | Maturity | Checked condition | Domain and disabled-check promise |
| --- | --- | --- | --- |
| `never-path-reached` | Accepted domain entry | A `[<never>]` branch, case, or point is reached | [Core flow control](../../language/core-flow-control.md#branch-hints-and-path-promises) and [switch](../../language/switch.md#case-hints-and-promises). Enabled by default; disabling promises the path is unreachable. |
| `always-path-missed` | Accepted domain entry | An `[<always>]` branch or case is not the one taken | [Core flow control](../../language/core-flow-control.md#branch-hints-and-path-promises) and [switch](../../language/switch.md#case-hints-and-promises). Enabled by default; disabling promises the marked path is always taken. |
| `inactive-variant-access` | Accepted domain entry | Direct access names a variant alternative that is not active because another name or absence is current | [Zax variants](../../language/variants.md#presence-and-named-access-answer-different-questions) and [safety and analysis](../../language/safety-and-analysis.md#checked-variant-access). Enabled by default; disabling promises the requested alternative is active and does not apply to `.=` or a case-routed binding. |

### Unassigned required panic categories

Current conceptual and raw owners require independently controllable categories
for these conditions but have not accepted exact identifiers:

- required allocation request failure;
- required fixed-width arithmetic result not representable;
- required division or remainder with a zero divisor;
- intrinsic array element index outside bounds;
- array splice endpoint or order outside bounds;
- requested array logical length outside its admitted range;
- array initializer cardinality mismatch;
- invalidated slice use;
- foreign slice origin supplied to mutation;
- iterable exact-count promise mismatch;
- array backing-extent overflow;
- required array backing allocation failure;
- pointer arithmetic requiring an ordinary target when the pointer reaches
  Nothing backing;
- access to a compiler-prepared member with no readable representation;
- access under a trapping Nothing policy;
- write to compiler-provided Nothing backing where a target-supported trap is
  part of the selected contract; and
- invocation of an unavailable function representation through a generated
  panic path.

The array conditions are listed by
[arrays and slices](../../language/arrays-and-slices.md#required-reporting-and-unchecked-behavior)
and [raw analysis controls](analysis-controls.md#panic-registry-pressure-from-intrinsic-arrays).
Nothing-instance conditions are preserved by
[Nothing instances](../../language/nothing-instances.md) and
[raw analysis controls](analysis-controls.md#nothing-instance-diagnostics-and-controls).
Allocation, arithmetic, and zero-divisor contract pressure is preserved by
[raw safety](safety.md#panic-boundary) and
[raw analysis controls](analysis-controls.md#general-runtime-check-contract-pressure).

### Legacy panic identifiers requiring disposition

The legacy compiler-directive registry contained these names, with the legacy
default state in parentheses and its legacy meaning:

- `out-of-memory` (enabled): memory was requested but insufficient memory
  existed; allocation failure normally panicked rather than returning a vacant
  pointer;
- `intrinsic-type-cast-overflow` (enabled): an intrinsic value overflowed during
  `as` to a type with fewer bits;
- `string-conversion-contains-illegal-sequence` (enabled): a string literal
  conversion found an illegal character sequence;
- `not-all-pointers-destructed-during-allocator-cleanup`: allocator cleanup ran
  while instances it allocated were not all destructed;
- `impossible-switch-value`, `impossible-if-value`, and `impossible-code-flow`
  (enabled): a value or path marked impossible by `[<never>]` or `[<always>]`
  occurred. **Superseded** by `never-path-reached` and `always-path-missed`
  below;
- `lazy-already-complete` (enabled): a `lazy` function was called after its
  final `return`; and
- `value-polymorphic-function-not-found` (enabled): a value-polymorphic
  function was called but none of its precondition checks succeeded.

None is promoted by appearing here. Future analysis-control work must compare
each name and behavior with current allocation, scalar, string, pointer, flow,
callable, panic, and safety design, then retain, rename, split, reject, or
supersede it explicitly. The legacy `reference-from-pointer-to-nothing` and
`pointer-to-nothing-accessed` entries are already marked superseded by current
Nothing-instance direction and must not be revived as one collapsed condition.

## Occurrence audit

The initial live-source audit on 2026-09-20 searched:

- exact `unsafe<...>` examples;
- prose and tables naming unsafe categories;
- `panic category`, `panic-category`, registry, disablement, and directive
  wording;
- exact `intent<...>` examples; and
- the legacy panic registry for value-based disposition.

Exact `unsafe<...>` examples currently occur in:

- `language/safety-and-analysis.md`;
- `language/unions.md`;
- `language/optional-values.md`;
- `language/intent-acknowledgements.md`; and
- `project/raw/analysis-controls.md`.

Panic-category obligations without a current registry occur in:

- `language/variants.md`;
- `language/arrays-and-slices.md`;
- `language/construction-and-destruction.md`;
- `language/nothing-instances.md`;
- `language/pointers-and-arenas.md`;
- `language/safety-and-analysis.md`;
- `project/raw/analysis-controls.md`;
- `project/raw/array-storage-strategies.md`; and
- `project/raw/safety.md`.

The accepted intent registry covers current accepted intent names. One live
interior-pointer example uses provisional
`intent<inner-pointer-tracks-replacement>{...}` (formerly
`anchored-pointer-tracks-replacement`) while explicitly stating that
its exact category identifier remains unsettled. That occurrence is intentional
future intent-registry pressure, not an accepted entry and not an unsafe or panic
category.

Archived work preserves provenance but is not a live registry source. A future
audit may inspect it when a current category's provenance or regression cannot
be resolved from live owners and raw inputs.

## Addition and retirement rules

Add a registry entry when a current work item or live owner establishes a
concrete unsafe responsibility or independently selectable runtime panic
condition. Record:

- the exact or provisional identifier;
- maturity;
- assertion, permission, or disabled-check meaning;
- behavior owner;
- false-claim or disabled-check consequence;
- representative live occurrences; and
- the pressure that must disposition it.

Do not add broad aliases for convenience, convert intent categories into unsafe
categories, or import a legacy panic name without current semantic review.

When accepted analysis-control owners exist, move each surviving entry into its
one lasting registry and domain owner. Retire this file after every provisional,
unassigned, and legacy entry has been promoted, renamed, rejected, superseded,
or moved to another indexed live destination.
