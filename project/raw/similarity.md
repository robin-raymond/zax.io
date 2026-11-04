# Raw input: future language similarity guide

| Field | Value |
| --- | --- |
| Status | Raw placeholder / non-authoritative |
| Audience | A future numbered work item evaluating comparative language guidance |
| Applies To | Requirements and cautions for a future Zax similarity guide |
| Owns | Preservation of the future guide's known purpose, inputs, and boundaries |
| Does Not Own | Zax semantics, historical provenance, foreign interoperability, or final comparison content |
| Source / Provenance | Work item `001`, Zax purpose and design principles; work item `036` generics comparison input |

## Purpose

Preserve the known need for a future guide that helps developers understand Zax
through concepts they already know from other programming languages.

Do not create the actual guide until enough Zax concepts are accepted to compare
their semantics accurately.

## Reader routes

The future guide should support both directions:

### From another language to Zax

A developer familiar with another language can find:

- the closest Zax concept, when one exists;
- important semantic and cost differences;
- concepts that are deliberately absent;
- common migration assumptions that would be incorrect; and
- whether direct interoperability is available or an adapter is required.

### From Zax to other languages

A developer reading about a Zax concept can find:

- superficially similar syntax elsewhere;
- genuinely comparable semantics;
- important guarantee, lifetime, safety, or performance differences;
- languages with no direct equivalent; and
- foreign boundaries relevant to that concept.

## Required distinctions

Each useful mapping should distinguish:

- syntax similarity;
- conceptual similarity;
- semantic equivalence or difference;
- ownership and lifetime behavior;
- safety guarantees and unchecked boundaries;
- runtime and compile-time cost;
- layout and ABI compatibility;
- missing concepts;
- migration traps; and
- actual interoperability.

Similarity must never be presented as exact equivalence without evidence.

## Boundaries

The future guide:

- does not define Zax semantics;
- does not replace a language concept owner;
- does not claim historical inspiration without evidence;
- does not require Zax to adopt another language's terminology;
- does not become the C/C++ FFI or ABI guide;
- does not compare unresolved Zax mechanisms as though they are accepted; and
- does not treat another language as an opponent.

Language concept owners remain authoritative. The similarity guide summarizes
relationships and links to them.

## Candidate language set

The following are starting inputs, not a required or exhaustive comparison list:

- C;
- C++;
- Pascal;
- Java;
- JavaScript;
- Jai;
- Objective-C;
- C#;
- F#;
- Haskell;
- Rust;
- Go; and
- other languages when a concrete reader need justifies them.

The public vision may identify influential languages broadly without assigning
specific Zax features to them.

## Generics comparison input

[Zax generics](../../language/generics.md) is the first current owner with a
substantial comparison need. It carries a short orientation table and inline
notes only where a C++ or Rust habit would lead to wrong Zax source. The
broader comparison belongs in the future guide:

- **C++ templates.** Zax also checks bodies per instantiation and allows
  specialization by any module, but has no SFINAE: a body failure is an error,
  and only constraints remove candidates. C++ explicit and partial
  specialization map to declarations with `requires` clauses ranked by
  constraint terms. Two-phase lookup and argument-dependent lookup map to
  "names not involving a parameter bind at the definition; calls involving one
  also search the argument types' modules and the use's visible modules",
  with disagreement diagnosed rather than left as an undiagnosed
  one-definition-rule violation.
- **C++20 concepts.** Closest to Zax constraints. Zax's ranking by identical
  terms resembles subsumption over atomic constraints. Zax constraints are
  ordinary `Boolean` functions rather than a separate concept declaration.
- **C++ non-type template parameters.** Zax `$` value parameters compare by
  stored parts, close to C++20's rules for class-type template arguments.
- **Rust traits and generics.** Rust checks generic bodies once against trait
  bounds; Zax checks per instantiation and constraints do not limit what the
  body may use. Rust's orphan rule forbids extension by a crate owning neither
  side; Zax allows it and requires program-wide agreement. Both monomorphize.
- **Rust const generics.** Stable Rust allows only integers, `bool`, and
  `char`; Zax allows any crossable value except pointers, references,
  capturing lambdas, and unions.
- **Zig `comptime`.** Closest model for types as compile-time values and
  building types from values computed during compile-time execution.
- **Syntax habits.** `<T>` becomes `$(...)`; generic types always need their
  list (`MyType$()`); a type passed as an argument (`as Fahrenheit`) is never
  worked out.

Migration traps to cover: expecting constructor inference of a generic type
(C++17), expecting a body failure to fall back to another overload (SFINAE),
expecting a by-value-looking parameter to copy a reference argument, and
expecting a library helper to be found when it is visible at one use but not
another.

## Toolchain terminology: host, build, and target

Zax calls the machine running the compiler the **compiler host** (`host size
of`, `CompilerHost.`, `Compilation.compilerHost`) and the machine the program
runs on the **target**. Current owners carry a one-line note for readers used
to the GNU convention; the fuller comparison belongs in the future guide:

- **Same usage as Zax:** Rust (`rustc -vV` reports the compiler's `host`; Cargo
  builds build scripts and procedural macros "for the host"), Zig (the build
  system's host versus target, the closest match because Zig also runs code at
  compile time), Go (`GOHOSTOS`/`GOHOSTARCH` versus `GOOS`/`GOARCH`), LLVM
  (host triple versus target), and CMake (`CMAKE_HOST_SYSTEM_NAME` versus
  `CMAKE_SYSTEM_NAME`).
- **GNU usage:** autoconf (`--build`, `--host`, `--target`), Meson
  (`build_machine`, `host_machine`, `target_machine`), and Nix
  (`buildPlatform`, `hostPlatform`). Here *build* is the compiling machine and
  *host* is where the built program runs. The three-machine scheme comes from
  building compilers, where the compiler's own build machine, the machine it
  runs on, and the machine it generates code for can all differ.
- **A related rename:** Bazel renamed its "host" configuration, for tools run
  during the build, to the "exec" configuration, partly because of this
  ambiguity.

Zax considered renaming `host` to `build` and kept `host`: it matches the
toolchains closest to Zax's compile-time model, it is already used consistently
across the language, and `Compilation.build` already names build
configuration, so `build size of` would invite a different confusion. These
toolchain facts were recalled rather than re-verified when recorded; the
future guide should confirm them against current documentation.

## Activation pressure

Create a future subject-specific numbered work item only when:

- enough relevant Zax concepts have accepted owners;
- comparisons can link to current Zax definitions;
- a real onboarding or migration need identifies priority languages; and
- the work can remain bounded rather than attempting an encyclopedia.

## Retirement

When future similarity work begins:

1. Treat this file as raw input, not authority.
2. Disposition every retained requirement.
3. Move accepted guidance to the lasting comparison owner proposed by that
   work's documentation fit dry run.
4. Move unresolved inputs to an explicitly justified successor or defer them
   with deciding pressure.
5. Retire or archive this placeholder so it cannot become a competing owner.
