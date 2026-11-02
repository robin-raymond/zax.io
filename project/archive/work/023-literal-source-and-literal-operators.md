# 023: Literal source and literal operators

| Field | Value |
| --- | --- |
| Status | Historical / non-normative / audit-only / completed |
| Work Item | `023` |
| Created | 2026-09-15 |
| Completed | 2026-09-16 |
| Owns | The bounded review defined by the fixed initiating input |
| Does Not Own | Complete generics, a complete text/Unicode type system, runtime string libraries, exhaustive numeric operations, compiler parser implementation, or formal grammar |

## Non-authority notice

This file is a collaborative working record. Existing statements, new
observations, candidate wording, and aligned findings remain non-authoritative
until a separately discussed, aligned, and explicitly authorized promotion
incorporates them into their lasting owners.

## Fixed initiating input

This section records the information aligned when work item `023` was created.
It is intentionally incomplete and must not be rewritten as work develops.

### Initiating concern

Establish a coherent programmer-facing model for Zax literal source and literal
operators, including ordinary numeric and string forms, attached
prefixed/custom literals, compile-time parsing, one concrete result selection,
joining, diagnostics, and interaction with expression typing.

The [literal-operator maintainer notes](../raw/literals-maintainer-notes.md) are
primary maintainer-supplied input that refreshes several legacy literal
concepts. Read and disposition their complete contents rather than treating
older root pages as the current proposal. The indexed
[literal-operator raw input](../raw/literal-operators.md) preserves current
token-boundary constraints and deferred questions discovered by prior work.

### Motivating pressure

Current integer design defines uncommitted integer source and realization.
Current fixed-point and floating-point design defines typed real-number
realization and decimal `e`/`E` power-of-ten exponents. Source structure and
operator phrases distinguish:

```zax
"ordinary text" // ordinary unprefixed literal
h'DEADBEEF'     // attached prefixed literal
foo 'bar'       // separated operator-phrase fence
```

The language still lacks one current explanation of:

- which remaining literal tokens and payloads exist;
- how a literal operator is declared and found;
- when and how its code executes during compilation;
- how one concrete result type is known;
- how a numeric payload can request enough width without always selecting an
  extreme maximum type;
- how mathematical-value and raw-bit interpretations differ;
- which ordinary and prefixed string forms exist; and
- when adjacent literal results join.

These questions cross parsing, type selection, generics, compile-time execution,
strings, and operators. The literal model must settle their shared boundary
without attempting to complete every adjacent subsystem.

### Known assumptions

- A resolved expression result has one concrete static type before it
  participates in its surrounding expression.
- A resolved prefixed literal does not remain an uncommitted integer merely so
  later context can reinterpret it.
- Payload-dependent numeric width remains legitimate generic pressure: a future
  literal/type factory may derive one concrete result from compile-time payload
  magnitude before resolution completes.
- Range failure does not retry unrelated literal overloads or search wider
  result types after one candidate has already been selected.
- Literal operators are compile-time facilities; exact execution requirements,
  sandboxing, resource limits, and failure paths must remain visible.
- An attached single quote introduces a literal payload. A whitespace-separated
  single quote is an exact operator-phrase fence, not an unprefixed literal.
- Current decimal real source accepts `e`/`E` with an optionally signed
  power-of-ten exponent.
- Typed integer, fixed-point, and floating realization semantics remain owned by
  their current scalar documents.

### Known inclusions

- Ordinary unprefixed integer, real-number, and string source as needed to give
  readers one literal taxonomy.
- Prefix recognition, attachment, namespace/import visibility, ambiguity, and
  owner/type relationship.
- Single- and double-quoted payload boundaries, escaping or absence of escaping,
  invalid source characters, multiline/continuation behavior, and empty
  payloads.
- Literal declaration shape, input, compile-time execution, result identity,
  diagnostics, and source stability.
- Built-in and custom numeric radix literals, including mathematical magnitude
  versus typed value versus raw bit-pattern interpretation.
- Payload-dependent concrete numeric result pressure and its exact handoff to
  future generic/type-factory work.
- Decimal point and accepted decimal exponent forms.
- Evaluation of binary, hexadecimal, octal, or other-base fractional/exponent
  forms when they provide a coherent programmer use case.
- Ordinary and prefixed string/character forms, result identities, supported
  encoding pressure, and conversion/join consequences far enough to assign one
  live owner to every material deferral.
- Adjacent literal merging or joining, including when prefixes, grouping,
  separators, or differing result types prevent one unified payload.
- Interaction with candidate selection, contextual completion, constants,
  arrays, source reflection, formatting, and documentation.
- Complete disposition of the literal maintainer notes, literal-operator raw
  input, and applicable legacy literal material.

### Exponent review boundary

Work item `023` does not presume that every numeric base or encoded payload
needs an exponent form.

Evaluate an exponent only when it gives the represented form a clear,
nonredundant use:

- decimal `e`/`E` remains the established power-of-ten form;
- binary or hexadecimal floating-style payloads may justify a `p`/`P`
  power-of-two exponent comparable to established programmer expectations;
- arbitrary-base payloads must not receive an exponent merely for catalog
  symmetry; and
- a prefix-specific parser may reject fractional or exponent syntax when its
  intended result is an integer or raw bit pattern.

Illustrative candidates such as `h'1.8p10'` or `b'1.1p-3'` are questions for
review, not accepted syntax.

### Known starting boundaries

- Complete generic declaration, type-factory, associated-type, inference, and
  specialization syntax.
- Complete compile-time execution scheduling, sandboxing, caching, and resource
  policy.
- A complete text, Unicode, locale, normalization, code-page, formatting, or
  runtime string-conversion model.
- Exhaustive numeric families or operations beyond literal formation.
- General implicit conversion or result-type lookahead.
- Arbitrary expression parsing inside literal payloads unless a concrete
  literal-operator use requires it.
- Source reflection and formatter implementation.
- Compiler tokenization/parsing algorithms and formal grammar.

These boundaries do not erase consequences. Preserve every material pressure in
a current owner or indexed raw destination rather than leaving it only in the
eventual archive of this work item.

### Intentionally unresolved framing

- Whether ordinary string source performs escapes, preserves source text
  directly, or uses another explicitly taught policy.
- Which built-in prefixes exist and which are aliases.
- Whether custom literal prefixes form an open, bounded, or otherwise governed
  catalog.
- Whether prefixed payloads may use both quote kinds and how delimiter
  characters are represented inside them.
- Whether adjacent equal-prefix tokens are parsed once as one payload or produce
  concrete values that then join.
- Which string, character, byte-array, or encoded-text identities each built-in
  form returns.
- Whether a numeric prefix denotes mathematical radix, one concrete type, a raw
  bit pattern, or separately named forms for those meanings.
- How a literal declaration can compute one bounded concrete result type from
  payload magnitude without reopening the result after selection.
- Whether binary/hexadecimal `p`/`P` exponents are useful, and whether any other
  encoded numbers warrant exponent syntax.
- Exact literal diagnostics, source limits, and malformed-token recovery.

### Initial stopping guidance

Stop when the work has:

- established a teachable literal taxonomy and token/attachment boundaries;
- described ordinary and prefixed numeric and string use with representative
  valid and invalid source;
- defined literal declaration, discovery, compile-time execution, and one
  concrete result boundary at the depth needed by programmers;
- dispositioned payload-dependent result types without prematurely completing
  generics;
- decided each materially proposed exponent form by use rather than symmetry;
- defined joining/merging behavior and its costs;
- identified supported built-in forms and assigned complete string/text
  consequences to current or indexed future owners;
- reconciled source structure, phrase fencing, scalar realization, operators,
  diagnostics, reflection, and source stability;
- dispositioned every useful item in the primary literal notes, focused raw
  input, and reached legacy material; and
- completed the required documentation-fit dry run.

Do not promote findings, archive this work item, begin work item `024`, or
design complete generics, compile-time execution, text/Unicode semantics,
runtime string libraries, or compiler parsing without the separately required
discussion, alignment, and authorization.

## Reading scope

### Required

- [Documentation architecture](../documentation.md) - governs focused reading,
  decision maturity, promotion, deferral, and closure.
- [Literal-operator maintainer notes](../raw/literals-maintainer-notes.md) -
  consumed during this work; now a temporary provenance stub retained because
  the fixed initiating input links it.
- [Raw literal operators](../raw/literal-operators.md) - consumed during this
  work; now a temporary provenance stub retained until work item `023` is
  archived.
- [Source structure](../../language/source-structure.md) - owns current token
  attachment, phrase-fence, string-delimiter, continuation, and layout
  boundaries.
- [Integer literals and realization](../../language/integer-literals.md) - owns
  uncommitted integers, decimal exponent token boundaries, candidate holes, and
  the rule that range failure does not retry another type.
- [Fixed-point scalars](../../language/fixed-point-scalars.md) - owns typed
  fixed-point real realization, rounding, range, and representation.
- [Binary floating-point scalars](../../language/floating-point-scalars.md) -
  owns typed floating real realization, format rounding, range, and
  representation.
- [Operator phrases](../../language/operator-phrases.md) - owns attached literal
  versus whitespace-separated single-quote phrase fencing.

### Consequence-driven

- [Raw type parameters and generics](../raw/type-parameters-and-generics.md)
  when payload-dependent concrete result identity, bounded size selection, or
  a literal/type factory becomes material.
- [Raw compile-time execution](../raw/compile-time-execution.md) when required
  literal execution, host/target semantics, resource failure, or static
  availability becomes concrete.
- [Function invocation](../../language/function-invocation.md),
  [declarations and bindings](../../language/declarations-and-bindings.md),
  [operators](../../language/operators.md), and the
  [operator catalog](../../language/operator-catalog.md) when declaration,
  result, discovery, ambiguity, or exact operator form crosses those owners.
- [Raw reflection](../raw/reflection.md) when source spelling, payload,
  generated result identity, or literal provenance requires metadata.
- [Raw numeric type families](../raw/numeric-type-families.md) when a
  representation-operation or nonstandard numeric family becomes necessary.
- Relevant legacy literal, string, character, array, encoding, namespacing, or
  casting material only when a concrete question cannot be resolved from the
  primary notes and current owners.

### Audit-only

- Archived work only when a concrete provenance or regression question cannot
  be answered from current owners and live raw input.

## Initial working posture

Begin with programmer source: an ordinary integer, an ordinary decimal real, an
ordinary string, a small radix payload, a payload requiring a much wider result,
and adjacent literal tokens. Explain what each token produces before designing
the declaration mechanism that supplies it.

Treat the maintainer notes as refreshed evidence rather than accepted behavior.
Preserve current phrase-fence and scalar-realization boundaries. Evaluate
non-decimal exponents only when they make a concrete encoding easier to express;
do not add one form per radix merely to complete a matrix.

## Working record

### Current post-promotion aligned refinements

These findings supersede the corresponding parts of the previously promoted
model below. They are aligned for the current review scope and have been
incorporated into the wider current owners through authorized follow-up
promotions. They include exact-rational real evaluation, implicit source merge,
string backing, byte admission, legacy character identity, policy-selected
termination, optional type availability, and mathematical constants.

#### Implicit and explicit source merge

Adjacent literal segments that independently resolve to the same
literal-operator declaration implicitly merge:

```zax
myString := s'"' s"'hello'" s'"'
// One s invocation with payload "hello".
```

`<|>` remains the explicit spelling of the same source operation:

```zax
myString := s'"' <|> s"'hello'" <|> s'"'
```

Different declarations cannot be adjacent:

```zax
myString := x'"' y"'hello'" z'"'
// error: the segments cannot form one literal invocation
```

Exact qualified/unqualified aliases still count as one declaration. Equal
result type without equal parser declaration remains insufficient.

Implicit merge establishes no physical-line continuation:

```zax
myValue := x'first ' \
  x'second'
```

Explicit trailing `<|>` establishes continuation under the symbolic-infix rule:

```zax
myValue := x'first ' <|>
  x'second'
```

The `same-prefix-literal-join` acknowledgement retains ordinary merge grammar:

```zax
myJoined := intent<same-prefix-literal-join>{
  s'first' <+> s'second'
}
```

The visible `<+>` states join. Adjacent same-declaration segments inside an
intent enclosure still merge implicitly; if the enclosure contains no
applicable `<+>`, the acknowledgement category is inapplicable rather than
changing adjacency into a join.

#### Signed raw-pattern sanity boundary

Type-qualified radix literals establish concrete raw logical bits before any
outer operator:

```zax
I8.h'FF'        // I8 -1
-I8.h'FF'       // negate concrete I8(-1), result I8 +1
I8.b'10000000'  // I8 -128
I8.dec'255'     // I8 -1
```

Signs remain illegal inside raw-pattern payloads:

```zax
I8.dec'-1' // error
I8.h'-1'   // error
I8.b'-1'   // error
```

Mathematical uncommitted source keeps the sign outside:

```zax
-h'1' // mathematical -1 before commitment
```

Negating a concrete signed minimum applies ordinary typed required negation:

```zax
-I8.h'80'
// compile-time diagnostic: required I8 negation of -128 overflows
```

#### Exact uncommitted real evaluation

Uncommitted integer and real source now share the same broad commitment model:
perform the permitted exact mathematical operations first, then realize one
final mathematical result at the commitment boundary.

```zax
myFloat : Float = 1.0 / 3.0
myQuad : Quad = 1.0 / 3.0
```

Both expressions first produce exact rational `1/3`. Each destination then
independently realizes and correctly rounds that value.

Parentheses create no commitment:

```zax
myValue1 : Binary32 = 0.1 + 0.2
myValue2 : Binary32 = (0.1 + 0.2)
// Both calculate exact rational 0.3, then realize it once as Binary32.
```

A concrete inner declaration ends exact uncommitted evaluation:

```zax
myValue : Binary32 =
  (: Binary32 = 0.1) + 0.2
// The left operand is concrete, the right adopts Binary32, and Binary32
// arithmetic performs per-operation rounding.
```

The initial exact uncommitted-real operation family includes:

- grouping and outer sign;
- `+`, `-`, and `*`;
- rational `/`, with zero divisor diagnosed;
- equality and ordering; and
- finite decimal and binary/hexadecimal power source.

Square root, transcendental functions, representation operations, and any
operation without an exact rational result require a concrete scalar first.

An exact rational need not be expanded into repeating decimal digits. `1/3`
remains numerator/denominator conceptually, while a decimal significand with a
large exponent remains an exact scaled value. An implementation may use
destination-aware bounds or intervals when they prove the same result as exact
evaluation followed by one rounding.

Zax promises no infinite compiler capacity. Exceeding the exact-rational
capacity or work budget is a compiler resource/capacity diagnostic, not hidden
rounding in the compiler host's largest float. Compiler-host floating
representation never determines source semantics.

#### Optional binary exponent

For first-class `b` and `h`, a fractional point implies `p0` when no explicit
`p` exponent appears:

```zax
h'1.8' // equivalent to h'1.8p0'
b'1.1' // equivalent to b'1.1p0'

h'1'   // integer
h'1p0' // real 1.0
```

`p` remains useful for scaling and for making an integral-looking significand
real. This grammar belongs only to the first-class `b` and `h` parsers.
Characters such as `.`, `+`, and `p` retain their alphabet-specific meanings in
`Radix.b64`, `Radix.b94`, and custom literal operators.

#### Floating special constants

Floating special values are generated type-owned constants rather than parsed
word payloads:

```zax
Float.inf
Float.ninf
Float.nan
Float.nzero

Quad.inf
Quad.ninf
Quad.nan
Quad.nzero
```

- `inf` and `ninf` are positive and negative infinity.
- `nan` is the format's canonical quiet NaN.
- `nzero` is negative zero, which exact rational zero cannot preserve.
- Each constant has the exact receiver identity.
- A format that lacks the applicable special value does not expose it.

