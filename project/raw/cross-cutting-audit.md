# Raw input: cross-cutting audit

| Field | Value |
| --- | --- |
| Status | Raw live audit input / non-authoritative |
| Audience | The Zax language maintainer and agents performing cross-owner audits and repository sweeps |
| Applies To | Concerns that cross several owners and must survive numbered-work archival |
| Owns | An indexed, live list of cross-cutting audit entries, each with a concern, why local review misses it, likely owners, resolution criteria, activation pressure, current constraints, and eventual disposition; the durable-queue purpose, future-agent workflow, add/do-not-add criteria, and retirement model |
| Does Not Own | Accepted language design, a miscellaneous design dump, ordinary local TODOs, or competing authority over any concept owner |
| Source / Provenance | Work item `007` promotion and its corrective promotion; work item `012` optional legacy disposition; work items `032` and `034` entries |

## Purpose

This file is a durable queue of repository-wide verification obligations that
cannot be completed responsibly inside one local concept review. It exists so
those obligations survive numbered-work archival and are eventually tested
against every affected owner.

An entry is not a design decision, a TODO, or a substitute for an owner. It
records a concern whose correctness can only be established by inspecting
several owners together.

## Future-agent workflow

An agent assigned work that activates an entry should:

1. read only the entries the assigned task activates;
2. inspect the owners and evidence the entry names;
3. determine whether the concern remains valid under current design;
4. identify every current or legacy surface affected;
5. discuss the finding and obtain authorization for edits;
6. apply corrections to the real owners rather than treating raw input as
   authority;
7. record evidence that the concern was resolved, narrowed, superseded, or
   remains open; and
8. remove the entry once it is resolved.

## When to add and when not to add

Add an entry only when all of the following hold:

- a concrete concern crosses several owners;
- local, single-owner review cannot prove repository-wide consistency;
- representative examples or evidence exist; and
- resolution criteria and activation pressure can be stated.

Do not use this file for ordinary local TODOs, speculation, personal notes, or
unresolved design that belongs in numbered work or a raw subject input.

## Reading posture

This file stays live across numbered-work archival. Unlike a numbered work item,
it is not consumed and archived as a unit: each entry is dispositioned
individually, and the file retires only after every entry is resolved.

It must not become a general design scratchpad or a competing authority. Every
immediately necessary question is still resolved in its owner or numbered work.
Read or update it when a cross-owner concern is discovered or an audit sweep is
performed.

## Entry shape

Each entry preserves:

- a concrete concern and a representative example;
- why local, single-owner review may miss it;
- affected or likely owners;
- what would count as resolution;
- activation pressure;
- current constraints; and
- eventual disposition.

## Entries

### Ternary convergence forms

- **Concern.** Conditional expressions (`a ?? c ;; d`) must converge to one
  statically usable result shape per complete operation, including inferred
  bindings, typed destinations, nested operators, and Boolean-producing paths
  inside a condition.
- **Representative example.** `whatAmI := (a < b ?? c ;; d)` must error when `c`
  and `d` remain different types, while `whatAmI : KnownType = (a < b ?? c ;; d)`
  converges through construction and
  `myType : MyType? = (a < b ?? (: MyType = a) ;; (: MyType?))` converges through
  a typed optional destination.
- **Why local review misses it.** Convergence spans core flow, function
  invocation (selection), operators (node classification), and construction
  (destination lifetime); no single owner sees every form.
- **Likely owners.** [Core flow control](../../language/core-flow-control.md),
  [function invocation](../../language/function-invocation.md),
  [operators](../../language/operators.md),
  [construction, replacement, and destruction](../../language/construction-and-destruction.md),
  and [optional values](../../language/optional-values.md).
- **Resolution.** A reviewed catalog of valid convergence forms and their errors,
  cross-checked across those owners.
- **Activation pressure.** Value-polymorphism, generics, or richer inference
  design.
- **Current constraints.** Statement composition does not extend convergence into
  a later statement; a stored branch-dependent runtime type is deferred.
- **Disposition.** Open.

### Branch-specific overload and mixfix selection and visible cost

- **Concern.** A conditional-expression arm may select a different overload or
  code path, and one `&&` or `||` node may be classified differently per path:
  the protected exact `Boolean`/`Boolean` short-circuit operation on one path and
  an ordinary eager overload on another. One path may also select a direct
  mixfix while another decomposes into ordinary component operations.
  Diagnostics and cost tooling should show branch-specific selections, tree
  boundaries, and per-path short-circuit behavior without implying duplicated
  evaluation of the surrounding operation.
- **Representative example.** `e := f + (condition ?? c ;; d)` evaluates `f`
  once and selects `f + c` or `f + d`. In

  ```zax
  result5 :=
    (?a1) ||
    (c < d ?? (?a2) ;; e) ||
    (?a3) ||
    (g < h ?? (?a3) ;; f)
  ```

  some paths may use protected short-circuit nodes while others require ordinary
  eager overloads, yet every path is validated at compile time and must converge.
