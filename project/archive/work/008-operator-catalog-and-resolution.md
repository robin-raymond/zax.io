# 008: Operator catalog, declarations, and expression resolution

| Field | Value |
| --- | --- |
| Status | Historical / non-normative / audit-only |
| Work Item | `008` |
| Created | 2026-08-26 |
| Completed | 2026-08-28 |
| Owns | The historical record of the bounded review defined by the fixed initiating input |
| Does Not Own | Current language behavior, accepted complete literal, phrase, conversion, generic, metaprogramming, type-identity, formal grammar, or compiler behavior |

## Non-authority notice

This archived file is historical and audit-only. Its aligned findings were
promoted into current language owners before closure. Current documentation and
live raw inputs, not this record, own the resulting language behavior and
deferred future pressure.

## Fixed initiating input

This section records the information known and aligned when work item `008` was
created. It is intentionally incomplete rather than exhaustive and must not be
rewritten as the work develops.

### Initiating concern

Develop a coherent human-developer-facing model for the operator system needed
to read and write ordinary Zax expressions:

1. the catalog of recognized operator spellings and operator families;
2. fixity, arity, overloadability, and protected built-in signatures;
3. declaration forms for ordinary pre-unary, post-unary, and binary operators;
4. global and type-defined declarations and receiver operands;
5. operand evaluation order and once-only evaluation;
6. lookup domains, candidate discovery, viability, preference, ambiguity, and
   expected-result interaction;
7. precedence, associativity, grouping, and expression-tree formation;
8. arithmetic overflow-policy families, including wrapping/modulating,
   saturating, and checked operations;
9. compound and mutating operators and their relationship to value operations,
   assignment, and reconstructive replacement;
10. explicit fallback or generated relationships among related operators;
11. costs, failures, diagnostics, and source-stability consequences; and
12. lasting documentation owners and boundaries.

Use current declaration, qualifier, invocation, construction, source-structure,
core-flow, and bounded operator owners as constraints. Recover Zax-specific
intent before borrowing operator behavior from other languages.

### Motivating pressure

Current conceptual owners now establish:

- visible declaration and assignment boundaries;
- receiver, value, access, and indirection qualifications;
- shared callable viability, expected-result limits, and partial-order
  preference;
- assignment versus reconstructive replacement;
- exact source continuation, token spacing, bodies, and layout intent;
- branch-specific callable selection and expression convergence;
- exact-`Boolean` conditions;
- direct and ambiguous `?`/`!` selection, Boolean-only opposite-operator
  fallback, ordinary eager `&&`/`||` overloads, and protected Boolean
  short-circuit operations; and
- required language support for protected primitive operations.

The remaining operator material proposes a large flat token list, global and
type-defined declaration forms, special literals and word operators, and
operator-specific examples without one reviewed catalog, expression-binding
model, or derivation policy.

New aligned initiating input adds explicit overflow-policy families:

```zax
result := a +% b // wrapping or modulating addition
result := a +| b // saturating addition
result := a +! b // checked addition
```

The same review must determine whether corresponding subtraction,
multiplication, division, unary, increment/decrement, shift, and compound forms
exist and where the pattern deliberately stops.

The operator system is an early dependency for selection and matching, optional
and error behavior, numeric and structural types, arrays and iteration, casting,
generics, and metaprogramming. Reviewing it now avoids each future domain
inventing local token, precedence, or fallback rules.

### Known assumptions

- [Zax language vision](../../../language/vision.md) is accepted foundational
  direction.
- [Zax language principles](../../../language/principles.md) owns reusable
  cross-cutting design and use principles.
- [Zax source structure](../../../language/source-structure.md) owns statement
  boundaries, continuation, whitespace intent, contextual keywords, and layout
  validation.
- [Zax declarations and bindings](../../../language/declarations-and-bindings.md)
  owns declaration forms, name visibility, assignment boundaries, and the
  ordinary identifier namespace.
- [Zax qualifiers](../../../language/qualifiers.md) owns place, value, access,
  receiver, and indirection qualifications.
- [Zax function invocation](../../../language/function-invocation.md) owns shared
  callable viability, source-order input evaluation, expected-result limits,
  preference, ambiguity, and branch-specific callable selection.
- [Zax construction, replacement, and destruction](../../../language/construction-and-destruction.md)
  owns assignment-versus-replacement lifetime behavior and hidden-cost
  constraints.
- [Zax core flow control](../../../language/core-flow-control.md) owns exact-Boolean
  conditions, conditional-expression selected-arm order, and convergence.
- [Zax operators](../../../language/operators.md) is the bounded current operator
  owner and must grow cohesively rather than be replaced by another competing
  owner.
- Operator result types may be arbitrary unless a specific protected operation
  constrains them.
- Direct operator selection, ambiguity, and opposite-operator fallback retain
  their current meaning.
- Exact `Boolean && Boolean` and `Boolean || Boolean` are language-provided,
  protected, left-to-right short-circuit operations that user declarations
  cannot replace.
- Other valid `&&`/`||` operand shapes are ordinary eager overloads and may
  return arbitrary types.
- Optimization may preserve established operations but may not silently select
  a different user-defined operator.
- No source-order, import-order, layout, or implementation guess may resolve an
  otherwise ambiguous operator meaning.
- There is no language implementation in this repository.

### Known inclusions

- Inventory every operator spelling found in current and legacy material.
- Classify every spelling as current, candidate, deferred, rejected, reserved,
  protected, or superseded.
- Decide whether Zax has a fixed recognized symbolic catalog, permits arbitrary
  symbolic declaration, or uses another bounded rule.
- For each reviewed operator, record token spelling, fixity, arity,
  overloadability, protected signatures, operand evaluation, result constraints,
  precedence, associativity, related compound forms, costs, failures,
  diagnostics, and source-stability effects.
- Pre-unary, post-unary, and binary declaration forms.
- Global and type-defined declarations and receiver operands.
- Lookup domains and candidate discovery at the depth required for ordinary
  symbolic operators.
- Reuse of function-invocation viability, preference, expected-result selection,
  ambiguity, and unavailable-best behavior.
- Parentheses, precedence, associativity, chaining, and expression-tree
  formation for the accepted catalog.
- Arithmetic baseline behavior for ordinary `+`, `-`, `*`, `/`, and `%`.
- Wrapping/modulating arithmetic candidates such as `+%`, `-%`, and `*%`.
- Saturating arithmetic candidates such as `+|`, `-|`, and `*|`.
- Checked arithmetic candidates such as `+!`, `-!`, `*!`, and possibly `/!`.
- Applicability of overflow policies to signed, unsigned, fixed-width,
  pointer-sized, floating, fixed-point, compile-time, and user-defined numeric
  types.
- Overflow, underflow, division-by-zero, invalid-shift, and constant-expression
  behavior at the depth required by the catalog.
- Compound forms such as `+=`, `+%=`, `+|=`, and `+!=`, where justified.
- Increment/decrement relationships to arithmetic or compound operations.
- Assignment and compound mutation versus reconstructive replacement.
- Explicit derivation or fallback candidates, including:
  - `!=` from Boolean `==` plus negation;
  - relational operators from `<=>`;
  - pre/post increment from compound arithmetic;
  - compound mutation from a value operation plus assignment/replacement;
  - overflow-policy compound forms from their value forms;
  - swapped-operand candidates; and
  - already accepted `?`/`!` Boolean fallback.
- The default presumption that distinct spellings are independent operations
  unless an explicit reviewed rule establishes a derivation.
- The question whether any derivation is implicit, explicitly generated, opt-in,
  or prohibited.
- Once-only place and operand evaluation across any generated relation.
- Hidden copy, temporary, allocation, destruction, mutation, conversion, alias,
  and result-shape costs.
- Tokenization, longest-token behavior, and whitespace/source-stability pressure
  from spellings such as `+!`, `+|`, `+%`, and their compound forms.
- Protected language-provided operations and diagnostics for conflicting user
  declarations.
- Programmer-visible costs, failures, diagnostics, formatting, and source
  stability.
- Candidate lasting owners and documentation boundaries.
- Preservation of useful but premature literal, word, conversion, type,
  generic, metaprogramming, and generated-operator findings in existing or newly
  justified live raw input.

### Known starting boundaries

- Complete literal parsing and user-defined literal operators.
- Complete word and compound-word operator semantics.
- Complete `as`, `unsafe as`, conversion, and casting design.
- Compiler-generated operator families beyond derivation questions necessary to
  evaluate the ordinary symbolic catalog.
- Generics, concepts, specialization, and generic operator constraints.
- Complete type identity, structural compatibility, layout, and conversion.
- Complete optional, error, selection, iteration, and pattern semantics.
- Complete allocator, pipeline, invocation, indexing, and function-composition
  operator domains when ordinary catalog coherence does not require them.
- Arbitrary compile-time operator generation and metaprogramming.
- Formal grammar, parser implementation, lowering, ABI, and toolchain internals.

These areas are not part of the initiating question and need not be designed to
begin. They do not prohibit following, recording, or dispositioning material
consequences.

### Intentionally unresolved framing

- The authoritative operator catalog and whether arbitrary symbolic operators
  are permitted.
- The exact meaning of ordinary arithmetic overflow once explicit policy
  variants exist.
- Whether `%`, `|`, and `!` are the accepted wrapping, saturating, and checked
  policy suffixes.
- Which base operations and built-in type families support each policy.
- Whether saturating operations clamp to both representable extrema.
- Whether checked operations panic, return a flag/error/optional result, use
  another result shape, or vary by operator/type.
- The exact spelling and availability of compound policy forms.
- Whether `a += b` and `a = a + b` are always independent operations.
- Whether any compound operation can be derived from a value operation plus
  assignment/replacement without hidden semantic change.
- Whether a non-mutating value operation can ever be derived from a mutating
  compound operation.
- Which comparison or logical operations have safe derivations.
- Which derivations are automatic, explicit, opt-in generated, or prohibited.
- Exact global, member, imported, and future lookup domains.
- Exact precedence and associativity for every accepted operator.
- Longest-token and whitespace rules needed to prevent future tokens from
  reinterpreting existing source.
- Which exact built-in signatures are protected and how their availability is
  taught without promising implementation mapping.
- Which findings belong in the current operators owner versus source structure,
  declarations, qualifiers, invocation, construction, vision, type, casting,
  diagnostics, or future raw owners.

### Initial stopping guidance

Stop when the work has:

- dispositioned every known operator spelling and family;
- established one organized operator catalog rather than a flat legacy list;
- established ordinary symbolic declaration forms, fixity, arity, and receiver
  behavior;
- established lookup, candidate discovery, viability, preference, ambiguity,
  and expected-result interaction;
- established precedence, associativity, grouping, and operand evaluation for
  the accepted catalog;
- established ordinary arithmetic overflow behavior and the wrapping,
  saturating, and checked policy families;
- established compound/mutating operator behavior and its boundary with
  assignment and reconstructive replacement;
- produced an explicit derivation/fallback matrix with once-only evaluation,
  hidden-cost, qualification, alias, and result-shape constraints;
- established protected built-in signatures and conflicting-declaration
  diagnostics;
- constrained literal, word, conversion, generated, generic, type, selection,
  iteration, optional, error, and metaprogramming consequences without
  prematurely designing those complete domains;
- identified costs, failures, diagnostics, formatting, and source-stability
  pressure;
- dispositioned useful legacy and newly supplied operator material;
- preserved every deferred consequence in a live owner or indexed raw
  destination;
- identified plausible lasting owners and their boundaries; and
- gathered enough information for a pre-promotion documentation-fit dry run.

Do not promote findings, update language examples broadly, archive this work
item, or begin work item `009` without the separately required discussion,
alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../../documentation.md) - governs ownership,
  focused reading, numbered work, promotion, and archival.
- [Language vision](../../../language/vision.md) - provides foundational direction
  and the required-language-support boundary.
- [Zax language principles](../../../language/principles.md) - provides the
  cross-cutting principles against which operator behavior should be evaluated.
- [Zax source structure](../../../language/source-structure.md) - provides token
  spacing, continuation, contextual keywords, layout intent, and expression
  source boundaries.
- [Zax declarations and bindings](../../../language/declarations-and-bindings.md) -
  provides declaration forms, name visibility, assignment boundaries, and
  operator-facing declaration constraints.
- [Zax qualifiers](../../../language/qualifiers.md) - provides place, value, access,
  receiver, and indirection qualifications used by operator candidates.
- [Zax function invocation](../../../language/function-invocation.md) - provides
  shared evaluation, binding, viability, expected-result selection, preference,
  ambiguity, and branch-specific callable selection.
- [Zax construction, replacement, and destruction](../../../language/construction-and-destruction.md) -
  provides assignment/replacement lifetime boundaries and hidden-cost pressure.
- [Zax core flow control](../../../language/core-flow-control.md) - provides
  exact-Boolean conditions, conditional-expression selected-arm order, and
  convergence.
- [Zax operators](../../../language/operators.md) - provides the current bounded
  operator behavior that this work must extend coherently.
- [Zax language-design terms](../../../language/terms.md) - provides current
  cross-cutting vocabulary.
