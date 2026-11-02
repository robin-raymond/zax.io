# Zax documentation architecture

| Field | Value |
| --- | --- |
| Status | Current project guidance |
| Audience | Language maintainers, human developers, and agents creating, reviewing, or navigating Zax documentation |
| Applies To | What Zax documents, where it belongs, and how it is developed and maintained |
| Owns | Documentation types, locations, authority, reading paths, numbered work, promotion, archival, and validation |
| Does Not Own | Zax language concepts, the current active work pointer, or session-specific authorization |

## Purpose

Zax documentation exists first to help human developers understand, evaluate,
and use the language. It must also let a focused agent comprehend relevant parts
of the language without reading the entire repository.

This document defines:

- **what we document:** audiences, document types, technical depth, authority,
  and maturity;
- **where we document:** repository surfaces, owner locations, naming,
  navigation, active work, and archives;
- **how we document:** focused reading, bounded refinement, promotion,
  continuation, and validation.

It is not a template requiring speculative folders or placeholder documents.

## Project roles

These are documentation and cooperation terms, not Zax language terms:

| Term | Meaning |
| --- | --- |
| Zax language maintainer | The human who guides the work and has authority to accept language decisions and authorize project changes |
| Human language maintainer | The same role when it is useful to distinguish the human from an assisting agent |
| Language author | The person whose original design intent is relevant to the current question |
| Human developer | A person learning, evaluating, or using Zax |
| Programmer | A human developer considered specifically as an author or reader of Zax code |
| Agent | An AI assistant helping investigate, reason about, or document Zax under the supplied operating guidance |

Use the most specific term needed by the context. Do not conflate a developer
using the language with the language maintainer who decides its design.

## Current repository surfaces

| Surface | Current role | Authority and reading posture |
| --- | --- | --- |
| `README.md` | Repository entry point | Router |
| `index.md` | Public website entry point | Router and legacy-note index |
| `language/vision.md` | Accepted foundational language direction | Current conceptual owner for purpose, audience, foundational direction, goals, advantages, non-goals, high-level tradeoffs, and the summary route to reusable principles |
| `language/principles.md` | Reusable cross-cutting design and use principles | Current conceptual owner for the full human-facing principles catalog |
| Root language-topic pages | Public human-developer-facing language material | Legacy design input until individually reviewed and promoted |
| `project/README.md` | Project-guidance router and current-work index | Current routing and numbered-work state |
| `project/handoff.md` | Generic source for the new-session copy/paste prompt | Current source; agents do not proactively read it |
| `project/rehydrate.md` | Generic source for the post-compaction copy/paste prompt | Current source; agents do not proactively read it |
| `project/documentation.md` | Documentation architecture | Current for the concerns owned here |
| `project/teaching-debt.md` | Suggested process for a separately assigned teaching refresh | Current task-specific guidance; not ordinary onboarding |
| `project/teaching-debt-log.md` | Durable unresolved current-owner teaching observations | Live non-authoritative queue; read only for targeted disposition or refresh |
| `project/work/` | The one active numbered work unit | Non-normative working material |
| `project/raw/` | Indexed future-work input whose lasting location is not yet known | Non-authoritative and available through consequence-driven reading |
| `project/archive/` | Completed project records | Historical and audit-only |

The existing public language pages remain at the repository root while the
language is being understood. Moving them requires a separately discussed,
aligned, and authorized structural change.

There is no formal specification area. Do not create one until accepted
conceptual design and real consumer pressure justify it.

Compiler, transpiler, runtime, and toolchain implementation belong in another
repository. This repository may document implementation constraints when they
affect the language design or programmer-visible behavior, but it does not own
implementation source or internal architecture.

Parser, tooling, analysis, and implementation feasibility are legitimate
pressures when evaluating conceptual language design. Lasting language
documentation states the resulting programmer-visible behavior and constraints,
not a speculative compiler mapping.

## Audiences

### Human developers

They need to understand why Zax exists, how its concepts work, what tradeoffs
they make, how to use them, and which areas remain unsettled.

Concept owners may assume a competent general programmer. They need not teach
programming from first principles, but they must explain Zax-specific behavior,
costs, terminology, and surprising differences clearly.

### Zax language maintainer

The maintainer needs durable ownership, visible maturity, bounded review units,
preserved findings, and explicit control over promotion and structural change.

### Agents

Agents need a supplied operating prompt, one active concern, a minimal reading
path, clear authority, and explicit stopping and authorization boundaries.

### Future compiler and tooling contributors

They may eventually need formal grammar, semantics, diagnostics, compatibility,
ABI, and conformance material. Those future needs inform feasibility without
prematurely turning conceptual documentation into a specification.