```zax
E4M3.inf // error: E4M3 has no infinity
MBF40.nan // error: MBF40 has no NaN
```

A canonical signaling NaN may use `snan` where the format supports one. Exact
NaN sign, payload, signaling state, or noncanonical representation uses raw
logical bits:

```zax
myPayloadNaN := Binary32.h'7FC0 0001'
```

Subnormals remain finite mathematical values:

```zax
mySmallest : Binary32 = b'1p-149'
mySame := Binary32.h'0000 0001'
```

A finite source value too small for a selected format realizes as a subnormal or
zero according to that format's rounding. Finite source overflow remains an
error rather than silently becoming infinity.

#### Mathematical scalar constants

Every applicable fixed-point and floating type supplies correctly rounded
type-owned mathematical constants:

```zax
Float.pi
Quad.e
Binary64.sqrt2
I16F8.pi
```

Each constant is rounded directly from its mathematical definition into the
receiver type; it is not derived from another already rounded constant.
`halfPi`, for example, is mathematical `pi / 2` rounded once for the type.

The aligned initial catalog is:

```text
pi
tau
e
phi

sqrt2
sqrt3

ln2
ln10
log2e
log10e

invPi
twoOverPi
invSqrtPi
halfPi
quarterPi
```

Fixed-point types expose a constant only when its correctly rounded value fits.
Integers do not expose this catalog.

Floating representation limits remain a separate catalog:

```text
maximumFinite
minimumFinite
minimumPositiveNormal
minimumPositiveSubnormal
epsilon
```

Fixed-point limits instead expose:

```text
minimum
maximum
quantum
```

Fixed point has no NaN, infinity, signed-zero class, subnormal class, or
floating epsilon.

#### Dozenal source presentation

Whenever dedicated dozenal digits appear visually, documentation also names
their code points:

```zax
myDozenal := doz'1↊↋'
// ↊ U+218A = dek/ten
// ↋ U+218B = el/eleven
```

This preserves meaning in fonts that cannot render the glyphs.

#### Explicit string code-unit backing

String code-unit representation is programmer-visible even though generic
container layout remains future work:

| Identity | Logical underlying units | Physical scalar representation |
| --- | --- | --- |
| `String` | `U8` | Unrestricted eight-bit values |
| `AsciiString` | `AsciiChar` | Strict identity over `U8`, valid `0x00..0x7F` |
| `Ucs2String` | `Ucs2` | Strict identity over `U16`, BMP nonsurrogate values |
| `Utf8String` | `U8` | Well-formed UTF-8 code units |
| `Utf16String` | `U16` | Well-formed UTF-16 code units |
| `Utf32String` | `U32` | Unicode scalar code units |
| `Rune` | one scalar | Strict identity over `U32` |
| `MbcsString<Encoding>` | `U8` | Valid sequence under the selected encoding |
| `Legacy.CharString` | `Legacy.Char` | Length-tracked platform-native narrow code units |
| optional `Legacy.WideString` | `Legacy.WChar` | Length-tracked platform-native wide code units |
| `Legacy.TerminatedCharString<Policy>` | `Legacy.Char` | Concrete policy-selected terminated representation |
| `Legacy.TerminatedWideString<Policy>` | `Legacy.WChar` | Concrete policy-selected terminated representation |

`Byte` is a strict identity over `U8`, not a transparent alias. It represents
one uninterpreted octet. `String` stores `U8` units rather than `Byte` elements.

Concrete `U16`/`U32` code-unit endianness must be visible in string type
identity. Unqualified forms follow the active environment; exact target,
compiler-host, or absolute-endian spelling remains future type-family pressure.

#### Byte and numeric-unit admission

`Byte` and singular `U8` values do not directly admit into validated text,
regardless of value:

```zax
utf8'' <+> Byte.h'41'   // error: Byte has no character semantics
utf8'' <+> (: U8 = 65) // error: U8 has numeric, not character, semantics
```

`AsciiChar`, `Legacy.Latin1Char`, `Ucs2`, and `Rune` carry character semantics
and may transcode under their destination's rules.

A complete `String` or `U8[]` may undergo whole-sequence UTF-8 validation:

```zax
myUtf8 := utf8'' <+> String.h'C2 AB'
```

`Byte.h'AB'` can join raw into `String` or `U8[]` and deliberately contribute
malformed UTF-8 there. It does not mean Unicode `U+00AB`.

`Byte.s` accepts exactly one resulting source byte; that phrase describes
source encoding length, not UTF-8 character semantics.

#### Quote counterexample

Zax source does not escape its delimiter:

```zax
c'\'' // error: the single quote closes at the Zax source layer
c"\'" // c receives backslash + quote and decodes the C escape
c"'"  // direct quote; no escape needed
```

The `c` parser recognizes `\'`; the Zax source scanner does not.

#### Optional legacy wide characters

The platform-selected legacy character identities are:

```text
Legacy.Char
Legacy.WChar
```

The platform chooses each available identity's width, signedness, encoding, and
representation. There is no separate `Legacy.UChar` or `Legacy.UWChar`.

`Legacy.Char` and `Legacy.CharString` are always defined.
`Legacy.WChar` and `Legacy.WideString` are platform-optional. A platform without
a wide-character model does not synthesize one from `I32`, `U32`, or `Rune`.
Interop-specific signed/unsigned C/C++ character identities remain future
foreign-contract work.

Future compile-time/reflection design must provide a static enclosed-source or
declaration-resolution query whose payload may mention an unavailable name
without producing an ordinary compilation error. This permits source using
`Legacy.WChar` to be included only when the platform supplies it. A quoted
string path is not preferred because it loses symbol-aware checking and
refactoring.

#### MBCS, Latin-1, and termination

MBCS is the general code-page encoded-byte family. An SBCS encoding is an MBCS
specialization whose maximum encoded-character sequence length is one.

`Legacy.Latin1Char` is the one-character specialization associated with
ISO-8859-1:

```text
Legacy.Latin1Char
  = MbcsChar<Legacy.CodePages.iso88591>
```

It is physically one `U8` and maps to Unicode `U+0000..U+00FF`.

`MbcsString<Encoding>`:

- uses `U8` units;
- validates the selected encoding;
- tracks logical byte length;
- permits embedded NUL;
- requires no trailing sentinel; and
- is not a `.c_str()` value.

Encoding and termination are separate concerns. Baseline `Legacy.CharString`
and optional `Legacy.WideString` are length-tracked, permit NUL, and promise no
sentinel or contiguous storage.

A terminated representation has a distinct concrete policy specialization:

```text
Legacy.TerminatedCharString<TerminatorPolicy>
Legacy.TerminatedWideString<TerminatorPolicy>
```

The policy owns terminator units/sequences, in-band acceptance or escaping,
contiguity, physical suffix, logical length, admission, and safe mutation.
Common NUL policies may expose `Legacy.NulTerminatedCharString` and
`Legacy.NulTerminatedWideString` aliases with no embedded NUL and `.c_str()`
semantics. Other platform/API policies may supply their own behavior.

#### Admissions to native legacy strings

Every available native legacy string validates construction from an array of
its exact code-unit type:

```text
Legacy.Char[]  -> Legacy.CharString
Legacy.WChar[] -> Legacy.WideString, when defined
```

Construction preserves the supplied logical units after validating any platform
encoding/code-unit invariant. NUL is permitted.

Additional text admission is profile-dependent. A profile with a stable Unicode
mapping may transcode ASCII, UCS-2, Rune, or UTF text and reject unrepresentable
scalars. `String` must first validate as UTF-8. A truly opaque native character
set may expose only raw-code-unit construction.

Construction of a terminated policy specialization additionally applies that
policy's terminator, in-band, contiguity, suffix, and admission rules.

#### Ordinary string storage and raw access pressure

Ordinary string identities promise their code-unit type but do not yet promise
one contiguous storage region. A future representation may be flat, segmented,
rope-like, inline, or another form.

Future string work must consider three distinct operations:

1. query whether a value already has one contiguous storage region;
2. explicitly force or coalesce it into one region, exposing allocation and
   copying; and
3. obtain an unsafe raw array pointer only from that proven/forced contiguous
   region.

The pointer exposes the exact underlying code-unit type and its concrete
endianness. Mutation may violate string invariants; lifetime and invalidation
remain caller responsibility. A safe readonly contiguous view is useful future
pressure but is not promised here.

Terminated policy specializations are exceptions when their policy requires
contiguous storage. NUL-terminated policies provide the applicable `.c_str()`
contract.

### Previously promoted aligned model

The following model records the promotion state before the refinements above.
Where it requires explicit-only `<|>`, concrete pre-operation real arithmetic,
mandatory `p0`, direct low-byte admission to UTF, paired
`Legacy.Char`/`Legacy.UChar` or `Legacy.WChar`/`Legacy.UWChar`, NUL-terminated
MBCS, or generally contiguous string storage, the current refinements above
supersede it.

The findings and reviewed catalogs in this section are aligned for the current
review scope but remain non-authoritative until separately promoted. Explicit
deferrals preserve unresolved implementation, runtime-string, generic,
code-page-data, alias/import, reflection, and interoperability design without
weakening the aligned literal model.

#### Programmer model

Zax has ordinary numeric and string source, attached literal-operator source,
an explicit source-payload merger, and an explicit value join:

```zax
myInteger := 55
// Uncommitted integer; suggested Integer when no other type is selected.

myReal := 1.5
// Uncommitted real; suggested Float when no other type is selected.

myText := "ordinary text"
// String; shorthand for the language-provided s literal.

myRadixInteger := h'FF'
// Uncommitted mathematical integer 255.

myPattern := I8.h'FF'
// Concrete I8 raw logical-bit pattern, value -1.

myMerged := s"contains '" <|> s' and "'
// Merge source payloads, then invoke s once.

myJoined := utf8'first line' <+> c'\n' <+> utf8'last line'
// Invoke each literal independently, then perform explicit compile-time joins.
```

There is no side-by-side literal operation:

```zax
x'first' x'second' // error: state merge or join intent
```

`<|>` and `<+>` answer different questions:

- `<|>` is a compiler-understood, non-overloadable source operation. It combines
  payload segments before one literal-operator invocation.
- `<+>` is an overloadable value operation. Each operand is parsed independently
  and the selected join combines their already concrete constant results.

#### Literal phrase names, attachment, and delimiters

A literal operator has one quoted lower-case phrase word:

```zax
operator literal 'cstyle' ...
operator literal 'b64' ...

operator literal 'c style' ... // error: literal phrase names are one word
```

The aligned initial word shape is ASCII `[a-z][a-z0-9]*`. A qualified owner path
uses ordinary path components; only the final literal phrase name is quoted in
its declaration:

```zax
MyType.cstyle'payload'
Rfc4648.Encoding.base64'VGhlIHF1aWNrIGJyb3duIGZveC4='
```

Attachment remains the source boundary already owned by source structure:

```zax
h'FF'       // attached literal payload
h 'FF'      // separated phrase fence, not a literal
"text"      // ordinary String shorthand
'text'      // phrase fence or phrase diagnostic, not a String
''          // error: empty phrase fence
s''         // attached empty literal; s decides whether it is valid
```

An attached literal may use either quote kind. The opening quote chooses the
closing quote. Zax performs no built-in escaping, and a backslash is an ordinary
payload character:

```zax
c'\n'                 // c receives backslash followed by n
s'my "quoted" text'   // the double quote is direct payload
s"single quote: '"    // the single quote is direct payload
```

The matching delimiter cannot occur directly in that segment. Use the other
delimiter or `<|>`:

```zax
myText := s"both ' kinds: " <|> s'"'
```

Backticks are not literal delimiters.

#### Source admission and payload

The source scanner, before any literal operator runs:

- requires well-formed UTF-8 source;
- rejects physically present C0 controls `U+0000..U+001F` inside quoted source;
- recognizes the matching quote as the segment terminator;
- preserves every other admitted payload scalar exactly;
- performs no escapes and no Unicode normalization; and
- retains source spans for diagnostics and reflection.

The C0 restriction applies only to physical source admission. Literal operators
may produce every `U8` value, including NUL, tab, CR, LF, and bytes that do not
form valid UTF-8.

Unicode noncharacters remain valid Unicode scalar values and are admitted when
their UTF-8 source encoding is well formed. Surrogate code points are not
Unicode scalars and cannot appear as valid UTF-8 source. No assigned/unassigned
or normalization policy silently changes payload admission.

A literal operator receives one `String` containing the admitted payload encoded
as UTF-8. Delimiters, `<|>`, comments, continuation, indentation, and
inter-segment trivia are not part of that value. Source metadata retains those
facts separately.

#### Explicit source merge with `<|>`

`<|>` combines two source segments that independently resolve to the same
literal-operator declaration and invokes that declaration once:

```zax
myValue := x"first '" <|> x' second"'
// One x invocation with payload: first ' second"
```

Qualification may deliberately verify an unqualified alias:

```zax
MyNamespace.x'first' <|> x'second'
// Valid when x independently resolves to the exact MyNamespace.x declaration.
```

The qualified side does not disambiguate the unqualified side. If `x` is
ambiguous, merge is ambiguous; if it uniquely resolves to another declaration,
merge reports a mismatch:

```zax
x'first' <|> y'second' // error: different declarations
```

Equal owner type or equal result identity is insufficient. The aliases must
identify the same literal declaration and implementation. A mismatch is a
non-acknowledgeable error: an intent enclosure cannot cause two different
parsers to receive one payload.

`<|>` forms a literal source group before ordinary expression parsing. It is
therefore effectively tighter than every value operator rather than occupying
an ordinary precedence level:

```zax
myValue := x'a' <|> x'b' <+> y'c'
// Parse x with `ab`, parse y with `c`, then join the results.
```

A chain invokes one parser:

```zax
myValue := x'a' <|> x'b' <|> x'c'
// One x invocation with `abc`.
```

Grouping may surround a complete merge but cannot turn an expression back into
literal source:

```zax
use((x'a' <|> x'b')) // grouping after source merge

(x'a') <|> x'b' // error: left operand is already an expression
(x'a') <+> (x'b') // value join; see same-prefix intent below
```

A trailing `<|>` establishes continuation under the symbolic-infix source rule:

```zax
myValue := x'first ' <|>
  x'second'
```

