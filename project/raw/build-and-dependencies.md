# Raw input: build execution and source dependencies

| Field | Value |
| --- | --- |
| Status | Raw placeholder / non-authoritative |
| Audience | A future numbered work item defining build-time execution or dependency behavior |
| Applies To | Trust, sandboxing, source resolution, immutable identity, provenance, durable builds, and required language support supplied by the toolchain |
| Owns | Preservation of aligned pressures, threats, required-support and linkage questions, and unresolved mechanisms |
| Does Not Own | Accepted build/dependency semantics or implementation contracts |
| Source / Provenance | Work item `001`, Zax purpose and design principles; operator and foundational-support pressure; work item `036` instantiation-depth and writable-setting pressure |

## Aligned pressure

Zax integrates build-time execution and source dependency resolution into the
language build. It does not require an external package-management step or an
official package store, though optional catalogs or package schemas may exist.

Build-time code is executable code. It is trusted unless the build environment
deliberately constrains it.

## Required language support and toolchain completeness

Zax does not require one mandatory conventional system library, but an
implementation cannot omit the support the language itself requires. Preserve the
distinction between:

- optional library APIs such as collections, I/O, networking, and frameworks; and
- required language support for fundamental types, protected primitive
  operations, and the runtime or lifetime behavior that source uses.

Protected intrinsic operator families are current concrete examples. Exact
`Boolean && Boolean` and `Boolean || Boolean` are protected short-circuit
operations; protected integer arithmetic, policy, bitwise, shift, reduction,
count, magnitude, mutation, and swap forms likewise belong to required language
support where the current
[operator catalog](../../language/operator-catalog.md) makes them available.
Their shared programmer model is owned by
[Zax operators](../../language/operators.md), and the foundational promise by
the [Zax language vision](../../language/vision.md).

A conforming implementation may satisfy a required operation through instructions,
constant folding, emitted target code, an intrinsic, or an automatically linked
helper. Those are implementation choices, and no lowering is promised.

```zax
// error: the protected Boolean || Boolean operation cannot be replaced
operator binary '||' final :
  (result : Boolean)(lhs : Boolean, rhs : Boolean) = {
  return asmCompare(lhs, rhs)
}
```

Future build and toolchain work must decide:

- how automatically linked helper objects or runtime fragments are located,
  versioned, and pinned;
- how required-support availability interacts with cross-compilation, freestanding
  targets, and host/target separation;
- how a toolchain reports missing required support, given that this is a broken
  or incomplete toolchain rather than the diagnostic the source was meant to
  produce; and
- how required support interacts with durable, reproducible, and locked builds.

Exact compiler and runtime architecture remains undecided here.

## Sandboxing approaches

Two complementary layers need future design:

1. Restricted build-time capabilities, libraries, compiler services, and host
   access.
2. Externally isolated builds using disposable machines, VMs, containers, or
   hosted workers without trusted credentials or privileged resources.

The complete execution surface includes filesystem, network, processes,
environment variables, dynamic loading, FFI, compiler intrinsics, and transitive
build code.

## Immutable dependency identity

Branches, tags, and version names are human-friendly selectors rather than
immutable identities.

A future dependency model should consider recording:

- repository identity;
- requested selector;
- resolved commit identity;
- canonical source-tree or content digest;
- recursive dependency identities;
- signatures or provenance; and
- build-time capabilities required.

Existing locked builds should not silently accept a moved tag. An explicit
update can report and accept newly resolved content. Development may permit
floating branches while durable or release builds require locked identities.

## Threat input

Preserve consideration of:

- moved, deleted, or recreated tags;
- compromised repository accounts;
- deletion and namespace takeover;
- dependency confusion;
- transitive mutation;
- submodules, LFS, generated content, and external downloads;
- malicious build-time code;
- cache poisoning;
- unsigned or unverifiable history;
- redirects and repository identity changes;
- hash migration or collision concerns;
- host-dependent behavior;
- deterministic and hermetic build policies;
- cross-compilation and host/target separation; and
- caching and incremental invalidation.

## Generative module and injection pressure

Current [namespace/module design](../../language/namespaces-and-modules.md)
establishes that every import creates one generative module instance:

```zax
First :: import Module.Dependency
Second :: import Module.Dependency
```

`First` and `Second` have distinct declaration identities and individualized
module state even when source and injection inputs are equal. Build caching may
reuse parsing, analysis, generated code, or another artifact only when that
optimization preserves those language-level distinctions.

An import's semantic input includes:

- immutable resolved source identity;
- complete importer-supplied injection;
- language/profile/environment selection;
- conditional source/import decisions made from that injection; and
- transitive dependency identities.