## Document types

Create a document type only when real content has a distinct audience,
authority, lifecycle, or independent reuse.

### Router

A `README.md` or index routes readers by audience and task. It contains only
enough context to choose the next document. Substantive definitions belong in
named owner documents.

The public `index.md` currently includes substantial legacy content. That is an
existing condition to review later, not precedent for new indexes.

### Language concept owner document

A language concept owner document explains an accepted part of Zax for human
developers. It may be technically detailed, but it prioritizes a usable mental
model, behavior, examples, costs, failures, and interactions over formal
specification language.

Each important concern has one owning document. Other documents may link, show a
local example, or summarize usage without becoming competing definitions.

### Project guidance

Project guidance defines cooperation and documentation practice. The supplied
operating prompts establish session behavior and current work. This document
owns the detailed documentation architecture.

### Active numbered work

`project/work/00n-<concept>.md` is the one shared, non-normative work unit for a
bounded concern, where `<concept>` is a short lower-case slug that names the
actual subject. It preserves the initiating input and evolving discussion
without presenting findings as accepted language design.

### Archived work

`project/archive/work/00n-<concept>.md` is the completed form of a numbered work
unit. It retains the same subject-specific filename and is kept for human
discovery and targeted audits, not onboarding or current authority.

### Candidate or raw material

Unaccepted proposals, recovered evidence, research, and unresolved ideas remain
clearly non-authoritative. They are read when a current work unit names them or
a concrete consequence makes them relevant.

`project/raw/` is a temporary, indexed holding area for evidence-backed input
whose future review is known but whose lasting documentation owner and directory
are not yet established. Its existence does not decide that long-term
documentation should be organized by maturity.

Create a raw placeholder only when it identifies:

- the future concern and why it requires review;
- what useful input must not be lost;
- what it explicitly does not decide;
- the pressure that should activate future work; and
- how the placeholder will retire or move.

Every raw placeholder appears in `project/raw/README.md`. Raw files are not
required for ordinary onboarding, but a work item may read or update a specific
file when a concrete consequence makes it relevant. The next applicable
documentation fit dry run must propose whether each file moves to a
domain-oriented owner, remains temporary, or retires.

### Teaching-debt guidance and log

`project/teaching-debt.md` explains motivated, suggested techniques for a
separately assigned refresh of current `language/` teaching. It does not define
language meaning or impose one content template.

`project/teaching-debt-log.md` preserves only concrete unresolved reader
friction that remains after promotion disposition. It is not raw language
design, an audit, ordinary onboarding, or a prompt to search for problems.

### Future specification or contract

A specification defines exact rules for compilers, tools, and conformance. A
formal contract defines a stable shape or compatibility promise for named
consumers. Neither should be created merely to make the documentation tree look
complete.

### Implementation documentation

Transpiler mappings, lowering algorithms, compiler data structures, and
toolchain internals belong with the implementation that owns them. Language
documentation includes such information only when it creates a
programmer-visible promise or design constraint.

## Human-developer-facing depth

Human-first does not mean introductory, simplified, or imprecise.

Include a detail in human-developer-facing language documentation when a
programmer needs it to:

- write or read Zax code;
- predict behavior or ordering;
- reason about types, ranges, operators, and conversions;
- understand initialization, lifetime, ownership, or allocation;
- anticipate cost, failure, panic, or undefined behavior;
- reason about portability or cross-feature interaction;
- debug an outcome.

Details that primarily serve compiler conformance, formal proof, lowering, or
internal implementation belong in future owners unless they change the
programmer's model.

Current concept owners begin with a usable mental model and common examples, but
they do not stop at introductory teaching. Material rules, diagnostics, costs,
corner cases, advanced behavior, and future-specification pressure remain in
current documentation when programmers need them or when omitting them would
force later specification work to rediscover accepted behavior.

Examples are essential teaching tools. Introduce prerequisite concepts before
examples depend on them, and use concrete Zax source to demonstrate behavior,
ordering, costs, and accepted surprising boundaries.

Layer that depth so ordinary use remains readable. A cohesive owner may progress
through common behavior, advanced behavior, diagnostics, costs, and reference
details without splitting into a premature specification. Working history,
rejected alternatives, and discovery chronology stay in project records unless
an accepted, likely-to-surprise boundary requires a counterexample.

Teach Zax directly. Comparison with another language belongs in a concept owner
only when it resolves a concrete likely misunderstanding. Language-specific
onboarding and migration guides are separate reader routes created only when
accepted Zax concepts and real reader pressure justify them.

Do not discard a useful idea merely because it is too detailed or immature for a
public concept owner. Give it another explicit disposition before the working
file is archived.

### Example obligations

