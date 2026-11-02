# Raw input: analysis controls

| Field | Value |
| --- | --- |
| Status | Raw future-work input / non-authoritative |
| Audience | Future work defining unsafe controls, static-analysis contracts, diagnostics, lints, or language-version behavior |
| Applies To | Future source syntax and category design for narrow semantic permissions and assertions |
| Owns | Preservation of construction-derived category behavior, concrete examples, the contiguous non-scoping unsafe-enclosure candidate, analysis provenance, remaining syntax, activation pressure, and retirement criteria |
| Does Not Own | The centralized provisional category inventory ([analysis-control registry](analysis-control-registry.md)); current proof, unsafe-permission, redundant-control, and lint boundaries ([safety and analysis](../../language/safety-and-analysis.md)) |
| Source / Provenance | Work items `005`, `006`, `007`, `012`, `015`, `032`, and `034`; construction/lifecycle, invocation/result, core-flow, optional proof/alias, panic-category, and intent-naming pressure |

## Reading posture

[Zax construction, replacement, and destruction](../../language/construction-and-destruction.md)
identifies places where compiler analysis may reject a valid low-level program.
This file preserves the future source-control and portability questions without
making provisional spellings current language syntax.

Current general prove/assert/permit/reject behavior and language-contract
evolution are owned by
[Zax safety and analysis](../../language/safety-and-analysis.md). The examples
below preserve candidate categories and source forms, not a competing current
definition.

It is not required for ordinary construction or qualifier use.

Provisional names, unnamed registry obligations, live occurrences, and legacy
panic identifiers are centralized in the
[raw analysis-control registry](analysis-control-registry.md). This file retains
the reasoning and examples that give those entries meaning; it is not a second
registry.

## Semantic controls are not broad unsafe mode

A future semantic control must:

- use the literal `unsafe`;
- identify the exact unsafe category;
- name the resolved member, binding, expression, or operation where applicable;
- apply at the narrowest useful source location;
- affect only the stated analysis or generated behavior;
- permit attached `///` documentation explaining the claim; and
- have no required runtime check.

Compiler debugging modes may instrument a claim and panic when misuse is
detected. Such instrumentation is tooling, not a language guarantee.

## Intent acknowledgement is separate

[Zax intent acknowledgements](../../language/intent-acknowledgements.md) now
owns defined but suspicious source such as deliberate terminal-source reuse.
Intent confirms an interpretation the compiler already understands. It does not
accept proof responsibility and cannot make an invalid operation valid.

This raw input retains only semantic permissions or assertions that change what
the compiler may trust about an unproved lifetime, alias, construction, or other
required property. Lint suppression remains separate from both.

The maintainer intends a later collective review of category names. The
partial categories `grant-redirected-selection`, `injected-partial-exposure`,
`uninvoked-hook-role`, and `application-closed-storage` entered the current
intent registry on that understanding. They avoid a `partial-` prefix because
`partial-enum-selection` and `partial-variant-selection` already use "partial"
to mean incomplete.

## Permission regions

Some operations are valid only when the programmer accepts responsibility for a
specific analysis boundary.

Illustrative syntax:

```zax
/// The callback consumes the view synchronously and stores nothing.
unsafe<lifetime-escape>{
  myCaptureBeyondLifetime(myType.view())
}
```

The angle-bracketed category is placeholder notation, not accepted syntax.

The aligned source shape is a contiguous, non-scoping unsafe enclosure, not a
keyword followed by a scope. Declarations retain their surrounding scope:

```zax
unsafe<lifetime-escape>{
  x : Integer
  myCaptureBeyondLifetime(myType.view())
}

x = 5
```

One enclosure may acknowledge several exact categories without becoming broad
unsafe mode:

```zax
unsafe<optional-presence, lifetime-escape, replacement-alias>{
  reset optionalValue
}
```

Every category applies only its named permission. Future grammar must validate
the contiguous opener, required whitespace after each comma separator,
documentation attachment, nesting, and source reflection.

## Lifecycle-state assertions

An opaque operation may establish a member state that ordinary analysis cannot
observe. Current construction design has one such assertion, made at the
establishing operation:

```zax
+++ final : ()() = {
  unsafe<opaque-construction>{ opaqueInitialize(_.prop2) }
}
```

The enclosure both takes `prop2` out of automatic construction and asserts that
the enclosed operation constructs it. Its category name is accepted for now and
may be revised. The syntax by which the enclosure names the places it
establishes is open: a call receiving two empty places must not silently
establish both.

For construction, the conservative first-construction rule has no unsafe
override: when the compiler cannot tell whether a place is constructed, the
code must be restructured. For destruction, the future mechanism must still
distinguish:

- suppressing a generated destruction;
- asserting that an opaque operation ended a lifetime;
- asserting destruction on every normal path; and
- asserting that apparently overlapping calls cannot end one lifetime twice.

A warning-suppression mechanism is insufficient because these claims may change
generated code or the compiler's semantic state.

## Known construction-derived category pressure

| Provisional identifier | Claim or permission |
| --- | --- |
| `opaque-construction` | Assert that the enclosed operation constructs the named empty member or result slot; the member receives no automatic construction |
| `manual-member-destruction` | Suppress generated member destruction because another operation ends or transfers the member lifetime |
| `destruction-path-complete` | Treat a member as destroyed or dispositioned on every applicable path despite incomplete proof |
| `destruction-at-most-once` | Trust that apparently overlapping paths cannot end one member lifetime twice |
| `partial-instance-access` | Permit bounded access to a current instance that is not fully constructed |
| `partial-instance-escape` | Permit an incomplete current instance or access path to escape or become externally observable |
| `lifetime-escape` | Permit an access path to cross a boundary whose lifetime cannot be proved sufficiently bounded |
| `replacement-alias` | Permit replacement despite unresolved possible aliasing between the destination and a right-hand operand |
| `terminal-reconstruction` | Permit reconstruction of a member during enclosing destruction |

`manual-member-construction`, `construction-path-complete`, and
`construction-at-most-once` are superseded. Every explicit construction step
suppresses automatic construction; opaque establishment
is `opaque-construction`; and unknown construction state is an error to
restructure rather than a fact to assert.

Invocation and result work adds future proof categories without establishing
their final identifiers:

- completing every result slot on every normal path;
- constructing each result slot at most once per lifetime;
- preventing operations unavailable in moved-from or terminal state;
- preventing a returned reference from escaping a temporary;
- consuming each source-result and destination slot at most once; and
- preserving diagnostic provenance for the contract or analysis that rejected a
  call.

Ordinary defined reuse after accepted `last` is an intent error and uses
`intent<terminal-source-reuse>{...}`. Unsafe analysis control is needed only
when a required valid lifetime, alias, or operation property cannot be proved.

