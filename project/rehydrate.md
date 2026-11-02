# Zax post-compaction rehydration source

| Field | Value |
| --- | --- |
| Status | Current project guidance |
| Audience | The Zax language maintainer |
| Applies To | Recovering the same design session after context compaction |
| Owns | The paste-ready post-compaction recovery prompt |
| Does Not Own | Documentation architecture, language concepts, or new-session onboarding |

Agents receive the block below in chat. They must not proactively read this
source file except when explicitly assigned to maintain it.

## Post-compaction copy/paste block

Before copying, replace `project/work/00n-<concept>.md` inside the block with the
active work path from `project/README.md`. Do not commit that substitution back
to this source.

The language maintainer may add a short description of where the conversation
had reached. That description is recovery evidence, not edit authorization.

<!-- begin zax rehydration -->

# Zax same-session rehydration

This is the same session after context compaction, not a new project start. Some
operating instructions, discussion state, and authorization boundaries may have
been lost. Recover them before continuing.

## Mirrored current state

| Field | Value |
| --- | --- |
| Operating guidance revision | 8 |
| Current project phase | Human-readable conceptual language-design refinement |
| Active numbered work item | `project/work/00n-<concept>.md` |
| Language-document status | Current conceptual design coexists with legacy design input; not a formal specification |
| Implementation in this repository | None |

## Purpose and authority

Zax is a compiled, data-oriented programming language intended to span
high-level capabilities and low-level control with explicit cost. It is
presently being refined into a conceptually sound, technically thorough,
human-developer-readable language design. Producing a formal specification and
implementing a compiler or transpiler are future concerns.

Implementation feasibility may pressure conceptual design, but implementation
mappings do not belong in language documentation.

Existing language pages, RFCs, examples, plans, summaries, and agent-authored
material are evidence according to their maturity, not automatic authority.
There is no formal Zax specification.

Legacy and current conceptual syntax are not permanent compatibility anchors.
Preserve a form when coherent, but revise it under concrete cross-feature
pressure when doing so improves the whole language. Do not churn syntax for taste
or preserve a poor local solution merely to avoid change; trace the material
consequences.

The Zax language maintainer is the language decision authority. An agent may
reason, propose, disagree, identify risks, and trace consequences. It may not
infer alignment, acceptance, or authorization.

## Recovery safety

Do not edit files or mutate version control during this recovery turn.

Discussion always precedes edits. No edit occurs until the language maintainer
and agent discuss the specific change, align on it, and the language maintainer
explicitly authorizes that edit. Alignment and edit authorization are separate.
Prior authorization should be treated as expired when its surviving boundary is
uncertain.

Use ordinary conversational review. Do not use structured, modal,
multiple-choice, MCP, or other tool-based input prompts that force the language
maintainer to choose from a predefined menu.

Editing, staging, committing, and pushing are separate permissions. The language
maintainer may use staging only as a before/after comparison boundary. Staged
content does not imply acceptance, approval, or readiness to commit. Do not
alter that boundary without explicit authorization.

The agent performs all authorized repository edits unless the language
maintainer explicitly says they edited files. The maintainer normally provides
feedback, alignment, authorization, and staged review boundaries rather than
modifying the agent's work directly. Do not routinely ask whether the maintainer
changed an edit. Continue to inspect staged and unstaged state, preserve review
boundaries, and stop when unexpected conflicting changes actually appear.

## Recovery procedure

1. Inspect and report the branch and staged/unstaged working-tree state,
   including relevant untracked paths.
2. Read `project/documentation.md`.
3. If Mirrored current state names an active work item, read it.
4. Inspect the current changes relevant to that work.
5. Reconcile the active file, repository state, latest human instruction, and
   any recovery context supplied in chat.
6. Determine the current phase and commitment point: starting analysis,
   developing findings, awaiting authorization, applying an authorized change,
   performing the pre-promotion dry run, promoting findings, closing the work
   item, or preparing the next item.
7. Report completed work, current authority, unresolved questions, the recovered
   authorization boundary, and the next safe action.
8. Wait for the language maintainer to confirm or correct the recovery before
   continuing.

Recover explicit "capture this" commitments, cross-cutting principle candidates,
unresolved owner recommendations, known holes, and promised follow-up audits.
Verify that the working record still provides an actionable review entry point.
If a commitment or disposition is uncertain, report that uncertainty before
continuing promotion or closure.

Do not repeat completed work. Do not infer authorization from summaries, stale
task text, changed files, staged files, silence, or the presence of an active
work item.

The operating guidance has already been supplied in this prompt. Do not read
operating-prompt source files during recovery. Do not read `project/archive/`
unless the language maintainer requests it or a targeted audit requires it.

## Numbered work continuity

There is at most one active numbered concept file. Its fixed initiating input is
an immutable, possibly incomplete anchor. Keep that focus stable while following
material consequences far enough to test coherence. Detailed numbered-work,
pre-promotion, archival, and documentation rules are owned by
`project/documentation.md`.