Readers often scan examples before prose, so critical teaching must appear in the
source and its comments as well as in the surrounding explanation.

- Keep each example focused on the behavior being taught. Include enough context
  to make its intent, prerequisites, and consequences understandable, and remove
  only details that distract.
- Place an example only after its prerequisites are taught, or use pieces a
  reader can safely treat as placeholders. Do not explain an example with a
  concept introduced only later.
- When a compile error is known, put an inline `// error` comment, or an equally
  clear comment, in the sample. Prose may expand the diagnosis but must not be
  the only indication that the source is invalid.
- Show critical diagnostics and costs in representative examples rather than only
  in prose.
- Repeat a complex example in the section that owns it rather than opening a
  document with it.
- Prefer `My...` and `my...` names when they help distinguish
  programmer-defined declarations from language-provided ones. Use alphabetic or
  numbered placeholder names only in a compact comparison whose relationships
  explain those names; standalone or reusable examples use meaningful role
  names.
- Preserve established Zax grammatical categories and ordering. Declaration
  keywords remain declaration keywords, operator phrases remain operator
  phrases, and qualifiers appear in the established position for the construct
  they qualify. Do not invent punctuation or compound pseudo-keywords that
  conflict with Zax source conventions.
- Do not omit a source choice merely to shorten an example when omission would
  obscure the intent or boundary being taught.
- When required syntax remains unsettled, preserve accepted surrounding grammar,
  invent only the unresolved portion, mark it illustrative, and keep related
  examples internally consistent.
- Consolidation may remove repeated explanation but must preserve examples,
  tables, and checklists that make a deciding boundary understandable. Verify
  that unmarked source uses current forms and label illustrative future syntax
  inline. When consecutive examples change declarations, candidate sets,
  visibility, types, or other material assumptions, state that change before
  contrasting their outcomes.
- Establish shared behavior before explaining variations. Organize differences by
  the property that actually varies so document structure does not make an
  ordinary family member appear categorically special.

### Raw material carries the same obligation

When deferred raw material involves source syntax or programmer-visible
behavior, preserve representative valid examples, invalid counterexamples, and
comments explaining the deciding distinction. Prose alone is insufficient when
code can disambiguate the intended meaning. A raw input may omit examples only
when no useful source form exists yet, and it should say so explicitly.

### Teachability controls presentation order

Completeness, accuracy, and teachability are simultaneous goals:

- teachability controls how a reader encounters the material;
- completeness controls what behavior must remain discoverable; and
- accuracy prevents a simpler explanation from changing the design.

Promotion reconstructs human-facing teaching rather than polishing or
redistributing a working record. Discovery order, audit categories, and
contract-shaped ledgers are evidence for the author, not a presentation plan.

Documentation guidance has three different strengths:

1. **Hard invariants** protect authority, correctness, and process. Examples
   include one current owner, explicit maturity, complete disposition, required
   dry-run output, authorization boundaries, and preservation of staged review
   state.
2. **Reader outcomes** describe what successful teaching achieves. A competent
   programmer should understand ordinary use, predict behavior and cost, and
   know where deeper rules belong without reconstructing project history.
3. **Suggested techniques** are accumulated ways to reach those outcomes.
   Examples include leading with source, explaining in everyday language before
   introducing a term, and moving exhaustive tables after the mental model.

Suggested techniques are diagnostic aids, not a required content template. Do
not add an example, definition, rule, table, or link merely to satisfy a
sequence. When techniques compete, preserve hard invariants and choose the
presentation that best achieves the reader outcomes without losing technical
content.

Use these outcome checks:

- **Cold-reader:** a competent programmer who has not read the working record
  can understand common use from the opening.
- **Vocabulary earns precision:** a Zax-specific or overloaded term gains
  technical weight only after the reader has enough meaning to use it.
- **Concrete consequence:** the reader can tell what an abstract rule changes
  in source, values, operations, failures, or costs.
- **Teaching before reference:** exhaustive matrices and per-form behavior
  follow the conceptual model or live in an independently useful catalog.
- **No working-record transcription:** the owner teaches the accepted model
  rather than preserving analytical chronology.

One reliable promotion technique is to work in two passes: first build the
reader's model, then trace every aligned finding into the appropriate later
layer. Another structure may work when it achieves the same outcomes and makes
the completeness trace equally reliable.

### Direct-entry and cross-owner teaching

A reader may enter any linked document or section directly. Success means that
the reader can quickly understand why the section matters, what representative
source does, and where the complete rule belongs without being assumed to have
followed a preferred reading order.

An often-effective progression for a cross-cutting concern is:

```text
representative source
-> plain-language outcome
-> precise Zax term
-> local boundary
-> complete owner
```