- **Why local review misses it.** Cost visibility is a tooling and diagnostics
  concern layered over invocation selection, operator classification, and flow
  evaluation order.
- **Likely owners.**
  [Function invocation](../../language/function-invocation.md),
  [operators](../../language/operators.md),
  [mixfix operators](../../language/mixfix-operators.md), future
  analysis/diagnostics work.
- **Resolution.** Branch-specific selection, per-path operator classification,
  mixfix/decomposition boundaries, and cost are shown, and a possible future
  lint flags different operations selected before convergence.
- **Activation pressure.** Diagnostics/tooling design or a reported surprise.
- **Current constraints.** `f` is evaluated exactly once; `??` is sufficient
  source acknowledgement; compile-time resolution never implies early runtime
  evaluation.
- **Disposition.** Open.

### Repository-wide two-space source and legacy-example repair

- **Concern.** Promotion reformatted current conceptual examples to exact
  two-space structural indentation and aligned separators, but legacy remainder
  pages still use older indentation and some superseded spellings.
- **Representative example.** Legacy pages using four-space indentation, bare
  optional conditions in untouched sections, or old label and target spellings.
- **Why local review misses it.** The sweep spans every legacy page; a focused
  review touches only the page it is on.
- **Likely owners.** Every legacy root page; ultimately their future focused
  reviews.
- **Resolution.** Each legacy page is either promoted, corrected, or explicitly
  retired, at which point no stale example remains.
- **Activation pressure.** Each legacy page's own focused review.
- **Current constraints.** Directly corrected or touched examples must already
  follow aligned semantics; untouched remainders may keep older layout until
  their review.
- **Disposition.** Open.

### Live owner for every deferred consequence

- **Concern.** Every deferred consequence must have a live, non-archive owner or
  indexed raw destination, so nothing is lost when numbered work is archived.
- **Representative example.** Generalized pattern bindings, optional proof
  pressure across direct body entry, and flow-derived analysis controls.
- **Why local review misses it.** The check is about the set of deferrals as a
  whole, not any one owner.
- **Likely owners.** [Documentation architecture](../documentation.md), the raw
  index, and this file.
- **Resolution.** A confirmed mapping from each deferred consequence to a live
  destination.
- **Activation pressure.** Any archival of numbered work.
- **Current constraints.** Design may be deferred, but capture may not; archived
  work may not be a current owner's source of meaning.
- **Disposition.** Open.

### Example prerequisite and placement audit

- **Concern.** Each current owner must read correctly top to bottom for a new
  programmer: examples must be understandable where they are placed, untaught
  pieces must be safely treatable as placeholders, and no explanation may depend
  on a concept introduced only later.
- **Representative example.** A complex labeled `if` with an initializer,
  condition, post operation, and targeted `break` placed in an opening mental
  model before labels and post operations are taught; the same example belongs in
  the section that owns those concepts. Likewise, an example with a known compile
  error must carry an inline `// error` comment rather than relying on prose:

  ```zax
  if condition ;; postOperation()
    body() // error unless postOperation() returns Boolean
  ```

- **Why local review misses it.** A focused review edits one section, while
  prerequisite order and diagnostic visibility are properties of the complete
  reading path across every owner.
- **Likely owners.** Every current owner in `language/`, with criteria owned by
  [documentation architecture](../documentation.md).
- **Resolution.** Each current owner is read end to end as a new programmer
  would, and its examples are confirmed short, prerequisite-safe, and
  inline-annotated where a diagnostic or cost is critical.
- **Activation pressure.** Any promotion, any reported reader confusion, or a
  periodic teaching sweep.
- **Current constraints.** Examples must be as short as possible but no shorter
  than the behavior being taught; a known compile error must be marked in code.
- **Disposition.** Open.

### Numbered-work provenance in current owners

- **Concern.** Current programmer-facing owners must not cite numbered work items
  or archived project records for provenance or meaning, because those files are
  eventually archived and are not reader destinations.
- **Representative example.** A `Source / Provenance` row reading
  `Work item 007; legacy operator overloading evidence`, or body prose accepting
  behavior "aligned during work item `007`".
- **Why local review misses it.** The occurrences are metadata and passing
  sentences spread across every promoted owner, and each looks harmless locally.
- **Likely owners.** Every current owner in `language/`, plus public routes such
  as [index](../../index.md); criteria owned by
  [documentation architecture](../documentation.md).
- **Resolution.** No current or public owner cites a numbered work item or
  archive, and every such owner instead cites current predecessors or live legacy
  evidence. Recurrence is caught during promotion validation.