An explicit `\` on that same newline is redundant. Comments and continuation
trivia follow ordinary Zax source rules and contribute no payload characters.

An empty attached payload is passed to its literal operator:

```zax
x''
```

An empty operand of `<|>` contributes nothing and is a non-acknowledgeable
redundant-structure intent error:

```zax
x'' <|> x'value' // error: remove the empty segment
```

#### Explicit value join with `<+>`

`<+>` is an overloadable binary value operator. For literal joining:

1. evaluate the left literal operator during compilation;
2. evaluate the right literal operator during compilation;
3. select the ordinary `<+>` candidate from the concrete result identities;
4. require the selected declaration to be available for compile-time execution;
5. execute it during compilation; and
6. produce its one concrete constant result.

Selection never falls through from a selected runtime-only or otherwise
compile-time-unavailable declaration to a weaker candidate.

The left operand owns the destination/result identity and admission policy:

```zax
utf8'text' <+> c'\n' // Utf8String result after validating c's String bytes
s'bytes' <+> utf8' text' // String result containing UTF-8 bytes
```

Joining two uses of the same literal prefix is defined but resembles a mistaken
`<|>`:

```zax
myValue := x'first' <+> x'second'
// intent error: did the programmer mean one x invocation?
```

The programmer confirms that both parser invocations are deliberate:

```zax
myValue := intent<same-prefix-literal-join>{
  x'first' <+> x'second'
}
```

`same-prefix-literal-join` applies when the immediate literal operands resolve
to the same literal declaration and `<|>` would otherwise have been available,
including a qualified/unqualified alias pair:

```zax
myValue := intent<same-prefix-literal-join>{
  MyNamespace.x'first' <+> x'second'
}
```

The enclosure changes no evaluation or result. Both literal operators still
execute independently before `<+>`.

Runtime `<+>` remains future string/operator input. Literal joining is
compile-time-only, and the compiler does not seek a runtime implementation when
required compile-time joining is unavailable.

#### Literal declarations

The aligned declaration shape is:

```zax
MyType :: type {
  operator literal 'cstyle' final once : (
    result : MyType
  )(
    payload : String
  ) = {
    // Parse payload and produce one compile-time-available MyType.
  }
}
```

`final once` follows the current type-callable declaration order. `once` states
that the owner supplies one shared implementation; it does not itself imply
compile-time execution. `operator literal` makes the declaration inaccessible
outside a compile-time literal scenario.

A literal declaration:

- has one `String` payload input;
- has exactly one concrete or explicitly uncommitted scalar result;
- has no runtime literal invocation path;
- has no instance receiver at the use;
- may be owned by a type or namespace;
- may be exposed under a shorter name by future alias/import behavior; and
- can deliberately reject its payload through a compile-time diagnostic
  facility whose exact syntax remains future compile-time work.

A reusable parser needed outside literal syntax is an ordinary compile-time
function called by the literal operator rather than an ordinary call surface
implicitly granted to the operator declaration.

Prefix lookup uses lexical visibility and explicit qualified paths. It does not
infer an owner from the expected result, search every type, or use a surrounding
operator operand as a receiver:

```zax
myValue : MyType = parse'payload'
// MyType does not cause lookup of MyType.parse.
```

If several visible declarations expose the same unqualified prefix, the use is
ambiguous and must be qualified. Source, declaration, import, and module order
do not break the tie.

Exact alias/import syntax, visibility details, and `forward operator literal`
are deferred as indexed alias/import/declaration pressure. The eventual design
must retain deterministic qualification and may not reintroduce order-based
operator lookup.

#### Uncommitted scalar results

`uncommitted` is an explicit generic literal-result posture, initially limited
to scalar source. It is not an anonymous, flexible, or runtime type:

```zax
operator literal 'h' final once : (
  result uncommitted : UInteger
)(
  payload : String
) = {
  // Produce a nonnegative mathematical integer.
}
```

Here `UInteger` is a suggested concrete specialization when no other result type
is supplied:

```zax
myDefault := h'FF'  // commits to suggested UInteger
myByte : U8 = h'FF' // commits directly to U8
```

Before the literal body executes, compile-time/generic processing uses the
payload, applicable candidate hole, sign intent, and declared suggestion to
select exactly one concrete result type. The literal body is then processed and
invoked under that concrete specialization. The compiler never invokes a
literal operator with an anonymous or unresolved output type, and the
surrounding expression sees only the one concrete result.

During specialization the compiler tracks the mathematical scalar source
category, value, and sign intent where applicable. That specialization process:

- may fill the same scalar candidate holes as ordinary numeric source;
- uses the literal declaration's suggested type when no other commitment
  boundary exists;
- never uses value magnitude to rank overloads;
- does not retry another overload or wider type after selected range failure;
- produces one concrete callable result prototype before ordinary typed
  compilation, storage, result routing, or `<+>` continues; and
- applies every ordinary result, operator, conversion, range, and identity rule
  as though the declaration had stated that selected concrete type directly.

The posture is therefore generic declaration input rather than a typeless
function result. Extending it to custom numeric families requires future
numeric/generic review.

A fixed suggested `UInteger` does not automatically widen for an enormous
unanchored payload:

```zax
myHuge := h'FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF'
// Error if the value does not fit the suggested UInteger.

myHuge : U128 = h'FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF'
// Directly realize in the stated destination when it fits.
```

A future literal/type factory may compute a payload-dependent suggested concrete
type during specialization. That remains bounded generic and compile-time
pressure, not range-failure fallback.

The suggested type does not bind sign intent. If unqualified `h` established
*unsigned intent*, current uncommitted-integer rules would make `-h'FF'`
invalid, conflicting with the aligned rule that the sign remains outside a
radix payload. Therefore:

- `h'FF'` begins with unknown sign intent and suggested `UInteger`;
- while its value remains nonnegative and intent remains unknown, an inferred
  boundary uses that suggestion;
- outer negation remains mathematical and legal;
- if the suggestion is incompatible with the final value or explicit sign
  intent, commitment uses the ordinary compatible `Integer`/`UInteger` default
  selected from that final intent rather than treating the suggestion as an
  already selected type; and
- once a concrete destination or candidate is selected, range failure still
  never retries another type.

This separates a default suggestion from binding sign intent.

Language-provided `b` and `h` additionally distinguish integer and real payload
grammar by the presence of a required `p` exponent. One generic declaration
specializes to one result:

- an integer payload specializes to an applicable candidate-hole type or
  suggested `UInteger`; and
- a `p` real payload specializes to an applicable fixed/floating candidate-hole
  type or suggested `Float`.

This is not result overloading. Exact generic declaration syntax, the
compile-time type-selection hook, and how the body observes its one selected
result type remain deliberately unspecified pressure for generic and
compile-time design. The observable literal rules operate only on the selected
concrete specialization.

#### Ordinary decimal real source

Ordinary decimal real source is uncommitted with suggested `Float`:

```zax
myDefault := 1.5 // Float
myExact : Binary64 = 1.5
myFixed : I16F8 = 1.5
```

Digits are required on both sides of the decimal point:

```zax
0.5
1.0

.5 // error: write 0.5
1. // error: write 1.0
```

Decimal `e`/`E` retains its current power-of-ten meaning. It has an optional
attached sign and required decimal exponent digits:

```zax
1e3
1.5E+3
1.5e-3

1e  // error
1e+ // error
e3  // not a number literal
```

A destination selects the concrete scalar operation before arithmetic occurs:

```zax
myValue : Binary32 = 0.1 + 0.2
```

Both literals first realize as `Binary32`; the selected `Binary32 + Binary32`
operation then performs its own rounding. Zax does not evaluate an unlimited
exact-real expression and round only once at the final destination:

```zax
myValue : Binary32 =
  16777216.0 + 1.0 - 16777216.0
// Binary32 operation sequence produces 0, not exact-real 1.
```

Without another anchor, the same operation uses `Float`. This is closer to
contextual concrete operation selection than to C++'s fixed unsuffixed `double`
rule.

#### Binary power exponents

Binary and hexadecimal payloads may use `p`/`P` for a power-of-two exponent:

```zax
myHex : Binary64 = h'1.8p+10' // 1.5 * 2^10
myBinary : I16F8 = b'1.1p-3'  // 1.5 * 2^-3
h'1.8'                        // equivalent to h'1.8p0'
```

- significand digits use the prefix radix;
- exponent digits are decimal;
- the exponent has an optional sign;
- a fractional point implies `p0` when no exponent is written;
- `b`/`h` without a point or `p` produces uncommitted integer source;
- `b`/`h` with a point or `p` produces uncommitted real source with suggested
  `Float`;
- type-qualified raw-pattern literals reject a point or exponent; and
- octal, decimal, dozenal, and arbitrary-base payloads receive no exponent merely
  for symmetry.

### Aligned numeric literal catalog

This catalog applies the aligned semantics to the complete maintainer inventory.
The main names, less-common namespace placement, digit aliases, and grouping
details have been reviewed as part of the aligned working model.

#### First-class main prefixes

| Prefix | Radix | Accepted digits | Result without destination |
| --- | ---: | --- | --- |
| `b` | 2 | `0 1` | Uncommitted integer, suggested `UInteger`; `p` form is uncommitted real, suggested `Float` |
| `o` | 8 | `0` through `7` | Uncommitted integer, suggested `UInteger` |
| `dec` | 10 | `0` through `9` | Uncommitted integer, suggested `UInteger` |
| `doz` | 12 | `0` through `9`; `X`/`T`/`↊` for dek/ten; `E`/`↋` for el/eleven; ASCII letters case-insensitive | Uncommitted integer, suggested `UInteger` |
| `h` | 16 | `0` through `9`, `A` through `F`, ASCII case-insensitive | Uncommitted integer, suggested `UInteger`; `p` form is uncommitted real, suggested `Float` |

`dec` and `doz` avoid assigning ambiguous `d`. `doz` gives the dozenal
community's dek/el spellings primary status:

```zax
doz'1XE'
doz'1TE'
```

A systematic `Radix.b12` form may use `A` and `B` as digit values ten and
eleven without making that computing-oriented alphabet the canonical `doz`
spelling:

```zax
Radix.b12'1AB'
```

`doz` also accepts the established Unicode dozenal digits `↊` (`U+218A`) for
dek/ten and `↋` (`U+218B`) for el/eleven. Formatters and source reflection
preserve whether the programmer used `X`, `T`, `E`, `↊`, or `↋`.

#### Less-common positional numeral catalog

These are useful standard/library literal declarations under `Radix`, not
recommended main-namespace reservations:

| Path | Radix | Digit sequence in increasing value order | Notes |
| --- | ---: | --- | --- |
| `Radix.b12` | 12 | `0123456789AB` | Systematic computing alphabet |
| `Radix.b20` | 20 | `0123456789ABCDEFGHIJ` | ASCII case-insensitive |
| `Radix.b32` | 32 | `0123456789ABCDEFGHJKMNPQRSTVWXYZ` | Canonical Crockford alphabet; ambiguous-character aliases need a separate decision |
| `Radix.b36` | 36 | `0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ` | ASCII case-insensitive |
| `Radix.bitcoin58` | 58 | `123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz` | Case-sensitive Bitcoin alphabet |
| `Radix.b60` | 60 | `0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwx` | Case-sensitive |
| `Radix.b62` | 62 | `0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz` | Case-sensitive |
| `Radix.b64` | 64 | `ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/` | Case-sensitive; `A` has digit value zero |
| `Radix.b94` | 94 | every ASCII character from `!` (`U+0021`) through `~` (`U+007E`) | Case-sensitive; quote digits require alternate delimiters and `<|>` |

The namespace distinguishes a positional numeral from a byte encoding:

```zax
myNumber := Radix.b64'9+/'
myBytes := Rfc4648.Encoding.base64'VGhlIHF1aWNrIGJyb3duIGZveC4='
```

RFC 4648 calls the latter “Base 64 Encoding,” but it maps bytes to text rather
than interpreting the text as one radix-64 positional number.

#### Sign, leading zeros, and grouping

Built-in numeric payloads contain no sign. Use ordinary pre-unary source:

```zax
-h'FF' // negative mathematical radix value
+h'FF' // applies the ordinary uncommitted sign-intent operation

h'-FF' // error
```

Leading zeros are accepted and have no effect on mathematical value or
type-qualified raw-pattern fit:

```zax
h'00FF'
I8.h'000000FF' // same logical pattern as I8.h'FF'
```

ASCII space is the sole built-in digit-grouping separator:

```zax
b'11110000 10101010'
dec'1 000 000'
doz'1 000 000'
h'FFFF FFFF'
h'FF FF FF FF'
```

For one grouped digit run:

- one ASCII space separates groups;
- no leading, trailing, or repeated space is accepted;
- all groups after the first have one equal width;
- the first group has from one through that many digits;
- spaces contribute no value or width; and
- commas and underscores are rejected by these built-ins, while custom literal
  operators may define them.

For a `b`/`h` real significand, the integral and fractional digit runs apply
grouping independently. A space cannot touch `.`, `p`, the exponent sign, or the
exponent digits. The decimal exponent itself is not grouped.

Malformed built-in payloads distinguish invalid digits, unsupported sign,
missing digits, misplaced/inconsistent grouping, unsupported point/exponent, and
compiler magnitude/resource failure.

#### Type-qualified raw-pattern forms

The first-class base phrase names are also available under exact integer,
fixed-point, and floating scalar identities:

```zax
I8.h'FF'
U8.dec'255'
I16F8.h'0180'
Binary32.h'3F80 0000'
```

For a scalar with `W` logical representation bits:

1. remove valid grouping;
2. parse one nonnegative mathematical integer `N`;
3. require `0 <= N < 2^W`;
4. place the most-significant written digit on the left;
5. interpret the resulting `W` logical bits under the destination type; and
6. ignore storage-envelope padding and physical byte endianness.

Integer results use the type's signedness and two's-complement interpretation:

```zax
I8.h'FF'    // -1
I8.dec'128' // -128
U8.dec'255' // 255
I8.h'100'   // error: pattern needs more than 8 logical bits
```

Fixed-point results interpret those bits as the coefficient:

```zax
I16F8.h'0180'
// Coefficient 384, represented value 1.5.
```

Floating results adopt exact logical format bits:

```zax
Binary32.h'3F80 0000' // 1.0
```

Every logical bit must be supplied by the parsed value after zero extension.
Valid noncanonical patterns are preserved. Formats with invalid patterns reject
them. Unspecified storage padding is neither supplied nor stabilized.

Type-qualified raw-pattern forms do not accept `.` or `p`; write an unqualified
uncommitted real and provide the typed destination:

```zax
myValue : Binary32 = h'1p0'
```

The earlier literal-only type suffix is superseded:

```zax
55 : U8 // error: no literal-only postfix type syntax