Use only the pieces the section needs. A concise reference entry, familiar
operation, or already established local term may need a different order or no
example at all.

A link does not replace local teaching. The current document must explain why
the linked rule matters to the reader's immediate task, while avoiding a
duplicate definition of the complete foreign mechanism.

Usually place an advanced interaction after the local prerequisites needed to
understand it rather than at the first technically related heading. If the
interaction is not needed for the local reading task, omit it or use a concise
later handoff.

Everyday language and established terminology are complementary. Everyday
language establishes meaning; terminology preserves precision after that
meaning is understood. Do not remove a useful term merely to sound informal,
and do not open with a dense cluster of terms that forces the reader to decode
the project model before seeing its effect. For example, prefer:

> A number such as `55` takes the parameter's `U8` type.

before:

> An uncommitted integer realizes into a concrete parameter hole.

when that wording better prepares the local reader. In a later reference section
where the term is already established, the precise shorter form may be better.

### Positive-first promoted teaching

Promoted programmer-facing documentation leads with valid forms and meaningful
boundaries. Mention an invalid form only when it prevents a likely
misunderstanding, demonstrates an intentional diagnostic, or distinguishes two
genuinely plausible source meanings.

Do not manufacture a concept merely to deny it, and do not enumerate arbitrary
malformed source in promoted language documentation. A working record may
preserve rejected hypotheses and diagnostic classification so that promotion
avoids inventing an obligation; the promoted owner removes that discovery history
and teaches the valid model.

Repair settled wording and examples directly. Remove a legacy or superseded
alternative when its only remaining purpose is to say that it is obsolete;
retain historical material only when it still preserves useful evidence,
compatibility context, or a current teaching boundary. This does not prohibit
counterexamples that teach a valid distinction or intentional diagnostic.

Consume legacy material during promotion. Remove a legacy passage when all of
its useful meaning has been promoted, moved to a live deferred owner, rejected,
or superseded. Do not retain duplicate teaching merely for provenance. When a
passage still contains unique unresolved input, extract the smallest useful
concern and representative examples into its indexed future owner, then remove
the superseded teaching from the public legacy page.

Preservation does not imply in-place retention. Preserve each finding in the
owner appropriate to its maturity: current behavior in current owners,
unresolved input in indexed raw material, and historical provenance in project
records or archives. Retain legacy text in an ordinary public reading path only
while it remains unique useful input for an unresolved concern or teaches a
concrete current compatibility boundary.

### Promotion re-authors for a different reader

A working record is written for its reviewers rather than for programmers
learning the language. Its findings must still be understandable to a reviewer
judging the work, with enough context and explanation to decide what should be
done; density is never a goal. Even so, a finding's meaning often depends on the
discussion that produced it. A finding may be phrased as the alternative that
survived, and an example may have been clear
only because the surrounding exchange supplied its question. By promotion time,
the promoting agent has usually spent a long session inside that record, so its
sense of what a reader already knows is its own.

Left unchecked, that vantage produces two recurring failures:

- **Decision history presented as teaching.** "We previously did X but rejected
  it because Y" teaches the reader about something they never learned.
- **Transplanted snippets.** An example moved without the situation it answers
  can confuse more than it clarifies.

The same finding can need very different treatment in different owners: a
paragraph and an example where a reader first meets the concept, a single
clause where it only bounds a local rule. Decide per destination, from the
position of a reader who arrived at that section directly. Two judgement tests
help:

- **Would a competent programmer who never saw the discussion plausibly write or
  expect this?** If so, teach against it. When a restriction exists for a
  reason, teach the reason as a consequence in the reader's terms, for example
  "if every type accepted added storage, no library could know any type's
  size", rather than as the decision that was taken.
- **Can you name the question this example answers for the reader at this
  point?** State that situation before the snippet and the consequence after
  it. If you cannot name the question, the example probably belongs elsewhere or
  not at all.

These are judgement aids, not a template.

## Teaching debt

Current language owners may accumulate teaching debt as a natural consequence
of incremental cross-owner design. This is not inherently an agent or process
failure: the active concern becomes more familiar than adjacent reader tasks,
later concepts change prerequisite order, and several individually reasonable
edits may become difficult for a cold reader only in combination.

That inevitability is not permission to lower the teaching standard. Agents
still make a conscientious reader-focused effort in every current `language/`
section they materially change. The teaching-debt process captures residual
problems despite that effort or outside the authorized change; it is not a place
to defer knowingly poor teaching.

Teaching debt is material reader friction in a current `language/` owner whose
meaning may be correct but whose motivation, examples, terminology order, costs,
or local handoffs make that meaning unnecessarily difficult to learn.

