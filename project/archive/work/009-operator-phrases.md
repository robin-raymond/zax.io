# 009: Operator phrases and keyword-neutral source

| Field | Value |
| --- | --- |
| Status | Historical project record / non-normative / audit-only |
| Work Item | `009` |
| Created | 2026-08-28 |
| Completed | 2026-08-29 |
| Owns | The bounded review defined by the fixed initiating input |
| Does Not Own | Accepted complete casting, literal, numeric, allocation, lifetime, reflection, generic, formal grammar, or compiler behavior |

## Non-authority notice

This archived file is a historical audit record. It is excluded from ordinary
onboarding and current design reading. Its former aligned findings remain
non-authoritative here; current language and raw owner documents now carry every
promoted or deferred result.

## Fixed initiating input

This section records the information known and aligned when work item `009` was
created. It is intentionally incomplete rather than exhaustive and must not be
rewritten as the work develops.

### Initiating concern

Develop a coherent human-developer-facing model for operator phrases and the
source interpretation required to use them safely:

1. the closed language-defined phrase catalog and any bounded custom/open phrase
   facility;
2. phrase words, holes, fixity, arity, precedence, associativity, and grouping;
3. declaration and forward forms for pre-unary, post-unary, binary, circumfix,
   call/index-related, and mixfix phrase components;
4. contextual keyword, identifier, and operator-phrase recognition;
5. longest-phrase and overlapping-phrase behavior;
6. whitespace, attachment, continuation, multiline layout, and future keyword
   stability;
7. keyword-neutral and confusable-form source, including the `bare{...}`
   candidate;
8. local phrase fencing, including the doubled-single-quote candidate and its
   literal interaction;
9. protected intrinsic phrase signatures, eager versus short-circuit behavior,
   and mixfix-consumption barriers;
10. namespace, import, visibility, custom-extension, ambiguity, and source
    stability;
11. costs, failures, diagnostics, reflection pressure, and formatting; and
12. lasting documentation owners and boundaries.

Use the promoted operator, catalog, mixfix, source-structure, declaration,
invocation, qualifier, and terminology owners as constraints. Recover
Zax-specific intent before borrowing phrase behavior from other languages.

### Motivating pressure

Current conceptual owners establish:

- a closed symbolic and circumfix catalog;
- one language-defined precedence and associativity per operator form;
- explicit pre-unary, post-unary, binary, circumfix, call/index, and mixfix
  categories;
- contextual keyword recognition;
- grouped independent unary applications;
- shared callable viability, expected-result limits, partial-order preference,
  ambiguity, and unavailable-best behavior;
- protected intrinsic signatures and distinct mixfix-consumption barriers;
- accepted operation concepts whose exact phrase spellings remain deferred;
- illustrative logical, bitwise, magnitude, shift, transform, scan, and
  multiword phrase operations; and
- a requirement that future phrase syntax not reinterpret ordinary source
  silently.

Legacy evidence proposes built-in phrases such as `as`, `unsafe as`,
`size of`, and host/target layout operations, together with arbitrary custom
phrases such as `run`, `run fast`, and `run fast from`. Checked-in wording does
not decide that those exact phrases or an open custom facility are accepted.

New aligned input adds a concrete keyword-neutral source candidate:

```zax
bare{ if + while }
```

and an intent-gated asymmetric circumfix:

```zax
// Illustrative future syntax.
saturated := bare{ ||value| }
```

The candidate `bare{...}` enclosure:

- uses one contiguous contextual opener, while bare `bare` is not a keyword and
  `bare {` is not the same form;
- creates no scope, runtime operation, final precedence boundary, expression
  node, or mixfix hole;
- requires one independently complete expression or effective statement;
- neutralizes non-operator keyword roles and permits recognized confusable
  forms;
- cannot obtain missing operands, joiners, or header sections outside its
  boundary; and
- becomes transparent before final precedence and mixfix selection.

Another candidate fences phrase words locally:

```zax
value''fast''
''run''value
x ''runs fast'' y
```

That form makes phrase extent and attachment visible but may conflict with empty
or adjacent literals, literal prefixes, escaping, keyboards, and documentation.

This work must refine, replace, or reject those candidates rather than assuming
either spelling is correct.

### Known assumptions

- [Zax language vision](../../../language/vision.md) is accepted foundational
  direction.
- [Zax language principles](../../../language/principles.md) owns reusable
  cross-cutting design and use principles.
- [Zax source structure](../../../language/source-structure.md) owns ordinary
  contextual keywords, whitespace intent, continuation, layout, and diagnostic
  categories.
- [Zax declarations and bindings](../../../language/declarations-and-bindings.md)
  owns namespaces, visibility, and name-introduction boundaries.
- [Zax function invocation](../../../language/function-invocation.md) owns shared
  callable mapping, defaults, result shapes, viability, expected results,
  preference, ambiguity, and unavailable best.
- [Zax qualifiers](../../../language/qualifiers.md) owns receiver and operand
  capability truth.
- [Zax operators](../../../language/operators.md) owns the shared operator model and
  selection.
- [Zax operator catalog](../../../language/operator-catalog.md) owns current exact
  symbolic/circumfix forms, precedence, built-in behavior, and accepted phrase
  operation concepts.
- [Zax mixfix operators](../../../language/mixfix-operators.md) owns tree matching,
  holes, phrase components, protected barriers, and nested outward resolution.
- [Zax language-design terms](../../../language/terms.md) owns eager,
  short-circuit, protection, mixfix, keyword-role, and intent terminology.
- Phrase parsing may not depend on runtime operand values.
- Types, namespaces, and imports may not assign another precedence to one phrase.
- Source, declaration, import, and discovery order may not resolve ambiguity.
- User-defined phrase operations do not gain short-circuit or unsafe behavior
  merely from their words.
- No source-order guess may rescue an otherwise ambiguous phrase meaning.
- There is no language implementation in this repository.

### Known inclusions

- Inventory every operator phrase or phrase-like operation in current, legacy,
  and live raw material.
- Classify each as accepted operation concept, candidate spelling, deferred
  domain behavior, rejected, reserved, protected, or superseded.
- Decide whether phrases form a closed catalog, an explicitly fenced custom
  facility, another bounded extension model, or a combination.
- Decide natural versus explicit source for language-defined and custom phrases.
- Pre-unary, post-unary, binary, circumfix, call/index, and mixfix phrase forms.
- Phrase declarations, type-defined receivers, global visibility, forward
  recognition, imports, and namespaces.
- Phrase holes and exact phrase extent.
- Longest phrase, common prefixes/suffixes, overlapping declarations, and
  ambiguity.
- Precedence and associativity for every accepted phrase family.
- Phrase whitespace, physical newlines, continuation, and formatting.
- Independent pre/post phrase composition versus one circumfix phrase.
- Keyword, identifier, and phrase-role conflicts.
- The complete `bare{...}` candidate, including:
  - one complete expression or effective statement;
  - no scope or lifetime boundary;
  - multiline layout without block-style statement separation;
  - transparent final precedence and mixfix behavior;
  - keyword-neutral interpretation;
  - confusable-form intent;
  - invalid joiner/operator boundary crossing;
  - nested enclosures, reflection, documentation, and formatting; and
  - interaction with future categorized unsafe source.
- Doubled-quote or other local phrase fences and literal conflicts.
- The intent taxonomy:
  - layout-intent error;
  - operator-attachment intent error;
  - redundant-structure intent error;
  - confusable-form intent error; and
  - keyword-role conflict.
- Exact Boolean short-circuit phrase operations and eager custom shapes.
- Protected intrinsic phrase signatures and mixfix-consumption barriers.
- Accepted phrase-operation concepts from the operator catalog:
  - logical NAND, AND-NOT, NOR, OR-NOT, XNOR, and mutation;
  - bitwise NAND, NOR, XNOR, OR-NOT, and mutation;
  - saturating magnitude;
  - modulo-count shifts;
  - bit and byte reversal;
  - masked extraction and deposit;
  - first/last set-bit scans;
  - multiword shifts, rotates, and funnel shifts; and
  - future phrase components inside mixfix declarations.
- Legacy conversion, endian, layout, reflection, lifetime, allocator, host, and
  target phrase evidence at the depth needed to design phrase source safely.
- Costs, diagnostics, source stability, tooling, and formatting.
- Lasting owners, phrase catalog/reference boundaries, and future raw
  destinations.

### Known starting boundaries

- Complete `as`, `unsafe as`, conversion, and casting semantics.
- Complete literal parsing, prefixes, payloads, and custom literal execution.
- Complete numeric, fixed-point, arbitrary-width, enum, or floating-point
  behavior.
- Complete pointer, lifetime, allocator, overhead, and layout semantics.
- Complete reflection and host/target compile-time behavior.
- Complete generics, concepts, specialization, and generic phrase constraints.
- Complete function composition, chaining, lambda, and capture behavior.
- Complete indexing, slicing, ranges, and proxies.
- Complete unsafe controls and categorized unsafe source.
- Formal grammar, parser recovery, compiler implementation, lowering, ABI, and
  toolchain internals.

These areas need not be designed to begin. They do not prohibit following,
recording, or dispositioning material consequences.

### Intentionally unresolved framing

- The exact phrase catalog and canonical words.
- Closed versus bounded open/custom phrase operators.
- Whether custom phrases require explicit fencing at every use.
- Whether `bare{...}` is the correct name, delimiter, and source model.
- Whether keyword-neutral parsing prefers identifiers, phrases, or diagnoses
  incomparable interpretations.
- Whether doubled single quotes or another fence can coexist with literals.
- Exact declaration, forward, import, qualification, and namespace syntax.
- Exact phrase precedence and associativity.
- Longest phrase and common-prefix behavior.
- Phrase circumfix and independent pre/post composition.
- Phrase components inside mixfix declarations.
- Exact protected phrase signatures and short-circuit barriers.
- Source reflection and formatting of transparent or fenced phrase source.
- Which confusable forms justify an explicit mechanism.
- Which findings belong in current operator, catalog, mixfix, source, terms,
  declaration, invocation, cast, literal, or future owners.

### Initial stopping guidance

Stop when the work has:

- dispositioned every known phrase spelling and operation family;
- established natural and explicit phrase source;
- established keyword/identifier/phrase recognition and future-keyword safety;
- established phrase fixity, arity, precedence, associativity, and grouping;
- established declarations, receivers, visibility, forward recognition, imports,
  and ambiguity behavior;
- established or rejected the `bare{...}` model with representative expression,
  statement, flow-header, invalid-boundary, and confusable-form examples;
- established or rejected local phrase fencing with literal coordination;
- established protected, eager, short-circuit, mixfix-component, and custom phrase
  behavior;
- constrained casting, literal, numeric, pointer, lifetime, allocation,
  reflection, generic, unsafe, and implementation consequences;
- identified costs, diagnostics, formatting, and source-stability pressure;
- preserved every deferred consequence in a live owner or indexed raw
  destination;
- identified plausible lasting owners and their boundaries; and
- gathered enough information for a pre-promotion documentation-fit dry run.

Do not promote findings, update current phrase examples broadly, archive this
work item, or begin work item `010` without the separately required discussion,
alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../../documentation.md) - governs ownership,
  focused reading, numbered work, promotion, and archival.
- [Zax language vision](../../../language/vision.md) - provides foundational
  language direction.
- [Zax language principles](../../../language/principles.md) - provides clear
  intent, readability, source stability, cost, and optimization constraints.
- [Zax source structure](../../../language/source-structure.md) - provides
  contextual-keyword, whitespace, continuation, layout, and diagnostic rules.
- [Zax declarations and bindings](../../../language/declarations-and-bindings.md) -
  provides names, visibility, namespaces, and declaration boundaries.
- [Zax function invocation](../../../language/function-invocation.md) - provides
  shared callable mapping, defaults, results, selection, and ambiguity.
- [Zax qualifiers](../../../language/qualifiers.md) - provides receiver and operand
  qualification truth.
- [Zax operators](../../../language/operators.md) - provides the shared operator
  model and selection.
- [Zax operator catalog](../../../language/operator-catalog.md) - provides current
  phrase-operation concepts, precedence obligations, and deferred forms.
- [Zax mixfix operators](../../../language/mixfix-operators.md) - provides phrase
  components, tree matching, holes, and barriers.
- [Zax language-design terms](../../../language/terms.md) - provides current intent,
  protection, and operator vocabulary.
- [Raw bare-source input](../../raw/bare-source.md) - preserves the complete
  keyword-neutral/confusable-form candidate and examples.
- [Raw literal input](../../raw/literal-operators.md) - preserves doubled-quote
  phrase fencing and literal collision pressure.
