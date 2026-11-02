# Zax integer literals and realization

| Field | Value |
| --- | --- |
| Status | Current conceptual design |
| Audience | Human developers reading, writing, or evaluating integer source and compile-time integer expressions |
| Applies To | Programmer-visible unprefixed and generic-literal integer source, uncommitted integer evaluation, commitment, realization, and interaction with typed operations |
| Implementation State | Not established by this repository |
| Owns | Uncommitted integer values from ordinary or generic literal source; sign intent; width-invariant operations; default and direct realization; candidate holes; contextual completion of numeric operands; commitment and stopping boundaries; conditional convergence; interaction with conversion and optional construction; costs, failures, diagnostics, and source stability |
| Does Not Own | Concrete integer types and ranges ([integers](integers.md)); general ordinary-real and prefixed/custom literal syntax, declarations, payloads, radix catalogs, merge, and joining ([literal source and operators](literal-source-and-operators.md)); fixed-point and floating real-number realization ([fixed-point scalars](fixed-point-scalars.md), [floating-point scalars](floating-point-scalars.md)); concrete protected operation policies ([integer operator catalog](integer-operator-catalog.md)); general callable and operator discovery ([function invocation](function-invocation.md), [operators](operators.md)); or exact forms and precedence ([operator catalog](operator-catalog.md)) |
| Source / Provenance | Legacy integer and literal design corpus, refined against current integer, declaration, invocation, flow, and operator design |

## Start with ordinary integer source

An ordinary integer begins as a mathematical value rather than a hidden
fixed-width machine value:

```zax
myDefault := 55
myUnsigned := +55
myByte : U8 = 255
myMinimum : I8 = -128
```

The surrounding boundary determines when that value receives a concrete type:

- `myDefault` has no requested type, so `55` becomes `Integer`;
- pre-unary `+` gives `myUnsigned` unsigned intent, so it becomes `UInteger`;
- the typed declarations realize `255` directly as `U8` and `-128` directly as
  `I8`.

The typed forms do not first create `Integer` and then convert it:

```zax
myByte : U8 = 255
```

There is no hidden intermediate declaration such as `(: Integer = 255)`,
implicit conversion, or runtime range check. The compiler checks the
mathematical value and creates the value expressed by `(: U8 = 255)` directly.

## The uncommitted integer

Before a concrete integer type is selected, numeric source produces an
**uncommitted integer**. It has:

- a mathematical integer value;
- unknown, signed, or unsigned intent; and
- no concrete type, logical width, storage, identity, or runtime
  representation.

**Uncommitted integer evaluation** applies the small language-defined family of
mathematical operations that does not require a concrete width:

```zax
myByte : U8 = 1000 - 745
```

The subtraction produces mathematical `255`; the typed declaration then
realizes it as `U8`.

An uncommitted integer is never a runtime value. It must reach a **commitment
boundary**, where one concrete integer type is selected. **Integer
realization** then checks intent and range and creates that concrete value.

The compiler necessarily places a practical capacity limit on mathematical
values. Reaching that limit is a compiler resource or capacity failure, not
overflow in a hidden `Integer`. The exact capacity and resource policy remain
future compile-time work.

## Sign intent

Bare numeric source begins with **unknown intent**:

```zax
55
-55
```

Mathematical negation changes the value but does not declare that the programmer
wants a signed type. A negative value is still unknown intent until another
source operation establishes intent or a concrete type is selected.

### Pre-unary `+` toggles intent

Pre-unary `+` is the only uncommitted operation that originates or toggles sign
intent:

| Input intent | Result intent |
| --- | --- |
| Unknown | Unsigned |
| Unsigned | Signed |
| Signed | Unsigned |

```zax
myUnknown := 55       // Integer
myUnsigned := +55     // UInteger
mySigned := +(+55)    // Integer
myUnsignedAgain := +(+(+55)) // UInteger
```

Known intent is binding:

```zax
myUnsignedByte : U8 = +55
mySignedByte : I8 = +(+55)
myConflict : U8 = +(+55) // error: signed intent cannot realize as U8
```

### Combining intent

An operation that produces one uncommitted integer from several uncommitted
integer operands combines their intent:

