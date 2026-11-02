# Zax language principles

| Field | Value |
| --- | --- |
| Status | Current conceptual principles |
| Audience | Human developers using, evaluating, teaching, or designing Zax |
| Applies To | Cross-cutting principles that guide Zax source, APIs, and future language decisions |
| Implementation State | Not applicable |
| Owns | The human-facing catalog and full definitions of accepted reusable Zax design and use principles |
| Does Not Own | Exact feature semantics or project cooperation/documentation rules |
| Source / Provenance | Foundational direction from the language vision and reusable principles recovered from legacy design material |

## Purpose

These principles help programmers understand what Zax values and why unfamiliar
features behave as they do. They also help API authors and language maintainers
evaluate whether a design fits the rest of the language.

A principle is broader than one syntax rule:

- it guides more than one feature or design decision;
- it remains useful when a local spelling changes;
- it helps a programmer predict behavior; and
- it can be stated clearly before its individual applications are explained.

Exact behavior remains with concept owners such as
[function invocation](function-invocation.md),
[source structure](source-structure.md),
[declarations and bindings](declarations-and-bindings.md),
[qualifiers](qualifiers.md), and
[construction, replacement, and destruction](construction-and-destruction.md).

The catalog grows deliberately. A memorable phrase is not accepted merely
because it sounds useful; new principles require evidence, discussion,
alignment, and authorized promotion.

## Expressiveness serves clear intent

> Zax seeks expressive and flexible ways for programmers to state their intent
> clearly. When the intended operation remains unclear, Zax rejects the source
> rather than guessing.

Flexibility is a tool for clearer expression, not permission for the language to
choose among plausible meanings silently.

Examples include:

- labels and explicit positional forms;
- complete result declarations selecting a result family;
- explicit `copy`, `deep`, `move`, reference, or `last` intent;
- selectable memory and lifetime policies; and
- structural compatibility postures and transformations; and
- future generic and metaprogramming relationships.

[Function invocation](function-invocation.md) applies this principle by
diagnosing incomparable call candidates and accepting explicit labels,
declarations, transfers, and prototype bindings as disambiguation.
[Transfer stances](transfer-stances.md) let a programmer distinguish ordinary
copying, independent copying, resource transfer, and final resource reuse
without requiring separate source syntax for every constructor, call,
assignment, and operator.

Defined source that strongly resembles a mistake may require an
[intent acknowledgement](intent-acknowledgements.md). That mechanism confirms a
specific suspicious interpretation; it does not make invalid source valid.

The tension is that expressive flexibility increases the number of forms and
interactions a programmer must learn. Good defaults and precise diagnostics keep
ordinary code approachable.

## Similar meaning should look similar

> Mechanisms that perform the same conceptual operation should share readable
> forms. A meaningful semantic difference should remain visible in source.

Zax should not make programmers memorize unrelated punctuation for behavior that
is genuinely shared. It also should not reuse convenient syntax when doing so
hides an important difference.

Examples include:

- ordinary calls and construction sharing input forms;
- `.member = expression` remaining visibly construction-specific;
- omission differing visibly from an explicit type-default expression; and
- one expression differing visibly from multiple-result mapping.

Familiar syntax is useful, but consistency inside Zax matters more than imitating
another language mechanically.

## Visible order should be trustworthy

> Observable work should follow an order programmers can see and learn. Hidden
> work must have one documented position rather than appearing unexpectedly
> between visible operations.

Examples include:

- explicit call inputs evaluating in source order;
- omitted parameter defaults evaluating afterward in selected-prototype order;
- construction input evaluation remaining distinct from member construction
  order; and
- return expressions establishing result slots from left to right.

An implementation may optimize storage and work, but it may not expose a
different order or effect.

Some mechanisms necessarily add work not written as an ordinary expression.
Their documentation must identify where that work occurs.

## Preference must be defensible, not guessed

> Zax selects one interpretation only when it is demonstrably better under the
> applicable rules. Equal or incomparable interpretations require explicit
> disambiguation rather than scoring tricks or source-order choice.