It does not include:

- semantic contradictions or unresolved design;
- style-only preferences;
- adequate prose that could merely be prettier;
- root legacy pages;
- `project/raw/`;
- `project/archive/`;
- or project-process prose.

### Opportunistic recognition

While reading current owners for assigned work, an agent may record concrete
firsthand teaching friction under an optional distinct heading in the active
work record. Do not search for teaching debt, scan unrelated current owners, or
read the [teaching-debt log](teaching-debt-log.md) to discover expected
observations. If required reading remains understandable under the criteria
above, do not invent a concern.

A useful observation identifies the current owner and section, the blocked
reader task, concrete evidence, why the issue is material, and possible
future circumstances that should trigger a refresh. It need not prescribe a
solution.

### Promotion and archival disposition

Promotion reconsiders provisional observations because the authorized rewrite
may resolve, consolidate, invalidate, or remove them.

Before archival, every observation is:

- resolved by promotion;
- discarded as mistaken or superseded;
- entered in the live
  [teaching-debt log](teaching-debt-log.md) when it remains valid in a current
  owner outside the authorized promotion; or
- retained as a promotion blocker when it remains in a section materially
  changed by that promotion.

No teaching concern may survive only in archived numbered work.

The log is read only as needed to update or deduplicate a specific independently
noticed concern. It is not ordinary onboarding, a checklist, or a test answer
key.

The language maintainer may select a bounded worst-first cluster from the log
for a separate teaching-debt work item. Suggested, motivated refresh techniques
are owned by [teaching-debt guidance](teaching-debt.md); they are not a rigid
content template.

## Authority and maturity

Document location does not create authority. Checked-in prose, polished
examples, generated output, plans, and agent-authored proposals remain evidence
until promoted deliberately.

Legacy and current conceptual syntax are evidence at their stated maturity, not
permanent compatibility anchors. Preserve a form when it remains coherent, but
revise it when concrete cross-feature pressure exposes ambiguity, inconsistency,
hidden cost or authority, or a locally convenient choice that harms the wider
language. Do not churn syntax for taste or preserve a poor solution merely to
avoid change. Trace every material consequence, and raise the threshold for
revision as implementation, users, formal contracts, and compatibility
commitments accumulate.

Use these decision-maturity states:

| State | Meaning |
| --- | --- |
| Raw input | Evidence, observations, concerns, rough ideas, or unresolved alternatives |
| Candidate | A coherent possible model that has not received sufficient review |
| Tentative proposal | A specific option offered for discussion |
| Open question | A consequential issue still requiring disposition |
| Aligned finding | Meaning accepted for the current review scope but not yet promoted |
| Accepted language design | An aligned finding incorporated into the document that owns the concern |
| Formal contract or specification | A future stable shape or rule on which named consumers may depend |
| Explicitly deferred | A question left for later with a reason and deciding pressure |
| Discarded or superseded | Material deliberately rejected or replaced |

Keep these distinctions visible:

- conceptual acceptance is not implementation;
- accepted design is not a formal specification;
- aligned working findings are not authoritative until promoted;
- historical records are not current owners;
- decision maturity does not authorize an edit.

### Current owners do not cite numbered work

A current programmer-facing owner must not cite a numbered work item or an
archived project record for its provenance or its meaning. Numbered work is a
process artifact whose file is eventually archived, and a public owner that
depends on it sends ordinary readers into project records or dead ends.

- Cite current conceptual predecessors or live legacy evidence instead.
- State a boundary as future work by its subject rather than by a work number.
- Keep numbered-work provenance in `project/` records. Active work files may
  retain it freely. A `project/raw/` input explains each finding in place and
  cites numbered work only in its `Source / Provenance` metadata, never in its
  body. A body reference invites future readers into archived work whose
  conclusions may since have been revised; archived work is read only in an
  intentional audit.
- Do not route ordinary readers from a current or public owner into
  `project/raw/` material.

New or materially rewritten owner documents should state the applicable parts
of:

| Field | Purpose |
| --- | --- |
| Status | Document lifecycle and authority |
| Audience | Primary readers |
| Applies To | Conceptual design, current behavior, contract, process, or another bounded purpose |
| Implementation State | Not implemented, partial, current, superseded, or not applicable |
| Owns | Concerns defined here |
| Does Not Own | Adjacent concerns owned elsewhere |
| Source / Provenance | Evidence or predecessor when useful |
| Supersedes / Superseded By | Ownership transitions when applicable |

`Does Not Own` identifies only plausible competing ownership or a direct
handoff. It is not an exhaustive exclusion list. Put detailed interactions,
future boundaries, and deferred mechanics in the relevant body or maturity
section instead of expanding metadata with everything the document does not
cover.