| Left | Right | Result |
| --- | --- | --- |
| Unknown | Unknown | Unknown |
| Signed | Unknown | Signed |
| Unknown | Signed | Signed |
| Unsigned | Unknown | Unsigned |
| Unknown | Unsigned | Unsigned |
| Signed | Signed | Signed |
| Unsigned | Unsigned | Unsigned |
| Signed | Unsigned | Error |
| Unsigned | Signed | Error |

```zax
myUnsignedSum := (+5) + 6
myOtherUnsignedSum := (+5) + (+7)
myConflict := (+5) + (+(+7)) // error: unsigned and signed intent conflict
```

Value history does not create intent. A negative intermediate is permitted while
intent remains unknown:

```zax
myValue : U8 = +(999 - 1000 + 2)
```

The parenthesized expression finishes as mathematical `1`; only then does `+`
apply unsigned intent.

Once unsigned intent exists, an operation may not produce a negative result:

```zax
myValue := (+5) - 10 // error: unsigned-intent result would be -5
```

## Commitment and realization

### Default commitment

An inferred boundary with no selected type uses:

- `Integer` for unknown or signed intent; and
- `UInteger` for unsigned intent.

```zax
myInteger := 55
mySignedInteger := +(+55)
myUnsignedInteger := +55
```

The preferred default is selected before range checking:

```zax
myValue := 999999999999999999999999999999999999
// error: the value does not fit Integer; state the intended type
```

The compiler does not silently try `Long`, `LongLong`, a maximum role, or a
generated exact width after `Integer` fails.

An anonymous inferred declaration commits immediately:

```zax
myValue : U8 = (:= 55) + 1
// error: (:= 55) commits to Integer, so the addition produces Integer
```

State the intended earlier boundary explicitly:

```zax
myValue : U8 = (: U8 = 55) + 1
```

### Direct typed realization

Exact intrinsic integers and language-defined integer role types directly
realize uncommitted integers:

```zax
myExact : U8 = 55
myRole : Byte = 55
myTarget : Target.Integer = 55
```

Grouping does not alter value, intent, type selection, or failure:

```zax
myA := +55
myB := +(55)
```

Both declarations produce the same `UInteger` value.

Realization accepts an exact fit only:

```zax
myMaximum : U8 = 255
myTooLarge : U8 = 256 // error: 256 is outside U8
myTooSmall : U8 = -1 // error: -1 is outside U8
```

The minimum signed value works because mathematical negation happens before
realization:

```zax
myMinimum : I8 = -128
myTooNegative : I8 = -129 // error: -129 is outside I8
```

There is never an impossible positive intermediate such as `(: I8 = 128)`.

### Identities do not become integers through storage

An arbitrary identity does not gain direct integer realization merely because
its underlying type is an integer:

```zax
MyValue :: identity admit expose type Integer
```

`admit` controls declared admission and `expose` adapts eligible underlying
operations. Neither places `MyValue` in the integer family.

A typed `MyValue` initializer performs finite construction or admission
selection. Its discovered candidates may contain concrete integer parameters
that an uncommitted value can fill, but selection never recursively constructs a
temporary merely to make another candidate viable.

Transparent aliases differ because they preserve the original identity:

```zax
MyIntegerAlias :: alias type Integer
myValue : MyIntegerAlias = 55 // direct Integer realization
```

Whether a future custom numeric-family mechanism may declare a genuine integer
type remains future numeric work.

## Which calculations need an integer width?

Some operations have one clear numerical result before an integer width is
chosen:

```zax
myShifted := 1 << 3 // mathematical 8, then Integer
myBits := 55 ^ 77   // mathematical 122, then Integer
```

Others do not:

```zax
myComplement := ~1
// error: signed complement is -2, while unsigned complement depends on width
```

The useful question is:

> Would choosing another sufficiently wide integer type change the number this
> operation produces?

If every fitting width produces the same number, Zax can calculate first and
choose the concrete type afterward. This is the **width-invariance** test.

The word *fitting* matters. For `1 << 8`, an eight-bit result type does not fit
the mathematical result `256`; it is not evidence that the mathematical shift
means zero. Every sufficiently wide fitting type produces `256`.