- **Activation pressure.** Any promotion, any archival of numbered work, or a
  repository-wide link sweep.
- **Current constraints.** `project/` records, including active numbered work and
  `project/raw/` inputs, may retain numbered provenance; current and public owners
  may not, and may not route ordinary readers into project raw material.
- **Disposition.** Open.

### Protected operations, required support, and toolchain consistency

- **Concern.** Protected intrinsic operator families are required language
  support rather than optional library content. Every surface that discusses
  libraries, the compiler/library split, builds, or linkage must agree, and none
  may promise a particular lowering.
- **Representative example.** Rejecting "one mandatory system library" as a
  non-goal while source still depends on protected operations that a toolchain
  must supply:

  ```zax
  // error: the protected Boolean || Boolean operation cannot be replaced
  operator binary '||' final :
    (result : Boolean)(lhs : Boolean, rhs : Boolean) = {
    return asmCompare(lhs, rhs)
  }
  ```

- **Why local review misses it.** The promise is split between the operator
  owner, the vision's non-goals and build model, and future build/toolchain work,
  so any one page can look self-consistent while the set disagrees.
- **Likely owners.** [Operators](../../language/operators.md),
  [operator catalog](../../language/operator-catalog.md),
  [language vision](../../language/vision.md),
  [raw build and dependency input](build-and-dependencies.md), and future
  toolchain work.
- **Resolution.** Required language support and optional library APIs are
  distinguished consistently everywhere, missing required support is described as
  an incomplete toolchain, and no surface promises instructions, intrinsics,
  emitted code, or helper linkage as a language guarantee.
- **Activation pressure.** Build/toolchain design, freestanding or
  cross-compilation targets, or a reported contradiction.
- **Current constraints.** The implementation may choose instructions, constant
  folding, emitted code, an intrinsic, or an automatically linked helper; the
  programmer-visible guarantee is only availability and semantics.
- **Disposition.** Open.

### Distinguishing the `own` keyword from ownership terminology

- **Concern.** Current documentation uses *owned* for by-value storage,
  life-path responsibility, `using` entries, resource ownership, and pointer
  control, while [composition](../../language/composition.md) uses `own` as a
  stored-member publication keyword. A reader must not infer that every owned
  value publishes members or that `own` establishes pointer ownership.
- **Representative example.** The transfer-stance phrase “an owned declaration
  carries its stance into unstanced members” describes a declaration that owns
  its value; it does not mean a member declared with the `own` keyword.
- **Why local review misses it.** Each usage is coherent within its own owner,
  but the collision appears only when composition, transfer, lifetime, pointer,
  and `using` documentation are read together.
- **Likely owners.** [Language-design terms](../../language/terms.md),
  [composition](../../language/composition.md),
  [transfer stances](../../language/transfer-stances.md),
  [lifetimes and references](../../language/lifetimes-and-references.md),
  [pointers and arenas](../../language/pointers-and-arenas.md), and
  [Zax `using`](../../language/using.md).
- **Resolution.** Each owner qualifies storage, path, resource, or pointer
  ownership where the bare word could be mistaken for the `own` keyword, and
  terms documentation keeps the concepts distinct without renaming accepted
  source.
- **Activation pressure.** Promotion or revision of any owner using the word
  *owned*, or a reported reader misunderstanding.
- **Current constraints.** `own` remains the composition member keyword. This
  audit may clarify prose but cannot merge or rename the separate concepts.
- **Disposition.** Open.

### Legacy continuation examples after symbolic-infix refinement