myValue : U8 = 55   // mathematical realization
myBits := U8.dec'55' // explicit raw-pattern source
```

### Aligned string, Unicode, and character model

#### Identity layers

`String` is a sequence of unrestricted eight-bit values. Ordinary source and
literal payload inputs begin as valid UTF-8, but the `String` identity does not
preserve that invariant after arbitrary byte construction:

```zax
myText := "hello ☃" // valid UTF-8 bytes in String
myBytes := String.h'00 FF' // raw String bytes, not valid UTF-8
```

Admission from `String` to `Utf8String` validates the complete byte sequence.
Invalid UTF-8 remains legal `String` data and is rejected only at that text
boundary.

`Rune` represents exactly one Unicode scalar value. It excludes surrogate code
points. It does not represent a grapheme cluster:

```text
U+00E9                 one Rune
U+0065 U+0301          two Runes, often displayed like U+00E9
family emoji sequence  several Runes, often displayed as one grapheme
```

Strings may contain any valid sequence of their admitted elements. Combining
order, grapheme segmentation, rendering support, and visually suspicious
sequences belong to presentation or higher-level text validation. Zax does not
implicitly normalize or reject them at the string layer.

UTF-16 uses a high/low surrogate pair to encode one supplementary scalar. A
validated UTF-16 string accepts valid pairs and rejects isolated surrogates.
Conversion decodes a pair to one scalar and then encodes that scalar normally
for the destination, including four UTF-8 bytes where applicable.

### Aligned text and character identity catalog

The following inventory reconciles the maintainer notes with current integer
identity boundaries. `AsciiChar` is a semantic identity over underlying `U8`
storage with a seven-bit valid set. Values with the high bit set remain valid
`Byte`, `U8`, or encoded legacy data but are not “extended ASCII,” because no
single extended-ASCII mapping exists.

The prior unqualified `Char`, `UChar`, `WChar`, and `UWChar` roles move under
`Legacy`. They describe profile-native narrow and wide character storage and
encoding rather than ordinary Zax text. `Legacy` means interoperability with a
legacy-era character model, not deprecated or unsupported.

| Identity | Storage/element model | Valid value set | Intended role |
| --- | --- | --- | --- |
| `Byte` | Distinct exact eight-bit byte identity | `0x00..0xFF` | One raw byte |
| `AsciiChar` | Distinct identity with underlying `U8` storage | Unicode/ASCII `U+0000..U+007F`; `0x80..0xFF` are invalid representations | One stable ASCII character |
| `Ucs2` | Semantic 16-bit scalar identity | `U+0000..U+FFFF`, excluding surrogates | One BMP scalar; no supplementary values |
| `Rune` | Semantic Unicode scalar identity | `U+0000..U+10FFFF`, excluding surrogates | One Unicode scalar |
| `Legacy.Char` / `Legacy.UChar` | Profile-selected signed/unsigned native narrow-character identity | Exact native narrow code-unit range and encoding | Legacy/native narrow character |
| `Legacy.WChar` / `Legacy.UWChar` | Profile-selected signed/unsigned native wide-character identity | Exact native wide code-unit range and encoding | Legacy/native wide character |
| `String` | Sequence of unrestricted eight-bit values | Every byte sequence | General byte string |
| `AsciiString` | Sequence of `AsciiChar` values | Every ASCII sequence, including controls produced indirectly | Validated ASCII text |
| `Ucs2String` | Sequence of `Ucs2` values | BMP scalar sequence without surrogates | BMP-only scalar text |
| `Utf8String` | Sequence of `U8` code units | Well-formed UTF-8 | Validated Unicode text |
| `Utf16String` | Sequence of `U16` code units | Well-formed UTF-16, including paired surrogates | Validated Unicode text |
| `Utf32String` | Sequence of `U32` units | Every unit is a Unicode scalar | Validated Unicode text |
| `Legacy.CharString` | Sequence of `Legacy.Char` code units plus trailing NUL | Valid under the selected native narrow encoding; no embedded NUL | NUL-terminated native narrow string |
| `Legacy.WideString` | Sequence of `Legacy.WChar` code units plus trailing wide NUL | Valid under the selected native wide encoding; no embedded NUL | NUL-terminated native wide string |
| `Legacy.MbcsString<CodePage>` | Sequence of `U8` code units under one exact code page plus trailing NUL | Valid sequence under the exact code page; no embedded NUL | NUL-terminated legacy encoded string |
| `U8[]` | Raw array | Every byte sequence | Binary data, not text |

`Legacy.MbcsString<CodePage>` and the shorter
`Legacy.MbcsString<P>` notation below describe a relationship, not accepted
generic Zax syntax. Exact code-page-dependent type formation remains future
text/generic work.

Noncharacters remain valid in `Ucs2`, `Rune`, and validated UTF strings.
Assigned-character status is not an identity invariant and therefore does not
change with a Unicode database version.

On a profile with ordinary eight-bit native characters, `Legacy.Char` and
`Legacy.UChar` may use `I8` and `U8` representation while retaining distinct
identities. Their mapping is still a profile fact rather than an ASCII
assumption. The wide pair likewise follows the profile's native legacy wide
representation, which may be sixteen bits, thirty-two bits, or another declared
width and encoding.

Each `Legacy` string has logical elements in `[0, length)` and a zero code unit
at `element[length]`. The sentinel:

- is excluded from length, iteration, comparison, hashing, and joining;
- exists for an empty string;
- requires storage for at least `length + 1` elements;
- is restored after every safe mutation or reallocation; and
- enables a contiguous `.c_str()`-style view with an applicable lifetime.

Embedded zero code units are rejected so that the complete logical string, not
merely a prefix, is observed by a C-style consumer. Arbitrary code-unit data
with embedded zero remains an array or ordinary `String`. Writable raw access
that can violate the terminator or encoding invariant requires constrained or
unsafe handling.

Ordinary `String`, `AsciiString`, `Ucs2String`, and UTF strings have no trailing
NUL requirement. Modern or Unicode-oriented source should prefer those
identities; the `Legacy` family remains available for native ABI and historical
encoding interoperability.

`Legacy.Latin1Char` is a useful exact encoded-character identity with underlying
`U8`: byte values `0x00..0xFF` map to Unicode `U+0000..U+00FF` under
ISO-8859-1. It is one character, not a string. The corresponding sequence is
conceptually:

```text
Legacy.MbcsString<Legacy.CodePages.iso88591>
```

or a future more generally named encoded-string specialization. Thus:

- `AsciiChar` is one seven-bit ASCII character and converts totally to Unicode;
- `Legacy.Latin1Char` is one ISO-8859-1 character and also has a fixed Unicode
  mapping;
- `Byte` is one uninterpreted eight-bit value; and
- `Legacy.MbcsString<...>` is a sequence under one exact single- or multibyte
  encoding.

Calling the whole family `MbcsString` remains reviewable because ISO-8859-1 is
single-byte. A future string owner may prefer `Legacy.EncodedString<Encoding>`
as the general family and retain `MbcsString` only where the selected encoding
actually uses variable-width multibyte sequences.

#### Namespace and type-spacing policy

The aligned organization uses type-owned declarations as the durable
definitions, short main-namespace aliases for frequent stable forms, and named
semantic namespaces for specialized/versioned transforms.

Recommended main aliases:

```text
s, c
b, o, dec, doz, h
byte, rune
ascii, utf8, utf16, utf32
```

Representative type-owned forms:

```zax
String.s'plain bytes from source text'
String.c'line one\nline two'
String.h'00 FF'

Byte.s'A'
Byte.c'\n'
Byte.h'0A'

AsciiChar.s'A'
AsciiChar.c'\t'
AsciiChar.h'7F'

Ucs2.s'☃'
Ucs2.c'\u2603'
Ucs2.h'2603'

Rune.s'☃'
Rune.c'\u2603'
Rune.h'2603'

Utf8String.s'valid Unicode text'
Utf16String.s'valid Unicode text'
Utf32String.s'valid Unicode text'

Legacy.Latin1Char.s'ÿ'
Legacy.Latin1Char.h'FF'
Legacy.Char.s'A'
Legacy.WChar.s'W'
Legacy.CharString.s'native string'
Legacy.WideString.s'native wide string'
```

The short aliases conceptually route to common type-owned declarations:

```text
s      -> String.s
c      -> String.c
byte   -> Byte.s
rune   -> Rune.s
ascii  -> AsciiString.s
utf8   -> Utf8String.s
utf16  -> Utf16String.s
utf32  -> Utf32String.s
```

Exact alias syntax remains deferred. The organization prevents suffix-packed
names from multiplying with every type and transform.

Specialized namespaces:

```zax
Rfc4648.Encoding.base64'...'
Xml.entities'...'
Legacy.CodePages.cp1252'...'
Radix.b32'...'
```

A prefix belongs in the main namespace when it is frequent, stable across
versions/locales, clear about its result category, and valuable enough to
justify a permanent short spelling. Generous support means specialized forms
exist and are discoverable, not that every alias is unqualified.

#### Character literal catalog

| Main/type-owned form | Input interpretation | Result | Required validation |
| --- | --- | --- | --- |
| `byte'X'` / `Byte.s'X'` | Direct source UTF-8 bytes | `Byte` | Exactly one resulting byte |
| `Byte.c'\n'` | C escape language | `Byte` | Exactly one decoded byte |
| `Byte.h'0A'` | Hex magnitude | `Byte` | `0..255` |
| `AsciiChar.s'X'` | Direct source scalar | `AsciiChar` | Exactly one scalar in `U+0000..U+007F` |
| `AsciiChar.c'\n'` | C escape language | `AsciiChar` | Exactly one decoded ASCII scalar |
| `AsciiChar.h'0A'` | Hex scalar | `AsciiChar` | At most `0x7F` |
| `Ucs2.s'☃'` | Direct source scalar | `Ucs2` | Exactly one BMP nonsurrogate scalar |
| `Ucs2.c'\u2603'` | C escape language | `Ucs2` | Exactly one decoded BMP nonsurrogate scalar |
| `Ucs2.h'2603'` | Hex scalar | `Ucs2` | BMP nonsurrogate |
| `rune'☃'` / `Rune.s'☃'` | Direct source UTF-8 | `Rune` | Exactly one Unicode scalar |
| `Rune.c'\u2603'` | C escape language | `Rune` | Exactly one decoded Unicode scalar |
| `Rune.h'2603'` | Hex scalar | `Rune` | Unicode scalar range, excluding surrogates |
| `Legacy.Latin1Char.s/c/h` | Direct, escaped, or numeric ISO-8859-1 character | `Legacy.Latin1Char` | Exactly one value in `U+0000..U+00FF` |
| `Legacy.Char.s/c/h` / `Legacy.UChar.s/c/h` | Direct, escaped, or numeric native narrow unit | Applicable narrow identity | Validate under the selected legacy narrow-character profile |
| `Legacy.WChar.s/c/h` | Direct, escaped, or numeric native wide unit | `Legacy.WChar` | Validate under the selected legacy wide-character profile |
| `Legacy.UWChar.s/c/h` | Direct, escaped, or numeric native wide unit | `Legacy.UWChar` | Validate under the selected legacy wide-character profile |

The source scanner ensures the payload is admitted UTF-8; the literal operator
performs element-count and domain validation. `rune'é'`, when written as `e`
plus combining acute, is rejected because it contains two scalars even if it
displays as one grapheme.

Aligned disposition of legacy compact names:

| Legacy names | Aligned replacement |
| --- | --- |
| `by`, `byc`, `byh` | `byte`/`Byte.s`, `Byte.c`, `Byte.h` |
| `ch`, `chc`, `chh` | `AsciiChar.s`, `AsciiChar.c`, `AsciiChar.h` |
| `bmp16`, `bmp16c`, `bmp16h` | `Ucs2.s`, `Ucs2.c`, `Ucs2.h` |
| `rune`/`u`, `uc`, `uh` | `rune`/`Rune.s`, `Rune.c`, `Rune.h` |
| `wch`, `wchc`, `wchh` | `Legacy.WChar.s`, `Legacy.WChar.c`, `Legacy.WChar.h` |

The replacement preserves the transformations while making the result identity
and shared `s`/`c`/`h` vocabulary visible.

#### Direct string literal catalog

| Main/type-owned form | Input interpretation | Result | Validation |
| --- | --- | --- | --- |
| `""`, `s''`, `String.s''` | Direct source characters encoded as UTF-8 bytes | `String` | Source admission only |
| `c''`, `String.c''` | C-style escapes producing bytes/UTF-8 | `String` | Escape grammar |
| `String.h''` | Pairs of hexadecimal digits | `String` | Whole bytes; arbitrary result bytes |
| `ascii''`, `AsciiString.s''` | Direct source scalars | `AsciiString` | Every scalar at most `U+007F` |
| `utf8''`, `Utf8String.s''` | Direct source scalars encoded as UTF-8 | `Utf8String` | Always valid after source admission |
| `utf16''`, `Utf16String.s''` | Direct source scalars encoded as UTF-16 | `Utf16String` | Generate valid surrogate pairs |
| `utf32''`, `Utf32String.s''` | Direct source scalars encoded as UTF-32 | `Utf32String` | One scalar per `U32` unit |
| `Text.ucs2''`, `Ucs2String.s''` | Direct source scalars | `Ucs2String` | Reject supplementary scalars |
| `Legacy.CharString.s''` | Direct source converted under the selected native narrow-character profile | `Legacy.CharString` | Valid encoding, no embedded NUL, trailing NUL |
| `Legacy.WideString.s''` | Direct source converted under the selected native wide-character profile | `Legacy.WideString` | Valid encoding, no embedded NUL, trailing wide NUL |
| `Legacy.CodePages.cp1252''` and peers | Direct source converted under the named code page | Code-page-specific `Legacy.MbcsString` | Every scalar representable, no embedded NUL, trailing NUL |

Representative raw encoded forms:

```zax
myBytes := String.h'00 0A 0D FF'
myUtf8 := Utf8String.h'E2 98 83'
myUtf16 := Utf16String.h'D83D DE00'
myUtf32 := Utf32String.h'0001F600'
```

For a validated destination, `Type.h` parses logical code units and validates
the complete resulting sequence. `String.h` permits every byte sequence.
Code-unit digit order is most-significant first and independent of target byte
endianness.

The legacy `hex` candidate becomes `String.h`; legacy `rhex` becomes an explicit
validated destination such as `Utf8String.h`, avoiding a second opaque catalog.

#### Aligned C-style escape set

`c` is a literal parser, not Zax lexical escaping. Its aligned initial set is:

```text
\\  \'  \"
\0  \a  \b  \t  \n  \v  \f  \r
\ooo
\xHH
\uHHHH
\UHHHHHHHH
```

- `\ooo` consumes one through three octal digits greedily and must produce a
  value no greater than `0xFF`; `\0` is its one-digit NUL form.
- After three octal digits, another digit is ordinary payload, so `\1234`
  decodes octal `123` followed by `4`.
- An octal value such as `\400` is rejected rather than truncated.
- `\xHH` uses exactly two hexadecimal digits and emits one byte.
- `\uHHHH` and `\UHHHHHHHH` name one Unicode scalar and emit its UTF-8 bytes.
- Surrogate or out-of-range scalar escapes are rejected.
- Unknown, incomplete, or overlong escapes are rejected.

Using the alternate source delimiter usually removes any need to escape a quote,
but quote escapes remain meaningful input to the C parser:

```zax
c"\'" // parser receives backslash + single quote
c'\"' // parser receives backslash + double quote
```

#### Transform and encoded-data catalog

| Path | Payload interpretation | Result |
| --- | --- | --- |
| `Rfc4648.Encoding.base16` | RFC 4648 Base16 text | `U8[]` |
| `Rfc4648.Encoding.base32` | RFC 4648 Base32 text | `U8[]` |
| `Rfc4648.Encoding.base32hex` | RFC 4648 extended-hex Base32 text | `U8[]` |
| `Rfc4648.Encoding.base64` | RFC 4648 Base64 text | `U8[]` |
| `Rfc4648.Encoding.base64url` | RFC 4648 URL/file-safe Base64 text | `U8[]` |
| `Xml.entities` | XML character/entity references in text | `Utf8String` |

RFC padding acceptance/canonicality, accepted case where applicable, and XML
version/entity policy must be explicit in those declarations. `Rfc4648` alone
is not a literal prefix because the RFC defines several encodings.

The encoded-data results are byte arrays rather than `String`: their payload is
text, but their decoded value is binary data. A programmer may explicitly join
or construct a `String` when byte-string identity is desired.

#### Code-page catalog

Each code-page prefix returns
`Legacy.MbcsString<ThatExactCodePage>` and belongs under
`Legacy.CodePages`:

```zax
myLegacy := Legacy.CodePages.cp1252'price: €10'
```

Aligned initial name catalog retained from the maintainer notes; exact mapping
tables remain deferred:

```text
Windows:
  cp1250 cp1251 cp1252 cp1253 cp1254 cp1255 cp1256 cp1257 cp1258
  cp932 cp936 cp949 cp950

DOS/OEM:
  cp437 cp850 cp852 cp855 cp857 cp858 cp866

ISO:
  iso88591 iso88592 iso88595 iso88597 iso88598 iso88599 iso885915

Separately named non-Windows encodings:
  shiftjis gb2312 gbk big5 euckr
