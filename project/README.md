# Zax project guidance

This directory contains the public-safe project guidance and working material
used to refine Zax. It is not part of the ordinary language-documentation
reading path.

## Start here

| Task | Action |
| --- | --- |
| Understand Zax's accepted foundational direction | Read the [language vision](../language/vision.md) |
| Start a new Zax design session | The language maintainer copies the block from [the handoff source](handoff.md) into the new session |
| Recover the same session after context compaction | The language maintainer copies the block from [the rehydration source](rehydrate.md) into that session |
| Understand what, where, and how Zax documents | Read the [documentation architecture](documentation.md) |
| Perform an assigned teaching-debt refresh | Read the selected [teaching-debt log](teaching-debt-log.md) entries and [refresh guidance](teaching-debt.md) |
| Choose a bounded future teaching refresh | Review the [teaching-debt log](teaching-debt-log.md) |
| Conduct a targeted audit of completed work | Read the [archive guidance](archive/README.md) |

## Current numbered work

| Field | Value |
| --- | --- |
| Active work item | None; `036` has not been prepared |
| Next work number | `036` |

Agents receive either the handoff block or the rehydration block in chat. They
must not proactively read either source file during normal work. Read them only
when the language maintainer explicitly asks for their maintenance.

Before pasting an operating block, the language maintainer replaces its generic
`project/work/00n-<concept>.md` placeholder with the active path listed above.
The committed prompt sources remain generic across numbered work items.

Teaching-debt guidance and the log are task-specific routes. Agents do not read
the log during ordinary concept work or use it to search for expected problems.

Maintain the operating prompt sources together only when their shared working
rules or revision change.

Completed working files are not ordinary reading. Do not inspect
`project/archive/` unless the language maintainer directly requests it or a
targeted audit requires it.