These names identify future-work concerns. They are not accepted keywords. Their
central maturity and occurrence inventory is maintained in the
[raw analysis-control registry](analysis-control-registry.md#unsafe-category-inventory).

An unsafe assertion may override incomplete proof or assert the result of an
opaque operation. It cannot make a known-ended lifetime live again.

## Flow-derived proof pressure

Core flow control adds cross-cutting proof pressure without accepting any
override syntax:

- **Optional presence and dereference.** Access through conditionally live
  storage, including a postfix optional dereference, requires proof that a live
  value exists on that path; the proof may come from a preceding presence test,
  construction, earlier control flow, or a recognized presence contract, and need
  not be immediately adjacent. A `&&` proof is only a proof when the right operand
  is unambiguously exactly `Boolean` and the node is therefore the protected
  short-circuit operation.
- **Reachability and accidental nontermination.** Obvious unreachable code and an
  obviously non-terminating loop, such as one whose empty body never changes its
  condition, are diagnosable without a complete termination proof.
- **Loop-sensitive result completion.** Result and instance completeness must
  hold on every normal path a loop, branch, or transfer produces.
- **Branch-dependent construction.** A conditional expression's arms may
  construct or select differently and must converge to one statically usable
  result shape per complete operation.
- **Selection entry paths.** A runtime case body may be reached by a successful
  test, by a positional `default`, by `continue` after re-testing, or directly
  by `goto`; only facts valid on every incoming path are available in the body.
  Direct entry cannot inherit optional presence, pointer validity,
  initialization, or range facts from a bypassed test.
- **Selection reachability and cycles.** Ordered tests may prove a later clause
  unreachable, while backward `continue` or `goto` may create an intentional
  cycle. Future analysis must distinguish language-defined proof from assumptions
  about user-defined operator purity or algebra. A reachable incoming transfer
  makes an ordinarily shadowed body reachable; a self-edge inside otherwise dead
  source does not bootstrap reachability.
- **Selection segments.** Transfer-only cases are skipped by ordered searches.
  Positional defaults delimit test segments, and `continue` may start a later
  search. Proof, completeness, and reachability are evaluated for each reachable
  search entry rather than only initial switch entry.
- **Enum coverage.** Declared-member coverage and complete reachable-backing
  coverage are distinct. `default` may satisfy the latter without silently
  satisfying omitted declared members. Duplicate-valued aliases represent one
  distinct value, and arbitrary predicates contribute only when their covered
  domain is provable. Only reachable tests contribute member coverage: `goto`
  may make a body reachable while bypassing its test, and a case after a
  positional default does not count for a search that cannot reach it.
- **Obsolete proof assertions.** A smarter compiler may make a previously required
  proof override redundant. Whether that redundancy is advisory or an error
  follows the selected static-analysis contract; see
  [Contract-dependent redundant controls](#contract-dependent-redundant-controls).

These are future analysis concerns. A semantic assertion may override incomplete
proof, while lint suppression may not change semantics; the two mechanisms remain
distinct as described below.

### Optional proof and invalidation pressure

[Zax optional values](../../language/optional-values.md) requires static proof of
the exact live boxed lifetime before postfix access. Proof may come from a
presence test, construction, earlier flow, or another recognized contract.

Reset, packet construction, complete-wrapper replacement, terminal transfer, or
another lifetime-ending operation invalidates earlier payload proof and aliases.
An assertion may accept responsibility when the compiler cannot prove valid
source. The assertion adds no required runtime handle or check. If its presence,
alias, or lifetime claim is false, behavior is undefined; debug instrumentation
may detect the violation and panic without making that check a language
guarantee. It cannot legalize unconditional known absence, a known-ended
lifetime, direct boxed destruction, readonly mutation, immutable mutation, or
replacement of a final place.

Exact same-object source is not a universal error:

```zax
value = value
value foos value
```

A protected lifecycle operation may define alias-safe behavior, while an
arbitrary user phrase may intentionally accept the same object twice. Future
analysis should use operation contracts or lint policy rather than a blanket
semantic rejection.

## Partial access and publication are distinct

A helper may use an incomplete current instance without retaining it:

```zax
+++ final : ()() = {
  _.first.+++()
  initializeRemaining(_)
}
```

Publishing the same instance is stronger:

```zax
+++ final : ()() = {
  registerGlobally(_)
}
```

The second call may retain an access path, notify subscribers, or reenter through
another path. A future permission for bounded access must not imply permission
for escape.

## Language contracts and compiler analysis

Future source may select a language-contract version whose required analysis
defines portable validity. Selection should be optional for small snippets and
may come from:

1. source;
2. project configuration;
3. build invocation or toolchain configuration; or
4. the compiler's stable default.

Omitting selection means accepting the active environment's contract, not
claiming compatibility with every historical and future version.

Analysis identifiers need visible provenance:

- language-contract identifiers;
- compiler or vendor experimental identifiers; and
- community identifiers implemented by multiple tools but not standardized.

An unknown standard identifier under a supported contract is an error. An
unknown lint extension may be ignored or reported according to policy. An
unknown unsafe semantic extension may be accepted only when the compiler
independently proves the code valid without it; otherwise the compiler reports
that the required extension is unsupported.

## Contract-dependent redundant controls

The mental model for any analysis the selected contract mandates is
prove-or-narrowly-assert:

> For every property mandatory under the selected language contract, Zax attempts
> to prove the operation valid. If proof cannot be established, the operation is
> rejected unless a narrow semantic assertion explicitly accepts responsibility
> for an unproved but valid boundary. An assertion cannot make a known violation
> valid.

Redundant-control validity is not absolute; it follows the selected contract:

| Situation | Severity of the now-redundant control |
| --- | --- |
| The contract does not require proof of that case | Implementation improvement may make the assertion redundant, but it is at most advisory |
| The selected contract requires that case to be recognized as safe | The redundant assertion may be a hard error |
| A compiler demands an assertion for a contract-mandated passing case | That compiler is nonconforming |
| Source adopts a later contract that newly mandates the proof | Source may need to remove a control that was valid under the earlier contract |

Source validity therefore follows the selected contract, not merely the executable
compiler's current cleverness. Compiler experimentation does not silently change
portable source validity, and a newly clever compiler does not by itself break
source whose contract did not change.

Removing an assertion that only one implementation has made redundant may make
the source depend on that stronger implementation. Portability improves only
when a selected language contract requires every conforming compiler to prove
the case.

This rule is general. It applies to every static analysis, not only conditional
live-storage presence or one lifecycle feature.

Concrete example shapes that must survive into the future owner:

```zax
if ?optionalValue
  use(optionalValue.) // proven; no assertion is required under any contract
```

```zax
// unsafe <construction-path-complete> _.prop2
// Advisory when the contract does not mandate this proof;
// a hard error under a contract that mandates recognizing the case.
```

## Lint suppression is a separate mechanism

Lint suppression:

- does not change language semantics;
- does not change lifecycle code generation;
- cannot make invalid code valid;
- should not require `unsafe`;
- should identify a narrow rule and source subject; and
- should permit documentation of intentional exceptions.

```zax
MyType :: type {
  bar : Foo
  foo : Bar // A naming lint may be intentionally suppressed here.
}
```

Layout-efficiency lints must permit intentional ABI layout, readable grouping,
or insignificant small-type overhead.

Future work may give unsafe controls and lint controls related surface patterns.
It must preserve their different authority and failure consequences.

### Arithmetic-overflow analysis

Current integer arithmetic and the two unchecked build contracts are owned by
[the integer operator catalog](../../language/integer-operator-catalog.md#unchecked-build-contracts).
This section retains their future option syntax, placement, source-local
controls, and analysis-contract integration.

Bare fixed-width arithmetic has stable exact-result-or-panic semantics,
independent from debug or release optimization mode. A future analysis policy
may:

- diagnose definitely overflowing constant operations;
- report statically evident suspicious ranges;
- request stronger warnings when runtime safety is not proved; or
- elevate selected warnings according to project policy.

An explicit compiler/build policy may instead remove required runtime
representability checks by changing the selected contract:

> Every required arithmetic result is representable by its result type.

When the contract holds, the result is the exact mathematical value. When it is
violated, Zax makes no behavioral promise; the operation does not become
implicitly wrapping.

Future teaching must explain the practical choice before the contract details:

- keeping checks catches range failures through panic;
- disabling checks removes those panic branches and permits stronger loop,
  vectorization, and no-overflow optimization;
- the unchecked contract is useful when trusted validation or domain invariants
  already prove representability; and
- violating it has undefined consequences that tests may not expose.

The option is never defined by debug versus release mode. Project/build metadata
must make the chosen contract reproducible and auditable.

### General runtime-check contract pressure

Arithmetic overflow is one instance of a broader possible mechanism. Future
work should evaluate whether other cheaply checkable runtime failures use the
same contract shape:

```text
default contract -> perform the required check and panic on violation
unchecked contract -> omit the check; violation has undefined consequences
```

Candidate domains include bounds, nonzero divisors, explicit panic-on-absence
operations, allocation failure, and other checks whose runtime condition is
well-defined. Each domain still owns its safe default and exact failure.

### Panic registry pressure from intrinsic arrays

Every defined runtime panic condition needs a stable registry entry rather than
an ad hoc feature-local switch. Language-owned and programmer/library-owned
categories need collision-free provenance. Tooling and reproducible build
metadata must expose the effective enabled/disabled state of each category.
The centralized provisional inventory is the
[raw analysis-control registry](analysis-control-registry.md#panic-category-inventory).

Source-level panic-control attachment is now current design in
[safety and analysis](../../language/safety-and-analysis.md#controlling-panic-checks):
attached and range directives that name one category each, no wildcard disable,
no crossing of `import`, and a build-configured starting state that source
refines. Remaining future work is the category names, the build-option syntax
that sets a module's starting state, and how tooling reports the effective
state.

Current [arrays and slices](../../language/arrays-and-slices.md) require
provisional registry entries for:

- element index outside bounds;
- splice endpoint or order outside bounds;
- requested logical length outside its admitted range;
- initializer cardinality mismatch;
- invalidated slice use;
- foreign slice origin supplied to mutation;
- iterable exact-count promise mismatch;
- backing extent overflow; and
- required backing allocation failure.

Exact identifiers remain future work. Each condition is independently
selectable. Disabling one permits omission of its required runtime check and
makes violation undefined, but it never suppresses a statically mandated error
or the check required by an optional/reporting operation.

Slice invalidation additionally pressures the registry to expose check cost and
provenance. Current storage-strategy direction may let a slice retain a
stability kind/token and observed version while the array and provider
cooperate to advance versions after mapping/chunk changes. A statically proved
use may eliminate the check; a mismatch enters the existing invalidated-slice
category.

Exact token/version representation remains future array-storage work rather
than registry authority.

Categories are independently selectable rather than one all-or-nothing panic
mode. For allocation, ordinary `@` checks request failure and enters panic.
Disabling only that category may omit the check under a success guarantee; an
actual failure then has undefined consequences. `@!` remains checked because its
defined result must distinguish success from pointer vacancy.

Static proof obligations remain distinct. Optional presence, reference lifetime,
and alias validity may be impossible or prohibitively expensive to track at
runtime without changing representation. A narrow unsafe assertion may accept
responsibility without requiring a check; debug tooling may add best-effort
instrumentation.

Future contract work must decide which domains share syntax, how several
contracts compose, whether source/build configuration may strengthen or weaken
them, and how tooling exposes the accepted cost and undefined-behavior boundary.

The policy is independent from optimization level, shifts proof responsibility
to the programmer, is recorded in build metadata, and does not affect optional,
wrapping, saturating, reporting, or narrowing forms. Compile-time-proven
violation remains a diagnostic.

Required representability and nonzero-divisor contracts are independent. Future
compiler-directive work must decide whether each may be selected at:

- project/build level;
- module or namespace level;
- source-file level;
- lexical region;
- declaration;
- expression/operation; or
- a deliberately smaller set.

It must also decide inheritance, narrower overrides, project-level minimum
safety that forbids weakening, forced checks, whether a build may force
unchecked behavior over explicit source checks, imported/generated source,
conflict diagnostics, language-contract versions, and reproducible metadata.

The current recommendation is a project/build default with narrower explicit
source choices and an optional minimum-safety policy. A project may force checks
without changing contract-respecting results; it should not silently force
unchecked behavior over source that explicitly requests checks.

Future work must decide:

- the policy's exact name;
- exact supported selection levels and source syntax;
- how tooling reports code relying on the contract;
- how contract versions preserve source and build reproducibility.

Ordinary source does not require a programmer-supplied proof for every
calculation. A lint cannot change arithmetic semantics. Checks may always be
optimized away when the compiler proves the selected operation is preserved.

## Structural transform non-overlap pressure

[Zax structural transformation](../../language/structural-shapes-and-compatibility.md#overlap-is-handled-statically)
uses conservative intermediate capture when source and destination may overlap.
It emits no runtime range check merely to choose a cheaper transform.

Future analysis-control work may define an explicit non-overlap contract for a
transform whose source cannot be copied, moved, or otherwise captured. That
contract would assert a semantic alias fact, affect generated operations, and
produce undefined behavior when false. It is not a lint suppression or general
optimizer hint.

No exact category name or syntax is established here.

## Nothing-instance diagnostics and controls

[Zax Nothing instances](../../language/nothing-instances.md) establishes the
semantic boundaries. Future analysis-control work must define exact,
independently selectable categories for:

- proved dereference or arithmetic from a vacant pointer;
- access that requests a trapping Nothing policy on a target with or without
  suitable hardware support;
- access to a member without a readable prepared representation;
- a proved write to compiler-provided Nothing backing;
- a runtime pointer-arithmetic check when ordinary target validity is required;
  and
- a proved call of an unavailable function value.

Compiler analysis remains intentionally incomplete. A selected category may
control a required check where the language defines one; it must not convert a
static error into valid source or imply that every unchecked dereference checks
vacancy. Debug instrumentation may catch additional writes or invalid access,
but it is tooling rather than a language guarantee.

The default and trapping policies do not authorize universal software vacancy
checks. Hardware write protection is used when available; a requested read trap
also relies on target capability. On a target without the needed trap, an access
that relies on it has undefined behavior. Future controls and diagnostics must
expose this capability difference without pretending a debugger can stop every
operation at the faulting instruction.

### Proven errors versus suspected flow

A compiler may prove with certainty that a pointer is vacant when source
dereferences or writes it. That is an error. A different case arises when
analysis only suspects that a pointer may become vacant before reaching a
later writer:

Provisional unsafe-catalog additions:

| Provisional identifier | Permission |
| --- | --- |
| `possible-nothing-write` | Accept a pointer handoff or operation whose downstream write may reach compiler-provided Nothing backing, without claiming that the pointer is present |
| `possible-nothing-read-trap` | Accept a pointer handoff or operation whose downstream read may reach a built-in type's canonical trapping Nothing or a defined type using `= trap` |

`unsafe` already communicates risk, so the identifiers name the uncertain
condition rather than appending `risk`. Both are permissions for possible flow,
not assertions that the pointer is non-vacant.

```zax
writeValues final : ()(value : MyType *) = {
  value.writeValuesIntoType()
}

// Illustrative placement; exact triggering rules remain future work.
unsafe<possible-nothing-write>{
  writeValues(possiblyVacant)
}
```

The risky boundary may be the call or handoff that supplies the pointer, not the
eventual store. Writing through a pointer is not inherently unsafe, so attaching
an unsafe marker mechanically to every write would identify the wrong
responsibility boundary.

This is an unsafe concern rather than intent acknowledgement: the programmer is
accepting possible memory-safety consequences, not merely confirming surprising
defined behavior. The provisional identifiers will exist in the unsafe catalog;
future work still owns their exact triggering scenarios and source placement.

Neither permission disables an actual hardware trap, makes a proved vacant
access valid, or promises successful execution. `possible-nothing-read-trap`
names only reads subject to trapping policy: built-in Nothing instances and
defined types selecting `= trap`. It is not a general warning for readable
defined-type Nothing backing.

Future safety/error work must choose how non-certain findings affect portable
source:

- compiler-dependent analysis with a standard unsafe escape;
- only language-mandated findings affecting standard validity; or
- language-mandated findings plus `x-` extension diagnostics and controls for
  stronger compiler analysis.

It may choose another coherent model. The design must avoid one compiler
requiring a standard unsafe marker that another compiler rejects as needless,
while still allowing implementations to report high-confidence problems beyond
the mandatory language baseline.

Future syntax must also express narrow unsafe responsibility for disabling an
applicable check or asserting ordinary target validity. A false assertion has
undefined consequences. Unsafe cannot legalize a proved managed-pointer leak,
and no control may collapse a vacant pointer, absent optional, or unavailable
function value into one universal state.

## What this input does not decide

This file does not establish:

- angle-bracket syntax;
- block, statement, expression, annotation, or directive form for unsafe
  assertions and permissions (panic-check controls are current directives);
- the final category names;
- which analyses are mandatory in each contract;
- language-version syntax;
- extension namespaces;
- lint severity or suppression syntax;
- runtime instrumentation; or
- compiler analysis algorithms.

## Activation and retirement

Activate this input when focused work defines unsafe boundaries, analysis
contracts, diagnostics, lints, or source-version selection.

That work must:

1. disposition every provisional category and example;
2. separate language guarantees from compiler experiments;
3. define unsupported-extension behavior;
4. give semantic controls and lint controls distinct effects;
5. move accepted behavior into safety, diagnostic, tooling, or contract owners;
   and
6. remove this file from the raw index and retire it when no unresolved material
   remains.