### Width-invariant operations

The initial uncommitted operation family is:

| Category | Forms | Mathematical behavior |
| --- | --- | --- |
| Grouping | `(value)` | Preserve value and intent; change only precedence. |
| Sign intent | Pre-unary `+` | Toggle unknown to unsigned, unsigned to signed, or signed to unsigned. |
| Negation | Pre-unary `-` | Negate and preserve intent. |
| Arithmetic | `+`, `-`, `*` | Produce the mathematical result without finite-width overflow. |
| Division | `/` | Truncate toward zero; diagnose a zero divisor. |
| Remainder | `%` | Diagnose a zero divisor; a nonzero result has the dividend's sign. |
| Magnitude | `\|value\|` | Produce the absolute mathematical value and preserve intent. |
| Mathematical shifts | `<<`, `>>` | Shift through an unbounded sign-extended representation using a nonnegative mathematical count. |
| Binary bitwise | `&`, `\|`, `^`, `&~` | Operate through infinite sign extension. |
| Equality | `==`, `!=` | Compare mathematical values and return `Boolean`. |
| Ordering | `<`, `<=`, `>`, `>=` | Compare mathematical values and return `Boolean`. |

```zax
mySum : U8 = 200 + 55
myProduct := 9999999999999 * 44444444444
myQuotient := -7 / 3  // mathematical -2
myRemainder := -7 % 3 // mathematical -1
myMagnitude : U16 = |-999|
myLeftShift := 1 << (1 << 3) // mathematical 256
myRightShift := -9 >> 2      // mathematical -3
myNegativeBits := -1 ^ 55    // mathematical -56
myComparison := (+5) < (+(+7)) // Boolean true
```

Left shift is multiplication by `2^count`. Mathematical right shift is floor
division by `2^count`. The count must be nonnegative.

Binary bitwise operations behave as though signed values have infinitely many
leading sign bits. This produces the same decoded number at every sufficiently
wide fitting signed or unsigned width.

Operations that produce one integer combine sign intent. Comparison produces
`Boolean`, so differing operand intent does not need to become one result
intent:

```zax
myComparison := (+5) < (+(+7))
myArithmetic := (+5) + (+(+7))
// error: arithmetic cannot combine unsigned and signed intent
```

### Complement depends on value and intent

Complement is available only when value and established intent leave one
width-invariant result.

Unknown positive values fit both signed and unsigned types, whose complement
results differ:

```zax
myValue := ~1 // error: signed and unsigned results differ
```

Unsigned complement changes with width:

```zax
myValue := ~(+1) // error: no width-independent unsigned result
```

Signed intent gives one result at every fitting width:

```zax
myNegative := ~(+(+1)) // signed mathematical -2
myZero := ~(-(+(+1)))  // signed mathematical 0
```

An unknown negative operand also has one result because no unsigned type can
contain the operand:

```zax
myZero := ~(-1) // unknown-intent mathematical 0, then Integer
```

Every inner expression must succeed on its own:

```zax
myError := (~1) ^ 55 // error at ~1
myValue := (~(-1)) ^ 55 // mathematical 55
```

XOR cannot repair an inner complement whose value depended on an unknown width.

### Failures before and after type selection

Keep three failures distinct:

| Failure | Example | Meaning |
| --- | --- | --- |
| No invariant result | `~1` | Fitting interpretations produce different mathematical values. |
| Intent error | `(+5) ^ (+(+7))` | One integer result cannot carry conflicting intent. |
| Range failure | `myValue : I8 = 240 ^ 15` | The mathematical result `255` exists but does not fit the selected type. |

Value and intent jointly decide whether an uncommitted operation has one
meaning. Value magnitude still does not rank later overloads.

### Operations that still require a concrete type

These operations depend on a finite width, an existing place, a concrete result
structure, or another type-owned contract:

- zero-fill right shift `>>>` and rotations;
- complemented-result bitwise forms such as NAND, NOR, XNOR, and OR-NOT;
- counts, masks, reductions, and bit positions;
- optional, wrapping, saturating, and reporting arithmetic;
- policy signedness counterpart, negation, and magnitude;
- optional division and remainder;
- increment, decrement, and compound mutation;
- optional/narrowing conversion;
- identity projection and admission; and
- custom receiver-owned operations.