Do not retrofit untouched legacy pages merely for cosmetic consistency.

## Naming and structure

Use lower-case file and directory names except for the conventional
`README.md`.

Split documentation when audience, authority, lifecycle, ownership, or
independent reuse differs. Do not split a cohesive concept merely because it is
long. Conversely, do not preserve a giant document when its sections have
different owners or reading paths.

Structural changes require discussion, alignment, and explicit edit
authorization. Do not introduce speculative folder families, empty indexes, or
placeholder specifications.

## Focused reading

Focused reading is a correctness and token-cost requirement.

The agent's operating guidance has already been supplied in chat. A reader
assigned to one concern should begin with:

1. this documentation architecture;
2. the active numbered work file;
3. its small required-reading set;
4. consequence-driven material when a stated boundary is crossed;
5. additional focused reading when a concrete consequence establishes the need.

Agents do not proactively read the source files for the supplied handoff or
rehydration prompts. They read those files only when explicitly assigned to
maintain them.

Indexes route by task. Owner documents teach. Each owner should make its
prerequisites and adjacent boundaries clear without an exhaustive "see also"
list.

### Cross-linking

Links should help a reader follow ownership, migration, or a concrete conceptual
dependency. They are not a substitute for router documents.

- A legacy document may link to a current owner to identify which of its concerns
  migrated or was superseded.
- Current owners may link when one document's concern depends on, constrains, or
  hands ownership to another specific concern.
- A raw or deferred input may link to the owner or pressure that must consume it.
- Generic sibling discovery belongs in `README.md` and index routers.
- Do not add "nice to know" sibling links or exhaustive related-document lists
  without a reader task or ownership reason.

Repository-wide reading is not required before contributing. Raw, public,
implementation, research, and historical material remain available when a
concrete consequence establishes relevance. Archived material remains
audit-only.

A reading scope defines an efficient starting path, not the complete universe of
material that may become relevant. If discussion or a finding crosses into
another concern, inspect the smallest relevant owner or section, state why it
became necessary, and capture the impact. Avoid unbounded scans and speculative
reading.

### Correcting legacy examples

Legacy snippets may intentionally omit surrounding declarations or other context.
Determine their intended semantics from nearby prose and the concern being
illustrated rather than assuming every isolated fragment is a complete program.

During a focused review or promotion:

- preserve assignment examples that assume existing names;
- correct declaration punctuation when doing so clarifies the example's existing
  intent;
- add only the smallest useful declaration or comment when omitted context is
  unnecessarily confusing;
- record ambiguity instead of choosing new language behavior silently;
- defer correction when the right form depends on an unreviewed concern; and
- avoid unrelated corpus-wide cleanup merely to make every fragment independently
  compilable.

Changing `x = y` to `x := y` is a semantic change from assignment to declaration,
not a mechanical completeness fix. Apply it only when introduction of `x` is the
stated or strongly evidenced intent.

## Numbered work

There is at most one active `00n-<concept>.md` file in `project/work/`.

The number records sequence; the slug names the concern. Do not use `concept`
as a literal default slug. The current `001-concept.md` name is appropriate
because its subject is the concept of Zax as a whole. A work item about another
subject uses that subject in its filename.

### Minimal file shape

Use only the stable divisions needed to preserve the work:

```markdown
# 00n: <concept title>

## Non-authority notice

## Fixed initiating input

## Reading scope

## Working record

## Dispositions and promotion dry run
```

The final section may remain absent or empty until the work approaches
promotion. Add other headings only when they make the particular concern easier
to understand.

Do not force every statement into a permanent category such as "raw
observation," "candidate model," or "impact trace." Organize the working record
naturally. Use explicit maturity labels in prose or local subsections when a
reader could otherwise confuse an observation, proposal, question, aligned
finding, or deferral.

### Fixed initiating input

The initiating input contains the aligned information known when the work item
is created, such as:

- the initiating concern;
- motivating pressure, when known;
- known assumptions, initial focus, or starting boundaries;
- deliberately unresolved framing;
- initial stopping guidance, when useful.

These elements are not exhaustive and need not all be supplied. A starting
boundary describes the initiating concern without prohibiting investigation of
material consequences.

Once created, the fixed initiating input is immutable. Record later
clarifications, discovered boundaries, corrections, and consequences in the
working record rather than rewriting what initiated the work. If the frame
proves materially unsuitable, record that finding and prepare a later work item
instead of silently broadening it.

### Reading scope

Name the smallest useful required-reading set and explain why each item matters.
Identify consequence-driven reading by the boundary that triggers it. Leave
other material unlisted rather than predicting that it will or will not become
necessary.

