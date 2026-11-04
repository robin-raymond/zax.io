# Zax literal source and literal operators

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers reading, writing, declaring, or evaluating Zax literals |
| Applies To | Programmer-facing ordinary, prefixed, merged, and joined literal source; not a formal grammar or specification |
| Implementation State | Not established by this repository |
| Owns | Literal taxonomy; ordinary decimal real source and default; attached literal phrases and payloads; source merge `<\|>`; compile-time literal join `<+>`; final unbound literal declarations and borrowed payloads; qualification, lookup, exact aliases, and forwarding; generic uncommitted result specialization; first-class and extended radix catalogs; binary-power exponents; type-qualified raw scalar patterns; literal diagnostics, costs, and source stability |
| Does Not Own | Uncommitted integer operations and realization ([integer literals and realization](integer-literals.md)); concrete [fixed-point](fixed-point-scalars.md) or [floating-point](floating-point-scalars.md) realization; string and character identities ([strings and characters](strings-and-characters.md)); general operator selection ([operators](operators.md)); complete source layout ([source structure](source-structure.md)); the general compile-time model ([compile-time code](compile-time-code.md)); generic declarations and processing ([generics](generics.md)); or runtime string libraries |
| Source / Provenance | Legacy literal, casting, alias, and forward-declaration material refined against current source, scalar, declaration, invocation, operator, and namespace/module design |

## Start with the value each form produces

```zax
myInteger := 55
// Mathematical integer source; defaults to Integer when nothing else selects it.

myReal := 1.5
// Exact decimal source; defaults to Float when nothing else selects it.

myText := "ordinary text"
// String containing the UTF-8 bytes of the admitted source characters.

myRadix := h'FF'
// Mathematical integer 255; defaults to UInteger.

myPattern := I8.h'FF'
// Concrete I8 raw logical-bit pattern, value -1.
```

Ordinary source and a literal operator both finish with one concrete static
result. A generic literal declaration may delay selecting that concrete type
while its payload and destination are examined during compilation, but no
anonymous or typeless value enters the surrounding expression.

Adjacent segments from one resolved literal declaration merge before parsing.
`<|>` is the optional explicit spelling of that same source operation; `<+>`
combines values after parsing:

```zax
myImplicit := s"contains '" s' and "'
myExplicit := s"contains '" <|> s' and "'
// One s invocation with one combined payload.

myJoined := utf8'first line' <+> c'\n' <+> utf8'last line'
// Three literal invocations followed by two compile-time joins.
```

Adjacent segments from different declarations cannot merge or imply a join:

```zax
x'first' y'second' // error: different literal declarations
```

## Ordinary numeric source

### Integers