```

The separately named forms are not assumed aliases:

- CP932/Windows-31J is not identical to base Shift_JIS;
- CP936 and a named GBK contract require exact mapping comparison;
- CP949/UHC extends ordinary EUC-KR;
- CP950 and a named Big5 contract may differ; and
- aliases are accepted only when their complete byte-to-scalar mappings agree.

Exact mapping tables and version identifiers belong to future text/encoding
design. They must be stable inputs rather than compiler-host locale behavior.

### Aligned directional join catalog

All automatic-looking adjacency has been removed; every value join writes
`<+>`. The common rule is:

```text
left sequence identity
<+> right value
-> admit/transcode right under the left identity's rules
-> append
-> return the left identity
```

Every literal operand and join executes left to right during compilation. A
conversion or capacity failure is a compiler diagnostic. The result length must
fit the selected sequence type's `IndexSize` limit.

#### `String` destination

`String <+> rhs` returns `String`:

- `String`, `Byte`, or `U8[]`: append raw bytes;
- `AsciiChar`/`AsciiString`: append ASCII bytes;
- `Legacy.Latin1Char`, `Ucs2`, `Rune`, validated UTF strings: encode scalars as
  UTF-8 bytes;
- `Ucs2String`: encode each BMP scalar as UTF-8;
- `Legacy.MbcsString<P>`: decode under `P`, then encode as UTF-8;
- `Legacy.Char`/`Legacy.WChar` and their strings: decode under their selected
  profile, then encode as UTF-8.

Appending text to `String` produces its UTF-8 representation but does not grant
the resulting `String` a persistent UTF-8 invariant.

#### `AsciiString` destination

`AsciiString <+> rhs` returns `AsciiString`:

- `AsciiChar`/`AsciiString`: direct;
- `Byte`: only values `0x00..0x7F`;
- `Legacy.Latin1Char`: only values `0x00..0x7F`;
- `String`: require valid UTF-8 and every decoded scalar at most `U+007F`;
- `Ucs2`, `Rune`, and UTF strings: require every scalar at most `U+007F`;
- `Legacy.MbcsString<P>` and other `Legacy` strings: decode under their exact
  profile/encoding, then require ASCII;
- `U8[]`: unavailable as implicit text admission.

#### `Ucs2String` destination

`Ucs2String <+> rhs` returns `Ucs2String`:

- `AsciiChar`, `AsciiString`, `Legacy.Latin1Char`, `Ucs2`, and `Ucs2String`:
  direct scalar append;
- `Byte`: interpret only `0x00..0x7F` as ASCII;
- `String`: require valid UTF-8 and BMP nonsurrogate scalars;
- `Rune` and UTF strings: require every scalar at most `U+FFFF`;
- `Legacy.MbcsString<P>` and other `Legacy` strings: decode and require BMP
  nonsurrogate scalars;
- `U8[]`: unavailable as implicit text admission.

#### UTF destinations

`Utf8String`, `Utf16String`, and `Utf32String` each return their left identity:

- `AsciiChar`/`AsciiString`, `Legacy.Latin1Char`, `Ucs2`/`Ucs2String`, `Rune`,
  and every validated UTF string transcode through Unicode scalars;
- `String` must first decode as complete valid UTF-8;
- `Byte` is admitted only as one ASCII scalar `0x00..0x7F`, never as a partial
  encoded UTF sequence;
- `Legacy.MbcsString<P>` decodes through its exact code page;
- `Legacy.CharString`/`Legacy.WideString` decode through their selected profile;
- raw `U8[]` receives no implicit text interpretation.

Once source validity is established, transcoding a Unicode scalar sequence among
UTF-8/16/32 is total except for destination capacity/resource failure.

#### `Legacy` encoded-string destinations

`Legacy.MbcsString<P> <+> rhs` returns the same code-page-specific identity:

- the same `Legacy.MbcsString<P>` appends directly while preserving one trailing
  NUL;
- another `Legacy.MbcsString<Q>` decodes under `Q` and re-encodes under `P`;
- `String` must decode as UTF-8;
- ASCII, Latin-1, UCS-2, Rune, and UTF values transcode through Unicode scalars;
- `Byte` is admitted only through the ASCII range;
- any scalar unrepresentable in `P` rejects the compile-time join; and
- raw `U8[]` is unavailable as implicit code-page text.

`Legacy.CharString` and `Legacy.WideString` apply the same left-owned rule under
their selected native profile. Every successful join rejects an embedded NUL
and restores exactly one trailing NUL outside logical length.

#### Raw byte-array destination

`U8[] <+> rhs` initially accepts only `U8`, `Byte`, `U8[]`, and raw `String`
bytes. Appending the storage representation of a UTF or MBCS string requires an
explicit representation operation so semantic transcoding cannot be mistaken
for raw byte extraction.

#### Character values are right operands

Character identities join into a sequence on the left:

```zax
utf8'temperature: ' <+> rune'℃'
```

Two characters do not auto-form a string, and a character on the left does not
acquire a hidden sequence result:

```zax
rune'a' <+> rune'b' // unavailable unless a separate explicit contract is added
```

This avoids making character addition or adjacency construct an inferred
container.

### Compile-time contract and pressure

Literal operators and literal-result `<+>` require compile-time execution.
Exact scheduling, capability, and diagnostic syntax remain future work, but the
aligned minimum contract is:

- evaluate under the enclosing active execution context;
- target-context source uses target type/profile facts;
- compiler-host facts require explicit compiler-host context;
- source decoding and numeric parsing are independent of host locale;
- results depend only on payload, declaration, explicit static inputs, selected
  environment/profile, and versioned language/library data;
- wall clock, randomness, process environment, filesystem, and network are
  unavailable unless future compile-time capabilities expose them explicitly;
- mutable compile-time state cannot make identical literal evaluations
  source-order dependent;
- temporary compile-time allocation cannot leak compiler objects into the
  result;
- language panic or deliberate literal rejection becomes a source diagnostic;
- resource exhaustion is a compiler diagnostic, not another candidate or
  fallback; and
- a selected compile-time-unavailable join does not retry a runtime or weaker
  overload.

Literal code needs a way to reject a payload with a message and payload offset.
The current work assumes that operation exists without inventing its source
syntax. The compile-time owner must distinguish deliberate source rejection from
an internal compiler failure and retain the literal evaluation path.

An ordinary `<+>` declaration likewise needs compile-time availability metadata
or a compile-time-only declaration mode. It will be declared under the same
future execution model as literal operators; `once` alone is not that marker.

Sandboxing, caching, work budgets, recursion limits, I/O capabilities, and exact
failure APIs remain in the indexed compile-time input.

### Diagnostics, formatting, and reflection

Diagnostics should distinguish:

- invalid physical source UTF-8;
- physically forbidden C0 source;
- unterminated or mismatched delimiters;
- an attached literal from a separated phrase fence;
- unknown, inaccessible, or ambiguous literal prefix;
- malformed payload with logical offset and physical segment;
- `<|>` operands that resolve to different literal declarations;
- grouped or empty `<|>` operands;
- adjacent literals missing `<|>` or `<+>`;
- same-prefix `<+>` requiring
  `intent<same-prefix-literal-join>`;
- literal parser rejection from compiler panic/resource failure;
- a nonconstant, zero-result, or multiple-result literal declaration;
- uncommitted source without a valid commitment;
- selected range failure without overload retry;
- oversized or invalid raw representation;
- invalid `AsciiChar` high-bit representation;
- invalid UTF/code-page sequence or unrepresentable scalar;
- embedded NUL or broken trailing-NUL invariant in a `Legacy` string;
- unavailable compile-time `<+>`; and
- joined/fused result exceeding its selected sequence capacity.

A formatter must preserve:

- prefix attachment and complete qualified spelling;
- quote kind and payload scalar spelling;
- `<|>` versus `<+>`;
- digit grouping;
- grouping parentheses and explicit continuation;
- comments and source segment boundaries; and
- intent acknowledgements.

It cannot rewrite a custom payload based on assumptions about its parser.

Source reflection must retain the written prefix path, delimiter kind, physical
segments, payload spelling, `<|>` grouping, `<+>` operations, and intent
acknowledgement even when the final expression tree contains one parsed value.
The literal operator still receives only the merged `String`.

Ordinary `type of` observes the concrete committed result. Reflection of an
uncommitted intermediate, parser declaration, suggested type, source payload, or
literal provenance remains indexed reflection pressure.

### Deferrals and disposition

The aligned model places these constraints on future owners:

- **Compile-time execution:** required literal/join execution, deterministic
  context, deliberate diagnostics, availability, and resource policy.
- **Generics/type factories:** payload-dependent suggested types and bounded
  type selection without constructing a language-maximum value or performing
  fallback overload search.
- **Text/string design:** final encoded-string family naming, legacy profile
  definitions, encoding tables, runtime admission/conversion, runtime
  concatenation, NUL-terminated view lifetimes, allocation, capacity,
  normalization APIs, and presentation layers. The full catalog and
  representative failures above must move to an indexed text/string destination
  before this work is archived.
- **Integer identities:** move the current unqualified `Char`, `UChar`, `WChar`,
  and `UWChar` roles under `Legacy` while preserving their profile-selected
  representations and distinct identities.
- **Alias/import/declarations:** exposing type-owned literal phrases, collision
  behavior, visibility, and `forward operator literal`.
- **Operators:** exact catalog placement for `<|>` and `<+>`, compile-time
  availability, precedence presentation, and the
  `same-prefix-literal-join` intent category.
- **Arrays/containers:** exact `IndexSize` capacity, result length identity,
  allocation, and general concatenation.
- **Reflection/tooling:** literal source metadata, uncommitted-result metadata,
  formatting stability, and selected parser/result reporting.

Runtime `<+>`, runtime string admission, and the complete directional conversion
surface are string-design pressure. The aligned matrix above preserves the
intended distinctions without asserting that runtime and compile-time calls may
change static result shape.

Legacy literal material may be retired as superseded only after every useful
catalog entry, example, source rule, conversion pressure, and failure has been
promoted, rejected, or transferred to one indexed live destination. Historical
inventory is not retained as duplicate public teaching merely for provenance.

### Explicitly deferred catalog questions

The following questions do not prevent promotion of the aligned literal and
text model. They remain future-owner input:

- whether the general legacy code-page sequence family is named
  `Legacy.MbcsString<Encoding>` or `Legacy.EncodedString<Encoding>`;
- canonical and accepted-alias policies for Crockford and RFC encodings;
- exact code-page mapping/version identities;
- final `<+>` placement among unrelated value-operator precedence levels,
  while `<|>`-before-join and left-associated join chains remain fixed; and
- runtime counterparts of the aligned compile-time directional joins.

### Superseded initial reconstruction

The initial reconstruction below is retained as raw discovery history. Its
side-by-side fusion, automatic join, narrower catalog, and earlier uncertainty
are superseded by the aligned model above and must not be read as the current
candidate.

This reconstruction is candidate analysis, not an aligned finding. It starts
from the maintainer notes, preserves the current source and scalar boundaries,
and deliberately makes concrete recommendations so review can test one coherent
model.

### Review entry point

The current candidate model has three source categories:

```zax
myInteger := 55
// Current behavior: mathematical integer source, then Integer realization.

myReal : Binary64 = 1.5e-14
// Current behavior: exact decimal source, then Binary64 realization.

myText := "hello " "there"
// Candidate: adjacent ordinary segments form one String payload.

myHex := h'FF'
// Illustrative candidate: attached prefix, required compile-time parsing,
// and one concrete unsigned result selected before surrounding use.

myWide := h'FFFFFFFFFFFFFFFFFFFFFFFFFFFFF'
// Illustrative candidate: the same declaration may need a much wider concrete
// result chosen from the payload under a bounded future type factory.

myEscaped := c"line one\nline two"
// Illustrative custom prefix: the lexer passes the backslash and `n`;
// the selected literal declaration, not universal string syntax, interprets them.
```

The candidate treats a prefixed literal as a dedicated compile-time source
facility, not as an ordinary runtime operator call and not as a typeless value
that surrounding context may reinterpret. A prefix identifies one literal
declaration; that declaration receives one payload, executes as required during
compilation, and produces exactly one concrete constant result.

The most important contradiction is the legacy description of a literal
operator as a type-based `once` function. In current Zax, `once` means one
type-owned callable implementation shared by instances; it does not require
compile-time execution. A literal therefore needs its own required-compile-time
contract. Reusing `once` as that contract would silently give one word two
unrelated meanings.

The second major risk is that "merge" currently names two materially different
operations:

```zax
myOne := a'first' a"second"
// Candidate lexical fusion: one prefix, one combined payload, one parser call.

myTwo := a'first' b'second'
// Candidate semantic joining: two parser calls and then a join, if one exists.
```

Those operations must remain separate. Fusion changes what the literal parser
sees. Joining combines already concrete values and can have conversion, failure,
allocation, and ordering costs.

The main maintainer decisions exposed by this reconstruction are:

- whether ordinary `String` is a byte string populated from UTF-8 source, as
  recommended below, or itself a permanently validated Unicode-text identity;
- whether both quote kinds delimit attached payload segments and identical
  segments fuse before parsing;
- whether a dedicated literal declaration is implicitly required at compile
  time and receives one `String` payload;
- how a mathematical radix literal selects its one concrete integer type;
- whether automatic joining begins only with identical result identities or
  immediately includes the large legacy cross-encoding conversion lattice; and
- which small set of prefixes, if any, is language-provided rather than supplied
  by libraries through the custom facility.

### Candidate programmer model

#### Ordinary source, attached source, and fences

Ordinary source provides:

```zax
myInteger := 55
myReal : I16F8 = 1.5
myText := "ordinary text"
myEmptyText := ""
```

An ordinary integer follows the current uncommitted-integer model. An ordinary
decimal real is an exact mathematical source value realized by a selected
fixed-point or floating type. An ordinary double-quoted payload produces
`String`; it does no universal backslash escaping.

An attached name or qualified path introduces a prefixed literal:

```zax
myShort := h'FF'                    // illustrative prefix
myParsed := c"line\n"               // illustrative prefix
mySpecific := MyType.cstyle'input'  // illustrative qualified prefix
myEmptyPayload := MyType.parser''   // candidate: attached empty payload
```

Attachment is exact:

```zax
h'FF'       // attached literal payload
h 'FF'      // not a literal; the quote starts a phrase fence
"text"      // ordinary unprefixed string
'text'      // phrase fence or phrase diagnostic, never an ordinary literal
''          // error: empty phrase fence
```

This preserves the current phrase-fence boundary. Backticks do not delimit
literals.

A prefix may use either quote kind:

```zax
myA := c"contains a single quote: '"
myB := c'contains a double quote: "'
```

The opening quote determines the closing quote. The delimiter itself is not
part of the payload, cannot occur directly in that segment, and is not escaped
by the lexer. A backslash is an ordinary payload character. A prefix-specific
parser may assign it escape meaning.

Allowing both delimiters is useful because Zax has no universal escape layer.
Identically prefixed adjacent segments can alternate delimiters to carry both
quote characters:

```zax
myQuotes := c"contains '" c' and "'
// Candidate parser input: contains ' and "
```

Every segment repeats its prefix. No prefix leaks across whitespace,
continuation, a comment, or an earlier segment.

#### Literal groups and lexical fusion

The candidate calls one ordinary string segment, or one or more identically
prefixed attached segments, a **literal group**. Segments in one group are
concatenated as source text and parsed once:

```zax
myText := "hello " "there"
// One ordinary String group with payload `hello there`.

myEncoded := c"first\n" c'second "quoted" part'
// One c group and one call to c's literal declaration.
```

Only source trivia may separate fused segments. A physical newline still needs
an accepted continuation:

```zax
myText := "Lorem ipsum " \
  "continued text"

myEncoded := c"first part " \
  c'second "quoted" part'
```

The prefix must be repeated after continuation. The continuation marker,
indentation, comments, and whitespace are not payload.

For fusion to be source-deterministic before name resolution, "identical
prefix" should mean the same prefix path spelling, not merely two aliases that
later resolve to one declaration:

```zax
h'AA' h'BB'                 // candidate: one h payload, `AABB`
h'AA' Numbers.h'BB'         // two groups even if h aliases Numbers.h
```

Parentheses or an operator end a group:

```zax
(h'AA') (h'BB')
// Candidate error: these are two adjacent expressions, not one literal group.