Use an audit-only category for archived, superseded, or confirmed dead material
that should not enter ordinary reading. Audit-only material requires direct
human instruction or a concrete audit or recovery need.

The scope may expand when discussion or a concrete consequence makes additional
material relevant. Explain the need and read only enough to follow that
consequence responsibly.

### Consequences and deferrals

Keep the initiating concern fixed while tracing material consequences.

Continue when a consequence is necessary to resolve the concern. Trace an
immediate dependency far enough to expose a constraint or contradiction.
Capture independently decidable impacts without designing them prematurely.
Avoid remote speculation without concrete pressure.

Do not use "out of scope" to erase a material finding. A useful deferral records:

- the finding;
- why it matters;
- why it can wait;
- the future question, use case, or pressure that should reopen it;
- any constraint it places on the current conclusion.

An unresolved contradiction cannot be hidden by deferral if it invalidates the
current proposal.

Design may be deferred, but capture may not. Record every material consequence,
assign it a live owner or indexed raw destination, identify what reactivates it,
and state any constraint it places on current work. A deferred consequence must
not lose its home when numbered work is archived, and archived work must not
become a current owner's source of meaning.

Discussion that exposes concrete future design pressure creates this capture
obligation even when the maintainer does not separately repeat “capture this.”
The obligation does not itself authorize an edit; normal discussion, alignment,
and explicit edit authorization still apply.

Until promotion, the active work file is the normal holding place for a
capture: record the finding, its destination, and what the destination must
preserve. The promotion change set writes it into the raw input or owner.

The active work file evolves only through discussed, aligned, and explicitly
authorized edits.

## Pre-promotion documentation fit dry run

The dry run is a read-only pre-promotion step used only when a numbered work item
is approaching incorporation into lasting repository documentation.

Do not perform it after each edit to the active work file. Do not require it for
unrelated mechanical documentation corrections. Its purpose is to test whether
the wider documentation is ready to absorb the findings from `00n`.

The dry run:

1. Maps every aligned finding to one lasting owner.
2. Identifies every existing file affected by the promotion.
3. Verifies that the proposed owners can absorb the material coherently.
4. Identifies missing, overloaded, or incorrectly bounded owner documents.
5. Identifies terminology, index, navigation, reading-path, and reference
   changes.
6. Separates material that must remain candidate, deferred, future
   specification, or implementation pressure.
7. Identifies relocations and archive effects.
8. Produces a proposed ownership map and minimal directory/index structure able
   to hold every disposition without creating duplicate authority or orphaned
   material.
9. Produces the exact proposed promotion change set.
10. Records a short teaching plan for each owner section the promotion
    changes: why a reader is there, what they already know at that point, what
    changes for them, and what they might wrongly try.

Record the result in the active work file and report **PASS** or **FAIL** in
chat. FAIL means the dry run found material design, ownership, or integration
questions that require new alignment before promotion can begin. PASS means the
aligned findings have a coherent owner structure, human reading path, deferred
destinations, and exact promotion set; it does not authorize promotion.

A dry run tests cohesive teaching as well as unique authority. A concern may
have one owner per rule yet still require a dedicated concept owner when readers
would otherwise reconstruct one feature across several local documents. A
material issue discovered during promotion review revises the recorded dry-run
result and change set before closure rather than being ignored because an
earlier result passed.

The dry run applies the cold-reader, vocabulary-before-use,
concrete-paraphrase, teaching-versus-reference, and two-pass promotion checks. It
fails when ownership is unique but readers must still reconstruct the concept
from abstract or poorly ordered material.

A disposition ledger or ownership map supports this work but does not replace
understandable findings. Preserve each material finding in plain language with
enough examples and reasoning for the maintainer to recognize what the later
owner must teach.

Resolve architecture gaps through discussion before promotion. The dry run does
not edit files, promote findings, or authorize later edits.

The dry-run result always includes a structure proposal, even when the proposal
is to retain the current layout. It explains:

- which current and proposed documents own each concern;
- which files remain routers, active owners, raw inputs, or historical records;
- the smallest directory and index changes needed;
- how focused reading reaches each owner;
- how raw or deferred material remains discoverable without entering ordinary
  reading paths; and
- which existing paths move, remain, or retire.

The structure is a proposal for discussion, not permission to scaffold or move
files.

## Promotion

Promotion is a separate authorized act, not a status label applied inside the
working file.

After the dry run:

1. Discuss its complete result.
2. Align on the lasting owners and coherent repository-wide change set.
3. Wait for explicit edit authorization.
4. Apply only the authorized promotion.
5. Validate the resulting current documentation.

Relocate useful content before archiving the working file. Current owners must
not depend on archived work for meaning.