```zax
myReport := 10 +!% 255 // error: no concrete width
myRotated := 1 <<% 2   // error: no concrete width
```

Commit a value before using a concrete operation:

```zax
myWrapped := (: U8 = 255) +% 10
myRotated := (: U8 = 1) <<% 2
myComplement := ~(: U8 = 1) // U8 value 254
```

Their complete policies, results, and failures are in the
[integer operator catalog](integer-operator-catalog.md).

A concrete count still does not determine the value being shifted:

```zax
myShifted := 1 << (: U8 = 3)
// error: U8 is a count value here, not a type for the left operand
```

## Number literals filling typed inputs

A number literal can take its integer type from the declaration, assignment,
parameter, or result it directly supplies:

```zax
myByte : U8 = 55   // typed initialization

myDestination : U8
myDestination = 55 // assignment input

consumeByte final : ()(value : U8) = {
}

consumeByte(55)    // function parameter
```

A complete typed result slot is also a hole:

```zax
makeByte final : (result : Byte)() = {
  return 55
}
```

The caller receives concrete `Byte`.

Zax calls these already selected destinations **candidate holes**. The compiler
considers only the finite declarations found through ordinary source and
receiver rules; the literal does not cause a search through unrelated types.

### Selection and range order

When number literals participate in ordinary callable selection:

1. Known sign intent filters incompatible signedness.
2. Concrete identity mismatches do not create implicit conversions.
3. An uncommitted value directly realizes only into an integer-family input.
4. One otherwise surviving candidate is selected.
5. Among several survivors:
   - unknown or signed intent selects an `Integer` input when present;
   - unsigned intent selects a `UInteger` input when present;
   - otherwise the candidates remain ambiguous.
6. Range is checked only after selection.
7. A selected range failure never retries another candidate.

Suppose `consume` has overloads accepting `Integer` and `I128`:

```zax
consume(999999999999999999999999)
// error: selects Integer for unknown intent, then fails Integer range
```

It does not fall through to the `I128` overload.

Suppose `choose` has overloads accepting `U32` and `I8`:

```zax
choose(55)       // error: ambiguous
choose(+55)      // selects U32
choose(+(+55))   // selects I8
choose(-55)      // error: ambiguous; the negative value does not create intent
```

Range does not resolve that last ambiguity. If only the `U32` candidate existed,
it would be selected first and realization of `-55` would then fail.

## When a visible type may complete an operand

The built-in integer case is straightforward:

```zax
myValue := 0 + (: U8 = 5)
```

The right operand visibly establishes `U8`. Zax can therefore check `0` against
`U8` and complete the ordinary `U8 + U8` operation:

```zax
myValue := (: U8 = 0) + (: U8 = 5)
```

The visible peer supplies its complete scalar type, including concrete
endianness and fixed-point `F`:

```zax
myBig : Scalars.Integers.Big.U32 = 1
myResult := myBig ^ 1024
// `1024` realizes directly as Scalars.Integers.Big.U32.

myFixed : U32F8 = 3.0
myFixedBits := myFixed ^ 1
// `1` realizes as U32F8 value 1.0, coefficient 256.
```

