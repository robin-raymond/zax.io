# Zax operator phrases

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers reading, writing, declaring, or evaluating word-spelled Zax operations |
| Applies To | The programmer-facing operator phrase feature; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | The operator phrase mental model; exact finite phrase words and word sequences; natural source as the ordinary use form; phrase pre-unary, post-unary, and binary fixity; the operator declaration as the phrase's only declaration; receiver ownership and the absence of global custom phrases; how type arguments, type-qualified discovery, and protected transfer-stance phrases are recognized; phrase candidate-tree formation, pruning, and ambiguity teaching; bottom-up outward results and the expected-result limit as phrases experience it; public and private phrase eligibility; natural, grouped, and fenced source; phrase-specific physical presentation validated after selection; keyword words in phrase roles; phrase enclosure boundaries; eager, protected, short-circuit, and mixfix interaction as phrases experience it; phrase costs, diagnostics, formatter obligations, and source stability |
| Does Not Own | The general operator/selection model ([operators](operators.md), [function invocation](function-invocation.md)); attached literal phrases, payloads, declarations, merge, and join ([literal source and operators](literal-source-and-operators.md)); runtime case-test interpretation ([switch, case, and default](switch.md)); structural recognition of `dispose` by [Zax `using`](using.md); exact forms and precedence ([operator catalog](operator-catalog.md)); or source token/layout behavior ([source structure](source-structure.md)) |
| Source / Provenance | Legacy [basics](../basics.md) operator-phrase evidence, refined against the current operator, source-structure, declaration, and mixfix owners |

## Mental model

An **operator phrase** is an operator spelled with words instead of punctuation:

```zax
loaf := baker bakes bread dough
converted := source as DestinationType
ready := left logical nand right
position := bits trailing set bit position
```

A phrase is an operator, not a second dispatch mechanism. Its receiver, parameter
mapping, viability, preference, result handling, availability, evaluation,
protection, and mixfix participation are exactly the shared behavior described by
[operators](operators.md) and [function invocation](function-invocation.md).
Only its *spelling* and the source questions that spelling creates are new.

Three sentences carry most of the model:

1. A phrase declaration names one exact, finite sequence of words.
2. The compiler forms every structurally complete reading of the source and
   requires exactly one to survive.
3. Presentation, grouping, and fencing help the programmer state which reading
   was meant; they never invent a reading.

Phrase forms are open:

- the language defines protected forms and protected intrinsic signatures where
  needed;
- user types may declare their own phrase forms;
- user-defined phrase implementations are receiver-owned rather than global;
- natural source is the ordinary use form;
- grouping and fencing are explicit aids when natural source stays ambiguous; and
- declaration, import, discovery, or source order never breaks a tie.

## Using a phrase

Natural source is ordinary source. Nothing marks a phrase at its use site:

```zax
chickens : Chickens

flock := chickens cluck loudly
```

