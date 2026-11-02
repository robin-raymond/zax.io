# Raw input: library surface and namespaces

| Field | Value |
| --- | --- |
| Status | Raw future-work input / non-authoritative |
| Audience | Future work deciding canonical library namespaces, protected root exposure, and exact aliases |
| Applies To | Public placement of opaque facilities, callable metadata, text identities, and the `Legacy` anchor |
| Owns | Placement pressure, candidate exposure shapes, activation pressure, and retirement criteria |
| Does Not Own | The semantics of the named facilities, current name lookup, accepted imports, or scalar ownership |
| Source / Provenance | Work item `027` promotion review and existing string, pointer, callable, and namespace owners; work item `034` execution-context naming |

## Why this input exists

Several accepted or candidate facilities need stable public names without yet
having an accepted canonical library home. Existing unqualified examples do not
decide whether a declaration is canonical at the protected root, exposed there
by an exact alias, or owned by an imported library namespace.

This input records the placement question without choosing among those shapes.
Future work must consider:

- discoverability and ordinary source brevity;
- collision risk and protected-root growth;
- whether an exact root alias preserves one canonical identity;
- import and module-instance boundaries;
- grouping for reflection and generated documentation;
- compatibility if a provisional spelling moves; and
- implementation or platform availability without changing name identity.

## Opaque ownership and observation facilities

Canonical placement remains unresolved for:

- `OpaqueOwner`;
- `OpaqueObserver`; and
- `OpaqueReferenceObserver`.

These are semantically owned by the pointer, ownership, lifetime, and safety
documentation. This file decides neither their behavior nor whether they belong
at root, under a pointer/runtime namespace, or behind exact root aliases.

## Callable metadata

Canonical placement remains unresolved for candidate
`Callables.BindingKind` and other callable metadata. Future placement work must
decide whether `Callables` is the lasting namespace, whether selected metadata
also receives protected short aliases, and which metadata is language-defined
versus reflection- or library-provided.

This file does not activate candidate metadata or settle its representation.

## Text and character identities

Canonical placement remains unresolved for:

- `String`;
- `AsciiString`;
- `Ucs2String`;
- `Utf8String`;
- `Utf16String`;
- `Utf32String`;
- `Rune`;
- `AsciiChar`;
- `Ucs2`;
- `MbcsString<Encoding>`;
- `Legacy.CharString`;
- optional `Legacy.WideString`;
- `Legacy.TerminatedCharString<Policy>`;
- `Legacy.TerminatedWideString<Policy>`; and
- the canonical namespace anchor `Legacy`.

Future work must decide which names are canonical at root, which are exact root
aliases, and which live only in a text, encoding, platform, or legacy namespace.
The optional wide identities must remain symbol-aware: absence on a platform
must not be disguised by manufacturing a substitute identity.

`U8`, `U16`, and `U32` remain under `Scalars`. This placement review does not
move or duplicate them merely because text identities use those scalar storage
units.

## Execution context type

The execution context's type is named `ExecutionContext`, so that partials can
target it. Its namespace is unresolved and should be decided only once every
system type needing a namespace is known. Considerations so far:

- it cannot live under `Scalars`;
- `Execution` could group the future thread, task, and context facilities,
  giving `Execution.ExecutionContext` or `Execution.Context` with a short alias;
- `Runtime` risks conflict with a compile-time host context;
- `Concurrency` is too narrow for a context that holds default arenas; and
- `System` suggests the operating system and tends to become a catch-all.

## Boundaries

A placement decision must not:

- change the semantic identity or validation rules of a type;
- create a second nominal identity through an alias;
- imply that every platform provides an optional legacy facility;
- move intrinsic scalar ownership out of `Scalars`;
- make importing one namespace silently re-export another; or
- treat illustrative qualified names as accepted source before review.

## Activation and retirement

Activate this input when canonical library modules, protected root declarations,
exact root aliases, callable metadata namespaces, text namespaces, or the
`Legacy` anchor are reviewed.

Move accepted placement into the namespace/module owner and each affected
domain owner. Retire this file after every listed declaration has an accepted
canonical home or alias policy, or its placement question has been explicitly
rejected or moved to a narrower live destination.