The language maintainer may stage changes to preserve a before/after comparison
boundary. Staging does not imply acceptance, approval, or permission to commit.
Do not alter the staged boundary without explicit authorization.

## Closure, archival, and continuation

When item `n` closes:

1. Give every material finding a disposition.
2. Complete the dry run, promotion, and validation when promotion applies.
3. Mark the working file historical, non-normative, and audit-only.
4. Move `project/work/00n-<concept>.md` to
   `project/archive/work/00n-<concept>.md` without changing its filename.
5. Propose the fixed initiating input and focused reading scope for `n+1`.
6. Discuss and align `n+1` with the language maintainer.
7. Wait for authorization to create it.
8. Create `n+1` with a subject-specific filename and update the current-work
   index in `project/README.md`.
9. Stop before analyzing `n+1`.

The agent that creates `n+1` does not begin it until the language maintainer
explicitly assigns the new work unit. A fresh session or the same session may
receive that assignment.

Current documents do not link to archived work as ordinary supporting material.
The archive index may identify completed work for targeted discovery.

Archived records are historical snapshots, not a second live documentation
tree. Their prose and links may name paths that existed when the work was
performed. A targeted audit uses Git history to recover that repository state.
Do not retain otherwise valueless compatibility stubs or rewrite archived
records merely to keep every historical link resolving in the current tree.

## Operating-prompt source maintenance

The new-session handoff source and post-compaction rehydration source are
mutually exclusive prompts. An agent receives one block in chat and never needs
the other for that entry or recovery path.

The two prompts intentionally duplicate safety-critical operating guidance.
They do not reference each other or require an agent to reconcile them during
normal work.

Both sources keep `project/work/00n-<concept>.md` as a generic placeholder. The
language maintainer replaces it in the copied text with the active path from
`project/README.md`. Do not update or commit either prompt source merely because
work moves from `n` to `n+1`.

Their operating-guidance revision, stable project phase, language-document
status, implementation state, and shared operating rules must remain
synchronized. When those rules change and maintenance is explicitly authorized,
inspect and update both in the same coherent change set.

Detailed documentation rules remain here rather than being copied into both
operating prompts.

## Website and repository navigation

All repository material must be public-safe.

Public website navigation routes human developers to current language
explanations, not active or archived project work. Project files may remain
reachable to someone who browses the public repository or knows their URL; they
need not be hidden from the site build.

The live `zax.io` website is rendered from an upstream version of this
repository. Current work refines and validates the documentation here. Do not
open pull requests or synchronize upstream as part of incremental concept work;
that integration is deferred until the repository has been brought into shape
and the language maintainer explicitly authorizes it.

Repository navigation routes project contributors from `README.md` to
`language/vision.md` or `project/README.md` according to their task. The
language maintainer supplies the appropriate operating prompt to an agent.

Validate both rendered website use and direct Markdown use. Relative links
should work for repository readers as well as the website wherever practical.

## Validation

For an authorized documentation change, check the applicable items:

- The changed concern has one owner.
- Authority and implementation state are not overstated.
- Human-developer-facing detail supports a programmer's mental model.
- A reader entering an edited owner or linked section can understand its
  immediate purpose, representative behavior, and route to deeper rules without
  reconstructing project history.
- After a substantial promotion, an agent that has not read the working record
  reviews the changed owner sections as a reader arriving directly. It reports
  what it cannot follow, leaked discovery history, and examples without an
  evident purpose. The promoting agent's own cold-reader check is not a
  substitute, because it shares the working context.
- Mirrored stable operating-prompt guidance agrees.
- Both operating-prompt sources retain the generic active-work placeholder.
- The `project/README.md` active-work pointer and next number agree with actual
  numbered work.
- Required reading is minimal and reasoned.
- Consequence-driven reading has a concrete trigger.
- Additional reading remains possible when justified.
- Audit-only material is absent from ordinary routes.
- Current references do not depend on archived material.
- Provisional teaching observations are resolved, discarded, logged, or remain
  explicit blockers before archival.
- The teaching-debt log contains only unresolved current `language/` concerns
  and is absent from ordinary concept-work reading.
- Moves update destinations, indexes, and references coherently.
- No obsolete compatibility stub remains without a real constraint.
- Lower-case naming is followed except for `README.md`.
- Local links and heading anchors resolve throughout the live tree. Historical
  links inside `project/archive/` may require Git history and are excluded from
  live-link validation; the current archive router's own live links must still
  resolve.
- Markdown renders coherently.
- Staged and unstaged review boundaries are preserved, including untracked paths
  when that boundary is being relied upon or reported.
- The final changed-file set matches the explicitly authorized scope.