- Focused [legacy basics operator phrases](../../../basics.md#remaining-historical-operator-phrase-input) -
  preserves the broad built-in phrase inventory.
- [Legacy forward declarations](../../../forward.md) - preserves custom pre/post/
  binary phrase and literal forward examples.

### Consequence-driven

- Read focused [casting](../../../casting.md) when `as`, `unsafe as`, conversion,
  or endian phrases constrain recognition or protected signatures.
- Read [raw function composition and chaining](../../raw/function-composition-and-chaining.md)
  when phrase call/chaining/capture behavior becomes necessary.
- Read focused [compiler directives](../../../compiler-directives.md),
  [meta-functions](../../../meta-functions.md), or [meta-types](../../../meta-types.md)
  when layout, reflection, host/target, or compile-time phrase behavior constrains
  the phrase model.
- Read focused [pointers](../../../pointers.md), [memory allocation](../../../memory-allocation.md),
  [custom allocators](../../../custom-allocators.md), or
  [raw lifetime input](../../raw/lifetimes.md) when pointer, lifetime, overhead, or
  allocator phrases constrain recognition.
- Read [raw indexing and slicing](../../raw/indexing-and-slicing.md) when phrase
  forms interact with index, range, or splice grammar.
- Read [raw analysis controls](../../raw/analysis-controls.md) or
  [raw safety boundaries](../../raw/safety.md) when `bare{...}` interacts with
  semantic assertions, lint control, or categorized unsafe source.
- Expand beyond this list whenever discussion or a material finding establishes
  a concrete need. State why the additional reading is relevant and keep it
  focused.

Material not listed in this reading scope is neither required nor prohibited. It
remains available when discussion or a concrete consequence makes it relevant.

### Audit-only

- `project/archive/`, including work items `001` through `008`.
- [Historical operator-overloading path](../../../operator.md).

Do not read archived work item `008` while conducting ordinary work on `009`.
Its applicable findings have been promoted into current owners and incorporated
into the fixed initiating input above. Read it only when the language maintainer
explicitly requests a targeted audit or provenance investigation.

## Working record

### Current aligned working findings

This section records findings aligned during review of the initial
reconstruction. They supersede conflicting recommendations in the staged
initial reconstruction preserved below. They remain non-authoritative working
findings until a separately aligned and authorized promotion incorporates them
into their lasting owners.

The aligned model is intentionally complete enough to guide promotion while
leaving reflection, generics, compile-time execution, unusual numeric
representations, and other named future concerns available for focused review.

#### Programmer model

An **operator phrase** is an operator form written as one or more exact words:

```zax
loaf := baker bakes bread dough
converted := source as DestinationType
ready := left logical nand right
```

A phrase is not a second dispatch mechanism. Its receiver, parameter mapping,
viability, partial-order preference, result handling, availability, evaluation,
protection, and mixfix participation use the shared callable and operator model.

Phrase forms are open:

- the language defines phrases with protected or reserved behavior where needed;
- user types may declare their own phrase forms;
- user-defined phrase implementations are receiver-owned rather than global;
- natural source is the ordinary use form;
- grouping and fencing are explicit aids when natural source remains ambiguous;
- declaration, import, discovery, or source order never breaks a tie.

A phrase declaration describes one exact finite form. It is not a regular
expression or generated family of word patterns.

#### Exact phrase words and fixed forms

Each phrase word:

- begins with an ASCII lower-case letter;
- continues with ASCII lower-case letters or digits;
- contains no underscore, capitalization, punctuation, or escaping; and
- participates in one exact, statically declared word sequence.

Valid individual words include:

```zax
alpha
alpha256mode
wood2x4
```

Invalid phrase words include:

```zax
Run        // error: upper-case initial
alphaMode  // error: mixed casing
alpha_mode // error: underscore
```

An exact form may contain repeated literal words:

```zax
yes really really really sure delete
```

This may be declared as the exact post-unary phrase
`'really really really sure delete'`. It does not create a repetition
facility.

The initial phrase facility has no:

- optional phrase words;
- repeated-component operators;
- wildcard word runs;
- variadic holes;
- omitted holes; or
- declaration syntax that generates several source forms.

Future work may add such behavior only without changing the meaning of existing
fixed forms.

#### Fixity, phrase extent, and prototype holes

Fixity states where a form's operands occur. The complete operator-form table is
general operator teaching and must become a first-class table in the shared
operator owner during promotion:

| Operator form or fixity | Source shape |
| --- | --- |
| Pre-unary | `operator operand` |
| Post-unary | `operand operator` |
| Binary | `left operator right` |
| Circumfix | `opening operand closing` |
| Call/index | Postfix delimited inputs |
| Mixfix | One complete operation consuming several fixed components and operand holes |

An ordinary phrase component uses only pre-unary, post-unary, or binary fixity:

| Phrase fixity | Source shape |
| --- | --- |
| Pre-unary phrase | `phrase operand` |
| Post-unary phrase | `operand phrase` |
| Binary phrase | `left phrase right` |

Mixfix may consume several phrase components, but each component retains one of
those three fixities. Circumfix, call, and index remain separate recognized
operator forms rather than phrase-component fixities. A word-delimited form
surrounding or separating several holes uses mixfix components rather than
creating an independent phrase-circumfix category.

Promoted phrase documentation should explain this phrase-specific subset and
link to the complete shared table rather than becoming a second owner for
general fixity.

An ordinary phrase declaration states fixity exactly as a symbolic declaration
does:

```zax
operator pre unary 'prepare' ...
operator post unary 'cluck loudly' ...
operator binary 'bakes bread' ...
```

The quoted form contains words only. Operand holes come from fixity and from the
callable prototype, never from substitution inside the quoted text:

```zax
Source :: type {
  operator binary 'as' final : (
    result : DestinationType
  )(
    DestinationType : type
  ) readonly = {
    // `_` is the source value receiver.
  }
}
```

`DestinationType : type` declares a type parameter completed by one concrete
type identity. It has no runtime storage or lifetime. It may determine a value
result type, as it does for `as`.

Type category and unused intent remain independent:

```zax
# : Value                 // runtime value is supplied; no body binding
value # : Value           // binding exists; unused use is intentional
DestinationType : type    // concrete type argument; no runtime value
```

The exact future interaction between `#` and unused type parameters does not
change the distinction between a value parameter and a type parameter.

The operator declaration is both the phrase-form declaration and its
implementation. Zax does not have a separate source-level phrase declaration,
and no phrase-forward form is currently justified.

#### Phrase components inside mixfix declarations

Phrase components use the current mixfix declaration shape directly. For:

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
holes in source order. Type-use holes, when present, are likewise declared by
parameters rather than interpolated into phrase text.

Exact normalization of declaration modifiers, directives, and multiline layout
remains future syntax polish.

#### Candidate-tree formation and selection

Phrase recognition does not use a longest-word-sequence preference. It forms
complete candidate trees and rejects incomplete combinations.

For:

```zax
chickens cluck loudly
```

the existence of both `cluck` and `cluck loudly` does not by itself create an
ambiguity. A shorter interpretation that leaves `loudly` unattached is not a
complete expression.

For:

```zax
chickens cluck loudly outdoors
```

possible combinations may include:

```zax
(chickens cluck loudly) outdoors
(chickens cluck) (loudly outdoors)
```

The second combination is not one expression because it leaves two adjacent
expressions without a joining operation. It is eliminated before parameter
matching.

The aligned semantic model is:

1. Establish the logical statement and hard source boundaries.
2. Form every structurally complete phrase/operator/mixfix candidate tree.
3. Eliminate incomplete holes, unattached source, unsupported fixities, and
   impossible receiver forms.
4. Resolve inner operations independently.
5. Require an inner expression node to produce exactly one usable result.
6. Propagate that selected result's type, qualifications, result shape, and
   transfer behavior outward to the next receiver position.
7. Apply ordinary parameter mapping, viability, preference, and availability
   within each candidate tree.
8. Accept the source when exactly one complete tree remains viable.
9. Diagnose an operator-attachment ambiguity when several complete trees remain
   viable.
10. Validate the selected tree's physical phrase presentation only after
    selection.

This is an observable dependency model, not a required compiler-pass
implementation. An implementation may prune inexpensive impossibilities early
so long as it produces the same result.

Resolution proceeds only through the finite candidate trees already formed from
source. A selected result cannot invent new phrase words, cause source to be
rescanned, or start a fixed-point rewrite.

#### Outward result flow and expected-result limits

A uniquely selected inner result may make an enclosing phrase viable:

```zax
(chickens cluck loudly) outdoors
```

If `cluck loudly` returns one `Chickens` value and `Chickens` supplies
`outdoors`, resolution may continue outward.

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
operation:

```zax
chickens cluck loudly outdoors
// `outdoors` cannot choose among result-only overloads of `cluck loudly`.
```

The programmer may establish the accepted narrow result boundary directly:

```zax
flock : Chickens = chickens cluck loudly
flock outdoors

(: Chickens = chickens cluck loudly) outdoors
```

Expected-result context may choose an implementation within one already formed
tree. It may not choose between distinct phrase extents or attachment trees:

```zax
result : Desired = source phrase middle phrase tail
// error if two phrase trees remain viable, even when only one returns Desired
```

The allowed direction is:

```text
selected inner result -> enclosing receiver selection
```

not:

```text
enclosing receiver requirement -> manufacture an inner result choice
```

#### Visibility, privacy, imports, and unavailable operations

Custom phrase implementations belong to a receiver type. There are no global
custom phrase implementations.

A private phrase form may be retained during structural candidate formation so
the compiler can understand and diagnose the possible source tree. Outside its
permitted context, its implementation becomes semantically ineligible before
callable preference. It cannot defeat, tie, or block a public operation.

Inside the declaring type, private and public implementations are both eligible.
Visibility is not match quality among eligible candidates:

1. compare receiver, inputs, results, qualifications, transfers, and constraints
   without preferring `public` over `private`;
2. require one clearly best eligible implementation under ordinary callable
   rules; and
3. diagnose equal or incomparable eligible candidates as ambiguous.

The ordinary unavailable-best rule remains for an eligible visible declaration
that is bodyless, forbidden, unsupported generated behavior, or otherwise
unavailable after selection. An inaccessible private declaration is instead
ineligible to the outside caller and cannot poison public-only source.

For example:

```zax
Chicken :: type {
  operator post unary 'cluck loudly' private final : (
    result : Chicken
  )() = {
  }
}
```

The modifier follows the declared form rather than preceding `operator`.
Complete private behavior remains future visibility work.

The same source may therefore be ambiguous inside a type and unambiguous
outside it. Suppose `Chicken` supplies private `cluck loudly`, public `cluck`,
and the public result of `cluck` supplies `loudly`:

```zax
CluckedResult :: type {
  operator post unary 'loudly' final : (
    result : CluckedResult
  )() = {
  }
}

Chicken :: type {
  operator post unary 'cluck loudly' private final : (
    result : CluckedResult
  )() = {
  }

  operator post unary 'cluck' final : (
    result : CluckedResult
  )() = {
  }

  inspect final : ()(chicken : Chicken) = {
    result := chicken cluck loudly
    // error: both the private complete phrase and public chained tree are eligible
  }
}

chicken : Chicken
result := chicken cluck loudly
// Outside Chicken, the private tree is ineligible.
// The public `(chicken cluck) loudly` tree may resolve uniquely.
```

Private-only implementations never change the meaning or validity of source
whose caller can see only public operations.

An unrelated import cannot contribute a viable natural phrase without affecting
the receiver type involved. Adding a public phrase to a receiver type remains a
source-compatibility event because it may turn previously unique source into a
loud ambiguity.

An imported extension may not add a natural phrase implementation to a type it
does not own. Future owner-authorized partial-type work may expand that rule only
after defining import visibility, coherence, conflicts, and source stability.

This intentionally narrows one capability of the shared symbolic operator
model. A global symbolic declaration can support a custom value that appears
only on the right of an intrinsic left operand. A custom phrase cannot:

```zax
intrinsicValue combines with customValue
// unavailable when only customValue's type could own the implementation
```

Phrase discovery does not search the right operand's type, and there is no
global custom phrase declaration. The programmer may reframe the wording around
the extensible receiver:

```zax
customValue accepts then combines intrinsicValue
```

An ordinary function or another already supported operation form remains
available when receiver-oriented wording is inappropriate. Promotion must teach
this creative workaround and the intentional ergonomic cost rather than
implying that every symbolic global declaration has a phrase equivalent.

Intrinsic types cannot currently be extended merely to recover the unavailable
left-receiver form. Future partial-type work may investigate owner-authorized
custom phrase extensions, but it must preserve protected signatures permanently
and account for a later Zax version adopting and reserving a phrase previously
supplied by an extension.

#### Value receivers, type arguments, and type receivers

An ordinary value receiver may accept a type argument:

```zax
MyReceiverType :: type {
  operator binary 'for' final : (
    result : ResultType
  )(
    SubjectType : type
  ) = {
  }
}

schema : MyReceiverType
result := schema for SomeType
```

`schema` supplies the value receiver. `SomeType` supplies a type parameter and
does not contribute receiver discovery.

Zax also has an aligned candidate category for an operation whose receiver is a
concrete type identity:

```zax
MyType :: type {
  operator type pre unary 'custom type info for' final : (
    result : MyCustomTypeInfo
  )() = {
    // `_` has the Nothing state because no MyType instance exists.
  }
}

info := custom type info for MyType
```

`operator type` distinguishes a type-identity receiver from an instance
receiver. The concrete type identity supplies discovery and has no runtime
storage or lifetime. `_` points to the `Nothing` instance state because there is
no receiver instance.

A type-receiver operation is not inherently compile-time. It may execute at
runtime and return a runtime value:

```zax
instance := factory create MyType
```

Compile-time execution remains directed and inferred under the ordinary
compile-time function model. Phrase work adds no special execution rule.

For a non-generic declaration, the enclosing type name may identify the
receiver type in its body. Generic instantiations, aliases, the exact meaning of
the enclosing name, and type-receiver qualifications remain future generic and
reflection work.

#### Ordinary and language-defined phrase precedence

Ordinary phrases have one language-wide precedence level between
shift/rotate/composition and relational operations:

| Relative order, high to low | Family |
| --- | --- |
| Higher | Multiplicative |
|  | Additive |
|  | Shift/rotate/composition |
|  | **Ordinary phrase** |
|  | Relational |
|  | Equality |
|  | Bitwise |
|  | Logical |
|  | Conditional expression |
| Lower | Assignment and swap |

This gives ordinary readings:

```zax
result = left combines with right + adjustment
// result = (left combines with (right + adjustment))

ready && value matches pattern
// ready && (value matches pattern)

converted compares with source == expected
// (converted compares with source) == expected

apple = banana nor pear
// apple = (banana nor pear)
```

Same-level ordinary binary phrase chains are left-associative:

```zax
a phrase1 b phrase2 c
// (a phrase1 b) phrase2 c
```

Association defines the tree. Runtime evaluation order remains the separate rule
that eager runtime operands evaluate in visible source order.

Language-defined phrase forms may occupy another globally fixed level. Every
permitted implementation of that exact form inherits its level; precedence never
depends on receiver or operand type.

| Language-defined form family | Precedence |
| --- | --- |
| Logical NAND and AND-NOT | Logical AND |
| Logical XNOR | Logical XOR |
| Logical NOR and OR-NOT | Logical OR |
| Bitwise NAND | Bitwise AND |
| Bitwise XNOR | Bitwise XOR |
| Bitwise NOR and OR-NOT | Bitwise OR |
| Modulo-count and multiword shifts/rotates/funnels | Shift/rotate/composition |
| Phrase mutation forms | Assignment and compounds, right-associative |

New ordinary forms use ordinary phrase precedence. A custom implementation of a
language-defined form inherits the form-specific level.

Low-precedence unary phrases consume the complete higher-precedence expression
on their operand side:

```zax
type of left + right
// type of (left + right)

left + right is constant
// (left + right) is constant
```

Type-use holes remain category-bounded rather than absorbing an ordinary value
expression:

```zax
size of MyType + overhead
// (size of MyType) + overhead
```

#### Phrase selection precedes physical-presentation validation

**Whitespace confirms the selected phrase interpretation; it never selects that
interpretation.**

Given a `Chicken` receiver with `cluck`, `cluck loudly`, and a result of `cluck`
that supplies `loudly`, all of these initially expose the same phrase
interpretations:

```zax
a = chickens cluck loudly

b = chickens cluck \
    loudly

c = chickens cluck  loudly
```

All three diagnose ambiguity when both trees remain viable:

```zax
chickens 'cluck loudly'
(chickens cluck) loudly
```

Invalid presentation must not remove one tree and make ambiguous source silently
valid.

This differs from an uncontinued newline:

```zax
d = chickens cluck
    loudly // error: the newline ended the first statement; layout is invalid
```

The newline is already a hard statement boundary under current source rules.

The next examples deliberately switch to a different candidate set. Suppose now
that `cluck loudly` is the only viable tree: the independent `cluck` followed by
`loudly` interpretation has already been eliminated by receiver, result, or
parameter viability. Only in this non-ambiguous scenario does physical
presentation decide whether the selected phrase is written correctly:

```zax
chickens cluck loudly    // valid
chickens cluck  loudly   // error: phrase-whitespace intent
chickens cluck \
    loudly               // error: phrase component spans physical lines
```

Every selected multiword phrase component must:

- contain exactly one ASCII space between adjacent words;
- remain on one physical line;
- contain no comment, continuation, tab, or other trivia between its words; and
- retain the same exact presentation in natural and fenced source.

A comment does not prune a candidate prematurely:

```zax
chickens cluck /* explanation */ loudly
```

If `cluck loudly` is selected, the comment creates a phrase-presentation intent
error. If `cluck` and `loudly` are selected as separate components, the comment
is outside both components and follows ordinary comment rules.

Physical tabs remain globally invalid under current source structure even when
they occur between separate operations.

A complete mixfix may span physical lines when its surrounding construct has a
valid continuation reason. Each individual phrase component remains on one
physical line.

A formatter must not repair phrase spacing, comments, or continuations until the
source has one selected interpretation. It may offer a presentation repair after
selection, but it must not turn ambiguity into another meaning.

#### Exact phrase fencing and literal coordination

A single-quoted, whitespace-separated form is an exact phrase fence:

```zax
foo 'bar'
'foo' bar
left 'bakes bread' right
```

**A fence helps weed candidates; it does not choose the flower.**

A fence:

- contains one exact contiguous phrase component;
- uses the same lower-case alphanumeric words and exact single spaces as a
  declaration;
- permits no escapes, leading space, or trailing space;
- eliminates identifier, keyword, literal, and differently worded phrase
  interpretations for the fenced words;
- does not add grouping or a precedence boundary;
- does not select an implementation;
- does not choose ordinary operator versus mixfix when both exact components
  remain viable; and
- may be repeated for several components of one mixfix source expression.

Given a post-unary `bar` on `Foo` and a pre-unary `foo` on `Bar`:

```zax
foo : Foo
bar : Bar

a = foo bar   // error: both attachments remain viable
b = foo 'bar' // exact post-unary phrase component
c = 'foo' bar // exact pre-unary phrase component
```

An unknown fenced form produces a phrase-not-found diagnostic. Quoting an exact
component shared by several candidate trees does not resolve the remaining tree
ambiguity:

```zax
high 'shift left through' low 'by' count
```

The fences establish exact components. Complete-tree pruning still determines
whether they form one mixfix or several operations.

Literal source uses a separate attachment distinction:

```zax
"ordinary text" // ordinary unprefixed literal
h'DEADBEEF'     // attached prefixed literal
name'payload'   // attached custom/prefixed literal
foo 'bar'       // separated phrase fence
```

A standalone single-quoted payload is no longer a literal:

```zax
'ordinary text' // phrase fence or phrase diagnostic, not a literal
''              // error: empty phrase fence, not an empty literal
```

Future literal, concatenation, juxtaposition, or implicit-call syntax must not
reinterpret a whitespace-separated single-quoted phrase fence as an expression
operand.

#### Keyword words and keyword-neutral source

Words inside a declared or fenced phrase have phrase roles rather than keyword
roles. Custom phrases may use contextual-keyword spellings unless the exact form
is language-reserved.

Natural source considers every complete keyword and phrase interpretation. The
compiler does not warn merely because a spelling might theoretically be
confused. It diagnoses only actual surviving ambiguity.

A fence requires the phrase role. `bare{...}` may instead neutralize a competing
keyword role.

The `bare{...}` candidate remains strict: neutralize means neutralize. It does
not restore keyword roles merely because they would be useful inside a type use:

```zax
bare{ value as Payload immutable readonly final & }
// error: the qualifier words have been neutralized
```

The programmer may name that complete type use outside:

```zax
MyQualifiedType :: alias type Payload immutable readonly final &

bare{ value as MyQualifiedType }
```

Outside bare source, a type argument consumes the complete type use. It ends
where the next token cannot continue type-use grammar:

```zax
value as Payload is immutable
// (value as Payload) is immutable
```

If a following phrase word can also continue the type use, candidate formation
retains both complete interpretations and diagnoses ambiguity when both survive.

#### Phrase boundaries and `bare{...}`

A phrase component cannot span a source enclosure whose payload must
independently form a complete expression or effective statement. It cannot
obtain phrase words, an operand, a joiner, or a header section from outside.

Using the current illustrative candidate:

```zax
bare{ chickens cluck loudly } outdoors
```

the payload must independently complete before `outdoors` participates. After
that check, the enclosure becomes transparent to final precedence and wider
mixfix matching, so `outdoors` may apply to the completed result.

`bare{...}` does not replace grouping or phrase fencing:

```zax
bare{ a runs b times bare{ jump shark } } // error
```

Nested bare source is unavailable for a more basic reason: inside an outer
enclosure, the inner `bare{` loses its contextual keyword-opener role. It is
seen as neutralized `bare` source beside an invalid `{...}` form, not as another
enclosure.

The no-spanning rule is stable phrase behavior even if future bare-source work
renames or respells the enclosure. Exact nesting, reflection, formatting, and
keyword-neutral behavior remain with future bare-source work.

#### Eagerness, short circuit, protection, and mixfix consumption

Ordinary user-defined phrase operations are eager. Their words do not grant
short-circuit, unsafe, compile-time, or other privileged behavior.

Users cannot declare their own short-circuit evaluation strategy. The language
protects exact Boolean phrase signatures whose evaluation boundary must remain
visible.

The following exact Boolean operations short-circuit and form
mixfix-consumption barriers:

- logical NAND;
- logical AND-NOT;
- logical NOR; and
- logical OR-NOT.

Exact Boolean logical XNOR is eager and mixfix-consumable. Permitted custom
operand implementations of the same language-defined forms are ordinary eager
operations and remain consumable.

Precedence belongs to the form, so an eager custom `logical nor` retains logical
OR precedence even though its evaluation and barrier behavior differ from the
protected exact Boolean signature.

#### Exact logical phrase catalog

The following exact binary forms are aligned:

| Operation | Exact form | Precedence | Exact Boolean evaluation |
| --- | --- | --- | --- |
| Logical NAND | `logical nand` | Logical AND | Skip RHS when LHS is false; result true |
| Logical AND-NOT | `logical and not` | Logical AND | Skip RHS when LHS is false |
| Logical NOR | `logical nor` | Logical OR | Skip RHS when LHS is true; result false |
| Logical OR-NOT | `logical or not` | Logical OR | Skip RHS when LHS is true |
| Logical XNOR | `logical xnor` | Logical XOR | Eager |

```zax
result := left logical nand right
result := left logical and not right
result := left logical nor right
result := left logical or not right
result := left logical xnor right
```

The following exact mutation forms are aligned at assignment precedence and
right association:

```zax
flag logical and assign rhs
flag logical nand assign rhs
flag logical and not assign rhs
flag logical or assign rhs
flag logical nor assign rhs
flag logical or not assign rhs
flag logical xor assign rhs
flag logical xnor assign rhs
```

The receiver/place evaluates once. Exact Boolean AND/OR-derived operations retain
their protected short-circuit behavior; XOR/XNOR remain eager.

#### Exact bitwise phrase catalog

The following exact binary forms are aligned:

| Operation | Exact form | Precedence |
| --- | --- | --- |
| Bitwise NAND | `bitwise nand` | Bitwise AND |
| Bitwise XNOR | `bitwise xnor` | Bitwise XOR |
| Bitwise NOR | `bitwise nor` | Bitwise OR |
| Bitwise OR-NOT | `bitwise or not` | Bitwise OR |

```zax
result := left bitwise nand right
result := left bitwise xnor right
result := left bitwise nor right
result := left bitwise or not right
```

Their exact direct phrase mutation counterparts are:

```zax
value bitwise nand assign rhs
value bitwise xnor assign rhs
value bitwise nor assign rhs
value bitwise or not assign rhs
```

Ordinary AND, OR, XOR, and AND-NOT retain the existing concise symbolic forms
and compounds. No redundant language-defined phrase mutations are introduced
for them.

#### Exact modulo-count shift phrases

The following exact binary forms are aligned at shift/rotate/composition
precedence:

```zax
result := value shift left modulo count
result := value shift right modulo count
result := value logical shift right modulo count
```

`shift right modulo` uses the type-natural signed/unsigned right-shift behavior.
`logical shift right modulo` explicitly zero-fills. All reduce the count modulo
the finite logical bit extent and discard outgoing bits; they are not rotations.

Their exact mutation counterparts are:

```zax
value shift left modulo assign count
value shift right modulo assign count
value logical shift right modulo assign count
```

Mutation forms use assignment precedence and right association.

#### Reserved, language-provided phrase forms

A **reserved phrase form** cannot be declared by user code. A form may be
reserved before every domain detail is complete, but promoted teaching must
state its theoretical prototype, broad behavior, and remaining future work so
later review does not have to rediscover the operation.

The following forms are aligned as reserved, language-provided,
non-overloadable operations at ordinary phrase precedence:

| Exact or candidate form | Fixity and theoretical prototype | Conceptual behavior | Deferred refinement |
| --- | --- | --- | --- |
| `as default` | Post-unary on a type identity or value expression | Produce the default-qualified type identity, or convert a value to a value compatible with that default type | Complete qualifier defaults, transfer, generics, and type-result behavior |
| `type of` | Pre-unary on one independently resolved expression | Return its concrete static type identity without executing or disambiguating it | Alias identity, qualified type identity, reflection representation |
| `size of` | Pre-unary type receiver | Return its byte size in the active execution context | Host/target context, unusual widths, padding |
| `alignment of` | Pre-unary type receiver | Return its required byte alignment in the active execution context | Host/target context and complete layout |
| `offset of` | Binary structural member path and containing type identity | Return the byte offset from the containing type entry point to that member path | Exact member-designator grammar, composition, layout |
| `is constant` | Post-unary expression query | Return whether that expression is available as a compile-time constant | Host/target context and compile-time execution |
| `is final` | Post-unary resolved type-use query | Return the underlying type-use/referent-place final stance, not one path's replacement restriction | Complete qualifier reflection |
| `is immutable` | Post-unary value/type-use query | Return the current value-lifetime mutability stance | Complete qualifier reflection |
| `is readonly` | Post-unary access/type-use query | Return the current access-path capability stance | Complete qualifier reflection |
| Type-information operation | Pre-unary type receiver; illustrative `type information of` | Return immutable, readonly, final metadata about a concrete type identity | Exact words and complete reflection shape |

The exact wording `type information of` remains illustrative rather than an
exact source reservation. The operation concept and exclusive language
ownership are reserved while reflection work selects final words.

The direct property list is intentionally small. Declaration properties such as
`private`, export status, deprecation, and directives belong to future
declaration reflection rather than an expanding `is <property>` family.

`as` and `unsafe as` are language-defined open forms at ordinary phrase
precedence rather than wholly reserved forms:

```zax
converted := source as DestinationType
converted := source unsafe as DestinationType
```

The left value supplies receiver discovery. The right operand is a complete type
argument. Protected intrinsic conversion domains remain language-provided.
User-defined words do not independently grant unsafe authority.

#### Compiler-provided enum phrase baseline

Every enum is an independent type backed by one fundamental intrinsic type. It
owns its own behavior and implementation surface while remaining constrained to
the backing type's instance storage:

- an enum cannot add stored instance members or hidden per-value storage;
- it may add `final` functions, type-receiver operators, and other behavior that
  requires no additional instance storage;
- storage and shape compatibility with the backing type do not grant implicit
  transfer or shared type identity; and
- complete enum validity, members, operation inheritance, and generic generation
  remain future enum work.

Every enum receives four protected language-provided phrase operations.

##### `underlying type`

```zax
UnderlyingType := EnumType underlying type
```

This is a post-unary type-receiver phrase:

```zax
operator type post unary 'underlying type' ...
```

It returns the exact fundamental intrinsic type backing the enum, without
use-site qualifiers and in its default-qualified type form. It does not require
or inspect an enum instance.

##### `underlying value`

```zax
rawValue := enumValue underlying value
```

This is a post-unary instance phrase:

```zax
operator post unary 'underlying value' ...
```

It returns one value of the exact underlying fundamental type containing the
stored backing value unchanged. It performs no semantic conversion or
validation.

##### `as UnderlyingType`

```zax
converted := enumValue as UnderlyingType
```

The enum value supplies receiver discovery. The right operand is the exact
underlying type argument. `as` performs the enum's semantic conversion to that
fundamental type.

For an ordinary restricted enum, semantic conversion and raw backing extraction
will commonly produce the same value. A semantic enum may distinguish them, as
endian enums do below.

##### `EnumType unsafe from rawValue`

```zax
enumValue := EnumType unsafe from rawValue
```

This is a binary type-receiver phrase:

```zax
operator type binary 'unsafe from' ...
```

It accepts a value of the enum's exact underlying fundamental type, preserves
that backing value unchanged, performs no membership, range, or semantic
validation, and establishes the independent enum type. For a restricted enum it
may create a value outside the ordinarily named or valid domain.

`unsafe from` is part of the baseline enum model rather than an endian-specific
operation. A future safe `EnumType from value` facility may validate, return an
optional or reporting result, or remain unavailable according to focused enum
work.

The four exact generated enum signatures are protected and cannot be replaced by
an enum declaration. Universal operations such as `type of`, `as default`, and
the direct qualifier predicates also apply to enum types and values but are not
enum-specific generated operations.

Legacy enum material also proposes `enumValue unsafe as OtherIntrinsicType` and
a two-step conversion through the exact underlying type. Those operations are
not part of the four generated baseline signatures established here. Their
overflow, narrowing, and safety behavior remains deferred to enum and casting
work rather than being silently accepted or discarded.

#### Endian semantic enum families

For every applicable fundamental basic integer type, Zax conceptually supplies
big-endian and little-endian enum versions backed by that same fundamental type.
These are full-domain semantic enums: every backing bit pattern represents an
endian value, so the language-provided closed bitwise operations described below
cannot create an invalid enum representation.

Endian enums:

- are independent nominal enum types rather than proxies;
- have the same fundamental backing type, instance size, alignment, and
  representation extent as that basic type;
- cannot add instance storage;
- require explicit conversion despite compatible storage and shape; and
- add a focused operation surface beyond the four operations every enum receives.

The exact generated enum names and generic mechanism remain future enum and
numeric work. Promoted source may explain the family as if ordinary named enum
types exist without exposing speculative compiler generation.

##### Receiver-correct conversion families

Entry into an enum domain is owned by the destination enum type receiver:

```zax
big := BigEndianU32 from nativeValue
little := LittleEndianU32 from nativeValue

little := LittleEndianU32 from big
big := BigEndianU32 from little
```

The additional safe type-receiver `from` operation:

- encodes native numeric meaning when the source is the exact fundamental basic
  type;
- transcodes numeric meaning when the source is another endian enum with the same
  backing family;
- returns the destination endian enum type; and
- performs no widening or narrowing.

Raw storage adoption uses the baseline enum operation:

```zax
big := BigEndianU32 unsafe from rawBigStorage
little := LittleEndianU32 unsafe from rawLittleStorage
```

No endian-specific raw-adoption phrase is required. The operation preserves the
backing value unchanged and records the programmer's unchecked assertion that
the raw storage carries the claimed endian meaning.

The enum value receiver owns both semantic exit and raw extraction:

```zax
nativeValue := big as U32       // decode numeric meaning
rawBigStorage := big underlying value // exact stored backing value
BackingType := BigEndianU32 underlying type
```

For an endian enum, `as UnderlyingType` and `underlying value` are not required
to agree when the active execution byte order differs from the enum's absolute
order:

- `as UnderlyingType` decodes represented numeric meaning;
- `underlying value` exposes exact stored backing representation.

Direct assignment remains unavailable:

```zax
nativeValue : U32 = big
// error: compatible storage does not grant implicit conversion
```

Earlier candidate forms put a fundamental value in receiver position and would
require an enum-aware operation to be supplied through that fundamental
receiver:

```zax
nativeValue as big endian
nativeValue as little endian
rawValue unsafe as MyEnum
```

Those forms are superseded. This is a deliberate enum ownership rule rather than
a claim that the shared symbolic operator model could never express a global
mixed signature: fundamental types are not given operations that teach them
about independent enum destinations. The corresponding receiver-correct forms
are:

```zax
BigEndianU32 from nativeValue
LittleEndianU32 from nativeValue
MyEnum unsafe from rawValue
```

Earlier source-enum-owned transcoding phrases are receiver-correct but redundant
with the uniform target-oriented `LittleEndianU32 from big` model. Earlier
`from big endian` and `from little endian` decoding phrases are likewise
superseded by the enum-owned semantic `as UnderlyingType` operation.

##### Native, compiler-host, and target endianness

**Native endianness** means the active byte order of the environment whose
execution semantics are currently in effect. It is distinct from:

- **compiler-host endianness**, the byte order of the environment running the
  compiler;
- **target endianness**, the byte order selected for generated target behavior;
- **big endian** and **little endian**, absolute representation orders.

At ordinary target runtime, native endianness is the target execution
environment's byte order. During compile-time execution, native endianness is
the byte order of the environment whose execution semantics are currently
active. Future compile-time-context work must distinguish actual compiler-host
execution from target-context evaluation rather than using `host`, `target`, and
`native` interchangeably.

A native-endian enum alias is useful for generic source but is context-relative.
Exact `native`, `host`, and `target` enum names remain future naming and
compile-time-context work.

##### Endian operation mental model

Programmer-facing endian teaching should lead with:

> For every operation that an endian type supports, behave as though the value
> were being operated on natively by an environment with that byte order.

When an available operation on an endian receiver accepts a native operand of
the exact underlying fundamental type, its observable result is the same as if
the native operand were first converted through `EndianType from nativeValue`
and the operation were then performed. This is an as-if semantic rule, not a
required implementation sequence. An implementation may fuse, elide, or replace
those conceptual steps with any equivalent behavior while preserving result
type, observable value, failures, and documented costs.

The endian value must supply receiver discovery:

```zax
masked := bigValue & nativeMask

nativeMask & bigValue
// unavailable by design: no fundamental-left enum-aware mixed signature
```

Different endian enum operands require explicit target-oriented transcoding
unless a future endian operation establishes another exact mixed signature.

##### Focused endian operation surface

Endian enum families add a deliberately limited set of operations whose meaning
is unambiguous under the mental model:

- same-type construction, copy, move, assignment, destruction, and swap;
- equality and inequality with the same endian enum type or the exact backing
  fundamental type on the right;
- complement on one endian enum value;
- AND, OR, XOR, and AND-NOT with the same endian enum type or the exact backing
  fundamental type on the right;
- language-defined bitwise NAND, NOR, XNOR, and OR-NOT;
- corresponding direct mutation forms;
- population count; and
- reduction AND, OR, XOR/parity, NAND, NOR, and XNOR.

Boolean bitwise operations commute with the fixed endian representation
permutation, so operating on matching encoded backing values produces the same
encoded result as operating natively in that endian environment. Population
count and reductions do not depend on bit position. Basic endian signatures are
language-provided and protected.

For equality, binary Boolean-bitwise operations, and their mutation forms, a
native right operand of the exact backing fundamental type participates through
an exact language-provided mixed signature. This is operation-local mapping, not
implicit assignment or general transfer. Its result follows the same as-if
conversion rule:

```zax
masked := bigValue & nativeMask
// same observable result as:
masked := bigValue & (BigEndianU32 from nativeMask)
```

A raw mask already carrying big-endian representation must instead use the
baseline raw-adoption operation:

```zax
masked := bigValue & (BigEndianU32 unsafe from rawBigMask)
```

The initial endian surface does not include:

- numeric arithmetic or magnitude;
- ordering;
- increment or decrement;
- shifts, rotates, or multiword shift operations;
- leading/trailing counts, masks, or positions;
- bit or byte reversal; or
- masked extraction and deposit.

For example:

```zax
shifted := bigValue << 2
// unavailable in the initial endian operation surface
```

If future enum/endian work adds this operation, the mental model requires a
numeric shift as if executed natively in the enum's endian environment, not an
accidental shift of how backing bytes appear in the active machine. Hidden
conversion cost and the absence of a direct target instruction are design
pressures, not automatic reasons to include or exclude an operation.

##### Storage and shape compatibility pressure

Endian enums and their backing fundamental types are aligned as storage- and
shape-compatible while remaining independent types. Future type-compatibility
work must refine those terms.

Candidate **exact storage compatibility** requires the complete instance storage
to agree in size, alignment, stored-member offsets and extents, required padding,
and representation boundaries from the base address.

Candidate **shape compatibility** additionally requires corresponding
storage-bearing members to use the same names and recursively compatible
storage. Zero-instance-storage declarations such as `final` functions do not
change instance shape.

Compatibility describes structural facts; a conversion rule grants transfer.
Neither storage nor shape compatibility permits:

```zax
nativeValue : U32 = big
```

Destination/source truncation should not silently weaken exact symmetric storage
compatibility. Future work should distinguish exact compatibility from a
directional storage-prefix projection or explicit truncating transfer.

Complete enum member validity, operation independence/inheritance, safe
conversion, generic generation, non-byte-multiple widths, padding, ABI, and
compatibility contracts remain future work.

#### Count, set-bit mask, and set-bit position families

The following symbolic meanings are aligned and supersede the current catalog's
leading/trailing-one count meanings for `#~<` and `#~>`:

| Form | Operation | Result |
| --- | --- | --- |
| `#<bits` | Leading-zero count | Canonical unsigned bit-count type |
| `#>bits` | Trailing-zero count | Canonical unsigned bit-count type |
| `#~<bits` | Leading set-bit mask | Same type and logical extent as operand |
| `#~>bits` | Trailing set-bit mask | Same type and logical extent as operand |

The mask forms preserve exactly one set bit at its original position:

- `#~<bits` preserves the most-significant set bit;
- `#~>bits` preserves the least-significant set bit; and
- zero produces zero.

Dedicated leading/trailing-one counts remain derivable:

```zax
leadingOnes := #<(~bits)
trailingOnes := #>(~bits)
```

The following exact post-unary phrase forms return optional positions:

```zax
leadingPosition := bits leading set bit position
trailingPosition := bits trailing set bit position
```

Their forms are:

```zax
operator post unary 'leading set bit position' ...
operator post unary 'trailing set bit position' ...
```

Position zero is the least-significant bit. For nonzero `bits` of logical width
`W`:

```text
leading position  = W - 1 - leading-zero count
trailing position = trailing-zero count

leading mask  = 1 << leading position
trailing mask = 1 << trailing position
```

Zero has no set-bit position, so each phrase returns an optional canonical
bit-count value:

```zax
position := bits trailing set bit position

if ?position
  shifted := x << position.
```

The optional payload uses the same canonical unsigned bit-count type as the
zero-count forms. That type must be directly viable as the count operand of every
applicable built-in shift without an explicit conversion. Future numeric work
may establish its exact name and representation.

A contrasting example makes the three result families visible:

```zax
bits : U8 = 0b01010100

leadingZeros := #<bits  // 1
trailingZeros := #>bits // 2

leadingMask := #~<bits  // 0b01000000
trailingMask := #~>bits // 0b00000100

leadingPosition := bits leading set bit position   // present: 6
trailingPosition := bits trailing set bit position // present: 2
```

For zero:

```zax
bits : U8 = 0

leadingZeros := #<bits  // 8
trailingZeros := #>bits // 8
leadingMask := #~<bits  // 0
trailingMask := #~>bits // 0

leadingPosition := bits leading set bit position   // absent
trailingPosition := bits trailing set bit position // absent
```

Mask results retain the operand's signed type. Preserving a signed sign-bit mask
is a bit-pattern operation and does not overflow or panic.

All six operations require finite defined logical bit extent. Direct position
forms remain useful despite derivability because they express optional zero
handling and permit target-specific optimization without promising a particular
instruction or lowering.

#### Saturating magnitude

Saturating magnitude retains the asymmetric symbolic family form:

```zax
bare{ ||value| }
```

Natural `||value|` remains a confusable-form intent error. The bare-source
candidate acknowledges the intended recognized form. Zax does not add a
redundant canonical phrase version merely to avoid the source-intent mechanism.

#### Accepted operation concepts with exact phrases deferred

The following remain cataloged language operation concepts while exact words,
complete numeric constraints, and some result shapes remain future work:

- multiword logical left and right shift;
- multiword arithmetic right shift;
- multiword explicit zero-fill right shift;
- multiword rotate left and right;
- funnel shift left and right;
- writable multiword forms involving several component places;
- bit reversal;
- byte reversal;
- masked extraction; and
- masked deposit.

Multiword operations are mixfix because they consume several value holes and
phrase components. They use shift/rotate/composition precedence and have
language-provided basic-integer forms. Existing illustrative source remains
evidence:

```zax
high shift left through low by count
high rotate left through low by count
result funnel right from high and low by count
```

Exact spellings stay deferred because arithmetic versus logical right shift,
receiver orientation, high/low ordering, result shape, multi-place mutation,
signedness, and aliasing affect the theoretical prototypes.

Bit and byte reversal are distinct finite-extent basic-integer concepts. Byte
reversal preserves bit order within each byte; bit reversal does not. Exact
phrases remain deferred.

Masked extraction packs every source bit selected by a mask into contiguous
low-order positions. Masked deposit scatters contiguous low-order input bits
into positions selected by a mask. Exact phrases, mask-width behavior, zero
fill, and unsupported-target costs remain future numeric work.

No useful finding may remain meaningful only through an archived numbered work
item. These concepts, examples, distinctions, and activation pressures require
live owners or indexed raw destinations before `009` closes.

#### Legacy phrase evidence preserved without current reservation

The following legacy operation concepts and spellings remain future evidence
without reserving their exact forms now:

- `count of`, whose type and variadic meanings differ;
- `outer of` and `unsafe outer of`;
- `lifetime of` and `unsafe lifetime of`;
- `unsafe copy as`;
- `overhead count of`;
- `overhead as`;
- `overhead size of`; and
- `allocator of`.

Their pointer, lifetime, allocation, function-representation, and variadic
semantics are not established by phrase work. Reserving broadly useful word
sequences before those domains establish a theoretical prototype would constrain
the open phrase facility without enough evidence.

The stray legacy spelling:

```zax
value cast DestinationType
```

is superseded by `as` in the same legacy casting material and must not be
reintroduced through promotion.

#### Corrected `final` and `varying` model

Review exposed a material conflict in the current qualifier and declaration
owners. The aligned model has two distinct `final`/`varying` layers:

| Location | Meaning |
| --- | --- |
| Type-use side | Whether the underlying place/type shape is actually `final` or `varying` |
| Declaration-name side | Whether this declaration or access path may exercise replacement authority |

This parallels the distinction between a mutable value and a readonly access
path.

The teachable model distinguishes underlying capability from authority through
one path:

| Kind of change | Underlying capability | Permission through this path |
| --- | --- | --- |
| Mutate the current value lifetime's contents | `mutable` | `writable` |
| Replace the entire value lifetime in place | type-side `varying` | `writable` plus declaration-side `varying` |

The restrictive counterparts are:

| Concern | Underlying restriction | Path restriction |
| --- | --- | --- |
| Content mutation | `immutable` | `readonly` |
| Whole-value replacement | type-side `final` | declaration-side `final` |

`readonly` and `writable` appear in the qualified type use, but semantically
describe the current access path rather than changing the nominal base type.
`readonly` is broad: it removes content-mutation and whole-value replacement
authority through that path. A second declaration-name `readonly` would repeat
the same path fact.

Declaration-side `final` is narrower. It removes only whole-value replacement
authority while preserving writable content mutation when the value is mutable:

```zax
source varying : MyType mutable varying

reader : MyType mutable readonly varying & = source
restricted final : MyType mutable writable varying & = source

reader.member = replacement     // error: readonly path
restricted.member = replacement // legal: mutable value and writable path
restricted = makeMyType()       // error: declaration-side final
```

This is why `final`/`varying` needs both type-use and declaration-name positions
while `readonly`/`writable` does not. The promoted qualifier owner must lead with
this capability-versus-permission model and concrete contrasting examples rather
than presenting only mechanical qualifier placement.

```zax
myType final : MyType varying
```

means:

- the underlying type/place is varying;
- an authorized path may replace its value lifetime in place;
- this declaration promises not to exercise that replacement authority; and
- the declaration may still mutate contents when the value is mutable and the
  path is writable.

A restricted same-place view preserves the underlying truth while narrowing
replacement authority through that declaration:

```zax
myType varying : MyType varying

myAliasType final : MyType varying & = myType
```

`myType` retains replacement access. `myAliasType` cannot initiate replacement,
but its type use still records that another path may replace the referent.

This matters for immutable varying values:

```zax
value varying : MyType immutable varying

observer final :
  MyType immutable readonly varying & = value

replacer varying :
  MyType immutable writable varying & = value

restricted final :
  MyType immutable writable varying & = value

replacer = makeMyType()   // reconstructive replacement
restricted = makeMyType() // error: this declaration lacks replacement authority
```

The underlying place can undergo compiler-recognized reconstructive replacement,
ending one immutable lifetime and beginning another. `observer` and `restricted`
must understand that `replacer` may do so. `restricted` remains writable for
operations that do not require whole-value replacement, but its declaration-side
`final` prevents it from initiating reconstructive replacement.

Effective whole-value replacement through one path requires:

- underlying type/place stance `varying`;
- declaration/access replacement permission `varying`;
- writable access;
- a viable replacement operation; and
- for reconstructive replacement, the applicable immutable-lifetime conditions.

Declaration-side replacement permission must survive aliases, argument mapping,
results, and captures. A declaration-final path cannot regain replacement
authority merely by being supplied to a callable whose referent type remains
varying. Exact prototype presentation belongs to qualifier and invocation
promotion.

The qualifier combinations align as:

```zax
name final : Type             // type-side omission resolves final for new storage
name final : Type varying     // legal: restrict access to varying storage
name varying : Type varying   // legal: retain replacement access
name final : Type final       // legal
name varying : Type final     // error: access cannot exceed underlying capability
```

These rows describe replacement permission through the declared path versus the
underlying type-use/referent-place stance. For a same-place alias, omitted
type-side truth inherits the actual referent stance rather than changing it.
Declaration-side `final` may narrow replacement access from varying to final but
may not widen final storage to varying.

The aligned model intentionally changes the current meaning of name-side
`final` on a pointer or reference declaration. Current owners use it for the
pointer/reference binding's own independent replacement place and allow a
name-side-final writable reference to reconstruct a varying referent. Under the
aligned model above, name-side `final` restricts whole-value replacement through
that declaration. Exact future syntax for independently replacing or rebinding
the pointer/reference binding itself must be recovered by pointer and reference
work rather than silently retaining both meanings.

`is final` inspects the resolved type-use/referent-place truth:

```zax
restricted final : MyType varying

restricted is final // false
```

It does not report whether this declaration may exercise replacement. That
access fact belongs to future access or declaration reflection.

Current qualifier and declaration owners conflict with the aligned model in two
different ways:

1. For direct storage, they collapse name-side and type-side `final`/`varying`
   into one boundary and reject `name final : Type varying`.
2. For references, they already distinguish the reference binding's place from
   the referent place, but name-side `final` qualifies the binding and does not
   restrict replacement through a writable varying referent. Their current
   `replacer final : Type immutable writable varying &` example performs
   reconstructive replacement.

Promotion must repair both rules coherently, replace the current reference
example with the access-restricted model above, and preserve the displaced
pointer/reference rebinding question for focused future work. This is not an
optional documentation clarification.

#### Type identity, expression facts, and reflection pressure

Every successfully selected single expression result has a concrete static type,
even when the type is anonymous, intermediate, structural, or known only to the
compiler. Phrase pruning and enclosing receiver discovery use that real type.

`type of` returns the selected expression's concrete static type identity. It
does not execute the expression merely to discover its type and does not:

- choose among result-only overloads;
- choose between phrase trees;
- create a type for zero or multiple results; or
- attach expression-specific facts to canonical type identity.

For example:

```zax
compileValue := 42
runtimeValue := readInteger()
```

Both may have type `Integer`, while only `compileValue` is constant. Constancy is
an expression-evaluation fact, not part of `Integer` identity. `is constant`
therefore remains a direct expression query.

Several distinct result slots each have a type but do not collectively form one
expression type. A future explicit combiner would itself return one typed
structure.

Future reflection does not require unrelated metadata result types for every
subject. One immutable, readonly, final metadata structure may contain optional
type-, expression-, declaration-, or access-specific sections. Canonical type
identity must remain independent of which expression produced it.

Current reservation pressure is limited to:

- exact `type of`;
- a type-information operation whose exact words remain open;
- future declaration reflection whose exact form remains open; and
- the four direct predicates `is constant`, `is final`, `is immutable`, and
  `is readonly`.

There is no current reservation for a general `expression information of` or
`access information of` phrase.

`as default` has two reserved shapes:

```zax
MyType as default   // default-qualified concrete type identity
expression as default // value compatible with the expression's default type
```

The value form is conceptually equivalent to:

```zax
expression as ((type of expression) as default)
```

Complete type-result, default-qualification, generic, and transfer behavior
remains future work. The mechanically determined reserved type result does not
authorize arbitrary user-defined runtime-dependent type-result functions.

#### Terms required by promotion

Promotion should add or refine these human-facing terms where they earn
independent reuse:

- **operator phrase** - an exact word-spelled operator form;
- **phrase word** - one canonical lower-case alphanumeric word in a form;
- **phrase component** - one contiguous exact word sequence participating as an
  operator or mixfix component;
- **phrase fence** - explicit local source requiring one exact phrase component
  without grouping or selecting an implementation;
- **ordinary phrase precedence** - the shared level for newly introduced phrase
  forms;
- **language-provided operation** - behavior supplied by the language rather
  than a user body;
- **reserved phrase form** - an exact form user code cannot declare;
- **unavailable reserved form** - a held form whose usable domain is not yet
  established;
- **protected intrinsic signature** - the existing narrower declaration
  ownership rule for exact closed-intrinsic operand shapes;
- **type parameter slot** - a prototype input completed by a concrete type
  identity;
- **type argument** - the concrete type identity supplied to that slot; and
- **type-receiver operator** - an operation discovered through a concrete type
  identity rather than an instance;
- **native endianness** - the active byte order of the environment whose
  execution semantics are currently in effect, distinct from compiler-host and
  build-target endianness; and
- **endian semantic enum** - a distinct enum backed by a fundamental integer
  type and carrying an absolute or context-relative representation meaning.

Future enum and type-compatibility work should also evaluate reusable terms for:

- exact underlying type;
- underlying value;
- unsafe enum adoption;
- exact storage compatibility;
- shape compatibility;
- storage-prefix projection; and
- truncating transfer.

Parameter and argument remain distinct:

- a parameter is a slot declared by a prototype;
- an argument is caller source supplied to complete a parameter; and
- an operator operand may supply an argument to one selected parameter.

Do not introduce a term merely to summarize several findings when ordinary
prose remains clearer.

#### Costs, diagnostics, tooling, and source stability

Open phrase forms add real compile-time and tooling cost:

- candidate-tree formation may retain several word extents and attachments;
- receiver and outward-result pruning may be needed before one tree remains;
- source additions can turn unique source into ambiguity;
- tooling must retain physical trivia until after selection; and
- phrase/mixfix interaction may require several exact components to be shown.

Fixed finite forms, one ordinary precedence level, receiver ownership, no global
custom implementations, outward-only result flow, and immediate ambiguity
diagnostics bound those costs without closing the facility.

Diagnostics must distinguish:

- unknown fenced phrase;
- unrecognized natural phrase;
- exact phrase found at an unsupported fixity;
- incomplete phrase or unattached source;
- several viable phrase trees;
- ambiguity among implementations of one tree;
- private phrase form ineligible in the caller's context;
- eligible uniquely best implementation that is unavailable;
- reserved form declaration;
- phrase-word spelling violation;
- phrase-whitespace presentation error;
- phrase component spanning a physical line;
- keyword-role conflict;
- type-use/phrase boundary ambiguity;
- zero or multiple results where one expression result is required;
- protected intrinsic declaration conflict; and
- short-circuit/barrier behavior where relevant.

A formatter:

- may not add or remove a phrase fence;
- may not add or remove `bare{...}`;
- may not repair physical phrase presentation before unique selection;
- may normalize one selected phrase to its exact single-space presentation; and
- must preserve grouping and source intent.

Source reflection and documentation must retain enough information to reproduce
explicit phrase fences and bare-source acknowledgement even when neither creates
a final expression-tree node. Complete source-reflection representation remains
future work.

Adding, removing, lengthening, reserving, or changing the precedence of a phrase
form is a source-compatibility event. It may make existing source ambiguous or
invalid but must never silently reinterpret one viable tree through source,
declaration, import, module, or discovery order.

#### Promotion and future-input obligations

The eventual documentation-fit dry run must map the aligned findings to all
affected owners. At minimum:

| Concern | Likely lasting owner or destination |
| --- | --- |
| Shared phrase operation, discovery, results, protection, and availability | `language/operators.md` |
| Exact phrase forms, precedence, reserved/protected domains, and compact endian/bit reference tables | `language/operator-catalog.md` |
| Phrase components, result-outward matching, and barriers | `language/mixfix-operators.md` |
| Phrase presentation, fences, keyword roles, continuation, and hard boundaries | `language/source-structure.md` |
| Receiver ownership, visibility, type parameters, and corrected declaration-side replacement access | `language/declarations-and-bindings.md` |
| Corrected type-side versus declaration-side `final`/`varying` behavior | `language/qualifiers.md` |
| Parameter/argument mapping, single/multiple results, and expected-result limits | `language/function-invocation.md` |
| Reusable phrase, reservation, and type-receiver vocabulary | `language/terms.md` |
| Endian enum mental model, conversions, operation surface, execution context, examples, and costs | A separate current endian teaching owner proposed by the dry run |
| Bare nesting, strict neutralization, and transparent no-spanning behavior | `project/raw/bare-source.md` until focused bare work |
| Literal prefixes, exact single-quote fencing, and future juxtaposition constraints | `project/raw/literal-operators.md` until focused literal work |
| External owner-authorized phrase extensions and import coherence | `project/raw/partial-types.md` until focused partial-type work |
| Finite extent, reversal, extraction/deposit, and bit-count type | `project/raw/numeric-type-families.md` until focused numeric work |
| Pointer/reference binding replacement after name-side `final` becomes path replacement permission | `project/raw/lifetimes.md` until focused pointer/reference work |
| Fundamental-backed enum independence, generated operations, validity, inheritance, and zero-storage behavior | `project/raw/numeric-type-families.md` initially; the dry run may propose a dedicated indexed enum raw destination that absorbs and retires this enum portion coherently |
| Storage/shape compatibility, compatibility versus conversion, and directional projection/truncation | `project/raw/structural-typing.md` until focused type-compatibility work |

The operator catalog should retain exact forms, fixity, precedence, protection,
result families, and a compact availability route. It should link to rather than
duplicate the separate endian owner. The endian owner should teach semantic enum
families, native/compiler-host/target context, conversion directions, the
focused operation surface, costs, and future boundaries without becoming the
complete enum or numeric owner.

No current indexed raw file cleanly owns general compile-time execution,
generics, computed type results, type-receiver generic identity, or complete
reflection. The dry run must propose one or more indexed raw destinations rather
than leaving those findings meaningful only through archived `009`.

The following deferrals are defensible and constrain current promotion:

| Deferred finding | Activation pressure | Constraint on current phrase work |
| --- | --- | --- |
| General computed or runtime-dependent type results | Generic, compile-time type-computation, or reflection work | Only mechanically determined reserved type results and value results dependent on concrete type arguments are assumed |
| Generic and alias type receivers | Generic or alias reflection work | Non-generic enclosing type identity is sufficient now |
| Compile-time execution availability and failure | Compile-time execution work | Type-receiver phrases are not inherently compile-time |
| Exact reflection phrase words and metadata shape | Reflection work | Reserve operation concepts without pretending illustrative words are final |
| Host/target constant and layout queries | Compile-time-context work | Preserve contextual variants without completing them here |
| Fundamental-backed enum operation inheritance and independence | Enum work currently routed through `project/raw/numeric-type-families.md` | Preserve the four generated enum operations and focused endian surface without inferring that other enums inherit backing operations |
| Endian enum generation, naming, and non-byte-multiple representation | Enum, numeric, and representation work | Preserve full-domain semantic enum types, same fundamental backing, and explicit receiver-correct conversion |
| Native/compiler-host/target endian context | Compile-time execution and build-target work | `native` always means the active execution environment rather than an unspecified build machine |
| Safe restricted-enum creation and validation result | Enum work | Baseline enums provide `unsafe from`; safe `EnumType from value` is not assumed universally |
| Complete endian arithmetic, ordering, shifts, and position-sensitive bit operations | Enum and endian work | Initial endian support is limited to the aligned conversion, lifecycle, equality, Boolean-bitwise, population-count, and reduction surface |
| Enum and backing-type validity under unsafe adoption | Enum work | `unsafe from` preserves backing value without membership or range validation |
| Legacy enum `unsafe as` conversion to non-underlying intrinsic types | Enum and casting work | The four generated enum operations do not decide narrowing, overflow, or two-step conversion behavior |
| Exact storage compatibility versus directional prefix/truncating transfer | Type-compatibility work currently routed through `project/raw/structural-typing.md` | Compatibility never grants implicit conversion or silently discards storage |
| Exact multiword shift/rotate/funnel words and writable aliasing | Multiword numeric work | Preserve every distinct operation concept and illustrative source |
| Exact bit/byte reversal and extraction/deposit words | Numeric bit-operation work | Preserve concepts, finite-extent constraints, and behavioral distinction |
| Canonical unsigned bit-count type name | Numeric type work | Counts and optional positions share one shift-compatible count type |
| Complete `offset of` member-designator grammar | Layout/reflection work | Treat it as reserved structural source, not an ordinary evaluated left value |
| Complete custom literal realization | Literal work | Standalone single quotes remain phrase fencing; literal prefixes stay attached |
| External partial-type phrase declarations | Partial-type/import work | A module cannot add natural phrases to another owner's type today |
| Bare-source final spelling and reflection | Bare/source work | No phrase spans an independently complete enclosure; nested bare is unavailable |
| Independent pointer/reference binding replacement or rebinding syntax | Pointer, reference, or lifetime work | Name-side `final` now restricts replacement through the declaration and cannot silently retain its old binding-place meaning |

This update does not promote any finding, edit a lasting owner or raw input,
perform the documentation-fit dry run, or authorize later integration.

### Supersession boundary

The remainder of the working record is the staged initial reconstruction. It is
preserved as evidence and as the maintainer's before/after comparison boundary.
Its inventories and source citations remain useful, but every conflicting model,
recommendation, decision request, or deferral is superseded by the current
aligned findings above.

### How to read this record

Everything below is candidate reconstruction, raw evidence, an open question, or
an explicit deferral. Nothing here is aligned or accepted. Where a statement
merely reports the status of material already present in a current owner, it
says so explicitly.

The record is ordered for review rather than for discovery:

1. the candidate programmer model;
2. the deciding contradiction and the next-ranked uncertainties;
3. worked Zax source, including counterexamples;
4. the phrase inventory and its disposition;
5. detailed recognition, declaration, and interaction analysis;
6. known holes that still need refinement;
7. explicit decisions requested from the language maintainer;
8. adjacent findings deliberately deferred; and
9. the consequence-driven reading performed and why.

### 1. Candidate programmer model

Plain language: an **operator phrase** is an ordinary operator whose recognized
source form is written with words instead of punctuation. It is a spelling
decision layered on the operator model that
[Zax operators](../../../language/operators.md) already owns, not a second dispatch
mechanism.

```zax
sum := left + right                 // symbolic binary operator
mask := flags bitwise nand bits     // candidate phrase binary operator
```

The candidate model in five statements:

1. **A phrase is a form, not a new kind of operation.** Discovery, operand
   mapping, viability, partial-order preference, unavailable-best behavior,
   once-only operand evaluation, protected intrinsic signatures, and
   mixfix-consumption barriers apply unchanged. This follows directly from the
   current operator owner; nothing in the phrase evidence argues for a separate
   selection path.
2. **Language-defined phrases are recognized in ordinary source.** They need no
   marker, fence, or enclosure. Their words, fixity, arity, precedence, and
   associativity are fixed by the language exactly as symbolic forms are.
3. **A phrase family inherits the precedence level of the symbolic family it
   extends.** The current
   [operator catalog](../../../language/operator-catalog.md#precedence-and-grouping)
   precedence table already reserves rows for future phrase families at the
   bitwise AND/XOR/OR, logical AND/XOR/OR, and shift levels. That is the
   strongest precedence evidence in the corpus and the candidate model adopts
   it. Families with no symbolic sibling (multiword shift-through, funnel
   shifts) require an explicitly assigned level.
4. **Phrase words are recognized only in operator position.** They are not
   reserved words, do not enter the ordinary identifier namespace, and never
   shadow an identifier of the same spelling. This matches
   [declarations and bindings](../../../language/declarations-and-bindings.md#assignment-and-overload-selection):
   "operators are not ordinary identifiers." Where a word sequence yields two
   complete interpretations, the candidate answer is an
   incomparable-interpretation diagnostic rather than a preference rule,
   because
   [principles](../../../language/principles.md) forbid guessing among plausible
   meanings.
5. **Phrase extent is closed by the declared word sequence, not by whitespace.**
   Words are always whitespace-separated, so the symbolic technique of
   presenting fixity through adjacency (`!value`, `value++`, `left + right`) is
   unavailable. Recognition is longest-declared-word-sequence at the operator
   position; fixity comes from the declaration.

Candidate fixity table, using illustrative spellings only:

| Form | Candidate source | Receiver operand |
| --- | --- | --- |
| Pre-unary phrase | `size of Integer` | Sole operand (see the hole problem in section 2) |
| Post-unary phrase | `value as big endian` | Sole operand |
| Binary phrase | `a logical nand b` | Left operand |
| Mutating binary phrase | `flag logical and assign rhs` | Left destination place |
| Phrase component in a mixfix | `high shift left through low by count` | Declared receiver anchor |
| Circumfix phrase | No live evidence proposes one | Not reconstructed |

Two source mechanisms are proposed by live raw material and are frequently
discussed as competing alternatives. The candidate reconstruction is that they
solve **different** problems and are not substitutes:

- `bare{...}` ([raw bare source](../../raw/bare-source.md)) neutralizes non-operator
  keyword roles and acknowledges recognized confusable forms. It is transparent
  to the final tree, so it cannot bound a phrase's extent or attachment.
- A local phrase fence such as the doubled-quote candidate
  ([raw literal input](../../raw/literal-operators.md)) makes phrase extent and
  attachment visible without grouping the surrounding expression. It says
  nothing about keyword roles beyond the fenced words.

If the phrase catalog is closed and language-defined, neither mechanism is
required for phrases at all; both would then be justified, or not, by their own
separate pressures.

### 2. The deciding contradiction

**Several of the most strongly evidenced phrases do not take expression
operands.** This is the single most important issue for maintainer review,
because it decides whether a "phrase" is an operator at all.

Live evidence from the legacy corpus:

```zax
// Legacy evidence spellings; not current Zax syntax.
u8 := 255 as U8                             // right operand is a type
byRef := myType as AnotherCompatibleType &  // type use plus a reference marker
reset := value as default                   // right operand is a keyword
bytes := size of Integer                    // sole operand is a type
wide := utf8String unsafe as WideString     // two-word phrase, type operand
```

The current owners define an operand hole as a position that "resolves as a
complete ordinary expression"
([mixfix operators](../../../language/mixfix-operators.md#trees-components-and-holes),
[terms](../../../language/terms.md#operand-hole)). A type use is not an expression,
`&` after a type is a type-use marker rather than a binary operator, and
`default` is a keyword rather than a value. So at least one of the following
must be true, and each has large consequences:

| Candidate resolution | Consequence |
| --- | --- |
| The phrase catalog admits typed holes, so each phrase declares the *kind* of each hole (value, type use, or nothing) | Phrase declarations, viability, preference, and diagnostics all grow a hole-kind dimension the current operator model does not have |
| `as`/`unsafe as` and the sizing family are not operator phrases but separate grammar owned by casting and by compile-time/reflection work | The phrase catalog shrinks dramatically, most legacy "phrase" evidence leaves this work item, and `unsafe as` keyword interaction becomes a casting concern |
| Types become ordinary expression values through future reflection (`type of`) | Depends on unreviewed reflection and meta-type behavior; cannot be assumed here |

Legacy casting evidence already shows the strain. The destination type is
smuggled into an operator declaration as the *type of a discarded parameter*:

```zax
// Legacy evidence; not current Zax syntax.
operator binary 'as' final :
  (result : IncompatibleType)(# : IncompatibleType) readonly = { }
```

That declaration selects on the destination type rather than on a supplied
value, which is closer to
[narrow expected-result selection](../../../language/function-invocation.md#narrow-expected-result-selection)
than to ordinary binary-operand selection.

Two further consequences make this contradiction urgent rather than cosmetic:

- **It breaks keyword neutralization.** `bare{...}` is specified to neutralize
  non-operator keyword roles. `value as default` requires the keyword `default`
  to keep a role *inside* an operator phrase, so neutralization must be
  scoped by grammatical role and position, never by word.
- **It collides with type-use qualifier words.** Qualifier words already
  follow a type on the type-use side
  ([qualifiers](../../../language/qualifiers.md#canonical-ordering)):

```zax
view final unpliable : Payload immutable readonly final & = source

// Candidate hazard: where does a post-unary phrase attach after a type use?
encoded := value as Payload from big endian // open question: is `from big
                                            // endian` part of the type use or a
                                            // post-unary phrase on the result?
```

#### Next-ranked uncertainties

2. **Custom phrases versus import stability.** If a custom phrase is recognized
   in ordinary source, adding an import can change how previously valid source
   parses. The catalog states that changing tokenization would reinterpret
   existing source and is therefore not routine library evolution. Either custom
   phrases are fenced at every use, are module-local and non-exported, or do not
   exist. This is decision D2/D3.
3. **Fixity, overlap, and identifier collision.** Whitespace cannot present
   phrase fixity, so overlapping declarations and adjacent independent unary
   phrases need rules that source order may not resolve.
4. **The doubled-quote fence collides with literals** in a way that appears
   fatal (section 5.9).
5. **Three operation concepts named by the fixed initiating input have no
   evidence in current owners or live raw material**: bit and byte reversal,
   masked extraction and deposit, and first/last set-bit scans. The current
   operator catalog records no such entries; only
   [raw numeric type families](../../raw/numeric-type-families.md) mentions reversal,
   and only as an operation requiring a finite logical bit extent. They are
   treated here as unverified concepts pending maintainer confirmation (D7)
   rather than as accepted catalog concepts.

### 3. Worked source

All source in this section is candidate or raw. Invalid forms are marked.

#### 3.1 Ordinary use

```zax
// Candidate natural source for language-defined phrases.
combined := flags bitwise nand mask
enabled := ready logical nand ?resource
shifted := value shift left modulo count
flag logical and assign otherFlag
```

#### 3.2 Precedence follows the extended symbolic family

```zax
// Candidate: `bitwise nand` sits at the bitwise AND level, so it binds
// tighter than `|` and looser than `+`.
value := a + b bitwise nand mask | tail
// candidate tree: ((a + b) bitwise nand mask) | tail
```

Legacy compile-time evidence requires a pre-unary phrase to bind tighter than a
relational operator, which the candidate model preserves:

```zax
// Legacy evidence; the phrase must group before `>`.
if size of Integer > size of U32 {
}
// required reading: (size of Integer) > (size of U32)
```

#### 3.3 Mutation phrases sit at the assignment level

```zax
flag logical and assign a logical or b
// candidate: flag <logical-and-assign> (a logical or b)
// The mutation phrase is right-associative at the assignment level, so the
// trailing `assign` word changes the precedence of the whole phrase.
```

This is the clearest reason recognition cannot commit to a precedence level
before the complete word sequence is known.

#### 3.4 Adjacent independent phrase applications require grouping

The mixfix owner already states this for phrase components; the candidate model
extends the accepted symbolic rule unchanged.

```zax
result := run value fast   // error: adjacent independent phrase applications
result := run (value fast) // candidate: explicit grouping states the intent
```

#### 3.5 Longest phrase and common prefixes

```zax
shifted := value shift left modulo count
fused   := high shift left through low by count
// Both begin `shift left`. Recognition may not commit at the shared prefix and
// then fail; it matches the longest declared word sequence at that position.
```

```zax
bytes     := size of Integer
hostBytes := host size of Integer
// `size of` is a suffix of `host size of`. Matching begins at the operator
// position, so the longer form wins there without making `host` reserved.
```

#### 3.6 Phrase words are not reserved

```zax
is := 1                    // candidate: legal; phrase words are not reserved
ok := value is constant    // `is constant` recognized in operator position
bad := is constant         // error: `is` here names the binding and `constant`
                           // has no operator role in operand position
```

#### 3.7 Whitespace, continuation, and layout

An incomplete phrase does not continue a statement, matching the accepted rule
that a dangling `+` does not continue one
([source structure](../../../language/source-structure.md#symbolic-operators-whitespace-and-adjacency)):

```zax
result := value shift left \
  modulo count

result := value shift left
  modulo count // error: an incomplete phrase does not continue the statement
```

#### 3.8 `bare{...}` cannot bound a phrase

```zax
// Candidate: bare source is transparent and cannot borrow operands.
result := bare{ run fast } value // error: incomplete payload for this position
result := bare{ run fast value } // candidate: the whole expression is enclosed
```

This is the structural reason `bare{...}` does not answer the phrase-extent or
phrase-attachment question, and therefore does not compete with a local fence.

#### 3.9 Local fencing collides with literal evidence

```zax
x := value''fast''
// error (candidate): also reads as `value`, an empty literal, `fast`, and a
// second empty literal, given legacy empty-literal and literal-prefix evidence
roman := roman'IX'                       // legacy literal prefix evidence
text  := a'normal c-style\t-string'      // legacy literal prefix evidence
```

#### 3.10 Candidate declarations

```zax
// Candidate: the phrase spelling occupies the same quoted slot that symbolic
// forms already use in current illustrative declarations.
operator binary 'bitwise nand' final :
  (result : Bits)(lhs : Bits, rhs : Bits) = {
}

Vector :: type {
  operator pre unary 'normalized' final :
    (result : Vector)() readonly = {
  }
}
```

Legacy evidence uses a different shape that places the phrase spelling in the
ordinary identifier position:

```zax
// Legacy evidence; not current Zax syntax.
run to a store with :: forward operator pre unary
TypeName.shake up :: forward operator pre unary
```

#### 3.11 A phrase may remove the only concrete need for `bare{...}`

The catalog already records that a saturating-magnitude phrase remains an
alternative to the gated asymmetric circumfix.

```zax
saturated := bare{ ||value| }               // raw candidate, gated form
saturated := saturating magnitude of value  // candidate phrase alternative
```

If the phrase alternative is accepted, the only concrete confusable form in the
corpus disappears and `bare{...}` retains only the keyword-role motivation.

### 4. Phrase inventory and disposition

Maturity labels: **accepted concept** means the operation concept is recorded in
a current owner (the spelling is not); **candidate spelling** means illustrative
words appear in a current owner; **raw** means live non-authoritative input;
**legacy** means an unreviewed root page; **unverified** means named by the
fixed initiating input without supporting evidence in current or live
material.

#### 4.1 Accepted operation concepts with deferred spellings

Source: [operator catalog](../../../language/operator-catalog.md) and
[operators](../../../language/operators.md).

| Operation concept | Candidate spelling in the catalog | Status and candidate disposition |
| --- | --- | --- |
| Logical NAND | `a logical nand b` | Accepted concept; short-circuit for exact Boolean; mixfix barrier |
| Logical AND-NOT | `a logical and not b` | Accepted concept; short-circuit; barrier |
| Logical NOR | `a logical nor b` | Accepted concept; short-circuit; barrier |
| Logical OR-NOT | `a logical or not b` | Accepted concept; short-circuit; barrier |
| Logical XNOR | `a logical xnor b` | Accepted concept; eager; consumable |
| Logical mutation family | `flag logical and assign rhs` and its nand/and-not/or/nor/or-not/xor/xnor forms | Accepted concepts; candidate assignment-level precedence, right-associative, destination evaluated once |
| Bitwise NAND / NOR / XNOR / OR-NOT | `a bitwise nand b` and siblings | Accepted concepts; eager; candidate precedence at the matching bitwise level |
| Bitwise phrase mutation counterparts | Not spelled in the catalog | Accepted concepts; spelling and precedence open |
| Saturating magnitude | `bare{ \|\|value\| }` or a future phrase | Accepted concept; candidate phrase spelling may retire the confusable form |
| Modulo-count shifts | `value shift left modulo count`, `value shift right modulo count`, `value logical shift right modulo count` | Accepted concepts; candidate shift-level precedence; explicitly not rotations |

#### 4.2 Multiword mixfix operations

Source: [mixfix operators](../../../language/mixfix-operators.md#multiword-bit-operations).

| Operation concept | Candidate spelling | Status and candidate disposition |
| --- | --- | --- |
| Multiword logical left/right shift | `high shift left through low by count` | Accepted concept; phrase components inside a mixfix skeleton; needs an explicit precedence level |
| Multiword arithmetic right shift | Not separately spelled | Accepted concept; spelling open |
| Multiword explicit zero-fill right shift | Not separately spelled | Accepted concept; spelling open |
| Multiword rotate left/right | `high rotate left through low by count` | Accepted concept; wraps across the combined extent |
| Funnel shift left/right | `result funnel right from high and low by count` | Accepted concept; returns one fixed-width window |
| Writable two-place forms | Not spelled | Accepted concept; mutation of one versus both components remains open in its owner |

#### 4.3 Conversion, layout, reflection, and lifetime evidence

Source: [legacy basics phrase list](../../../basics.md#remaining-historical-operator-phrase-input),
[legacy casting](../../../casting.md), and legacy compile-time pages.

| Evidence spelling | Legacy classification | Candidate disposition |
| --- | --- | --- |
| `as` | Binary safe conversion | Blocked on D1; type-use operand. Complete conversion semantics stay with casting |
| `unsafe as` | Binary unsafe conversion | Blocked on D1; also a keyword-role and `unsafe`-prefix collision case |
| `as default` | Qualifier reset | Blocked on D1; keyword operand |
| `cast` (in `myType cast IncompatibleType`) | Stray legacy spelling in a casting example | Superseded by `as` in the same page; recorded so the inconsistency is not re-imported |
| `as big endian`, `as little endian`, `from big endian`, `from little endian` | Post-unary encoding conversion | Deferred to casting/representation work; words reserved-but-unassigned; post-unary attachment after a type use is an open hazard |
| `outer of`, `unsafe outer of` | Binary container recovery | Deferred to pointer/lifetime work |
| `lifetime of`, `unsafe lifetime of` | Shared lifetime binding | Deferred to lifetime work |
| `unsafe copy as` | Unsafe function-pointer copy cast | Deferred to interop/lifetime work |
| `size of`, `alignment of`, `offset of` | Sizing and layout | Blocked on D1; type-use operand; host/target context already owned by compile-time material |
| `type of` | Meta-data retrieval | Deferred to reflection/meta work; may be the mechanism that turns a type into an expression value |
| `count of` (variadic and type forms) | Pre-unary count | Deferred; two distinct operations share one spelling in the legacy list |
| `overhead count of`, `overhead as`, `overhead size of`, `allocator of` | Pointer/allocator introspection | Deferred to pointer, allocation, and lifetime work |
| `is constant`, `is host constant`, `is target constant` | Post-unary compile-time predicate | Deferred to compile-time/reflection work; the only clear post-unary phrase evidence with a value operand |
| `host …` / `target …` variants of the sizing family | Context-specific sizing | Deferred; the context default is already described by compile-time material and must not be redecided here |
| `size of :input` | Legacy compile-time example spelling | Unexplained; the leading `:` resembles explicit positional intent rather than a phrase word. Recorded as a hole |

#### 4.4 Custom and open-phrase evidence

Source: [legacy forward declarations](../../../forward.md) and
[raw bare source](../../raw/bare-source.md).

| Evidence spelling | Legacy classification | Candidate disposition |
| --- | --- | --- |
| `run to a store with` | Custom pre-unary phrase | Raw evidence that arbitrary multiword custom phrases were once intended; blocked on D2/D3 |
| `jump farther` | Custom post-unary phrase | Same |
| `connect with` | Custom binary phrase | Same |
| `TypeName.shake up` | Type-defined custom pre-unary phrase | Same, plus a qualified-path-with-spaces problem (section 5.7) |
| `roman` (`roman'IX'`) | `forward operator literal` | Not a phrase; literal-prefix operator. Belongs to literal work and is the direct source of the fence collision |
| `run`, `run fast`, `run fast from` | Overlapping custom phrases | Raw longest-match and common-prefix evidence |
| `run value fast`, `runs fast` | Attachment and fence examples | Raw evidence for the grouping and fence questions |

#### 4.5 Phrase-like forms that are not operator phrases

These constrain the available word space and the recognition rules even though
they are not operators. Recording them prevents a phrase catalog from colliding
with accepted grammar.

| Form | What it actually is | Constraint it imposes |
| --- | --- | --- |
| `unsafe pliable`, `unpliable` | Accepted multiword qualifier on the declaration-name side | Proves multiword contextual recognition already exists in accepted Zax, and puts `unsafe` in two roles |
| `replacement +++` | Contextual lifecycle declaration | A word plus punctuation that is not an operator; not consumable by a user mixfix |
| `forward operator pre unary`, `alias type`, `each … from`, `yield suspend`, `redo while` | Legacy compound keywords | Multiword keyword sequences already exist; keyword-role conflicts are therefore not hypothetical |
| `unsafe <category> { … }` | Raw future categorized unsafe region | Word-plus-block pattern that must be co-designed with `bare{...}` |
| `next outer:`, `continue outer:` | Contextual transfer keywords with targets | Accepted precedent that position, not a reserved list, decides keyword status |

#### 4.6 Concepts named by the fixed initiating input without live evidence

| Named concept | Status |
| --- | --- |
| Bit and byte reversal | Unverified; reversal appears only in raw numeric material as a finite-extent requirement |
| Masked extraction and deposit | Unverified; no live evidence found |
| First/last set-bit scans | Unverified; the catalog records leading/trailing zero and one counts as symbolic `#<`, `#>`, `#~<`, `#~>` forms instead |

### 5. Detailed analysis

#### 5.1 Words, holes, and extent

A phrase declaration fixes an ordered word sequence and the positions of its
holes. Candidate constraints:

- every hole is a complete expression, subject to D1;
- a hole may not be empty, so two adjacent phrase words are always literal words
  and never an implicit hole;
- the phrase's first word begins at an operator position, and a pre-unary
  phrase's first word begins at an operand position; and
- a phrase never spans a statement boundary, a header separator, or a grouping
  delimiter, because those are not ordinary operator components.

Open: whether interior words may be optional, and whether a phrase may end with
a hole *and* be followed by another phrase word belonging to an enclosing
phrase. The multiword shift evidence (`high shift left through low by count`)
already has three holes and four interior words, so this is not hypothetical.

#### 5.2 Fixity and arity

Candidate: fixity and arity come from the declaration, never from whitespace.
Consequences:

- the same word sequence may not be declared at two fixities, because no source
  evidence could distinguish them and source order may not resolve ambiguity;
- pre-unary and post-unary phrases sharing an operand require grouping
  (section 3.4); and
- a phrase used at a position its declared fixity does not allow is an
  unsupported-fixity diagnostic, which the current operator diagnostics list
  already anticipates.

#### 5.3 Precedence, associativity, and grouping

Candidate rules:

- a phrase extending an existing symbolic family takes that family's level and
  associativity, as the catalog's reserved rows already state;
- a mutation phrase takes the assignment level and right-associativity;
- a phrase family with no symbolic sibling requires an explicitly assigned
  level, and the multiword shift/rotate/funnel family is the concrete case;
- no type, namespace, or import may assign a different level to a phrase; and
- parentheses remain the only grouping mechanism.

Open: whether a phrase and its symbolic sibling at the same level may chain
without grouping, and whether the relational/equality grouping-required rule
extends to any phrase comparison spellings.

#### 5.4 Natural versus fenced source

Candidate: language-defined phrases are natural, requiring no marker. The
argument is readability and the accepted principle that ordinary code should
remain readable; a permanently fenced built-in operation would be worse than the
symbolic form it complements.

Custom phrases are the opposite case. Making them natural means the meaning of
ordinary source depends on the transitive import graph, which contradicts the
catalog's source-stability position on tokenization. The candidate answer is
that custom phrases, if they exist at all, are visibly marked at every use.

#### 5.5 Keyword, identifier, and phrase recognition

Candidate recognition order at a given position:

1. grammar-recognized construct openings (declaration, flow header, block);
2. a contextual keyword whose construct is grammatically permitted here;
3. the longest declared phrase word sequence recognized at this position; then
4. an ordinary identifier.

Where 3 and 4 both produce complete, well-formed interpretations of the same
source, the candidate answer is a diagnostic naming both readings, not a
preference. This preserves "preference must be defensible, not guessed" and
avoids making a new import silently re-point a name.

`bare{...}` neutralizes step 2 only. It must not neutralize step 3, because
`unsafe as` and `as default` are phrase spellings built from keyword words.

#### 5.6 Longest phrase and overlapping declarations

Candidate: longest match at the operator position within one fixity. Overlapping
declarations across fixities, or two equally long viable matches at the same
position, are ambiguity errors. A longer skeleton does not automatically win in
the mixfix owner, and the same discipline is adopted here.

Open: whether a failed longest match may back off to a shorter match. Backing
off would make adding a longer phrase change existing source silently; refusing
to back off makes adding a longer phrase break existing source loudly. The
candidate preference is the loud failure, consistent with how the catalog treats
tokenization changes.

#### 5.7 Declarations, forwards, receivers, visibility, imports, and namespaces

- **Declaration shape.** Candidate: quote the phrase spelling in the same slot
  that symbolic forms use (section 3.10). The legacy
  `run to a store with :: forward operator pre unary` shape places a phrase in
  the ordinary identifier namespace, which conflicts with the accepted statement
  that operators are not ordinary identifiers, and with mandatory
  naming-intent casing that a phrase spelling cannot satisfy.
- **Forward.** Candidate: `forward` for a phrase states the spelling and fixity
  only, matching the legacy intent that a forward categorizes a symbol without
  defining qualifiers. Whether a phrase must be forward-declared before its
  first use is open, and it matters more for phrases than for symbols because
  recognition itself depends on the declaration.
- **Type-defined phrases.** Candidate: the receiver rules in the operator owner
  apply unchanged: sole operand for unary, left operand for binary. The legacy
  `TypeName.shake up` qualified path has an embedded space and should instead be
  declared inside the type body.
- **Operations with no value operand.** `size of Integer` has no receiver, so it
  cannot be receiver-discovered and must be language-provided or global. This is
  another form of the D1 problem.
- **Imports and namespaces.** Open and consequential. Because a phrase changes
  how source is *read*, a namespace-qualified phrase use is hard to spell and an
  unqualified one makes recognition depend on visibility. No current owner
  supplies a rule. This is the strongest argument for D2's closed catalog.
- **Global mixfix parity.** Global mixfix declarations are not permitted today.
  Whether a phrase component inside a mixfix inherits that restriction is open.

#### 5.8 `bare{...}` assessment

Accurately reported from raw material: the candidate enclosure is non-scoping,
creates no expression node, precedence boundary, or mixfix hole, requires one
complete expression or effective statement, neutralizes non-operator keyword
roles, and permits recognized confusable forms.

Candidate findings from this reconstruction:

- it cannot bound phrase extent or attachment (section 3.8), so it is not a
  phrase mechanism;
- its keyword neutralization must be role-scoped, not word-scoped (section 5.5);
- the contiguous `bare{` opener is deliberately the one `{` that lacks preceding
  whitespace, which is otherwise a layout-intent error. That makes the
  distinction learnable but demands a precise diagnostic for `bare {`;
- multiline payload lines should be validated as expression continuation rather
  than as a structural level, because the enclosure creates no scope. The
  closing `}` level rule is unspecified and is a hole; and
- if the saturating-magnitude phrase is accepted, `bare{...}` loses its only
  concrete confusable-form use in the corpus.

#### 5.9 Local phrase fencing and literal collision

The doubled-single-quote candidate appears unusable as written. Given legacy
evidence for empty single-quoted literals, literal prefixes (`roman'IX'`,
`w'hello'`, `a'…'`), and payload escaping, `value''fast''` has a complete
alternative reading as an identifier, an empty literal, an identifier, and a
second empty literal. Resolving this by prohibiting empty literals would decide
literal grammar from inside phrase work, which this item does not own.

Candidate alternatives to evaluate, none of them accepted:

- a non-quote delimiter that does not collide with literal lexing;
- reusing the declaration's quoted spelling only at declaration sites and
  requiring qualified custom-phrase calls to use ordinary call syntax instead;
  or
- rejecting the custom-phrase facility, which removes the need for a fence.

#### 5.10 Protection, eagerness, short circuit, and mixfix barriers

Reported from current owners, not decided here: exact Boolean phrase NAND,
AND-NOT, NOR, and OR-NOT are short-circuit and are mixfix-consumption barriers;
exact Boolean XNOR is eager and consumable; mixed custom operand shapes are
ordinary eager operations. Phrase signatures whose every operand is a closed
intrinsic value are protected intrinsic signatures under the existing rule, so a
user cannot claim `Boolean logical nand Boolean`.

Candidate consequence to review: two visually identical phrase uses may have
different consumption and evaluation behavior based solely on operand types.

```zax
gate := ready logical nand ?resource   // protected, short-circuit, barrier
mixed := customFlags logical nand mask // eager custom overload; consumable
```

Diagnostics must therefore report evaluation mode and barrier status per use,
not per spelling. The operator and mixfix cost lists already require exposing
eager-versus-short-circuit behavior and phrase origin, so this is a refinement
rather than a new obligation.

#### 5.11 Costs, diagnostics, reflection, formatting, and source stability

Already accepted in current owners: phrase origin is part of the discoverable
operator origin set, and phrase forms appear in the catalog's cost list.

Candidate additions this work should carry into promotion:

- diagnostics distinguishing an unrecognized word sequence, an incomplete phrase
  at a statement boundary, overlapping or equal-length phrase matches, an
  identifier/phrase incomparable interpretation, a fixity conflict, a
  keyword-role conflict, and a missing required fence;
- a formatter must not add or remove `bare{...}` or a phrase fence, by the same
  reasoning that forbids adding or removing parentheses at a multiple-result
  boundary;
- source reflection must be able to represent tokens whose enclosure is absent
  from the final tree; and
- adding, renaming, or lengthening a phrase is a source-compatibility event of
  the same class as changing tokenization.

### 6. Known holes needing refinement

1. Hole kinds: value, type use, or keyword operands, which block most of
   section 4.3.
2. Whether a phrase may contain optional or repeated words.
3. Backing off from a failed longest match (section 5.6).
4. Phrase visibility, qualification, and import behavior (section 5.7).
5. Whether phrase components may anchor or be consumed by mixfix skeletons on
   the same terms as symbolic components, including global-declaration parity.
6. Precedence levels for families with no symbolic sibling.
7. Whether any phrase family needs the grouping-required comparison rule.
8. `bare{...}` closing-brace level, nesting, and documentation attachment.
9. A fence spelling that survives literal lexing, if custom phrases survive.
10. The legacy `size of :input` spelling.
11. Whether `count of` names one operation or two.
12. How a phrase interacts with the `??`/`;;` separators and flow headers when
    its words would otherwise straddle a header section.

### 7. Decisions requested from the language maintainer

| ID | Decision | Candidate recommendation | Why it matters |
| --- | --- | --- | --- |
| D1 | Are `as`, `unsafe as`, and the sizing family operator phrases, or separate grammar owned by casting and compile-time work? | Treat them as separate grammar; keep the phrase catalog to value-operand operations | Decides the hole model, declaration shape, and most of the legacy inventory |
| D2 | Closed language phrase catalog, a bounded custom facility, or both? | Closed catalog now; revisit custom phrases only under concrete pressure | Decides whether ordinary source can be parsed without the import graph |
| D3 | If custom phrases exist, must every use be visibly marked? | Yes | Prevents an import from reinterpreting existing source |
| D4 | Is the doubled-single-quote fence viable? | No; it collides with empty literals and literal prefixes | Avoids deciding literal grammar from phrase work |
| D5 | Should saturating magnitude get a phrase spelling instead of the gated `\|\|value\|` form? | Yes, evaluate the phrase first | May remove the only concrete confusable-form use of `bare{...}` |
| D6 | Do phrase families inherit the precedence of the symbolic family they extend? | Yes, matching the catalog's reserved rows | Fixes precedence without inventing a new table |
| D7 | Confirm or drop bit/byte reversal, masked extraction/deposit, and set-bit scans | Confirm provenance before reserving words | The fixed initiating input and the catalog disagree |
| D8 | Phrase declaration shape: quoted spelling in the operator declaration, or the legacy `name :: forward operator …` form | Quoted spelling | Keeps phrases out of the ordinary identifier namespace |

### 8. Deferred adjacent findings

| Finding | Why it can wait | Destination | Activation pressure | Constraint it places on this work |
| --- | --- | --- | --- | --- |
| Complete `as`/`unsafe as` conversion, overload disabling, and generated reference casts | Conversion semantics are a separate accepted boundary of this item | Casting owner and future cast work | D1, or any focused casting review | Phrase grammar must either reserve a type-use hole or exclude these forms |
| Empty literals, literal prefixes, payload escaping, and custom literal operators | Literal grammar is explicitly outside this item | [Raw literal input](../../raw/literal-operators.md) | Literal work, or any fence proposal using quote characters | No phrase fence may use `'` until empty-literal behavior is decided |
| Host/target sizing, `type of`, and compile-time context defaults | Already described by legacy compile-time material and owned by future reflection work | Future reflection/meta and compiler-directive owners | Reflection or meta-programming work | The sizing word space stays reserved-but-unassigned |
| Pointer, allocator, overhead, and lifetime phrases | Their semantics depend on unreviewed lifetime design | Pointer, allocation, and [raw lifetime input](../../raw/lifetimes.md) | Lifetime or allocator work | Their words stay reserved-but-unassigned |
| Indexing, slicing, and range interaction with phrase words | No concrete collision surfaced from the required corpus | [Raw indexing and slicing](../../raw/indexing-and-slicing.md) | Indexing work, or a phrase proposal using words inside index grammar | A phrase may not silently become part of index or range grammar |
| Function composition and chaining alternatives to `\|>` and `>>` | Not required to resolve phrase recognition | [Raw function composition and chaining](../../raw/function-composition-and-chaining.md) | Composition, chaining, or capture work | A future chaining phrase must not reopen precedence decided here |
| Categorized unsafe source, `unsafe <category> { … }`, and non-scoping permission regions | The unsafe boundary is future work | [Raw analysis controls](../../raw/analysis-controls.md) and [raw safety](../../raw/safety.md) | Unsafe-boundary design | `bare{...}` opener style and `unsafe`-prefixed phrases must be co-designed so the two do not look alike |
| Owner-authorized extension of another type with phrase or mixfix declarations | Depends on partial-type coherence rules | [Raw partial types](../../raw/partial-types.md) | Partial-type or extension work | Phrase receivers inherit the current no-global-mixfix restriction until decided |
| Finite logical bit extent for reversal-style operations | Numeric families are outside this item | [Raw numeric type families](../../raw/numeric-type-families.md) | Numeric or bit-extent work | Any reversal phrase must require a finite defined extent |
| Source reflection and formatting of transparent or fenced source | Depends on whether either mechanism survives | [Raw bare source](../../raw/bare-source.md) until phrase work concludes | Source-reflection or formatter work | Formatters may not add or remove either mechanism |

### 9. Consequence-driven reading performed

| Material read | Concrete trigger from the required corpus | What it changed |
| --- | --- | --- |
| Focused [casting](../../../casting.md): the `as`/`unsafe as` intrinsic section and the `as` overloading and disabling sections | The catalog defers `as` and `unsafe as` to phrase work, the legacy basics list classifies them as binary phrases, and `forward.md` shows phrase forwards beside them | Produced the deciding contradiction in section 2: these phrases take type uses and a keyword as operands, and their legacy declaration encodes the destination type as a discarded parameter |
| Focused [compiler directives](../../../compiler-directives.md): host/target sizing context passages and their examples | The legacy basics list defines `size of` and its host/target variants as phrases and gives them compile-time context meaning | Supplied the only precedence evidence for a pre-unary phrase, `size of Integer > size of U32`, and the `size of :input` and `is host constant` spellings |
| Focused [raw analysis controls](../../raw/analysis-controls.md): the permission-region section | [Raw bare source](../../raw/bare-source.md) states that a future categorized unsafe enclosure may share a contiguous visual pattern, and the initiating input includes that interaction | Confirmed a real word-plus-block collision, `unsafe <category> { … }`, and the precedent that such a region should not silently create a scope |
| Focused [raw safety](../../raw/safety.md): the unsafe-mechanism paragraph | Same trigger as above | Confirmed that no `unsafe { … }` spelling is accepted, so `bare{...}` does not yet conflict with an accepted form |
| Focused [raw numeric type families](../../raw/numeric-type-families.md): the finite-extent paragraph | Section 4.6 required checking whether reversal-style concepts exist in live material | Established that reversal appears only as a finite-extent requirement and that masked extraction/deposit and set-bit scans have no live evidence |
| Focused [qualifiers](../../../language/qualifiers.md) canonical-ordering and `unsafe pliable` sections, within the required reading | The `unsafe as` phrase evidence collides with an accepted multiword qualifier | Established that multiword contextual recognition already exists in accepted Zax and produced the type-use attachment hazard in section 2 |

[Raw function composition and chaining](../../raw/function-composition-and-chaining.md)
and [raw indexing and slicing](../../raw/indexing-and-slicing.md) were **not** read.
No concrete consequence in the required corpus forced them; both are recorded as
deferrals in section 8 instead.

## Dispositions and promotion dry run

### Result: PASS

The read-only pre-promotion documentation-fit dry run passes.

**Pass means that no further language-design alignment is required to produce a
coherent promotion.** Every aligned finding has a plausible lasting owner or an
explicit live/raw destination, the proposed structure can teach the concepts
without duplicate authority, and the exact promotion change set is identifiable.

Pass does not promote any finding, authorize repository edits, approve the exact
future wording, archive this work item, or authorize creation of work item `010`.
Promotion remains a separately discussed, aligned, and explicitly authorized
act.

The dry run began and ended with only this work item staged and no unstaged
changes. It performed no repository edit.

### Readiness judgment

The operator-phrase model is ready for promotion:

- general and phrase-specific fixity;
- fixed finite phrase forms;
- open receiver-owned custom phrases;
- candidate-tree formation and result-outward pruning;
- private eligibility;
- natural, grouped, fenced, and keyword-neutral source;
- post-selection physical-presentation validation;
- ordinary and language-defined precedence;
- eager, protected, short-circuit, and mixfix-barrier behavior;
- exact logical, bitwise, mutation, shift, count, mask, and position forms;
- reserved conversion, layout, reflection, and qualifier-query concepts;
- enum baseline phrases and endian semantic enum behavior;
- costs, diagnostics, formatting, reflection pressure, and source stability.

The following remain deliberately deferred rather than blocking promotion:

- exact reflection phrase words and metadata shape;
- complete enum design and generic generation;
- complete compile-time execution behavior;
- generic and alias type receivers;
- general computed type results;
- exact multiword shift/rotate/funnel words;
- exact reversal and masked extraction/deposit words;
- canonical bit-count type name;
- complete casting, layout, compatibility, pointer, lifetime, allocator, and
  literal realization behavior.

### Programmer-facing structure proposal

A post-promotion architecture review found that distributing phrase teaching
across the general operator, declaration, and source owners made the programmer
reconstruct one cohesive feature from several documents. The dry-run result
remains **PASS**, with the corrected structure below.

Retain the shared operator owner set and add one dedicated phrase concept owner:

1. [Zax operators](../../../language/operators.md) owns the general operator model,
   fixity, declarations, receiver discovery, candidate-tree formation, result
   flow, selection, visibility interaction, evaluation, and protection.
2. [Zax operator phrases](../../../language/operator-phrases.md) owns the cohesive
   human-facing phrase feature: exact finite words, natural source, phrase
   fixity and declarations, receiver ownership, candidate interpretation,
   grouping, fencing, phrase-specific presentation, keyword roles, visibility
   examples, costs, diagnostics, and source stability.
3. [Zax operator catalog](../../../language/operator-catalog.md) owns exact symbolic,
   circumfix, and phrase forms; the single precedence table; compact built-in,
   protected, reserved, and availability reference material.
4. [Zax mixfix operators](../../../language/mixfix-operators.md) owns phrase
   components inside complete mixfix skeletons, holes, receiver anchors, and
   barriers while applying rather than redefining the shared candidate-tree
   model.
5. [Zax source structure](../../../language/source-structure.md) owns the general
   tokenization, comment, continuation, contextual-keyword, physical-line, and
   hard-boundary mechanics applied by the phrase owner.

Do not split a separate phrase **catalog** from the operator catalog. One
cohesive catalog preserves one authoritative precedence table and one lookup
surface. That catalog decision does not preclude, and is now paired with, the
dedicated phrase **concept owner** above.

Create one current human-facing endian owner:

```text
language/endianness.md
```

That document should own:

- the endian semantic enum mental model;
- absolute, native, compiler-host, and target endianness;
- the four compiler-provided enum phrase operations used by endian values;
- receiver-correct encode, transcode, decode, adoption, and extraction;
- full-domain endian enum behavior;
- the focused endian equality, Boolean-bitwise, population-count, reduction, and
  lifecycle surface;
- native right-operand as-if semantics;
- unavailable arithmetic, ordering, shift, and position-sensitive operations;
- storage/shape compatibility without implicit transfer;
- costs, diagnostics, and representative storage/serialization examples.

It should not own complete enum, generic, numeric, ABI, compatibility, or
compiler-lowering behavior. Names such as `BigEndianU32` may be used as explicitly
illustrative generated type names until generic naming is reviewed.

Update the public [website entry point](../../../index.md) to route to both operator
phrases and endianness from the starting path and current-conceptual-design list.
The root repository `README.md` requires no change because it already routes
public readers through `index.md`.

### Lasting ownership map

| Finding cluster | Lasting owner |
| --- | --- |
| General operator forms and first-class fixity table | `language/operators.md` |
| Cohesive phrase mental model, exact finite words, natural/grouped/fenced source, phrase declarations and fixity, receiver ownership, candidate teaching, visibility examples, presentation, keyword roles, enclosure boundaries, eager/protected interaction, costs and diagnostics | new `language/operator-phrases.md` |
| Shared candidate-tree algorithm, outward result flow, selection and general protection | `language/operators.md` |
| Exact phrase forms, ordinary phrase precedence, special family levels, catalogs and reservations | `language/operator-catalog.md` |
| Exact `#~<`/`#~>` mask meanings and optional set-bit-position phrases | `language/operator-catalog.md` |
| Compact enum/endian operator reference | `language/operator-catalog.md` |
| Phrase components and protected phrase barriers inside mixfix skeletons | `language/mixfix-operators.md` |
| General tokenization, comment, continuation, physical-line, contextual-keyword and hard-boundary mechanics used by phrases | `language/source-structure.md` |
| General type parameters, type receivers, receiver ownership and bounded private eligibility as applied by phrase declarations | `language/declarations-and-bindings.md` |
| Type-side truth versus declaration-side replacement permission | `language/qualifiers.md` |
| Reconstructive replacement requiring declaration-side permission | `language/construction-and-destruction.md` |
| Parameter/argument distinction, type-argument mapping, result arity and expected-result limits | `language/function-invocation.md` |
| Exact logical phrase references in flow teaching | `language/core-flow-control.md` |
| Reusable phrase and native-endianness vocabulary | `language/terms.md` |
| Endian semantic enum teaching and operation behavior | new `language/endianness.md` |
| Public routing to current endian teaching | `index.md` |

The declaration owner should establish only the bounded private rule needed by
current behavior: a private type member is eligible only from the owning type's
permitted private context. Complete nested-type, friendship, module, reflection,
and other private behavior remains future visibility work.

The qualifier correction is a required coordinated promotion, not a quiet rider:

- direct storage no longer collapses name-side and type-side `final`/`varying`;
- declaration-side `final` may restrict replacement through a varying type use;
- `name varying : Type final` remains unavailable;
- current reference examples that allow a name-side-final writable alias to
  reconstruct a varying referent must change;
- independent pointer/reference binding replacement or rebinding moves to live
  lifetime input; and
- construction teaching must require declaration-side replacement permission in
  addition to immutable, writable, and type-side varying.

### Reusable terminology disposition

Promote or refine these cross-cutting terms in `language/terms.md`:

1. operator phrase;
2. phrase word;
3. phrase component;
4. phrase fence;
5. reserved phrase form;
6. type parameter slot;
7. type argument;
8. type-receiver operator; and
9. native endianness.

Keep ordinary phrase precedence, endian semantic enum, unavailable reserved form,
and language-provided operation local to their owning explanations unless later
reuse proves a global term necessary. `protected intrinsic signature` already has
a current definition.

### Existing raw destinations

Update these indexed raw files during promotion:

| Raw file | Material it must retain |
| --- | --- |
| [Bare source](../../raw/bare-source.md) | Strict neutralization, unavailable nesting, phrase no-spanning behavior, post-completeness transparency, formatting and source reflection |
| [Literal operators](../../raw/literal-operators.md) | Attached literal prefixes, double-quoted unprefixed literals, single-quoted phrase fences, rejected doubled fence, future juxtaposition constraint |
| [Partial types](../../raw/partial-types.md) | External phrase extensions, intrinsic-left/custom-right workaround, protected signatures, future language reservation conflicts |
| [Numeric type families](../../raw/numeric-type-families.md) | Finite extent, canonical count type, reversal, extraction/deposit, multiword numeric behavior, numeric interaction with enums |
| [Structural typing](../../raw/structural-typing.md) | Exact storage compatibility, shape compatibility, compatibility versus conversion, directional storage projection and truncating transfer |
| [Lifetimes](../../raw/lifetimes.md) | Displaced pointer/reference binding replacement and future independent rebinding syntax |
| [Feature catalog](../../raw/feature-catalog.md) | Routing of enum, generic, compile-time and reflection entries into focused destinations |

### New indexed raw destinations

The dry run recommends four focused placeholders rather than one broad
future-semantics file.

#### `project/raw/enum-types.md`

Preserve:

- fundamental backing and independent enum identity;
- no added instance storage and permitted zero-storage behavior;
- generated `underlying type`, `underlying value`, `as UnderlyingType`, and
  `unsafe from`;
- safe `EnumType from value` pressure;
- restricted versus full-domain validity;
- operation independence or inheritance;
- endian enum motivation; and
- legacy enum `unsafe as` conversion evidence.

It should absorb the enum-specific inheritance material currently in
`numeric-type-families.md`; that file should retain only numeric interactions and
link to the enum input. Retire the placeholder when complete enum behavior moves
to a current enum owner.

#### `project/raw/type-parameters-and-generics.md`

Preserve:

- `ParameterType : type`;
- generic type arguments and constraints;
- dependent value result types;
- computed type results;
- generic and alias type receivers; and
- generated enum families.

Activate when generic/type-parameter behavior is reviewed. Retire after accepted
behavior moves to generic, declaration, invocation, type, and reflection owners.

#### `project/raw/compile-time-execution.md`

Preserve:

- directed and inferred compile-time execution;
- native/compiler-host/target execution context;
- `is constant`;
- type-receiver execution; and
- compile-time availability and failure.

Activate during compile-time execution or host/target-context work. Retire after
accepted behavior moves to compile-time, invocation, diagnostics, and applicable
domain owners.

#### `project/raw/reflection.md`

Preserve:

- `type of`;
- type-information wording and metadata shape;
- alias versus canonical identity;
- declaration reflection;
- unified immutable metadata with optional subject-specific facts; and
- source reflection of phrase fences and transparent bare-source acknowledgement.

Activate during reflection, metadata, source reflection, or tooling work. Retire
after accepted behavior moves to reflection and the domain owners whose facts it
exposes.

Add all four to `project/raw/README.md` with their activation and retirement
rules.

### Legacy and raw disposition

| Existing file | Promotion disposition |
| --- | --- |
| `basics.md` | Mark phrase inventory disposition; route exact forms to current owners; preserve deferred pointer/lifetime/allocator evidence in raw; replace or retire unprefixed single-quoted literal examples |
| `forward.md` | Mark phrase-forward syntax superseded; preserve literal-forward evidence in literal raw input; retain unrelated namespace/type forward evidence |
| `casting.md` | Replace the discarded-parameter type-argument explanation; route complete casting behavior as future work; change unprefixed single-quoted examples to double quotes; disposition enum conversion evidence |
| `enums.md` | Route generated enum phrase forms to the catalog, endian behavior to `language/endianness.md`, and complete enum behavior to enum raw input; preserve non-underlying `unsafe as` evidence as deferred |
| `compiler-directives.md` | Route native/compiler-host/target and compile-time execution pressure to focused raw input while preserving sizing examples |
| `meta-functions.md` | Route type parameters, generics and compile-time execution to focused raw inputs without rewriting complete legacy generic syntax |
| `meta-types.md` | Route type parameters, generated families and compile-time type behavior to focused raw inputs |
| `language/core-flow-control.md` | Replace stale statements that exact logical phrases remain future work |

The single-quote decision requires coordinated migration. Live legacy
unprefixed-single-quote source occurs in `basics.md` and `casting.md`; updating
only raw literal input would leave public legacy pages teaching conflicting
syntax.

No whole legacy page moves or retires during promotion. Useful legacy evidence
remains until its complete page has been dispositioned by value.

### Exact proposed promotion change set

#### Current owner and router files

1. `language/operators.md`
2. new `language/operator-phrases.md`
3. `language/operator-catalog.md`
4. `language/mixfix-operators.md`
5. `language/source-structure.md`
6. `language/declarations-and-bindings.md`
7. `language/qualifiers.md`
8. `language/function-invocation.md`
9. `language/construction-and-destruction.md`
10. `language/core-flow-control.md`
11. `language/terms.md`
12. new `language/endianness.md`
13. `index.md`

#### Existing raw files

14. `project/raw/README.md`
15. `project/raw/bare-source.md`
16. `project/raw/literal-operators.md`
17. `project/raw/partial-types.md`
18. `project/raw/numeric-type-families.md`
19. `project/raw/structural-typing.md`
20. `project/raw/lifetimes.md`
21. `project/raw/feature-catalog.md`

#### New indexed raw files

22. `project/raw/enum-types.md`
23. `project/raw/type-parameters-and-generics.md`
24. `project/raw/compile-time-execution.md`
25. `project/raw/reflection.md`

#### Legacy pages

26. `basics.md`
27. `forward.md`
28. `casting.md`
29. `enums.md`
30. `compiler-directives.md`
31. `meta-functions.md`
32. `meta-types.md`

No change is proposed to `README.md`, `project/README.md`, project operating
prompts, archived work, or implementation repositories. No file move or deletion
is proposed during promotion.

### Promotion validation plan

After an authorized promotion:

- verify one owner per concern and accurate owner-document headers;
- verify the dedicated phrase owner teaches the complete phrase feature while
  general owners retain only shared mechanisms and local impact;
- verify the single precedence table and every exact phrase form;
- test logical, bitwise, mutation, modulo-shift, set-bit, enum and endian examples;
- search for stale "phrase spelling remains future" statements;
- search for old `#~<`/`#~>` leading/trailing-one meanings;
- search for superseded fundamental-receiver enum/endian conversions;
- search for live unprefixed single-quoted source literals;
- verify private-only declarations cannot affect public callers;
- verify qualifier examples distinguish underlying truth from path permission;
- verify reconstructive replacement requires declaration-side permission;
- verify exact catalog links to rather than duplicates endian teaching;
- verify every new raw file is indexed and every relocation updates its prior raw
  owner;
- validate Markdown tables, fences, links, anchors, rendering and navigation;
- confirm current owners do not cite this work item or raw files for meaning;
- confirm the final changed-file set matches the authorized scope; and
- preserve staged and unstaged review boundaries.

### Remaining alignment before promotion

None is required for the dry run to pass.

The structure and scope conclusions above apply prior alignments:

- bounded private eligibility belongs in declarations;
- endian teaching receives a separate current owner with illustrative generated
  type names;
- four focused raw placeholders are justified;
- qualifier, declaration and construction corrections promote coherently in the
  same change set;
- live single-quote legacy examples migrate with the phrase fence;
- operator phrases receive a dedicated current concept owner while the exact
  operator catalog remains unified;
- the operator catalog remains one document;
- saturating magnitude retains `bare{ ||value| }` with no phrase alias;
- stale core-flow phrase deferrals are corrected; and
- the compact nine-term set is sufficient.

The only remaining prerequisite for promotion is separate discussion and
explicit edit authorization for the exact change set above.

Archival of this work item, raw-file retirement, and creation of work item `010`
remain later closure actions with their own alignment and authorization.
