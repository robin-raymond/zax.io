# Raw input: callable selection

| Field | Value |
| --- | --- |
| Status | Raw future-work input / non-authoritative |
| Audience | Future work refining overload, generic-specialization, and callable-prototype selection |
| Applies To | Callable candidate comparison beyond the currently accepted fixed-arity partial-order baseline |
| Owns | Naturally unselectable declaration pressure, cross-axis comparison, exact-prototype resolution, generic-specialization comparison, generated-versus-declared ambiguity, source compatibility, activation pressure, and retirement criteria |
| Does Not Own | Accepted current invocation behavior; `switch`/`case` or pattern-like selection; generic syntax; or runtime dispatch implementation |
| Source / Provenance | Transfer-stance review of value/reference, qualification, stance, result, generated-family, and exact-prototype selection; work item `015` minted-implementation refinement; work item `020` composition constraints |

## Why this input exists

[Zax function invocation](../../language/function-invocation.md) establishes a
partial-order model:

- candidates compare slot by slot;
- one candidate dominates only when it is no worse everywhere and better
  somewhere;
- incomparable candidates produce ambiguity;
- and declaration, import, source, or discovery order never breaks a tie.

Transfer-stance review adds concrete cases that need a later complete preference
design without weakening that model.

## Legal declarations may be naturally unselectable

Distinct declarations remain legal even when every currently obvious natural
call is ambiguous:

```zax
process final : ()(
  input : Message move
) = {
  // Copy in a private value, then process it under move stance.
}

process final : ()(
  input : Message mutable readonly & move
) = {
  // Alias caller storage through readonly access.
}
```

Some source may not distinguish these contracts. The compiler need not prove at
declaration time that each declaration will eventually be naturally selectable.
A use reports ambiguity when several viable candidates remain nondominated.

Exact canonical duplicate signatures remain redeclaration errors.

A private declaration outside its permitted caller context is ineligible before
preference. It does not become an unavailable best candidate or block a public
declaration. **Unavailable** applies only to an eligible visible shape that
cannot be invoked, such as a bodyless, forbidden, or unsupported generated
declaration.

## Exact-prototype resolution

A typed function alias or resolved function value can select one complete
prototype:

```zax
myProcess : ExactProcessPrototype = process
myProcess(message)
```

This is intentional contract selection. It is not merely a workaround for
incomplete overload rules.

