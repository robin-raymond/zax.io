# Zax operators

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers reading, writing, defining, or evaluating Zax operators |
| Applies To | Programmer-facing operator model and selection; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | The operator mental model; the general operator form and fixity table; symbolic, phrase, circumfix, call/index, and mixfix categories; type ownership, ordinary receiver ownership, type-qualified `unbound`/`once` discovery, and the absence of global user operators; candidate-tree formation, structural completeness, and pruning; outward result flow and expected-result limits; candidate discovery; contextual/explicit operator completion and direct-before-contextual fallback; application of shared callable viability, expected-result, preference, ambiguity, unavailable-best, and post-discovery preferred-projection rules; private eligibility before preference; once-only evaluation; eager, protected, and short-circuit behavior; protected intrinsic domains; compile-time value join `<+>` as an ordinary left-owned operator; generic transfer-stance source forms; direct-before-fallback and optional presence/reset/transfer-source behavior; operator costs, diagnostics, source stability, and summary menu |
| Does Not Own | Complete transfer semantics ([transfer stances](transfer-stances.md)); runtime case-test interpretation ([switch, case, and default](switch.md)); composition exposure and projection eligibility ([Zax composition](composition.md)); complete [structural shapes and compatibility](structural-shapes-and-compatibility.md); phrase-specific behavior ([operator phrases](operator-phrases.md)); exact forms and domain reservation ([operator catalog](operator-catalog.md)); complete [optional behavior](optional-values.md); uncommitted integer evaluation and realization ([integer literals and realization](integer-literals.md)); protected integer behavior ([integer operator catalog](integer-operator-catalog.md)); fixed-point behavior ([fixed-point scalars](fixed-point-scalars.md)); floating-point behavior ([floating-point scalars](floating-point-scalars.md)); mixfix matching ([mixfix operators](mixfix-operators.md)); or shared callable preference/result routing ([function invocation](function-invocation.md)) |
| Source / Provenance | Legacy [basics](../basics.md), current [Nothing-instance design](nothing-instances.md), and retired optional evidence together with dispositioned operator-overloading material |

## Mental model

An operator is a callable operation selected from a language-recognized source
form and qualified operands.

```zax
result := left + right
```

Symbolic source syntax forms one fixed expression tree. Word-spelled source may
present several structurally complete readings, so the compiler forms every
complete candidate tree and requires exactly one to remain viable. Operator
discovery and selection then choose one declaration for each ordinary node, or
one direct [mixfix](mixfix-operators.md) declaration for a matching
multi-component tree.

Zax permits custom types to define domain-specific results and effects. It does
not let a declaration invent arbitrary punctuation, change precedence, break
protected built-in behavior, or resolve ambiguity by source order.

## Operator forms

Zax distinguishes:

- **symbolic operators**, such as `+`, `&~`, and `<<%`;
- **[operator phrases](operator-phrases.md)**, operators spelled as one or more
  exact words such as `logical nand` and `as`;
- **circumfix operators**, such as `|value|`, with recognized opening and closing
  components;
- **postfix delimited call and index forms**, `callable(...)` and
  `container[...]`; and
- **mixfix operators**, one operation selected from a tree containing several
  recognized components and operand holes.