(h'AA') <+> (h'BB')
// Illustrative explicit join if the result type supplies the proposed operator.
```

This intentionally rejects the legacy suggestion that parentheses split parser
calls but might still trigger an unexplained implicit merge. If grouped
expressions should combine, source should state a real operation.

Changing whether comments count as fusion-preserving trivia does not appear to
change payload meaning, but it affects formatting and source reflection. The
candidate permits comments while preserving their segment boundary in source
metadata. This detail needs testing against documentation attachment and
formatter behavior.

#### Payload value

After source decoding and segment fusion, a literal declaration receives the
exact payload character sequence:

- delimiters and inter-segment trivia are absent;
- no escape processing or Unicode normalization has happened;
- backslashes remain backslashes;
- case, spaces, and all other admitted payload characters remain unchanged; and
- source location remains available to diagnostics even though it is not an
  ordinary argument.

The maintainer notes name `String` as the sole input. That is coherent if
ordinary `String` is a byte sequence and the compiler encodes admitted source
characters as UTF-8. It also bootstraps custom literal parsing without requiring
reflection to be designed first.

A dedicated future source-payload type would be justified only if literal code
must directly inspect segment boundaries, delimiter choice, or source spans.
The baseline should not add that type speculatively. Source reflection must
still preserve those facts for tooling; they need not be ordinary parser input.

#### Literal declarations and lookup

Illustrative declaration spelling:

```zax
MyType :: type {
  // Illustrative only: exact literal-declaration grammar is not aligned.
  operator literal cstyle final : (
    result : MyType
  )(
    payload : String
  ) = {
    // Parse payload and return one compile-time-available MyType value.
  }
}

myValue := MyType.cstyle'payload'
```

The required behavior is more important than this spelling:

1. The prefix or qualified prefix path resolves under ordinary lexical,
   visibility, import, and qualification boundaries.
2. It identifies one literal declaration, not a runtime receiver search.
3. The declaration accepts exactly one payload value.
4. It provides exactly one value result.
5. Its baseline result type is declared and concrete.
6. Its body is required to be available and executed during compilation.
7. The produced value is fully compile-time available and retains its concrete
   type when placed into the surrounding expression.

Literal declarations may be namespace-scoped or type-owned. Type ownership
provides a stable qualified prefix; it does not imply an instance receiver.
An explicit alias/import facility may expose a shorter prefix, but its exact
syntax remains declaration/namespacing work.

An unqualified use sees only declarations introduced through its lexical and
import path. It never searches every type, infers an owner from the expected
result, or considers the right side of a surrounding operator:

```zax
myValue : MyType = parse'payload'
// MyType does not cause lookup of MyType.parse.
```

If two visible declarations expose `parse`, the use is ambiguous and must be
qualified. Source, declaration, import, and module order do not break the tie.
Adding an import can therefore produce a loud ambiguity, but cannot silently
select another parser.

Ordinary result-only overloading is a poor fit for a literal prefix because
every declaration receives the same payload shape and the expected result must
not choose the prefix meaning. The baseline should require one declaration
family per visible prefix. A future dependent-result family may compute one
result type from its payload; that is one selected family, not several unrelated
result overloads retried after parsing.

Literal declarations belong to the operator/declaration system but form a
distinct source category. They have no ordinary pre-unary, post-unary, binary,
phrase, call, or mixfix receiver. Current receiver-owned phrase discovery
therefore cannot be copied mechanically.

#### Required compile-time execution

A literal is accepted only if its selected declaration can complete during
compilation. Constant inputs alone do not normally mandate compile-time
execution, so this requirement belongs to the literal source form itself.

`once` remains orthogonal:

- `once` says one type-owned implementation is shared by type and instance
  calls;
- a type receiver is not inherently compile-time;
- a literal declaration has no runtime invocation path at the literal use; and
- literal-required execution must not make every `once` callable static.

The baseline execution model must preserve these visible rules:

- candidate selection and prefix resolution finish before execution;
- the active host/target execution context is explicit and does not default
  accidentally to compiler-host numeric behavior;
- target-selected fixed/floating results use target format, rounding,
  endianness, and validity;
- a language-level panic becomes a source diagnostic with the literal call
  path, not an internal compiler failure;
- unavailable compile-time dependencies are diagnosed rather than deferred to
  runtime;
- allocation, recursion, work, output size, and termination are bounded by the
  future compile-time resource policy; and
- a result cannot contain a runtime-only address, borrowed compiler resource,
  unresolved type, or other state that cannot be materialized as its declared
  constant value.

Whether literal code may perform I/O, inspect environment state, use nondeterminism,
or depend on compiler/plugin versions is unresolved compile-time/source-stability
work. The safe starting pressure is deterministic execution from payload,
declaration, explicitly selected environment/profile, and versioned static
dependencies.

#### One concrete result

After a literal group is parsed, it has one concrete static result type:

```zax
myValue := MyType.cstyle'payload'
```

The surrounding inferred declaration adopts that type. A typed destination may
accept the value only through already defined exact construction, admission, or
conversion behavior. It does not ask the literal parser to return something
else:

```zax
myByte : U8 = h'FF'
// Valid only if the h declaration itself selected U8, or an explicit current
// U8 construction/conversion accepts h's already concrete result.
```

This is a direct contradiction in the legacy "coerced numerical literal"
examples: annotations such as `myHex : U8 = h'FF'` appear to make `h` change its
return type. The current candidate rejects that lookahead. The prefix must state
or compute its result independently.

Compile-time evaluation never reopens a concrete result as an uncommitted
integer or exact typeless real. Range failure, join failure, or later operand
mismatch does not retry another literal declaration or wider result.

Zero or multiple result slots cannot fill the literal expression. Return a
single explicit structure when the literal concept naturally produces several
pieces.

### Numeric source

#### Ordinary decimal integers and reals

Current integer behavior remains unchanged. Ordinary decimal integer source is
uncommitted until its current commitment boundary and then realizes directly in
the selected integer family.

Current typed real behavior also remains unchanged:

```zax
myFixed : I16F8 = 1.5
myFloat : Binary32 = 0.1
myExponent : Binary64 = 1.5e-14
```

`e` or `E`, followed by an optional attached sign and required decimal digits,
scales the exact decimal source by a power of ten. Its presence makes `1e3`
real-number source.

The candidate exact decimal token boundaries are:

```zax
1.5
1e3
1.5E+3
1.5e-3

1.    // candidate error: digits are required after the point
.5    // candidate error: digits are required before the point
1e    // error: missing exponent digits
1e+   // error: missing exponent digits
e3    // not a number literal
```

Requiring digits on both sides of the point avoids collision with current and
future uses of `.` and makes damaged source visible. Digit separators, grouping,
and suffixes should not be accepted until their exact source value is
demonstrated.

Real expression typing still has a genuine hole. A concrete fixed/floating peer
or candidate hole can select the destination. The candidate default for an
otherwise unanchored real is `Float`, because that identity already means the
active environment's preferred general-purpose floating format:

```zax
myDefaultReal := 1.5 // candidate: Float
```

For literal-only arithmetic, operations should occur in one selected concrete
type rather than evaluate an unlimited exact-real expression and round only at
the end:

```zax
myA : Binary32 = 0.1 + 0.2
// Candidate: both sources realize as Binary32, then Binary32 addition occurs.

myB := 0.1 + 0.2
// Candidate: both sources realize as Float, then Float addition occurs.

myC : I16F8 = 1.5 + 2
// Candidate: both inputs realize as I16F8 under the selected same-type operation.
```

This preserves the scalar's per-operation rounding and cost model. It requires a
focused extension of candidate-hole selection for a structurally fixed built-in
numeric tree; it must not let expected results choose among source trees or
arbitrary custom receivers. This is an important point for maintainer review
rather than an assumed consequence of current integer rules.

The proposed bare-literal suffix:

```zax
myA := 55 : U8
```

should not be adopted in the baseline. `(: U8 = 55)`, a typed declaration, and
`55 as U8` already provide explicit commitment, while the suffix conflicts with
declaration/result colon roles and creates special postfix grammar that applies
only to one token shape.

#### Mathematical radix literals

The useful initial built-in numeric catalog appears to be binary, octal, and
hexadecimal mathematical integers:

```zax
myBinary := b'1011101'
myOctal := o'12345670'
myHex := h'ABC123'
```

These prefixes should parse unsigned mathematical magnitude:

- leading zero digits do not state representation width;
- hex digits accept consistently defined upper/lower case;
- a sign is written as an ordinary outer operation, not hidden in the payload;
- fractions, decimal exponents, `p` exponents, separators, and empty payloads
  are rejected by these integer parsers initially; and
- malformed digits identify the payload offset and source segment.

The exact prefix names remain candidates, but `b`, `o`, and `h` are compact and
well evidenced. Aliases such as `b2`, `b8`, and `b16` add catalog surface without
adding meaning. `b64` is particularly dangerous: the legacy corpus uses that
shape both for a radix-64 number and for RFC 4648 base-64 byte decoding. Those
are different operations and should not share an unqualified built-in name.

Duodecimal, bases 20/32/36/58/60/62/64/94, and competing digit alphabets are
good tests of the custom facility, not evidence that the language must reserve
all of them. Their alphabets, normalization, typo handling, and result purpose
are domain policy.

#### Payload-sized integer result

Three coherent result policies remain possible for `h'FF'`:

- smallest fitting exact unsigned specialization;
- smallest fitting member of a documented width tier; or
- `UInteger` while the value fits, then a bounded wider exact specialization.

The candidate recommendation is the third, an **ordinary-floor policy**:

1. parse one nonnegative mathematical value;
2. select `UInteger` when it fits the active environment's ordinary unsigned
   role;
3. otherwise select the least wider exact unsigned specialization permitted by
   the declared factory policy; and
4. fail when payload magnitude or compiler resources exceed the language limit.

This keeps common radix literals in one ordinary arithmetic domain while still
letting a very large payload request sufficient width. It avoids making
`h'F'`, `h'10'`, and `h'100'` three unexpectedly incompatible tiny identities.
The selected type remains independent of the surrounding declaration.

This recommendation has a real portability tradeoff: `UInteger` follows the
active profile, and crossing its capacity boundary may select another exact
identity on another target. A prefix requiring a stable identity should be
type-qualified or use an explicitly exact-width declaration.

The future mechanism is conceptually:

```text
compile-time payload
-> parsed mathematical magnitude
-> bounded integer type factory
-> one concrete closed specialization
-> value realization
```

It is not overload fallback. It must expose the selected public/canonical type
and maximum, avoid instantiating `LanguageMaxU` merely to inspect a small value,
and complete before the literal enters surrounding operator selection.

The baseline custom literal facility can require a declared concrete result
while generics are unresolved. Language-provided radix literals may either wait
for the factory or use a narrowly specified intrinsic form; an intrinsic must
not become evidence that arbitrary user declarations already have dependent
result types.

#### Mathematical value versus raw bits

A radix magnitude and a bit-pattern adoption must not share one ambiguous
prefix:

```zax
h'FF'
// Candidate mathematical meaning: positive 255; leading zero digits do not
// change the value or selected width policy.

I8.bits'FF'
// Illustrative raw-pattern meaning: exact I8 bit pattern 11111111, value -1.
```

The second spelling is illustrative, but the semantic separation is required.
A raw form needs the destination width, signedness, endianness/field order, exact
digit-to-bit mapping, padding policy, and invalid-pattern behavior before it can
parse. Leading zeros and payload length can be significant there.

Typed decimal forms such as `u8'255'` offer little over:

```zax
myValue : U8 = 255
myOther := (: U8 = 255)
```

They should not become a broad language-provided catalog. A type may expose a
qualified literal parser when it adds a real representation or validation
contract.

#### Non-decimal fractions and exponents

`p`/`P` power-of-two exponents are useful for exact hexadecimal or binary
floating significands:

```zax
// Illustrative mathematical readings, not accepted source.
Binary64.hex'1.8p10' // 1.5 * 2^10
Binary64.bin'1.1p-3' // 1.5 * 2^-3
```

They are not coherent additions to the candidate integer `h` and `b` prefixes,
because those prefixes currently promise an integer result and no surrounding
type lookahead. The recommendation is therefore:

- keep decimal `e`/`E` as current ordinary real source;
- reject `p`/`P` in the initial integer radix prefixes;
- introduce power-of-two exponent syntax only with a parser whose one concrete
  fixed/floating result and rounding policy are explicit; and
- add no exponent to arbitrary bases merely for symmetry.

### String, character, and encoded data

#### Ordinary `String`

The most coherent reading of the maintainer notes is:

- a Zax source file is valid UTF-8;
- ordinary `"..."` contributes the UTF-8 bytes for its admitted source
  characters;
- the result identity is `String`, whose storage element is `U8`/`Byte`;
- the ordinary literal result is therefore valid UTF-8 at creation time; but
- `String` as a general byte-string identity need not promise that every value
  later constructed through raw byte operations remains valid UTF-8.

That reading explains why the notes separately propose `Utf8String`: the latter
can carry a validated-text invariant while `String` remains byte-oriented.
If `String` itself is meant to guarantee valid UTF-8 permanently, then
`Utf8String` is redundant and raw byte-producing forms must return `U8[]` rather
than `String`. This identity decision must be made before accepting the proposed
conversion/join lattice.

There is no universal escape processing:

```zax
myText := "a\nb"
// Candidate contents: the four source characters a, backslash, n, b.