- Focused [legacy basics operator catalog](../../../basics.md#operators) - preserves
  the flat proposed operator list, protected forms, and word-operator boundary.
- [Legacy operator overloading](../../../operator.md) - preserves primary evidence
  for declaration forms, arbitrary results, global/type-defined operators,
  receiver use, literals, and word operators.
- Relevant entries in the
  [cross-cutting audit](../../raw/cross-cutting-audit.md) - preserve operator,
  branch-convergence, required-support, example, and source-stability audit
  obligations that must be dispositioned.

### Consequence-driven

- Read focused [casting](../../../casting.md) material when conversion operators,
  `as`, or result-shape adaptation becomes necessary.
- Read focused [optional](../../../optional.md), [Nothing](../../../nothing.md), or
  [except](../../../except.md) material when presence, negation, checked-result, or
  error-flow behavior constrains an operator family.
- Read focused [type definition](../../../type-definition.md),
  [composition](../../../composition.md), [alias](../../../alias.md), or
  [raw structural typing](../../raw/structural-typing.md) material when operator
  identity, receiver membership, structural compatibility, or result type
  relationships become necessary.
- Read focused [arrays](../../../arrays.md), [variadic](../../../variadic.md), or
  remaining legacy operator sections only when indexing, invocation, literals,
  or variadic behavior constrains the ordinary catalog.
- Read [raw analysis controls](../../raw/analysis-controls.md) or
  [raw safety boundaries](../../raw/safety.md) when checked failure, proof
  assertions, lint behavior, or protected-operation diagnostics become
  necessary.
- Read [raw build and dependency input](../../raw/build-and-dependencies.md) when
  required primitive support or automatic helper/toolchain behavior becomes
  programmer-visible.
- Read focused [compiler directives](../../../compiler-directives.md),
  [meta-functions](../../../meta-functions.md), or [meta-types](../../../meta-types.md)
  only when ordinary operator generation cannot be separated cleanly from
  compile-time facilities.
- Expand beyond this list whenever discussion or a material finding establishes
  a concrete need. State why the additional reading is relevant and keep it
  focused.

Material not listed in this reading scope is neither required nor prohibited. It
remains available when discussion or a concrete consequence makes it relevant.

### Audit-only

- `project/archive/`, including work items `001` through `007`.

Do not read archived work item `007` while conducting ordinary work on `008`.
Its applicable findings have been promoted into current language and project
owners and incorporated into the fixed initiating input above. Read it only when
the language maintainer explicitly requests a targeted audit or provenance
investigation.

Other audit-only material likewise requires direct language-maintainer
instruction or a specific audit or recovery need.

## Working record

This working record contains aligned findings and the initial candidate
reconstruction that prompted their review. Aligned findings remain
non-authoritative until a separately discussed and authorized promotion
incorporates them into their lasting owners.

### Aligned findings after maintainer review

The language maintainer and agent aligned the findings in this section through
conversation on 2026-08-26 and 2026-08-27. Exact formal grammar, implementation,
and deferred adjacent concepts remain unsettled unless explicitly stated.

#### Programmer model and operator categories

Zax has a closed, language-defined catalog of recognized symbolic operators.
Types and libraries may overload permitted recognized forms; they may not invent
an arbitrary punctuation sequence and thereby add it to the grammar.

This is a source-stability decision, not a claim that familiar punctuation has
intrinsic meaning. `==` is special only because Zax deliberately allocates it a
fixed spelling, fixity, and precedence. An arbitrary sequence such as `==!@==`
does not become an operator through a declaration.

The operator vocabulary distinguishes:

- a **symbolic operator**, written primarily with punctuation such as `+`, `&~`,
  or `<<%`;
- an **operator phrase**, written with one or more language-recognized contextual
  words such as candidate `is not`, `reverse bits`, or `unsafe as`;
- an **operator form**, the generic category when the symbolic/phrase distinction
  does not matter;
- a **circumfix operator**, one complete operation with a language-recognized
  opening component, closing component, and enclosed operand hole; and
- a **mixfix operator**, one complete overloadable operation selected from an
  expression-tree pattern containing three or more operand holes and two or more
  recognized operator components.

Operator phrases exist conceptually. Their exact catalog, declaration grammar,
fixity presentation, overloadability, custom-extension boundary, keyword
collision behavior, and phrase recognition remain focused future work, likely
work item `009`. Current illustrative phrases are evidence and candidates rather
than accepted source syntax. The symbolic and circumfix catalogs are closed.
Whether work `009` establishes a deliberately bounded open phrase facility
remains unsettled.

Ordinary operator declarations state `pre unary`, `post unary`, `binary`, or
`circumfix` explicitly even when the current catalog gives a spelling only one
form. Mixfix declarations state `mixfix` and identify every component's exact
spelling and fixity.

An operator may declare any result shape: zero, one, or several result slots. A
use that participates as one node inside a larger value expression requires
exactly one usable result. Zero-result and multiple-result uses require a
complete surrounding context able to consume that result shape explicitly.

```zax
customLeft + customRight // may be a complete zero-result operation

sum:, carry: = customLeft + customRight
// A selected custom + may supply two explicitly routed results.

combined := customLeft + customRight
// error if the selected + has two mandatory results
```

Several results do not become an implicit tuple or anonymous structure, and
grouping does not combine them. Exact result-routing integration reuses the
shared callable model rather than creating operator-specific mapping.

User-defined operator results remain otherwise unrestricted. Familiar result
types and effects are strong conventions for readable APIs, not general
restrictions on custom types.

#### Parsing, precedence, whitespace, and adjacency

The parser forms one expression tree from the closed operator catalog, each
form's fixed precedence and associativity, grouping, and syntax context. It does
not inspect operand types, namespaces, imports, overload declarations, or result
types to decide that tree.

Every spelling and fixity has one language-defined precedence and associativity.
A namespace or type cannot decide that `*` binds above `+` for one operand family
and below it for another.

The aligned precedence baseline follows familiar C++ ordering where Zax shares
the same operations. Higher levels bind before lower levels:

| Level | Forms | Grouping |
| --- | --- | --- |
| Enclosed/grouped | Parentheses and recognized circumfix forms | Delimiter-owned |
| Postfix/delimited | Call, index, member/dereference, post `++`/`--` families | Left-to-right chaining |
| Prefix | `?`, `!`, `~`, `~=`, reduction/count `#...`, pre `++`/`--` families, unary arithmetic policies | Separate applications require grouping |
| Multiplicative | `*`, `/`, `%` and accepted policy variants | Left |
| Additive | `+`, `-` and accepted policy variants | Left |
| Shift/rotate/composition | `<<`, `>>`, `>>>`, `<<<`, `<<%`, `>>%`, future modulo-count phrases | Left |
| Relational | `<`, `<=`, `>`, `>=`, `<=>` | Ungrouped chaining is an intent error |
| Equality | `==`, `!=` | Ungrouped chaining is an intent error |
| Bitwise AND family | `&`, `&~`; future NAND phrase family | Left |
| Bitwise XOR family | `^`; future XNOR phrase family | Left |
| Bitwise OR family | `\|`; future NOR/OR-NOT phrase family | Left |
| Logical AND family | `&&`; future NAND/AND-NOT phrase family | Left |
| Logical XOR family | `^^`; future XNOR phrase family | Left |
| Logical OR family | `\|\|`; future NOR/OR-NOT phrase family | Left |
| Conditional expression | `condition ?? trueValue ;; falseValue` | Right-nesting |
| Assignment/compound mutation | `=`, arithmetic/bitwise/shift compounds | Right |
| Swap | `<<>>` | Left |

Parentheses deliberately create another tree and therefore may change mixfix
matching:

```zax
a[b] = (c + d) * e
a[b] = c + d * e
```

Relational and equality chains are mechanically parseable but rejected as
suspicious ungrouped intent:

```zax
a < b < c   // error: grouping required
a == b == c // error: grouping required
```

Explicit grouping may request a tree when the intermediate result supports it:

```zax
(a < b) < c
```

This rule makes no claim that custom comparison operations are transitive or
equivalence relations.

Assignment remains right-associative:

```zax
a = b = c = d // a = (b = (c = d))
```

Swap is left-associative:

```zax
a <<>> b <<>> c // (a <<>> b) <<>> c
```

Protected built-in swap returns no results, so that ordinary decomposed chain is
unavailable. A custom receiver may define a direct `<<>>`, `<<>>` mixfix over the
formed tree.

Mixfix has no independent precedence. It matches the tree produced by these
rules. Work `009` must assign every accepted operator phrase one fixed precedence
and associativity, normally by placing it in an existing semantic level.

Symbolic-operator whitespace expresses fixity:

```zax
!value       // pre-unary: attached on the right
value++      // post-unary: attached on the left
left + right // binary: whitespace on both sides
```

Newlines count as whitespace only where another accepted continuation rule
permits the expression to continue.

The fixed longest recognized symbolic token wins at an operator position.
Separate adjacent unary applications are an intent error and require grouping:

```zax
--value    // one recognized pre-decrement token
-(-value)  // two pre-unary negations
!(!value)  // two pre-unary logical negations
```

`!!value` may not mean two adjacent `!` applications. Requiring `!(!value)`
preserves the ability to allocate `!!` as one future language-defined spelling
without reinterpreting existing source. The accepted `?`/`!` fallback is a
semantic relationship and does not require generated source spelled `!!value`.

Whitespace distinguishes policy tokens from adjacent ordinary operations:

```zax
a +! b // one binary operator token
a + !b // binary + with a pre-unary ! right operand
```

Operator phrases use whitespace within their recognized words and between the
phrase and its operands. Separate adjacent phrase applications also require
grouping:

```zax
// Illustrative phrase syntax.
reverse bytes (reverse bits value)

// error: adjacent phrase applications are not grouped
reverse bytes reverse bits value
```

Phrase work must define exact longest-phrase, contextual-word, continuation, and
future-keyword rules without making visible declarations decide the parse.

#### Circumfix operators

`circumfix` is a distinct Zax declaration category rather than a relaxation that
turns its opening and closing components into independent pre/post unary
operators.

The language first allocates each circumfix form in a closed catalog:

- exact opening component;
- exact closing component;
- enclosed operand placement;
- binding and grouping behavior;
- overloadability; and
- protected intrinsic domains.

A declaration may overload a recognized form. It may not introduce an arbitrary
delimiter pair:

```zax
MyType :: type {
  // Illustrative declaration syntax.
  operator circumfix '|' '|' final :
    (result : MyResult)() readonly = {
    // `_` is the enclosed receiver operand.
  }
}
```

The opening and closing `|` components exist only as the recognized pair. No
independent pre-unary or post-unary `|` declaration is implied.

This circumfix coexists with binary bitwise OR:

```zax
left | right // binary bitwise OR
|value|      // circumfix magnitude/absolute operation
```

Binary `|` has whitespace on both sides. The circumfix opener attaches to the
enclosed expression on its right, and the closer attaches on its left.

The initial recognized circumfix catalog is:

| Opening | Closing | Candidate operation |
| --- | --- | --- |
| `\|` | `\|` | Required magnitude/absolute-like operation |
| `\|%` | `\|` | Wrapped magnitude |
| `\|?` | `\|` | Optional magnitude |
| `\|!` | `\|` | Combined magnitude overflow report |
| `\|\|` | `\|\|` | Norm-like operation |

Recognition does not make every form available for every type. A custom vector
type may define:

```zax
length := ||vectorValue||
```

while a built-in integer with no protected norm signature produces an ordinary
unavailable-operation diagnostic:

```zax
length := ||integerValue||
// error: the recognized circumfix has no applicable built-in operation
```

This is not a syntax or intent error and is not nested magnitude. Nested
magnitude is grouped explicitly:

```zax
|(|integerValue|)|
```

Adding a future built-in norm signature may turn previously unavailable source
into valid source without reinterpreting existing valid code.

Closed intrinsic receiver families reserve every recognized circumfix signature,
including currently unsupported forms. User declarations cannot claim those
signatures. Custom receiver types may overload recognized circumfix forms and
may return arbitrary result shapes. Extensible library types such as `String`
follow their library-extension rules rather than automatically becoming closed
intrinsic circumfix domains.

For protected fixed-width signed integers, required magnitude returns the
same-type absolute value and panics when the positive magnitude is not
representable:

```zax
value : I8 = -127
magnitude := |value| // I8(127)

minimum : I8 = -128
magnitude := |minimum| // panic: I8 cannot represent 128
```

The policy forms address that failure explicitly:

```zax
wrapped := |%minimum| // I8(-128)
checked := |?minimum| // empty I8?
report := |!minimum|
```

The combined report has the same conceptual shape as other arithmetic overflow
reports:

```zax
<anonymous> :: type {
  wrapped : I8       // -128
  saturated : I8     // 127
  overflowed : Boolean // true
}
```

`|%value|`, `|?value|`, and `|!value|` are complete recognized circumfix forms.
They are not `|value|` wrapped around independent pre-unary `%`, `?`, or `!`
operations.

Protected unsigned fixed-width integers support the same magnitude forms for
generic numeric code:

```zax
|unsigned|  // same unsigned value
|%unsigned| // same unsigned value
|?unsigned| // present optional containing that value
|!unsigned| // wrapped == saturated == value; overflowed == false
```

These are genuine magnitude operations whose results happen to be unchanged.
The norm form `||unsigned||` remains unavailable unless a future numeric review
establishes it.

A symbolic saturating-magnitude form would collide visually with the `||...||`
norm form. Saturating magnitude therefore remains an operator-phrase candidate:

```zax
// Illustrative phrase syntax.
saturating magnitude of value
```

The combined `|!value|` report still exposes the saturated result. Specialized
forms such as `|!%value|` are not added without separate pressure.

The catalog does not reserve floor or ceiling circumfixes now. They may be added
later if concrete use and a readable source form justify them.

Existing grouping, call, indexing, array, block, construction-packet,
compiler-directive, and literal delimiters do not become overloadable circumfix
operators merely because they surround source:

```text
(...)  [...]  {...}  [{...}]  [[...]]  '...'  "..."
```

Once recognized, one complete circumfix form may be referenced as a component of
a later mixfix. A protected built-in circumfix node may be accepted as a
completed hole value but may not be consumed and replaced by a user mixfix.

Phrase work may later allocate a closed or deliberately bounded phrase
circumfix. Independent pre/post phrase applications require grouping; an
ungrouped opening/operand/closing phrase is valid only when the complete
circumfix is recognized.

#### Declarations, receivers, discovery, and ties

A global ordinary unary declaration supplies its one operand. A global ordinary
binary declaration supplies its left and right operands.

A type-defined operator has a [receiver operand](../../../language/terms.md#receiver-operand):

| Form | Receiver operand | Explicit parameters |
| --- | --- | --- |
| Type-defined pre-unary | Sole operand | None |
| Type-defined post-unary | Sole operand | None |
| Type-defined binary | Left operand | Right operand only |

Call and index are grammar-recognized **postfix delimited forms**, not ordinary
post-unary operators:

```zax
callable(arguments)
container[indices]
```

Their delimiters do not become independently overloadable. A direct custom
definition states the complete form:

```zax
// Illustrative declaration syntax.
operator call final :
  (result : Result)(
    first : Input,
    second : OtherInput
  ) = {
}

operator index final :
  (result : Element)(
    row : Row,
    column : Column
  ) readonly = {
}
```

Direct `operator call` and `operator index` definitions derive their argument or
index count from their complete input prototypes. Declared functions are
language-recognized as callable without declaring `operator call`; custom
callable types use the overloadable form.

The current qualifier-owner example that gives a type-defined binary `=` both
`lhs` and `rhs` parameters in addition to `_` is an example error. Its evident
correction is to remove the unused explicit `lhs`; a later authorized promotion
must correct that owner.

Only the sole unary operand or left binary operand contributes type-defined
operator discovery. The right operand never contributes declarations from its
type. Visible global declarations remain a separate discovery domain and support
forms such as a built-in value on the left and a custom value on the right.

Declaration origin supplies no selection preference. Type-defined and global
candidates participate in ordinary viability and partial-order preference. A
candidate wins only when it is demonstrably better; a true equal or incomparable
undominated set is ambiguous.

The same discipline applies to mixfix overloads sharing one structural pattern.
No source, declaration, import, module, generation, or pattern-discovery order
breaks a tie.

#### Shared selection and expected results

After source structure fixes the ordinary expression tree, operator resolution
reuses the shared callable model:

1. discover candidates from the applicable operator domains;
2. map receiver and operand positions;
3. test types, qualifications, indirection, transfer behavior, constraints,
   result viability, and availability;
4. compare viable candidates using partial-order dominance;
5. select one unique undominated candidate;
6. diagnose equal or incomparable candidates;
7. diagnose a uniquely best unavailable operation rather than falling through;
   and
8. apply only an explicitly reviewed operator-specific fallback.

An unavailable best candidate includes a viable bodyless declaration, an exact
`forbidden` declaration, a requested `default`/generated implementation that the
language cannot supply, or another viable selected declaration that cannot be
invoked. A mere type or qualification mismatch makes a candidate nonviable
rather than selected-but-unavailable.

A direct, complete, explicitly typed declaration may provide narrow expected
result context:

```zax
operator binary '+' final :
  (result : Integer)(lhs : Scalar, rhs : Scalar) = { ... }

operator binary '+' final :
  (result : Float)(lhs : Scalar, rhs : Scalar) = { ... }

integer : Integer = left + right // may select the Integer result
inferred := left + right         // ambiguous when inputs compare equally
```

Result quality breaks a tie only after receiver and supplied-input comparisons
are equal. A better result may not compensate for worse operand binding.

Assignment syntax does not introduce an unresolved name:

```zax
total = left + right  // error if total has no existing binding
total := left + right // declaration and initialization
```

An overload may perform unusual domain effects or return an arbitrary result,
but it cannot retroactively turn an unresolved caller identifier into a lexical
declaration.

#### Closed intrinsic and extensible library domains

A user declaration may not claim an operator signature whose every operand
belongs to a **closed intrinsic operator family**. This reserves both supplied
and currently unsupported all-intrinsic combinations for language evolution.

```zax
// error: Integer is a closed intrinsic family on both sides
operator binary '+' final :
  (result : Strange)(lhs : Integer, rhs : Integer) = { ... }

// permitted: one operand is a custom type
operator binary '+' final :
  (result : MyType)(lhs : Integer, rhs : MyType) = { ... }
```

Result type does not evade the restriction. An alias retaining intrinsic type
identity remains intrinsic; a genuinely new wrapper type is custom. Future
generic declarations must not instantiate over a protected all-intrinsic shape.

Language recognition and library provision do not automatically make a type's
operator domain closed. `String` is intended to be language-recognized and
library-supplied, with allocation and library behavior, while remaining
operator-extensible. A later library declaration overlapping a user declaration
may create a visible ambiguity that requires source correction.

Whether exact language-owned callable combinations receive protection remains
future composition work. A custom-left operation such as
`myReflectionAnalyzer << myFunc` remains outside an all-intrinsic domain even if
function types later receive protected combinations.

Protection is recorded by canonical type family rather than repeating every
width and alias:

| Family | Protected availability established or required by `008` |
| --- | --- |
| `Boolean` | Truth operations, equality/complement behavior, and ordinary value operations accepted for Boolean; exact short-circuit forms receive the additional mixfix barrier below |
| Fixed-width signed integers | Accepted ordinary/policy arithmetic, comparison, bitwise, reduction/count, shift/rotate, in-place complement, magnitude, compound, increment/decrement, and swap forms |
| Fixed-width unsigned integers | The same applicable forms, with signedness counterpart conversion, zero-filling natural right shift, and signed-only operations unavailable |
| Pointer-sized numeric integers | Follow the applicable signed/unsigned integer family; pointer values themselves do not inherit numeric operators |
| Floating point | Reserve the closed domain; ordinary C++-familiar arithmetic, comparison, unary negation, and magnitude remain candidates for later float review; integer policy and bitwise forms are unavailable |
| Future fixed-point/arbitrary-width intrinsics | Reserve the domain; availability follows a later numeric-type review and its finite logical-extent guarantees |
| Other closed intrinsic families | Reserved but unavailable where character, rune, UUID/word alias identity, `Unknown`, `Nothing`, pointer/reference, enum, or another type review has not established an operation |
| Extensible library families | Library-owned availability and ordinary ambiguity; `String` is not closed merely because the language recognizes its literals |

This family-level matrix is sufficient to preserve future language ownership
without pretending every type-specific operation is settled. Aliases inherit
protected behavior only when later type-identity work establishes that they
retain the underlying intrinsic identity.

Built-in floating values do not receive ordinary bitwise operators merely
because a storage format has a fixed width. Numeric conversion, safe
bit-preserving reinterpretation, unsafe representation access, NaN payloads,
canonicalization, and byte order require an explicit future conversion or
representation operation. The float bitwise domain remains reserved and
unavailable until that review.

#### Mixfix mental model

A mixfix declaration describes an operator-tree skeleton. Fixed component nodes
must match their exact written operator forms. Operand holes accept complete
arbitrary expression subtrees.

For:

```zax
a[b] = c + d + e
```

an indexed-assignment mixfix may match:

```text
=
|- []
|  |- receiver-hole: a
|  `- index-hole: b
`- rhs-hole: c + d + e
```

The RHS arithmetic is inside one hole. It resolves normally and supplies its
result to the mixfix. The consumed `[]` and `=` nodes do not execute
independently.

The same pattern matches:

```zax
a[b * d] = c * e
```

with `b * d` as the index hole and `c * e` as the right-hand hole.

A type-defined mixfix anchors one distinguished receiver hole. Receiver-bearing
slots are followed through the pattern to that source operand:

```text
a -> [] -> =
```

For `a[b] = c`, `a` is the receiver; `b` and `c` are explicit callable inputs.
The right-hand `c` cannot become the receiver because it occupies the nonreceiver
slot of `=`.

Parentheses preserve a mixfix match when they preserve the operator tree:

```zax
a[b] = c
(a[b]) = c
```

They may change another tree deliberately:

```zax
a + b * c   // a + (b * c)
(a + b) * c // a different tree
```

#### Mixfix declaration model

`operator mixfix` is a distinct declaration category. It lists recognized
symbolic or phrase components individually, with exact fixity, rather than
embedding a pseudo-expression containing invented operand names in one string.

Illustrative declaration syntax:

```zax
MyType :: type {
  operator mixfix
    index 1
    binary '='
  final : (
    result : MyResult
  )(
    index : MyIndex,
    rhs : MyValue
  ) writable = {
    // `_` is the receiver operand.
  }
}
```

The exact declaration grammar is not accepted by this example. The lasting
conceptual requirements are:

- each component identifies an already recognized operator spelling and form;
- the component sequence and ordinary fixed precedence determine a tree
  skeleton;
- the receiver hole is implicit in a type-defined declaration;
- all other holes map to explicit prototype parameters in source-evaluation
  order;
- the declaration may have any callable result shape, while nested value use
  requires exactly one usable result; and
- a phrase component eventually uses the phrase-reference mechanism established
  by phrase work.

Component count does not universally equal operand count minus one. Pre/post
unary components add no side operand, and an indexing or splice component may
introduce one or several holes.

Call and index may be named mixfix components. Unlike their direct definitions,
their flattened mixfix use states the number of source holes contributed by that
component:

```zax
CustomCallable :: type {
  // Illustrative declaration syntax.
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

`call 2` contributes the first two input slots; binary `+` contributes the third.
The omitted `:` still counts as the second call slot and is viable only when that
mixfix parameter declares a default. Direct `operator call` needs no count
because its complete prototype belongs to the call. The same distinction applies
to direct `operator index` versus `index 2` in a flattened mixfix.

A consumed call/index component contributes source input holes but no
intermediate call result or index proxy. The complete mixfix owns the final
result shape. Ordinary call/index results matter only when no direct mixfix is
applicable and the component tree decomposes.

Named inputs, omission, and declared defaults conceptually use the mixfix
prototype segment. Exact default timing across a flattened mixfix, variadics,
multiple-result input forwarding, lambda/generated callable types,
construction-like call syntax, slices, and component-local mapping remain
future invocation/indexing integration.

Exact syntax for explicit nondefault skeleton grouping, repeated spellings,
multi-hole components, structural diagnostics, reflection, and generic
substitution remains later refinement.

#### Mixfix matching and mandatory selection

Conceptually, mixfix resolution:

1. parses source with the fixed ordinary operator rules;
2. considers mixfix declarations whose root component matches an expression
   node;
3. structurally matches each fixed component and operand hole;
4. discovers type-defined candidates through the receiver anchor;
5. resolves every hole as an ordinary expression;
6. tests the complete mixfix prototype for viability and availability;
7. uses ordinary partial-order preference among overloads sharing one pattern;
8. diagnoses distinct viable structural patterns that overlap the same tree;
9. selects one complete mixfix operation; or
10. considers ordinary component decomposition only when no direct mixfix is
    applicable.

If exactly one direct mixfix structure matches and has one uniquely selected
viable declaration, it must be used. It is not ambiguous merely because its
component operations could also resolve. The mixfix may deliberately
have different results, rounding, overflow, mutation, allocation, or domain
meaning from executing those components separately.

For example, a declared `*`, `+` mixfix may own:

```zax
a * b + c
```

as one operation. Floating-point rounding or integer overflow may differ from a
separate multiply followed by addition. This is semantic mixfix selection, not an
optimizer claiming the operations are equivalent. Optimization without a
selected mixfix remains required to preserve the established separate
operations.

Distinct mixfix structures matching overlapping parts of the same root are an
ambiguity error. A longer pattern does not automatically win. Declarations with
the same skeleton but different operand types or qualifications form an ordinary
overload set.

A viable bodyless, `forbidden`, unsupported `default`, or otherwise uniquely best
unavailable mixfix blocks decomposition and produces the applicable diagnostic.
If no direct mixfix candidate is viable, ordinary decomposition may be
considered. A future bodyless or `forbidden` declaration can claim the shape
deliberately and prevent that fallback.

#### Mixfix holes, fallbacks, and protected nodes

Written tree structure is matched before operator fallback. Fallback never
rewrites source to manufacture or destroy a mixfix match.

For:

```zax
a + ?c
```

if the mixfix consumes only binary `+`, then `?c` lies inside a right-hand hole
and resolves normally, including the accepted opposite-operator fallback.

If the mixfix skeleton explicitly consumes both binary `+` and pre-unary `?`,
that written `?` is part of the one mixfix and does not resolve independently.
The different source `a + !(!c)` does not match that skeleton.

Signature protection and mixfix-consumption protection are distinct. User code
cannot replace any exact language-owned built-in signature. A user mixfix
anchored on a custom receiver may nevertheless consume an eager protected
operation whose independently visible evaluation has no short-circuit boundary.

The closed symbolic logical core is:

| Operation | Symbolic spelling | Exact Boolean evaluation |
| --- | --- | --- |
| Negation | `!a` | Eager unary negation |
| AND | `a && b` | Skip `b` when `a` is false |
| OR | `a \|\| b` | Skip `b` when `a` is true |
| XOR | `a ^^ b` | Eager |

Exact `?Boolean` is direct Boolean identity and exact `!Boolean` is direct
Boolean negation:

```zax
?true  // true
?false // false
!true  // false
!false // true
```

This avoids fallback cost for the basic Boolean family and does not introduce
integer or pointer truthiness.

Less-familiar extensions remain aligned operation concepts but move to canonical
operator phrases rather than duplicate symbolic aliases. Their exact source
wording remains illustrative until work `009`:

| Operation | Illustrative phrase | Exact Boolean evaluation |
| --- | --- | --- |
| NAND | `a logical nand b` | Skip `b` when `a` is false; result true |
| AND-NOT | `a logical and not b` | Skip `b` when `a` is false |
| NOR | `a logical nor b` | Skip `b` when `a` is true; result false |
| OR-NOT | `a logical or not b` | Skip `b` when `a` is true |
| XNOR | `a logical xnor b` | Eager |

Each operation has one canonical source form. Symbols such as candidate `!&&`,
`&&!`, `!\|\|`, `\|\|!`, and `!^^` are superseded rather than retained as aliases
for the phrase operations.

The exact built-in signatures for the complete operation family are protected.
Only exact `Boolean`/`Boolean` operations whose semantics short-circuit are
mixfix-consumption barriers: symbolic AND/OR and the future phrase NAND,
AND-NOT, NOR, and OR-NOT operations. Exact Boolean XOR and future phrase XNOR are
eager and may be consumed by a custom-receiver mixfix.

Mixed shapes such as `Boolean && MyType` or `MyType || Boolean` are ordinary
eager overloads and may also participate in a key. Result type does not decide
the barrier.

A user mixfix may accept a completed protected short-circuit result inside a
hole:

```zax
custom + (ready && perform())
```

when its skeleton consumes only `+`. The Boolean subtree then retains its
protected left-to-right short-circuit behavior. A concrete mixfix declaration
that consumes one of the six exact Boolean short-circuit forms is invalid, and a
future generic substitution may not capture that protected specialization.

A consumed eager logical operation does not grant custom short-circuiting. Its
mixfix holes evaluate in source order before the body. Delayed/lazy mixfix inputs
would require a separate callable mechanism.

Direct logical compound mutation also exists as an operator-phrase family rather
than a dense symbolic family:

```zax
// Illustrative phrase syntax only.
flag logical and assign rhs
flag logical nand assign rhs
flag logical and not assign rhs
flag logical or assign rhs
flag logical nor assign rhs
flag logical or not assign rhs
flag logical xor assign rhs
flag logical xnor assign rhs
```

For exact Boolean operands, AND/NAND/AND-NOT and OR/NOR/OR-NOT retain the same
short-circuit decisions as their value operations; XOR/XNOR are eager. The
receiver place evaluates once, successful protected operations update it, and
return writable access to the updated Boolean place.

These are direct operations, not mixfix contraction of a value operation and
assignment. Symbolic forms such as `&&=`, `!&&=`, `\|\|=`, or `^^=` are not
canonical aliases. Exact phrase spelling, precedence integration, declarations,
and formatting remain work `009`.

Declaration initialization, constructors, replacement constructors,
destructors, statement composition, and flow/conditional separators are not
ordinary overloadable operator nodes and cannot be consumed by a user mixfix.

#### Mixfix qualifications, ownership, and decomposition

A mixfix receives no capability from its spelling. Receiver and hole types,
place/value/access qualifications, indirection, transfers, constraints, and
future logical capabilities participate in viability.

A mixfix requiring writable receiver access is nonviable through a readonly
path. Mutation still requires mutable value state plus writable access.
Compiler-owned reconstructive replacement remains compiler-owned; a user mixfix
does not acquire that lifecycle skeleton merely by consuming a binary `=` token.

User-defined mixfixes cannot be anchored on a closed intrinsic receiver. A custom
receiver may accept intrinsic values in its other holes.

Global mixfix declarations are disallowed. A mixfix belongs to its receiver
type. A future authorized `partial` type mechanism may permit another source
piece to complete that type with a mixfix, subject to explicit ownership,
coherence, import, conflict, and source-stability rules. Unrelated modules may
not inject a mixfix into a type they do not own under the current model.

Direct indexed mixfix and proxy behavior are distinct:

```zax
value := container[index] // indexed read
container[index] = value  // possible direct indexed-write mixfix
```

The existence of indexed read does not block indexed-write mixfix selection. If
no applicable direct mixfix exists, ordinary decomposition is possible only when
indexed read genuinely returns an assignable place or real proxy. Zax never
synthesizes a hidden proxy.

An explicitly captured proxy is an ordinary real value:

```zax
proxy := container[index]
proxy = value
```

Whether any indexing operation supplies such a value remains indexing work. A
direct indexed operation need not and does not make this source legal:

```zax
proxy : & = container[index]
```

#### Branch-specific mixfix selection

Conditional expressions may select different mixfix behavior on different
paths, reusing the accepted branch-graph model.

When a conditional fills one hole:

```zax
container[index] = (condition ?? first ;; second)
```

the same indexed mixfix structure may select different overloads per arm when
the arm types differ.

A conditional may also change whether a mixfix structure exists:

```zax
result := (condition ?? a * b ;; fallback) + c
```

The true path may select one `*`, `+` mixfix while the false path selects ordinary
binary `+`.

Every path is statically validated. Runtime evaluation:

- evaluates the condition once;
- evaluates only the selected arm;
- preserves once-only evaluation of common operands in their original source
  positions;
- applies direct mixfix/ordinary selection independently on each path; and
- requires every path to converge to one statically usable result shape by the
  complete operation boundary.

An ambiguous or unavailable selection on any possible path is a compile-time
error. User mixfixes do not consume `??` or `;;`; the conditional remains a
language-owned branch boundary across which ordinary branch-specific resolution
operates.

#### Boolean complement fallbacks

Automatic fallback is opt-in by the language for this exact closed list:

| Written operation | Direct counterpart |
| --- | --- |
| `?value` | `!value`, then protected Boolean negation |
| `!value` | `?value`, then protected Boolean negation |
| `a == b` | `a != b`, then protected Boolean negation |
| `a != b` | `a == b`, then protected Boolean negation |
| `a < b` | `a >= b`, then protected Boolean negation |
| `a >= b` | `a < b`, then protected Boolean negation |
| `a <= b` | `a > b`, then protected Boolean negation |
| `a > b` | `a <= b`, then protected Boolean negation |

For every pair:

1. resolve the directly written operation first;
2. an applicable direct candidate wins even if its result later fails a required
   Boolean context;
3. direct ambiguity is an error;
4. a uniquely best unavailable direct operation is an error;
5. only no applicable direct operation activates fallback;
6. resolve the counterpart directly with the same operands in the same order;
7. do not recursively activate the counterpart's fallback;
8. do not filter its candidates to Boolean results;
9. require its normally selected result to be exactly `Boolean`; and
10. apply one protected Boolean negation.

Fallback never swaps operands, introduces a domain-value temporary, combines
several comparisons, or changes receiver discovery. Zax does not derive `<` from
swapped `>`, `<=` from `< || ==`, `&&` from a De Morgan expression, or a
comparison from `<=>`.

Returning `Boolean` does not itself create a fallback relationship. A future
operator phrase pair such as `is`/`is not` must opt in explicitly if its review
establishes the same complement contract.

#### Arithmetic policy families

For fixed-width built-in integer arithmetic, the aligned policy model is:

| Form | Built-in result and failure |
| --- | --- |
| `a + b` | Required mathematical result; panic when unrepresentable |
| `a +? b` | `Integer?`; value when representable, otherwise empty |
| `a +% b` | Always the modular/wrapped result |
| `a +\| b` | Always the saturated result |
| `a +! b` | One anonymous report containing wrapped, saturated, and overflow status |
| `a +!% b` | One anonymous report containing wrapped value and overflow status |
| `a +!\| b` | One anonymous report containing saturated value and overflow status |

Corresponding subtraction and multiplication forms follow the same spelling
pattern, subject to the later complete type/operation table. `+!?` adds no result
different from `+?`, and `+!!` has no coherent meaning; neither belongs to the
family.

Custom overloads may return any result shape and need not reproduce the built-in
policy shape. Following the built-in conventions is recommended for readability
rather than mandatory.

The specialized overflow-report shapes use common field vocabulary:

```zax
// Conceptual shape for +!% and +!|.
<anonymous> :: type {
  value : Integer
  overflowed : Boolean
}
```

For `+!%`, `value` is wrapped. For `+!|`, `value` is saturated.

The combined `+!` shape is:

```zax
<anonymous> :: type {
  wrapped : Integer
  saturated : Integer
  overflowed : Boolean
}
```

These are conceptual anonymous structural results, not accepted anonymous-type
declaration syntax.

The language-provided report type supports:

```zax
if ?report
  useExact(report.wrapped)

if !report
  handleOverflow(report.wrapped)
```

`?report` means no overflow; the accepted opposite fallback makes `!report` mean
overflow. Report fields always exist, so this status operation is not an
optional-presence proof. Custom result types do not acquire the shape or status
operation automatically.

Ordinary member access extracts report fields. No new pre-unary `*`, `%`, `|`,
`~`, or overloadable `.` extraction operation is needed:

```zax
wrappedReport.value
combined.wrapped
combined.saturated
combined.overflowed
```

Exact anonymous structural identity, conversion, layout, member inference, and
reflection remain future structural-type work.

Value, reporting, and compound mutation forms remain distinct:

| Form | Mutates left? | Built-in result |
| --- | --- | --- |
| `a +% b` | No | Wrapped value |
| `a +!% b` | No | Wrapped value plus `overflowed` |
| `a +%= b` | Yes, always writes wrapped value | Writable access to updated `a` |
| `a +!%= b` | Yes, always writes wrapped value | Wrapped value plus `overflowed` |
| `a +\| b` | No | Saturated value |
| `a +!\| b` | No | Saturated value plus `overflowed` |
| `a +\|= b` | Yes, always writes saturated value | Writable access to updated `a` |
| `a +!\|= b` | Yes, always writes saturated value | Saturated value plus `overflowed` |

Optional compound mutation:

```zax
result := destination +?= rhs
```

updates the destination only when the mathematical result is representable.
Success returns present optional writable access to the updated destination;
failure leaves the destination unchanged and returns empty.

Combined reporting compound mutation is also defined:

```zax
report := destination +!= rhs
```

When the exact result is representable, it updates the destination. On overflow,
it leaves the destination unchanged. In both cases it returns the combined
wrapped/saturated/status report. The specialized `+!%=` and `+!|=` forms select
and write their named policy result even on overflow.

Corresponding subtraction and multiplication compounds follow the same pattern:

```text
+=   +?=   +%=   +|=   +!=   +!%=   +!|=
-=   -?=   -%=   -|=   -!=   -!%=   -!|=
*=   *?=   *%=   *|=   *!=   *!%=   *!|=
```

The leading `+` in the first text row is the addition operator spelling, not a
list marker. Every compound is a direct protected built-in operation rather than
fallback through a value operation and `=`.

#### Unary negation policy family

Fixed-width signed unary negation uses the same policy vocabulary:

```zax
-value   // required result; panic if unrepresentable
-%value  // wrapped result
-|value  // saturated result
-?value  // optional result
-!value  // combined report
-!%value // wrapped value plus overflow status
-!|value // saturated value plus overflow status
```

For the minimum signed value, bare negation panics, wrapping produces the same
minimum representation, saturation produces the signed maximum, and optional
negation is empty. Unsigned intrinsic types receive no ordinary unary negation
unless a later reviewed result-type rule establishes one; signedness counterpart
conversion remains pre-unary `+`.

#### Integer division and remainder

Built-in integer division initially provides:

```zax
quotient := a / b
checked : Integer? = a /? b
```

Bare division panics for a zero divisor and for signed minimum divided by `-1`.
Optional division is empty for either failure. Successful signed division
truncates toward zero.

No built-in `/%` or `/|` exists: a zero divisor has no meaningful wrapped or
saturated result. `/!` is not currently defined because `/?` already reports
success through presence. A future reason-distinguishing report could motivate
`/!`, but would need to distinguish division by zero from representability
overflow.

Built-in integer remainder provides:

```zax
remainder := a % b
checked : Integer? = a %? b
```

Bare remainder panics only for a zero divisor. Signed minimum remainder `-1`
produces zero even when a target instruction couples it to overflowing division.
For negative values, quotient and remainder follow familiar C++ behavior:
division truncates toward zero, and a nonzero remainder has the dividend's sign.
No wrapping or saturating remainder forms are needed.

#### Floating-point arithmetic boundary

Floating-point intrinsic domains remain protected and reserved. They receive the
ordinary arithmetic, comparison, unary negation, and magnitude operations later
float review finds meaningful and familiar to C++ programmers. Integer policy,
bitwise, reduction, count, and shift forms remain unavailable unless a future
float review establishes a distinct coherent operation.

Infinity, NaN, payload, rounding, division-by-zero, and fixed representation
behavior remain float work. Raw bit extraction requires an explicit future
conversion or representation operation rather than ordinary bitwise operators.

#### Unary signedness-counterpart family

Pre-unary `+` on a concretely typed integer requests the same-width
opposite-signedness counterpart. It does not negate, compute an absolute value,
or perform general widening/narrowing.

```zax
unsignedValue : U8 = 100
signedValue := +unsignedValue // I8(100)
```

The policy family applies to counterpart conversion:

| Input and form | Result |
| --- | --- |
| `+U8(100)` | `I8(100)` |
| `+U8(200)` | Panic |
| `+%U8(200)` | `I8(-56)` |
| `+\|U8(200)` | `I8(127)` |
| `+?U8(200)` | Empty `I8?` |
| `+!U8(200)` | Wrapped `I8(-56)`, saturated `I8(127)`, `overflowed: true` |
| `+I8(-56)` | Panic |
| `+%I8(-56)` | `U8(200)` |
| `+\|I8(-56)` | `U8(0)` |
| `+?I8(-56)` | Empty `U8?` |
| `+!I8(-56)` | Wrapped `U8(200)`, saturated `U8(0)`, `overflowed: true` |

Equal-width built-in counterparts participate. General width expansion or
contraction remains explicit casting work. Pointer-sized numeric integer
counterparts may participate; pointer values themselves do not.

An integer literal with unary `+` requests unsigned realization:

```zax
a := +55
b := +(55)
```

These forms are equivalent. Grouping may not change type realization, choose
another width, or turn success into panic. Exact width selection, out-of-range
behavior, arbitrary compile-time realization, and explicit literal prefixes
remain future literal work.

Arbitrary but finite fixed-width pairs such as possible future `I137`/`U137`
support counterpart policies if Zax defines them. A truly unbounded mathematical
integer may have a signed/nonnegative counterpart, but signed-to-unsigned
wrapping has no result without a finite modulus. Its protected `%` counterpart
form is therefore unavailable unless the type defines a finite logical extent.

Custom types may eventually advertise a signedness property and counterpart
relationship without requiring separate mutable/immutable-style
implementations. Exact declaration, reflection, generic, layout, and operation
rules remain dedicated future numeric-type-family work.

The legacy claim that all intrinsic compatible conversions require `as` must be
adjusted when this finding is promoted. `as` remains available for general width
or type conversion; signedness counterpart `+` is a deliberate specialized
operation rather than conventional promotion.

#### Bare arithmetic overflow, analysis, and panic

Bare fixed-width built-in arithmetic requires its mathematical result and panics
when that result is unrepresentable. It does not wrap silently, vary between
debug and release behavior, become undefined in ordinary safe code, or require
the programmer to prove safety.

The compiler may remove a runtime check when it proves representability. Optional
analysis may:

- diagnose a definitely overflowing constant;
- warn about a statically evident suspicious range;
- or let a project request stronger unproven-overflow diagnostics.

Such analysis does not change operator semantics. Ordinary runtime input does not
require a proof and should not be warned merely because overflow is theoretically
possible.

Overflow panic produces no result. It remains an unresolved fatal graceful crash
under the current panic model. A future handler may not be assumed to resume the
failed expression with a wrapped value; supplying a replacement result would
require a complete resumable-panic/effect contract covering control transfer,
once-only evaluation, destruction, nesting, and optimization. That question is
deferred to future safety/panic work.

A language-level panic during compile-time execution is not an internal compiler
crash. The compiler catches it at the compile-time evaluation boundary, fails
that evaluation, and reports:

- the operation that panicked;
- its immediate source location;
- relevant operand values when practical;
- the compile-time call/evaluation path;
- whether it occurred during constant realization, reflection, generation, or
  another build-time action; and
- a clear distinction from a compiler implementation failure.

The compiler may continue collecting independent diagnostics where safe, then
fail the build normally. No compile-time panic handler is implied by the current
model.

#### Bitwise Boolean algebra

The closed symbolic bitwise core is:

| Operation | Symbolic spelling | Meaning |
| --- | --- | --- |
| Complement | `~a` | Flip every logical bit |
| AND | `a & b` | `a AND b` |
| OR | `a \| b` | `a OR b` |
| XOR | `a ^ b` | `a XOR b` |
| AND-NOT / clear | `a &~ b` | `a & ~b` |

Less-familiar extended truth-table operations use canonical operator phrases.
Exact wording remains illustrative until work `009`:

| Operation | Illustrative phrase | Meaning |
| --- | --- | --- |
| NAND | `a bitwise nand b` | `~(a & b)` |
| NOR | `a bitwise nor b` | `~(a \| b)` |
| XNOR | `a bitwise xnor b` | `~(a ^ b)` |
| OR-NOT | `a bitwise or not b` | `a \| ~b` |

The symbolic direct compounds are:

```text
&=  |=  ^=  &~=
```

Extended direct compound mutation remains an operator-phrase family:

```zax
// Illustrative phrase syntax only.
value bitwise nand assign rhs
value bitwise nor assign rhs
value bitwise xnor assign rhs
value bitwise or not assign rhs
```

Every direct compound evaluates the receiver place once, evaluates its right
operand once, updates the receiver, and conventionally returns writable access
to the updated place. These are direct operations, not automatic
value-operation-plus-assignment fallbacks.

Candidate symbols `~&`, `~\|`, `~^`, `\|~`, `~&=`, `~\|=`, `~^=`, and `\|~=` are
superseded rather than retained as aliases. Unary complement and bit-reduction
operations have no binary compound form.

The direct symbolic and phrase operations remain first-class operators rather
than implicit mixfix contractions:

```zax
// Illustrative phrase syntax.
a bitwise nand b // one NAND operation
~(a & b)         // binary AND followed by pre-unary complement
```

For custom types the two sources may differ in result type, effects,
qualifications, failure, cost, or availability. A declared mixfix is not assumed
merely because several component operators could express the same mathematical
truth table.

An optimizer may fuse the separate-operation form only when it proves the
programmer-visible result, selected operations, evaluation order, effects,
failure, and cost-relevant semantics remain unchanged. Such optimization does
not select the standalone NAND/NOR/XNOR/clear overload and cannot repair a
programmer's incorrectly written sequence of component bit operations.

Genuinely multi-operand multiword shifts, rotates, funnels, indexed mutations,
and splice operations remain mixfix candidates because their complete source
operation has several operand holes rather than being a shorthand truth-table
identity.

The complete bit-reduction family uses `#` to avoid conflicts with reference/
capture `&` and circumfix `|...|`:

| Operation | Spelling | Built-in result |
| --- | --- | --- |
| Reduction AND | `#&bits` | `Boolean`; true only when every logical bit is one |
| Reduction OR | `#\|bits` | `Boolean`; true when any logical bit is one |
| Reduction XOR / parity | `#^bits` | `Boolean`; true for an odd number of one bits |
| Reduction NAND | `#~&bits` | Protected Boolean negation of reduction AND |
| Reduction NOR | `#~\|bits` | Protected Boolean negation of reduction OR |
| Reduction XNOR | `#~^bits` | Protected Boolean negation of reduction XOR |

Each is one recognized pre-unary token. The earlier candidate `^bits` parity
spelling is superseded by `#^bits` so the whole reduction family remains
systematic. These have no useful logical counterparts for a single Boolean
operand and no in-place forms: reducing a bit sequence to Boolean changes value
shape.

Pre-unary `~=` is the separate shape-preserving in-place complement operation:

```zax
~=getRegisteredValue()
```

For a protected built-in bit sequence it evaluates one mutable, writable place
once, flips every logical bit, writes the same-type result, and returns writable
access to the updated place. A final place may permit this content mutation.
There is no post-unary `value~=` form. Custom receivers may return another
result shape.

#### Shifts, rotates, and counts

The aligned built-in shift family is:

| Operation | Spelling |
| --- | --- |
| Left shift | `value << count` |
| Type-natural right shift | `value >> count` |
| Explicit zero-filling right shift | `value >>> count` |
| Rotate left | `value <<% count` |
| Rotate right | `value >>% count` |

For signed input, `>>` sign-fills; for unsigned input, it zero-fills. `>>>`
zero-fills for both signed and unsigned input. Allowing unsigned `>>>` preserves
explicit zero-fill intent if the left operand later changes signedness; it should
not receive a default redundancy lint.

`<<<` remains a recognized binary spelling available for custom receiver types
but receives no built-in basic-type signature. Its compound spelling is `<<<=`
where a custom type supplies it.

Shift count accepts an ordinary unsigned integer. A nonnegative source literal
may realize directly against that requirement:

```zax
value << 3
```

A typed signed count is not implicitly accepted:

```zax
count : Integer = getCount()
value << count // error: count is signed
```

For logical bit extent `W`, oversized counts have defined nonpanicking behavior:

| Operation | Behavior when `count >= W` |
| --- | --- |
| Left shift | Zero |
| Unsigned/type-logical right shift | Zero |
| Signed arithmetic right shift | All zeros for nonnegative input; all ones for negative input |
| Explicit `>>>` | Zero |
| Rotate | Use `count mod W` |

Bits shifted out by an in-range ordinary shift are discarded. Shifts do not
widen the result; source must cast to a wider type before shifting when wider
retention is desired.

An arbitrary runtime count is valid and should not receive a warning merely
because it might exceed the extent. A constant oversized count may receive an
advisory diagnostic because it can indicate a mistake while retaining the
defined result.

Some targets mask variable counts rather than produce the aligned oversized
shift result. Implementations must preserve Zax semantics and may pay an
additional comparison, selection, or other cost when target behavior differs.

Modulo-count shifts are distinct operator-phrase candidates because symbolic
`<<%`/`>>%` already mean rotation:

```zax
// Illustrative phrase syntax.
value shift left modulo count
value shift right modulo count
value logical shift right modulo count
```

They first reduce the count modulo the logical extent, then perform an ordinary
shift whose outgoing bits are discarded. A separate modulo-count rotate adds no
behavior because rotate counts already use modulo.

The symbolic counting family is:

| Operation | Spelling |
| --- | --- |
| Population count | `#bits` |
| Leading-zero count | `#<bits` |
| Trailing-zero count | `#>bits` |
| Leading-one count | `#~<bits` |
| Trailing-one count | `#~>bits` |

Every spelling is one recognized pre-unary token, not a sequence of adjacent
applications. Signedness does not change the represented-bit count.

First/last set-bit scans differ mainly at the all-zero value: counts can return
the logical extent, while a scan naturally returns no index. The counts determine
set-bit positions for nonzero values, so separate scans are not required in the
symbolic catalog. Candidate operator phrases may provide convenient
optional-index results later.

`#bits` must coexist with standalone contextual result discard. Attached
pre-unary use and a source position that expects an expression distinguish the
count operation. Future discard work must validate the full interaction rather
than assuming the shared character is harmless.

#### Swap

Binary `<<>>` remains the recognized swap spelling:

```zax
left <<>> right
```

Protected built-in swap evaluates both places once from left to right, requires
compatible mutable values through writable access, exchanges them, and returns
zero results. Exact self-swap is a no-op. It is a direct operation rather than a
fallback sequence of moves, copies, assignments, or temporaries.

Custom receivers may define another result shape. Because `<<>>` is
left-associative, a custom mixfix may give an ungrouped swap chain one complete
domain meaning even though zero-result built-in swap cannot participate in an
ordinary decomposed chain.

Complete overlapping-storage, alias, move/copy, failure, and lifetime behavior
requires later lifetime integration.

#### Finite logical bit extent

Bit counts, shifts, rotates, reversal, and related operations require a finite,
defined **logical bit extent**, not necessarily one compile-time fixed physical
width.

- A fixed-width primitive uses its type width.
- A runtime-sized bit vector may use its current logical length.
- A custom finite `I137` may use 137 bits.
- A scalable integer may define a canonical finite magnitude extent.
- Allocation capacity, alignment padding, unused limb space, and other hidden
  representation padding do not automatically participate.

The same numeric value must not gain a different leading-zero result merely
because an allocation retained more capacity. A type must define the finite
logical bit sequence observed by its operators. Operations without meaningful
finite extent remain unavailable.

#### Advanced bit-operation phrase candidates

No bitwise family prefix is added to familiar symbolic operations. Symbolic
forms remain compact and recognizable; language-defined operator phrases are
preferred when no honest symbolic metaphor exists.

Concrete phrase candidates to preserve for future phrase work include:

```zax
// Illustrative phrase syntax only.
reverse bits value
reverse bytes value

value extract masked bits mask
value deposit masked bits mask

first set bit of value
last set bit of value
```

Bit reversal reverses every individual position in the logical extent. Byte
reversal preserves bit order within each byte while reversing byte order.

Masked extraction selects possibly noncontiguous positions and packs their bits
at the low end. Masked deposit scatters low source bits into positions selected
by a possibly noncontiguous mask. These differ from one-bit test/set operations
and contiguous bit-field projection.

These are intended as primitive language-level operator concepts with protected
intrinsic signatures where Zax supplies them. That source preference does not
promise a particular instruction or prevent ordinary optimization of functions.

#### Indexed, splice, and bit-place mixfix candidates

Indexed read, indexed write, and indexed compound mutation may be distinct
complete operations:

```zax
isSet : Boolean = bits[index]
bits[index] = true
bits[index] = false
bits[index] ^= true
```

The write and compound forms may select direct mixfixes:

```text
indexed assignment
|- receiver: bits
|- index
`- rhs
```

They need not produce or capture a semantic proxy. A compiler may retain any
internal representation, but no programmer-visible `BitIndexedInteger`,
anonymous projection type, or reference is implied.

A custom type may define the direct indexed mixfix forms independently of
indexed read. If it deliberately provides a real proxy value through indexed
read, explicit proxy use is ordinary separate source rather than a hidden
requirement of direct mixfix.

Future contiguous splice syntax may select one operation with receiver, low
bound, high bound, and replacement holes:

```zax
// Illustrative future syntax; range/splice syntax is not established.
bits[low ..< high] = replacement
```

The compiler may form one splice-assignment node rather than require a range
value or proxy. Exact bounds, omission, overlap, aliasing, result, custom
declaration, and syntax belong to indexing/splice work.

Representative illustrative code must remain in live raw input if it cannot be
taught as accepted source during promotion.

#### Multiword shift, rotate, and funnel mixfix candidates

Future mixfix/phrase work must preserve distinct candidates for:

- multiword logical left/right shift;
- multiword arithmetic right shift;
- multiword explicit zero-filling right shift;
- multiword rotate left/right;
- funnel shift left/right; and
- in-place forms affecting two writable component places.

Illustrative operator phrases:

```zax
// Illustrative phrase syntax only.
high shift left through low by count
high rotate left through low by count
result funnel right from high and low by count
```

These are single complete operations with three operand holes rather than
three-operand ordinary symbolic operators or implicit proxy chains.

Baseline components have equal logical bit extents. Counts are unsigned.
Ordinary multiword shifts zero-fill or sign-fill at the outer boundary; rotates
wrap across the complete combined extent; funnel shifts select one fixed-width
window from two source components. Count overflow does not panic and follows the
applicable shift or rotate count policy.

Whether signedness must match, whether an operation mutates one or both
components, and whether it returns one component or one anonymous combined
result remain specific signature decisions. Writable multi-place forms must
state qualification and alias constraints explicitly.

#### Closed catalog disposition ledger

This ledger inventories every symbolic form in the required legacy catalog
together with the forms aligned during `008`. It distinguishes recognized
operators from reserved source constructs, future operator work, and superseded
legacy meanings.

##### Aligned ordinary symbolic forms

| Form/family | Recognized spellings | Current disposition |
| --- | --- | --- |
| Truth/status | Pre `?`, pre `!` | Overloadable; protected exact Boolean identity/negation; closed opposite fallback |
| Bit complement | Pre `~` | Overloadable; protected built-in logical-bit complement where available |
| In-place complement | Pre `~=` | Overloadable direct place mutation; legacy binary classification superseded |
| Signedness counterpart | Pre `+`, `+?`, `+%`, `+\|`, `+!`, `+!%`, `+!\|` | Protected equal-width integer counterpart family; custom overloads permitted |
| Signed negation | Pre `-`, `-?`, `-%`, `-\|`, `-!`, `-!%`, `-!\|` | Protected signed-integer family; unavailable for protected unsigned intrinsics |
| Increment | Pre/post `++`, `++?`, `++%`, `++\|`, `++!`, `++!%`, `++!\|` | Direct mutation family with pre/post result distinctions |
| Decrement | Pre/post `--`, `--?`, `--%`, `--\|`, `--!`, `--!%`, `--!\|` | Direct mutation family mirroring increment |
| Count/edge count | Pre `#`, `#<`, `#>`, `#~<`, `#~>` | Population and leading/trailing zero/one counts |
| Bit reduction | Pre `#&`, `#\|`, `#^`, `#~&`, `#~\|`, `#~^` | Protected Boolean reductions for built-in logical bit sequences |
| Add/subtract/multiply | Binary `+`, `-`, `*` with `?`, `%`, `\|`, `!`, `!%`, and `!\|` suffix families | Protected integer value/policy families; custom results unrestricted |
| Add compounds | `+=`, `+?=`, `+%=`, `+\|=`, `+!=`, `+!%=`, `+!\|=` | Direct protected mutation family |
| Subtract compounds | `-=`, `-?=`, `-%=`, `-\|=`, `-!=`, `-!%=`, `-!\|=` | Direct protected mutation family |
| Multiply compounds | `*=`, `*?=`, `*%=`, `*\|=`, `*!=`, `*!%=`, `*!\|=` | Direct protected mutation family |
| Divide | `/`, `/?`, `/=`, `/?=` | Required/optional value and direct compound forms |
| Remainder | `%`, `%?`, `%=`, `%?=` | Required/optional value and direct compound forms |
| Assignment | `=` | Existing-destination operation; protected built-in result convention; generated reconstructive scenario remains compiler-owned |
| Equality | `==`, `!=` | Overloadable; exact built-in signatures protected; closed Boolean complement fallback |
| Ordering | `<`, `<=`, `>`, `>=` | Overloadable; exact built-in signatures protected; closed complement fallback |
| Three-way comparison | `<=>` | Recognized overloadable spelling; closed intrinsic signatures reserved but unavailable until an ordering-result type is reviewed |
| Logical core | `&&`, `\|\|`, `^^` | Symbolic AND/OR short-circuit for exact Boolean; XOR eager; other shapes eager |
| Bitwise core | `&`, `\|`, `^`, `&~` | AND, OR, XOR, and AND-NOT/clear |
| Bitwise compounds | `&=`, `\|=`, `^=`, `&~=` | Direct mutation forms |
| Shift | `<<`, `>>`, `>>>` | Left, type-natural right, and explicit zero-fill right shift |
| Rotate | `<<%`, `>>%` | Left/right rotate with modulo count |
| Shift/rotate compounds | `<<=`, `>>=`, `>>>=`, `<<%=`, `>>%=` | Direct mutation forms |
| Custom-reserved shift-like form | `<<<`, `<<<=` | Recognized overloadable custom forms; no protected basic-type implementation |
| Swap | `<<>>` | Direct left-associative place operation; protected built-ins return zero results |

The binary `>>` spelling retains one fixed precedence. Future composition work
may add a language-generated callable signature for the same operator form; it
does not create another token or parsing rule.

No built-in division/remainder policy forms beyond those listed are recognized
by this catalog. In particular `/!`, `/%`, `/\|`, `%!`, and `%\|` remain future
language additions rather than current custom-overload spellings.

##### Aligned circumfix forms

| Opening/closing | Operation status |
| --- | --- |
| `\|` ... `\|` | Recognized magnitude/absolute form |
| `\|%` ... `\|` | Recognized wrapped magnitude |
| `\|?` ... `\|` | Recognized optional magnitude |
| `\|!` ... `\|` | Recognized combined-report magnitude |
| `\|\|` ... `\|\|` | Recognized norm form; custom overloadable, protected built-in availability type-specific |

Floor, ceiling, arbitrary delimiter pairs, and specialized forms such as
`|!%...|` are not in the current circumfix catalog.

##### Grammar-recognized complete forms

| Form | Disposition |
| --- | --- |
| `operator call` / source `callee(...)` | Overloadable postfix delimited call form; declared functions are implicitly callable |
| `operator index` / source `value[...]` | Overloadable postfix delimited index form |
| `operator mixfix` | Type-owned tree-pattern declaration; no global mixfix |
| `call N` / `index N` mixfix component | Aligned fixed-hole-count component model; exact grammar deferred |
| `condition ?? trueValue ;; falseValue` | Language-owned conditional expression, not an ordinary overloadable binary operator |

Call/index delimiters are not independent pre/post unary operators. Construction
or another language-owned form that happens to use delimiters is not captured by
an ordinary call/index overload merely because the source looks similar.

##### Aligned operation concepts with phrase spelling deferred

The following operation concepts are retained for work `009`. Source shown here
is illustrative, not canonical:

| Family | Operation concepts |
| --- | --- |
| Logical value | `logical nand`, `logical and not`, `logical nor`, `logical or not`, `logical xnor` |
| Logical mutation | Logical AND/NAND/AND-NOT/OR/NOR/OR-NOT/XOR/XNOR assign |
| Bitwise value | `bitwise nand`, `bitwise nor`, `bitwise xnor`, `bitwise or not` |
| Bitwise mutation | Bitwise NAND/NOR/XNOR/OR-NOT assign |
| Magnitude | Saturating magnitude |
| Shift | Left/right/logical-right modulo-count shift |
| Bit transforms | Reverse bits, reverse bytes |
| Mask operations | Masked bit extraction and deposit |
| Scans | First/last set-bit optional-index operations |
| Multiword | Multiword logical/arithmetic/zero-fill shift, rotate, funnel shift, and writable forms |

These concepts have no duplicate symbolic aliases. Phrase work must establish
their exact words, fixity, precedence, declaration form, overloadability,
keyword interaction, and whether a bounded custom phrase facility exists.

##### Deferred legacy operator proposals

| Legacy spelling/family | Disposition |
| --- | --- |
| `@`, `@@`, `@!` | Allocation-policy evidence; defer complete semantics and overloadability to allocation work |
| `\|>` | Invocation chaining evidence; defer with function composition/chaining |
| `->`, `<-` | Result combine/split evidence; defer to invocation and structural-value work |
| Literal prefix forms and quote declarations | Defer to literal parsing, compile-time execution, and literal-operator work |
| Arbitrary words such as `run`, `run fast`, `run fast from` | Preserve as phrase-design evidence; not accepted under the current closed symbolic catalog |
| `as`, `unsafe as`, endian, `outer of`, `lifetime of`, layout/reflection, allocator, and host/target phrases | Preserve for work `009` and their casting, lifetime, reflection, allocation, or metaprogramming owners |
| Function composition using `>>` | Preserve as a future generated signature on the existing binary form |

These deferrals are not current operator availability promises.

##### Superseded or rejected legacy symbolic meanings

| Legacy form | Disposition |
| --- | --- |
| Pre `~\|` parity | Superseded by reduction XOR `#^` |
| Binary `~&` clear | Superseded by symbolic `&~`; bitwise NAND is a future phrase |
| Binary `~=` complement | Reclassified as pre-unary in-place complement |
| Binary `~\|=` parity | Superseded; reduction has no in-place form, bitwise NOR assign is a future phrase |
| Binary `~&=` clear | Superseded by `&~=`; bitwise NAND assign is a future phrase |
| Binary `~^`, `\|~` and their compounds | Superseded candidate symbols; corresponding extended operations use phrases |
| Logical `!&&`, `&&!`, `!\|\|`, `\|\|!`, `!^^` and compounds | Superseded candidate symbols; corresponding extended operations use phrases |
| Legacy `<<<`/`>>>` rotate meaning | `>>>` now means explicit zero-fill right shift; rotate uses `<<%`/`>>%`; `<<<` remains custom-reserved |
| Literal `'`/`"` as generic pre/post unary operators | Rejected classification; quotes belong to literal grammar |
| `()`/`[]` as generic post-unary operators | Reclassified as postfix delimited call/index forms |

##### Reserved source constructs that are not ordinary overloadable operators

| Source form | Current owner/boundary |
| --- | --- |
| Postfix `*`/`&` in type uses; prefix capture `&` | Future pointer/reference/capture grammar |
| `.` member/projection/optional dereference and namespace-like access | Access, optional, and source grammar; not an arbitrary user operator |
| `,` | List, argument, packet, and result mapping structure |
| `;`, `;;` | Statement composition and header/conditional separation |
| `:`, `:=`, `::` | Declaration, label, omission/default, routing, and non-value definition syntax |
| Postfix type `?`, `unsafe ???` | Optional-type and explicit uninitialized-storage syntax |
| `\` | Explicit source continuation |
| `$`, `...`, `$...` | Generic/variadic/metaprogramming syntax evidence |
| `{}`, `[{...}]`, `[[...]]` | Block, construction packet, and directive delimiters |
| `_`, `___` | Current-instance/context forms |
| `+++`, `replacement +++`, `---` | Complete lifecycle declaration/invocation forms |
| `#` in result/omission contexts | Contextual acknowledgement/discard, distinct from attached count/reduction tokens |

This ledger closes the flat legacy symbolic inventory without promoting complete
literal, phrase, allocation, invocation, indexing, pointer, generic, or
metaprogramming domains prematurely.

#### Compound and generated relationships

Distinct ordinary spellings and fixities remain independent operations unless
an explicit reviewed fallback or generation rule relates them.

Protected ordinary assignment for a mutable intrinsic value evaluates the left
place once, evaluates the right operand once, updates the existing left value,
and returns writable access to the updated place:

```zax
updated : Integer writable & = destination = source
```

This result supports familiar right-associated assignment chains. It requires
mutable value state through writable access; a final place may still permit
content mutation. Immutable varying storage remains governed by the separate
compiler-owned reconstructive candidate. Custom `=` overloads may return another
result shape.

In particular:

```zax
counter += delta
counter = counter + delta
```

are not automatically equivalent. The second source performs a value operation,
creates or transfers its result, and independently selects `=`, possibly
including reconstructive replacement. It may introduce different copies,
temporaries, aliases, failure order, allocation, destruction, result shape, and
atomicity.

An indexed destination demonstrates the once-only problem:

```zax
array[nextIndex()] += rhs
```

A general compound fallback would require hidden place retention and a complete
alias/lifetime contract. Built-in primitive types instead receive direct
protected compound signatures, and custom types declare the direct operations
they support.

No general compound-from-value, value-from-compound, increment-from-compound,
swapped-operand, or relational-from-`<=>` fallback is accepted. Future explicit
generation may be reviewed only with exact signatures, once-only evaluation,
qualifications, hidden costs, availability, and result shape.

#### Increment and decrement policy families

Pre/post increment and decrement are direct protected operations rather than
fallback through compound arithmetic.

Bare pre-increment:

```zax
updated := ++value
```

evaluates the place once, computes required-result addition by one, leaves the
destination unchanged and panics on overflow, otherwise writes the result, and
returns writable access to the updated place.

Bare post-increment:

```zax
previous := value++
```

evaluates the place once, snapshots the old value, performs the same
panic-on-overflow transition, and returns the old value by value. The snapshot
is a programmer-visible copy cost. Pre/post decrement mirrors these rules with
required-result subtraction by one.

The complete direct policy families are:

```text
++   ++%   ++|   ++?   ++!   ++!%   ++!|
--   --%   --|   --?   --!   --!%   --!|
```

Every spelling has both pre and post fixity.

Wrapping forms always write the wrapped next value:

```zax
++%value
value++%
```

Saturating forms always write the saturated next value:

```zax
++|value
value++|
```

For ordinary nonreporting pre forms, the result is writable access to the
updated place. Ordinary post forms return the old value snapshot.

Optional forms:

```zax
++?value
value++?
```

update only when the exact next value is representable. Failure leaves the
destination unchanged and returns empty. Pre success returns optional writable
access to the updated place; post success returns an optional old-value snapshot.

Combined reporting forms:

```zax
report := ++!value
report := value++!
```

update to the exact next value on success and leave the destination unchanged on
overflow. They return one transition report:

```zax
<anonymous> :: type {
  previous : Integer
  wrapped : Integer
  saturated : Integer
  overflowed : Boolean
}
```

The `previous` field always records the value before the attempt. On success,
`wrapped` and `saturated` equal the exact value written. On overflow, the
destination remains `previous` and the report exposes both alternatives.

Therefore:

```zax
previous := value++!
```

binds `previous` to the complete report, not directly to the prior integer.
Ordinary `value++` remains the form whose direct result is the prior value.

Specialized reporting forms:

```zax
report := ++!%value
report := value++!%
report := ++!|value
report := value++!|
```

always write the named wrapped or saturated policy result and return:

```zax
<anonymous> :: type {
  previous : Integer
  value : Integer
  overflowed : Boolean
}
```

`value` is the policy result written to the destination. Using the same report
shape for pre and post reporting forms keeps the transition fully visible even
though ordinary pre/post forms return different direct values.

All increment/decrement forms require mutable value state through writable
access. A final place may still permit mutation of its current value. Readonly or
immutable access makes the protected built-in candidate nonviable. Custom
overloads may return another result shape.

#### Source continuation and operator declarations

Current conceptual owners already show declaration headers split after `:`
without `\`, while the source-structure prose does not document declaration
colon as a continuation reason. The aligned correction is narrowly
context-specific rather than a general "incomplete syntax continues" rule.

A declaration colon that requires a type or prototype opens continuation:

```zax
myType :
  MyType = 5

operator pre unary '--' final :
  ()() = {
  }
```

A flow-label, target, result-routing, or other contextual colon does not inherit
this behavior:

```zax
if my_label: // error: label does not continue the header
  init() ;;
  condition {
}
```

That header requires explicit continuation:

```zax
if my_label: \
  init() ;;
  condition {
}
```

Initializer/assignment `=` does not implicitly continue:

```zax
myType : MyType =
  5 // error: `=` does not continue the declaration
```

Explicit continuation may state the intent:

```zax
myType : MyType = \
  5
```

`myType : MyType := 5` remains invalid even on one line because `:=` introduces
an inferred declaration; typed initialization uses `=`.

A body-opening `{` stays on the final physical declaration-header line.
Explicit `\` may not rescue a detached body opener:

```zax
operator pre unary '--' final : ()() =
{ // error: body opener is detached
}
```

`operator mixfix` opens a grammar-delimited component-list continuation region:

```zax
operator mixfix
  index 1
  binary '='
final : (
  result : MyResult
)(
  index : MyIndex,
  rhs : MyValue
) writable = {
}
```

The grammar, not indentation, determines that component specifications continue
until the declaration qualifier/prototype begins. Layout validation requires one
consistent hanging indentation. The open result/input delimiters then permit
their own internal newlines.

Each physical newline still has one sufficient continuation reason. Redundant
`\` inside an open mixfix component list, an open delimiter, or another already
continued form is an intent error.

These findings require a later authorized source-structure owner correction and
validation of other declaration forms. They do not authorize that edit now.

#### Source stability, diagnostics, and visible cost

Tooling and diagnostics must distinguish and expose:

- unrecognized or reserved symbolic spellings;
- unsupported spelling/fixity/arity combinations;
- declaration conflicts with protected intrinsic signatures;
- no discovered candidate, nonviability, ambiguity, and unavailable best;
- zero or several operator results used where one expression value is required;
- expected-result mismatch versus absence of expected context;
- direct versus fallback Boolean selection;
- direct mixfix selection versus ordinary decomposition;
- the exact mixfix tree skeleton and receiver anchor;
- call/index mixfix arity and flattened parameter-segment mismatch;
- same-pattern overload ambiguity versus distinct structural-pattern overlap;
- protected nodes that a user mixfix attempted to consume;
- qualification or constraint failure at a mixfix hole;
- branch-specific paths that differ between mixfix and ordinary operation;
- arithmetic policy and overflow behavior;
- compile-time source panic versus internal compiler failure;
- shift count and logical-extent behavior;
- reduction/count result and finite-extent requirements;
- generated, protected, declared, or mixfix operation origin;
- copies, moves, references, temporaries, allocation, mutation, replacement, and
  destruction;
- eager versus protected short-circuit evaluation;
- once-only operand evaluation;
- and source-layout/continuation errors.

Adding a direct operator or mixfix may change an existing selected operation or
turn decomposition into one mixfix call. Adding a direct `?`/`!` or comparison
counterpart suppresses prior fallback. Adding an overload can create ambiguity.
Changing an operand type can move a logical node between protected
short-circuiting and eager custom behavior. These are visible compatibility
effects, not permission to choose by source order.

#### Future bare source enclosure

`bare{...}` is an aligned candidate and required initiating input for future
operator-phrase/source work, with its complete live record preserved in
[raw bare-source input](../../raw/bare-source.md). It is **not current Zax
syntax**.

The exact contiguous opener matters:

```zax
bare{ ... } // candidate bare source enclosure
bare { ... } // not the same form
```

Bare `bare` is not a keyword. It would gain contextual meaning only as part of
the exact `bare{` construct opener.

The candidate is a non-scoping, keyword-neutral, tree-transparent source
enclosure. Its payload must independently form exactly one complete expression
or one complete effective statement.

For an expression:

```zax
a = b + c * d
a = bare{ b + c } * d
```

the payload `b + c` is independently complete, but the enclosure does not create
a final grouping or mixfix-hole boundary. After payload validation it is
transparent to the surrounding precedence tree, so both forms above produce
`a = b + (c * d)`.

Likewise:

```zax
a = a + bare{ b * c / d }
```

does not isolate its payload from an otherwise matching enclosing mixfix. The
annotated payload participates in the final surrounding tree as though the bare
delimiter were absent.

The payload may not obtain missing syntax from outside:

```zax
c = bare{ a + b + } d // error: incomplete bare payload
```

For one effective statement:

```zax
bare{
  myFunc();
  myOtherFunc()
}
```

the semicolon remains necessary to compose the calls. This is invalid even where
the surrounding context accepts several statements:

```zax
bare{
  myFunc()
  myOtherFunc()
}
// error: two uncomposed statements
```

The same rule applies to flow initialization:

```zax
if bare{
  i : Integer = 0;
  k : Integer = 1
} ;; condition {
}
```

Both bindings belong to the ordinary flow-header scope. The bare closer creates
no scope or destruction boundary. A trailing semicolon remains invalid, and a
missing semicolon does not become block-style statement separation.

Inline and multiline forms have identical semantics:

```zax
bare{ myFunc(); myOtherFunc() }

bare{
  myFunc();
  myOtherFunc()
}
```

The opener permits physical multiline layout but does not change composition,
header separators, continuation, qualification, protection, lifetime, or
diagnostics.

Joiners and partial operations cannot be isolated:

```zax
a = b bare{ ; } c = d
while i : Integer = 0 bare{ ;; } condition {
}
while i : Integer = 1 bare{ + a } ;; condition {
}
```

Each bare payload is incomplete for its surrounding category and therefore
invalid.

Inside:

```zax
a = bare{ if + while } + foo
```

`if` and `while` do not acquire ordinary non-operator keyword roles. They must
resolve as identifiers, permitted operator-phrase components, or another valid
nonkeyword expression role.

The candidate processing model is:

1. recognize `bare{...}` and determine whether its position requires an
   expression or effective statement;
2. parse the payload independently with non-operator keyword roles neutralized
   and recognized confusable forms enabled;
3. require one complete unit of that category;
4. retain lexical-role and confusable-form acknowledgement on its tokens;
5. remove the enclosure as a precedence, tree, hole, scope, and runtime boundary;
6. form the complete surrounding tree normally; and
7. perform ordinary/mixfix selection without weakening any semantic rule.

This mechanism may make the asymmetric saturating-magnitude form expressible:

```zax
// Illustrative future syntax.
saturated := bare{ ||value| }
```

Natural `||value|` remains a confusable-form intent error because it resembles a
missing norm closer. `bare{...}` cannot invent an unrecognized form such as
`|value||`.

Future work must also compare the bare enclosure with a local doubled-quote
phrase-fence candidate:

```zax
value''fast''
''run''value
x ''runs fast'' y
```

Doubled quotes make phrase extent and attachment visible but may collide with
literal tokenization, empty literals, prefixes, and escaping. That collision is
preserved in [raw literal input](../../raw/literal-operators.md).

The intent taxonomy supporting this work distinguishes:

- layout-intent errors;
- operator-attachment intent errors;
- redundant-structure intent errors;
- confusable-form intent errors; and
- keyword-role conflicts.

Work `009` must refine or replace the `bare{...}` candidate, determine phrase
fencing and keyword behavior, and update source structure only after the exact
model is accepted.

#### Promotion maturity and plausible ownership

Promotion must preserve the distinction among settled source, settled operation
concepts with unsettled spelling, and unreviewed legacy input.

Symbolic operations whose exact spelling and behavior complete review in `008`
may be taught as current conceptual design. An operator phrase whose operation
is aligned but whose exact phrase remains work `009` may be preserved only as
clearly illustrative syntax or schematic text:

```zax
// Illustrative operator phrase; exact phrase syntax is not established.
value shift left modulo count
```

Unreviewed legacy phrases, literals, and custom sequences remain evidence:

```zax
run fast value
roman'IX'
```

Polished examples or placement in an operator catalog must not make those forms
canonical before their focused reviews.

Programmer-facing promotion should begin with a compact family menu rather than
the exhaustive contract matrix:

| Family | Representative current or candidate forms |
| --- | --- |
| Arithmetic | `+`, `+?`, `+%`, `+\|`, `+!` and corresponding mutation |
| Comparison | `==`, `!=`, `<`, `<=`, `>`, `>=`, reserved `<=>` |
| Logical | `!`, `&&`, `\|\|`, `^^`, plus extended phrase operations |
| Bitwise | `~`, `&`, `\|`, `^`, `&~`, shifts, rotates, reductions, counts |
| Mutation | Compounds, increment/decrement policies, swap, `~=` |
| Circumfix | Magnitude policies and norm |
| Delimited/specialized | Call, index, mixfix, and future phrase forms |

That teaching route should link to a detailed reference catalog for programmers
who need an exact spelling, fixity, precedence, result, evaluation,
short-circuit, intrinsic-availability, overloadability, compound, fallback,
failure, cost, or maturity lookup.

The exhaustive catalog is a plausible independent reference owner because
"understand the operator model" and "look up one exact operator contract" are
different reader tasks. This is not a requirement to split merely because the
tables are long; the dry run must test whether a separate reference improves
ownership and focused reading without duplicating authority.

Mixfix is large enough to be a plausible independent programmer-facing owner.
It has its own mental model, tree patterns, holes, receiver anchoring,
declarations, mandatory direct selection, structural ambiguity, qualification,
protected-node, branch-path, cost, diagnostic, and source-stability concerns.

A likely documentation-fit proposal is:

- the current operators owner teaches the ordinary symbolic catalog, fixity,
  precedence, ordinary selection, arithmetic, bitwise behavior, fallbacks, and
  the route into mixfix;
- a possible operator-catalog reference owns exhaustive per-form tables while
  the main operator owner retains the teaching model and summary menu;
- a future mixfix owner teaches complete tree-pattern declaration and resolution;
  and
- source structure owns only recognition, whitespace, continuation, and layout.

This is a dry-run candidate rather than an authorized structural decision.
Exact ownership must be tested against all aligned findings during the
pre-promotion documentation-fit dry run.

#### Deferred findings and live destinations

The active work file temporarily owns these captured deferrals. Before work
`008` is archived, each requires a live owner or indexed raw destination with
representative code, activation pressure, and constraints:

1. **Literal realization.** Preserve `+55`, `+(55)`, unsigned intent, exact
   equivalence under grouping, width selection, explicit literal prefixes, and
   out-of-range behavior for future literal work.
2. **Numeric counterpart families.** Preserve custom signedness properties,
   equal-width counterparts, finite/unbounded policy boundaries, reflection,
   generics, and layout for dedicated future numeric-type-family work.
3. **Enums.** Preserve whether enums expose counterpart/bitwise behavior directly
   or require explicit conversion to their underlying type for future enum work.
4. **Function composition and chaining.** Preserve compiler-generated
   prototype-compatible callable results, capture/lifetime/allocation costs,
   `>>`, `|>`, custom reflection analyzers, generics, and protected-callable
   questions in dedicated future raw input.
5. **Operator phrases.** Preserve terminology, illustrative phrase operators,
   fixity, whitespace, nested grouping, longest phrase, overloadability,
   protected forms, possible phrase circumfixes, saturating magnitude,
   modulo-count shifts, the `bare{...}` source-enclosure candidate, doubled-quote
   phrase fencing, intent categories, custom-extension policy, unsafe phrases,
   keyword evolution, imports, and precedence for likely work item `009`; the
   complete bare-source model is live in
   [raw bare-source input](../../raw/bare-source.md).
6. **Anonymous structural reports.** Preserve overflow-report shapes, fields,
   status operations, conversion, layout, identity, and reflection for
   [raw structural typing](../../raw/structural-typing.md).
7. **Partial type extensions.** Preserve the prohibition on global mixfix and the
   possible owner-authorized partial-type route, including coherence and import
   consequences, for dedicated partial-type input.
8. **Call, indexing, and splice integration.** Preserve `operator call`,
   `operator index`, `call N`/`index N` mixfix components, flattened parameter
   segmentation, omission/default ordering, variadics, multiple-result
   forwarding, direct read/write/compound mixfix, lack of an implicit proxy,
   illustrative bit/range forms, bounds, references, aliases, and custom
   declarations for future invocation/array/indexing work.
9. **Discard interaction.** Preserve attached `#bits` and `#`-prefixed reduction
   tokens versus standalone contextual `#` for future discard review.
10. **Panic recovery.** Preserve arithmetic overflow as a concrete nonfatal-use
    pressure without assuming resumable expression semantics in
    [raw safety boundaries](../../raw/safety.md).
11. **Branch-specific cost tooling.** Retain the existing
    [cross-cutting audit](../../raw/cross-cutting-audit.md) destination for per-path
    overload, mixfix, convergence, and short-circuit reporting.
12. **Floating representation access.** Preserve the distinction among numeric
    conversion, bit-preserving reinterpretation, unsafe raw access, NaN payload,
    canonicalization, and byte order for future float/casting work; ordinary
    float bitwise operators remain reserved and unavailable.

Creating or updating dedicated raw files remains a separate discussed and
authorized edit. Until then, this active file is the live record and must not be
archived.

#### Pre-dry-run readiness

The completion pass:

- dispositioned every symbolic spelling in the required legacy catalog;
- replaced superseded candidate symbols with their aligned canonical form or
  deferred phrase operation;
- established one precedence/associativity level or explicit future phrase
  obligation for every retained operation;
- established protected canonical type-family boundaries without pretending
  deferred type semantics are known;
- established operator result routing through the shared zero/one/multiple
  callable model;
- bounded integer arithmetic, magnitude, compounds, increment/decrement,
  division, remainder, shift, rotate, count, reduction, assignment, comparison,
  logical, bitwise, and swap behavior;
- classified call, index, circumfix, and mixfix forms;
- preserved every unreviewed literal, phrase, allocation, composition, pointer,
  generic, floating-representation, and indexing consequence explicitly;
- identified costs, diagnostics, formatting, and source-stability requirements;
  and
- identified plausible owner splits without making a structural decision.

No known semantic question now prevents the pre-promotion documentation-fit dry
run. The dry run must still:

1. test every aligned finding against the complete lasting documentation set;
2. assign every deferral to an existing or proposed live raw owner;
3. verify that the proposed operator, catalog-reference, mixfix, phrase, source,
   invocation, qualifier, construction, flow, and future numeric owners do not
   duplicate or orphan authority;
4. identify every affected current and legacy file;
5. produce the exact proposed structure and promotion change set; and
6. report failure if that wider fit uncovers a new design or ownership question
   requiring maintainer alignment.

### Initial reconstruction preserved for review history

The reconstruction below remains raw candidate analysis. The aligned findings
above supersede it wherever they disagree. Its evidence, alternatives, and
questions remain useful until this work is dispositioned.

### Initial reconstruction review entry point

#### Candidate programmer model

An ordinary symbolic operator is a callable selected by a fixed operator spelling
and the qualified operands in the already formed expression tree.

```zax
sum := left + right
```

The candidate model is:

1. Source structure recognizes a fixed catalog of symbolic spellings and forms an
   expression tree from fixity, precedence, associativity, grouping, and syntax
   context. Types and overloads do not change that tree.
2. The operator form determines the operand positions and candidate-discovery
   domains.
3. Candidate selection reuses function invocation's mapping, viability,
   narrow expected-result boundary, partial-order preference, ambiguity, and
   unavailable-best behavior.
4. Runtime evaluates each written operand once in visible left-to-right order,
   except that an already established protected short-circuit operation may skip
   its right operand.
5. Distinct spellings and fixities are independent operations unless one reviewed
   rule explicitly establishes a fallback or generated relationship.

This keeps parsing stable, makes costs reviewable, and preserves the accepted
fact that an operator may return an arbitrary type:

```zax
sum : WideInteger = left + right
```

The complete typed declaration may select a same-family `WideInteger` result when
the input comparisons are otherwise equal. These forms do not supply that result
context:

```zax
sum := left + right
consume(left + right)
```

An inferred declaration and an outer overloaded call therefore cannot rescue an
operator set that differs only by result.

#### Most important contradiction

The legacy corpus treats the operator surface simultaneously as:

- a flat catalog of recognized punctuation;
- overloadable hooks for forms such as `()` and `[]`;
- non-overloadable syntax using some of the same spellings; and
- an open-ended word-operator facility.

It also assigns binary `>>` both to overloadable right shift and to
non-overloadable function composition. Those meanings ordinarily require
different precedence and cannot be selected by operand type without making
expression-tree formation type-dependent.

The first decision should therefore be whether Zax has a fixed symbolic catalog
whose parse is independent of declarations. This reconstruction recommends
**yes**. Arbitrary symbolic punctuation should not become an operator merely
because a declaration exists. Word, literal, invocation, indexing, allocation,
conversion, and metaprogramming forms should be reviewed as their own bounded
domains instead of being used to keep the ordinary symbolic grammar open.

#### Other immediate holes

1. Current receiver terminology says a type-defined operator has a receiver
   operand. Most legacy examples use that receiver as the unary operand or the
   left binary operand and declare only the remaining operands. One current
   qualifier example instead places two explicit parameters on a type-defined
   binary operator in addition to the receiver. The arity model must be made
   consistent.
2. The new `+!` family says "checked" without deciding what a successful or failed
   expression produces. A same-type result plus panic, an explicit status/result
   carrier, and a proof-required operation are materially different models.
3. The legacy list contains gaps and suspicious entries: it has `|=` and `^=` but
   not `&=`, calls binary `~=` a one's-complement operator, and gives unary parity
   `~|` a binary-looking `~|=` form. These cannot be promoted as a coherent
   bitwise family without review.
4. Pre/post increment and compound assignment are shown throughout current
   examples, but their result, alias, mutation, replacement, and derivation
   behavior is not yet owned.

### Recovered evidence and candidate corrections

#### What current owners already constrain

- Operator results may be arbitrary types.
- Pre-unary `?` and `!` use direct selection first. Ambiguity and an unavailable
  best direct operation are errors; only no applicable direct operation can reach
  the accepted opposite-operator fallback.
- Exact `Boolean && Boolean` and `Boolean || Boolean` are protected,
  language-provided, left-to-right short-circuit operations. Other viable
  `&&`/`||` shapes are ordinary eager overloads and may return arbitrary types.
- A condition validates an already selected exact `Boolean`; it does not provide
  expected-result context.
- A complete explicitly typed declaration may provide narrow expected-result
  context. Result quality is only a tie-breaker after equal receiver and supplied
  input quality.
- Selection separates discovery, mapping, viability, preference, and
  availability. Preference is partial-order dominance, never a score or
  source-order tie-break.
- Receiver, value, access, and indirection qualifications participate in
  viability. Punctuation such as `=`, `+=`, or `++` does not grant conventional
  mutation authority by itself.
- Assignment uses an existing destination. An operator cannot introduce an
  unresolved name.
- Immutable, varying storage reached through writable access has an accepted
  compiler-generated reconstructive `=` candidate. Its lifecycle skeleton is not
  an arbitrary user `=` body, and being generated does not automatically make it
  win.
- Conditional-expression paths may select different overloads while preserving
  once-only evaluation of source preceding the branch. Every path must converge
  by the complete operation boundary.
- Statement composition `;`, header separation `;;`, and conditional separation
  `??` are source constructs, not ordinary overloadable operations.

#### Receiver-arity inconsistency

The legacy type-defined form strongly suggests that the receiver occupies an
operator operand position:

```zax
Counter :: type {
  operator binary '+=' final :
    (result : Counter &)(rhs : Integer) writable = {
    _.value += rhs
    return _
  }
}
```

Here `_` is the left operand and `rhs` is the right operand. A global binary
declaration instead supplies both:

```zax
operator binary '>' final :
  (result : Boolean)(lhs : Integer, rhs : Counter readonly &) = {
  return lhs > rhs.value
}
```

That explains the legacy distinction:

```zax
counter > 5 // may use a Counter type-defined operator
5 > counter // needs a visible global declaration
```

However, the current qualifier owner contains a type-defined binary `=` example
with both `lhs` and `rhs` explicit while also describing `_` as the receiver
operand. Taken literally, that produces three operands for a binary spelling.

The candidate correction is:

| Declaration location and fixity | Receiver operand | Explicit parameters |
| --- | --- | --- |
| Global pre-unary | None | One operand |
| Global post-unary | None | One operand |
| Global binary | None | Left and right operands |
| Type-defined pre-unary | Sole operand | None |
| Type-defined post-unary | Sole operand | None |
| Type-defined binary | Left operand | Right operand only |

A type-defined operator would therefore always act on an instance of the
containing type. An operation that has no receiver uses a global declaration.
This is only a candidate correction; the contradictory current example would
need separate promotion-time correction if the model is aligned.

### Candidate symbolic catalog

The tables below inventory the required legacy sources and current operator
owner. "Candidate" means the spelling deserves review in the ordinary symbolic
catalog, not that its conventional semantics are accepted.

#### Behavior already established at a bounded depth

| Spelling | Form | Current constraint |
| --- | --- | --- |
| `?` | Pre-unary | Ordinary direct selection, then the accepted exactly-`Boolean` opposite-operator fallback when no direct candidate applies |
| `!` | Pre-unary | Ordinary direct selection, then the mirrored fallback |
| `&&` | Binary | Protected short-circuit for exact `Boolean` operands; otherwise ordinary eager overload |
| `\|\|` | Binary | Protected short-circuit for exact `Boolean` operands; otherwise ordinary eager overload |
| `=` | Binary | Ordinary selectable operation on an existing destination, plus the accepted generated reconstructive candidate for its exact qualification scenario |

These accepted constraints do not yet settle their precedence, all protected
signatures, complete discovery domains, or declaration grammar.

#### Ordinary value-operation candidates

| Family | Spellings | Proposed fixity | Evidence or issue |
| --- | --- | --- | --- |
| Sign/arithmetic | `+`, `-` | Pre-unary and binary | Present in the legacy catalog; built-in overflow behavior is unresolved |
| Arithmetic | `*`, `/`, `%` | Binary | Present in the legacy catalog; division by zero and signed minimum divided by `-1` need separate treatment |
| Bitwise complement | `~` | Pre-unary | Clear conventional candidate |
| Bitwise binary | `&`, `^`, `\|`, `~&` | Binary | `~&` is described as bit clear; the family lacks legacy `&=` |
| Shift/rotate | `<<`, `>>`, `<<<`, `>>>` | Binary | `>>` conflicts with legacy function composition |
| Parity | `~\|` | Pre-unary | Legacy intent is recoverable, but the spelling and need are untested |
| Logical exclusive-or | `^^` | Binary | Legacy candidate; exact Boolean and arbitrary overload behavior remain undecided |
| Equality | `==`, `!=` | Binary | Return type remains arbitrary unless a protected signature constrains it |
| Ordering | `<`, `<=`, `>`, `>=`, `<=>` | Binary | Chaining and any relationship to `<=>` remain undecided |

The candidate rule is that a shared spelling with different fixity names
different operations. Pre-unary `-` and binary `-` may have unrelated user
declarations even when built-in numeric types give them familiar related
semantics.

#### Mutation-shaped candidates

| Family | Spellings | Proposed fixity | Immediate issue |
| --- | --- | --- | --- |
| Increment/decrement | `++`, `--` | Pre-unary and post-unary | Pre/post result and old-value copy costs are unowned |
| Arithmetic compound | `+=`, `-=`, `*=`, `/=`, `%=` | Binary | Must not be assumed to rewrite through the corresponding value operation |
| Bitwise compound | `^=`, `\|=`, `<<=`, `>>=`, `<<<=`, `>>>=` | Binary | Legacy omits `&=` |
| Unclear bitwise forms | `~=`, `~\|=`, `~&=` | Binary in the legacy list | Base-operation and arity relationships are inconsistent |
| Swap | `<<>>` | Binary | Requires two place operands, once-only evaluation, alias rules, and a defined result |

This reconstruction recommends adding `&=` to the candidate inventory rather than
silently accepting the legacy omission. It does not recommend accepting `~=`,
`~|=`, or `~&=` until their base operations and arities are explained with
representative use.

#### Arithmetic-policy candidates

The initiating input justifies reserving the following coherent family for
review:

| Policy | Value-operation spellings | Compound spellings | Candidate meaning for fixed-width integers |
| --- | --- | --- | --- |
| Wrapping/modulating | `+%`, `-%`, `*%` | `+%=`, `-%=`, `*%=` | Produce the unique representable residue modulo the type's range cardinality |
| Saturating | `+\|`, `-\|`, `*\|` | `+\|=`, `-\|=`, `*\|=` | Clamp overflow to the maximum and underflow to the minimum representable value |
| Checked | `+!`, `-!`, `*!` | `+!=`, `-!=`, `*!=` | Detect an unrepresentable mathematical result; result and failure shape unresolved |

For an unsigned type, saturating subtraction would clamp underflow to zero. For a
signed type, saturation would clamp toward the applicable minimum or maximum.
Wrapping should be defined mathematically from the type's representable range,
not by promising a backend instruction or a particular signed representation.

The first coherent boundary is addition, subtraction, and multiplication:

- division by zero is not overflow and has no meaningful saturating endpoint;
- signed minimum divided by `-1` is an overflow case, but mixing it with
  division-by-zero argues for a separately reviewed `/!` contract;
- remainder normally has a representable mathematical result even where a target
  instruction couples it to overflowing division;
- invalid or excessive shift counts are not ordinary arithmetic overflow;
- rotations already discard no information in the same sense; and
- floating-point infinity, NaN, and rounding are not fixed-width integer
  overflow.

Candidate unary spellings such as `-%value`, `-|value`, and `-!value` could cover
negation of the minimum signed value, but they should not be accepted merely
because the binary family exists. `++%`, `++|`, and `++!` would multiply the token
surface and should wait until increment semantics are known.

The policy families should initially apply only to built-in fixed-width signed,
unsigned, and pointer-sized integers for which the operation has a defined
representable range. Further review is required for:

- arbitrary-precision compile-time integers, which do not overflow until
  conversion to a bounded type;
- floating-point and decimal/fixed-point types;
- user-defined numeric types and whether a generic numeric concept promises the
  policy meaning;
- constant-expression failure and diagnostic timing; and
- the checked result shape.

Constant evaluation must preserve runtime semantics. Folding `+%` or `+|` may
compute early but cannot change its result. Whatever failure behavior is chosen
for `+!` must also remain the same operation when its operands are constant.

#### Syntax and complete operations that are not ordinary symbolic overloads

| Spelling | Candidate classification |
| --- | --- |
| `;` | Statement composition owned by source structure |
| `;;` | Header/conditional section separator owned by source structure and flow |
| `??` | Conditional-expression separator owned by flow; paired with `;;` |
| `:` / `:=` | Declaration, label, omission, type-default, or routing syntax according to context |
| `::` | Non-value definition syntax |
| `,` | List/mapping separator with source-order consequences |
| `\` | Explicit statement continuation |
| `{` / `}` | Block delimiters |
| `[{` / `}]` | Construction-packet delimiters |
| `[[` / `]]` | Compiler-directive delimiters |
| `#` | Contextual discard/acknowledgement form |
| `_` / `___` | Current-instance and current-context forms, not free operators |
| `unsafe ???` | Explicit uninitialized-storage form |
| `+++` / `replacement +++` / `---` | Complete lifecycle declaration and invocation forms, not ordinary operator hooks |

The complete construction, replacement, or destruction operation must not be
called merely an operator because one of its hooks uses punctuation. In
particular, `replacement +++` is a customization inside compiler-owned
reconstructive replacement; it is not the complete `=` operation.

#### Domain-specific spellings to defer without losing

| Domain | Legacy spellings or forms | Constraint on later work |
| --- | --- | --- |
| Pointer/reference/type syntax | Postfix `*`, postfix `&`, prefix capture `&`, postfix `.`, binary/member `.` | Grammar context must distinguish type formation, access, projection, and any value operation without overload-based parsing |
| Allocation | `@`, `@@`, `@!` | Allocation and construction costs require an allocator owner before overloadability is decided |
| Function composition/chaining | `>>`, `\|>` | `>>` cannot also have a different composition precedence while remaining the shift token; one meaning needs another spelling or one shared precedence |
| Result transformation | `->`, `<-` | Combine/split changes value shape and belongs with invocation/structural-value work |
| Invocation/indexing | `()`, `[]` | Delimiter pairs are source forms; future callable or index customization hooks must be distinguished from the complete operation |
| Literal operators | `'`, `"`, forms such as `roman'VIII'` | Quotes are delimiters and the prefix is name-like; result selection, compile-time execution, and escaping need literal-focused work |
| Variadics/metaprogramming | `$`, `...`, `$...` | Not ordinary symbolic value operators |

Representative literal evidence that must survive deferral:

```zax
value : Byte = roman'IX'
```

This syntax raises tokenization, declaration, expected-result, build-time
execution, and literal-payload questions. Treating either quote as a generic
pre/post unary operator does not answer them.

The legacy word and compound-word inventory is also deferred as one source
domain:

```text
as
unsafe as
as big endian
as little endian
from big endian
from little endian
outer of
lifetime of
unsafe outer of
unsafe copy as
unsafe lifetime of
count of
size of
alignment of
offset of
type of
overhead count of
overhead as
overhead size of
allocator of
is constant
host size of
host alignment of
host offset of
host overhead size of
is host constant
target size of
target alignment of
target offset of
target overhead size of
is target constant
```

These forms cross casting, layout, reflection, allocation, lifetime, and
host-versus-target metaprogramming. The legacy proposal also permits arbitrary
user-defined words such as:

```zax
run fast value
value run fast from 5
```

Accepting arbitrary word operators would make ordinary identifier sequences
depend on visible declarations and longest phrase matching. That is a stronger
source-stability and readability cost than overloading a fixed symbolic token.
The candidate disposition is to preserve these examples for focused word and
metaprogramming review, not to let them determine ordinary symbolic resolution.

### Candidate source formation

#### Fixed recognition before selection

The parser should know every ordinary symbolic token and its permitted fixities
without consulting declarations, operand types, imports, or overload results.

```zax
a + b * c
```

must always form:

```text
a + (b * c)
```

even if only an overload resembling `(a + b) * c` would be viable.

Unknown punctuation should not become an operator through a declaration. A
declaration using an unrecognized spelling or unsupported fixity is a declaration
error.

#### Adjacency and future-token stability

The new suffix families expose a concrete lexical requirement:

```zax
a +! b  // one checked-add token candidate
a + !b  // binary + whose right operand begins with pre-unary !
!!value // two pre-unary ! operations
```

Adding `+!` must not reinterpret source that previously meant `+ !value`, and a
future `!!` token must not steal the currently meaningful two-prefix form used by
the accepted opposite-operator model.

Candidate direction:

- require whitespace around ordinary binary symbolic operators;
- permit no whitespace inside a multi-character operator token;
- recognize the fixed longest token at an operator position;
- permanently reserve known valid adjacent decompositions such as `!!` and `!?`
  from later reinterpretation; and
- reject an otherwise unknown contiguous punctuator rather than guessing from
  visible declarations.

This needs source-structure review before promotion. The current source owner
defines whitespace for `;`, `;;`, and `??`, but not a complete ordinary-operator
spacing rule.

#### Candidate precedence and associativity skeleton

This table is a review scaffold, not a completed precedence decision:

| Higher to lower | Forms | Candidate grouping |
| --- | --- | --- |
| Postfix/source forms | calls, indexing, projection/dereference, post `++`/`--` | Left-to-right chaining |
| Prefix | pre `?`, `!`, `+`, `-`, `~`, `~\|`, `++`, `--` | Right-to-left nesting |
| Multiplicative | `*`, `/`, `%` and corresponding policy forms | Left |
| Additive | `+`, `-` and corresponding policy forms | Left |
| Shift/rotate | `<<`, `>>`, `<<<`, `>>>` | Left |
| Relational | `<`, `<=`, `>`, `>=`, `<=>` | Non-associative without parentheses |
| Equality | `==`, `!=` | Non-associative without parentheses |
| Bitwise AND | `&`, `~&` | Left |
| Bitwise XOR | `^` | Left |
| Bitwise OR | `\|` | Left |
| Logical AND | `&&` | Left, as already required by chained behavior |
| Logical XOR | `^^` | Left |
| Logical OR | `\|\|` | Left, as already required by chained behavior |
| Conditional expression | `condition ?? trueArm ;; falseArm` | Candidate right nesting |
| Assignment/mutation | `=`, compounds, candidate swap | Candidate right association except non-associative swap |

Policy variants inherit the precedence and fixity of their base operation. Their
suffix does not create a new binding tier.

Comparisons are proposed as non-associative because arbitrary operator results
make `a < b < c` an ordinary second comparison on the first result, not a
mathematical chained comparison. Parentheses can request that unusual operation
explicitly:

```zax
result := (a < b) < c
```

The `>>` collision must be resolved before this table can be aligned. A token
cannot be both shift precedence and a separately binding function-composition
form while preserving type-independent parsing.

### Candidate declarations and discovery

#### Declaration forms

Global declarations expose every operand:

```zax
operator pre unary '-' final :
  (result : Vector)(rhs : Vector) = {
  return negate(rhs)
}

operator binary '+' final :
  (result : Vector)(lhs : Vector, rhs : Vector) = {
  return add(lhs, rhs)
}
```

Type-defined declarations use `_` as the sole or left receiver operand:

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

`pre unary`, `post unary`, and `binary` should be required even where the
spelling currently has only one form. The explicit fixity makes declarations
robust if another form is added later.

The examples use the current single-quoted spelling. Legacy evidence also uses
backticks in one declaration. The declaration delimiter itself is unresolved and
must be made consistent with literal syntax; it does not make quote characters
ordinary overloadable operators.

#### Candidate discovery domains

For an ordinary symbolic use, form one discovery set from:

1. protected or otherwise language-provided candidates for that exact spelling
   and fixity;
2. visible global operator declarations, including those made visible by an
   explicit import mechanism; and
3. for unary and binary receiver forms, type-defined declarations on the
   receiver operand's resolved type.

Under the candidate receiver model, the sole unary operand and left binary
operand are receiver positions. The right operand's type does not contribute
type-defined declarations. A reversed operation is global or is written
explicitly in a type that can actually be the receiver.

This left-receiver restriction matches the legacy examples and avoids searching
both operand types, but it still needs maintainer review. Searching both types
would make a type-defined declaration act without its instance as receiver and
would need a new declaration form and preference rule.

Lexical scope and imports determine which global declarations are visible; they
do not rank them. Source, declaration, import, module, and generation order never
break a tie.

Structural compatibility must not make every structurally similar type an
operator-discovery domain. Future structural-typing work may define
substitutability after a declaration is discovered, but widening discovery by
shape would make adding a structural member change distant candidate sets.

#### Resolution pipeline

After the expression tree is fixed:

1. identify spelling, fixity, and receiver/operand positions;
2. discover candidates from the applicable domains;
3. map written operands to receiver and explicit parameter slots;
4. test type, qualification, indirection, transfer, availability, and any
   permitted complete expected-result constraint;
5. compare viable candidates using the shared per-slot partial order;
6. select the unique undominated candidate;
7. diagnose ambiguity when several equal or incomparable candidates remain;
8. diagnose a uniquely best unavailable candidate rather than falling through;
9. apply an operator-specific fallback only when its reviewed trigger is met; and
10. evaluate and invoke the selected operation under its established evaluation
   contract.

The accepted `?`/`!` relationship is deliberately after direct resolution:

```zax
if ?value
  use(value)
```

- an applicable direct `?` wins even if its result later fails the condition's
  exact-`Boolean` requirement;
- ambiguous direct `?` is an error;
- a uniquely best but bodyless, forbidden, or otherwise unavailable direct `?`
  is an unavailable-operation error;
- only no applicable direct `?` attempts the `!` fallback; and
- the normally selected `!` must be unambiguous and exactly `Boolean`.

No generic "try another spelling if this one fails" rule follows from that
specific relationship.

#### Protected operations

Protection is a property of an exact language-provided signature, not of every
use of a spelling. The current example is:

```zax
operator binary '||' final :
  (result : Boolean)(lhs : Boolean, rhs : Boolean) = {
  return replacementLogic(lhs, rhs)
}
// error: conflicts with the protected exact Boolean operation
```

Other operand shapes remain ordinary overloads. Candidate direction:

- protect exact built-in primitive signatures whose behavior is required for
  language meaning;
- reject a conflicting declaration rather than merely ranking it below the
  built-in;
- permit user-defined and mixed-type signatures where the spelling is
  overloadable; and
- require future generic declarations to preserve the protected specialization
  boundary rather than capturing it accidentally.

The exact protected primitive matrix is not yet reconstructed. At minimum it
contains exact `Boolean` `&&` and `||`. Fixed-width arithmetic policy operations
probably also need protected exact primitive signatures if their spellings are
to promise portable wrapping, saturation, or checked behavior.

### Evaluation, mutation, and generated relationships

#### Ordinary evaluation

Candidate ordinary evaluation is:

1. select without executing runtime operands;
2. evaluate the receiver/left operand once;
3. evaluate the right operand once;
4. bind each operand immediately according to the selected qualifications and
   transfer behavior; and
5. invoke the body.

```zax
result := makeLeft() + makeRight()
```

`makeLeft()` completes before `makeRight()`. An optimization may remove
temporaries only when it preserves selection, side effects, transfers, aliases,
lifetimes, and results.

Protected exact `Boolean` `&&` and `||` are the established exception: they
evaluate the left operand first and may skip runtime evaluation of the right.
Ordinary overloads of those spellings evaluate both.

Pre/post unary operations evaluate their one operand or place once. The fixity
selects a distinct operation; it does not itself promise whether the result is
the old value, new value, a reference, or another type.

#### Compound and assignment-shaped operations stay independent

These two sources are not candidate equivalents:

```zax
counter += delta
counter = counter + delta
```

The second source:

- reads `counter` as a right-hand operand;
- selects `+`;
- creates whatever result and temporary that `+` requires;
- selects `=` independently, potentially including reconstructive replacement;
  and
- may expose alias and lifetime behavior absent from a direct `+=`.

The direct `+=` evaluates its destination place once and invokes its own selected
candidate. Its body may mutate, replace, perform unrelated domain work, or return
an arbitrary result when its declaration and qualifications permit that.

Therefore this reconstruction recommends no implicit compound-from-value
fallback and no value-from-compound fallback.

#### Candidate derivation matrix

| Relationship | Candidate disposition | Reason |
| --- | --- | --- |
| `?` from exactly-`Boolean` `!` | Keep accepted automatic fallback | Already bounded by no direct candidate, ordinary opposite selection, and exact result |
| `!` from exactly-`Boolean` `?` | Keep accepted automatic fallback | Mirrored accepted rule |
| `!=` from Boolean `==` plus `!` | No automatic fallback; consider explicit generation later | Result type, direct-candidate stability, and maintenance intent must be explicit |
| `<`, `<=`, `>`, `>=`, `==`, `!=` from `<=>` | No automatic fallback | Requires an accepted ordering-result contract and can add hidden comparisons |
| Pre/post `++` or `--` from compound arithmetic | No automatic fallback | Old/new result, copy cost, and place behavior differ |
| Compound operation from value operation plus `=` | No automatic fallback | Adds a temporary, a second selection, and possible reconstructive replacement |
| Value operation from compound operation | Prohibit implicit derivation | Would require hidden storage and mutation for a value expression |
| Overflow-policy compound from policy value form | No automatic fallback | Same compound-versus-value problem |
| Swapped operands | No automatic fallback | May reverse side effects, conversion quality, receiver ownership, and noncommutative meaning |

An explicit generated form may be worth designing later, possibly reusing the
existing declaration-state vocabulary of body, bodyless, `default`, `existing`,
and `forbidden`. That vocabulary is currently established for lifecycle
operations, not for every arbitrary operator, so this work must not assume the
generalization.

Any future generated relationship must state:

- the exact source and target signatures;
- which operand is the receiver;
- once-only and left-to-right evaluation;
- intermediate construction and destruction;
- copy, move, reference, and alias requirements;
- qualification requirements;
- the exact result shape;
- how the generated candidate participates in preference and availability; and
- what later direct declaration does to existing source.

Writing an ordinary operator body remains the unambiguous way to request a
relationship before such a contract exists.

### The checked-arithmetic decision

`+!` cannot yet be taught as "checked addition" without choosing one of these
materially different programmer models:

1. **Checked panic:** return the arithmetic value on success and panic on
   overflow. This keeps expression shape simple but needs a distinct ordinary
   `+` policy and makes "checked" non-recoverable.
2. **Explicit failure result:** return an optional/error/check-result value or a
   value-plus-status result shape. This supports recovery but depends on unreviewed
   result-carrier or multi-result operator behavior.
3. **Proof-required arithmetic:** accept only when static analysis proves no
   overflow. This is useful in specialized code but does not replace a runtime
   checked operation for dynamic input.

Representative source exposes the difference:

```zax
sum := left +! right
```

Does `sum` have the numeric type, a future `Checked<Number>`-like type, an
optional/error type, or only exist when overflow is statically impossible?

A multi-result candidate would instead need mapping behavior:

```zax
sum:, overflowed: = left +! right
```

Current operator documentation establishes arbitrary result types, but not
whether an operator expression may expose several result slots. This must be
decided before using multi-result shape as the solution.

The ordinary built-in `+` policy is coupled to this choice. Plausible choices
include checked panic, unchecked/undefined overflow, wrapping, a numeric-type
policy, or a configured policy. Leaving ordinary overflow implementation-defined
would conflict with predictable source and constant evaluation; making it
wrapping would duplicate `+%`; making it checked panic would narrow the purpose
of `+!` to recoverable failure or proof.

This is the most important arithmetic decision after the fixed-catalog decision.

### Diagnostics and visible costs

Operator diagnostics should distinguish:

- an unrecognized or reserved spelling;
- a recognized spelling declared with an unsupported fixity or arity;
- a type-defined declaration whose explicit operands conflict with its receiver
  arity;
- an attempted declaration of syntax rather than an overloadable operator;
- a declaration conflicting with a protected exact signature;
- no discovered candidate;
- discovered candidates that are nonviable, with operand and qualification
  reasons;
- an ambiguous undominated set;
- a uniquely best but unavailable candidate;
- an expected-result mismatch versus a context that supplies no expected result;
- an unsupported arithmetic policy for the operand type;
- a checked arithmetic failure according to the eventual selected model;
- a comparison chain requiring parentheses;
- a token-spacing or adjacency error; and
- a branch path whose selected operator or result fails to converge.

Programmers and tooling need to expose:

- which discovery domain supplied the selected declaration;
- whether it is language-provided, declared, or generated;
- whether both operands evaluate or a protected operation may short-circuit;
- operand evaluation and immediate-binding order;
- copies, moves, references, conversions, and temporaries;
- mutation versus reconstructive replacement;
- checked, wrapping, or saturating arithmetic work;
- fallback negation or another explicitly generated operation;
- branch-specific selections; and
- result construction and destruction.

### Source-stability consequences

- Adding an overload may change a selected body or make an existing operation
  ambiguous. Source order cannot mask that change.
- Adding a direct `?` or `!` suppresses a prior opposite-operator fallback.
- Adding a longer symbolic token can reinterpret adjacency unless the lexical
  catalog and spacing rules reserve old decompositions.
- Adding a word operator can reinterpret ordinary identifier sequences if word
  recognition depends on visible declarations.
- Changing an operand type can move `&&` or `||` between protected
  short-circuiting and eager ordinary overload behavior.
- Adding an implicit derivation can silently change "no operation" into generated
  behavior or can compete with a formerly selected declaration.
- Changing precedence is a broad source break even when overload sets do not
  change.
- Changing the ordinary arithmetic overflow policy changes both runtime and
  compile-time results and failures.

These pressures favor a fixed symbolic catalog, explicit generation, protected
primitive signatures, and precedence that never depends on overload discovery.

### Candidate owner boundaries

If later aligned, the likely lasting ownership is:

- **Operators:** fixed ordinary symbolic catalog; fixity and arity; operator
  declarations; receiver mapping; discovery domains; operator-specific use of
  shared callable selection; precedence and associativity; operand evaluation;
  policy arithmetic; compound/mutating behavior; protected signatures; and
  explicit fallback/derivation rules.
- **Source structure:** lexical recognition, punctuation adjacency, ordinary
  operator spacing, longest-token behavior, and layout/continuation interaction.
- **Declarations and bindings:** the operator namespace boundary, declaration
  visibility, and the continued rule that an operator cannot introduce an
  unresolved operand.
- **Qualifiers:** operand and receiver qualification truth. The contradictory
  receiver-arity example will require correction if the candidate receiver model
  is accepted.
- **Function invocation:** shared viability, partial-order preference, narrow
  expected-result selection, ambiguity, unavailable-best behavior, and
  branch-specific callable selection.
- **Construction, replacement, and destruction:** generated reconstructive `=`,
  replacement constructors, lifecycle effects, and hidden temporary/destruction
  costs.
- **Core flow control:** exact-`Boolean` condition validation, selected-arm order,
  and convergence.
- **Future numeric owner:** primitive numeric ranges, ordinary overflow,
  division/remainder edge cases, floating/fixed-point behavior, and conversions.
- **Future literal, word/conversion, indexing, allocation, and metaprogramming
  owners or indexed raw inputs:** the deferred domain-specific spellings above.
- **Vision and build/toolchain work:** required support remains available without
  promising instructions, intrinsics, emitted code, or helper linkage.

### Adjacent findings deliberately deferred

1. **Literal operators.** Preserve `roman'IX'`, expected-result selection, and
   compile-time execution pressure. Reopen when literal parsing and build-time
   evaluation are reviewed. It does not constrain the ordinary symbolic catalog
   beyond keeping quote delimiters out of it.
2. **Word and compound-word operators.** Preserve the complete legacy inventory
   and `run fast from` example. Reopen with contextual keywords, casting,
   reflection, and source stability. Current work should not depend on arbitrary
   word declarations.
3. **Invocation and indexing hooks.** Preserve the claim that `()` and `[]` may be
   customizable, but distinguish delimiter syntax, the complete call/index
   operation, and any hook. Reopen when indexing or callable customization is
   reviewed.
4. **Allocation and pipeline/composition.** Preserve `@`, `@@`, `@!`, `|>`,
   `->`, `<-`, and the `>>` collision. The collision constrains current work:
   `>>` cannot receive two precedence meanings.
5. **Numeric type semantics.** A live numeric owner or indexed raw destination
   will be needed before this item can close if ordinary overflow and policy
   applicability are not fully resolved here.
6. **Generics and structural typing.** Future work must decide how generic
   declarations overlap protected signatures and how structural compatibility
   affects viability without silently widening discovery.
7. **Branch-specific cost tooling.** The existing cross-cutting audit entry
   remains the live destination for showing per-path overload and short-circuit
   behavior.

### Initial decisions that prompted maintainer review

The aligned findings above disposition these initial questions where they reach
a conclusion and identify the remaining review or deferral where they do not.
This list is preserved as reconstruction history rather than the current review
queue.

Review can proceed in this dependency order:

1. Accept, revise, or reject the fixed symbolic-catalog model and the separation
   of domain-specific word/literal/call/index forms.
2. Resolve the `>>` collision and punctuation-adjacency/source-stability rule.
3. Confirm whether a type-defined binary operator uses its receiver as the left
   operand and therefore declares only one explicit operand.
4. Refine the precedence skeleton and comparison chaining.
5. Decide ordinary fixed-width integer overflow and what checked arithmetic
   returns or does on failure.
6. Bound policy suffixes to operations and type families.
7. Decide the protected primitive signature matrix.
8. Confirm that compounds, increments, comparisons, and swapped operands have no
   implicit derivation beyond the already accepted `?`/`!` fallback.
9. Decide whether operators can expose multiple result slots.
10. Review the candidate catalog row by row, including the incomplete bitwise
    compound family and `<<>>`.

The first decision is intentionally narrow. Until token recognition and
expression-tree formation are stable, detailed overload, precedence, and
arithmetic decisions risk being built on incompatible grammars.

## Dispositions and promotion dry run

### Dry-run result

**Pass.** The documentation-fit review found no new language-design
contradiction or ownership hole requiring further conceptual alignment before a
promotion proposal can be reviewed.

This pass does not promote the findings or authorize the proposed structure.
Promotion still requires discussion and alignment on the complete result below,
followed by explicit edit authorization.

The staged state at the start of the dry run contained the complete aligned
working record. This dry-run result is an unstaged review layer above that
boundary.

### Proposed lasting structure

The current [Zax operators](../../../language/operators.md) owner should remain the
primary operator concept owner rather than being replaced. Two cohesive concerns
should split into their own human-developer-facing owners:

| Proposed path | Role | Owns |
| --- | --- | --- |
| `language/operators.md` | Main operator teaching and selection owner | Programmer mental model; operator categories; declarations and receiver mapping; discovery; shared callable-selection application; expected-result boundary; once-only evaluation; direct-before-fallback behavior; protected-signature model; costs, diagnostics, source stability; compact family menu; routes to the exact catalog and mixfix model |
| `language/operator-catalog.md` | Exact operator reference owner | Closed symbolic and circumfix catalogs; canonical phrase-operation concepts whose exact spelling is deferred; fixity; precedence; associativity; per-family evaluation; Boolean fallback matrix; arithmetic policies; bitwise/logical operations; shift/rotate/count/reduction; assignment and compounds; increment/decrement; swap; protected family availability; reserved, unavailable, superseded, and deferred forms |
| `language/mixfix-operators.md` | Mixfix concept owner | Tree skeletons, holes, receiver anchors, declarations, call/index components, structural matching, mandatory direct selection, decomposition, unavailable best, ambiguity, protected barriers, qualifications, branch-specific selection, costs, diagnostics, and source stability |

This split is based on distinct reader tasks and ownership:

- learning how operators select and evaluate;
- looking up one exact operator contract; and
- understanding the separate tree-pattern model of mixfix operations.

The split does not create competing definitions. The main operator owner defines
shared selection and routes exact form questions to the catalog. The catalog
does not restate selection. The mixfix owner depends on both and defines only the
multi-component tree operation.

No `language/operator-phrases.md` should be created during `008` promotion.
Phrase syntax and its complete catalog remain likely work item `009`. Current
owners may preserve aligned phrase-operation requirements and clearly marked
illustrative source without presenting those spellings as accepted.

### Lasting ownership map

| Finding group | Lasting owner | Integration boundary |
| --- | --- | --- |
| Closed symbolic catalog and arbitrary-punctuation rejection | `language/operator-catalog.md` | Source structure owns token recognition and spacing |
| Symbolic/phrase/form/circumfix/mixfix vocabulary | `language/terms.md` | Behavior remains in the applicable operator owner |
| Ordinary declarations, global/type-defined receiver arity, left-receiver discovery | `language/operators.md` | Declarations owns namespace/visibility; qualifiers owns receiver capabilities |
| Shared viability, partial-order preference, narrow result context, unavailable best | `language/function-invocation.md` | Operators applies rather than duplicates the shared model |
| Zero/one/multiple operator results and routing | `language/function-invocation.md` | Catalog records each built-in form's result shape |
| Exact precedence, associativity, grouping-required comparisons, swap grouping | `language/operator-catalog.md` | Source structure owns whitespace and parentheses as source structure |
| Operator whitespace, longest token, adjacent unary grouping | `language/source-structure.md` | Catalog supplies the recognized forms |
| Declaration-colon and mixfix-header continuation | `language/source-structure.md` | Operator/mixfix owners supply declaration examples |
| Protected intrinsic signature policy and required support | `language/operators.md`, summarized in `language/operator-catalog.md` | Vision owns the general required-language-support principle; raw build input retains toolchain pressure |
| Boolean direct operations, short-circuit family, eager XOR, complement fallback | `language/operator-catalog.md` | Operators owns direct-before-fallback selection; flow owns exact-Boolean conditions |
| Arithmetic policy families and anonymous report contracts | `language/operator-catalog.md` | Structural typing owns future anonymous type identity |
| Unary signedness counterpart | `language/operator-catalog.md` | Literal, casting, enum, and numeric-family details remain deferred |
| Division/remainder, shifts, rotates, counts, reductions, magnitude, swap | `language/operator-catalog.md` | Future numeric owners refine currently reserved type families |
| Assignment, compound mutation, increment/decrement | `language/operator-catalog.md` | Qualifiers owns authority; construction owns reconstructive replacement |
| Call/index declaration categories and shared argument/result mapping | `language/function-invocation.md` for call; future indexing owner/raw input for index | Mixfix owns only their use as tree components |
| Mixfix declarations and resolution | `language/mixfix-operators.md` | Operators owns shared callable selection; source structure owns layout |
| Branch-specific mixfix/callable selection | `language/mixfix-operators.md` and `language/function-invocation.md` | Core flow owns condition, selected-arm order, and convergence |
| Lifecycle and qualification barriers | `language/construction-and-destruction.md` and `language/qualifiers.md` | Mixfix owner states the dependency without taking lifecycle ownership |
| Programmer-facing navigation | `index.md` | Repository `README.md` remains unchanged |

### Current-owner integration

The promotion change set must update these existing current owners:

| File | Required promotion work |
| --- | --- |
| `language/operators.md` | Rewrite from the current bounded `?`/`!`/`&&`/`\|\|` scope into the shared operator mental model and compact menu; preserve accepted direct/fallback and short-circuit behavior; route exact forms to the new catalog and mixfix behavior to its owner; remove future-work claims now resolved; remove dependence on the legacy operator page |
| `language/source-structure.md` | Add symbolic whitespace/fixity presentation, longest-token behavior, grouped adjacent unary applications, declaration-colon continuation, mixfix component-list continuation, and associated diagnostics/formatting |
| `language/declarations-and-bindings.md` | Add the operator namespace/declaration boundary, preserve unresolved-name rejection, state protected ordinary assignment's result convention, and route complete selection/catalog behavior |
| `language/qualifiers.md` | Correct the three-operand type-defined binary example, teach receiver/mixfix qualification viability at the owned depth, and preserve reconstructive-replacement boundaries |
| `language/function-invocation.md` | State operator zero/one/multiple result integration, `operator call`, `call N`/`index N` mixfix segmentation at the shared callable depth, default/omission consequences, and branch-specific mixfix selection routes |
| `language/construction-and-destruction.md` | Clarify direct compound/mixfix versus generated reconstructive `=`, assignment result/lifetime boundaries, and prohibition on user mixfix capture of compiler-owned lifecycle skeletons |
| `language/core-flow-control.md` | Update truth-producing summaries for the symbolic logical core and future phrase operations, grouped conceptual `?`/`!` fallback notation, protected short-circuit barriers, and branch-specific mixfix paths |
| `language/terms.md` | Add symbolic operator, operator phrase, operator form, circumfix operator, mixfix operator, operator component, operand hole, and receiver anchor; distinguish complete operations from hooks/components |

[Zax language vision](../../../language/vision.md) already owns the general
protected-required-support boundary and needs no semantic change. It should be
validated against the promoted wording but does not belong in the proposed
changed-file set unless promotion reveals a link or terminology mismatch.

[Zax language principles](../../../language/principles.md) already owns the
applicable clear-intent, trustworthy-order, defensible-preference,
context-boundary, visible-cost, and optimization-preservation principles. No
change is required.

### New owner-document content requirements

#### `language/operators.md`

Promotion should teach in this order:

1. a simple ordinary operator call;
2. fixed tree formation before selection;
3. symbolic, phrase, circumfix, call/index, and mixfix categories;
4. global and receiver declarations;
5. discovery, viability, preference, expected result, and unavailable best;
6. left-to-right once-only evaluation;
7. protected intrinsic domains;
8. direct-before-fallback behavior;
9. a compact operator-family menu;
10. costs, diagnostics, source stability, and routes to detailed owners.

It should retain short concrete examples and move exhaustive tables out.

#### `language/operator-catalog.md`

The catalog should contain:

- one precedence table;
- one exact symbolic/circumfix disposition table;
- per-family sections with valid common examples before reference rows;
- exact built-in result, failure, evaluation, mutation, and short-circuit rules;
- protected-family availability and reserved/unavailable boundaries;
- the complement fallback matrix;
- explicit phrase-operation concepts marked as awaiting phrase syntax;
- rejected/superseded spellings only where needed to prevent likely confusion;
- costs and diagnostics alongside the affected family; and
- a compact final lookup table.

It must not transcribe discovery chronology or the initial reconstruction.

#### `language/mixfix-operators.md`

The mixfix owner should begin with:

```zax
a[b] = c
a * b + c
```

and explain one selected tree operation before declaration syntax. It should then
layer:

- tree skeletons and holes;
- receiver anchoring;
- illustrative `operator mixfix` declarations;
- `call N`/`index N`;
- matching and mandatory direct selection;
- ambiguity and unavailable best;
- hole resolution and fallback;
- short-circuit/lifecycle barriers;
- qualifications and no-global rule;
- conditional paths;
- multiword and indexing candidates;
- costs, diagnostics, and source stability.

Every unsettled declaration or phrase spelling must be marked illustrative
inline.

### Legacy-page disposition

| File | Proposed disposition |
| --- | --- |
| `basics.md` | Preserve the `### Operators` anchor required by historical links, replace the flat operator lists with a legacy-retirement note and links to current operator owners; name unreviewed literal/phrase/allocation subjects as deferred without linking ordinary readers into raw project input |
| `operator.md` | Retire from ordinary navigation and mark historical/audit-only; retain the path because immutable numbered-work records link to it; identify current operator owners and state that literal/phrase evidence was preserved elsewhere |
| `forward.md` | Mark arbitrary phrase and literal forward examples as unaccepted legacy evidence and route future review; do not imply open operator phrases |
| `functions.md` | Mark `>>` composition, capture, and `\|>` chaining as deferred evidence; retain representative code for the new raw destination |
| `casting.md` | Qualify the legacy claim that every intrinsic conversion requires `as`; route signedness-counterpart `+` and phrase/cast boundaries to their owners |
| `arrays.md` | Mark direct index, indexed mutation, splice, and proxy behavior as future indexing input; preserve representative direct-mixfix examples without claiming settled syntax |
| `partial.md` | Record that global mixfix is prohibited and future owner-authorized partial additions require coherence/import review |
| `enums.md` | Mark direct numeric/bitwise inheritance and signedness-counterpart behavior as deferred; do not infer it solely from the underlying integer |
| `discard.md` | Record the future interaction between standalone contextual `#` and attached count/reduction tokens |
| `warnings-errors.md` | Replace the legacy mandatory `shift-count-overflow` warning with the aligned defined oversized-shift behavior and possible constant-only advisory diagnostic; preserve signed-count rejection |
| `compiler-directives.md` | Mark panic-disabling semantics as legacy and unable to change promoted required-result arithmetic without future panic/recovery review |

Untouched legacy examples elsewhere may retain older indentation or unreviewed
operator usage under the existing cross-cutting audit. Promotion must correct
any example it edits and must not perform an unrelated repository-wide style
sweep.

### Raw and deferred ownership proposal

#### New indexed raw inputs

| Proposed path | Material to preserve | Activation pressure |
| --- | --- | --- |
| `project/raw/literal-operators.md` | `+55` unsigned realization, grouping equivalence, width/prefix behavior, roman/custom literal evidence, quote grammar, expected results, compile-time execution | Literal parsing, literal prefix, or compile-time literal operator work |
| `project/raw/numeric-type-families.md` | Custom signedness property/counterpart, finite versus unbounded policy operations, fixed-point/arbitrary-width availability, enum inheritance, float representation access, intrinsic alias identity | Numeric type, enum, cast, generic numeric, fixed-point, arbitrary-width, or float review |
| `project/raw/function-composition-and-chaining.md` | `>>`, `\|>`, generated callable-compatible results, captures, `operator call`, lambda/unnameable types, custom reflection analyzers, protected callable combinations | Function composition, chaining, capture, lambda, generic callable, or reflection work |
| `project/raw/partial-types.md` | Prohibition on global mixfix, possible owner-authorized partial mixfix additions, coherence, imports, conflicts, source stability | Partial/open-type or extension-method work |
| `project/raw/indexing-and-slicing.md` | `operator index`, `index N`, direct index/write/compound mixfix, no implicit proxy, bit indexing, splice/range examples, bounds, references, aliases, multi-index mapping | Array, indexing, slicing, bit-place, range, or proxy review |

Each file must be added to `project/raw/README.md` with future pressure and
retirement criteria.

#### Existing raw inputs to update

| Path | Dry-run disposition |
| --- | --- |
| `project/raw/structural-typing.md` | Add anonymous arithmetic-report shapes, member-name contracts, one-value versus multiple-result distinction, and future conversion/reflection pressure |
| `project/raw/safety.md` | Add required-result arithmetic panic, compile-time panic reporting, and the concrete future question of resumable panic supplying a missing expression result |
| `project/raw/analysis-controls.md` | Add optional strict arithmetic-overflow analysis/lint pressure while preserving that lints do not change operator semantics |
| `project/raw/build-and-dependencies.md` | Generalize required protected support from exact Boolean short-circuit examples to the promoted protected intrinsic operator families without promising lowering |
| `project/raw/cross-cutting-audit.md` | Extend branch-specific selection/cost coverage to mixfix paths; update protected-operation and legacy-contradiction entries after the affected legacy pages are corrected |
| `project/raw/feature-catalog.md` | Disposition operator overloading and bitwise-operation entries to current owners; route literal, phrase, indexing, numeric, and composition remainder to their live destinations |

#### Deferrals that do not need a new raw file

- Operator phrases transfer directly into the fixed initiating input of likely
  work item `009` when `008` closes.
- Local/type/memory-policy discard remains live in `discard.md`.
- General casting phrase behavior remains live in `casting.md` and future
  operator-phrase work.
- Allocation remains live in `memory-allocation.md`,
  `custom-allocators.md`, and the legacy `@`/`@@`/`@!` evidence preserved from
  `basics.md`.
- Optional representation/reset/unwrapping remains live in `optional.md`.
- Exact `Nothing` behavior remains live in `nothing.md`.

### Router and navigation changes

The exact promotion navigation change is:

1. Update `index.md` "Start here" with the main operator model, exact catalog,
   and mixfix routes.
2. Add `language/operator-catalog.md` and
   `language/mixfix-operators.md` to "Current conceptual design."
3. Remove `operator.md` from ordinary legacy navigation after marking the file
   historical/audit-only.
4. Keep `README.md` unchanged; its public/project routing remains correct.
5. Keep `project/README.md` unchanged during promotion; its active-work pointer
   changes only during separately authorized closure and creation of `009`.
6. Update `project/raw/README.md` for every new raw input.

Current programmer-facing owners must not cite `008`, archived work, or raw
files. Project records and legacy historical notes may preserve those links.

### Exact proposed promotion change set

#### Add

- `language/operator-catalog.md`
- `language/mixfix-operators.md`
- `project/raw/literal-operators.md`
- `project/raw/numeric-type-families.md`
- `project/raw/function-composition-and-chaining.md`
- `project/raw/partial-types.md`
- `project/raw/indexing-and-slicing.md`

#### Update current owners and routers

- `language/operators.md`
- `language/source-structure.md`
- `language/declarations-and-bindings.md`
- `language/qualifiers.md`
- `language/function-invocation.md`
- `language/construction-and-destruction.md`
- `language/core-flow-control.md`
- `language/terms.md`
- `index.md`

#### Update live project/raw records

- `project/raw/README.md`
- `project/raw/analysis-controls.md`
- `project/raw/build-and-dependencies.md`
- `project/raw/cross-cutting-audit.md`
- `project/raw/feature-catalog.md`
- `project/raw/safety.md`
- `project/raw/structural-typing.md`

#### Update or retire legacy surfaces

- `basics.md`
- `operator.md`
- `forward.md`
- `functions.md`
- `casting.md`
- `arrays.md`
- `partial.md`
- `enums.md`
- `discard.md`
- `warnings-errors.md`
- `compiler-directives.md`

#### Update the active record

- `project/work/008-operator-catalog-and-resolution.md`

No file should be deleted or moved during promotion. `operator.md` remains as a
historical audit-only path because the immutable initiating input and future
archived work preserve links to it. This is a real link-stability constraint,
not an ordinary-reader compatibility stub.

### Proposed promotion order

One authorized coherent promotion should:

1. add the exact catalog and mixfix owners;
2. rewrite the main operator owner around the new ownership boundaries;
3. integrate source, declaration, qualifier, invocation, construction, flow, and
   terminology dependencies;
4. add and index every new raw destination;
5. update existing raw/audit records;
6. disposition and route legacy evidence;
7. update public navigation;
8. validate the complete current reading path, links, headings, examples,
   maturity wording, and staged/unstaged boundary; and
9. update this work item's disposition record with actual promoted paths and any
   authorized deviations from the dry run.

Promotion, closure, archival, creation of `009`, staging, committing, and pushing
remain separate permissions.

### Dry-run validation

The proposed structure satisfies the dry-run checks:

- every aligned finding has one lasting behavioral owner;
- the main operator model, reference catalog, and mixfix concept have distinct
  reader tasks and do not require duplicate authority;
- current source, declaration, qualifier, invocation, construction, and flow
  owners can absorb their cross-cutting constraints coherently;
- every deferred consequence has an existing or proposed live destination;
- illustrative phrase/index/splice/multiword syntax remains visibly unsettled;
- ordinary public navigation does not route into raw or numbered work;
- immutable historical links justify retaining the retired `operator.md` path;
- no archive material is needed for current meaning;
- the exact changed-file set is enumerated above; and
- no implementation mapping or formal specification is introduced.

**Final dry-run status: PASS.** No new language-design or documentation-ownership
finding requires alignment before the proposed promotion can be discussed. The
structure proposal and complete promotion set themselves still require
maintainer alignment, followed by explicit promotion edit authorization.

### Promotion application record

The language maintainer aligned the dry-run structure and explicitly authorized
promotion on 2026-08-27.

The promotion was applied as one coherent unstaged change set:

- `language/operators.md` now owns the shared teaching and selection model;
- `language/operator-catalog.md` owns exact symbolic/circumfix forms,
  precedence, built-in behavior, and availability;
- `language/mixfix-operators.md` owns tree-pattern operations;
- source, declaration, qualifier, invocation, construction, flow, and
  terminology owners contain their cross-cutting integration;
- five new raw inputs preserve literal, numeric-family, composition/chaining,
  partial-type, and indexing/slicing deferrals;
- existing raw/audit inputs preserve structural reports, safety, analysis,
  required support, branch costs, and feature dispositions;
- public navigation routes readers to the three operator owners;
- the dedicated legacy operator page is historical and audit-only; and
- affected legacy pages now identify current ownership or deferred input without
  presenting unreviewed behavior as current.

One dry-run implementation detail changed without altering ownership:
`basics.md` retains its flat lists as explicitly labeled historical evidence
rather than deleting their contents. This preserves literal, phrase, allocation,
pointer, and metaprogramming provenance while routing current readers to the
operator owners. The section no longer competes as a current catalog.

No implementation mapping, formal specification, archive move, work-item
closure, `009` creation, staging, commit, or push is part of this promotion.
Validation results are recorded after the complete change set is checked.

### Promotion validation record

Promotion validation passed:

- the actual changed-file set matches all 35 paths authorized by the dry run;
- `git diff --check` reports no whitespace errors;
- every local Markdown link target and heading anchor in changed files resolves;
- code fences are balanced;
- the new operator-catalog and mixfix tables have consistent column separators;
- current language owners and public navigation do not route ordinary readers
  into `project/raw/`, active work, or archive material;
- the historical `basics.md#operators` anchor remains available;
- current owners contain no stale claim that operator lookup, precedence, or
  declaration behavior remains wholly future work;
- the staged `008` review boundary remains unchanged and the complete promotion
  is unstaged; and
- an independent skeptical review found no high-confidence semantic,
  ownership, maturity, or routing issue.

**Promotion validation status: PASS.** The authorized findings are incorporated
into lasting owners and every deferred consequence has a live destination.
Conceptual promotion is complete; closure and archival remain separate work.

### Post-promotion correction record

The language maintainer reviewed the promoted operator documentation and
authorized one direct correction on 2026-08-28. A second documentation-fit dry
run was unnecessary because the correction does not change owner boundaries or
the promotion structure.

The correction:

- prunes promoted, superseded, and rejected forms from the retained
  `basics.md#operators` lists while preserving unresolved literal, phrase,
  pointer, allocation, composition, result-shape, and metaprogramming evidence;
- adds concrete declaration/use examples for pre-unary, post-unary, binary,
  circumfix, call, and index forms;
- adds unary and comparison fallback examples;
- defines eager operator, short-circuit operator, protected intrinsic signature,
  mixfix-consumption barrier, and the distinct intent-error categories;
- distinguishes signature ownership from mixfix evaluation barriers;
- defines bounded outward resolution of disjoint nested mixfixes without
  fixed-point rescanning or overlap ordering;
- recognizes asymmetric `||...|` saturating magnitude as a confusable,
  intent-gated form while leaving its explicit source syntax deferred;
- records `bare{...}` as the leading non-scoping, keyword-neutral,
  tree-transparent future source-enclosure candidate in a dedicated indexed raw
  owner;
- preserves doubled single quotes as an alternative future phrase fence and
  records their literal collision pressure; and
- confirms that `bare{` would be one contiguous contextual opener while bare
  `bare` is not a keyword and `bare {` is another or invalid form.

`bare{...}`, doubled-quote phrase fences, and exact asymmetric-circumfix use are
not promoted as current source syntax. They remain captured input for future
operator-phrase/source work, expected to begin with work item `009`.

**Correction validation status: PASS.** The correction layer retains the staged
promotion boundary; local links, anchors, code fences, and operator tables
validate; and an independent focused review found no semantic, maturity, or
routing blocker.