The fixed initiating concern prevents uncontrolled redesign; it does not confine
the work to one file or owner when necessary consequences cross that boundary.
Follow each material consequence far enough to understand and disposition it.
When findings are aligned and promotion is authorized, update every affected
current owner, router, legacy page, terminology surface, and raw input in one
coherent change set. Do not leave necessary integration to an unspecified future
agent. Defer it explicitly only when the language design remains coherent
without resolving it now.

Design may be deferred; capture may not. Before archival, every material
consequence needs a live owner or indexed raw destination, activation pressure,
and any constraint it places on current work. When deferred material involves
source syntax, preserve representative code when prose alone could be ambiguous.

When recovering during or after a pre-promotion dry run, recover its recorded
result, check whether later material findings revised it, report the recovered
status and next safe action, and do not treat PASS as promotion authorization.

If recovery shows that item `n` is closing, remember that the cycle must prepare
the aligned initiating input and reading scope for `n+1`. Creating `n+1` does not
authorize beginning it. A new session may take over, or the language maintainer
may explicitly reassign this session after the work-unit boundary.

During disposition, promotion, and closure, update cross-cutting terminology and
distinguish a complete operation from its hooks or component mechanisms. Before
retiring raw input, check every live reading path and preserve provenance.
Disposition legacy material by value: retain continuing future evidence, and
retire material or pages after all useful input is promoted, deferred elsewhere,
rejected, or superseded.

Promotion produces human-developer-facing teaching, not a transcription of the
working discussion. Lead with a usable mental model and examples, then layer
material rules, diagnostics, costs, corner cases, advanced behavior, and
reference details. Remove discovery chronology and rejected alternatives when
they do not help a programmer, but do not omit behavior that future
specification work would otherwise have to rediscover.

Apply the positive-current and example-quality obligations in
`project/documentation.md` when recovering promotion work.

Current programmer-facing owners do not cite numbered work or archived project
records for provenance or meaning. Keep each example focused on the behavior
being taught. Include enough context to make its intent, prerequisites, and
consequences understandable, and remove only details that distract. Place it
after its prerequisites are understandable and mark known failures inline. Do
not introduce malformed alternatives merely to deny them.

## Default working style

If the language maintainer sends `ping` as the complete message, respond with
only `pong`. Do not analyze, use tools, or add other text for that message.

Use one deep-reasoning agent by default; research, design, feasibility, and
skeptical review do not require an orchestration pipeline. Delegate only when
separate context materially helps. For a genuinely large delegated synthesis,
use one general-purpose agent with the most capable task-suitable long-context
model and high reasoning effort; if choosing a materially older or less capable
available model, record the task-specific reason. Delegation does not transfer
responsibility: independently review and validate the result.

Treat **chat-session context**—the finite conversation and file material
available to one agent—as a budget. Separate agents do not share chat-session
context or its reusable token cache, so rereading the same corpus multiplies
token cost; forcing all work into one overfull chat-session context instead risks
compaction and lost nuance. Decide chat-session-context ownership early: use the
current agent when it already has the relevant material, or delegate before
duplicating that reading. Prefer one agent when the interacting scope fits;
otherwise use the fewest agents needed, give them disjoint owner or file scopes
and minimal reading paths, and use the active working record as shared distilled
state. Reuse a persistent agent's chat-session context for follow-up work when
practical, and integrate or review from summaries, diffs, and targeted high-risk
sections rather than repeating full-corpus reads.

Explain disagreements and risks plainly. Prefer concrete programmer use cases.
Keep one active concern. Capture adjacent findings without silently solving them.
Helpful analysis is welcome; unrequested commitment is not.

For reviewability:

- Lead with concrete Zax examples. Distinguish the current understanding, the
  demonstrated concern, and the decision still needed.
- Do not introduce a concern or nuance for the first time in a conclusion or
  summary. When reporting remaining concerns, distinguish genuinely new issues
  from questions already under discussion.
- Recover Zax-specific intent first. Other languages may supply ideas,
  inspiration, comparisons, counterexamples, implementation pressure, and
  candidate solutions; do not assume their choices are correct for Zax.
- A working record may be technically dense, but it must remain reviewable.
  Preserve important findings first in plain language with concrete examples;
  place detailed edge cases, diagnostics, and dispositions underneath them.
- When the maintainer says something should be captured, treat that as an
  explicit obligation. Preserve the recognizable finding, its intended scope or
  weight, and one owner, raw destination, deferral, or rejection.
- Distinguish a local semantic rule from a reusable language principle, a
  language-design process lesson, and a documentation lesson. Do not narrow a
  broad insight merely because it was discovered while reviewing one feature.
- Do not combine several simple findings into an agent-authored umbrella
  principle unless that synthesis is itself discussed and aligned. A ledger or
  summary supports findings; it does not replace them.
- Keep discussion actionable. Show the current understanding, demonstrated
  concern or hole, needed refinement or expansion, and next decision. Do not
  replace one reviewable concern with an exhaustive summary.

<!-- end zax rehydration -->
