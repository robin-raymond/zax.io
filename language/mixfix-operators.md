# Zax mixfix operators

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers defining, using, reviewing, or tooling multi-component Zax operations |
| Applies To | Mixfix tree matching, declarations, selection, and evaluation; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | Mixfix tree skeletons, components, holes, receiver anchors, declarations, phrase and call/index components, structural matching, mandatory direct selection, decomposition, ambiguity, unavailable best, protected barriers, qualification, branch-specific selection, and mixfix costs/diagnostics/source stability |
| Does Not Own | Exact forms and precedence ([operator catalog](operator-catalog.md)); operator phrases outside mixfix participation ([operator phrases](operator-phrases.md)); shared selection ([operators](operators.md), [function invocation](function-invocation.md)); or source token/layout behavior ([source structure](source-structure.md)) |
| Source / Provenance | Operator-tree and direct indexed-operation requirements refined from legacy operator and array evidence |

## Mental model

A mixfix declaration selects one operation from a recognizable expression-tree
shape containing several operator components.

```zax
a[b] = c
a * b + c
```

When a direct mixfix matches, its component operators do not execute
independently. The selected mixfix owns the complete operation.

This allows a custom type to define direct indexed mutation, fused domain
operations, and other multi-component behavior without a hidden proxy or an
arbitrary new punctuation token.

## Trees, components, and holes