The reserved `@`, `@!`, `@<`, and `@!<` spellings are
**declaration-bound allocation initializers**. They use the symbolic token
catalog but are not ordinary open unary operators: a declaration supplies the
resident type and pointer result role, and user declarations cannot overload
their allocation behavior. See
[Zax pointers, allocation, and arenas](pointers-and-arenas.md#allocate-through-a-declaration).

**Fixity** states where an operation's operands occur relative to its recognized
components:

| Operator form or fixity | Source shape |
| --- | --- |
| Pre-unary | `operator operand` |
| Post-unary | `operand operator` |
| Binary | `left operator right` |
| Circumfix | `opening operand closing` |
| Call/index | Postfix delimited inputs |
| Mixfix | One complete operation consuming several fixed components and operand holes |

Symbolic and phrase spellings share this table. A phrase component uses only
pre-unary, post-unary, or binary fixity; circumfix, call, and index remain
separate operator forms rather than phrase-component fixities. A word-delimited
operation that surrounds or separates several holes is a
[mixfix](mixfix-operators.md) built from several phrase components rather than a
phrase circumfix.

The complete current symbolic/circumfix menu, exact phrase forms, and the
precedence table are in the [operator catalog](operator-catalog.md). Phrases have
their own [programmer model](operator-phrases.md), and mixfix has
[its own](mixfix-operators.md).

## Declarations and receiver operands

User-defined nonliteral operators belong to their owning type and state their
form explicitly. For ordinary instance operations, `_` is the receiver operand:

```zax
Vector :: type {
  operator pre unary '-' final :
    (result : Vector)() readonly = {
    return negate(_)
  }

  operator binary '+' final :
    (result : Vector)(rhs : Vector) readonly = {
    return add(_, rhs)
  }
}
```

Composition adapters name operators by the same complete structured form used
by their declarations:

```zax
Car :: type {
  engine : Engine

  operator binary '+' final :
    (result : Power)(rhs : Fuel) readonly =
      via engine.operator binary '+'

  operator binary '-' final :
    (result : Power)(rhs : Fuel) readonly =
      via family engine.operator binary '-'
}
```

Singular `via` selects one overload through the written prototype. `via family`
uses that prototype as an exact anchor and adopts the whole structured operator
family; each eligible overload still needs its own unique mechanical mapping.
The family selector is not punctuation alone.

| Type-defined form | Receiver | Explicit inputs |
| --- | --- | --- |
| Pre/post unary, symbolic or phrase | Sole operand | None |
| Binary, symbolic or phrase | Left operand | Right operand only |
| Circumfix | Enclosed operand | None |
| Call/index | Callee/base operand | Declared call/index inputs |
| Mixfix | Declared receiver-anchor hole | Every other hole |

When a concrete type identity occupies the source position that anchors
type-owned lookup, it is not automatically a runtime receiver. An `unbound`
operator uses the type identity only for qualification and discovery, so `_` is
unavailable. A `once bound` operator deliberately supports both the
type-qualified receiverless route and an instance-qualified route. Complete
declaration examples are in
[Zax operator phrases](operator-phrases.md#type-arguments-and-type-qualified-operators).

Representative declarations and uses:

### Pre-unary

```zax
negated := -vector
```

The type-defined `operator pre unary '-'` above receives `vector` as `_`.

### Post-unary

```zax
Counter :: type {
  operator post unary '++' final :
    (previous : Counter)() writable = {
    // Update `_`, then return its previous value.
  }
}

previous := counter++
```

### Binary

```zax
sum := left + right
```

The left `Vector` supplies the receiver-owned `+` declarations; `right` binds the
selected declaration's explicit parameter.

### Circumfix

```zax
Vector :: type {
  // Illustrative declaration syntax.
  operator circumfix '|' '|' final :
    (result : Float)() readonly = {
    return calculateMagnitude(_)
  }
}

length := |vector|
```

The enclosed `vector` is the receiver operand. Exact recognized circumfix forms
are in the [operator catalog](operator-catalog.md#circumfix-forms).

### Call

```zax
Interpolator :: type {
  // Illustrative declaration syntax.
  operator call final :
    (result : Float)(position : Float) readonly = {
    return interpolate(_, position)
  }
}

curve : Interpolator
sample := curve(0.5)
```

A declared function is implicitly callable; a custom callable value uses the
overloadable complete call form.

### Index

```zax
MySequence :: type {
  // Illustrative declaration syntax.
  operator index final :
    (result : MyResult)(index : IndexSize) readonly = {
    return readElement(_, index)
  }
}

sequence : MySequence
element := sequence[index]
```

Each postfix index bracket supplies one index. Multidimensional source chains
brackets:

```zax
element := matrix[row][column]
```

Intrinsic arrays use each operation to select the corresponding element or
nested array storage. A custom declaration may instead return an ordinary value,
a reference, or a helper object that represents later access; that helper is
called a proxy.

A bracket containing an inclusive or half-open range forms a distinct splice
operation. Custom `operator splice 1` declarations may return any result type.
Direct indexed or spliced mutation can select one mixfix without an
intermediate proxy. Complete array bounds, slicing, mutation, and result
behavior is defined by
[Zax arrays and slices](arrays-and-slices.md#indexing-element-places).

Mixfix declarations and uses are taught by
[Zax mixfix operators](mixfix-operators.md).

The receiver's type and qualifications participate in selection. Complete
qualification behavior is defined by [qualifiers](qualifiers.md).

## Literal value joining

`<+>` is an ordinary overloadable binary operator whose left operand supplies
receiver ownership:

```zax
combined := left <+> right
```

Its common literal use joins two already concrete constant values during
compilation:

```zax
myText := utf8'first line' <+> c'\n' <+> utf8'last line'
```

Each literal independently specializes and executes before ordinary `<+>`
selection. The selected join must itself be available for required compile-time
execution. A selected runtime-only or otherwise unavailable declaration reports
unavailable best; selection does not fall through to a weaker compile-time
candidate.

The left result identity owns admission, transcoding, failure, and the joined
result. Complete byte, ASCII, Unicode, and legacy string behavior is defined by
[Zax strings and characters](strings-and-characters.md#compile-time-joining).

Intrinsic arrays also define runtime `<+>` as nonmutating sequence joining and
`<+>=` as direct append into a resizable left receiver:

```zax
combined := left <+> right
left <+>= right
```

Their result bounds, layout, allocation, transfer, overlap, and source-state
behavior belong to
[Zax arrays and slices](arrays-and-slices.md#joining-arrays).

`<+>` is distinct from implicit adjacent source merge and its optional explicit
`<|>` spelling. That
[literal-source merge](literal-source-and-operators.md#merge-literal-source) is
non-overloadable and combines payload before any literal value or receiver
exists.

When both immediate literal operands resolve to the same literal declaration,
written `<+>` requires
`intent<same-prefix-literal-join>` because source merge was also available.
The acknowledgement does not alter ordinary operator selection or evaluation.

## Operator phrases

An **operator phrase** is an operator form written as one or more exact words:

```zax
loaf := baker bakes bread dough
converted := source as DestinationType
ready := left logical nand right
```

A phrase is not a second dispatch mechanism. Its receiver, parameter mapping,
viability, partial-order preference, result handling, availability, evaluation,
protection, and mixfix participation use the shared callable and operator model
described by the rest of this document, so a phrase adds spelling rather than
selection machinery.

The cohesive phrase feature is owned by
[Zax operator phrases](operator-phrases.md): exact finite word sequences, phrase
declarations, receiver ownership and the receiver-oriented workaround, phrase
candidate teaching and ambiguity, natural/grouped/fenced source, presentation
after selection, keyword roles, enclosure boundaries, private phrase eligibility,
and phrase costs and diagnostics.

Three consequences matter to the shared operator model and are applied
throughout this document:

- a phrase component uses only pre-unary, post-unary, or binary fixity;
- custom phrase implementations are receiver-owned, which narrows
  [discovery](#discovery); and
- word-spelled source may present several structurally complete readings, which
  is why [candidate-tree formation](#candidate-tree-formation-and-selection)
  exists.

Ordinary user-defined phrase operations are eager, and users cannot declare their
own evaluation strategy. The language protects the exact Boolean signatures whose
evaluation boundary must remain visible; exact Boolean `logical nand`,
`logical and not`, `logical nor`, and `logical or not` short-circuit and form
[mixfix-consumption barriers](mixfix-operators.md#protected-barriers), while
exact Boolean `logical xnor` is eager and consumable. Precedence belongs to the
form rather than the receiver. A **protected form** is an exact form user code
cannot declare at all; exact forms, precedence levels, and the protected forms
are in the [operator catalog](operator-catalog.md#operator-forms).

## Discovery

For an ordinary symbolic use, candidates come from:

1. protected or language-provided declarations for the exact form;
2. type-defined declarations on the sole unary, enclosed circumfix, call/index
   base, or left binary receiver.

The right binary operand does not contribute declarations from its type.
User-defined global operators do not exist:

```zax
myInteger + myCustomValue
// No declaration on MyCustomValue is discovered because it is not the receiver.
```

Symbolic, phrase, circumfix, call/index, and mixfix declarations all follow this
receiver rule. When the receiver is a type you did not write, a
[partial](partials.md) can add the operator to it:

```zax
MyIntegerOps :: partial Integer {
  operator binary '+' final : (
    result : MyCustomValue
  )(
    rhs : MyCustomValue
  ) = {
    // ...
  }
}

addBoth final : (
  result : MyCustomValue
)(
  myInteger : Integer,
  myCustomValue : MyCustomValue
) = {
  Module.MyIntegerOps :: expose partial
  return myInteger + myCustomValue   // valid: the partial's `+` is a receiver candidate here
}
```

The operator is still owned by the receiver: it joins `Integer`'s candidates, and
only where the partial is granted. Protected signatures remain unavailable to
partials.

Importing or exactly aliasing a type preserves its receiver-owned operators.
Generative imported types have distinct receiver and operator identities.
Compatibility posture never widens discovery.

Custom phrase implementations therefore discover only protected or
language-provided declarations for the exact form and type-defined declarations
on their receiver operand. That narrowing and its rationale are taught by
[operator phrases](operator-phrases.md#receiver-ownership).

Structural similarity does not widen discovery to every type with a compatible
shape. Once discovery supplies one concrete expected type, an argument or
result compatibility posture may affect viability without turning member
additions into distant lookup changes. See
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md#compatibility-posture).

`expose` and `preferred` affect operator lookup differently. An operator
generated by `expose` becomes a declaration on the outer receiver, so ordinary
discovery can find it there. `preferred` alone does not make the
compiler search the contained type:

```zax
engine + load // Engine can contribute its operator declarations
car + load    // Car does not contribute Engine declarations through preferred
```

If an independently discovered candidate already expects `Engine`, preferred
projection may still map `car` to `car.engine` during viability. Complete
exposure eligibility belongs to [Zax composition](composition.md#exposing-behavior).

### Contextual completion

A visible typed peer can sometimes supply the missing type for a number literal:

```zax
myValue := 0 + (: MyInteger = 5)
```

This is allowed only when `MyInteger` permits contextual construction from the
number and its `+` operator also permits contextual completion. The source then
behaves as though the programmer had written:

```zax
myValue := (: MyInteger = 0) + (: MyInteger = 5)
```

The construction is real and retains its source-order effects and costs.

Constructors and operators share one completion mode:

- `contextual` permits one bounded completion in a source context defined to
  support it;
- `explicit` requires the concrete value to be present before selection; and
- omission means `explicit`.

Exact final keyword placement remains future source integration. Conceptually,
both declarations must opt in:

```zax
// Illustrative completion-mode syntax.
+++ contextual final : ()(rhs : Integer) = {
}

operator binary '+' contextual final : (
  result : MyInteger
)(
  rhs : MyInteger
) = {
}
```

### Direct operations win before contextual fallback

Contextual completion is attempted only when ordinary selection has no viable
direct candidate. It never repairs:

- ambiguity among direct candidates;
- a range failure after a direct candidate is selected;
- a uniquely best but unavailable operation; or
- a source tree that remains structurally ambiguous.

The fallback obtains one type from one visible peer or declared type anchor,
tries one contextual construction, and then tests only the already identified
operator form on that type. It never chains constructors or conversions and
never searches unrelated types.

The right operand still contributes no declarations during ordinary discovery.
The contextual fallback is a later, explicitly permitted completion path, not a
general reversal of receiver ownership.

Only operator contexts currently opt in. A contextual constructor does not by
itself authorize implicit construction in ordinary calls, returns, or
conditional convergence.

The complete number-literal examples, sign/range checks, `as` behavior, and
stopping rules are in
[Zax integer literals and realization](integer-literals.md#when-a-visible-type-may-complete-an-operand).

## Candidate-tree formation and selection

A symbolic source form fixes one expression tree before selection. Word-spelled
source may present several structurally complete readings, so recognition forms
candidate trees and rejects incomplete combinations rather than preferring the
longest word sequence. A reading that leaves source unattached, or that places
two adjacent expressions with no joining operation, is not one expression and is
eliminated before parameter matching.

The observable model is:

1. establish the logical statement and hard source boundaries;
2. form every structurally complete phrase/operator/mixfix candidate tree;
3. eliminate incomplete holes, unattached source, unsupported fixities, and
   impossible receiver forms;
4. resolve inner operations independently;
5. require an inner expression node to produce exactly one usable result;
6. propagate that selected result's type, qualifications, result shape, and
   transfer behavior outward to the next receiver position;
7. apply ordinary parameter mapping, viability, preference, and availability
   within each candidate tree;
8. accept the source when exactly one complete tree remains viable;
9. diagnose an operator-attachment ambiguity when several complete trees remain
   viable; and
10. validate the selected tree's physical phrase presentation only after
    selection.

This is an observable dependency model, not a required compiler-pass
implementation. An implementation may prune inexpensive impossibilities early so
long as it produces the same result.

Resolution proceeds only through the finite candidate trees already formed from
source. A selected result cannot invent new phrase words, cause source to be
rescanned, or start a fixed-point rewrite.

Physical presentation confirms a selected interpretation and never selects it.
The phrase-specific presentation contract and worked examples are owned by
[operator phrases](operator-phrases.md#presentation-confirms-a-selection-it-never-makes-one);
general physical-line and trivia mechanics remain with
[source structure](source-structure.md#operator-phrase-source-integration).

Worked word-spelled examples, the distinction between overlapping words and real
ambiguity, and the grouping or fencing a programmer uses to state the intended
reading are taught by
[operator phrases](operator-phrases.md#how-word-spelled-source-is-interpreted).

## Viability, preference, and availability

Operators apply the shared callable stages:

1. discovery;
2. operand mapping;
3. viability;
4. partial-order preference; and
5. availability.

During operand mapping, an already discovered candidate may supply an expected
type that permits `preferred` projection. The key ordering is:

1. discover operator candidates from the ordinary operand surfaces;
2. let each candidate's parameter types guide operand mapping;
3. consider preferred projection only for a candidate that is already known.

Projection never contributes new declarations, swaps operands, or outranks a
direct operand match. Several viable projections remain subject to the ordinary
partial order and ambiguity rules. See
[Zax composition](composition.md#nested-and-operator-projection).

A candidate is viable only when receiver, inputs, qualifications, transfers,
constraints, result context, and availability requirements fit.

Preference is not a score. Candidate A dominates B only when A is no worse for
every comparable receiver/input/result slot and strictly better somewhere.
Declaration or partial provenance supplies no preference among declarations on
the receiver: equal or incomparable surviving candidates are ambiguous. A
granted partial's operators compete with the receiver's own on equal terms.

A uniquely best bodyless, `forbidden`, unsupported generated/default, or
otherwise unavailable declaration reports an unavailable operation. Selection
does not silently fall through to a weaker candidate.

The complete shared relation is defined by
[function invocation](function-invocation.md#candidate-selection).

## Result shapes and expected context

An operator may declare zero, one, or several result slots:

```zax
sum:, carry: = customLeft + customRight
```

A nested expression requires exactly one usable value:

```zax
combined := customLeft + customRight
// error if the selected + has two mandatory results
```

Several results do not become an implicit tuple or structural value.

### Outward result flow

A uniquely selected inner result may make an enclosing operation viable. Its
type, qualifications, result shape, and transfer behavior propagate outward to
the next receiver position.

Zero or multiple results cannot fill one expression position:

```zax
sum:, carry: = addWithCarry(a, b)

combined := addWithCarry(a, b) next operation
// error: no single intermediate receiver
```

There is no implicit "result of results" type. A future explicit combiner may
produce one structure value, after which that structure has an ordinary
compiler-known type and may become a receiver.

An inner result-only ambiguity does not flow backward from an enclosing
operation. The allowed direction is `selected inner result -> enclosing receiver
selection`, never `enclosing receiver requirement -> manufacture an inner result
choice`. The programmer states an intended narrow boundary with a complete typed
declaration.

Worked word-spelled examples of outward flow are taught by
[operator phrases](operator-phrases.md#results-flow-outward-never-inward).

### Narrow expected result

A direct, complete, explicitly typed declaration may provide narrow expected
result context:

```zax
integer : Integer = parseableLeft + parseableRight
```

An inferred declaration, outer overloaded call, later assignment, condition, or
unresolved surrounding operator does not invent that context. Result quality can
break a tie only after receiver and supplied-input comparisons are equal.

Expected-result context may choose an implementation within one already formed
tree. It may not choose between distinct operator extents or attachment trees.

See [function invocation](function-invocation.md#narrow-expected-result-selection)
for the shared boundary and
[operator phrases](operator-phrases.md#results-flow-outward-never-inward) for the
word-spelled example.

## Evaluation order

Selection does not execute runtime operands. After selection, an ordinary binary
operation:

1. evaluates the receiver/left operand once;
2. immediately binds it according to the selected qualification and transfer;
3. evaluates the right operand once;
4. immediately binds it; and
5. invokes the operation.

```zax
result := makeLeft() + makeRight()
```

`makeLeft()` completes before `makeRight()`.

Pre/post unary, circumfix, call/index, and mixfix forms likewise evaluate every
written operand hole once in visible source order. Protected short-circuit forms
are the documented exception: they may skip a later runtime operand while still
requiring every possible path to resolve at compile time.

Optimization may elide work only when the selected operation, order, effects,
results, and lifetimes remain unchanged.

### Runtime case-test interpretation

A runtime `case` supplies its retained selector as an omitted operand through a
construct-specific sequence:

```zax
case < upperBound
case expectedValue
case ?
```

Runtime selection first tries a direct binary or post-unary operation, then
equality with the complete written expression, then an exact operandless
pre-unary component. Each final operation must return exactly `Boolean`.

This sequence chooses the case interpretation before the ordinary operator model
selects a declaration within that interpretation. Direct ambiguity and an
unavailable uniquely best direct operation remain errors; case fallback does not
erase them.

The full ordering, grouping boundary, selector capture, evaluation behavior, and
source-stability consequence are owned by
[Zax switch, case, and default](switch.md#how-one-case-test-is-interpreted).

## Protected intrinsic domains

A **protected intrinsic signature** is an exact operator signature whose every
operand belongs to a closed intrinsic family. A user declaration cannot replace
it or claim a currently unsupported all-intrinsic combination.

```zax
// error: protected intrinsic signature
operator binary '+' final :
  (result : Strange)(lhs : Integer, rhs : Integer) = {
}
```

A mixed custom signature remains extensible:

```zax
operator binary '+' final :
  (result : MyType)(lhs : Integer, rhs : MyType) = {
}
```

The test is whether a programmer type may still declare the same spelling. When
it may, the language owns only its intrinsic signatures; when no receiver may
declare it, the spelling is a **protected form**, listed by the
[operator catalog](operator-catalog.md#protected-forms). `inner` is a protected
intrinsic signature whose operand is a pointer-rooted member path rather than a
value; see
[interior pointers](pointers-and-arenas.md#interior-pointers).

This reserves fundamental Boolean, integer, pointer-sized numeric, fixed-point,
floating, and other closed domains for coherent language evolution. Exposed
integer identities receive the identity-adjusted protected surface defined by
[Zax integers](integers.md) and [Zax identity types](identity-types.md).
Fixed-point and floating availability is defined by
[fixed-point scalars](fixed-point-scalars.md) and
[floating-point scalars](floating-point-scalars.md). Extensible library types
such as `String` may expose ordinary library-owned overloads.

Signature protection differs from a
[mixfix-consumption barrier](terms.md#mixfix-consumption-barrier). A barrier is a
resolved source node that a user mixfix may accept as a completed hole value but
may not consume as a key component. Exact Boolean short-circuit operations are
barriers because consumption would erase their conditional RHS evaluation.

Exact Boolean XOR illustrates the distinction:

- `Boolean ^^ Boolean` is a protected intrinsic signature, so user code cannot
  replace it;
- it is eager, so a custom-receiver mixfix may consume that node without erasing
  short-circuit behavior.

Mixed custom logical shapes are eager and may participate in a mixfix key.

Protection does not by itself promise that every exact scalar format has every
operation on every target. Missing support for a language-required operation
indicates an incomplete toolchain; a format operation explicitly classified as
target-optional instead produces an availability diagnostic. Implementations may
use instructions, constant folding, emitted code, intrinsics, or automatically
linked helpers; operator semantics promise no lowering.

Exact family availability is listed in the
[operator catalog](operator-catalog.md#protected-domains). Complete protected
integer behavior is in the
[integer operator catalog](integer-operator-catalog.md).

## Boolean operations and fallback

Exact `Boolean && Boolean` and `Boolean || Boolean` are language-provided,
protected, left-to-right short-circuit operations:

```zax
if ready && ?resource
  use(resource)
```

Other valid `&&`/`||` shapes are ordinary eager overloads and may return
arbitrary result shapes. Exact Boolean `^^` is protected and eager.

Zax also supplies exact logical NAND, AND-NOT, NOR, OR-NOT, and XNOR phrase
operations. The
[catalog](operator-catalog.md#exact-logical-phrases) records their exact
forms, precedence, and evaluation.

### Direct-before-fallback

The closed complement fallback set is:

```text
?  <-> !
== <-> !=
<  <-> >=
<= <-> >
```

For a written operation:

1. resolve the direct candidate normally;
2. use one applicable direct candidate even if its result later fails a Boolean
   requirement;
3. report direct ambiguity or unavailable best;
4. only no applicable direct candidate attempts the paired operation;
5. resolve that counterpart directly without recursive fallback or Boolean
   filtering;
6. require its selected result to be exactly `Boolean`; and
7. apply one protected Boolean negation.

Fallback never swaps operands, combines comparisons, or changes receiver
discovery.

For example, a type may define only direct `!`:

```zax
Ready :: type {
  isReady : Boolean

  operator pre unary '!' final :
    (result : Boolean)() readonly = {
    return !_.isReady
  }
}

ready : Ready

if ?ready
  use(ready)
```

When no direct `?Ready` candidate applies, the compiler selects `!ready`
normally, requires its result to be exactly `Boolean`, and applies protected
Boolean negation. Conceptually:

```zax
if !(!ready)
  use(ready)
```

The grouping is required because adjacent independent unary applications are not
valid source.

Comparison fallback follows the same model:

```zax
Key :: type {
  operator binary '==' final :
    (result : Boolean)(rhs : Key) readonly = {
    return sameKey(_, rhs)
  }
}

if left != right
  handleDifference()
```

With no applicable direct `!=`, the operation may use:

```zax
if !(left == right)
  handleDifference()
```

Direct ambiguity, unavailable best, or a non-Boolean counterpart result reports
an error rather than activating fallback.

```zax
if : Boolean = ?value
  useValue(value)
```

The typed declaration supplies result context that an ordinary condition does
not.

### Optional and variant presence

For an optional value, `?value` is the recognized presence operation and returns
`Boolean`:

```zax
if ?optionalValue
  use(optionalValue.)
```

Postfix optional dereference resolves before a pre-unary operator, so
`?optionalValue.` means `?(optionalValue.)`, not "test presence and then
dereference." Dereference requires proof that the optional contains a live value.

An arbitrary user-defined Boolean-returning `?` does not prove storage live. The
optional-presence contract must be recognized by the analyzer. The lifetime
obligation is owned by
[construction, replacement, and destruction](construction-and-destruction.md#conditionally-live-storage-and-access-proof).

For a [variant](variants.md), `?value` returns whether any named alternative is
active:

```zax
if ?message
  print("some alternative is present")
```

Presence does not identify the active name and therefore does not by itself
prove `message.text` available. Named access uses the variant's checked-access
contract, while variant `switch` routes and binds an exact alternative.
Complete behavior belongs to
[Zax variants](variants.md#presence-and-named-access-answer-different-questions).

### Pointer, function, and receiver presence

The protected `?` form also returns exactly `Boolean` for:

- a pointer, reporting whether its immediate relationship is non-vacant;
- a function value, reporting whether an implementation is assigned; and
- `_` inside a `once bound` function, reporting whether the call supplied an
  ordinary receiver source.

For a weak pointer or weak-capable callable, presence does not prove that strong
ownership remains open. `liveness probe value` is the protected nonacquiring
snapshot query. Actual weak-to-strong construction is the operation that pins
the target.

`binding kind of callback` reports the implementation/receiver mode currently
installed in receiver-capable callable storage. Complete callable behavior is
defined by
[Zax lambdas and callable composition](lambdas-and-callable-composition.md#presence-liveness-and-installed-binding-kind).

`?_` in a non-`once` bound function is a non-acknowledgeable intent error.
Postfix access still binds first, so `?_.` dereferences `_` and applies ordinary
`?` selection to the resulting value.

These domains share source spelling without sharing one universal value.
Complete Nothing-instance and unavailable-function behavior belongs to
[Zax Nothing instances](nothing-instances.md).

### Transfer-stance source forms

The protected post-unary phrase forms:

```zax
source as copy
source as deep
source as move
source as last
```

preserve the source's complete value shape and restate one offered transfer
stance for the immediate consumer.

The form itself performs no `copy`, `move`, destruction, or runtime call because
it only changes the stance available to selection. A selected constructor,
assignment, parameter, result slot, receiver-qualified function, or another
consumer produces any source effect after accepting the stance.

These forms are distinct from binary conversion:

```zax
converted := source as DestinationType
moved := source as move
```

Candidate-tree formation recognizes a complete destination type argument for the
binary form and one exact protected post-unary phrase for the stance form. The
source does not require an exact phrase fence.

Complete `copy`/`deep`/`move`/`last` meaning, fallback, declaration stance,
receiver behavior, and source state are defined by
[Zax transfer stances](transfer-stances.md).

### Opaque type probes and transfer

`opaque is type MyType` is a protected exact-type probe for `OpaqueOwner`,
`OpaqueObserver`, and `OpaqueReferenceObserver`. It creates no pointer or
reference, transfers nothing, and grants no liveness proof.

`unsafe transfer (opaqueOwner as last)` is the protected unchecked ownership
recovery form. It reconstructs typed ownership from allocation metadata rather
than reinterpreting pointer bits. Complete behavior belongs to
[Zax pointers, allocation, and arenas](pointers-and-arenas.md#type-erased-ownership-and-observation).

### Protected structural forms

Compatibility posture restatement, source-relative `anchor`, safe structural
`as`, safe and unsafe coercive `as`, and [`unsafe cast`](casting.md) are
protected forms. User declarations cannot replace them.

`>-`, `-<`, and `-<>-` are mapping-bound structural operators rather than
ordinary overloadable arithmetic expressions. Their punctuation visually opens
a packed source, closes open values into a packed destination, or performs both.
Each source expression or producer evaluates once.

Structural posture and safe conversion preserve complete qualifications.
Coercive conversion may be safe when its representation relation is total and
uses `unsafe` only for validity or writable-restoration responsibility.
Unchecked reinterpretation remains explicitly unsafe; see
[Zax conversions and casts](casting.md). Exact source forms are listed by the
[operator catalog](operator-catalog.md), and complete programmer behavior belongs to
[Zax structural shapes and compatibility](structural-shapes-and-compatibility.md).

### Optional, pointer, and function reset

`reset value` is a protected pre-unary optional phrase. It destroys a present
boxed value, leaves the same wrapper absent, and returns a reference to that
wrapper:

```zax
reset optionalValue
(reset optionalValue) .= [{}]
```

For a pointer, `reset` releases the allocation, ownership, observation, or
scheduled-disposition relationship for which the pointer or its declaration has
authority, then leaves the pointer vacant:

```zax
reset pointer
```

An open-ended raw pointer requires allocation-root and disposition-authority
proof or narrow unsafe responsibility. Complete pointer behavior is defined by
[Zax pointers, allocation, and arenas](pointers-and-arenas.md#resetting-a-pointer).

For a varying function value, `reset` releases its callable representation and
owned captures, then leaves the slot unavailable. Function presence becomes
false. Complete behavior belongs to
[Zax Nothing instances](nothing-instances.md#function-values-have-presence).

### Raw pointer vacate

`vacate pointer` is a protected pre-unary phrase for raw pointers. It discards
the address and installs vacancy without disposition. Plain use requires a
proved non-owning relationship; `unsafe vacate` accepts responsibility for an
opaque but potentially valid external relationship.

Managed pointers and any use that would guarantee a leak reject the form even
under `unsafe`. Complete behavior belongs to
[Zax pointers, allocation, and arenas](pointers-and-arenas.md#vacating-a-raw-pointer).

### Protected transfer-source operations

`last value` and `move value` are protected pre-unary optional source adapters.
They produce the same complete optional type and present distinct transfer
stances to a consumer:

```zax
source : MyType?

terminallyTransferred : MyType? = last source
```

Neither form performs postfix optional access or produces a bare boxed
`MyType`. An absent wrapper can therefore remain absent without creating an
invalid boxed access.

`last value` is not merely another spelling of `value as last`. When accepted,
the optional immediately enters terminal source state for analysis, keeps the
required boxed state live through the complete consumer, then becomes absent.
`move value` schedules no wrapper cleanup; it preserves source presence and
leaves a present boxed value live in its defined moved-from state.

Postfix optional access yields a reference under the boxed qualifications after
proof. It is not a runtime-checking `unwrap` operation. Safe source that lacks
proof is rejected rather than receiving an implicit runtime fallback.

A future narrow unsafe presence/lifetime assertion may permit access the compiler
cannot prove. It adds no required runtime check; if the asserted lifetime or
presence is false, the language provides no defined behavior. Debug
instrumentation may detect a violation and panic without making that check a
language guarantee. Complete optional state, construction, qualification,
nesting, and transfer behavior is defined by
[Zax optional values](optional-values.md).

## Callable composition and chaining

`>>` constructs a callable without invoking it:

```zax
combined := first >> second
```

Compatible result/input slots map positionally. A reshape supplies explicit
no-storage remapping:

```zax
combined := first >> MyMapping >> second
```

The three-part form is one composition operation, not two calls and not an
intermediate reshape value.

`|>` invokes immediately:

```zax
result := source |> first() |> second()
```

Complete partial capture, mapping order, defaults, evaluation, and cost behavior
belongs to
[Zax lambdas and callable composition](lambdas-and-callable-composition.md#compose-functions-for-later-use).

## Branch-specific selection

A conditional expression may select another overload or mixfix on each path:

```zax
result := f + (condition ?? c ;; d)
```

`f` evaluates once. The condition evaluates once, only the selected arm runs,
and that path invokes its selected operation.

One path may use a direct mixfix while another decomposes into ordinary
operators. One logical node may be protected short-circuiting on one path and
eager on another. Every possible path is validated and must converge by the
complete operation boundary.

Condition, selected-arm, and convergence behavior are defined by
[core flow control](core-flow-control.md#conditional-expression-and-branch-convergence).
Mixfix path behavior is defined by
[mixfix operators](mixfix-operators.md#branch-specific-mixfix-selection).

## Assignment and mutation boundaries

Assignment uses an existing destination:

```zax
total = value  // error if total is unresolved
total := value // declaration and initialization
```

A user operator cannot introduce an unresolved caller name. Declaration
initialization remains language syntax rather than an overloadable `=` node.

Protected ordinary intrinsic assignment updates the existing mutable value and
returns writable access to the destination. Assignment is right-associative.

Compound operators are direct operations, not automatic rewrites through a
value operation and assignment:

```zax
counter += delta
counter = counter + delta
counter .= counter + delta
```

The second form may select two operations and create a temporary before ordinary
in-lifetime assignment. The first evaluates its destination once and invokes
its own direct candidate. The third explicitly requests complete reconstructive
replacement.

`.=` is protected rather than overloadable. An ordinary operator body retains
its receiver qualifications and cannot grant itself transitional construction
authority. Complete replacement, including immutable varying replacement,
remains the compiler-owned lifecycle operation defined by
[construction, replacement, and destruction](construction-and-destruction.md#reconstructive-replacement).

### Discardable access from assignment forms

Several protected forms return access to the place they changed, so the access
can be used in a larger expression. That access is discardable, so each form
is also a complete statement:

```zax
total = value        // returns writable access to total
first = second       // generated copy assignment: readonly copy access
counter += delta     // intrinsic compound: writable access to counter
++counter            // intrinsic pre-increment: writable access to counter
reset message        // access to the wrapper
place .= source      // access to the place it constructed
```

The same choice is available to operator authors through the ordinary `#`
result marker:

```zax
operator binary '+=' final : (
  total # : MyValue writable &
)(
  rhs : MyValue
) = { … }

total += amount      // fine: the result is discardable

operator binary '+' final : (
  sum : MyValue
)(
  rhs : MyValue
) readonly = { … }

a + b                // error: the sum is required and unused
```

- For an operator that changes a place, such as `=` or `+=`, declare the result
  with `#`. The change is the point, and the result is a convenience for
  chaining.
- For an operator whose result is meant to be used, such as `+` producing a new
  value, leave the result required. A statement that computes the value and
  drops it is almost always a mistake, and the required result diagnoses it.

The intrinsic integer forms follow this pattern: pre-increment returns
discardable access, while post-increment returns a required copy of the
previous value and reporting forms return a required report. A type that
declares its own `++` or reporting operators chooses its results the same way.

Complete required-result rules are defined by
[Zax function invocation](function-invocation.md#required-and-discardable-results).

## Costs

Candidate-tree formation costs real compile time when several structurally
complete readings must be retained and pruned. The phrase-specific cost profile
and the design choices that bound it are recorded by
[operator phrases](operator-phrases.md#costs-diagnostics-formatting-and-source-stability).

Programmers must be able to discover:

- selected declaration and discovery domain;
- protected, declared, generated, circumfix, phrase, or mixfix origin;
- eager versus short-circuit evaluation;
- fallback negation;
- copies, moves, references, temporaries, captures, and old-value snapshots;
- allocation and destruction;
- mutation versus replacement;
- arithmetic checks and policy results;
- exact, optional, narrowing, unchecked, and unsafe conversion/admission;
- associated count, delta, and distance result identities;
- unchecked arithmetic and divisor contracts;
- branch-specific selection; and
- zero/multiple result handling.

The detailed per-form costs are recorded alongside their families in the
[operator catalog](operator-catalog.md) and applicable type-specific references
such as the [integer operator catalog](integer-operator-catalog.md).

## Diagnostics

Diagnostics should distinguish:

- unrecognized form;
- declaration that claims a protected form;
- unsupported fixity or arity;
- no discovered candidate;
- incomplete or unattached source;
- several viable candidate trees;
- ambiguity among implementations of one tree;
- an inaccessible declaration that is ineligible rather than merely worse;
- operand, result, transfer, qualification, or constraint mismatch;
- equal or incomparable ambiguity;
- uniquely best unavailable operation;
- protected-signature conflict;
- exact Boolean short-circuit barrier;
- expected-result context versus no such context;
- direct selection versus fallback;
- zero/multiple results where one value is required;
- mixfix selection and structural overlap;
- grouping-required comparison;
- arithmetic policy failure;
- invalid shift count type;
- unproved exact integer conversion;
- failed or unavailable identity admission;
- unavailable maximum-width delta;
- unchecked-contract dependence;
- compile-time source panic; and
- source spacing, presentation, or continuation error.

Phrase-specific diagnostics are listed by
[operator phrases](operator-phrases.md#costs-diagnostics-formatting-and-source-stability).
Exact diagnostic identifiers remain future diagnostics work.

## Source stability

- Adding an overload may select another body or create ambiguity.
- Adding a direct complement suppresses prior fallback.
- Adding a direct mixfix may replace component decomposition.
- Changing operand type may move a logical operation between protected
  short-circuit and eager custom behavior.
- Adding, removing, lengthening, reserving, or repricing a
  [phrase form](operator-phrases.md#costs-diagnostics-formatting-and-source-stability)
  is a source-compatibility event that may make existing source ambiguous or
  invalid.
- Adding a language-defined phrase or built-in signature may make previously
  unavailable source valid.
- Adding an exact-`Boolean` direct operation applicable to an ungrouped case test
  may activate its higher-priority selector-relative interpretation instead of
  prior implicit equality; grouping or explicit `==` preserves equality intent.
- Adding a range fact or declared identity bridge may enable a conversion.
- Changing an integer role's associated type may change an operator result.
- Changing precedence or tokenization would reinterpret existing source.

Source order, import order, and implementation convenience never resolve these
effects silently.

## Boundaries and maturity

This document is current conceptual design, not formal grammar, a compatibility
contract, or an implementation mapping.

See the [operator catalog](operator-catalog.md) for exact forms,
[operator phrases](operator-phrases.md) for word-spelled operations, and
[mixfix operators](mixfix-operators.md) for tree-pattern operations. Literal
declarations and required compile-time joining are defined by
[literal source and operators](literal-source-and-operators.md). Runtime `<+>`,
outside the current array family and future runtime string behavior,
unbounded/custom numeric families, exhaustive fixed/floating forms, non-array
call/index edge cases, pointers, generics, reflection, build-contract syntax,
and panic recovery remain future focused work. Current
fixed-point and floating semantics are owned by
[fixed-point scalars](fixed-point-scalars.md) and
[floating-point scalars](floating-point-scalars.md).

Runtime `switch` uses the shared operator model after its construct-specific
case interpretation; complete behavior is defined by
[Zax switch, case, and default](switch.md).
