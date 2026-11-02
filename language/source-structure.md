# Zax source structure

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers reading, writing, or evaluating Zax |
| Applies To | Programmer-facing source structure; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | ASCII identifier character domain; statement-level newlines, explicit, construct-open, comma-list, and trailing-symbolic-infix continuation; effective statements and bodies; semicolon composition; comment and physical-line trivia retained through composition and layout validation; exact two-space structural indentation and physical-tab rejection; symbolic-operator whitespace and adjacency; longest recognized symbolic tokens; grouped separate unary applications; allocation-token/enclosure attachment; application of general tokenization/comment/continuation mechanics to operator phrases and their literal boundary; declaration-colon and mixfix-component-list continuation; the boundary between structural operands and expression continuation; `;;` and `??` separator whitespace; flow-header continuation and the explicit `\` alignment escape hatch; `each` header and named-binding presentation; switch clause-region, case-list, and case-post presentation; exceptional `catch`, forwarding `except`, and outcome-`reshape` clause attachment and layout; brace layout, `else` attachment and layout, body boundaries and the empty-header-block intent error; contextual keyword recognition, postfix `_` keyword-role escape, and strict tree-transparent `bare{...}` neutralization; intent-acknowledgement enclosure boundaries; acknowledgeable/non-acknowledgeable intent-form distinction; mandatory layout validation; diagnostic categories; and comment forms and attachment |
| Does Not Own | Declaration behavior ([declarations and bindings](declarations-and-bindings.md)); enum member-prologue grammar ([enums](enums.md)); allocation semantics ([pointers, allocation, and arenas](pointers-and-arenas.md)); integer realization ([integer literals and realization](integer-literals.md)); literal declarations, payload meaning, merge results, and joining ([literal source and operators](literal-source-and-operators.md)); string/character identities ([strings and characters](strings-and-characters.md)); flow semantics ([core flow control](core-flow-control.md)); exceptional result semantics ([exceptional result flow](except.md)); `using` resource enrollment and disposal ([Zax `using`](using.md)); or operator/phrase interpretation ([operators](operators.md), [operator phrases](operator-phrases.md)) |

## Mental model

Zax uses significant statement-level newlines without making indentation define
the program's structure.

At statement level, an uncontinued newline ends a simple statement. Ordinary
statements therefore need no terminator:

```zax
doA()
doB()
```

Newlines may remain inside an open expression delimiter such as `(` or `[`.
The exact multiline behavior of each expression form belongs with that form,
but ordinary delimited layout does not require an explicit continuation on
every physical line.

Outside an open construct, `\` explicitly continues one statement across a
physical newline. Zax does not infer continuation merely because an operator is
missing an operand.

Grammar and explicit tokens determine structure. Indentation must truthfully
present that structure, but indentation does not create blocks or decide which
body owns a statement.

### ASCII identifiers

Zax identifiers use ASCII spelling. Identifier-specific capitalization and
naming intent belongs to
[declarations and bindings](declarations-and-bindings.md#naming-intent).

Operations derived from identifier spelling, such as enum member lookup that
ignores case, therefore fold ASCII letter case only. They do not depend on a
runtime locale or Unicode-version changes. Complete identifier token grammar is
separate from the statement and layout rules owned here.

## Explicit continuation

`\` suppresses an otherwise significant physical newline:

```zax
value = calculate() \
    + more()
```

The continuation marker must be the last non-comment code token on its line. An
ordinary `//` comment may follow it:

```zax
value = calculate() \ // Explain the continued expression.
    + more()
```

A `///` documentation comment may not follow `\` because the continued
construct is not yet complete.

### Continuation indentation

Every continued physical line must be indented beyond the structural
indentation of the statement being continued:

```zax
value = calculate() \
    + more()
```

The exact expression alignment may vary:

```zax
value = calculate() \
                   + more()
```

This is an error because the line visually presents itself as a new statement
at the surrounding level:

```zax
value = calculate() \
+ more() // error: continuation is not hanging-indented
```

At a nested level, the continuation must be deeper than the continued statement,
not merely deeper than the enclosing control header:

```zax
if condition
  value = calculate() \
    + more()
```

### Continued and continuation-only lines

A continued line may contribute source tokens and continue again:

```zax
value = 1 + 2 \
    + 3 \
    + 4
```

It may also consist only of an appropriately indented `\`. Such a line visibly
carries the continuation across another physical newline:

```zax
value = 1 + 2 \
    \
    + 3
```

A continuation-only line may have an ordinary `//` comment after `\`.

A blank or comment-only line does not carry continuation:

```zax
value = 1 + 2 \

    + 3 // error: blank line broke the continuation
```

```zax
value = 1 + 2 \
    // error: comment-only line does not carry continuation
    + 3
```

The joined physical lines must eventually form one valid statement. A
continuation at end of file is an error. The source before `\` need not be
incomplete in isolation when the joined statement is valid:

```zax
value = 1 + 2 \
    + 3
```

## Symbolic operators, whitespace, and adjacency

The [operator catalog](operator-catalog.md) fixes every recognized symbolic
form. Whitespace presents its fixity:

```zax
isOff := !value        // pre-unary: attached to the operand on its right
previous := value++    // post-unary: attached to the operand on its left
sum := left + right    // binary: whitespace on both sides
```

A recognized symbolic infix operator or multi-part component that is the last
non-comment code token on a physical line establishes continuation when
whitespace before it presents infix use and the form requires a following
operand or section. The newline supplies the right-side whitespace:

```zax
value := first +
  second
```

This applies to assignment/initializer `=`, inferred declaration `:=`,
contained reconstruction `.=` and ordinary symbolic binary operators, plus
symbolic components such as `??` and `;;`:

```zax
result : MyType? =
  condition ??
    presentValue ;;
    (: MyType?)
```

An attached symbol does not present binary continuation, and phrase spacing does
not prove that a phrase expects another physical-line operand:

```zax
value := first+
  second // error: attached `+` did not establish continuation

value := left runs with
  right // error: phrase source did not establish continuation
```

The diagnostic may identify the unexpectedly indented next line rather than
claim that a particular `\` is missing. The error is that no accepted rule
connected the physical lines; explicit continuation is only one possible repair.

Explicit `\` is redundant when the trailing symbolic form already continues:

```zax
value := first + \ // error: `+` already establishes continuation
  second
```

### Longest token and separate unary applications

At an operator position, the longest recognized symbolic token wins:

```zax
a +! b // checked/reporting addition form
a + !b // binary + whose right operand begins with pre-unary !
```

Separate adjacent unary applications require grouping:

```zax
--value    // one pre-decrement token
-(-value)  // two pre-unary negations
!(!value)  // two pre-unary logical negations
```

`!!value` cannot mean two independent `!` applications. This leaves that
contiguous spelling available for future allocation without reinterpreting
existing valid source.

The rule applies to ordinary symbolic unary operations. Call, index, projection,
and other grammar-recognized postfix forms retain their own chaining rules.

### Dot-equals is distinct from postfix access then assignment

`.=` is one longest-match protected binary token at assignment precedence:

```zax
value .= [{ constructorInputs }]
optional .= value
variant.text .= value
```

Whitespace separates postfix payload access followed by ordinary assignment:

```zax
optional. = value
optional. .= [{ replacementInputs }]
```

Postfix `.` first accesses an optional payload; the following `.=` then targets
complete replacement of that payload rather than wrapper-owned contained
reconstruction.

These forms have different lifecycle meaning and a formatter must preserve the
distinction. `.=`, like another binary operator, requires whitespace on both
sides. It cannot be split into postfix `.` and `=` without changing source
meaning.

`.=` may also end a statement-level result-routing group, in the same position
where an `=` group places its `=`:

```zax
#, resultB: kept .= makeResults()
```

This is one routing group whose producer is `makeResults()`. It is not a
`kept .= makeResults()` expression nested inside the second entry. Group
behavior is defined by
[Zax function invocation](function-invocation.md#construct-existing-places-with-a-dot-equals-group).

### Allocation-token attachment

`@`, `@!`, `@<`, and `@!<` are exact longest-match allocation tokens. Their
ordinary source position is a declaration initializer:

```zax
owner : MyValue * unique = @
maybeManual : MyValue * = @!<
```

Any typed pointer destination may also use direct allocation assignment:

```zax
scheduled : MyValue * = @
reset scheduled
scheduled = @{ anotherArena }

ordinary : MyValue *
ordinary = @
```

Semantic analysis uses the destination's pointer role and scheduled/open
contract. An undeclared or otherwise untyped `existing = @` remains invalid.

An allocation-policy enclosure attaches directly to the token:

```zax
owner : MyValue * unique = @{ myArena }
spaced : MyValue * unique = @ { myArena } // error
```

The enclosure permits at most one positional object arena; remaining inputs are
named. Empty compact or spaced enclosures, two positional arenas, and duplicate
named slots are non-acknowledgeable intent errors:

```zax
first : MyValue * unique = @{}  // error: use @
second : MyValue * unique = @{ } // error: use @
third : MyValue * strong =
  @{ myObjectArena, myControlArena } // error: name controlArena
```

Ordinary enclosure layout applies inside a nonempty policy enclosure. A
following construction packet is separate and therefore has whitespace before
it:

```zax
owner : MyValue * strong =
  @{ myArena } [{ endpoint }]
```

A trailing ordinary expression instead supplies an existing allocation for
adoption or missing-metadata allocation:

```zax
prepared : MyValue * unique shareable =
  @ originalValue as last
```

The construction-packet and trailing-source shapes are distinct; source does not
reinterpret one as the other.

Complete allocation semantics and policy inputs are defined by
[Zax pointers, allocation, and arenas](pointers-and-arenas.md#allocation-policy-enclosure).

### Optional layers and empty construction packets

`??` is the conditional-expression token. Two optional type layers therefore
require visible separation:

```zax
single : MyType?
nested : MyType? ?
compact : MyType?? // error: `??` is not two optional markers
```

The space acknowledges two independently qualified wrapper layers and must not
be removed by formatting.

One optional marker attaches directly to the complete qualified inner type:

```zax
plain : MyType?
qualified :
  MyType immutable readonly final? mutable writable varying

spaced :
  MyType immutable readonly final ? mutable writable varying
// error: the optional marker must attach to the inner type layer
```

Whitespace after an earlier optional marker remains required only when another
optional layer follows, as in `MyType? ?`.

`[{}]` is the one contiguous zero-entry
[construction packet](terms.md#construction-packet):

```zax
explicit : MyType = [{}]
```

The spaced form is a confusable-form intent error:

```zax
explicit : MyType = [{ }] // error: blank packet looks like omitted arguments
```

A packet supplies constructor inputs only where a declaration or another
construction boundary establishes its destination. It is not independently an
expression value:

```zax
inner : MyType?

result : MyType? ? =
  condition ?? [{ inner }] ;; (: MyType? ?) // error: packet has no destination

result : MyType? ? =
  condition ?? (: MyType? ? = [{ inner }]) ;; (: MyType? ?)
```

For a variant destination, a dotted packet entry names one alternative rather
than an ordinary direct member:

```zax
choice : MyChoice = [{
  .text = "ready"
}]
```

The packet contains zero such entries for absent default construction or
exactly one for presence. An unmanaged union accepts no construction packet;
its lenses are not constructed members.

For complete reconstructive `value .= [{...}]`, positional and named
constructor inputs are available. Direct `.member = ...` entries are rejected
because the selected `+++ replacement` owns old-member recycling and successor
member establishment.

Complete optional depth and construction behavior is defined by
[Zax optional values](optional-values.md#nested-optionals).

### Array expressions, capture delimiters, and slicing

One bracket pair forms an array expression:

```zax
values := [ 1, 2, 3 ]
matrix := [ [ 1, 2 ], [ 3, 4 ] ]
empty : Integer[0..] = []
oneEmptyRow : Integer[0..][0..] = [ [] ]
```

`[]` is the canonical zero-entry array expression. `[ ]` is a confusable-form
intent error because its blank interior resembles an unfinished nonempty array.
In nested source, `[ [] ]` contributes one empty inner array; it is not another
spelling of a zero-entry outer array.

A contiguous double-bracket opener instead begins lambda capture. `[[]]` is the
canonical zero-capture form:

```zax
emptyCallback := [[]] ()() {
  doSomething()
}

callback := [[ values ]] ()() {
  use(values)
}
```

This is a mandatory intent boundary:

- `[ [` and `] ]` present nested array source;
- `[[]]` presents zero capture;
- a nonempty single-line capture uses `[[ ... ]]` with interior spaces;
- multiline capture uses a newline after `[[` and before `]]`;
- compact nonempty `[[value]]` is a confusable-form error;
- compact nested-array `[[1, 2], [3, 4]]` is a confusable-form error;
- `]]` has capture-closing meaning only in the corresponding capture context;
  and
- adjacent `][` remains ordinary chained indexing.

```zax
matrix[row][column] = replacement
```

An array expression's comma list follows the ordinary delimiter and list rules.
Each ordinary entry contributes one element, including an array or slice value:

```zax
slices := [ firstRow[..], secondRow[..] ]
```

Contextual `from` in an array entry expands an array, slice, or cursor-driven
iterable:

```zax
values := [
  from firstRow,
  from secondRow[1..],
  from generatedValues
]
```

Final bare `..` requests default construction of an exact remaining element
count:

```zax
values : MyItem[5] = [ first, second, .. ]
```

It is not a general expression or open-ended source outside this array-entry
position.

Inside an array expression, `[{...}]` remains one construction packet for one
expected element:

```zax
items : MyItem[2] = [
  [{ "first", 1 }],
  [{ "second", 2 }]
]
```

Postfix brackets containing an index remain index source. A bracket containing
`..` or `..<` forms splice source:

```zax
element := values[index]
inclusive := values[start..end]
halfOpen := values[start..<end]
```

`..<` is the longest recognized token. `start..<` is malformed because the
exclusion marker has no endpoint. Bare and omitted inclusive endpoints remain
contextual inside the splice brackets.

In an array type, `T[0..]` and `T[..]` are equivalent open length ranges.
Inclusive `T[0..0]` permits only zero; half-open `T[0..<0]` permits no length
and cannot declare a value.

Array storage source must preserve an explicit provider instance versus
provider type:

```zax
byInstance : Integer[50] storage provider
byType : Integer[50] storage type MyStorage
both : Integer[50] storage provider type MyStorage
```

One storage clause follows the complete dimension list and applies to the whole
multidimensional array:

```zax
matrix : Integer[4][3] storage type Flat
```

Putting separate storage clauses inside individual dimensions is invalid.

A type alias may retain a storage type requirement but cannot capture a runtime
provider instance:

```zax
MyFlatArray :: alias type Integer[4][3] storage type Flat
```

`storage` precedes its provider instance expression. An optional following
`type MyStorage` explicitly restates the inferred provider type. `storage type`
without an instance supplies only the provider type requirement.

Future storage-contract work may add capability requirements without changing
this single-clause, whole-array attachment.

Dimension descriptors remain written from outermost to innermost. Slicing one
dimension replaces that dimension's count/range with `[]` while preserving
later dimensions:

```zax
myValues : Integer[5][6]
rows := myValues[..]
// Integer[][6]

row := myValues[0][..]
// Integer[]
```

`Integer[][]` instead describes an outer slice whose elements are themselves
slices.

Complete array identity, inference, endpoint normalization, capture meaning,
and operation behavior belongs to
[Zax arrays and slices](arrays-and-slices.md#array-expressions). Complete lambda
capture meaning belongs to
[Zax lambdas and callable composition](lambdas-and-callable-composition.md).

### Compiler-directive enclosure

Contiguous `[[ ... ]]` belongs to lambda capture. A compiler directive instead
uses contiguous `[<` and `>]` delimiters:

```zax
[<deprecate>]
drawLineOld final : ()() = {
}

if condition [<likely>] {
  handleExpectedCase()
}
```

`[<deprecate>]` is metadata about the declaration on the next line, and
`[<likely>]` is metadata about the branch whose header it ends. Neither is an
array, capture, grouped expression, or ordinary `<`/`>` comparison.

The enclosure is recognized only where compiler-directive attachment is
permitted. Its payload must follow the selected directive's own argument
grammar. Several directives may be adjacent:

```zax
[<deprecate>][<inline>]
drawLineOld final : ()() = {
}
```

A directive on its own line attaches to the statement or declaration on the
line directly below it. With a blank line between them, it is unattached. That
physical-line relationship is part of directive attachment, so formatters must
preserve blank lines around directives, and `\` continuation is never used to
express it.

Which names exist, the three attachment positions, ranges, and each directive's
meaning are described by [compiler directives](compiler-directives.md).

### Circumfix attachment

A circumfix opener attaches to the enclosed expression on its right, and its
closer attaches on the left:

```zax
|value|
||vector||
```

An ungrouped recognized opener/closer pair is one operation, not independent
pre/post unary applications. Nested magnitude uses grouping:

```zax
|(|value|)|
```

Exact circumfix forms and availability are defined by the
[operator catalog](operator-catalog.md#circumfix-forms).

## Operator phrase source integration

The complete programmer model for phrase words, interpretation, grouping,
fencing, and phrase-specific presentation is owned by
[Zax operator phrases](operator-phrases.md). This section records how the general
source machinery owned here applies to that feature.

### Phrase words and selected presentation

A phrase word is an ASCII `[a-z][a-z0-9]*` token. After phrase selection, each
multiword component must contain exactly one ASCII space between words and remain
on one physical line:

```zax
chickens cluck loudly    // valid when this phrase is uniquely selected
chickens cluck  loudly   // error: phrase-whitespace intent
chickens cluck \
    loudly               // error: selected phrase spans physical lines
```

Comments, doubled spaces, and explicit continuation remain source trivia while
candidate trees are formed. They do not remove a competing phrase reading.
Only after one interpretation survives does this document's physical-line and
trivia validation report a phrase-presentation error. An uncontinued newline is
different because it already ends the logical statement.

The complete ambiguity and presentation examples are in
[operator phrases](operator-phrases.md#presentation-confirms-a-selection-it-never-makes-one).

### Phrase fences and literal coordination

An attached single quote starts a literal payload; a whitespace-separated single
quote starts an exact phrase fence:

```zax
"ordinary text" // ordinary unprefixed literal
h'DEADBEEF'     // attached prefixed literal
foo 'bar'       // whitespace-separated phrase fence
```

Every fenced word follows the phrase-word token rule. The fence permits no
escapes or leading/trailing spaces. A whitespace-separated single-quoted payload
is a phrase fence rather than an unprefixed literal, and `''` is an invalid empty
fence.

The phrase feature owns what fencing does to candidate interpretations; see
[exact phrase fencing](operator-phrases.md#exact-phrase-fencing). Complete
unprefixed integer realization is defined by
[Zax integer literals and realization](integer-literals.md). Prefixed/custom
literal catalogs, payload behavior, and compile-time execution are defined by
[Zax literal source and literal operators](literal-source-and-operators.md).
Neither may reinterpret this attachment boundary.

### Quoted literal source

An attached literal payload may use either quote kind:

```zax
s'my "quoted" text'
s"single quote: '"
```

Before a literal operator runs, the source scanner:

- requires well-formed UTF-8;
- rejects physically present C0 controls `U+0000..U+001F`;
- treats the matching quote as the terminator;
- performs no escape processing or Unicode normalization; and
- preserves physical spans for diagnostics and source reflection.

The other quote remains an ordinary payload character. A backslash likewise has
no lexical escape meaning. A literal operator such as `c` may interpret those
characters after source scanning.

Adjacent segments that independently resolve to one literal declaration
implicitly merge:

```zax
myText := s"contains '" s' and "'
```

`<|>` is the optional explicit spelling:

```zax
myText := s"contains '" <|> s' and "'
```

Both forms bind before ordinary expression precedence and require every prefix
to resolve independently to one declaration. Different declarations cannot be
adjacent:

```zax
x'first' y'second' // error: different literal declarations
```

Grouping can surround a complete merge but cannot turn an expression back into
source:

```zax
use((x'a' x'b'))
(x'a') <|> x'b' // error: left side is already an expression
```

Implicit merge does not continue a physical line:

```zax
myValue := x'first ' \
  x'second'
```

A trailing explicit `<|>` establishes symbolic-infix continuation, so `\` on
the same newline is redundant:

```zax
myValue := x'first ' <|>
  x'second'
```

An empty segment participating in implicit or explicit merge is a
non-acknowledgeable redundant-structure error. Complete merge,
declaration-identity, joining, and result behavior belongs to the literal owner.

### Contextual keywords and enclosure boundaries

The contextual-keyword machinery owned here considers keyword roles only where
their constructs are grammatically permitted. Phrase words may use the same
spellings; complete phrase interpretation and ambiguity behavior are defined by
[operator phrases](operator-phrases.md#keyword-words-in-phrase-roles).

A phrase component cannot cross a hard statement boundary. It also cannot span a
source enclosure whose payload must independently form a complete expression or
effective statement. General statement, continuation, delimiter, and enclosure
recognition happen before the phrase owner applies candidate pruning.

### Formatter and source-reflection impact

A formatter must retain comments, spaces, physical lines, and fences until phrase
selection completes. It may then normalize one selected phrase to its exact
single-space presentation, but it may not add or remove a fence or turn ambiguity
into another meaning.

Source reflection and documentation must retain enough physical trivia to
reproduce an explicit phrase fence even though the fence creates no final
expression-tree node. Complete source-reflection representation remains future
work.

## Comma-list continuation

A recognized comma separator has no whitespace before it and requires whitespace
after it. A newline satisfies the following-whitespace requirement:

```zax
consume(first, second)
consume(first,
  second)

// consume(first,second) // error: no whitespace after comma
// consume(first , second) // error: whitespace before comma
```

This is a mandatory separator-intent rule rather than a formatting lint. The
comma visibly completes the previous entry and separates the next. It does not
conflict with a completed post-unary operand:

```zax
consume(value--, nextValue)
```

A comma already recognized as a separator in a comma-list implicitly continues
that list across the following physical newline:

```zax
return number: first:,
  text: second: = produce()

switch value {
  case firstCandidate,
    secondCandidate,
    thirdCandidate {
    handleCandidate()
  }
}

using resources: (
  resource := acquireResource(),
  grant := resource.acquireGrant(),
  sharedService
) {
  use(resource, grant, sharedService)
}
```

This is a narrow list rule, not general continuation for incomplete
expressions:

```zax
value := first +
  second // error: `+` does not continue the statement
```

The continuation line must be hanging-indented beyond the structural indentation
of the statement:

```zax
return number: first:,
text: second: = produce() // error: continuation is not hanging-indented
```

A blank or comment-only physical line breaks comma-list continuation. A
continuation-only `\` line may carry the list across one additional newline:

```zax
return \
  number: first:,
  \
  text: second: = produce()
```

### One continuation reason per newline

Each continued physical newline has one sufficient reason:

1. an open delimiter permits the newline;
2. a recognized comma-list separator continues it; or
3. a recognized construct such as a declaration colon or mixfix component list
   opens continuation;
4. a trailing recognized symbolic infix form requires a right operand or
   section; or
5. `\` explicitly suppresses an otherwise significant newline.

An explicit `\` is an error when another rule already continues that same
newline:

```zax
return \
  number: first:, \ // error: the comma already continues the list
  text: second: = produce()
```

```zax
consume(
  first, \ // error: the open `(` already permits the newline
  second
)
```

```zax
value = first + \ // error: `+` already continues the expression
  second
```

The explicit marker is not harmless defensive punctuation. Redundancy makes
source intent contradictory.

When a list begins on the line after an otherwise complete construct such as a
bare `return`, the first newline still needs explicit continuation:

```zax
return \
  number: first:,
  text: second: = produce()
```

Once the list exists, each comma carries its following newline.

### Grouping and list contexts

Grouping parentheses require their contents to form one expression. They do not
preserve or introduce multiple-result mapping:

```zax
return (produce()) // one expression
```

Call argument delimiters, construction packets, and naked return-result lists
are distinct syntactic contexts that may accept their applicable mapping forms.
When one routing entry names both sides, the source-result selector is written
before the destination selector:

```zax
return sourceResult: outerResult: = produce()
```

Within a function input or output routing entry, a bare destination `:` selects
the current positional destination, `: Type` introduces an anonymous typed
destination, and `#` supplies no destination. A `using` list has no predeclared
parameter or outer-result slots: there, bare `:` introduces an anonymous enrolled
destination and `#` introduces an anonymous enrolled destination that suppresses
disposal. Outside result mapping, an ordinary anonymous declaration may use
either a missing name or `#`; those declaration forms do not acquire routing
behavior merely because they share these tokens.

Complete expression-versus-result-mapping behavior is defined by
[Zax function invocation](function-invocation.md#expression-mode-and-result-mapping-mode).

The mandatory parentheses after `using` likewise delimit a mapping-capable
resource list rather than grouping one expression:

```zax
using (acquirePair()) {
  // Every result is enrolled.
}

using ((acquirePair())) {
  // The inner parentheses require one expression value.
}
```

The first form preserves a bare result sequence. Complete enrollment behavior is
owned by [Zax `using`](using.md#multiple-results-and-names).

## Exceptional outcome clauses

`catch` and forwarding `except` are contextual postfix clauses on one complete
invocation. They cover the receiver, argument evaluation, nested invocations,
and selected outer callable represented by that invocation. Outcome `reshape`
may qualify that same invocation or a complete callable-valued expression whose
visible outcome contract is being adapted.

```zax
value := operation() catch failure {
  return
}

value := operation() except failure: outerFailure:

value := operation() reshape failure: localFailure:
```

The first clause remains on the invocation's logical statement. `catch` always
uses a braced effective body whose `{` is on the clause header's final physical
line. A following clause attaches on the same physical line as the preceding
handler's `}`:

```zax
value := operation() catch firstFailure {
  return
} catch secondFailure {
  return
} except thirdFailure: outerFailure:
```

The braced handler owns its ordinary body scope. A source-to-destination pair
uses adjacent result labels:

```zax
catch failure: localFailure: {
  return
}
```

A typed destination keeps declaration spacing:

```zax
catch failure: report : MyFailureReport {
  return
}
```

A discarded payload destination uses `#` and still handles the outcome:

```zax
catch failure: # {
  return
}
```

Same-name handling and forwarding omit the destination label:

```zax
catch failure {
  return
}

except failure
```

Outcome reshape accepts one inline source/destination mapping or one named or
local anonymous `reshape` declaration:

```zax
makeValue() reshape failure: makeFailure:
makeValue() reshape MyFailureNames
parse reshape MyFailureNames

makeValue() reshape {
  value: producedValue:
  failure: makeFailure:
}
```

The inline source form does not create an anonymous structural value or invoke a
runtime operator. A reshape body writes one mapping per declaration and never
uses commas. One application cannot list several inline mapping pairs; use a
named or anonymous reshape declaration instead. Complete no-storage mapping and
outcome behavior is owned by
[structural shapes and compatibility](structural-shapes-and-compatibility.md#outcome-reshape)
and [exceptional result flow](except.md#reshape-an-exposed-outcome-label).

Inside an open call, packet, or `using` delimiter, ordinary delimiter layout
permits multiline source. Outside an open construct, moving an outcome clause to
another physical line requires the ordinary explicit `\` continuation; the
completed invocation alone does not continue its statement:

```zax
value := operation() \
  catch failure {
    return
  }
```

Grouping fixes the invocation or expression that a clause follows. A handler on
an outer invocation can receive outcomes exposed by nested calls; a handler
written directly on a nested call handles that outcome before outer invocation
begins.

Formatters preserve clause order, source/destination label order, handler
bodies, explicit continuation, and grouping. They do not move a clause across
an invocation or add/remove a reshape.

## Statements, blocks, and bodies

Zax distinguishes these source forms:

- A **simple statement** performs one ordinary statement-level operation.
- A **composed statement** combines complete statements with `;`.
- A **block statement** contains statements within `{` and `}`.
- A **flow-control body** consumes one effective statement, which may be simple,
  composed, or a block.

```zax
if condition
  doA()
```

```zax
if condition
  doA();
  doB()
```

```zax
if condition {
  doA()
  doB()
}
```

A flow-control clause establishes a scope around its complete body regardless
of which source form supplies that body. Names introduced inside the body do not
escape after the clause. Operands of a composed body share the clause scope, so
a name introduced by an earlier operand may be used by a later operand.

The exact relationship between a clause scope and a braced block's scope,
including destruction and scope-exit behavior, is owned by
[core flow control](core-flow-control.md) and
[construction, replacement, and destruction](construction-and-destruction.md).
Braces do not make body-local names escape.

An identifier followed by `#`, with no `:` or `:=`, is one complete statement.
It acknowledges a binding that is already in scope:

```zax
value #
```

`value # : Type` is the declaration form, which introduces the binding and
permits this scope to leave it unread. Both meanings are taught by
[declarations and bindings](declarations-and-bindings.md#names-the-body-may-leave-unread).

## Semicolon composition

`;` is a statement-composition operator, not a statement terminator. It requires
a complete statement on both sides and produces one effective statement.

`;` attaches to the complete statement on its left and requires whitespace on its
right. Whitespace before `;`, or no whitespace after it, is a deliberate intent
error:

```zax
foo(); bar()

foo();
bar()

// foo() ; bar() // error: whitespace before ;
// foo();bar()   // error: no whitespace after ;
```

The doubled `;;` header-section separator and the `??` conditional-expression
separator are distinct tokens with their own surrounding-whitespace rule; see
[Header sections and separators](#header-sections-and-separators).

### Horizontal composition

Several statements may be composed on one physical line:

```zax
doA(); doB(); doC()
```

The final statement has no trailing semicolon because there is no further
operand.

This differs intentionally from C in a one-statement body:

```zax
if condition
  doA(); doB()
```

Both calls belong to the Zax `if`. In the visually similar C form, only the
first call would be conditional.

### Vertical composition

Vertical composition extends a source position that otherwise accepts one
statement:

```zax
if condition
  doA();
  doB();
  doC()
```

Each right operand must have structural indentation consistent with the same
body. Leading and trailing semicolons are errors because an operand is missing:

```zax
; doA()       // error: missing left operand
doA();        // error: missing right operand
```

A block already accepts vertically separated sibling statements, so vertical
composition there is redundant and is an error:

```zax
if condition {
  doA();
  doB() // error: vertical composition is redundant
}
```

Horizontal composition remains legal where a newline is not separating the
statements:

```zax
if condition {
  doA(); doB()
}
```

Comment trivia may appear between vertical operands, including while `;` is still
waiting for its right operand. No `\` is required:

```zax
if condition
  doA(); // `;` establishes that another operand follows.
  // Comment trivia may occur before that operand.
  doB()
```

A physically blank line may not separate them because it visually contradicts
their composition.

The source processor must therefore preserve comment and physical-line trivia
long enough to validate composition, blank-line boundaries, documentation
attachment, and layout intent. It need not expose comments as ordinary semantic
tokens, but it may not erase the relevant positions before those checks run.

### Structural operands versus expression continuation

Composed operands are new statements and stay at exactly one structural level:

```zax
if true
  a();
  b();
  c()
```

Progressively indenting sibling operands is an error:

```zax
if true
  a();
    b(); // error: composed operands must share one structural level
      c()
```

A body that begins four spaces deeper is also an error:

```zax
if true
    a(); // error: the body must begin exactly two spaces deeper
    b();
    c()
```

Deeper alignment remains legal for tokens continuing the same expression once
continuation is established, even when those expressions are then composed:

```zax
if true
  a := 1 + 2 + 3 + 4 \
    + 5 + 6;
  b := 1 + 2 +
       3 + 4;
  calc(a, b)
```

The lasting rule is:

> Parsed continuation may use deeper alignment for tokens that continue the same
> expression. It does not relax exact structural indentation for a new body
> statement, composed operand, header section, or sibling declaration.

### Nested control statements

A semicolon after an unbraced inner body composes at the inner level:

```zax
if outer
  if inner
    doA();
    doB()
```

To compose the complete inner control statement with another statement in the
outer body, braces expose the inner statement's end:

```zax
if outer
  if inner {
    doA()
  };
  doB()
```

Without those braces, syntax and indentation disagree:

```zax
if outer
  if inner
    doA();
  doB() // error: semicolon composition and indentation disagree
```

```zax
if outer
  if inner
    doA()
  doB() // error: outer body membership is not expressed by syntax
```

## Mandatory layout validation

Indentation does not determine Zax grammar, but it is significant to source
validity.

After parsing explicit source structure, Zax validates that statement-start
indentation presents that structure truthfully. Contradictory indentation is a
mandatory error; it is not an optional lint and cannot be disabled.

Structural indentation uses ASCII spaces only, and one structural level is
exactly two spaces:

```zax
if condition
  doConditionalWork()
```

- statement-start indentation must use the exact two-space structural level;
- physical tab characters are deliberate intent errors; and
- continuation or expression alignment may be deeper only where the parsed source
  already establishes continuation.

```zax
if condition
  doA()
  doB() // error: indentation claims unexpressed body membership
```

Removing a required semicolon therefore produces an error rather than silently
moving `doB()` outside the body. Adding a semicolon while dedenting its intended
right operand is also an error:

```zax
if condition
  doA();
doB() // error: composition and indentation disagree
```

Structural indentation and expression alignment are different concerns.
Statement-start lines must occur at the indentation expected for their parsed
position. Lines already known to continue a statement may align for readability
within the hanging-indentation requirement.

A formatter may normalize valid source. It must not silently rescue source whose
layout contradicts its structure.

## Header sections and separators

`;;` separates construct-specific flow-header sections, and `??` separates the
arms of a conditional expression. Both require whitespace on both sides, and
neither is `;` statement composition:

```zax
if i := 0 ;; i < 100 ;; ++i {
  body()
}

e := a > b ?? c ;; d
```

Their flow-control and conditional-expression meaning is owned by
[core flow control](core-flow-control.md); this document owns only their token
spacing and their relationship to statement composition.

`using` is deliberately not one of those header schemas. Its mandatory
parenthesized resource list accepts comma-separated mapping groups and no `;;`
initializer or post section:

```zax
using (resource) ;; record(resource) {
} // error: using has no post section
```

Complete resource behavior is owned by
[Zax `using`](using.md#no-initializer-or-post-sections).

`each` uses the same separator and continuation rules around its traversal
clause:

```zax
each values := loadValues() ;;
  value : in values ;;
  record(value) {
  use(value)
}
```

A positional traversal binding remains unenclosed. Parentheses select the
wholly named form:

```zax
each outer: (
  name: memberName :,
  value: memberValue :
) in record {
  use(memberName, memberValue)
}
```

`outer:` is the flow label because it immediately follows the complete
introducer. Inside the binding packet, `name:` and `value:` are role labels;
`memberName :` and `memberValue :` are declarations. Complete binding and
traversal behavior is owned by [Zax iteration](iteration.md).

Runtime selection uses `;;` for switch and clause posts:

```zax
switch choose: ;; readValue() ;; recordSelection() {
  case failure: {
    handleFailure()
  }

  case small: 1,
    2,
    3 ;; recordSmall() {
    if failed()
      goto failure:

    handleSmall()
  }

  default fallback: ;; recordFallback()
    handleFallback()
}
```

The switch label follows `switch`; a clause label follows `case` or `default`.
The outer braced region contains clauses. A tested case has one or more tests. A
transfer-only case has a required label and no tests. Each tested case,
transfer-only case, and default consumes one required effective body statement.
A comma continues a case-test list, while `;;` separates a test list or testless
clause from its post.

When the selector type is a variant, the same clause region accepts
alternative-routing entries:

```zax
switch choice {
  case text {
    use(text)
  }

  case number,
    alternateNumber bind selectedNumber {
    use(selectedNumber)
  }

  case ! {
    handleAbsent()
  }
}
```

The optional clause label still follows `case` first:

```zax
case retry: number, alternateNumber bind selectedNumber {
}
```

`bind` follows the complete comma-separated alternative list. It introduces one
ordinary body-local binding; the flow label remains a separate switch-wide label
category. `case ? bind value` uses the same placement. Bare `case ?`, `case !`,
and `default` introduce no payload binding.

Transfer-only cases may appear anywhere and are skipped by ordinary testing.
Several positional defaults may likewise appear; their runtime search behavior
belongs to the selection owner.

An explicitly empty clause body is `{ }`. Compact `{}` is an intent error, and a
bodyless tested case cannot borrow the body of a following clause.

Cases have no initializer section. Complete selector, test, post, and transfer
semantics belong to
[switch, case, and default](switch.md#cases-bodies-and-posts).
Variant routing and binding belong to
[switch, case, and default](switch.md#variant-alternative-selection).

### Header continuation

Top-level operands and sections continued across a flow header use one common
two-space continuation level rather than aligning under a variable-length keyword
or label prefix:

```zax
if i := 0;
  j := 1;
  k := 2 ;;
  i + j + k == 7 ;;
  ++i; --j; ++k {
  body()
}
```

Progressively indented sibling sections contradict the header structure:

```zax
if i := 0 ;;
    i < 100 ;;
      ++i {
  body()
}
```

### Explicit continuation as the alignment escape hatch

An alignment-sensitive programmer may move the whole header to the common
continuation level with `\`:

```zax
if \
  firstInitializer();
  secondInitializer();
  thirdInitializer() ;;
  condition ;;
  postOperation() {
  body()
}
```

The same form remains stable under a long label:

```zax
if shadowable extraordinarily_long_label: \
  firstInitializer();
  secondInitializer() ;;
  condition {
  body()
}
```

A label does not itself continue the physical line, so this explicit `\` is
necessary and legal. By contrast, a trailing `;` or `;;` already establishes
continuation across the following newline, so `\` after `;` or `;;` is redundant
continuation and therefore a deliberate intent error.

## Declaration-header continuation

A declaration colon that requires a type or callable prototype opens a narrow
construct-specific continuation:

```zax
value :
  MyType = source

operator pre unary '--' final :
  ()() = {
  }
```

This is not a general rule that incomplete syntax continues. A flow-label,
target, result-routing, or other contextual colon does not inherit declaration
continuation:

```zax
if retry: // error: the flow label does not continue the header
  initialize() ;;
  condition {
}
```

That header requires explicit continuation:

```zax
if retry: \
  initialize() ;;
  condition {
}
```

Initializer and assignment `=` use the trailing symbolic-infix rule:

```zax
value : MyType =
  source
```

An explicit marker is redundant because `=` already proves the initializer
continues:

```zax
value : MyType = \ // error: `=` already continues the declaration
  source
```

### Mixfix component-list continuation

`operator mixfix` opens a grammar-delimited component list:

```zax
operator mixfix
  index 1
  binary '='
final : (
  result : MyResult
)(
  indexValue : MyIndex,
  rhs : MyValue
) writable = {
}
```

The grammar recognizes component specifications and the declaration
qualifier/prototype that ends the list. Indentation validates that structure but
does not create it. Component lines use one consistent hanging indentation.

An explicit `\` is redundant while the open mixfix component list or an open
prototype delimiter already permits the newline.

Exact mixfix declaration semantics are defined by
[mixfix operators](mixfix-operators.md#declaration-model).

## Braces and body boundaries

A `{` used as a scope opener has whitespace on both sides, with a newline
counting as whitespace. A braced body begins on the final physical header line,
and a multiline body-closing `}` aligns with its flow keyword:

```zax
if condition {
  doA()
  doB()
}

using cleanup: (
  resource := acquireResource()
) {
  use(resource)
}
```

A body-opening `{` separated onto the next physical line, or a closing `}` at the
wrong structural level, is a deliberate intent error. An unbraced body ends after
its one effective statement, so a following dedented statement runs
independently. An empty braced body is legal:

```zax
if condition { }
```

### Empty-header-block intent

An exact empty block used as an initializer or post header is a deliberate intent
error, because it reads more like a missing or misplaced body than meaningful
header work:

```zax
while i := 0 ;; i < 100 ;; { } {
  body()
}
```

The rule is syntactic rather than an attempt to prove whether arbitrary nested
statements have effects. A nonempty but apparently effectless header operation
may instead become lint material.

### Blocks inside composed initializers

A block in an initializer keeps its ordinary nested lexical scope, so names it
introduces do not escape to the condition or body. Binding visibility across a
flow header is owned by
[declarations and bindings](declarations-and-bindings.md#flow-control-initialization).

```zax
if {
  temporary := inspectEnvironment()
  recordInspection(temporary)
} ;; condition {
  body()
}
```

When a multiline block is one operand of vertically composed header
initialization, explicit continuation places the block and its sibling operands
at the common level:

```zax
if \
  {
    temporary := inspectEnvironment()
    recordInspection(temporary)
  };
  resource := getResource() ;;
  validated(resource) ;;
  discard(resource) {
  use(resource)
}
```

The compact horizontal form is also legal:

```zax
if {
  temporary := inspectEnvironment()
  recordInspection(temporary)
}; resource := getResource() ;;
  validated(resource) ;;
  discard(resource) {
  use(resource)
}
```

This vertical form is a deliberate layout-intent error even though its tokens can
be parsed, because the block closes at the flow-keyword level while `;` claims an
indented right operand as its structural sibling:

```zax
if {
  temporary := inspectEnvironment()
  recordInspection(temporary)
};
  resource := getResource() ;;
  validated(resource) ;;
  discard(resource) {
  use(resource)
}
```

Use explicit continuation to move both operands to the header-continuation level,
or put the right operand horizontally after `};`. Moving `}` onto the last inner
statement line is not a workaround; a multiline block's closing delimiter returns
to the level where that block statement began.

## `else` attachment and layout

An `else` clause must present its attachment to the `if` it completes.

An unbraced clause aligns exactly with its owning `if`, follows the complete true
body with no physically blank line between them, and begins its own unbraced body
exactly two spaces deeper:

```zax
if condition
  trueBody()
else
  falseBody()
```

A braced clause keeps `else` on the physical line that closes the preceding body,
separates `}` and `else` with whitespace, opens the `else` body on the `else`
line, and closes at the owning `if`'s level:

```zax
if condition {
  trueBody()
} else {
  falseBody()
}
```

A newline between `}` and `else` is a deliberate attachment-intent error:

```zax
if condition {
  trueBody()
}
else { // error: else is visually detached from the completed if
  falseBody()
}
```

A blank line before an unbraced `else`, or an `else` at the wrong structural
level, is the same class of error:

```zax
if condition
  trueBody()

else // error: a blank line detaches else from its if
  falseBody()
```

```zax
if condition
  trueBody()
  else // error: else must align with its owning if
    falseBody()
```

`else if` places `else` and `if` on one physical line separated by whitespace, and
every clause of the chain aligns with the owning `if`:

```zax
if firstCondition
  firstBody()
else if secondCondition
  secondBody()
else
  finalBody()
```

Comment trivia may precede or follow a clause without weakening the attachment
rule, because a comment line is not a blank line:

```zax
if condition
  trueBody()
// Explain why the alternative exists.
else
  falseBody()
```

Which clause runs, what a flow label names, and when a post operation runs are
owned by [core flow control](core-flow-control.md).

## Contextual keyword recognition

A spelling has keyword status only where that keyword's construct is
grammatically permitted. Outside those positions, that spelling is not a keyword:

```zax
next          // transfer keyword
next outer:   // transfer keyword with a target
next()        // next has no keyword status here

continue          // transfer keyword
continue outer:   // transfer keyword with a target
continue()        // continue has no keyword status here

goto outer:   // transfer keyword with a required target
goto()        // goto has no keyword status here
```

The rule is about position rather than about a reserved-word list, so a
concept owner that shows a likely confusion should link here instead of
restating the rule. The term itself is defined by
[language-design terms](terms.md#contextual-keyword).

`catch`, forwarding or producer `except`, and use-site outcome `reshape`
likewise have keyword roles only in their established result-flow positions.
Elsewhere the same spellings remain subject to ordinary identifier or phrase
interpretation.

### Postfix `_` keyword escape

A single postfix `_` forces one word through non-keyword grammar and is removed
from its semantic spelling:

```zax
erase outer:  // keyword operation and flow target
erase_ cursor // ordinary non-keyword phrase `erase`
```

The escape affects recognition, not identity. A declaration remains named
`erase`, not `erase_`, and `word` and `word_` cannot introduce two different
semantic names.

The escape remains legal when the current compiler has no keyword
interpretation for the word in that position. Source may therefore protect a
word before a later language revision adds a contextual keyword. A formatter
preserves an explicit escape rather than removing currently redundant
forward-compatibility intent.

Only one trailing `_` is the escape. It does not recursively create a semantic
name ending in `_`. After keyword suppression, ordinary grammar may recognize
the word as an identifier, callable name, operator-phrase component, or another
available non-keyword role.

This narrow one-word mechanism complements `bare{...}`, which neutralizes
keyword roles across one complete enclosed source unit.

### Keyword-neutral `bare` source

`bare{...}` is a non-scoping, keyword-neutral, tree-transparent source
enclosure:

```zax
result := bare{ if + while } + fallback
```

The contiguous opener is required:

```zax
bare{ source }
```

Bare `bare` is not a keyword, and `bare { source }` is not the enclosure.

The payload must independently form exactly one complete expression or effective
statement. It cannot borrow an operand, separator, declaration piece, or body
from outside. After that check:

- the enclosure creates no runtime operation;
- it creates no scope or destruction boundary;
- it creates no final precedence boundary or expression-tree node;
- wider operator and mixfix matching may use the completed payload result;
- non-operator keyword roles remain strictly neutralized; and
- ordinary qualification, protection, lifetime, layout, and diagnostics remain
  active.

Neutralization is strict:

```zax
bare{ value as Payload immutable readonly final & }
// error unless these neutralized words form another valid non-keyword reading
```

`as`, `immutable`, `readonly`, and `final` do not regain keyword roles merely
because they would make the payload convenient. Name the complete qualified type
outside the enclosure when needed.

Nesting is unavailable by ordinary recognition:

```zax
bare{ first bare{ second } }
// Inner bare has no keyword role; ordinary parsing rejects the resulting source.
```

The language registers no special nested-`bare` diagnostic. Inside the outer
payload, `bare` is an ordinary neutralized word; the attached `{` and any
unresolved name receive the normal applicable diagnostics.

The enclosure does not acknowledge a confusable form, grant unsafe authority,
recognize arbitrary operators, select an ambiguous overload, or disable source
layout. [Intent acknowledgements](intent-acknowledgements.md) own defined but
suspicious forms.

A phrase component cannot span the enclosure because the payload must first
complete independently. Complete phrase interaction is defined by
[operator phrases](operator-phrases.md#enclosure-boundaries).

## Comment lexical modes

Comment delimiters do not cross lexical modes. At ordinary source level, the
recognized form determines how its contents are scanned:

- `///` begins documentation through the end of its physical line.
- `//` begins an ordinary comment through the end of its physical line.
- `/*` followed immediately by one or more `#` characters begins a raw block
  comment.
- An otherwise ordinary `/*` begins a nestable block comment.

The more specific documentation and raw forms are recognized before their
ordinary prefixes.

### Line comments

Once `//` or `///` begins, apparent comment delimiters later on that physical
line are payload:

```zax
/// Documentation text /* remains documentation through this */ text.
// Ordinary comment text /* does not open a block comment.
```

An apparent block opener cannot affect later lines:

```zax
/// The following /* is documentation payload.
doSomething()
*/ // error: no block comment was opened
```

### Ordinary block comments

Ordinary block comments nest:

```zax
/*
  copiedCode()
  /* An existing nested comment. */
  moreCopiedCode()
*/
```

Inside an ordinary block comment, `//` and `///` are inert text. Another
ordinary `/*` increases nesting depth, and `*/` decreases it, even when those
sequences appear in copied text that would otherwise be a string or another
source form.

### Raw block comments

A raw block comment closes only with a contiguous run of exactly the same number
of `#` characters followed immediately by `*/`:

```zax
/*#
Arbitrary content containing /*, */, "*/", or malformed source.
#*/
```

The fence can be lengthened when its closing sequence occurs in the content:

```zax
/*###
Content containing #*/ and ##*/ remains opaque.
###*/
```

Raw contents recognize no nested comments, strings, escapes, documentation, or
other source forms. A raw comment does not itself nest.

A raw comment may appear inside an ordinary block comment at any nesting depth.
The raw opener is recognized before another ordinary nested opener. Everything
through the exact raw closer is ignored, after which scanning resumes at the
same ordinary nesting depth:

```zax
/*
  /*
    /*##
    Everything here is raw, including /*, */, ///, and /*# #*/.
    ##*/
  */
*/
```

## Documentation comments

`///` identifies documentation metadata. Zax recognizes, groups, preserves, and
attaches it deterministically without prescribing XML, Markdown, or another
payload language.

### Grouping and payload

Adjacent full-line `///` comments at the same structural indentation form one
documentation block. Line boundaries are preserved. An empty documentation line
is represented by `///` and remains part of the block:

```zax
/// First paragraph.
///
/// Second paragraph.
```

A physically blank line ends the block.

Everything after `///` is opaque payload:

```zax
/// A URL such as https://zax.io and /* delimiters */ remain documentation.
```

An apparent `//`, `/*`, or `*/` is not stripped or interpreted by Zax.
Documentation markers inside ordinary or raw block comments have no
documentation meaning and require no attachment.

### Leading attachment

Every documentation block has a deterministic target. A leading block attaches
forward to the following construct at the same structural indentation:

```zax
/// Documentation for doSomething.
doSomething()
```

When the target introduces one or more bindings, documentation attaches to the
complete declaration construct rather than to one individual name. Declaration
meaning and multi-binding behavior are defined by
[Zax declarations and bindings](declarations-and-bindings.md).

Documentation is prefix metadata rather than a statement. It does not consume a
body position:

```zax
if condition
  /// Documentation for the body statement.
  doSomething()
```

Misaligned documentation is an error:

```zax
if condition
/// error: documentation does not match the body indentation
  doSomething()
```

Ordinary comment trivia may intervene without becoming documentation:

```zax
/// Documentation for doSomething.
// An ordinary source note.
doSomething()
```

A physically blank line breaks forward attachment.

### File attachment

A top-level block separated from the first code construct by a blank line
attaches to the source file as preamble documentation. Top-level documentation
after the last code construct attaches to the file as epilogue documentation.
Several separately grouped file-level blocks may attach in source order.

An otherwise unattributed block in the middle of source is an error; it does not
silently become file documentation.

### Trailing attachment

An inline trailing block attaches backward to the complete effective statement
or construct at the structural indentation represented by that line:

```zax
doSomething() /// Documentation for doSomething.
```

Attachment does not bubble outward merely because completing an inner construct
also completes an enclosing construct:

```zax
if condition
  doSomething() /// Documentation for doSomething.
```

Leading documentation may independently attach to the enclosing `if`:

```zax
/// Documentation for the if.
if condition
  doSomething() /// Documentation for doSomething.
```

A trailing block after a braced body occurs at the outer structural level:

```zax
if condition {
  doSomething()
} /// Documentation for the if.
```

For a vertically composed body, trailing documentation on the final operand
attaches to the complete effective composed statement:

```zax
if condition
  doA();
  doB() /// Documentation for the composed body.
```

The target must be complete:

```zax
if condition /// error: the if is incomplete
  doSomething()

doA(); /// error: the composition operator requires a right operand
doB()
```

Documentation may appear where a multiline construct finishes:

```zax
value = 1 + 2 + // Ordinary commentary is legal here.
    3 /// Documentation for the complete assignment.
```

### Duplicate attachment

A non-file construct may receive one directly attached documentation block,
using either leading or trailing placement but not both:

```zax
/// error: doSomething also has trailing documentation
doSomething() /// Competing documentation for doSomething.
```

The rule applies independently at each structural level:

```zax
if condition
  /// Leading documentation for doSomething.
  doSomething() /// error: duplicate documentation for doSomething
```

Several adjacent `///` lines are one block. Distinct leading blocks separated by
ordinary comment trivia remain distinct; if both target the same construct,
they produce the same duplicate-attachment error. Use an empty `///` line for
separation within one documentation block.

## Programmer-visible diagnostics

Diagnostics should identify the local contradiction and distinguish, where
applicable:

- a missing `\`;
- a broken or unterminated continuation;
- redundant explicit continuation where an open delimiter, comma, or trailing
  symbolic infix form already continues the newline;
- a broken comma-list continuation;
- a missing semicolon operand;
- redundant vertical composition;
- missing braces around a nested control statement;
- unexpected or contradictory indentation;
- an unmatched comment delimiter;
- unattributed documentation; and
- duplicate documentation attachment.

Layout and separator diagnostics additionally distinguish:

- statement-start indentation that does not use the exact two-space structural
  level;
- physical tab characters;
- whitespace before a comma separator or missing whitespace after it;
- whitespace before `;` or missing whitespace after it;
- missing whitespace around `;;` or `??`;
- symbolic pre/post/binary whitespace that contradicts the recognized fixity;
- `.=` split into postfix `.` and ordinary `=`, or `optional. =` joined into
  contained reconstruction, when that changes lifecycle intent;
- adjacent independent unary applications without grouping;
- an unknown contiguous symbolic token where no catalog form exists;
- a phrase-whitespace presentation error in a selected component;
- a selected phrase component spanning a physical line;
- an empty phrase fence, or a fence with an escape, leading space, or trailing
  space;
- a spaced `bare {` form from the contiguous `bare{` opener;
- a `bare{...}` payload that is not one complete expression or effective
  statement;
- adjacent literal segments that resolve to different declarations;
- an implicit literal merge broken by an uncontinued physical newline;
- an empty segment participating in implicit or explicit literal merge;
- an explicit `\` where a declaration colon or mixfix component list already
  continues the newline;
- an initializer `=` followed by an uncontinued newline;
- mixfix component lines at conflicting continuation levels;
- redundant `\` after `;` or `;;`;
- composed operands at progressively deeper structural levels;
- a body beginning more than one structural level deeper than its header;
- sibling header operands or sections at conflicting continuation levels;
- a malformed case-test comma list or clause post separator;
- variant `bind` before the complete alternative list, without a binding name,
  or after the body has begun;
- missing `using` resource-list parentheses or a malformed resource mapping
  group;
- a `using` header containing `;;`;
- a tested case or default without one effective body;
- an unlabeled testless case;
- compact `{}` where a clause's explicit empty body requires `{ }`;
- a `case` or `default` body whose indentation contradicts the enclosing switch
  region;
- a body-opening `{` separated onto the next physical line;
- a scope-opening `{` without whitespace on both sides;
- spaced `[ ]` where an empty array requires `[]`;
- spaced `[[ ]]` where an empty capture requires `[[]]`;
- compact nonempty `[[value]]` where capture entries require interior spacing;
- compact nested-array `[[...]]` where contiguous `[[` presents lambda capture;
- malformed or mismatched `[[ ... ]]` capture delimiters;
- malformed or mismatched `[< ... >]` compiler-directive delimiters;
- a compiler-directive enclosure in a source position that accepts no
  directive attachment;
- an outcome clause detached from the invocation it qualifies;
- an unbraced `catch` body;
- a following `catch`, forwarding `except`, or outcome `reshape` clause
  physically detached from the preceding handler;
- exceptional source/destination labels whose adjacency or declaration spacing
  presents another mapping;
- comma-separated reshape entries or several inline reshape mappings where one
  reshape declaration is required;
- an array-entry `from` without a complete iterable source;
- bare default-remainder `..` outside a valid final array-entry position;
- `..<` without a written end;
- compact `T??` where two optional type layers require `T? ?`;
- whitespace before a type-layer `?` instead of attachment to the complete
  qualified inner type;
- spaced `[{ }]` where a zero-entry construction packet requires `[{}]`;
- a bare construction packet in an expression position without a destination;
- a variant packet naming several alternatives or supplying wrapper-level
  constructor arguments;
- a construction packet targeting an unmanaged union;
- a multiline closing `}` at the wrong structural level;
- an `else` separated from a preceding `}` by a newline, preceded by a blank
  line, or placed at a level other than its owning `if`;
- exact `{ }` used as an initializer or post header; and
- the parseable multiline initializer-block composition whose layout places its
  operands at conflicting levels.

Phrase-word, recognition, fencing, attachment, and other phrase-specific
diagnostics are listed by
[Zax operator phrases](operator-phrases.md#costs-diagnostics-formatting-and-source-stability).

Source diagnostics fall into ordinary syntax rejection (source that matches no
legal production), semantic errors (source that parses but violates a type,
name, or completion rule owned elsewhere), and deliberate intent or layout errors
(a bounded set of near-miss patterns whose tokens may parse but present
contradictory intent).

Intent diagnostics distinguish:

- a **layout-intent error**, where indentation, brace placement, clause
  attachment, or physical-line position contradicts parsed structure;
- an **operator-attachment intent error**, where whitespace, adjacency, or
  grouping presents another fixity or number of operations;
- a **redundant-structure intent error**, where two mechanisms claim one
  continuation or structural role; and
- a **confusable-form intent error**, where a coherent recognized form is gated
  because its natural spelling strongly resembles a damaged neighboring form and
  ordinary whitespace/grouping cannot preserve the intended source.

An **acknowledgement-required intent error** has one defined but suspicious
interpretation and may use an applicable `intent<category>{...}` enclosure. A
**non-acknowledgeable intent error** recognizes a malformed or forbidden near
miss only to provide a precise diagnostic; it has no accepted interpretation and
must be rewritten. An intent enclosure cannot make the latter valid.

A keyword-role conflict is a related lexical interpretation question rather than
permission to ignore grammar. These terms are defined by
[language-design terms](terms.md).

### Intent acknowledgement enclosure

Defined but suspicious source uses:

```zax
intent<category>{
  source
}
```

The category is a lower-case hyphenated identifier registered by
[Zax intent acknowledgements](intent-acknowledgements.md). Each category defines
the smallest complete contextual source unit around one occurrence. Most such
units are one complete expression, effective statement, or declaration. A
category can instead identify another complete contextual unit, such as one
complete `case`/`default` clause in a switch region.

An enclosure may contain one such unit or a larger complete source region with
several occurrences of the same category. One surrounding
`intent<category>{...}` acknowledges every matching occurrence in that payload.
No payload can borrow a required operand, separator, declaration piece, or body
from outside the enclosure. Complete category matching and the requirement that
at least one occurrence be present are owned by
[Zax intent acknowledgements](intent-acknowledgements.md#source-form).

The acknowledgement does not:

- create an operator or expression result;
- create a declaration scope or lifetime boundary;
- remain as a final expression-tree node;
- grant qualifier, lifetime, or unsafe authority;
- suppress unrelated diagnostics;
- change precedence or operator selection;
- or add a required runtime operation.

Its braces disappear for scope purposes:

```zax
intent<category>{
  value : Integer
}

value = 5 // value remains in the surrounding scope
```

Ordinary layout applies inside the payload. Exact token-level grammar, nesting,
and source-reflection representation remain future source-contract details.

Most rules in this document are deliberate intent or layout errors. The category
is intentionally bounded and does not require recognizing every malformed token
sequence.

Exact diagnostic identifiers and presentation belong to later diagnostics
design. These source-validity conditions remain mandatory errors rather than
configurable warnings.

## Boundaries and maturity

This document owns the accepted programmer-facing source model, not a formal
grammar, parser algorithm, formatter implementation, or syntax-highlighting
architecture.

`;;` and `??` are accepted header-section and conditional-expression separators.
Each requires whitespace on both sides and is distinct from `;` statement
composition; neither may be inferred from `;`. Their flow-control and
conditional-expression meaning is owned by
[core flow control](core-flow-control.md).

Mandatory `using (...)` delimiters, their mapping-capable comma list, and their
lack of `;;` sections are current source structure. Their resource and exit
meaning is owned by [Zax `using`](using.md).

The switch clause region, case-list continuation, and case/default body
presentation are current source structure. Their runtime meaning is owned by
[Zax switch, case, and default](switch.md).

Contextual keyword recognition is accepted as a positional rule: a spelling is a
keyword only where its keyword construct is grammatically permitted. Words inside
a declared or fenced operator phrase carry phrase roles instead. The complete
keyword catalog and each construct's grammar remain with their own owners and
future work.

Compiler directives use the `[< ... >]` enclosure. A directive on its
own line attaches to the line directly below it, and a blank line makes it
unattached; [compiler directives](compiler-directives.md#where-a-directive-goes)
defines the attachment positions. A directive on one physical line and a
related construct on the next do not use `\` merely to express that
relationship. Explicit continuation retains its single purpose of continuing
one statement across physical lines.

Keyword-neutral `bare{...}` uses a contiguous opener, requires one independently
complete payload, creates no scope or final tree boundary, neutralizes
non-operator keyword roles strictly, and does not recognize a nested opener.
Complete behavior is defined above; source reflection must retain the written
enclosure even though it creates no final expression-tree node.

`compile{...}` and `wellformed{...}` also use contiguous openers: `compile{`,
and `wellformed{` or `wellformed<options>{`, with no space before the brace.
Unlike `bare{...}`, their braces enclose statements as a block. A spaced
`wellformed {` is an ordinary name followed by a brace, so a variable named
`wellformed` remains usable. Their meaning is defined by
[compile-time code](compile-time-code.md).

Comma-list continuation is the ordinary list-level implicit continuation because
the parsed comma already requires another element. Declaration colon and mixfix
component-list continuation are separate grammar-open construct rules. A
trailing recognized symbolic infix form is the only incomplete-expression shape
that continues by its own presentation; arbitrary missing syntax does not.

[Declaration and binding behavior](declarations-and-bindings.md) is defined by
its current conceptual owner. Operator semantics, candidate-tree formation, and
precedence are defined by [operators](operators.md), the
[operator catalog](operator-catalog.md), and
[mixfix operators](mixfix-operators.md). Individual flow-control behavior,
scope-exit effects, literal realization, and documentation payload
interpretation remain with their applicable owners or future work.

For Zax's accepted foundational direction, see the
[language vision](vision.md).