[Function invocation](function-invocation.md#partial-order-preference) applies
this principle through partial-order dominance. Future generic, conversion,
operator, and structural matching should preserve the same discipline where it
fits their domains.

This principle does not require every possible operation to be comparable.
Declaring two choices incomparable is often more honest than inventing a
language-wide preference between different costs or ownership effects.

## Context may clarify intent, but it must not invent it

> Explicit surrounding information may select among meanings when it states what
> the programmer wants. Zax does not recursively search unrelated conversions or
> later uses to manufacture intent.

For example:

```zax
value : Integer = parseValue(source)
```

The complete declaration clearly requests an `Integer` result family. An
inferred declaration does not search later statements, unresolved operators, or
every destination constructor to guess a result.

Useful context should reduce repetition. Speculative context makes code fragile
and turns small API changes into distant semantic changes.

## Costs follow chosen behavior and remain discoverable

> Programs should pay for the behavior they use, and consequential work should
> remain discoverable where it is introduced.

Meaningful costs include:

- allocation and deallocation;
- copying and movement;
- indirection and reference counting;
- synchronization and scheduling;
- runtime type information;
- checks and unsafe boundaries;
- generated code and executable size; and
- build-time computation.

Examples of applying the principle include:

- result slots not default-constructing unless requested;
- relabeling a compatible static function not inherently adding runtime
  dispatch;
- captured defaults retaining state when they need it; and
- async or synchronization machinery appearing only when used.

A programmer may deliberately select a more expensive mechanism for safety,
convenience, or flexibility. The principle requires visibility and choice, not
minimum cost at all times.

## Optimization preserves the programmer-visible operation

> Optimization may remove storage or work only when the programmer-visible
> operation and its observable consequences remain unchanged.

Examples include:

- liveness analysis implementing a selected `move` or `last` transfer without
  choosing another stance or user body;
- temporary storage being elided without changing lifetime effects;
- result construction being fused without changing ordering or destruction; and
- direct calls replacing an unnecessary function-value slot without changing the
  visible contract.

Future formal semantics may define exact equivalence more precisely. The current
principle establishes the conceptual boundary: optimization implements the
program, not a nearby program that appears cheaper.

## Contracts and implementations separate only without hidden adaptation

> A visible contract may improve names, defaults, and caller obligations while
> reusing a compatible implementation. If executable transformation is needed,
> the adaptation must be explicit.

[Compatible visible prototypes](function-invocation.md#compatible-visible-prototypes)
apply this principle:

- compatible ordered slots may be relabeled;
- call-boundary defaults may change;
- caller result-acknowledgement policy may change; but
- reordering, conversion, synthesis, or changed transfer behavior requires a
  wrapper or lambda.

This gives APIs room to improve their public vocabulary without pretending two
different executable contracts are identical.

## Values do not silently become a different shape

> Several values remain several values until source explicitly combines or
> transforms them into one value.

Examples include:

- multiple function results not becoming an implicit tuple;
- forwarding results to constructor parameters not populating stored members by
  result label; and
- structural recomposition using `-<` when several results should become one
  value; and
- structural transformation using `-<>-` when one value must change shape.

Convenient transformations may exist, but shape changes affect types, names,
layout, construction, and lifetime. Source should reveal that boundary.
See
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md#opening-and-packing-structural-values).

## Panic is not alternate control flow

> Panic blocks the operation that encountered its condition. A matching helper
> may repair the condition and let that same operation resume; otherwise the
> process crashes gracefully.

Panic never skips the failed operation, returns a substitute, unwinds scopes or
completed members, rolls work back, or returns partial lifecycle state to
ordinary execution.

This keeps APIs from treating panic as a hidden exception channel and prevents
cleanup, replacement, result, and structural-transformation rules from relying
on unwinding that Zax does not perform. Operations that need recoverable absence
or failure expose an ordinary result such as an optional or report instead.

Exact proof, unsafe-control, category, and helper behavior belongs to
[Zax safety and analysis](safety-and-analysis.md#panic-boundary).

## High-level expression does not hide fundamentals

> Zax provides high-level expression while keeping consequential lower-level
> behavior, costs, and tradeoffs understandable.

Low-level control is part of the language's normal range rather than merely an
emergency escape hatch. Higher-level code remains available without requiring
every programmer to work at the lowest level everywhere.

Abstractions should reduce repeated work and improve readability without making
allocation, indirection, mutation, synchronization, lifetime, or representation
impossible to discover.

## Policies are selectable rather than compulsory

> Different domains may choose different balances among performance, memory,
> safety, portability, and convenience.

Zax may provide strong defaults and opinions while permitting explicit choices
for concerns such as:

- lifetime and ownership;
- allocation;
- mutability and access;
- copying and movement;
- concurrency;
- runtime information; and
- low-level access.

Choice is not free complexity. Each selectable policy needs clear behavior,
costs, conversion boundaries, and diagnostics.

## Familiarity serves readability

> Zax prefers familiar forms when they express the design cleanly and departs
> from convention when a concrete improvement earns the difference.

Elegance is not terseness alone. Ordinary code should remain readable without
expert-level knowledge, and advanced code should expose its complexity rather
than compressing it into cryptic forms.

Clean-slate design is an opportunity to remove inherited accidents, not a reason
to make recognizable ideas unfamiliar without benefit.

## Feasibility informs design without defining source semantics

> Parser, tooling, analysis, and implementation feasibility are legitimate
> language-design pressures. Lasting language documentation still states the
> programmer-visible behavior rather than a compiler mapping.

This principle helps programmers evaluating why Zax has a particular shape and
contributors designing future concepts.

Its primary day-to-day value is for evaluators, API authors, and language
contributors. Programmers using Zax encounter its result through source rules
that are deterministic, diagnosable, and practical to tool.

Feasibility cannot justify behavior that is incoherent or surprising merely
because it is easy to implement. Conversely, a design that cannot be parsed,
analyzed, diagnosed, or implemented reliably is not improved by ignoring that
pressure during conceptual work.

Exact compiler architecture, lowering, backend mappings, and implementation data
structures remain with the implementation that owns them.

## Applying and extending the catalog

Concept owners should link a principle only when it helps a reader understand a
concrete dependency. They remain authoritative for behavior.

Future design work should ask:

- Which existing principles support or constrain this proposal?
- Does the proposal reveal a reusable new principle?
- Is the insight truly cross-cutting, or is it a local semantic rule?
- What tension exists with other principles?
- Where will programmers find the principle after promotion?

New principles may refine the catalog. They do not silently rewrite accepted
feature behavior; affected concept owners require their own aligned changes.