myEscaped := c"a\nb"
// Illustrative: c may produce a, line feed, b.
```

Physical tab characters are already invalid Zax source. LF/CR delimit physical
lines and cannot occur inside one direct segment. The exact source-wide policy
for remaining C0 controls, especially NUL, is still a source-structure question.
Literal parsers may produce control bytes even when those bytes cannot appear
directly in source.

#### Unicode corrections and unresolved policy

The two legality lists in the maintainer notes conflict and should not be copied
into current teaching as written:

- `0xFDD0..0xDFEF` is a reversed/mistyped range; the Unicode noncharacter block
  is `U+FDD0..U+FDEF`.
- Surrogates `U+D800..U+DFFF` are not Unicode scalar values and cannot be
  encoded as valid UTF-8 source text.
- `U+10FFFF` is within the Unicode scalar range but is a designated
  noncharacter; values above it are outside Unicode.
- Noncharacters such as `U+FDD0..U+FDEF` and plane-ending `U+FFFE`/`U+FFFF`
  are valid Unicode scalar values reserved for internal use. Calling them
  "invalid UTF-8" or claiming that Unicode conversion necessarily fails is
  technically incorrect.
- UTF-16 must use surrogate pairs for supplementary scalar values. A type that
  bans every surrogate code unit cannot also encode full UTF-16.

The candidate policy is to separate:

1. source UTF-8 well-formedness;
2. Unicode-scalar validity;
3. source presentation restrictions such as tabs/newlines/delimiters;
4. an encoded result type's invariant; and
5. advisory policy for noncharacters or discouraged controls.

The recommendation is not to make Unicode noncharacters globally unparsable
without a concrete Zax source-security reason. A stricter text identity may
reject them explicitly. This remains a maintainer decision.

No normalization should occur implicitly. Source stability and identifiers may
have their own rules; a literal payload preserves admitted scalar spelling.

#### Candidate character forms

Standalone single quotes are unavailable, so source for one byte or Unicode
scalar needs an attached prefix or an ordinary numeric construction.
Representative categories are:

```zax
myByte := byte'A'   // illustrative: exactly one resulting byte
myRune := rune'☃'  // illustrative: exactly one Unicode scalar value
```

Exact names remain open. A direct byte parser must reject a source character
whose UTF-8 encoding uses more than one byte unless its own documented
transformation yields one byte. A rune parser accepts exactly one scalar, not
one UTF-8 code unit and not one user-perceived grapheme cluster.

Escape-processing variants, hexadecimal character forms, UCS-2, UTF-16 code
units, wide characters, and code-page characters are separate parsers with
separate result identities. They should not be inferred from suffix letters
such as `c` and `h` until the text/character model owns those distinctions.

#### Encoded strings and byte data

The legacy catalog contains several useful facility tests:

- C-style escape decoding;
- XML entity decoding;
- hexadecimal and RFC 4648 byte decoding;
- ASCII validation;
- UTF-8/16/32 encoding;
- UCS-2/wide code-unit storage; and
- named multibyte code pages.

They do not all need language-reserved prefixes. The candidate disposition is:

- ordinary source plus a small character/radix baseline belongs to language
  literal teaching;
- C/XML/base-N decoding is implementable through public custom literal
  declarations and normally belongs to a versioned standard or domain library;
- byte decoders return a byte array or byte string, not a text identity merely
  because source payload was text;
- UTF encoders return their explicit validated/encoded identity only after the
  text owner defines it; and
- MBCS/code-page types and the long code-page catalog require a dedicated
  text/encoding owner and versioned mapping data, not permanent compiler
  reservations for every code-page prefix.

The legacy table also alternates `Usc2` and `Ucs2` spellings and treats some
byte, code-point, code-unit, and encoded-string relationships as if they were
the same conversion. Those names and conversions must be reconstructed under
the future text owner rather than accepted by inventory.

Strings and arrays having an `IndexSize` maximum is plausible container
pressure. Literal fusion and joining must diagnose a compile-time result whose
length exceeds the selected container's maximum. Exact container capacity,
fixed versus dynamic length, and allocation belong to their container owners.

### Adjacent literal joining

#### Separate fusion from joining

After lexical fusion, two different quote-delimited literal groups may remain
adjacent:

```zax
myText := "plain " c"escaped\n"
myOther := utf8'first ' xml'second &amp;'
```

The candidate may recognize this only as a special **literal sequence**. It
must not establish general juxtaposition, function application by whitespace,
or reinterpret a separated single-quoted phrase fence.

Each group resolves and executes independently. Its concrete result then enters
one left-to-right join step:

```text
parse first group
-> parse second group
-> join first result with second result
-> continue with the concrete joined result
```

Evaluation remains source ordered even though all required work occurs during
compilation.

#### Conservative initial join rule

The safest initial rule is automatic joining only when:

- both groups produce the same concrete string or array identity;
- that identity supplies one language-recognized required join;
- element/encoding invariants remain unchanged;
- the complete result is available during compilation; and
- size/resource limits are satisfied.

This permits independently parsed prefixes that intentionally return the same
type:

```zax
myText := "prefix " c"escaped\n"
// Candidate-valid only if both groups return the same String identity.
```

Numbers, characters, records, and arbitrary values do not auto-join merely
because they are adjacent. Arrays join by sequence concatenation, not numeric
addition of their elements.

The maintainer notes propose `<+>` as the exact join operator. That form is not
in the current operator catalog. If accepted, it should be a protected or
language-recognized binary form whose left operand owns the result contract:

```zax
combined := left <+> right // proposed form, not current syntax
```

Automatic literal joining would be defined in terms of its required
compile-time signature, while manual runtime use could have separately declared
required or optional behavior. Automatic source must never unwrap an optional
result or turn absence into a diagnostic implicitly.

The broad legacy lattice allows `String`, `AsciiString`, UTF variants, wide
strings, characters, bytes, and MBCS values to convert while joining. Adopting
that lattice now would make source order select result encoding, hide narrowing
and code-page work in adjacency, and introduce value-dependent failure. The
recommendation is exact-identity auto-join first. Cross-encoding composition
should require explicit conversion or an explicitly written join until the text
owner establishes safe directional relationships.

Grouping ends automatic literal sequencing:

```zax
(a'first') (b'second') // error: no general implicit juxtaposition
```

An explicit `<+>` remains possible if that form is later accepted.

### Diagnostics and source stability

Literal diagnostics need to distinguish:

- invalid source UTF-8 from a valid payload rejected by its parser;
- unterminated ordinary or prefixed segments;
- an attached payload from a separated phrase fence;
- an empty attached payload rejected by a parser from the always-invalid empty
  phrase fence;
- an unknown, inaccessible, or ambiguous prefix;
- malformed payload characters with an offset and originating segment;
- unavailable required compile-time execution;
- a language panic from compiler resource/capacity failure;
- a parser panic or explicit parser diagnostic;
- a nonconstant, zero-result, or multiple-result declaration;
- failure to compute or realize one bounded concrete result type;
- a selected range failure, with no overload/factory retry;
- fusion boundaries versus semantic join boundaries;
- missing, ambiguous, unavailable, or wrong-result-shape join behavior; and
- a joined/fused container exceeding its selected maximum length.

For a fused payload, diagnostics should report both logical payload position and
the physical source segment when practical.

Material source-stability events include:

- changing an ordinary literal token boundary or default type;
- adding/removing a built-in prefix or changing its payload alphabet;
- changing prefix import/alias visibility;
- changing whether segments fuse and what exact prefix equality means;
- changing a literal's concrete result or factory policy;
- changing escape, normalization, encoding, or raw-bit interpretation;
- changing required compile-time environment or resource limits;
- adding/removing automatic join viability; and
- changing a join's result identity or failure policy.

A formatter must preserve attachment, prefix spelling, quote delimiters, segment
boundaries, continuation, and explicit join/grouping until the language defines
which rewrites are semantics-preserving. Source reflection must be able to
recover those facts even when fused segments produce one parser call and fences
produce no final expression node.

### Known holes and proposed deferrals

The following consequences are captured but should not be completed inside this
literal review unless the candidate model proves incoherent without them.

- **Compile-time execution:** exact required/permitted syntax, sandboxing, I/O,
  determinism, caching, recursion, allocation, resource budgets, static branch
  selection, and host/target execution remain with the indexed compile-time
  input. Literal work constrains that owner to provide required execution and
  source diagnostics.
- **Generics and type factories:** declaration syntax for payload-dependent
  result types, compile-time type returns, bounded probing, and specialization
  remains with the indexed generic input. Literal work requires one family to
  select one concrete type before surrounding selection.
- **Text and Unicode:** String identity, validated UTF identities, character and
  code-unit types, normalization, code pages, conversion, and runtime string
  libraries need one future text/encoding owner. Before archival, the useful
  catalog and contradictions in the maintainer notes need an indexed live raw
  destination if they are not resolved here.
- **Arrays and containers:** fixed/dynamic length identity, `IndexSize` capacity,
  allocation, and general concatenation remain container work. Literal work
  needs only one concrete result and compile-time size diagnostics.
- **Reflection:** concrete result identity follows current `type of`; exact
  prefix spelling, delimiter, segments, fused payload, and provenance remain in
  indexed reflection input.
- **Operators:** exact `<+>` reservation, declaration form, precedence, result
  ownership, and manual runtime behavior require operator-catalog integration
  if joining is accepted. Literal adjacency itself remains a bounded literal
  source rule.
- **Namespacing and declarations:** exact alias/import syntax for exposing a
  type-owned literal prefix and collision behavior must reuse the one lexical
  namespace/qualified-path model without speculative receiver lookup.
- **Source structure:** source-wide control-character policy, exact segment
  trivia, token recovery, and formatter preservation belong with source
  structure while retaining the accepted attached-quote/fence boundary.
- **Numeric families:** raw pattern adoption, unusual numeric families, and
  representation-operation spelling remain numeric-family work. Mathematical
  radix parsing must not collapse into those operations.

Likely lasting documentation has a cohesive literal concept owner for the
taxonomy, payloads, declaration model, compile-time result boundary, built-in
catalog, and literal sequencing. Existing source, integer, fixed, floating,
declaration, invocation, operator, and operator-catalog owners would receive
only their local integration rules. A separate future text/encoding owner is
needed if more than ordinary `String` and representative character forms are
accepted; distributing the legacy conversion lattice across literal and
casting pages would create duplicate authority.

### Focused review questions

Review should begin with the three high-pressure examples:

```zax
myHex : U8 = h'FF'
myWide := h'FFFFFFFFFFFFFFFFFFFFFFFFFFFFF'
myJoined := "plain " c"escaped\n"
```

For `myHex`, the decision is whether `h` independently happens to return `U8`,
returns another concrete type that must be converted, or uses a different
payload-sized policy. The `U8` destination may not choose after the fact.

For `myWide`, the decision is which bounded type policy chooses one result and
whether the ordinary-floor recommendation gives sufficiently stable,
programmer-useful identity.

For `myJoined`, the decision is whether both groups must already produce
`String`, whether adjacency may invoke a cross-encoding operation, and whether
the visible costs justify implicit syntax.

After those, the next decisions are the ordinary `String` invariant, the
dedicated compile-time declaration model, and the initial built-in prefix
catalog. Resolving those determines whether the remaining text inventory and
`p`/`P` forms can be cleanly deferred rather than left as contradictions.

## Dispositions and promotion dry run

### Pre-promotion documentation fit dry run: PASS

Conducted 2026-09-15 after maintainer review of the aligned model and complete
catalog.

**PASS** means the aligned findings have a coherent human reading path, one
lasting owner for each rule, live destinations for every deferral, and an exact
promotion change set. It does not authorize promotion, archival, raw-input
retirement, creation of work item `024`, staging, committing, or pushing.

### Structure proposal

Retain the current repository layout and add two cohesive current concept
owners:

```text
language/
  literal-source-and-operators.md
  strings-and-characters.md
```

Add two indexed raw destinations for deliberately unresolved work:

```text
project/raw/
  namespaces-imports-and-visibility.md
  string-runtime-and-encoding.md
```

No directory move or broader restructuring is needed.

`language/literal-source-and-operators.md` is necessary because the general
literal model would otherwise be reconstructed across source structure, integer
literals, scalar owners, declarations, and operators. It does not replace the
specialized integer, fixed-point, or floating realization owners.

`language/strings-and-characters.md` is necessary because `String`,
`AsciiChar`, `Rune`, UTF identities, legacy character identities, literal
catalogs, and directional joins form one reader concept. Keeping them only in a
literal reference would make programmers reconstruct text identity and
conversion rules from parser examples.

### Lasting ownership map

#### Literal source and operator owner

`language/literal-source-and-operators.md` owns:

- the literal taxonomy and ordinary programmer entry examples;
- quoted one-word literal phrase names;
- attached single/double payload delimiters and no built-in escaping;
- admitted payload formation and the one-`String` parser input;
- `<|>` source merge, declaration-identity matching, continuation, grouping,
  empty operands, and non-acknowledgeable mismatches;
- explicit compile-time `<+>` use as literal joining, including same-declaration
  intent acknowledgement and required compile-time availability;
- `operator literal 'name' final once` declarations, lookup, qualification,
  ambiguity, and the absence of expected-result owner search;
- generic `uncommitted` result specialization to one concrete type before
  invocation;
- suggested `Integer`/`UInteger`/`Float` commitment behavior and sign intent;
- decimal real token forms, default `Float`, and concrete per-operation rounding;
- `p`/`P` power-of-two exponents for binary/hexadecimal real payloads;
- first-class and `Radix` numeric prefix catalogs, digit sets, spaces, errors,
  and source spelling stability;
- exact type-qualified scalar raw-pattern literals;
- rejection of literal-only `55 : U8` syntax;
- literal-specific compile-time guarantees, costs, diagnostics, and source
  reflection requirements; and
- concise routing to scalar realization and string/character identities.

The owner begins with ordinary integer, real, string, radix, raw-pattern,
merge, and join source. Declaration machinery follows use. Catalog tables,
diagnostics, costs, source stability, and deferred contracts come after the
mental model.

#### String and character owner

`language/strings-and-characters.md` owns:

- `Byte`, `AsciiChar`, `Ucs2`, and `Rune` distinctions needed for text;
- `String` as unrestricted bytes populated from admitted UTF-8 source without a
  permanent UTF-8 invariant;
- `AsciiString`, `Ucs2String`, `Utf8String`, `Utf16String`, and `Utf32String`
  value invariants;
- Unicode scalar, surrogate-pair, noncharacter, normalization, and
  scalar-versus-grapheme boundaries;
- direct, C-escaped, and raw-code-unit literal forms;
- the aligned C-style escape set, including greedy one-to-three-digit octal;
- type-owned literal spacing and the aligned main aliases;
- `Legacy.Char`, `Legacy.UChar`, `Legacy.WChar`, `Legacy.UWChar`,
  `Legacy.Latin1Char`, `Legacy.CharString`, and `Legacy.WideString`;
- trailing-NUL, no-embedded-NUL, logical-length, safe-mutation, and
  `.c_str()`-view guarantees for legacy strings;
- the conceptual code-page string family and complete retained code-page
  catalog;
- RFC 4648 encoding and XML entity transformation routes;
- left-owned compile-time `<+>` admission/transcoding behavior for byte, ASCII,
  UCS-2, UTF, legacy, and raw-array destinations;
- `IndexSize` result-capacity pressure as experienced by these values; and
- string/character diagnostics, costs, and representation boundaries.

The owner teaches ordinary `String`, `AsciiChar`, `Rune`, and UTF use before
legacy encodings, code-page catalogs, raw representations, or exhaustive join
directions.

#### Existing current owners

`language/source-structure.md` owns the lexical/layout application:

- valid source UTF-8 and physical C0 rejection inside payloads;
- attached quote recognition versus separated phrase fencing;
- quote-delimited lexical modes and terminators;
- `<|>` as a source-level form before value-operator precedence;
- continuation, comments, grouping boundaries, and source diagnostics; and
- formatter preservation of attachment, delimiters, merge, and trivia.

`language/integer-literals.md` owns shared uncommitted numeric realization:

- generic uncommitted literal results filling integer candidate holes;
- suggested type versus sign intent;
- concrete-before-use specialization and no post-selection retry;
- exact destination realization for unqualified radix payloads; and
- the handoff from ordinary/prefixed literal source to integer realization.

It no longer claims that every resolved prefixed literal must have a
declaration-fixed result type before payload-dependent generic specialization.

`language/fixed-point-scalars.md` and
`language/floating-point-scalars.md` continue to own destination realization,
rounding, range, and representation. Each receives local examples and links for:

- uncommitted ordinary real source with suggested `Float`;
- `b`/`h` `p` exponents;
- concrete operation selection before arithmetic; and
- type-qualified raw logical-pattern construction.

`language/integers.md` owns:

- exact integer interpretation of type-qualified radix patterns; and
- relocation of the current `Char`/`UChar`/`WChar`/`UWChar` profile roles under
  `Legacy`, with string semantics handed to the string/character owner.

`language/declarations-and-bindings.md` owns the declaration-facing
`operator literal 'name' final once` form, the distinction between `once` and
compile-time availability, and generic uncommitted result slots.

`language/function-invocation.md` owns the callable-facing rule that
compile-time/generic processing selects one concrete result prototype before a
literal body is invoked and before ordinary result selection continues.

`language/operators.md` owns ordinary `<+>` discovery, left receiver ownership,
selection, one concrete result, evaluation order, and unavailable-best
behavior. It distinguishes the overloadable value operator from
non-overloadable source merge.

`language/operator-catalog.md` owns the exact recognized `<|>` and `<+>` forms,
their symbolic presentation, `<|>` source-binding position, left-associated
join chains, the grouping requirement while mixed `<+>` precedence remains
unsettled, and the route to their complete domain owners.

`language/operator-phrases.md` continues to own the separated single-quote
phrase fence and gains a direct handoff to the literal owner for attached
payloads.

`language/intent-acknowledgements.md` owns the registered
`same-prefix-literal-join` category. The complete expression is acknowledgeable
only when both immediate literal operands resolve to the same declaration and
`<|>` was available.

`language/terms.md` owns concise definitions for literal phrase, literal
payload/segment, source merge, literal join, uncommitted generic result,
suggested commitment, Unicode scalar, grapheme cluster, and encoded legacy
string where cross-owner vocabulary requires them.

### Deferred ownership map

`project/raw/compile-time-execution.md` retains:

- exact compile-time-only declaration/availability syntax;
- the literal rejection/diagnostic operation;
- sandboxing, caching, allocation, recursion, termination, and resource limits;
- deterministic static dependencies and capabilities; and
- host/target scheduling beyond the literal-local guarantees.

`project/raw/type-parameters-and-generics.md` retains:

- exact generic syntax for `uncommitted` literal result slots;
- payload/context-driven selection of one concrete specialization;
- body processing under that specialization;
- computed suggested result types and bounded type factories; and
- custom numeric-family participation.

`project/raw/string-runtime-and-encoding.md` is created with representative
source and activation pressure for:

- runtime admission and conversion among byte, ASCII, UCS-2, UTF, native, and
  code-page strings;
- runtime concatenation and whether `<+>` has any runtime role;
- required/optional failure contracts without constness-dependent result types;
- final `Legacy.MbcsString` versus `Legacy.EncodedString` family naming;
- exact code-page mapping/version data and aliases;
- Crockford permissive input aliases and RFC padding/canonicality;
- allocation, mutation, capacity, normalization, grapheme, locale, and
  presentation APIs; and
- interaction with NUL-terminated foreign views.

`project/raw/namespaces-imports-and-visibility.md` is created with:

- literal-operator alias/import/export and visibility;
- exact alias identity required by `<|>`;
- independent resolution of qualified and unqualified merge operands;
- collision and ambiguity behavior;
- `forward operator literal`; and
- the constraint that import/source order never selects a literal operator.

`project/raw/reflection.md` retains source segments, delimiters, merge/join
spelling, selected literal declaration, generic specialization, suggested type,
intent acknowledgement, and result provenance.

`project/raw/interop.md` gains the aligned legacy NUL-terminated string
guarantees as pressure on C/C++ pointer views, ABI layout, lifetime, writable
access, and code-page boundaries.

`project/raw/cpu-provider-model.md` gains profile facts for legacy narrow/wide
character widths, signedness identities, native encodings, and host/target
selection.

`project/raw/indexing-and-slicing.md` gains string/array `IndexSize`, compile-time
joined length, and future container allocation/concatenation pressure.

`project/raw/numeric-type-families.md` continues to own custom/unbounded numeric
family participation and runtime representation-operation wording. Current
scalar literal raw patterns do not turn arbitrary integer-backed identities into
numeric families.

### Raw-input and legacy disposition

`project/raw/literal-operators.md` and
`project/raw/literals-maintainer-notes.md` are fully consumed by the two current
owners and the indexed deferrals above. During promotion they are marked
consumed, their competing technical bodies are pruned, and the paths are retained
only because the active work item's immutable fixed initiating input still links
them. `project/raw/README.md` records that temporary state.
Closure removes both files and their index entries after this work item moves to
the audit-only archive, where historical links may require Git history.

`project/raw/bare-source.md` changes its literal handoff from the retiring raw
input to the current literal owner. Its keyword-neutral enclosure question
remains otherwise unchanged.

`project/raw/feature-catalog.md` routes custom literals and string identities to
the new current owners and runtime remainder to the new string raw input.

Legacy pages are dispositioned by value:

- `basics.md` routes current literal and string behavior to the two new owners
  and removes superseded literal/string inventory that has no unique future
  value.
- `casting.md` removes duplicate string-conversion proposals after their useful
  runtime pressure and representative failures move to the indexed string raw
  input.
- `forward.md` routes `forward operator literal` to the indexed
  namespace/import input.
- `operator.md` replaces its retiring raw-literal route with the current literal
  owner.
- `namespacing.md` identifies literal alias/import behavior as current deferred
  pressure and routes it to the indexed namespace/import input; its unrelated
  legacy module material remains.

No useful literal finding remains only in a legacy page, retiring raw file, or
this eventual archive.

### Human reading-path dry run

The cold-reader path is coherent:

```text
index.md
-> literal-source-and-operators.md
   -> integer-literals.md / fixed-point-scalars.md /
      floating-point-scalars.md for numeric realization
   -> strings-and-characters.md for result identities and joins
   -> source-structure.md for complete layout/token rules
   -> declarations-and-bindings.md / operators.md for reusable machinery
