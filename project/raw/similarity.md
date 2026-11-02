# Raw input: future language similarity guide

| Field | Value |
| --- | --- |
| Status | Raw placeholder / non-authoritative |
| Audience | A future numbered work item evaluating comparative language guidance |
| Applies To | Requirements and cautions for a future Zax similarity guide |
| Owns | Preservation of the future guide's known purpose, inputs, and boundaries |
| Does Not Own | Zax semantics, historical provenance, foreign interoperability, or final comparison content |
| Source / Provenance | Work item `001`, Zax purpose and design principles |

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
