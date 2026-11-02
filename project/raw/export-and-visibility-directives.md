# Raw input: export and visibility directives

| Field | Value |
| --- | --- |
| Status | Raw future-work input / non-authoritative |
| Audience | Future work defining exact private, export, selective exposure, and re-export source |
| Applies To | Compiler-directive and declaration syntax layered over the current module visibility baseline |
| Owns | Preserved exact-syntax questions, representative source pressure, activation pressure, and retirement criteria |
| Does Not Own | Current module-internal defaults, explicit-export requirement, import identity, or name lookup ([namespaces and modules](../../language/namespaces-and-modules.md)) |
| Source / Provenance | Legacy compiler-directive and namespacing material narrowed after namespace/module review |

## Current semantic baseline

Future syntax must preserve:

- declarations are module-internal by default;
- source files are not separate default visibility scopes;
- nothing crosses an import unless explicitly exported;
- importing a module does not automatically re-export it;
- a named import binding may be explicitly exported;
- visibility filters eligibility before callable/operator preference;
- imported collision provenance remains visible; and
- no source, declaration, file, import, or module order breaks a tie.

These semantics are current conceptual design. This raw input does not reopen
them.

## Current export directive

Export is now a current directive. Attached `[<export>]` exports one
declaration, and an `[<export=always>]` ... `[<export=never>]` range exports the
declarations written directly inside it, bounded by its opening or file. A
range never exports import bindings, aliases, injected declarations,
declarations from partials elsewhere, or nested namespace contents. See
[namespaces and modules](../../language/namespaces-and-modules.md#exporting-with-directives)
and [compiler directives](../../language/compiler-directives.md#directive-ranges).

## Remaining syntax pressure

Legacy material also proposed `[<export=yes>]` and `[<export=no>]` for a single
following declaration. Current attachment makes `yes` redundant with the
attached form; whether a `no` exclusion inside a range is useful remains open:

```zax
// Illustrative; not current.
[<export=always>]

open final : ()() = {
}

[<export=no>]
internalHelper final : ()() = {
}
```

Future work must still decide whether selective import/exposure lists
complement the directive.

## Alias and re-export pressure

An alias may deliberately export only its new spelling while the original name
remains unavailable:

```zax
// Illustrative export marker.
[<export>]
PublicType :: alias type InternalType
```

Future work must define:

- whether the target must itself permit re-export;
- how an exact variable or namespace alias is exported;
- how a polymorphic variable family is selected;
- whether an exported import binding preserves one generative module instance;
- how selective exposure renames one declaration;
- whether an exported namespace path recursively exports members or only the
  path;
- how later exports added below that path affect importers; and
- diagnostics when two selected exports collide.

An exported named import must preserve the existing module-instance identity. It
does not perform another import.

## Private pressure

Exact `private` behavior remains unsettled outside already current type-member
eligibility. Future review must decide:

- whether module/namespace-private declarations are visible throughout the
  owning module instance or only an owning declaration context;
- whether source-file privacy is independently useful despite files not being
  namespaces;
- how injected declarations select target-module privacy;
- whether an alias can expose a private target under owner authority;
- how private candidates affect declaration reflection; and
- whether a visibility change can activate a nearer lexical scope or another
  callable candidate.

Ineligible declarations do not compete as worse candidates. Explicitly
qualified inaccessible source reports visibility failure rather than searching
another root.

## Activation and retirement

Activate this input when exact `private`, export exclusions, selective exposure,
alias export, import-module export, or re-export syntax is reviewed.

Move accepted syntax into the namespace/module, declaration, source, and
compiler-directive owners. Retire this file after every preserved question is
accepted, rejected, or moved to a narrower live destination.
