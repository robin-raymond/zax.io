# Zax teaching-debt refresh guidance

| Field | Value |
| --- | --- |
| Status | Current project guidance |
| Audience | Language maintainers and agents assigned a bounded teaching refresh |
| Applies To | Improving materially difficult teaching in current `language/` owners without changing accepted meaning |
| Owns | Why teaching debt accumulates; selection of a bounded refresh; suggested reader-first techniques; semantic stopping boundaries; validation and log disposition |
| Does Not Own | Language design; ordinary concept-work reading; the live debt queue; raw, legacy, archive, or project-document prose quality |

## Why teaching debt happens

Teaching debt is a natural consequence of incremental language-design work. Its
existence does not mean that an agent failed or that the documentation process
failed.

During one concept review:

- the active concept becomes much more familiar than every adjacent owner's
  reader task;
- a necessary local integration may be accurate before the wider concept is
  familiar enough to teach naturally in that location;
- an explanation that worked under an earlier design may become poorly ordered
  after later concepts depend on it;
- several individually reasonable edits may accumulate prerequisites and
  terminology that no one edit made visibly difficult;
- and a cold reader may experience friction that an author already familiar with
  the design cannot experience.

Trying to prevent all teaching debt would require either perfect foresight or
unbounded rewriting during every promotion. Neither produces good language
documentation. The project instead records concrete reader friction and
periodically refreshes the worst bounded cluster after enough context exists to
improve it responsibly.

Teaching debt is unavoidable residual risk, not an acceptable output target.
The blame-free model does not reduce the effort expected in current
`language/` sections an agent materially changes. Agents still make a
conscientious reader-focused effort with the context available, explain the
local reader task and motivation, and seek discussion rather than inventing
dense pseudo-precision when an adjacent area is not understood well enough to
reteach.

A known teaching problem in a section materially changed by the current
promotion remains a promotion blocker. The debt log is reserved for residual
problems outside the authorized change or remaining despite a genuine effort.

## When this guide is used

Read this guide when:

- the language maintainer assigns a teaching-debt refresh;
- a numbered work item names selected entries from the
  [teaching-debt log](teaching-debt-log.md); or
- promotion must disposition a specific teaching concern already noticed during
  focused reading.

Do not read the debt log merely to discover what problems an ordinary concept
agent is expected to notice. Ordinary recognition and promotion lifecycle are
defined by [documentation architecture](documentation.md#teaching-debt).

## Select a bounded cluster

A refresh starts from concrete evidence, not from a general request to make the
documentation better.

Useful selection pressure includes:

- the same section repeatedly causes a concrete misunderstanding;
- several entries identify one underlying presentation problem;
- a high-traffic owner blocks later reading;
- a foundational concept is harder to learn than the advanced material that
  depends on it;
- or an upcoming work item needs a clearer prerequisite.

Each log entry describes **when to revisit** the concern in plain language,
rather than relying on internal project shorthand.

Prefer the smallest owner set that can resolve the reader problem coherently.
Do not add nearby pages merely because their prose could also be improved.

Slightly awkward but understandable prose is not automatically worth a refresh.
The purpose is to improve material reader outcomes, not to pursue stylistic
perfection.

## Recover meaning before rewriting

Teaching refreshes preserve accepted language design.

Before changing a section:

1. identify its current owner and direct prerequisites;
2. recover the accepted behavior that must remain true;
3. identify examples, costs, diagnostics, and corner cases that must survive;
4. distinguish local teaching from a foreign owner's complete rule;
5. stop if an apparent wording problem is actually semantic uncertainty.

When semantic uncertainty appears, return it to ordinary discussion and
language-design work. Do not decide it silently as prose cleanup.

## Start from the reader's task

A useful refresh asks:

> What is this programmer trying to understand or do when entering this section?

Then show enough valid source and plain-language consequence for that task before
requiring the reader to decode the full technical model.

One possible authoring brief is:

```text
Reader task
Opening valid example
Recognizable problem
Plain-language outcome
Vocabulary introduced after that outcome
Common path
Visible cost or failure
Later reference material
Foreign-owner handoff
```

This is a diagnostic aid, not a required template. Omit, reorder, or replace its
parts when another structure teaches the material better.

## Techniques that often help

### Lead with a concrete reason

Show why the feature matters before listing every rule:

```zax
snapshot := source
independent := source as deep
```

Explain the sharing or independence question first. Introduce precise selection
and qualification terms afterward.

### Separate syntax from selected behavior

When source only presents intent, say so explicitly:

> `source as move` changes which consumer can be selected. It does not transfer
> resources by itself.

This distinction is easier to retain when the example shows both the source form
and the selected consumer's consequence.

### Explain why related forms differ

A paired example may teach more than two isolated definitions:

```zax
process final : ()(
  input : Message move
) = {
}

process final : ()(
  input : Message mutable writable & move
) = {
}
```

Explain which object each form affects and why before presenting their complete
selection matrix.

### Earn technical vocabulary

Formal terms are useful after their meaning is visible. Prefer:

> Neither candidate is better overall: one copies, while the other may change
> caller-owned storage.

before:

> The candidates are incomparable.

The later reference section can then use the concise exact term.

### Use tables to consolidate

Tables work well after the reader understands what varies. A table should help
compare an established model rather than serve as the only explanation of that
model.

### Keep local handoffs meaningful

A link does not replace local teaching. Explain why the foreign rule matters to
the reader's immediate task, then link to its complete owner.

### Preserve technical depth

Improving the opening does not justify deleting:

- costs;
- diagnostics;
- failure modes;
- advanced examples;
- qualifier matrices;
- source-stability constraints;
- or future formal pressure.

Move those details after their prerequisites or into a clearly useful reference
layer.

## A teaching pass and a completeness pass

One useful workflow is:

1. **Teaching pass.** Rewrite the common reader path from motivation, valid
   source, and plain consequence.
2. **Completeness pass.** Trace every accepted finding, cost, diagnostic, corner
   case, and ownership handoff into the teaching or later reference layers.

The point is to prevent the finding ledger from becoming the presentation
outline. Another workflow remains valid when it achieves the same reader outcome
and completeness trace more effectively.

## Validate the refresh

Useful cold-reader questions include:

- Can a programmer explain why this section exists?
- Can they predict what the first valid example does?
- Can they distinguish what source syntax presents from what selection or
  execution performs?
- Does each precise term have enough prior meaning to be useful?
- Are costs and important failures visible?
- Do tables consolidate rather than replace the model?
- Does a direct-entry reader understand why each foreign link matters?
- Did the rewrite preserve accepted behavior?

Also apply the ordinary documentation validation defined by
[documentation architecture](documentation.md#validation).

## Update the debt log

After the authorized refresh:

- remove entries that are resolved;
- narrow entries that are only partly resolved;
- discard entries shown to be mistaken or superseded;
- move newly exposed semantic questions into language-design work;
- and leave unrelated entries untouched.

The log is a queue of unresolved observations, not a history of completed
rewrites.

## Boundaries

Teaching-debt refreshes apply only to current `language/` owners.

Do not include:

- root legacy pages;
- `project/raw/`;
- `project/archive/`;
- project process prose;
- semantic contradictions;
- unresolved design;
- or style-only preferences.

Raw and legacy files are updated only when their own evidence/disposition
lifecycle requires it, not for teaching polish.