- **Concern.** Current source structure now treats a recognized spaced symbolic
  infix form at physical line end as sufficient continuation intent and rejects
  redundant `\`. Explicitly legacy pages still preserve older examples using
  `&& \` or `= \`.
- **Representative example.** `meta-functions.md` line 112 retains the old
  form.
- **Why local review misses it.** Those pages are outside ordinary current
  language reading and contain broader unreviewed legacy syntax. Mechanically
  rewriting one continuation token could make the remaining example appear
  current.
- **Likely owners.** Future meta-function review, with
  [source structure](../../language/source-structure.md) retaining the current
  continuation rule.
- **Resolution.** Each focused legacy review either updates the complete example
  to current source or preserves it as explicitly marked historical evidence.
- **Activation pressure.** Focused review or promotion of `meta-functions.md`,
  or a repository-wide retirement of legacy example source.
- **Current constraints.** New current and raw examples use trailing symbolic
  continuation without redundant `\`; arbitrary incomplete expressions and
  phrase operators do not continue.
- **Disposition.** Open.

### `Does Not Own` metadata scope

- **Concern.** Current language-owner metadata can accumulate exhaustive
  `Does Not Own` lists that obscure the few adjacent concerns a direct-entry
  reader might plausibly mistake for this document's authority.
- **Representative example.** A scalar owner benefits from handing complete
  endianness and aggregate coercion to their real owners, but does not need to
  enumerate every future grammar, compiler, ABI, vector, reflection, and
  implementation concern merely because each is outside its scope.
- **Why local review misses it.** Each additional exclusion looks defensible in
  isolation; the reader friction appears across many independently promoted
  metadata blocks and grows incrementally.
- **Likely owners.** Every current concept owner under `language/`, with the
  governing rule in
  [documentation architecture](../documentation.md#authority-and-maturity).
- **Resolution.** Review current-owner metadata and retain only plausible
  competing ownership or direct handoffs. Move detailed interactions and future
  boundaries into the body, and remove exclusions that merely state the
  obvious.
- **Activation pressure.** The later documentation cleanup after numbered
  concept work, or a focused review that already materially rewrites an owner's
  metadata.
- **Current constraints.** `Does Not Own` is not an exhaustive exclusion list.
  A focused concept promotion should trim the owners it materially changes but
  must not expand into an unauthorized repository-wide cleanup.
- **Disposition.** Open.

### Word order after `+++` and other declaration keywords

- **Concern.** Constructor variants now place every word after `+++`, but the
  relative order of several words is not settled:

  ```zax
  +++ final : ()(source : Source) = { … }
  +++ contextual final : ()(rhs : Integer) = { … }
  +++ explicit final : ()(rhs : Integer) = { … }
  +++ final once : ()() = { … }
  +++ replacement final : ()(settings : Settings) = { … }
  ```

  `contextual`, `explicit`, and `replacement` precede `final`, while `once`
  follows it. The construction owner states that the exact keyword position
  remains future declaration and source integration.

  Type declarations now carry a comparable question. The `seal` clause of
  [partials](../../language/partials.md) follows `type`, but its position
  relative to other type-declaration words is not decided:

  ```zax
  MyCatalog :: type seal open storage { … }
  ExecutionContext :: type seal open storage module { … }
  ```
- **Why local review misses it.** Each owner introduced its word locally:
  `replacement` was moved to the post position to match the others without
  deciding their relative order. The inconsistency is visible only
  when the variants are compared.
- **Likely owners.** Construction, replacement, and destruction; declarations
  and bindings (qualifier attachment); qualifiers; Nothing instances
  (`+++ final once`); source structure; `terms.md`; and any other declaration
  that stacks words after its keyword.
- **Resolution.** One stated ordering rule for words after `+++`, and for
  comparable declaration keywords where the same question arises, applied to
  every live example.
- **Activation pressure.** A new constructor or declaration word; a
  declaration that combines two of these words; or formal grammar work.
- **Current constraints.** Every word stays in the post position after `+++`.
  Do not reorder existing examples until the rule is decided. The `seal` clause
  stays directly after `type`.
- **Disposition.** Open.

### Language-contract locking for syntax evolution

- **Concern.** A later Zax version may adopt or reserve syntax that existing
  source already uses, for example a phrase word sequence that a partial
  supplies:

  ```zax
  MyCombining :: partial U32 {
    operator binary 'combines with' final : (result : MyType)(rhs : MyType) = { … }
  }
  // A later language version reserves `combines with`.
  ```

  The maintainer does not want Zax to promise that such changes never happen.
  Instead, compilers should accept reasonable locking of source to an older
  language contract, lint where the language has since evolved, and avoid
  conflicts with code compiled against newer contracts.
- **Why local review misses it.** Each owner that allows programmer-chosen words
  or forms treats reservation locally. The same exposure exists for phrases,
  partial additions, literal operators, and other extensible surfaces, and the
  pattern is visible only across them. This is a candidate reusable language
  principle, not a partial-specific rule.
- **Likely owners.** [Safety and analysis](../../language/safety-and-analysis.md#language-contracts-and-compiler-analysis),
  which already owns language-contract selection for required analysis;
  [principles](../../language/principles.md) if it is adopted as a principle;
  [operator phrases](../../language/operator-phrases.md); and
  [partials](../../language/partials.md).
- **Resolution.** An aligned statement of how language-contract selection
  applies to syntax and reservation changes, placed in its owner and applied
  to the affected surfaces.
- **Activation pressure.** The first language-version change that reserves or
  redefines a form, formal compatibility work, or principles review.
- **Current constraints.** Current owners describe reservation of a
  programmer-supplied form as a source-compatibility event. They do not promise
  that it cannot occur.
- **Disposition.** Open.

## Activation and retirement

Add an entry when a concrete concern crosses several owners, local review cannot
prove repository-wide consistency, representative examples exist, and resolution
criteria and activation pressure can be stated. Disposition entries individually
as their owners consume them. Retire this file only after every entry is
dispositioned and no cross-cutting concern remains open.