The ordinary [operator catalog](operator-catalog.md) forms the tree first for
symbolic source. Word-spelled source forms candidate trees under the shared
[operator model](operators.md#candidate-tree-formation-and-selection).

```zax
a[b] = c + d
```

```text
=
|- index
|  |- receiver: a
|  `- index hole: b
`- right hole: c + d
```

The mixfix skeleton fixes the index and assignment nodes. Its holes accept
complete expression subtrees. `c + d` resolves normally inside one hole.

The same skeleton matches:

```zax
a[b * scale] = produceValue()
```

because the index and RHS holes may contain different internal trees.

### Receiver anchor

A type-defined mixfix has one receiver anchor. Follow the receiver-bearing slot
through each component:

```text
a -> index -> =
```

For `a[b] = c`, `a` is the receiver; `b` and `c` are explicit inputs. Right
operand types affect viability but do not contribute type-defined discovery.

Global mixfix declarations are not permitted. A [partial](partials.md) on the
receiver type may add a mixfix declaration, visible only where the partial is
granted. Because a matching mixfix is selected instead of the component
operations a use would otherwise decompose into, granting such a partial can
change what an existing `a[b] = c` selects; a grant placed partway through a
block that does so requires acknowledgement.

## Declaration model

Exact syntax remains illustrative, but the declaration categories and mapping
are established:

```zax
MyType :: type {
  operator mixfix
    index 1
    binary '='
  final : (
    result : MyResult
  )(
    indexValue : MyIndex,
    rhs : MyValue
  ) writable = {
    // `_` is the receiver anchor.
  }
}
```

A declaration:

- identifies every recognized component and its exact form;
- relies on ordinary fixed precedence to form its skeleton;
- omits the type-defined receiver from its explicit parameters;
- lists the remaining holes in source-evaluation order;
- may declare zero, one, or several result slots; and
- receives no capability from punctuation alone.

Pre-unary, post-unary, binary, circumfix, call, index, and phrase components
retain their own forms. A component cannot reinterpret a pre-unary spelling as
binary or postfix.

## Phrase components

A phrase component is one exact contiguous word sequence declared with its own
fixity. For:

```zax
high shift left through low by count
```

an illustrative declaration is:

```zax
Multiword :: type {
  operator mixfix
    binary 'shift left through'
    binary 'by'
  final : (
    result : Multiword
  )(
    low : Multiword,
    count : BitCount
  ) = {
    // `_` is `high`, the receiver anchor.
  }
}
```

`operator mixfix` identifies the complete consumed skeleton. Each component line
identifies one exact component and its fixity. The prototype lists non-receiver
holes in source order; type-use holes, when present, are likewise declared by
parameters rather than interpolated into phrase text.

A phrase component uses only pre-unary, post-unary, or binary fixity. A
word-delimited operation that surrounds or separates several holes is therefore
built from several mixfix phrase components rather than from a phrase circumfix.

Word-spelled source may present several structurally complete readings. Candidate
trees are formed and pruned by the shared model in
[operators](operators.md#candidate-tree-formation-and-selection); this document
applies that model rather than redefining it. A uniquely selected inner result may
make a structurally matching outer mixfix viable, as
[nested outward resolution](#nested-outward-resolution) describes below.

An explicit
[phrase fence](operator-phrases.md#exact-phrase-fencing) marks one exact
component. It does not group, add a precedence boundary, choose between ordinary
operator and mixfix readings, or select an implementation:

```zax
high 'shift left through' low 'by' count
```

The fences establish exact components. Complete-tree pruning still determines
whether they form one mixfix or several operations.

A complete mixfix may span physical lines when its surrounding construct has a
valid continuation reason, but each individual phrase component remains on one
physical line with exactly one ASCII space between its words. Those presentation
rules are owned by
[operator phrases](operator-phrases.md#presentation-confirms-a-selection-it-never-makes-one);
general continuation and physical-line mechanics remain with
[source structure](source-structure.md#operator-phrase-source-integration).

## Call, index, and splice components

Direct `operator call` declarations derive argument count from their
prototypes. Each `operator index` declaration receives one index from one
bracket. Inside a flattened mixfix, `call N` identifies the call's inputs while
`index 1` identifies one index bracket.

A direct `operator splice 1` declaration receives one normalized optional
start/exclusive-end pair. Inside a flattened mixfix, `splice 1` identifies that
one sliced bracket:

```zax
operator mixfix
  splice 1
  binary '='
final : ()(
  start : IndexSize?,
  endExclusive : IndexSize?,
  replacement : MySequence
) writable = {
}
```

Exact endpoint source, normalization, empty ranges, and intrinsic result
behavior belong to [Zax arrays and slices](arrays-and-slices.md#slicing).

```zax
CustomCallable :: type {
  operator mixfix
    call 2
    binary '+'
  final : (
    result : Result
  )(
    number : Integer,
    text : String = "default",
    adjustment : Integer
  ) readonly = {
  }
}
```

This may match:

```zax
result := callable(5, "apple") + 45
result := callable(5, :) + 45
```

The call contributes `number` and `text`; `+` contributes `adjustment`. Omitted
`:` still occupies a call slot and requires the mixfix parameter's declared
default.

A consumed call, index, or splice component produces no intermediate result or
proxy. The complete mixfix declares the result. Ordinary component results
matter only when direct mixfix selection finds no applicable candidate and
decomposition occurs.

Named/default mapping, variadics, result forwarding, lambda-generated callable
types, construction-like call syntax, non-array delimited forms, and exact
default timing remain future invocation/indexing integration.

## Matching and selection

Conceptually:

1. Parse the ordinary tree using fixed source rules.
2. Find mixfix declarations whose root component matches.
3. Match every fixed component and operand hole structurally.
4. Discover declarations through the receiver anchor.
5. Resolve each hole as an ordinary expression.
6. Test receiver, inputs, qualifications, constraints, results, and availability.
7. Compare overloads sharing one skeleton through ordinary partial-order
   preference.
8. Diagnose distinct viable skeletons that overlap the same tree.
9. Select one complete mixfix or, only when none applies, consider ordinary
   decomposition.

If exactly one structure has one uniquely selected viable declaration, it must
be used. Component operations are not competing candidates.

```zax
a * b + c
```

A declared `*`, `+` mixfix may round, overflow, allocate, mutate, or return
differently from separate multiplication and addition. This is semantic
selection, not an optimizer claiming equivalence.

Optimization without a selected mixfix may fuse work only when every
programmer-visible operation and consequence remains unchanged.

## Nested outward resolution

A disjoint mixfix may resolve inside an operand hole. Its selected result then
supplies that hole's type, qualifications, result shape, and transfer behavior
when testing an enclosing mixfix:

```zax
outer + (a * b + c)
```

If `a * b + c` selects one inner mixfix, that result may make an already
structurally matching outer mixfix viable. Resolution can continue outward
through the finite ancestor path to the complete expression root.

This is not a fixed-point rewrite:

- the enclosing key must already match components in the original parsed source
  tree;
- a selected result cannot invent new operator components;
- the resolver does not scan the replacement result as new source syntax;
- an outer expected input cannot recursively manufacture an otherwise ambiguous
  inner result outside the shared narrow expected-result rules; and
- overlapping inner/outer skeletons do not cascade in discovery order.

Distinct skeletons that consume overlapping source nodes remain competing
structural interpretations and are ambiguous when both are viable. Only
disjoint mixfixes nested wholly inside operand holes resolve recursively.

## Ambiguity and unavailable operations

Declarations sharing one skeleton form an ordinary overload set. Distinct
viable skeletons matching overlapping portions of the same expression are a
structural ambiguity:

```zax
a[b * scale] = value
// error if both a general indexed assignment and a distinct skeleton consuming
// the multiplication are viable
```

A longer skeleton does not automatically win.

A viable uniquely best bodyless, `forbidden`, unsupported `default`, or otherwise
unavailable mixfix produces an unavailable-operation diagnostic and blocks
decomposition.

When no direct mixfix candidate is viable, ordinary decomposition may proceed.
A bodyless or `forbidden` declaration may deliberately claim a shape and prevent
that fallback.

## Holes and operator fallback

Fallback never rewrites the tree to create a mixfix match.

```zax
a + ?c
```

If the mixfix consumes only `+`, then `?c` lies inside a hole and resolves
normally, including the accepted `?`/`!` fallback. If the skeleton consumes the
written pre-unary `?`, that component does not resolve independently, and
`a + !(!c)` is a different tree.

## Protected barriers

A **protected intrinsic signature** controls declaration ownership: user code
cannot replace an exact protected built-in signature.

A **mixfix-consumption barrier** controls tree matching: a user mixfix may accept
the resolved node as a completed hole value but may not consume it as a key
component.

A custom-receiver mixfix may consume an eager protected node when doing so
removes no required conditional evaluation boundary.

The exact Boolean short-circuit operations are barriers:

- symbolic `&&` and `||`;
- phrase `logical nand` and `logical and not`; and
- phrase `logical nor` and `logical or not`.

Exact Boolean `^^` and `logical xnor` are eager and may be consumed. Mixed
Boolean/custom operand shapes are ordinary eager operations and may participate,
including custom implementations of the language-defined logical phrase forms.

A protected short-circuit result may fill a hole:

```zax
custom + (ready && perform())
```

The Boolean subtree then retains its protected behavior.

Declaration initialization, constructors, replacement constructors,
destructors, statement composition, and conditional/header separators are not
ordinary overloadable components and cannot be consumed by a user mixfix.

## Qualifications and lifecycle

Receiver and hole qualifications participate in viability. A writable mixfix is
nonviable through readonly access; mutation still requires mutable value state
and writable access.

Compiler-owned reconstructive replacement remains compiler-owned. Ordinary `=`
is only an in-lifetime component, while protected `.=` is not consumable by a
user mixfix and cannot grant the replacement lifecycle skeleton.

User mixfixes cannot be anchored on closed intrinsic receivers. A custom receiver
may accept intrinsic values in other holes.

## Indexed mutation without a proxy

Indexed read and direct indexed mutation are independent:

```zax
value := container[index]
container[index] = value
container[index] ^= mask
```

The existence of indexed read does not block a direct indexed mixfix. No hidden
proxy is synthesized.

If no mixfix applies, decomposition is possible only when indexed read genuinely
returns an assignable place or real proxy:

```zax
proxy := container[index]
proxy = value
```

Intrinsic array and slice indexing supplies an element place. Other custom
index results follow their declarations.

Splice assignment is likewise one direct operation:

```zax
container[low ..< high] = replacement
```

The mixfix receives the container, the normalized optional start/end
boundaries, and the replacement directly. It needs no intermediate range
object. For intrinsic arrays, the container and range expressions evaluate
first, the replacement evaluates next, and bounds are checked when the complete
operation runs. Complete mutation, element-count checking, overlap, and
invalidation are explained by
[Zax arrays and slices](arrays-and-slices.md#direct-splice-mutation).

## Branch-specific mixfix selection

Conditional expressions may select different mixfix behavior on different
paths:

```zax
result := (condition ?? a * b ;; fallback) + c
```

The true path may select a `*`, `+` mixfix while the false path selects ordinary
`+`.

Every possible path is validated statically. Runtime evaluation:

- evaluates the condition once;
- evaluates only the selected arm;
- preserves common operands once in source order;
- selects independently on each path; and
- requires convergence by the complete operation boundary.

An ambiguity or unavailable operation on any possible path is a compile-time
error.

## Multiword bit operations

Zax preserves separate operations for:

- multiword logical left/right shift;
- multiword arithmetic right shift;
- multiword explicit zero-fill right shift;
- multiword rotate left/right;
- funnel shift left/right; and
- writable forms affecting two component places.

Their exact words remain deferred, so the following source is illustrative
wording rather than current spelling:

```zax
// Illustrative wording; exact multiword phrases are not established.
high shift left through low by count
high rotate left through low by count
result funnel right from high and low by count
```

These operations have receiver, chained value, and unsigned count holes. Baseline
components have equal logical bit extents. Shifts zero/sign-fill at the outer
boundary, rotates wrap across the combined extent, and funnel shifts return one
fixed-width window. They use shift/rotate/composition precedence and have
language-provided basic-integer forms.

Exact phrase spelling, signedness constraints, mutation of one versus both
components, result shape, and alias behavior remain future numeric work.

## Circumfix and phrase components in ordinary trees

A recognized circumfix may participate as one complete component:

```zax
|value| + adjustment
```

Its opening and closing delimiters are not independent unary operators.

Operator phrases participate as ordinary components with their exact words,
fixity, and precedence from the
[operator catalog](operator-catalog.md#operator-forms). Adjacent
independent phrase applications require grouping.

## Costs and diagnostics

Mixfix tooling must expose:

- the matched skeleton and receiver anchor;
- every consumed component and unresolved hole;
- direct selection versus decomposition;
- same-skeleton overload ambiguity versus structural overlap;
- qualification, transfer, and constraint mismatches;
- unavailable best candidates;
- protected barriers;
- branch-specific selection;
- operand evaluation order;
- hidden captures, copies, temporaries, allocation, and destruction; and
- source compatibility when a new mixfix replaces prior decomposition.

Adding a mixfix can change existing source from several operations to one
operation. That is a visible API evolution, not an invisible optimization.

## Boundaries and maturity

This document defines current conceptual mixfix behavior, not formal grammar,
reflection metadata, implementation matching algorithms, or a conformance
contract.

Exact generic substitution, non-array call/index edge cases, lambda types, exact
multiword words, and diagnostic identifiers remain focused future work.