Injected declarations behave as target-module source while retaining lexical
references to the injection site. Cache keys must therefore capture canonical
declaration identities and semantic inputs rather than only source text.

Exact namespace injection can deliberately share one already imported dependency
and its state. That sharing must not arise merely because two import requests
hash alike.

### Import repeat and cycle identity

Current module design forbids unbounded recursive import expansion but does not
yet define which dependency facts constitute a repeated node on one active
ancestry.

Generative module-instance identity is insufficient because each import creates
a new instance. Future dependency work must decide whether repetition uses:

- immutable resolved source/content identity;
- repository identity independent of revision;
- selected version, commit, or content digest;
- complete injection environment;
- conditional source/import result;
- or another canonical dependency key.

Consequently, `A-v1 -> A-v2` is not yet classified as a cycle merely from the
shared human name or repository, while `A-v1 -> A-v2 -> A-v1` demonstrates an
obvious repeated resolved source identity. The final rule must permit finite
multiple-version dependency graphs without allowing dynamic selectors or
changing injections to manufacture unbounded unique instances.

Diagnostics should show the active import ancestry and exact repeat facts rather
than reporting only a human module name.

## Source-list and visible-cache pressure

Every module has an ordered `module.zax` source list. Legacy input preserves:

- explicit relative source paths;
- wildcard source selection;
- generated-source markers;
- once-only inclusion of one selected source; and
- a visible local immutable cache that developers can inspect and refresh.

Wildcard expansion needs a canonical host-independent path order because global
initialization and root availability may observe source order. Future work must
define path normalization, case behavior, invalid duplicate paths, generated
inputs, and whether lifecycle-sensitive modules must use explicit lists.

Branch and tag selectors remain update intent, not immutable identity. A durable
import needs a resolved revision and/or canonical source-tree digest. A digest
must define treatment of file modes, submodules, large-file content, generated
inputs, line endings, paths, and algorithm migration.

Deleting or repairing a cache may reacquire the same locked source. It must not
silently update a moved selector or change a module instance's identity.

## Directive-related build pressure

The root legacy compiler-directive page keeps the `source` and `asset` directive
sections (required, warning, and generated variants, wildcard ordering, asset
renaming) as deferred input for this work. Current design already fixes some
constraints they must meet:

- **Inclusion carries directive state.** A file brought in by source inclusion
  starts with the directive state in effect at the inclusion line, and its own
  ranges end at its end. See
  [directive ranges](../../language/compiler-directives.md#what-bounds-a-range).
- **Reproducibility.** The legacy `generated=yes` check compares file
  timestamps, which conflicts with reproducible builds. Writing files from
  compile-time code is a host-world effect of
  [compile-time code](../../language/compile-time-code.md#two-programs-from-one-source).
- **Panic starting state.** Build configuration sets each module's starting
  panic-check state, which source directives refine. See
  [controlling panic checks](../../language/safety-and-analysis.md#controlling-panic-checks).
  This work must define that build option and how it is recorded.
- **Pinning non-reproducible facts.** `Compilation.build.time`,
  `Compilation.build.variable`, `Compilation.module.path`,
  `Compilation.module.git`, and `Compilation.file.modified` vary between builds.
  Build configuration must be able to pin them, and `Compilation.*.setting`
  values come from build configuration. See
  [compilation information](../../language/compile-time-code.md#compilation-information).
- **Import versions.** `deprecate` `min` and `max` compare against the version an
  importer requests. This work defines how an import requests a version, and
  should review those two options when it does.
- **Instantiation depth.** [Generics](../../language/generics.md#limits) sets a
  compiler-wide default instantiation depth of `4096`, raised per declaration
  with `[<instantiation-depth=...>]`. Build configuration should be able to
  set the project-wide default; this work defines that option.

## Writable compilation settings

The maintainer raised the idea that some `Compilation` settings could be
writable from source, for cases such as raising the instantiation depth for
one construct, with the compiler restoring the setting when the construct
ends. For instantiation depth, a directive plus build configuration was
chosen instead. The idea remains open for other settings.

```zax
// Illustrative only; not current source.
compile{
  Compilation.limits.instantiationDepth = 8192
}
```

Concerns to resolve before adopting it:

- every `Compilation` answer is currently a compile-time value; a writable
  setting would let an answer change between reads;
- results would depend on the order compile points run in; and
- one module could change a setting another module relies on.

## Activation and retirement

Activate this input before defining the lasting build/dependency owner,
dependency declaration and locking, sandbox policy, required-support and helper
linkage behavior, or an implementation contract. Consume its findings through
that work and retire or archive this placeholder afterward.