The second literal does not become a raw `U32F0` mask. These are the same
bounded peer-completion rules applied to the complete numeric identity.
Fixed-point behavior is defined by
[fixed-point scalars](fixed-point-scalars.md#bitwise-operations-inspect-the-coefficient).

The same idea can be useful for a programmer-defined numeric identity:

```zax
myValue := 0 + (: MyInteger = 5)
```

Unlike creating `(: U8 = 0)`, constructing `(: MyInteger = 0)` may run user
code or have other visible cost. Zax permits that completion only when both the
construction and the operator explicitly allow it.

### `contextual` and `explicit`

Declarations use one **completion mode**:

- `contextual` permits a specifically supported source context to complete one
  missing typed value through one construction step;
- `explicit` requires source to provide the concrete value before the
  declaration is selected; and
- omission means `explicit`.

Illustrative declaration syntax:

```zax
MyInteger :: type {
  +++ contextual final : ()(rhs : Integer) = {
  }

  operator binary '+' contextual final : (
    result : MyInteger
  )(
    rhs : MyInteger
  ) = {
  }
}
```

Both declarations consent to:

```zax
myValue := 0 + (: MyInteger = 5)
```

For number resolution, the result is equivalent to:

```zax
myValue := (: MyInteger = 0) + (: MyInteger = 5)
```

The construction is real and keeps its source-order effects and costs. The
rewrite explains behavior; it does not require an implementation temporary or
promise elision.

Writing `explicit` on either declaration, or omitting the mode, disables this
completion:

```zax
+++ explicit final : ()(rhs : Integer) = {
}
```

Exact final keyword placement remains future declaration/source integration;
the completion modes and explicit default are current conceptual behavior.

### Direct candidates come first

Contextual construction is a fallback, not a competitor to an ordinary
operation.

Suppose visible direct `+` candidates accept `(U8, MyInteger)` and
`(U16, MyInteger)`. Then:

```zax
myValue := 55 + (: MyInteger = 5) // error: ambiguous direct candidates
```

Zax does not insert `(: MyInteger = 55)` to escape that ambiguity.

If only the `U16` candidate exists, it wins and `55` becomes `U16`. If that
selected `U16` later fails its range check, the compiler reports the failure
instead of trying contextual construction.

Only when no direct candidate is viable may the compiler try one contextual
construction into the one type supplied by the visible peer. It does not chain
constructors, conversions, or another fallback.

### Phrase source must already be unambiguous

Contextual completion never guesses a receiver type and then tries several word
partitions to find an operator.

An exact phrase fence can identify one multiword component:

```zax
0 'foobar things make nice decorations' (: MyInteger = 5)
```

Grouping may still be needed to establish one complete tree. Fencing is not
privileged; any future source mechanism that establishes one tree and exact
component before the receiver type is applied can serve the same purpose.

Unfenced source that requires a combination search is an error:

```zax
0 foobar things make nice decorations (: MyInteger = 5)
// error: contextual completion cannot search phrase combinations
```

Complete phrase formation and fencing are defined by
[operator phrases](operator-phrases.md).

### Completion does not spread automatically

A contextual constructor does not authorize every compiler-known destination
to insert it. Current contextual completion applies to:

- a peer-directed operator that also says `contextual`;
- destination-directed plain `as`; and
- language-provided fundamental integer realization.

Ordinary function arguments, returns, and conditional convergence do not
contextually construct arbitrary identities. Another category may opt in only
through a separate language decision.

## Where uncommitted evaluation stops

```zax
myMathematicalResult : U8 = 200 + 55
myTypedResult := (: U8 = 200) + 55
myOtherResult := 200 + (: U8 = 55)
```

For number-literal resolution, these become:

```zax
myMathematicalResult : U8 = 255
myTypedResult := (: U8 = 200) + (: U8 = 55)
myOtherResult := (: U8 = 200) + (: U8 = 55)
```

The resolver's job is to give each participating number one concrete type. Once
that is done for an expression node, ordinary typed compilation resumes. It may
calculate the typed operation early or emit runtime work, but the values never
become uncommitted again.

Stopping is local:

```zax
myValue := (0 + (: U8 = 5)) + 1
```

The inner expression is concrete `U8`. It anchors the outer protected
`U8 + U8`, where the separate uncommitted `1` realizes as `U8`.

Typed optional and reporting results remain concrete:

```zax
myValue := 10 + ((0 +!% (: U8 = 255)).value)
```

The inner reporting operation is concrete `U8` and returns a concrete report.
Ordinary field resolution gives `.value` concrete type `U8`; that field then
anchors the outer addition. The resolver does not carry an uncommitted value
through the report structure.

## Conditional convergence

```zax
myValue : U8 = condition ?? 1 ;; 2
```

The declaration requires one `U8` result regardless of which arm runs, so both
`1` and `2` are checked and used as `U8`. No typeless integer value crosses the
runtime `??` boundary.

Without an outer type, merged intent selects the ordinary default:

```zax
myInteger := condition ?? 1 ;; 2
myUnsigned := condition ?? +1 ;; +2
```

If one arm is a concrete integer, the uncommitted arm adopts that type:

```zax
myValue := condition ?? (: U8 = 1) ;; 2
```

Sign and range are checked after selection:

```zax
mySignError := condition ?? (: U8 = 1) ;; +(+2)
// error: signed intent cannot realize as U8

myRangeError := condition ?? (: U8 = 1) ;; 256
// error: 256 does not fit U8
```

An arbitrary identity is not a direct realization destination:

```zax
myValue := condition ?? makeMyValue() ;; 55
// error when makeMyValue returns a non-integer MyValue
```

Commit that arm explicitly:

```zax
myValue := condition ?? makeMyValue() ;; (: MyValue = 55)
```

Two concrete arms use ordinary convergence and do not reopen:

```zax
myValue := condition ?? (: U8 = 1) ;; (: Byte = 2)
// error: U8 and Byte are distinct identities
```

The convergence hole reaches only each arm's final result. It cannot invent an
inner receiver:

```zax
myValue : U8 = condition ?? ~1 ;; 1
// error: unknown positive complement has no width-invariant result
```

Commit before the width-dependent operation:

```zax
myValue := condition ?? ~(: U8 = 1) ;; 1
```

Nested conditional result positions may share the same convergence type.
Whether the condition is compiler-known does not change typing: both arms are
validated.

## Concrete results and compile-time execution

Every selected function result has its declared concrete type. Executing the
function during compilation does not erase that type:

```zax
compileTimeResult final : (result : Integer)() = {
  return 55
}

myValue := compileTimeResult() // Integer
myByte : Byte = compileTimeResult()
// error: concrete Integer does not reopen for Byte realization
```

Constant arguments do not by themselves move a call to compile time. Candidate
selection, integer realization, and execution timing are separate:

1. select a callable or concrete operation;
2. realize its uncommitted integer inputs;
3. run it during compilation when the caller writes `compile` or the callee's
   execution policy requires it; otherwise run it at runtime.

Execution policies, and when a result counts as a compile-time value, are
defined by
[compile-time code](compile-time-code.md#letting-a-function-decide-execution-policies).

A compile-time-known panic from a selected concrete operation becomes a compiler
diagnostic. Constant evaluation does not reopen typed values as uncommitted
integers.

Every invoked prefixed literal also has one concrete type. A generic
`uncommitted` literal result is specialized to that type before its body runs:

```zax
myDefault := h'FF'  // specializes to suggested UInteger
myByte : U8 = h'FF' // specializes directly to U8
```

Before specialization, the parser's mathematical integer source may fill the
same integer candidate holes as ordinary source. The suggestion does not bind
sign intent:

```zax
myNegative := -h'FF'
```

After one type is selected, ordinary realization and range rules apply. A range
failure does not retry another type, and the concrete result never reopens.
Complete prefix, payload, generic-result, and raw-pattern behavior is defined by
[Zax literal source and literal operators](literal-source-and-operators.md).

## Real-number source and decimal exponents

Source containing a fractional or floating exponent form does not become an
uncommitted integer. It is uncommitted exact real source. Typed fixed-point and
floating destinations realize its final mathematical value according to their
own range and rounding rules:

```zax
myFixed : I16F8 = 1.5
myFloat : Binary32 = 0.1
myExponent : Binary64 = 1.5e-14
myDefaultReal := 1.5 // Float
```

Decimal `e` or `E`, followed by an optionally signed decimal exponent, applies
a power of ten to the exact mathematical source. It makes `1e100` real-number
source even without a fractional point. Bare `e100` is not a number literal.

Digits are required on both sides of a decimal point:

```zax
.5 // error: write 0.5
1. // error: write 1.0
```

The initial uncommitted-real operation family calculates exact rational results
for grouping, sign, `+`, `-`, `*`, rational `/`, equality, and ordering:

```zax
myFloat : Float = 1.0 / 3.0
myQuad : Quad = 1.0 / 3.0

myA : Binary32 = 0.1 + 0.2
myB : Binary32 = (0.1 + 0.2)
// Both realize exact rational 0.3 once as Binary32.
```

Parentheses create no commitment. A concrete inner declaration does:

```zax
myConcrete : Binary32 =
  (: Binary32 = 0.1) + 0.2
```

After that boundary, ordinary concrete Binary32 arithmetic and per-operation
rounding apply. Square root, transcendental functions, representation
operations, and other operations without an exact rational result require a
concrete scalar first.

Exact real evaluation has practical compiler capacity limits. Exceeding them is
a compiler resource/capacity diagnostic rather than rounding in a hidden
compiler-host float.

The ordinary-real token boundary, suggested `Float`, optional binary/hexadecimal
`p` exponents, and complete exact-real behavior are defined by
[literal source and operators](literal-source-and-operators.md#ordinary-numeric-source).
Destination realization is defined by
[fixed-point scalars](fixed-point-scalars.md#initialization-and-real-number-source)
and
[floating-point scalars](floating-point-scalars.md#initialization-and-real-number-source).

## Conversion and admission

Plain `as` may use contextual completion because its written type supplies one
clear type anchor:

```zax
myByte := 55 as U8
myTooLarge := 355 as U8 // error: 355 cannot construct U8
```

`as` receives no resolver-specific exception. Its operator declaration permits
contextual completion, and `U8` supplies the applicable contextual integer
construction. A custom type works only when both its construction and the
applicable `as` form opt in.

Sign intent is checked while constructing the receiver:

```zax
myError := +55 as I8 // error: unsigned intent cannot construct I8
myValue := (: U8 = +55) as I8 // concrete U8 may convert when 55 fits
```

The protected optional and narrowing forms require an already concrete receiver:

```zax
myInvalidOptional := 55 as U8? // error: optional `as` requires a concrete receiver
myInvalidNarrowed := 355 narrowing as U8 // error: narrowing requires a concrete receiver

myWide : I16 = 355
myOptional := myWide as U8? // absent
myNarrowed := myWide narrowing as U8 // U8 value 99
```

Optional conversion cannot reinterpret failed contextual construction as
absence, and narrowing cannot reinterpret it as permission to discard bits.

A destination-owned `from` operation is already explicit and may give a number
literal the type of its declared integer parameter:

```zax
myValue := MyInteger unchecked from 55
```

The operation performs no global source-type search or construction chain.

## Optional construction still has to build a value

Constructing an optional from a concrete value uses its ordinary present-value
construction:

```zax
myValue : FooBar
myOptional := (: FooBar? = myValue)
```

If `FooBar` has an applicable contextual constructor from `Integer`, an
optional may forward numeric source to that contained construction:

```zax
myOptional := (: FooBar? = 355)
```

For construction and failure, this is equivalent to:

```zax
myOptional := (: FooBar? = (: FooBar = 355))
```

An implementation may construct `FooBar` directly in optional storage; the
equivalence does not require a temporary.

Failure to construct the contained value is an error, not absence:

```zax
myOptional := (: U8? = 355) // error: 355 does not fit U8
```

Default absence, optional conversion, and optional construction are separate.
General `copy`/`deep`/`move`/`last` intent and fallback are defined by
[Zax transfer stances](transfer-stances.md). Complete optional wrapper
construction, packets, protected `move`/`last` effects, reset, proven access, and
boxed lifetime behavior is defined by [Zax optional values](optional-values.md).

## Environment-selected destinations

The destination's concrete identity must be known before fit is checked:

```zax
myActive : Integer = 42
myTarget : Target.Integer = 42
myCompilerHost : CompilerHost.Integer = 42
myAbsolute : Scalars.Integers.Little.I32 = 42
```

- `Integer` and `UInteger` use the active execution environment.
- `Target.Integer` and `Target.UInteger` use the target profile.
- `CompilerHost.Integer` and `CompilerHost.UInteger` use the compiler-host
  profile.
- Exact-width identities use fixed language ranges. Their unqualified names
  resolve active-environment endianness; an absolute-endian path fixes stored
  byte order.

Performing realization in the compiler does not make the active environment
mean compiler host: source written in the target program realizes against the
target. Inside [compile-time code](compile-time-code.md#which-definitions-apply-while-compiling),
the active environment is the compiler host. Programmers use an
absolute-endian exact type or explicit environment path when representation
must agree across environments.

## Costs and failures

Uncommitted integer evaluation and realization introduce no runtime conversion
or range check. Compiler work can still be material for very large values.

After realization, operations inherit the selected type's:

- logical width and range;
- protected failure policy;
- native or software-emulated implementation;
- result identity and associated types; and
- runtime or compile-time execution cost.

Failures are kept distinct:

| Failure | Meaning |
| --- | --- |
| Intent conflict | Known signed and unsigned intent cannot produce one uncommitted integer, or known intent conflicts with the selected type. |
| Ambiguous hole | Several finite candidates survive and the applicable `Integer`/`UInteger` preference is absent. |
| Range failure | The selected concrete type cannot represent the mathematical value. |
| No invariant result | Every fitting interpretation does not produce the same mathematical value. |
| Missing receiver | An explicit-only or custom operation has no discoverable receiver/type anchor. |
| Width required | A rotation, zero-fill shift, policy, count, or related operation has no concrete logical width. |
| Contextual completion failure | A contextual operation cannot find one permitted construction or one already fixed operator form. |
| Mathematical domain failure | An uncommitted operation such as division uses an invalid mathematical input. |
| Compiler capacity failure | The compiler cannot represent or evaluate the requested mathematical value under its resource policy. |
| Typed operation failure | An already concrete operation applies its ordinary panic, optional, wrapping, saturation, or reporting policy. |

## Diagnostics

A realization diagnostic should identify:

- the mathematical value;
- known sign intent;
- the selected public type and range;
- the declaration, parameter, operand, branch, or default rule that selected
  it; and
- the environment/profile when the type is environment-selected.

```text
cannot realize integer value 256 as U8
U8 represents 0..255; U8 was selected by the declaration of myByte
```

When candidate preference selected `Integer` or `UInteger`, diagnostics should
say so and must not imply that a wider overload would be tried after range
failure.

Diagnostics distinguish malformed future prefix payloads, candidate ambiguity,
intent conflict, missing receivers, width requirements, range failure,
mathematical-domain failure, compiler capacity, and failure of an already typed
operation.

## Source stability

- Grouping does not change value, intent, type, or failure.
- Value magnitude does not rank overloads.
- Range failure does not fall through to another candidate.
- Adding a finite viable overload may introduce a loud ambiguity; source or
  declaration order never breaks it.
- Granting a [partial](partials.md) that adds a candidate may change an
  existing `Integer`/`UInteger` preference where the grant applies; partial
  provenance does not alter ranking.
- Contextual construction never repairs a direct ambiguity, selected range
  failure, or unavailable-best operation.
- Exact-width destinations retain their range across environments; their
  unqualified endianness follows the active environment.
- Profile-selected destinations follow their documented environment contract.
- A concrete result never reopens because an optimizer proves it constant.
- An unrelated type or import cannot become a speculative receiver for numeric
  source.
- Future prefix syntax cannot reinterpret the established boundary between an
  attached single-quoted payload and a separated phrase fence.

Use explicit commitment when a later type change must not alter selection:

```zax
consumeByte((: U8 = 55))
```

## Boundaries and maturity

This document is current conceptual design, not formal grammar, a compiler
algorithm, or a conformance contract. The compiler may implement resolution
differently while preserving the programmer-visible selection, type, value,
failure, and cost model.

Still future:

- compiler magnitude and resource limits;
- exact generic syntax and compile-time processing for an `uncommitted` literal
  result specialization;
- general required, inferred, and optional compile-time execution;
- custom integer-family declarations;
- exact final declaration grammar for `contextual`/`explicit` completion modes;
- future callable categories that may opt into contextual completion;
- generic and computed destination-type syntax;
- reflection of literal spelling or realization provenance; and
- any future numeric suffix beyond the rejected literal-only `55 : U8` form.

Those future concerns do not change the current rule that an uncommitted integer
never becomes a runtime value and that every selected expression result
ultimately has one concrete static type.