A phrase carries a precedence level like any other operator, so ordinary
expressions read the way the [operator catalog](operator-catalog.md#precedence-and-grouping)
precedence table says they read:

```zax
result = left combines with right + adjustment
// result = (left combines with (right + adjustment))

ready && value matches pattern
// ready && (value matches pattern)

converted compares with source == expected
// (converted compares with source) == expected
```

Same-level ordinary binary phrase chains associate to the left:

```zax
a phrase1 b phrase2 c
// (a phrase1 b) phrase2 c
```

Adjacent *independent* phrase applications need grouping, exactly as adjacent
symbolic unary applications do:

```zax
prepare deliver package    // one reading at most; not two applications
prepare (deliver package)  // two independent applications
```

### Phrase fixity

A phrase component states where its operands sit:

| Phrase fixity | Source shape |
| --- | --- |
| Pre-unary phrase | `phrase operand` |
| Post-unary phrase | `operand phrase` |
| Binary phrase | `left phrase right` |

Those three are the complete set for a phrase component. Circumfix, call, and
index remain separate operator forms rather than phrase fixities, and a
word-delimited operation that surrounds or separates several holes is a
[mixfix](mixfix-operators.md) built from several phrase components rather than a
phrase circumfix. The complete operator form and fixity table, covering symbolic
and phrase spellings together, is owned by
[operators](operators.md#operator-forms).

## Declaring a phrase

The operator declaration *is* the phrase. Zax has no separate source-level
phrase-form declaration and no phrase `forward` form: there is nothing to declare
ahead of the implementation, because the implementation is what introduces the
words.

```zax
Chicken :: type {
  operator post unary 'cluck loudly' final : (
    result : Chicken
  )() readonly = {
    // `_` is the receiver operand.
  }
}
```

A declaration states its fixity exactly as a symbolic declaration does, and the
quoted form contains words only:

```zax
operator pre unary 'prepare' ...
operator post unary 'cluck loudly' ...
operator binary 'bakes bread' ...
```

Operand holes come from fixity and from the callable prototype, never from
substitution inside the quoted text. Declaration ownership, prototype shape, and
qualification attachment are owned by
[declarations and bindings](declarations-and-bindings.md#operator-phrase-declarations-and-type-parameters).

### Exact finite forms

Each phrase word begins with an ASCII lower-case letter and continues with ASCII
lower-case letters or digits:

```zax
alpha
alpha256mode
wood2x4
```

Invalid phrase words make the deciding boundary visible:

```zax
Run        // error: upper-case initial
alphaMode  // error: mixed casing
alpha_mode // error: underscore
```

Phrase words permit no underscore, capitalization, punctuation, or escaping.

A declaration describes one exact finite word sequence. It is **not** a regular
expression, a wildcard, or a generated family of word patterns. The initial
phrase facility has no:

- optional phrase words;
- repeated-component operators;
- wildcard word runs;
- variadic holes;
- omitted holes; or
- declaration syntax that generates several source forms.

An exact form may nevertheless *contain* a repeated literal word:

```zax
yes really really really sure delete
```

That is the exact post-unary component `'really really really sure delete'`, one
finite sequence that happens to repeat a word. It creates no repetition facility.
Later work may add richer patterns only without changing the meaning of existing
fixed forms.

This document owns the phrase-specific word and presentation contract. General
tokenization, comments, continuation, physical lines, and layout mechanics are
owned by [source structure](source-structure.md#operator-phrase-source-integration).

### Type arguments and type-qualified operators

A phrase prototype may declare a **type parameter slot** completed by one
concrete type identity:

```zax
Source :: type {
  operator binary 'as' final : (
    result : DestinationType
  )(
    DestinationType : type
  ) readonly = {
  }
}

converted := source as DestinationType
```

A type argument has no runtime storage or lifetime, and it may determine the
value result type as it does for `as`. A value receiver may accept a type
argument without that argument contributing receiver discovery:

```zax
schema : MyReceiverType
result := schema for SomeType
```

A phrase may also use a concrete type identity as its qualification and
discovery anchor without inventing a separate receiver category:

```zax
MyType :: type {
  operator pre unary 'custom type info for' final : (
    result : MyCustomTypeInfo
  )() unbound = {
    // `_` is unavailable.
  }
}

info := custom type info for MyType
```

`MyType` supplies type-owned lookup and no runtime receiver slot. The declaration
is `unbound`, so `_` is unavailable. The phrase is not inherently compile-time;
it may execute at runtime and return a runtime value.

When one declaration deliberately supports both an instance operand and a
type-qualified receiverless route, it uses ordinary `once`:

```zax
MyType :: type {
  operator pre unary 'inspect' final once : ()() = {
    if ?_
      inspectInstance(_.)
    else
      inspectType()
  }
}

value : MyType

inspect value
inspect MyType
```

Type parameter slots, type arguments, type-qualified `unbound`, and
type-callable `once` are general declaration and invocation concerns owned by
[declarations and bindings](declarations-and-bindings.md#type-qualified-operators)
and [function invocation](function-invocation.md#type-and-instance-calls-to-once-functions).

### Protected transfer-stance phrases

These exact post-unary phrases are protected forms:

```zax
source as copy
source as deep
source as move
source as last
```

They restate the complete source value's transfer stance for one consumer. They
do not invoke a user-defined phrase implementation and perform no transfer by
themselves.

The word `as` also begins binary conversion whose right operand is a complete
type argument:

```zax
converted := source as DestinationType
moved := source as move
```

Candidate formation uses the grammar and exact protected forms:

- a concrete destination type completes binary `as`;
- the exact stance word completes one post-unary protected phrase;
- expected results do not choose between source structures;
- and natural source needs no exact phrase fence.

Complete transfer semantics belong to
[Zax transfer stances](transfer-stances.md). Exact fixity, precedence, and
reservation belong to the [operator catalog](operator-catalog.md).

### Phrase components inside a mixfix

A word-delimited operation with several holes is one mixfix built from several
phrase components:

```zax
high shift left through low by count
```

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

Each component keeps one of the three phrase fixities. Skeletons, holes, receiver
anchors, matching, and decomposition are owned by
[mixfix operators](mixfix-operators.md#phrase-components).

## Receiver ownership

Custom phrase implementations belong to a receiver type. There are **no global
custom phrase implementations**, and an imported extension may not add a natural
phrase to a type it does not own. A phrase use therefore discovers only:

1. protected or language-provided declarations for the exact form; and
2. type-defined declarations on its receiver operand.

Phrase discovery never searches the right operand's type. Adding a public phrase
to a receiver type remains a source-compatibility event, because it may turn
previously unique source into a loud ambiguity, but an unrelated import cannot
quietly contribute a viable natural phrase to somebody else's type.

Some language constructs may look for one exact ordinary receiver-owned phrase
without protecting that phrase form. In particular, `using` checks whether an
enrolled receiver has a compatible pre-unary `dispose` operation:

```zax
operator pre unary 'dispose' final : ()() writable = {
}
```

`dispose` remains open, non-protected, and subject to normal visibility,
qualification, transfer, selection, availability, and result rules. The
resource construct adds structural recognition, not a second phrase declaration
or dispatch mechanism. Complete compatibility and invocation timing are owned by
[Zax `using`](using.md#structural-disposal).

### Receiver-oriented wording

Receiver ownership means a custom value appearing only on the right of an
intrinsic left operand cannot contribute either a symbolic or phrase
declaration:

```zax
intrinsicValue : U32
customValue : MyType

intrinsicValue combines with customValue
// error: U32 declares no such phrase, and MyType is not the receiver
```

The workaround is to reframe the wording around the extensible receiver:

```zax
customValue accepts then combines intrinsicValue
```

Requiring the custom type to be the receiver can make source less natural than
the unavailable intrinsic-first wording. The programmer can rewrite the phrase
around the custom receiver, use an ordinary function, or choose another
supported form.

The intrinsic-first wording is still available when you want it, through a
[partial](partials.md) on the intrinsic receiver:

```zax
MyCombining :: partial U32 {
  operator binary 'combines with' final : (
    result : MyType
  )(
    rhs : MyType
  ) = {
    // ...
  }
}
```

Where a scope grants `MyCombining`, `intrinsicValue combines with customValue`
selects this phrase. Everywhere else it remains unavailable. The phrase still
belongs to the receiver type, protected phrase signatures stay protected, and a
later Zax version may reserve a word sequence that a partial already uses,
which is a source-compatibility event like any other phrase change.

## How word-spelled source is interpreted

Symbolic source fixes one expression tree before selection begins. Word-spelled
source may present several structurally complete readings, so the compiler forms
every complete candidate tree and requires exactly one to survive. **Phrase
recognition is not longest-word-sequence matching.**

The shared formation, pruning, and selection algorithm is owned by
[operators](operators.md#candidate-tree-formation-and-selection). This section
teaches what it means when the operator is spelled with words.

### Overlapping words are not automatically ambiguous

Suppose `Chicken` supplies both `cluck` and `cluck loudly`:

```zax
chickens cluck loudly
```

The existence of both forms does not by itself create an ambiguity. Reading only
`cluck` leaves `loudly` unattached, and unattached source is not a complete
expression, so that reading is discarded before anything is compared.

The reframe matters: ambiguity is *not* "two declarations share words." Ambiguity
is "two structurally complete readings both survive every other rule."

Add one more word and the picture changes:

```zax
chickens cluck loudly outdoors
```

Possible combinations include:

```zax
(chickens cluck loudly) outdoors
(chickens cluck) (loudly outdoors)
```

The second is still not one expression, because it leaves two adjacent
expressions with no joining operation. It is eliminated before parameter
matching. If the first reading and some other complete reading both survive
receiver, parameter, result, and availability checks, the compiler diagnoses an
operator-attachment ambiguity rather than guessing.

Resolution proceeds only through the finite candidate trees already formed from
source. A selected result cannot invent new phrase words, cause source to be
rescanned, or start a fixed-point rewrite.

### Results flow outward, never inward

Inner operations resolve first, and a uniquely selected inner result may make an
enclosing phrase viable:

```zax
(chickens cluck loudly) outdoors
```

If `cluck loudly` returns one `Chickens` value and `Chickens` supplies
`outdoors`, resolution continues outward. Zero or several results cannot fill one
expression position:

```zax
sum:, carry: = addWithCarry(a, b)

combined := addWithCarry(a, b) next operation
// error: no single intermediate receiver
```

There is no implicit "result of results" type. An inner result-only ambiguity
does not flow backward from an enclosing operation:

```zax
chickens cluck loudly outdoors
// `outdoors` cannot choose among result-only overloads of `cluck loudly`.
```

The programmer states the intended boundary directly:

```zax
flock : Chickens = chickens cluck loudly
flock outdoors

(: Chickens = chickens cluck loudly) outdoors
```

Expected-result context may choose an implementation *within* one already formed
tree. It may not choose between distinct phrase extents or attachment trees:

```zax
result : Desired = source phrase middle phrase tail
// error if two phrase trees remain viable, even when only one returns Desired
```

The allowed direction is `selected inner result -> enclosing receiver selection`,
never `enclosing receiver requirement -> manufacture an inner result choice`. The
shared expected-result boundary is owned by
[function invocation](function-invocation.md#expected-results-never-choose-a-source-structure).

## Writing source that says what you mean

Three tools are available, in increasing explicitness: natural source, grouping,
and fencing.

### Grouping

Parentheses create a different tree, exactly as they do for symbolic operators:

```zax
(chickens cluck) loudly
prepare (chickens cluck loudly)
```

Grouping decides *structure*. It does not say which words form one component.

### Exact phrase fencing

A single-quoted, whitespace-separated form is an **exact phrase fence**:

```zax
foo 'bar'
'foo' bar
left 'bakes bread' right
```

**A fence helps weed candidates; it does not choose the flower.**

A fence:

- contains one exact contiguous phrase component;
- uses the same words and exact single spaces as the declaration;
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

Fencing every component of a mixfix still leaves tree pruning to do:

```zax
high 'shift left through' low 'by' count
```

The fences establish exact components. Complete-tree pruning still determines
whether they form one mixfix or several operations. An unknown fenced form
produces a phrase-not-found diagnostic, which is a different error from an
unrecognized natural phrase.

Runtime case tests may omit the selector operand:

```zax
case similar expected
case 'is hot'
case 'bad'
```

The case construct decides whether those words form a direct binary/post-unary
test, an expression compared through equality, or a final operandless pre-unary
test. Fencing still fixes only the exact phrase component; grouping fixes an
equality-expression boundary:

```zax
case (similar expected)
```

This construct-specific ordering is defined by
[switch, case, and default](switch.md#how-one-case-test-is-interpreted).
Once an interpretation supplies a complete phrase operation, the ordinary
candidate-tree and phrase rules in this document apply.

### Contextual completion never searches word combinations

A number literal may use a visible peer type only after source has established
one operator tree and one exact phrase component:

```zax
0 'foobar things make nice decorations' (: MyInteger = 5)
```

The fence fixes the words in one component. Grouping may still be required to
fix the complete tree, and the `MyInteger` declaration must actually provide a
matching contextual phrase and construction.

Unfenced source is ineligible when applying a guessed receiver type would
require trying several word partitions:

```zax
0 foobar things make nice decorations (: MyInteger = 5)
// error: contextual completion cannot search phrase combinations
```

Fencing is one way to provide the needed certainty, not a privileged completion
mechanism. Any future source form that establishes the same single component
and tree may qualify. General contextual operator fallback is defined by
[Zax operators](operators.md#contextual-completion).

The fence's phrase role is owned here. Its interaction with general tokenization
and attached literal prefixes is summarized by
[source structure](source-structure.md#phrase-fences-and-literal-coordination).
Attached literal phrases and their complete payload/result behavior are defined
by
[literal source and operators](literal-source-and-operators.md#attached-literal-phrases).

### Presentation confirms a selection; it never makes one

**Whitespace confirms the selected phrase interpretation; it never selects that
interpretation.**

Suppose `Chicken` supplies `cluck` and `cluck loudly`, and the result of `cluck`
supplies `loudly`. All of these initially expose the same interpretations:

```zax
a = chickens cluck loudly

b = chickens cluck \
    loudly

c = chickens cluck  loudly
```

All three diagnose ambiguity while both trees remain viable:

```zax
chickens 'cluck loudly'
(chickens cluck) loudly
```

Invalid presentation must not remove one tree and make ambiguous source silently
valid. An uncontinued newline is different, because it is already a hard
statement boundary:

```zax
d = chickens cluck
    loudly // error: the newline ended the first statement; layout is invalid
```

Now switch to a different candidate set. Suppose `cluck loudly` is the *only*
viable tree, because receiver, result, or parameter viability already eliminated
the separate `cluck` then `loudly` reading. Only in that unambiguous case does
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

A comment likewise does not prune a candidate prematurely:

```zax
chickens cluck /* explanation */ loudly
```

If `cluck loudly` is selected, the comment creates a phrase-presentation intent
error. If `cluck` and `loudly` are selected as separate components, the comment
lies outside both components and follows ordinary comment rules. General comment
and continuation mechanics remain with
[source structure](source-structure.md#operator-phrase-source-integration).

### Keyword words in phrase roles

Words inside a declared or fenced phrase carry phrase roles rather than keyword
roles, so a custom phrase may use a contextual-keyword spelling unless the exact
form is a protected form. For example, a programmer type may declare a phrase
named `unsafe as`; `unsafe` is an ordinary phrase word there and grants no unsafe
authority.

Natural source considers every complete keyword and phrase interpretation. The
compiler does not warn merely because a spelling might theoretically be confused;
it diagnoses only actual surviving ambiguity.

A type argument consumes the complete type use and ends where the next token
cannot continue type-use grammar:

```zax
value as Payload is immutable
// (value as Payload) is immutable
```

If a following phrase word could also continue the type use, candidate formation
retains both complete interpretations and diagnoses ambiguity when both survive.
Contextual keyword recognition itself is owned by
[source structure](source-structure.md#contextual-keyword-recognition).

### Enclosure boundaries

A phrase component cannot span a source enclosure whose payload must
independently form a complete expression or effective statement. It cannot obtain
phrase words, an operand, a joiner, or a header section from outside that
enclosure. After the payload independently completes, the enclosure may become
transparent to final precedence and wider mixfix matching, so an outer operation
may apply to the completed result.

The keyword-neutral `bare{...}` enclosure demonstrates that boundary:

```zax
bare{ chickens cluck loudly } outdoors
```

`bare{...}` strictly suppresses non-operator keyword roles inside one
independently complete payload, but it does not suppress phrase roles or change
selection. An inner `bare{` is not recognized because the outer enclosure
neutralizes the word. Complete source behavior is defined by
[source structure](source-structure.md#keyword-neutral-bare-source).
The enclosure is never a substitute for grouping or an exact phrase fence.

## Visibility and private phrase eligibility

A private phrase form may be retained during structural candidate formation so
the compiler can understand and diagnose the possible source tree. Outside its
permitted context, its implementation becomes semantically *ineligible* before
callable preference: it cannot defeat, tie, or block a public operation.

Inside the declaring type, private and public implementations are both eligible
and compete under ordinary preference. Visibility is never match quality among
eligible candidates, so the same source may be ambiguous inside a type and
unambiguous outside it.

Suppose `Chicken` supplies a private `cluck loudly`, a public `cluck`, and the
public result of `cluck` supplies `loudly`:

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
    // error: both the private complete phrase and the public chained tree are eligible
  }
}

chicken : Chicken
result := chicken cluck loudly
// Outside Chicken, the private tree is ineligible.
// The public `(chicken cluck) loudly` tree may resolve uniquely.
```

Private-only implementations therefore never change the meaning or validity of
source whose caller can see only public operations. The visibility modifier
follows the declared form rather than preceding `operator`.

The ordinary unavailable-best rule still applies to an eligible *visible*
declaration that is bodyless, `forbidden`, unsupported generated behavior, or
otherwise unavailable after selection. The bounded private-member rule and
complete visibility behavior are owned by
[declarations and bindings](declarations-and-bindings.md#bounded-private-eligibility).

## Evaluation, protection, and short circuit

Ordinary user-defined phrase operations are eager. Their words grant no
short-circuit, unsafe, compile-time, or other privileged behavior, and users
cannot declare their own evaluation strategy.

The language protects the exact Boolean signatures whose evaluation boundary must
remain visible. Exact Boolean `logical nand`, `logical and not`, `logical nor`,
and `logical or not` short-circuit and form
[mixfix-consumption barriers](mixfix-operators.md#protected-barriers). Exact
Boolean `logical xnor` is eager and consumable. Permitted custom operand
implementations of the same language-defined forms are ordinary eager operations
and remain consumable.

Precedence belongs to the form rather than to the receiver, so an eager custom
`logical nor` retains logical OR precedence even though its evaluation and
barrier behavior differ from the protected exact Boolean signature.

A **protected form** is an exact form that user code cannot declare at all.
Exact forms, their precedence levels, the protected forms, and protected
availability are owned by the
[operator catalog](operator-catalog.md#operator-forms).

## Costs, diagnostics, formatting, and source stability

Open phrase forms add real compile-time and tooling cost:

- candidate-tree formation may retain several word extents and attachments;
- receiver and outward-result pruning may be needed before one tree remains;
- source additions can turn unique source into ambiguity;
- tooling must retain physical trivia until after selection; and
- phrase/mixfix interaction may require several exact components to be shown.

Fixed finite forms, one ordinary phrase precedence level, receiver ownership, no
global custom implementations, outward-only result flow, and immediate ambiguity
diagnostics bound those costs without closing the facility.

Diagnostics should distinguish:

- an unknown fenced phrase;
- an unrecognized natural phrase;
- an exact phrase found at an unsupported fixity;
- an incomplete phrase or unattached source;
- several viable phrase trees;
- ambiguity among implementations of one tree;
- a private phrase form ineligible in the caller's context;
- an eligible uniquely best implementation that is unavailable;
- a declaration that claims a protected form;
- a phrase-word spelling violation;
- a phrase-whitespace presentation error;
- a selected phrase component spanning a physical line;
- a keyword-role conflict;
- a type-use versus phrase boundary ambiguity;
- zero or several results where one expression result is required; and
- a protected intrinsic declaration conflict.

A formatter:

- may not add or remove a phrase fence;
- may not repair phrase spacing, comments, or continuations before the source has
  one selected interpretation;
- may offer a presentation repair after selection, but must never turn ambiguity
  into another meaning;
- may normalize one selected phrase to its exact single-space presentation; and
- must preserve grouping and source intent.

Source reflection and documentation must retain enough information to reproduce
an explicit phrase fence even when it creates no final expression-tree node.

Adding, removing, lengthening, reserving, or changing the precedence of a phrase
form is a source-compatibility event. It may make existing source ambiguous or
invalid, but it must never silently reinterpret one viable tree through source,
declaration, import, module, or discovery order.

Adding an applicable `dispose` phrase to a receiver type also changes whether
existing `using` source invokes disposal. That resource-specific compatibility
effect is defined by
[Zax `using`](using.md#source-stability).

An ungrouped runtime case test has an explicit construct-specific exception:
adding an exact-`Boolean` direct phrase may activate the higher-priority
selector-relative interpretation instead of prior implicit equality. This is the
documented case-test fallback, not ordinary phrase-tree tie-breaking. Grouping
or explicit `==` preserves equality intent.

## Boundaries and maturity

This document is current conceptual design for the operator phrase feature, not
a formal grammar, a compatibility contract, or an implementation mapping.

Complete source-reflection representation, richer phrase patterns,
owner-authorized external phrase extensions, complete literal realization, the
final spelling of a keyword-neutral source enclosure, exact diagnostic
identifiers, and generic or alias type-qualified discovery remain focused future
work.

For exact forms and precedence see the
[operator catalog](operator-catalog.md); for the shared operator model see
[operators](operators.md); for multi-component operations see
[mixfix operators](mixfix-operators.md); for omitted-selector phrase use see
[switch, case, and default](switch.md#how-one-case-test-is-interpreted).