```

Direct entry into the string owner begins with ordinary byte string, ASCII,
Rune, and UTF examples. Direct entry into the literal owner begins with source,
results, merge, and join rather than declaration syntax or an exhaustive
catalog. Each local scalar/current-owner section gives the immediate outcome and
routes the complete foreign concern without requiring project history.

Current programmer-facing owners cite only current owners. Raw inputs remain
outside ordinary reading paths.

### Exact promotion change set

Create:

- `language/literal-source-and-operators.md`
- `language/strings-and-characters.md`
- `project/raw/namespaces-imports-and-visibility.md`
- `project/raw/string-runtime-and-encoding.md`

Modify current language owners and routers:

- `index.md`
- `language/source-structure.md`
- `language/integer-literals.md`
- `language/fixed-point-scalars.md`
- `language/floating-point-scalars.md`
- `language/integers.md`
- `language/declarations-and-bindings.md`
- `language/function-invocation.md`
- `language/operators.md`
- `language/operator-catalog.md`
- `language/operator-phrases.md`
- `language/intent-acknowledgements.md`
- `language/terms.md`

Modify live raw routing and deferred inputs:

- `project/raw/README.md`
- `project/raw/literal-operators.md`
- `project/raw/literals-maintainer-notes.md`
- `project/raw/compile-time-execution.md`
- `project/raw/type-parameters-and-generics.md`
- `project/raw/reflection.md`
- `project/raw/interop.md`
- `project/raw/cpu-provider-model.md`
- `project/raw/indexing-and-slicing.md`
- `project/raw/bare-source.md`
- `project/raw/feature-catalog.md`

Modify affected legacy pages:

- `basics.md`
- `casting.md`
- `forward.md`
- `operator.md`
- `namespacing.md`

Update this working file only to record promotion validation and any material
dry-run revision discovered during authorized promotion:

- `project/work/023-literal-source-and-literal-operators.md`

No other file is in the proposed promotion set. In particular:

- `README.md` remains the repository router without a topic-level list;
- `project/README.md` remains unchanged until separately authorized closure;
- operating-prompt sources remain unchanged;
- implementation mappings are not added; and
- no archive, work item `024`, stage, commit, push, branch, or history operation
  belongs to promotion.

### Validation result

PASS:

- every aligned finding has one complete owner;
- the two new owners are cohesive and independently teachable;
- no current owner depends on numbered work or raw input for meaning;
- scalar realization remains with its specialized owners;
- literal-local compile-time guarantees are current while general execution
  mechanics remain indexed;
- text/runtime, alias/import, generic, reflection, interop, provider, and
  container consequences have live destinations and activation pressure;
- root legacy and raw literal evidence have complete value-based disposition;
- terminology, intent registry, catalog, routes, and source diagnostics are in
  the exact change set;
- the staged/unstaged boundary remains preservable; and
- promotion can be completed without implementation documentation or a formal
  specification.

Any material design or ownership change found during promotion revises this
result to FAIL before work continues.

### Promotion validation: PASS

Validated 2026-09-15 after applying the authorized promotion.

The promotion:

- created the cohesive current literal and string/character owners;
- integrated source, integer, fixed, floating, declaration, invocation,
  operator, phrase, intent, terminology, and navigation surfaces;
- created indexed namespace/import and runtime-string deferrals;
- updated compile-time, generic, reflection, interop, provider, container,
  bare-source, and feature-catalog raw destinations;
- consumed and pruned the two literal raw inputs while retaining temporary
  provenance stubs required by the immutable fixed initiating links; and
- dispositioned affected basics, casting, forward, operator, and namespace
  legacy material by value.

The fixed initiating input retains its historical description of the two raw
sources because that section is immutable. The live reading-scope descriptions
and this validation record state their consumed status, and both stubs route to
the current owners and indexed deferrals without retaining competing technical
behavior.

Validation confirms:

- current owners contain no links into `project/raw/`;
- current and public literal/string routes no longer depend on raw input for
  meaning;
- local Markdown link targets and changed heading anchors resolve;
- ordinary examples introduce their required concepts before advanced catalogs;
- invalid examples carry inline error comments;
- the generic `uncommitted` placeholder always specializes to one concrete
  result before invocation;
- scalar realization and string/character identity have unique owners;
- deferred runtime, generic, module, reflection, interop, provider, and
  container work has a live indexed destination;
- consumed raw bodies no longer teach superseded adjacent merge/join, Unicode,
  radix, or result behavior;
- the changed-file set matches the recorded exact promotion scope;
- Markdown diff whitespace validation passes; and
- the maintainer's staged baseline remains intact, with promotion changes
  unstaged.

**PASS** confirms the authorized promotion is coherent. It does not authorize
archival, work item `024`, staging, committing, pushing, or another
version-control operation.

## Post-promotion refinement dry run

### Result: PASS

Conducted 2026-09-16 after maintainer review aligned the refinements recorded at
the beginning of the working record.

The refinements do not require a new owner or directory structure:

- `language/literal-source-and-operators.md` remains the complete owner for
  implicit/explicit merge, exact uncommitted real source, optional `p0`, raw
  pattern signs, and literal-facing type constants.
- `language/strings-and-characters.md` remains the complete owner for explicit
  code-unit backing, byte/text admission, quote examples, legacy character
  identities, MBCS termination, native-string admission, and raw-access
  pressure.
- fixed/floating/integer/source/operator/intent/term owners receive only their
  local integration rules.
- existing indexed compile-time, string-runtime, reflection, provider, and
  interop inputs can absorb every deferred mechanism.

The current wider documentation still teaches the previous promoted model and
must not be treated as incorporating these refinements until a separate
follow-up promotion is authorized.

### Exact follow-up promotion set

Modify current owners:

- `language/literal-source-and-operators.md`
- `language/strings-and-characters.md`
- `language/source-structure.md`
- `language/integer-literals.md`
- `language/fixed-point-scalars.md`
- `language/floating-point-scalars.md`
- `language/integers.md`
- `language/operator-catalog.md`
- `language/operators.md`
- `language/intent-acknowledgements.md`
- `language/terms.md`

Modify indexed future-pressure owners:

- `project/raw/string-runtime-and-encoding.md`
- `project/raw/compile-time-execution.md`
- `project/raw/reflection.md`
- `project/raw/cpu-provider-model.md`
- `project/raw/interop.md`

Update this work file only for follow-up promotion validation:

- `project/work/023-literal-source-and-literal-operators.md`

No router, new file, legacy page, consumed literal stub, archive, operating
prompt, implementation document, or additional raw destination is required.

### Teaching and ownership checks

PASS:

- implicit merge remains one source operation and `<+>` remains an explicit
  value operation;
- intent acknowledgement confirms a written join without changing merge grammar;
- exact uncommitted real evaluation mirrors integer commitment while retaining
  a bounded compiler-capacity failure;
- type-owned mathematical/special constants have scalar-specific owners;
- code-unit backing is explicit without promising contiguous ordinary-string
  storage;
- `Byte`, `U8`, `AsciiChar`, `Rune`, and whole-sequence validation remain
  distinct;
- only `Legacy.CharString` and optional `Legacy.WideString` own contiguous
  NUL-terminated semantics;
- MBCS remains length-tracked, permits embedded NUL, and has no implied
  sentinel;
- optional `Legacy.WChar` produces concrete compile-time/reflection pressure
  without inventing a fallback type; and
- every deferred mechanism has a live indexed destination.

This revised **PASS** does not authorize the follow-up promotion or alter the
maintainer's staged comparison boundary.

### Follow-up promotion validation: PASS

Validated 2026-09-16 after applying the authorized follow-up promotion directly
to the wider current documentation.

The follow-up:

- permits implicit same-declaration literal merge while retaining optional
  explicit `<|>` and explicit `<+>` joining;
- aligns source continuation, intent acknowledgement, and operator-catalog
  presentation with that merge grammar;
- replaces destination-propagated literal real arithmetic with bounded exact
  rational evaluation before commitment;
- makes a missing `p` imply `p0` when a first-class `b`/`h` payload has a
  fractional point;
- adds concrete floating special constants and shared fixed/floating
  mathematical constants;
- makes every string's logical underlying unit type explicit without promising
  contiguous ordinary storage;
- rejects singular `Byte`/`U8` admission into text while preserving
  whole-sequence validation;
- reduces native character roles to always-defined `Legacy.Char` and optional
  `Legacy.WChar`;
- separates length-tracked, embedded-NUL-permitting MBCS from the two
  NUL-terminated native legacy strings; and
- preserves contiguity, raw pointer, exact-real capacity, optional declaration,
  reflection, provider, and interop pressure in existing indexed owners.

Validation confirms:

- every changed wider file belongs to the recorded exact follow-up set;
- no new owner, router, legacy page, consumed stub, or raw destination was
  required;
- current owners agree on implicit merge, exact rational commitment, optional
  `p0`, scalar constants, backing units, byte admission, optional wide
  characters, and MBCS termination;
- invalid source examples carry inline error comments;
- current owners still contain no links into `project/raw/`;
- local Markdown targets and changed heading anchors remain valid;
- the earlier mandatory-`p0` catalog wording was reconciled;
- Markdown diff whitespace validation passes; and
- the fully staged prior promotion remains intact while this follow-up is
  unstaged.

**PASS** confirms the follow-up promotion is coherent. It does not authorize
archival, creation of work item `024`, staging, committing, pushing, or another
version-control operation.

## Termination-policy refinement dry run and validation

### Dry run: PASS

The refinement generalizes termination without changing the current owner
structure:

- baseline `Legacy.CharString`, optional `Legacy.WideString`, and MBCS remain
  length-tracked strings with no implied terminator;
- `Legacy.TerminatedCharString<TerminatorPolicy>` and
  `Legacy.TerminatedWideString<TerminatorPolicy>` are concrete policy
  specializations;
- each policy owns terminator units/sequences, in-band admission or escaping,
  contiguity, physical suffix, logical length, conversion, and mutation
  restoration;
- common NUL-terminated aliases provide `.c_str()` behavior without imposing it
  on every platform/API string; and
- platform-specific policies may provide another concrete representation
  without entering ordinary current-language teaching as a historical example.

The exact refinement set is:

- `language/strings-and-characters.md`
- `language/terms.md`
- `project/raw/README.md`
- `project/raw/string-runtime-and-encoding.md`
- `project/raw/interop.md`
- `project/raw/reflection.md`
- `project/raw/type-parameters-and-generics.md`
- `project/work/023-literal-source-and-literal-operators.md`

No new owner, router, legacy page, consumed stub, or directory change is needed.

### Promotion validation: PASS

Validation confirms:

- baseline native and MBCS strings permit embedded NUL and promise no sentinel;
- termination policy is part of concrete static type identity rather than a
  runtime mode;
- NUL-terminated aliases retain no-embedded-NUL, contiguous storage, trailing
  sentinel, and `.c_str()` behavior;
- policy-owned admission can model another platform/API terminator without
  weakening ordinary strings;
- current owners contain no links into `project/raw/`;
- generic, runtime-string, reflection, and interop consequences have live
  indexed owners;
- the changed-file set matches the recorded refinement set;
- Markdown diff whitespace validation passes; and
- the fully staged prior documentation remains intact while this refinement is
  unstaged.

**PASS** does not authorize archival, work item `024`, staging, committing,
pushing, or another version-control operation.

## Closure

Work item `023` is complete. Its aligned findings and refinements have been
promoted and validated. Literal source, string/character identity, compile-time
joining, exact-real commitment, scalar constants, and termination-policy
behavior have current owners; remaining generic, compile-time, runtime-string,
namespace/import, reflection, interop, provider, and container mechanics have
indexed raw destinations.

This file is historical, non-normative, and audit-only. Current language owners
do not depend on it for meaning.