Ordinary decimal integer source follows the
[uncommitted integer](integer-literals.md#the-uncommitted-integer) model:

```zax
myDefault := 55
myByte : U8 = 255
myMinimum : I8 = -128
```

The source begins as a mathematical value. Its candidate hole, sign intent, or
default selects one concrete integer type, after which ordinary typed behavior
continues.

### Decimal reals

Ordinary decimal real source is exact mathematical input until compilation
selects one concrete fixed-point or floating type:

```zax
myDefault := 1.5          // Float
myFixed : I16F8 = 1.5
myFloat : Binary32 = 0.1
myExponent : Binary64 = 1.5e-14
```

`Float` is the default when no destination or concrete peer selects another
type.

Digits are required on both sides of a decimal point:

```zax
0.5
1.0

.5 // error: write 0.5
1. // error: write 1.0
```

Decimal `e` or `E` applies a power of ten. An optional sign is attached to the
required decimal exponent digits:

```zax
1e3
1.5E+3
1.5e-3

1e  // error: missing exponent digits
1e+ // error: missing exponent digits
e3  // not a number literal
```

The initial exact uncommitted-real operations calculate before commitment:

```zax
myValue1 : Binary32 = 0.1 + 0.2
myValue2 : Binary32 = (0.1 + 0.2)
// Both produce exact rational 0.3, then realize it once as Binary32.
```

Parentheses change precedence but create no commitment. A concrete inner
declaration ends exact evaluation:

```zax
myConcrete : Binary32 =
  (: Binary32 = 0.1) + 0.2
// Binary32 is established before +, so concrete Binary32 arithmetic occurs.
```

The exact uncommitted family includes grouping, sign, `+`, `-`, `*`, rational
`/`, equality, and ordering. Division by zero is diagnosed. Square root,
transcendental functions, representation operations, and any operation without
an exact rational result require a concrete scalar first.

An exact rational need not be expanded into repeating decimal digits:

```zax
myFloat : Float = 1.0 / 3.0
myQuad : Quad = 1.0 / 3.0
```

Both first produce exact `1/3`; each destination then rounds once. The compiler
may use destination-aware bounds when they prove the same result as exact
evaluation followed by one rounding.

Exact evaluation has practical compiler capacity and work limits. Exceeding
them is a compiler resource/capacity diagnostic, never hidden rounding in the
compiler host's largest float. The scalar owners define destination range,
precision, rounding, special values, and representation.

## Attached literal phrases

A literal operator declares one quoted lower-case phrase word:

```zax
MyType :: type {
  operator literal 'cstyle' final : (
    result : MyType
  )(
    payload : String immutable readonly final &
  ) unbound = {
    // Compile-time parsing.
  }
}

myValue := MyType.cstyle'payload'
```

The owner supplies qualification but no receiver instance. `final` fixes the
implementation and `unbound` removes `_`; adding `once` would have no effect and
is an intent error. Source processing constructs one immutable payload in a
final place and lends only readonly access to the invocation.

The initial phrase-name shape is ASCII `[a-z][a-z0-9]*`. Literal phrase names do
not contain spaces:

```zax
operator literal 'b64' ...
operator literal 'c style' ... // error: literal phrase names are one word
```

A qualified owner path uses ordinary path components. The quoted word in the
declaration is the literal phrase; the source use attaches its delimiter
directly to that final name.

### Attachment distinguishes literals from phrase fences

```zax
h'FF'       // attached literal payload
h 'FF'      // separated phrase fence, not a literal
"text"      // ordinary String shorthand
'text'      // phrase fence or phrase diagnostic, not a String
''          // error: empty phrase fence
s''         // attached empty payload; s accepts it as an empty String
```

This boundary is shared with
[operator phrases](operator-phrases.md#exact-phrase-fencing). Backticks are not
literal delimiters.

### Delimiters do not provide escapes

An attached payload may use either quote kind. The opener selects the matching
closer:

```zax
s'my "quoted" text'
s"single quote: '"
c'\n' // c receives backslash followed by n
```

The matching delimiter cannot appear directly in that segment. Zax itself does
no escape processing. A selected literal operator may interpret backslashes or
any other admitted payload characters.

Before the literal operator runs, source processing:

- requires well-formed UTF-8;
- rejects physically present C0 controls `U+0000..U+001F`;
- recognizes the matching quote as the terminator;
- preserves every other admitted scalar exactly;
- performs no Unicode normalization; and
- retains physical spans for diagnostics and source reflection.

Unicode noncharacters are valid scalar values and remain admissible. Surrogate
code points are not Unicode scalars and cannot appear as valid UTF-8 source.

The literal operator receives one `String` containing the admitted payload
encoded as UTF-8. Delimiters and source trivia are absent from that value.
Literal operators may produce control bytes or invalid UTF-8 byte sequences;
the source-input restriction does not restrict the values a result type can
hold.

## Merge literal source

Adjacent literal segments implicitly combine before invoking one literal
operator:

```zax
myValue := x"first '" x' second"'
// One x invocation with payload: first ' second"
```

`<|>` is the optional explicit spelling of the same compiler-understood,
non-overloadable source operation:

```zax
myValue := x"first '" <|> x' second"'
```

Whether adjacent or explicitly connected, every prefix resolves independently
and must identify the exact same literal-operator declaration. Qualification
can therefore verify an unqualified alias:

```zax
MyNamespace.x'first' x'second'
// Valid only when x independently resolves to MyNamespace.x.
```

The qualified side does not disambiguate `x`. An ambiguous `x` remains
ambiguous, and a uniquely resolved different declaration is an error:

```zax
x'first' <|> y'second' // error: different literal declarations
```

Equal result type or equal owner type is insufficient. A mismatch cannot be
acknowledged because two different parsers cannot receive one payload.

Implicit or explicit merge forms literal source before value-operator
precedence:

```zax
myValue := x'a' x'b' <+> y'c'
// Parse x with `ab`, parse y with `c`, then join.
```

A merge chain invokes one parser in either spelling:

```zax
myImplicit := x'a' x'b' x'c'
myValue := x'a' <|> x'b' <|> x'c'
```

Grouping may surround the complete merge but cannot turn a completed expression
back into literal source:

```zax
use((x'a' <|> x'b'))
(x'a') <|> x'b' // error: left side is already an expression
```

Implicit merge does not establish physical-line continuation:

```zax
myValue := x'first ' \
  x'second'
```

A trailing explicit `<|>` establishes ordinary symbolic-infix continuation:

```zax
myValue := x'first ' <|>
  x'second'
```

An explicit `\` on that newline is redundant. Comments and continuation trivia
contribute no payload characters.

An empty attached payload is passed to its parser. An empty segment participating
in either implicit or explicit merge changes nothing and is a
non-acknowledgeable redundant-structure error:

```zax
x''
x'' <|> x'value' // error: remove the empty segment
x'' x'value'     // error: remove the empty segment
```

## Join values with `<+>`

`<+>` is an overloadable binary value operator. During literal joining:

1. each literal independently selects one concrete type and executes during
   compilation;
2. ordinary operator discovery selects `<+>` from those concrete operands;
3. the selected declaration must be available for compile-time execution;
4. the operation executes from left to right; and
5. each step produces one concrete constant result.

Selection does not fall through from a selected runtime-only or otherwise
compile-time-unavailable declaration to a weaker candidate.

String and character identities define their
[directional join behavior](strings-and-characters.md#compile-time-joining).
Other types may declare `<+>` when compile-time joining is meaningful for them.

### Same declaration requires intent acknowledgement

Joining two independently parsed uses of the same declaration strongly
resembles a mistaken merge:

```zax
x'first' <+> x'second'
// error: acknowledge that two parser invocations are deliberate
```

Use:

```zax
myValue := intent<same-prefix-literal-join>{
  x'first' <+> x'second'
}
```

The rule also applies through exact aliases:

```zax
myValue := intent<same-prefix-literal-join>{
  MyNamespace.x'first' <+> x'second'
}
```

The acknowledgement changes no operation. Both literals still execute
independently before `<+>`.

Intent acknowledgement does not disable implicit merge:

```zax
intent<same-prefix-literal-join>{
  x'first' x'second'
}
// error: the segments merge; no same-prefix join exists to acknowledge
```

The category is inapplicable rather than changing adjacency into `<+>`.

## Lookup, aliases, and forwarding

Literal lookup follows lexical visibility and explicit qualification. A result
type does not cause owner discovery:

```zax
myValue : MyType = parse'payload'
// MyType does not cause lookup of MyType.parse.
```

If several visible declarations expose `parse`, the use is ambiguous and must
be qualified. Source, declaration, import, and module order do not break a tie.

An exact short alias names the unique same-named literal family under one type
or namespace:

```zax
x :: alias operator literal MyType

MyType.x'first' <|> x'second'
// Both prefixes independently denote the same declaration.
```

One literal family contains exactly one declaration. Generic result
specialization occurs inside that declaration rather than through result-only
overloads, so the alias needs no prototype preference.

This is not a second compatible literal declaration reusing the same body.
Equal implementation or result identity is insufficient for source merge.

An unqualified prefix can be forwarded before its direct declaration or alias:

```zax
x :: forward operator literal

value := x'payload'
// Prefix identity is known; prototype and execution remain pending.
```

A qualified prefix needs no separate forward when its type or namespace owner
already anchors the pending suffix. General imports, exposure, visibility, and
collision behavior are defined by
[Zax namespaces and modules](namespaces-and-modules.md).

### Every invocation has one concrete result

A non-generic literal declaration states its concrete result directly.

`uncommitted` is a generic scalar-result posture:

```zax
operator literal 'h' final : (
  result uncommitted : UInteger
)(
  payload : String immutable readonly final &
) unbound = {
  // The body is processed under the one selected concrete result type.
}
```

`UInteger` is the suggested concrete specialization when no other destination
selects a type:

```zax
myDefault := h'FF'  // UInteger
myByte : U8 = h'FF' // U8
```

Before invoking the body, compile-time processing uses the payload, candidate
hole, sign intent, and suggestion to select one concrete result prototype. The
body and every surrounding rule then operate exactly as though that concrete
type had been declared directly.

`uncommitted` is a literal-only opt-in. Its result behaves like number-literal
source, so it can take its type from any candidate hole: a typed declaration,
an assignment, a parameter, or a result slot:

```zax
consumeByte final : ()(value : U8) = { }
consumeByte(h'FF')   // the result specializes to U8 from the parameter
```

That is broader than an ordinary
[generic result](generics.md#working-out-from-the-expected-result), which takes
an expected type only from a fully typed declaration.

No anonymous `uncommitted` value enters runtime, storage, result routing, or an
operator. Value magnitude does not rank overloads, and selected range failure
does not retry another candidate.

The suggested `UInteger` does not establish unsigned sign intent:

```zax
myPositive := h'FF'  // unknown intent, suggested UInteger
myNegative := -h'FF' // legal mathematical negation
```

If the suggestion is incompatible with final value or explicit sign intent, the
ordinary compatible `Integer`/`UInteger` default is selected before range
checking. This is selection, not fallback after a selected failure.

The payload is a compile-time input, because literal operators exist only
through compile-time literal source; it needs no `[<constant>]`. Although it is
declared as a reference, payloads are compared by their string contents. The
body is processed once per distinct payload, and the suggestion may be computed
from it:

```zax
operator literal 'h' final : (
  result uncommitted : suggestHexInteger(payload)   // illustrative suggestion
)(
  payload : String immutable readonly final &
) unbound = {
}
```

The suggestion is evaluated during compilation, like any
[default that depends on another compile-time input](generics.md#defaults), and
yields one bounded concrete specialization.

## First-class radix literals

The protected language-provided module surface provides:

| Prefix | Radix | Digits |
| --- | ---: | --- |
| `b` | 2 | `0 1` |
| `o` | 8 | `0` through `7` |
| `dec` | 10 | `0` through `9` |
| `doz` | 12 | `0` through `9`; `X`/`T`/`↊` (`U+218A`) for dek; `E`/`↋` (`U+218B`) for el |
| `h` | 16 | `0` through `9`, `A` through `F` |

ASCII letters are case-insensitive in `doz` and `h`. Dozenal formatters preserve
whether source used `X`, `T`, `E`, `↊` (`U+218A`), or `↋` (`U+218B`).

```zax
myDozenal := doz'1↊↋'
// ↊ U+218A = dek/ten
// ↋ U+218B = el/eleven
```

These payloads contain no sign:

```zax
-h'FF'
+h'FF'
h'-FF' // error: sign belongs outside the payload
```

Leading zeros are accepted and do not change mathematical value.

### Digit grouping

ASCII space is the only built-in grouping separator:

```zax
b'11110000 10101010'
dec'1 000 000'
doz'1 000 000'
h'FFFF FFFF'
h'FF FF FF FF'
```

One space separates groups. There is no leading, trailing, or repeated space.
All groups after the first have equal width; the first contains from one through
that many digits. Grouping changes neither value nor result type. Built-ins
reject commas and underscores; a custom literal parser may define another
policy.

### Binary power exponents

Binary and hexadecimal payloads use `p` or `P` for a power-of-two exponent:

```zax
myHex : Binary64 = h'1.8p+10' // 1.5 * 2^10
myBinary : I16F8 = b'1.1p-3'  // 1.5 * 2^-3
h'1.8'                        // equivalent to h'1.8p0'
b'1.1'                        // equivalent to b'1.1p0'
```

Significand digits use the prefix radix; exponent digits are decimal with an
optional sign. A fractional point implies `p0` when the exponent is omitted.

Without a point or `p`, `b` and `h` produce uncommitted integer source with
suggested `UInteger`. A point or `p` produces uncommitted real source with
suggested `Float`. Thus `h'1'` is integer source while `h'1p0'` is real source.
One generic declaration specializes to an applicable destination or that
suggestion before invocation.

Octal, decimal, dozenal, and arbitrary bases receive no exponent merely for
symmetry. Their payload characters retain their prefix-specific meanings rather
than acquiring universal point, sign, or exponent syntax.

### Extended radix catalog

Less-common positional numeral parsers live under `Radix`:

| Path | Radix | Digits in increasing value order |
| --- | ---: | --- |
| `Radix.b12` | 12 | `0123456789AB` |
| `Radix.b20` | 20 | `0123456789ABCDEFGHIJ` |
| `Radix.b32` | 32 | `0123456789ABCDEFGHJKMNPQRSTVWXYZ` |
| `Radix.b36` | 36 | `0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ` |
| `Radix.bitcoin58` | 58 | `123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz` |
| `Radix.b60` | 60 | `0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwx` |
| `Radix.b62` | 62 | `0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz` |
| `Radix.b64` | 64 | `ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/` |
| `Radix.b94` | 94 | ASCII `!` (`U+0021`) through `~` (`U+007E`) |

Radices through 36 are ASCII case-insensitive where upper/lower forms represent
the same digit. Base 58 and above are case-sensitive under their exact listed
alphabets. `Radix.b32` uses the Crockford alphabet. In `Radix.b64`, `A` is digit
zero.

Base 94 includes both quote characters. Alternate delimiters and `<|>` allow
both to occur in one payload.

A positional numeral is not an RFC byte encoding:

```zax
myNumber := Radix.b64'9+/'
myBytes :=
  Rfc4648.Encoding.base64'VGhlIHF1aWNrIGJyb3duIGZveC4='
```

## Type-qualified raw scalar patterns

The first-class base names also exist under exact integer, fixed-point, and
floating scalar identities:

```zax
I8.h'FF'
U8.dec'255'
I16F8.h'0180'
Binary32.h'3F80 0000'
```

For a scalar with `W` logical representation bits, the parser:

1. removes valid grouping;
2. parses one nonnegative integer `N`;
3. requires `0 <= N < 2^W`;
4. zero-extends to the logical width;
5. interprets those logical bits under the exact destination type; and
6. ignores storage-envelope padding and physical byte endianness.

Digits are most-significant first regardless of target byte order. Leading
zeros do not alter fit.

Integer patterns use the destination's signedness and two's-complement
interpretation:

```zax
I8.h'FF'    // -1
-I8.h'FF'   // I8 +1: construct concrete -1, then negate it
I8.dec'128' // -128
U8.dec'255' // 255
I8.h'100'   // error: needs more than eight logical bits
I8.h'-1'    // error: signs are not raw-pattern payload digits
```

Fixed-point patterns interpret the bits as the coefficient:

```zax
I16F8.h'0180' // coefficient 384, represented value 1.5
```

Floating patterns adopt exact logical format bits:

```zax
Binary32.h'3F80 0000' // 1.0
```

Valid noncanonical floating patterns are preserved. Invalid patterns are
rejected. Non-value storage padding is not supplied or stabilized.

Type-qualified raw-pattern forms reject a point or exponent. Use uncommitted
mathematical source with a typed destination:

```zax
myValue : Binary32 = h'1p0'
```

Zax has no literal-only postfix type form:

```zax
55 : U8 // error

myValue : U8 = 55    // mathematical realization
myBits := U8.dec'55' // explicit raw pattern
```

## Type-owned scalar constants

Values that require a concrete scalar identity use generated type-owned
constants rather than word payloads.

### Floating special values

Applicable floating formats expose:

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

`inf`/`ninf` are positive/negative infinity, `nan` is the canonical quiet NaN,
and `nzero` is negative zero. Each constant has the receiver type's identity.
A format lacking one of those classes does not expose the constant:

```zax
E4M3.inf // error: E4M3 has no infinity
MBF40.nan // error: MBF40 has no NaN
```

A canonical `snan` may exist where the format supports signaling NaN. Exact
NaN payload, sign, signaling state, or noncanonical representation uses a raw
logical pattern:

```zax
myPayloadNaN := Binary32.h'7FC0 0001'
```

### Mathematical constants

Applicable fixed-point and floating types expose correctly rounded
mathematical constants:

```zax
Float.pi
Quad.e
Binary64.sqrt2
I16F8.pi
```

Each constant is rounded directly from its mathematical definition into the
receiver type. It is not computed from another already rounded constant.

The initial catalog is:

```text
pi  tau  e  phi
sqrt2  sqrt3
ln2  ln10  log2e  log10e
invPi  twoOverPi  invSqrtPi  halfPi  quarterPi
```

A fixed-point type exposes a constant only when its correctly rounded value
fits. Integers do not expose this catalog.

Floating representation limits remain separate:

```text
maximumFinite
minimumFinite
minimumPositiveNormal
minimumPositiveSubnormal
epsilon
```

Fixed-point types instead expose `minimum`, `maximum`, and `quantum`. They have
no NaN, infinity, signed-zero class, subnormal class, or floating epsilon.

Complete format-specific availability and value behavior remains with the
[fixed-point](fixed-point-scalars.md) and
[floating-point](floating-point-scalars.md) owners.

## Required compile-time execution

Literal operators and literal-result joins must complete during compilation.
A literal body is ordinary
[compile-time code](compile-time-code.md): it runs on the build machine, the
same way a `compile` expression does, and its result crosses into the program
as a constant.

```zax
MyColor :: type {
  operator literal 'rgb' final : (result : MyColor)(
    payload : String immutable readonly final &
  ) unbound = {
    // Runs while compiling. Integer here is the build machine's Integer.
  }
}

myAccent := MyColor.rgb'#3366CC'   // a constant in the program; no parsing at runtime
```

Because the body runs on the build machine, environment-relative types inside
it use the build machine's definitions. A result whose type's definition
differs in the target is converted when it crosses, with the range checks
described by
[compile-time code](compile-time-code.md#values-crossing-into-the-program). A
literal whose result must not depend on the build machine computes with exact
types, such as `U32`, or with explicit target types, such as `Target.Integer`.

The local guarantee is:

- source decoding and numeric parsing are independent of the build machine's
  locale;
- results depend only on the payload, the declaration, explicit compile-time
  inputs, the definitions of the environments the body uses, compile-time state
  the body reads, and versioned language and library data;
- a literal body may read and write compile-time state; such evaluations
  happen in source order, as described by
  [compile-time code](compile-time-code.md#compile-time-writes-and-order);
- temporary compile-time allocation cannot leak into the result;
- deliberate literal rejection and language panic become source diagnostics;
- resource exhaustion is a compiler diagnostic rather than another candidate;
  and
- a selected join that cannot run at compile time does not retry a runtime or
  weaker overload.

Exact rejection APIs, sandboxing, caching, and filesystem or network access
remain future compile-time design. `final` and `unbound` describe declaration
storage and receiver shape; required compile-time execution comes from the
literal category.

## Costs and diagnostics

Literal work may include source decoding, arbitrary-precision mathematical
parsing, generic specialization, user compile-time execution, allocation,
transcoding, validation, and result construction. A source form that creates no
runtime parsing can still impose substantial compiler cost and produce a large
constant.

Diagnostics distinguish:

- invalid source UTF-8 or physically forbidden C0 source;
- unterminated or mismatched delimiters;
- attached literal versus separated phrase fence;
- unknown, inaccessible, or ambiguous prefix;
- malformed payload with logical offset and physical segment;
- `<|>` operands resolving to different declarations;
- grouped or empty `<|>` operands;
- adjacent literal segments resolving to different declarations;
- an implicit merge whose physical newline lacks continuation;
- same-declaration `<+>` lacking
  `intent<same-prefix-literal-join>`;
- unavailable required compile-time execution;
- deliberate parser rejection, panic, or resource failure;
- no valid concrete generic specialization;
- selected range failure without retry;
- oversized or invalid raw representation; and
- result capacity failure.

## Formatting, reflection, and source stability

A formatter preserves prefix attachment and qualification, quote kind, payload
spelling, `<|>` versus `<+>`, digit grouping, grouping parentheses,
continuation, comments, and intent acknowledgement. It does not rewrite a custom
payload based on assumptions about its parser.

Source reflection must retain the written prefix path, delimiter, physical
segments, payload spelling, merge and join operations, and acknowledgement even
when the final expression contains one parsed result. Ordinary `type of`
observes the selected concrete type.

Compatibility events include changing:

- an ordinary token boundary or default;
- a built-in prefix, alphabet, grouping, or exponent rule;
- prefix visibility or exact alias target;
- implicit/explicit merge and declaration-identity matching;
- a literal's result-specialization policy;
- escape, normalization, encoding, or raw-bit interpretation;
- required execution context or resource contract; or
- `<+>` availability, result identity, or failure behavior.

## Boundaries and maturity

This document is current conceptual design, not formal grammar, a compile-time
execution specification, or an implementation mapping.

Still future are numeric type-factory interfaces, compile-time sandboxing and
capabilities, exact export/private compiler-directive syntax, runtime joining,
code-page data, complete source-reflection representation, and exact diagnostic
identifiers.
Those future mechanisms must preserve the one-concrete-result, explicit-cost,
no-order-fallback, and source-stability rules established here.