Current
[function invocation](../../language/function-invocation.md#minted-implementation-model)
establishes that a concrete callable body is minted once under its implementation
prototype. A compatible visible prototype changes permitted call-boundary setup
or outward presentation without reprocessing the body.

Future exact-prototype work must preserve:

- body-entry facts required by the minted implementation;
- fixed operations already selected inside the body;
- results the minted implementation can actually produce;
- separate visible labels, defaults, acknowledgement, stance, and scheduled/open
  presentation; and
- the distinction from generic specialization, which may reprocess a generic
  body before minting each concrete implementation.

Future work must define:

- exact prototype spelling;
- when generic arguments or qualifiers are inferred;
- visibility and availability;
- whether aliases preserve labels and defaults;
- how an opaque reference result preserves its origin relationship to a
  receiver, named input, one of several inputs, or another stable life path;
- interaction with generated declarations;
- and diagnostics when no declaration has that exact shape.

Concrete bodies use the static origin model defined by
[Zax lifetimes and references](../../language/lifetimes-and-references.md#returned-references).
This input retains only the contract pressure that appears when the body is
opaque or separately represented; it does not establish programmer-written
lifetime parameters.

### Receiver binding and declaration provenance

Current declarations distinguish bound and unbound implementations. A bound
implementation has an installed receiver available through `_`; an unbound
implementation does not.

```zax
myValue : MyType
BoundPrototype :: alias type type of myValue.process
```

`type of` inspects callable type information without evaluating `myValue`.
The query does not create a runtime callable waiting for later receiver
application. The expression `myValue.process` forms the actual bound callable.

Receiver-capable varying storage may install unbound, borrowed-bound, or
applicable unique/strong/weak targets. Future preference work must compare the
visible explicit prototype without treating an erased receiver type as another
caller-supplied slot. Complete current behavior is owned by
[Zax lambdas and callable composition](../../language/lambdas-and-callable-composition.md).

Exact prototype and selected declaration provenance remain distinct. This
sequence selects one declaration:

```zax
selectedProcess final : ExactProcessPrototype = process
```

`type of selectedProcess` preserves its callable type, not a hidden claim that
every later use of the original name `process` selects the same declaration:

```zax
Prototype :: alias type type of selectedProcess
wrapper final : Prototype = process
// May repeat selection and remain ambiguous if several declarations are
// compatible with Prototype.
```

Targeting `selectedProcess` instead preserves the explicit selection.

Current exact `alias variable` has another role:

```zax
processAgain :: alias variable process
```

It aliases the same variable or approved polymorphic family and creates no
visible-prototype wrapper, capture, or slot. Future selection work must preserve
that exact family identity while distinguishing it from:

- a prototype type alias;
- one compatible visible declaration;
- a reference to a varying function slot;
- a bound callable value and its installed binding kind; and
- whole-family composition adoption.

Composition adds selection constraints without changing the partial-order
baseline. `expose` and `preferred` are independent of `own`. Exposure produces a
finite set of exact outer prototypes; it does not create a qualification-erased
operation or an open-ended generic surface.

Singular `= via` and `= existing` select one source operation and one complete
mechanical mapping. Ambiguity after permitted adaptation remains an error. An
exact private helper may give a public singular route an unambiguous target, but
does not add a preference rule or make the helper visible.

Outer-result provenance adds an explicit three-way mapping distinction.
Ordinary `via` may produce a non-optional outer result only when the selected
language contract proves its exact origin. `tracked via` names a checked
optional mapping through the applicable `outer tracked` capability. `unsafe via`
asserts an unproved non-optional mapping. `= existing` must not silently discover
tracked outer-result work; that runtime-capable route remains explicitly named.
If selected-contract proof makes tracking redundant, retaining `tracked via`
requires `intent<redundant-outer-tracking>`; stronger private analysis cannot
create that requirement.

Whole-family adoption is a separate operation:

- `= existing family` must discover one unique source callable family;
- `= via family` names one source family explicitly and may rename it on the
  outer surface;
- the written prototype is an exact selection anchor, not a wildcard;
- every eligible family member must independently obtain one unique mechanical
  outer mapping; and
- an adaptation that works only for the anchor does not become a family-wide
  conversion rule.

Adding a source-family overload may therefore invalidate the adoption. The new
member must not be silently omitted or selected by source order.

An exact `= forbidden` fence identifies one signature. `= forbidden family`
uses its exact anchor to identify an outer callable name or structured operator
form, then fences that complete outer family regardless of source provenance. A
later direct declaration in the fenced outer family conflicts; it does not
bypass the fence.

Preferred projection joins input mapping only after a candidate has provided an
expected type. Future generic work must decide whether projection can
participate after deduction without allowing it to invent a deduction target,
widen callable or operator discovery, or outrank an exact direct match.

Compatibility posture follows the same discovery boundary under
[current structural design](../../language/structural-shapes-and-compatibility.md#compatibility-posture):

- it may make a source viable only after one concrete expected type exists;
- exact identity remains better than posture-based adaptation;
- posture never widens discovery or invents a generic target;
- an anchor selects one source place but does not rank destinations; and
- several remaining non-identity adaptations are ambiguous rather than scored
  by shape kind, offset, or apparent cost.

Future cross-axis work must integrate that accepted baseline with value/reference,
qualification, transfer, result, generated, and generic-specialization
comparison.

## Callable precondition and postcondition pressure

Current result initializers can guarantee pointer presence by allocating before
body entry:

```zax
makeFoo final : (
  result : Foo * last = @
)() = {
}
```

Current syntax cannot express an unconstructed owning, raw, or optional result
that is established later while still promising presence on every normal exit.
Likewise, a pointer parameter cannot promise non-vacant entry while retaining
pointer ownership/rebinding semantics; a reference supplies non-vacant
borrowed access but is a different contract.

Future callable contracts should investigate general preconditions and
postconditions:

```zax
// Illustrative only; no syntax is accepted.
makeFoo final : (
  result : Foo * unique
)() ensures ?result = {
}

useFoo final : ()(
  foo : Foo *
) requires ?foo = {
}
```

The mechanism should generalize beyond pointers to optional presence, ranges,
collection size, ownership state, and other value predicates. Future work must
define:

- what facts the minted body may assume on entry;
- which normal exits must prove each result condition;
- how visible compatible prototypes weaken or strengthen contracts;
- what callers may assume after selection;
- how opaque proof uses narrow unsafe responsibility;
- and reflection and diagnostics.

Callable contracts must also preserve Nothing-instance route facts rather than
infer them from runtime state:

- a contract may promise that a pointer input is present or that a pointer
  result is non-vacant on every normal exit;
- a closed alias or future generated callable that represents a receiverless
  type call must preserve that route and its synthesized `copy`-stanced Nothing
  receiver;
- an alias representing an instance-qualified call must preserve its evaluated
  receiver route;
- callable selection must not merge those routes merely because their visible
  parameter and result slots match; and
- an exact `last` receiver remains unavailable to a receiverless type call even
  when another candidate accepts unstanced `copy`.

Exact contract syntax, proof, alias metadata, and selection comparison remain
future work.

## Cross-axis comparison

A callable slot may differ in several independent dimensions:

- base type and conversion;
- mutable/immutable value shape;
- final/varying place shape;
- writable/readonly access;
- value versus reference or pointer binding;
- offered and accepted transfer stance;
- source effect;
- and result compatibility.

Transfer fallback contributes one comparison:

```text
last -> move -> copy
move -> copy
deep -> no fallback
copy -> no fallback
```

It does not automatically dominate every other axis.

Examples requiring future comparison work include:

- exact `last` by value versus fallback `move` by writable reference;
- exact `move` through readonly reference versus fallback `copy` by value;
- better stance match versus better type conversion;
- better qualification match versus better expected result;
- and explicit receiver stance versus unstanced receiver fallback.

When no candidate dominates across every applicable slot and dimension,
ambiguity remains the correct outcome.

## Value versus reference transfer

For otherwise equal `move` or `last` candidates, a mutable/writable reference is
better than a by-value form when the source directly supplies that authority:

```zax
process(message as move)
```

The reference candidate acts on caller-owned storage without inserting a
copy-in barrier.

A readonly restatement can make that candidate nonviable:

```zax
process((message as readonly) as move)
```

If a readonly-reference `move` candidate also exists, the readonly reference and
by-value candidates may remain incomparable.

Future work must preserve the distinction:

- value form means `copy` in and process independent storage;
- reference form means operate on caller-visible storage;
- cheaper execution alone does not justify changing ownership semantics.

## Receiver comparison

Receiver stance is one independent callable slot.

- An exact receiver stance admits only that stance.
- An unstanced receiver declaration is a fallback for every stance.
- Exact receiver stance is better than fallback when other comparisons permit
  dominance.
- Receiver matching does not use the source stance fallback ladder.

Future work must integrate receiver comparison with parameter and result slots
without making receiver stance a lexicographic winner over every other
dimension.

A directly constructed compiler-managed unnamed by-value temporary inherently
offers `last` as its receiver/source stance. This is a stable source category,
not liveness analysis of a named declaration.

## Result-mapping intent

An omitted owned by-value result remains implicit `copy`. At its structurally
final mapping boundary, current invocation behavior requires explicit intent
when offering `last` could materially change the accepted consumer or source
outcome.

Future selection work must preserve:

- producer result stance as public prototype information;
- caller-side visibility of the complete destination hole;
- per-result-slot comparison;
- no terminal inference for reference results;
- and no intent error when `last` changes neither accepted contract nor source
  outcome.

Generic and generated result families must expose enough exact metadata for that
mapping decision without treating every temporary reference or pointer pointee
as terminal.

## Generic specializations

Future explicit qualifier generics may produce exact demanded specializations:

```zax
// Illustrative only.
process$(Mutability, PlaceStance) final : (
  result : Message Mutability readonly PlaceStance &
)(
  input : Message Mutability readonly PlaceStance &
) = {
}
```

Selection work must decide:

- when qualifier or type arguments are inferred;
- how constraints affect viability;
- whether a concrete declaration is better than an inferred specialization;
- how several valid specializations compare;
- when an invalid specialization remains latent;
- and how diagnostics expose inferred metadata.

Ordinary qualifier omission never creates genericity.

## Generated and declared candidates

Generated lifecycle declarations participate like exact declarations. A
programmer declaration that fails to match a generated shape may coexist with
it and create ambiguity.

Future selection work must preserve:

- exact generated signature identity;
- demand-driven generation;
- body/default/existing/bodyless/forbidden availability;
- unavailable-best behavior;
- concrete specialization identity;
- and diagnostics showing the generated and programmer-declared differences.

## Source compatibility

Adding or removing a declaration, generic specialization, generated shape, or
preference relation may:

- make previously unique source ambiguous;
- make an unavailable declaration uniquely best;
- or make previously invalid source valid.

It must never silently reinterpret a tie by declaration, import, source, module,
or discovery order.

Future revisions may improve the comparison model while Zax remains conceptual
design. Once implementations, users, and compatibility commitments accumulate,
preference changes require an appropriately higher threshold and migration
guidance.

## What this input does not decide

This file does not establish:

- a complete preference table;
- a generic syntax or constraint system;
- exact callable-prototype syntax;
- runtime dispatch;
- template or specialization implementation;
- formal diagnostic identifiers;
- or `switch`/`case` and pattern-matching behavior.

## Activation and retirement

Activate this input when callable preference, overload resolution, generic
specialization, exact function aliases, generated declaration comparison, or
`own` surface selection is reviewed.

That work must:

1. preserve partial-order dominance and loud ambiguity;
2. define each comparison dimension and its interaction with results;
3. test value/reference, qualifier, stance, receiver, generic, and generated
   cases together;
4. move accepted behavior into invocation, declaration, generic, composition,
   and reflection owners;
5. update source-compatibility guidance; and
6. remove this file from the raw index and retire it when no unresolved callable
   selection input remains.
